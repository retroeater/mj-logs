# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-23 20:24 JST（見た範囲: 2026-11-22 18:24 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-23 20:21 regenerate-page.yml in_progress（push・cloudflare） https://github.com/retroeater/mj/actions/runs/45164449850
- 2026-11-23 20:21 regenerate-page.yml pending（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/45164464560

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし

## 6. Actions の使用量（mj。ジョブごとに1分へ切り上げた見積もり、日付は JST。前日が240分以上か、月の累計が含まれる3,000分の50%・75%を超えたとき）

- 前日（11/22）: 37分
- 11月の累計（11/1〜11/22）: 5,094分（含まれる 3,000分の 169%。75% を超えた）

## 7. 国家サイバー統括室の投稿（@cyber_forecast、直近36時間。取得は run-daily.yml のジョブ xcyber。「なし」は投稿なし、「取得できず」は取れなかった日）

なし
