# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-14 07:53 JST（見た範囲: 2026-11-13 05:53 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

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
- 2026-11-13 15:33 update-sns-book.yml failure（workflow_dispatch・cloudflare）ジョブ「update」 https://github.com/retroeater/mj/actions/runs/43379515995
- 2026-11-13 15:58 oneoff-news-read.yml failure（workflow_dispatch・cloudflare）ジョブ「read」 https://github.com/retroeater/mj/actions/runs/43382390682
- 2026-11-13 16:02 oneoff-news-read.yml failure（workflow_dispatch・work/1113-nws）ジョブ「read」 https://github.com/retroeater/mj/actions/runs/43382823289
- 2026-11-13 16:41 oneoff-news-read.yml cancelled（workflow_dispatch・work/1113-nws）ジョブ「read」 https://github.com/retroeater/mj/actions/runs/43387550650
- 2026-11-13 17:30 research-candidates.yml failure（workflow_dispatch・work/1113-rsc）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43393805625
- 2026-11-13 20:41 research-candidates.yml failure（workflow_dispatch・work/1113-rsc）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43419074229
- 2026-11-13 20:42 research-candidates.yml failure（workflow_dispatch・work/1113-rsc）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43419175939
- 2026-11-13 21:17 research-candidates.yml failure（workflow_dispatch・cloudflare）ジョブ「research」 https://github.com/retroeater/mj/actions/runs/43423864654
- 2026-11-13 21:54 oneoff-news-read.yml cancelled（workflow_dispatch・work/1113-nws-league）ジョブ「read」 https://github.com/retroeater/mj/actions/runs/43429134232
- 2026-11-14 07:51 oneoff-news-read.yml in_progress（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/43522702849

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-13 17:24 oneoff-news-read.yml success（workflow_dispatch・cloudflare）所要 16分36秒（直近20回の中央値 7分10秒） https://github.com/retroeater/mj/actions/runs/43392968392
- 2026-11-13 20:06 fetch-gsc.yml success（workflow_dispatch・cloudflare）所要 43分43秒（直近20回の中央値 1分09秒） https://github.com/retroeater/mj/actions/runs/43414332570

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

- 2026-11-13 17:03 oneoff-news-read.yml success（workflow_dispatch・cloudflare）それ以前の cloudflare の実行 1 件に success なし https://github.com/retroeater/mj/actions/runs/43390206093

## 5. 未報告のログ（`## 報告` の節が無い）

なし
