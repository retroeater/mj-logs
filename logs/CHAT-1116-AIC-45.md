# CHAT-1116-AIC-45

- 着手日時: 2026-11-16（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: 56d8d22

## 指示

【Claude作成】Claude Code 向け指示：（質問欄の週次の書き出し）Google の一時的な失敗に再試行を足し、Actions から手で再実行できる道（Worker の認証付きの入口＋workflow_dispatch のワークフロー）を作る。マージ後に再実行して、11/16 に書き出せなかった 11/09〜11/15 の行をブックに入れる Chat-Ref: CHAT-1116-AIC-45 マージ: 承認済み（チャットで。2026-11-16、平野さん。新しい機能についての個別の承認）。次の2つを1つでも満たさなければマージしない

* テストが通る（今の検査を含む）
* 質問欄の答え（`/api/ask` の応答）を作る処理に変更が無い（変えるのは週次の書き出しと、新しい入口・ワークフロー・文書だけ） 貼る時機: 平野さんが Secret `ASK_WEEKLY_TOKEN` を Worker（mj-ask）と GitHub Actions（retroeater/mj）の両方に登録したとチャットで伝えた後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。GitHub Actions の Secret の名前の一覧に `ASK_WEEKLY_TOKEN` があるかを確かめ（値は見ない・書かない）、無ければ止まる。識別子 `AIC` はこのチャットの系列（AIC-01〜44 で使用。AIC-17・24・31 は欠番）。CHAT-1109-AIC-44 のログの `## 報告` には手を入れない（この指示はその続きではない。持ち越しの一覧は AIC-44 のログに残す）。

目的
2026-11-16 03:00 JST の週次の書き出し（Worker `mj-ask` の cron）で、Google の Sheets API が HTTP 500 を返し、ブックへの書き出しだけが失敗した。今後は一時的な失敗を自動でやり直し、それでも失敗したときは Actions の「Run workflow」で書き出しだけを再実行できるようにする。今回の分（11/09〜11/15）はマージ後の再実行で入れる。
決定（2026-11-16、平野さん）

* 週次の書き出しに、Google の一時的な失敗のときの再試行を足す
* 書き出しは、手作業の SQL を使わず、ジョブの再実行で取り直す運用にする。再実行の道は「Actions から再実行」（ワークフローから Worker の書き出しを呼ぶ。合言葉の Secret を Worker と GitHub に登録する）。毎日の自動のやり直し（cron を毎日にする）は採らない
* この変更は、上の「マージ:」の行の条件でマージしてよい
* （2026-11-09 の決定。記録が漏れていたもの）CHAT-1109-AIC-43 で、指示に無く docs/notes/chat-side-operations.md の UA（`mj-internal`）の項目を CLAUDE.md への参照1行に置き換えたことは「このままでよい」（2026-11-09 22:21、平野さん）。AIC-43・44 のログに「回答なし」とあるもの

前提（チャット側。平野さんの決定ではない）

