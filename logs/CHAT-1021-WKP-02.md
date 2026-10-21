# CHAT-1021-WKP-02

- 着手日時: 2026-10-21
- 対象issue: #564, #504
- ブランチ: work/1021-wkp
- 着手時HEAD: 857dc254e

## 指示

【Claude作成】Claude Code 向け指示：週次・月次の定期実行を run-weekly.yml・run-monthly.yml に束ね、Worker の起動の表から起動する（#564 の段④の前半） Chat-Ref: CHAT-1021-WKP-02 マージ: 承認済み（チャットで） 貼る時機: CHAT-1021-WKP-01 のマージの後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1021-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。「貼る時機」の前提（CHAT-1021-WKP-01 が cloudflare にマージ済み）を確かめ、満たされなければ何もせず止まる。

目的
GitHub の `schedule` に残る週次・月次のワークフローは予定より 2.5〜5 時間遅れる（#491）。段④（docs/decisions/automation.md、#564）のとおり Worker `mj-scheduler` の起動の表から時刻どおり起動する。Workers Paid で cron 1 回の CPU 時間が 10 ms から 30 秒になり、起動の表の行を増やしても余裕があるため前倒しする。段③（run-daily.yml）と同じ形で、週次は `run-weekly.yml`、月次は `run-monthly.yml` に束ねる。
決定（2026-10-21、平野さん）

* 段④を「#504 の観察の後」から今に前倒しする
* 週次・月次は 1 本ずつ起動の表の行にせず、`run-weekly.yml`・`run-monthly.yml` に束ねる（起動の表は run-daily と合わせて 3 行）
* GitHub 側の `schedule`（週次・月次・毎日の保険）は、Worker からの起動が 1 回成功した後に外す（run-daily と同じ手順。この指示では外さず、次の指示 CHAT-1021-WKP-03 で外す）
* Cron Triggers を Worker ごとに増やす形は採らない（起動の表 1 か所のままにする）

前提（チャット側。平野さんの決定ではない）

