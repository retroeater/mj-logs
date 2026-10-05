# CHAT-1005-RUN-08

- 着手日時: 2026-10-05
- 対象issue: #491（ほか #448・#472・#298・#498・#449 を参照）
- ブランチ: work/1005-run-08
- 着手時HEAD: a12c2f8e

## 指示

【Claude作成】Claude Code 向け指示：予約実行を Cloudflare の Worker から時刻どおりに起動する仕組みと、起動失敗・遅れの検知の設計案を作る（調査と設計のみ。実装しない）
Chat-Ref: CHAT-1005-RUN-08
マージ: ドキュメントのみ（docs/logs/・docs/decisions/。設計案を docs/notes/ に置くならそれも）の変更なので、CLAUDE.md「ブランチ運用」の規則どおり、完了報告のうえマージしてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる
貼る時機: いつでも（CHAT-1005-RUN-07 とは作業ブランチが別で、結果も使わない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1005-run-08〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-run-08 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1005-run-08 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-run-08 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
変更の範囲: docs/logs/・docs/decisions/（設計案を置くなら docs/notes/）と、#491 へのコメント。ワークフロー・コード・`wrangler.jsonc`・Cloudflare の設定は変えない。ワークフローの手動実行もしない。issue の起票・クローズ・題の変更はしない（案を報告に書く）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
GitHub Actions の予約実行（schedule）が、どのワークフローも予定より2時間半〜5時間遅れて動いている。平野さんは、Cloudflare の Worker の定時実行から GitHub のワークフローを起動する方式に変えると決めた。実装の指示を出す前に、実物（ワークフロー・デプロイの仕組み・既存の issue）を調べて、設計案と平野さんの作業の一覧を作る。

### 決定（2026-10-05、平野さん）
- 予約実行の遅れへの対処は、Cloudflare の Worker の定時実行（Cron Triggers）から GitHub のワークフローを起動する方式にする。理由: やがて時刻の正確さが問われるジョブが出る見込みで、先に備える
- 朝のジョブは、6時（JST）までに完了していること
- 起動の失敗や遅れを検知する仕組みを、別に入れる

### 前提（チャット側。平野さんの決定ではない）
- 識別子 RUN は同じチャットの RUN-01〜07 で使っている。識別子の確認でそれらのコミットやログが見つかっても、同じチャットの指示なので重複ではない
- 実測（チャット側が mj-logs の actions/status.md〈2026-10-05 10:18 JST の書き出し〉と CHAT-1001-GSC-01 のログから読んだ値。開始時刻は JST）:

| ワークフロー | 予定 | 実際の開始 |
|---|---|---|
| update-live-channel.yml（毎日） | 02:43 | 09-29 07:37・10-04 05:10・10-05 05:23 |
| sync-dojo-calendar.yml（毎日） | 07:12 | 10-03 10:07・10-04 09:32・10-05 09:50 |
| delete-merged-branches.yml（毎日） | 07:53 | 09-30 10:39・10-01 10:37・10-02 10:50・10-03 10:29・10-04 11:10 |
| sync-logs.yml（毎日、#498 で追加） | 08:29 | 10-05 は 10:18 の時点でまだ動いていない |
| check-image-links.yml（月曜） | 03:00 | 09-28 06:03・10-05 06:00 |
| check-meibo.yml（月曜） | 05:07 | 09-28 07:57・10-05 07:59 |
| sync-birthday-calendar.yml（月曜） | 05:17 | 09-21 07:27・09-28 08:04・10-05 08:11 |
| regenerate-page.yml（月曜） | 05:37 | 10-05 08:33 |
| cleanup-logs.yml（月曜） | 06:23 | 09-21 08:19・09-28 08:50・10-05 09:00 |
| check-saikyo-unregistered.yml（月曜） | 06:50 | 09-28 09:01・10-05 09:13 |
| fetch-gsc.yml（月次） | 06:00 | 10-01 09:13 |

