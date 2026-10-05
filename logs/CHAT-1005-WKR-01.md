# CHAT-1005-WKR-01

- 着手日時: 2026-10-05
- 対象issue: #504
- ブランチ: work/1005-wkr-01
- 着手時HEAD: 未取得（`git rev-parse --short HEAD` が分類器に拒否された。#493）。origin/cloudflare から作成

## 指示

【Claude作成】Claude Code 向け指示：#504 段階1（予約実行を起動する Worker を workers/scheduler/ に作り、delete-merged-branches の1本を起動の表に入れる。入力 scheduled の追加と、通知用の常設 issue の作成を含む） Chat-Ref: CHAT-1005-WKR-01 マージ: 承認済み（チャットで）。ただし「止まる条件」のどれかに当たったら、マージせず判断待ちで止まる 貼る時機: いつでも（結果を前提にする実行中の指示は無い） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1005-wkr-01〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-wkr-01 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1005-wkr-01 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-wkr-01 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: `workers/scheduler/`（新設）、`.assetsignore`、`.github/workflows/delete-merged-branches.yml`（`scripts/delete_merged_branches.py` が起動の契機を見ていれば、そこも）、docs/notes/・docs/handover.md・docs/decisions/・docs/logs/、issue の操作（常設 issue の起票1件、#504 へのコメント）。サイトの `wrangler.jsonc`・ほかのワークフロー・生成物・CLAUDE.md は変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#504「予約実行を Cloudflare の Worker から時刻どおりに起動する」の段階1。Worker のコードと起動の表をリポジトリに置き、delete-merged-branches.yml を Worker から「予約の起動」として起動できる形にする。 Worker をダッシュボードでつなぐ作業とトークンの登録は、マージの後に平野さんが行う。この指示のマージだけでは Worker は動き出さない。
決定（2026-10-05、平野さん）

* この指示のマージは承認済み（止まる条件つき）
* 次は #504 の本文と docs/decisions/automation.md に記録済みの決定で、この指示もそれに従う: 起動の契機は `workflow_dispatch` の入力 `scheduled`（真偽）で伝える（`repository_dispatch` は採らない）／デプロイは Workers Builds をもう1つつなぐ／通知先は新しい常設の issue「予約実行の起動」で、段階1で作る／delete-merged-branches の1本から始める／今の予約実行（`schedule`）は試験の間は残す／トークンは fine-grained の PAT（mj だけ、Actions と Issues の読み書き）

前提（チャット側。平野さんの決定ではない）

