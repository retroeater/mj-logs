# CHAT-1008-WKR-11

- 着手日時: 2026-10-08
- 対象issue: #504
- ブランチ: work/1008-wkr-11
- 着手時HEAD: 未取得（HEAD の SHA を読むコマンドは WKR-01 で分類器に拒否されたため取りにいっていない。#493）。origin/cloudflare から作成

## 指示

【Claude作成】Claude Code 向け指示：#504 段階2の後の回 — update-live-channel を Worker からの起動（04:00）に移し、保険の予約実行にゲートを付けて 06:43 に移す（作業ブランチで作って確かめるまで。マージしない） Chat-Ref: CHAT-1008-WKR-11 マージ: 判断待ちで止まる（平野さんがログを読んでから、続きの指示でマージする。ドキュメントだけの変更も、この指示では cloudflare へ入れない。ワークフローの変更と同じブランチに入っていて、分けると文書だけが先に本番の動きと食い違うため） 貼る時機: その日（JST）の update-live-channel の毎日の実行（`schedule`）が終わった後（2026-10-08 は 07:14 開始の run #74 が success で済んでいる。チャット側が mj-logs の actions/status.md で確かめた）。CHAT-1008-WKR-10 は完了・マージ済み 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1008-wkr-11〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-wkr-11 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1008-wkr-11 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-wkr-11 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: `.github/workflows/update-live-channel.yml`、`workers/scheduler/schedule.json` と `workers/scheduler/test/`、（下の「前提」の c で直すと決めたときだけ）`scripts/sync_live_calendar.py` とそのテスト、下の「文書」に挙げた docs、docs/decisions/・docs/logs/、#504 へのコメント（着手・結果）、`update-live-channel.yml` の作業ブランチでの手動実行（下の試験 S・D を各1回。やり直しは各1回まで）。`workers/scheduler/src/`・`wrangler.jsonc`・`regenerate-page.yml`・ほかのワークフローは変えない（変える必要が出たら止まる）。cloudflare では手動実行しない。#504 の本文は変えない（マージの後の続きの指示で直す）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。続けて、当日（JST）の update-live-channel の `schedule` の実行が終わっていること、同じ組（concurrency の `update-live-channel`）に実行中・待ちの実行が無いことを確かめ、どちらかが満たされなければ何もせず止まる。

目的
#504 の段階2の後の回。毎日の取り込み（update-live-channel）は予約実行（`schedule`、02:43 JST）が 2.5〜5 時間遅れて動いている（10/8 は 07:14）。Worker `mj-scheduler` から 04:00 JST に時刻どおり起動し、今の予約実行は保険として 06:43 JST に移して「当日に予約の起動が成功済みなら何もしない」ゲートを付ける。この指示では作業ブランチで作り、書き込まない形で確かめるところまで行う。
決定（2026-10-08、平野さん）

* 後の回（update-live-channel）の指示を、チャット側の予定（10/10）より早く出す

決定（2026-10-06、平野さん。docs/decisions/automation.md にある）

* #504 の起動時刻の表は変えない（update-live-channel は 04:00 JST）
* 保険として残す予約実行: update-live-channel だけ「当日に予約の起動が成功済みなら何もしない」ゲートを付け、予定を 06:43 JST に移す

前提（チャット側。平野さんの決定ではない）

