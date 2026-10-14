# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-14 17:35 JST（見た範囲: 2026-10-13 15:35 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-14 15:32 regenerate-page.yml failure（push・cloudflare） https://github.com/retroeater/mj/actions/runs/38522800783

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-14 09:25 check-image-links.yml success（workflow_dispatch・cloudflare）予定 2026-10-14 04:30 → 開始 2026-10-14 09:25（＋4:55） https://github.com/retroeater/mj/actions/runs/38490119497

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-13 16:27 regenerate-page.yml success（push・cloudflare）所要 2分50秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38362749491
- 2026-10-14 04:37 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 3分38秒（直近20回の中央値 0分37秒） https://github.com/retroeater/mj/actions/runs/38456349472
- 2026-10-14 12:45 regenerate-page.yml success（push・cloudflare）所要 3分07秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38507848314
- 2026-10-14 13:38 regenerate-page.yml success（push・cloudflare）所要 2分54秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38512423600
- 2026-10-14 15:32 regenerate-page.yml failure（push・cloudflare）所要 3分14秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38522800783
- 2026-10-14 16:50 regenerate-page.yml success（push・cloudflare）所要 3分22秒（直近20回の中央値 0分51秒） https://github.com/retroeater/mj/actions/runs/38531008713

## 4. 初回成功

- 2026-10-14 11:08 assets-check.yml success（push・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38499469812

## 5. 未報告のログ（`## 報告` の節が無い）

なし
