# Garry Tan（YC社長）のClaude Code設定を活用する

> 作成日：2026-05-11　最終更新日：2026-05-11  
> 対象：Web版Claude Codeユーザー・複数GitHubリポ所有・CLI初心者  
> ファクトチェック実施済み（2026-05-11）

---

## 1. エグゼクティブサマリー

Y Combinatorの社長・Garry Tanは自身のClaude Code設定をOSSとして公開している。2つのリポジトリが中心だ。

| リポジトリ | スター（2026-05-11時点） | 一言説明 |
|-----------|----------------------|----------|
| [garrytan/gstack](https://github.com/garrytan/gstack) | 約93,000+ | Claude Code用スキルキット（46スキル確認済み） |
| [garrytan/gbrain](https://github.com/garrytan/gbrain) | 14,600+ | AIエージェント向け永続知識ベース（会議・メール等を自動蓄積） |

> **スキル数について**：GitHubのリポジトリ説明文には「23 opinionated tools」と記載されているが、`docs/skills.md`に掲載されているスキルは現在**46件**。リリース後に継続追加されている。

**今すぐ使える推奨アクション3点**

1. **gstackをインストール**：`git clone`でセットアップ。`/office-hours`・`/review`・`/careful` から始めるだけでよい
2. **gbrainのPGLiteローカルモードを試す**：複数リポの作業記録を横断検索できるようになる
3. **ETHOS.mdを読む**：Garry Tanの設計哲学が凝縮されており、自分のCLAUDE.mdに転用できる考え方が多い

---

## 2. 公開ファイル構成

### gstack のファイル構造

```
~/.claude/skills/gstack/     ← インストール先（正確なパス）
├── CLAUDE.md                ← プロジェクト開発ルール（テスト・ビルド・セキュリティ）
├── ETHOS.md                 ← 設計哲学（必読）
├── setup                    ← 一括インストールスクリプト（冪等）
├── docs/skills.md           ← 全46スキル一覧リファレンス
└── [各スキルディレクトリ]/SKILL.md
```

**インストール（正確なコマンド）**：

```bash
git clone https://github.com/garrytan/gstack ~/.claude/skills/gstack
cd ~/.claude/skills/gstack
./setup
```

**ワークフロー全体の流れ**

```
/office-hours（需要検証）
  → /plan-ceo-review（CEO視点でプロダクト再考）
  → /plan-eng-review（技術設計レビュー）
  → /plan-design-review（UI/UX評価）
  → 実装
  → /review（スタッフエンジニアレビュー）
  → /qa（ブラウザQA）
  → /ship（リリース・PR作成）
  → /land-and-deploy（マージ→デプロイ→ヘルスチェック）
  → /retro（振り返り）
```

### ETHOS.md の重要哲学

| 原則 | 内容 |
|------|------|
| **Boil the Lake** | 完全性が僅かなコストで実現できるなら、必ず完全に実装する |
| **Search Before Building** | 既存パターン→最新プラクティス→第一原理の順で探索してから実装 |
| **User Sovereignty** | AIは提案のみ。判断は常にユーザー。どのモデルが同意しても例外なし |
| **Build for Yourself** | 仮定的汎用解より、実際の自分の問題を解決する |

---

## 3. 全46スキル × あなたへの活用アイデア

### カテゴリA：プロダクト設計・検証（コードを書く前に）

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/office-hours` | YCスタイルで「本当に作る必要があるか？」を6つの強制質問で検証 | 新しいGitHubリポを作る前に実行。「このツール、自分以外の誰が使うか？」を明確にしてからコードに入れる |
| `/plan-ceo-review` | CEO/創業者視点でプロダクトの本質的な問題を再考 | 複数リポが乱立しているときに「このリポは本当に必要か？既存リポとマージできないか？」を判断する |
| `/autoplan` | CEO→デザイン→エンジニアのレビューを1コマンドで連続実行 | 新機能の設計フェーズをまとめて終わらせたいとき。3つの視点レビューが1回で完了 |
| `/design-consultation` | デザインシステムをゼロから構築するクリエイティブパートナー | 複数リポでUIが統一されていないとき、共通デザインシステムの方針を決める |
| `/plan-tune` | AskUserQuestion感度を自動チューニング | Claude Codeがしつこく質問してくると感じたら実行してチューニング |

### カテゴリB：コードレビュー・品質管理

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/review` | スタッフエンジニア視点で「CIは通るが本番でバグになるコード」を発見 | 全リポのPR作成前に標準実行。並列サブエージェントがセキュリティ・パフォーマンス・保守性を同時チェック |
| `/investigate` | 仮説検証による根本原因デバッグ | バグが再現しないときや「なぜ壊れたか分からない」ときに使う |
| `/health` | 重み付き0-10スコアでコード品質ダッシュボードを生成 | 複数リポの健全度を定期的に比較し、最もリファクタリングが必要なリポを特定 |
| `/codex` | Claude + OpenAI Codexによるクロスモデルレビュー | 重要な実装は2つのAIモデルで独立レビューして安全性を高める |
| `/benchmark-models` | Claude vs GPT vs Geminiでモデル比較 | どのタスクにどのAIモデルが最適かを実測値で判断する材料に |

### カテゴリC：テスト・QA

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/qa` | Playwrightで実ブラウザQAを実行しバグを発見・修正 | WebアプリのデプロイまえにE2Eテストを自動実行。「ボタンが押せない」などの画面バグをClaude自身が発見する |
| `/qa-only` | コード変更なしのバグレポートのみ生成 | 「今の状態を記録したい」「修正はまだしたくない」ときに使う |
| `/browse` | スクリーンショット付きの実Chromiumブラウザ操作 | 本番サイトの表示確認、競合他社サイトの調査、スクレイピング前の動作確認 |
| `/scrape` | ウェブページからデータを取得（約200ms） | 複数サイトの情報収集、価格比較、リサーチ系タスクの自動化 |
| `/skillify` | スクレイピングのプロトタイプをコード化されたスクリプトに変換 | 1回限りのスクレイピングを定期実行スクリプトに昇格させる |

### カテゴリD：デザイン・UI

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/plan-design-review` | 7次元でデザインプランを評価（AIスロップ拒否付き） | UI変更のPRを出す前に実行。「よくある凡庸なデザイン」を弾いてくれる |
| `/design-review` | 80項目チェックリストでライブサイトを視覚的に監査 | 本番サイトのデザイン品質を定期チェック |
| `/design-shotgun` | 複数のデザインバリアントを比較生成 | UIの方向性を決める前に、複数案を並べて見比べる |
| `/design-html` | 本番品質のHTMLを生成 | プロトタイプを素早く作りたいとき |
| `/devex-review` | TTHW（Time To Hello World）計測付きの開発者体験監査 | 自分のOSSリポのREADMEやセットアップ手順が初心者に分かりやすいか検証 |

### カテゴリE：リリース・デプロイ

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/ship` | mainとの同期→テスト→カバレッジ確認→push→PRオープンまで一括 | 「PRを出す」という繰り返し作業を1コマンドで終わらせる |
| `/land-and-deploy` | PRマージ→デプロイ→本番ヘルス確認まで自動 | 承認済みPRのマージ・デプロイを手動でやっていたら、これで自動化 |
| `/canary` | デプロイ後のエラーとリグレッションを監視 | 「デプロイしたけど大丈夫か？」をClaude自身が確認してくれる |
| `/landing-report` | 全リポのshipキュー状況を読み取り専用で表示 | 複数リポで「どのPRがどの状態か」を一覧で把握 |
| `/benchmark` | ページロード時間とWeb Vitalsを計測 | パフォーマンスの基準値を記録して改善前後を比較 |

### カテゴリF：セキュリティ・安全ガード

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/cso` | OWASP Top 10 + STRIDE脅威モデリング監査 | 認証・決済・個人情報を扱うリポで定期実行するセキュリティ監査 |
| `/careful` | 破壊的コマンドの前に警告を追加 | **CLI初心者に最重要**。`rm -rf`や`DROP TABLE`前に「本当にやるか？」を確認してくれる |
| `/freeze` | ファイル編集を単一ディレクトリに制限 | 「このファイルは絶対触らないで」という制約をClaude Codeに課す |
| `/guard` | `/careful` + `/freeze` を同時有効化 | 複数リポ操作のとき、Claude Codeに最大限の安全制約をかけたい場合 |
| `/unfreeze` | `/freeze`の制約を解除 | 制約を外すときに使う |

### カテゴリG：セッション・メモリ管理（複数リポユーザーに特に有効）

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/learn` | 学習パターンとプロジェクト固有の設定をClaude Codeに記憶させる | 「このリポではTypeScript strict modeを使う」「コメントは日本語で」という自分のルールを蓄積 |
| `/context-save` | 現在の作業コンテキストを保存（セッション跨ぎ対応） | 「今日はここまで」という状態を保存し、翌日どこから再開するか迷わない |
| `/context-restore` | 保存済みコンテキストから別リポのワークスペースで再開 | 複数リポを行き来するときに「前回の状態」を素早く復元 |
| `/setup-gbrain` | gbrainとのメモリ同期をセットアップ | gbrainをインストール後、このコマンドで紐付けを完了させる |
| `/sync-gbrain` | リポジトリの最新コードに合わせてgbrainを更新 | コードを変更したら実行してgbrainの知識を最新状態に保つ |

### カテゴリH：ドキュメント・その他

| コマンド | 説明 | あなたへの活用アイデア |
|---------|------|----------------------|
| `/document-release` | 出荷した変更に合わせてドキュメントを自動更新 | READMEやCHANGELOGの更新を忘れずに済む。リリースのたびに自動で同期 |
| `/retro` | メトリクス付きの週次振り返りを生成 | 週末に実行して「今週何をやったか・何が遅れたか」を記録する習慣に |
| `/make-pdf` | MarkdownをPDF化 | このdeep-researchリポのレポートをPDFで共有したいときに使える |
| `/plan-devex-review` | 開発者体験レビュー（プランステージ） | 新しいツールやAPIの設計段階で「使いやすいか？」を検証 |
| `/pair-agent` | リモートAIエージェントとブラウザをペアリング | 外部AIエージェントとClaude Codeを連携させる高度な使い方 |

### CLI初心者にまず使うべき5スキル

```
Week 1: /careful → すべての操作で安全を確保（必須）
Week 1: /review  → PRを出す前に自動レビュー
Week 2: /office-hours → 新しいものを作る前に需要を確認
Week 2: /context-save / /context-restore → 複数リポ切り替えをスムーズに
Week 3: /ship → PRの作成・pushを自動化
```

---

## 4. gbrain永続知識ベース：あなたへの活用シナリオ

### gbrainとは

AIエージェントが会議・コード・設計決定を自動記録し、後から横断検索できるデータベース。Claude CodeのMCPサーバーとして動作するため、会話中に過去の知識を自動で参照できる。

```bash
# PGLiteローカルモード（ローカルPC完結）
git clone https://github.com/garrytan/gbrain ~/.gbrain
cd ~/.gbrain && bun install && bun link

# Claude CodeにMCPサーバーとして登録
claude mcp add gbrain -- gbrain serve

# gstackと連携（gstackインストール後）
/setup-gbrain
```

### シナリオA：複数リポの「設計判断」を記憶する

**問題**：`web-app`で「なぜこのアーキテクチャにしたか」を `api-server` のセッションで覚えていない  
**gbrainの解決策**：`/sync-gbrain` を実行するたびに、各リポの設計決定・コメント・CLAUDE.mdが知識として蓄積される

```
# web-appで設計決定後
cd ~/projects/web-app && /sync-gbrain

# api-serverのセッションで自動参照
> web-appでの認証方式の決定内容を教えて
← gbrainが「JWT + Refresh Token方式、理由はスマホ対応のため」と返答
```

### シナリオB：「先週解決したバグ」を別リポで再利用する

**問題**：2週間前に解決したnode_modulesの依存エラーを、別リポで同じエラーが出たとき再び調べている  
**gbrainの解決策**：解決方法がgbrainに蓄積されているので、「前に同じエラーを解決したときの方法は？」と聞けば即答

### シナリオC：Web版Claude Code→CLI移行中の学習記録

**問題**：CLI初心者として学んだコマンドやTipsを忘れてしまう  
**gbrainの解決策**：`/learn` コマンドで「今日学んだこと」を記録。自分専用ナレッジベースになる

```
/learn
> git conflictの解消をClaude Codeに任せたとき、--oursフラグの意味を学んだ
← 次回のセッションで「--oursって何だったっけ？」と聞けばgbrainが回答
```

### シナリオD：deep-researchリポとの連携

このリポジトリの過去レポート一覧をgbrainに記録することで：
- 「AIスタートアップについて過去に調査したか？」→ 自動で2026-04-20のレポートを参照
- 「このテーマ、以前似た調査をしていないか？」→ 重複調査を防止
- 次のリサーチテーマを検討するとき、過去の知見を自動で引き継げる

### リポジトリ別の信頼ポリシー設定

```json
// ~/.gstack/gbrain-repo-policy.json
{
  "~/projects/production-app": "read-only",
  "~/projects/experiments": "read-write",
  "~/deep-research": "read-write",
  "~/projects/archived": "deny"
}
```

---

## 5. ユーザープロフィール適合度評価

| 評価軸 | 適合度 | 理由 |
|--------|--------|------|
| CLI初心者への学習コスト | ★★★★☆ | `/careful`/`/guard`が安全ガードになる。コマンドは直感的 |
| Warp連携適性 | ★★★★★ | SessionStartフック（`--team`）でWarp起動時に自動同期 |
| 複数GitHubリポ横断 | ★★★★★ | `/context-save`/`/context-restore` + gbrainで知識が横断 |
| 外出先・モバイル操作 | ★★★☆☆ | gbrainはPC-localのため初期設定はPC必須。Warp Remote Controlと組み合わせると強力 |
| Web版からの移行容易性 | ★★★★☆ | ETHOS哲学は考え方として即日適用可。CLIインストールは1時間以内に完了 |

---

## 6. 移行ロードマップ

### Week 1：インストールと安全ガード習得

```bash
git clone https://github.com/garrytan/gstack ~/.claude/skills/gstack
cd ~/.claude/skills/gstack
./setup
```

最初の3コマンド（順番通りに）：

```
/careful  → 全操作に安全ガードをかける（CLI初心者は最初に設定）
/review   → PRを出す前に必ず実行する習慣をつける
/office-hours → 新しいリポを作る前、新機能を作る前に実行
```

### Week 2：コンテキスト管理とCLAUDE.md設定

ETHOS.mdから転用できるCLAUDE.md記述例：

```markdown
# CLAUDE.md

## 基本哲学
- Search Before Building：実装前に既存パターンを必ず調査する
- AIは提案のみ。最終判断は常に自分が行う

## 安全ルール
- 破壊的操作は /careful を必ず付けること
- 本番リポでは /guard を有効にして作業する
```

セッション管理の習慣化：

```
作業開始時: /context-restore で前回の状態を復元
作業終了時: /context-save で状態を保存
```

### Week 3：gbrain導入と横断記憶

```bash
git clone https://github.com/garrytan/gbrain ~/.gbrain
cd ~/.gbrain && bun install && bun link
claude mcp add gbrain -- gbrain serve
/setup-gbrain   # gstackとgbrainを紐付け
```

各リポで作業後に `/sync-gbrain` を実行する習慣をつける。

### Week 4：自動化とSessionStartフック

```bash
# --teamフラグでSessionStartフックを自動登録
cd ~/.claude/skills/gstack && ./setup --team
# → Warpを開いてclaudeを起動するだけで、毎回最新状態に自動同期
```

---

## 7. よく使うコマンドチートシート

| フェーズ | コマンド | タイミング |
|---------|---------|----------|
| 企画 | `/office-hours` | 新機能・新リポを作る前 |
| 企画 | `/autoplan` | 設計を一気に固めたいとき |
| 実装中 | `/careful` | 削除・DB操作・本番操作の前 |
| 実装中 | `/freeze` | 触ってはいけないファイルがあるとき |
| 実装後 | `/review` | PR作成前（必須） |
| 実装後 | `/cso` | セキュリティ確認が必要なとき |
| テスト | `/qa` | リリース前のE2Eテスト |
| リリース | `/ship` | PR作成・push を一括実行 |
| 振り返り | `/retro` | 週次で進捗を記録 |
| 状態管理 | `/context-save` | 作業終了時 |
| 状態管理 | `/context-restore` | 作業再開時 |
| 知識同期 | `/sync-gbrain` | コード変更後 |

---

## 8. 参考リンク（全URL生存確認済み・2026-05-11）

| リソース | URL |
|---------|-----|
| garrytan/gstack | [github.com/garrytan/gstack](https://github.com/garrytan/gstack) |
| garrytan/gbrain | [github.com/garrytan/gbrain](https://github.com/garrytan/gbrain) |
| gstack 全スキル一覧 | [gstack/docs/skills.md](https://github.com/garrytan/gstack/blob/main/docs/skills.md) |
| gstack ETHOS（哲学） | [gstack/ETHOS.md](https://github.com/garrytan/gstack/blob/main/ETHOS.md) |
| gbrain セットアップ | [gbrain/INSTALL_FOR_AGENTS.md](https://github.com/garrytan/gbrain/blob/master/INSTALL_FOR_AGENTS.md) |
| gbrain×gstack統合 | [gstack/USING_GBRAIN_WITH_GSTACK.md](https://github.com/garrytan/gstack/blob/main/USING_GBRAIN_WITH_GSTACK.md) |
| TechCrunch 解説記事 | [Why Garry Tan's Claude Code setup has gotten so much love, and hate](https://techcrunch.com/2026/03/17/why-garry-tans-claude-code-setup-has-gotten-so-much-love-and-hate/) |

---

## 付録：ファクトチェック結果

| 主張 | 判定 | 実際 |
|------|------|------|
| gstack 93,100+スター | ✓ 概ね正確 | 約93,000（変動中）。初期報道では89.7kだったが急増中 |
| gbrain 14,600+スター | ✓ 正確 | 一致 |
| 「23スキル＋8パワーツール」 | △ 古い情報 | GitHubのREADMEには今も「23 opinionated tools」と記載されているが、`docs/skills.md`には現在**46スキル**が掲載されており大幅に増加 |
| インストール先 `~/.gstack` | ✗ 誤り（本版で修正済み） | 正しくは `~/.claude/skills/gstack` |
| 全参考リンク | ✓ 全件live | 2026-05-11時点で全URL生存確認済み |
| TechCrunch記事 2026-03-17 | ✓ 存在する | 記事内のスター数は「nearly 20,000」（公開直後の数値） |
