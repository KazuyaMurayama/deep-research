## 🛠 Part 1：セットアップ — 3分でインストール完了

### 推奨インストール方法（2026年版）

**Native Installer（推奨）** が2026年から正式推奨です。Node.js不要で、macOS / Windows / Linuxすべてに対応します。

#### macOS / Linux

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

#### Windows（PowerShell）

```powershell
irm https://claude.ai/install.ps1 | iex
```

#### npm経由（Node.js環境がある場合）

```bash
npm install -g @anthropic-ai/claude-code
```

> ⚠️ **絶対にやってはいけないこと**：`sudo npm install -g` は権限・セキュリティ問題を引き起こします。公式も明確に非推奨。

### システム要件

| 項目 | 要件 |
|-----|------|
| OS | macOS / Windows / Linux |
| Shell | Bash / Zsh / PowerShell / CMD |
| Node.js | 18以上（npm経由の場合のみ） |
| ネット環境 | 必須（オフライン動作不可） |

### バージョン確認

```bash
claude --version
```

最新を保つには：

```bash
claude --version  # 現在のバージョン
# Native Installerなら自動更新（latest / stable から選択可）
```

### 初回認証

```bash
claude
```

初回起動でブラウザが開き、Anthropicアカウントでログインします。**Pro / Max サブスクリプション**または**API Key**が必要です。

| 認証方式 | おすすめ層 | 月額目安 |
|---------|---------|---------|
| Pro サブスク | 個人開発者・学習用途 | $20 |
| Max 5x | 1日中使うエンジニア | $100 |
| Max 20x | パワーユーザー・多Agent並列 | $200 |
| API Key | 法人・従量課金重視 | 使用量 |

### モデル選択

```bash
claude
> /model
# Claude Opus 4.7 / Sonnet 4.6 / Haiku 4.5 などから選択
```

> 💡 **おすすめ**：通常作業は **Sonnet 4.6**、複雑な設計は **Opus 4.7**、軽い処理は **Haiku 4.5** で使い分けるとコスト効率◎。

📚 公式ドキュメント：[Advanced setup](https://code.claude.com/docs/en/setup)

---
