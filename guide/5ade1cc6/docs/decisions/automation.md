# 自動化（GitHub Actions の予約実行とその起動）

分野の決定の記録。書き方は [README.md](README.md)。

## 2026-10-05（CHAT-1005-RUN-08）

- 予約実行の遅れへの対処として、Cloudflare の Worker の定時実行（Cron Triggers）から GitHub のワークフローを起動する方式にする。やがて時刻の正確さが問われるジョブが出る見込みで、先に備える（#491）
- 朝のジョブは、6時（JST）までに完了していること
- 起動の失敗や遅れを検知する仕組みを、別に入れる

## 2026-10-05（CHAT-1005-RUN-10）

- 設計と決定は #504「予約実行を Cloudflare の Worker から時刻どおりに起動する」に置く。#491 の「毎朝の実行の遅れの観測」は #504 を指して済にした
- 起動の契機は `workflow_dispatch` の入力 `scheduled`（真偽）で伝える。`repository_dispatch` は採らない
- 起動時刻は #504 の表（04:00 から5分刻み、05:30 に sync-logs、06:00 に検知）のとおり。起動時刻・依存関係・並行実行の可否の包括的な確認は #505 で行う
- 今の予約実行（`schedule`）は、試験の間は遅い時刻に保険として残し、移行が済んだら外す。外す予定日は仮に 2026-11-30
- デプロイは、Workers Builds をもう1つつなぐ
- GitHub のトークンは fine-grained の PAT（対象は mj だけ、Actions と Issues の読み書き）。期限は 366 日にし、切れる1か月前にカレンダーで知らせる
- 通知先は、新しい常設の issue「予約実行の起動」（#504 の段階1で作る）
- delete-merged-branches の1本から始める

## 2026-10-05（CHAT-1005-WKR-01）

- #504 段階1（`workers/scheduler/` の Worker `mj-scheduler`、delete-merged-branches の1行、入力 `scheduled`、通知用の常設 issue #506）のマージを承認する。止まる条件のどれかに当たったらマージしない

## 2026-10-05（CHAT-1005-WKR-02）

- mj-scheduler が起動の API に送る入力 `scheduled` を、真偽値から文字列の `"true"` に直す（手動実行で通ることを確かめてある形にそろえる）。マージは承認済み（止まる条件つき）

## 2026-10-06（CHAT-1006-WKR-05）

- mj-scheduler にログの設定（observability、Workers Logs）を足す。マージは承認済み（止まる条件つき）

## 2026-10-06（CHAT-1006-WKR-07）

- #504 の起動時刻の表は変えない（sync-dojo-calendar は 04:15 のまま）（#505）
- 保険として残す予約実行（`schedule`）: update-live-channel だけ「当日に予約の起動が成功済みなら何もしない」ゲートを付け、予定を 06:43 JST に移す。sync-dojo-calendar と sync-logs はゲートなしで、今の時刻のまま（2026-10-05 の「試験の間は遅い時刻に保険として残す」の中身を具体にした）
- #504 の段階2は2回に分ける。先に sync-dojo-calendar と sync-logs、数日見てから update-live-channel
- sync-logs の concurrency の `queue: max`（取り消しを無くす）は、段階2とは別の指示で試す（#509）
- #505 を閉じる。残る作業（`queue: max` の試し）は #509 に起票した
