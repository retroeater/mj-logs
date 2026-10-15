# CHAT-1015-WFL-18

- 着手日時: 2026-10-15
- 対象issue: #564
- ブランチ: work/1015-wfl-s7
- 着手時HEAD: 40840c0ce

## 指示

【Claude作成】Claude Code 向け指示：段③-2 の続き（Worker の表のテストを新しい表に合わせ、切り替えをマージする） Chat-Ref: CHAT-1015-WFL-18 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき） 貼る時機: CHAT-1015-WFL-17 の判断待ちへの回答。JST 05:15〜23:30 の間（マージの後、翌朝 04:00 より前に Worker のデプロイを確かめる時間を残すため）。JST 03:45〜05:15 はマージの push と cloudflare での手動実行をしない 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-wfl-s7 を続けて使う（WFL-17 の途中までの変更〈6309ec101〉をそのまま使うため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-WFL-17 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
WFL-17 で止まった残り（Worker の表のテストの直し、作業ブランチでの run-daily の手動実行、docs、マージ）を済ませ、毎朝の起動を run-daily.yml に切り替える。
決定（2026-10-15、平野さん）
WFL-17 の `## 報告`「判断が必要なこと」への回答。いずれも Claude Code の推奨のとおり。

1. `workers/scheduler/test/scheduler.test.mjs` の表の中身を確かめるテスト（「06:00 の確かめの対象は5行」など、`schedule.json` の中身の写しを期待値に持つもの）を新しい表（04:00 の回に `run-daily.yml` を1本、確かめの対象は1行）に合わせて直す。表の中身の写しのテストは表と一緒に変わる性質のものとして、「Worker のコードを変えない」（2026-10-14）の例外にはしない（Worker の動き〈`src/`〉は変えない）
2. morning_report.py の所要時間の判定は「判定する実行自身を除いた、ほかの実行の中央値が 600 秒未満の本だけ絶対値（600 秒）で見る。ほかの実行が無ければ絶対値で見る」とする（WFL-17 の読みのとおり）

前提（チャット側。平野さんの決定ではない）

* WFL-17 の前提「Worker のテストは schedule.json を読まない」はチャット側（WFL-14 の調べに基づく）の誤り。テストの直しは、`schedule.json` の中身を期待値に持つ箇所だけにする（`src/` の動きを確かめるテスト・例の文字列のテストは変えない）。`schedule.json` の行の形を確かめるテスト（51 行付近）が新しい 1 行で通るかも確かめる（要確認）
* WFL-17 の止まる条件・手順の残りはそのまま: 作業ブランチで run-daily を `scheduled` なしで手動実行して success と書かない道筋を確かめる（外のサイトへの接続の一時的な失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない）、docs（docs/notes/scheduler-worker.md の起動の表、docs/notes/github-actions.md の 6 本と run-daily の説明・一覧の表、run-daily の節に戻し方）を直す、`docs/decisions/automation.md` に WFL-17・WFL-18 の節を足す（WFL-17 の節は「再掲」、WFL-18 の節は上の決定）
* 戻し方（docs に書く）: `schedule.json` を 5 行に戻し、regenerate-saikyo の `workflow_run` を戻し、Worker のテストの期待値を戻す 1 コミット（ゲートは両方の和なので戻さなくてよい）
* マージの後の確かめは WFL-17 の手順3 のとおり（`mj-scheduler` のビルドの check-run の成否を 15 分まで待つ。失敗・付かないときは戻さずに止まる）

手順

1. WFL-17 のログ（`docs/logs/CHAT-1015-WFL-17.md`）の `## 報告` の状態の末尾に `/ 続き: CHAT-1015-WFL-18` を足す。Worker のテストを直し、テスト（unittest・部品の `node --test`・Worker の `node --test`）がすべて通ることを確かめる
2. 作業ブランチで run-daily を `scheduled` なしで手動実行し（run の番号と結果をログに書く）、docs と `docs/decisions/automation.md` を直す
3. `origin/cloudflare` を取り込み（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）、テストが通ることを確かめて cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）。マージのコミットの check-run で `mj-scheduler` のビルドの成否と、check・assets-check の成否を確かめる。#564 にコメントする（本文は変えない）。切り替えの翌朝 04:00 JST の run-daily・06:00 の朝の確かめ・同日の morning.md を「未確認の項目」に日時つきで書く

止まる条件

* WFL-17 のログの状態が「判断待ち」でない
* Worker のテストを通すのに、`schedule.json` の中身を期待値に持つ箇所以外（`src/`・ほかのテスト）を変える必要がある（変えずに止まる）
* テストが失敗する（今回の変更で落ちると分かった unittest は直してよい。直したらログに書く）
* 作業ブランチの run-daily の手動実行で、いずれかの本が書く道筋で動いた（失敗の知らせ以外の issue の書き込み・カレンダー・シート・ブランチ）: 書いた中身をログに書いて止まる（マージしない）
* 取り込みで両立しない衝突がある
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `workers/scheduler/`・6 本・run-daily.yml・`scripts/morning_report.py`・`scripts/actions_status.py` を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* マージの後に `mj-scheduler` のビルドが失敗した、または 15 分待っても check-run が付かない: 戻さずに止まり、状態を書く（翌朝の扱いは判断待ち）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-WFL-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-WFL-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5150009b）: https://github.com/retroeater/mj-logs/tree/main/guide/5150009b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5150009b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
