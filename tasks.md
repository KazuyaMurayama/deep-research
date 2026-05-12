# deep-research-forge タスク管理

## ステータス凡例
- [ ] 未着手 / [x] 完了 / [~] 進行中 / [!] ブロック中

## 初期構築タスク
- [x] GitHubリポジトリ作成（KazuyaMurayama/deep-research）
- [x] CLAUDE.md作成
- [x] skills/01〜04.md作成
- [x] session.json初期化
- [x] tasks.md作成
- [x] file_index.md作成
- [x] 初回テストラン実施（日本AIスタートアップ投資トレンド 2024-2025）

## リサーチ実行タスク（テーマごとにここに追記）

### 2026-05-12 日本5都市移住先比較レポート（花小金井/札幌/仙台/軽井沢/新潟）
- [x] Agent1（Opus）: 研究設計（6軸設計・前提仮定・都市別調査ポイント・検索クエリ25本）
- [x] Agent2（Sonnet×5並列）: Web調査・検証（5都市並行・SUUMO/HOME'S/気象庁/各市公式）
- [x] Agent3（Sonnet）: 分析・統合（年間コスト算出・6軸スコアリング・推奨順位決定）
- [x] Agent4: レポート統合・公開（mainブランチ・約600行）

### 2026-05-11 Garry Tan（YC）のClaude Code設定（gstack×gbrain）活用ガイド
- [x] Agent1: 研究設計（Opus・5検索クエリ・5評価軸・レポート構成）
- [x] Agent2: Web調査（Sonnet・garrytan/gstack・garrytan/gbrain・全主要ファイル取得）
- [x] レポート生成（200行・push済み）

### 2026-05-11 ヘルメスエージェント調査レポート
- [x] Agent1（Opus）: 研究設計（SCR分析・調査ポイント整理・課題定義）
- [x] Agent2（Sonnet）: Web調査・検証（Hermes Agent実態・VS Code互換性・Remote Control比較）
- [x] Agent4: レポート統合・公開（mainブランチ）

### 2026-05-08 Bybit・Binance・MetaMask 取引履歴ダウンロードガイド
- [x] Agent1（Opus）: プレリサーチ・構成設計（SCR分析・3プラットフォーム調査ポイント整理）
- [x] Agent2a（Sonnet）: Bybit調査・検証（公式ヘルプ・Data Export手順・ZIP形式・5年遡及確認）
- [x] Agent2b（Sonnet）: Binance調査・検証（Binance Japan移行問題・期間制限・全種別確認）
- [x] Agent2c（Sonnet）: MetaMask/Etherscan調査・検証（CSV機能なし確認・Etherscan手順・5000件上限）
- [x] Agent4: レポート統合・公開（mainブランチ）

### 2026-05-01 Claude Code × Warp 移行・補完ガイド
- [x] Agent1: 研究設計（Opus・SCR・7ニーズ分析・10検索クエリ）
- [x] Agent2: Web調査（Sonnet・公式URL全件確認・URLリダイレクト修正含む）
- [x] Agent3: 分析・構成（Sonnet・7ニーズ可否判定・詳細アウトライン）
- [x] Agent4: レポート4パート分割執筆（Sonnet・883行・push済み）

### 2026-05-01 GMOコイン 入金合計額の確認方法ガイド
- [x] 執筆：高校生向け手順ガイド（mainブランチ直接、ブランチ作成なし）
- [x] 対象：2020年8月〜2026年の投資・現金化ケース
- [x] 内容：ログイン→履歴→CSVダウンロード→SUM集計の3ステップ手順
- [x] 検証：公式サイト実アクセスで修正済み（年間取引報告書・メニュー名・ZIP形式）

### 2026-04-30 パワーローラーS 効果的使い方・健康カテゴリー別スコア評価
- [x] Agent1: 研究設計（Opus・SCR・仮説ツリー・10検索クエリ）
- [x] Agent2: Web調査（Sonnet・10クエリ・Tier1論文7件含む19ソース）
- [x] Agent3: 分析・スコアリング（Sonnet・6カテゴリー×4アプローチ・438行レポート設計）
- [x] Agent4: レポート5パート分割生成（Sonnet・タイムアウト対策・438行・push済み）
- [x] タイムアウト分析・対策計画（Opus）

### 2026-04-23 Claude Code CLI 初心者向け完全マニュアル
- [x] Agent1: マニュアル設計（11パート構成・タイムアウト対策込み）
- [x] Agent2: Web調査（6クエリ・公式ドキュメント中心）
- [x] Agent3: 構成統合
- [x] Agent4: 11パート分割執筆→マージ公開（924行・3回中間commit）
- [x] 2026-04-28 ブラッシュアップ：Windows対応版（1031行）
  - Windowsターミナル4種早見表追加（PowerShell/Git Bash/Claude Code内部/CMD）
  - 全コードブロックにターミナル種別ラベル付与
  - インストール手順をWindows-first構成に変更
  - Windows専用トラブルシューティング追加

### 2026-04-23 EC3社サクラレビュー推定割合 比較
- [x] Agent1: 研究設計（10判定軸＋追加5アプローチ）
- [x] Agent2: Web調査（6クエリ、消費者庁・サクラチェッカー・業界記事中心）
- [x] Agent3: 分析・統合
- [x] Agent4: レポート7分割生成→マージ公開（302行・84/100）

### 2026-04-23 Claude Code 4UIの使い分け比較 2026
- [x] Agent1: 研究設計（SCR・仮説ツリー・12評価軸）
- [x] Agent2: Web調査（6クエリ、Tier1/2中心）
- [x] Agent3: 分析・統合
- [x] Agent4: レポート7分割生成→マージ公開（284行・86/100）

### 2026-04-20 日本のAIスタートアップ投資トレンド 2024-2025
- [x] Agent1: 研究設計（SCR・仮説ツリー）
- [x] Agent2: Web調査（7クエリ、Tier1/2中心）
- [x] Agent3: 分析・統合
- [x] Agent4: レポート生成・公開（品質スコア 87/100）
