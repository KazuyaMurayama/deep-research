# deep-research-forge ファイルインデックス

## コアファイル（毎回参照）
| ファイル | 役割 | 優先度 |
|----------|------|--------|
| CLAUDE.md | ルーターとルール | ★★★ |
| tasks.md | タスク管理 | ★★★ |
| session.json | セッション状態 | ★★★ |

## スキルファイル（エージェント順に参照）
| ファイル | 役割 |
|----------|------|
| skills/01_research_architect.md | 研究設計 |
| skills/02_web_researcher.md | ウェブ調査 |
| skills/03_analyst_synthesizer.md | 分析・統合 |
| skills/04_quality_publisher.md | 品質審査・公開 |
| skills/timeout.md | タイムアウト対策（全エージェント共通） |

## 品質保証スキル（.claude/skills/）
| ファイル | 役割 |
|----------|------|
| .claude/skills/fact-check/SKILL.md | ハルシネーション検出（クレーム台帳・出典整合・triangulation・CoVe独立再検証）。push前必須ゲート |
| .claude/skills/independent-review/SKILL.md | 独立レビュアー＋多観点パネル（Contrarian必須）。公開スコアは独立実測値 |
| .claude/skills/coverage-check/SKILL.md | 視点マトリクス・反証探索クエリ・premortem・completeness critic による網羅性担保 |
| .claude/skills/sp-dispatching-parallel-agents/SKILL.md | 並列サブエージェント委譲の判断基準（superpowers由来・MIT） |