- 原因の見立て: GitHub の公式の文書は、予約実行は負荷の高い時間帯に遅れることがあり、負荷が高ければ落とされることもあるとしている。cron の分をずらしても遅れは一律に出ており、こちらの設定では直せない（チャット側が 2026-10-05 に検索で確認。文書の今の文言は要確認）
- Cloudflare 側の事実（チャット側が検索で確認。今の値は要確認）: Cron Triggers はアカウントあたり Workers Free で5つ、Workers Paid で250。CLAUDE.md「構成」と docs/notes/cloudflare.md によると、本番反映は Workers Builds（ダッシュボードの Git 連携）で、`CLOUDFLARE_API_TOKEN` を GitHub Secret に登録しない方針（二重デプロイの防止）。Workers Builds は Free（同時に走るビルド1本）
- 設計のたたき台（チャット側の案。実物に合わなければ変えてよい。変えた理由を書く）:
  1. サイト本体とは別の Worker にする（サイトの Worker にコードを足すと `_headers` が効かなくなる。#449）
  2. cron は少ない本数にする（例: 5分おきの1本）。「どのワークフローを、何曜日の何時何分（JST）に起動するか」の表をコードの設定として持ち、時刻が来たものを GitHub の API（workflow_dispatch）で起動する。表はリポジトリに置く
  3. 検知は2層にする。層1: Worker が6時（JST）に、当日ぶんの実行が起動され成功したかを API で確かめ、未起動・失敗・実行中を通知する。層2: GitHub 側（#498 の status.md の書き出しに相乗りする案）でも同じ判定をし、Worker やトークンが止まっていても気づけるようにする
  4. 通知先は、既存の常設の通知 issue の流儀（「種類: 常設」のラベル）に合わせる
  5. 今の schedule は、保険として遅い時刻に残すか、外すかを決める（残すなら、当日すでに成功していれば何もしない作りが要る）
- 調べて、表にまとめること:
  1. 予約実行を持つ全ワークフロー: cron、`workflow_dispatch` の有無と入力、`github.event_name` や `github.event.schedule` で分岐している箇所（起動の契機が手動実行に変わると動きが変わるもの）、所要時間、ほかのワークフローとの順序の依存（同じファイルへの push がぶつかる組など）
  2. 6時（JST）までに完了させるための起動時刻の案と、Worker から起動する範囲の案（毎日のものだけか、週次・月次も含めるか）。予約実行のままでよいものがあれば理由を書く
  3. 別の Worker を同じリポジトリに置く場合の、デプロイの経路の選択肢（Workers Builds をもう1つつなぐ、ほか）と、それぞれで要る設定・Secret。push のたびに余計なビルドが走らないか（Workers Builds は同時1本）。`CLOUDFLARE_API_TOKEN` を GitHub に置かない方針との整合
  4. GitHub 側のトークン: 種類（fine-grained の PAT など）、要る権限（ワークフローの起動、通知に issue のコメントを使うならその権限）、期限と更新の手間、置き場所（Worker の Secret）。トークンが切れたときに層2で気づけるか
  5. 検知の具体案: 何を「遅れ」「失敗」とするか、通知の文面と通知先、誤報を出さないための条件（祝日や手動実行との重なりなど）。status.md（#498）への相乗りでできる範囲
  6. 費用と上限: Workers のリクエスト数・CPU 時間・Cron Triggers の本数、Actions の使用量（#298）への影響
  7. 段階の案（例: まず毎日の1〜2本で試し、問題が無ければ広げる）と、試験の方法（Worker の定時実行をどう試すか、失敗の検知をどう試すか）
  8. 平野さんの作業の一覧（トークンの発行、Cloudflare のダッシュボードでの設定、Workers のプランの確認など）。それぞれ、いつ・どの画面で・何を、まで書く
  9. 同じ論点の issue（クローズ済みとコメントの決定も含めて検索する）: #491（実行時刻の遅れ）、#448（#491 の元）、#472（週次実行の確認・再試行）、#298（Actions の使用量）、#498（status.md）、#449 など。これまでの決定とこの方式が食い違わないかを整理し、この作業を #491 の範囲を広げて扱うか、新しい issue にするかの案を書く
- #491 には、上の実測の表と、平野さんの決定（Worker から起動する方式・6時までに完了・検知を入れる）を1つのコメントにまとめる（末尾に Chat-Ref）。本文・題・ラベルは変えない
- 設計案は `## 経過` に書く。長くなるなら docs/notes/ に1ファイルで置き、ログにはその場所と要点を書く
- 決定は docs/decisions/ の合う分野のファイルに追記する
- ログは public（mj-logs）。トークンや鍵の値、非公開の URL は書かない

