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

## 報告

- 状態: 作業中
- ブランチ: work/1021-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1021-wkp/docs/logs/CHAT-1021-WKP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 035e5984）: https://github.com/retroeater/mj-logs/tree/main/guide/035e5984

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
