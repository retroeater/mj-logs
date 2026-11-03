# CHAT-1104-TTL-02

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 0c106d379

## 指示

【Claude作成】Claude Code 向け指示：TTL-01 の続き（work/1101-prp-pub との衝突を承知で、title/ 全ページを新しい形で作り、比較ページつきのプレビューまで） Chat-Ref: CHAT-1104-TTL-02 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなく、TTL-01 のログだけの未マージのブランチなら、続けて使ってよい（`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい。rebase しない）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-02` を足す。

目的
CHAT-1104-TTL-01 の指示（目的・決定・前提・手順・止まる条件・完了条件）を、下の変更だけを加えてそのまま実行する。TTL-01 は未マージの `work/1101-prp-pub`（#641、pro/ の公開）との重なりで着手前に止まった。
決定（2026-11-04、平野さん）

* TTL-01 の判断が必要なことへの回答: 案 B。`work/1101-prp-pub` との衝突を承知で、今の `origin/cloudflare` の上で期ページを含む全ページを作る。後でマージする側が `generate_title_pages.py` の `member_card_html()`・`period_body()` と title/ の期ページの HTML の取り込みを解く
* （TTL-01 の決定はそのまま。中身はそのまま・写真カード案と面の行の案を比較ページで・対象は全ページ）

前提（チャット側。平野さんの決定ではない）

* TTL-01 の前提はそのまま
* 期ページの決勝メンバーの行き先は、今の `origin/cloudflare` の形（X）で作る。`work/1101-prp-pub` のマージ後に「個別ページのある在籍者は pro/、無ければ X」へ変わる前提で、行の案・写真カード案のどちらでも行き先を1か所（関数）で決める形にしておくと、後の取り込みが楽になる（要確認。作りは任せる）
* `work/1101-prp-pub` は消さない・取り込まない・変えない

手順

1. TTL-01 の手順1を行う。重なりの洗い出しでは、`work/1101-prp-pub` の `member_card_html()`・`period_body()` の変更の中身（行き先の決め方・HTML の形）を `## 経過` に引用し、後で取り込むときに解く箇所の見込みを書く
2. TTL-01 の手順2を行う（期ページを含む）
3. TTL-01 の手順3を行う

止まる条件

