# CHAT-1023-OHA-13

- 着手日時: 2026-10-23
- 対象issue: #563
- ブランチ: work/1023-oha
- 着手時HEAD: fe3d9966e

## 指示

【Claude作成】Claude Code 向け指示：run-daily の最後に mj-logs の同期を起動し、毎朝の運行の後に morning.md を必ず書き直す Chat-Ref: CHAT-1023-OHA-13 マージ: 承認済み（チャットで） 貼る時機: いつでも（mj の Secret `MJ_LOGS_DISPATCH_TOKEN` は登録済み。0章で確かめる）。CHAT-1023-OHA-10 と同じセッションで続けてよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-oha の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-oha を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-oha origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。あわせて mj の Actions の Secret に `MJ_LOGS_DISPATCH_TOKEN` があるか（名前だけ。`GET /repos/retroeater/mj/actions/secrets` などで）を確かめ、無ければ何もせず止まる。

目的
#563。朝の報告を 06:00 JST の自動実行（スケジュールされたタスク）にする準備。mj-logs の actions/morning.md は今、mj への push と mj-logs の 03:41 JST の予備の実行のときにしか書き直されず、run-daily（04:00 JST、04:25 ごろまでに終わる）の後の最初の書き直しは 10/16〜10/23 で 05:58〜07:39 JST だった。run-daily の最後に mj-logs の同期を起動し、毎朝の運行の後に必ず書き直されるようにする。CHAT-1023-OHA-11・OHA-12 は送る前に差し替えたため欠番（OHA-12 のルーティンの API の起動は、プロジェクトのスケジュールされたタスクに API の起動を付けられなかったためやめた）。
決定（2026-10-23、平野さん）

* 朝の報告は、スケジュールされたタスクで毎朝 06:00 JST に自動実行する（案 B）。終わりの知らせはスマホへの通知、モデルは Opus 5.5。タスクはチャット側が 2026-10-23 に作った（「OHA 朝の報告（毎朝06:00）」）。API での起動はやめた（プロジェクトのタスクの編集画面に API の起動が無かった）
* run-daily の後に morning.md を書き直す方法は「run-daily から mj-logs の同期を起動する」。Worker（mj-scheduler）のコードは変えない（2026-10-14 の決定のまま）。毎朝 mj へ空のコミットを push する案は採らない
* 起動に使うのは、平野さんが新しく作る mj-logs の Actions の Read and write だけを持つ fine-grained のトークン（mj の Secret `MJ_LOGS_DISPATCH_TOKEN`）
* この指示のマージは承認済み（下の止まる条件つき）

前提（チャット側。平野さんの決定ではない）

* Worker は今、mj に push があった直後の回に mj-logs の `sync-from-mj.yml`（ref `main`）を `workflow_dispatch` で起動している（`workers/scheduler/src/scheduler.mjs` の `SYNC`）。同じ起動を run-daily の最後のジョブで行う
* ジョブの案: run-daily のほかのジョブすべての後に（`needs`、`if: ${{ !cancelled() }}`。どれかが失敗しても動く）、`POST /repos/retroeater/mj-logs/actions/workflows/sync-from-mj.yml/dispatches`（`ref: main`）を `MJ_LOGS_DISPATCH_TOKEN` で呼ぶ。起動が失敗したら、そのジョブだけ失敗にする（ほかのジョブの結論は変えない）。既存の部品（`.github/actions/` の composite action）で足りるかを先に確かめる
* 起動するのは cloudflare の実行のときだけにするか、作業ブランチの手動実行でも起動するか（要確認。同期は何度動いても同じ結果になる作りで、作業ブランチから起動しても害は無い見込み）は Code が決めてよい。決めた形をログに書く
* 新しいワークフローは足さない（run-daily.yml にジョブを足すだけ）
* 置き場所の文書は docs/notes/github-actions.md の run-daily.yml の行・節と、docs/notes/scheduler-worker.md の mj-logs の同期の説明（要確認）。決定は docs/decisions/automation.md

手順

1. #563 に着手中のコメントを書く（本文・ラベルは変えない）。#564 に他セッションの着手中コメントが無いか確かめる。未マージの work/ ブランチが `.github/workflows/run-daily.yml` の同じジョブを変えていないか確かめる。上の「前提」の1つ目を origin/cloudflare で確かめ、食い違えば止まる。docs/notes/github-actions.md「ワークフローを変更したとき」を読む
2. run-daily.yml に mj-logs の同期を起動するジョブを足す（`MJ_LOGS_DISPATCH_TOKEN` を `run:` に直接展開せず env で受ける、#132）。作業ブランチで run-daily を手動実行し（書かない道筋）、最後のジョブが success になり、mj-logs の `sync-from-mj.yml` の実行がその時刻に `workflow_dispatch` で始まったことを確かめる（mj-logs の実行は REST で読める。run の ID をログに書く）。ログ・実行ログにトークンの値が出ていないことも確かめる
3. 確かめが済んだら cloudflare へマージし、文書と docs/decisions/automation.md を直す。#563 にこのログの `## 経過` の結果を引用してコメントする（#563 は閉じない）。翌朝（2026-10-24）の run-daily の定期実行の後に mj-logs の同期が起動され、06:00 より前に actions/morning.md が書き直されたかは「未確認の項目」に、予約実行の初回の日時として書く

止まる条件

* 0章の Secret が無い
* 前提の1つ目が食い違う
* #564 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが run-daily.yml の同じジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 作業ブランチの手動実行で、足したジョブが failure（権限の不足を含む）、または足したジョブ以外が手動実行の前と違う結論になる
* トークンの値がログ・実行ログ・文書に出た（すぐに止まり、平野さんにトークンの作り直しを頼む旨を報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を報告に書いたうえで残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-OHA-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-OHA-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b4b933ba）: https://github.com/retroeater/mj-logs/tree/main/guide/b4b933ba

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4b933ba/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
