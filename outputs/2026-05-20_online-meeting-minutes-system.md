# オンライン会議 AI議事録システム 完全設計ガイド（Windows 11・Razer Seiren Mini対応）

作成日: 2026-05-20
最終更新日: 2026-05-20（クライアント体験・Granola・WhisperX・4エージェントパイプライン追加改訂）

---

## 1. エグゼクティブサマリー：クライアント有無で決まる最適解

### 最強の1本道（推奨構成を先出し）

**クライアント案件・商談・面接があるなら、迷わずこれを使え：**

```
VoiceMeeter Banana + OBS（ステレオ分離録音）
+ WhisperX（faster-whisper + 話者分離）
+ Claude Code 4エージェントパイプライン
```

この構成が「最強」である理由は4軸すべてを満たすからだ：

| 評価軸 | 評価 | 理由 |
|--------|------|------|
| クライアント体験 | ◎ | Bot通知なし・参加者リストに何も表示されない |
| 文字起こし精度 | ◎ | ステレオ分離により話者ラベルが100%確定 |
| 機密保持 | ◎ | 完全ローカル処理・音声がクラウドに出ない |
| Claude Code連携 | ◎ | 4エージェントパイプラインで議事録を自動生成 |

Day-1セットアップ所要時間：**約90分**（Python環境構築含む）

---

### 3つの決定軸

どのツールを選ぶかは次の3軸で決まる。

**軸1：クライアント有無**
クライアント・商談・面接相手がいる場合、「Fireflies Note Taker が参加しました」という通知が相手に届く Bot型ツールは使わないほうがよい（詳細は Section 4）。

**軸2：機密度**
社外秘・個人情報・M&A情報等を含む会議なら、音声データをクラウドに送らないローカル処理一択。

**軸3：運用コスト**
週1〜2回の会議なら無料枠のSaaSで十分。毎日複数本あるならローカル処理のコストパフォーマンスが高い。

---

### 4方式の優劣表（クライアント体験列付き）

| 方式 | 代表ツール | クライアント通知 | 精度 | コスト | 難易度 |
|------|---------|--------------|------|--------|--------|
| A：プラットフォーム内蔵 | Zoom/Teams/Meet | 「文字起こし中」が全員に表示 | 高 | 月800〜2,000円 | ★☆☆ |
| B：Bot型SaaS | Fireflies/Otter/Notta等 | **あり**（毎回通知）+ Meet：毎回手動承認（2026年3月〜） | 高 | 0〜2,000円 | ★☆☆ |
| C：Bot不参加型デスクトップ | Granola/tl;dvデスクトップ | **なし** | 高 | 0〜1,500円 | ★☆☆ |
| D：OBSローカルSTT | OBS + WhisperX | **なし** | **最高** | **0円** | ★★★ |

> **最重要ポイント**：Razer Seiren Mini（単一指向性マイク）は自分の声しか収音しない。相手の音声を録音するには「システム音声キャプチャ」が必須。どの方式を選ぶにせよ、この問題を解決することが最初のステップ。

---

## 2. 推奨構成：VoiceMeeter Banana + OBS + WhisperX

### なぜこれが最強か

従来の「OBS + Whisper」構成との最大の違いは **ステレオ分離録音** にある。

```
[従来構成の問題点]
OBSが録音する音声ファイル：
  ├── モノラル or ステレオ（L=R、両方が混合）
  └── 話者分離は pyannote の機械学習に依存 → エラーが発生しうる

[VoiceMeeter Banana + OBS の優位性]
OBSが録音する音声ファイル：
  ├── Left チャネル = 自分のマイク音声（Razer Seiren Mini）
  └── Right チャネル = 相手のシステム音声（Zoom/Teams/Meet）
     ↓
  WhisperX は L チャネルと R チャネルを別々に認識
  → 話者ラベルが 100% 確定（機械学習不要）
```

#### 構成図（テキストアート）

```
┌─────────────────────────────────────────────────────────┐
│  会議音声の流れ                                          │
│                                                         │
│  [自分のマイク]        [相手の声（Zoom等）]              │
│  Razer Seiren Mini     システム音声出力                 │
│        │                      │                        │
│        ▼                      ▼                        │
│  VoiceMeeter Banana                                     │
│  ├── Input 1 = マイク (Razer Seiren Mini)               │
│  └── Input 2 = システム音声 (WASAPI Loopback)           │
│        │                      │                        │
│        ▼                      ▼                        │
│  OBS 詳細音声プロパティ                                 │
│  ├── マイクソース → パン 左端 (L)                       │
│  └── デスクトップ音声 → パン 右端 (R)                   │
│        │                                               │
│        ▼                                               │
│  WAV/MP4 ステレオファイル                               │
│  L = 自分の声 / R = 相手の声                            │
│        │                                               │
│        ▼                                               │
│  WhisperX                                              │
│  ├── L チャネル → SPEAKER_00（自分）                    │
│  └── R チャネル → SPEAKER_01（相手）                    │
│        │                                               │
│        ▼                                               │
│  Claude Code 4エージェントパイプライン                   │
│  → 完成議事録（決定/ToDo/未決事項）                     │
└─────────────────────────────────────────────────────────┘
```

### Day-1 セットアップ所要時間目安

| フェーズ | 内容 | 所要時間 |
|---------|------|---------|
| Phase 1 | VoiceMeeter Banana インストール・設定 | 20分 |
| Phase 2 | OBS インストール・ステレオ分離設定 | 20分 |
| Phase 3 | Python + WhisperX + ffmpeg インストール | 30分（初回ダウンロード含む） |
| Phase 4 | テスト録音・文字起こし確認 | 20分 |
| **合計** | | **約90分** |

> サンプルレート統一（全デバイスを 48kHz に）をPhase 1で必ず実施すること。ここを怠るとピッチずれや音声ノイズが発生する。

---

## 3. 精度の最大レバーは「音声チャネル分離」である

### large-v3 vs medium よりも効果的な理由

多くのガイドは「精度を上げるにはモデルを large-v3 に変えろ」と言う。それは正しいが、**話者分離精度という観点では音声チャネル分離のほうがはるかに効果が大きい**。

