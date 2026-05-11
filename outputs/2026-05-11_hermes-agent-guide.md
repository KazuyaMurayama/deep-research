# ヘルメスエージェント 調査レポート
〜スマートフォンからVS Code・Claude Codeを操作できるか？〜

作成日: 2026-05-11
最終更新日: 2026-05-11

---

## TL;DR（結論・3行）

- **ヘルメスエージェント（Hermes Agent）は実在する**。Nous Research が開発するオープンソース自己学習型AIエージェントで、Telegram・Discord等のメッセージアプリ経由でスマホから操作できる。
- ただし**VS Codeのローカルセッション（`~/.claude/projects/`）への直接アクセスや、Claude Codeとのネイティブ統合は提供していない**。Hermes AgentはClaude Codeと別のエージェント製品である。
- **スマホからPC上のClaude Codeを操作したい場合**、Claude Code公式の「Remote Control」または「Channels（Telegram連携）」機能を使うのが最適解であり、VS Code拡張機能から `/remote-control` コマンドで起動できる。

---

## 1. 調査の背景

VS CodeでもクロードコードClaude Code CLIツール）を使っているユーザーが、以下の課題を抱えている：

- VS CodeのローカルセッションやClaude Codeの会話履歴（`~/.claude/projects/`）がスマートフォンから閲覧・操作できない
- 「ヘルメスエージェント（Hermes Agent）」というアプリがスマホからPCを操作できると聞き、この課題を解消できるか確認したい
- Claude Code公式の `/remote-control` 機能は知っているが、VS Codeローカルセッションへの適用可否が不明

本レポートは上記課題を解消するため、ヘルメスエージェントの実態調査と、ユーザー課題への適合性評価を行う。

---

## 2. ヘルメスエージェントとは（実在確認・製品概要）

### 実在確認：Yes

「ヘルメスエージェント（Hermes Agent）」は**実在する製品**である。

