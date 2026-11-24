# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-24 18:02 JST（見た範囲: 2026-11-23 16:02 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-24 17:58 regenerate-page.yml failure（push・cloudflare）ジョブ「regenerate」：生成に失敗したページを飛ばしました: mtsuku, mtsuku_episodes, wayhome_episodes（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/45360788510

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし

## 6. Actions の使用量（mj。ジョブごとに1分へ切り上げた見積もり、日付は JST。前日が240分以上か、月の累計が含まれる3,000分の50%・75%を超えたとき）

- 前日（11/23）: 124分
- 11月の累計（11/1〜11/23）: 5,218分（含まれる 3,000分の 173%。75% を超えた）

## 7. 国家サイバー統括室の投稿（@cyber_forecast、直近36時間。取得は run-daily.yml のジョブ xcyber。「なし」は投稿なし、「取得できず」は取れなかった日）

なし