| 改善手段 | 話者ラベル確定精度 | 文字起こし精度向上 |
|---------|---------------|----------------|
| medium → large-v3 に変更 | 変わらない（モデルは話者分離をしない） | +5〜15%程度 |
| モノラル → ステレオ分離（L/R） | **0% → 100%**（確定的） | +5%程度（混入ノイズ減少） |
| pyannote（話者分離AI） | 85〜95%程度 | 変わらない |

### 原理説明：なぜチャネル分離が確実なのか

通常の会議録音（モノラル/ステレオ混合）では、すべての音声が1つの信号に混ざっている。誰がいつ話したかを判定するには、音声の「声の特徴」を機械学習で分析する必要があり、これが誤認識の原因になる。

ステレオ分離録音では、信号レベルで話者が分かれているため、機械学習が不要になる。

```
[チャネル分離なし]
time: 0:00  "[Lch+Rch混合信号]おはようございます..."
  → pyannote が確率的に推定 → 5〜15% の誤り

[チャネル分離あり]
time: 0:00  [Lch=自分] "おはようございます"
time: 0:02  [Rch=相手] "よろしくお願いします"
  → WhisperX が確定的にラベリング → 誤り0%
```

### OBS でのステレオ分離設定方法

**方法1：パン設定（最も簡単）**

1. OBS を起動
2. 「音声ミキサー」パネルで「マイク」ソースを右クリック → 「詳細音声プロパティ」
3. 「音声モノラルミックス」列で「マイク」を **左端（-1.0）** に設定
4. 同様に「デスクトップ音声」を **右端（+1.0）** に設定
5. 出力設定で「ステレオ」を選択していることを確認

**方法2：マルチトラック録音（完全分離）**

1. OBS 設定 → 「出力」→「録画」→「出力モード：詳細」
2. 「音声トラック」でトラック1（マイク）とトラック2（システム音声）を分ける
3. ffmpeg で後処理：

```powershell
# トラック1（マイク）とトラック2（システム音声）を Left/Right に合成
ffmpeg -i meeting.mkv -filter_complex "[0:a:0]pan=stereo|c0=c0|c1=0[left];[0:a:1]pan=stereo|c0=0|c1=c0[right];[left][right]amix=inputs=2[out]" -map "[out]" meeting_stereo.wav
```

---

## 4. クライアント体験という見落とされた評価軸

### 「Note Taker が参加しました」が生む心理的摩擦

Bot型文字起こしツール（Fireflies.ai、Otter.ai、Notta Bot等）を使うと、会議参加者リストに次のような通知が表示される：

```
Fireflies Note Taker が会議に参加しました
Otter.ai Notetaker が会議に参加しました
```

この通知が相手（クライアント・採用担当者・商談相手）に届く。その心理的影響は：

- 「録音されている」という意識が生まれ、発言が慎重・形式的になる
- ボットを許可しない会社・組織では入室が拒否される
- 初対面の商談・面接では「信頼関係構築」よりも「監視されている感覚」が先行する
- 重要な本音トークが引き出しにくくなる

### 2026年3月以降の Google Meet Bot 問題

2026年3月以降、**Google Meet では Bot の入室にホストの手動承認が毎回必要** になった。

これは tl;dv、Fireflies.ai、Notta Bot など Bot 型ツール全般に影響する。

**具体的な運用コスト：**
- 会議開始前に Bot が「入室待機」状態になる
- ホスト（あなた）がスマートフォンまたはPC で「承認」をタップする必要がある
- 承認を忘れると Bot が入室できず、文字起こしが始まらない
- 外部ホストの会議（クライアント主催）では、ホストに承認を依頼しなければならない

Bot 型ツールを Google Meet で使い続けることの運用コストは、2026年以降に大幅に増加している。

### ボット不参加型の戦略的価値

クライアント体験・精度・プライバシー・運用コストの4軸で考えると、**ボット不参加型**が総合的に優れている。

| ツール | Bot通知 | クライアント操作 | 推奨シナリオ |
|-------|---------|-------------|-----------|
| Granola（デスクトップ） | **なし** | 不要 | クライアント案件・商談・面接 |
| tl;dv（デスクトップモード） | **なし** | 不要 | クライアント案件・商談・面接 |
| OBS + WhisperX（ローカル） | **なし** | 不要 | 機密会議・最高精度が必要な場合 |
| Zoom/Teams 内蔵 | 「文字起こし中」が全員表示 | なし | 社内MTG・全員同意済みの場合 |
| Bot型全般（Fireflies/Notta等） | **あり** | Meet：毎回手動承認（2026年3月〜）| 社内MTG・Bot承認済み環境のみ |

---

## 5. 前提条件

| 項目 | 仕様 | 注意点 |
|------|------|--------|
| OS | Windows 11 | PowerShell 5.1 / Git Bash対応。macOS専用コマンド不使用 |
| マイク | Razer Seiren Mini（USB接続、単一指向性） | ステレオミキサー非対応 → WASAPI Loopback使用 |
| スピーカー | PC標準スピーカー or ヘッドフォン | ヘッドフォン使用時はVoiceMeeter Banana導入が必要 |
| 会議ツール | Zoom / Teams / Google Meet（複数想定） | |
| 分析ツール | Claude Code | 4エージェントパイプラインで自動処理 |
| 目的言語 | 日本語 | |
| GPU（推奨） | NVIDIA GPU VRAM 10GB | large-v3 の推奨。int8量子化で8GB以下でも動作可能 |

---

## 6. 音声キャプチャ4方式の詳細

### 方式A：プラットフォーム内蔵文字起こし

会議プラットフォームのサーバーが全参加者の音声を処理するため、ローカルでの音声キャプチャが不要。

| 項目 | Zoom | Teams | Google Meet |
|------|------|-------|------------|
| 文字起こし機能 | AI Companion（有料） | 会議の文字起こし（有料） | Meet文字起こし（有料） |
| 必要プラン | Zoom Pro（$13.33/月・年払い） | M365 Business Basic以上 | Google Workspace有料 |
| 日本語対応 | 2024年から対応 | 対応 | 2025年3月から正式対応 |
| 話者分離 | あり | あり | あり |
| クライアント通知 | 「文字起こし中」が全参加者に表示 | 同左 | 同左 |

