# Garry Tan（YC社長）のClaude Code設定を活用する

> 作成日：2026-05-11  
> 対象：Web版Claude Codeユーザー・複数GitHubリポ所有・CLI初心者

---

## 1. エグゼクティブサマリー

Y Combinatorの社長・Garry Tanは自身のClaude Code設定をOSSとして公開している。2つのリポジトリが中心だ。

| リポジトリ | スター | 一言説明 |
|-----------|--------|---------|
| [garrytan/gstack](https://github.com/garrytan/gstack) | 93,100+ | Claude Code用スキルキット（23専門スキル＋8パワーツール） |
| [garrytan/gbrain](https://github.com/garrytan/gbrain) | 14,600+ | AIエージェント向け永続知識ベース（会議・メール等を自動蓄積） |

**今すぐ使える推奨アクション3点**

1. **gstackをインストール**：setupスクリプト1本でClaude Codeに23スキルが追加される。`/office-hours`・`/review`・`/qa` から始めるだけでよい
2. **gbrainのPGLiteローカルモードを試す**：30秒でセットアップ完了、複数リポの作業記録を横断検索できるようになる
3. **ETHOS.mdを読む**：Garry Tanの設計哲学が凝縮されており、自分のCLAUDE.mdに転用できる考え方が多い

---

## 2. 公開ファイル構成

### gstack の主要スキル

```
gstack/
├── CLAUDE.md               ← プロジェクト開発ルール（テスト・ビルド・セキュリティ）
├── ETHOS.md                ← 設計哲学（必読）
├── setup                   ← 一括インストールスクリプト（冪等）
├── office-hours/SKILL.md   ← YCスタイルのプロダクト検証
├── review/SKILL.md         ← スタッフエンジニアレベルのPRレビュー
├── plan-design-review/SKILL.md ← 実装前デザインを7次元評価
├── qa/SKILL.md             ← Playwright実ブラウザQA
├── ship/SKILL.md           ← リリース管理
└── docs/skills.md          ← 全スキル一覧リファレンス
```

**ワークフロー全体の流れ**

```
/office-hours（需要検証）
  → /plan-eng-review（技術設計レビュー）
  → /plan-design-review（UI/UX評価）
  → 実装
  → /review（PRレビュー）
  → /qa（ブラウザQA）
  → /ship（リリース）
  → /retro（振り返り）
```

### ETHOS.md の重要哲学

| 原則 | 内容 |
|------|------|
| **Boil the Lake** | 完全性が僅かなコストで実現できるなら、必ず完全に実装する |
| **Search Before Building** | 既存パターン→最新プラクティス→第一原理の順で探索してから実装 |
| **User Sovereignty** | AIは提案のみ。判断は常にユーザー。どのモデルが同意しても例外なし |
| **Build for Yourself** | 仮定的汎用解より、実際の自分の問題を解決する |

### セキュリティ設計（gstack）

- **多層プロンプトインジェクション防御**：ONNXクラシファイア（しきい値0.85）＋トランスクリプト解析＋カナリーパターン
- **安全ガード3コマンド**：`/careful`（破壊的コマンド警告）／`/freeze`（編集範囲制限）／`/guard`（両方有効化）
- **トークン上限警告**：160KB（約40Kトークン）でfeature bloat検出

---

## 3. ユーザープロフィール適合度評価

| 評価軸 | 適合度 | 理由 |
|--------|--------|------|
| CLI初心者への学習コスト | ★★★★☆ | setupスクリプト1本で完結、コマンドは `/review` など直感的 |
| Warp連携適性 | ★★★★★ | SessionStartフックがWarpのブロックUI・タブと相性良い |
| 複数GitHubリポ横断 | ★★★★★ | gbrain-repo-policyでリポ単位の信頼設定、gbrainで横断検索 |
| 外出先・モバイル操作 | ★★★☆☆ | SessionStartフックで状態自動復元。ただしgbrain設定は最初に必要 |
| Web版からの移行容易性 | ★★★★☆ | ETHOS哲学・SKILL.mdパターンはWeb版でも使える考え方 |

---

## 4. 移行ロードマップ

### Week 1：gstackのインストールと基本スキル習得

```bash
# gstackをインストール（~/.claude/skills/gstack に配置される）
git clone https://github.com/garrytan/gstack ~/.gstack
cd ~/.gstack
./setup
```

まず使うべき3スキル：

```
/office-hours    → 新機能・新プロジェクトを始める前にYCスタイルで需要検証
/review          → PRを出す前に並列サブエージェントが自動でレビュー
/careful         → 削除・上書き系の操作前に必ず付ける安全ガード
```

### Week 2：CLAUDE.mdをプロジェクトに設定

ETHOS.mdから自分のリポジトリに転用できる記述例：

```markdown
# [リポジトリ名] CLAUDE.md

## 基本哲学
- Search Before Building：実装前に既存パターンを必ず調査する
- 完全性が低コストで達成できるなら、必ず完全に実装する
- AIは提案のみ。最終判断は常に自分が行う

## テスト・品質
- コミット前に必ず [テストコマンド] を実行
- 破壊的操作は /careful を付けて実行

## 関連リポジトリ
- [他のリポジトリへの参照を記述]
```

### Week 3：gbrainで複数リポを横断記憶

```bash
# PGLiteローカルモード（最速・30秒で完了）
git clone https://github.com/garrytan/gbrain ~/.gbrain
cd ~/.gbrain && bun install && bun link

# Claude CodeにMCPサーバーとして登録
claude mcp add gbrain -- gbrain serve
```

各リポジトリへの信頼ポリシーを設定：

```json
// ~/.gstack/gbrain-repo-policy.json
{
  "~/projects/my-main-app": "read-write",
  "~/projects/shared-lib": "read-only",
  "~/projects/experiments": "deny"
}
```

### Week 4：SessionStartフックで毎セッション自動初期化

```bash
# --teamフラグでSessionStartフックを自動登録
./setup --team
# → 毎セッション開始時に gstack-session-update が実行される
# → Warpを開いてclaudeを起動するだけで最新状態に
```

---

## 5. CLI初心者向け：よく使うコマンドチートシート

| コマンド | タイミング | 効果 |
|---------|-----------|------|
| `/office-hours` | 新機能・新プロジェクト開始前 | YCスタイルで需要仮説を検証 |
| `/review` | PR作成前 | 並列サブエージェントが多角的にコードレビュー |
| `/plan-design-review` | UI変更前 | 7次元でデザインを評価 |
| `/careful` | 削除・DB操作・本番デプロイ前 | 破壊的コマンドに警告を追加 |
| `/freeze` | 特定ファイルを変更したくないとき | 編集範囲をロック |
| `/qa` | リリース前 | Playwrightで実ブラウザQA |

---

## 6. 複数GitHubリポジトリへの具体的な適用例

現在のリポジトリ構成がたとえば以下のような場合：

```
~/projects/
├── web-app/          ← Reactフロントエンド
├── api-server/       ← Node.js バックエンド
├── mobile-app/       ← React Native
└── deep-research/    ← このリポジトリ
```

gstack×gbrainで実現できること：

1. **横断記憶**：`web-app` で解決したバグの解法を `api-server` でのセッション中に参照できる
2. **プロジェクト間の設計整合**：`/office-hours` の出力（設計ドキュメント）が `~/.gstack/projects/` に蓄積され、後続プロジェクトで再参照できる
3. **自動レビュー**：全リポで `/review` を使うことで、スタッフエンジニアレベルのPRチェックが標準化される
4. **リポ別信頼管理**：本番APIのリポは `read-only`、実験リポは `read-write` と使い分けてリスクを制御

---

## 7. 参考リンク

| リソース | URL |
|---------|-----|
| garrytan/gstack | [github.com/garrytan/gstack](https://github.com/garrytan/gstack) |
| garrytan/gbrain | [github.com/garrytan/gbrain](https://github.com/garrytan/gbrain) |
| gstack 全スキル一覧 | [gstack/docs/skills.md](https://github.com/garrytan/gstack/blob/main/docs/skills.md) |
| gstack ETHOS（哲学） | [gstack/ETHOS.md](https://github.com/garrytan/gstack/blob/main/ETHOS.md) |
| gbrain セットアップ | [gbrain/INSTALL_FOR_AGENTS.md](https://github.com/garrytan/gbrain/blob/master/INSTALL_FOR_AGENTS.md) |
| gbrain×gstack統合 | [gstack/USING_GBRAIN_WITH_GSTACK.md](https://github.com/garrytan/gstack/blob/main/USING_GBRAIN_WITH_GSTACK.md) |
| TechCrunch 解説記事 | [Why Garry Tan's Claude Code setup has gotten so much love, and hate](https://techcrunch.com/2026/03/17/why-garry-tans-claude-code-setup-has-gotten-so-much-love-and-hate/) |
