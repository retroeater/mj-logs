# CHAT-1010-WHS-06

- 着手日時: 2026-10-10
- 対象issue: #195
- ブランチ: work/1010-whs
- 着手時HEAD: 18147a46

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」の一覧と各話の見た目の直し（件数の表示を消す、ヒーローの X・note のアイコンを X のアイコン1つにする）。プレビューで止まる Chat-Ref: CHAT-1010-WHS-06 マージ: 判断待ちで止まる（平野さんがプレビューで見た目を確かめてから、別の指示でマージ） 貼る時機: CHAT-1010-WHS-04 の完了（マージ済み）の後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1010-WHS-04.md の `## 報告` を読み、WHS-04 のマージが済んでいなければ何もせず止まる。

目的
平野さんの指定どおり、帰り道の一覧（`video_wayhome.html`）と各話（`wayhome/*.html`）の見た目を直す。CHAT-1010-WHS-05 は送る前に差し替えたため欠番。
決定（2026-10-10、平野さん）

* 一覧: 「39件中 39件を表示」の件数の表示を、絞り込みの最中も含めて消す。0件のときの知らせは残す
* 一覧・各話のヒーロー: 選手の「X」「note」のリンクをやめ、X のアイコン1つにする。置き場所は今の X のアイコンと同じ「名前の右」（各話は名前の左にタイトル戦名が入るため）。アイコンの高さは名前の文字の高さに合わせる。リンクはアイコンだけに付け、その選手の X のプロフィールに飛ぶ（名前はリンクにしない。ヒーローの文字は大きいので押しにくくない）
* X ID が無い選手は、アイコンを出さず名前だけ
* note は帰り道では使わない
* このリンク先は、やがて選手個人ページ（#219）に変える（#195 の「X・note のリンク先を選手個別ページへ切り替える検討」の続き）

前提（チャット側。平野さんの決定ではない）

* 今の作り（cloudflare の WHS-03 の版で読んだ。要確認）: `scripts/lib/wayhome.py` の `index_player_links()` が「プロ」シートから X ID・note ID を見出しで読み（#536）、`build_player_links_html()` が選手名の右に X・note のアイコン（`X_ICON_SVG`・`NOTE_ICON_SVG`）を並べる（WH-53。一覧のヒーローは #317 で同じ部品を使う）。件数は `generate_video_wayhome.py` が「N件中 N件を表示」を焼き込み、`video_wayhome.js` が絞り込みのたびに書き換える
* 作り方の案（実物に合わせて変えてよい）:
   * 件数の要素が `role="status"` などで読み上げにも使われていれば、0件の知らせの読み上げが残るようにする
   * X のリンクのアクセシブルネーム（「<名前>さんのX」など）と、新しいタブで開くかなどの動きは今の X のリンクと同じにする
   * アイコンは今の `X_ICON_SVG` を使い、高さは `1em`（名前の文字に合わせる）。外部のファイル・ドメインは増やさない
   * note のアイコン（`NOTE_ICON_SVG`）を帰り道で使わなくなる。ほかのページ（jpml_pros など）が使っていれば残す。「プロ」シートの note ID の読み込みは、ほかで使わなければ外してよい
* 一覧の「決勝戦を見る」・再生ボタン・各話の前後の回などは変えない

手順

1. 確かめる: WHS-04 がマージ済みで、`origin/cloudflare` から作業ブランチを作れること。上の「前提」の今の作りを実物で確かめる（食い違えば止まる）。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が `scripts/lib/wayhome.py`・`generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`video_wayhome.js`・`style.css` の帰り道の節の同じ行・同じ関数を変えていないか、取り込みで衝突しないかを確かめる。同じ目的の issue があるか検索する（無ければ起票しなくてよい。#195 にコメントする）
2. 直す: 件数の表示を消し、ヒーローの X・note のアイコンを、名前の右の X のアイコン1つ（文字の高さ）にする（一覧のヒーローと各話のヒーロー。ほかに X・note を出している所があれば同じにする）。テストを直す・足す。`docs/notes/video-wayhome.md` と `docs/notes/design.md`（帰り道の部品の行があれば）を今の内容を読んでから直す。決定を `docs/decisions/` の WHS-02〜04 と同じ分野に足す。#195 に、帰り道の X・note のリンクを X のアイコン1つにしたことと、選手個人ページへの切り替えはこのリンクを差し替えればよいことをコメントする
3. 生成して止まる: 帰り道の2ページだけを生成し、差分を種類ごとに数えてログに書く（変わるのは件数の要素・X/note の部品・note のアイコンの読み込み、のはず）。push して Cloudflare のプレビューを出す。平野さんが見るページは一覧・X ID がある選手の回1つ・X ID が無い選手の回1つ（あれば）。スマホ幅（390px）とパソコン幅で、アイコンの高さが名前の文字とそろい、折り返しで崩れないことをスクリーンショットで確かめ、ログに書く

止まる条件

* WHS-04 がマージ済みでない
* 「前提」の今の作りと実物が大きく食い違う（例: X・note のリンクがヒーロー以外にもあって直し方が決まらない）
* 未マージのブランチが同じ行・同じ関数を変えている、または取り込みで衝突する
* ほかのページ（jpml_pros・title/ など）の生成物が変わる
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-06"` は0件（WHS-05 は欠番と指示文にある）
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている
- WHS-04 の `## 報告`: 状態 完了、マージ 済（40eea4b1）
- 作業ブランチ: ローカルの `work/1010-whs`（dbea2935）は `origin/cloudflare` の祖先、リモートもマージ済み → `git merge --ff-only origin/cloudflare`（18147a46）

## 報告

- 状態: 作業中
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 未
- issue: #195
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 18147a46）: https://github.com/retroeater/mj-logs/tree/main/guide/18147a46

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/18147a46/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
