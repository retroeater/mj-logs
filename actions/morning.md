# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-31 10:17 JST（見た範囲: 2026-10-30 08:17 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

なし

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 遅れて実行: 2026-10-31 09:12 fetch-gsc.yml success（schedule・cloudflare）予定 2026-10-31 06:00 → 開始 2026-10-31 09:12（＋3:12） https://github.com/retroeater/mj/actions/runs/41148907501
- 遅れて実行: 2026-10-31 09:56 fetch-redirect-counts.yml success（schedule・cloudflare）予定 2026-10-31 06:30 → 開始 2026-10-31 09:56（＋3:26） https://github.com/retroeater/mj/actions/runs/41152866065

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-30 21:31 regenerate-page.yml success（push・cloudflare）所要 9分37秒（直近20回の中央値 1分58秒） https://github.com/retroeater/mj/actions/runs/41055954054

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1031-PRO-04.md（着手 2026-10-31） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-04.md
