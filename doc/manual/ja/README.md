[トップ](../../../README.md) > ユーザーガイド

# sdplane-oss ユーザーガイド

**Language:** [English](../en/README.md) | **日本語**

sdplane-ossは高性能なDPDKベースのソフトウェアルーターです。このユーザーガイドでは、sdplaneの全コマンドと機能について説明します。

## シナリオガイド

- [L2リピーターアプリケーション](l2-repeater-application.md) - MACラーニングによるシンプルなL2パケット転送
- [拡張リピーターアプリケーション](enhanced-repeater-application.md) - TAPインターフェース付きVLAN対応スイッチング
- [パケットジェネレーターアプリケーション](packet-generator-application.md) - 高性能トラフィック生成とテスト
- [スイッチを使う](scenario-switch.md) - VLANベースのL2スイッチングを構成する
- [ルータを設定する：静的経路のみ](scenario-static-router.md) - 静的経路によるIPルーターを構成する

## 管理・設定ガイド

1. [ポート管理・統計](port-management.md) - DPDKポートの管理と統計情報
2. [ワーカー・lcore管理・スレッド情報](worker-lcore-thread-management.md) - ワーカースレッド、lcore、スレッド情報の管理

3. [デバッグ・ログ](debug-logging.md) - デバッグとログ機能
4. [VTY・シェル管理](vty-shell.md) - VTYとシェルの管理
5. [システム情報・監視](system-monitoring.md) - システム情報と監視機能
6. [RIB・ルーティング](routing.md) - RIBとルーティング機能
7. [キュー設定](queue-configuration.md) - キューの設定と管理
8. [パケット生成](packet-generation.md) - PKTGENを使用したパケット生成

9. [TAPインターフェース](tap-interface.md) - TAPインターフェースの管理
10. [lthread管理](lthread-management.md) - lthreadの管理
11. [デバイス管理](device-management.md) - デバイスとドライバーの管理
12. [rte-flow](rte-flow.md) - ハードウェアフロー分類の設定

## 基本的な使い方

### 接続方法

sdplaneに接続するには：

```bash
# sdplaneを起動
sudo ./sdplane/sdplane

# 別のターミナルからCLIに接続
telnet localhost 9882
```

### ヘルプの表示

各コマンドでは `?` を使用してヘルプを表示できます：

```
sdplane# ?
sdplane# show ?
sdplane# set ?
```

### 基本的なコマンド

- `show version` - バージョン情報の表示
- `show port` - ポート情報の表示
- `show worker` - ワーカー情報の表示
- `exit` - CLIからの終了

## コマンドの分類

sdplaneのコマンドは以下の機能カテゴリに分類されています：

1. **ポート管理・統計** (10コマンド) - DPDKポートの制御と統計
2. **ワーカー・lcore管理** (6コマンド) - ワーカースレッドとlcoreの管理
3. **デバッグ・ログ** (2コマンド) - デバッグとログ機能
4. **VTY・シェル管理** (4コマンド) - VTYとシェルの制御
5. **システム情報・監視** (10コマンド) - システム情報と監視
6. **RIB・ルーティング** (26コマンド) - ルーティング情報の管理（SRv6含む）
7. **キュー設定** (3コマンド) - キューの設定
8. **パケット生成** (3コマンド) - PKTGENによるパケット生成
9. **TAPインターフェース** (2コマンド) - TAPインターフェースの管理
10. **lthread管理** (6コマンド) - lthreadの管理
11. **デバイス管理** (2コマンド) - デバイスとドライバーの管理
12. **rte-flow** (3コマンド) - ハードウェアフロー分類の設定

## コマンド一覧

- [全コマンド一覧（アルファベット順）](command-list.md) - 全112コマンドの索引

## ライセンス

このプロジェクトはMITライセンスの下でライセンスされています。ライセンスの詳細については、[LICENSE](../../../LICENSE)ファイルを参照してください。

詳細な使用方法については、各カテゴリのドキュメントを参照してください。