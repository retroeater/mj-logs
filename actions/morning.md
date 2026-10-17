# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-17 12:23 JST（見た範囲: 2026-10-16 10:23 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-17 12:01 convert-x-posts.yml in_progress（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/38997199612
- 2026-10-17 12:19 check-assets.yml failure（push・work/OUK）ジョブ「check」 https://github.com/retroeater/mj/actions/runs/38998306192
- 2026-10-17 12:23 check-assets.yml in_progress（push・work/1017-ogr） https://github.com/retroeater/mj/actions/runs/38998572272

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-16 11:40 fetch-youtube.yml success（workflow_dispatch・cloudflare）予定 2026-10-12 05:37 → 開始 2026-10-16 11:40（＋102:03） https://github.com/retroeater/mj/actions/runs/38841595184

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-17 09:21 convert-x-posts.yml success（workflow_dispatch・cloudflare）所要 1分43秒（直近20回の中央値 0分32秒） https://github.com/retroeater/mj/actions/runs/38985966701

## 4. 初回成功

- 2026-10-16 10:45 check-assets.yml success（push・work/1016-wfl-glossary）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38836805510
- 2026-10-16 11:40 fetch-youtube.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38841595184
- 2026-10-17 09:34 check-build-failure.yml success（check_run・cloudflare）それ以前の 77 件に success なし https://github.com/retroeater/mj/actions/runs/38987017435

## 5. 未報告のログ（`## 報告` の節が無い）

なし
