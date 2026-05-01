# Deep Research — AIディープリサーチエージェント

> 複数の情報源（Web・論文・ニュース・SNS）から深掘り調査を行うAIリサーチエージェントです。

## 📋 概要

複数の情報源（Web・論文・ニュース・SNS）から深掘り調査を行うAIリサーチエージェントです。ユーザーが調査テーマを入力すると、AIが自律的に情報収集・要約・レポート生成を行います。

## ✨ 主な機能

- マルチソース並列リサーチ（Web・論文・ニュース）
- AIによる情報の信頼性評価・ファクトチェック
- 構造化レポートの自動生成（概要→詳細→結論）
- 参照元リストの自動整理・引用形式出力
- 日本語・英語バイリンガル対応

## 🛠️ 技術スタック

| カテゴリ | 技術・ライブラリ |
|----------|----------------|
| 言語 | Python 3.10+ |
| AIフレームワーク | Claude API / LangChain |
| 検索 | SerpAPI / Tavily / DuckDuckGo |
| 出力 | Markdown, PDF |

## 🚀 セットアップ

### 前提条件

- Python 3.9 以上
- APIキー（Claude / OpenAI 等）を `.env` ファイルに設定

### インストール

```bash
git clone https://github.com/KazuyaMurayama/deep-research.git
cd deep-research
pip install -r requirements.txt
```

### 環境設定

```bash
cp .env.example .env
# .env ファイルに必要なAPIキーを設定
```

## 💻 使い方

```bash
python research.py --topic "調査テーマ"
```

## 👨‍💻 開発者情報

**男座員也（Kazuya Oza / おざ かずや）**

| | |
|---|---|
| GitHub | [@KazuyaMurayama](https://github.com/KazuyaMurayama) |
| 専門領域 | データサイエンス・生成AIコンサルタント |
| 主要スキル | Python, LightGBM, LangChain, RAG, Streamlit, React, TypeScript |
| 事業 | AIコンサルティング（月単価目標300万円）/ SaaS開発 / 定量投資 |

## 📄 ライセンス

© 2025 男座員也（Kazuya Oza）. All rights reserved.

---

> このリポジトリは **男座員也（Kazuya Oza）** が開発・管理しています。
> 命名・ドキュメント等での表記は必ず **男座員也** または **Kazuya Oza** を使用してください。