## 手順
1. 0章ゲート（docs/notes/branch-operations.md）と、同じ論点の issue の検索・整理（上の 9）
2. ワークフロー・デプロイの仕組み・Cloudflare の記録（docs/notes/cloudflare.md）を読み、上の 1〜8 を調べる。Cloudflare と GitHub の公式の文書で、今の上限と仕様を確かめる
3. 設計案（構成・起動時刻の表・検知・段階・平野さんの作業）と、決めてほしい点を書き出す
4. #491 にコメントし、決定を docs/decisions/ に追記して、マージする（冒頭の「マージ:」の行）

## 止まる条件
- 同じ論点の issue に、この方式と食い違う決定（例: Worker を足さない、外部のスケジューラを使わない）が既にある（設計は進めず、その決定を報告する）
- #491 が閉じている（コメントはせず、報告に書く。調査は続ける）
- 調べるのに、ワークフローの実行や Cloudflare の設定の変更が要る（やらずに、要る理由を報告に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RUN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RUN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致。
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。
- 識別子の確認: `CHAT-1005-RUN-08` のコミットは 0件。RUN の他のコミットは同じチャットの RUN-01〜07。
- `origin/work/1005-run-08` は無く、`git checkout -b work/1005-run-08 origin/cloudflare` で作成。
- ログを先行 push（1eba6133）。

### 1. 0章ゲートと同じ論点の issue（調べること 9）

- 変えるのは docs/logs・docs/decisions だけ（設計案はこのログに置いた。docs/notes/ に新しいファイルを置くと handover.md「関連文書」の表に1行足す規則があり、指示の変更の範囲に handover が無いため）。未マージのブランチとの重なりは無い
- #491（Open）: #448 から分けた「放送対局カレンダーの運用の残り」。残りの「毎朝の実行の遅れの観測（10-05 に確認）」が今回の起点。決定は「数日の実行時刻を見て、時刻をずらすか・そのままにするかを決める」（2026-09-30）で、Worker の方式と食い違わない
- #448（Open）: 09-29 のコメントで「毎朝の実行の遅れは数日観測してから判断」。食い違わない
- #472（Open）: 外部への接続の再試行。起動の方式とは別の論点。Worker から起動しても再試行の仕組みはそのまま効く
- #298（Open）: Actions の使用量。予約実行を手動実行に置き換えても実行の回数は同じ。保険の予約実行を残すと、空振りの実行が1回1分ずつ増える（下の「費用」）
- #498（Open）: status.md の書き出し。層2の検知を相乗りさせる先
- #449（closed）: Worker のコードで応答を返すと `_headers` が効かなくなる。サイトの Worker にコードを足さず、別の Worker にする理由
- 検索（#448・#298・#498・#449 の本文とコメント、docs/notes・docs/decisions・handover を「遅れ・schedule・cron・スケジューラ・Worker・dispatch」で）: **Worker を足さない・外部のスケジューラを使わない、といった決定は無い**
- 扱いの案: #491 は放送対局カレンダーの運用の issue で、範囲を広げると題と合わない。**新しい issue「予約実行を Cloudflare の Worker から時刻どおりに起動する」を起こし、#491 の「毎朝の実行の遅れの観測」の項目はその issue を指して済にする**のがよいと考える（報告の「判断が必要なこと」）

### 2. 予約実行を持つワークフロー（調べること 1）

所要時間は直近の schedule の実行（API の run_started_at〜updated_at）。「push」は cloudflare への git push の有無、「再試行」は push がぶつかったときの `git pull --rebase` の有無。