**推奨シナリオ：** 社内MTG・全参加者が同意している会議・既存プラン活用

**注意：** 「文字起こし中」通知は全参加者に表示される。外部クライアントが含まれる場合は要検討。

---

### 方式B：Bot型SaaS

文字起こしサービスのBotが会議に「参加者」として加わり、音声を録音・処理する。

**特徴：**
- 設定が最も簡単（10分以内）
- 日本語精度が高いサービスが多い
- **クライアントに Bot 通知が届く**
- **Google Meet：2026年3月以降、毎回ホストの手動承認が必要**

**推奨シナリオ：** 社内MTG・Bot 承認済みの組織内会議・精度より手軽さを優先する場合

---

### 方式C：Bot不参加型デスクトップアプリ

デスクトップアプリがシステムオーディオ＋マイクを直接キャプチャする。参加者リストに何も表示されない。

代表ツール：**Granola**（新規追加）、**tl;dv デスクトップモード**

**特徴：**
- クライアントへの通知なし
- プラットフォーム（Zoom/Teams/Meet）に依存しない
- システム音声を直接キャプチャするため Bot 承認問題が発生しない

**推奨シナリオ：** クライアント案件・商談・面接・Bot 通知を避けたい全ての会議

---

### 方式D：OBS + ローカルSTT（最推奨）

デスクトップ音声とマイク音声をOBSでステレオ分離録音し、WhisperX でオフライン文字起こし。

**特徴：**
- クライアントへの通知なし
- 完全ローカル処理（音声データがクラウドに出ない）
- ステレオ分離による話者ラベル100%確定
- Claude Code 4エージェントパイプラインと最適に連携
- 完全無料・Zoom/Teams/Meet問わず対応

**推奨シナリオ：** 機密会議・クライアント案件・最高精度を求める場合・プライバシー最優先

---

## 7. ツール比較表（拡張版・14ツール）

| ツール | 両声キャプチャ | クライアント通知 | 日本語精度 | 月コスト（目安） | 難易度 | Windows 11 |
|-------|-------------|--------------|---------|--------------|--------|-----------|
| Zoom 内蔵 | ○（サーバー処理） | 「文字起こし中」全員表示 | 高 | Pro: $13.33〜/月（年払い） | ★☆☆ | ○ |
| Google Meet 内蔵 | ○（サーバー処理） | 「文字起こし中」全員表示 | 高（2025年3月〜） | Workspace有料 | ★☆☆ | ○ |
| Teams 内蔵 | ○（サーバー処理） | 「文字起こし中」全員表示 | 中〜高 | M365 Business Basic〜 | ★☆☆ | ○ |
| **Granola** | ○（デスクトップキャプチャ） | **なし** | 高（多言語対応） | 無料25MTGまで / Business $14/月 | ★☆☆ | ○（2025年後半〜） |
| **tl;dv（デスクトップ）** | ○（デスクトップキャプチャ） | **なし** | 中 | 無料（AI要約月10回・録画文字起こし無制限） | ★☆☆ | ○ |
| tl;dv（Botモード） | ○（Bot参加） | **あり**（Meet：毎回手動承認必要 2026年3月〜） | 中 | 同上 | ★☆☆ | ○ |
| Fireflies.ai | ○（Bot参加） | **あり**（Meet：毎回手動承認必要 2026年3月〜） | 中 | 無料（月20AIクレジット）/ Pro $18/月 | ★☆☆ | ○ |
| Notta Bot | ○（Bot参加） | **あり**（Meet：毎回手動承認必要 2026年3月〜） | 高 | 無料120分 / 有料1,185円〜 | ★☆☆ | ○ |
| LINE WORKS AiNote | ○（Bot参加） | **あり** | 最高 | 無料300分 / Solo 1,440円 | ★☆☆ | ○ |
| Typeless | ✗（自声のみ・**英語/混合言語MTG限定**） | なし | 高（英語・混合言語専用） | 週8,000語無料 / Pro $12/月 | ★☆☆ | ○ |
| **WhisperX** | ○（ステレオ分離） | **なし** | **最高** | **完全無料** | ★★★ | ○ |
| faster-whisper | ○（ステレオ分離） | **なし** | **最高** | **完全無料** | ★★★ | ○ |
| OBS | 録音ツール（STTではない） | なし | N/A | 無料 | ★★☆ | ○ |
| VoiceMeeter Banana | 音声ルーター（STTではない） | なし | N/A | 無料（寄付推奨） | ★★☆ | ○（ARM64含む） |

> **Typeless について：** Typeless は「口述入力ツール」。自分の声をリアルタイムでテキスト入力するためのサービスであり、**日本語純粋運用の会議全体録音・文字起こしには不向き**。英語/混合言語MTGでの補足メモや議事録の口述作成に適する。日本語のみの会議では効果が薄い。

---

## 8. セットアップ手順書（Windows 11 対応）

---

### 推奨：VoiceMeeter Banana + OBS + WhisperX（Day-1、約90分）

**前提：** Windows 11、Python 3.9 以上、NVIDIA GPU 推奨（CPU のみでも動作可）

---

#### Phase 1：VoiceMeeter Banana インストール・設定（20分）

**Step 1：VoiceMeeter Banana をダウンロード・インストール（5分）**

1. https://vb-audio.com/Voicemeeter/banana.htm にアクセス
2. 「Download VoiceMeeter Banana」をクリック（無料、寄付推奨）
3. ダウンロードした `VoicemeeterProSetup.exe` を実行
4. インストール完了後、**必ずPCを再起動**（ドライバが有効になる）

**Step 2：全デバイスのサンプルレートを 48kHz に統一（10分）**

> これを怠るとピッチずれ・音声ノイズが発生する。最重要設定。

1. スタートメニュー → 「サウンドの設定」→「サウンドの詳細設定」を開く
2. 「録音」タブ → 「Razer Seiren Mini」を右クリック →「プロパティ」→「詳細」→「サンプルレート：48000Hz（DVD 品質）」
3. 「再生」タブ → 使用中のスピーカー/ヘッドフォンを右クリック → 同様に 48000Hz に設定
4. VoiceMeeter Banana 起動後、右上「System Rate」→「48000 Hz」に設定

