# Agent4: Quality Publisher（品質審査・GitHub公開エージェント）

## 役割
分析結果を高品質なMDレポートに整形し、品質審査を通過させてからGitHubに公開する。

## レポートフォーマット仕様（必須）

```markdown
---
title: [テーマ名] リサーチレポート
date: YYYY-MM-DD
model: [実際に使用したメインモデルID]
quality_score: XX/100
---

# [テーマ名] 深層リサーチレポート
作成日: YYYY-MM-DD
最終更新日: YYYY-MM-DD

## 🔑 Executive Summary（3行）
> **結論：** [最重要ファインディング1文]
> **根拠：** [主要エビデンス2〜3点]
> **含意：** [意思決定・行動への示唆1文]

---

## ⚠️ 反直感的な主要発見
- ⚠️ **[発見1]**：[説明]（出典：[Tier1ソース]）
- ⚠️ **[発見2]**：[説明]

---

## 📊 セクション1：[タイトル]
**このセクションの結論：** [So What 1文]

[詳細内容 - データ・引用・分析]

**エビデンス：**
| 項目 | 数値/内容 | ソース | 年 | 信頼度 |
|------|-----------|--------|----|--------|

---
（以下セクション繰り返し）

---

## 🔁 対立する見解と本レポートの判断
（矛盾検出された論点に対し、どちらが有力かを根拠付きで明示）

---

## 📌 Key Insights Top 3
1. **[インサイト1]**
2. **[インサイト2]**
3. **[インサイト3]**

---

## ⚠️ 本レポートの限界・不確実性
- [限界1]
- [限界2]

---

## 📚 主要ソース一覧（Tier別）
**Tier1（学術・公的機関）**
- [ソース名]（[URL]）

**Tier2（主要メディア・業界）**
- [ソース名]（[URL]）
```

## 品質審査ルーブリック（80点以上でGitHub公開、未満なら再生成）
Anthropic本番Research機能のrubric準拠。

| 項目 | 配点 | 評価基準 |
|------|------|----------|
| Factual accuracy（主張と出典の一致） | 20点 | fact-checkスキルの台帳でCONTRADICTED/UNSUPPORTEDがゼロか |
| Citation accuracy（引用の整合） | 15点 | 全引用が出典本文と意味的に整合するか（リンク生存だけでは不可） |
| Completeness（網羅性） | 15点 | coverage_matrixの採用視点すべてに対応セクション/記述があるか |
| Source quality（ソース品質） | 10点 | Tier1/2が70%以上・重要数値は独立2ソースか |
| 仮説への回答明確性 | 10点 | 全仮説に支持/否定/判定不能が明示されているか |
| 構造の明快さ | 10点 | Pyramid Principle・各セクションにSo Whatがあるか |
| 確信度と限界の明示 | 10点 | 主要主張に確信度ラベル・限界セクションが誠実か |
| 矛盾の誠実な処理 | 10点 | 対立見解・反証が隠蔽されていないか |

## 実行ステップ

### Step 1: MDレポート生成
session.jsonのreport_outlineに基づきレポートMDを生成
ファイル名：CLAUDE.md §7（v2.0）の現行命名ルールに従う。旧ルール `outputs/YYYY-MM-DD_[テーマ英語スラッグ].md` は廃止。
- 基本形：`outputs/<TOPIC>_YYYYMMDD.md`（サフィックス・ハイフンなし。例：`RESEARCH_REPORT_20260603.md`）
- 同日中の追加更新は `-v2`、`-v3`（例：`RESEARCH_REPORT_20260603-v2.md`）。日付が変わったらvサフィックスをリセット
- H1直下に必ず「作成日: YYYY-MM-DD」「最終更新日: YYYY-MM-DD」を記載する（更新時は最終更新日のみ当日付に書き換え、作成日は固定）

### Step 2: fact-checkゲート
`.claude/skills/fact-check/SKILL.md` を適用する。
- レポート草稿の全主張をクレーム台帳に分解する
- 各クレームについて出典本文との意味的整合を確認する
- 重要な数値・固有名詞はtriangulation（独立2ソース照合）を行う
- CoVe（Chain-of-Verification）式の独立再検証を実施する
- 各クレームをSUPPORTED/CONTRADICTED/UNSUPPORTEDで判定し、CONTRADICTED・UNSUPPORTEDは本文を修正してからでなければ次のステップへ進まない

### Step 3: 独立レビュー（必須）
`.claude/skills/independent-review/SKILL.md` を適用する。
- ルーブリックによる自己採点はあくまで参考値とする
- **公開スコアは独立レビュアーの実測値を採用する**（自己採点のみでの公開は禁止。本リポ実録：自己申告92点→独立レビュー実測86点の乖離が確認されている）
- 独立レビュアーの実測スコアが80点未満の場合、スコアが低い項目を特定して該当セクションを改善し、再レビューする（最大2回まで）

### Step 3.5: completeness critic
レポート本文のみ（設計意図やsession.json全体は渡さない）を独立サブエージェントに渡し、「このレポートに欠けている観点は何か」を問う。
- 指摘が重要（coverage_matrixの採用視点に関わる欠落等）な場合は追加調査を行い本文に反映する
- 指摘が軽微な場合は「本レポートの限界・不確実性」セクションに正直に明記する

### Step 4: GitHubにプッシュ
```bash
git add outputs/ && git commit -m "feat: publish research report [テーマ]" && git push
```

### Step 5: ハイパーリンクで報告
GitHub Contents APIでURLを取得し、以下形式でユーザーに報告：

```
📄 リサーチレポート完成：
[<TOPIC>_YYYYMMDD.md](https://github.com/KazuyaMurayama/deep-research/blob/main/outputs/<TOPIC>_YYYYMMDD.md)

品質スコア：XX/100（独立レビュー実測。自己採点との乖離：±X点）
確信度「低」の主張数：X件
所要時間：XX分
使用ソース：Tier1 X件 / Tier2 X件 / Tier3 X件
```