| ワークフロー | cron（UTC）→ JST | workflow_dispatch の入力 | `schedule` で変わる動き | 所要 | push |
|---|---|---|---|---|---|
| update-live-channel.yml | `43 17 * * *` → 毎日 02:43 | apply・verify・allow_many_changes・allow_shrink・allow_many・backfill・yotei_apply・calendar_max_delete・calendar_apply（9個） | schedule のとき APPLY と CALENDAR_APPLY が真、ALLOW_* は偽、SCHEDULE_ENABLED で止められる、水曜だけ verify。`scripts/sync_live_calendar.py` が `GITHUB_EVENT_NAME` で削除の上限を既定に戻す。regenerate-page.yml を workflow_call で呼ぶ | 3.5〜6分 | あり（再試行あり） |
| sync-dojo-calendar.yml | `12 22 * * *` → 毎日 07:12 | image・month・apply | schedule のとき `--auto-update`、SCHEDULE_ENABLED で止められる | 20〜30秒（失敗時 2分強） | なし |
| delete-merged-branches.yml | `53 22 * * *` → 毎日 07:53 | dry_run | 手動は入力どおり、schedule は SCHEDULE_ENABLED で dry_run を決める | 20〜50秒 | なし（ブランチの削除） |
| sync-logs.yml | `29 23 * * *` → 毎日 08:29 | なし | schedule・手動は目印に関係なく走る（#498） | 30秒 | mj-logs へ（再試行あり） |
| check-image-links.yml | `0 18 * * 0` → 月曜 03:00 | saikyo_limit | なし | 3分 | なし |
| check-meibo.yml | `7 20 * * 0` → 月曜 05:07 | dry_run | 手動は入力どおり、schedule は SCHEDULE_ENABLED | 25秒 | なし |
| sync-birthday-calendar.yml | `17 20 * * 0` → 月曜 05:17 | apply・allow_many_deletes | schedule のとき APPLY が真 | 25秒 | なし |
| sync-books-calendar.yml | `27 20 * * 0` → 月曜 05:27 | apply・allow_many_deletes・delete_all | schedule のとき APPLY が真 | — | なし（**無効化済み〈disabled_manually〉。対象外**） |
| regenerate-page.yml | `37 20 * * 0` → 月曜 05:37 | target_page | push のときは変更ページだけ、schedule・手動（空）は全ページ | 1.5分 | あり（再試行あり） |
| cleanup-logs.yml | `23 21 * * 0` → 月曜 06:23 | dry_run | 手動は入力どおり、schedule は SCHEDULE_ENABLED | 20秒 | あり（**再試行なし**） |
| check-saikyo-unregistered.yml | `50 21 * * 0` → 月曜 06:50 | dry_run | 手動は入力どおり、schedule は SCHEDULE_ENABLED | 20秒 | なし |
| fetch-gsc.yml | `0 21 28-31 * *` → 毎月 29〜1日 06:00 | commit | schedule のときだけ「JST で1日か」の判定で止め、COMMIT を真にする | 25秒（1日以外は8秒で終わる） | あり（**再試行なし**） |

- **起動の契機が `schedule` から `workflow_dispatch` に変わると、上の「schedule で変わる動き」がすべて手動の動きになる**（apply が偽になる、dry_run の既定が変わる、fetch-gsc が毎回走る、sync_live_calendar の削除の上限が変わる など）。そのため、Worker から起動するワークフローには「予約の起動である」ことを伝える入力（案: `scheduled`、真偽）を足し、`github.event_name == 'schedule'` の判定を「schedule か scheduled が真」に置き換える作業が要る（12本の YAML と `scripts/sync_live_calendar.py`）。`repository_dispatch` を使う案は、トークンに Contents の write が要り（下）、影響が大きいので採らない
- 順序の依存:
  - cloudflare へ push するのは update-live-channel（中で regenerate-page を呼ぶ）・regenerate-page・cleanup-logs・fetch-gsc。cleanup-logs と fetch-gsc は push の再試行を持たないので、ほかの push と時間を重ねない
  - sync-logs は status.md を書くので、朝のジョブの後に置くと、その朝の結果が mj-logs に出る
  - sync-dojo-calendar は「週次のジョブと重ねない」として 07:12 にした（docs/notes/dojo-guest-calendar.md）。update-live-channel の 02:43 は平野さんがシートを編集しない時間（docs/notes/live-channel-write.md）。どちらの時刻にも、ほかに強い理由の記録は無い

### 3. 起動時刻の案と範囲（調べること 2）

6:00（JST）までに完了させ、push するものの時間を離す。Worker の定時実行は5分刻みとし、起動は予定の時刻を過ぎて最初の回に行う。

| JST | ワークフロー | 曜日・日 | 所要の目安 | 備考 |
|---|---|---|---|---|
| 04:00 | update-live-channel | 毎日 | 〜6分 | 中で regenerate-page を呼んで push する |
| 04:15 | sync-dojo-calendar | 毎日 | 〜1分（失敗時3分） | |
| 04:20 | delete-merged-branches | 毎日 | 〜1分 | |
| 04:25 | check-image-links | 月曜 | 3分 | |
| 04:30 | check-meibo | 月曜 | 〜1分 | |
| 04:35 | sync-birthday-calendar | 月曜 | 〜1分 | |
| 04:40 | regenerate-page（全ページ） | 月曜 | 1.5分 | push |
| 04:50 | cleanup-logs | 月曜 | 〜1分 | push（再試行なし。前後10分あける） |
| 04:55 | check-saikyo-unregistered | 月曜 | 〜1分 | |
| 05:00 | fetch-gsc | 毎月1日 | 〜1分 | push（再試行なし）。Worker が1日だけ起動するので、YAML の「1日か」の判定は不要になる |
| 05:30 | sync-logs | 毎日 | 30秒 | 朝の結果を status.md に出す |
| 06:00 | 検知（層1） | 毎日 | — | Worker 自身が行う |

