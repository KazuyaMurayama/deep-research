# deep-research-forge CLAUDE.md

## このリポジトリの目的
任意テーマの高品質リサーチレポートを自動生成してGitHubに公開する。

## ルール（厳守）
- 本ファイルは軽量に保つ。詳細ルールはagents/配下を参照。
- 毎回プロンプト受信時にfile_index.mdとtasks.mdを参照してから開始する。
- session.jsonで進捗を保存し、途中停止時は/continueで再開できるようにする。
- ブランチ作成禁止。mainブランチのみで運用。
- コミットは各エージェント完了ごとに必ず実行する。
- 成果物はGitHub Contents APIでURLを取得し、ハイパーリンクで報告する。

## モデル使い分け
- Agent1（研究設計）：claude-opus-4-5（高度な論理推論）
- Agent2〜4（実行系）：claude-sonnet-4-5（コスト効率）

## エージェント実行順序
1. agents/01_research_architect.md
2. agents/02_web_researcher.md
3. agents/03_analyst_synthesizer.md
4. agents/04_quality_publisher.md

## タイムアウト対策
詳細ルールは **agents/timeout.md** を必ず参照。
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

## ファイル保存ルール
- 成果物・スクリプトは本リポジトリ内のみに保存。`C:\\Users\\user\\Desktop` への出力禁止（ユーザー明示指定時を除く）。

<!-- SKILLS_RULES_START -->
## Skill 起動ルール（v2.1 / 2026-05-29）
以下のスキルは **必須・スキップ禁止**。該当シーンでは SKILL.md を読んでから作業を開始すること。

- **調査トピックを受け取ったら最初に必ず** `.claude/skills/research-deep/SKILL.md` を読み、手順に従って並列 Web リサーチを実行する
- **複雑な多段タスクに着手する前に必ず** `.claude/skills/sp-writing-plans/SKILL.md` で計画を作成し、`.claude/skills/sp-executing-plans/SKILL.md` の手順で実行する
- **レポート・ドキュメントに図表が必要な時は必ず** `.claude/skills/mermaid-agents365/SKILL.md` を読んでからダイアグラムを作成する
- **アイデア出し・選択肢の洗い出しが必要な時は** `.claude/skills/sp-brainstorming/SKILL.md` を読んでから実施する
- **成果物の納品・コミット前、または品質チェック（QC）・レビューフェーズに入る時は必ず** `.claude/skills/sp-verification-before-completion/SKILL.md` のチェックリストを実行する
- **分析・レポートの品質チェック（QC）・レビュー・共有前は必ず** `.claude/skills/analysis-qa-checklist/SKILL.md` を読んでチェックリストを実施する
- **データ品質・整合性の確認が必要な時は必ず** `.claude/skills/data-quality-audit/SKILL.md` を読んで監査を実行する
<!-- SKILLS_RULES_END -->
