# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-16 10:06 JST（見た範囲: 2026-10-15 08:06 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-15 18:23 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38712589327
- 2026-10-15 18:27 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38713117727
- 2026-10-15 18:29 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38713280073
- 2026-10-15 18:50 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38715940246
- 2026-10-15 21:14 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38733088201
- 2026-10-15 21:29 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38735048354
- 2026-10-16 08:25 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38823450448
- 2026-10-16 10:02 run-daily.yml in_progress（workflow_dispatch・work/1016-wfl-s8） https://github.com/retroeater/mj/actions/runs/38832956460
- 2026-10-16 10:02 update-live-channel.yml pending（workflow_dispatch・work/1016-wfl-s8） https://github.com/retroeater/mj/actions/runs/38832959472

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-15 14:58 regenerate-page.yml success（push・cloudflare）所要 3分39秒（直近20回の中央値 1分09秒） https://github.com/retroeater/mj/actions/runs/38689705396
- 2026-10-15 16:05 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 5分11秒（直近20回の中央値 0分40秒） https://github.com/retroeater/mj/actions/runs/38696400299
- 2026-10-15 21:27 regenerate-page.yml success（push・cloudflare）所要 4分25秒（直近20回の中央値 1分09秒） https://github.com/retroeater/mj/actions/runs/38734822366
- 2026-10-16 02:10 regenerate-page.yml success（push・cloudflare）所要 3分33秒（直近20回の中央値 1分09秒） https://github.com/retroeater/mj/actions/runs/38775180393
- 2026-10-16 09:49 regenerate-page.yml success（push・cloudflare）所要 4分55秒（直近20回の中央値 1分09秒） https://github.com/retroeater/mj/actions/runs/38831721544

## 4. 初回成功

- 2026-10-15 12:18 assets-check.yml success（push・work/1015-wfl-s6）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38675612084
- 2026-10-15 12:26 fetch-x-posts.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38676252888
- 2026-10-15 12:54 run-daily.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38678691802
- 2026-10-15 21:21 convert-x-posts.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38734003614
- 2026-10-16 10:02 check-assets.yml success（push・work/1016-wfl-s8）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38832950597

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1016-SKR-06.md（着手 2026-10-16） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-06.md
- CHAT-1016-WFL-20.md（着手 2026-10-16） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-WFL-20.md
