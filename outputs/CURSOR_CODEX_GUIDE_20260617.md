# Cursor で OpenAI Codex の機能を使う方法（最短手順ガイド）

作成日: 2026-06-17
最終更新日: 2026-06-17

> 高校生でもそのままなぞれば動かせる「最短手順」と「所要時間」をまとめたガイドです。
> 公式ドキュメント＋実機解説記事を相互照合のうえ手順を組み立て、引っかかりやすいポイントは末尾の「検証と修正メモ」に反映済みです。

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
- [ ] **ChatGPT の有料プラン**（Plus 月額 約 $20〜）に加入済み、または **OpenAI API キー**（クレジット残高あり）を持っている
- [ ] インターネットに接続できる

> 無料の ChatGPT アカウントだけでは Codex IDE 拡張機能にはサインインできません。API キーで使う場合は使った分だけ従量課金になります。

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

6. インストールが終わると、左サイドバー（または画面のどこか）に **Codex のアイコン**（黒い丸に「Codex」）が追加される
   - 見当たらないときは、**サイドバーの「…」（その他のアクション）を押して「Codex」にチェック**を入れる。Cursor は初期状態でアクティビティバーが横向きになっていて Codex アイコンが隠れることがある（後述のトラブルシューティング参照）
7. Codex アイコンをクリックすると、サイドパネルが開いてサインイン画面が出る
8. **「Sign in with ChatGPT」**（推奨）を選ぶ
   - ブラウザが開き、ChatGPT のログイン画面に遷移する
   - メールアドレス＋パスワード（または Google などのソーシャルログイン）でログイン
   - 「Cursor からのアクセスを許可しますか？」と聞かれたら **「Allow / 許可」**
   - ブラウザに「You can close this window（このウィンドウは閉じてOK）」と出たら閉じる
9. Cursor に戻ると Codex パネルに「You're signed in」と表示される

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
| **Agent**（既定） | 自動でファイルを編集・コマンド実行 | 実装を任せたいとき |
| **Agent (Full Access)** | ネットワークアクセスも許可 | npm install など外部通信が必要なとき |

> 初心者はまず **Chat** → 慣れたら **Agent** が安全です。

---

## 4. 手順 B：Cursor のターミナルから Codex CLI を使う（自由度重視）

「ターミナル黒画面派」や「拡張機能をなるべく入れたくない人」向け。**手順 A だけで十分な人はここは飛ばして OK。**

### 前提

- **Node.js（v18 以降）** がインストール済み（無ければ https://nodejs.org の LTS 版を入れる）
- ターミナルで `node -v` と打って `v18.x` 以上が出ること

### B-1. Cursor のターミナルを開く（約 10 秒）