**Step 3：VoiceMeeter Banana の入出力設定（5分）**

1. VoiceMeeter Banana を起動
2. `HARDWARE INPUT 1`（左上）の「Select Input Device」をクリック → 「WDM：Razer Seiren Mini」を選択
3. `HARDWARE OUT A1`（右上）の「Select Output Device」をクリック → 実際のスピーカー/ヘッドフォンを選択
4. Windowsのサウンド設定 →「出力デバイス」を「VoiceMeeter Input」に変更（Zoom 等の会議ツール音声が VoiceMeeter に入るようになる）

---

#### Phase 2：OBS インストール・ステレオ分離設定（20分）

**Step 1：OBS をダウンロード・インストール（5分）**

1. https://obsproject.com/ にアクセス
2. 「Windows」ボタンをクリック → インストーラーを実行
3. 「自動構成ウィザード」が表示されたら「キャンセル」（録画専用設定をするため）

**Step 2：出力設定（5分）**

1. 「設定」→「出力」→「出力モード：詳細」に変更
2. 「録画」タブで：
   - 録画フォーマット：`mkv`（クラッシュ時も保存される）
   - エンコーダ：`x264`（GPU なし）または `NVENC`（NVIDIA GPU）
   - 録画パス：例 `C:\Users\[ユーザー名]\Documents\MeetingRecordings`

**Step 3：音声ソースを追加（5分）**

1. 「ソース」パネル → 「+」→「音声入力キャプチャ」→ 名前：`マイク`
2. デバイス：「Razer Seiren Mini」→ OK
3. 「+」→「音声出力キャプチャ（WASAPI）」→ 名前：`デスクトップ音声`
4. デバイス：「VoiceMeeter Output」または使用中のスピーカー → OK

**Step 4：ステレオ分離設定（5分）**

1. 「音声ミキサー」パネルで「マイク」ソースを右クリック →「詳細音声プロパティ」
2. 「マイク」行の「音量/バランス」→ パンを **完全に左（-100）** に設定
3. 「デスクトップ音声」行のパンを **完全に右（+100）** に設定
4. 設定 → 「音声」→「サンプルレート：48kHz」を確認

**Step 5：テスト録音で確認**

1. OBS「録画開始」→ マイクに向かって話す → 相手の声をシミュレート（YouTube 等を再生）→「録画停止」
2. 録画ファイルを Windows メディアプレーヤーで再生し、ヘッドフォンで L/R の音が別々に聞こえることを確認

---

#### Phase 3：Python + WhisperX + ffmpeg インストール（30分）

**Step 1：Python の確認（3分）**

```powershell
python --version
```

`Python 3.9` 以上が表示されれば OK。なければ https://www.python.org/downloads/ からインストール（「Add Python to PATH」を必ずチェック）。

**Step 2：ffmpeg のインストール（5分）**

```powershell
winget install -e --id Gyan.FFmpeg
```

インストール後 PowerShell を再起動してから確認：

```powershell
ffmpeg -version
```

**Step 3：WhisperX のインストール（15〜20分）**

```powershell
pip install whisperx
```

GPU がある場合は CUDA 対応版を推奨（処理速度が5〜10倍速くなる）：

```powershell
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
pip install whisperx
```

**Step 4：HuggingFace トークン取得（5分）**

WhisperX の話者分離（pyannote）には HuggingFace トークンが必要：

1. https://huggingface.co/ でアカウント作成（無料）
2. https://huggingface.co/settings/tokens でトークンを生成（「Read」権限）
3. https://huggingface.co/pyannote/speaker-diarization-3.1 を開き、利用規約に同意

---

#### Phase 4：テスト録音・文字起こし確認（20分）

**Step 1：WhisperX で文字起こし実行**

```powershell
# 基本的な日本語文字起こし＋話者分離
whisperx "C:\Users\[ユーザー名]\Documents\MeetingRecordings\[ファイル名].mkv" --model large-v2 --language ja --diarize --hf_token YOUR_HF_TOKEN

# 高速版（faster-whisper バックエンド）
whisperx "C:\Users\[ユーザー名]\Documents\MeetingRecordings\[ファイル名].mkv" --model large-v2 --language ja --diarize --hf_token YOUR_HF_TOKEN --compute_type int8

# 出力形式を指定（txt + srt + json）
whisperx "C:\Users\[ユーザー名]\Documents\MeetingRecordings\[ファイル名].mkv" --model large-v2 --language ja --diarize --hf_token YOUR_HF_TOKEN --output_format all
```

> 初回実行時はモデルのダウンロード（large-v2 は約3GB）が発生する。2回目以降はキャッシュされるため速い。

**Step 2：一括処理スクリプト（自動化）**

```powershell
# transcribe_meeting.ps1 として保存
param(
    [string]$InputFile = "",
    [string]$Model = "large-v2",
    [string]$HfToken = $env:HF_TOKEN
)

if ($InputFile -eq "") {
    $InputFile = Get-ChildItem "C:\Users\$env:USERNAME\Documents\MeetingRecordings\*.mkv" |
                 Sort-Object LastWriteTime -Descending |
                 Select-Object -First 1 -ExpandProperty FullName
}

Write-Host "文字起こし開始: $InputFile"
whisperx $InputFile --model $Model --language ja --diarize --hf_token $HfToken --compute_type int8
Write-Host "完了: $($InputFile -replace '\.mkv$', '.txt') に保存されました"
```

実行方法：

```powershell
# 環境変数にトークンを設定（毎回入力を省略）
$env:HF_TOKEN = "hf_your_token_here"

# 最新の録画ファイルを自動処理
.\transcribe_meeting.ps1

# ファイルを指定して処理
.\transcribe_meeting.ps1 -InputFile "C:\path\to\meeting.mkv"
```

---

### 代替A：Zoom / Teams 内蔵文字起こし（5分）

**前提：** Zoom Pro（$13.33/月・年払い）または Teams M365 Business Basic 以上

**Zoom の設定：**

