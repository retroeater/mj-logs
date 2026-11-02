# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-02 10:00 JST（見た範囲: 2026-11-01 08:00 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-01 08:24 run-daily.yml failure（workflow_dispatch・work/1101-wkp）ジョブ「sns / update」 https://github.com/retroeater/mj/actions/runs/41252379802
- 2026-11-02 08:15 regenerate-page.yml failure（workflow_dispatch・work/1017-lvp）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41361234293
- 2026-11-02 09:40 regenerate-page.yml in_progress（push・cloudflare） https://github.com/retroeater/mj/actions/runs/41368410875
- 2026-11-02 09:58 regenerate-page.yml in_progress（workflow_dispatch・work/1017-lvp） https://github.com/retroeater/mj/actions/runs/41369997845
- 2026-11-02 09:59 check-assets.yml in_progress（push・work/1102-wkp） https://github.com/retroeater/mj/actions/runs/41370111591

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 未実行: run-weekly.yml 予定 2026-11-02 03:00 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/run-weekly.yml

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-01 11:48 regenerate-page.yml success（push・cloudflare）所要 14分27秒（直近20回の中央値 9分35秒） https://github.com/retroeater/mj/actions/runs/41266703100
- 2026-11-02 09:29 regenerate-page.yml success（push・cloudflare）所要 16分42秒（直近20回の中央値 9分35秒） https://github.com/retroeater/mj/actions/runs/41367390119

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1102-AIC-11.md（着手 2026-11-02（JST）） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-AIC-11.md
- CHAT-1102-NOD-02.md（着手 2026-11-02） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-NOD-02.md
