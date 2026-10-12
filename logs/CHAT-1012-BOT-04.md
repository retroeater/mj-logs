# CHAT-1012-BOT-04

- 着手日時: 2026-10-12
- 対象issue: #304
- ブランチ: work/1012-bot
- 着手時HEAD: d627615c1

## 指示

【Claude作成】Claude Code 向け指示：Workers `mj` の非本番ブランチのビルドの除外に `workers/**` を足した（平野さんの手作業）ことを、作業ブランチへの push の動きで確かめ、記録を直す（コードは変えない） Chat-Ref: CHAT-1012-BOT-04 マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs。試験で足す `workers/.watch-test` は同じブランチで消すので差分に残らない）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: 平野さんがダッシュボードで、非本番ブランチのビルドの除外に `workers/**` を足した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-bot を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-bot origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Workers `mj` の Workers Builds には、本番（cloudflare）と非本番ブランチの2つのトリガーがあり、除外（Build watch paths の Exclude）が違っていた。2026-10-06 に `workers/**` を足したのは本番の側だけで、非本番には無い。そのため、`workers/` だけを変えた作業ブランチの push でも、サイトの `mj` のプレビューがビルドされていた（docs/notes/scheduler-worker.md「未確認」の「理由は分からない」の件）。平野さんが非本番の側にも足したので、動きで確かめて記録を直す。
決定（2026-10-12、平野さん）

* 非本番ブランチのビルドの除外にも `workers/**` を足すのが本来の形とする（本番と同じ4つ）。設定は平野さんがダッシュボードで変える

前提（チャット側。平野さんの決定ではない）

* 変える前の値（チャット側が 2026-10-12 15:20 ごろ、Claude in Chrome でダッシュボードと同じ画面の API を GET で読んだ値）: 本番のトリガー（branch `cloudflare`、`npx wrangler deploy`）の除外は `.git/`・`docs/**`・`node_modules/**`・`workers/**`。非本番のトリガー（「Deploy non-production branches」、branch `*` から `cloudflare` を除く、`npx wrangler versions upload`）の除外は `.git/`・`docs/**`・`node_modules/**`
* `mj-ask`・`mj-scheduler` の Worker は自分のディレクトリ（`workers/ask/`・`workers/scheduler/` の見込み。要確認）だけを Include にしているので、`workers/` の直下のファイルではビルドされない見込み（要確認。docs/notes/scheduler-worker.md・ask-worker.md で確かめる）
* 平野さんがダッシュボードで変えたこと・画面の名前は申告値。設定が効いたかは、スクリーンショットではなく check-run で確かめる（docs/notes/chat-side-operations.md「外部サービスの設定」）

手順

1. 確かめる: docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」の表、docs/notes/scheduler-worker.md の「未確認」の該当行、`mj-ask`・`mj-scheduler` の Include（docs/notes/scheduler-worker.md・ask-worker.md）。`workers/` の直下に足すファイルが、ほかの Worker のビルドの Include に入らないことを確かめ、入るなら止まる
2. 動きで確かめる:
   * 作業ブランチに `workers/.watch-test`（1行のテキスト）だけを足すコミットを push し、そのコミットの check-run に「Workers Builds: mj」が付かないこと（数分待つ。待つ上限は CLAUDE.md「判断・作業の原則」）を確かめる。ほかの Worker の check-run も付かないことを書く
   * 続けて、そのファイルを消すコミットを push する（このコミットも `workers/` だけの変更なので、同じく「Workers Builds: mj」が付かないことを確かめる）
   * 比べるために、docs と `workers/` 以外のファイルを変えない限りビルドは走らないので、対照の push はしない
3. 記録する: docs/notes/cloudflare.md の表の「Build watch paths: Exclude」の行を、本番と非本番の2つのトリガーに分けて書き（2026-10-12 に非本番にも `workers/**` を足した、平野さんの申告と check-run の確認）、docs/notes/scheduler-worker.md「未確認」の該当行を、原因（非本番のトリガーに `workers/**` が無かった）と直した日に置き換える。決定を `docs/decisions/` の合う分野（cloudflare.md の見込み）に足す。#304 の BOT-03 の補足のコメントに、直したことを返信のコメントで書く

止まる条件

* 試験の push のどちらかに「Workers Builds: mj」の check-run が付いた（設定が効いていない。ログに check-run の名前と結論を書き、ファイルを消すコミットまで push してから止まる。マージはしない）
* `workers/` の直下のファイルが、ほかの Worker のビルドの Include に入る
* 追記先に、この決定と矛盾する記述がある（同じ趣旨なら止めず、置き換え・拡張してよい。どう処理したかを報告に書く）
* 未マージの work/ ブランチが docs/notes/cloudflare.md・scheduler-worker.md の同じ節、または `docs/decisions/` の同じファイルの末尾を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（マージの前に、`git diff origin/cloudflare...HEAD --stat` に `workers/` が無いことを確かめる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-BOT-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-BOT-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-bot
- ログ: https://github.com/retroeater/mj/blob/work/1012-bot/docs/logs/CHAT-1012-BOT-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-bot
- 確認用URL: なし
- マージ: 未
- issue: #304
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d627615c）: https://github.com/retroeater/mj-logs/tree/main/guide/d627615c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
