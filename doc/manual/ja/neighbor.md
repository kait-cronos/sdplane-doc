[トップ](../../../README.md) > [ユーザーガイド](README.md) > 管理・設定ガイド > ネイバーテーブル（ARP/ND）

# ネイバーテーブル（ARP/ND）

**Language:** [English](../en/neighbor.md) | **日本語**

ネイバーテーブルは、IPv4のARP（Address Resolution Protocol）およびIPv6のND（Neighbor Discovery）によって解決されたIPアドレスとMACアドレスの対応関係を管理するテーブルです。L3ルーティングにおいて、パケット転送時の宛先MACアドレス解決に使用されます。

## データ構造

![neighborデータ構造](images/neighbor-structure.png)

![neighborテーブル構造体](images/neighbor-table.png)

## コマンド一覧

- [`show_neighbor`](#show_neighbor) - ネイバーテーブル表示

## コマンド詳細

### show_neighbor

ネイバーテーブル表示
```
show neighbor (|ipv4|ipv6) (|<WORD>)
```

ネイバーテーブル（ARPテーブル / NDテーブル）を表示します。

**パラメータ：**
- `ipv4` - IPv4のARPテーブルを表示
- `ipv6` - IPv6のNDテーブルを表示
- `<WORD>` - 特定のインターフェース名でフィルタ
- パラメータなし - 全てのネイバーエントリを表示

**使用例：**
```bash
show neighbor
show neighbor ipv4
show neighbor ipv6
show neighbor ipv4 tap0
```

## ネイバー解決の仕組み

### 制御パケットの扱い

ルータ処理スレッド（`router` / `srv6-router`）は以下を制御パケットとして判定し、ルーターインターフェース（TAPデバイス）へ送出します：

- ARP
- IPv6 NS/NA（近傍探索/近傍広告）
- OSPF（IPv4/IPv6）
- 宛先が自ルーターインターフェースのIP/MACに一致するパケット

近傍解決情報（ARP/ND）は内部メッセージ経由でネイバーテーブルに反映されます。

### ネイバーエントリの構造

ネイバーテーブルは2種類（ARP / ND）のテーブルで構成されますが、データ構造は共通です。`neigh_manager` がスレッドローカルに管理する `master_neigh_tables` と、`rib_manager` が `rib_info` 内で管理する `rib_info->neigh_tables` があります。

## 関連ワーカー

### neigh-manager

ARP/NDテーブルの管理を行う lthread ワーカーです。Linuxカーネルのネイバー情報と sdplane 内部テーブルの同期を担当します。

```bash
# neigh-managerワーカーの設定
set worker lthread neigh-manager
```

## 確認手順

```bash
# 全ネイバーエントリの確認
show neighbor

# IPv4ネイバーのみ
show neighbor ipv4

# 特定インターフェースのネイバー
show neighbor ipv4 rif1
```

## 定義場所

これらのコマンドは以下のファイルで定義されています：
- `sdplane/rib.c` - ネイバーテーブル関連コマンド

## 関連項目

- [RIB・ルーティング](routing.md) - RIBとルーティング機能
- [仮想スイッチ（vswitch）](vswitch.md) - 仮想スイッチの設定と管理
- [FDB（転送データベース）](fdb.md) - MACアドレス学習テーブル
- [lthread管理](lthread-management.md) - neigh-managerを含むlthread管理
- [ルータを設定する：静的経路のみ](scenario-static-router.md) - 静的経路ルータのシナリオガイド
