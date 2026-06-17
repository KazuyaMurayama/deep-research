# Cursor で OpenAI Codex の機能を使う方法（最短手順ガイド）

作成日: 2026-06-17
最終更新日: 2026-06-17（v2：ファクトチェック反映＋GitHubリポジトリ連携セクション追加）

> 高校生でもそのままなぞれば動かせる「最短手順」と「所要時間」をまとめたガイドです。
> 公式ドキュメント＋実機解説記事を相互照合のうえ手順を組み立て、引っかかりやすいポイントは末尾の「検証と修正メモ」に反映済みです。
>
> **v2 での主な改訂**：① Node.js 必須バージョンを v22 以上に修正（公式現行要件）、② `npm i -g codex`（スコープなし）を実行してしまうと無関係な 2012 年製パッケージが入る罠を警告に追加、③ Agent モードの権限境界を明文化、④ 新セクション「§5 既存 GitHub リポジトリを Codex に引き継いで検証する方法」追加（ローカル拡張機能／Codex Cloud／PR レビュー連携の3方式比較）。

---

## 0. 結論（まずこれだけ）

**いちばん簡単な方法 = Cursor に「OpenAI Codex 拡張機能」を入れて ChatGPT アカウントでログインする**。これで AI にコードを書かせたり直させたりできるようになります。

- 所要時間：**約 5〜10 分**（インターネット接続が良好で、ChatGPT 有料プランに既加入の場合）
- 必要なもの：① Cursor がインストール済みの PC、② ChatGPT の有料プラン（Plus / Pro / Business / Edu / Enterprise のどれか1つ）または OpenAI API キー

「ターミナルから黒い画面で操作したい」「より自由に動かしたい」人向けに、**手順 B：Codex CLI を Cursor のターミナルで使う方法**も後半に記載しています。

---

## 1. そもそも何ができる？（30秒で把握）

| 用語 | 意味（高校生向け） |
|---|---|
| **Cursor** | VS Code をベースにした「AI が標準で組み込まれているコードエディタ」。 |
| **OpenAI Codex** | OpenAI（ChatGPT の会社）が出している **コードを書く専用 AI エージェント**。2025〜2026年に大きく刷新され、CLI（ターミナル版）・IDE 拡張機能・クラウド版がある。 |
| **Cursor × Codex の意味** | Cursor の中に「ChatGPT の Codex くん」を住まわせて、ファイルを読ませたり書き換えさせたりできるようにすること。**Cursor 標準の AI とは別物**で、両方併用してもよい。 |

---

## 2. 前提条件チェック（30秒）

- [ ] **Cursor がインストール済み**（未インストールなら https://cursor.com からダウンロード）
- [ ] **Codex の実行枠を持つ ChatGPT 有料プラン**（Plus / Pro / Business / Edu / Enterprise）に加入済み、または **OpenAI API キー**（クレジット残高あり）を持っている
- [ ] インターネットに接続できる
- [ ] （手順 B：CLI を使う場合のみ）**Node.js v22 以降** が入っている

> 無料の ChatGPT アカウントでも拡張機能のサインイン UI 自体は動く場合がありますが、**実際に Codex を動かすには有料プラン枠または API キーのいずれかが必要**です。API キーで使うと、使った分だけ従量課金になります。

---

## 3. 手順 A：IDE 拡張機能で Codex を使う（**最も簡単・推奨**）

### A-1. 拡張機能をインストールする（約 1 分）

1. **Cursor を起動**
2. 画面左の縦に並んだアイコンの中から、四角が組み合わさったマークの **「Extensions（拡張機能）」** をクリック
   - もしくはショートカット：Windows / Linux は `Ctrl + Shift + X`、Mac は `⌘ + Shift + X`
3. 上の検索ボックスに **`OpenAI Codex`** と入力
4. 一覧に「**OpenAI Codex**」（発行元：OpenAI、識別子：`openai.chatgpt`）が出てくるのでクリック
5. 右側に表示された **「Install」** ボタンを押す

