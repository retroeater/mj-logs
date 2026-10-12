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
- 0. 指示欄の末尾（「…この行が指示文の最後の行です。」）が指示文の最後の行と一致することを確認

### 1. 同じ論点の issue・#496

- 検索（カレンダー・calendar・【R#・期日・予定表、open/closed とも）: 当たったのは #96（サイトのカレンダー機能。#496 本文で別 issue と決定済み）・#390・#391・#392・#426・#448・#480・#379・#407・#481（連盟の予定表の取り込み通知）など。issue の期日の予定の置き場所・完了時の扱いを論点にするものは #496 以外に無い
- #496: コメント0件（着手前）、着手中コメント無し。本文の決定は 2026-10-02（独立 issue）・2026-10-03（前提作業＝SA を平野さんの予定表に共有、代替案＝グレー＋【Closed】）。前提の見込みと食い違う決定は無い
- 未マージの work/ ブランチ: chat-side-operations.md・ask-worker.md・docs/decisions/operations.md を変えているものは無い（`git diff --stat origin/cloudflare...<branch>` が空）

### 2. 文書

- chat-side-operations.md「期日とカレンダー」: 1・3項目目を案で置き換え。変更前 26,595 バイトで余裕が29バイトしか無く、案そのままでは 26,896 バイト。同じ節の中で詰めた:
  - 1項目目: 「Google カレンダー」→「カレンダー」、「（専用）」「（カレンダーの色）」を省き、「平野さんの個人の予定表」→「個人の予定表」、コネクタの括弧を「ryoei.pro だけを共有した別アカウント」に
  - 2項目目（繰越し）: 「その回（この予定のみ）を翌日へ繰り越し、済むまで繰り返す」→「その回を済むまで翌日へ繰り越す」、「チャットを開いた時にまとめて行えばよい」→「開いた時にまとめてよい」
  - 3項目目: 検索の手順（件名の「【R#番号】」で引く）は1項目目の件名の決まりから自明なので省いた
  - 4項目目（常設 issue）: 「ラベル「種類: 常設」」→「種類: 常設」、理由の括弧（本文は通知の読み方の説明を兼ねる）を省いた
  - 変更後 26,623 バイト（≦26,624）
- 同じ趣旨の他の記述: `grep` で「平野さんの予定表／平野さんの Google カレンダー／【R#」を探した。文書で置き換え対象は ask-worker.md の1行だけ。docs/notes/scheduler-worker.md の「【R#504】の予定がカレンダーに入っている」はカレンダー名を書いておらず移行後も正しいので変えない。docs/decisions/operations.md の 2026-10-02・10-03 の行は当時の決定の記録なので変えず、新しい決定を足した
- ask-worker.md: 「差し替えの予定は平野さんの Google カレンダーの 2027-09-28」→「差し替えの予定は ryoei.pro カレンダーの 2027-09-28」
- コミット 0e1eb1444

### 3. #496

- コメント: https://github.com/retroeater/mj/issues/496#issuecomment-6118953505 （決定4項目・見込み〈チャット側の案と明記〉。題は変えず、変えるなら候補を書いた）
- 本文の書き換え（書き込み直前に本文が取得時から変わっていないことを確認）。状態・ラベルは変えていない。書き換え前 → 後:
  - 前: 「issue の期限などを平野さんの予定表（Google カレンダー）に入れているが、issue を閉じても予定が残る。issue のクローズで、その issue の予定を自動で消す。」
    後: 「issue の期限などを Google カレンダー（2026-10-12 から専用カレンダー「ryoei.pro」）に入れているが、issue を閉じても予定が残る。issue のクローズで、その issue の予定を自動で済みにする（件名の冒頭に【済】・グラファイト。2026-10-12 に予定を消さない運用に変えた。自動化も同じにする見込み＝チャット側の案）。」
  - 前: 「その issue の予定を探して消す。再オープンのとき予定を戻すかも決める」
    後: 「その issue の予定を探して【済】・グラファイトにする。再オープンのとき【済】を外すかも決める」
  - 前: 「過去の予定（終わった日の予定）を消すかも決める」 → 後: 「過去の予定（終わった日の予定）も済みにするかも決める」
  - 前: 「…`GCP_SA_KEY`）を平野さんの予定表に招待する（平野さんの作業。予定の変更の権限）」 → 後: 「…`GCP_SA_KEY`）をカレンダー「ryoei.pro」に招待する（平野さんの作業。予定の変更の権限）」
  - 前: 「消す前に確かめる範囲（対象のカレンダーの ID、消す件数の上限など。…）」 → 後: 「書き換える前に確かめる範囲（対象のカレンダーの ID、書き換える件数の上限など。…）」
  - 前: 「**前提作業**: 既存のサービスアカウントを平野さんの予定表に「予定の変更」権限で共有する（平野さん、10/13 ごろ）」
    後: 「**前提作業**: 既存のサービスアカウントをカレンダー「ryoei.pro」に「予定の変更」権限で共有する（平野さん、10/13 ごろ。2026-10-03 時点の共有先は平野さんの予定表だったが、2026-10-12 に予定を「ryoei.pro」へ移したため変わる見込み＝チャット側の案）」
  - 前: 「**代替案**: 削除ではなく、予定をグレーにして件名の冒頭に【Closed】を付ける」 → 後: 同文の末尾に「（2026-10-12 の決定「【済】＋グラファイト」がこれに近い）」を足した
  - 「## 参考」に「2026-10-12 の変更: コメント（CHAT-1012-GCL-01）」を足した
- 気づいたこと（変えていない）: #496 本文の件名の例は「【#番号】」だが、chat-side-operations.md の決まりは「【R#番号】」。自動化の実装の時に合わせる
- 題「issue のクローズで Google カレンダーの予定を自動削除する」は指示が本文だけを対象にしているので変えていない（コメントに候補を書いた）

### 決定の記録

- docs/decisions/operations.md に 2026-10-12（CHAT-1012-GCL-01）の決定4項目を足した。作業中の回答: なし

## 報告

- 状態: 完了
- ブランチ: work/1012-gcl
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-GCL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-gcl
- 確認用URL: なし
- マージ: 済（cloudflare へ fast-forward。先頭はこのログを含むコミット）
- issue: #496（コメント・本文の書き換え。状態・ラベルは変えていない）
- 判断が必要なこと: なし（#496 の題の変更案は #496 のコメントに書いた）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 752118a0）: https://github.com/retroeater/mj-logs/tree/main/guide/752118a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/752118a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