* TTL-01 の止まる条件のうち、未マージの work/ ブランチとの重なりは次に置き換える: `work/1101-prp-pub` との重なりでは止まらない（報告に書く）。ほかの未マージの work/ ブランチが `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突するときは止まる（同じファイルの別の箇所の変更では止まらず、ログに書く）
* ほかの止まる条件は TTL-01 のとおり

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、(1) 写真カード案と行の案のどちらにするか (2) 仮置きの一覧（1件ずつ、今の値と理由）(3) `work/1101-prp-pub` との衝突の見込み（ファイル・関数・解き方の案）を書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1104-TTL-02'` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし。対象 issue の指定なし
- 作業ブランチ: `work/1104-ttl` は TTL-01 のログだけの未マージのブランチ（`origin/work/1104-ttl` と同じ d584816ea、cloudflare は祖先でなかった）。指示のとおり続けて使い、`git merge origin/cloudflare` で取り込んだ（0c106d379、衝突なし）
- 手順0: このログの「指示」欄の末尾の行は指示文の最後の行と一致。TTL-01 のログの `## 報告` の状態に ` / 続き: CHAT-1104-TTL-02` を足した
- 手順1（読んだもの）は TTL-01 の `## 経過` のとおり。ほかに `scripts/generate_live_pages.py` の白地のページ（`.mj-live-n-page`。`body:is(.mj-saikyo-n-page, .mj-live-n-page)` に `--mj-n-*` を置き、最強戦の部品を共有している）を手本にした
- 手順1（未マージのブランチ、着手時と実装後に洗い直し）: `generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js` を変えているのは `work/1029-lgn-page`・`work/1101-prp-pub`・`work/1104-top` の3本（TTL-01 と同じ。`work/1104-top` は navbar.js・style.css の末尾への追加が 206→209 行に増えたが、場所は同じ）
  - `git merge-tree --write-tree HEAD <branch>`（実装のコミット 3d0d3c72d の時点）: `work/1029-lgn-page`・`work/1104-top` は衝突なし。`work/1101-prp-pub` は `scripts/generate_title_pages.py` だけが衝突（title/ の HTML は再生成の前の時点。再生成後は期ページの HTML も衝突する見込み）
  - `work/1101-prp-pub` の変更の中身（`member_card_html()`・`period_body()`、0ed406ea8）:

    ```python
    def member_card_html(entry: Entry, slugs: dict) -> str:
        """期ページの写真カード。帯は順位、押すと連盟プロの個別ページ(個別ページのある在籍者、2026-11-01 の決定⑨)、
        無ければ X のプロフィール(X ID の無い選手はリンクなし、TP-11)"""
        ...
        slug = None if person.is_unknown else slugs.get(person.name)
        if slug:
            return photo_card_html(person, label, pro_search.page_path(slug), alt, ASSET_PREFIX_SUB)
        if person.x_url:
            alt += "（X）"
        return photo_card_html(person, label, person.x_url, alt, ASSET_PREFIX_SUB, external=True)

    def period_body(period, broadcasts, taikai_list, warnings, meta, wayhome_episodes, slugs: dict) -> str:
        ...
        cards = [member_card_html(e, slugs) for e in period.entries]
    ```
    ほかに `pages()` で `slugs = pro_search.load_slugs()` を読み、`period_body(..., slugs)` に渡す。import に `pro_search` を足す
  - 取り込むときに解く箇所の見込み: この作業ブランチでは `member_card_html()` を `member_link()`（行き先と新しいタブで開くかを返す、写真カード案・行の案で共通）と `member_holder()` に分けた。prp-pub の行き先の決め方は `member_link()` の中に移せば足りる（`slugs` を引数で渡すか、`member_link()` の中で読む）。`period_body()` は引数に `slugs` を足し、`members_html()` → `member_holder()` → `member_link()` へ渡す。期ページの HTML は解いた後のスクリプトで生成し直す
- 手順2（実装、3d0d3c72d）: 下の「作ったもの」。生成はこの環境ではシートの鍵（`SHEETS_READER_SA_KEY`）が無く走らないため、`regenerate-page.yml` を作業ブランチで手動実行した（target_page=title）