* 今 GitHub の `schedule` に残る 11 本（2026-10-21 の cloudflare 495cef69。JST。要確認）: 週次 6 本〈check-image-links 月 03:00・check-meibo 月 05:07・sync-birthday-calendar 月 05:17・fetch-youtube 月 05:37・cleanup-logs 月 06:23・check-saikyo-unregistered 月 06:50〉、月次 2 本〈fetch-gsc 1 日 06:00・fetch-redirect-counts 1 日 06:30、後者は前者の後〉、毎日の保険 3 本〈update-live-channel 06:43・sync-dojo-calendar 07:12・cleanup-branches 07:53。run-daily が起動済みなので run-weekly には入れない〉。check-image-links は run-daily に入っているので run-weekly には入れない
* `run-weekly.yml` に入れる 5 本: check-meibo・sync-birthday-calendar・fetch-youtube・cleanup-logs・check-saikyo-unregistered。並びの案: `fetch-youtube`（最後に regenerate-page を all で呼び重い）を最後にし、ほかは並行でよい（互いに依存が無ければ。依存があれば `needs:` で直列）。`run-monthly.yml` に入れる 2 本: `fetch-gsc` → `fetch-redirect-counts`（`needs:`）
* 5 本とも `workflow_call` を持たない（`grep -l workflow_call .github/workflows/*.yml` に無い。要確認）。段③と同じく `workflow_call` 化して残し、手動実行は各本の画面でできるようにする。`scheduled` 入力と `run-name` は check-meibo・sync-birthday-calendar・cleanup-logs・check-saikyo-unregistered にはあり、fetch-youtube・fetch-gsc・fetch-redirect-counts には無い（要確認）。無い 3 本には足す（scheduler-worker.md「予約の起動の見分け方」の 3 点）
* 手動実行（`scheduled` なし）の run-weekly・run-monthly は run-daily と同じく「書かない道筋だけ」にする。dry-run の入力を持たない本（fetch-youtube・fetch-gsc・fetch-redirect-counts は本番に書く・issue にコメントする見込み。要確認）は、`if: ${{ inputs.scheduled }}` で手動実行では飛ばす（飛ばす本と理由を run-weekly のコメントに書く）
* 権限と Secret は run-daily と同じ規則（`permissions: {}` と job ごと、Secret は呼ぶ本が使うものだけ。docs/notes/github-actions.md「権限と Secret の渡し方」）。`concurrency` の組は `run-weekly`・`run-monthly`
* 起動の表（`workers/scheduler/schedule.json`）に足す 2 行の案: `run-weekly.yml` を `weekdays: [1]`（月曜）の 02:00、`run-monthly.yml` を `monthdays: [1]` の 03:00（いずれも JST、`enabled: true`）。理由: run-daily（04:00）より前に終わらせ、06:00 の朝の確かめの対象（予定が 06:00 より前の行）に入れる。1 日が月曜の日は両方動く（組が別なので詰まらない）。fetch-youtube の regenerate all を含む所要時間が過去の実行で 2 時間を超えていれば、時刻を早める案を報告に書く（この指示では 02:00 で入れる）
* 朝の確かめ・埋め合わせは行ごとに `weekdays`・`monthdays` で有効な日だけ見る設計（scheduler.mjs の `validRows()`・当日の行の判定。要確認）。曜日・日の無い日に「未起動」と出ないことをテストで確かめる（既にテストがあればそれで足りる）
* 新規のワークフロー（run-weekly・run-monthly）は作業ブランチから `gh workflow run` できない（github-actions.md「ワークフローを変更したとき」）。手動実行が書かない道筋であることを確かめたうえでマージし、cloudflare で run-weekly・run-monthly を手動実行（`scheduled` なし）して結果を見る。`workflow_call` 化した 5 本＋2 本は、作業ブランチで各本を手動実行（dry-run か件数だけ）して壊れていないことを確かめる
* docs: github-actions.md「ワークフローの一覧」の表に 2 本を足す（`test_workflows_table.py`）。「run-daily.yml」の節と同じ形で「run-weekly.yml・run-monthly.yml」の節を 1 つ足す（戻し方を含む）。scheduler-worker.md「起動の表の直し方」の「今の表」を 3 行に直す。`scripts/morning_report.py`（mj-logs の morning.md「2. 予約実行の未実行」）が run-daily.yml の `uses:` だけを読んでいれば、run-weekly・run-monthly も読む形に直す（直し方が大きければ直さず報告に書く）
* GitHub の `schedule` は残す（Worker の初回の起動まで保険）。初回は run-weekly が 2026-10-26（月）02:00 JST、run-monthly が 2026-11-01 03:00 JST の見込み。報告の「予約実行の初回の日時」に書く（カレンダーはチャット側が作る）
* 決定の書き場所は docs/decisions/automation.md（#564 の段④の節に続ける）。未マージの work/1018-log-367-migrate が github-actions.md の表の別の行を変えている（別の箇所。衝突したら両方を残して解いてよい）

手順

1. #564・#504 に他セッションの着手中コメントが無いことを確かめ、#564 に着手中のコメントを書く。未マージの work/ ブランチに同じ目的（週次・月次の Worker への移行、対象 7 本の `workflow_call` 化）の作業が無いことを確かめる（あれば止まる）
2. (a) 7 本を `workflow_call` 化し、無い本に `scheduled`・`run-name`・判定を足す (b) run-weekly.yml・run-monthly.yml を作る (c) schedule.json に 2 行を足し、Worker のテストを直して通す (d) docs を直す。作業ブランチで 7 本の手動実行（書かない道筋）と `python3 -m unittest discover -s scripts/tests`・`node --test 'workers/scheduler/test/*.test.mjs'` を通す
3. マージ（承認済み）。cloudflare で run-weekly・run-monthly を手動実行（`scheduled` なし）して success を確かめる。#564 に、作った本・起動の表・初回の日時・次の指示（`schedule` を外す）の前提をコメントする。#564 は閉じない

