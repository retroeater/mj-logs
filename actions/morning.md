# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-29 16:00 JST（見た範囲: 2026-10-28 14:00 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-29 10:07 run-monthly.yml cancelled（workflow_dispatch・work/1028-lgn）ジョブ「logplaces / convert」 https://github.com/retroeater/mj/actions/runs/40798423892
- 2026-10-29 14:53 convert-x-posts.yml failure（workflow_dispatch・work/1029-lgn-eki）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/40826011390

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-29 09:28 fetch-gsc.yml success（schedule・cloudflare）予定 2026-10-29 06:00 → 開始 2026-10-29 09:28（＋3:28） https://github.com/retroeater/mj/actions/runs/40794333811
- 遅れて実行: 2026-10-29 10:08 fetch-redirect-counts.yml success（schedule・cloudflare）予定 2026-10-29 06:30 → 開始 2026-10-29 10:08（＋3:38） https://github.com/retroeater/mj/actions/runs/40798548431

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし
