## 🧠 Part 7：コンテキスト管理と自動実行

### コンテキスト消費を「常に見える」状態にする

Claude Code はセッション中に**コンテキストウィンドウ**を消費していきます。Sonnet 4.6 の場合 1M トークンですが、無駄遣いすると本題で使えなくなります。

#### 方法1：`/context` コマンドで都度確認

```
> /context
```

セッションの累積消費を表示します。

#### 方法2：StatusLine でリアルタイム表示

**StatusLine** は Claude Code の状態をターミナル下部に常時表示する機能。`context_window.remaining_percentage` を含むJSONを毎ターン受け取れます。

```bash
# ~/.claude/statusline.sh の例
#!/bin/bash
data=$(cat)
remaining=$(echo "$data" | jq -r '.context_window.remaining_percentage')
echo "Context: ${remaining}%"
```

人気ツール **ccstatusline** を使うとさらに簡単です：

```bash
npm install -g ccstatusline
ccstatusline init
```

> 💡 **Hooks の中で StatusLine だけがコンテキスト残量を取得できる**。`PreToolUse` や `PostToolUse` は知らない。これは公式仕様。

### `/compact` で自動圧縮

長時間セッションで残量が減ったら：

```
> /compact
```

過去のやり取りを要約して圧縮し、コンテキストを空けます。重要情報が消えないよう CLAUDE.md に：

```markdown
## Compaction Rule
- 圧縮時は「変更したファイル一覧」「実行したテストコマンド」を必ず保持
```

### Auto Mode（2026年3月研究プレビュー）

**Auto Mode** は「安全な操作は自動許可、危険な操作は遮断」を分類器で判定する機能です（Web版経由・Pro/Max）。

```
> /auto on
```

| 操作 | 自動実行 | 確認必須 |
|-----|---------|---------|
| ファイル読込 | ✅ | — |
| 同一ディレクトリ内Edit | ✅ | — |
| `git commit` | ✅ | — |
| `git push --force` | ❌ | ✅ |
| `rm -rf` | ❌ | ✅ |

### Hooks：12種のライフサイクルイベント

Hooksは Claude のライフサイクルに**自動実行される shell コマンド**です。プロンプトと違い「**必ず実行が保証される**」のが特徴。

#### 主要なHooks

| Hook | 発火タイミング | 主な用途 |
|-----|--------------|---------|
| **PreToolUse** | ツール実行**前** | セキュリティチェック・ブロック |
| **PostToolUse** | ツール実行**後** | フォーマット・lint・通知 |
| **PreCompact** | 圧縮前 | 重要情報のバックアップ |
| **PostCompact** | 圧縮後 | コンテキスト復元 |
| **SessionStart** | セッション開始 | 環境準備 |
| **Stop** | 応答終了 | ログ・通知 |

#### 実例1：PostToolUse で自動 Prettier

`.claude/settings.json`：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$FILE_PATH\""
          }
        ]
      }
    ]
  }
}
```

これで Claude が編集したファイルは**必ず**整形されます。

#### 実例2：PreToolUse で危険操作ブロック

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"$COMMAND\" | grep -qE '(rm -rf|--force)' && exit 2 || exit 0"
          }
        ]
      }
    ]
  }
}
```

`rm -rf` や `--force` を含むコマンドは**実行前に止まる**。

📚 公式ドキュメント：[Hooks reference](https://code.claude.com/docs/en/hooks)

---