止まる条件

* 「前提」の一覧（11 本・`workflow_call` の有無・`scheduled` の有無）が実物と食い違い、どちらが正か判断が要る
* 対象の本に手動で安全に試せる道筋（dry-run・件数だけ）が無く、作業ブランチで確かめられない（その本だけ外して進めてよい。外した本と理由を報告に書く。全部が外れるときだけ止まる）
* テストが今回の変更で失敗する（無関係な失敗なら原因を報告に書いて進めてよい）
* 未マージの work/ ブランチが、変える本・schedule.json・テスト・docs の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* マージ後の cloudflare での手動実行が今回の変更で失敗する（無関係な失敗なら原因を報告に書いたうえで残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（「予約実行の初回の日時」に run-weekly・run-monthly の見込みを書く）。決定は docs/decisions/automation.md に足す
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-WKP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-WKP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 貼る時機: CHAT-1021-WKP-01 の成果物（875f4d24d）とログ（857dc254e）が origin/cloudflare に入っていることを確かめた
- 同じ Chat-Ref のコミット: 0 件。`WKP` は同じセッションの続きの指示（02）なので識別子の重複の確認は対象外
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行ともあり。欠けなし
- 作業ブランチ: `work/1021-wkp` はローカル・リモートとも origin/cloudflare の祖先（マージ済み）。ローカルにあるので `git merge --ff-only origin/cloudflare`（Already up to date、857dc254e）
- #564・#504: 着手中のまま残るコメントは無い（5件の着手中にはどれも完了の報告がある）。#564 に着手中のコメントを書いた（https://github.com/retroeater/mj/issues/564#issuecomment-6306532886 ）。#564・#504 に run-weekly・run-monthly の語は無く、同じ目的の作業は無い
- 未マージの work/ ブランチ: 同じ目的のコミットは無い。触るファイルを変えているのは `work/1018-log-367-migrate`（docs/notes/github-actions.md の表の `convert-x-posts.yml`・`fetch-x-posts.yml` の行。今回の行・節とは別）と `work/1017-lvp`・`work/1018-log-367-migrate` の scripts/tests の別ファイルだけ。止まらずに進めた

### 前提と実物の照合

- GitHub の `schedule` に残る 11 本は前提のとおり（cron と JST の時刻も一致）。check-image-links は run-daily の `images`
- `workflow_call` は対象の 7 本とも無かった（前提のとおり）
- `scheduled` と `run-name` は check-meibo・sync-birthday-calendar・cleanup-logs・check-saikyo-unregistered にあり、fetch-youtube・fetch-gsc・fetch-redirect-counts に無かった（前提のとおり）
- 前提と違った点（判断は要らない）: fetch-gsc は入力 `commit`（既定は偽。偽なら取得だけ）、fetch-redirect-counts は入力 `dry_run`（既定は真）を持っていた。ただし fetch-gsc は `commit` を外しても、robots.txt に差分があれば #304 にコメントする（コメントのステップに `commit` の条件が無い）。そのため run-monthly の手動実行では fetch-gsc を飛ばし、fetch-redirect-counts は dry-run で動かす形にした
- 起動の表の `weekdays`・`monthdays` は `runsOn()` で見ている。朝の確かめ（`morningRows`）・起動（`dueRows`）・埋め合わせはどれも `runsOn` を通る

### fetch-youtube を外した

fetch-youtube には書かない道筋が無い（入力なし。手動実行でも取得結果をコミットし、regenerate-page を all で呼ぶ。regenerate-page は wayhome を真にして呼ばれると、帰り道の新しい回を issue に知らせることがある）。作業ブランチで手動実行すると issue へ書きうるため、止まる条件「手動で安全に試せる道筋が無く、作業ブランチで確かめられない（その本だけ外して進めてよい）」に当てはめた。fetch-youtube.yml は変えず、run-weekly に入れず、GitHub の `schedule`（月曜 05:37）のまま。
所要時間は過去 2 回とも約 5〜6 分（#1 手動 5分10秒、#2 予約 5分44秒）で、02:00 に入れても 2 時間を超える心配は無い。

