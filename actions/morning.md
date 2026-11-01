# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-01 09:28 JST（見た範囲: 2026-10-31 07:28 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-01 03:00 run-monthly.yml failure（workflow_dispatch・cloudflare）ジョブ「redirects / fetch」 https://github.com/retroeater/mj/actions/runs/41227250544
- 2026-11-01 04:00 run-daily.yml failure（workflow_dispatch・cloudflare）ジョブ「sns / update」 https://github.com/retroeater/mj/actions/runs/41232112701
- 2026-11-01 08:24 run-daily.yml failure（workflow_dispatch・work/1101-wkp）ジョブ「sns / update」 https://github.com/retroeater/mj/actions/runs/41252379802
- 2026-11-01 09:27 check-assets.yml in_progress（push・work/1031-pro-dojo） https://github.com/retroeater/mj/actions/runs/41256853435

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-31 10:18 regenerate-page.yml success（push・cloudflare）所要 11分12秒（直近20回の中央値 4分59秒） https://github.com/retroeater/mj/actions/runs/41154722499
- 2026-10-31 10:19 regenerate-page.yml success（push・cloudflare）所要 25分12秒（直近20回の中央値 4分59秒） https://github.com/retroeater/mj/actions/runs/41154797689

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

- 2026-10-31 10:18 update-graph success（dynamic・cloudflare）それ以前の cloudflare の実行 0 件に success なし https://github.com/retroeater/mj/actions/runs/41154727561
- 2026-10-31 10:19 research-candidates.yml success（workflow_dispatch・cloudflare）それ以前の cloudflare の実行 0 件に success なし https://github.com/retroeater/mj/actions/runs/41154805538
- 2026-10-31 23:27 check-build-failure.yml success（check_run・cloudflare）それ以前の cloudflare の実行 80 件に success なし https://github.com/retroeater/mj/actions/runs/41210216337

## 5. 未報告のログ（`## 報告` の節が無い）

なし
