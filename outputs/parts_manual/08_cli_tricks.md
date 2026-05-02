## ⚡ Part 8：CLIならではの活用例

ここからは CLI 真骨頂。Web版では絶対に再現できない高度な機能群です。

### 1. Subagents：並列タスク実行

Subagentsは「**独立したコンテキストを持つ別Claude**」を呼び出す機能。

```
> 3つのsubagentに並列調査させて：
  - APIレイヤーの構造
  - データモデル
  - デプロイ設定
```

メインClaudeは**サマリだけ受け取る**ので、コンテキスト消費を抑えながら3倍速で進む。

#### subagent定義（.claude/agents/）

`.claude/agents/security-reviewer.md`：

```markdown
---
description: セキュリティ脆弱性のレビュー専門
tools: Read, Grep, Bash
---

あなたはセキュリティ専門のレビュアーです。
SQLインジェクション・XSS・認証バイパスを重点的にチェック。
```

呼び出し：

```
> security-reviewer に最新のPRをチェックさせて
```

### 2. MCP：3,000+の外部ツール接続

MCPは「Claude を任意の外部システムにつなぐ」プロトコル。

#### よく使うMCPサーバー

| サーバー | 用途 |
|---------|------|
| `github` | PR・Issue管理 |
| `slack` | メッセージ送受信 |
| `postgres` | DB操作（読み取り限定推奨） |
| `puppeteer` | ブラウザ自動化 |
| `sentry` | エラー監視連携 |

#### 追加方法

```bash
claude mcp add github
# または
> /plugin add github
```

設定済みのMCPは `/mcp` で確認：

```
> /mcp
# 接続中のサーバー一覧
```

### 3. カスタム Slash Commands

`.claude/commands/` にMarkdownファイルを置くだけで `/` 始まりのコマンドになります。

`.claude/commands/review.md`：

```markdown
---
description: PR レビュー専用ワークフロー
---

このリポジトリのコーディング規約に基づき、
直近のPRを以下の観点でレビューせよ：
1. テストカバレッジ
2. 命名規則
3. パフォーマンス影響
```

呼び出し：

```
> /review
```

#### Skills との違い（2026年版）

| 機能 | 場所 | 呼び出し |
|-----|------|---------|
| **Slash Command** | `.claude/commands/` | 明示的に `/name` |
| **Skill** | `.claude/skills/` | Claudeが自動判断 |

> 💡 **2026年の推奨**：明示呼び出しは Slash Command、自動発火はSkillで使い分ける。

### 4. Headless Mode：CI/CDに組み込む

`--bare` フラグでスクリプト実行が可能。

```bash
# GitHub Actionsでの例
claude --bare --prompt "READMEを最新化" > result.md
```

これで**Claude をbashの一部品として**扱えます。CI失敗時の自動修正、定期メンテナンスタスクなどに最適。

### 5. /loop：定期実行

```
> /loop 30m /check-pr-status
```

30分ごとに `/check-pr-status` を実行。長時間プロジェクトの「監視員」として使えます。

### 6. /simplify：並列コードレビュー

```
> /simplify
```

複数subagentが**同時に**コードを再利用性・品質・効率性の観点でレビューし、改善提案を統合します。

### 7. /team-onboarding：チーム展開

```
> /team-onboarding
```

このプロジェクトの慣行・MCP設定・スラッシュコマンドを反映した**新メンバー向けガイド**を自動生成。

📚 公式ドキュメント：[Best Practices](https://code.claude.com/docs/en/best-practices)

---

### 活用パターン・チートシート

| やりたいこと | 一発コマンド |
|------------|------------|
| プロジェクト全体把握 | `> このリポジトリの構造を要約` |
| バグ修正サイクル | `> Issue #X を見て修正→PR作成` |
| 大規模リファクタ | `> /plan で設計→subagent並列実装` |
| ドキュメント生成 | `> README/CHANGELOG/DEVELOPMENT.md を作って` |
| テスト自動化 | `> 未テストの関数すべてに単体テスト追加` |
| コードレビュー | `> /simplify` |
| 依存関係更新 | `> 依存をアップデートして破壊的変更の影響をテスト` |

---