* 識別子 WKR はこのチャットで初めて使う（mj-logs の chat-ids/b89c3b19 の一覧に無いことをチャット側で確かめた。受け手側の確認は省かない）
* 設計の正は #504 の本文。チャット側は #504 を読めないため、同じ内容とされる CHAT-1005-RUN-08 のログ（経過 2〜9）と CHAT-1005-RUN-10 のログで確かめて書いた。#504 の本文を読み、その「決定」「必ず守ること」とこの指示が食い違えば止まる。 ファイル名・関数の分け方などの細部は、実物に合わせて変えてよい（変えた点は報告に書く）
* 着手時に docs/notes/branch-operations.md「ワークフローを変更したとき」と docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」を読む
* 作るもの（案）:
   * `workers/scheduler/wrangler.jsonc`: `name` は `mj-scheduler`（平野さんがダッシュボードで作る Worker の名前と同じにする。名前が違うと Workers Builds のビルドがどうなるかは要確認: 公式の文書）。`triggers.crons` は `*/5 * * * *` の1本。`assets` は持たない。応答を返す入口（fetch）は作らず、`workers.dev` の公開 URL も要らない（切る設定は要確認: 公式の文書）
   * `workers/scheduler/schedule.json`: 起動の表。1行は、ワークフローのファイル名・曜日または日（毎日は指定なし）・JST の時刻・有効か。段階1は `delete-merged-branches.yml`・毎日・04:20・有効 の1行だけ。週次・月次の行を後で足せる形にする
   * Worker のコード: 外部のパッケージを足さない（npm の依存なし）。判定（この回に起動する行、当日の行の状態、通知の文面）は、時刻と `fetch` を引数で受ける関数に分け、Node の標準のテスト（`node --test`）で確かめる。テストでは GitHub の API を呼ばない（偽の `fetch` を渡す）
   * 起動: 定時実行の予定時刻（`scheduledTime`）を JST に直し、その回の5分の窓に時刻が入る有効な行を、`POST /repos/retroeater/mj/actions/workflows/<ファイル名>/dispatches`（ref は `cloudflare`、inputs は `scheduled` を真）で起動する。同じ行を二重に起動しない。取りこぼした回の埋め合わせは段階1では入れない（下の検知に「未起動」で出る）
   * 起動の API が 2xx 以外を返したら、その場で通知用の issue にコメントする
   * 検知（#504 の「層1」）: 06:00 JST の回に、当日の有効な行（予定が 06:00 より前のもの）ごとに実行の一覧を引き、未起動（予定＋15分を過ぎても無い）・失敗（結論が success 以外）・実行中を判定する。すべて success なら何もしない。それ以外は通知用の issue に1件コメントする（表: ワークフロー・予定・起動の時刻・結論・run の番号）
   * 「`scheduled` が真の起動」を実行の一覧から見分ける方法は要確認（一覧の API に入力は出ない見込み）。案: ワークフローに `run-name` を足し、`scheduled` が真のとき題に目印を入れて、API の `display_title` で見分ける。公式の文書と実物で確かめて決め、決めた方法を報告に書く。mj-logs の `actions/status.md` の出力（`scripts/actions_status.py`）は変えない
   * トークンは Worker の Secret から読む（名前の案: `GITHUB_TOKEN`）。無いときは起動も通知もできないので、理由を `console.error` に書いて終わる。通知用の issue の番号・リポジトリ・ref は秘密でないので設定に置く。GitHub の API に要るヘッダ（User-Agent など）は公式の文書で確かめる
   * `wrangler` は入れない。`wrangler deploy` もしない（CLAUDE.md「禁止事項」）。定時実行の入口（scheduled ハンドラ）の通しの確認は、平野さんが Worker をつないだ後の最初の回で行うので、「未確認の項目」に書く
* `.assetsignore` に `workers` を足す（サイトの Worker は `assets.directory` が `./`。#504 の「必ず守ること」）。`workers/` を足すコミットと同じか、それより前のコミットに入れる（作業ブランチのプレビューにコードを出さないため）
* delete-merged-branches.yml:
   * 今の作りの見込み（RUN-08 のログの経過「2」の表）: 入力は `dry_run`。手動実行は入力どおり、`schedule` は `SCHEDULE_ENABLED` で dry-run かを決める。実物で確かめ、違えば止まる
   * 入力 `scheduled`（真偽、既定は偽。説明に「Worker からの予約の起動。手では付けない」）を足し、`schedule` の契機を見ている判定を「`schedule` か、`scheduled` が真」に置き換える。`schedule:` の行（今の予約実行）は変えない
   * `inputs.scheduled`（真偽）と `github.event.inputs.scheduled`（文字列）は型が違う見込み（要確認: 公式の文書）。判定の式は実行して確かめる
   * `SCHEDULE_ENABLED` の今の値: docs/notes/static-generation.md「ワークフローを手動実行するとき」は「現在 `'false'`（毎日の実行は dry-run）」、docs/notes/cloud-sessions.md「ブランチの削除」は「毎日削除する」と書いている（どちらも mj-logs の guide/86229eca の版）。実物で確かめ、食い違っている側の文書を直す
   * マージの前に、作業ブランチ（ref は work/1005-wkr-01）で2回手動実行して確かめる。入力は文字列で渡す。待つのは1回15分まで
      * (a) `scheduled` なし・`dry_run` は既定 → 見込み: dry-run で動く（これまでの手動実行と同じ）
      * (b) `scheduled` を真 → 見込み: 予約実行と同じ分岐（`SCHEDULE_ENABLED` の値どおり）。値が `'true'` なら、毎朝と同じ削除（マージ済みで先頭が24時間より前の `work/*`）が実際に起きる。毎朝の実行と同じ動きなので、これは許す。消えたブランチ名と先頭の SHA をログに書く
      * どちらの分岐に入ったかは、ジョブのログで確かめる