- 範囲の案: **毎日・週次・月次のすべてを Worker から起動する**（どれも 6:00 までに終わらせる対象のため）。予約実行のままでよいものは、今は無い。強いて言えば delete-merged-branches は時刻に意味が薄いが、「24時間より前」の判定があるので、毎日同じ時刻に動くほうがよい
- 起動の窓が重なっても、GitHub の実行は並列に走る（同じ concurrency の組は1本ずつ）。所要が短いので、実際の完了は 05:05 ごろ（sync-logs を除く）。5:00〜6:00 は、Worker の遅れや再起動の余白にする
- 予約実行（schedule）の扱い（たたき台の5）: 案A 外す（二重の実行が無い。Worker が止まると何も動かないが、層2で気づける）／案B 遅い時刻（例 09:00 JST）に残し、当日にすでに成功していれば何もしないゲートを足す（保険になるが、空振りの実行が1回1分の課金で月約150分増える〈#298〉）。**試験の段階は案B、移行が済んだら案Aを推す**

### 4. 別の Worker の置き場所とデプロイの経路（調べること 3）

- 構成: リポジトリに `workers/scheduler/`（Worker のコード・`wrangler.jsonc`・起動の表）を置く。**サイトの Worker は `assets.directory` が `./` なので、`.assetsignore` に `workers` を足さないとコードが公開される**（実装の作業に入れる）
- 起動の表: `workers/scheduler/schedule.json`（ワークフローのファイル名・曜日または日・JST の時刻・有効か）。Worker はビルド時に取り込み、層2（Python）も同じファイルを読む
- デプロイの経路の選択肢:

| 案 | 内容 | 要る設定・Secret | push のたびのビルド | `CLOUDFLARE_API_TOKEN` を GitHub に置かない方針 |
|---|---|---|---|---|
| A（推す） | Workers Builds でもう1つ同じリポジトリをつなぐ（Root directory `workers/scheduler`） | ダッシュボードで新しい Worker を作りリポジトリを接続、Root directory・Build watch paths（Include `workers/scheduler/*`）、Worker の Secret に GitHub のトークン | watch paths で scheduler の変更の時だけビルド。サイトの Worker の watch paths に Exclude `workers/*` を足せば、scheduler の変更でサイトのビルドも走らない。Workers Builds は同時1本なので、両方が走るときは待つ | 合う（GitHub にトークンを置かない） |
| B | ダッシュボードのエディタにコードを貼って保存（Git 連携なし） | Worker の Secret、cron はダッシュボードの Triggers | 走らない | 合う。ただしコードと表の版がリポジトリと食い違いうる（表を変えるたびに平野さんが貼る） |
| C | GitHub Actions から `wrangler deploy` | `CLOUDFLARE_API_TOKEN` を GitHub Secret に | — | **合わない（禁止事項）** |

- 補足: Workers Builds の接続は、平野さんのアカウントに紐づく User API Token を自動で作る（`mj build token` と同じ。docs/notes/cloudflare.md「APIトークンの棚卸し」）。接続を足すとトークンが1本増える見込み（棚卸しの表に足す）
- Cron Triggers は scheduler の `wrangler.jsonc` の `triggers.crons` に書けば、デプロイで反映される（変更の反映に最大15分）

### 5. GitHub 側のトークン（調べること 4）

- 種類: **fine-grained の personal access token**（対象リポジトリを retroeater/mj だけにする）
- 権限（GitHub の文書の fine-grained の権限表による）:
  - `POST /repos/{owner}/{repo}/actions/workflows/{workflow_id}/dispatches`（ワークフローの起動）→ **Actions: write**
  - 実行の一覧（検知）→ Actions: read（write に含まれる）
  - 通知に issue のコメントを使う → **Issues: write**
  - Metadata: read は自動
  - 参考: `repository_dispatch` は **Contents: write** が要る（コードを書き換えられる権限）ので採らない
