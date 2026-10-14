# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-14 11:57 JST（見た範囲: 2026-10-13 09:57 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-13 11:12 update-sns-book.yml failure（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/38335781712
- 2026-10-13 23:38 notify-build-failure.yml failure（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/38415113000

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-13 11:12 update-sns-book.yml failure（workflow_dispatch・cloudflare）所要 3分40秒（直近20回の中央値 0分37秒） https://github.com/retroeater/mj/actions/runs/38335781712
- 2026-10-13 11:30 regenerate-page.yml success（push・cloudflare）所要 3分58秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38337166122
- 2026-10-13 12:41 regenerate-page.yml success（push・cloudflare）所要 3分29秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38342868015
- 2026-10-13 13:57 regenerate-page.yml success（push・cloudflare）所要 2分52秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38348958077
- 2026-10-13 16:27 regenerate-page.yml success（push・cloudflare）所要 2分50秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38362749491
- 2026-10-14 04:37 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 3分38秒（直近20回の中央値 0分37秒） https://github.com/retroeater/mj/actions/runs/38456349472

## 4. 初回成功

- 2026-10-13 11:15 assets-check.yml success（push・work/1012-dsn）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38335986035
- 2026-10-13 23:38 notify-build-failure.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38415024219

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1014-ACF-20.md（着手 2026-10-14） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-20.md
