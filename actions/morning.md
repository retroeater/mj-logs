# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-23 19:09 JST（見た範囲: 2026-10-22 17:09 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-22 20:31 regenerate-page.yml failure（workflow_dispatch・work/1022-hth）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/39771190352
- 2026-10-23 09:16 regenerate-page.yml failure（workflow_dispatch・work/1023-oha）ジョブ「regenerate」：生成に失敗したページを飛ばしました: resource_efficiency, resource_logs（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/39877120835
- 2026-10-23 11:55 regenerate-page.yml failure（workflow_dispatch・work/1022-hth）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/39892214978
- 2026-10-23 17:04 regenerate-page.yml failure（workflow_dispatch・work/1022-hth）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/39922029210
- 2026-10-23 19:06 check-image-links.yml in_progress（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/39936774087

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。直近20回の中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-23 09:38 regenerate-page.yml success（push・cloudflare）所要 9分31秒（直近20回の中央値 0分45秒） https://github.com/retroeater/mj/actions/runs/39879277297
- 2026-10-23 18:54 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 10分48秒（直近20回の中央値 0分55秒） https://github.com/retroeater/mj/actions/runs/39935314394

## 4. 初回成功

- 2026-10-23 18:53 check-build-failure.yml success（check_run・cloudflare）それ以前の 95 件に success なし https://github.com/retroeater/mj/actions/runs/39935094873

## 5. 未報告のログ（`## 報告` の節が無い）

なし
