# DMウォッチシステム（dm-watch）

決まった時刻（日本時間 10 / 14 / 17 / 19時）に Instagram の新着DMを確認し、
対応が必要なものだけ返信案付きで LINE に通知する。Claude のクラウドルーティンで動くので
PC・スマホは閉じていてよい。Instagram 側への外向きの操作は一切しない（読むだけ）。

**解説ページ： https://ryoya9595.github.io/dm-watch/**

| 読む順番 | ファイル | 誰が |
|---|---|---|
| 1 | `はじめにお読みください.md` | 人 |
| 2 | `事前準備ガイド.md` | 人（Instagram・Meta・LINE・Claude環境の設定） |
| 3 | `導入手順_ClaudeCodeに読ませる.md` | Claude Code |
| — | `導入をサポートする人へ.md` | 誰かに設置してあげる人（教える側） |
| — | `routine-prompt.md` | ルーティンに貼る |
| — | `scripts/*.py` | ルーティンが実行 |
| — | `dev/test_scripts.py` | 開発用の検証（納品物ではない。scripts を直したら実行する） |

構成：Claude ルーティン（cron `0 1,5,8,10 * * *` UTC）→ `fetch_dms.py`（Instagram Graph API）
→ Claude が判定 → `send_line.py`（LINE Messaging API push）。
トークンは Claude 環境の API credentials に預け、コードにも環境変数にも置かない。

通知済みIDは2段階（`pending` → `seen`）で記録し、LINE送信が成功したときだけ
`--commit-state` で確定させる。送信に失敗した回のDMは次回もう一度通知される。
