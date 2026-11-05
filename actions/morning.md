# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-05 11:07 JST（見た範囲: 2026-11-04 09:07 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-04 12:33 convert-x-posts.yml failure（workflow_dispatch・cloudflare）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/41767827901
- 2026-11-04 12:43 convert-x-posts.yml failure（workflow_dispatch・cloudflare）ジョブ「convert」 https://github.com/retroeater/mj/actions/runs/41768833961
- 2026-11-04 14:19 regenerate-page.yml failure（workflow_dispatch・work/1104-lgn-gnotice）ジョブ「regenerate」：生成に失敗したページを飛ばしました: exam_episodes（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/41778530568
- 2026-11-05 00:13 regenerate-page.yml failure（workflow_dispatch・work/1104-skn）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41855451367

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 未実行: run-weekly.yml 予定 2026-11-02 03:00 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/run-weekly.yml

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-04 09:13 regenerate-page.yml success（push・cloudflare）所要 12分39秒（直近20回の中央値 2分43秒） https://github.com/retroeater/mj/actions/runs/41745261928
- 2026-11-04 11:49 regenerate-page.yml success（push・cloudflare）所要 22分07秒（直近20回の中央値 2分43秒） https://github.com/retroeater/mj/actions/runs/41763190152
- 2026-11-04 12:19 update-sns-book.yml success（workflow_dispatch・cloudflare）所要 17分59秒（直近20回の中央値 0分48秒） https://github.com/retroeater/mj/actions/runs/41766370389
- 2026-11-04 12:33 convert-x-posts.yml failure（workflow_dispatch・cloudflare）所要 7分30秒（直近20回の中央値 0分40秒） https://github.com/retroeater/mj/actions/runs/41767827901
- 2026-11-04 12:43 convert-x-posts.yml failure（workflow_dispatch・cloudflare）所要 5分46秒（直近20回の中央値 0分40秒） https://github.com/retroeater/mj/actions/runs/41768833961
- 2026-11-04 12:50 regenerate-page.yml success（push・cloudflare）所要 19分54秒（直近20回の中央値 2分43秒） https://github.com/retroeater/mj/actions/runs/41769583706
- 2026-11-04 13:01 regenerate-page.yml success（push・cloudflare）所要 10分44秒（直近20回の中央値 2分43秒） https://github.com/retroeater/mj/actions/runs/41770619431

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1105-AIC-38.md（着手 2026-11-05（JST）） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-AIC-38.md
