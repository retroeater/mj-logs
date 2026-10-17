# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-17 12:36 JST（見た範囲: 2026-10-16 10:36 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-17 12:19 check-assets.yml failure（push・work/OUK） https://github.com/retroeater/mj/actions/runs/38998306192
- 2026-10-17 12:24 check-assets.yml failure（push・work/OUK） https://github.com/retroeater/mj/actions/runs/38998660380
- 2026-10-17 12:30 check-assets.yml failure（push・work/OUK）ジョブ「check」 https://github.com/retroeater/mj/actions/runs/38999071147
- 2026-10-17 12:35 regenerate-page.yml in_progress（push・cloudflare） https://github.com/retroeater/mj/actions/runs/38999347153

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-17 09:21 convert-x-posts.yml success（workflow_dispatch・cloudflare）所要 1分43秒（直近20回の中央値 0分34秒） https://github.com/retroeater/mj/actions/runs/38985966701
- 2026-10-17 12:01 convert-x-posts.yml success（workflow_dispatch・cloudflare）所要 28分46秒（直近20回の中央値 0分34秒） https://github.com/retroeater/mj/actions/runs/38997199612

## 4. 初回成功

- 2026-10-16 11:27 check-assets.yml success（push・work/1016-hou2）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38840542480
- 2026-10-16 11:40 fetch-youtube.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38841595184
- 2026-10-17 09:34 check-build-failure.yml success（check_run・cloudflare）それ以前の 73 件に success なし https://github.com/retroeater/mj/actions/runs/38987017435

## 5. 未報告のログ（`## 報告` の節が無い）

なし
