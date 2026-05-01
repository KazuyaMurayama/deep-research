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

詳細比較：[Warp 公式ドキュメント](https://docs.warp.dev/)

### 組み合わせのメリット一覧

- できる ✓ ローカルファイルのフル編集（Claude Code）
- できる ✓ 複数リポジトリ横断（Claude Code + --add-dir）
- できる ✓ 音声でClaude Codeに指示（Warpの全入力インターフェースで利用可）
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

Claude Codeセッション起動時にWarpが自動でチップを表示する。手動インストールする場合はターミナルで：

```bash
claude plugin marketplace add warpdotdev/claude-code-warp
claude plugin install warp@claude-code-warp
```

またはClaude Codeセッション内のスラッシュコマンドでも可：

```
/plugin marketplace add warpdotdev/claude-code-warp
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
| 音声入力でClaude Codeに指示 | できる ✓（Warpの全入力インターフェース） |
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

参考：[Warp ドキュメント](https://docs.warp.dev/)

---

### 4-2. 音声で入力する（ニーズ2）

**実現可否： ○完全対応**

音声入力はWarpの全入力インターフェース（Agent Mode・Terminal Modeを含む）で利用できる。Wispr FlowがリアルタイムでWarpの入力欄に書き起こし、Claude Code CLIへ送信する構成だ。

#### Warp音声入力の有効化と使い方

1. Warpの入力欄右のマイクボタンをクリックする（または `Fn`（macOS）/ `Alt+右矢印`（Windows/Linux）でホットキー起動）
2. **Settings → AI → Voice** でホットキーのカスタマイズが可能（推奨：`Option+V`（Mac））
3. ホットキーを押しながら話す、または入力欄右のマイクボタンをクリックして話す
4. 話し終えると Wispr Flow がリアルタイムで文字起こしし、入力欄に挿入される
5. `Enter` を押してClaude Codeへ送信する

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

---

### 4-4. 履歴をわかりやすく見る（ニーズ4）

**実現可否： ○完全対応**

#### Warp Blocks UIの仕組み

Warpでは1コマンドの実行（入力＋出力）が「ブロック」として自動的にグルーピングされる。

- **ブロック間の移動**：`↑` / `↓` キーで前後のブロックに移動できる
- **ブロック内スクロール**：`Ctrl+Shift+↑` / `Ctrl+Shift+↓` でブロックの先頭・末尾へジャンプ
- **ブロック操作メニュー**：ブロック右上の`...`から「コマンドをコピー」「出力をコピー」「ブックマーク」「共有リンク生成」ができる
- **Claude Codeの長い出力**：長大な出力もブロック単位で折りたたみ・展開できるため、スクロールが必要な場面が激減する
- **コンテキストへの添付**：ブロック右のスパークルアイコンをクリックすると、そのブロックの出力をAgent Modeのコンテキストに添付できる

参考：[Warp Blocks 基本操作](https://docs.warp.dev/terminal/blocks/block-basics)

#### Claude Codeのセッション履歴を再開する

Claude Codeはセッションの全メッセージ履歴とツール使用ログを永続化している。再開方法は2通り。

**直前のセッションをすぐ再開する（`-c`）**

```bash
claude -c
# 最後のセッションをそのまま再開する
# 「昨日の続きから」というときに最速
```

**セッション選択ピッカーから再開する（`-r`）**

```bash
claude -r
# 過去のセッション一覧が表示される
# 日時・プロジェクトパス・最初のメッセージが一覧で確認でき、選択して再開する
```

セッション内でも再開コマンドが使える：

```bash
/resume     # セッション選択ピッカーを起動（一次コマンド）
/continue   # /resume のエイリアス（同じ動作）
```

#### 実用例：昨日の作業を今日再開する

1. Warpでプロジェクトのディレクトリへ移動する：
```bash
cd ~/projects/my-app
```

2. セッションを再開する：
```bash
claude -c
# または直前以外のセッションを探すなら
claude -r
```

3. Claude Codeが「前回どこまで進めたか」を自動で把握しているので、「昨日の続きを進めて」と入力するだけで作業が再開できる

4. Warpのブロック履歴も残っているため、前回実行したコマンドの出力をブロックからコピーしてClaudeに添付することができる

---

### 4-5. コンテキスト・トークン使用状況を確認する（ニーズ5）

**実現可否： ○完全対応**

#### スラッシュコマンド一覧（確認・最適化系）

Claude Codeセッション内で以下のコマンドを実行する。

```
/usage   → セッションコスト・プラン使用量・アクティビティ統計を表示
           （/cost や /stats も同じ機能）
/context → コンテキストウィンドウの使用状況をカテゴリ別カラーグリッドで可視化
/compact → 会話を自動要約してコンテキストを解放（コスト削減）
/status  → Claudeバージョン・接続モデル・アカウント・接続状況を確認
/clear   → コンテキストを完全リセット（要約なし）
```

#### 各コマンドの使い方と出力の読み方

**`/usage`：コストとプラン使用量の確認**

```
/usage
# 出力例（APIユーザーの場合）：
#   入力トークン: 45,320  ($0.068)
#   出力トークン: 8,150   ($0.122)
#   合計コスト:   $0.190
#   プラン使用量: 22% (Max)
```

Proプランユーザーはドルコストではなく使用率（%）で表示される。

**`/context`：コンテキストウィンドウの可視化**

```
/context
# カテゴリ別のカラーグリッドが表示される：
#   システムプロンプト  ██░░░░  12%
#   ツール定義         ████░░  28%
#   CLAUDE.md/メモリ   ██░░░░  15%
#   メッセージ履歴      ████░░  32%
#   空き              ░░░░░░  13%
```

カテゴリ別の割合グラフでコンテキストの圧迫度を直感的に把握できる。

**`/compact`：コンテキストの圧縮**

```bash
/compact
# 会話を自動的に要約してコンテキストを解放する

/compact 重要な決定事項・変更したファイル名だけ保持して要約して
# 保持したい内容を指定して圧縮する（指示付き圧縮）
```

#### コンテキストが増えてきたときの対処法

1. **`/compact` でコンテキストを圧縮する**（推奨・情報を保持しながら圧縮）
2. **`--max-turns` でターン数を制限する**（非対話モードでのコスト上限設定）：
```bash
claude -p 'バグを修正して' --max-turns 20
```
3. **CLAUDE.mdを軽量化する**（`/context` の出力でCLAUDE.mdが肥大化していれば整理）
4. **新しいセッションを開始する**（`/clear` でリセット、または `claude` を再起動）：
```bash
/clear
# または Ctrl+C でセッション終了 → claude で新規起動
```

#### コスト管理のTips

- `/usage` を作業開始時と終了時に確認する習慣をつけると、コストの感覚が身につく
- 長い出力が予想されるタスク（「全ファイルのimportを整理して」など）の前に `/compact` を実行しておくとコンテキスト枯渇を防げる
- 探索フェーズ（「このコードを理解して」）と実装フェーズ（「修正して」）でセッションを分けると、探索中に蓄積した不要な中間出力がコンテキストを圧迫しにくくなる

コスト詳細：[Claude Code コスト管理](https://code.claude.com/docs/en/costs) ／ [CLIリファレンス](https://code.claude.com/docs/en/cli-reference)

---

### 4-6. 多数のタブで並行セッションを管理する（ニーズ6）

**実現可否： ○完全対応**

#### Warpのタブ・ペイン操作

Warpは複数のタブ・スプリットペインを標準機能として持ち、各タブで独立したClaude Codeセッションを起動できる。

**新しいタブを開く**

```bash
# Mac
Cmd+T

# Windows / Linux
Ctrl+T
```

**画面を分割（スプリットペイン）**

```bash
# Mac：縦分割
Cmd+D

# Mac：横分割
Cmd+Shift+D

# ペイン間の移動
Cmd+Option+← / →（Mac）
Ctrl+Alt+← / →（Windows/Linux）
```

スプリットペインを使うと同一ウィンドウ内に複数のClaude Codeセッションを並べて表示でき、片方の出力を見ながらもう片方に指示を送れる。

参考：[Warp ドキュメント](https://docs.warp.dev/)

#### Claude Code並行起動のパターン

```
タブ1: repo-A のフロントエンド修正
  cd ~/projects/frontend && claude
  > コンポーネントのTypeScriptエラーを全て修正して

タブ2: repo-B のAPIエンドポイント追加
  cd ~/projects/api && claude
  > POST /api/v2/users エンドポイントを追加して

タブ3: インフラ設定の確認・変更
  cd ~/projects/infra && claude
  > ステージング環境のdocker-compose.ymlを本番設定に合わせて更新して
```

各タブは完全に独立したClaude Codeセッションとして動作する。あるタブのClaudeが別のタブのClaudeの状態を参照することはない。

**Warpの縦タブUIを活用したタブ管理**

タブ名をリポジトリ名に変更しておくと、どのタブがどのリポジトリに対応しているか一目でわかる。タブ名はWarpのタブ右クリックメニュー →「Rename Tab」から変更できる。

#### 並行実行時の注意事項

- **同一ファイルへの同時編集は避ける**：2つのClaudeセッションが同じファイルを同時に書き換えると、後で保存した方が前の変更を上書きする。共有ファイルは1タブで担当を固定する
- **git競合を防ぐためにブランチを分ける**：複数タブで同一リポジトリを操作する場合、各タブで別ブランチを使う：
```bash
# タブ1
git checkout -b feature/frontend-fix
claude

# タブ2（同じリポジトリの別作業）
git checkout -b feature/api-update
claude
```
- **プラン上限に注意**：Proプランの場合、並行セッションそれぞれがトークンを消費する。`/usage` で各タブのコストを定期確認する

#### セッションIDによる管理

各Claude Codeセッションには固有のIDが付与される。`claude -r` でセッション一覧を表示すると、セッションIDと開始ディレクトリを確認できる。

```bash
claude -r
# 出力例：
#   [1] 2026-05-01 09:15  ~/projects/frontend  "TypeScriptエラーを..."
#   [2] 2026-05-01 10:30  ~/projects/api       "エンドポイントを追加..."
#   選択 [1-2]:
```

タブを閉じても後からセッションを再開できるため、急な割り込みが入っても作業を中断せず後で戻れる。

---

### 4-7. スマホから監視・指示を出す（Warp Remote Control）（ニーズ7）

**実現可否：△（監視・承認・テキスト指示は可、新規セッション起動は不可）**

Warp Remote Controlは、PC上で起動中のClaude Codeセッションにスマホのブラウザから接続し、リアルタイムで監視・操作できる機能だ。スマホ側にアプリのインストールは不要。

#### セットアップ手順（PC側）

1. WarpでClaude Codeセッションを起動する：

```bash
cd ~/projects/my-app
claude
# バックグラウンドでタスクを実行する場合
claude -p '全テストを実行して失敗を修正して' --permission-mode auto > claude-log.txt 2>&1 &
```

2. Warpのエージェントユーティリティバー（画面上部）を確認し、**`/remote-control`** チップをクリックする
3. 自動生成されたURLがクリップボードにコピーされる
4. そのURLをSMS・メモアプリなどでスマホに送り、Safariまたは Chromeで開く（アプリインストール不要）

公式ドキュメント：[Warp Remote Control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control)

#### スマホ側でできること

- セッションのリアルタイム閲覧（出力がPCと同期される）
- テキスト入力・追加指示の送信
- 承認プロンプト（ファイル書き込みの確認など）への ✓/✗ 応答

#### スマホ側でできないこと ✗

- 新規セッションの起動（PCで起動済みのセッションへの接続のみ）
- ファイルブラウザ操作
- キーボードショートカット

#### 実用シナリオ

通勤中にPCで「全テスト実行＋バグ修正」タスクをバックグラウンド実行しておき、スマホのブラウザでRemote Controlを開いて進捗を確認。Claude Codeからの承認プロンプトが出たら ✓ をタップして承認。必要なら「この方向ではなく〇〇で実装して」と追加指示を送信する。

---

## 第5章：実運用Tips

### CLAUDE.mdの効果的な書き方

プロジェクトルートの `CLAUDE.md` はClaude Codeが起動のたびに自動で読み込む設定ファイルだ。ここにプロジェクト共通ルールを書いておくことで、毎回同じ説明をせずに済む。

```markdown
# プロジェクト名

## 基本情報
- 言語: TypeScript 5.x / Node.js 20
- テスト: Jest（npm test）
- リント: ESLint + Prettier（npm run lint:fix で自動修正）

## 必須ルール
- コミット前に必ず npm test && npm run lint を実行すること
- 型の any は禁止（unknown を使う）
- コメントは日本語で書く

## 関連リポジトリ
- ../api/  : バックエンドAPI（レスポンス型は api/src/types/ を参照）
```

セッション内で `/memory` を実行するとエディタでCLAUDE.mdをその場で編集できる。

参考：[Claude Code メモリ設定](https://code.claude.com/docs/en/memory)

### セッション開始時の定番コマンドセット

```bash
# 1. プロジェクトに移動してセッション起動
cd ~/projects/my-app && claude

# セッション内で最初に実行する定番コマンド
/status        # モデル・バージョン・接続状況を確認
/context       # コンテキスト使用量を把握してからスタート
/usage         # 前セッションまでのコストを確認
```

### コスト削減のコツ

| テクニック | 効果 |
|-----------|------|
| `/compact` を定期実行 | 不要な中間出力を要約してトークンを節約 |
| `--max-turns` で上限設定 | 非対話バッチ実行のコスト上限を制御 |
| CLAUDE.mdを100行以内に保つ | 毎ターンのシステムトークン削減 |
| 探索と実装でセッションを分ける | 探索時の出力が実装コンテキストを汚染しない |

```bash
# コスト上限付きバッチ実行
claude -p 'バグを全て修正して' --max-turns 30 --permission-mode auto
```

### よくある失敗と対処

**権限エラー（Permission denied）**

```bash
# 原因：デフォルトモードでClaude Codeがファイル書き込みを試みた
# 対処：--permission-mode auto で自動許可、または /permissions でルールを追加
claude --permission-mode auto
```

**コンテキスト溢れ（Context window full）**

```bash
# 対処1：/compact で圧縮してから継続
/compact 重要な決定事項と変更ファイル名だけ保持

# 対処2：新規セッションを開始
/clear
```

**git競合（Merge conflict）**

```bash
# 複数タブで同一ブランチを操作すると発生
# 対処：タブごとに別ブランチを使う
git checkout -b feature/fix-A  # タブ1
git checkout -b feature/fix-B  # タブ2

# 競合が発生した場合はClaudeに解決させる
# > このgit競合を解決して（優先する変更はXの方向で）
```

---

## 第6章：できること・できないことの早見表

| ニーズ | Web版Claude Code | Warp + CLI | Remote Control（スマホ） |
|--------|----------------|------------|------------------------|
| ローカルファイル編集 | ✗ | ✓ | ✗（CLI経由で可） |
| 複数リポ横断 | △ | ✓ | △ |
| 音声入力 | ✗ | ✓ | ✗ |
| 履歴管理 | △ | ✓ | 閲覧のみ ✓ |
| トークン確認 | ✗ | ✓ | 閲覧のみ ✓ |
| 並行セッション | △ | ✓ | 監視のみ ✓ |
| 外出先操作 | ✓（ブラウザ） | △（PC起動必要） | ✓（PC起動必要） |

---

## 第7章：まとめ ― 3層運用モデル

### いつどの層を使うか

```
軽い質問・設計相談・アイデア整理
    → Layer 1: Web版Claude Code（インストール不要・すぐ使える）

実装・ファイル編集・複数リポ作業・Git操作
    → Layer 2: Warp + Claude Code CLI（ローカル環境で最大パフォーマンス）

外出先での確認・承認・追加指示
    → Layer 3: Warp Remote Control（PC上のセッションにスマホから接続）
```

### 学習ロードマップ（Week 1〜4）

| 週 | 目標 | 主な操作 |
|----|------|---------|
| Week 1 | Warp + CLIインストール・基本操作 | `claude` 起動、ファイル編集指示、`/usage` 確認 |
| Week 2 | 複数リポ・CLAUDE.md設定 | `--add-dir`、CLAUDE.md作成、`claude -r` セッション再開 |
| Week 3 | 音声入力・並行セッション | Warp Agent Mode + Voice、マルチタブ運用、`/compact` |
| Week 4 | Remote Control・フル3層運用 | スマホからの監視・承認、バックグラウンド実行 |

### 参考リンク集（公式）

| ドキュメント | URL |
|------------|-----|
| Claude Code 概要 | [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) |
| Claude Code クイックスタート | [code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart) |
| Claude Code CLIリファレンス | [code.claude.com/docs/en/cli-reference](https://code.claude.com/docs/en/cli-reference) |
| Claude Code 権限モード | [code.claude.com/docs/en/permission-modes](https://code.claude.com/docs/en/permission-modes) |
| Claude Code メモリ・CLAUDE.md | [code.claude.com/docs/en/memory](https://code.claude.com/docs/en/memory) |
| Claude Code コスト管理 | [code.claude.com/docs/en/costs](https://code.claude.com/docs/en/costs) |
| Claude Code ベストプラクティス | [code.claude.com/docs/en/best-practices](https://code.claude.com/docs/en/best-practices) |
| Warp セットアップガイド | [docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) |
| Warp Remote Control | [docs.warp.dev/agent-platform/third-party-agents/remote-control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control) |
| Warp 音声入力 | [docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice) |
| Warp ドキュメント（トップ） | [docs.warp.dev](https://docs.warp.dev/) |