> ✅ 公式の入手元は VS Code Marketplace。Cursor は VS Code 互換なので同じ拡張機能がそのまま動きます。

### A-2. サインインする（約 1〜2 分）

6. インストールが終わると、サイドバーに **Codex のアイコン**が追加される（見当たらないときは §7 トラブルシュート参照）
7. Codex アイコンをクリックすると、サイドパネルが開いてサインイン画面が出る
8. **「Sign in with ChatGPT」**（推奨）を選ぶ
   - ブラウザが開き、ChatGPT のログイン画面に遷移する
   - メールアドレス＋パスワード（または Google などのソーシャルログイン）でログイン
   - OAuth 認可画面（"Authorize Codex" 等）で **「Allow / Authorize」** をクリック
   - 「You can close this window（このウィンドウは閉じてOK）」と出たら閉じる
9. Cursor に戻ると Codex パネルに **サインイン済み表示**（"You're signed in" 等）が出る

> API キーで使う場合は、サインイン画面の **「Use API key」** を選び、`sk-...` で始まるキーを貼り付け。OpenAI Platform（platform.openai.com）でキーを発行し、クレジットを購入しておく必要があります。

### A-3. オンボーディング画面を進める（約 30 秒）

10. 「Welcome to Codex」のような説明が数枚出るので **「Next」→「Get started」** で読み進める

### A-4. 最初のプロンプトを送って動作確認する（約 1〜2 分）

11. 作業したいフォルダを Cursor で開いていることを確認（File → Open Folder）
12. Codex パネル下部の入力欄に、まず安全なお試しとして次のように入力：

    ```
    このプロジェクトの構成をざっと説明して
    ```

13. **Enter** または送信ボタンで送る
14. Codex がファイルを読みに行き、要約を返してくれれば成功 ✅

### A-5. コードを書かせる／直させる（実践）

15. 入力欄に「**何をしたいか日本語で**」書く。ファイルを直接指定したいときは **`@ファイル名`** と打つと候補がポップアップする
    - 例：`@app.py に簡単なログイン機能を追加して`
    - 例：`@style.css のボタン色を青に変えて`
16. Codex が **変更プランと差分（diff）** を提案する
17. 内容を読み、よければ **「Accept / Apply」** をクリックして反映、嫌なら **「Reject / Discard」**

### A-6. モードを使い分ける（覚えておくとよい）

入力欄の近くに「モード切替」がある：

| モード | 何をする？ | いつ使う？ |
|---|---|---|
| **Chat** | 相談だけ。ファイルは書き換えない | 設計を考えてもらう、質問するだけのとき |
| **Agent**（既定） | プロジェクト**内**のファイル編集・コマンド実行は自動。プロジェクト外への変更や**ネットワークアクセスは承認待ち** | 実装を任せたいとき |
| **Agent (Full Access)** | 承認プロンプトを最小化して最大限自律的に実行する強権限モード（ネット・プロジェクト外操作も承認待ちしない） | `npm install` など外部通信が必要で、内容を把握済みのとき |

> 初心者はまず **Chat** → 慣れたら **Agent** が安全です。

---

## 4. 手順 B：Cursor のターミナルから Codex CLI を使う（自由度重視）

「ターミナル黒画面派」や「拡張機能をなるべく入れたくない人」向け。**手順 A だけで十分な人はここは飛ばして OK。**

### 前提

- **Node.js v22 以降** がインストール済み（無ければ https://nodejs.org の LTS 版を入れる。2026 年現行の `@openai/codex` は v22+ を要求）
- ターミナルで `node -v` と打って `v22.x` 以上が出ること
- ⚠️ **罠**：`npm i -g codex`（スコープなし）と打つと **OpenAI とは無関係な 2012 年製の同名パッケージ**が入ります。必ず後述の `@openai/codex`（スコープ付き）を指定すること

### B-1. Cursor のターミナルを開く（約 10 秒）