- 作ったもの（3d0d3c72d・7bc3fc318・6f8b9cdc8）:
  - `<body class="mj-title-n-page">`（`NEW_BODY_CLASS`、`render_page()` で付ける。live/ の `WHITE_BODY_CLASS` と同じ差し込み方）。`style.css` の最強戦の節の `body:is(.mj-saikyo-n-page, .mj-live-n-page)`（14か所、暗い配色の `@media` を含む）に `.mj-title-n-page` を足し、`--mj-n-*` と最強戦の部品をそのまま使う
  - `navbar.js`: `<body>` の `mj-title-n-page` で白地のナビにし、「タイトル戦」に印（`newNavCurrent` の判定の末尾、`mj-live-n-page` の次に足した。lgn-page の先頭・prp-pub の `/pro/` の行・top の `if (newNavCurrent)` の後ろとは離した）
  - パンくず: `breadcrumb_html()` を `saikyo.build_breadcrumb()` に（13px 灰色、紺のリンク 44px）。JSON-LD の BreadcrumbList は見えているパンくずと同じ並びのまま（`test_breadcrumb_json_ld` が通る）
  - 題名: 大会ページ・期ページは `page_title_html()`（最強戦の対局のページの `.mj-saikyo-n-page-title`、28px 太字＋2行目 13px 灰色）。h1 の文言は読み上げ用の `visually-hidden` の語を足して今の「… 歴代優勝者」「… 決勝結果」を保った
  - 入口の年の切り替え: `year_picker_html()`（最強戦の年度ページと同じ `.mj-saikyo-n-picker`・`.mj-houou-sheet`。項目は `?year=` のリンク）。`assets/title.js` が開閉（`bindYearSheet` と同じ動き）・再読み込みなしの切り替え・`?year=`・h1・タブの題名を担う
  - 検索欄: `search_html()`（`.mj-saikyo-n-search`、題名の下）。0件・失敗の知らせは欄の下の `#title_filter_status`。検索の動き（`?q=`・`?name=`・`/`・Esc・120ms・結果の出し方）は変えていない。navbar とバーの固定、`--mj-title-nav-h`・`--mj-title-filter-h`、年のプルダウンの CSS は消した
  - 写真カード案と行の案: `Holder`（中身）→ `holders_html(holders, layout, …)` が写真カード（今の `photo_card_html()`）か面の中の行（`row_html()`、最強戦の `.mj-saikyo-n-row`・`.mj-saikyo-n-avatar`・`.mj-saikyo-n-chev`・`.mj-saikyo-n-pill`・`.mj-saikyo-n-place`）を作る。本体のページは `LAYOUT`（写真カード案）。期ページの決勝メンバーの行き先は `member_link()` の1か所
  - 比較ページ `title/compare-layout.html`（`compare_body()`）: noindex、どこからもリンクしない（`grep -r compare-layout` で本ページ以外に0件）、sitemap に載せない（`sitemap-title.xml` に0件）。ラジオボタンと `:has(#title_cmp_….:checked)` で切り替える（JS なし）。入口（現タイトルホルダーと年を選んだとき、年のシートも動く）・大会ページ（鳳凰戦・王位戦）・期ページ（第41期鳳凰戦〈決勝4名〉・第42期十段戦〈決勝5名〉）を両方の案で並べる
  - 期ページの節（決勝ライブ・決勝動画・帰り道）と大会ページの「成績」の見出しは `.mj-saikyo-n-label`（13px 灰色）。動画のカードは変えていない。「成績」のリンクは面の中の押せる行（›）
  - 検索結果の期のカードは面の色（`--mj-n-surface`・角丸 12px・影なし）、順位のバッジは灰色の地に灰色の字（最強戦の敗退のピルと同じ）、優勝の淡い金はそのまま
- 生成（`regenerate-page.yml`、作業ブランチで手動実行）: 1回目は target_page=title で「生成スクリプトがありません: scripts/generate_title.py」で失敗（ページ名は `title_pages`。`python3 scripts/regenerate.py --list` で確かめて再実行）。2回目（36ca28110）と、レビューの修正の後の3回目（f4b38e70a）は成功
  - 生成物の差分（`git diff --stat origin/cloudflare...HEAD -- title/ sitemap-title.xml`）: 387 files。title/ の HTML（入口1・大会20・期365）、新しい `title/compare-layout.html`、`sitemap-title.xml` の lastmod だけ（`git diff … sitemap-title.xml` の lastmod 以外の行は0）。`title/search.json`・`title/years.json`・`llms.txt`・`sitemap.xml` は差分なし。ほかのページの HTML の変化なし（`git diff --name-only origin/cloudflare...HEAD` の title/・docs/ 以外は assets/title.js・navbar.js・scripts の4本・sitemap-title.xml・style.css）
  - 公開ページの noindex・canonical・og:* は変わっていない（レビューのサブエージェントが meta の差分0を確かめた）
