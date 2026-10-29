# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-30 07:59 JST（見た範囲: 2026-10-29 05:59 JST から）
- 書き出した実行の契機: schedule（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-29 10:07 run-monthly.yml cancelled（workflow_dispatch・work/1028-lgn）ジョブ「logplaces / convert」 https://github.com/retroeater/mj/actions/runs/40798423892
- 2026-10-29 14:53 convert-x-posts.yml failure（workflow_dispatch・work/1029-lgn-eki）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/40826011390
- 2026-10-29 17:08 convert-x-posts.yml failure（workflow_dispatch・work/1029-lgn-eki）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/40839961758

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れの可能性: fetch-gsc.yml 予定 2026-10-30 06:00 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/fetch-gsc.yml
- 遅れの可能性: fetch-redirect-counts.yml 予定 2026-10-30 06:30 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/fetch-redirect-counts.yml

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-29 21:00 regenerate-page.yml success（push・cloudflare）所要 11分11秒（直近20回の中央値 1分39秒） https://github.com/retroeater/mj/actions/runs/40869750475
- 2026-10-29 21:01 regenerate-page.yml success（push・cloudflare）所要 20分43秒（直近20回の中央値 1分39秒） https://github.com/retroeater/mj/actions/runs/40869892032
- 2026-10-29 21:47 regenerate-page.yml success（push・cloudflare）所要 9分00秒（直近20回の中央値 1分39秒） https://github.com/retroeater/mj/actions/runs/40876177948
- 2026-10-29 22:48 regenerate-page.yml success（push・cloudflare）所要 9分22秒（直近20回の中央値 1分39秒） https://github.com/retroeater/mj/actions/runs/40885395125

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし
