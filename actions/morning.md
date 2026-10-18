# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-18 18:41 JST（見た範囲: 2026-10-17 16:41 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-18 02:57 check-assets.yml failure（push・work/OUK） https://github.com/retroeater/mj/actions/runs/39055991711
- 2026-10-18 03:35 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39058733366
- 2026-10-18 03:38 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39059005500
- 2026-10-18 03:45 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39059506583
- 2026-10-18 17:09 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39110332920
- 2026-10-18 17:18 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39110962540
- 2026-10-18 17:24 check-assets.yml failure（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39111333144
- 2026-10-18 18:34 check-assets.yml failure（push・work/WRC）ジョブ「check」 https://github.com/retroeater/mj/actions/runs/39115837863
- 2026-10-18 18:34 check-assets.yml failure（push・work/WRC）ジョブ「check」 https://github.com/retroeater/mj/actions/runs/39115852769
- 2026-10-18 18:41 check-assets.yml in_progress（push・work/WRC） https://github.com/retroeater/mj/actions/runs/39116299694

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

なし

## 4. 初回成功

- 2026-10-18 13:54 security-check.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/39098697779

## 5. 未報告のログ（`## 報告` の節が無い）

なし
