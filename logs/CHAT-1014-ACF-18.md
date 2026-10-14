# CHAT-1014-ACF-18

- 着手日時: 2026-10-14
- 対象issue: #504・#506
- ブランチ: work/1014-acf-cron
- 着手時HEAD: 705aa4237f0e4861338ff60f6be6e1c1a756e980

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1014-ACF-16 の判断待ちへの回答 — Worker（mj-scheduler）に、取りこぼした予定を後の回で起動する埋め合わせと、朝の確かめの書き直しを入れる Chat-Ref: CHAT-1014-ACF-18 マージ: 承認済み（チャットで、2026-10-14。平野さんが ACF-16 の4つの選ぶ点をすべて勧める案で答えた）。条件は「止まる条件」に当たらず、テストが通り、マージ後の Workers Builds（check-run「Workers Builds: mj-scheduler」）が success であること 貼る時機: いつでも（CHAT-1014-ACF-16 は完了報告済み〈状態: 判断待ち〉）。ただし毎朝の起動と重ならないよう、03:30〜06:45 には cloudflare へ入れない（デプロイの反映は最大15分）。CHAT-1014-ACF-17 とはブランチが別なので、並行で動かすなら別のセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-acf-cron の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16・17 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1014-acf-cron を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-acf-cron origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1014-ACF-16 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
2026-10-14 の朝、Cloudflare の定時の回が抜け・遅れて届き、check-image-links が未起動になった（ACF-16）。Worker に、取りこぼした予定を後の回で起動する埋め合わせを入れ、遅れて届いた回で二重に起動しないようにし、06:00 の朝の確かめの回が抜けても確かめが書かれるようにする。
決定（2026-10-14、平野さん。#504 の ACF-16 の案〈https://github.com/retroeater/mj/issues/504#issuecomment-6159187821 〉の勧めるとおり）

* さかのぼる幅は予定から 60 分
* 二重起動を避ける方法は案 A: 状態を持たず、予定から 5・10・20・40 分後の回だけ、その行の当日の実行の一覧を REST で引き、`[scheduled]` の実行が無ければ起動する（1行1日4回、5行で約20回）
* 遅れて届いた回（実際の時刻と `scheduledTime` の差が 2 分超）は、ふだんの起動をせず、一覧を確かめてから起動する。チェックポイントの判定は実際の時刻で行う
* 06:00 の朝の確かめの回が抜けたときに備え、06:15・06:30 の回でも確かめ、#506 に当日の確かめが無ければ書く
* 「取りこぼした回の埋め合わせはしない」（docs/notes/scheduler-worker.md「起動」）を改める

前提（チャット側。平野さんの決定ではない）

* 作りの詳細（一覧の引き方 `GET …/actions/workflows/<ファイル>/runs?event=workflow_dispatch&branch=cloudflare&created>=<予定>`、`[scheduled]` の見分け方、起動の順番を表の順に保つこと、テストの項目、マージ後の確かめ方）は ACF-16 の案の (ii)〜(vi) が正。実物と合わなければ合わせて、どう直したかをログに書く
* 未マージの work/ ブランチ（2026-10-14 11:10 JST、REST）: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize・1014-gds・1014-oha。どれも `workers/scheduler/`・docs/notes/scheduler-worker.md に触れていない。着手時に改めて確かめる
* ダッシュボードの Trigger Events はチャット側の Chrome で開いたが、過去の回の一覧（「View events」）は見当たらなかった（2026-10-14）。Observability の 15 分ごとの件数で抜けは確かめてある（ACF-16 の前提）。この指示では見ない
* 本番で抜けは起こせないので、マージ後の確かめは「ふだんの朝が変わらない」「チェックポイントの回の REST の数が見込みどおり」を数日見ることになる（ACF-16 の案の (vi)）。Observability は平野さんかチャット側が見る
* 10/13 の mj-scheduler のビルドの失敗は #561（Closed、BLD のチャット）で扱い済み。この指示では扱わない
* 使う skill は無い

手順

