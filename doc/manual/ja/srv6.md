[トップ](../../../README.md) > [ユーザーガイド](README.md) > 管理・設定ガイド > SRv6（Segment Routing over IPv6）

# SRv6（Segment Routing over IPv6）

**Language:** [English](../en/srv6.md) | **日本語**

sdplaneはSRv6（Segment Routing over IPv6）Transit Node動作を実装しています。SRv6により、IPv6パケットのセグメントルーティングヘッダ（SRH）を処理し、指定されたセグメントリストに沿ってパケットを転送できます。

## SRv6転送機能

![SRv6転送機能](images/srv6-forwarding.png)

## コマンド一覧

- [`set_srv6_local_sid`](#set_srv6_local_sid) - SRv6 Local SID設定
- [`show_srv6_local_sid`](#show_srv6_local_sid) - SRv6 Local SID表示

## 対応機能

- SRH type 4（`RTE_IPV6_SRCRT_TYPE_4`）を処理
- End相当の基本動作（segments_leftデクリメント + 次SIDをIPv6宛先へ反映）
- local-sidは最大16個（0〜15）設定可能
- `srv6-router` ワーカータイプで使用

## コマンド詳細

### set_srv6_local_sid

SRv6 Local SIDの設定
```
set srv6 local-sid <WORD> (|<0-15>)
```

SRv6のLocal SIDを設定します。

**パラメータ：**
- `<WORD>` - IPv6アドレス（SID）
- `<0-15>` - Local SIDインデックス（省略時は0）

**使用例：**
```bash
set srv6 local-sid 2001:db8::1
set srv6 local-sid 2001:db8::2 1
```

### show_srv6_local_sid

SRv6 Local SIDの表示
```
show srv6 local-sid
```

設定されているSRv6 Local SIDを表示します。

**使用例：**
```bash
show srv6 local-sid
```

## 関連ワーカー

### srv6-router

SRv6（Segment Routing over IPv6）によるルーティングを行う lcore ワーカーです。Transit Node機能を実装し、SRHの処理を行います。routerワーカーと大半のコードベースを共有し、IPv6転送処理にSRH処理を追加しています。

```bash
# srv6-routerワーカーの設定
set worker lcore 1 srv6-router
```

## 定義場所

これらのコマンドは以下のファイルで定義されています：
- `sdplane/rib.c` - SRv6関連コマンド

## 関連項目

- [RIB・ルーティング](routing.md) - RIBとルーティング機能
- [仮想スイッチ（vswitch）](vswitch.md) - 仮想スイッチの設定と管理
- [ワーカー・lcore管理](worker-lcore-thread-management.md) - srv6-routerワーカーの管理
