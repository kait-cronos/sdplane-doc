[トップ](../../../README.md) > [ユーザーガイド](README.md) > 管理・設定ガイド > FDB（転送データベース）

# FDB（転送データベース）

**Language:** [English](../en/fdb.md) | **日本語**

FDB（Forwarding Database）は、MACアドレス学習によって構築されるL2転送テーブルです。受信パケットの送信元MACアドレスを学習し、宛先MACアドレスに基づいてパケットの出力ポートを決定します。

## コマンド一覧

- [`show_fdb`](#show_fdb) - FDBエントリの表示
- [`show_fib_route`](#show_fib_route) - FIBテーブル表示

## コマンド詳細

### show_fdb

FDB（転送データベース）表示
```
show fdb
```

FDB（Forwarding Database）のエントリを表示します。

**表示項目：**
- MACアドレス（送信元）
- 出力ポートID
- VLAN ID
- エントリ状態（Active / None）
- 最終検出時刻（last_seen）

**使用例：**
```bash
show fdb
```

FDBは最大1024エントリを保持し、デフォルトのエージングタイムは7200秒（2時間）です。

### show_fib_route

FIBテーブル表示
```
show fib (ipv4|ipv6) route
```

IPv4またはIPv6のFIB（Forwarding Information Base）テーブルを表示します。FIBはRIBから選択された最適経路が格納される、実際の転送に使用されるテーブルです。

**パラメータ：**
- `ipv4` - IPv4 FIBテーブルを表示
- `ipv6` - IPv6 FIBテーブルを表示

**使用例：**
```bash
show fib ipv4 route
show fib ipv6 route
```

## FDBの仕組み

### MAC学習

仮想スイッチ（vswitch）は、受信パケットの送信元MACアドレスを自動的に学習し、FDBに登録します。学習されたエントリは、受信ポートとVLAN IDに関連付けられます。

### フラッディング

宛先MACアドレスがFDBに存在しない場合（未学習）、パケットは同一VLAN内の全ポートにフラッディングされます。ブロードキャストフレームも同様にフラッディングされます。

- **Unicast**: 宛先MACがFDBに存在する場合、単一ポートに転送
- **Unicast（未学習）**: 宛先MACがFDBに存在しない場合、VLAN内フラッディング
- **Broadcast/Multicast**: VLAN内フラッディング

### エージング

FDBエントリは一定時間（デフォルト7200秒）通信がないと自動的に削除されます。これにより、ネットワークトポロジの変更に追従できます。

## 確認手順

```bash
# FDBエントリの確認
show fdb

# 仮想スイッチ設定と合わせて確認
show vswitch
show rib vswitch-link
```

## 定義場所

これらのコマンドは以下のファイルで定義されています：
- `sdplane/rib.c` - FDB関連コマンド

## 関連項目

- [仮想スイッチ（vswitch）](vswitch.md) - 仮想スイッチの設定と管理
- [ネイバーテーブル](neighbor.md) - ARP/NDテーブルの管理
- [RIB・ルーティング](routing.md) - RIBとルーティング機能
- [スイッチを使う](scenario-switch.md) - L2スイッチングのシナリオガイド
- [システム情報・監視](system-monitoring.md) - FDBの監視