### 変更（7627e1cca）

- (a) 6 本に `workflow_call` を足した（入力は各本の書くかの入力〈`dry_run`・`apply`・`commit`〉と `scheduled`、Secret は使う本だけ: sync-birthday-calendar に `GCP_SA_KEY`、fetch-gsc に `GSC_SERVICE_ACCOUNT_JSON`、fetch-redirect-counts に `CF_ANALYTICS_TOKEN`）。fetch-gsc・fetch-redirect-counts に `scheduled` と `run-name` を足した。fetch-gsc はコミットの判定を `schedule || scheduled || commit` に、fetch-redirect-counts は resolve-mode に `scheduled` を渡す形にした。ほかの 4 本は run-name の上のコメントを「run-weekly.yml から呼ぶ」に直した
- (b) `run-weekly.yml`（meibo・birthday・logs・saikyo を並行。手動実行は dry-run・差分だけ）と `run-monthly.yml`（gsc → redirects、redirects は `!cancelled()`。手動実行は gsc を飛ばし redirects は dry-run）を作った。`permissions: {}` と job ごと、Secret は1つずつ、`concurrency` は `run-weekly`・`run-monthly`
- (c) `schedule.json` に `run-weekly.yml`（02:00、`weekdays: [1]`）・`run-monthly.yml`（03:00、`monthdays: [1]`）を足した。テストに「月曜 02:00・1日 03:00 に起動し、その日だけ朝の確かめの対象に入る」（2026-10-26 月曜・2026-11-01 日曜で1日・2027-02-01 月曜で1日）と、ふつうの日の 02:00・03:00 に起動しないことを足した。前の schedule.json に新しいテストを当てると 1 件失敗、新しい表では 47 件すべて成功
- (d) docs/notes/github-actions.md: 表の 6 本の契機を直し、run-weekly・run-monthly の行を足し、run-daily の行の「Worker の起動の表の行はこの1行だけ」を消した。「権限と Secret の渡し方」の表に 6 行を足した。「run-weekly.yml・run-monthly.yml」の節（戻し方を含む）を足した。docs/notes/scheduler-worker.md「起動の表の直し方」の今の表を 3 行に直した
- `scripts/morning_report.py` は直していない。`daily_callers()` が起動の表のすべての行のワークフローの `uses:` を読むので、run-weekly・run-monthly もそのまま読む（呼ばれる本の cron の予定は、同じ JST の日の `[scheduled]` の実行のジョブが success なら数えない）
- `python3 -m unittest discover -s scripts/tests` 1,469 件成功（`test_workflows_table.py` を含む）、`node --test 'workers/scheduler/test/*.test.mjs'` 47 件成功

### 作業ブランチでの手動実行（書かない道筋）

6 本とも work/1021-wkp（7627e1cca）で手動実行し、すべて success。どれも書いていない（作業ブランチのコミットも増えていない）。

| 本 | 実行 | 入力 | 結果 |
|---|---|---|---|
| check-meibo | 39533791028 | dry_run | success。`apply: false`、upsert-issue は dry-run |
| sync-birthday-calendar | 39533793678 | apply なし | success。`--compare` だけ、追加・更新・削除 0 件 |
| cleanup-logs | 39533795984 | dry_run | success。削除のコミットは skipped、#357 は dry-run |
| check-saikyo-unregistered | 39533798396 | dry_run | success。upsert-issue は dry-run |
| fetch-gsc | 39533801092 | commit なし | success。`robots.txt: 前回(2026-10-09)との差分なし`（実行前に本番の robots.txt と docs/gsc/2026-10-09/robots.txt が同じことを確かめてから実行した）。コミットは skipped |
| fetch-redirect-counts | 39533803420 | dry_run | success。`apply: false`、#568 は dry-run |

