# 始めるには

**Language:** [English](getting-started.en.md) | **日本語**

sdplane-ossのインストールから実行までの手順を説明します。

## 1. 依存関係のインストール

liburcu-qsbr、libpcap、lthread、DPDKを含む全ての必要な依存関係のインストールについては、[依存関係インストールガイド](manual/ja/install-dependencies.md)を参照してください。

## 2. プリコンパイルDebianパッケージからのインストール

Intel Core i3-n305/Celelon j3160では、Debianパッケージによるクイックスタートが可能です。

ビルド済みDebianパッケージをダウンロード・インストールします：

```bash
# 最新パッケージのダウンロード (n305用)
wget https://www.yasuhironet.net/download/n305/sdplane_0.1.4-36_amd64.deb
wget https://www.yasuhironet.net/download/n305/sdplane-dbgsym_0.1.4-36_amd64.ddeb

# もしくは (j3160用)
wget https://www.yasuhironet.net/download/j3160/sdplane_0.1.4-35_amd64.deb
wget https://www.yasuhironet.net/download/j3160/sdplane-dbgsym_0.1.4-35_amd64.ddeb

# パッケージのインストール
sudo apt install ./sdplane_0.1.4-*_amd64.deb
sudo apt install ./sdplane-dbgsym_0.1.4-*_amd64.ddeb
```

**注意**: 最新パッケージバージョンについては [yasuhironet.net ダウンロード](https://www.yasuhironet.net/download/)を確認してください。

**注意**: 他のCPUでこのプリコンパイル済みバイナリを使用するとSIGILL（不正命令）が発生する可能性があります。その場合は自分でコンパイルする必要があります。

5. システム設定へジャンプしてください。

## 3. ソースからのビルド・インストール

sdplane-ossをソースコードからビルドする詳細な手順については、[ソースからのビルド・インストールガイド](manual/ja/build-install-source.md)を参照してください。

5. システム設定へジャンプできます。

## 4. Debianパッケージのビルド・インストール

ソースからDebianパッケージを作成・インストールする手順については、[Debianパッケージビルドガイド](manual/ja/build-debian-package.md)を参照してください。

## 5. システム設定

ヒュージページ、ネットワークインターフェース、オプションカーネルモジュールの設定手順については、[システム設定ガイド](manual/ja/system-configuration.md)を参照してください。

## 6. sdplane設定

### 設定ファイル

Debian Packageからインストールした場合、`/etc/sdplane/sdplane.conf.sample`やsystemd service fileが自動的に生成されます。

サンプルを参考に `/etc/sdplane/sdplane.conf`を作成してください。

#### OS設定例（`etc/`）

- [`etc/sdplane.conf.sample`](../etc/sdplane.conf.sample)：メイン設定テンプレート
- [`etc/sdplane.service`](../etc/sdplane.service)：systemdサービスファイル
- [`etc/modules-load.d/`](../etc/modules-load.d/)：カーネルモジュール読み込み設定

#### アプリケーション設定例（`example-config/`）

- [`example-config/sdplane-pktgen.conf`](../example-config/sdplane-pktgen.conf)：パケットジェネレーター設定
- [`example-config/sdplane-topton.conf`](../example-config/sdplane-topton.conf)：Toptonハードウェア設定
- [`example-config/sdplane_l2_repeater.conf`](../example-config/sdplane_l2_repeater.conf)：L2リピーター設定
- [`example-config/sdplane_enhanced_repeater.conf`](../example-config/sdplane_enhanced_repeater.conf)：拡張リピーター設定（VLANスイッチング、ルーターインターフェース、キャプチャインターフェース）

## 7. sdplaneアプリケーションの実行

```bash
# フォアグラウンドで実行
sudo sdplane

# 設定ファイル指定で実行
sudo sdplane -f /etc/sdplane/sdplane_enhanced_repeater.conf

# aptでインストールした場合、systemd経由で実行
sudo systemctl enable sdplane
sudo systemctl start sdplane

# CLIに接続
telnet localhost 9882
```
