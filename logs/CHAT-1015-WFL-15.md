# CHAT-1015-WFL-15

- 着手日時: 2026-10-15
- 対象issue: #564
- ブランチ: work/1015-wfl-s6
- 着手時HEAD: b2da47b94

## 指示

【Claude作成】Claude Code 向け指示：段③-1 毎朝の 6 本を workflow_call で呼べるようにし、run-daily.yml を書かない道筋で入れる Chat-Ref: CHAT-1015-WFL-15 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき）。`workers/scheduler/schedule.json` は変えない（切り替えは段③-2） 貼る時機: いつでも（CHAT-1015-WFL-14 の判断待ちへの回答）。ただし JST 03:45〜05:15（毎朝のワークフローの起動の間）はマージの push と cloudflare での手動実行をしない 作業ブランチ: クラウドセッションで実行する。work/1015-wfl-s6 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-wfl-s6 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-WFL-14 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。

目的
#564 の段③-1。WFL-14 の設計（ログの「手順2: 設計」）のとおり、毎朝の 6 本を `workflow_call` で呼べるようにし、`run-daily.yml` を足す。`run-daily.yml` は Worker の表に載せず、書かない道筋だけを cloudflare の手動実行で確かめる。毎朝の動きはこの指示では変えない。
決定（2026-10-15、平野さん）
WFL-14 の `## 報告`「判断が必要なこと」1〜7 への回答。いずれも Claude Code の推奨のとおり。

1. run-daily.yml の並びは live → sns → images → saikyo の直列（後の 3 つは前が失敗しても `!cancelled()` で動かす）とし、dojo・branches は並行にする
2. update-live-channel の保険のゲートは、run-daily の実行のジョブ `live / update` の結論を見る（run-daily 全体の結論では見ない）
3. sync-yotei.yml は update-live-channel の job yotei を `uses: ./.github/workflows/sync-yotei.yml` に置き換えて呼ぶ（run-daily の job にしない）
4. fetch-youtube.yml は WFL-02 の決定のとおり regenerate-page から切り出す。update-live-channel の層1（YouTube の取り込み）は分けない（WFL-14 の指示文の前提の誤り。チャット側の書き違い）
5. 改名の残りは WFL-01 の案で確定する: `assets-check.yml` → `check-assets.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`、`name:` の言い回し（WFL-14 の経過の改名の表）。段③-3 でまとめて行う
6. 実装は段③-1（この指示）→ ③-2（切り替え: schedule.json・ゲート・`workflow_run` の削除・morning_report.py）→ ③-3（改名・sync-yotei）→ ③-4（fetch-youtube）の順に分ける
7. morning_report.py は段③-2 で 2 点直す: 所要時間の絶対値（600 秒）の判定を中央値が 600 秒未満の本だけにする・失敗の行に失敗したジョブ名を足す

前提（チャット側。平野さんの決定ではない）

* 足す入力・`permissions`・`secrets: inherit`・`concurrency` の形は WFL-14 のログの「各本を workflow_call にする差分」のとおり（要確認。実物と違えば実物に合わせ、違いをログに書く）。`workflow_dispatch` の入力・各本の run-name・保険の `schedule`・ゲート・regenerate-saikyo の `workflow_run` は変えない（段③-2 で変える）
* run-daily.yml の形は WFL-14 のログの「run-daily.yml のジョブ」のとおり（`name:` 毎朝の運行、run-name の `[scheduled]`、入力 `scheduled`・`dry_run`〈既定 true〉、`permissions: {}` と job ごとの権限、`concurrency: run-daily`）。`scheduled` の道筋も書くが、この指示では使わない
* `dry_run: true` で各本に渡す値: live `apply: false`、sns `mode: check`、dojo `apply: false`、branches・images `dry_run: true`、saikyo `abema: check`・`regenerate: true`（WFL-14 の設計。要確認。各本の実物で「書かない」になる値を選び直してよい。選び直したらログに書く）
* `dry_run: false` かつ `scheduled` なしの手動実行は書く道筋になりうる。この段では作らない（`dry_run: false` を選んだら最初の job で止めるか、入力を `scheduled` 以外に設けない）。どちらにしたかをログに書く
* run-daily.yml は新規なので作業ブランチでは起動できない（404、docs/notes/github-actions.md）。マージ後に cloudflare で `dry_run: true`（`scheduled` なし）の手動実行をする。run-name は空になるので題に `[scheduled]` は付かず、Worker の朝の確かめには数えられない
* saikyo に `regenerate: true` を渡すので、cloudflare の手動実行で regenerate-page が saikyo のページを生成する。差分があれば cloudflare に生成物のコミットが入る（ふだんの再生成と同じ）。入ったらコミットの SHA と変わったファイルをログに書く
* WFL-14 の未確認の項目（呼ばれる側のワークフロー全体の `concurrency` が workflow_call のときにどう効くか、入れ子 3 段が通るか）をこの手動実行で確かめる
* docs/notes/github-actions.md の「ワークフローの一覧」の表に run-daily.yml の行を同じコミットで足す（`scripts/tests/test_workflows_table.py`）

手順

1. 6 本（update-live-channel・update-sns-book・sync-dojo-calendar・delete-merged-branches・check-image-links・regenerate-saikyo）に `workflow_call` を足す。各本を作業ブランチで今の手動実行（書かない入力）で 1 回ずつ動かし、`workflow_call` を足す前と動きが変わらないことを確かめる（run の番号と結論をログに書く）
2. `run-daily.yml` を足し、docs/notes/github-actions.md の表と run-daily の説明を直す。`python3 -m unittest discover -s scripts/tests` と部品のテストが通ること。上の「決定」を `docs/decisions/automation.md` に `## 2026-10-15（CHAT-1015-WFL-15）` として足す
3. cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）。マージの後で cloudflare で run-daily.yml を `dry_run: true` で手動実行し、次を確かめてログに書く: 6 つの job が設計の `needs`・並行のとおりに動いた時刻、入れ子 3 段（run-daily → regenerate-saikyo → regenerate-page）が通ったか、`concurrency` で詰まった・取り消された job が無いか、各本が書かない道筋で動いたか（issue・シート・カレンダー・ブランチに書いていないこと。各ジョブのログの該当行を引用）、所要。#564 にコメントする（本文は変えない）。段③-2 の書く道筋の初回の確かめ（切り替えの翌朝 04:00）は「未確認の項目」に書く

止まる条件

* WFL-14 のログの状態が「判断待ち」でない
* 手順1 の手動実行のいずれかが failure、または `workflow_call` を足す前と動きが変わった（マージしない）
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが 6 本・`.github/actions/`・docs/notes/github-actions.md の一覧の表を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* cloudflare での run-daily の手動実行で、いずれかの本が書く道筋で動いた（issue・シート・カレンダー・ブランチに書いた）: 直ちに書いた中身をログに書いて止まる（戻すかは判断待ち）。failure や `concurrency` の詰まりは止まらず、原因と直し方の案をログに書いて判断待ちにする
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-WFL-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-WFL-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b2da47b9）: https://github.com/retroeater/mj-logs/tree/main/guide/b2da47b9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