1. Cursor のメニュー：**Terminal → New Terminal**（または `` Ctrl + ` ``）

### B-2. Codex CLI をインストール（約 1〜2 分）

公式は **ネイティブインストーラ（推奨）** と **npm 経由** の 2 通りを提示しています。どちらでもOK。

**(a) 公式インストーラ（推奨・Node.js 不要）**

macOS / Linux：
```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

Windows（PowerShell）：
```powershell
irm https://chatgpt.com/codex/install.ps1 | iex
```

**(b) npm 経由**（Node.js v22 以降が必要）：
```bash
npm install -g @openai/codex
```

インストール後、確認：
```bash
codex --version
```
バージョンが出れば成功 ✅

### B-3. サインイン（約 1 分）

3. ターミナルで：
   ```bash
   codex
   ```
4. 起動した TUI（ターミナル UI）の案内に従ってサインイン（初回は `/login` コマンドで認証フローを開始する形式）
5. ChatGPT を選ぶとブラウザが開き、ログイン → OAuth 認可 → ターミナルに戻る
6. プロンプトが返ってきたらサインイン完了 ✅

### B-4. 使う（実践）

6. Cursor で開いている作業フォルダ（プロジェクトのルート）に **cd 済み** であることを確認
7. プロンプトを直接日本語で打つ：

   ```
   このプロジェクトのバグを探して直して
   ```

8. Codex がファイルを読み、変更案を提示。ターミナル上で **y/n** で承認／却下する

### B-5. 役立つコマンド（最低限）

| コマンド | 効果 |
|---|---|
| `codex` | 対話セッション開始 |
| `codex "<指示>"` | ワンショットで指示実行 |
| `codex --help` | 全コマンド一覧（最も信頼できる一次情報） |
| Ctrl + C | 中断 |
| `/login` / `/logout` | 認証 |
| `/exit` または `/quit`（環境による） | セッション終了 |

> ⚠️ TUI 内コマンドは版によって名前が変わることがあります。詳細は必ず `codex --help` で確認してください。

---

## 5. 既存 GitHub リポジトリを Codex に引き継いで検証を進める方法

「すでに GitHub に置いてあるリポジトリ」を Codex に読み込ませ、コード理解 → テスト実行 → バグ修正 → PR 作成まで任せたいときの方法は、**用途に応じて3通り**あります。最も簡単なのは方式 ①。

### 5-0. どれを選ぶ？（30秒で決める）

| 方式 | 何が起きる | 向いている用途 | 所要時間 |
|---|---|---|---|
| ① **ローカルで clone → Cursor の Codex 拡張機能で操作** | 自分の PC 上でファイルが書き換わる。Git は自分で操作 | 細かく動作確認しながら進めたい、私用・小規模リポ | 約 5〜10 分 |
| ② **Codex Cloud（chatgpt.com/codex）に GitHub 接続** | クラウドのサンドボックスでリポをクローンし、Codex が独立に作業 → PR で返ってくる | 大きめのタスクをバックグラウンド実行、複数並列、出先で iPhone からも指示したい | 約 10〜15 分（初回設定込み） |
| ③ **GitHub の PR 上で `@codex review` / `@codex fix`** | 既存 PR のコメント欄に Codex を呼び出してレビュー・修正コミットさせる | 同僚（人間）の PR を AI に追加レビューさせる、レビュー指摘の機械的な修正 | 約 5 分（事前に ② の接続が必要） |

> ✅ **おすすめ初回ルート**：まず ① を 1 回通してから、慣れたら ② に進む。③ は ② を有効化すると自動で使える。

---

### 5-1. 方式 ①：ローカル clone → Cursor の Codex 拡張機能で検証する

#### 手順（約 5〜10 分）

1. **GitHub からリポジトリ URL をコピー**
   - 対象リポの「Code」緑ボタン → HTTPS タブの URL をコピー（例：`https://github.com/yourname/yourrepo.git`）

2. **PC のターミナル（または Cursor のターミナル）でクローン**
   ```bash
   git clone https://github.com/yourname/yourrepo.git
   cd yourrepo
   ```
   - プライベートリポなら事前に `gh auth login` か SSH 鍵設定済みであること