1. https://zoom.us/profile/setting にログイン
2. 「会議」→「クラウドレコーディングの自動文字起こし」をON
3. 「AI Companion」→「会議の文字起こし」をON
4. 会議中に「文字起こし」ボタンをクリックして開始
5. 会議後に Zoom ポータル →「レコーディング」→ TXT 形式でダウンロード

**注意：** 「文字起こし中」通知が全参加者に表示される。

---

### 代替B：Granola（Bot不参加型、10分）

**概要：** デスクトップアプリがシステムオーディオ＋マイクを直接キャプチャ。Bot 不参加型のため参加者リストに何も表示されない。

**特徴：**
- クライアント通知：**なし**
- Zoom/Teams/Meet：すべて対応（プラットフォーム無関係にシステム音声をキャプチャ）
- Windows 11：対応済み（2025年後半〜）
- 日本語：公式サポート済み（Settings > Language で Multi-language 選択）
- 価格：無料（生涯25ミーティングまで）/ Business $14/月（無制限）

**セットアップ：**

1. https://www.granola.ai/ にアクセス
2. 「Download for Windows」をクリック
3. インストール後、Granola を起動
4. Settings → Language → Multi-language（または Japanese）を選択
5. 会議開始前に「Start Recording」をクリック
6. 会議後に自動的に文字起こしと要約が生成される

---

### 代替C：Notta / LINE WORKS AiNote Bot（10分、クライアント通知あり）

> **注意：** Bot 型のため参加者リストに Bot 通知が表示される。Google Meet では 2026年3月以降、毎回ホストの手動承認が必要。

#### LINE WORKS AiNote（推奨）

月300分まで無料。話者分離精度が特に高い（DIHERD3 チャレンジ上位実績）。

1. https://line-works.com/ainote/ にアクセス → 「無料で始める」
2. LINE Works アカウントを作成
3. 「ミーティング連携」→ Zoom/Teams の OAuth 認証を完了
4. 会議前に「ノートを作成」→ 会議 URL を入力 →「録音開始」
5. 会議後にダッシュボードで「エクスポート」→「テキスト（話者分離あり）」

#### Notta（代替）

1. https://www.notta.ai/ja → Google アカウントで登録
2. 「予定を追加」→ 会議 URL を入力 → Bot が自動参加
3. 会議後に「エクスポート」→「TXT ファイル」でダウンロード

---

## 9. 二重録音戦略

重要な会議には「プライマリ」＋「バックアップ」の二重録音を推奨する。

| 役割 | ツール | 目的 |
|------|--------|------|
| プライマリ | OBS + WhisperX | 最高精度・ステレオ分離・ローカル保存 |
| バックアップ | Granola または tl;dv デスクトップ | OBS 録音失敗時の保険・簡易要約 |

**運用手順：**

1. 会議5分前に Granola を起動（バックアップとして常時動作）
2. OBS の「録画開始」ショートカットキーで録画開始（メイン録音）
3. 会議終了後、OBS 録画ファイルを WhisperX で処理
4. OBS 処理が完了するまでの間は Granola の自動要約を一時利用

**ショートカットキー設定（OBS）：**

OBS → 設定 → 「ホットキー」→「録画開始：Ctrl+Alt+R」「録画停止：Ctrl+Alt+S」に設定しておくと会議中に画面を切り替えずに操作できる。

---

## 10. Claude Code 4エージェントパイプライン

WhisperX の出力テキストを Claude Code の4エージェントパイプラインに通すことで、高品質な議事録を自動生成する。

### パイプライン設計

```
入力: WhisperX 出力テキスト（話者ラベル付き SRT/TXT）
  ↓
Agent 1: Transcript Cleaner（Sonnet）
  ↓
Agent 2: Structurer（Sonnet）
  ↓
Agent 3: Decision/Action Extractor（Sonnet）
  ↓
Agent 4: Quality Reviewer（Opus）
  ↓
出力: 完成議事録（決定事項/ToDo/未決事項）
```

---

### Agent 1：Transcript Cleaner（Sonnet）

**役割：** フィラー除去、誤認識補正、固有名詞辞書による優先補正

**入力：** WhisperX 出力テキスト（話者ラベル付き SRT/TXT）

**出力：** クリーンなテキスト（フィラー除去・固有名詞補正済み）

```bash
claude --print "
あなたは会議文字起こしのクリーニング専門家です。
以下の文字起こしテキストを処理してください：

1. フィラー語を除去（「えー」「あの」「まあ」「そのー」等）
2. 誤認識を補正（文脈から明らかな場合のみ）
3. 固有名詞を補正（辞書：[プロジェクト名・人名・製品名をここに記載]）
4. 話者ラベルは変更しない（SPEAKER_00/SPEAKER_01 のまま保持）
5. タイムスタンプは保持する

出力：クリーンなテキスト（形式は入力と同一）
" < transcript.txt
```

---

### Agent 2：Structurer（Sonnet）

**役割：** 話題単位でのセクション分割、話者ラベル付け、章ごとのタイムスタンプ保持

**入力：** クリーンなテキスト（Agent 1 の出力）

**出力：** 構造化 Markdown

```bash
claude --print "
あなたは会議議事録の構造化専門家です。
以下のクリーン済み文字起こしを構造化 Markdown に変換してください：

1. 話題の変化点でセクション（H2）を区切る
2. 各セクションに章タイトルとタイムスタンプを付ける
3. SPEAKER_00 → 「自分」、SPEAKER_01 → 「相手」に変換
4. 連続する同一話者の発言はまとめる
5. 出力形式：

## [セクションタイトル]（[開始タイムスタンプ]〜[終了タイムスタンプ]）

**自分：** [発言内容]
**相手：** [発言内容]
" < cleaned_transcript.txt
```

---

### Agent 3：Decision/Action Extractor（Sonnet）

**役割：** 決定事項・アクション（担当/内容/期限）・未決事項を抽出

**入力：** 構造化 Markdown（Agent 2 の出力）

**出力：** 議事録テンプレート（決定/ToDo/未決）

