# 仮想通貨の取引履歴をダウンロードしよう
### 〜 Bybit・Binance・MetaMask 完全ガイド 〜

**作成日：** 2026年5月8日  
**対象：** 2020年頃から仮想通貨取引をしてきた方  
**難易度：** ★☆☆☆☆（高校生でもできる！）  
**検証済み：** 各公式サイト・サポートページへの実アクセスで確認。3エージェントによる品質レビュー実施済み。

---

## まず全体の流れを確認しよう

このガイドでやることは3ステップです。

```
STEP A：各サービスから「取引履歴のCSV（表のファイル）」を取得する
         ↓
STEP B：税計算ツール（Cryptact等）にCSVをアップロードする
         ↓
STEP C：損益レポートを確認して確定申告に使う
```

このガイドでは **STEP A** を詳しく説明します。

---

## ⚠️ 知らないと損する！税金の基本ルール4つ

CSVを集める前に、この4つだけ頭に入れておきましょう。

| ルール | 説明 |
|--------|------|
| ① 売って利益が出たら申告 | 年間利益が **20万円超**（会社員）なら確定申告が必要 |
| ② **交換も「売却」扱い** | BTCをETHに換えた時点で課税対象。「売らなかった」とはならない |
| ③ すべての取引所分が必要 | Bybit・Binance・MetaMask・GMOコインすべての履歴が必要 |
| ④ **取引所間の送付に注意** | A取引所で買ったコインをB取引所に送ると、Bでの取得原価がゼロ扱いになりやすい（後述） |

> **DeFi・NFT・エアドロップを使った場合は複雑になります。**  
> 税理士（仮想通貨専門）への相談を強くお勧めします。

---

## 第0章：「取引所」と「ウォレット」の違い（重要）

### 取引所（Bybit・Binance）

```
あなた ←→ 取引所のサーバー（履歴を保管してくれる）
```

銀行口座のようなもの。**ログインしてダウンロードできます。**

### ウォレット（MetaMask）

```
あなた ←→ ブロックチェーン（世界中に分散した取引台帳）
```

MetaMaskは「鍵を管理するアプリ」であり取引所ではありません。  
**MetaMask自体にはCSVダウンロード機能がありません**（公式確認済み）。  
代わりに「ブロックエクスプローラー（Etherscan等）」というサイトを使います。

| 比較 | 取引所（Bybit・Binance） | ウォレット（MetaMask） |
|------|------------------------|----------------------|
| 履歴の保管場所 | 取引所のサーバー | ブロックチェーン（Etherscan等で見られる） |
| CSV取得方法 | ログインして「エクスポート」 | Etherscanでアドレス検索→エクスポート |
| ログイン必要 | 必要（2段階認証あり） | 不要（アドレスだけでOK） |

---

## 第1章：Bybitの取引履歴をダウンロードする

### ⚠️ 2020年・2021年分に関する重要な注意

Bybitのデータエクスポートで取得できる期間には制限があります。

| データ種別 | 遡れる期間 |
|-----------|-----------|
| Transaction Log・Order History | **直近3年分**（2023年以降が中心） |
| Account Statement（PDF） | **2023年7月1日以降** |

