# Claude CodeをWarpで使う：Web版ユーザー向け移行・補完ガイド

> 対象読者：Web版Claude Codeを使っているCLI初心者・複数GitHubリポジトリを持つ開発者  
> 作成日：2026-05-01

---

## 第1章：はじめに

### Web版の制約とWarp + CLIで得られること

| Web版の制約 | Warp + Claude Code CLIで解決 |
|------------|----------------------------|
| ローカルファイルに触れない | ファイルを直接読み書き・削除できる |
| 複数リポジトリの横断が困難 | `--add-dir` や親CLAUDE.mdで横断参照 |
| Git操作ができない | `git add/commit/push` まで自律実行 |
| 外出先スマホからの操作が不可 | Remote Controlでスマホブラウザから監視・承認 |
| ターミナル出力との統合がない | Warp BlocksでコマンドとClaude出力を一体管理 |

### 3層運用モデル

```
Layer 3: スマホ Remote Control（外出先）
  → PC上のセッションを監視・承認・追加指示。アプリ不要。

Layer 2: Warp + Claude Code CLI（実装の主戦場）
  → ローカルファイル編集・Git・複数リポ横断。音声・タブ・ブロックUIで生産性向上。

Layer 1: Web版Claude Code（軽い壁打ち）
  → アイデア整理・設計レビュー。実装には向かない。
```

---

## 第2章：役割分担

### Claude Code vs Warp — 何が違うか

| 比較項目 | Claude Code CLI | Warp |
|---------|----------------|------|
| 本質 | 自律型AIコーディングエージェント | モダンターミナルGUI（「器」） |
| できること | ファイル編集・コマンド実行・Git・サブエージェント生成 | Blocks UI・音声入力・タブ・Remote Control |
| 単体利用 | 可（他のターミナルでも動く） | 可（Claude Codeなしでも普通のターミナルとして使える） |
| 組み合わせの効果 | WarpのUI機能をClaude Codeに上乗せできる |

### Warp Agent Modeとの違い（競合しない）

| 比較項目 | Warp Agent Mode | Claude Code CLI |
|---------|----------------|----------------|
| 役割 | 「どのコマンドを実行すべきか」を提案 | コマンドを自律実行し、結果を見て次の判断をする |
| 自律実行 | 提案→人間が承認 | 多段階を自動で繰り返す |
| CI/バックグラウンド実行 | 不可 | 可（`claude -p ... &`） |
| 向いている用途 | 「このエラーを直すコマンドは？」の確認 | 「この機能を実装して」の丸投げ |

---

## 第3章：セットアップ

### 3-1. インストール

