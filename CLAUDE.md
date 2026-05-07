# deep-research-forge CLAUDE.md

## このリポジトリの目的
任意テーマの高品質リサーチレポートを自動生成してGitHubに公開する。

## ルール（厳守）
- 本ファイルは軽量に保つ。詳細ルールはskills/配下を参照。
- 毎回プロンプト受信時にfile_index.mdとtasks.mdを参照してから開始する。
- session.jsonで進捗を保存し、途中停止時は/continueで再開できるようにする。
- ブランチ作成禁止。mainブランチのみで運用。
- コミットは各エージェント完了ごとに必ず実行する。
- 成果物はGitHub Contents APIでURLを取得し、ハイパーリンクで報告する。

## モデル使い分け
- Agent1（研究設計）：claude-opus-4-5（高度な論理推論）
- Agent2〜4（実行系）：claude-sonnet-4-5（コスト効率）

## エージェント実行順序
1. skills/01_research_architect.md
2. skills/02_web_researcher.md
3. skills/03_analyst_synthesizer.md
4. skills/04_quality_publisher.md

## タイムアウト対策
詳細ルールは **skills/timeout.md** を必ず参照。
- 各エージェントのターン上限：10ターン
- パイプライン全体上限：40ターン
- 各ウェブ検索は最大10クエリ/エージェントに分割
- 途中保存：各エージェント完了時にsession.jsonを更新してgit commit
- ファイル生成はWrite/Editでローカル完結→Bashでcommit+push（`mcp__github__push_files`は初期構築時のみ）

## ユーザー操作
- 新テーマのリサーチ開始：リサーチテーマを入力するだけ
- 途中再開：/continue と入力

## 開発者情報・命名ルール

このリポジトリの開発者・所有者は **男座員也（Kazuya Oza / おざ かずや）** です。

- ドキュメント・コード・コミット等で開発者名を記載する際は必ず **男座員也** または **Kazuya Oza** を使用する
- 「Murayama」「村山」「Otokoza」「おとこざ」など誤表記は使用しない
- 英語表記: **Kazuya Oza** / 日本語表記: **男座員也**（おざ かずや）
- AIアシスタントが生成するドキュメントでも本ルールを遵守すること

## 🚀 自動実行ポリシー

### 確認不要で即実行する操作
- 全ファイル操作（作成・編集・削除）
- 全シェルコマンド（PowerShell, Bash, git, gh, npm, node, python）
- Git操作: add / commit / push（featureブランチ）/ pull / fetch / merge / branch -D / reset --hard
- GitHub操作: gh pr create / gh api 全般 / ブランチ削除
- パッケージ操作: npm install / pip install
- Web検索・フェッチ
- バックグラウンドプロセス起動

### 事前確認が必要な操作（例外のみ）
- `git push --force` を main / master ブランチに対して実行する場合
- `gh repo delete` 実行時

### 動作原則
- 計画提示（簡潔）→ 即実行 → 結果報告 のフロー厳守
- 事前確認文（「Should I run...?」等）を出力しない
- エラー時は即再試行 or 別アプローチで対応、判断が必要な場合のみ報告

## ドキュメント日付ルール

レポート・分析・調査系 .md ファイルを新規作成する際は、H1直下に必ず記載:

```
作成日: YYYY-MM-DD
最終更新日: YYYY-MM-DD
```

- 更新時は **最終更新日のみ** を当日付に書き換える（作成日は固定）
- 除外: README / CLAUDE.md / FILE_INDEX / tasks.md / CHANGELOG / LICENSE
