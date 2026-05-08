# 仮想通貨の取引履歴をダウンロードしよう
### 〜 Bybit・Binance・MetaMask 完全ガイド 〜

**作成日：** 2026年5月8日  
**対象：** 2020年頃から仮想通貨取引をしてきた方  
**難易度：** ★☆☆☆☆（高校生でもできる！）  
**検証済み：** 各公式サイト・サポートページへの実アクセスで確認済み

---

## はじめに：なぜ取引履歴が必要なの？

ビットコインなどを売って利益が出た場合、**確定申告（税金の申告）** が必要になることがあります。

| 確認したいこと | 使うデータ |
|--------------|-----------|
| いくら利益が出たか | 取引履歴（買値・売値） |
| いくら入金・出金したか | 入出金履歴 |
| 手数料はいくらかかったか | 取引手数料の記録 |

> **申告が必要な目安：** 年間利益が **20万円超**（会社員の場合）

---

## ⚠️ 第0章：まず知っておこう「取引所」と「ウォレット」の違い

この章は **MetaMaskを使っている人は必読** です！

### 取引所（Bybit・Binance）

```
あなた ←→ 取引所のサーバー（履歴を保管してくれる）
```

銀行口座のようなもの。履歴は **取引所のサーバー** に記録されているので、
ログインしてダウンロードできます。

### ウォレット（MetaMask）

```
あなた ←→ ブロックチェーン（履歴が刻まれる）
```

MetaMaskは「鍵を持つ財布」です。取引履歴は **ブロックチェーン上** に記録されており、
MetaMask自体にはCSVダウンロード機能がありません。
履歴を取得するには **ブロックエクスプローラー（Etherscanなど）** を使います。

| 比較 | 取引所（Bybit・Binance） | ウォレット（MetaMask） |
|------|------------------------|----------------------|
| 履歴の保管場所 | 取引所のサーバー | ブロックチェーン |
| CSV取得方法 | ログインして「エクスポート」 | Etherscanで検索→エクスポート |
| ログイン必要 | 必要（2段階認証あり） | MetaMaskは不要（アドレスだけでOK） |

---

## 第1章：Bybitの取引履歴をダウンロードする

### 全体の流れ

```
STEP 1：PCブラウザでログイン
     ↓
STEP 2：データエクスポートページへ移動
     ↓
STEP 3：種類・期間を選んでCSV生成
     ↓
STEP 4：ZIPを解凍してExcelで開く
```

> **⚠️ スマホアプリではCSVダウンロードできません。必ずPCブラウザで操作してください。**

---

### STEP 1：PCブラウザでログイン

