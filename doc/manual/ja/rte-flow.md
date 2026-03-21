[トップ](../../../README.md) > [ユーザーガイド](README.md) > 管理・設定ガイド > rte-flow

# rte-flow

**Language:** **日本語**

DPDKのrte_flowを使用したハードウェアフロー分類の設定コマンドです。NICのハードウェア機能を利用して、パケットのパターンマッチングとアクション（特定キューへの転送やドロップなど）を設定できます。

## コマンド一覧

- [`set_rte_flow_pattern`](#set_rte_flow_pattern) - フローパターンの定義
- [`set_rte_flow_action`](#set_rte_flow_action) - フローアクションの定義
- [`set_rte_flow`](#set_rte_flow) - フロールールの適用

## 概要

rte-flowの設定は以下の3ステップで行います：

1. **パターン定義** (`set rte-flow-pattern`) - マッチするパケットの条件を定義
2. **アクション定義** (`set rte-flow action`) - マッチしたパケットに対する動作を定義
3. **フロールール適用** (`set rte-flow port`) - ポートにパターンとアクションを関連付けて適用

## コマンド詳細

### set_rte_flow_pattern

フローパターンの定義
```
set rte-flow-pattern <ID> index <INDEX> (ether|vlan|ipv4|end) ...
```

パケットマッチ条件（パターン）を定義します。各パターンは複数のindexで構成され、レイヤーごとにマッチ条件を指定します。最後は `end` で終端する必要があります。

**Ethernetレイヤー：**
```bash
# 全Ethernetフレームにマッチ
set rte-flow-pattern 1 index 0 ether any

# 送信元MACアドレスでマッチ
set rte-flow-pattern 1 index 0 ether src 5e:3d:bb:8d:fe:52

# EtherTypeでマッチ（例: IPv6 = 0x86dd）
set rte-flow-pattern 1 index 0 ether type 0x86dd
```

**VLANレイヤー：**
```bash
# VLANフィールドをゼロ初期化（ワイルドカード）
set rte-flow-pattern 1 index 1 vlan zero

# 特定のVLAN IDでマッチ
set rte-flow-pattern 1 index 1 vlan id 100
```

**IPv4レイヤー：**
```bash
# IPv4フィールドをゼロ初期化（ワイルドカード）
set rte-flow-pattern 1 index 2 ipv4 zero

# 送信元IPアドレスでマッチ
set rte-flow-pattern 1 index 2 ipv4 src 100.100.100.100

# 宛先IPアドレスでマッチ
set rte-flow-pattern 1 index 2 ipv4 dst 200.200.200.200

# プロトコル番号でマッチ（例: OSPF = 89）
set rte-flow-pattern 1 index 2 ipv4 proto 89
```

**パターンの終端：**
```bash
set rte-flow-pattern 1 index 3 end
```

### set_rte_flow_action

フローアクションの定義
```
set rte-flow action <ID> index <INDEX> (queue|drop|end) ...
```

マッチしたパケットに対するアクションを定義します。最後は `end` で終端する必要があります。

```bash
# 特定のキューに転送
set rte-flow action 1 index 0 queue 1

# アクションの終端
set rte-flow action 1 index 1 end
```

キューに転送されたパケットは、`set thread <lcore> port <port> queue <queue>` コマンドによりそのキューを担当するDPDKスレッドに送られます。

### set_rte_flow

フロールールの適用
```
set rte-flow port <PORT> pattern <ID> action <ID>
```

定義済みのパターンとアクションをDPDKポートに関連付けて、フロールールを適用します。

**パラメータ：**
- `<PORT>` - DPDKポートID
- `pattern <ID>` - パターンID（`set rte-flow-pattern` で定義）
- `action <ID>` - アクションID（`set rte-flow action` で定義）

**使用例：**
```bash
set rte-flow port 0 pattern 1 action 1
```

## 設定例

### OSPFパケットを特定キューに振り分ける

```bash
# パターン: Ethernet → IPv4 → プロトコル89（OSPF）
set rte-flow-pattern 1 index 0 ether any
set rte-flow-pattern 1 index 1 ipv4 proto 89
set rte-flow-pattern 1 index 2 end

# アクション: queue 1 に転送
set rte-flow action 1 index 0 queue 1
set rte-flow action 1 index 1 end

# ポート0に適用
set rte-flow port 0 pattern 1 action 1
```

### 特定VLAN IDのトラフィックを振り分ける

```bash
# パターン: Ethernet → VLAN 100
set rte-flow-pattern 2 index 0 ether any
set rte-flow-pattern 2 index 1 vlan id 100
set rte-flow-pattern 2 index 2 end

# アクション: queue 1 に転送
set rte-flow action 2 index 0 queue 1
set rte-flow action 2 index 1 end

# ポート0に適用
set rte-flow port 0 pattern 2 action 2
```

## 注意事項

- rte-flowはNICのハードウェア機能に依存します。使用するNICがrte_flow APIをサポートしている必要があります。
- パターンとアクションのindex番号は0から順に指定し、`end` で終端してください。

## 関連項目

- [キュー設定](queue-configuration.md) - DPDKキューの設定
- [ワーカー・lcore管理](worker-lcore-thread-management.md) - ワーカーとキューの割り当て
- [ポート管理・統計](port-management.md) - DPDKポートの管理
