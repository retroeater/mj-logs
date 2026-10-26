# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-10-26 10:06 JST（見た範囲: 2026-10-25 08:06 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-10-25 09:02 regenerate-page.yml failure（workflow_dispatch・work/1024-vod）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/40145130087

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-25 19:40 regenerate-page.yml success（push・cloudflare）所要 11分11秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185765407
- 2026-10-25 19:42 regenerate-page.yml success（push・cloudflare）所要 18分05秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185846374
- 2026-10-26 02:00 run-weekly.yml success（workflow_dispatch・cloudflare）所要 12分21秒（直近20回の中央値 12分21秒） https://github.com/retroeater/mj/actions/runs/40214364620
- 2026-10-26 07:47 fetch-youtube.yml success（schedule・cloudflare）所要 10分06秒（直近20回の中央値 7分55秒） https://github.com/retroeater/mj/actions/runs/40241832250

## 4. 初回成功

なし

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1026-AIC-08.md（着手 2026-10-26（JST）） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-AIC-08.md
- CHAT-1026-MSR-02.md（着手 2026-10-26） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-MSR-02.md