3. **Cursor で開く**
   - File → Open Folder → 今クローンしたフォルダを選ぶ

4. **AGENTS.md を用意（強く推奨・約 2 分）**
   - リポジトリの **ルート直下** に `AGENTS.md` というファイルを新規作成
   - 中に「テストの走らせ方」「依存インストール方法」「PR 前のチェックリスト」「触ってほしくないファイル」を書く
   - 最小テンプレ（コピペでOK）：

     ```markdown
     # Agent Instructions

     ## このリポについて
     （1〜2 行で目的を書く）

     ## セットアップ
     - 依存インストール: `npm install`
     - 環境変数: `.env.example` をコピーして `.env` を作成

     ## テスト
     - 全テスト実行: `npm test`
     - 1ファイルだけ: `npm test -- path/to/file.test.ts`

     ## PR 前のチェック
     - [ ] `npm test` がすべて通る
     - [ ] `npm run lint` がエラーなし
     - [ ] 触ってほしくないファイル：`migrations/` 配下、`legacy/` 配下

     ## コーディング規約
     - インポートは alphabetical
     - 関数コメントは英語
     ```
   - Codex は実行時に毎回これを読み込みます（公式仕様：実行ごとに再構築）

5. **Codex 拡張機能を開いて「Chat」モードで状況把握**
   ```
   このプロジェクトの全体像と、最近のコミット履歴の要点を教えて
   ```
   - 数十秒〜1分で要約が返ってきたら準備完了 ✅

6. **「Agent」モードに切り替えて検証作業を依頼**
   - 例 1：`npm test を実行して、失敗しているテストがあれば原因を特定して修正案の diff を出して`
   - 例 2：`@src/auth.ts のリファクタリング案を出して。テストは壊さない範囲で`
   - 例 3：`README に従ってセットアップを再現し、書かれていない手順や古い記述があれば PR 形式で修正案を出して`

7. **Codex の提案を Accept / Reject で取り込み**
   - 変更ごとに diff が出るので、内容を確認して採用
   - 不要な変更は Reject

8. **Git でブランチを切ってコミット → push → GitHub 上で PR**
   ```bash
   git checkout -b fix/codex-suggested
   git add -A && git commit -m "fix: Codex の提案を反映"
   git push -u origin fix/codex-suggested
   ```
   - GitHub の Web 画面で「Compare & pull request」ボタンが出る → PR 作成

> 💡 **コツ**：Codex に「git コミット作業まで任せる」こともできます（Agent モードでコミット用コマンドを実行）。ただし、初回は自分で Git 操作する方が**何が変わったか**が把握しやすいです。

---

### 5-2. 方式 ②：Codex Cloud（chatgpt.com/codex）に GitHub を接続する

ブラウザだけで完結。PC のスペックを使わず、複数タスクをバックグラウンド並列実行できます。

#### 手順（初回約 10〜15 分、2回目以降は約 1 分）

1. **ブラウザで `https://chatgpt.com/codex` を開く**
   - ChatGPT に未ログインなら先にログイン（Plus/Pro/Business/Edu/Enterprise プランが必要）

2. **「Connect GitHub」または「環境を作成」ボタンをクリック**
   - 初回のみ GitHub の OAuth 画面に遷移し、**「ChatGPT connector」アプリ**の認可を求められる
   - 「All repositories」または「Only select repositories」（推奨）から対象リポを選んで **Authorize**
   - チーム/Org のリポを使う場合は、Org 管理者が GitHub の Settings → Third-party access で承認している必要あり

3. **Environment（環境）の設定**（リポごとに 1 回）
   - 「**Repository**」：先ほど認可したリポ一覧から選択
   - 「**Default branch**」：通常 `main`。`develop` 運用なら `develop` に変更
   - 「**Setup script**」：必要な依存インストールやビルドコマンドを記述
     - 例（Node）：
       ```bash
       npm install
       npm run build --if-present
       ```
     - 例（Python）：
       ```bash
       pip install -e ".[dev]"
       pytest --collect-only
       ```
   - 「**Environment variables**」：`NODE_ENV=test` 等、平文で保管されてOKな値
   - 「**Secrets**」：API キーなど暗号化が必要な値。**セットアップスクリプト中でのみ参照可**（タスク実行時のシェルからは見えない）
   - 「Save」で確定