* 失敗の記録（平野さんが送った Workers Logs、11/15 18:00:11 UTC）: `週次: ブックへの書き出しに失敗: ブックの情報: HTTP 500`、続けて `週次: {"cutoff":"2026-08-18T03:00:05+09:00","until":"2026-11-16T00:00:00+09:00","sheet":null,"d1Deleted":0}`。ブックの最後の行は 2026-11-08T23:12:27+09:00（平野さんの CSV とスクリーンショット）。11/09 の回は成功していた
* docs/notes/ask-worker.md「週次の cron」によると、書き出しは「ブックの最後の行の日時より後」かつ「今週の月曜 0 時（JST）より前」の D1 の行を足すので、同じ回を2度動かしても同じ行は二度入らず、取りこぼした週は次の回に入る。再実行は、この同じ処理（`src/weekly.mjs`、入口は `index.mjs` の `scheduled`）を呼ぶ形にする（関数名・行番号は（要確認）。開いた関数名と行番号をログに書く）
* 再試行の案: Google の呼び出し（トークン・ブックの情報・追記・行の削除）が 429・5xx・ネットワークの失敗のとき、数秒あけて2回までやり直す。それでも失敗したら今と同じ記録（`週次: ブックへの書き出しに失敗: …`）を残し、`sheet: null` で終わる。待ち時間・回数は実物に合わせて決めてよい（理由をログに書く）
* 入口の案: `POST /api/ask/weekly`（mj-ask のルートで届くパスか（要確認）。届かなければ届くパスにしてよい）。`Authorization: Bearer <ASK_WEEKLY_TOKEN>` を時間の一定な比較で確かめ、合わなければ 401、Secret が Worker に無ければ 404。POST 以外は 405。応答は `週次:` の行と同じ要約（cutoff・until・sheet〈appended・sheetDeleted〉・d1Deleted）だけで、質問文・答えは返さない。来訪者の質問の受付（回数の制限・Origin の確かめ）とは別の扱いにし、受付の処理は変えない
* ワークフローの案: `.github/workflows/ask-weekly.yml`（名前「AIボットの週次書き出し」）。`workflow_dispatch` だけ（予約実行は Worker の cron のまま）。Secret は env で受け、curl で入口を1回呼び、応答の要約を出し、HTTP 200 以外は failure。UA に `mj-internal` を含める（CLAUDE.md「判断・作業の原則」）
* 文書: docs/notes/ask-worker.md「週次の cron」（再試行と再実行の手順。「ブックに今週の行が無ければ Actions の『AIボットの週次書き出し』を Run workflow」）、docs/notes/github-actions.md のワークフローの表、docs/decisions/houou.md（上の「決定」4つ）

手順

1. 実物で確かめる: 週次の書き出しの関数と Google の呼び出し、`index.mjs` の入口、mj-ask のルート（どのパスが届くか）を読み、上の前提と食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/src/weekly.mjs`・`workers/ask/index.mjs` の同じ関数を変えていないか確かめる
2. 作る: 再試行・認証付きの入口・ワークフロー・文書（上の前提）。テストを足す: (a) Google が 500 を1回返した後 200 なら書き出せる (b) 500 が続けば今と同じ失敗の記録で終わる (c) 入口: Bearer が合わない 401・Secret が無い 404・GET 405・合えば要約を返す (d) 同じ書き出しを2回呼んでも行が重ならない（既存の検査で足りれば、その検査名を書く）。テストが通れば cloudflare へマージする（冒頭の「マージ:」の行の条件）
3. マージ後に再実行する: Workers Builds のビルドの成否を確かめ（docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」）、成功してから `ask-weekly.yml` を `actions_run_trigger`（ref `cloudflare`）で起動する。応答の要約の `sheet.appended` が1以上で、`until` が 2026-11-16T00:00:00+09:00 であることを確かめる。もう1回起動し、`appended` が 0 になる（重ならない）ことを確かめる。結果をログと #654 のコメントに書く（件数と時刻だけ。質問文は書かない）

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる（並走すると Sheets API の上限〈429〉で失敗する、#662）
止まる条件

* 前提（書き出しの処理・取りこぼした週を拾う仕組み・ルート）が実物と食い違う
* Secret `ASK_WEEKLY_TOKEN` が GitHub Actions に無い（0章）
* 質問欄の答えを作る処理を変えないと作れない
* 未マージの work/ ブランチが `workers/ask/src/weekly.mjs`・`workers/ask/index.mjs` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の再実行が 401・404 を返した、または `appended` が0だった（戻さずに止まり、応答の要約と Workers Logs で分かる範囲をログに書く。Worker の Secret の登録漏れは平野さんの手作業として「判断が必要なこと」に書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、次の月曜（2026-11-23 03:00 JST）の cron の回で `週次:` の `sheet` が null でないことを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-AIC-45.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-AIC-45 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 56d8d22f）: https://github.com/retroeater/mj-logs/tree/main/guide/56d8d22f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/56d8d22f.md
