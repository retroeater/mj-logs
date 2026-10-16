# CHAT-1016-WFL-20

- 着手日時: 2026-10-16
- 対象issue: #564・#565
- ブランチ: work/1016-wfl-s8
- 着手時HEAD: 4fdda151b

## 指示

【Claude作成】Claude Code 向け指示：段③-3 ワークフローの改名と sync-yotei.yml の切り出し Chat-Ref: CHAT-1016-WFL-20 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき）。`workers/` は変えない 貼る時機: いつでも（CHAT-1016-WFL-19 は完了済み）。ただし JST 03:45〜05:15（run-daily の起動の間）はマージの push と cloudflare での手動実行をしない 作業ブランチ: クラウドセッションで実行する。work/1016-wfl-s8 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-wfl-s8 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-WFL-19 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。

目的
#564 の段③-3。ワークフローのファイル名と `name:`・run-name・入力の description・冒頭コメント・docs を `<動詞>-<対象>` の規則で揃え、update-live-channel の job yotei を `sync-yotei.yml` に切り出す。毎朝の動き（run-daily の並び・書く中身）は変えない。
決定（平野さん）

* 改名はファイル名だけでなく `name:`・run-name・入力の description・冒頭コメント・docs も同じ規則で揃える（2026-10-14）
* 改名の表は WFL-14 のログの「改名の表」のとおりに確定（2026-10-15、WFL-15 の決定5）: `assets-check.yml` → `check-assets.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`、`sitemap-lastmod.yml` → `regenerate-sitemap.yml`、`name:` の言い回し（表の「新しいファイル（`name:`／run-name の `[scheduled]` の後）」の列）。`regenerate-saikyo.yml`・`update-sns-book.yml`・`check-image-links.yml`・`check-meibo.yml` の名前は変えない
* `sync-yotei.yml` は update-live-channel の job yotei を `uses: ./.github/workflows/sync-yotei.yml` に置き換えて呼ぶ（run-daily の job にしない。2026-10-15、WFL-15 の決定3）
* `fetch-youtube.yml` の切り出しは段③-4（この指示では行わない）

前提（チャット側。平野さんの決定ではない）

* 改名の表の「新しい `name:`」の言い回しは WFL-14 の案（チャット側の案ではない）。表に無い本（check-saikyo-unregistered・sync-birthday-calendar・fetch-gsc・fetch-redirect-counts・cleanup-logs・regenerate-page・fetch-x-posts・convert-x-posts など）の `name:` は、表の言い回し（「〜の検査」「〜の同期」「〜の取り込み」など、名詞止め）と揃っていなければ揃える案を作り、変えずに判断待ちに入れる（この指示ではファイル名も変えない）
* 参照を追う所は WFL-14 のログの「`name:`・ファイル名を参照する所」のとおり（要確認。`git grep` で洗い直す。docs/logs・docs/decisions は過去の記録なので直さない）: run-daily.yml の `uses:`、Worker の `schedule.json`（今は run-daily の 1 行なので改名する本は載っていない見込み）、update-live-channel のゲート、`scripts/tests/test_workflows_table.py` と docs/notes/github-actions.md の一覧の表、CLAUDE.md・docs/notes の各所、ワークフローの冒頭コメントの古い参照（#492 に書かれた 6 か所）。CLAUDE.md の文言は改名だけ直す（中身は変えない。28KB の上限を確かめる）
* 改名した本は既定ブランチに無いうちは作業ブランチで `workflow_dispatch` できない（404、docs/notes/github-actions.md）。push・check_run で動く本（check-assets・check-build-failure・regenerate-sitemap）は作業ブランチの push・マージの後の cloudflare の push で動くかを確かめる。cleanup-branches は run-daily から `uses:` で呼ばれるので、作業ブランチで run-daily を `scheduled` なしで手動実行して確かめられる（ローカルの再利用ワークフローは同じコミットのものが使われる）。マージの後に cloudflare で cleanup-branches を `dry_run: true` で手動実行する
* sync-yotei は update-live-channel から `uses:` で呼ばれるので、作業ブランチで update-live-channel を書かない入力（`apply: false`）で手動実行して、yotei が sync-yotei 経由で今と同じ env（`APPLY`・`SCHEDULED`・`CALENDAR_APPLY` など）で動くことを確かめる。入れ子は run-daily → update-live-channel → sync-yotei の 3 段で、WFL-16 で 3 段が通ることは確かめ済み。update-live-channel の保険の実行（schedule）でも sync-yotei が今と同じに動くこと（schedule のときの値の決まり方）を、コードを読んで書く
* 改名で GitHub の実行の履歴は新しいファイルに引き継がれない（新しいワークフローとして数えられる）。mj-logs の status.md・morning.md で、改名した本が「初回成功」に出ること、古いファイルの節が消えることは想定どおり（WFL-11 で、消したファイルの節は残らないと確かめ済み）
* `assets-check` のジョブ名が必須の確認（ブランチ保護など）に使われていないか確かめる（使われていればジョブ名は変えない）
* ACF のチャットが `scripts/actions_status.py` を変える指示（CHAT-1016-ACF-24、work/1016-acf-retry）を出している。この指示では `scripts/actions_status.py`・`scripts/morning_report.py` を変えない
* 文書参照の検査テスト（#565）は改名の後に入れる予定（2026-10-14 の決定）。この指示では入れない。#565 に「段③-3 で改名した」とコメントする

