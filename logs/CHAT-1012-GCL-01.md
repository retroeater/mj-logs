# CHAT-1012-GCL-01

- 着手日時: 2026-10-12
- 対象issue: #496
- ブランチ: work/1012-gcl
- 着手時HEAD: 7b3e13348

## 指示

【Claude作成】Claude Code 向け指示：ryoei.pro の予定を専用カレンダー「ryoei.pro」に移した運用を文書と #496 に反映する Chat-Ref: CHAT-1012-GCL-01 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-gcl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-gcl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
チャット側が作る ryoei.pro の予定の置き場所を、平野さんの個人の予定表から専用の Google カレンダー「ryoei.pro」に変え、完了時の扱い（消す→【済】＋グラファイト）を変えた。これを文書と #496 に反映する。
決定（2026-10-12、平野さん）

* ryoei.pro 関連の予定は、専用の Google カレンダー「ryoei.pro」に作る。既存の予定はすべて移し、元のカレンダー（平野さんの個人の予定表）からは消した（移行・削除はチャット側で 2026-10-12 に済み）
* 予定の色は指定しない（平野さんの手元でカレンダーの色をトマトにしている）
* 予定が完了したら、消さずに件名の冒頭に【済】を付けて、色をグラファイトにする
* チャットから元のカレンダーへの変更権限は外す。方法は「仕組みで分ける」: 「ryoei.pro」カレンダーだけを別の Google アカウントに共有し（平野さんが共有済み）、チャットのカレンダーのコネクタをそのアカウントにつなぎ替える

前提（チャット側。平野さんの決定ではない）

* 文書に書くのはカレンダーの名前「ryoei.pro」だけにする。カレンダー ID・共有先のアカウント名は書かない（mj-logs は public）
* docs/notes/chat-side-operations.md「期日とカレンダー」の1項目目と3項目目の差し替え案（実物に合わせて文言は変えてよい）:
   * 「issue に期日を書かせるときは、同時に Google カレンダー「ryoei.pro」（専用）に予定を作る。 件名は「【R#番号】…」、説明の冒頭に issue のリンク、色は指定しない（カレンダーの色）。issue 番号の無い ryoei.pro の予定も同じ所へ。平野さんの個人の予定表には書かない（コネクタは ryoei.pro だけを共有したアカウントにつなぐ）」
   * 「予定が済んだら・issue のクローズを知ったら、消さずに件名の冒頭に【済】を付けてグラファイトにする。 説明欄の残課題は行き先の予定へ移す」
* docs/notes/ask-worker.md の「差し替えの予定は平野さんの Google カレンダーの 2027-09-28」は「ryoei.pro カレンダーの 2027-09-28」に直す（予定は移行済み）
* #496（issue のクローズでカレンダーの予定を自動削除）は、前提作業の共有先が個人の予定表から「ryoei.pro」カレンダーに変わり、クローズ時の処理も「削除」から「件名の冒頭に【済】・グラファイト」に変わる見込み（チャット側の案。#496 の 2026-10-03 の代替案「グレー＋【Closed】」に近い）。カレンダーの予定【R#496】（10/13）の説明欄にも同じ追記を済ませた（要確認: #496 の本文・コメントの現状）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語: カレンダー、calendar、【R#、#496）、#496 以外にあれば止まって報告する。#496 の本文とコメントを読み、上の「前提」の #496 の見込みと食い違う決定（他のセッションの決定）があれば止まる。
2. docs/notes/chat-side-operations.md「期日とカレンダー」節と docs/notes/ask-worker.md の該当行の今の内容を読み、上の案で置き換える（追記ではなく置き換え。同じ趣旨の記述が他にあれば置き換え・統合してよく、どう処理したかを報告に書く）。chat-side-operations.md は変更後に 26,624 バイト（警告域）以下に収める。超えるなら同じ節の文言を詰めて収め、それでも超えるなら止まる。
3. #496 にコメントで「決定」の4項目と、前提の #496 の見込み（チャット側の案と明記）を書く。本文に「自分の予定表（hirano@ryoei.net）に共有」「予定を削除」などの記述があれば、決定に合わせて書き換え、書き換え前後をログに引用する。状態・ラベルは変えない。完了時に docs/decisions/operations.md に決定を足す（CLAUDE.md「作業ログ」節）。

止まる条件

* 同じ論点の issue（#496 以外）がある、#496 に食い違う決定がある、他セッションの着手中コメントがある
* chat-side-operations.md が節の中の詰めでは 26,624 バイト以下に収まらない
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が chat-side-operations.md「期日とカレンダー」節、または ask-worker.md の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。マージしてよいのは、docs/notes/chat-side-operations.md・docs/notes/ask-worker.md・docs/decisions/・docs/logs/ の変更だけのとき
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-GCL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-GCL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 GCL: 全ブランチのコミット・`docs/logs/` の履歴に無し（`git fetch --unshallow` の後に確認）。`origin/work/1012-gcl` 無し → `git checkout -b work/1012-gcl origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている

## 報告

- 状態: 着手中
- ブランチ: work/1012-gcl
- ログ: https://github.com/retroeater/mj/blob/work/1012-gcl/docs/logs/CHAT-1012-GCL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-gcl
- 確認用URL: なし
- マージ: 未
- issue: #496
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 90c58c63）: https://github.com/retroeater/mj-logs/tree/main/guide/90c58c63

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/90c58c63/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