* 通知用の常設 issue「予約実行の起動」を起票する（ラベル「種類: 常設」「分野: 自動化」。ラベルの実在は要確認）。本文には、何が書き込まれるか（未起動・失敗・実行中、起動の API の失敗）、読み方、気づいたときにすること、起動の表の場所、#504 との関係を書く。起票の前に、同じ目的の issue（Open・Closed）が無いかを確かめる
* delete-merged-branches の結果を知らせる常設 issue があれば（要確認）、今回の変更で本文の説明が古くならないかを確かめ、古くなるなら直す
* 文書:
   * docs/notes/ に Worker の文書を1つ足す（名前の案: `scheduler-worker.md`）。構成、起動の表の直し方、テストの実行、通知の読み方、トークンの期限と差し替え、平野さんがマージの後に行う作業（#504 の本文の一覧のうちマージ後の分を、決まった Worker の名前・Secret の名前で書き直す。新しい Worker は「Builds for non-production branches」を切る案も添える）
   * docs/handover.md「7. 関連文書」に1行足す（上限 28KB・警告域 26KB。直す前後のバイト数をログに書く）
   * docs/notes/static-generation.md「ワークフローの一覧」「ワークフローを手動実行するとき」の delete-merged-branches の記述を直す
   * docs/notes/cloudflare.md に、2つ目の Worker があることと新しい文書への参照を1〜2行足す（設定値の表は、平野さんがつないだ後の指示で書く）
   * CLAUDE.md に足すべき規則があると考えたら、足さずに案を報告に書く
   * docs/decisions/automation.md に、この指示の決定（マージの承認）を足す
* #504 に着手中のコメントを残し、完了時に段階1の実装が入ったことをコメントする（末尾に Chat-Ref）。#504 は閉じない
* ログは public（mj-logs）。トークンや鍵の値、プレビューの URL は書かない

手順

1. 0章ゲートと確かめ: #504 が Open で、本文の決定がこの指示と合うこと。同じ目的の issue と未マージの `work/` ブランチが無いこと。delete-merged-branches.yml の今の作りと `SCHEDULE_ENABLED` の値。食い違えば止まる
2. 常設 issue を起票し、Worker（コード・表・テスト）と `.assetsignore`、delete-merged-branches.yml の変更、文書を作る
3. マージの前の確認をすべて行い、通ればマージして、マージの後の確認を行う
   * マージの前: (1) `node --test` が通る。既存の `python3 -m unittest discover -s scripts/tests` も通る (2) 作業ブランチへの push で動いた assets-check.yml が success で、配信される最上位の項目に `workers` が出ない (3) 上の手動実行 (a)(b) が見込みどおり
   * マージの後: cloudflare への push でサイトの Workers Builds が1回走る見込み（表示は変わらない）。check-run「Workers Builds: mj」が success になることを確かめる（待つのは15分まで。超えたら、その時点の状態を書いて「未確認の項目」に回す）。本番で `workers/scheduler/` の下のファイルが公開されていないこと（存在しないパスと同じ応答になること）を確かめる。公開されていたら、報告の「エラー」の先頭に書く

止まる条件
どれかに当たったら、マージせず、判断待ちで報告する。

* #504 が閉じている、または本文の決定・「必ず守ること」がこの指示と食い違う
* 同じ目的の issue（通知用の常設 issue の重複を含む）や、未マージの `work/` ブランチの作業がある
* delete-merged-branches.yml の今の作りが前提と違い、置き換え方に判断が要る
* `node --test` を実行できない、またはテストが通らない
* 作業ブランチでの手動実行ができない（起動が拒否される・15分待っても終わらない）、または (a)(b) の結果が見込みと違う。手動実行ができないときは、平野さんが GitHub の画面の「Run workflow」で実行できるように、選ぶブランチと入力を報告に書く
* assets-check.yml が失敗する、または `workers` が配信される項目に出る
* docs/handover.md が直した後に警告域（26KB）を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。次も書く: Worker の名前、Secret の名前、通知用の issue の番号、起動の表の中身、手動実行 (a)(b) の run の番号と結果、`scheduled` の起動を見分ける方法、平野さんがマージの後に行う作業が書いてある文書と節の名前
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-WKR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-WKR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