1. 同じ目的の issue と、未マージの work/ ブランチで `workers/scheduler/` に触れるものが無いことを確かめる。#504・#506 の最新のコメント、docs/notes/scheduler-worker.md、`workers/scheduler/` の src・test・schedule.json を読む。#504 に他セッションの着手中コメントが無ければ、着手中のコメントを残す
2. テストを先に足し（ACF-16 の案の (vi) の項目。偽の fetch と時計）、直す前のコードで通らないことを確かめる。そのうえで実装し、`workers/scheduler/` のテスト（`node --test` など、既存のやり方）と `python3 -m unittest discover -s scripts/tests` を通す。1日の REST の増分を、テストかコードの数え上げで見込み（約20回＋朝の確かめの書き直しの分）と比べてログに書く
3. docs/notes/scheduler-worker.md の「起動」（埋め合わせの記述）と「朝の確かめ」を直す。#506 の本文に読み方の説明があれば合わせる。上の「決定」を docs/decisions/automation.md に足す。cloudflare へマージする（03:30〜06:45 を避ける）。マージ後、check-run「Workers Builds: mj-scheduler」が success になることを確かめる（Cloning の失敗〈cloudflare.md「ビルドが Cloning で失敗したとき」〉なら設定は変えず、ログに書いて「判断が必要なこと」に Retry build を頼む旨を書く）。#504 に結果と、マージ後に数日見ること（ACF-16 の案の (vi)）をコメントする。ACF-16 のログの状態の末尾に `/ 続き: CHAT-1014-ACF-18` を足す

止まる条件

* CHAT-1014-ACF-16 の状態が「判断待ち」でない、同じ目的の issue がある、未マージの work/ ブランチが `workers/scheduler/` を変えている、または #504 に他セッションの着手中コメントがある
* 案の前提（`dueRows()` の窓・起動の表の作り・ゲートの見分け方）が実物と違い、決定のとおりに作れない
* 足したテストが直す前のコードで通ってしまう、またはテストが通らない
* 1日の REST の増分が見込み（約20回＋朝の確かめの分）の2倍を超える
* Worker の設定（cron・vars・秘密）を変える要が出た
* 差分が `workers/scheduler/`・上の文書・#506 の本文・docs/decisions・ログ以外に出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。マージ後に数日見ることは「未確認の項目」に書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた。CHAT-1014-ACF-16 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- Chat-Ref の重複: `git log --all --grep="CHAT-1014-ACF-18"` は 0 件
- 作業ブランチ: ローカルの `work/1014-acf-cron`（66e8343c）は origin と同じで cloudflare の祖先。`git checkout work/1014-acf-cron` → `git merge --ff-only origin/cloudflare`（705aa423）。**この ff をログの先行 push より前に行った**（CLAUDE.md「作業ログ」節の順番と違う。内容への影響は無い）
- 手順1:
  - 同じ目的の issue は ACF-16 の検索で #506 だけ（常設の通知先）。未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize・1014-gds）で `workers/scheduler/`・scheduler-worker.md に触れるものは無い
  - #504 の最新は ACF-16 の案のコメントで、他セッションの着手中は無い。着手中のコメント https://github.com/retroeater/mj/issues/504#issuecomment-6159454137
  - 取り込んだ cloudflare に `scripts/morning_report.py`（OHA のチャット、mj-logs の `actions/morning.md`）が入っていた。`workers/scheduler/schedule.json` の `rows` を読むだけで、この指示では schedule.json を変えないので重ならない
  - 前提（`dueRows()` の窓 `(scheduledTime - 1分, scheduledTime]`・表の作り・`[scheduled]` の見分け方）は実物どおり
