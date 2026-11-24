# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-25 04:03 JST（見た範囲: 2026-11-24 02:03 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-24 17:58 regenerate-page.yml failure（push・cloudflare）ジョブ「regenerate」：生成に失敗したページを飛ばしました: mtsuku, mtsuku_episodes, wayhome_episodes（理由はジョブのサマリ、#533） https://github.com/retroeater/mj/actions/runs/45360788510
- 2026-11-25 04:00 run-daily.yml in_progress（workflow_dispatch・cloudflare） https://github.com/retroeater/mj/actions/runs/45465574358

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし

## 6. Actions の使用量（mj。ジョブごとに1分へ切り上げた見積もり、日付は JST。前日が240分以上か、月の累計が含まれる3,000分の50%・75%を超えたとき）

- 前日（11/24）: 57分
- 11月の累計（11/1〜11/24）: 5,275分（含まれる 3,000分の 175%。75% を超えた）

## 7. 国家サイバー統括室の投稿（@cyber_forecast、直近36時間。取得は run-daily.yml のジョブ xcyber。「なし」は投稿なし、「取得できず」は取れなかった日）

- 2026-11-24 11:28 JST https://x.com/cyber_forecast/status/2125053912822731247
  > 【更新情報】
  > 米国時間2026年11月23日、Microsoft Edgeの更新プログラムが公開されました。
  > 本件更新（158.0.4370.33）にはセキュリティ修正が含まれています。更新が配信されましたら忘れずにアップデートしてください。
  > https://t.co/rnSFlgax8X
  - リンク: https://learn.microsoft.com/en-us/deployedge/microsoft-edge-relnotes-security
- 2026-11-24 11:28 JST https://x.com/cyber_forecast/status/2125053958259609710
  > 【更新情報】
  > 米国時間2026年11月23日、デスクトップ向けChromeの更新情報が公開されました。
  > 本件更新(Windows/Mac:158.0.8127.88/.89、Linux:158.0.8127.88)にはセキュリティ修正が含まれています。配信されましたら忘れずにアップデートしてください。
  > https://t.co/0FVs8bJjhN
  - リンク: https://chromereleases.googleblog.com/2026/11/stable-channel-update-for-desktop_01734282054.html
- 2026-11-24 11:28 JST https://x.com/cyber_forecast/status/2125053999061856559
  > 【更新情報】
  > 米国時間2026年11月23日、Android向けChromeの更新プログラムが公開されました。
  > 本件更新(158.0.8127.92)には、セキュリティ修正が含まれています。更新が配信されましたら忘れずにアップデートしてください。
  > https://t.co/48BlcxCdLy
  - リンク: https://chromereleases.googleblog.com/2026/11/chrome-for-android-update_0411169060.html
- 2026-11-24 13:53 JST（リポスト） https://x.com/cyber_forecast/status/2125090495441567857
  > RT @caa_shohishacho: 昨今、事業者等への不正アクセスによる個人情報の漏えい等が発生しています。流出した個人情報の悪用や被害を防ぐため、６つの対策を御確認ください！
  > https://t.co/5ybRVobI00 https://t.co/cmbCysnH4K
  - リンク: https://www.caa.go.jp/policies/policy/consumer_policy/caution/caution_039/
  - リンク: https://x.com/caa_shohishacho/status/2123627831544664412/photo/1
