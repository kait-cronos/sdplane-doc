# sdplane-doc プロジェクトコンテキスト

## sdplane-oss とは
- DPDKを使ったソフトウェアデータプレーン
- 対話的シェル（CLI）でDPDKスレッドを制御する「DPDK-dock開発環境」
- OS: Ubuntu 24.04 LTS

## 以前のリリース（既存ドキュメントの範囲）
- L2リピータ
- キャプチャー機能
- パケットジェネレーター
- 拡張リピーター（VLAN対応スイッチング）

## 今回の更新で追加された機能
- L2スイッチング
- ルーティング（L3フォワーディング）
- 経路表（RIB）
- FDB（Forwarding Database）
- ネイバーテーブル
- その他の機能追加

## ドキュメント方針
- 主言語: 日本語
- 将来、他言語も追加予定
- 新機能を学習し、分かりやすくドキュメント化する

## 現在のリポジトリ構成
```
sdplane-doc/
├── README.md                    # 英語メイン README
├── sdplane-logo.png
├── example-config/              # 設定ファイル例
│   ├── sdplane-pktgen.conf
│   ├── sdplane-topton.conf
│   ├── sdplane_l2_repeater.conf
│   ├── sdplane_enhanced_repeater.conf
│   ├── sdplane_l2fwd.conf
│   └── sdplane_l3fwd-lpm.conf
├── doc/
│   ├── README.ja.md             # 日本語 README（doc/内）
│   ├── README.{fr,zh,de,it,ko,th,es}.md  # 各言語 README
│   ├── install-memo.txt         # インストールメモ（内部用）
│   └── manual/
│       ├── en/                  # 英語マニュアル（19ファイル）
│       ├── ja/                  # 日本語マニュアル（19ファイル）
│       ├── de/                  # ドイツ語マニュアル
│       ├── es/                  # スペイン語マニュアル
│       ├── fr/                  # フランス語マニュアル
│       ├── it/                  # イタリア語マニュアル
│       ├── ko/                  # 韓国語マニュアル
│       ├── th/                  # タイ語マニュアル
│       └── zh/                  # 中国語マニュアル
└── ai-workspace/                # AI作業用ディレクトリ
```

## 既存マニュアルファイル一覧（各言語共通）
- README.md - 全機能の概要とコマンド分類
- install-dependencies.md - 依存関係インストール
- build-install-source.md - ソースからのビルド
- build-debian-package.md - Debianパッケージビルド
- system-configuration.md - システム設定
- l2-repeater-application.md - L2リピーターアプリ
- enhanced-repeater-application.md - 拡張リピーターアプリ
- packet-generator-application.md - パケットジェネレーターアプリ
- port-management.md - ポート管理・統計
- worker-lcore-thread-management.md - ワーカー・lcore管理
- debug-logging.md - デバッグ・ログ
- vty-shell.md - VTY・シェル管理
- system-monitoring.md - システム監視
- routing.md - RIB・ルーティング
- queue-configuration.md - キュー設定
- packet-generation.md - パケット生成
- tap-interface.md - TAPインターフェース
- lthread-management.md - lthread管理
- device-management.md - デバイス管理
- enhanced-repeater.md - 拡張リピーター詳細
- dpdk-integration-guide.md - DPDK統合ガイド
