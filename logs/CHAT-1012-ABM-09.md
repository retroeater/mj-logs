# CHAT-1012-ABM-09

- 着手日時: 2026-10-12
- 対象issue: #425
- ブランチ: work/1012-abm
- 着手時HEAD: 4e18a785b01cc398bdc19c4d12192737b4ceb6ed

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓のページを放送対局（/live）の個別ページのような作りに試作する（ABEMA のサムネイルのヒーロー・関連動画） Chat-Ref: CHAT-1012-ABM-09 マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめる（CHAT-1011-ABM-03 の着手中コメントは同じ系列のもの）。

目的
未公開の最強戦の卓のページ（`saikyo/<年度>/<卓のアンカー>.html`）を、放送対局の個別ページ（`live/`）のような作りに試作し、プレビューで平野さんに見てもらう。
決定（平野さん）

* （2026-10-11）卓のページを放送対局の個別ページのようなレイアウトにする。作り直すのは卓のページだけで、対局のページ・年度ページは今のまま
* （2026-10-11）ABEMA のサムネイルを画面の上半分（ヒーロー）に使う。ヒーローの「▶ 再生」とサムネイルを押すと ABEMA のその回を新しいタブで開く。今の「ABEMA で見る」と出典「画像: ABEMA」はヒーローの中にまとめる（出典は隅に小さく）
* （2026-10-11）放送対局の「ライブ」の節の代わりに「関連動画」として、同じ対局のほかの卓を下に並べる（例: ベスト8 A卓のページなら「ベスト8 B卓」「決勝卓」）。関連動画のカードを押すと ryoei.pro のその卓のページへ行く（ABEMA へは各卓のページのヒーローから行く）
* 決定の正は docs/decisions/saikyo.md（CHAT-1011-ABM-02・04・07・08 の節）。個別ページに共有ボタンを付けない（ABM-02 の grill 4）は変えない

前提（チャット側。平野さんの決定ではない）

* ABEMA の URL（M 列）が無い卓（未対局の卓と、URL をまだ入れていない卓）のヒーロー・関連動画のカードは、試作ではその年度の今の OGP 画像（`img/ogp/saikyo/<年度>-black.png`）を同じ大きさで出し、「▶ 再生」は出さない。どうするかは、全年度の ABEMA の突き合わせの結果を見て平野さんが決める（未決）
* 放送対局の個別ページの作り（ヒーロー・パンくず・写真カード・「〇〇の他の対局」の横スクロールのカード）は docs/notes/live-page-design.md「2-5」「2-6」と `generate_live_pages.py`（実名は確かめる）・`style.css` の今の部品を読み、使い回せる部品は使い回す（新しく作るより、見た目と a11y の扱いをそろえるため）。どれを使い回し、どれを新しく足したかをログに書く
* 卓のページの中身の並び（案。実物に合わせて変えてよく、変えたらログに書く）: ヒーロー（サムネイル・題名「麻雀最強戦<年度> <対局名> <ステージ・卓>」・対局日・パンくず・「▶ 再生」・出典）→ 出場者の4人（今の写真カード・順位・通過/敗退）→ 関連動画（同じ対局のほかの卓のカード。カードはサムネイルと卓の名前）。固定バー（年度プルダウン）は今の卓のページと同じ
* 試作でも未公開のまま（noindex、サイトマップ・メニュー・今の公開ページからのリンクなし）

手順

1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`generate_live_pages.py` の同じ関数・同じ行を変えていないか。live/ の個別ページの部品（関数名・クラス名）を洗い出してログに書く
2. 作る: 上の決定と前提のとおり、卓のページの生成を作り直す（対局のページ・年度ページは変えない）。テストを直し・足し、`python3 -m unittest discover -s scripts/tests` を通す。live/ の部品を共有の部品にするために `generate_live_pages.py` 側を変えるときは、live/ の生成物に差分が出ないこと（全ページを再生成して確かめる）を条件にする
3. 確かめてプレビューに出す: 生成して、(a) 変わったのが卓のページ（見込み 643。CHAT-1011-ABM-01 のログ「手順2」の表の計。シートの更新で 2026年度が増えていれば +10 まで許す）と CSS・JS だけで、今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・対局のページ・年度ページ・live/ に差分が無いこと、(b) 内部リンクがすべて実在のファイルを指すこと、(c) id の重複 0・noindex と canonical が全ページ、(d) headless Chromium でスマホ幅（390）と PC 幅（1280）と 1920×1080 の見え方（ヒーローが画面の上半分を占めるか、はみ出し・崩れの有無。外部の画像が読めない環境では枠の大きさ）を確かめてログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2026 超頭脳合戦 ベスト8 A卓（`saikyo/2026/20260920-s1-a.html`）・同じ対局の決勝（`20260920-s2.html`）・URL の無い卓の例（2025 の `20251214-2-s1-a.html`）・未対局の卓の例（2026 の未対局の卓1つ）の URL を並べる

