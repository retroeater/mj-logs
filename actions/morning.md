# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-21 09:35 JST（見た範囲: 2026-10-20 07:35 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-21 09:14 convert-x-posts.yml in_progress（workflow_dispatch・work/1018-log-367-migrate） https://github.com/retroeater/mj/actions/runs/39520139651

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

なし

## 4. 初回成功

- 2026-10-21 09:32 check-sheets-reader.yml success（workflow_dispatch・cloudflare）それ以前の 0 件に success なし https://github.com/retroeater/mj/actions/runs/39521938139

## 5. 未報告のログ（`## 報告` の節が無い）

なし
