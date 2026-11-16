# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-16 11:17 JST（見た範囲: 2026-11-15 09:17 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-15 09:51 regenerate-page.yml failure（workflow_dispatch・work/1115-lgv-samestore2）ジョブ「regenerate」 https://github.com/retroeater/mj/actions/runs/43660275730
- 2026-11-15 13:31 update-live-channel.yml failure（workflow_dispatch・work/1115-acf）ジョブ「update」 https://github.com/retroeater/mj/actions/runs/43677131896
- 2026-11-16 10:42 fetch-youtube.yml failure（workflow_dispatch・cloudflare）ジョブ「regenerate / regenerate」：生成に失敗したページを飛ばしました: jpml_pros, title_pages（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/43787848850

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

- 2026-11-16 10:08 ask-weekly.yml success（workflow_dispatch・cloudflare）それ以前の cloudflare の実行 0 件に success なし https://github.com/retroeater/mj/actions/runs/43784440230

## 5. 未報告のログ（`## 報告` の節が無い）

なし