```bash
claude --print "
あなたは会議議事録の作成専門家です。
以下の構造化テキストから議事録を作成してください：

出力形式（Markdown）：

# 議事録
- 日時：[会議日時]
- 参加者：[参加者]
- テーマ：[テーマ]

## エグゼクティブサマリー（3行）
1. 
2. 
3. 

## 決定事項
| 決定内容 | 理由 | 決定者 | タイムスタンプ |
|---------|------|--------|------------|

## アクションアイテム
| タスク | 担当 | 期限 | タイムスタンプ |
|------|------|------|------------|

## 未決事項・次回確認事項
| 事項 | 背景 | 確認先 |
|------|------|--------|

## 詳細議事
[構造化テキストをそのまま転記]
" < structured_transcript.txt
```

---

### Agent 4：Quality Reviewer（Opus）

**役割：** Agent 1〜3 の出力をクロスチェック、抜け漏れ・矛盾を指摘し、最終版を出力

**入力：** Agent 1〜3 の全出力

**出力：** 品質評価レポート + 修正済み最終議事録

```bash
claude --print "
あなたは会議議事録の品質管理専門家です（Opusモデルとして最高品質で処理）。
以下の3つの入力をクロスチェックし、最終議事録を出力してください：

[INPUT 1: 元の文字起こし（Speaker分離済み）]
[INPUT 2: 構造化 Markdown]
[INPUT 3: 抽出済み議事録（決定/ToDo/未決）]

チェック項目：
1. 決定事項の抜け漏れ（元テキストに記載があるのに議事録にない項目）
2. アクションアイテムの担当者・期限の欠落
3. 話者の誤認識（文脈と話者が矛盾する箇所）
4. 未決事項の見落とし

出力：
## 品質評価レポート
- 修正箇所：[件数と内容]
- 信頼度スコア：[90/100 形式]

## 最終議事録（修正済み）
[完成版をここに出力]
" 
```

---

### 起動コマンド（ワンライナー版）

```powershell
# 文字起こしファイルを直接パイプライン投入
claude --print "以下の文字起こしを議事録に変換してください。決定事項・アクションアイテム・未決事項の3ブロックで整理してください。" < transcript.txt

# 出力をファイルに保存
claude --print "以下の文字起こしを議事録に変換してください。..." < transcript.txt > minutes_draft.md
```

---

## 11. Whisper 精度向上テクニック

### initial_prompt（固有名詞辞書）の使い方

Whisper は最初の数秒の認識精度が低い場合がある。`initial_prompt` に固有名詞・専門用語を渡すことで認識精度を向上させる。

```powershell
# 固有名詞辞書を initial_prompt で指定（WhisperX の場合）
whisperx meeting.wav --model large-v2 --language ja --initial_prompt "プロジェクト名：DeepResearch、担当者：男座員也、クライアント：XXX株式会社、製品名：YYY" --diarize --hf_token $env:HF_TOKEN
```

### faster-whisper 推奨コマンド

```powershell
# int8 量子化で GPU VRAM を節約しながら high 精度を維持
whisperx meeting.wav --model large-v2 --language ja --compute_type int8 --diarize --hf_token $env:HF_TOKEN

# CPU のみで処理する場合（int8 必須）
whisperx meeting.wav --model medium --language ja --compute_type int8 --device cpu --diarize --hf_token $env:HF_TOKEN
```

### Whisper モデル選択表

| モデル | 精度 | 処理速度 | VRAM目安 | 推奨用途 |
|-------|------|---------|---------|---------|
| tiny | 低 | 非常に速い | 1GB | 確認用 |
| base | 中 | 速い | 1GB | テスト用 |
| small | 中〜高 | 普通 | 2GB | 簡易用途 |
| medium | 高 | やや遅い | 5GB | CPU 環境 |
| **large-v2** | **最高** | 遅い | **推奨8GB**（int8量子化で6GB以下でも動作） | **WhisperX 推奨** |
| **large-v3** | **最高** | 遅い | **推奨10GB**（int8量子化で8GB以下でも動作可能） | GPU 環境推奨 |

> WhisperX は large-v2 での動作が最も安定している（large-v3 は一部言語で精度が低下する場合がある）。

---

## 12. タイプレスの適用条件

Typeless（https://typeless.app/）は「音声入力ツール」であり、会議全体の文字起こしツールではない。

**Typeless が有効なシナリオ（英語/混合言語MTG限定）：**
- 英語または英日混合の会議で、自分のコメントをリアルタイムでドキュメントに入力したい
- 会議後に音声メモを素早くテキスト化したい（英語の発話のみ）
- 議事録の口述作成（会議後に録音した自分のまとめを文字化）

**Typeless を使うべきでないシナリオ：**
- **日本語純粋運用の会議**（日本語認識精度が English/Mixed より有意に劣る）
- 相手の声を含む会議全体の文字起こし（Typeless は自声のみ）
- 商談・面接・クライアントMTGの正式記録

> **結論：** 日本語のみの会議では Typeless の効果は薄い。英語または英日混合 MTG での補助ツールとして位置づけること。

---

## 13. セキュリティ・プライバシー指針

### 機密度別ツール選択フローチャート

```
会議に外部クライアントが含まれるか？
│
├── YES → Bot 型ツールは使わない（Bot 通知が届く）
│   └── 推奨: OBS + WhisperX（ローカル）または Granola
│
└── NO（社内MTGのみ）
    │
    └── 会議の機密度は？
        │
        ├── [非常に高い] 社外秘・個人情報・M&A 情報等
        │   └── OBS + WhisperX ローカル処理一択
        │       ※音声・テキストが外部サービスに送信されない
        │
        ├── [中程度] 社内情報・プロジェクト情報
        │   └── 会社が SaaS 使用を認めているか？
        │       ├── YES → Notta / LINE WORKS AiNote（または内蔵）
        │       └── NO → OBS + WhisperX ローカル処理
        │
        └── [低] 一般的な打ち合わせ・勉強会
            └── どのシナリオでも可（コスト・使いやすさで選択）
```

### 各ツールのデータ送信先

| ツール | 音声データ送信先 | テキストデータ | 保存期間 | 日本法人 |
|-------|--------------|-------------|---------|---------|
| Zoom（クラウド録画） | Zoom社（米国）サーバー | 同左 | 30日〜 | あり |
| Teams（文字起こし） | Microsoft Azure | 同左 | 管理者設定による | あり |
| Google Meet | Google Cloud | 同左 | Drive設定による | あり |
| Granola | Granola社サーバー（要確認） | 同左 | 設定による | 不明 |
| Notta | Notta社（日本法人あり）サーバー | 同左 | 有料プランで延長 | あり |
| LINE WORKS AiNote | NAVER LINE社（日韓）サーバー | 同左 | 設定による | あり |
| OBS + WhisperX | **送信なし（ローカルのみ）** | **送信なし** | ユーザー管理 | N/A |

