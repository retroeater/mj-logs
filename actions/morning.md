# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-17 09:30 JST（見た範囲: 2026-10-16 07:30 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-16 08:25 assets-check.yml failure（push・work/JBK） https://github.com/retroeater/mj/actions/runs/38823450448

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れの可能性: cleanup-branches.yml 予定 2026-10-17 07:53 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/cleanup-branches.yml
- 遅れて実行: 2026-10-16 11:40 fetch-youtube.yml success（workflow_dispatch・cloudflare）予定 2026-10-12 05:37 → 開始 2026-10-16 11:40（＋102:03） https://github.com/retroeater/mj/actions/runs/38841595184

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

- 2026-10-16 09:49 regenerate-page.yml success（push・cloudflare）所要 4分55秒（直近20回の中央値 1分31秒） https://github.com/retroeater/mj/actions/runs/38831721544
- 2026-10-16 12:02 regenerate-page.yml success（push・cloudflare）所要 4分54秒（直近20回の中央値 1分31秒） https://github.com/retroeater/mj/actions/runs/38843505197

## 4. 初回成功

- 2026-10-16 10:02 check-assets.yml success（push・work/1016-wfl-s8）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38832950597
- 2026-10-16 10:18 cleanup-branches.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38834411797
- 2026-10-16 10:19 regenerate-sitemap.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38834457795
- 2026-10-16 11:40 fetch-youtube.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/38841595184

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1017-AMP-04.md（着手 2026-10-17） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-04.md
