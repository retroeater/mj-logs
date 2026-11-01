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

## 報告

- 状態: 作業中
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1102-wkp/docs/logs/CHAT-1102-WKP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

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
