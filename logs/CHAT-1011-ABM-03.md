# CHAT-1011-ABM-03

- 着手日時: 2026-10-11
- 対象issue: #425
- ブランチ: work/1011-abm
- 着手時HEAD: d43b0f9135a478e635f86f8c6849d64e806331b7

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の年度・対局・卓の新しいページを試作する（未公開・プレビューで確かめる）
Chat-Ref: CHAT-1011-ABM-03
マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める）
貼る時機: いつでも（平野さんが「最強戦」シートに M「動画URL」を足し、URL を1〜2卓入れた後なら、ABEMA のリンクも確かめられる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

## 目的
#425 の決定（docs/decisions/saikyo.md の 2026-10-11〈CHAT-1011-ABM-02〉の節）のとおりに、新しい年度ページ・対局のページ・卓のページを試作し、プレビューで平野さんに見てもらう。今の公開中のページ（トップ・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）はこの指示では変えない。

### 決定（平野さん）
- この指示で新しく決めたことは無い。決定の正は docs/decisions/saikyo.md の CHAT-1011-ABM-02 の節（grill 1〜11）。そこと食い違う作りにしない

### 前提（チャット側。平野さんの決定ではない）
- 試作は新しい URL の場所に作る: `saikyo/<年度>/index.html`（新しい年度ページ）・`saikyo/<年度>/<対局識別子>.html`・`saikyo/<年度>/<卓のアンカー>.html`。どれも noindex（`NOINDEX_TAG` の定数にし、外すのを公開の指示の1か所の変更にする）、サイトマップ・`llms.txt`・navbar に載せず、今のトップ・年度ページからリンクしない（docs/new-page-checklist.md「段1」）。新しいページ同士のリンク（年度 → 対局 → 卓、パンくず）は張る
- 年度プルダウンとパンくずの年度の行き先は、試作の間は新しい年度ページ（`/saikyo/<年度>/index.html` か `/saikyo/<年度>/`。`_redirects` の行はこの指示では足さないので、プレビューで開ける方にする。どちらにしたかをログに書く）
- 未対局の対局・卓のページも作る（中身は「未対局」）
- 同じ日の2つ目の対局の枝番 `-2` は、新しいページにだけ使う。今の年度ページの `?match=` と共有ボタンは変えない（公開の指示で年度ページごと置き換えるため）
- ページ数の見込み: CHAT-1011-ABM-01 のログ「手順2」の表の計（対局 219・卓 643）と年度 16 から、新しいファイルは 878（シートの更新で 2026年度が増えることがあるので、対局・卓の増加は合わせて +10 まで許す。減るのは許さない）
- M「動画URL」列は、あれば使い、無ければ ABEMA のリンクは出さずに作る（どちらだったかをログに書く）

## 手順
1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`saikyo/`・`style.css` の同じ関数・同じ行を変えていないか。「最強戦」シートを生成と同じ経路で読み、見出し（M 列の有無）・行数・M 列に値のある卓の数と、4行で値が食い違う卓を書く
2. 作る: `generate_saikyo_pages.py`（と必要なら `assets/saikyo.js`・`style.css`）に、上の3種のページの生成を足す。中身は決定 grill 1・3〜6・10 のとおり（年度ページは勝ち上がり者と「未対局」、選手名の絞り込みなし、対局ごとの共有ボタンなし。対局のページは今の `?match=` の表示と同じで卓の見出しから卓のページへ。卓のページは4人・「ABEMA で見る」・同じ対局のほかの卓と対局のページへの導線。個別ページに共有ボタンなし。新しい年度ページの JS は `#<卓のアンカー>`・`#match-…` を卓・対局のページへ `location.replace`）。canonical は自分自身、OGP 画像は年度の今の画像、題名と説明はページごと。固定バー・`mj-saikyo-page` のクラスは今の年度ページと同じ。テストを `scripts/tests/` に足し、`python3 -m unittest discover -s scripts/tests` を通す。`regenerate.py` の `saikyo_pages` の対象パスで新しいファイルが拾われ、毎朝の再生成で消されないことを確かめる
3. 確かめてプレビューに出す: 生成して、(a) 新しいファイルの数（見込みとの差）、(b) 今の公開中のファイル（トップ・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）に差分が無いこと、(c) 新しいページの内部リンクがすべて実在のファイルを指すこと、(d) 各ページの id の重複が無いこと、(e) 同じ日の2対局（例: 2025 の `20251214` と `20251214-2`）がそれぞれのページになり中身が違うこと、(f) 配信ファイル数（`check_asset_limits.py`）、(g) headless Chrome でスマホ幅と PC 幅の見え方（崩れの有無を文で）を確かめてログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2025年度の新しい年度ページ・`20251214` と `20251214-2` の対局のページ・卓のページ1つ・2026年度の新しい年度ページ（未対局を含む）・`#<卓のアンカー>` 付きの新しい年度ページの URL を並べる

