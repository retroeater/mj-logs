# CHAT-1102-WKP-09

- 着手日時: 2026-11-02
- 対象issue: #564
- ブランチ: work/1102-wkp
- 着手時HEAD: b1c2c3de7

## 指示

【Claude作成】Claude Code 向け指示：定期実行の時刻をそろえ（月次 02:00・週次 03:00）、週次の6本を run-daily へ移し、全部を日次にする案を起票する
Chat-Ref: CHAT-1102-WKP-09
マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる
貼る時機: いつでも（WKP の同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#564 の段④の後の見直し。定期実行の時刻をそろえ、週次の中身を fetch-youtube だけに減らし（ほかの6本は run-daily へ）、全部を日次にする案（案A）を別の issue に起票する。

### 決定（2026-11-02、平野さん。AIC のチャット経由の伝言）
- 時刻をそろえる（2026-11-09 の週から）: 月次（run-monthly）は毎月1日 03:00 → 02:00 JST、週次（run-weekly）は月曜 02:00 → 03:00 JST、日次（run-daily）は毎日 04:00 JST のまま。質問欄（mj-ask）の週次の書き出しも月曜 03:00 JST にそろえるが、それは AIC の系列（CHAT-1102-AIC-11）で mj-ask の Cron Trigger を変える（この指示では触らない）
- 週次の中身を減らす（案B）: fetch-youtube（→ regenerate-page の all）だけを run-weekly に残し、ほかの週次（check-meibo・sync-birthday-calendar・cleanup-logs・check-saikyo-unregistered・fetch-x-posts → convert-x-posts）を run-daily に移す
- 別の issue を起票する:「直近の Actions の使用量のペースを確かめ、問題なければ全部を日次にする（案A）」
- マージは、作業ブランチの手動実行がすべて success（書く時点で分かっている無関係な失敗は除く）で、止まる条件に1つも当たらなければ行ってよい（2026-11-02、チャットで）

### 前提（チャット側。平野さんの決定ではない）
- 2026-11-02 の run-weekly #8（run 41329205064、02:00、cloudflare）と run-daily #28（run 41339489114、04:00、cloudflare、26分34秒）はどちらも success（mj-logs の actions/status.md、04:28 JST の書き出し）。mj-logs の actions/morning.md（06:24 JST の書き出し）の1節に cloudflare の失敗は無い（作業ブランチ work/1101-wkp の run-daily の sns / update の失敗が1件あるだけで、シートの直しで解消済み）。したがって、この指示の手動実行で例外にする既知の失敗は無い
- 伝言の実測（2026-10-26 の run-weekly）: 移す6本はジョブの合計で約5分、fetch-youtube は約9分（regenerate が522秒）。B で増える Actions の使用時間は月に約100分の見込み（AIC のチャット側の見積もり。要確認）
- 移す本を毎日にすると、回数あたりで決まるもの（issue へのコメント・外部 API の呼び出しの最低料金など）が7倍になりうる（チャット側の見立て。要確認）。特に:
  - cleanup-logs: 条件外のログを #357 に通知する（docs/notes/github-actions.md の表）。毎日だと、変わっていない同じ通知が毎日付くか
  - fetch-x-posts（X API の全期間検索、費用の上限はスクリプトの定数）・convert-x-posts（Anthropic API・Google Places）: 取り込む投稿の数は同じでも、1回ごとの固定の呼び出し（検索の最初のページなど）が毎日になると月の費用がいくら増えるか。fetch-x-posts の dry-run は予定の件数・費用を出す
  - check-meibo・check-saikyo-unregistered・sync-birthday-calendar: 常設 issue の書き換え（upsert）・カレンダーの差分の同期なので、変わらなければ書かない見込み（要確認）
