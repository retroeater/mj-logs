# CHAT-1013-ACF-08

- 着手日時: 2026-10-13
- 対象issue: #558
- ブランチ: work/1013-acf-xsize
- 着手時HEAD: 4c9b6825c10c968de1a97f5aa517c863c2bbd3a8

## 指示

【Claude作成】Claude Code 向け指示：#558 の段階1 — ページ側で X の画像を表示に合う大きさに変える（books/ は `_200x200`、houou/ の表のアイコンと houou_race の丸は `_80x80`。jpml_pros は `_80x80` のまま） Chat-Ref: CHAT-1013-ACF-08 マージ: 承認済み（チャットで、2026-10-13。平野さんが #558 の案〈段階はページ側→シート側の2本〉を選んだ）。条件は「止まる条件」に当たらず、「完了条件」の確かめがすべて通ること 貼る時機: いつでも（CHAT-1013-ACF-07 は完了報告済み〈状態: 判断待ち〉。CHAT-1013-ACF-09 とは別のセッションに貼る。ブランチも別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-xsize の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06・07 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1013-acf-xsize を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-xsize origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-ACF-07 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
#558 の段階1。SNS ブックの X の画像 URL を `_400x400` にそろえる（段階2、別の指示）前に、ページ側で表示に合う大きさを使う形にする。そろえた日にページの重さが変わらないようにするため、また表示に対して大きすぎる画像を小さくして表示を速くするため。
決定（2026-10-13、平野さん。#558 の案〈https://github.com/retroeater/mj/issues/558#issuecomment-6135967577 〉のとおり）

* jpml_pros.html の X アイコン（48px 表示）は `_80x80` のままにする
* シートの X画像URL は案 A（毎日の更新〈`update_sns_book.py`〉で前の値を `_400x400` に直して書く）でそろえる（段階2）
* 段階はページ側（段階1）→ シート側（段階2）の2本の指示にする
* 毎朝の検知で SNS ブックの全行を見るとき（CHAT-1013-ACF-06 の案 B）、見る X の画像はシートの値の大きさ（`_400x400`）だけにする（CHAT-1013-ACF-09 で扱う）

前提（チャット側。平野さんの決定ではない）

* 直す箇所と大きさは ACF-07 の案の (d) の1のとおり（要確認）:
   * books/: `generate_books_pages.py` の `author_image_url()` で `with_size(url, SIZE_200)` にする（96px 表示）
   * houou/ の表の X アイコン（`.mj-houou-avatar`、28px）: `_80x80` にする（既に持っている `thumb` を使う）
   * houou_race の丸（`.mj-race-chip`、28px）: `IMAGE_SIZE = SIZE_80` にする
   * houou/ の選手の写真（72〜88px、`_200x200`）・候補（32px、`_80x80`）、saikyo/・live/・title/・jpml_test・帰り道・jpml_pros は変えない
* 共通の関数は `scripts/lib/x_images.py` の `with_size()` をそのまま使う。`lib/sns.py` は変えない
* X の画像以外（note・YouTube など）の URL は変えない。`with_size()` が X 以外の URL や `abs.twimg.com` の既定アイコンをどう扱うかは、テストで確かめる
* 大きさによっては片方だけ 404 になる写真がある（ACF-07 の (d) の3）。`_200x200`・`_80x80` が今の値で返るかは、この指示では全件を確かめない（#380 と同じ。毎朝の検知に任せる）。ただし手順3の数人分の確かめは行う
* 使う skill は無い

手順

1. #558 の状態と最新のコメントを確かめ、他セッションの着手中コメントが無ければ着手中のコメントを残す。未マージの work/ ブランチで `generate_books_pages.py`・houou/ の生成スクリプト・`generate_houou_race.py`・`lib/x_images.py` に触れるものが無いかを確かめる（`git branch -r --no-merged origin/cloudflare`）
2. テストを先に足し、直す前のコードで通らないことを確かめる（books の著者写真が `_200x200`、houou/ の表のアイコンと houou_race の丸が `_80x80` になること。X 以外の URL と既定アイコンは変わらないこと）。そのうえで上の3か所を直し、`python3 -m unittest discover -s scripts/tests` を通す
3. 全ページを再生成し、生成物の差が books/・houou/・houou_race だけで、X の画像 URL の末尾の変化に限られることを確かめる（末尾ごとの件数の前後をログに書く）。変わった URL から数人分（books・houou・houou_race から各2人以上）を GET し 200 を確かめる。プレビュー（docs/notes/cloud-sessions.md の手順）で books/ の著者写真・houou/ の表・houou_race の丸が代替画像に落ちていないことを見る。docs/notes/sns-book.md（または X の画像の大きさを書いている文書）に「ページの大きさは各生成スクリプトの定数」を書き足し、上の「決定」を docs/decisions/saikyo.md の #558 の項に足して、#558 に結果をコメントし、マージする。ACF-07 のログの状態の末尾に `/ 続き: CHAT-1013-ACF-08` を足す

止まる条件

* CHAT-1013-ACF-07 の状態が「判断待ち」でない、#558 が Closed、他セッションの着手中コメントがある、または未マージの work/ ブランチが上のファイルに触れている
* 前提の箇所（`author_image_url()`・`thumb`・`IMAGE_SIZE`）が実物と違い、直す箇所を決められない
* 足したテストが直す前のコードで通ってしまう
* 再生成の差が books/・houou/・houou_race 以外に出る、または末尾以外の変化がある
* 数人分の確かめで 404 が出る（URL と人をログに書いて止まる）、またはプレビューで代替画像に落ちている
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（確認用URL にプレビューの URL を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7929efdc）: https://github.com/retroeater/mj-logs/tree/main/guide/7929efdc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