## 止まる条件
- 決定（docs/decisions/saikyo.md の CHAT-1011-ABM-02 の節）と食い違う作りが要る（作らずに報告する）
- 新しいファイルの数が見込みの 878 より少ない、または +10 を超えて多い
- 今の公開中のファイルに差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
- 未マージの work/ ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- シートを2回読んで行数が違う
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-10-11）

- 同じ Chat-Ref のコミットは0件。ローカルの `work/1011-abm` は `origin/cloudflare` の祖先、リモートもマージ済み → `git merge --ff-only origin/cloudflare`（d43b0f91）
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4つともある
- #425 は Open。他セッションの着手中コメントは無い。着手中のコメント: https://github.com/retroeater/mj/issues/425#issuecomment-6105110138

### 手順1: 確かめ

- 未マージのブランチ（自分を除く2件: origin/work/1009-swp-526・origin/work/1011-swp-nav）は `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`saikyo/`・`style.css` をどれも変えていない（merge-base からの差分）
- 「最強戦」シート（`fetch_sheet` と見出しは gviz の列の label、2回読んだ）: 見出しは A 対局日〜L 備考の12列で **M「動画URL」は無い**。全行 2,744 / 2,744、K=Y 2,572 / 2,572（同じ）。M 列が無いため、値のある卓・4行で食い違う卓は数えられない（0）→ **ABEMA のリンクは出さずに作った**（列ができれば生成が読む）

### 手順2: 作ったもの（a9e7b3f1。生成物は 61fed0e3）

- `scripts/generate_saikyo_pages.py`
  - 定数: `NOINDEX_TAG`（公開の指示でここを空にする）・`SUB_ASSET_PREFIX = "../../"`・`SUB_INDEX = "index.html"`・`SUB_TOP_HREF`・`SUB_AVATAR_FALLBACK`・`SUB_CLASS = "mj-saikyo-sub"`・`VIDEO_HEADER = "動画URL"`・`VIDEO_LABEL = "ABEMA で見る"`・`UNPLAYED_LABEL`・`SECOND_MATCH_SUFFIX`
  - `Match.page_ident`（新しいページの識別子）と `Match.table_slug()`（卓のアンカーの対局識別子の部分を page_ident に置き換えたもの）、`Table.video`
  - `assign_page_idents()`: 表示順（`display_order()` 済み）で同じ識別子の2つ目以降に `-2`。先に出る方（今の `?match=` が選ぶ方）が日付だけを引き継ぐ（grill 1）。今の `ident`・アンカー・`?match=`・共有ボタンは変えない
  - `load_videos()` / `videos_from_rows()`: シート全体を `fetch_table()` で見出しで読み、「動画URL」が無ければ空。（年度, 対局日, 対局, ステージ, 卓）ごとに、1行以上に入っていればその URL、4行で違えば警告してリンクなし（grill 10）。`attach_videos()` で卓に付ける
  - `winners()`: 最後のステージで「通過」の人。表示は `Player.label`（決勝の通過は「優勝」、ほかは「通過」）（grill 5）
  - `has_page()`: 対局日が空の対局は個別ページを作らず、生成のログに警告を出す。年度ページには出す（見出しはリンクにしない）（grill 3。今は該当0件）
  - `build_sub_year_body()`（年度ページ: 固定バーは年度プルダウンだけ、パンくず、対局ごとに見出し〈対局のページへのリンク〉と勝ち上がり者の写真＋「優勝」「通過」のバッジ、勝ち上がり者がいなければ「未対局」、選手名の絞り込み・共有ボタンなし、今のアンカーの対応表 `#saikyo_anchor_map`）・`build_match_page_body()`（対局のページ: 今の `?match=` の表示と同じく全卓・各卓4人、卓の見出しから卓のページへ）・`build_table_page_body()`（卓のページ: 4人・「ABEMA で見る」〈URL があるときだけ〉・対局のページと同じ対局のほかの卓への導線）。未対局の卓は4人と「未対局」
  - `write_sub_pages()`: `saikyo/<年度>/index.html`・`<page_ident>.html`・`<table_slug>.html` を書く。canonical・og:url は自分自身（年度ページは `https://ryoei.pro/saikyo/<年度>/`）、OGP 画像は年度の今の画像（`og_image_for()`）、題名と説明はページごと。`<body class="mj-saikyo-page">`（`add_body_class()`）と固定バーは今の年度ページと同じ
  - `remove_stale_sub_pages()`: シートから消えた対局・卓・年度の新しいページを消す（今の年度ページの削除は今のまま）
  - `build_year_select()` に行き先の引数を足した（既定は今の動き）。`build_player_html()` に代替アバターの引数を足した（深さが違うため。既定は今の動き）
  - パンくずの年度と年度プルダウンの行き先は **`index.html`**（`../<年度>/index.html`）にした。`html_handling: none` で `_redirects` の行が無いと `/saikyo/<年度>/` は開けないため。「最強戦」と「歴代最強位」は今のトップ `/saikyo/`（`../`）
