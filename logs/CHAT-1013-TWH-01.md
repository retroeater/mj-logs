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

## 経過

### 着手前の確認

- 同じ Chat-Ref・識別子 `TWH` のコミット・ログ: なし（`git fetch --unshallow` の後、`git log --all -E --grep 'CHAT-[0-9]{4}-TWH-'` と `docs/logs/CHAT-*-TWH-*.md` の履歴とも0件）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 作業ブランチ: `work/1013-twh` はローカル・リモートとも無し → `git checkout -b work/1013-twh origin/cloudflare`（0186d17f7）
- #553: 他セッションの着手中コメントなし（WHS-16 の着手中は WHS-18・19 で完了済み）。着手中のコメントを残した（本文・状態・ラベルは変えていない）
- CLAUDE.md 22444 バイト・handover.md 23794 バイト（触らない）

### 1. 確かめ

- 前提のコードは origin/cloudflare にあった: `scripts/generate_title_pages.py` の `wayhome_html()` が `<h2 id="title_wayhome">{esc(wayhome.SERIES_NAME)}</h2>`、`wayhome_card_html()` が `.mj-title-video-summary`（中に `.mj-title-wayhome-name`＋公開日）。`style.css` に `.mj-title-wayhome-name`（太字）と `.mj-title-video-summary`（決勝ライブも使う）
- docs/decisions/wayhome.md の G5-09（nav「帰り道」、一覧の title・h1「帰り道ついていってイイっすか」を正式とみなす）は一覧・各話の呼称の決定で、title/ の期ページの節の見出しを短くすることとは食い違わないと判断した
- 未マージの work/ ブランチ: 触るファイル（`scripts/generate_title_pages.py`・`style.css`・`docs/notes/title-pages.md`・`docs/notes/video-wayhome.md`・`docs/decisions/title.md`・`scripts/tests/test_wayhome_links.py`）を変えているのは `origin/work/1013-dsn` の `style.css` だけ（houou/ の 5700 行台。`.mj-title-wayhome-name` は 2989 行付近）で、同じ行ではない。`git merge-tree` で cloudflare と衝突なし。止まる条件に当たらない
- `git grep -n "帰り道ついていってイイっすか"`（docs/logs を除く）
  - title/ の期ページに関わるもの: `scripts/generate_title_pages.py`（`wayhome_html()` の docstring）、`scripts/tests/test_wayhome_links.py:144`（期ページの h2 の検査）、`docs/notes/title-pages.md:67`、`docs/notes/video-wayhome.md:50`、生成物 `title/` の37ファイル（各1件）
  - それ以外（変えない）: `scripts/lib/wayhome.py`（`SERIES_NAME` と docstring）、`scripts/generate_video_wayhome.py`・`scripts/apply_page_meta.py`（一覧の title・説明）、`scripts/check_wayhome.py`（知らせの issue）、`scripts/tests/test_wayhome_intake.py`（11件）・`test_wayhome_links.py:67,70`（紹介文）、`docs/decisions/wayhome.md`（2件）、`docs/notes/ogp.md:54`、`docs/notes/video-wayhome.md:41`（紹介文）、`llms.txt`（2件）、`wayhome/`（一覧9件・各話各8件）、`data/live_channel_raw.jsonl`（38件、動画タイトル）

### 2. 直し（fb98b95e6）

- `generate_title_pages.py`: 定数 `WAYHOME_HEADING = "帰り道"` を title/ 側に足し、`wayhome_html()` の見出しに使う（`SERIES_NAME` は変えない）。`wayhome_card_html()` から `.mj-title-video-summary` を外した。`date_label()` は決勝ライブ等で使うので残る。h2 の id・節の位置・リンク先は変えていない
- `style.css`: `.mj-title-wayhome-name` を消した（参照が残らないことを `git grep` で確認）。`.mj-title-video-summary` は残した
- テスト: `test_wayhome_links.py` の期ページの h2 の期待を「帰り道」にし、`.mj-title-video-summary` が無いことを足した（修正前のコードは h2 が「帰り道ついていってイイっすか」なので通らない）。`python3 -m unittest discover -s scripts/tests` 995件 OK
- 文書: `docs/notes/title-pages.md`「期ページの帰り道」の節名・定数・カードの説明を直した。パンくずの行の「… → 帰り道（その期の回があるときだけ、#553）」は今の実装と合っているので変えていない。`docs/notes/video-wayhome.md` の title/ の節名を直した。決定は `docs/decisions/title.md` に足した（このログと同じコミット）

### 3. 生成と差の確かめ

- origin/cloudflare の `generate_title_pages.py` を一時ファイルにして実行（警告0件、帰り道 37期/41本）→ title/ と `sitemap-title.xml` を scratchpad に退避 → 直したスクリプトで実行（警告0件、37期/41本）
- 旧スクリプトの生成物は origin/cloudflare のコミット済みの生成物と差なし（シートの変化による差は0件）
- 旧→新の差: 37ファイル。各ファイルで、旧の帰り道の節に (a) h2 の文字の置き換え (b) `.mj-title-video-summary` の削除 を当てたものが新と完全に一致（ずれ0件）。(a) 37件、(b) 41件。帰り道の節を持たないページの差は0、`sitemap-title.xml` も差なし、増減したファイルなし
- 帰り道の節を持つ期ページ（37）: gpmax/15・16、houou/41、joryu-nihon-series/2025、judan/40・41・42、jwrc/15・16・17・18、jwrc-r/3・4・5・6、masters/32・33、nihon-series/2025、oui/49・50、ouka/19・20、ourai/8・9・11、ranwa/4・5・6、shinjinou/37・38、teiou/3・4、wakajishi/8・9・10・11、wrc/4
- 帰り道の一覧・各話: `python3 scripts/regenerate.py video_wayhome wayhome_episodes` で差なし
- 気づき（変えていない）: カードに見える文字が無くなったため、リンクの読み上げ名は回の番号と再生時間だけになる（番号の無い回は再生時間だけ）。決勝動画のカードも回戦名だけなので同じ形だが、必要なら別の指示で `visually-hidden` の選手名を足す

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7557aba1）: https://github.com/retroeater/mj-logs/tree/main/guide/7557aba1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