手順

1. 参照を洗い出してログに表で書く。改名（`git mv`）と、`name:`・run-name・description・冒頭コメント・docs・CLAUDE.md・一覧の表の直しを行う。sync-yotei.yml を切り出す（`workflow_call` と `workflow_dispatch` を持ち、入力・`permissions`・`secrets`・`concurrency` は今の job yotei と同じ動きになるようにする）。テスト（unittest・部品の `node --test`・Worker の `node --test`）が通ること
2. 作業ブランチで次を手動実行し、run の番号と結果をログに書く: run-daily（`scheduled` なし）、update-live-channel（`apply: false`）。作業ブランチの push で check-assets が動いて success になることを確かめる。書く道筋で動いた本が無いこと（WFL-16 と同じ確かめ）
3. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-16（CHAT-1016-WFL-20）` として足す（新しい決定が無ければ「再掲」とする）。cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）。マージの後で、cloudflare の push で check-assets・regenerate-sitemap が動いて success、check-build-failure が skipped か success になることと、cleanup-branches の `dry_run: true` の手動実行が success になることを確かめる。#564・#565 にコメントする（本文は変えない）。翌朝 04:00 の run-daily（改名の後の初回）を「未確認の項目」に書く

止まる条件

* WFL-19 のログの状態が「完了」でない
* `workers/` を変える必要があると分かった（変えずに止まる）
* `assets-check` のジョブ名がブランチ保護などの必須の確認に使われていて、ファイル名の改名でも影響が出ると分かった（改名せずに判断待ち）
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 作業ブランチの手動実行で、いずれかの本が書く道筋で動いた（失敗の知らせ以外の issue の書き込み・カレンダー・シート・ブランチ）: 書いた中身をログに書いて止まる（マージしない）。外のサイトへの接続の一時的な失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが改名する本・update-live-channel.yml・run-daily.yml・docs/notes/github-actions.md の一覧の表・CLAUDE.md の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージの後の cloudflare での確かめで failure が出た: 戻さずに原因と直し方の案を書いて判断待ち（翌朝の run-daily に効くもの〈cleanup-branches・sync-yotei〉の failure は、報告の先頭に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、表に無い本の `name:` を揃える案を書く（比較と推奨つき）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-WFL-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-WFL-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4fdda151）: https://github.com/retroeater/mj-logs/tree/main/guide/4fdda151

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