- `assets/saikyo.js`: `.mj-saikyo-sub` のページの処理を足した（固定バーの高さ・年度プルダウン、年度ページは `#<卓のアンカー>`・`#match-…` を対応表で引いて `location.replace`）。今の年度ページの処理は `.mj-saikyo-year:not(.mj-saikyo-sub)` にした（新しいページも CSS のため `mj-saikyo-year` を持つ）
- `style.css`: パンくず・見出しの帯のリンク（下線、44px）・勝ち上がり者のバッジ・「未対局」・「ABEMA で見る」・ほかの卓の導線（いずれも新しいクラス。今のページの見た目は変わらない）
- テスト `scripts/tests/test_saikyo_pages.py`（9件。枝番・今のアンカーが変わらないこと・勝ち上がり者・年度ページに絞り込みと共有が無いこと・未対局・対応表・卓のページの導線と動画・動画URLの列が無い／1行だけ／食い違い）。新しい関数のテストなので、変更前のコードでは関数が無く通らない。`python3 -m unittest discover -s scripts/tests` は 827件 OK
- `regenerate.py saikyo_pages` で生成すると、対象パス `saikyo/` に新しいファイルが入り（`regenerate-page.yml` は `git add -A -- saikyo/`）、2回続けて生成しても 878件のまま（消えない）

### 手順3: 確かめ