fetch-redirect-counts のログに「トークンの確認(/user/tokens/verify)は通らなかった: HTTP 401」が出るが、続く取得は成功している（今回の変更と無関係。未調査）。

### 保険の `schedule` が残るあいだに起きること

GitHub の `schedule` は次の指示（WKP-03）まで残すので、10/26（月）は run-weekly（02:00）と各本の保険（05:07〜06:50、遅れて動く）の両方が動く。check-meibo・check-saikyo-unregistered は常設 issue の状態を合わせるだけ、sync-birthday-calendar は差分が無ければ何もしないので害は無い。cleanup-logs は 2 回目の時点で条件外のログがあれば #357 に 2 回目の通知を書く。11/1 も WKP-03 より前なら fetch-redirect-counts が #568 に同じ集計を 2 回コメントし（同じ CSV の 2 回目のコミットは差分なし）、robots.txt に差分があれば fetch-gsc が #304 に 2 回コメントする。

### マージと cloudflare での手動実行

push の直前に再 fetch した。`origin/cloudflare`（857dc254e）が HEAD の祖先であることを確かめてから、`git push origin work/1021-wkp:cloudflare`（857dc254e..9032b793f）でマージした。
続いて cloudflare で run-weekly・run-monthly を手動実行した（`scheduled` なし）。

- run-weekly 39534074313: success、約45秒。5ジョブとも `scheduled: false / apply: false`。upsert-issue は dry-run、cleanup-logs の削除のコミットは skipped、sync-birthday-calendar は `--compare` だけ（0件）
- run-monthly 39534076694: success、約37秒。gsc は skipped。redirects は `apply: false`、コミットは skipped、#568 は dry-run
- cloudflare の先頭は 9032b793f のままで、2本の実行によるコミットは無い

#564 に、作った本・起動の表・初回の日時・次の指示の前提をコメントした（https://github.com/retroeater/mj/issues/564#issuecomment-6306646960 ）。#564 は閉じていない。

## 報告

- 状態: 判断待ち
- ブランチ: work/1021-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1021-WKP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-wkp
- 確認用URL: なし（配信物は変えていない）
- マージ: 済（9032b793f。fast-forward）
- issue: #564（着手・結果のコメント。閉じていない）
- 判断が必要なこと:
  - fetch-youtube.yml を外した。書かない道筋が無く、作業ブランチで確かめられないため（止まる条件の「その本だけ外す」）。GitHub の `schedule`（月曜 05:37、遅れて動く）のまま。移すなら案は2つ: (1) dry-run の入力を足す（取得だけしてコミットと regenerate を飛ばす）。そのうえで run-weekly に入れる (2) 確かめを cloudflare での予約の起動に任せて、そのまま run-weekly に入れる。どちらにするか、WKP-03 で `schedule` を外すときに fetch-youtube の分をどう扱うかを決めてほしい
  - 保険の `schedule` が残るあいだは、保険の回でも書く。10/26 は cleanup-logs が #357 に2回目の通知を書くことがある。11/1 が WKP-03 より前なら、fetch-redirect-counts が #568 に2回コメントする（robots.txt に差分があれば、fetch-gsc も #304 に2回）。WKP-03 をいつ入れるか
- 未確認の項目:
  - 予約実行の初回: run-weekly 2026-10-26（月）02:00 JST、run-monthly 2026-11-01（日）03:00 JST（Worker から。題が `[scheduled]` の実行が success か）
  - Workers Builds の `mj-scheduler` のデプロイ（9032b793f を含むビルド）が Ready か（ダッシュボードで平野さん）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9032b793）: https://github.com/retroeater/mj-logs/tree/main/guide/9032b793

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9032b793/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