- run-daily の中の並べ方は実物に合わせて決めてよい（例: 移す4本は並行、fetch-x-posts → convert-x-posts は順に。どれも前が失敗しても `!cancelled()` で動く形）。cleanup-logs のコミットは WKP-05 で `retry: rebase` になっている。run-daily の中で同じブランチに push するほかのジョブとの重なりは WKP-05 の洗い出しの表（CHAT-1101-WKP-05 のログ）で見直す
- fetch-x-posts・convert-x-posts は LOG 系列（#367 は 2026-11-02 に閉じた。残りは #636）の本。CHAT-1102-LOG-01 が run-weekly の初回（11/02）を確かめて記録した。チャット側から LOG のチャットへ、run-daily に移すことを伝言する
- 起動の表は workers/scheduler/schedule.json（`weekdays`・`monthdays` は JST、docs/notes/scheduler-worker.md「起動の表の直し方」）。`workers/scheduler/` を変えるマージの後は、平野さんが Workers Builds のダッシュボードで、そのコミットを含むビルドが Ready になったことを確かめる（同じ文書。Skipped のことがある）
- 12/1 の run-monthly は、この変更が入れば 02:00 JST に起動する。カレンダー 2026-12-01【R#564】の予定の時刻は、チャット側が直す
- 起票する issue の本文の材料（AIC のチャットの伝言から。書き方は実物に合わせてよい）: A では fetch-youtube を毎日にすると、全ページの作り直しが月に約4時間増える。YouTube のデータが変わった日だけ使うページ（生成スクリプトで約10本）を作り直す形に分け、全ページの作り直しは週1回に残す案がある（全ページの作り直しは、シートの最新の値をサイト全体に入れる週次の役目も持つ見込み）。cleanup-logs のコミットは docs/** だけなので本番のビルドは動かない（Build watch paths の除外。対処は不要）。ラベルは既存のものから選ぶ（分野: 自動化 など）

## 手順
1. 着手前の確かめ。#564 が Open であること。`git branch -r --no-merged origin/cloudflare` で、run-daily.yml・run-weekly.yml・workers/scheduler/schedule.json・移す6本のワークフローの同じ行・同じジョブを変えている未マージのブランチが無いこと。そのうえで、毎日にしたときの増え方を調べて表にする: 移す6本ごとに (a) issue・カレンダー・シートへの書き込みが、変わらない日にも起きるか（コードの分岐で確かめる） (b) 外部 API の呼び出しと費用が回数でどう増えるか（fetch-x-posts は dry-run で1回分の予定の件数・費用を出し、週1回と毎日の月の見込みを並べる） (c) 1回の所要時間（2026-11-02 の run-weekly #8 のジョブの時間）。(a) で同じ内容のコメント・通知が毎日付くもの、(b) で月の費用が週1回より $1 を超えて増えるものがあれば、その本の移し方の案（変わったときだけ通知する・その本だけ週次に残す など）を書いて止まる
2. 移す。schedule.json の run-monthly を 02:00、run-weekly を 03:00 にする（run-daily は変えない）。run-weekly.yml から6本を外して fetch-youtube だけにし、run-daily.yml に6本を足す（Secret・権限・入力 `scheduled` の渡し方は run-weekly の今の形を写す）。関わる文書（docs/notes/github-actions.md の表と run-daily・run-weekly の節、docs/notes/scheduler-worker.md、各本の冒頭のコメント、docs/handover.md など）を、`git grep -n -E '月曜02:00|毎週月曜|run-weekly'` などリポジトリ全体の検索で洗って直す（docs/decisions・docs/logs・handover-archive の過去の行は除く）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/scheduler/test/*.test.mjs'` を通す。作業ブランチで run-daily と run-weekly を手動実行し（書かない道筋）、すべて success であることを確かめる
3. 案A の issue を起票し、決定を docs/decisions/automation.md に足し、#564 に結果をコメントする（文面は `## 経過` から引用する）。止まる条件に当たらなければ cloudflare へマージする。マージの後、平野さんに頼む手作業（Workers Builds の mj-scheduler のビルドが Ready か）を最終報告に書く

## 止まる条件
- #564 が閉じている、または同じワークフローに他セッションの着手中のコメントがある
- 手順1の (a)(b) に当たる本がある（案を書いて止まる）
- 未マージの work/ ブランチが、この作業で変えるファイルの同じ行・同じジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- 手順2の手動実行が success にならない（外のサイト・API の一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない。この指示を書いた時点で cloudflare の既知の失敗は無い）
- 手順2の手動実行で、作業ブランチの外（cloudflare・issue・シート・カレンダー）への書き込みが起きた
- マージの差分に、`.github/workflows/`・`workers/scheduler/schedule.json` とそのテスト・`docs/` のほかが出る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージの後の check-run の失敗は、今回の変更によるものなら止まり、無関係なら原因をログに書いて残りの手順（#564 へのコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順1の表は移す6本の全件で書き、「略」にしない
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確かめ（2026-11-02）

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 作業ブランチ: work/1102-wkp はローカル・リモートとも無かったので `git checkout -b work/1102-wkp origin/cloudflare`（b1c2c3de7）
- 指示文の issue の state: #564 は Open（最新のコメントは WKP-06 の結果、他セッションの着手中のコメントは無い）
- 未マージの work/ ブランチ: 変える予定のファイル（run-daily.yml・run-weekly.yml・workers/scheduler/・移す6本）を変えているのは work/1028-lgn の convert-x-posts.yml だけ（今回は呼ぶ側の run-daily・run-weekly を変えるだけで、convert-x-posts.yml は変えない予定）

### 手順1: 毎日にしたときの増え方（移す6本、全件）

材料: cloudflare の run-weekly #8（run 41329205064、2026-11-02 02:00 JST、`scheduled: true / apply: true`、success）のジョブのログと、各本のコード。

| 本（run-weekly のジョブ） | (a) 変わらない日にも書くか（コードの分岐） | (b) 外部 API と費用（回数でどう増えるか） | (c) 11/02 の所要時間 |
|---|---|---|---|
| check-meibo（meibo / meibo・meibo / ampai） | 書かない。upsert-issue の `mode: state` は、不一致が無く issue が閉じていれば「閉じるものが無い」で何もしない（11/02 は両ジョブとも `#445(closed) は閉じるものが無い`・`「Ampai の URL が無い期」(無い) は閉じるものが無い`）。不一致が続くあいだは、開いた issue の本文を毎回書き換える（`action === 'update'`。コメントではなく本文の書き換え） | 名簿（公開の CSV）とシートの読み取りだけ。費用なし | 35秒・29秒 |
| sync-birthday-calendar（birthday / sync） | 書かない。`--compare` で差分を出し、`--apply` は差分だけを書く（11/02 は `書き込みました: 追加 0 / 更新 0 / 削除 0`） | Google Calendar API・シートの読み取り。費用なし | 31秒 |
| cleanup-logs（logs / cleanup） | **書く。** `scripts/cleanup_logs.py` の `issue_comment()` は、条件外のログ（`notify`）が1件でもあれば #357 へのコメントを作る（`if not notify: return ""` のほかに、前回と同じかの判定は無い）。upsert-issue の `mode: notice` はコメントを足すだけ。11/02 は条件外 25件（判断待ち 16・書き方の違反 9）で、#357 に 17:01:05Z のコメントが付いた（10/26 の #5 も 198件でコメント）。判断待ちのログは日をまたいで残るので、毎日ほぼ同じ一覧のコメントが付く。削除のコミットは docs/logs だけ | 費用なし | 37秒 |
| check-saikyo-unregistered（saikyo / check） | 書かない。check-meibo と同じ `mode: state`（11/02 は `#609(closed) は閉じるものが無い`）。登録漏れが続くあいだは #609 の本文を毎回書き換える | シートの読み取りだけ。費用なし | 29秒 |
| fetch-x-posts（xfetch / fetch） | 新しい投稿があるときだけ【1】に足す（同じ投稿 ID は足さない。11/02 は「10行を足した（すでにあった投稿 5件は足さない）」）。見出しの行を書いて権限を確かめる（同じ見出しの上書き） | X API の検索。費用は読んだ投稿1件 $0.005（`COST_PER_READ`）。期間の初めが【1】の最も新しい投稿日なので、その日の投稿を毎回読み直す（11/02 は呼び出し 4回、読んだ 15件 = $0.075、うち既にあった 5件）。毎日にすると、読み直しが週1回から毎日になる。新しい投稿の分は同じ。読み直しを1回 5件とすると、月に約 30回×5件×$0.005 = $0.75（週1回なら約 $0.1）で、増えるのは約 $0.65。作業ブランチの dry-run（run 41353785316、success）は上限の見込み（最大 1200件・$6.0）しか出さず、1回分の実際の件数は出さない | 23秒 |
| convert-x-posts（xconvert / convert、all） | 【2】に無い投稿だけを変換して足す（`targets` が0件なら書かない） | Claude・Places とも、変換する投稿1件ごとの呼び出しで、固定の呼び出しは無い（`run_all` は `targets` の件数だけ呼ぶ）。11/02 は 10件で Claude $0.0261・Places 7回。毎日にしても、投稿の数が同じなら費用は同じ | 110秒 |

まとめ:

- (a) に当たる: **cleanup-logs**（毎日、ほぼ同じ内容の #357 へのコメントが付く）
- (b) に当たる本は無い: fetch-x-posts の増え方は月に約 $0.65 の見込み（$1 未満）。読み直しの件数はその日の投稿の数で変わるので、見込みとして書いた
- check-meibo・check-saikyo-unregistered は、問題が続くあいだ本文を毎日書き換えるが、コメント・通知ではない（GitHub は本文の編集では通知しない）ので (a) には当てていない
- 6本の合計の所要時間は、並行に動くので 11/02 で約2分半（xfetch→xconvert の直列が最長、133秒）

### 止まった

止まる条件「手順1の (a)(b) に当たる本がある（案を書いて止まる）」に当たった。手順2（時刻の変更・移す）と手順3（起票・マージ）は行っていない。作業ブランチ work/1102-wkp に成果物は無い（ログだけ）。

cleanup-logs の移し方の案:

1. **cleanup-logs だけ run-weekly に残す（推奨）。** ほかの5本を run-daily へ移す。変える行が少なく、#357 の通知は今と同じ週1回のまま。ログの削除も週1回のまま（7日を過ぎたログを消す本なので、毎日にしても消える日が早まるだけ）
2. 毎日動かし、#357 へのコメントは前回の通知と一覧が変わったときだけにする。`scripts/cleanup_logs.py` か upsert-issue に、#357 の最後の bot のコメントと比べる処理とテストが要る（upsert-issue の `mode: read` の marker で前回のコメントは引ける）。変える量が多い
3. 毎日動かし、削除は毎日、#357 への通知は月曜だけにする（cleanup-logs.yml に曜日の判定を足す）

## 報告

- 状態: 判断待ち
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1102-wkp/docs/logs/CHAT-1102-WKP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため。成果物は無い）
- issue: #564（中断のコメント。閉じていない）
- 判断が必要なこと:
  - cleanup-logs を毎日にすると、条件外のログの一覧（11/02 は25件）のコメントが #357 に毎日ほぼ同じ内容で付く（止まる条件 (a)）。移し方を選んでほしい: (1) cleanup-logs だけ run-weekly に残し、ほかの5本を run-daily へ移す（推奨。変える行が少なく、通知は今と同じ週1回） (2) 毎日動かし、#357 へは前回と一覧が変わったときだけコメントする（コードとテストが要る） (3) 毎日動かし、削除は毎日・#357 への通知は月曜だけ
  - 時刻の変更（run-monthly 02:00・run-weekly 03:00）と、案A の issue の起票も、まだ行っていない（止まる条件で手順2・3に進んでいない）
- 未確認の項目:
  - fetch-x-posts を毎日にしたときの X API の読み直しの件数（見込み 1回 5件で月に約 $0.65 増。その日の投稿の数で変わる。dry-run は上限の見込みしか出さない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5953e2f7）: https://github.com/retroeater/mj-logs/tree/main/guide/5953e2f7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5953e2f7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b1c2c3de.md