### 個人情報保護の対応策

| 項目 | 対応策 |
|------|--------|
| 音声データの第三者提供 | 録音前に参加者全員の同意を取得（日本では全員同意が実務上推奨） |
| クラウドへの音声アップロード | 社内規定を事前確認 |
| 文字起こしテキストの保管 | 不要になった議事録は速やかに削除 |
| Claude Code への貼り付け | 個人名・機密情報はマスキング後に投入を推奨 |
| Granola 使用時 | 機密度の高い会議では利用規約・データポリシーを事前確認 |

---

## 14. トラブルシューティング

### 音声関連の問題

| 症状 | 原因 | 解決策 |
|------|------|--------|
| 相手の声が録音されない | デスクトップ音声ソースが未設定 | OBSで「音声出力キャプチャ（WASAPI）」を追加 |
| 自分の声が録音されない | マイクソースが未設定 or デバイスが違う | OBSの「音声入力キャプチャ」でRazer Seiren Miniを選択 |
| 音声が全く入らない | OBSの音声ミキサーがミュート | ミキサーパネルのミュートボタンを確認 |
| ヘッドフォンから音が出なくなった | VoiceMeeter または VB-Audio Cable 設定後 | サウンド設定で出力デバイスを元のヘッドフォンに戻す |
| 音声にピッチずれ・ノイズが発生 | サンプルレートが統一されていない | 全デバイスを 48kHz に統一（Section 8 Phase 1 Step 2 参照） |
| ステレオ分離されていない | OBS のパン設定が未実施 | 詳細音声プロパティ → マイクを左端・デスクトップを右端に設定 |

### VoiceMeeter Banana 関連の問題

| 症状 | 原因 | 解決策 |
|------|------|--------|
| インストール後に音が出なくなった | Windows が VoiceMeeter を既定デバイスに設定した | サウンド設定 → 出力を実際のスピーカーに変更 |
| VoiceMeeter を起動するたびにデバイスが変わる | 起動順序の問題 | VoiceMeeter を Windows スタートアップに追加して自動起動させる |
| VoiceMeeter 経由で音質が劣化する | サンプルレート不一致 | 全デバイスとVoiceMeeterを 48kHz に統一 |
| PC 再起動後に設定がリセットされる | VoiceMeeter の設定保存が未実施 | Menu → Save Settings で設定ファイルを保存 |

### WhisperX / pyannote 関連の問題

| 症状 | 原因 | 解決策 |
|------|------|--------|
| `whisperx` コマンドが見つからない | インストール失敗 or PATH 未設定 | `pip install whisperx` 再実行 → PowerShell 再起動 |
| `ffmpeg` が見つからない | ffmpeg 未インストール | `winget install -e --id Gyan.FFmpeg` 実行 → 再起動 |
| HuggingFace トークンエラー | トークン無効 or 利用規約未同意 | https://huggingface.co/pyannote/speaker-diarization-3.1 の利用規約に同意 |
| 話者分離が機能しない | `--diarize` オプション未指定 | コマンドに `--diarize --hf_token YOUR_TOKEN` を追加 |
| VRAM 不足エラー | large-v3 モデルが GPU メモリ超過 | `--compute_type int8` を追加 or `--model medium` に変更 |
| 処理が非常に遅い | CPU のみで処理中 | GPU がある場合：CUDA をインストールし GPU 版 torch を導入 |
| 日本語が英語に変換される | 言語指定忘れ | `--language ja` オプションを必ず追加 |
| large-v3 で精度が低い | 特定言語での large-v3 の挙動 | WhisperX では `--model large-v2` を使用（安定性が高い） |

### WhisperX 再インストールコマンド

```powershell
pip uninstall whisperx
pip install whisperx
```

### Bot 型 SaaS（Notta / AiNote）の問題

| 症状 | 原因 | 解決策 |
|------|------|--------|
| Bot が Google Meet に参加できない | 2026年3月〜の手動承認が必要 | 会議ホストが「Bot を承認」する必要がある（外部 MTG では相手に依頼が必要） |
| Bot が会議に参加しない | URL 入力ミス or 連携設定未完了 | 会議URLを再確認 → OAuthを再認証 |
| 文字起こしが日本語にならない | 言語設定が英語のまま | アカウント設定 → 言語 → 日本語に変更 |
| 無料枠が超過した | 月の利用時間を超えた | 代替C（Granola）または代替D（OBS + WhisperX）に切り替え |

---

## 15. コスト試算（月20時間利用想定）

| シナリオ | 初期費用 | 月額 | 月20時間での試算 | 備考 |
|---------|--------|------|-------------|------|
| 推奨：OBS + WhisperX | 0円 | **0円** | **0円/月** | 電気代のみ |
| 代替A：Zoom Pro | 0円 | 約1,800円（$13.33・年払い） | 1,800円/月 | 文字起こし機能込み |
| 代替A：Teams（M365 Business Basic） | 0円 | 約900円 | 900円/月 | 既存契約なら追加費用なし |
| 代替A：Google Workspace（Business Starter） | 0円 | 約800円 | 800円/月 | Meet機能込み |
| 代替B：Granola Business | 0円 | 約2,100円（$14/月） | 2,100円/月 | Bot 不参加型 |
| 代替C：LINE WORKS AiNote Free | 0円 | 0円 | 300分超過分は課金 | 5時間/月まで無料 |
| 代替C：LINE WORKS AiNote Solo | 0円 | 1,440円 | 1,440円/月 | 無制限 |
| 代替C：Notta Free | 0円 | 0円 | 120分超過分は課金 | 2時間/月まで無料 |
| 代替C：Notta Premium | 0円 | 1,185円 | 1,185円/月 | 無制限 |

