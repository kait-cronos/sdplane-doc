[トップ](../../../README.md) > [ユーザーガイド](README.md) > シナリオガイド > ルータを設定する：静的経路のみを使う

# ルータを設定する：静的経路のみを使う

**Language:** **日本語**

sdplaneを静的経路によるIPルーターとして構成するシナリオです。仮想スイッチ（vswitch）とルーターインターフェースを組み合わせ、Linux側で経路設定を行います。

## 目的

- sdplaneをIPルーター（L3フォワーダー）として動作させる
- 各ポートに異なるサブネットを割り当て、静的経路でルーティングする
- Linuxのネットワークスタックと連携してIPアドレスと経路を管理する

## 前提条件

- sdplane-ossがインストール済みであること
- DPDKに対応したNICが搭載されていること（2ポート以上）
- hugepagesが設定済みであること

## 構成例

2つのポートをそれぞれ異なるサブネットに接続し、ルーティングを行います。

```
  [192.168.1.0/24] ---- port 0 ---- [sdplane router] ---- port 1 ---- [192.168.2.0/24]
                        vswitch 1                          vswitch 2
                       router-if rif1                     router-if rif2
                     192.168.1.1/24                      192.168.2.1/24
```

## 設定手順

### 1. sdplane設定ファイルの作成

以下の内容で設定ファイルを作成します（例: `/etc/sdplane/sdplane.conf`）。

```bash
# ログ設定
log file /var/log/sdplane.log
log stdout

# デバッグ（必要に応じて）
debug sdplane netlink
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
set worker lthread neigh-manager
set worker lthread netlink-thread

# lcoreワーカー設定
set worker lcore 1 router
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

# 仮想スイッチとルーターインターフェースの設定
set vswitch 1 vlan 1
set vswitch 1 port 0 untag
set vswitch 1 router-if rif1
set vswitch 1 capture-if cif1

set vswitch 2 vlan 2
set vswitch 2 port 1 untag
set vswitch 2 router-if rif2
set vswitch 2 capture-if cif2
```

### 2. sdplaneの起動

```bash
sudo sdplane -f /etc/sdplane/sdplane.conf
```

### 3. ルーターインターフェースにIPアドレスを付与

sdplaneが起動すると、TAPインターフェース（rif1、rif2）がLinux上に作成されます。Linux側でIPアドレスを設定します。

```bash
# rif1（port 0側）にIPアドレスを設定
sudo ip addr add 192.168.1.1/24 dev rif1
sudo ip link set rif1 up

# rif2（port 1側）にIPアドレスを設定
sudo ip addr add 192.168.2.1/24 dev rif2
sudo ip link set rif2 up
```

`netlink-thread` が動作しているため、Linuxで設定したIPアドレスと経路はnetlink経由で自動的にsdplaneのRIBに反映されます。

### 4. 静的経路の追加（必要な場合）

directly connected な経路はIPアドレスの設定で自動的にRIBに登録されます。追加の経路が必要な場合は、Linux側で設定します。

```bash
# 例: 10.0.0.0/8 への経路を 192.168.1.254 経由で追加
sudo ip route add 10.0.0.0/8 via 192.168.1.254
```

### 5. 対向ホスト側のデフォルトゲートウェイ設定

各サブネットのホストにsdplaneのルーターインターフェースをデフォルトゲートウェイとして設定します。

```bash
# Host A（192.168.1.0/24側）
sudo ip route add default via 192.168.1.1

# Host B（192.168.2.0/24側）
sudo ip route add default via 192.168.2.1
```

## 動作確認

### RIBの確認

```bash
show rib ipv4 route
```

登録されているIPv4経路（宛先、ネクストホップ、インターフェース）を確認できます。

### FIBの確認

```bash
show fib ipv4 route
```

RIBから選択された最適経路が格納されたFIB（実際の転送テーブル）を確認できます。

### ネイバーテーブルの確認

```bash
show neighbor
show neighbor ipv4
```

ARPにより解決されたネイバーエントリ（IPアドレス、MACアドレス、状態）を確認できます。

### 仮想スイッチの確認

```bash
show vswitch
show rib vswitch-link
```

### ルーターインターフェースの確認

```bash
show rib router-if
```

TAPインターフェースの名前、MACアドレス、IPアドレス、VLAN IDなどを確認できます。

### ping確認

```bash
# Host A から Host B への疎通確認
ping 192.168.2.100
```

### ネクストホップの確認

```bash
show rib nexthop-group
show rib nexthop-pool
```

## ワーカー構成の解説

| ワーカー | 種別 | 役割 |
|----------|------|------|
| `router` | lcore | RIB/FIBに基づくL3パケット転送 |
| `l3-tap-handler` | lcore | ルーターインターフェース経由のパケット処理 |
| `rib-manager` | lthread | RIBの管理・更新 |
| `neigh-manager` | lthread | ARP/NDテーブルの管理 |
| `netlink-thread` | lthread | Linuxカーネルとのnetlink通信（経路・アドレス同期） |
| `stat-collector` | lthread | 統計情報の収集 |

## トラブルシューティング

### 経路がRIBに表示されない
1. `netlink-thread` が動作しているか確認: `show worker`
2. TAPインターフェースがupしているか確認: `ip link show rif1`
3. RIB全体の状態を確認: `show rib`

### ARPが解決されない
1. `neigh-manager` が動作しているか確認: `show worker`
2. ネイバーテーブルを確認: `show neighbor ipv4`
3. ポートの状態を確認: `show port`

### パケットが転送されない
1. FIBに経路があるか確認: `show fib ipv4 route`
2. ネクストホップが解決されているか確認: `show rib nexthop-group`
3. ポート統計を確認: `show port statistics`

## 関連項目

- [RIB・ルーティング](routing.md) - RIBコマンドのリファレンス
- [スイッチを使う](scenario-switch.md) - L2スイッチングのシナリオ
- [ワーカー・lcore管理](worker-lcore-thread-management.md)
- [lthread管理](lthread-management.md)
- [TAPインターフェース](tap-interface.md)
