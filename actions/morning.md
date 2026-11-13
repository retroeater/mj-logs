# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-13 15:29 JST（見た範囲: 2026-11-12 13:29 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-12 15:36 fetch-gsc.yml failure（workflow_dispatch・cloudflare）ジョブ「fetch」 https://github.com/retroeater/mj/actions/runs/43169347821
- 2026-11-13 00:44 fetch-gsc.yml failure（workflow_dispatch・cloudflare）ジョブ「fetch」 https://github.com/retroeater/mj/actions/runs/43251047782
- 2026-11-13 09:24 fetch-gsc.yml failure（workflow_dispatch・cloudflare）ジョブ「fetch」 https://github.com/retroeater/mj/actions/runs/43336769308
- 2026-11-13 09:32 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43337927213
- 2026-11-13 09:38 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43338663748
- 2026-11-13 09:41 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43339090522
- 2026-11-13 09:43 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43339343365
- 2026-11-13 09:45 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43339658485
- 2026-11-13 09:48 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43339934427
- 2026-11-13 09:50 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43340237157
- 2026-11-13 09:53 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43340590405
- 2026-11-13 09:55 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43340896677
- 2026-11-13 09:57 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43341186772
- 2026-11-13 09:59 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43341470866
- 2026-11-13 10:01 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43341722497
- 2026-11-13 10:03 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43341961334
- 2026-11-13 10:05 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43342271395
- 2026-11-13 10:08 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43342636066
- 2026-11-13 10:11 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43342941429
- 2026-11-13 10:13 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43343248057
- 2026-11-13 10:15 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43343561351
- 2026-11-13 10:17 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43343853553
- 2026-11-13 10:20 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43344151292
- 2026-11-13 13:17 regenerate-page.yml failure（workflow_dispatch・work/1113-typ）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/43364600528

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-12 15:36 fetch-gsc.yml failure（workflow_dispatch・cloudflare）所要 174分34秒（直近20回の中央値 0分21秒） https://github.com/retroeater/mj/actions/runs/43169347821
- 2026-11-13 00:44 fetch-gsc.yml failure（workflow_dispatch・cloudflare）所要 37分15秒（直近20回の中央値 0分21秒） https://github.com/retroeater/mj/actions/runs/43251047782

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし
