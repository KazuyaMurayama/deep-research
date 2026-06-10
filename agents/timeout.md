# タイムアウト対策（全エージェント共通）

## 5つのコア原則

1. **Local-first, push-last**：ファイル生成は `Write`/`Edit` でローカル完結→最後に `Bash` で `git add && commit && push`。`mcp__github__push_files` は初期構築時のみ
2. **1 commit = 1〜3 files**：push_files の files 配列は最大3要素。長文MDと他メタ更新は必ず分離
3. **長文は分割生成**：1ターンあたり1500トークン程度を目安に、アシスタント出力を区切る。必要なら commit を挟んで新ターンで継続
4. **WebSearch 結果は即座に要約抽出**：raw結果を session.json に貼らず `{source, tier, key_info}` の形に圧縮してから保存
5. **session.json は軽量ポインタ化**：収集データ本体は `session/research_data.json` に分離、session.json にはパスのみ

## エージェント別チェックリスト

### Agent1（研究設計）
- [ ] session.json は骨格のみ（search_queries 最大10件）
- [ ] 完了時に即 `git commit`

### Agent2（Web調査）
- [ ] WebSearch は1クエリずつ実行（並列は2本まで）
- [ ] 結果は即サマリ化→`session/research_data.json` に追記
- [ ] 3クエリごとに commit

### Agent3（分析・統合）
- [ ] report_outline は session.json に構造化JSONで格納
- [ ] 本文生成は Agent4 に委譲（ここでは書かない）

### Agent4（レポート生成・公開）
- [ ] `Write` ツールで完全版MDをローカル作成（1ショット生成OK）
- [ ] `Bash` で commit → push（MCP経由は使わない）
- [ ] push失敗時リトライ：2s → 4s → 8s → 16s

## 障害時フォールバック

| 症状 | 対処 |
|------|------|
| `push_files` が大ペイロードでhang | Write → Bash push に即切替 |
| WebSearchで長文が溜まる | サブエージェント（Agent tool）に分離して context 隔離 |
| アシスタント出力が長くなりそう | セクション単位で commit を挟み、新ターンで再開 |
| ブランチ作成要求が来る | main 固定で上書き（CLAUDE.md ルール） |

## ルール違反チェック（作業開始時に必ず確認）

- ブランチ作成していないか
- push_files に1万文字以上のコンテンツを載せようとしていないか
- WebSearch raw結果を session.json に貼ろうとしていないか