1. Chat-Ref・識別子の確認: `git fetch --unshallow origin` の後、`git log --all --grep="WKR"` と `docs/logs/*WKR*` の履歴は0件。リモートに `work/1005-wkr-01` は無く、ローカルにも無い → `git checkout -b work/1005-wkr-01 origin/cloudflare` で作成
2. 指示欄の末尾の確認（手順0）: 指示欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
3. 着手時HEAD の取得（`git rev-parse --short HEAD`）が分類器に拒否された（理由: Modify Shared Resources。#493 と同じ症状）。HEAD の SHA を得る別の手段は試していない
4. 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
5. 0章ゲート
   - #504 は Open。本文の「決定」「必ず守ること」（`.assetsignore` に `workers`、常設 issue を段階1で作る、`CLOUDFLARE_API_TOKEN` を置かない）と指示は合う。コメントは0件だった → 着手中のコメントを出した
   - 同じ目的の issue: 「予約実行の起動」「Worker」「起動の失敗・遅れの検知」で検索し、#504・#505・#503 だけ。delete-merged-branches の結果を知らせる常設 issue は無い（「種類: 常設」の Open は #481・#475・#426・#357・#352）
   - 未マージのブランチ: `git branch -r --no-merged origin/cloudflare` は `origin/work/1005-wkr-01` だけ（着手前の確認で見えた `work/1002-cld` は fetch の後にマージ済みになっていた）
   - ラベル「種類: 常設」「分野: 自動化」は実在する
   - delete-merged-branches.yml の今の作り: 入力 `dry_run`（真偽、既定 true）。手動は入力どおり、schedule は `SCHEDULE_ENABLED` で決める。前提どおり。`scripts/delete_merged_branches.py` は契機を見ていない（`schedul`・`event` の grep で0件）
   - `SCHEDULE_ENABLED` は **`'true'`**。docs/notes/static-generation.md「ワークフローを手動実行するとき」の「現在 `'false'`」が古い（cloud-sessions.md の「毎日削除する」が正しい）→ static-generation.md を直す
6. 公式の文書で確かめたこと（docs.github.com はプロキシで拒否されたので、`raw.githubusercontent.com/github/docs` の原稿で読んだ）
   - Workers Builds: wrangler の `name` がダッシュボードの Worker の名前と違うと「The name in your Wrangler configuration file (…) must match the name of your Worker」でビルドが失敗する
   - `workers_dev`: 「scheduled のイベントだけの Worker なら false にできる」。`workers_dev` を切っても Preview URLs は切れない → `preview_urls: false` も書く（省略時は workers_dev に従う、と wrangler の設定の文書）
   - scheduled ハンドラ: `controller.scheduledTime`（ms）・`controller.cron`
   - GitHub: `inputs` コンテキストは真偽を真偽のまま保ち、`github.event.inputs` は文字列にする。`workflow_dispatch` の入力は最大 25 個（#504 の未確認の「入力の数の上限」が分かった。update-live-channel は 10 個にしても足りる）
   - 起動の API: Actions: write。204（`return_run_details` が真なら 200 と run の ID）。REST の全リクエストに User-Agent が要る
   - `run-name` は `github` と `inputs` のコンテキストを使える。空なら契機ごとの既定の題
   - 実行の一覧の API の `created` に `>=2026-10-03T15:00:00Z` を渡すと、その時刻より後だけが返った（実物で確かめた。delete-merged-branches の schedule の実行 #12〈2026-10-05T01:19:19Z〉・#11）
