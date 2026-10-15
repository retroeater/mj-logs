# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-15 15:43 JST（見た範囲: 2026-10-14 13:43 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-14 15:32 regenerate-page.yml failure（push・cloudflare） https://github.com/retroeater/mj/actions/runs/38522800783
- 2026-10-14 17:52 assets-check.yml failure（push・work/MTK） https://github.com/retroeater/mj/actions/runs/38538215818
- 2026-10-14 17:54 assets-check.yml failure（push・work/MTK） https://github.com/retroeater/mj/actions/runs/38538480828
- 2026-10-15 02:56 fetch-redirect-counts.yml failure（workflow_dispatch・cloudflare）ジョブ「fetch」 https://github.com/retroeater/mj/actions/runs/38610972729
- 2026-10-15 02:58 fetch-redirect-counts.yml failure（workflow_dispatch・cloudflare）ジョブ「fetch」 https://github.com/retroeater/mj/actions/runs/38611141347
- 2026-10-15 04:10 update-sns-book.yml failure（workflow_dispatch・cloudflare）ジョブ「regenerate / regenerate」 https://github.com/retroeater/mj/actions/runs/38621492985

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-15 02:56 fetch-redirect-counts.yml failure（workflow_dispatch・cloudflare）予定 2026-10-01 06:30 → 開始 2026-10-15 02:56（＋332:26） https://github.com/retroeater/mj/actions/runs/38610972729
- 遅れて実行: 2026-10-15 12:54 run-daily.yml success（workflow_dispatch・cloudflare）予定 2026-10-15 04:00 → 開始 2026-10-15 12:54（＋8:54） https://github.com/retroeater/mj/actions/runs/38678691802

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-14 15:32 regenerate-page.yml failure（push・cloudflare）所要 3分14秒（直近20回の中央値 0分58秒） https://github.com/retroeater/mj/actions/runs/38522800783
- 2026-10-14 16:50 regenerate-page.yml success（push・cloudflare）所要 3分22秒（直近20回の中央値 0分58秒） https://github.com/retroeater/mj/actions/runs/38531008713
- 2026-10-15 04:10 update-sns-book.yml failure（workflow_dispatch・cloudflare）所要 4分24秒（直近20回の中央値 0分38秒） https://github.com/retroeater/mj/actions/runs/38621492985
- 2026-10-15 12:21 regenerate-page.yml success（push・cloudflare）所要 3分19秒（直近20回の中央値 0分58秒） https://github.com/retroeater/mj/actions/runs/38675846949
- 2026-10-15 14:58 regenerate-page.yml success（push・cloudflare）所要 3分39秒（直近20回の中央値 0分58秒） https://github.com/retroeater/mj/actions/runs/38689705396

## 4. 初回成功

- 2026-10-14 15:39 assets-check.yml success（push・work/1014-skd）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38523497953
- 2026-10-15 02:58 fetch-redirect-counts.yml success（workflow_dispatch・cloudflare）それ以前の 2 件に success なし https://github.com/retroeater/mj/actions/runs/38611255688
- 2026-10-15 12:26 fetch-x-posts.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38676252888
- 2026-10-15 12:54 run-daily.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38678691802

## 5. 未報告のログ（`## 報告` の節が無い）

なし