4. **AGENTS.md がリポにあれば自動参照される**
   - § 5-1 のテンプレで作っておけば、Cloud 側でもそのまま効く

5. **タスクを投げる**
   - 上部の入力欄に日本語で指示：
     ```
     test/auth.spec.ts が落ちている原因を特定し、最小修正で直して PR を出して
     ```
   - 「Run」または Enter

6. **進捗をブラウザで監視**
   - Codex がクラウドコンテナ内でリポをクローンし、セットアップスクリプトを実行
   - ファイルを読み、コマンドを走らせ、変更を加える様子がリアルタイム表示

7. **自動で PR が作成される**
   - 完了画面に **「Open pull request」リンク**が出る
   - クリックすると GitHub の PR ページに飛び、レビュー → Merge できる

#### 方式 ② の強み
- 自分の PC を消費しない（移動中でも放置できる）
- **複数タスク並列**（A タスクの PR を待ちながら B タスクも投げられる）
- iPhone の ChatGPT アプリからもタスク投入可

#### 方式 ② の注意
- GitHub に置いたリポでないと使えない（オンプレ / GitLab / Bitbucket は不可）
- 大規模リポはセットアップスクリプトのキャッシュが効くが、初回は数分かかる
- シークレットを `echo $SECRET` でログに出さないよう注意（ログは ChatGPT 側に保存される）

---

### 5-3. 方式 ③：既存 PR で `@codex review` / `@codex fix` を使う

**事前条件**：方式 ② の GitHub 接続が完了していること＋ Codex 設定で「Code Review」トグルが ON。

#### 手順（1 件約 5 分）

1. **GitHub の対象 PR を開く**
2. **コメント欄に次のいずれかを入力して送信**
   - `@codex review` → 自動レビューコメントを投稿
   - `@codex review focus on security` → 観点指定レビュー
   - `@codex fix the P1 issue` → 指摘事項を直接修正コミットして push（ブランチ書き込み権限が必要）
3. **Codex が 👀 リアクションをつけ、数十秒〜数分で結果コメント／コミットを返す**
4. **自動レビューを毎 PR に効かせたい場合**は、Codex 設定で **「Automatic reviews」** を ON
5. **レビュー観点をリポ固有にチューニングしたい場合**は、AGENTS.md に「Review guidelines」セクションを追記
   ```markdown
   ## Review Guidelines for Codex
   - 必ず指摘する：N+1 クエリ、未処理の Promise、any 型の濫用
   - 指摘しなくてよい：コメント文体、import 順（Lint で見ている）
   ```

---

### 5-4. 3方式まとめ（迷ったら）

```
                                    ┌─ ローカル PC で細かく確認したい
                                    │   → 方式 ① (IDE 拡張機能)
既存 GitHub リポを Codex で検証 ────┼─ 大きめタスクをクラウドに任せたい / 並列実行
                                    │   → 方式 ② (Codex Cloud)
                                    └─ チーム PR のレビューを AI で補強
                                        → 方式 ③ (@codex review)
```

> 🔑 **どの方式でも、AGENTS.md をリポルートに置いておくと精度が劇的に上がります**。これが Codex に対する「リポの取扱説明書」になります。

---

## 6. 所要時間まとめ

| ステップ | 想定時間 |
|---|---|
| 手順 A：拡張機能インストール～動作確認まで通し | **5〜10 分** |
| 手順 B：CLI インストール～動作確認まで通し | **5〜10 分**（Node.js が未インストールならさらに +5 分） |
| 既に ChatGPT 有料プランも Cursor も準備済みで「とにかく動かす」だけ | **約 3 分** |
| ChatGPT 有料プラン契約から始める場合 | **+ 5〜10 分**（決済とメール確認） |

