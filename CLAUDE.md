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
