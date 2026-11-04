# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-04 11:51 JST（見た範囲: 2026-11-03 09:51 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-04 00:58 regenerate-page.yml failure（workflow_dispatch・work/1104-ttl）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41669293763
- 2026-11-04 02:20 fetch-youtube.yml failure（workflow_dispatch・cloudflare）ジョブ「regenerate / regenerate」：生成に失敗したページを飛ばしました: houou_pages（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/41683310617
- 2026-11-04 02:28 regenerate-page.yml failure（workflow_dispatch・work/1029-lgn-page）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41684544766
- 2026-11-04 02:43 regenerate-page.yml failure（workflow_dispatch・work/1103-ryo8）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41686981568
- 2026-11-04 02:56 regenerate-page.yml failure（push・cloudflare）ジョブ「regenerate」：生成に失敗したページを飛ばしました: pro_pages（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/41689008141
- 2026-11-04 03:26 regenerate-page.yml failure（push・cloudflare）ジョブ「regenerate」：生成に失敗したページを飛ばしました: jpml_pros, saikyo_pages（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/41693768390
- 2026-11-04 03:48 regenerate-page.yml cancelled（workflow_dispatch・work/1104-top）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41697295116
- 2026-11-04 07:58 regenerate-page.yml failure（workflow_dispatch・work/1104-lgn-release）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/41735344487
- 2026-11-04 11:36 regenerate-page.yml in_progress（workflow_dispatch・work/1104-acf-count） https://github.com/retroeater/mj/actions/runs/41761816704
- 2026-11-04 11:36 regenerate-page.yml in_progress（workflow_dispatch・work/1104-top2） https://github.com/retroeater/mj/actions/runs/41761870869
- 2026-11-04 11:48 run-daily.yml in_progress（workflow_dispatch・work/1104-rsc） https://github.com/retroeater/mj/actions/runs/41763112104
- 2026-11-04 11:49 regenerate-page.yml in_progress（push・cloudflare） https://github.com/retroeater/mj/actions/runs/41763190152

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

- 未実行: run-weekly.yml 予定 2026-11-02 03:00 の後に schedule・workflow_dispatch（cloudflare）の実行なし https://github.com/retroeater/mj/actions/workflows/run-weekly.yml

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-11-04 00:55 regenerate-page.yml success（workflow_dispatch・cloudflare）所要 23分45秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41668885312
- 2026-11-04 02:20 fetch-youtube.yml failure（workflow_dispatch・cloudflare）所要 23分52秒（直近20回の中央値 9分34秒） https://github.com/retroeater/mj/actions/runs/41683310617
- 2026-11-04 02:56 regenerate-page.yml failure（push・cloudflare）所要 10分47秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41689008141
- 2026-11-04 03:19 research-candidates.yml success（workflow_dispatch・cloudflare）所要 13分14秒（直近20回の中央値 1分38秒） https://github.com/retroeater/mj/actions/runs/41692708935
- 2026-11-04 03:26 regenerate-page.yml failure（push・cloudflare）所要 32分15秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41693768390
- 2026-11-04 03:35 research-candidates.yml success（workflow_dispatch・cloudflare）所要 11分25秒（直近20回の中央値 1分38秒） https://github.com/retroeater/mj/actions/runs/41695240844
- 2026-11-04 04:20 regenerate-page.yml success（push・cloudflare）所要 16分00秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41702345684
- 2026-11-04 07:46 regenerate-page.yml success（push・cloudflare）所要 10分44秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41733700777
- 2026-11-04 08:25 regenerate-page.yml success（push・cloudflare）所要 11分58秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41738984175
- 2026-11-04 08:26 regenerate-page.yml success（push・cloudflare）所要 13分02秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41739199804
- 2026-11-04 08:36 research-candidates.yml success（workflow_dispatch・cloudflare）所要 19分20秒（直近20回の中央値 1分38秒） https://github.com/retroeater/mj/actions/runs/41740463715
- 2026-11-04 09:13 regenerate-page.yml success（push・cloudflare）所要 12分39秒（直近20回の中央値 10分44秒） https://github.com/retroeater/mj/actions/runs/41745261928

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

- 2026-11-03 16:51 regenerate-page.yml success（push・cloudflare）それ以前の cloudflare の実行 0 件に success なし https://github.com/retroeater/mj/actions/runs/41599057709

## 5. 未報告のログ（`## 報告` の節が無い）

- CHAT-1104-TOP-16.md（着手 2026-11-04） https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-16.md
