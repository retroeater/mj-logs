# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-09 11:59 JST（見た範囲: 2026-11-08 09:59 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-09 11:53 regenerate-page.yml in_progress（workflow_dispatch・work/1109-pyv） https://github.com/retroeater/mj/actions/runs/42553368530
- 2026-11-09 11:53 check-image-links.yml in_progress（workflow_dispatch・work/1109-pyv） https://github.com/retroeater/mj/actions/runs/42553378748
- 2026-11-09 11:53 update-sns-book.yml failure（workflow_dispatch・work/1109-pyv）ジョブ「update」 https://github.com/retroeater/mj/actions/runs/42553404832

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1109-AIC-41.md（着手 2026-11-09（JST）） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-AIC-41.md
- CHAT-1109-PYV-01.md（着手 2026-11-09） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-PYV-01.md
