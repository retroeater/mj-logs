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

### 手順1: 確かめ

- 前提の今の作りは実物どおり: `lib/wayhome.py` の `index_player_links()` が「プロ」シートの 名前・X ID・note ID（`PRO_COLUMNS`、見出しで読む）を引き、`build_player_links_html()` が `<span class="mj-video-player-links">` に X・note のリンク（`X_ICON_SVG`・`NOTE_ICON_SVG`、44px の丸）を並べる。
  使うのは一覧のヒーロー（`generate_video_wayhome.py`、h2 の右）と各話のヒーロー（`generate_wayhome_episodes.py`、h1 の右）だけ。カードには X・note は無い。`X_ICON_SVG`・`NOTE_ICON_SVG` を使うのは帰り道だけ（jpml_pros は `img/note.svg` を使い別の作り）
- 件数: `generate_video_wayhome.py` の `build_filterbar_html(count)` が `<p id="result_count" class="mj-filterbar-count" role="status" aria-live="polite">N件中 N件を表示</p>` を焼き込み、`video_wayhome.js` の `render()` が絞り込みのたびに「N件中 M件を表示」か「該当する動画がありません」を書く
- 未マージのブランチで対象のファイルに触れるのは `work/1008-hou` の `style.css`（末尾 3446行以降への追記だけ）。こちらは 913〜960行・1350行付近で重ならない
- 同じ目的の issue は無い（「帰り道 X note アイコン 件数」「帰り道 選手 X リンク」で検索。#195・#238 が出たが目的は選手個別ページへのリンク）。起票せず #195 にコメントする

### 手順2: 直し

- 件数: `build_filterbar_html()` は空の `#result_count`（`role="status"`・`aria-live="polite"` は残す）を出す。`video_wayhome.js` は0件のときだけ「該当する動画がありません」、それ以外は空にする（`totalCount` を消した）。
  `style.css` に `body:has(.mj-video-list) .mj-filterbar-count:empty { display: none; }`（`saikyo/`・`title/`・`live/` と同じ形）を足し、空のときは固定バーの場所をとらない
- X のアイコン: `PRO_COLUMNS` から note ID を外し（`PlayerLinks` は `x_id` だけ）、`NOTE_ICON_SVG` を消した。`build_player_links_html()` は X ID があれば `<a class="mj-video-player-link">` 1つ（読み上げ用の「<名前>さんのX」と新しいタブの注記は今までどおり、`target="_blank" rel="noopener"`）、無ければ空。囲みの span はやめた
- `style.css`: `.mj-video-hero-title` に見出しと同じ `font-size: clamp(1.75rem, 6vw, 3rem)`・`line-height: 1.15` を付け、アイコンの svg を `1em` にした。リンクは `inline-block`・`padding: 8px`・`margin: -8px`（タップ領域を広げ、行の高さは変えない）・`vertical-align: -0.12em`。`.mj-video-player-links` の規則は消した
- テスト: `test_wayhome_player_links.py` を直した（note ID を読まない、X のアイコン1つ・名前の読み上げ、X ID が無い・`None` なら空）。修正前のコードでは失敗1・エラー3、修正後は全体 749件 OK
- 文書: `docs/notes/video-wayhome.md` に節「件数の表示を消し、ヒーローの X・note を X のアイコン1つにした」を足し、「選手のX・noteアイコン」節の頭に当時の記録である旨を書いた。`docs/notes/design.md` に帰り道のこの部品の行は無い（直していない）。決定を `docs/decisions/wayhome.md` に足した
- #195 にコメントした（リンク先の差し替えは `build_player_links_html()` の1か所）

### 手順3: 生成とプレビュー

- `python3 scripts/regenerate.py video_wayhome wayhome_episodes`: 40件・`6WAPjcxT78A` を外した警告・各話 39ページ。警告「jpml_prosに該当する選手がいないため、Xアイコンを出しません: タマシュ・エルドス」
- 差分: `video_wayhome.html` と各話 38ページ（`o28svvuVI0M`〈タマシュ・エルドス、「プロ」シートにいない〉は変わらず）。旧版に「X・note の囲み span を X のリンク1つに置き換え」「件数の文言を空に」を当てると、39ファイルとも新版と完全に一致（スクリプトで比較）。
  note のリンクが消えたのは 15ファイル（一覧を含む）、X だけだった 24ファイルは囲みの span が外れただけ。sitemap・OGP は変わらず。帰り道以外の生成物は変えていない（`style.css` の規則は帰り道の部品だけ）
- push（f70223b7）→「Workers Builds: mj」success。プレビューを Playwright（Chromium）で、390×844 と 1280×800 で見た:

| 幅 | ページ | 名前の字の大きさ | アイコン（svg）の高さ | 名前の最終行とアイコンの上端・下端 | タップ領域 |
|---|---|---|---|---|---|
| 390 | 一覧（紺野真太郎、1行） | 28px | 28px | 行 420〜451 / アイコン 420〜448 | 44×44 |
| 390 | `OoK3O2BCm8M`（第4期小島武夫杯帝王戦 三浦智博、2行に折り返し） | 28px | 28px | 行 442〜473 / アイコン 442〜470。最終行「浦智博」の直後 | 44×44 |
| 390 | `76OsWTSSnso`（第4回リーチ麻雀世界選手権 内川幸太郎、2行） | 28px | 28px | 行 442〜473 / アイコン 442〜470 | 44×44 |
| 390 | `o28svvuVI0M`（X ID なし） | 28px | — | 名前だけ | — |
| 1280 | 一覧 | 48px | 48px | 行 383〜436 / アイコン 384〜432 | 64×64 |
| 1280 | `OoK3O2BCm8M` | 48px | 48px | 行 407〜460 / アイコン 407〜455 | 64×64 |
| 1280 | `76OsWTSSnso` | 48px | 48px | 行 384〜437 / アイコン 385〜433 | 64×64 |
| 1280 | `o28svvuVI0M` | 48px | — | 名前だけ | — |

  - スクリーンショットで、アイコンが名前の最終行の右に名前と同じ高さで並び、折り返しても名前の下に回り込まないことを見た
  - 一覧の固定バー: 件数の要素は空で `display: none`（検索欄だけ）。「zzzzzz」で0件にすると「該当する動画がありません」が出る（`display: block`、検索欄の下）。「三浦」では4件に絞られ、文言は空
  - タップ領域は、アイコンの左 4px が名前の最後の字に重なる（名前と 4px の間隔に対し、タップ領域を 8px 広げているため）

## 報告

- 状態: 判断待ち / 続き: CHAT-1010-WHS-07
- ブランチ: work/1010-whs（未マージ）
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: プレビューあり（URL は最終報告）。見るページは一覧・X ID がある回 `OoK3O2BCm8M`（第4期小島武夫杯帝王戦 三浦智博）・X ID が無い回 `o28svvuVI0M`（ワールド・リーチ・プロ タマシュ・エルドス）
- マージ: 未（平野さんがプレビューで見た目を確かめてから、別の指示で）
- issue: #195（コメント）
- 判断が必要なこと:
  - プレビューの見た目でよいか（件数の表示なし・0件の知らせ、名前の右の X のアイコン1つ）。よければマージの指示を
  - アイコンのタップ領域が名前の最後の字に 4px 重なる（名前と 4px の間隔のまま、タップ領域を上下左右 8px 広げたため）。間隔を広げる（8px）か、このままか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a930b4a9）: https://github.com/retroeater/mj-logs/tree/main/guide/a930b4a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a930b4a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