**公式情報源：**
- 公式サイト：[https://hermes-agent.org/](https://hermes-agent.org/)
- 公式ドキュメント：[Hermes Agent Docs（Nous Research）](https://hermes-agent.nousresearch.com/docs/getting-started/quickstart)
- GitHubリポジトリ：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

### 開発元・ライセンス・料金

| 項目 | 内容 |
|---|---|
| 開発元 | [Nous Research](https://nousresearch.com/) |
| ライセンス | MIT（オープンソース） |
| 本体料金 | 無料（セルフホスト） |
| 従量費用 | 利用するLLMプロバイダーのAPI料金のみ（OpenAI / Anthropic / Google Gemini / OpenRouter 等） |
| VPS費用目安 | 月$5程度のVPSで動作可能 |

### 対応プラットフォーム

**実行環境（サーバー側）：**
- Linux、macOS、WSL2（推奨）
- Termux（Android、ベータ）
- Windows PowerShell（アーリーベータ）

**スマートフォンからのアクセス方法（クライアント側）：**
Hermes Agent専用のスマホアプリは**存在しない**。代わりに既存のメッセージアプリ経由でアクセスする。

| 対応プラットフォーム | 補足 |
|---|---|
| Telegram | 最も推奨。ボット設定が容易 |
| Discord | ボット連携 |
| Slack | ワークスペース連携 |
| WhatsApp | 連携可能 |
| Signal | 連携可能 |
| Email | 連携可能 |
| Home Assistant | スマートホーム連携 |

### 主要機能

1. **永続メモリ（Persistent Memory）**：プロジェクトや好みを複数セッションにわたって記憶
2. **自律スキル生成**：使いながら自動的にスキル（手順書）を作成・改善する学習ループ
3. **マルチプラットフォームゲートウェイ**：Telegram/Discord等から単一エージェントに接続
4. **スケジューラ**：cronジョブのような定期実行タスク
5. **サブエージェント並列実行**：複数の並列ワークストリームをスポーン
6. **複数ターミナルバックエンド**：ローカル・Docker・SSH・Modal・Daytona・Vercel Sandbox等
7. **40以上の統合ツール**：Web検索（Firecrawl）、画像生成（FAL/FLUX）、ブラウザ自動操作等

---

## 3. ユーザー課題への適合性

### VS Codeローカルセッション閲覧の可否

**判定：不可**

Hermes Agentは独立したAIエージェントであり、**VS CodeのClaude Code拡張機能のセッション（`~/.claude/projects/`）には直接アクセスできない**。

- Hermes AgentはClaude Codeとは別のエージェントシステムである
- Hermes Agent自身がAIエージェントとして動作するため、Claude Codeの会話履歴を引き継ぐ仕組みはない
- ACP（Agent Client Protocol）による [VS Codeへの接続実装](https://dev.to/formulahendry/openclaw-is-old-run-hermes-agent-in-vs-code-through-acp-agent-client-protocol-now-2721) は存在するが、これはHermes AgentをVS Code内で操作するもので、Claude Codeのセッションへのアクセスではない

### スマホからClaude Code操作の可否

**判定：Hermes Agent経由では不可（ただし代替手段あり）**

- HermesはTelegram等経由でHermes自身を操作できるが、Claude Codeを操作する用途には設計されていない
- 技術的には「Hermes AgentがClaude Codeをサブエージェントとして呼び出す」構成は可能とされる情報があるが、公式ドキュメントでの明示的な手順は確認できなかった [要確認]

### 判定根拠

[公式README](https://github.com/NousResearch/hermes-agent/blob/main/README.md) によると、Hermes Agentは「自己成長型AIエージェント」として設計されており、Claude Codeとは別の製品。公式ドキュメントにVS CodeのClaude Codeセッションとの連携について記載はない。

---

## 4. セットアップ手順（できる場合）または代替案（できない場合）

Hermes AgentではユーザーのVS Code / Claude Codeセッション引き継ぎは実現できない。以下に**実際に課題を解決できる代替案**を示す。

---

### 代替案A（最推奨）：Claude Code Remote Control（VS Code対応）

Claude Code公式の **Remote Control** 機能を使えば、**VS Code上のClaude Codeセッションをスマートフォン（iOSアプリ / Androidアプリ / ブラウザ）から継続操作できる**。

**要件：**
- Claude Code v2.1.51以降
- Pro / Max / Team / Enterprise サブスクリプション（APIキーは不可）
- claude.ai アカウントでログイン済み（`claude auth login`）

**VS Codeでの起動手順：**
1. VS CodeのClaude Code拡張機能プロンプトボックスに `/remote-control` を入力してEnter
2. VS Code画面上部にバナーが表示されセッションURLが発行される
3. スマートフォンの [Claude iOS アプリ](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) または [Android アプリ](https://play.google.com/store/apps/details?id=com.anthropic.claude) を開く
4. セッションリストに表示されたセッションをタップして接続
5. または、[claude.ai/code](https://claude.ai/code) をスマホブラウザで開いてセッションを選択

**制限事項：**
- ローカルClaude Codeプロセスが起動し続けている必要がある（VS Codeを閉じるとセッション終了）
- ネットワーク障害が約10分以上続くとタイムアウト
- `/mcp`、`/plugin`、`/resume` 等の対話型ピッカーコマンドはローカルCLIからのみ動作

---

### 代替案B：Claude Code Channels（Telegram / Discord連携）

Claude Code v2.1.80以降で提供されるMCPサーバーベースの **Channels** 機能。外部メッセージアプリからCLI上のClaude Codeセッションにプッシュメッセージを送れる。

**注意：VS Code拡張機能では非対応。CLIモードのみ。**

**セットアップ概要：**
1. Telegramでボット作成（BotFather経由）
2. プラグインインストール：`/plugin install telegram@claude-plugins-official`
3. トークン設定：`/telegram:configure [token]`
4. Channelsフラグで起動：`claude --channels plugin:telegram@claude-plugins-official`
5. ペアリングコードを交換し、`policy allowlist` でセキュリティ設定

**制限事項：**
- CLIセッションが稼働している間のみ有効
- セッション維持にはZellij等のターミナルマルチプレクサが推奨
- vs. Remote Controlと異なり、会話の双方向継続より「外部イベントに反応する」用途向け

---

### 代替案C：Hermes Agent（独立エージェントとして活用）

ユーザーがClaude Codeを離れて別のAIエージェントを使っても良い場合に選択肢となる。

**セットアップ概要：**
```bash
# 1. インストール（Linux/macOS）
curl -sSL https://hermes-agent.org/install.sh | bash

# 2. モデル設定
hermes model

# 3. 起動（CLI）
hermes

# 4. メッセージゲートウェイ設定（Telegram等）
hermes gateway setup
```

**スマホからのアクセス：**
- Telegramアプリ → 作成したHermesボットにメッセージを送信
- Hermes AgentがVPS/PC上で動作してコマンドを実行・結果を返信

---

## 5. Claude Code Remote Controlとの比較表

| 項目 | Hermes Agent | Claude Code Remote Control | Claude Code Channels |
|---|---|---|---|
| 製品カテゴリ | 独立型AIエージェント | Claude Code公式機能 | Claude Code公式機能 |
| スマホアプリ | なし（Telegram等経由） | Claude公式アプリ（iOS/Android） | Telegram / Discord等 |
| VS Codeセッション継続 | **不可** | **可能**（VS Code拡張機能対応） | 不可（CLI専用） |
| ローカルセッション引き継ぎ | 不可 | **可能**（会話履歴込み） | 不可（新規セッション） |
| `~/.claude/projects/`参照 | 不可 | **可能** | 不可 |
| 料金 | 無料（APIキー必要） | Pro以上のサブスク必要 | Pro以上のサブスク必要 |
| 永続メモリ | あり（独自実装） | なし（Claude Code標準） | なし |
| 自己学習・スキル生成 | あり（主要機能） | なし | なし |
| セットアップ難易度 | 高（サーバー構築必要） | 低（コマンド1つ） | 中（ボット作成必要） |
| プッシュ通知（スマホ） | なし | **あり**（v2.1.110以降） | なし |
| 接続先 | Hermes自身を操作 | PC上のClaude Codeを操作 | PC上のClaude Codeに指示 |

---

## 6. 注意点・制限事項・セキュリティ

### Claude Code Remote Controlのセキュリティ

- すべてのトラフィックはTLS経由でAnthropicAPIを経由。インバウンドポートは開かない
- 各接続は短命な認証情報を使用し、単一目的にスコープされ独立して期限切れ
- APIキーでは使用不可。claude.ai OAuth認証が必須

### Hermes Agentのセキュリティ

- セルフホストのため、サーバーのセキュリティ管理は利用者の責任
- Telegramボットのトークン管理が必要
- LLMプロバイダーにプロンプト・コンテキストが送信される

### Remote Controlの重要な制限

- **「ローカルプロセスは実行し続ける必要がある」**：VS Codeを閉じるかPCがスリープするとセッションは一時的に切断（ネットワーク回復で再接続、ただし10分以上の障害でタイムアウト）
- Team/Enterpriseプランでは管理者が管理設定でRemote Controlを有効化する必要がある
- Ultraplan（超長時間計画セッション）実行中はRemote Controlと同時使用不可

---

## 7. 推奨アクション（今日試せる具体的ステップ）

1. **Claude Codeのバージョン確認** → `claude --version` でv2.1.51以降であることを確認。古ければ `npm update -g @anthropic-ai/claude-code`

2. **VS CodeのClaude Code拡張機能でRemote Controlを起動** → プロンプトボックスに `/remote-control` と入力してEnter → 表示されたセッションURLをコピー

3. **スマートフォンにClaudeアプリをインストール** → [iOS版](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) または [Android版](https://play.google.com/store/apps/details?id=com.anthropic.claude) → PCと同じAnthropicアカウントでログイン

4. **スマートフォンのClaudeアプリでセッションに接続** → アプリのセッションリストに表示されたPC上のセッションをタップ → ローカルClaude Codeの会話履歴がスマホに表示される

5. **常時有効化の設定（任意）** → VS CodeのClaude Code内で `/config` を実行 → "Enable Remote Control for all sessions" を `true` に設定 → 以後、毎回コマンドを入力しなくても自動的にリモート対応セッションになる

---

## 参考・出典（URLとアクセス日を明記）

| 情報源 | URL | アクセス日 |
|---|---|---|
| Hermes Agent 公式サイト | [hermes-agent.org](https://hermes-agent.org/) | 2026-05-11 |
| Hermes Agent GitHub（NousResearch） | [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | 2026-05-11 |
| Hermes Agent 公式ドキュメント（クイックスタート） | [hermes-agent.nousresearch.com/docs/getting-started/quickstart](https://hermes-agent.nousresearch.com/docs/getting-started/quickstart) | 2026-05-11 |
| ConoHa VPS：Hermes Agent スタートアップスクリプト | [doc.conoha.jp](https://doc.conoha.jp/products/vps-v3/startupscripts-v3/hermes-agent/) | 2026-05-11 |
| Claude Code Remote Control 公式ドキュメント（日本語） | [code.claude.com/docs/ja/remote-control](https://code.claude.com/docs/ja/remote-control) | 2026-05-11 |
| Claude Code Channels 解説記事（Qiita） | [qiita.com/nogataka](https://qiita.com/nogataka/items/0f1af785d19b18b0d9d6) | 2026-05-11 |
| Hermes Agent vs Claude Code 比較（Bluehost Blog） | [bluehost.com/blog/hermes-agent-vs-claude-code/](https://www.bluehost.com/blog/hermes-agent-vs-claude-code/) | 2026-05-11 |
| Claude Code Channels Telegram セットアップ解説 | [tinybeans.net/blog](https://tinybeans.net/blog/2026/03/claude-code-channels-telegram.html) | 2026-05-11 |
