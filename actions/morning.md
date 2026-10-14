# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-14 12:23 JST（見た範囲: 2026-10-13 10:23 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-13 11:12 update-sns-book.yml failure（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/38335781712
- 2026-10-13 23:38 notify-build-failure.yml failure（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/38415113000

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-14 09:25 check-image-links.yml success（workflow_dispatch・cloudflare）予定 2026-10-14 04:30 → 開始 2026-10-14 09:25（＋4:55） https://github.com/retroeater/mj/actions/runs/38490119497

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-13 11:12 update-sns-book.yml failure（workflow_dispatch・cloudflare）所要 3分40秒（直近20回の中央値 0分37秒） https://github.com/retroeater/mj/actions/runs/38335781712
- 2026-10-13 11:30 regenerate-page.yml success（push・cloudflare）所要 3分58秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38337166122
- 2026-10-13 12:41 regenerate-page.yml success（push・cloudflare）所要 3分29秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38342868015
- 2026-10-13 13:57 regenerate-page.yml success（push・cloudflare）所要 2分52秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38348958077
- 2026-10-13 16:27 regenerate-page.yml success（push・cloudflare）所要 2分50秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38362749491
- 2026-10-14 04:37 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 3分38秒（直近20回の中央値 0分37秒） https://github.com/retroeater/mj/actions/runs/38456349472

## 4. 初回成功

- 2026-10-13 11:43 assets-check.yml success（push・work/1013-whs-nav）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38338234140
- 2026-10-13 23:38 notify-build-failure.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38415024219

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1014-ACF-21.md（着手 2026-10-14） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-21.md
