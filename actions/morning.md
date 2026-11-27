# 朝の報告用の異常（retroeater/mj）

- 書き出した時刻: 2026-11-27 16:49 JST（見た範囲: 2026-11-26 14:49 JST から）
- 書き出した実行の契機: workflow_dispatch（main）
- 異常だけを書く。全体は actions/status.md。判定は mj の scripts/morning_report.py（各ワークフローの直近 100 回まで）

## 1. 失敗など（結論が success・skipped 以外。実行中を含む）

- 2026-11-27 15:32 update-live-channel.yml failure（workflow_dispatch・cloudflare）ジョブ「update」 https://github.com/retroeater/mj/actions/runs/45952480725

## 2. 予約実行の未実行（予定から3時間以内は「遅れの可能性」。3時間を超えて始まった実行は「遅れて実行」）

なし

## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

なし

## 4. 初回成功（cloudflare の実行だけ。それ以前の cloudflare の実行に success が無いもの）

なし

## 5. 未報告のログ（`## 報告` の節が無い）

なし

## 6. Actions の使用量（mj。ジョブごとに1分へ切り上げた見積もり、日付は JST。前日が240分以上か、月の累計が含まれる3,000分の50%・75%を超えたとき）

- 前日（11/26）: 60分
- 11月の累計（11/1〜11/26）: 5,442分（含まれる 3,000分の 181%。75% を超えた）

## 7. 国家サイバー統括室の投稿（@cyber_forecast、直近36時間。取得は run-daily.yml のジョブ xcyber。「なし」は投稿なし、「取得できず」は取れなかった日）

- 2026-11-26 10:59 JST https://x.com/cyber_forecast/status/2125771271975870892
  > 【更新情報】
  > 米国時間2026年11月25日、Microsoft Edgeの更新プログラムが公開されました。
  > 本件更新（158.0.4370.45）にはセキュリティ修正が含まれています。更新が配信されましたら忘れずにアップデートしてください。
  > https://t.co/rnSFlgax8X
  - リンク: https://learn.microsoft.com/en-us/deployedge/microsoft-edge-relnotes-security
- 2026-11-26 10:59 JST https://x.com/cyber_forecast/status/2125771320059437178
  > 【注意喚起】
  > 2026年11月25日、不正アプリのインストールへ誘導するフィッシングメールの報告を受けているとして、フィッシング対策協議会が注意喚起をしています。ご確認ください。
  > https://t.co/dWbmUsqImA
  - リンク: https://www.antiphishing.jp/news/alert/daiichilife_20261125.html