1. Cursor のメニュー：**Terminal → New Terminal**（または `` Ctrl + ` ``）

### B-2. Codex CLI をグローバルインストール（約 1〜2 分）

2. 開いたターミナルに次のコマンドを貼り付けて Enter：

   ```bash
   npm install -g @openai/codex
   ```

   - インストールが終わったら確認：
   ```bash
   codex --version
   ```
   バージョンが出れば成功 ✅

### B-3. サインイン（約 1 分）

3. ターミナルで：
   ```bash
   codex
   ```
4. 初回起動時に「Sign in with ChatGPT / Use API key」を選ぶ画面が出る
5. ChatGPT を選ぶとブラウザが開き、ログイン → 「Allow」 → ターミナルに戻る

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
| `codex --help` | ヘルプ表示 |
| Ctrl + C | 中断 |
| `/exit` | セッション終了 |

---

## 5. 所要時間まとめ

| ステップ | 想定時間 |
|---|---|
| 手順 A：拡張機能インストール～動作確認まで通し | **5〜10 分** |
| 手順 B：CLI インストール～動作確認まで通し | **5〜10 分**（Node.js が未インストールならさらに +5 分） |
| 既に ChatGPT 有料プランも Cursor も準備済みで「とにかく動かす」だけ | **約 3 分** |
| ChatGPT 有料プラン契約から始める場合 | **+ 5〜10 分**（決済とメール確認） |

---

## 6. 困ったとき（よくあるつまずき）

| 症状 | 原因と対処 |
|---|---|
| **Codex アイコンが見当たらない** | Cursor のアクティビティバーが横向き表示だと隠れていることがある。**サイドバーの空きエリアを右クリック → 「Codex」にチェック**。または **設定 → `workbench.activityBar.orientation`** を vertical にしてエディタ再起動 |
| **「Sign in failed」と出る** | ブラウザの広告ブロッカーをオフにして再試行。社用 PC の場合 VPN／プロキシ設定が干渉することあり |
| **無料の ChatGPT アカウントしか持っていない** | IDE 拡張機能は使えません。**API キー**を発行（platform.openai.com → API keys → Create new secret key）し、Billing にクレジット（$5〜）を入れてから「Use API key」でサインイン |
| **`codex: command not found`（CLI 版）** | `npm i -g` の PATH が通っていない。`npm bin -g` で出るパスを環境変数 PATH に追加、またはターミナルを開き直す |
| **動くがクレジットを食いつぶしそうで怖い** | Codex の **Chat モード**から始めるか、API キー利用時は OpenAI Platform の **Usage limits** で月上限を設定 |
| **ファイル指定（@）が効かない** | 対象ファイルがプロジェクトに含まれているか確認。Cursor で「Open Folder」したフォルダ配下のみが対象 |
| **Cursor 標準の AI と混乱する** | Cursor 右側の AI チャットは Cursor 製、左から開く Codex パネルは OpenAI 製で別物。どちらを使っているかパネル上部の名前で確認 |

---

## 7. セキュリティ・コスト面の注意（必読）

- **Agent モードはファイルを自動で書き換えます**。コミット前の作業中ファイルがあるなら、先に Git でコミットしておくと事故っても戻せます
- **シークレット（API キー、パスワード）を含むファイルは @ で指定しない**。プロンプトに含まれた情報は OpenAI のサーバへ送信されます
- **API キー利用時は従量課金**。長文ファイルを毎回読ませると料金が膨らみます。`@` で必要なファイルだけ渡すのがコツ
- **ChatGPT プラン利用時はプラン枠内**で使えるが、過度な利用はレート制限がかかります

---

## 8. 検証と修正メモ（透明性のために残す）

本ガイドは以下の方法で手順の正確性を確認しました。

### 確認したこと（OK）
- ✅ **公式ドキュメント `developers.openai.com/codex/ide`** にて、Codex IDE 拡張機能が **VS Code / Cursor / Windsurf** の3エディタ公式対応であること、ChatGPT 有料プラン（Plus/Pro/Business/Edu/Enterprise）または API キーが必要であることを確認
- ✅ **公式 Quickstart `developers.openai.com/codex/quickstart`** にて、IDE 拡張のセットアップ手順が「拡張機能インストール → サイドパネル開く → サインイン → エージェントモードで作業」の流れであることを確認
- ✅ **拡張機能の識別子は `openai.chatgpt`**（VS Code Marketplace 上の publisher.name）。Cursor から `cursor:extension/openai.chatgpt` 形式の直リンクでもインストール可能
- ✅ **Codex CLI のパッケージ名は `@openai/codex`**（GitHub: openai/codex リポジトリ）。`npm install -g @openai/codex` でインストール可能
- ✅ **`@ファイル名` でのファイル参照、Chat / Agent / Agent (Full Access) の3モード切替**は公式 Features ページに記載あり

### 当初手順から修正した点
1. **「Codex アイコンが必ず左サイドバーに出る」と書きかけていたが修正** → 公式ドキュメントと note.com の実機記事の両方が「Cursor では横向きアクティビティバーで隠れることがある」と注意喚起していたため、トラブルシュート欄に明記
2. **CLI のインストールコマンドを最新化** → 旧コマンド（`@openai/codex-cli` 等の表記揺れ）ではなく、公式 GitHub の現行表記 `@openai/codex` に統一
3. **「無料プランでも IDE 拡張が動く」かのように読める箇所を削除** → 公式が明確に有料プランまたは API キー必須としているため、前提条件と FAQ で明示
4. **モード名を実装に揃えた** → 「Plan モード」ではなく公式名の **Chat / Agent / Agent (Full Access)**

### 残る不確実性
- 本レポートは PC 上で実機操作するハーネス（GUI 操作の自動化）を持たないため、**ボタンの位置やラベルの細部（例：「Install」が「インストール」と日本語化されるか）は環境依存**で異なる可能性があります。手順の本質（検索→Install→サイドバーから開く→ChatGPT でサインイン）は変わりません
- Cursor 側の UI は頻繁に更新されるため、アクティビティバーの設定キー名（`workbench.activityBar.orientation`）が将来変わる可能性があります

---

## 9. 参考リンク

- [OpenAI Codex 公式 — IDE extension](https://developers.openai.com/codex/ide)
- [OpenAI Codex 公式 — Quickstart](https://developers.openai.com/codex/quickstart)
- [OpenAI Codex 公式 — Features](https://developers.openai.com/codex/ide/features)
- [OpenAI Codex 公式 — Changelog](https://developers.openai.com/codex/changelog)
- [GitHub: openai/codex（CLI）](https://github.com/openai/codex)
- [Cursor 公式](https://cursor.com)
- [Codex CLI を Cursor で使う方法（note.com・Manabu Uekusa 氏 / 実機スクショあり）](https://note.com/mauekusa/n/n3fc3c64e6b3d)
- [CursorでCodex CLIのモデルを使う方法（zenn.dev）](https://zenn.dev/galirage/articles/cursor-codex-cli-setup)
- [Ubuntu 24.04 に Cursor をインストールして OpenAI Codex を入れてみた（Qiita）](https://qiita.com/Marron-chan/items/066e3d6a94787e506d26)

---

著者：男座員也（Kazuya Oza）
リポジトリ：[KazuyaMurayama/deep-research](https://github.com/KazuyaMurayama/deep-research)

**Next Action（推奨）**：まず **手順 A** の A-1〜A-4 まで（拡張機能インストール → ChatGPT サインイン → 「このプロジェクトの構成をざっと説明して」と送る）を 5 分で実施し、動作確認できたら A-5 で実際の作業に進んでください。
