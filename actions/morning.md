# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-22 15:44 JST（見た範囲: 2026-10-21 13:44 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-22 04:00 run-daily.yml failure（workflow_dispatch・cloudflare）ジョブ「dojo / sync」 https://github.com/retroeater/mj/actions/runs/39657805637
- 2026-10-22 09:38 sync-dojo-calendar.yml failure（schedule・cloudflare）ジョブ「sync」 https://github.com/retroeater/mj/actions/runs/39700900041

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（直近20回の中央値の3倍以上、または10分以上。skipped は除く）

なし

## 4. 初回成功

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし
