# CHAT-1015-WFL-17

- 着手日時: 2026-10-15
- 対象issue: #564
- ブランチ: work/1015-wfl-s7
- 着手時HEAD: 3f3ec6ede

## 指示

【Claude作成】Claude Code 向け指示：段③-2 毎朝の起動を run-daily.yml に切り替える Chat-Ref: CHAT-1015-WFL-17 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき） 貼る時機: CHAT-1015-WFL-16 の完了後、JST 05:15〜23:30 の間（マージの後、翌朝 04:00 より前に Worker のデプロイを確かめる時間を残すため）。JST 03:45〜05:15 はマージの push と cloudflare での手動実行をしない 作業ブランチ: クラウドセッションで実行する。work/1015-wfl-s7 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-wfl-s7 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-WFL-16 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」と docs/notes/scheduler-worker.md を読む。

目的
#564 の段③-2。毎朝の起動を本ごとの 5 行から `run-daily.yml` の 1 行に切り替える。切り替えに伴うゲート・`workflow_run`・朝の報告の直しを同じマージに入れる（片方だけだと、朝の本が 2 回動くか、1 回も動かない）。
決定（2026-10-15、平野さん。WFL-15 の決定の再掲）

* run-daily.yml の並びは live → sns → images → saikyo の直列（`!cancelled()`）と、dojo・branches の並行（WFL-16 の cloudflare の手動実行で確かめ済み）
* update-live-channel の保険のゲートは、run-daily の実行のジョブ `live / update` の結論を見る（run-daily 全体の結論では見ない）
* 段③-2 は schedule.json・ゲート・regenerate-saikyo の `workflow_run` の削除・morning_report.py の 2 点（所要時間の絶対値〈600 秒〉の判定を中央値が 600 秒未満の本だけにする・失敗の行に失敗したジョブ名を足す）を同じマージに入れる
* Worker のコードは変えない（2026-10-14）

前提（チャット側。平野さんの決定ではない）

* 変える所は WFL-14 のログの「schedule.json・Worker の朝の確かめ・埋め合わせ」「update-live-channel の保険のゲート・regenerate-saikyo の workflow_run」「mj-logs の morning.md・status.md」のとおり（要確認。実物と違えば実物に合わせ、違いをログに書く）:
   * `workers/scheduler/schedule.json`: 5 行を `{"workflow": "run-daily.yml", "time": "04:00", "enabled": true}` の 1 行にする（キーは今の行の形に合わせる）
   * update-live-channel のゲート: 当日・cloudflare・`[scheduled]` の run-daily.yml の実行のジョブ `live / update` が success、または今と同じ当日・cloudflare・`[scheduled]`・success の update-live-channel.yml の実行があれば、schedule は何もしない（両方の和）。引けなかったときは止めずに実行する（今の方針）
   * regenerate-saikyo: `on.workflow_run` と、`if:` の `workflow_run` に関する条件を外す。`workflow_dispatch`・`workflow_call` は残す
   * `scripts/morning_report.py`（と要れば `scripts/actions_status.py`）: 決定の 2 点。テストを足す
* docs（docs/notes/scheduler-worker.md の起動の表、docs/notes/github-actions.md の 6 本と run-daily の説明・一覧の表）を今の動きに直す。`scripts/tests/test_workflows_table.py` が通ること
* Worker の `node --test`（`workers/scheduler/`）が通ること。テストは例の文字列でファイル名を持ち、schedule.json は読まない（WFL-14 の調べ。要確認）
* `schedule.json` が cloudflare に入ると Workers Builds の `mj-scheduler` がデプロイする（watch paths `workers/scheduler/**`）。マージの後に、そのコミットの check-run で `mj-scheduler` のビルドが success になったことを確かめる。check-run が無い・失敗のときは Worker は前の 5 行の表のまま動く（そのとき regenerate-saikyo の `workflow_run` は外れているので、翌朝は最強戦の再生成だけが動かない。ほかの本は今までどおり本ごとに起動される）
* 作業ブランチでの確かめ: run-daily.yml は cloudflare にあるので、作業ブランチで `scheduled` なしの手動実行ができる（書かない道筋。WFL-16 と同じ確かめを短く）。ゲートは作業ブランチで update-live-channel を手動実行してもゲートの判定は schedule のときだけ働くので、判定の関数・ステップを手で通せる形があればそれで、無ければ API の引き方（URL・jq の filter）を今日の実行（WFL-16 の run-daily #1 は `[scheduled]` が付かないので、当たらないことの確かめになる）に当ててログに書く
* 切り替えの翌朝（マージした日の翌日）04:00 JST の run-daily が書く道筋の初回。06:00 の Worker の朝の確かめ・mj-logs の status.md・morning.md で確かめるのは次の指示で行う（この指示では「未確認の項目」に日時を書く）。保険の `schedule`（update-live-channel 06:43・sync-dojo-calendar 07:12・delete-merged-branches 07:53・check-image-links 月曜 03:00）は段④まで残す
* 2026-10-15 時点で update-sns-book は video_mtsuku の失敗（#569、MTK が対応中）で job が失敗する。切り替えの後は run-daily 全体が failure になり、Worker の朝の確かめが #506 に知らせる（今も update-sns-book の失敗で同じ知らせが出る）。ゲートは `live / update` を見るので、保険の update-live-channel は二重に動かない
* 戻し方: `schedule.json` を 5 行に戻し、regenerate-saikyo の `workflow_run` を戻す 1 コミット（ゲートは両方の和なので戻さなくてよい）。手順を docs/notes/github-actions.md の run-daily の節に書く

手順

1. 「前提」の要確認を実物で確かめ、変える所を直す。テスト（unittest・部品の `node --test`・Worker の `node --test`）が通ること。作業ブランチで run-daily を `scheduled` なしで手動実行して success と書かない道筋を確かめ、ゲートの引き方を確かめる（run の番号と結果をログに書く）
2. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-15（CHAT-1015-WFL-17）` として足す（「再掲」とし、新しい決定は無いと書く）
3. cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）。マージのコミットの check-run で `mj-scheduler` のビルドの成否と、check・assets-check の成否を確かめる（待ちは 15 分まで）。#564 にコメントする（本文は変えない）。切り替えの翌朝 04:00 JST の run-daily・06:00 の朝の確かめ・同日の morning.md を「未確認の項目」に日時つきで書く

止まる条件

* WFL-16 のログの状態が「完了」でない
* 作業ブランチの run-daily の手動実行で、いずれかの本が書く道筋で動いた（失敗の知らせ以外の issue の書き込み・カレンダー・シート・ブランチ）: 書いた中身をログに書いて止まる（マージしない）。外のサイトへの接続の一時的な失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* Worker のテストを通すのに `workers/scheduler/` のコード（`schedule.json` 以外）を変える必要がある（変えずに止まる）
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `workers/scheduler/`・6 本・run-daily.yml・`scripts/morning_report.py`・`scripts/actions_status.py` を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージの後に `mj-scheduler` のビルドが失敗した、または 15 分待っても check-run が付かない: 戻さずに止まり、状態を書く（翌朝の扱いは判断待ち）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-WFL-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-WFL-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ddd0fcee）: https://github.com/retroeater/mj-logs/tree/main/guide/ddd0fcee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
