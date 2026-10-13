# CHAT-1013-ACF-11

- 着手日時: 2026-10-13
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: dcab5f22c823ddcbcadd983e59a2ef34b02c19ac

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1013-ACF-10 の判断待ちへの回答 — #558 の段階1のうち books/ の著者写真（`_200x200`）だけを先に直してマージする。houou_race の丸は houou/ と一緒に work/1012-dsn のマージ後に回す Chat-Ref: CHAT-1013-ACF-11 マージ: 承認済み（チャットで、2026-10-13。平野さんが ACF-10 の判断待ちに (a) を選んだ）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: いつでも（CHAT-1013-ACF-10 は完了報告済み〈状態: 判断待ち〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜10 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。このブランチには ACF-08・ACF-10 のログと docs/decisions/saikyo.md の追記、cloudflare の取り込みが未マージで載っている（どちらも止まる条件に当たってマージしなかった）。この場合に限り、マージ済みでなくても止まらず、`origin/work/1013-acf-xsize` を起点に続ける。ログの先行 push を済ませてから `origin/cloudflare` を取り込む（ACF-10 で順番を誤ったため。cloud-sessions.md「作業ブランチの用意」の続きのブランチの場合の手順。`checkout -B` は使わない）。cloudflare と ACF-08・ACF-10 のログ・docs/decisions 以外の差がブランチにあれば止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-ACF-10 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
ACF-10 で、`generate_houou_race.py` の `IMAGE_SIZE` が houou/ の選手の写真（72〜88px）・表のアイコン・`houou/race/*.json` にも効くとわかり、止まった。work/1012-dsn と重ならない books/ の著者写真だけを先に直してマージする。シートの値をそのまま出しているのは books/ だけ（ACF-07 の要点）なので、これで #558 の段階2（シートを `_400x400` にそろえる）へ進める。
決定（2026-10-13、平野さん）

* ACF-10 の「判断が必要なこと」は (a) にする: books/ の `_200x200` だけを先に行う。houou_race の丸（28px）の `_80x80` 化は、houou/ の表のアイコンと一緒に work/1012-dsn のマージ後に行う（そのとき `IMAGE_SIZE` が houou/ の写真にも効くことを踏まえて直し方を決める）

前提（チャット側。平野さんの決定ではない）

* 直すのは `generate_books_pages.py` の `author_image_url()` だけ（`with_size(url, SIZE_200)`、96px 表示）。`generate_houou_race.py`・`generate_houou_pages.py`・`lib/x_images.py`・`lib/sns.py` は変えない
* ACF-07 の (b) では books/ の X 画像は `_80x80` 81件・`_400x400` 76件。直した後はすべて `_200x200` になる見込み
* `with_size()` が X 以外の URL と `abs.twimg.com` の既定アイコンをどう扱うかは、テストで確かめる
* 使う skill は無い

手順

1. ログを先行 push してから `origin/cloudflare` を取り込む。#558 の状態と最新のコメントを確かめ、他セッションの着手中コメントが無ければ着手中のコメントを残す。`git branch -r --no-merged origin/cloudflare` の各ブランチで、`generate_books_pages.py`・`lib/x_images.py`・books/ に触れるものが無いことを確かめる。`author_image_url()` の呼び出し元が books/ の生成だけであることを確かめる（ほかのページにも効くなら止まる）
2. テストを先に足し、直す前のコードで通らないことを確かめる（books の著者写真が `_200x200` になる。X 以外の URL と既定アイコンは変わらない）。そのうえで直し、`python3 -m unittest discover -s scripts/tests` を通す
3. 全ページを再生成し、生成物の差が books/ だけで、X の画像 URL の末尾の変化に限られることを確かめる（末尾ごとの件数の前後をログに書く）。変わった URL から3人分以上を GET して 200 を確かめる。プレビュー（docs/notes/cloud-sessions.md の手順）で、books/ の著者写真が代替画像に落ちていないことを見る。docs/notes/sns-book.md（または X の画像の大きさを書いている文書）に「ページの大きさは各生成スクリプトの定数」と「houou_race の丸・houou/ の表のアイコンの `_80x80` 化は dsn のマージ後（未了。`IMAGE_SIZE` は houou/ の写真にも効く）」を書き足す。上の「決定」を docs/decisions/saikyo.md の #558 の項に足す。#558 に結果と残り（houou_race・houou/ の表のアイコン・段階2）をコメントしてマージする。ACF-10 のログの状態の末尾に `/ 続き: CHAT-1013-ACF-11` を足す

止まる条件

* CHAT-1013-ACF-10 の状態が「判断待ち」でない、#558 が Closed、または他セッションの着手中コメントがある
* `origin/work/1013-acf-xsize` に、cloudflare・ACF-08 と ACF-10 のログ・docs/decisions/saikyo.md の追記以外の差がある
* 未マージの work/ ブランチが `generate_books_pages.py`・`lib/x_images.py`・books/ に触れている
* `author_image_url()` が books/ 以外のページの生成にも使われている、または前提と実物が違い直す箇所を決められない
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が books/ 以外に出る、または末尾以外の変化がある
* 確かめで 404 が出る（URL と人をログに書いて止まる）、またはプレビューで代替画像に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり（ACF-08・ACF-10 のログと docs/decisions の追記も一緒に入る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 346c65b1）: https://github.com/retroeater/mj-logs/tree/main/guide/346c65b1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/346c65b1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
