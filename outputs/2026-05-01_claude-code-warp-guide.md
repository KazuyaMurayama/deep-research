# Claude CodeをWarpで使う：Web版ユーザー向け移行・補完ガイド

> 対象読者：Web版Claude Codeを使っているCLI初心者・複数GitHubリポジトリを持つ開発者  
> 作成日：2026-05-01

---

## 第1章：はじめに ― なぜWeb版ユーザーがWarp + Claude Code CLIに移行するのか

### Web版Claude Codeでできないこと

Web版（ブラウザ）でClaude Codeを使っていると、次の壁にぶつかる。

| 制約 | 詳細 |
|------|------|
| ローカルファイルに触れない | ブラウザ経由では自分のPCのファイルを直接読み書きできない |
| 複数リポジトリの横断が困難 | 別リポジトリの内容を把握させるにはコピー&ペーストが必要 |
| Git操作ができない | `git commit` や `git push` をClaude自身に実行させられない |
| 外出先スマホからの操作が不可 | ブラウザを開いても、進行中の作業には戻れない |
| 長時間タスクの監視ができない | バックグラウンド実行しつつ進捗を確認する手段がない |
| ターミナル出力との統合がない | コマンド実行結果をそのままClaude Codeに見せられない |

### Warp + Claude Code CLIで解決できること

ローカルにインストールしたClaude Code CLIをWarpターミナルで実行することで、上記の制約がすべて解消する。

- **ローカルファイルを直接編集**：プロジェクトフォルダ内のファイルをClaude Codeが読み書き・作成・削除する
- **複数リポジトリを横断**：親ディレクトリから起動するだけで複数リポを参照できる
- **Git操作を自律実行**：`git add / commit / push` まで指示するだけでClaudeが行う
- **外出先スマホから監視・追加指示**：WarpのRemote Control機能で外出中でも作業を確認できる
- **ターミナル履歴との連携**：Warpのブロック機能でコマンド結果を整理・参照できる

### 3層運用モデル

```
┌─────────────────────────────────────────────────────┐
│  Layer 3: スマホ Remote Control（外出先）              │
│  ・WarpのRemote Controlリンクをスマホブラウザで開く     │
│  ・PC上のセッションを監視・コマンド承認・追加指示         │
│  ・アプリインストール不要                              │
├─────────────────────────────────────────────────────┤
│  Layer 2: Warp + Claude Code CLI（実装の主戦場）       │
│  ・ローカルファイル編集・Git操作・複数リポ横断            │
│  ・Warpのブロック・タブ・音声入力で生産性向上             │
│  ・長時間の自律タスク実行                              │
├─────────────────────────────────────────────────────┤
│  Layer 1: Web版Claude Code（軽い壁打ち）               │
│  ・アイデア整理・設計レビュー・コード断片の質問           │
│  ・インストール不要ですぐ使える                         │
│  ・実装には向かない（ファイル編集不可）                  │
└─────────────────────────────────────────────────────┘
```

**使い分けの原則**：設計・質問はWeb版、実装・自動化・外出監視はWarp + CLI + Remote Controlで行う。

---

## 第2章：役割分担 ― Claude CodeとWarpは何が違うか

### Claude Code = AIコーディングエージェント本体

Claude Code CLIは、Anthropicが提供する自律型コーディングエージェントだ。

- **ファイル編集**：プロジェクト内のファイルを自律的に読み書き・作成・削除
- **コマンド実行**：`npm test` や `git commit` などをClaude自身が実行し、結果を観察して次の判断をする
- **セッション継続**：前回の会話履歴を `claude -c` で復元して長期タスクを継続できる
- **サブエージェント生成**：大規模タスクを並列サブエージェントに分割して処理
- **CI/スクリプト統合**：`claude -p 'タスク' --permission-mode auto` で非対話型バックグラウンド実行

