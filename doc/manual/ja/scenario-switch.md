# スイッチを使う

**Language:** **日本語**

DPDKポート間でVLANベースのL2スイッチングを行うシナリオです。仮想スイッチ（vswitch）を使用して、MACアドレス学習とFDBに基づくパケット転送を実現します。

## 目的

- DPDKポートを仮想スイッチに接続し、L2スイッチングを行う
- VLANによるネットワークの分離
- タグ付き（トランク）/タグなし（アクセス）ポートの設定

## 前提条件

- sdplane-ossがインストール済みであること
- DPDKに対応したNICが搭載されていること
- hugepagesが設定済みであること

## 構成例

2つの物理ポート（port 0、port 1）を1つの仮想スイッチに接続し、VLAN 2031でL2スイッチングを行います。

```
  [Host A] ---- port 0 ---- [vswitch 2031 (VLAN 2031)] ---- port 1 ---- [Host B]
```

## 設定手順

### 1. 設定ファイルの作成

以下の内容で設定ファイルを作成します（例: `/etc/sdplane/sdplane.conf`）。

```bash
# ログ設定
log file /var/log/sdplane.log
log stdout

# デバッグ（必要に応じて）
debug sdplane fdb-change
debug sdplane rib

# デバイスバインド（環境に合わせてPCIアドレスを変更）
set device 02:00.0 driver unbound
set device 02:00.0 driver vfio-pci driver_override
set device 02:00.0 driver vfio-pci bind

set device 03:00.0 driver unbound
set device 03:00.0 driver vfio-pci driver_override
set device 03:00.0 driver vfio-pci bind

# EAL初期化
set rte_eal argv -c 0x7
rte_eal_init
set mempool

# lthreadワーカー設定
set worker lthread rib-manager
set worker lthread stat-collector

# lcoreワーカー設定
set worker lcore 1 enhanced-repeater
set worker lcore 2 l3-tap-handler

# ポート設定
stop port all
set port all dev-configure 1 4
set port all nrxdesc 1024
set port all ntxdesc 1024
set port all promiscuous enable
start port all

# キュー設定（rib-managerの後に実行）
set thread 1 port 0 queue 0
set thread 1 port 1 queue 0

# ポート起動待ち
sleep 5
start worker lcore all

# 仮想スイッチ設定
set vswitch 2031 vlan 2031
set vswitch 2031 port 0 tagged
set vswitch 2031 port 1 tagged

# キャプチャインターフェース（パケットモニタリング用、任意）
set vswitch 2031 capture-if cif2031
```

### 2. sdplaneの起動

```bash
sudo sdplane -f /etc/sdplane/sdplane.conf
```

## タグなし（アクセスポート）の場合

ホスト側でVLANタグを扱わない場合は `untag` を使用します。

```bash
set vswitch 10 vlan 10
set vswitch 10 port 0 untag
set vswitch 10 port 1 untag
```

## 複数VLANの設定例

1つのトランクポートに複数のVLANを収容する構成例です。

```bash
# VLAN 2031
set vswitch 2031 vlan 2031
set vswitch 2031 port 0 tagged

# VLAN 2032
set vswitch 2032 vlan 2032
set vswitch 2032 port 0 tagged
```

## VLANタグ変換

異なるVLAN間でタグを変換（スワップ）する場合：

```bash
set vswitch 10 vlan 10
set vswitch 10 port 0 untag
set vswitch 10 port 1 tag swap 100
```

この設定では、port 0からのタグなしフレームがport 1ではVLAN 100タグ付きで送出されます。

## 動作確認

### 仮想スイッチの確認

```bash
show vswitch
```

仮想スイッチの構成（VLAN ID、ポート数、ルーターIF、キャプチャIF）を確認できます。

### ポートリンクの確認

```bash
show rib vswitch-link
```

各ポートとVLANの関連付け（tagged/untag、タグID）を確認できます。

### FDBの確認

```bash
show fdb
```

MAC学習によって構築されたFDBエントリ（MACアドレス、出力ポート、VLAN、最終検出時刻）を確認できます。FDBは最大1024エントリ、デフォルトエージングタイムは7200秒（2時間）です。

### ポート統計の確認

```bash
show port statistics
```

## 関連項目

- [RIB・ルーティング](routing.md) - vswitch設定コマンドのリファレンス
- [ルータを設定する：静的経路のみ](scenario-static-router.md) - L3ルーティングのシナリオ
- [ワーカー・lcore管理](worker-lcore-thread-management.md)
- [lthread管理](lthread-management.md)