* 識別子 WKR は同じチャットの WKR-01〜10 で使っている（04 は欠番）。それらのコミットやログが見つかっても、同じチャットの指示なので重複ではない
* 2026-10-06 の決定「数日見てから update-live-channel」は、道場部の Worker からの起動を1回（10/8 04:15:21、success）見た時点で、上の 10/8 の決定により時期を早めた。docs/decisions/automation.md には、この指示の決定として足す（10/6 の行は消さない）
* マージの承認は、まだ伺っていない。 そのためこの指示はマージせずに止まる。平野さんがログを読んで承認したら、チャット側が続きの指示（マージ、マージの後の確かめ、#504 の本文の直し）を出す
* 着手時に docs/notes/branch-operations.md「ワークフローを変更したとき」、docs/notes/scheduler-worker.md「予約の起動の見分け方」「起動の表の直し方」、docs/notes/yotei-sheet.md「手動実行」、#505 のコメント 2/3（段階2の材料と案。置き換える箇所とゲートの案）を読む。`git log -- .github/workflows/update-live-channel.yml` も見る
* 今のファイル（チャット側が 2026-10-08 に cloudflare の `update-live-channel.yml` を読んだ。468 行。行番号はその時点のもの。実物と食い違えば実物が正で、食い違いを報告に書く）
   * `on:` は `workflow_dispatch`（入力 9 個: apply・verify・allow_many_changes・allow_shrink・allow_many・backfill・yotei_apply・calendar_max_delete〈number、既定 30〉・calendar_apply）と `schedule`（`43 17 * * *`）。ワークフローの `permissions` は `contents: write`、concurrency は組 `update-live-channel`・`cancel-in-progress: false`
   * ジョブ `update`（`permissions` は contents: write・issues: write、`outputs` は `run`・`apply`）の env: `SCHEDULE_ENABLED: 'true'`、`APPLY: ${{ github.event_name == 'schedule' || inputs.apply }}`、`ALLOW_SHRINK`・`ALLOW_MANY`・`ALLOW_MANY_CHANGES` は `${{ github.event_name != 'schedule' && inputs.<名前> }}`
   * ステップ「実行するかどうかを決める」（`id: gate`）: `schedule` で `SCHEDULE_ENABLED` が true でなければ `run=false` で終わる。それ以外は `run=true`・`apply=$APPLY` を出し、`verify` は `schedule` なら水曜（`VERIFY_WEEKDAY`）だけ true、ほかは `inputs.verify`
   * 以降のステップは `steps.gate.outputs.run == 'true'` で動く。層1の取り直しのステップは `&& inputs.backfill == true`
   * ジョブ `regenerate` は `needs.update.outputs.run == 'true' && needs.update.outputs.apply == 'true'`、ジョブ `yotei` は `needs.update.outputs.run == 'true'`。`yotei` の env: `APPLY: ${{ needs.update.outputs.apply == 'true' || inputs.yotei_apply == true }}`、`CALENDAR_APPLY: ${{ github.event_name == 'schedule' || inputs.calendar_apply == true }}`
   * `schedule` の契機を見ている箇所は、#505 の調べで 7 か所（env の APPLY・ALLOW_SHRINK・ALLOW_MANY・ALLOW_MANY_CHANGES、gate の SCHEDULE_ENABLED の判定、gate の verify の判定、yotei の CALENDAR_APPLY）。ほかに `scripts/sync_live_calendar.py` の `delete_limit()` が `GITHUB_EVENT_NAME` を見る
* 作り（チャット側の案。#505 のコメント 2/3 の Code の案を元にした。実物に合わせて形を変えてよい。変えた点と理由を報告に書く）
   * a. 入力 `scheduled`（boolean、既定 false。説明に「Worker からの予約の起動用。手では付けない」）と `run-name: ${{ inputs.scheduled && '[scheduled] 「連盟ch」の毎日の取り込み' || '' }}` を足す（sync-dojo-calendar.yml・delete-merged-branches.yml と同じ形。入力は 10 個になり、上限 25 に収まる）
   * b. 「`schedule` か、`scheduled` が真」を1か所で決め（sync-dojo-calendar.yml の env `SCHEDULED` と同じ形）、上の 7 か所をそれに置き換える。`scheduled` が真のときは、ほかの手動の入力（apply・verify・allow_*・backfill・yotei_apply・calendar_apply・calendar_max_delete）を無視し、`schedule` と同じ動きにする（書く・水曜だけ確認・上限は外さない・backfill は行わない・カレンダーに書く）。backfill のステップの条件と、yotei の APPLY も、この形で矛盾しないかを確かめる
   * c. `scripts/sync_live_calendar.py` の `delete_limit()`: Worker からの起動では入力の既定 30 が渡り、`MAX_DELETES`（30）と同じなので動きは変わらない見込み。直す（`scheduled` のときも `schedule` と同じ道を通す）か、直さないかは、実物を読んで決め、理由を報告に書く。直すならテストを足す
   * d. `schedule` の cron を `43 21 * * *`（06:43 JST）にする。ファイルの冒頭の注記の時刻も直す
   * e. ゲート: ステップ「実行するかどうかを決める」の中で、Actions の API から「当日（JST の 0 時以降）に作られた、このワークフローの `workflow_dispatch` の実行のうち、題（`display_title`）が `[scheduled]` で始まり、ブランチ（`head_branch`）が `cloudflare` で、`conclusion` が `success` のもの」を探す（引き方は Worker の朝の確かめ〈`workers/scheduler/src/scheduler.mjs`〉と同じ `event=workflow_dispatch&created=>=…`。`+09:00` の `+` は URL で `%2B` にする）
      * 見つかり、かつ契機が `schedule` のときだけ、`run=false` にしてサマリに「当日の予約の起動（run #番号）が成功済みのため、保険の実行は何もしません」と書いて終わる（以降のステップと、ジョブ regenerate・yotei は動かない。実行の結論は success）
      * 引いた結果（あり・なし、ありなら run の番号）は、契機にかかわらず毎回ログに1行出す（`schedule` は手で起こせないので、手動実行で引き方を確かめられるようにする）。止めるのは `schedule` のときだけで、`scheduled` が真の起動（04:00 の本番）と、ふつうの手動実行は止めない
      * API を引けなかったとき（HTTP のエラー・応答が読めない）は、止めずに実行する（保険の実行なので、動かないより2回動くほうを選ぶ）。その旨をサマリに書く
      * ジョブ `update` の `permissions` に `actions: read` を足す
      * Worker が起動した実行がまだ動いているうちに保険の実行が始まっても、同じ concurrency の組なので保険の実行は待たされ、ゲートの判定は先の実行が終わってからになる見込み（実物の concurrency の位置で確かめる）
   * f. `workers/scheduler/schedule.json` に `update-live-channel.yml` 毎日 04:00（有効）の行を足す。テストで、04:00 の回に1本起動すること、06:00 の朝の確かめの対象が3行になることを確かめる
   * g. Worker の朝の確かめが、作業ブランチの実行（`head_branch` が `cloudflare` でないもの）を当日の成功に数える作りかどうかを読んで、報告に書く（この指示では直さない）