- 手順2:
  - テストを新しいファイル `workers/scheduler/test/catchup.test.mjs` に 15 件足した（遅れた回の見分け・チェックポイントの回〈04:30 の行は 04:35・04:40・04:50・05:10〉・04:30 の回が来ず 04:35 に一覧が空なら起動・一覧に `[scheduled]` があれば起動しない・手動の実行は数えない・04:10 の回が 04:37 に届いたら確かめて起動しない／一覧に無ければ起動・60 分を過ぎたら起動しない・ふだんの回は一覧を引かない・同じ回は表の順・ふだんの1日の REST の数・06:15／06:30 の回・#506 に当日の確かめがあれば何もしない・無ければ書く・すべて success なら書かない）。直す前のコードでは、このファイルが「The requested module '../src/scheduler.mjs' does not provide an export named 'checkMorningRetry'」で落ちた（既存の 25 件は通った）
  - 実装（`workers/scheduler/src/scheduler.mjs`）: 定数 `CHECKPOINT_MINUTES = [5, 10, 20, 40]`・`CATCHUP_LIMIT_MS = 60 分`・`LATE_TICK_MS = 2 分`・`CHECK_RETRY_MINUTES = [06:15, 06:30]`、関数 `isLateTick()`・`checkpointRows()`・`isCheckRetryTick()`・`checkMorningRetry()`。`dispatchDue()` に引数 `now`（既定は `scheduledTime`。既存のテストはそのまま通る）を足し、起動の本体を `dispatchRow()`、一覧の確かめを `startedSince()`（`event=workflow_dispatch&branch=<ref>&created>=<予定>`、題が `[scheduled]`、一覧を引けなければ起動しない）に分けた。`index.mjs` は `Date.now()` を渡し、遅れて届いた回では朝の確かめをしない（まとめて届くと二重に書くため）。朝の確かめの判定は実際の時刻で行う
  - 案の (ii) との違い: 「前の行がまだ起動していなければ同じ回で先に起動する」は、同じ回に起動する行を表の順に処理する形にした（チェックポイントの間隔〈5 分〉が予定の間隔〈5〜10 分〉以下なので、前の行の埋め合わせは後の行の予定より前か同じ回に来る）
  - テスト: `node --test 'workers/scheduler/test/*.test.mjs'` 40 件すべて pass、`python3 -m unittest discover -s scripts/tests` OK（取り込み後も同じ）
  - REST の増分（テスト「REST の数」と数え上げ）: チェックポイントの一覧は1行1日4回 → 5行で 20 回。06:15・06:30 の書き直しは、すべて success の日に各回 #506 のコメント1回＋一覧5回で 12 回（未起動がある日は状態の GET が足される）。合わせて 1日 約32回（見込み「約20回＋朝の確かめの分」の内、2倍を超えない）。今の同期の判定（毎分 1,440 回）と比べて小さい。遅れて届いた回の確かめは、抜けた日だけ
- 手順3（マージ前）: docs/notes/scheduler-worker.md の「動き」の 1（起動）に埋め合わせと遅れて届いた回の段落、3（朝の確かめ）に 06:15・06:30 の書き直しを足し、「取りこぼした回の埋め合わせはしない」を消した。決定を docs/decisions/automation.md に足した。ACF-16 のログの状態に `/ 続き: CHAT-1014-ACF-18` を足した。`origin/cloudflare` を取り込んだ（衝突なし）
- cloudflare へマージ: 11:24 JST に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1014-acf-cron:cloudflare`（6151b103..592334b3）。03:30〜06:45 の外
- マージ後の check-run「Workers Builds: mj-scheduler」（592334b3、id 115647536628）は success（02:25:42Z、Version ID 5f530ebb）
- #506 の本文を REST で書き換えた（書き換える直前の `updated_at` が 2026-10-13T21:01:47Z のままなのを確かめた）。変えたのは次の3か所:
  - 「Worker は5分おきに動き、…で起動する。」→「Worker は毎分動き、…で起動する。回が抜けたときは、予定から 5・10・20・40 分後の回で当日の予約の起動の有無を確かめ、無ければ起動する（埋め合わせ。予定から60分まで、2026-10-14 から）。」（「5分おき」は 10/7 から古い記述だった）
  - 表の「朝の予約実行の確かめ」の「いつ」に「（06:00 の回が抜けた日は 06:15・06:30 の回で、この issue に当日の確かめが無ければ）」を足した
  - 「実行中: 06:00 の時点で終わっていない」→「実行中: 確かめた時点（ふだんは 06:00）で終わっていない」
  - 書き換えた本文の末尾に、サーバが署名（`_Generated by Claude Code_`）を足した
- #504 に結果とマージ後に数日見ることをコメントした https://github.com/retroeater/mj/issues/504#issuecomment-6159520853

## 報告

- 状態: 判断待ち（マージ済み。マージ後に数日見ることが残る）
- ブランチ: work/1014-acf-cron
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-ACF-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-acf-cron
- 確認用URL: なし（Worker のコードと docs のみ）
- マージ: 済（592334b3）。Workers Builds: mj-scheduler は success
- issue: #504（着手中・結果をコメント）・#506（本文を書き換えた）
- 判断が必要なこと:
  - なし（数日見た結果で、#504 の段階を進めるかを決める）
- 未確認の項目:
  - ふだんの朝が変わらないこと（#506 に何も出ない、`[scheduled]` の実行の作られた時刻が予定から1分以内）。2026-10-15 の朝から数日
  - チェックポイントの回の REST の数と、`埋め合わせ:` の行が出ないこと（Observability。平野さんかチャット側が見る）。06:15・06:30 の回に #506 への書き込みが増えないこと
  - 次に回が抜けた朝に、埋め合わせで起動して結論が success になること
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 592334b3）: https://github.com/retroeater/mj-logs/tree/main/guide/592334b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