- テスト: `python3 -m unittest discover -s scripts/tests` → 1807 tests OK（自分の変更で落ちた3本〈成績のリンク・帰り道の見出し・入口の年の切り替えの文言〉は新しい形に直した。行・カードの組み立てのテスト7本を足した）。`node --check assets/title.js navbar.js` 通過
- 確かめ（headless Chromium、`python3 -m http.server` で作業ツリーを配信、UA に mj-internal。スクリプトは `docs/logs/CHAT-1104-TTL-02/shoot.js`）:
  - 9ページ（入口の既定・`?year=2025`・`?q=白鳥`、大会ページ 鳳凰戦・王位戦、期ページ 第41期鳳凰戦〈決勝ライブ・帰り道あり〉・第42期十段戦〈決勝5名・帰り道あり〉、比較ページ〈写真カード案・行の案〉）× 360px・1280px × 明・暗 = 36枚
  - はみ出し（横スクロール・main の要素が画面の右端を越える）・切れ（overflow hidden の葉の要素の文字の切れ）・重なり（行・題名・検索欄・パンくずの子どうし）: 標準の字体で 36/36 ページとも 0件。幅の広い字体（IPA ゴシック・WenQuanYi Zen Hei を `font-family` に当てた）でも 36/36 ページとも 0件（`measure-default-font.json`・`measure-wide-font.json`）
  - 文字の拡大: ルートの文字 150%・200% で 入口・第41期鳳凰戦・比較ページ（行の案）のはみ出し 0件
  - 部品の状態ごとの高さ（360px・1280px とも同じ値）: 題名の選ぶボタン 44px（「現タイトルホルダー」・「2025年優勝者」とも）、年のシートを開いた高さ 663px、検索欄のまとまり 44px（知らせなし）→ 72px（「該当する選手はいません」あり。検索中は本来の内容が隠れるため、下のずれは本来の内容に及ばない）、期ページの題名 55px（日付ありの第41期・「1984年」の第1期とも）
  - 年の切り替え: シートで「2025年優勝者」を押すと URL `?year=2025`・タブの題名「2025年優勝者 | タイトル戦 | …」・題名のラベル・h1「タイトル戦 2025年優勝者」が替わり、カード 25枚。シートを開いた間は `/` で検索欄へ移らない（フォーカスはシートのまま）
  - 共通ナビ: `/title/`・`/title/houou/41.html` は `mj-nav-new` で「タイトル戦」に印、`/saikyo/2025/`・`/houou/` は従来どおり（「最強戦」「鳳凰戦」）
  - 画像（標準の字体の36枚）は `docs/logs/CHAT-1104-TTL-02/`
- レビュー（docs/notes/self-review.md、新しい文脈の general-purpose に目的・決定・差分・コード規約を渡した。1回目）:

  | 指摘 | 扱い |
  |---|---|
  | `row_html`・`rank_mark_html`・`holders_html` の分岐にテストが無い | 直した（6f8b9cdc8、`test_title_years.LayoutTest` 7本） |
  | docs/notes/title-pages.md が固定バー・年のプルダウンの記述のまま | 直した（e0e050120、docs だけの別コミット） |
  | シートを開いている間も `/` が背面の検索欄へ移る | 直した（6f8b9cdc8） |
  | 比較ページに h1 が5つある | 意図どおり（noindex・リンクなしの一時のページで、節の題名を本体のページと同じ部品で見せるため） |
  | 比較ページの期の節の代替画像のパスが1階層ずれる | 直した（6f8b9cdc8、`members_html()` に asset_prefix） |
  | 暗い配色で検索結果の順位のバッジが明るいまま、メンバー限定のバッジの縁が見えない | 順位のバッジは直した（灰色の地に灰色の字）。メンバー限定のバッジは「動画のカード自体は変えない」の前提どおり残した（仮置きの一覧） |
  | 年のシートが saikyo/live の写し・「完了」が直書き | 「完了」は `saikyo.SHEET_DONE_LABEL` に（6f8b9cdc8）。HTML・JS の共通化は見送り（案が決まった後に検討） |
  | navbar.js だけパスで判定 | 直した（6f8b9cdc8、`<body>` の class で判定） |
  | 参考: `scripts/promo_video/title/capture.mjs` が `.mj-filterbar`・`#title_select` を見ている | 見送り（`#title_select` はこの変更の前から無く、すでに壊れている。報告に書く） |

  2回目の往復はしていない（修正は指摘の範囲だけで、テスト・生成・撮影をやり直した）
