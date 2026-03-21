[トップ](../../../README.md) > [ユーザーガイド](README.md) > シナリオガイド > L2リピーターアプリケーション

# L2リピーターアプリケーション

**Language:** [English](../en/l2-repeater-application.md) | **日本語**

l2-repeaterは、DPDKポート間でEthernetフレームを中継するシンプルなLayer 2リピーター機能です。

## 概要

l2-repeaterは、割り当てられたDPDKポートをポーリングし、受信した各フレームを受信ポート以外の割り当てられたポートに複製送信します。MACアドレス学習や経路探索は行わず、Ethernetフレームをそのまま複製するため、シンプルなL2中継や複数ポートへの同報用途に適しています。

`tap-handler` を併用することで、受信フレームは物理ポートへの複製送信に加えてTAPデバイスにもコピーされ、Linux側に引き渡されます。これにより、ホストOSからのパケット観測やユーザー空間ツールによる受信確認を行えます。

## 主要機能

### レイヤー2転送
- **全ポート複製**: 受信ポート以外の割り当てられたポートすべてにフレームを複製送信
- **MAC学習なし**: 転送テーブルを構築しない直接的なパケットリピート
- **透過転送**: 宛先MACに関係なくすべてのパケットを転送
- **スプリットホライゾン**: 受信ポートにはパケットを送り返さない

### パフォーマンス特性
- **ゼロコピー処理**: DPDKの効率的なパケット処理の使用
- **バースト処理**: 最適なスループットのためのパケットのバースト処理
- **低遅延**: 高速転送のための最小限の処理オーバーヘッド
- **マルチコアサポート**: スケーリング用の専用lcore上での実行

## 設定

### 基本セットアップ
L2リピーターは、メインのsdplane設定システムを通じて設定されます：

```bash
# ワーカータイプをL2リピーターに設定
set worker lcore 1 l2-repeater

# ポートとキューの設定
set thread 1 port 0 queue 0  
set thread 1 port 1 queue 0

# 学習のためにプロミスキャスモードを有効化
set port all promiscuous enable
```

### 設定ファイル例
完全な設定例については、[`example-config/sdplane_l2_repeater.conf`](../../../example-config/sdplane_l2_repeater.conf)をご覧ください：

```bash
# デバイスバインディング
set device 02:00.0 driver vfio-pci bind
set device 03:00.0 driver vfio-pci bind

# DPDK初期化
set rte_eal argv -c 0x7
rte_eal_init

# バックグラウンドワーカー（キュー設定前に開始が必要）
set worker lthread stat-collector
set worker lthread rib-manager
set worker lthread netlink-thread

set mempool

# キュー設定（rib-manager開始後に実行が必要）
set thread 1 port 0 queue 0
set thread 1 port 1 queue 0

# ポート設定
stop port all
set port all dev-configure 1 4
set port all nrxdesc 1024
set port all ntxdesc 1024

# ワーカーセットアップ
set worker lcore 1 l2-repeater
set worker lcore 2 tap-handler
set port all promiscuous enable
start port all

# ポートの起動を待つ
sleep 5

# ワーカー開始
start worker lcore all
```

## 動作

### ブロードキャスト転送
L2リピーターは他のすべてのアクティブポートにパケットを転送します：
- **ブロードキャスト動作**: ポートで受信したすべてのパケットを他のすべてのアクティブポートに転送
- **スプリットホライゾン**: 受信ポートにはパケットを送り返さない
- **フィルタリングなし**: すべてのパケットタイプを転送（ユニキャスト、ブロードキャスト、マルチキャスト）

### 転送動作
- **全トラフィック転送**: 宛先MACに関係なくすべてのパケットをリピート
- **全ポートブロードキャスト**: 入力ポート以外のすべてのアクティブポートにパケットを転送
- **透過的**: パケット内容の変更なし（MAC更新が有効でない限り）
- **マルチポート複製**: 各宛先ポート用にパケットのコピーを作成

### MACアドレス更新
有効にすると、L2リピーターはパケットのMACアドレスを変更できます：
- **送信元MAC更新**: 送信元MACを出力ポートのMACに変更
- **透過ブリッジング**: 元のMACアドレスの維持（デフォルト）

## パフォーマンス調整

### バッファ設定
```bash
# ワークロードに合わせてディスクリプタ数を最適化
set port all nrxdesc 2048  # 高パケットレート用に増加
set port all ntxdesc 2048  # バッファリング用に増加
```

### ワーカー割り当て
```bash
# L2転送用に特定のlcoreを専用化
set worker lcore 1 l2-repeater  # 専用コアに割り当て
set worker lcore 2 tap-handler  # TAP処理を分離
```

### メモリプールサイジング
メモリプールは予想されるトラフィックに適切にサイズ設定する必要があります：
- パケットレートとバッファ要件を考慮
- バーストサイズと一時的なパケットストレージを考慮

## 監視とデバッグ

### ポート統計
```bash
# 転送統計の表示
show port statistics all

# 特定のポートの監視
show port statistics 0
show port statistics 1
```

### デバッグコマンド
```bash
# L2リピーターデバッグの有効化
debug sdplane l2-repeater

# VLANスイッチデバッグ（代替ワーカータイプ）
debug sdplane vlan-switch

# 一般的なsdplaneデバッグ
debug sdplane fdb-change
debug sdplane rib
```

## 使用ケース

### ハブ型リピーター
- 接続されたすべてのポートにトラフィックを複製
- 透過レイヤー2リピート
- 学習機能なしの基本的なハブ機能

### ポートミラーリング/リピーティング
- ポート間でのトラフィックミラーリング
- ネットワーク監視と解析
- シンプルなパケット複製

### パフォーマンステスト
- 転送パフォーマンスの測定
- L2転送ベンチマークのベースライン
- DPDKポート設定の検証

## 制限事項

- **VLAN処理なし**: VLAN認識なしのシンプルなL2リピート
- **MAC学習なし**: アドレス学習なしの全ポートブロードキャスト転送
- **STPサポートなし**: スパニングツリープロトコルの実装なし
- **フィルタリングなし**: 宛先に関係なくすべてのパケットを転送

## 関連アプリケーション

- **拡張リピーター**: VLANサポートとTAPインターフェースを備えた高度版
- **L3転送**: レイヤー3ルーティング機能
- **VLANスイッチ**: VLAN対応スイッチング機能

VLANサポートを含むより高度なレイヤー2機能については、[拡張リピーター](enhanced-repeater.md)のドキュメントをご覧ください。