止まる条件

* 決定（docs/decisions/saikyo.md）と食い違う作りが要る（作らずに報告する）
* 変わるファイルが見込みと違う（卓のページが 643 より少ない、または +10 を超えて多い。対局のページ・年度ページ・今の公開中のファイル・live/ に差分が出る。シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが上の4ファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-10-12）

- 同じ Chat-Ref のコミットは0件。`work/1012-abm` はローカル・リモートとも無かったので `git checkout -b work/1012-abm origin/cloudflare`（4e18a785）
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行は4つともある（1行目にまとめて書かれている）。#425 は Open で、着手中コメントは CHAT-1011-ABM-03（同じ系列・このセッション）のものだけ

### 手順1: 確かめ

- 未マージのブランチ（自分を除く3件: origin/work/1009-swp-526・origin/work/1011-swp-hnd・origin/work/1011-swp-nav）は `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`generate_live_pages.py` のどれも変えていない。`git merge-tree` で衝突なし
- /live の個別ページの部品（`scripts/generate_live_pages.py`・`style.css`。設計は docs/notes/live-page-design.md「2-5」「2-6」）:
  - 本文: `build_individual()` が `<main class="mj-video-page mj-live" id="main">` を自分で持つ（`render_content(wrap_main=False)`）。`.mj-video-page` で帰り道と同じ濃色（`body:has(.mj-video-page)` の色・navbar の sticky）
  - ヒーロー: `build_hero()` → `section.mj-video-hero` > `img.mj-video-hero-bg`・`.mj-video-hero-scrim`・`a.mj-video-back`・`.mj-video-hero-content`（パンくず・`h1.mj-video-hero-player`・`p.mj-video-hero-meta`・`.mj-video-hero-actions`）
  - 「▶ 再生」: `play_button()` → `a.mj-video-btn.mj-video-btn-primary`
  - パンくず: `build_breadcrumb()` → `ol.mj-live-breadcrumb`
  - 固定バー: `build_filterbar()` → `form.mj-filterbar`（`select.mj-live-title-select`・検索欄・共有ボタン）。sticky は `body:has(.mj-video-list, .mj-live) .mj-filterbar`、高さは `assets/live.js` の `watchHeight()`（`--mj-nav-h`・`--mj-filter-h`）
  - 写真カード: `build_player_card()` → `li.mj-saikyo-player` > `.mj-saikyo-card`（最強戦の写真カード）を `ul.mj-saikyo-players.mj-live-players` に並べる
  - 節: `section.mj-video-episodes` > `h2.mj-video-episodes-heading.mj-live-heading`
  - 「〇〇の他の対局」: `div.mj-live-other-head` > `h2.mj-live-subheading`＋`a.mj-live-more`、`ul.mj-video-track` > `build_page_card()` の `li.mj-video-card` > `a.mj-video-card-link`（`.mj-video-card-thumb` > `img.mj-video-card-img`・`.mj-video-card-date`・`.mj-video-card-title`）

### 手順2: 作ったもの

- `scripts/generate_saikyo_pages.py`（卓のページだけ。対局のページ・年度ページの関数は変えていない）:
  - `build_table_page_body()` を作り直し、`<main class="mj-video-page mj-live mj-saikyo-sub mj-saikyo-table-page">` を自分で持つ形にした。`write_sub_page(..., table_page=True)` で `render_content(wrap_main=False)`、`<body>` に `mj-saikyo-page` を付けない（今の固定の navbar・上余白の CSS と /live の sticky がぶつかるため）
  - `build_table_hero()`（新規。/live の `build_hero()` と同じ HTML の形）: 画像は ABEMA の 1280×720（`table_image()`）、`data-fallback` に年度の OGP 画像。ABEMA の回の URL があるときだけ、画像の上に透明なリンク `a.mj-saikyo-hero-link`（`tabindex="-1" aria-hidden="true"`）・「▶ 再生」・隅の出典 `span.mj-saikyo-hero-credit`「画像: ABEMA」。回の URL の形でない値はボタン「ABEMA で見る」だけ
  - `build_live_breadcrumb()`（新規。/live の `build_breadcrumb()` と同じ形の `ol.mj-live-breadcrumb`。/live の関数は相対パスの計算が /live のパスに結び付いているので使い回さず、同じ HTML を出す小さな関数にした）
  - `build_related_card()`（新規。/live の `build_page_card()` と同じ `li.mj-video-card`）: サムネイル（ABEMA の 640×360、無ければ年度の OGP 画像）・対局日・卓の見出し。押すとその卓のページ
  - 関連動画の節: `div.mj-live-other-head`（見出し「関連動画」＋右に `a.mj-live-more`「<対局名>のすべての卓」→ 対局のページ）＋ `ul.mj-video-track`。ほかの卓が無い対局（卓が1つ）では見出しとリンクだけ
  - 固定バー: 今と同じ年度プルダウン（`build_year_select()` に `select_class` の引数を足し、/live の見た目 `mj-live-title-select` で出す）を `div.mj-filterbar` に入れた。**位置は /live と同じくヒーローの下の sticky**（前提の「固定バーは今の卓のページと同じ」から変えた。/live の作りにそろえるため。中身は今と同じ年度プルダウンだけ）
  - 中身の並びは前提の案のとおり（ヒーロー → 出場者の4人 → 関連動画）。見出しの「出場者」は /live の「対局者」に当たる節
  - 定数: `TABLE_PAGE_CLASS`・`RELATED_HEADING`・`PLAY_LABEL`・`OGP_WIDTH, OGP_HEIGHT = 1200, 630`
- 使い回した部品（CSS は変えずにそのまま）: `.mj-video-page`（濃色）・`.mj-video-hero` 一式・`.mj-video-btn(-primary)`・`.mj-live-breadcrumb`・`body:has(.mj-live) .mj-filterbar`（sticky）・`.mj-live-title-select`・`.mj-video-episodes`・`.mj-live-heading`・`.mj-live-players`（名前のグラデーション）・`.mj-saikyo-card` 一式（順位・帯）・`.mj-live-other-head`・`.mj-live-subheading`・`.mj-live-more`・`.mj-video-track`・`.mj-video-card` 一式。`generate_live_pages.py` は変えていない（live/ の生成物は変わらない）
- 新しく足した CSS（`style.css`）: `.mj-saikyo-hero`（`isolation`）・`.mj-saikyo-hero-link`・ヒーローの文字の部分の `pointer-events`・`.mj-saikyo-hero-credit`・`.mj-saikyo-table-page .mj-live-players` の `--mj-saikyo-rank-size`。順位が空の行の三角の2つの選択子を `:is(.mj-saikyo-year, .mj-saikyo-table-page)` に広げた
- `assets/saikyo.js`: 新しいページの処理で、卓のページ（`.mj-saikyo-table-page`）は navbar・固定バーの高さを /live と同じ `--mj-nav-h`・`--mj-filter-h` に入れる（`watchHeight()` を足した。`assets/live.js` の同名の関数と同じ）。ほかのページは今までどおり
- テスト（`scripts/tests/test_saikyo_pages.py`）: 卓のページのテストを新しい作りに直し（ヒーロー・「▶ 再生」と画像の2か所が ABEMA・出典・関連動画のカードと対局のページへのリンク・共有ボタンなし）、URL の無い卓（OGP 画像・「▶」と出典なし）を足し、ABEMA のサムネイルのテストは対局のページだけにした。`python3 -m unittest discover -s scripts/tests` は 835件 OK
- docs/notes/saikyo-page-design.md「新しい年度・対局・卓のページ」の「卓のページ」「ABEMA のサムネイル」の項を新しい作りに直した

### 手順3: 確かめ

- 生成（`generate_saikyo_pages.py`）: 「動画URLのある卓: 18」「新しいページ(#425、未公開): 878件」、警告なし
- (a) 変わったファイル: 卓のページ **643**（`saikyo/<年度>/` の `-s<n>[-<卓>].html` の全部。見込み 643 との差 0）と `assets/saikyo.js`・`style.css`（とコード・テスト・文書）。今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・対局のページ・年度ページ・live/ の差分は 0。シートの変化による差分も 0
- (b) 内部リンク（href・src・プルダウンの value、28,296件）はすべて実在。`data-fallback` の行き先（`img/avatar.svg`・`img/ogp/saikyo/2026-black.png`）も実在
- (c) id の重複 0、noindex と canonical（自分自身）は 878ページすべて。卓のページに `mj-saikyo-page` のクラスは付かない
- (d) headless Chromium（ローカルの `http.server`。外部の ABEMA の画像は届かないので、`data-fallback` で年度の OGP 画像に替わった状態）: 卓 `2026/20260920-s1-a`・決勝 `20260920-s2`・URL の無い卓 `2025/20251214-2-s1-a`・未対局の卓 `2026/20261011-s1-a`
  - ヒーローの高さ: 390×844 で 608px（画面の72%）、1280×900 で 640px（71%）、1920×1080 で 640px（59%）。navbar の直下から始まり、固定バーはその下（390 で 664px、1280・1920 で 720px）。どれも画面の上半分以上を占める
  - 横のはみ出しは無い（`scrollWidth - clientWidth` は -15〜0。`scrollbar-gutter: stable` の分）。JS のエラー 0。navbar・固定バーは sticky で、`--mj-nav-h` は 390 で 56px、1280・1920 で 80px が入る
  - 見え方: ヒーローの左下にパンくず・見出し（「麻雀最強戦2026 超頭脳合戦 ベスト8 A卓」）・対局日・「▶ 再生」、右下の隅に「画像: ABEMA」。その下に年度プルダウンの固定バー、「出場者」の4人（スマホは2×2、PC は1行）、「関連動画」の見出しと「超頭脳合戦のすべての卓」、カード2枚（ベスト8 B卓・決勝）。URL の無い卓・未対局の卓は「▶ 再生」と出典が無い。崩れは見つからなかった
  - 気づいたこと: ABEMA の画像が読めないときも `data-fallback` で OGP 画像に替わり、出典「画像: ABEMA」は残る（HTML は生成時に決まるため。ABEMA の画像が読める環境では起きない）
- プレビュー（8f063365）: check-run「Workers Builds: mj」「check」とも success。上の4ページが 200・ヒーローあり・noindex あり（URL はターミナルの最終報告にだけ書く）

### docs/decisions

この指示の「決定」（2026-10-11 の3つ）を docs/decisions/saikyo.md に足した

## 報告

- 状態: 判断待ち / 続き: CHAT-1012-ABM-11
- ブランチ: work/1012-abm
- ログ: https://github.com/retroeater/mj/blob/work/1012-abm/docs/logs/CHAT-1012-ABM-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-abm
- 確認用URL: プレビューあり（URL は最終報告）。2026 超頭脳合戦 ベスト8 A卓・同じ対局の決勝・URL の無い卓（2025 ファイナル 2nd Stage ベスト8 A卓）・未対局の卓（2026 団体分裂合戦 ベスト8 A卓）
- マージ: 未（平野さんの判断待ち）
- issue: #425（読むだけ）
- 判断が必要なこと:
  - プレビューを見て、卓のページの新しい作り（/live と同じ濃色のヒーロー・出場者・関連動画）でよいか。マージするか
  - 固定バー（年度プルダウン）の位置を /live と同じくヒーローの下の sticky にした（前提の「今の卓のページと同じ」から変えた）。これでよいか
  - ABEMA の URL が無い卓（未対局・未入力）のヒーローとカードは、試作では年度の OGP 画像（「▶ 再生」・出典なし）。どうするかは全年度の ABEMA の突き合わせの後に決める（前提のとおり未決）
- 未確認の項目:
  - ABEMA の画像が実際に出たときのヒーローの見え方（セッションから `image.p-c2-x.abema-tv.com` に届かず、確かめた見え方は OGP 画像に替わった状態。平野さんのブラウザのプレビューで見える）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73f88804）: https://github.com/retroeater/mj-logs/tree/main/guide/73f88804

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/73f88804/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/32ed04cb.md
