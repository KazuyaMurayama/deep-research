# Claude CodeをWarpで使う：Windows向け実践ガイド

> 対象読者：Web版Claude Codeを使い始めた方・Windowsで複数のGitHubプロジェクトを扱いたい方  
> 作成日：2026-05-01　最終改訂：2026-05-07

---

## 第1章：はじめに

このガイドはWindows 10/11を使っている方向けです。難しいコマンド入力は不要で、クリックと自然な日本語で操作できます。

### Web版Claude CodeとWarp + Claude Codeの違い

| 比較項目 | Web版Claude Code | Warp + Claude Code |
|---------|----------------|-------------------|
| 自分のPCのファイルを直接編集 | ✗ できない | ✓ できる |
| GitHubのリポジトリを使ったコード作業 | ✓ クラウドVM上でPR作成まで可能 | ✓ ローカルで直接操作 |
| GitHubへの保存（コミット）を自動でやってくれる | ✓ Web上で可能 | ✓ ローカルで可能 |
| スマホから使う | ✓ 公式iOS/Androidアプリ＋ブラウザで使える | △ Remote Control経由でPC作業を遠隔操作 |
| 音声入力 | ✗ なし | ✓ マイクボタン一発 |
| 会話の履歴を見やすく管理 | △ | ✓ Warpの会話メニューから選ぶだけ |

> **Web版Claude Codeについて**：claude.ai/code はAnthropicのクラウドサーバーでGitHubリポジトリをクローンし、コード変更からプルリクエスト作成まで完結できます。公式の **Claude iOSアプリ／Androidアプリ** からも使えます。「自分のPCにあるローカルファイルを直接編集すること」だけができませんが、GitHub上のコード作業は十分できます。外出時の軽い作業はWeb版＋スマホアプリが便利、PCのファイルを直接編集したい本格作業はWarp + Claude Codeという使い分けになります。

### 3つの使い方のイメージ

```
スマホ（外出先）
  → 公式Claudeアプリ（iOS/Android）からWeb版をそのまま使うか、
    PCで動いている作業の「進み具合を確認」「OK/NGを押す」。アプリ不要でブラウザからも可。

PC + Warp + Claude Code（メインの作業場所）
  → PCのファイル直接編集・GitHubへの保存・複数プロジェクトの横断。音声入力やタブも使える。

Web版Claude Code（どこからでも使えるAI相談相手）
  → スマホ・タブレット・PCのブラウザどこからでも使える。GitHubリポジトリの作業も可。
```

---

## 第2章：役割分担

### Claude CodeとWarpはそれぞれ何者？

| 比較項目 | Claude Code | Warp |
|---------|------------|------|
| 何者？ | 自分で考えて作業を進めるAIアシスタント | Claude Codeを快適に動かすための新しい画面 |
| できること | ファイル編集・GitHubへの保存・複数プロジェクト横断・複数のAIに分担作業 | 会話履歴の見やすい整理・音声入力・タブで並行作業・スマホから操作 |
| 単体でも使える？ | ✓（他の画面でも動く） | ✓（Claude Codeなしでも普通の作業画面として使える） |
| 組み合わせると | WarpのUI機能をClaude Codeに上乗せできる | |

### WarpにもAI機能があるけど、Claude Codeと何が違う？

| 比較項目 | WarpのAI機能 | Claude Code |
|---------|------------|------------|
| 役割 | 「このエラーを直すには？」の提案をしてくれる | 「この機能を作って」と丸投げしたら全部やってくれる |
| 自動でどこまでやるか | 提案するだけ → 自分で承認してから実行 | 「修正→テスト→直す」を自動で何度も繰り返す |
| 向いている使い方 | コマンドの調べ物・エラーの原因確認 | 機能実装・バグ修正・複数ファイルの一括修正 |

**2つは競合しません。WarpのAI機能で調べながら、Claude Codeで実装する、という使い方ができます。**

---

## 第3章：セットアップ（クリックだけで完了）

### 3-1. Warpのインストール