- (a) 新しいファイル: **878**（年度 16・対局 219・卓 643）。見込み 878 との差 0
- (b) 今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html` 16枚・`sitemap-saikyo.xml`）: 生成後の `git diff` は空
- (c) 新しいページの内部リンク（href・src・プルダウンの value、26,320件）はすべて実在のファイルを指す（最初の生成では代替アバター `../img/avatar.svg` が深さ違いで856件切れていたので、`SUB_AVATAR_FALLBACK` を渡す形に直した）。対応表の行き先もすべて実在。今の年度ページ16枚の対局・卓の id はすべて対応表にある
- (d) id の重複: 878ページとも0。noindex は878ページとも付いている。canonical は878ページとも自分自身
- (e) 同じ日の2対局: `saikyo/2025/20251214.html`（ファイナルトーナメント、決勝1卓 `20251214-s1`）と `saikyo/2025/20251214-2.html`（ファイナル 2nd Stage、ベスト8 A・B卓 `20251214-2-s1-a`・`-s1-b`）は別のページで、題名・卓・対局者が違う。年度ページの見出しのリンクもそれぞれへ向く
- (f) 配信ファイル数（`check_asset_limits.py`）: 3,335 / 20,000（16.7%）
- (g) headless Chromium（ローカルの `python3 -m http.server`、390×844 と 1280×900）: 2025年度の年度ページ・`20251214`・`20251214-2`・卓 `20251214-2-s1-a`・2026年度の年度ページの5ページとも横のはみ出し 0、JS のエラー 0。`body` の上余白は 117px（390）・141px（1280）で固定バーと navbar に重ならない。最初の撮影でパンくずの「/」が文字より上にずれていたので、`.breadcrumb-item` を縦の中央そろえにして直した。スマホ幅は4人が2×2、年度ページの勝ち上がり者は1人の対局で左半分に1枚、ファイナルの段は2列で並ぶ。2026年度は未対局の5対局が見出しと「未対局」だけになる
  - `#` の移動: `2025/index.html#20251214-s1-a` → `20251214-2-s1-a.html`、`#match-20251214-s1-a` → `20251214-2.html`、`#20251214-s1` → `20251214-s1.html`、対応表に無い `#nothing` はそのまま。年度プルダウンで 2024 を選ぶと `../2024/index.html` へ移る
  - 今のページの回帰（JS を変えたため）: `2025.html?match=20251214` は1対局だけ（今までどおりファイナルトーナメント）、選手名の絞り込み（桑田→3卓）、トップの `?q=桑田`（1人）。エラー 0

### プレビュー

- push 61fed0e3。check-run「Workers Builds: mj」「check」とも success。作業ブランチの別名のプレビューで、2025年度の年度ページ・`20251214`・`20251214-2`・卓 `20251214-2-s1-a`・2026年度の年度ページが 200（どれも noindex あり）、`/saikyo/2025/` は 404（`_redirects` の行が無いため。想定どおり）、`assets/saikyo.js` は新しい版。URL はターミナルの最終報告にだけ書く

### docs/decisions

この指示の「決定」節は新しい決定が無い（CHAT-1011-ABM-02 の節が正）。作業中の平野さんの回答も無い。docs/decisions/ には足していない

## 報告

- 状態: 判断待ち
- ブランチ: work/1011-abm
- ログ: https://github.com/retroeater/mj/blob/work/1011-abm/docs/logs/CHAT-1011-ABM-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-abm
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページ: 2025年度の新しい年度ページ・対局 `20251214` と `20251214-2`・卓 `20251214-2-s1-a`・2026年度の新しい年度ページ・`#<卓のアンカー>` 付きの2025年度の年度ページ
- マージ: 未（平野さんの判断待ち）
- issue: #425（着手中のコメント）
- 判断が必要なこと:
  - プレビューを見て、試作の見た目・中身でよいか。マージするか（cloudflare へ入れると、noindex のまま本番に 878 ページが出る）
  - 見た目で決めてほしいところ: 年度ページの勝ち上がり者の写真は順位の数字を出さず、左上の金の三角（今の年度ページで順位が空欄の通過者と同じ形）と写真の下の「優勝」「通過」のバッジにした。対局名・卓の見出しは帯ごとリンクにし、下線で示した
  - 決定 grill 3 の「知らせる」は、今は生成のログ（Actions のジョブの出力）に警告を出すだけ。issue などへの通知にするかは未決（今は該当0件）
  - 動画URLの列（M）がまだ無いため、「ABEMA で見る」はテスト（`scripts/tests/test_saikyo_pages.py`）でしか確かめていない。列を足して1〜2卓入れた後に、再生成とプレビューで確かめる
  - マージ（段1）のときに要るもの: docs/notes/static-generation.md「ページの一覧」への「noindex・メニュー未掲載、公開は #NNN」の記載と、公開の issue の起票（docs/new-page-checklist.md「段1」）。この指示では行っていない
- 未確認の項目:
  - 「ABEMA で見る」のリンクの実際の表示（M 列が無い）
  - プレビューをブラウザで開いた見え方（セッションでは HTTP の状態と中身だけ確かめた。見え方はローカルの headless Chromium で確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d43b0f91）: https://github.com/retroeater/mj-logs/tree/main/guide/d43b0f91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
