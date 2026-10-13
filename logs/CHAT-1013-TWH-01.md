# CHAT-1013-TWH-01

- 着手日時: 2026-10-13
- 対象issue: #553
- ブランチ: work/1013-twh
- 着手時HEAD: 0186d17f7

## 指示

【Claude作成】Claude Code 向け指示：title/ の期ページの帰り道の節を「帰り道」にし、カードの下の選手名・公開日を消す
Chat-Ref: CHAT-1013-TWH-01
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-twh の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1013-twh を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-twh origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
title/ の期ページ（例: title/ranwa/6.html）の最後にある帰り道の節（#553、クローズ済み）の見出しを短くし、カードの下の文字をなくして決勝動画のカードと同じ形にする。

### 決定（2026-10-13、平野さん）
- title/ の期ページの帰り道の節の見出しを「帰り道ついていってイイっすか」から「帰り道」にする
- 帰り道のカードの下のキャプション（選手名と公開日。例「白鳥翔 2026年10月11日」）は出さない
- 確かめが通れば、プレビューを見ずにマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 見出しは `scripts/generate_title_pages.py` の `wayhome_html()` が `wayhome.SERIES_NAME` で出している（チャット側が cloudflare の版で読んだ。要確認）。`SERIES_NAME` は帰り道の各話・一覧でも使う共有の定数なので変えず、title/ 側だけ「帰り道」にする（定数を足すなら title/ の側に）
- キャプションは `wayhome_card_html()` の `.mj-title-video-summary`（中に `.mj-title-wayhome-name`）。消した後のカードは、サムネイルと左上の回の番号（#1・#2）・右下の再生時間だけになる（決勝動画のカードと同じく下に文字なし）。使われなくなる `style.css` の `.mj-title-wayhome-name` は消す。`.mj-title-video-summary` は決勝ライブのカードが使うので残す
- 帰り道の呼称の決定（docs/decisions/wayhome.md の G5-09「今の呼称をそれぞれ正式なものとみなす」）は帰り道の一覧・各話のもので、この変更とは食い違わない（読んで、食い違うと判断したら止まる）
- `h2` の id（`title_wayhome`）・節の位置・カードのリンク先は変えない
- 文書: docs/notes/title-pages.md「期ページの帰り道（#553 …）」の節名とカードの説明、パンくずの行の「帰り道」の記述を今の実装に合わせる。docs/notes/video-wayhome.md ほかに節名「帰り道ついていってイイっすか」を title/ の期ページのものとして書いた所があれば同じく直す。決定は docs/decisions/title.md に足す

## 手順
1. 確かめる: 上の「前提」のコード（関数・定数・CSS）が今の origin/cloudflare にあることを確かめ、食い違えば止まる。`git branch -r --no-merged origin/cloudflare` の各ブランチで、`scripts/generate_title_pages.py` の `wayhome_html()`・`wayhome_card_html()`、`style.css` の `.mj-title-wayhome-name` の同じ行を変えているもの、または取り込みで衝突するものが無いかを確かめ、結果をログに書く。`git grep -n "帰り道ついていってイイっすか"` の結果を、title/ の期ページに関わるもの・それ以外に分けてログに書く。#553 に着手中のコメントを残す（本文・状態・ラベルは変えない。再オープンしない）
2. 直す: 上の「前提」のとおりコード・CSS・文書・決定を直す。テストがあれば合わせて直し、全部を流す
3. 生成して確かめ、マージする: 同じシートの読み込みで差を見るため、origin/cloudflare のスクリプトと直したスクリプトで title/ を続けて生成し、両者の差が (a) 期ページの帰り道の節の `h2` の文字 (b) その節のカードの `.mj-title-video-summary` の削除 の2種類だけであることを確かめる（種類ごとの件数と、帰り道の節を持つ期ページの一覧をログに書く。帰り道の節を持たないページは差が0のこと）。直したスクリプトの生成物と origin/cloudflare の生成物の差のうち、シートの変化によるものがあれば種類と件数をログに書く。CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、push を契機の check-run を待つ（上限15分）。本番（`curl`、`?v=<未使用の値>`）で `/title/ranwa/6.html` の見出しが「帰り道」で、カードの下に選手名・日付が無いこと、`/wayhome/` と各話（例 `/wayhome/Yjp_PKHvldw.html`）の見出しが変わっていないことを確かめる。#553 にこの直しをコメントする。作業ブランチを片付ける

## 止まる条件
- 前提のコードが見つからない、または決定と前提が食い違う
- 手順3の生成の差に (a)(b) 以外の種類がある、または帰り道の節を持たないページに差がある
- 帰り道の一覧・各話の生成物に差がある
- 未マージの work/ ブランチが上の関数・CSS の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-TWH-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-TWH-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0186d17f）: https://github.com/retroeater/mj-logs/tree/main/guide/0186d17f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0186d17f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
