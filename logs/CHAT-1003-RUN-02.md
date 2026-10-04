# CHAT-1003-RUN-02

- 着手日時: 2026-10-04
- 対象issue: #459・#461・#485・#142
- ブランチ: work/1003-run-02
- 着手時HEAD: 3dfac105

## 指示

【Claude作成】Claude Code 向け指示：10/1 取得の query-page.csv を集計する（#459・#461・#485 の基準値・#142 の公開前の値） Chat-Ref: CHAT-1003-RUN-02 マージ: 承認済み（チャットで、2026-10-03。変更は docs/gsc/・docs/logs/・docs/decisions/ のみ） 貼る時機: いつでも（CHAT-1003-RUN-01 の結果は使わない。作業ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1003-run-02〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1003-run-02 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1003-run-02 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1003-run-02 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: docs/gsc/（集計結果のファイル）・docs/logs/・docs/decisions/ と、issue のコメント（#459・#461・#485・#142。直す候補があれば #5 にも1行）。コード・ワークフロー・生成物・ページの title と description は変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
10/1 の月次取得（期間 2026-09-01〜09-28）の query-page.csv を読む作業を、1本の指示にまとめて行う。平野さんのカレンダーの 10/2 の予定「query-page.csv の集計を指示」に当たる。
決定（2026-10-03、平野さん）

* 10/1 取得分の query-page.csv の集計を1本にまとめて行う。対象は次の4つ
   * #459: 掲載順位4〜20位・クリック率の低い「クエリ×ページ」の洗い出し
   * #461: 選手名のクエリの着地先ページの集計
   * #485: 旧表 jpml_titles.html への着地を「廃止直前の基準値」として記録
   * #142: title/ の公開（09-28）の前後の着地先の比較（元は #413 の予定。#413 の GC-20 の表と同じ語で比べる）
* マージしてよい（docs と issue のコメントのみ）

前提（チャット側。平野さんの決定ではない）

* 識別子 RUN は同じチャットの RUN-01・RUN-03 でも使っている（別々のセッションに貼られることがある）。識別子の確認で RUN-01・RUN-03 のコミットやログが見つかっても、同じチャットの指示なので重複ではない
* 材料: `docs/gsc/2026-10-01/20260901-20260928/query-page.csv`（見出しを除いて73行。CHAT-1001-GSC-01 のログ `## 経過`「1. #269 の照合」）。同じフォルダの query.csv・page.csv も使ってよい
* #459: 条件（順位の範囲・表示回数・クリック率の閾値）は #459 の本文に従う。本文に数が無ければ、順位4〜20位の組を表示回数の多い順に全件並べ、クリック率を添える。各組について、今のページの title・description にクエリの語が入っているかを見る（直さない）。直す候補があれば #459 のコメントに挙げ、#5（title 整備の受け皿。docs/decisions/seo-bing.md の CHAT-0930-BNG-05）に、そのコメントを指す1行をコメントする
* #461: 選手名のクエリの見分け方と表の形は、#461 の本文と前例（#122 の `name-param-pages.md`・`tournament-queries.md`。場所はリポジトリを探す）に合わせる。jpml_titles.html（`?name=` 付きを含む）への着地は「旧表（2026-09-30 に廃止。今は /title/ へ301）」と読めるように書く（#461 の 09-30 のコメント、CHAT-0930-DUP-06）
* #485: 旧表 jpml_titles.html（`?name=` 付きを含む）への着地のクリック・表示を、合計と行ごとに記録する。期間 09-01〜09-28 はすべて 09-30 の廃止より前なので、これが「廃止直前の基準値」になる。次は 11/2 に 11/1 の取得分と比べる（#485 の本文の期日）
* #142: 取得の期間（〜09-28）は title/ の公開（09-28）の当日までなので、公開後の値はほぼ入らない見込み。 #413 の GC-20 の表（2026-09-21 の基準値）と同じ語について今回の値を並べ、「公開前の2回目の値」として #142 にコメントする。公開後の比較は 10/7 の取得（09-09〜10-06、#142 の本文の期日）で行う、と書き添える。今回の期間に title/ 配下への着地があれば、その行をそのまま書く
* 集計結果のファイルは、前例に合わせて docs/gsc/ に置く（フォルダとファイル名は前例に合わせる）。issue のコメントは要点と、ファイルを指す1行にする
* ログは public（mj-logs）。選手名ごとの表はログに書かず、ファイルと issue に書く。ログには件数と要約だけを書く
* 4つの issue はクローズしない。#459・#461 の本文の完了条件を満たしたかを、報告の「判断が必要なこと」に書く

手順

1. #459・#461・#485・#142 の本文とコメントを読み、Open であること、この指示と食い違わないこと、他セッションの着手中コメントが無いことを確かめる。#413 の GC-20 の表の場所（issue のコメントかログか）も確かめる
2. query-page.csv を読み（見出しを除いて73行であることを確かめる）、4つの集計を行って結果のファイルを書く
3. 各 issue に結果をコメントする（末尾に Chat-Ref）
4. マージする（冒頭の「マージ:」の行）

止まる条件

* query-page.csv が無い、または見出しを除く行数が73でない
* 対象の issue が閉じている、または本文がこの指示と食い違う（その issue の分だけ飛ばし、ほかは続けて、報告に書く。4つとも当たれば止まる）
* 対象の issue に他セッションの着手中コメントがある（その issue の分だけ飛ばし、報告に書く）
* #413 の GC-20 の表が見つからない（#142 の分だけ飛ばし、報告に書く。全体は止めない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-RUN-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-RUN-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致。
- 識別子の確認: `CHAT-1003-RUN-02` のコミットは 0件。RUN のコミットは同じチャットの RUN-01（work/1003-run-01、マージ済み）のみ。
- `origin/work/1003-run-02` は無く、`git checkout -b work/1003-run-02 origin/cloudflare` で作成。

## 報告

- 状態: 作業中
- ブランチ: work/1003-run-02
- ログ: https://github.com/retroeater/mj/blob/work/1003-run-02/docs/logs/CHAT-1003-RUN-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-run-02
- 確認用URL: なし
- マージ: 未
- issue: #459・#461・#485・#142
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 200ed2c4）: https://github.com/retroeater/mj-logs/tree/main/guide/200ed2c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d951d060.md