- 期限: fine-grained の PAT は期限なしも選べる（組織の方針が無い個人のアカウントの場合）。1〜366日の期限も選べる。**案: 366日の期限にし、切れる1か月前にカレンダーで知らせる**（期限なしは漏れたときの害が長い）
- 置き場所: scheduler の Worker の Secret（Settings > Variables and Secrets）。リポジトリ・ログには書かない
- 切れたとき: Worker の起動も層1の通知も止まる（同じトークン）。**層2（GitHub の `GITHUB_TOKEN` で動く）が「今日の起動が無い」を出すので気づける**。Worker のエラーはダッシュボードの Metrics にも出る

### 6. 検知の具体案（調べること 5）

- 判定の単位: 表の各行（ワークフロー × その日）
  - 「起動済み」: そのワークフローの実行のうち、当日（JST）に作られ、`scheduled` が真の workflow_dispatch（移行前は schedule）のものがある
  - 「遅れ」: 予定の時刻＋15分を過ぎても起動済みが無い
  - 「失敗」: 起動済みの最後の実行の conclusion が success 以外（failure・cancelled・timed_out）
  - 「実行中」: 6:00 の時点で status が completed でない
- 層1（Worker、毎日 06:00 JST）: 表の当日分を API で確かめ、すべて success なら何もしない。それ以外は通知 issue に1件のコメント（表: ワークフロー・予定・起動の時刻・結論・run の番号）。起動の時点で API が 4xx・5xx を返したら、その場で通知（トークン切れ・ワークフローの無効化・入力の誤りが分かる）
- 層2（GitHub、`scripts/actions_status.py` に相乗り）: status.md に「今日の予定と起動」の表を足し、同じ判定をする。sync-logs の実行のたび（push と毎日）に書き、遅れ・失敗があれば通知 issue にコメントする（sync-logs に `issues: write` を足す）。**Worker やトークンが止まっても、GitHub 側から気づける**。ただし sync-logs も予約実行で動く間は遅れる（移行後は Worker から 05:30 に起動）。Worker が止まると sync-logs も起動されないので、**層2の判定は push の契機（ログの写し）でも走る**ようにする。sync-logs の予約実行は保険として残す案もある（遅れても1日1回は動く）
- 通知先: 新しい常設の issue「予約実行の起動」（ラベル「種類: 常設」「分野: 自動化」。既存の常設は #426・#475・#481・#352・#357 の流儀）
- 文面の案: 「YYYY-MM-DD の朝の予約実行: 未起動 1・失敗 1・実行中 0（予定 7）」＋表＋「再実行は Actions の画面か、平野さんの判断で手動実行」
- 誤報を避ける条件: 表で無効にした行は見ない／ワークフローが無効化（state が active でない）なら「無効」と書いて失敗にしない／同じ日に手動で成功させた実行（`scheduled` 無し）があれば、遅れの通知に「手動で成功済み」と添える／祝日で変わるジョブは今は無い／ジョブ自身が別の issue に失敗を知らせるもの（道場部 → #426 など）も、層1の表には出す（重複になるが一覧性を優先）
- status.md でできる範囲: 実行の一覧は今も直近5回を書いている。それに表の当日分の判定を足すだけで、API の追加の呼び出しは少ない

### 7. 費用と上限（調べること 6。Cloudflare の公式の Limits のページ、2026-10-05 取得）

| 項目 | Workers Free の上限 | この方式の使用 |
|---|---|---|
| リクエスト | 100,000/日 | 定時実行 288回/日（5分おき）＋少し |
| CPU 時間 | 1回 10ms（定時実行も同じ） | 表の判定と JSON の組み立てだけ。待ち（fetch）は数えない。足りる見込み |
| サブリクエスト | 1回 50 | 起動の回: 起動 1〜3件＋確認 1〜3件。検知の回: ワークフロー数（〜12）＋通知 1。足りる |
| Cron Triggers | アカウントあたり 5（Paid は 250） | 1本（`*/5 * * * *`）。06:00 の検知も同じ cron の中で時刻で分ける。サイトの Worker は cron を持たない（`wrangler.jsonc` に triggers 無し） |
| Workers Builds | 月 3,000分・同時1本 | scheduler のビルドは表やコードを変えたときだけ（watch paths） |

- Actions の使用量（#298）: 起動の契機が変わるだけで、実行の回数・時間は変わらない。予約実行を保険に残す案Bでは、空振りの実行（1回10秒前後 → 1分に切り上げ）が毎日4本＋週6本で月約150分増える。層2を sync-logs に相乗りさせる分は数秒

### 8. 段階と試験（調べること 7）

1. 準備（平野さん）: Workers のプランと Cron Triggers の数の確認、トークンの発行（下の9）
2. 段階1: scheduler の Worker を作り、**delete-merged-branches だけ**を表に入れる（入力 `scheduled` を足す。二重に動いても害が無い。予約実行はそのまま）。層1の検知も入れる。数日、起動の時刻と通知を見る
3. 段階2: 毎日の3本（update-live-channel・sync-dojo-calendar・sync-logs）を移す。YAML の `schedule` 判定を置き換え、予約実行は遅い時刻のゲート付きに（案B）
4. 段階3: 週次・月次を移す。層2を status.md に足す
5. 段階4: 安定したら予約実行を外す（案A）
- 試験の方法:
  - Worker の定時実行: ローカルで `wrangler dev` を動かし `/cdn-cgi/local/scheduled?cron=*/5+*+*+*+*&time=<時刻>` で、時刻を変えて呼ぶ（公式の Cron Triggers のページ）。表の判定は単体テスト（JS）。本番では、表に「数分後の時刻」の行を一時的に足して確かめる
  - 起動の失敗: 表に無効化済みのワークフロー（sync-books-calendar）か存在しない入力の行を入れて、API の 4xx が通知されるか
  - 遅れ・未起動の検知: 表の行を Worker からは起動しない設定（有効フラグ）にして、層1・層2が「未起動」を出すか
  - トークン切れ: 期限の短い試験用のトークンで、層2が気づくか
- 注意: クラウドのセッションからは `wrangler` が無く、Cloudflare のダッシュボードも見られない。Worker の設定・ログの確認は平野さんの作業になる（ビルドの成否は check-run「Workers Builds: <名前>」で見られる見込み）

### 9. 平野さんの作業の一覧（調べること 8）

| いつ | どこで | 何を |
|---|---|---|
| 実装の前 | Cloudflare ダッシュボード > Workers & Pages > プラン（Plans） | Workers のプランが Free かを確かめる（`scripts/check_asset_limits.py` の注記では 09-28 に Free と申告） |
| 実装の前 | Cloudflare ダッシュボード > Workers & Pages > 各 Worker > Settings > Triggers | アカウントの Cron Triggers の数を確かめる（Free は5本まで。今は0本の見込み） |
| 実装の前 | GitHub > Settings > Developer settings > Personal access tokens > Fine-grained tokens > Generate new token | 名前（例 mj-scheduler）、Resource owner は retroeater、Repository access は Only select repositories で mj、Permissions は Actions: Read and write・Issues: Read and write、期限 366日。値はどこにも貼らず、次の手順まで手元に |
| 実装の前 | Google カレンダーなど | トークンの期限の1か月前に知らせる予定 |
| 実装のマージ後 | Cloudflare ダッシュボード > Workers & Pages > Create > Import a repository | retroeater/mj を選び、Worker の名前（例 mj-scheduler）、Production branch は cloudflare、Root directory は `workers/scheduler`、Deploy command は既定のまま |
| 同上 | 新しい Worker > Settings > Build > Build watch paths | Include `workers/scheduler/*` |
| 同上 | 新しい Worker > Settings > Variables and Secrets | Secret を1つ足す（名前は実装で決める。例 GITHUB_TOKEN）。値は上のトークン |
| 同上 | 既存の Worker（mj）> Settings > Build > Build watch paths | Exclude に `workers/*` を足す（今の Exclude は `docs/**`） |
| 同上 | Cloudflare ダッシュボード > My Profile > API Tokens | 接続で増えたトークンを確かめ、docs/notes/cloudflare.md「APIトークンの棚卸し」に足すよう Claude Code に頼む |
| 段階ごと | 通知の issue | 数日分の通知と起動の時刻を見て、次の段階に進むかを決める |
| 毎年 | GitHub のトークンの画面 | 期限の前にトークンを作り直し、Worker の Secret を差し替える |

### 10. 公式の文書で確かめたこと（2026-10-05）