* 確かめ（マージの前、作業ブランチで。どちらも書き込まない）。 1本ずつ行い、前の実行が終わってから次を起動する。起動の直前に、同じ組に実行中・待ちの実行が無いことを確かめる（待ちの実行は、後から来た実行に取り消される。本番の実行を取り消さないため）。待つ上限は1本につき 15 分。超えたらその時点の状態を書き、「未確認の項目」に回す
   * 試験 S（`scheduled` の道筋）: `SCHEDULE_ENABLED` を `'false'` にしたコミットを作業ブランチに push し、ref `work/1008-wkr-11`・inputs `{"scheduled": "true"}` で起動する。起動の前に、gate のシェルを手元で env を与えて走らせ、`run=false` になることを確かめる（ならなければ起動しない）。 見込み: success、題が `[scheduled] 「連盟ch」の毎日の取り込み`、ログの env で `APPLY` が true・`ALLOW_*` が false、gate が「毎日の実行は無効」で `run=false`、以降のステップとジョブ regenerate・yotei は動かない（層1のコミット・シート・カレンダー・issue に何も書かない）
   * 試験 S の後、`SCHEDULE_ENABLED` を `'true'` に戻すコミットを push する（差分がその1行だけであることを `git diff` で確かめる）
   * 試験 D（入力なしの手動実行。件数だけ）: 戻した後のファイルで、ref `work/1008-wkr-11`・inputs なしで起動する。見込み: success、題は既定、`APPLY` が false で何も書かない（層1のコミットなし、ジョブ regenerate は動かない）、ゲートの行は「なし」（試験 S の実行は作業ブランチのものなので数えない。API が引けて、`actions: read` が足りていること）
   * 件数だけの実行でも【3】の見出しの検査は行われ、通らなければ失敗する（docs/notes/yotei-sheet.md「手動実行」）。試験の失敗でも平野さんに通知メールが届くので、最終報告に「試験による失敗の通知は対応不要」と書く（失敗が無ければ書かない）
   * 確かめられないこと（「未確認の項目」に書く）: `scheduled` が真で実際に書き込む道筋と、`schedule` の契機でゲートが止める道筋は、マージの後の朝（Worker の 04:00 と、保険の 06:43 予定）に初めて動く
