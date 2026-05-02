## 🔧 Part 9：トラブルシューティング

### よくあるエラーと対処

| 症状 | 原因 | 対処 |
|-----|------|------|
| `claude: command not found` | PATHが通っていない | シェル再起動 or `~/.bashrc` 確認 |
| 認証画面が開かない | ブラウザ未指定 | `BROWSER=firefox claude` で明示 |
| `Rate limit exceeded` | API制限到達 | 待機 or プラン上位化 |
| `Context window full` | コンテキスト満杯 | `/compact` or `/clear` |
| Edit が失敗する | 文字列が一意でない | 周辺コンテキストを増やして再試行 |
| MCP server接続失敗 | 設定ミス | `claude mcp list` で状態確認 |
| Remote Control切断 | 10分以上ネット断 | 再接続、PC側の terminal 確認 |
| Worktreeが残る | 異常終了 | `git worktree prune` で掃除 |

### ログの場所

```bash
# macOS / Linux
~/.claude/logs/

# Windows
%USERPROFILE%\.claude\logs\
```

### バージョン関連の問題

```bash
claude --version       # 現在のバージョン確認
# Native Installerなら自動更新（latest/stable channel）
```

> ⚠️ **古いバージョンが原因の問題が多い**。エラーが出たらまずアップデート。

### サポート問い合わせ

- 公式 GitHub Issues：[anthropics/claude-code](https://github.com/anthropics/claude-code/issues)
- ヘルプセンター：[support.claude.com](https://support.claude.com/)
- ステータスページ：[status.claude.com](https://status.claude.com/)（API障害時）

---