---

## 7. 困ったとき（よくあるつまずき）

| 症状 | 原因と対処 |
|---|---|
| **Codex アイコンが見当たらない** | Cursor のアクティビティバーが横向き表示だと隠れていることがある。**サイドバーの空きエリアを右クリック → 「Codex」にチェック**。または **設定 → `workbench.activityBar.orientation`** を vertical にしてエディタ再起動 |
| **「Sign in failed」と出る** | ブラウザの広告ブロッカーをオフにして再試行。社用 PC の場合 VPN／プロキシ設定が干渉することあり |
| **無料の ChatGPT アカウントしか持っていない** | IDE 拡張機能は使えません。**API キー**を発行（platform.openai.com → API keys → Create new secret key）し、Billing にクレジット（$5〜）を入れてから「Use API key」でサインイン |
| **`codex: command not found`（CLI 版）** | ① **そもそも `npm i -g codex` で入れてしまった**可能性（無関係パッケージ）→ `npm uninstall -g codex && npm install -g @openai/codex` でやり直し ② グローバル bin の PATH が通っていない → `npm config get prefix` で出るパスに `/bin`（Windows は直下）を足した値を PATH に追加し、ターミナルを開き直す |
| **動くがクレジットを食いつぶしそうで怖い** | Codex の **Chat モード**から始めるか、API キー利用時は OpenAI Platform の **Usage limits** で月上限を設定 |
| **ファイル指定（@）が効かない** | 対象ファイルがプロジェクトに含まれているか確認。Cursor で「Open Folder」したフォルダ配下のみが対象 |
| **Cursor 標準の AI と混乱する** | Cursor 右側の AI チャットは Cursor 製、左から開く Codex パネルは OpenAI 製で別物。どちらを使っているかパネル上部の名前で確認 |

---

## 8. セキュリティ・コスト面の注意（必読）

- **Agent モードはファイルを自動で書き換えます**。コミット前の作業中ファイルがあるなら、先に Git でコミットしておくと事故っても戻せます
- **シークレット（API キー、パスワード）を含むファイルは @ で指定しない**。プロンプトに含まれた情報は OpenAI のサーバへ送信されます
- **API キー利用時は従量課金**。長文ファイルを毎回読ませると料金が膨らみます。`@` で必要なファイルだけ渡すのがコツ
- **ChatGPT プラン利用時はプラン枠内**で使えるが、過度な利用はレート制限がかかります

---

## 9. 検証と修正メモ（透明性のために残す）

本ガイドは以下の方法で手順の正確性を確認しました。

### 確認したこと（OK）
- ✅ **拡張機能の識別子は `openai.chatgpt`**（VS Code Marketplace で「Codex – OpenAI's coding agent」として公開、Publisher = OpenAI）
- ✅ **公式対応エディタ**は VS Code / Cursor / Windsurf。JetBrains 系（IntelliJ/PyCharm/WebStorm/Rider など）にも 2026 年から拡張あり
- ✅ **モード名は Chat / Agent / Agent (Full Access)** が公式表現
- ✅ **Codex CLI のパッケージ名は `@openai/codex`**。`npm install -g @openai/codex`（Node.js v22+）、または公式インストーラ `curl -fsSL https://chatgpt.com/codex/install.sh | sh`（macOS/Linux）／`irm ... | iex`（Windows）
- ✅ **Codex Cloud（chatgpt.com/codex）**：GitHub OAuth で "ChatGPT connector" アプリを認可 →リポジトリ選択 → Environment（リポ・ブランチ・セットアップスクリプト・環境変数・シークレット）設定 → タスク実行で PR を自動作成
- ✅ **AGENTS.md** は Codex が実行ごとに参照する一次的なコンテキスト。リポルートが基本。ディレクトリごとに置いて階層化可能。グローバル `~/.codex/AGENTS.md` も可
- ✅ **GitHub PR 連携**：`@codex review`（レビュー）／`@codex fix the P1 issue`（修正コミット push）。Codex 設定で Automatic reviews を ON にすると全 PR 自動レビュー