公式ドキュメント：[Claude Code 概要](https://code.claude.com/docs/en/overview) ／ [クイックスタート](https://code.claude.com/docs/en/quickstart)

### Warp = ターミナルGUI（Claude Codeの「器」）

Warpはモダンなターミナルアプリ（Agentic Development Environment）だ。Claude Codeの中身には関与せず、使いやすい「器」として機能する。

- **Blocks UI**：コマンドと出力を「ブロック」として管理。ブロック単位でコピー・共有・ブックマークできる
- **音声入力**：Wispr Flowによるリアルタイム音声認識。マイクボタンまたはホットキーで発動
- **タブ・スプリットペイン**：複数のClaude Codeセッションを並列管理
- **Remote Control**：実行中のClaude Codeセッションをスマホブラウザから監視・操作
- **通知**：Claude Codeの完了・エラーをデスクトップ通知で受け取る
- **コードレビューパネル**：Claude Codeが生成した差分をインラインコメント付きで確認

Warpドキュメント：[docs.warp.dev](https://docs.warp.dev/)

### WarpのAgent Modeとの違い（競合しない・補完関係）

Warpには独自の「Agent Mode」も搭載されている。Claude Code CLIとの違いを整理する。

| 比較項目 | Warp Agent Mode | Claude Code CLI |
|---------|----------------|----------------|
| 役割 | 「どのコマンドを実行すべきか」を提案するUI | コマンドを自律実行し、結果を見て次の判断をする |
| 自律実行 | 提案→人間が承認して実行 | 多段階を自動で繰り返す（自律的） |
| 多ファイル編集 | 限定的 | 得意（複数ファイルを一度に変更） |
| 対応モデル | GPT系・Gemini等の複数モデルに対応 | Anthropicモデル（Sonnet/Opus）のみ |
| CI/バックグラウンド実行 | 不可 | 可（`claude -p ... &`） |
| 向いている用途 | 「このエラーを直すコマンドは？」の確認 | 「この機能を実装して」の丸投げ実行 |

**重要**：両者は競合せず補完関係にある。Claude Code CLIをWarp内で実行することで、WarpのUI機能（音声入力・Remote Control・ブロック管理）をClaude Codeに上乗せできる。

詳細比較：[Warp vs Claude Code](https://www.warp.dev/university/getting-started/warp-vs-claude-code)

### 組み合わせのメリット一覧

- できる ✓ ローカルファイルのフル編集（Claude Code）
- できる ✓ 複数リポジトリ横断（Claude Code + --add-dir）
- できる ✓ 音声でClaude Codeに指示（Warp Agent Mode経由）
- できる ✓ コマンド履歴のブロック管理（Warp Blocks）
- できる ✓ スマホからセッション監視・操作（Warp Remote Control）
- できる ✓ 複数セッションの並列実行（Warpタブ）
- できる ✓ トークン・コスト確認（Claude Code `/usage` `/context`）

---

## 第3章：セットアップ ― WarpにClaude Code CLIを導入する

### 3-1. Warpのインストール

**Mac**

1. [https://www.warp.dev/download](https://www.warp.dev/download) にアクセスする
2. 「Download for Mac」をクリックして `.dmg` ファイルをダウンロードする
3. `.dmg` を開き、Warp.appをアプリケーションフォルダにドラッグする
4. Launchpadまたはスポットライトから「Warp」を起動する

または、Homebrewを使う場合：

```bash
brew install --cask warp
```

**Windows**

1. [https://www.warp.dev/download](https://www.warp.dev/download) にアクセスする
2. 「Download for Windows」をクリックして `.exe` インストーラーをダウンロードする
3. インストーラーをダブルクリックして手順に従いインストールする
4. スタートメニューから「Warp」を起動する

**Linux**

1. [https://www.warp.dev/download](https://www.warp.dev/download) にアクセスする
2. ディストリビューションに合わせて `.deb`（Debian/Ubuntu）または `.rpm`（Fedora/RHEL）をダウンロードする
3. 以下のコマンドでインストールする：

```bash
# Debian / Ubuntu
sudo dpkg -i warp-terminal_*.deb

# Fedora / RHEL
sudo rpm -i warp-terminal_*.rpm
```

4. アプリメニューまたはターミナルから `warp-terminal` を起動する

### 3-2. Warpの初期設定

1. Warpを初めて起動すると、アカウント作成画面が表示される
2. 「Sign up」からメールアドレス、またはGitHub/Googleアカウントで登録する
3. 無料プランで開始できる（料金プランは [https://www.warp.dev/pricing](https://www.warp.dev/pricing) を参照）
4. ログイン完了後、通常のターミナル画面（Terminal Mode）が表示される

### 3-3. Claude Code CLIのインストール

**前提確認：Claude subscriptionの準備**

Claude CodeにはClaude Pro / Max / Team / Enterpriseのサブスクリプション、またはAnthropic Consoleアカウントが必要だ。事前に [https://claude.ai](https://claude.ai) でプランを確認しておく。

**インストールコマンド**

Warpのターミナルを開き、OSに応じたコマンドを実行する。

```bash
# Mac / Linux
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows（PowerShellで実行）
irm https://claude.ai/install.ps1 | iex
```

**インストールの確認**

```bash
claude --version
```

バージョン番号が表示されればインストール成功だ。

**認証（初回ログイン）**

1. Warpで以下のコマンドを実行する：

```bash
claude auth login
```

2. ブラウザが自動で開き、Claude.aiのログイン画面が表示される
3. アカウントでサインインして認証を完了する
4. ターミナルに「Logged in as [メールアドレス]」と表示されれば完了

公式クイックスタート：[https://code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart)

### 3-4. 動作確認

1. バージョン確認：

```bash
claude --version
```

2. プロジェクトに移動してClaude Codeを起動：

```bash
cd ~/projects/my-project
claude
```

3. 初回は利用規約への同意が求められる。画面の指示に従って進む
4. プロンプト `>` が表示されたら完了。「このプロジェクトの構造を説明して」と入力してみる

**Warp統合プラグイン（任意・推奨）**

Claude Codeセッション起動時にWarpが自動でチップを表示するが、手動インストールも可：

```bash
/plugin marketplace add warpdotdev/claude-code-warp
/plugin install warp@claude-code-warp
```

参考：[Warp公式セットアップガイド](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) ／ [Claude Code クイックスタート](https://code.claude.com/docs/en/quickstart)

---

### 第1〜3章まとめ：できること評価

| 機能 | 評価 |
|------|------|
| ローカルファイルの直接編集 | できる ✓ |
| 複数リポジトリの横断参照 | できる ✓ |
| Git操作の自律実行 | できる ✓ |
| スマホからの外出先監視 | 部分的 △（PC上のセッション監視のみ、新規起動は不可） |
| 音声入力でClaude Codeに指示 | できる ✓（Warp Agent Mode経由） |
| コマンド履歴のブロック管理 | できる ✓ |
| 複数セッションの並列実行 | できる ✓ |

> ※ 本ガイドは第4〜7章に続く（Part B以降）
