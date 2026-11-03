# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-04 03:03 JST（見た範囲: 2026-11-03 01:03 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-04 00:58 regenerate-page.yml failure（workflow_dispatch・work/1104-ttl）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41669293763
- 2026-11-04 01:11 check-build-failure.yml failure（check_run・cloudflare）ジョブ「notify」 https://github.com/retroeater/mj/actions/runs/41671687021
- 2026-11-04 01:11 check-build-failure.yml failure（check_run・cloudflare）ジョブ「notify」 https://github.com/retroeater/mj/actions/runs/41671698335
- 2026-11-04 01:37 check-build-failure.yml failure（check_run・cloudflare）ジョブ「notify」 https://github.com/retroeater/mj/actions/runs/41676151633
- 2026-11-04 02:20 fetch-youtube.yml failure（workflow_dispatch・cloudflare）ジョブ「regenerate / regenerate」：生成に失敗したページを飛ばしました: houou_pages（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/41683310617
- 2026-11-04 02:28 regenerate-page.yml failure（workflow_dispatch・work/1029-lgn-page）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41684544766
- 2026-11-04 02:43 regenerate-page.yml in_progress（workflow_dispatch・work/1103-ryo8） https://github.com/retroeater/mj/actions/runs/41686981568
- 2026-11-04 02:47 regenerate-page.yml in_progress（workflow_dispatch・work/1104-top） https://github.com/retroeater/mj/actions/runs/41687676575
- 2026-11-04 02:48 regenerate-page.yml in_progress（workflow_dispatch・work/1029-lgn-page） https://github.com/retroeater/mj/actions/runs/41687839172
- 2026-11-04 02:56 regenerate-page.yml in_progress（push・cloudflare） https://github.com/retroeater/mj/actions/runs/41689008141
- 2026-11-04 03:02 check-assets.yml in_progress（push・work/1104-rsc） https://github.com/retroeater/mj/actions/runs/41689906059

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 未実行: run-weekly.yml 予定 2026-11-02 03:00 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/run-weekly.yml

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-04 00:55 regenerate-page.yml success（workflow_dispatch・cloudflare）所要 23分45秒（直近20回の中央値 9分34秒） https://github.com/retroeater/mj/actions/runs/41668885312
- 2026-11-04 02:20 fetch-youtube.yml failure（workflow_dispatch・cloudflare）所要 23分52秒（直近20回の中央値 9分34秒） https://github.com/retroeater/mj/actions/runs/41683310617

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

- 2026-11-03 23:50 check-build-failure.yml success（check_run・cloudflare）それ以前の cloudflare の実行 31 件に success なし https://github.com/retroeater/mj/actions/runs/41657643690

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1104-PIL-03.md（着手 2026-11-04） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PIL-03.md
- CHAT-1104-RYO-12.md（着手 2026-11-04） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-RYO-12.md
- CHAT-1104-TOP-13.md（着手 2026-11-04） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-13.md