- 実装後の未マージのブランチとの取り込みの確かめ（`git merge-tree --write-tree HEAD <branch>`、実装のコミットの時点）: `work/1029-lgn-page`・`work/1104-top` は衝突なし。`work/1101-prp-pub` は `scripts/generate_title_pages.py` が衝突（再生成後は title/ の期ページの HTML も衝突する見込み）
- プレビュー: 作業ブランチの push のうち、Workers Builds の check-run が付いたのは f4b38e70a（3回目の再生成）だけで、結果は failure（Worker `mj`、Build ID c6a53676-1e3a-41b0-a2f6-408eb92e4655、completed 2026-11-03T16:32:33Z）。失敗した check-run は本番とプレビューの区別も段階も出ない（docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」）。ほかのコミット（3d0d3c72d〜6f8b9cdc8）には check-run が付いていない（まとめられたか、待ちの間に次の push が来た）
  - 同じ時間帯に、cloudflare の d30c186d3（他セッションのコミット、15:55）の本番の `mj`・`mj-ask` も failure（16:11）。16:20 以降の cloudflare・work/1104-aic・work/1104-pil のビルドは結果が出ていなかった（16:35 の時点）。`work/1029-lgn-page` は 16:33 に success。原因はこのセッションからは確かめられない
  - 空のコミットで再ビルドは起こさなかった。ローカルの配信（`python3 -m http.server`）で全ページを撮って確かめた（上の「確かめ」）

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-TTL-03
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: プレビューのビルドが失敗（f4b38e70a、Build ID c6a53676-1e3a-41b0-a2f6-408eb92e4655）し、確認用の URL は無い。確認したページ（ローカルの配信）は入口（既定・?year=2025・?q=白鳥）・鳳凰戦・王位戦・第41期鳳凰戦・第42期十段戦・比較ページ（/title/compare-layout.html）。この版の印: 入口の題名が「現タイトルホルダー ▾」（ナビが白地で「タイトル戦」が紺）
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - (1) 写真カード案と行の案のどちらにするか。本体のページは今は写真カード案（`LAYOUT = LAYOUT_CARDS`）。比較ページ /title/compare-layout.html のラジオボタンで切り替えて見られる（入口〈年のシートも動く〉・鳳凰戦・王位戦・第41期鳳凰戦・第42期十段戦）。決まったら使わない案のコード・ラジオボタン・比較ページを消す
  - (2) 仮置きの一覧（今の値と理由）:
    - 共通ナビの判定: `<body>` の class `mj-title-n-page`（前提の案は「`/title/` のパス」。最強戦・live/ と同じ見分け方にそろえた、レビューの指摘）
    - 期ページのパンくずの最後: 「第41期」（対局日を外した。13px の1行で「タイトル戦 / 鳳凰戦 / 第41期（2025年2月1日）」は 360px で折り返し、題名の2行目と同じ日付が2度出るため）。JSON-LD の BreadcrumbList も同じ並びに変わる
    - 期ページの題名: 期の題（「第41期鳳凰戦」28px 太字）と2行目に対局日（13px 灰色、最強戦の対局のページと同じ）。日付が空なら2行目なし。h1 の読み上げは「第41期鳳凰戦 決勝結果2025年2月1日」
    - 大会ページの題名: 大会名（28px）と2行目に期の範囲「第1期〜第42期」（13px 灰色。今まで出していなかった期の範囲を題名の2行目に使った）
    - 節の見出し（決勝ライブ・決勝動画・帰り道・成績）: 最強戦の「歴代最強位」と同じ 13px 灰色（`.mj-saikyo-n-label`）。上の余白 8px を足した（決まった値の表に無い）
    - 節の間隔: `.mj-title-section` の下 16px（今まで 32px。面の間隔 16px にそろえた）
    - 「成績」のリンク: 面の中の押せる行（名前の字＋›）。今までは本文のリンク
    - 検索欄: 最強戦・live/ の白地のページの検索欄（`.mj-saikyo-n-search`）を題名の下に。form にはしない（Enter で再読み込みしない、今の動きのまま）。0件の知らせは欄の下 13px 灰色（出ている間は欄のまとまりが 44px → 72px）
    - 年のシート: 題「年を選ぶ」、項目は「現タイトルホルダー」と「2026年優勝者」…（`?year=` のリンク）
    - navbar の固定をやめた（スクロールでナビも流れる。最強戦の新しいページと同じ）
    - 行の案の入口: 写真・選手名（16px）・その下に大会名（13px 灰色）・›。押すと大会ページ（年を選んだときは期ページ、大会名に期を足す帯の文字のまま）
    - 行の案の大会ページ: 左に期（13px 灰色、最小幅 3.5rem〈決まった値の表に無い。「第42期」の4字が入る幅〉）・写真・名前・›
    - 行の案の期ページ: 右端に順位（優勝は金のピル、ほかは「2位」の 13px 灰色。前提の「灰色の数字」に「位」を付けた）と ›（X へ、新しいタブ）。X の無い選手は › なしで押せない
    - 写真の無い選手の行: 名前の頭の1字の灰色の丸（最強戦と同じ）。名前の記録が無い行は「—」
    - 検索結果の期のカード: 面の色・角丸 12px・影なし。順位のバッジは灰色の地に灰色の字（最強戦の敗退のピルと同じ）、優勝の淡い金（#fff3cd／#8a5a00）はそのまま
    - 写真カード（影・帯・名前・ホバーで浮く動き）と動画のカードは変えていない。メンバー限定のバッジ（#212529 の地）は暗い配色では地の縁が見えない
  - (3) `work/1101-prp-pub` との衝突の見込み:
    - `scripts/generate_title_pages.py`: `member_card_html()`（この作業ブランチで `member_link()`・`member_holder()`・`members_html()` に分けた）、`period_body()`（引数と本体を書き換えた）、`pages()`（prp-pub が `slugs` を読む行）。解き方の案: prp-pub の行き先の決め方（個別ページのある在籍者は `pro_search.page_path(slug)`・新しいタブにしない、無ければ X）を `member_link()` に移し、`slugs` を `period_body()` → `members_html()` → `member_holder()` → `member_link()` へ渡す（比較ページの `compare_body()` の `members_html()` の呼び出しにも渡す）。import の `pro_search` を足す
    - title/ の期ページの HTML（365ページ）と `sitemap-title.xml` の lastmod: 解いた後のスクリプトで生成し直す（docs/notes/branch-operations.md の生成物の衝突の解き方）
    - `navbar.js`・`style.css`: prp-pub が変えた行（「連盟プロ」の href・`/pro/` のコメント、houou/ の節）とは離れており、`git merge-tree` では衝突なし（`generate_title_pages.py` だけが衝突）
  - そのほか: `scripts/promo_video/title/capture.mjs` が `.mj-filterbar`・`#title_select` を見ている（`#title_select` はこの変更の前から無い。title/ の固定バーも今回なくなった）。docs/notes/design.md の「共通ナビ（新しい形）」の適用範囲には title/ を足していない（案が決まってから）
- 未確認の項目:
  - プレビュー（Workers Builds）の成功と確認用 URL。f4b38e70a のビルドが失敗した（同じ時間帯に本番も失敗）。ダッシュボードの「Retry build」（Build ID c6a53676-1e3a-41b0-a2f6-408eb92e4655）か次の push で通るかを確かめる
  - 実機（iPhone）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6fe4b43f）: https://github.com/retroeater/mj-logs/tree/main/guide/6fe4b43f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