1. [https://www.bybit.com/](https://www.bybit.com/) を開く
2. 右上「ログイン」をクリック
3. メールアドレス・パスワードを入力
4. 二段階認証（Google認証または SMS）を入力

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

**方法B（現物・先物の取引履歴のみ）**

```
上部メニュー「注文（Orders）」をクリック
     ↓
「統合取引注文（Unified Trade Orders）」を選択
     ↓
左メニューで「現物（Spot）」「デリバティブ（Derivatives）」等を選択
     ↓
「取引履歴（Trade History）」タブ → 右上「エクスポート（Export）」
```

---

### STEP 3：種類・期間を選んでCSV生成

「データエクスポート（Data Export）」画面での操作：

| 操作 | 内容 |
|------|------|
| ① データ種別を選択 | Transaction Log（入出金・手数料） / Order History（注文） |
| ② 期間を指定 | 1回最大 **1年分**（2020年分から1年ずつ取得） |
| ③「Export Now」をクリック | 生成開始（最大1〜3日かかる場合あり） |
| ④ ステータスが「Exported」になったら「Download」 | ZIPファイルがダウンロードされる |

> **期間制限まとめ：**
> - 1回のエクスポートで最大 **1年分**
> - 最大 **5年前（2022年1月以降）** まで遡れる
> - 月50回・1日5回の上限あり
> - ダウンロードリンクは **7日間有効**

---

### STEP 4：ZIPを解凍してExcelで開く

1. ダウンロードしたファイルを右クリック →「すべて展開」
2. 解凍ツールが必要な場合：[7-Zip（無料）](https://7-zip.org/) または WinRAR
3. Excelで「データ」→「テキストから」でインポート（文字化け対策）

公式解凍ガイド：[How to Extract The Content From Your Data Export File](https://www.bybit.com/en/help-center/article/How-to-Extract-The-Content-From-Your-Data-Export-File)

---

### 取引種別ごとの取得先まとめ

| 取引種別 | 取得先 |
|---------|------|
| 現物（スポット）取引 | Orders → Unified Trade Orders → Spot → Trade History → Export |
| 先物（デリバティブ） | Orders → Unified Trade Orders → Derivatives → Trade History → Export |
| 日本円・暗号資産の入出金 | 資産（Assets）→ 入出金履歴（Deposit/Withdrawal History）→ Export |
| レンディング・コピートレード | Data Export から取得 |

---

### Bybit 税務ツール連携

日本語対応の [Cryptact（クリプタクト）](https://cryptact.com/) や英語圏の [Koinly](https://koinly.io/) に
CSVを読み込ませると、利益計算が自動でできます。

公式税務ページ：[https://www.bybit.com/en/tax-api/](https://www.bybit.com/en/tax-api/)

---

## 第2章：Binanceの取引履歴をダウンロードする

### ⚠️ 重要：日本居住者の方へ

Binanceのグローバル版（binance.com）は **2023年11月30日** に日本居住者向けサービスを終了しました。

| 状況 | 対応 |
|------|------|
| **Binance Japan**（jp.binance.com）に移行済み | → Binance Japanの手順で取得（後述） |
| **グローバル版**のまま（未移行） | → 2026年現在アクセスできない可能性あり。Binanceサポートへ問い合わせを |
| 旧グローバル版の取引履歴 | → 移行前にダウンロードしていない場合はサポート問い合わせが必要 |

---

### Binance Japan（日本語版）の手順

**Binance Japanログイン：** [https://jp.binance.com/](https://jp.binance.com/)

**現物（スポット）取引の履歴：**

```
右上「現物注文（Spot Orders）」をクリック
     ↓
「取引履歴（Trade History）」タブをクリック
     ↓
右上の「取引履歴のエクスポート」をクリック
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
「Trade History」タブ →「Export Trade History」アイコン
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
| ダウンロードリンクの有効期限 | **7日間** |
| 取得できる最古のデータ | 2017年7月13日以降 |
| 言語設定の注意 | **英語（English）設定でダウンロードを推奨**（日本語設定だとファイル名が文字化けする場合あり） |

---

### 取引種別ごとの取得先

| 種別 | 取得先 |
|------|------|
| 現物（Spot） | Orders → Spot Order → Trade History → Export |
| 先物（Futures）※グローバル版のみ | Wallet → Asset History → Export Transaction Records |
| コンバート | Wallet → Transaction History → Convert History → Export |
| 入出金 | Wallet → Asset History → Export（タイムゾーンをUTC+9に設定） |
| Earn（ステーキング等） | Wallet → Transaction History → All Statements |

> **Binance Japanでは先物（Futures）・クロスマージン取引は利用不可。**
> グローバル版で先物取引があった場合は、グローバル側での取得が必要です。

---

## 第3章：MetaMaskの取引履歴を取得する

### MetaMaskの特殊な仕組み（重要）

MetaMaskは「ウォレット（財布）」であり取引所ではないため、
**MetaMask自体にはCSVダウンロード機能がありません**（2025年時点、公式確認済み）。

代わりに **ブロックエクスプローラー** を使います。

> **ブロックエクスプローラー** ＝ ブロックチェーンの「取引台帳」を誰でも閲覧できるサイト

---

### STEP 0：自分のウォレットアドレスを確認する

MetaMaskを開いて、アドレス（`0x`で始まる42文字）をコピーします。

**ブラウザ拡張機能：**
```
MetaMaskを開く → 上部のアカウント名下にある「0x...」をクリックしてコピー
```

**スマホアプリ：**
```
アプリ起動 → ウォレット画面上部のアカウント名 → アドレスをタップしてコピー
```

---

### STEP 1：Etherscanでアドレスを検索する

[https://etherscan.io/](https://etherscan.io/) を開いて、コピーしたアドレスを検索欄に貼り付けてEnter。

---

### STEP 2：履歴の種類を選ぶ

| タブ名 | 取得できる内容 |
|--------|--------------|
| **Transactions** | ETHの送受信 |
| **Internal Transactions** | スマートコントラクト経由のETH移動 |
| **Token Transfers (ERC-20)** | USDTやその他トークンの取引 |
| **NFT Transfers** | NFTの送受信 |

---

### STEP 3：CSV Exportをクリック

1. 該当タブを開く
2. **ページの一番下右側**にある「**Download: CSV Export**」リンクをクリック
3. 期間（開始日・終了日）を入力
4. **CAPTCHA（ロボットでないことの確認）** を入力
5. 「**Download**」ボタンをクリック → CSVファイルが保存される

> **上限：** 1回のダウンロードで最大 **5,000件**。5,000件を超える場合は期間を分けて複数回実施。

---

### チェーン別のエクスプローラー一覧

MetaMaskで複数のネットワーク（チェーン）を使っていた場合は、それぞれのサイトで同様の操作が必要です。

| 使っていたネットワーク | 対応エクスプローラー | URL |
|--------------------|-------------------|-----|
| Ethereum（イーサリアム） | Etherscan | [https://etherscan.io/](https://etherscan.io/) |
| BNBチェーン（BSC） | BscScan | [https://bscscan.com/](https://bscscan.com/) |
| Polygon（ポリゴン） | PolygonScan | [https://polygonscan.com/](https://polygonscan.com/) |
| Arbitrum | Arbiscan | [https://arbiscan.io/](https://arbiscan.io/) |

> 操作手順は全サイト共通（アドレス検索 → タブ選択 → CSV Export → 期間指定 → CAPTCHA → ダウンロード）

---

### MetaMaskアプリ内の履歴確認（ざっと確認したいとき）

CSV取得は不要で「最近の取引を見たいだけ」という場合：

**ブラウザ拡張機能：**
```
MetaMaskを開く → 「Activity（アクティビティ）」タブをクリック
→ 取引をクリック → 「View on explorer（エクスプローラーで表示）」でEtherscanに飛べる
```

**スマホアプリ：**
```
アプリ起動 → 下部の履歴アイコンをタップ
→ 確認したいトークンをタップ → 取引一覧が表示される
```

公式ヘルプ：[MetaMask Transaction History Reporting](https://support.metamask.io/manage-crypto/taxes/transaction-history-reporting/)

---

### 🔐 セキュリティの注意（重要）

> **シードフレーズ（12個の英単語）や秘密鍵は絶対に他人に教えないでください！**
> Etherscanなどのサイトでは「アドレス（0x...）」だけあれば履歴が見られます。
> シードフレーズの入力を求めるサイトは **すべて詐欺** です。

---

## 第4章：3つの履歴をまとめて確定申告に使う

### 方法A：Cryptact（クリプタクト）を使う（日本語対応）

[https://cryptact.com/](https://cryptact.com/) は日本語対応の仮想通貨損益計算サービスです。
各取引所のCSVをそのままアップロードするだけで自動計算してくれます。

対応取引所：Bybit・Binance・MetaMask（Etherscanデータ）を含む多数

### 方法B：自分でExcelにまとめる

1. 各プラットフォームのCSVを1つのエクセルファイルに貼り付ける
2. 「入金」「売買」「出金」ごとに整理
3. 利益 ＝ 売却額 − 取得費 − 手数料 で計算

---

## まとめ：やることリスト

### Bybit
- [ ] PCブラウザでBybitにログイン
- [ ] プロフィール → Account → Data Export
- [ ] 2020年〜現在まで1年ずつTransaction Log・Order Historyを生成
- [ ] ZIPをダウンロードして解凍
- [ ] 入出金履歴も別途エクスポート

### Binance
- [ ] 日本居住者はBinance Japanでログイン（[jp.binance.com](https://jp.binance.com/)）
- [ ] グローバル版の旧履歴が必要な場合はサポートに問い合わせ
- [ ] 言語設定を英語にしてからエクスポート
- [ ] 現物・入出金を1年ずつ取得（最大1年／回）

### MetaMask
- [ ] MetaMaskからウォレットアドレス（0x...）をコピー
- [ ] [etherscan.io](https://etherscan.io/) でアドレスを検索
- [ ] Transactions・Token Transfers それぞれCSV Exportをクリック
- [ ] 期間指定・CAPTCHA入力・ダウンロード
- [ ] 複数チェーンを使っていた場合は各エクスプローラーで同様に実施

---

## よくあるトラブルQ&A

### Q. CSVを開いたら文字化けした
**A.** Excelで直接開かずに、「データ」→「テキストから」でインポートし、文字コードを **UTF-8** に設定してください。Binanceは英語設定でダウンロードすると起きにくくなります。

### Q. Bybitのエクスポートに時間がかかる
**A.** 「Data Export」は処理に最大1〜3日かかる場合があります。生成後のダウンロードリンクは **7日間有効** なので、翌日以降にダウンロードしても大丈夫です。

### Q. Binanceのグローバル版にログインできない
**A.** 2023年11月以降、日本居住者向けのグローバル版は機能制限されています。[Binanceサポート](https://www.binance.com/en/chat) に問い合わせるか、Binance Japanで確認してください。

### Q. MetaMaskのトランザクションが5,000件を超えている
**A.** 期間を短く区切って（例：3ヶ月ずつ）複数回ダウンロードしてください。Etherscanは1回最大5,000件の制限があります。

### Q. どのチェーンを使っていたかわからない
**A.** MetaMaskのActivity（アクティビティ）タブを開いて、過去の取引を確認してください。「Ethereum Mainnet」「BNB Chain」「Polygon」などネットワーク名が表示されます。

---

## 困ったときの連絡先

| サービス | サポートリンク |
|---------|--------------|
| Bybit | [https://www.bybit.com/en/help-center/](https://www.bybit.com/en/help-center/) |
| Binance Japan | [https://jp.binance.com/en/support](https://jp.binance.com/en/support) |
| MetaMask公式サポート | [https://support.metamask.io/](https://support.metamask.io/) |

---

## 参考・検証に使用した情報源

- [Bybit公式：アカウントデータの自己エクスポート方法](https://www.bybit.com/en/help-center/article/How-to-Self-Export-Account-Data)
- [Bybit公式：データエクスポートファイルの解凍方法](https://www.bybit.com/en/help-center/article/How-to-Extract-The-Content-From-Your-Data-Export-File)
- [Bybit公式：税務連携](https://www.bybit.com/en/tax-api/)
- [Binance公式：Spot取引履歴ダウンロード](https://www.binance.com/en/support/faq/how-to-download-spot-trading-transaction-history-statement-e4ff64f2533f4d23a0b3f8f17f510eab)
- [Binance公式：Transaction Records エクスポート](https://www.binance.com/en/support/faq/990afa0a0a9341f78e7a9298a9575163)
- [MetaMask公式：取引履歴と税務報告](https://support.metamask.io/manage-crypto/taxes/transaction-history-reporting/)
- [Etherscan](https://etherscan.io/)
- [BscScan](https://bscscan.com/)
- [PolygonScan](https://polygonscan.com/)

---

*作成：男座員也（Kazuya Oza）/ deep-research-forge*  
*2026年5月8日（公式サイト・サポートページへの実アクセスで検証済み）*