## アウトプット（最新順）
| ファイル | テーマ | スコア | 日付 |
|----------|--------|--------|------|
| [outputs/SHIBA_NAMING_20260702.md](outputs/SHIBA_NAMING_20260702.md) | 新しい赤柴（メス・2/13生まれ）の命名レポート。希望5条件（①2月/2月13日 ②茶/赤 ③短く呼びやすい ④国内外で通用 ⑤めい/かのん/しおんと弁別）を各20点の評価軸にして8案を採点。1位ショコラ(チョコ)84・2位ルビー82・3位モカ76…7位ウメ(紅梅)70。犬は頭子音＋語尾母音で識別するためR/B/D/Z/T始まり×語尾-a/-o/-iが混同しにくい旨も明記 | 90/100 | 2026-07-02 |
| [outputs/YOUNG_GEN_LECTURE_STRATEGY_20260630.md](outputs/YOUNG_GEN_LECTURE_STRATEGY_20260630.md) | 大学生・20代社会人への「直接講義」実現方法（6テーマ＝DS/GAFA働き方/投資/幸福/思考法/キャリア）。12手法＋補足3手法を6軸（実現性25/調整15/時間対効果20/リーチ15/収益性15/ブランド10）で加重採点→note84.5・ストアカ69.5・企業研修69.0が上位、非常勤講師43.5が最下位。結論=「note(母艦)×ストアカ(即収益ライブ)」の二枚看板で今月開始→企業研修/大学ゲスト/Udemy/サロンへ拡張の3段ロケット。パイロット3案＋KPI＋note連載の記事化順序＋発信→有料講義ファネル＋0-12か月ロードマップ＋投資テーマの金商法/特商法セルフチェック。§7=実装に向けた精緻化（看板の1コア＋衛星・顧客二層マネタイズ・工数管理＆ハードストップ・金額目標＆ユニットエコノミクス・講義品質設計・系統B/本業リスク） | 96/100 | 2026-06-30 |
| [outputs/2026-06-26_kyokaikenpo-voluntary-bank-debit.md](outputs/2026-06-26_kyokaikenpo-voluntary-bank-debit.md) | 協会けんぽ任意継続の保険料を「銀行口座振替（自動引き落とし）」にする方法。第一希望の住信SBI・次善のみずほとも収納代行みずほファクター2026/6一覧で○。ネット銀行は3ステップ（支部へ郵送→メール→本人WEB登録14日以内）、開始まで約3か月・毎月1日引落・再振替なし・前納不可。独立レビューで①「2026/6開始が任意継続の根拠」という論理矛盾を是正（住信SBIは2008年からみずほファクター対応／2026/6は年金機構ルートで別物）②支部送付先の調べ方・二重払い回避・初回納付・残高不足後の復帰・辞退届正式名を追記③結論3行に圧縮・着手前チェックリスト追加。さらに様式304の記入見本（欄ごとの書き方＝表E）を追記（92/100） | 92/100 | 2026-06-27 |
| [outputs/JAPAN_CITIES_RELOCATION_REPORT_20260624.md](outputs/JAPAN_CITIES_RELOCATION_REPORT_20260624.md) | 日本国内移住先 14都市比較レポート（拡張版）：札幌・仙台・新潟・軽井沢・花小金井に加え、八戸・帯広・函館・盛岡・富山・金沢・松本・長野・北九州の9市を新候補としてプレリサーチ＋6軸スコア再評価。教育レベルを全国学テ(R6)・全国体力(R6)・難関大2025合格者数・教員1人当たり児童数で定量化。トップ3=八戸79.5/帯広76.5/函館76.0、札幌72.0・仙台70.5・花小金井50.5の元位置は維持 | -/100 | 2026-06-24 |
| [outputs/KITAKYUSHU_PRERESEARCH_20260624.md](outputs/KITAKYUSHU_PRERESEARCH_20260624.md) | 北九州市プレリサーチ（拡張版用の単独プロファイル・総合75点/100） | -/100 | 2026-06-24 |
| [outputs/2026-06-22_freelance-health-insurance-cheapest.md](outputs/2026-06-22_freelance-health-insurance-cheapest.md) | フリーランス世帯の健康保険「最安・最低限」化ガイド（40代個人事業主＋パート妻＋幼児2人・世帯所得800万円）：任意継続/市区町村国保/国保組合(文美国保)/マイクロ法人社保/扶養最適化を制度別に比較。独立レビューで「健保＋年金の総額比較表」「事業所得別の感度分析」「傷病手当金など給付差」「妻の年収の壁」を追記・トーン是正 | 90/100 | 2026-06-22 |
| [outputs/BOULDERING_SKILLUP_GUIDE_20260615.md](outputs/BOULDERING_SKILLUP_GUIDE_20260615.md) | ボルダリング上達ガイド（初心者→中級→上級のステップアップ＋技術を学ぶ厳選リソース＋前腕がすぐ疲れる/1週間続く筋肉痛・握力低下の予防/対策） | -/100 | 2026-06-15 |
| [outputs/2026-06-02_two-dog-shiba-bordercollie.md](outputs/2026-06-02_two-dog-shiba-bordercollie.md) | 柴犬5歳＋ボーダーコリー0歳の2頭飼い 実現可能性・影響・課題対策ガイド（東京都花小金井・幼児2人世帯）※サイズ比較・楽しみ・生涯費用＋スコアリング刷新(健康/静か/抜け毛/コスト)＋BC♀子犬の選定〜育成ロードマップ＋参考犬種(シェルティ/秋田/ハスキー♀)比較 追記 | 92/100 | 2026-06-02 |
| [outputs/2026-05-20_online-meeting-minutes-system.md](outputs/2026-05-20_online-meeting-minutes-system.md) | オンライン会議 AI議事録システム完全設計ガイド（Windows 11・Razer Seiren Mini対応） | -/100 | 2026-05-20 |
| [outputs/2026-05-20_sbi-cfd-nasdaq-8x-9x-leverage-guide.md](outputs/2026-05-20_sbi-cfd-nasdaq-8x-9x-leverage-guide.md) | SBI証券CFDでNASDAQ-100に8倍・9倍レバレッジをかける方法【ウェブ検証レポート】 | -/100 | 2026-05-20 |
| [outputs/2026-05-17_gold-bond-3x-leverage-products.md](outputs/2026-05-17_gold-bond-3x-leverage-products.md) | ゴールド・ボンドに対するレバレッジ3倍以上の投資商品比較ガイド（SBI証券中心） | -/100 | 2026-05-17 |
| [outputs/2026-05-15_nasdaq-4x-leverage-products.md](outputs/2026-05-15_nasdaq-4x-leverage-products.md) | NASDAQ-100で4倍以上レバレッジを実現する全商品比較ガイド（CFD/ETF/先物/税制） | -/100 | 2026-05-15 |
| [outputs/2026-05-13_zeami-genius-analysis.md](outputs/2026-05-13_zeami-genius-analysis.md) | 世阿弥の天才性：650年前のスーパープロデューサー（松岡正剛評価・現代アナロジー） | -/100 | 2026-05-13 |
| [outputs/2026-05-13_baby-bottle-prolonged-use-effects.md](outputs/2026-05-13_baby-bottle-prolonged-use-effects.md) | 哺乳瓶を1歳以降も使い続けることの悪影響（仮説検証・エビデンス・卒業ガイド） | -/100 | 2026-05-13 |
| [outputs/2026-05-12_japan-5cities-relocation-report.md](outputs/2026-05-12_japan-5cities-relocation-report.md) | 日本5都市 子育て×柴犬世帯の移住先比較（花小金井/札幌/仙台/軽井沢/新潟） | -/100 | 2026-05-12 |
| [outputs/2026-05-11_garry-tan-gstack-gbrain-guide.md](outputs/2026-05-11_garry-tan-gstack-gbrain-guide.md) | Garry Tan（YC）のgstack×gbrain活用ガイド | -/100 | 2026-05-11 |
| [outputs/2026-05-11_hermes-agent-guide.md](outputs/2026-05-11_hermes-agent-guide.md) | ヘルメスエージェント調査レポート（VS Code/スマホ操作可否） | -/100 | 2026-05-11 |
| [outputs/2026-05-08_crypto-history-download-guide.md](outputs/2026-05-08_crypto-history-download-guide.md) | Bybit・Binance・MetaMask 取引履歴ダウンロードガイド（高校生向け） | -/100 | 2026-05-08 |
| [outputs/2026-05-01_claude-code-warp-guide.md](outputs/2026-05-01_claude-code-warp-guide.md) | Claude Code × Warp 移行・補完ガイド（Remote Control含む） | -/100 | 2026-05-01 |
| [outputs/2026-05-01_gmo-coin-deposit-check-guide.md](outputs/2026-05-01_gmo-coin-deposit-check-guide.md) | GMOコイン 入金合計額の確認方法ガイド（高校生向け） | -/100 | 2026-05-01 |
| [outputs/2026-04-30_power-roller-s-effects.md](outputs/2026-04-30_power-roller-s-effects.md) | パワーローラーS 効果評価・使い方・カテゴリー別スコア | -/100 | 2026-04-30 |
| [outputs/2026-04-23_claude-code-cli-manual.md](outputs/2026-04-23_claude-code-cli-manual.md) | Claude Code CLI 初心者向け完全マニュアル 2026（Windows対応版） | 88/100 | 2026-04-28 |
| [outputs/2026-04-23_ec-sakura-review-comparison.md](outputs/2026-04-23_ec-sakura-review-comparison.md) | EC3社サクラレビュー推定割合 比較 2026 | 84/100 | 2026-04-23 |
| [outputs/2026-04-23_claude-code-ui-comparison.md](outputs/2026-04-23_claude-code-ui-comparison.md) | Claude Code 4UIの使い分け比較 2026 | 86/100 | 2026-04-23 |
| [outputs/2026-04-20_japan-ai-startup-investment-trends-2024-2025.md](outputs/2026-04-20_japan-ai-startup-investment-trends-2024-2025.md) | 日本のAIスタートアップ投資トレンド 2024-2025 | 87/100 | 2026-04-20 |

<!-- ブランチマージで追加 2026-05-11 -->
- outputs/2026-05-01_wakkiyai-wemof-fx-indicator.md
- outputs/2026-05-08_bitcoin-price-forecast-2028.md
- outputs/2026-05-10_happy-men-40s-comprehensive-report.md
- outputs/2026-05-10_happy-men-40s-research.md
- outputs/2026-04-29_manga-apps-free-comparison.md
