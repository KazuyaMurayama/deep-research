## 📊 セクション4：タスク別・最適UI選択ガイド

**このセクションの結論：** タスクの性質が「コード中心か・自動化か・共有か・モバイルか」でUIが決まる。

| タスク | 第1推奨 | 第2推奨 | 理由 |
|--------|--------|--------|------|
| 新機能実装・バグ修正 | **CLI** | VS Code拡張 | subagents並列＋hooks自動lintが最速 |
| コードレビュー・差分確認 | **VS Code拡張** | Web版 | visual diff・Plan Modeで変更を精密制御 |
| CI/CD組み込み・自動化 | **CLI** | — | headless mode・`--bare`・hooksが必須 |
| 非エンジニアへのPR対応 | **Web版** | — | Auto Mode＋PR auto-fixがターミナル不要で完結 |
| 外出中・スマホで作業 | **Web版** | CLI※Remote | iOS/AndroidアプリでGitHub操作が可能 |
| ローカル文書・資料作成 | **Cowork** | Web版 | ファイル横断・アプリ間操作が得意 |
| チームへのデモ・共有 | **Web版** | — | URLで即共有、Computer Useでライブデモも可 |
| 初学者のコード入門 | **VS Code拡張** | Web版 | GUIで変更が視覚化され安心して使える |
| MCP外部ツール連携 | **CLI** | VS Code拡張※ | MCPサーバーはCLI設定が必須 |
| 大規模リファクタリング | **CLI** | — | subagents並列で複数ファイルを同時処理 |

### ペルソナ別おすすめ組み合わせ

**個人エンジニア（上級）**
→ **メインCLI + 必要時VS Code拡張** で二刀流。バッチ・自動化はCLI、差分レビューはVS Code。

**チーム開発（混合スキル）**
→ **エンジニアはCLI/VS Code拡張、PMはWeb版**で役割分担。Web版のURL共有でブリッジ。

**非エンジニア・知識労働者**
→ **Cowork + Web版**の組み合わせ。文書・メール・資料はCowork、コード関連はWeb版。

**フルリモート・モバイル多用**
→ **Web版メイン + CLIのRemote Control（--channels）**でスマホから承認だけ行う。

---