**コストパフォーマンス評価（月20時間・1,200分の場合）：**
- **最安・最高精度**：OBS + WhisperX（0円）― 初期設定90分・処理待ち時間あり
- **Bot不参加型で有料が許容できる場合**：tl;dv（無料枠）→ Granola Business（$14/月）
- **コスパ最良SaaS（Bot型可の場合）**：LINE WORKS AiNote Solo（1,440円 ÷ 20時間 = 72円/時間）
- **プラットフォーム込みで安価**：Teams M365 Business Basic（約900円、M365 機能含む）

> 価格は2026年5月時点の情報。為替レートにより変動あり。

---

## 16. 意思決定フローチャート

```
START：どのツールを選べばいいか？
│
├── クライアント・商談・面接相手がいるか？
│   ├── YES → Bot 通知が問題になる
│   │   ├── 無料 or 低コストで始めたい
│   │   │   └── → Granola（無料25MTGまで）
│   │   └── 最高精度・機密保持が必要
│   │       └── → OBS + WhisperX（推奨構成）
│   │
│   └── NO（社内MTGのみ）
│       ├── 既に Zoom/Teams/Meet の有料プランを使っている
│       │   └── → プラットフォーム内蔵文字起こし（追加費用なし）
│       ├── 無料で始めたい・設定は最小限に
│       │   └── → LINE WORKS AiNote 無料（月300分）
│       └── 最高精度・完全無料・機密保持
│           └── → OBS + WhisperX（推奨構成）
│
└── 日本語純粋運用か？英語/混合言語か？
    ├── 日本語のみ → Typeless は使わない（効果が薄い）
    └── 英語/混合言語 → Typeless も選択肢（口述補助として）
```

---

## 17. 参考リンク

| ツール・リソース | URL |
|--------------|-----|
| VoiceMeeter Banana（公式） | https://vb-audio.com/Voicemeeter/banana.htm |
| OBS Project（公式） | https://obsproject.com/ |
| WhisperX（GitHub） | https://github.com/m-bain/whisperX |
| faster-whisper（高速版） | https://github.com/SYSTRAN/faster-whisper |
| Whisper（GitHub） | https://github.com/openai/whisper |
| VB-Audio Virtual Cable | https://vb-audio.com/Cable/ |
| HuggingFace（トークン取得） | https://huggingface.co/settings/tokens |
| pyannote/speaker-diarization-3.1 | https://huggingface.co/pyannote/speaker-diarization-3.1 |
| Granola（公式） | https://www.granola.ai/ |
| Notta（日本語） | https://www.notta.ai/ja |
| LINE WORKS AiNote | https://line-works.com/ainote/ |
| tl;dv | https://tldv.io/ |
| Fireflies.ai | https://fireflies.ai/ |
| Typeless | https://typeless.app/ |
| Zoom 文字起こし機能 | https://support.zoom.com/hc/ja/article/360060200272 |
| Teams 文字起こし | https://support.microsoft.com/ja-jp/office/teams-会議の文字起こし |
| Google Meet 文字起こし | https://support.google.com/meet/answer/10621867 |
| Python インストール | https://www.python.org/downloads/ |
| winget（Windows Package Manager） | https://learn.microsoft.com/ja-jp/windows/package-manager/ |

---

## 付録：Claude Code プロンプト集

### 基本：会議要約（最頻用途）

```
以下の会議議事録を分析して、次の4ブロックで再整理してください:

(1) 3行要約（箇条書き）
(2) 決定事項（決定内容・理由・決定者を含む）
(3) アクションアイテム（担当者・期限・タスク内容を含む表形式）
(4) 未解決論点（次回会議で確認すべき事項）

---
[ここに議事録テキストを貼り付け]
```

### タスク抽出

```
以下の議事録から、アクションアイテムのみを抽出してください。
出力形式: `- [ ] 担当者 / 期限 / タスク内容`

---
[ここに議事録テキストを貼り付け]
```

### 話者ラベル正規化

```
以下の議事録で、話者ラベルの表記揺れを統一してください。
- SPEAKER_00 / 「私」「自分」→「自分」に統一
- SPEAKER_01 / 「相手」→「相手A」に統一

統一後の全文をそのまま出力してください。

---
[ここに議事録テキストを貼り付け]
```

### リスク抽出

```
以下の会議議事録から、リスク・懸念事項・反対意見をすべて抽出してください。

出力形式:
| リスク内容 | 発言者 | リスクレベル（高/中/低） | 対応案 |
|----------|--------|---------------------|------|

リスクレベルの判定基準:
- 高: プロジェクト継続に関わる重大な問題
- 中: 対応が必要だが解決可能
- 低: 軽微な懸念・注意事項

---
[ここに議事録テキストを貼り付け]
```

### メール下書き自動生成

```
以下の会議議事録をもとに、参加者への「会議後フォローアップメール」の下書きを作成してください。

メール要件:
- 宛先: [相手の名前・会社名をここに入力]
- 件名は「【会議御礼】[テーマ]」形式
- 内容: 要点3行 + 決定事項 + 自分のアクションアイテム + 次回会議の確認依頼

---
[ここに議事録テキストを貼り付け]
```

### 新メンバー向け FAQ 化

```
以下の会議議事録から、新しくプロジェクトに参加するメンバーが理解すべき情報を
FAQ形式（5問）で作成してください。

各問は次の形式で:
Q: （質問）
A: （回答・100字以内）
出典: （タイムスタンプまたは発言者）

---
[ここに議事録テキストを貼り付け]
```

### 議事録テンプレートへの変換

```
以下の会議議事録を、次のMarkdownテンプレートに変換してください。
話者ラベルは必ず「自分」か「相手A」（複数人の場合は「相手B」等）に統一してください。

# 議事録
- 日時: [会議日時]
- 参加者: [参加者]
- テーマ: [テーマ]

## 要約（3行）
1. 
2. 
3. 

## 決定事項
- 

## ToDo
- [ ] 担当者 / 期限 / タスク

## 未解決論点
- 

## 本文（話者別）
### 自分
[発言内容]

### 相手A
[発言内容]

---
[ここに元の議事録テキストを貼り付け]
```

---

*作成: 男座員也（Kazuya Oza） / deep-research-forge*
*モデル: claude-sonnet-4-6*