**Warp**：[warp.dev/download](https://www.warp.dev/download) からOS別インストーラーをダウンロード。Macは `brew install --cask warp` でも可。

**Claude Code CLI**：

```bash
# Mac / Linux
curl -fsSL https://claude.ai/install.sh | bash

# Windows（PowerShell）
irm https://claude.ai/install.ps1 | iex
```

**認証**：

```bash
claude auth login
# ブラウザが開き、Claude.aiでサインイン → "Logged in as ..." で完了
```

> Claude Pro / Max / Team / Enterprise サブスクリプションが必要。

### 3-2. 動作確認

```bash
claude --version          # バージョン表示されればインストール成功
cd ~/projects/my-project
claude                    # プロジェクト内で起動
```

### 3-3. Warp統合プラグイン（任意・推奨）

Warp起動時に通知チップから自動インストールできる。手動の場合：

```bash
# ターミナルから
claude plugin marketplace add warpdotdev/claude-code-warp
claude plugin install warp@claude-code-warp
```

参考：[Warp公式セットアップガイド](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) ／ [クイックスタート](https://code.claude.com/docs/en/quickstart)

---

## 第4章：7つのニーズ別 実現手順

### 4-1. 複数GitHubリポジトリを横断操作する ○

**方法A：`--add-dir` で複数ディレクトリを同時指定（v2.1.20+）**

```bash
cd ~/projects/frontend
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude --add-dir ../api-repo --add-dir ../shared
```

**方法B：CLAUDE.mdを親ディレクトリに配置**

```
~/projects/
├── CLAUDE.md       ← 共通ルール（言語・テスト・横断ルール等）
├── frontend/
├── api/
└── shared/
```

Claude Codeはディレクトリツリーを上向きに走査してCLAUDE.mdを自動読み込みする。

**方法C：Warpの複数タブで並行管理**

```bash
# Mac: Cmd+T / Windows・Linux: Ctrl+T で新規タブ
# タブ1
cd ~/projects/frontend && claude
# タブ2
cd ~/projects/api && claude
```

参考：[Claude Code メモリ設定](https://code.claude.com/docs/en/memory)

---

### 4-2. 音声で入力する ○

Warpの全入力インターフェース（Agent Mode・Terminal Mode含む）で音声入力が利用できる。Wispr Flowがリアルタイムで書き起こし、Claude Code CLIへ送信する。

**有効化**：入力欄右のマイクボタン、または `Fn`（macOS）/ `Alt+右矢印`（Windows/Linux）  
**ホットキー変更**：Settings → AI → Voice（推奨：`Option+V`）

```bash
/voice    # セッション内でも音声入力のON/OFFが可能
```

日本語対応。専門用語（関数名等）は英語発音か手動修正が効率的。  
**制限**：Warpなしの環境（他ターミナル・サーバー）では使用不可。

公式：[Warp Voice 入力](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice)

---

### 4-3. ローカルPCのファイルを編集する ○（Web版との最大の差分）

| 比較項目 | Web版 | CLI（Warp経由） |
|---------|------|--------------|
| ローカルファイルへのアクセス | ✗ | ○ 直接読み書き |
| Git操作の自律実行 | ✗ | ○ add/commit/push |
| ターミナルコマンド実行 | ✗ | ○ npm/pytest/make等 |

**権限モードの選択**：

```bash
claude                              # デフォルト：変更のたびに確認（初心者向け）
claude --permission-mode auto       # 許可ルール範囲内で自動実行
claude --dangerously-skip-permissions  # ⚠️ コンテナ・CI専用。通常環境では非推奨
```

参考：[Claude Code 権限モード](https://code.claude.com/docs/en/permission-modes)

---

### 4-4. 履歴をわかりやすく見る ○

**Warp Blocks**：1コマンドの入力＋出力が「ブロック」として自動グルーピングされる。ブロック単位でコピー・ブックマーク・コンテキストへの添付が可能。

参考：[Warp Blocks 基本操作](https://docs.warp.dev/terminal/blocks/block-basics)

**Claude Codeのセッション再開**：

```bash
claude -c    # 直前のセッションをすぐ再開
claude -r    # 過去のセッション一覧から選択して再開
```

セッション内コマンド：

```bash
/resume     # セッション選択ピッカーを起動（一次コマンド）
/continue   # /resume のエイリアス（同じ動作）
```

---

### 4-5. コンテキスト・トークン使用状況を確認する ○

```bash
/usage      # セッションコスト・プラン使用量を表示
/context    # コンテキストウィンドウの使用状況をカテゴリ別に可視化
/compact    # 会話を自動要約してコンテキストを解放（指示付き圧縮も可）
/status     # Claudeバージョン・接続モデル・アカウント確認
/clear      # コンテキストを完全リセット
```

コスト上限付きバッチ実行：

```bash
claude -p 'バグを修正して' --max-turns 20 --permission-mode auto
```

参考：[Claude Code コスト管理](https://code.claude.com/docs/en/costs) ／ [CLIリファレンス](https://code.claude.com/docs/en/cli-reference)

---

### 4-6. 多数のタブで並行セッションを管理する ○

```bash
# 新しいタブ：Mac=Cmd+T / Win・Linux=Ctrl+T
# 縦分割：Mac=Cmd+D / 横分割：Mac=Cmd+Shift+D
# ペイン間移動：Cmd+Option+←/→（Mac）/ Ctrl+Alt+←/→（Win/Linux）
```

各タブで独立したClaude Codeセッションを起動できる。

**注意事項**：
- 同一ファイルへの同時編集は後書きが前の変更を上書きする → 共有ファイルは1タブで担当固定
- 同一リポジトリを複数タブで操作する場合はタブごとにブランチを分ける
- Proプランは並行セッション分だけトークン消費が増える

参考：[Warp ドキュメント](https://docs.warp.dev/)

---

### 4-7. スマホから監視・指示を出す △（PC起動必要）

PC上で起動中のClaude Codeセッションにスマホのブラウザから接続。アプリ不要。

**セットアップ（PC側）**：

1. Warpでセッションを起動する
2. エージェントユーティリティバーの **`/remote-control`** チップをクリック
3. 自動生成されたURLをスマホへ送りブラウザで開く

| スマホでできること | スマホでできないこと |
|------------------|-------------------|
| セッションのリアルタイム閲覧 | 新規セッションの起動 |
| テキスト入力・追加指示の送信 | ファイルブラウザ操作 |
| 承認プロンプトへの ✓/✗ 応答 | キーボードショートカット |

公式：[Warp Remote Control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control)

---

## 第5章：実運用Tips

### CLAUDE.mdテンプレート（最小構成）

```markdown
# プロジェクト名

## 基本情報
- 言語: TypeScript 5.x / Node.js 20
- テスト: Jest（npm test）
- リント: ESLint（npm run lint:fix）

## 必須ルール
- コミット前に npm test && npm run lint を実行
- 型の any は禁止（unknown を使う）
```

セッション内で `/memory` を実行するとエディタでその場で編集できる。

### コスト削減のポイント

| テクニック | 効果 |
|-----------|------|
| `/compact` を定期実行 | 不要な中間出力を要約してトークン節約 |
| CLAUDE.mdを100行以内に保つ | 毎ターンのシステムトークン削減 |
| 探索と実装でセッションを分ける | 探索中の出力が実装コンテキストを汚染しない |
| `--max-turns` で上限設定 | バッチ実行のコスト上限を制御 |

### よくある失敗と対処

```bash
# 権限エラー → --permission-mode auto か /permissions でルール追加
claude --permission-mode auto

# コンテキスト溢れ → /compact で圧縮、または /clear で新規起動
/compact 重要な決定事項と変更ファイル名だけ保持

# git競合 → タブごとに別ブランチを使う
git checkout -b feature/fix-A   # タブ1
git checkout -b feature/fix-B   # タブ2
```

---

## 第6章：できること早見表

| ニーズ | Web版 | Warp + CLI | Remote Control |
|--------|------|------------|---------------|
| ローカルファイル編集 | ✗ | ✓ | ✗ |
| 複数リポ横断 | △ | ✓ | △ |
| 音声入力 | ✗ | ✓ | ✗ |
| 履歴管理 | △ | ✓ | 閲覧のみ |
| トークン確認 | ✗ | ✓ | 閲覧のみ |
| 並行セッション | △ | ✓ | 監視のみ |
| 外出先操作 | ✓（ブラウザ） | △（PC起動必要） | ✓（PC起動必要） |

---

## 第7章：まとめ

### 学習ロードマップ

| 週 | 目標 | 操作 |
|----|------|------|
| Week 1 | 基本操作 | `claude` 起動・ファイル編集・`/usage` |
| Week 2 | 複数リポ・CLAUDE.md | `--add-dir`・CLAUDE.md作成・`claude -r` |
| Week 3 | 音声・並行セッション | Voice・マルチタブ・`/compact` |
| Week 4 | Remote Control・フル3層 | スマホ監視・承認・バックグラウンド実行 |

### 参考リンク集（公式）

| ドキュメント | URL |
|------------|-----|
| Claude Code 概要 | [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) |
| Claude Code クイックスタート | [code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart) |
| Claude Code CLIリファレンス | [code.claude.com/docs/en/cli-reference](https://code.claude.com/docs/en/cli-reference) |
| Claude Code 権限モード | [code.claude.com/docs/en/permission-modes](https://code.claude.com/docs/en/permission-modes) |
| Claude Code メモリ・CLAUDE.md | [code.claude.com/docs/en/memory](https://code.claude.com/docs/en/memory) |
| Claude Code コスト管理 | [code.claude.com/docs/en/costs](https://code.claude.com/docs/en/costs) |
| Warp セットアップガイド | [docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) |
| Warp Remote Control | [docs.warp.dev/agent-platform/third-party-agents/remote-control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control) |
| Warp 音声入力 | [docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice) |
| Warp ドキュメント（トップ） | [docs.warp.dev](https://docs.warp.dev/) |
