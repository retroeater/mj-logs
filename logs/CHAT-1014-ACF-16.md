# CHAT-1014-ACF-16

- 着手日時: 2026-10-14
- 対象issue: #504・#506
- ブランチ: work/1014-acf-cron
- 着手時HEAD: 191d9dbde91bd18f25f95de07fd0d032d05e1417

## 指示

【Claude作成】Claude Code 向け指示：2026-10-14 の朝、Worker（mj-scheduler）の定時の起動が抜け、check-image-links が「未起動」になった件を調べ、取りこぼした回を埋め合わせる作りの案を出す（調べと案まで。コードは変えない） Chat-Ref: CHAT-1014-ACF-16 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1013-ACF-15 とは別のブランチ。並行で動かすなら別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-acf-cron の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1014-acf-cron を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-acf-cron origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-14 の朝、#506 の朝のまとめが「未起動 1」（check-image-links、予定 04:30）を出した。ほかの4つも 17〜27 分遅れて起動した。Worker は毎分の回ごとに1分の窓の予定だけを起動し、取りこぼした回の埋め合わせをしない作り（docs/notes/scheduler-worker.md「起動」）なので、Cloudflare 側で回が抜けるとその日は動かない。原因を確かめ、取りこぼしを埋め合わせる作りの案を出す。
決定（2026-10-14、平野さん）

* 取りこぼした回の埋め合わせを足す（今の「埋め合わせはしない」を改める）。まず調べと案、そのあと実装の指示

前提（チャット側。平野さんの決定ではない。2026-10-14 09:40〜10:00 JST に REST と Chrome〈Cloudflare のダッシュボード〉で見た）

* #506 の 2026-10-13T21:01Z のコメント: update-live-channel 04:00→04:17、update-sns-book 04:10→04:37、sync-dojo-calendar 04:15→04:37、delete-merged-branches 04:20→04:37、check-image-links 04:30→未起動。04:37 の3つは同じ時刻（19:37:47Z）に作られた
* `workers/scheduler/src/scheduler.mjs` の `dueRows()` は窓を `(scheduledTime - TICK_MS, scheduledTime]` とする。04:37 に 04:10・04:15・04:20 の行が起動したので、遅れて届いた回が元の `scheduledTime` を持っていたと見られる（推測）。04:30 の回は届かなかったと見られる（推測）
* Observability（mj-scheduler、Events、直近12時間、15分ごとの棒）: 10/13 23:00 ごろ〜10/14 00:30 ごろは0件、02:45〜03:00 ごろと 04:00〜04:30 ごろも0件の棒があり、04:37 ごろの棒が突出（26件）。12時間で 573件（毎分の回だけで 720 のはず。Worker の書く行も含む）。保存は3日なので、見るなら 10/16 までに見る。セッションからダッシュボードは見られない見込み（平野さんの画面・チャット側の Chrome が要る。要れば「判断が必要なこと」に、見る項目を書く）
* 同じ画面の右上に「Latest build failed」。Deployments の一覧で、Workers Builds の mj-scheduler のビルドが「Merge remote-tracking branch 'origin/cloudflare' into work/1012-dsn」（cloudflare、約19時間前＝10/13 14時台 JST）で Failed（11m 44s）。動いている版は 718589a6（3日前、CHAT-1011-WHS-12 のコミット、Ready）。`workers/scheduler/` の外だけの push ではビルドされないと scheduler-worker.md にあるが、このビルドが走った理由と失敗の原因は未確認。今の動きへの影響は無い見込み（動いている版は変わっていない）
* 今朝の check-image-links は、平野さんか別のセッションが 09:25 JST に手動実行し（run 38490119497、題に「[scheduled]」なし）、check・saikyo・sns_book の3ジョブとも success
* 未マージの work/ ブランチで `workers/scheduler/` に触れるものは無い（10/14 09:50 JST、REST。1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize）
* 関わる issue: #504（予約実行を Worker から起動、Open）、#506（朝のまとめ）、#298（Actions の使用量。毎分の起動にしたのは #298）
* 使う skill は無い

手順

1. 同じ目的の issue（クローズ済みを含む。「取りこぼし」「埋め合わせ」「未起動」「cron」など）と、未マージの work/ ブランチで `workers/scheduler/` に触れるものが無いことを確かめる。#504・#506 の本文と最新のコメント、docs/notes/scheduler-worker.md、`workers/scheduler/src/`・`test/`・`schedule.json` を読む
2. 調べる（直さない）: (a) 10/7〜10/14 の朝ごとに、Worker から起動した run（`[scheduled]` の題、workflow_dispatch）の予定と作られた時刻の差を表にする（REST）。未起動・遅れの日と幅を出す (b) Cloudflare 側の原因: Cloudflare の status ページ・Cron Triggers の文書（遅れ・抜け・`scheduledTime` の扱い・再送の有無）を、繋がる範囲で確かめる。推測と確かめたことを分ける (c) Workers Builds の失敗: 10/13 の mj-scheduler のビルドが走った理由（ビルドの監視するパスの設定）と失敗の原因を、check-run（REST）で確かめられる範囲で確かめる。ダッシュボードでしか見られないなら、見る項目を「判断が必要なこと」に書く
3. 案を書く: 取りこぼした行を後の回で起動する作り。少なくとも、(i) どこまでさかのぼるか（例: 予定から N 分以内）、(ii) 二重に起動しない方法（その日の `[scheduled]` の run を REST で確かめる、など）と REST の呼び出しの数（毎分の回で増やさない工夫、#298）、(iii) 遅れて届いた回（古い `scheduledTime`）との関係、(iv) 朝の確かめ（06:00）の回が抜けたときの扱い、(v) ワークフロー側の保険（`schedule` やゲート）との重なり、(vi) テストの足し方と、マージ後に確かめる方法、を書く。選ぶ点は「判断が必要なこと」に書く。案を #504 にコメントし、上の「決定」を docs/decisions/automation.md に足す

止まる条件

* 同じ目的の issue がある、または未マージの work/ ブランチが `workers/scheduler/` を変えている
* コード・ワークフロー・Worker の設定を変える要が出た（この指示では変えない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（案の選ぶ点は「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 916667a9）: https://github.com/retroeater/mj-logs/tree/main/guide/916667a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
