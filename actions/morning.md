# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-23 09:23 JST（見た範囲: 2026-10-22 07:23 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-22 09:38 sync-dojo-calendar.yml failure（schedule・cloudflare）ジョブ「sync」 https://github.com/retroeater/mj/actions/runs/39700900041
- 2026-10-22 15:53 regenerate-page.yml failure（workflow_dispatch・work/1022-hth） https://github.com/retroeater/mj/actions/runs/39736761040
- 2026-10-22 16:31 regenerate-page.yml failure（workflow_dispatch・work/1022-hth） https://github.com/retroeater/mj/actions/runs/39741104321
- 2026-10-22 20:31 regenerate-page.yml failure（workflow_dispatch・work/1022-hth） https://github.com/retroeater/mj/actions/runs/39771190352
- 2026-10-23 09:16 regenerate-page.yml failure（workflow_dispatch・work/1023-oha）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/39877120835
- 2026-10-23 09:16 regenerate-page.yml in_progress（workflow_dispatch・work/1023-srg） https://github.com/retroeater/mj/actions/runs/39877148386

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。直近20回の中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし
