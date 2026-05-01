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

---

## 第4章：7つのニーズ別 実現手順

### 4-1. 複数GitHubリポジトリを横断操作する（ニーズ1）

**実現可否： ○完全対応**

複数のリポジトリを1つのClaude Codeセッションで扱う方法は3通りある。

#### 方法A：同一セッション内で `cd` 切替

最もシンプルな方法。セッションを起動したまま別リポジトリに移動できる。

```bash
cd ~/projects/repo-A
claude
# セッション内で別リポに移動する場合
# > ../repo-B/src/utils.ts を参照して、このファイルと整合性を確認して
```

#### 方法B：`--add-dir` フラグで複数ディレクトリを同時指定（v2.1.20+）

追加のリポジトリを起動時に指定する。各リポジトリのCLAUDE.mdも自動で読み込まれる。

```bash
# api-repo を追加リポジトリとして指定しながら frontend から起動
cd ~/projects/frontend
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude --add-dir ../api-repo
```

- `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` 環境変数を付けることで、追加ディレクトリのCLAUDE.mdも読み込まれる
- 複数のリポジトリを指定する場合は `--add-dir` を繰り返す：

```bash
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude --add-dir ../api-repo --add-dir ../shared
```

参考：[Claude Code メモリ設定](https://code.claude.com/docs/en/memory)

#### 方法C：CLAUDE.mdを親ディレクトリに配置してワークスペース横断

親ディレクトリにCLAUDE.mdを置くと、Claude Codeはディレクトリツリーを上向きに走査して自動で読み込む。

```
~/projects/
├── CLAUDE.md           ← ここに共通指示を書く（親ディレクトリ）
├── frontend/
│   ├── CLAUDE.md       ← フロントエンド固有の指示
│   └── src/
├── api/
│   ├── CLAUDE.md       ← API固有の指示
│   └── src/
└── shared/
    └── src/
```

親ディレクトリのCLAUDE.md記述例：

```markdown
# このワークスペース（~/projects）の共通ルール

## リポジトリ構成
- frontend/ : Reactフロントエンド（レスポンス形式は api/CLAUDE.md を参照）
- api/       : Node.js バックエンドAPI
- shared/    : 共有TypeScript型定義（型は shared/src/types/ を参照）

## 横断ルール
- APIの変更が必要な場合は frontend/ と api/ を必ず同時に修正すること
- コミット前に各リポで npm test && npm run lint を実行
- shared/ の型変更時は全リポへの影響を確認すること
```

- 大規模モノレポでは `claudeMdExcludes` 設定で不要なCLAUDE.mdを除外してトークン節約が可能
- セッション内で `/memory` コマンドを実行するとCLAUDE.mdをその場で編集できる

#### Warpの複数タブで各リポを並行管理

Warpのタブ機能を使うと、複数リポジトリを別々のタブで同時に操作できる。

```bash
# Mac：Cmd+T　/ Windows・Linux：Ctrl+T で新規タブを開く
# タブ1：frontendリポ
cd ~/projects/frontend && claude

# タブ2（新規タブ）：apiリポ
cd ~/projects/api && claude
```

- 縦タブUIでタブに名前を付けて「どのタブがどのリポか」を識別しやすくなる
- スプリットペインで同一ウィンドウ内に複数セッションを並列表示することも可能

参考：[Warp タブ・ウィンドウ](https://docs.warp.dev/terminal/windows-and-tabs)

---

### 4-2. 音声で入力する（ニーズ2）

**実現可否： ○完全対応（Warp Agent Mode経由）**

音声入力はWarpのAgent Mode内の機能として提供されている。Claude Code CLIへの入力をWarpが音声認識（Wispr Flow）して橋渡しする構成だ。

#### Warp音声入力の有効化と使い方

1. WarpでAgent Modeを開く（`Ctrl+I` または入力欄右のスパークルアイコン）
2. **Settings → AI → Voice** を開く
3. ホットキーを設定する（デフォルト：マイクボタンのクリック。推奨：`Option+V`（Mac）などに割り当て）
4. ホットキーを押しながら話す、または入力欄右のマイクボタンをクリックして話す
5. 話し終えると Wispr Flow がリアルタイムで文字起こしし、入力欄に挿入される
6. `Enter` を押してClaude Codeへ送信する

`/voice` スラッシュコマンドでも音声入力のトグルが可能：

```bash
/voice
# 音声入力モードがON/OFFされる
```

公式ドキュメント：[Warp Voice 入力](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice)

#### 日本語対応について

Wispr Flowは多言語に対応しており、日本語での音声入力も可能だ。ただし以下の点に注意する。

- 専門用語（関数名・ライブラリ名など）は英語発音か、入力後に手動修正が効率的
- 静かな環境での録音精度が高い
- 長い指示文は短い文節に分けて話すと認識精度が上がる

#### 重要な制限事項

音声入力はWarpのAgent Mode経由で動作するため：

- **Warpがインストールされていない環境では音声入力は利用できない**
- Claude Code CLI単体（他のターミナルやサーバー上）には音声入力機能がない
- WarpがWispr Flowで文字起こしした内容をClaude Code CLIへ送信する「橋渡し構成」である
- アンチアビューズ制限が設けられており、詳細は非公開

---

### 4-3. ローカルPCのファイルを編集する（ニーズ3）

**実現可否： ○完全対応（Web版との最大の差分）**

#### Web版 vs CLI版の比較

| 比較項目 | Web版Claude Code | Claude Code CLI（Warp経由） |
|---------|----------------|--------------------------|
| ローカルファイルへのアクセス | ✗ 不可（ブラウザのサンドボックス内のみ） | ○ 直接読み書き・作成・削除が可能 |
| Git操作の自律実行 | ✗ 不可 | ○ `git add/commit/push` まで実行 |
| プロジェクト全体の把握 | △ コピー&ペーストが必要 | ○ ディレクトリ構造を自動スキャン |
| ファイル横断的な変更 | ✗ 困難 | ○ 複数ファイルを一度に変更可能 |
| ターミナルコマンド実行 | ✗ 不可 | ○ npm/pytest/makeなどをClaude自身が実行 |

#### 基本的な使い方

```bash
cd ~/my-project
claude
```

起動後に日本語でそのまま指示できる：

```
> このディレクトリのREADME.mdを更新して
> src/index.ts の型エラーを全部修正して
> プロジェクト全体のimportを整理して（相対パスから絶対パスに統一）
> テストを実行して、失敗したものを修正して
```

Claude Codeはファイルを読み、編集案を提示し、許可を得てから書き込む（デフォルト動作）。

#### 権限設定の選択

用途に応じて3つのモードから選択する：

**デフォルト（確認プロンプトあり）** ― 初心者・重要プロジェクト向け

```bash
claude
# ファイルを変更するたびに「この変更を適用しますか？」と確認される
# 意図しない変更を防げる
```

**`--permission-mode auto`** ― 繰り返しタスク・信頼できるプロジェクト向け

```bash
claude --permission-mode auto
# 事前に許可したルールの範囲内で自動実行する
# セッション内で /permissions コマンドを使って許可ルールを細かく管理できる
```

**`--dangerously-skip-permissions`** ― ⚠️ 隔離された環境（コンテナ等）専用

```bash
claude --dangerously-skip-permissions
# 全ての確認プロンプトをスキップして完全自動実行
# コンテナ内・CI環境など、誤操作しても問題ない隔離環境でのみ使用すること
# 通常の開発環境では非推奨
```

許可モードの詳細：[Claude Code 権限モード](https://code.claude.com/docs/en/permission-modes)

#### 実用的な指示例

```
> プロジェクト全体のimportを整理して（node_modules以外のすべての.tsファイル対象）
> ESLintエラーを全て自動修正して
> package.jsonの依存関係を最新バージョンに更新して、壊れたテストを修正して
> src/components/ 配下のReactコンポーネントにすべてJSDocコメントを追加して
> .env.example に対応する .env ファイルがなければ作成して、デフォルト値を入れて
```

変更後は通常のGitコマンドで差分を確認できる：

```bash
git diff
git add -p  # 変更を個別に確認しながらステージング
```

ベストプラクティス：[Claude Code ベストプラクティス](https://code.claude.com/docs/en/best-practices)
