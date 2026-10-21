# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-21 12:20 JST（見た範囲: 2026-10-20 10:20 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-21 09:51 convert-x-posts.yml failure（workflow_dispatch・work/1018-log-367-migrate）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/39523831303
- 2026-10-21 09:56 check-build-failure.yml failure（check_run・cloudflare） https://github.com/retroeater/mj/actions/runs/39524306758
- 2026-10-21 12:14 run-daily.yml in_progress（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/39537463560

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-21 09:14 convert-x-posts.yml success（workflow_dispatch・work/1018-log-367-migrate）所要 35分56秒（直近20回の中央値 0分28秒） https://github.com/retroeater/mj/actions/runs/39520139651
- 2026-10-21 09:51 convert-x-posts.yml failure（workflow_dispatch・work/1018-log-367-migrate）所要 26分36秒（直近20回の中央値 0分28秒） https://github.com/retroeater/mj/actions/runs/39523831303
- 2026-10-21 10:18 convert-x-posts.yml success（workflow_dispatch・work/1018-log-367-migrate）所要 33分35秒（直近20回の中央値 0分28秒） https://github.com/retroeater/mj/actions/runs/39526586569
- 2026-10-21 10:53 convert-x-posts.yml success（workflow_dispatch・work/1018-log-367-migrate）所要 3分59秒（直近20回の中央値 0分28秒） https://github.com/retroeater/mj/actions/runs/39529855218
- 2026-10-21 11:34 regenerate-page.yml success（workflow_dispatch・work/1021-prv）所要 15分20秒（直近20回の中央値 2分16秒） https://github.com/retroeater/mj/actions/runs/39533792334
- 2026-10-21 12:04 regenerate-page.yml success（push・cloudflare）所要 8分56秒（直近20回の中央値 2分16秒） https://github.com/retroeater/mj/actions/runs/39536568633

## 4. 初回成功

- 2026-10-21 09:32 check-sheets-reader.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/39521938139
- 2026-10-21 11:37 run-weekly.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/39534074313
- 2026-10-21 11:37 run-monthly.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/39534076694

## 5. 未報告のログ（`## 報告` の節が無い）

なし