1. ブラウザで [warp.dev/download](https://www.warp.dev/download) にアクセス
2. 「Download for Windows」ボタンをクリック
3. ダウンロードした `Warp.exe` をダブルクリックしてインストール
4. 起動したらGoogleアカウントまたはGitHubアカウントでサインイン

**料金プラン**（[公式Warp料金ページ](https://www.warp.dev/pricing) で最新情報を確認）：

| プラン | 月額 | 特徴 |
|-------|-----|------|
| Free | 無料 | 個人利用・基本機能すべて使える |
| Build | $20 | チーム共有・より多くのAI機能 |
| Business | $50 | 管理機能・セキュリティ強化 |

個人での使い始めは **Freeプランで十分** です。

### 3-2. Claude Codeの連携

1. Warpを起動する
2. 画面左下の **歯車マーク（設定）** をクリック
3. 「**AI**」→「**Agents**」→「**Claude Code**」→「**Connect**」をクリック
4. ブラウザが自動で開く → Claude.aiのアカウントでサインイン → 「**接続を許可**」をクリック
5. Warpに戻ると「**Claude Code 準備完了**」と表示される

> 起動時に通知バナーが表示される場合はそこから「Install」をクリックするだけでも連携できます。

> Claude Pro / Max / Team / Enterprise のサブスクリプションが必要です。

### 3-3. 動作確認

Warpの入力欄に日本語で話しかけてみましょう：

> 「こんにちは。今日から使い始めます。よろしく」

返事が返ってきたら成功です。

参考：[Warp公式セットアップガイド](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) ／ [クイックスタート](https://code.claude.com/docs/en/quickstart)

---

## 第4章：7つのニーズ別 実現手順

### 4-1. 複数GitHubリポジトリを横断操作する ○

複数のGitHubプロジェクトをまとめて扱いたいとき、コマンド入力は最小限で済みます。

**手順（URL貼り付け + 自然言語）**：

1. Warpを開いてClaude Codeの会話画面を起動
2. 横断したいGitHubリポジトリのURL（例：`https://github.com/myname/frontend`）を**そのまま会話欄に貼り付ける**
3. 続けて日本語で「以下のリポジトリを順番に **クローンして**、〜を比較して」と書く

**サンプル指示文**：

> 以下の3つのGitHubプロジェクトをクローンして、それぞれのログイン処理を比べてください。共通化できそうな部分があれば教えてください。
>
> https://github.com/myname/frontend
> https://github.com/myname/api
> https://github.com/myname/shared

Claude Codeが内部でリポジトリをダウンロードし（最初の1回だけ「OK？」と確認が来ます）、横断的に比較・分析してくれます。「クローンして」という一言がポイントです。

> **もっと手軽にやりたい場合**：Web版Claude Code（claude.ai/code）にGitHubアカウントを連携しておけば、URLを貼り付けるだけでクラウド側が自動でクローンします。スマホからも同じ操作が可能です。

**複数タブで並行管理する場合**：

- Warpで新しいタブを開く：`Ctrl + T`
- タブ1でフロントエンドのプロジェクト、タブ2でAPIプロジェクト、というように分けて作業できます
- 各タブで「このプロジェクトのバグを直して」と話しかけるだけでOK

参考：[Claude Code メモリ設定](https://code.claude.com/docs/en/memory)

---

### 4-2. 音声で入力する ○

キーボードを打たずに話しかけるだけでClaude Codeに指示できます。

**有効化方法**：Warpの入力欄右側の **マイクボタン** をクリック  
**ショートカットの確認・変更**：設定画面（左下の歯車マーク）→「AI」→「Voice」から確認・変更できます

日本語対応しています。プログラムの関数名など専門用語は、入力後に手入力で修正すると確実です。

**注意**：Warp以外の環境（他のターミナルアプリなど）では使用できません。

公式：[Warp Voice 入力](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice)

---

### 4-3. 自分のPCのファイルを編集する ○（Web版との最大の差分）

Web版Claude Codeにはできない、PCのファイルへの直接アクセスができます。

| できること | Web版 | Warp + Claude Code |
|----------|------|-------------------|
| 自分のPCのファイルを直接読み書き | ✗ | ✓ |
| GitHubへの保存を自動で実行 | ✓（クラウド経由） | ✓（ローカル直接） |
| プログラムのテストや実行 | ✗ | ✓ |

**どこまで自動でやらせるかの設定（3段階）**：

| モード | どんな動き？ | おすすめの人 |
|-------|-----------|----------|
| 慎重モード（標準） | ファイルを変える前に毎回「OK？」と確認してくる | 使い始めたばかりの人 |
| おまかせモード | 決めたルールの範囲なら自動で進める | 少し慣れてきた人 |
| 全自動モード | 何でも自動でやる | ⚠️ 通常は使わない |

設定はWarpの会話中に「慎重モードに変えて」と話しかけるだけで切り替わります。

参考：[Claude Code 権限モード](https://code.claude.com/docs/en/permission-modes)

---

### 4-4. 過去の会話履歴をクリックで再開する ○

コマンドを打たなくても、昨日の続きや先週の作業を簡単に再開できます。

**Claude Codeの過去の会話を呼び出す方法**：

WarpにはVS Codeのような「過去セッションを一覧で選ぶ専用サイドバー」はありません。代わりに、Claude Code自体が持つ「セッションピッカー」機能を使います。

Warpの入力欄に **`claude -r`** と入力して送信すると、Claude Codeがターミナル内にセッション選択メニューを表示してくれます。あとは方向キーで選んでEnterを押すだけで再開できます。

> **全プロジェクトの履歴を横断して探したい場合**：メニューが開いた状態で `Ctrl + A` を押すと、PC内のすべてのプロジェクトの過去セッションが一覧表示されます。

**現在進行中のセッションを並行して管理したい場合**：

Warpの左サイドバーの **Vertical Tabs（縦タブ）** に、今動いているClaude Codeセッションのタブが並んで表示されます。各セッションの進み具合（進行中・完了・エラー）がひと目でわかり、クリックで切り替えられます。これは「今開いているタブの管理」です（過去の履歴ブラウザとは異なります）。

**Warpの「Blocks」機能**：会話のやりとりが1つのかたまり（ブロック）として整理され、過去の作業内容がひと目でわかります。ブロック単位でコピーやブックマークもできます。

参考：[Claude Code セッション管理](https://code.claude.com/docs/en/sessions) ／ [Warp Vertical Tabs](https://docs.warp.dev/terminal/windows/vertical-tabs)

---

### 4-5. 使用量（トークン）を確認する △

「あとどのくらい使えるのか」は、以下の方法で確認できます。

> **2026年5月時点の注意**：Warpの設定画面でトークン使用量を「常時表示」にするオプションは現在のところ存在しません（公式に機能要望が出ている段階）。以下の方法で代用できます。

**方法1：Claude Codeに自然言語で聞く（最も簡単）**

会話欄に「今のコンテキスト使用量は？」「今日のコストは？」と話しかけるだけで、使用状況を教えてくれます。

**方法2：Warpの別ペインで使用量を常時表示する（"ずっと見たい"なら）**

Warpの画面分割（画面右上の「分割」アイコン）で下ペインを開き、Claude Codeに「`ccusage` をインストールしてリアルタイム表示して」と話しかけると、使用量モニターが常時更新表示されます。

> これはコミュニティ製のツール（[ccusage](https://github.com/ryoppippi/ccusage)）で、Warpの標準機能ではありません。

**AIの記憶を整理して容量を空けたいとき**：

会話欄に「これまでの会話を要約して整理して」と話しかけるだけでOKです。

参考：[Claude Code コスト管理](https://code.claude.com/docs/en/costs)

---

### 4-6. 複数のタブで並行して作業する ○

複数のプロジェクトやタスクを同時に進めるとき便利です。

**タブ操作（Windowsの場合）**：
- 新しいタブを開く：`Ctrl + T`
- 画面を2分割する：Warp画面右上の「分割」アイコンをクリック
- タブやパネルを切り替える：マウスでクリックするだけ

各タブで別々のプロジェクトを開き、それぞれにClaude Codeで話しかけることができます。

**注意事項**：
- 同じファイルを2つのタブから同時に編集しようとすると、後から書いた方が上書きされてしまいます → 同じファイルは1つのタブだけで担当する
- 同じGitHubプロジェクトを複数タブで操作するときは、「タブAはログイン機能担当」のように役割を分けると安全
- 並行して動かす分だけ使用量（トークン）が増えます

参考：[Warp ドキュメント](https://docs.warp.dev/)

---

### 4-7. スマホから確認・指示を出す ○（公式アプリ対応）

外出中、PCで動いているClaude Codeの作業をスマホから確認・操作できます。Claude Code v2.1.51以降の公式機能で、QRコード読み取りに対応しています。

**接続手順（5ステップ）**：

1. PCのWarpでClaude Codeを起動し、プロジェクトを開いて作業を始める
2. Warpの会話欄に **`/remote-control`**（または短縮形 `/rc`）と入力する → ターミナル内にセッションURLとQRコードが表示される
3. スマホで以下のいずれかを実行：
   - **公式Claudeアプリ**（[iOS](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) ／ [Android](https://play.google.com/store/apps/details?id=com.anthropic.claude)）を開き「Code」タブからセッションをタップ
   - スマホカメラでQRコードを読み取る
   - スマホブラウザで claude.ai/code を開き、セッション一覧から選ぶ
4. 同じ会話がスマホとPCで同期。スマホから日本語で追加指示・「OK」「やめて」の承認ができる

> **公式アプリをまだ入れていない場合**：Warpの会話欄に `/mobile` と入力すると、アプリのダウンロード用QRコードが表示されます。

**スマホでできること・できないこと**：

| できること | できないこと |
|----------|----------|
| PCでの作業をリアルタイムで確認 | 新しい作業の開始 |
| 日本語で追加指示を送る | PCのファイルを直接開いて見る |
| 「OK」「やめて」の承認・拒否 | キーボードショートカット |

**注意事項**：
- PCのClaude Codeプロセスは起動したままにする（終了すると接続も切れる）
- PCとスマホが別のWi-Fiでも使えます（クラウド経由でつながる）
- Pro / Max / Team / Enterprise いずれかのプランが必要

公式：[Claude Code Remote Control](https://code.claude.com/docs/en/remote-control) ／ [Warp Remote Control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control)

---

### 4-8. VS Code・Web版・Warpのセッション履歴を引き継げるか？ △

VS Codeを使っている方はご存知かもしれませんが、VS CodeのClaude Code拡張には左サイドバーに「CLAUDE CODE」パネルがあり、「Local」タブと「Web」タブで過去のセッション（例：「論文サーチv10」「大喜利v1」）をクリックして引き継げます。**同じことがWarpでもできるのか、よく聞かれます。結論から言うと「ローカルセッションは引き継げる。Webセッションは引き継げない」です。**

#### セッションには2種類ある

| 種類 | 保存場所 | どこから見える？ |
|-----|---------|-------------|
| **ローカルセッション** | 自分のPC内（`~/.claude/projects/`） | Warp・VS Code統合ターミナル・PowerShell・VS Code拡張のLocalタブ、**どこからでも共有** |
| **クラウドセッション** | Anthropicのサーバ（Web版） | VS Code拡張のWeb/Remoteタブのみ。**Warpからは直接見えない** |

#### WarpとVS Code拡張の履歴UI比較

| 機能 | VS Code拡張 | Warp |
|------|------------|------|
| 過去のローカルセッション一覧（クリックで再開） | ✓ Localタブ | △ `claude -r` でCLIのセッションピッカーを使う |
| Webセッション（claude.ai/code）の一覧・引き継ぎ | ✓ Web/Remoteタブ | **✗ できない** |
| 現在進行中のセッションをタブで管理 | ✓ | ✓ Vertical Tabs |

#### ローカルセッションはどのツール間でも共有される

Warp・VS Code統合ターミナル・通常のターミナル・VS Code拡張のLocalタブは、いずれも同じPC内の `~/.claude/projects/` を共有しています。そのため：

- **Warpで途中まで進めた相談** → VS Code拡張のLocalタブで続きを開ける ✓
- **VS Code統合ターミナルで始めた作業** → Warpで `claude -r` から再開できる ✓

#### WebセッションをWarpで引き継ぎたいときの間接的な方法

公式に直接つなぐ手段はありませんが、VS Codeを経由して間接的に引き継げます。

1. VS Codeを開き、Claude Code拡張のSession history → **Web（Remote）タブ** を開く
2. 引き継ぎたいWebセッションをクリック → ローカル（`~/.claude/projects/`）に取り込まれる
3. Warpで作業フォルダを開き、`claude -r` でセッションピッカーを起動
4. さっき取り込んだセッションを選んで再開

> **注意**：この方法はGitHubリポジトリと連携して開始したWebセッションのみ対応しています。また、続きで書いた会話はWebのクラウド側には反映されません（一方通行）。

> **「全インターフェースでセッション履歴を統合してほしい」という要望**は公式GitHubのIssue（#49775）に上がっており、現在も未実装です（2026年5月時点）。

参考：[Claude Code セッション管理](https://code.claude.com/docs/en/sessions) ／ [VS Code拡張 Remote Session機能](https://code.claude.com/docs/en/vs-code)

---

## 第5章：まとめ

### 学習ロードマップ

| 週 | 目標 | やること |
|----|------|--------|
| Week 1 | 基本操作に慣れる | Warpを開いてClaude Codeに話しかける・ファイルを編集してもらう・使用量を自然言語で確認する |
| Week 2 | 複数プロジェクトに挑戦 | GitHubのURLを貼り付けて「クローンして」と頼む・`claude -r` で過去の会話を再開 |
| Week 3 | 音声・並行作業を使いこなす | マイクボタンで音声入力・複数タブで並行作業・「要約して整理して」で容量を空ける |
| Week 4 | スマホ連携でフル活用 | `/remote-control` でQRコード表示・公式アプリでスマホから承認・PC作業をスマホで監視 |
| Week 5 | セッション管理を理解する | ローカルセッションの仕組みを理解・VS Codeと連携してWebセッションを引き継ぐ |

### 参考リンク集（公式）

英語のページが多いですが、ブラウザの翻訳機能（右クリック→「日本語に翻訳」）で読めます。

| ドキュメント | URL |
|------------|-----|
| Claude Code 概要 | [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) |
| Claude Code クイックスタート | [code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart) |
| Claude Code Remote Control | [code.claude.com/docs/en/remote-control](https://code.claude.com/docs/en/remote-control) |
| Claude Code 権限モード | [code.claude.com/docs/en/permission-modes](https://code.claude.com/docs/en/permission-modes) |
| Claude Code メモリ・CLAUDE.md | [code.claude.com/docs/en/memory](https://code.claude.com/docs/en/memory) |
| Claude Code コスト管理 | [code.claude.com/docs/en/costs](https://code.claude.com/docs/en/costs) |
| Claude Code セッション管理 | [code.claude.com/docs/en/sessions](https://code.claude.com/docs/en/sessions) |
| Claude Code VS Code拡張（Remoteセッション） | [code.claude.com/docs/en/vs-code](https://code.claude.com/docs/en/vs-code) |
| Warp セットアップガイド | [docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code](https://docs.warp.dev/guides/external-tools-and-integrations/how-to-set-up-claude-code) |
| Warp Agent Conversations | [docs.warp.dev/agents/using-agents/agent-conversations](https://docs.warp.dev/agents/using-agents/agent-conversations) |
| Warp Remote Control | [docs.warp.dev/agent-platform/third-party-agents/remote-control](https://docs.warp.dev/agent-platform/third-party-agents/remote-control) |
| Warp 音声入力 | [docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice](https://docs.warp.dev/agent-platform/warp-agents/interacting-with-agents/voice) |
| Warp Vertical Tabs | [docs.warp.dev/terminal/windows/vertical-tabs](https://docs.warp.dev/terminal/windows/vertical-tabs) |
| Warp ドキュメント（トップ） | [docs.warp.dev](https://docs.warp.dev/) |
| Claude iOSアプリ | [Apple App Store](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) |
| Claude Androidアプリ | [Google Play](https://play.google.com/store/apps/details?id=com.anthropic.claude) |
| ccusage（使用量モニター） | [github.com/ryoppippi/ccusage](https://github.com/ryoppippi/ccusage) |