7. 予約の起動の見分け方: `run-name: ${{ inputs.scheduled && '[scheduled] マージ済みの作業ブランチを削除する' || '' }}` にし、Worker は `event` が `workflow_dispatch` で `display_title` が `[scheduled]` で始まるものを予約の起動とみなす。`scripts/actions_status.py` は display_title を書かないので status.md は変わらない
8. 常設 issue #506「予約実行の起動」を起票（ラベル 種類: 常設・分野: 自動化）
9. Worker の実装（`workers/scheduler/`）: `wrangler.jsonc`（name `mj-scheduler`、cron `*/5 * * * *`、`workers_dev`・`preview_urls` は false、vars に GITHUB_REPOSITORY・DISPATCH_REF・NOTIFY_ISSUE=506）、`schedule.json`、`src/index.mjs`（入口）、`src/scheduler.mjs`（判定と API）、`test/scheduler.test.mjs`
   - 指示の案から変えた点: ファイルは `.mjs`（`package.json` を置かずに Node でも ESM として読むため）。起動の窓は「前の回より後〜この回まで」で、00:00 の回は前の日の 23:56〜 の予定を拾う。朝の確かめで、予定から15分以内で起動が無い行は「起動待ち」、API が失敗した行は「確認できず」として通知する。起動の API につながらなかったときも通知する。行のキーは `weekdays`（0=日曜）・`monthdays`
   - `node --test 'workers/scheduler/test/*.test.mjs'`: 16件すべて通過。Node 22 では `node --test workers/scheduler/test/`（ディレクトリ）は MODULE_NOT_FOUND で失敗する
   - 束ねの確認: scratchpad に esbuild を入れ（リポジトリには入れない）、`src/index.mjs` を束ねて、偽の fetch で `scheduled` を3回呼んだ。04:20 の回は dispatch 1件（ref cloudflare、inputs `{"scheduled":true}`）、06:00 の回は一覧→ワークフローの状態→#506 へのコメント（偽の応答で未起動）、Secret なしは `console.error` だけ
   - `python3 -m unittest discover -s scripts/tests`: 546件 OK
10. delete-merged-branches.yml: 入力 `scheduled`（真偽、既定 false）と `run-name` を足し、判定を「workflow_dispatch で scheduled が真でない → 入力どおり、それ以外 → SCHEDULE_ENABLED」にした。`schedule:` の行は変えていない
11. コミット `f153aab5`（`.assetsignore` に `workers`、`workers/scheduler/`、ワークフロー）を push
    - assets-check.yml run 2847（job 111704750655）: success。「除外後に配信される最上位の項目」に `workers` は無い
    - 手動実行 (a) run #13（id 37292138645、入力なし）: success。ログ `INPUT_DRY_RUN: true`・`INPUT_SCHEDULED: false`・`DRY_RUN: true`。題は既定の「マージ済みの作業ブランチを削除する」。`work/1003-vid-01` を「削除対象（dry-run）」と出しただけ
    - 手動実行 (b) run #14（id 37292147782、`scheduled` を `"true"`）: success。ログ `INPUT_SCHEDULED: true`・`DRY_RUN: false`（予約実行と同じ分岐。SCHEDULE_ENABLED が 'true'）。題は `[scheduled] マージ済みの作業ブランチを削除する`。**削除: `work/1003-vid-01` 3dfac105fe16879fee6a4592e39ae7f0d5f35833（マージ済み、先頭から26.4時間）**。ほかは猶予中・未マージで残した
    - 文字列の `"true"` で渡した入力が真偽の入力として通り、`${{ inputs.scheduled }}` は `true`/`false` で env に入った
12. 文書: `docs/notes/scheduler-worker.md` を新設。handover.md「7. 関連文書」に1行（24380 → 24584 バイト、警告域 26624 の内側）。static-generation.md の「ワークフローの一覧」「ワークフローを手動実行するとき」、cloudflare.md の「本番反映（デプロイ）の仕組み」に1行、decisions/automation.md に決定

## 報告

- 状態: 作業中
- ブランチ: work/1005-wkr-01
- ログ: https://github.com/retroeater/mj/blob/work/1005-wkr-01/docs/logs/CHAT-1005-WKR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-wkr-01
- 確認用URL: なし
- マージ: 未
- issue: #504
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー:
  - 着手時HEAD の取得（`git rev-parse --short HEAD`）が分類器に拒否された（#493）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 64aa604f）: https://github.com/retroeater/mj-logs/tree/main/guide/64aa604f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/64aa604f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1faab674.md