> **つまり、2020年・2021年分はBybitのエクスポートから取得できない可能性があります。**  
> この期間の記録が必要な場合は、[Bybitサポート](https://www.bybit.com/en/help-center/) へ問い合わせてください。

---

### 全体の流れ

```
STEP 1：PCブラウザでログイン
     ↓
STEP 2：データエクスポートページへ移動
     ↓
STEP 3：種類・期間を選んでファイル生成
     ↓
STEP 4：圧縮ファイルを解凍してExcelで開く
```

> **⚠️ スマホアプリではCSVダウンロードできません。必ずPCブラウザで操作してください。**

---

### STEP 1：PCブラウザでログイン

1. [https://www.bybit.com/](https://www.bybit.com/) を開く
2. 右上「ログイン」をクリック
3. メールアドレス・パスワードを入力
4. 二段階認証（Google認証または SMS）のコードを入力

---

### STEP 2：データエクスポートページへ移動

**方法A（全種類まとめてダウンロード）―推奨―**

```
右上のプロフィールアイコンにマウスを合わせる
     ↓
「アカウント（Account）」をクリック
     ↓
「データエクスポート（Data Export）」をクリック
```

公式ヘルプ：[How to Self-Export Account Data](https://www.bybit.com/en/help-center/article/How-to-Self-Export-Account-Data)

**方法B（取引履歴のみ素早く確認）**

```
上部メニュー「注文（Orders）」をクリック
     ↓
「統合取引注文（Unified Trade Orders）」を選択
     ↓
「現物（Spot）」「デリバティブ（Derivatives）」等を選択
     ↓
「取引履歴（Trade History）」タブ → 右上「エクスポート（Export）」
```

---

### STEP 3：種類・期間を選んでファイル生成

| 操作 | 内容 |
|------|------|
| ① データ種別を選択 | Transaction Log（入出金・手数料） / Order History（注文） |
| ② 期間を指定 | 1回最大 **12ヶ月分**（種別により6ヶ月・3ヶ月の場合あり） |
| ③「Export Now」をクリック | 生成開始（最大1〜3日かかる場合あり） |
| ④ ステータスが「Exported」になったら「Download」 | 圧縮ファイルがダウンロードされる |

> **制限まとめ：**
> - ダウンロードリンクは **7日間有効**
> - 月50回・1日5〜10回の上限（種別により異なる）

---

### STEP 4：圧縮ファイルを解凍してExcelで開く

> **⚠️ Bybitのファイルは「.7z」または「WinRAR」形式で届きます。**  
> 通常のZIP解凍ソフトでは開けない場合があります。

解凍ソフト（無料）：
- **7-Zip**（Windows）：[https://7-zip.org/](https://7-zip.org/)
- **Keka**（Mac）：App Storeから無料インストール可能

解凍後、Excelで「データ」→「テキストから」でインポートする（文字化け対策）。

公式解凍ガイド：[How to Extract The Content From Your Data Export File](https://www.bybit.com/en/help-center/article/How-to-Extract-The-Content-From-Your-Data-Export-File)

---

### 取引種別ごとの取得先まとめ

| 取引種別 | 取得先 |
|---------|------|
| 現物（スポット）取引 | Orders → Unified Trade Orders → Spot → Trade History → Export |
| 先物（デリバティブ） | Orders → Unified Trade Orders → Derivatives → Trade History → Export |
| 入出金 | 資産（Assets）→ 入出金履歴（Deposit/Withdrawal History）→ Export |
| レンディング・コピートレード・ボーナス | Data Export → 該当カテゴリを選択 |

> **ステーキング・ボーナス報酬も課税対象になります。** Data Exportから忘れずに取得してください。

---

### Bybit 税務ツール連携

[Cryptact（クリプタクト）](https://cryptact.com/)（日本語）や [Koinly](https://koinly.io/)（英語）にCSVを読み込ませると損益計算が自動でできます。

公式税務ページ：[https://www.bybit.com/en/tax-api/](https://www.bybit.com/en/tax-api/)

---

## 第2章：Binanceの取引履歴をダウンロードする

### ⚠️ 重要：日本居住者の方へ

| 状況 | 対応 |
|------|------|
| **Binance Japan**（jp.binance.com）に移行済み | → Binance Japanの手順で取得（後述） |
| グローバル版のまま（未移行） | → 2026年現在アクセスできない可能性あり。[Binanceサポート](https://www.binance.com/en/chat)へ問い合わせを |
| 旧グローバル版の取引履歴 | → 移行前にダウンロードしていない場合はサポート問い合わせが必要 |

> **⚠️ 2023年移行時の「強制変換」に注意：**  
> 移行の際に、日本未承認銘柄がBTC等に強制変換された場合、その変換は **課税イベント（売却扱い）** になります。  
> 「Convert History」や「Transaction History」からその記録を必ず取得してください。

---

### Binance Japan（日本語版）の手順

**Binance Japanログイン：** [https://jp.binance.com/](https://jp.binance.com/)

**現物（スポット）取引の履歴：**

```
右上「現物注文」をクリック
     ↓
「取引履歴（Trade History）」タブをクリック
     ↓
「取引履歴のエクスポート」をクリック
     ↓
期間を選択 →「出力（Generate）」をクリック
     ↓
完了通知（メールまたはSMS）が届いたら「ダウンロード」
```

**入出金履歴：**

```
右上「ウォレット」アイコン →「資産履歴（Asset History）」
     ↓
「トランザクション履歴のエクスポート」をクリック
     ↓
時間範囲・通貨を選択 →「エクスポート」→「ダウンロード」
```

---

### Binance グローバル版（英語）の手順

**現物取引履歴：**

```
右上「Profile」アイコン →「Orders」→「Spot Order」
     ↓
「Trade History」タブ →「Export Trade History」をクリック
     ↓
期間を選択 →「Generate」→ 完了後「Download」
```

公式ヘルプ（英語）：[How to Download Spot Trading Transaction History](https://www.binance.com/en/support/faq/how-to-download-spot-trading-transaction-history-statement-e4ff64f2533f4d23a0b3f8f17f510eab)

**全ステートメント一括：**

```
「Wallet」→「Transaction History」
     ↓
エクスポートアイコン →「Export Transaction Records」をクリック
     ↓
範囲・口座・通貨を選択 →「Generate」→ 通知後「Download」
```

公式ヘルプ（英語）：[Export Transaction Records](https://www.binance.com/en/support/faq/990afa0a0a9341f78e7a9298a9575163)

---

### 期間制限と注意点

| 項目 | 内容 |
|------|------|
| 1回の最大取得期間 | **最大1年（12ヶ月）** |
| 即時ダウンロード | 6ヶ月以内なら即時・最大10,000件 |
| **月あたりのエクスポート上限** | **月5回** |
| ダウンロードリンクの有効期限 | **7日間** |
| 取得できる最古のデータ | 2017年7月13日以降 |
| 言語設定の注意 | **英語（English）設定でダウンロードを推奨**（日本語設定だとファイル名が文字化けする場合あり） |

> **言語設定を英語に変える方法：** 右上のプロフィールアイコン → Language → English  
> ダウンロード完了後は同じ手順で日本語に戻せます。

---

### 取引種別ごとの取得先

| 種別 | 取得先 |
|------|------|
| 現物（Spot） | Orders → Spot Order → Trade History → Export Trade History |
| コンバート | Wallet → Transaction History → Convert History → Export |
| 入出金 | Wallet → Asset History → Export（タイムゾーンをUTC+9に設定） |
| Earn（ステーキング等） | Wallet → Transaction History → All Statements |
| 先物（Futures）※グローバル版のみ | Wallet → Asset History → Export Transaction Records |

> **Binance Japanでは先物（Futures）は利用不可。** グローバル版で先物取引があった場合はグローバル側での取得が必要です。

---

## 第3章：MetaMaskの取引履歴を取得する

### MetaMaskとは？

MetaMaskは「ブラウザの拡張機能」または「スマホアプリ」として動作するウォレット（財布）アプリです。
銀行口座のような「運営会社」はなく、自分の秘密鍵を自分で管理します。

取引履歴は世界中のコンピューターが共有する「ブロックチェーン」に記録されており、
**「Etherscan」というサイトで誰でも確認・ダウンロードできます**（MetaMask公式確認済み）。

---

### STEP 0：自分のウォレットアドレスを確認する

ウォレットアドレスとは「`0x`で始まる42文字の英数字」です。銀行口座番号のようなものです。

**ブラウザ拡張機能（PC）の場合：**

```
① ブラウザ右上のMetaMaskアイコンをクリック
② ポップアップが開く → 上部にアカウント名が表示される
③ アカウント名のすぐ下の「0xABCD...」という文字をクリック
④ 「コピーされました」と表示されたらOK
```

**スマホアプリの場合：**

```
① MetaMaskアプリを開く
② ホーム画面の上部にアカウント名が表示される
③ アカウント名の下に「0x...」という短縮形が表示される
④ それをタップ → コピーされる
```

---

### STEP 1：Etherscanでアドレスを検索する

[https://etherscan.io/](https://etherscan.io/) を開いて、コピーしたアドレスを検索欄に貼り付けてEnter。

> **🔐 フィッシング詐欺に注意：**  
> 必ずURLが `https://etherscan.io` であることを確認してください。  
> シードフレーズ（12個の英単語）や秘密鍵の入力を求めるサイトは **すべて詐欺** です。  
> Etherscanはアドレスの入力だけで使えます。

---

### STEP 2：履歴の種類を選ぶ

ページを開くとタブが表示されます。

| タブ名 | 取得できる内容 | 補足 |
|--------|--------------|------|
| **Transactions** | ETHの送受信 | まずここから |
| **Internal Transactions** | スマートコントラクト経由のETH | DeFiを使った場合は必須 |
| **Token Transfers (ERC-20)** | USDT・UNI等トークンの取引 | ETH以外を取引した場合はこちら |
| **NFT Transfers** | NFTの送受信 | NFTを使った場合 |

> **ERC-20とは？** イーサリアム上で動く「トークン（コイン）の規格」です。USDTやその他多くのコインがこれに該当します。

---

### STEP 3：CSV Exportをクリック

1. 該当タブを開く
2. 以下のどちらかでCSVエクスポートを開く：
   - **ページ下部右側**の「**Download CSV Export**」リンクをクリック
   - **または** ページ上部右側の「**More**」ボタン → 「**Export CSV**」（2024年以降の新UI）
3. 期間（開始日・終了日）を入力
4. **CAPTCHA（キャプチャ）** を入力 ← ロボットでないことを確認する画面（画像を選んだり、チェックを入れる）
5. 「**Download**」ボタンをクリック → CSVファイルが保存される

> **上限：** 1回のダウンロードで最大 **5,000件**。5,000件を超える場合は期間を短く区切って複数回実施。

---

### どのチェーンを使っているか確認する方法

「チェーン」とはブロックチェーンの種類のことです。MetaMaskで複数のネットワークを使っていた場合、それぞれのサイトで別々に履歴を取得する必要があります。

**使っていたチェーンの確認方法：**

```
MetaMaskを開く → Activity（アクティビティ）タブをクリック
→ 過去の取引一覧が表示される
→ 各取引の右上や詳細画面にネットワーク名が表示される
（例：「Ethereum Mainnet」「BNB Chain」「Polygon」）
```

| 使っていたネットワーク | 対応エクスプローラー | URL |
|--------------------|-------------------|-----|
| Ethereum（ETH） | Etherscan | [https://etherscan.io/](https://etherscan.io/) |
| BNBチェーン（BSC） | BscScan | [https://bscscan.com/](https://bscscan.com/) |
| Polygon（MATIC） | PolygonScan | [https://polygonscan.com/](https://polygonscan.com/) |
| Arbitrum | Arbiscan | [https://arbiscan.io/](https://arbiscan.io/) |

> 各サイトでの操作手順はEtherscanと同一です（アドレス検索 → タブ選択 → CSV Export → 期間指定 → CAPTCHA → ダウンロード）

---

### MetaMaskアプリ内での確認（ざっと見るだけのとき）

**ブラウザ拡張機能：**
```
MetaMaskを開く → 「Activity（アクティビティ）」タブをクリック
→ 取引をクリック → 「View on explorer」でEtherscanに飛べる
```

**スマホアプリ：**
```
アプリ起動 → 下部の履歴アイコンをタップ
→ 確認したいトークンをタップ → 取引一覧が表示される
```

公式ヘルプ：[MetaMask Transaction History Reporting](https://support.metamask.io/manage-crypto/taxes/transaction-history-reporting/)

---

## ⚠️ 重要：取引所間でコインを送付した場合の落とし穴

**例：Binanceで買ったBTCをBybitに送って、Bybitで売った場合**

```
Binance: 100万円でBTC購入 → Bybitへ出金
Bybit: BTCが200万円になったので売却 → 利益200万円？
```

**実際の正しい計算：**

```
利益 = Bybit売却額（200万円）− Binance取得価格（100万円）= 100万円
```

しかし税計算ツールは「Binanceの出金」と「Bybitの入金」を自動でつなげてくれません。
手動で「送付（Transfer）」として処理しないと、Bybitでの取得原価がゼロとして扱われ、
**利益が200万円と過大計算される**危険があります。

> **対処法：** Cryptactでは「送付元・送付先を手動マッチング」する作業が必要です。  
> アップロード後に「Unknown」「Missing cost basis」などの警告が出た場合は必ず確認してください。

---

## 第4章：CSVを集めた後にやること

### 税計算ツールを使う（推奨）

手動Excelはミスが起きやすいため、専用ツールの利用を強く推奨します。

| ツール | 言語 | 特徴 |
|--------|------|------|
| [Cryptact（クリプタクト）](https://cryptact.com/) | 日本語 | 国内取引所・Bybit・Binance・Etherscan対応。日本の確定申告に特化 |
| [Koinly](https://koinly.io/) | 英語 | 海外対応が広い |

**アップロード後にすべき作業（「入れるだけ」ではありません）：**

1. **警告を確認する**：「Unknown」「Missing cost basis」が出たら手動で修正
2. **送付のマッチング**：取引所間のコイン移動を「送付」タグで手動紐付け
3. **年間損益レポートをダウンロード**：確定申告で使う最終レポートを出力

### 自分でExcelにまとめる場合

```
利益 = 売却額 − 取得費（総平均法） − 手数料
```

> **総平均法とは？** 1年間のすべての購入コストを合計して、1BTCあたりの平均単価を出す計算方法です。複数回に分けて購入した場合でも、この方法で統一して計算します。

---

## まとめ：やることリスト

### Bybit
- [ ] PCブラウザでBybitにログイン
- [ ] Profile → Account → Data Export
- [ ] Transaction Log・Order History を取得（直近3年分が上限に注意）
- [ ] **2020年・2021年分が必要な場合はサポートへ問い合わせ**
- [ ] 圧縮ファイルを 7-Zip/Keka で解凍
- [ ] 入出金・ボーナス履歴も別途エクスポート

### Binance
- [ ] 日本居住者はBinance Japanでログイン（[jp.binance.com](https://jp.binance.com/)）
- [ ] グローバル版の旧履歴・**2023年強制変換履歴**が必要な場合はサポートへ
- [ ] 言語設定を英語にしてからエクスポート
- [ ] 現物・コンバート・入出金・Earnを1年ずつ取得
- [ ] **月5回のエクスポート上限**に注意

### MetaMask（Etherscan等）
- [ ] MetaMaskからウォレットアドレス（0x...）をコピー
- [ ] [etherscan.io](https://etherscan.io/) でアドレスを検索
- [ ] Transactions・Token Transfers (ERC-20) 各タブでCSV取得
- [ ] 複数チェーンを使っていた場合は各エクスプローラーで同様に実施
- [ ] DeFi・NFT・エアドロップがある場合は専門家に相談

### 全体
- [ ] Cryptact等にすべてのCSVをアップロード
- [ ] 警告を確認・取引所間の送付を手動マッチング
- [ ] GMOコインの履歴も別途取得（専用ガイド参照）

---

## よくあるトラブルQ&A

### Q. CSVを開いたら文字化けした
**A.** Excelで直接開かずに「データ」→「テキストから」でインポートし、文字コードを **UTF-8** に設定してください。Binanceは英語設定でダウンロードすると起きにくくなります。

### Q. Bybitのファイルが解凍できない
**A.** Bybitは通常のZIPではなく **7-zip（.7z）形式** を使用しています。[7-Zip（Windows）](https://7-zip.org/) または [Keka（Mac）](https://www.keka.io/ja/) をインストールしてから解凍してください。

### Q. Bybitのエクスポートに時間がかかる
**A.** Data Export は処理に最大1〜3日かかる場合があります。ダウンロードリンクは **7日間有効** なので翌日以降に確認しても大丈夫です。

### Q. Binanceのグローバル版にログインできない
**A.** 2023年11月以降、日本居住者向けのグローバル版は機能制限されています。[Binanceサポート](https://www.binance.com/en/chat) に問い合わせるか、Binance Japanで確認してください。

### Q. MetaMaskのトランザクションが5,000件を超えている
**A.** 期間を短く区切って（例：3ヶ月ずつ）複数回ダウンロードしてください。Etherscanは1回最大5,000件の制限があります。

### Q. どのチェーンを使っていたかわからない
**A.** MetaMaskのActivity（アクティビティ）タブで過去の取引を確認してください。各取引の詳細画面にネットワーク名が表示されます。

### Q. Cryptactに入れたら「Unknown」という警告が大量に出た
**A.** 取引所間でコインを移動した記録が「送付」として処理されていません。送付元と送付先の記録を手動でマッチングする作業が必要です。Cryptactのヘルプまたはサポートに相談してください。

---

## 困ったときの連絡先

| サービス | サポートリンク |
|---------|--------------|
| Bybit | [https://www.bybit.com/en/help-center/](https://www.bybit.com/en/help-center/) |
| Binance Japan | [https://jp.binance.com/en/support](https://jp.binance.com/en/support) |
| MetaMask公式サポート | [https://support.metamask.io/](https://support.metamask.io/) |
| Cryptactサポート | [https://support.cryptact.com/hc/ja](https://support.cryptact.com/hc/ja) |

---

## 参考・検証に使用した情報源

- [Bybit公式：アカウントデータの自己エクスポート方法](https://www.bybit.com/en/help-center/article/How-to-Self-Export-Account-Data)
- [Bybit公式：データエクスポートファイルの解凍方法](https://www.bybit.com/en/help-center/article/How-to-Extract-The-Content-From-Your-Data-Export-File)
- [Bybit公式：税務連携](https://www.bybit.com/en/tax-api/)
- [Binance公式：Spot取引履歴ダウンロード](https://www.binance.com/en/support/faq/how-to-download-spot-trading-transaction-history-statement-e4ff64f2533f4d23a0b3f8f17f510eab)
- [Binance公式：Transaction Records エクスポート](https://www.binance.com/en/support/faq/990afa0a0a9341f78e7a9298a9575163)
- [MetaMask公式：取引履歴と税務報告](https://support.metamask.io/manage-crypto/taxes/transaction-history-reporting/)
- [Etherscan](https://etherscan.io/) / [BscScan](https://bscscan.com/) / [PolygonScan](https://polygonscan.com/)

---

*作成：男座員也（Kazuya Oza）/ deep-research-forge*  
*2026年5月8日 初版 → 2026年5月8日 3エージェント品質レビュー（事実確認・UX・税務）により改訂*