### v1 → v2 で修正した点（合計 7 件）
1. **Node.js 要件を v18→ v22 に修正**（npm 経由インストールの公式現行要件）
2. **`npm i -g codex`（スコープなし）の罠を追加** — 無関係な 2012 年製パッケージが入る事故に対する警告
3. **公式インストーラ（curl/irm）を主流路として追加** — npm はサブ手段に格下げ
4. **「無料プランでは拡張機能にサインインできない」断定を緩和** — 公式が明示しているのは「Codex の実行に有料プラン枠または API キーが必要」
5. **Agent (Full Access) の説明を「ネット可否」から「承認プロンプトを最小化する強権限モード」に修正**
6. **アクティビティバーの注意を 3 箇所重複から 1 箇所（§7 トラブルシュート）に集約**
7. **OAuth ダイアログ文言・CLI セッション終了コマンドの断定を緩和**（環境依存・版依存のため）

### 残る不確実性
- 本レポートは PC 上で実機 GUI を自動操作する手段を持たないため、**ボタンのラベル細部（"Install" の日本語化有無、OAuth 同意画面の正確な文言）は環境依存**で異なる場合があります
- Cursor / Codex の UI は頻繁に更新されます。設定キー名（`workbench.activityBar.orientation` 等）が将来変わる可能性あり
- TUI 内コマンド（`/exit` / `/quit`）は版依存。`codex --help` を一次情報として推奨

---

## 10. 参考リンク

### 一次情報（公式）
- [OpenAI Codex 公式 — IDE extension](https://developers.openai.com/codex/ide)
- [OpenAI Codex 公式 — Features](https://developers.openai.com/codex/ide/features)
- [OpenAI Codex 公式 — Quickstart](https://developers.openai.com/codex/quickstart)
- [OpenAI Codex 公式 — Cloud（Web版）](https://developers.openai.com/codex/cloud)
- [OpenAI Codex 公式 — Cloud Environments](https://developers.openai.com/codex/cloud/environments)
- [OpenAI Codex 公式 — GitHub integration](https://developers.openai.com/codex/integrations/github)
- [OpenAI Codex 公式 — AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [OpenAI Codex 公式 — Changelog](https://developers.openai.com/codex/changelog)
- [VS Code Marketplace — Codex（openai.chatgpt）](https://marketplace.visualstudio.com/items?itemName=openai.chatgpt)
- [GitHub: openai/codex（CLI）](https://github.com/openai/codex)
- [npm: @openai/codex](https://www.npmjs.com/package/@openai/codex)
- [Cursor 公式](https://cursor.com)

### 二次情報（実機解説・日本語）
- [Codex CLI を Cursor で使う方法（note.com・Manabu Uekusa 氏 / 実機スクショあり）](https://note.com/mauekusa/n/n3fc3c64e6b3d)
- [CursorでCodex CLIのモデルを使う方法（zenn.dev）](https://zenn.dev/galirage/articles/cursor-codex-cli-setup)
- [Ubuntu 24.04 に Cursor をインストールして OpenAI Codex を入れてみた（Qiita）](https://qiita.com/Marron-chan/items/066e3d6a94787e506d26)

---

著者：男座員也（Kazuya Oza）
リポジトリ：[KazuyaMurayama/deep-research](https://github.com/KazuyaMurayama/deep-research)

**Next Action（推奨）**：
1. まず **§3 手順 A の A-1〜A-4** を 5 分で実施（拡張機能インストール → ChatGPT サインイン → 「このプロジェクトの構成をざっと説明して」で動作確認）
2. 動作確認できたら **§5-1 方式 ①** で、自分の既存 GitHub リポジトリをクローンして AGENTS.md を1枚追加 → Codex に「全体把握 → テスト実行 → 修正案」を依頼
3. 慣れたら **§5-2 方式 ②（Codex Cloud）** を試して、PR が自動で返ってくる体験をする