* 文書（作業ブランチで直す。今の内容を読んでから、古い記述を置き換える。同じ趣旨の記述があれば置き換え・拡張してよい）
   * docs/notes/scheduler-worker.md（起動の表が3行になること、「予約の起動の見分け方」を持つワークフロー、保険のゲートの説明と見方）
   * docs/notes/live-channel-write.md「7. 毎日の取り込み」（毎日 02:43 JST → Worker から 04:00、保険は 06:43 予定でゲート付き。「02:43 JST ごろ」と書いている編集の注意の2か所も）、docs/notes/live-page-design.md「毎日の流れ」の時刻
   * docs/notes/yotei-sheet.md「手動実行」（表の列「schedule」が `scheduled` の起動にも当てはまること、`scheduled` は手で付けないこと）、docs/notes/static-generation.md「ワークフローの一覧」「ワークフローを手動実行するとき」の該当の行
   * docs/instruction-template.md の「貼る時機」の例（「毎朝の実行〈02:43 JST の schedule〉」）
   * ほかに `02:43`・`43 17` を書いている文書（`grep` で探す。docs/decisions/ の過去の決定の記録は直さない）
   * docs/handover.md は、この指示では変えない（5章の #504 の行は、マージの後の続きの指示で直す）
   * docs/notes/chat-side-operations.md を直す必要があれば、直す前後のバイト数をログに書く（上限 28KB・警告域 26KB）
   * 3文書（CLAUDE.md・handover.md・chat-side-operations.md）に出典としての Chat-Ref は書かない
* #504 に着手中のコメントを残し、完了時に結果（入れた作り、試験 S・D の run の番号と結論、確かめられないこと、マージ待ちであること）をコメントする（末尾に Chat-Ref）。#504 は閉じない
* 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）に、`update-live-channel.yml`・`workers/scheduler/`・`scripts/sync_live_calendar.py` を変えるものがあれば止まる。同じ文書の同じ箇所を直しているもの（#298 の続きの作業など）があれば、その箇所は直さずに報告する
* ログは public（mj-logs）。鍵やトークンの値、シートの ID は書かない

手順

1. #504 が Open で他セッションの着手中のコメントが無いこと、`update-live-channel.yml` の今の形（契機を見ている箇所の一覧）、未マージのブランチとの重なりを確かめる
2. ワークフロー・起動の表・テスト・文書を直し、試験 S → `SCHEDULE_ENABLED` を戻す → 試験 D の順に確かめる
3. #504 にコメントし、ログの報告を書いて、マージせずに止まる

止まる条件
どれかに当たったら、その時点でやめて判断待ちで報告する。

* #504 が閉じている、または他セッションの着手中のコメントがある
* 契機を見ている箇所に、前提の一覧（7 か所・backfill の条件・`delete_limit()`）に無く、`schedule` と同じ扱いにしてよいか判断が要るものがある（同じ形で置き換えられるものは、置き換えて報告に書く）
* 未マージの `work/` ブランチに、同じワークフロー・`workers/scheduler/`・`scripts/sync_live_calendar.py` を変えるものがある
* gate のシェルの手元の確かめで `run=false` にならない（試験 S を起動しない）
* 試験 S か D で、書き込みのステップが動いた（報告の「エラー」の先頭に、何に何を書いたかを書く。元に戻す操作はしない）
* 試験 S か D が見込みと違う（題・`APPLY`・`run`・ゲートの行・結論のどれか。他の実行が間に入って取り消されたときだけ、1回やり直してよい）
* `workers/scheduler/src/` を変えないと起動の行が足せない、または `node --test` か `python3 -m unittest discover -s scripts/tests` が通らない
* 手動実行が起動できない（平野さんが GitHub の画面の「Run workflow」で実行できるように、選ぶブランチと入力を報告に書く）
* docs/notes/chat-side-operations.md が直した後に警告域（26,624 バイト）を超える

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」。「結果の要点」に、置き換えた箇所の数と場所、`scheduled` とほかの入力が両方来たときの動き、ゲートの作り（引き方・止める条件・引けなかったときの動き）、`delete_limit()` を直したかと理由、試験 S・D の run の番号・題・結論・ログで確かめた値、Worker の朝の確かめがブランチで絞っているか、直した文書を書く。「判断が必要なこと」に「マージしてよいか」を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-WKR-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-WKR-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

1. Chat-Ref の確認: `git log --all --grep="CHAT-1008-WKR-11"` は0件。リモート・ローカルに `work/1008-wkr-11` は無い → `git checkout -b work/1008-wkr-11 origin/cloudflare`
2. 手順0: 指示欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
3. 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1008-wkr-11
- ログ: https://github.com/retroeater/mj/blob/work/1008-wkr-11/docs/logs/CHAT-1008-WKR-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-wkr-11
- 確認用URL: なし
- マージ: 未
- issue: #504
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5b12292）: https://github.com/retroeater/mj-logs/tree/main/guide/c5b12292

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cb2ba7f5.md