- GitHub（github/docs のソース。docs.github.com はセッションのプロキシに拒否されるため、raw.githubusercontent.com の github/docs から読んだ）:
  - `schedule` の注記: 「The `schedule` event can be delayed during periods of high loads of GitHub Actions workflow runs. High load times include the start of every hour. If the load is sufficiently high enough, some queued jobs may be dropped.」予約実行は既定ブランチでだけ動く
  - `workflow_dispatch` は、ワークフローのファイルが既定ブランチにあるときだけ受け付ける
  - fine-grained の PAT の権限: workflow の dispatches は Actions の write、repository dispatch は Contents の write
  - fine-grained の PAT の期限: 1〜366日か、期限なし（組織の方針で禁止されていなければ）
  - `workflow_dispatch` の入力の数の上限は、今回読んだ範囲の文書では見つからなかった（未確認）
- Cloudflare（developers.cloudflare.com）: Limits（Free: 100,000リクエスト/日・CPU 10ms・サブリクエスト 50・Cron Triggers 5/アカウント）、Cron Triggers（UTC で動く、変更の反映に最大15分、`wrangler dev` の `/cdn-cgi/local/scheduled` で試せる）、Workers Builds の Build watch paths（Include・Exclude、20コミット以上か3000ファイル以上の push は判定を飛ばして必ずビルド）、Builds の設定の Root directory
- 決定を docs/decisions/automation.md（新設）に書き、docs/decisions/README.md の分野の一覧に1行足した
- マージ: push 前の再 fetch で cloudflare は進んでおらず（祖先を確かめた）、c78734e4 を cloudflare へ push

## 報告

- 状態: 完了
- ブランチ: work/1005-run-08
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1005-RUN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-run-08
- 確認用URL: なし（docs のみ）
- マージ: 済（c78734e4。work/1005-run-08 の先頭を fast-forward で cloudflare へ push）
- issue: #491 にコメント（実測の表と平野さんの決定）。起票・クローズ・題の変更はしていない
- 判断が必要なこと:
  - この作業の issue: 新しい issue「予約実行を Cloudflare の Worker から時刻どおりに起動する」を起こし、#491 の「毎朝の実行の遅れの観測」はそれを指して済にする案（#491 は放送対局カレンダーの運用の issue で、範囲を広げると題と合わない）。設計案（経過 2〜9）は、起こす issue の本文に移す（ログは定期削除の対象のため）
  - 起動の契機の伝え方: Worker からは `workflow_dispatch` に入力 `scheduled`（真偽）を付けて起動し、12本の YAML と `scripts/sync_live_calendar.py` の `schedule` の判定を「schedule か scheduled」に置き換える案（`repository_dispatch` はトークンに Contents の write が要るので採らない）
  - 起動時刻の表（経過 3）: 04:00 から5分刻みで並べ、push するもの（update-live-channel・regenerate-page・cleanup-logs・fetch-gsc）の時間を離す。update-live-channel を 02:43 から 04:00 に、sync-dojo-calendar を 07:12 から 04:15 に早める（どちらも元の時刻に強い理由の記録は無い）。この時刻でよいか
  - 予約実行（schedule）の扱い: 試験の段階は遅い時刻にゲート付きで残し（月約150分の空振り。#298）、移行後は外す案
  - デプロイの経路: Workers Builds でもう1つつなぐ案A（Root directory `workers/scheduler`、Build watch paths で分ける）。`.assetsignore` に `workers` を足すことが必須（サイトが `./` を配信しているため）
  - トークン: fine-grained の PAT（mj だけ、Actions: Read and write・Issues: Read and write、366日）。期限なしにするか
  - 通知先: 新しい常設の issue「予約実行の起動」を作るか、既存の常設の issue に寄せるか
  - 段階: delete-merged-branches の1本から始める案（経過 8）
- 未確認の項目:
  - Workers のプランが Free であることと、アカウントの Cron Triggers の数（ダッシュボード。平野さんの作業）
  - Cloudflare の Cron Triggers 自体の遅れ（公式の文書に保証の記述は無い。段階1で実測する）
  - `workflow_dispatch` の入力の数の上限（update-live-channel は今9個で、1個足すと10個）
  - Workers Builds を2つ目につないだときのトークンの増え方と、check-run の名前
- エラー:
  - docs.github.com への接続がセッションのプロキシに拒否された（organization policy）。GitHub の文書は github/docs のソース（raw.githubusercontent.com）で読んだ

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0c1ee22）: https://github.com/retroeater/mj-logs/tree/main/guide/d0c1ee22

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0c1ee22/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
