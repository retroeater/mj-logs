# CHAT-0930-CAL-18

- 着手日時: 2026-09-30 20:49（JST）
- 対象issue: #450・#453
- ブランチ: work/0930-cal-450
- 着手時HEAD: 77033776（origin/work/0930-cal-450 と同じ）

## 指示

【Claude作成】Claude Code 向け指示：#450（過去の放送もカレンダーに載せる）を cloudflare へ入れ、今夜のうちに層1の取り直しと初回の作成（約2,500件）を手動実行で行って見届ける Chat-Ref: CHAT-0930-CAL-18 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push、cloudflare へのマージ、書き込みありのワークフローの手動実行（下の手順のもの）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-cal-450 を続けて使う（CHAT-0930-CAL-16 の実装のコミットがあるため）。`git checkout -b work/0930-cal-450 origin/work/0930-cal-450` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる マージ: 承認済み（チャットで、2026-09-30。work/0930-cal-450 を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-16 のログの `## 報告` が「判断待ち」であることを確かめ、状態を「判断待ち → 続き: CHAT-0930-CAL-18」と直す。

目的
CAL-16 の実装を本番に入れ、CAL-16 の報告の順番の案（除外タブ → マージ → 層1の取り直し → 初回の作成）どおりに、明朝の毎朝の実行より前に初回の作成まで終える。
決定（2026-09-30、平野さん）

* CAL-16 の順番の案で、今夜すべて行う（マージ → 層1の取り直し → 初回の作成を手動実行で見届ける）。朝6時（JST）ころまでに終える。
* 除外のタブは、平野さんが CAL-16 の表の4件で作った。タブ名は「【4】カレンダー非掲載」に変えた（CAL-12・CAL-16 の「カレンダーの除外」を置き換え）。見出し（動画ID・参考:題名・理由）は CAL-16 の表のまま。
* #453 は、#450 の実装が cloudflare に入ったら閉じる（CAL-16 の指示の決定）。JPMLリーグは大会の一覧に足さない（CAL-16 のとおり）。

前提（チャット側。平野さんの決定ではない）

* CAL-16 の見込み: 層1の取り直しは 4,050行の追記、返らない 0本、層1 14,177 → 18,230行。初回の作成は、除外の後 2,503件（直す 0・消す 0）、約21分。載せる予定は 2,649件（枠 2,525・予定表 124）。大会名の追加で予定表の【2】が29行変わる（【3】は増減しない）。
* 毎朝の実行（cron 17:43 UTC＝02:43 JST、最近は 06:30〜07:40 JST に動く）と重ならないようにする。

手順

1. 除外タブの確かめとタブ名の変更: コードが読む除外のタブ名を「カレンダーの除外」から「【4】カレンダー非掲載」に変える（定数と、それを使う所・テスト・docs/notes/yotei-sheet.md など資料の記述をすべて。変えた所を挙げる）。テストを通してコミットする。そのうえで /live の3層のスプレッドシートに「【4】カレンダー非掲載」タブがあり、見出し（動画ID・参考:題名・理由）で読めて、動画IDが CAL-16 の表の4件（oc5eK9LEXu0・p4G1enKcSTw・2UQGDePTDl0・0PuFUIz_dk0）と同じであることを確かめる。無い、または違えば止まる（マージもしない）。新しいタブ名で書き込みなしの見込みを1回出し、除外が4件効いている（作る 2,503）ことを確かめる。
2. マージ: テストと `python3 scripts/check_asset_limits.py` を通し、差分が CAL-16 のもの（同期のコード・テスト・ワークフロー・資料・ログ）と手順1のタブ名の変更のほかに無いことを確かめて、CLAUDE.md の手順どおり cloudflare へ入れる。#453 に「#450 の実装が cloudflare に入った」旨を1件コメントして閉じる。
3. 層1の取り直し: `update-live-channel.yml` が実行中でないことを確かめ、cloudflare で `apply` と `backfill` を付け、`yotei_apply`・`calendar_apply` は外して起動する。成否、追記した行数、返らない本数、層1の行数、コミット（Actions のもの）の SHA、/live の再生成の成否を書く。CAL-16 の見込み（追記 4,050・返らない 0）と大きく違えば止まる。
4. 初回の作成: 手順3の完了を待ち、実行中の実行が無いことを確かめて、cloudflare で `calendar_apply` だけを付けて起動する（ほかは外す）。直前に同じ入力で書き込みなしの見込みを1回出してもよい。作成が上限などで途中で止まったら、何件目で・何時に・どんなエラーで止まったかを書いて、そこで止まる（翌朝の実行が続きから作る）。終わったら、作る／直す／消すの件数、カレンダーの予定の総数、#450 のコメント（ワークフローが残すもの）を書く。見込み（作る 2,503・直す 0 前後・消す 0）と大きく違えば止まる。
5. 確かめ: 公開 iCal などで「mj_放送対局」を読み、予定の総数と、過去の予定を年ごとに数件（2015年・2020年・2024年など）選んで、件名・開始・終了・説明欄が今日以降の枠の予定と同じ形であることを書く。除外の4件が載っていないことも確かめる。#450 に結果を1件コメントする（#450 は明朝の毎朝の実行を確かめるまで開けておく）。

止まる条件

* CAL-16 の `## 報告` が「判断待ち」でない。
* 手順1で「【4】カレンダー非掲載」タブが無い・違う、または新しいタブ名で除外が効かない。手順2の差分に想定外のものがある、またはマージで衝突する。
* 手順3・4の結果が見込みと大きく違う、または失敗する（マージ済みのまま原因を書いて止まる）。
* 手順4を始める時点で 05:30 JST を過ぎている、または毎朝の実行が動いている（そのときは作成を始めず、翌朝の実行に任せたことを書いて止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、明朝の毎朝の実行の見込み（作成が途中で止まったなら続きの件数、止まらなかったなら作る 0・直す 数件・消す 0）を書く。決定は CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-18.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-18` は0件。ローカルの `work/0930-cal-450` は origin/work/0930-cal-450（77033776）と同じ（未コミットの変更なし）
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-16 の `## 報告` は「判断待ち（…マージは未承認）」だったので「判断待ち → 続き: CHAT-0930-CAL-18」に直した（このコミットに含める）

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal-450
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-450/docs/logs/CHAT-0930-CAL-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-450
- 確認用URL: なし
- マージ: 未
- issue: #450・#453
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9fd7d3a9）: https://github.com/retroeater/mj-logs/tree/main/guide/9fd7d3a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9fd7d3a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
