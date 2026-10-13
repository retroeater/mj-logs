# CHAT-1013-ACF-10

- 着手日時: 2026-10-13
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: 7547d6975（origin/work/1013-acf-xsize。取り込み後 013da854c）

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1013-ACF-08 の判断待ちへの回答 — #558 の段階1のうち、work/1012-dsn と重ならない books/（`_200x200`）と houou_race の丸（`_80x80`）だけを先に直す。houou/ の表のアイコンは DSN のマージ後に回す Chat-Ref: CHAT-1013-ACF-10 マージ: 承認済み（チャットで、2026-10-13。平野さんが ACF-08 の判断待ちに (2) を選んだ）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: いつでも（CHAT-1013-ACF-08 は完了報告済み〈状態: 判断待ち〉。CHAT-1013-ACF-09 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜09 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。このブランチには ACF-08 のログと docs/decisions/saikyo.md の追記が未マージで載っている（ACF-08 は止まる条件に当たってマージしなかった）。この場合に限り、マージ済みでなくても止まらず、`origin/work/1013-acf-xsize` を起点に続け、着手時に `origin/cloudflare` を取り込む（cloud-sessions.md「作業ブランチの用意」の続きのブランチの場合の手順。`checkout -B` は使わない）。cloudflare と ACF-08 以外の差がブランチにあれば止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-ACF-08 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
ACF-08 は、未マージの `work/1012-dsn`（DSN のチャット）が houou/ の表のアイコン（`generate_houou_pages.py` の `avatar_html()`）と `assets/houou_race.js` を変えていたため止まった。重ならない2か所（books/ の著者写真と houou_race の丸）だけを先に直してマージし、#558 の段階2（シート側）へ進めるようにする。
決定（2026-10-13、平野さん）

* ACF-08 の「判断が必要なこと」は (2) にする: books/ と houou_race（`generate_houou_race.py` の `IMAGE_SIZE`）だけを先に行い、houou/ の表のアイコンの `_80x80` 化は `work/1012-dsn` が cloudflare に入った後に別の指示で行う

前提（チャット側。平野さんの決定ではない）

* 直す箇所と大きさは ACF-08 の指示文の前提のとおり（要確認）: books/ は `generate_books_pages.py` の `author_image_url()` で `with_size(url, SIZE_200)`、houou_race は `IMAGE_SIZE = SIZE_80`。`lib/x_images.py`・`lib/sns.py`・`generate_houou_pages.py`・`assets/houou_race.js` は変えない
* houou/ の表のアイコンは、今も `with_size()` で `_200x200` に書き換えて出している（ACF-07 の (b)）。シートの末尾によらないので、houou/ を後に回しても段階2（シートを `_400x400` にそろえる）でページの差は出ない見込み。段階2の指示は、この指示のマージ後に、houou/ を待たずに出す予定
* ACF-08 の時点で `work/1012-dsn` は `generate_books_pages.py`・`generate_houou_race.py`・`lib/x_images.py` に触れていなかった。着手時に改めて確かめる（DSN-07 が作業中のため）。dsn が houou_race/*.json（生成物）を変えている場合は、再生成の差がぶつかるので止まる
* 使う skill は無い

手順

1. #558 の状態と最新のコメントを確かめ、他セッションの着手中コメントが無ければ着手中のコメントを残す（ACF-08 では残していない）。`git branch -r --no-merged origin/cloudflare` の各ブランチについて、`generate_books_pages.py`・`generate_houou_race.py`・`lib/x_images.py`・books/・houou_race/*.json に触れるものが無いことを確かめる（`work/1012-dsn` が `generate_houou_pages.py`・`assets/houou_race.js` だけに触れているのは止まる条件に当たらない）
2. テストを先に足し、直す前のコードで通らないことを確かめる（books の著者写真が `_200x200`、houou_race の丸が `_80x80` になること。X 以外の URL と `abs.twimg.com` の既定アイコンは変わらないこと）。そのうえで2か所を直し、`python3 -m unittest discover -s scripts/tests` を通す
3. 全ページを再生成し、生成物の差が books/・houou_race だけで、X の画像 URL の末尾の変化に限られることを確かめる（末尾ごとの件数の前後をログに書く）。変わった URL から数人分（books・houou_race から各2人以上）を GET して 200 を確かめる。プレビュー（docs/notes/cloud-sessions.md の手順）で、books/ の著者写真と houou_race の丸が代替画像に落ちていないことを見る。docs/notes/sns-book.md（または X の画像の大きさを書いている文書）に「ページの大きさは各生成スクリプトの定数」と「houou/ の表のアイコンは dsn のマージ後に `_80x80` にする（未了）」を書き足す。上の「決定」を docs/decisions/saikyo.md の #558 の項に足す。#558 に結果と残り（houou/ の表のアイコン・段階2）をコメントしてマージする。ACF-08 のログの状態の末尾に `/ 続き: CHAT-1013-ACF-10` を足す

止まる条件

* CHAT-1013-ACF-08 の状態が「判断待ち」でない、#558 が Closed、または他セッションの着手中コメントがある
* `origin/work/1013-acf-xsize` に cloudflare と ACF-08 以外の差がある
* 未マージの work/ ブランチが `generate_books_pages.py`・`generate_houou_race.py`・`lib/x_images.py`・books/・houou_race/*.json に触れている
* 前提の箇所（`author_image_url()`・`IMAGE_SIZE`）が実物と違い、直す箇所を決められない
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が books/・houou_race 以外に出る、または末尾以外の変化がある
* 数人分の確かめで 404 が出る（URL と人をログに書いて止まる）、またはプレビューで代替画像に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1733ea64）: https://github.com/retroeater/mj-logs/tree/main/guide/1733ea64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1733ea64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
