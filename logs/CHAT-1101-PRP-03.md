# CHAT-1101-PRP-03

- 着手日時: 2026-11-01
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 2caddbbcc

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その1: 生成・メタ・導線）を作業ブランチに作り、プレビューで止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-03
マージ: 判断待ちで止まる（この作業ブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: いつでも（CHAT-1101-PRP-02 と別のセッションで同時に動かしてよい。PRP-02 は別の作業ブランチ）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-prp-pub を使う（同じ系列の work/1101-prp・work/1101-prp-docs と分ける。公開までこのブランチを続ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-prp-pub origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
pro/ の公開（#641）のうち、生成・メタ情報・導線の変更を作業ブランチに作り、プレビューで確かめられる状態にする。転送（`jpml_pros.html` の 301・Worker の `?name=` の 301）・検知の付け替え・質問欄のカード（K8）・`playerUrl()`・title/ の優勝者は、このブランチの続きの指示（CHAT-1101-PRP-04 の予定）で行う。番号は CHAT-1101-PRP-01 のログの `## 経過`「2. 棚卸しの表」（以下「表」）の番号。

### 決定（2026-11-01、平野さん）
- ① 個別ページ 1,098件もサイトマップに載せる。形は saikyo/・title/ と同じ別ファイル（`sitemap-pro.xml`）（表5）
- ② 名前の面だけのページも含め、全員を index させる（表33）
- ⑤ 個別ページの title と og:title は「<名前> | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」にする（表13）
- ⑦ トップの表の並べ替えのボタンを 44px の基準まで広げる。表の行の高さ 40px は変えない（表35）
- ⑧ 画像の alt は空のままにする（表36）
- pro/ のトップに canonical（自分自身）を付ける（表9）。トップページ（`index.html`）のサイドメニュー・`llms.txt` の「プロ」の行・`sitemap-pages.xml` の `jpml_pros.html` を pro/ に置き換える（表3・4・5）
- 前の決定から: noindex を外す（表1）、navbar の「連盟プロ」の行き先を `/pro/` に（表2）、個別ページに canonical（og:url と同じ値、K13、表10）、トップの OGP 画像を `img/ogp/pros/` から `img/ogp/pro/` へ移す（写さない。`jpml_pros.html` の参照も移した先に、K14、表16）、「未公開」の記述を直す（表24）
- 公開の時期は点検が済んだら。live/（#362）を pro/ より先に公開する（マージはこの指示ではしない）

### 前提（チャット側。平野さんの決定ではない）
- 実物の場所（チャット側が origin/cloudflare の 50dcad88 で見た。要確認）: noindex は `scripts/generate_pro_pages.py:52` の `NOINDEX_TAG`、個別ページの title は同じファイルの `PageMeta(title=f"{pro.name} | {SECTION} | ryoei.pro", …)`（726行目付近）。並べ替えのボタンは `.mj-pro-sort`（`assets/pro.js`）。サイトマップの束ねは `sitemap.xml`（コメントに配下のファイルの一覧がある）と `scripts/update_sitemap_lastmod.py`（`SITEMAP_*` の定数）
- `sitemap-pro.xml` は、saikyo/・title/ の生成スクリプトが別ファイルを書く形に倣う。lastmod は手で書かない（`update_sitemap_lastmod.py --from-git`、docs/notes/sitemap-lastmod.md）
- `llms.txt` の「プロ」の行は、今の pro/ の中身（1,098名・個別ページ・絞り込み・質問欄）に合わせ、個別ページの URL の形を書くかはほかの行の書き方に倣う（チャット側の案）
- canonical の文書（`docs/notes/cloudflare.md`「配信設定」の付ける・付けないページの一覧と、K13 の理由の言い方）もこの指示で直す。CHAT-1101-PRP-02 は同じ文書のこの節を直さない
- navbar.js は未マージの work/1029-lgn-page・work/1028-lgn・work/1017-lvp も別の行（現在地の分岐）を変えている（PRP-01 のログの重なりの表）。行き先の行（30行目付近）とは離れている見込み
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下にする（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）。全ページの確かめは生成物・ローカル・プレビューで行う

## 手順
1. 着手時に `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: pro_pages` で手動実行し、生成が止まる理由が無いことを確かめる（表26。止まる理由があれば一覧を書いて止まる）。#641 に着手中のコメントを書く
2. 生成とメタ情報: noindex を外す・canonical（トップと個別）・個別ページの title と og:title の形・OGP 画像の移動（K14）・`sitemap-pro.xml` の生成と `sitemap.xml`・`update_sitemap_lastmod.py`・docs/notes/sitemap-lastmod.md・並べ替えのボタンの大きさ・「未公開」の記述（表24 の一覧）・`docs/notes/cloudflare.md`「配信設定」と `docs/notes/static-generation.md`「ページの一覧」。テストを足す・直す（`scripts/tests/test_pro_pages.py` ほか）。pro/ を再生成する
3. 導線: `navbar.js` の「連盟プロ」の行き先・`index.html` のサイドメニュー・`llms.txt`・`sitemap-pages.xml`（`jpml_pros.html` の行を `/pro/` に）。`scripts/tests/test_navbar.py` ほかを直す。そのうえで次を確かめて `## 経過` に書く:
   - テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る
   - 生成物の全件で: noindex 0件・canonical が og:url と一致・title の形（最短と最長の名前つき）・`sitemap-pro.xml` の URL の数が pro/ のページの数と一致・ほかのページの生成物に想定外の差分が無い（差分を種類ごとに数えて書く）
   - プレビューで、トップ・`pro/yurika.aoi.24/`・`pro/a-ji.skull-reaper.27/`・404（`pro/taro.yamada.99/`）を 360px と 1280px・明暗で見る（並べ替えのボタンの大きさを測った値も書く）
   - 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

## 止まる条件
- 手順1の手動実行で、生成が止まる理由が出た（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 前提の実物の場所が食い違い、どこを変えればよいか決められない
- #641 にほかのセッションの着手中のコメントが残っている
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- pro/ 以外のページの生成物に、この指示の変更で説明できない差分が出た（一覧を書いて止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 同じ Chat-Ref のコミット・ログ: なし。`PRP` は3本目。0章ゲート: 触るファイルと未マージの `work/*` の重なりは下
- 作業ブランチ: `work/1101-prp-pub` はローカル・リモートとも無かったので `git checkout -b work/1101-prp-pub origin/cloudflare`（起点 2caddbbcc）
- #641 にほかのセッションの着手中は無い（最後は PRP-01 の完了。PRP-02 は #641 の本文を直したがコメントは書いていない）。着手中のコメントを書いた
- 前提の実物の場所: `NOINDEX_TAG` は `scripts/generate_pro_pages.py:52`、個別ページの title は 726行目の `PageMeta(title=f"{pro.name} | {SECTION} | ryoei.pro", …)`、`.mj-pro-sort` は `assets/pro.js:183`（並べ替えの動き）と `style.css:5700`（見た目）、`sitemap.xml` のコメントと `update_sitemap_lastmod.py` の `SITEMAP_*`。すべて前提どおり
- 未マージの `work/*` と変えるファイルの重なり: `navbar.js` は work/1029-lgn-page・1028-lgn（`/log/` の現在地の分岐を先頭に足す）・1017-lvp（`mj-live-n-page` の分岐）が変えているが、どれも現在地の分岐で、変えた行（30行目の行き先、82行目の pro/ のコメント）とは別の行。`.github/workflows/check-assets.yml` は 1029-lgn-page も変えている（下で確かめる）。止まる条件には当たらない

### 1. 生成が止まる理由の確かめ（表26）

- `regenerate-page.yml` を `work/1101-prp-pub`・`check: true`・`target_page: pro_pages` で手動実行: run 41270870476 は success、「check: 止まるページなし」（`pro_pages: 通る(--check)`）

### 2. 生成とメタ情報

- `scripts/generate_pro_pages.py`: `NOINDEX_TAG` を消した。canonical をトップ（`TOP_URL`）と個別ページ（`pro.url`、og:url と同じ）に付けた。個別ページの title を `PLAYER_TITLE`「<名前> | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」にした（og:title は `PageMeta.og_title` を渡さないので同じ）。OGP 画像を `img/ogp/pro/index-white.png` に。`build_sitemap()` で `sitemap-pro.xml`（トップ＋個別ページ全件。exam/ の `build_sitemap()` と同じ形、既にある URL の lastmod は残し新しい URL は当日）を書き、`--check` でも組み立てる
- サイトマップの置き方: 決定の「`sitemap-pages.xml` の `jpml_pros.html` を pro/ に置き換える」と「個別ページは別ファイル `sitemap-pro.xml`」を合わせて、`/pro/` は `sitemap-pro.xml` の先頭に置き（exam/・mtsuku/ が一覧を自分のサイトマップの先頭に置くのと同じ）、`sitemap-pages.xml` からは `jpml_pros.html` の行を消して除外の理由をコメントに書いた（同じ URL を2つのサイトマップに載せないため）。`sitemap.xml` に `sitemap-pro.xml` を足し、`update_sitemap_lastmod.py` に `SITEMAP_PRO`（`pro/` 配下）を足した。`regenerate-page.yml` の XML の確かめの一覧にも `sitemap-pro.xml` を足した（コミットの手順は `sitemap*.xml` で拾う）
- OGP 画像: `git mv img/ogp/pros img/ogp/pro`（写していない）。`generate_jpml_pros.py` のテンプレートと `jpml_pros.html` の og:image を移した先に直した（jpml_pros.html はこのブランチでは再生成せず、テンプレートと同じ1行だけを直した）。`scripts/tests/test_og_image.py` の許可の一覧と `docs/notes/ogp.md` も直した
- 並べ替えのボタン: `style.css` の `.mj-pro .mj-pro-sort` に `width: 100%`・`min-width: 44px`・`min-height: 44px`・`text-align: inherit`。行の高さは変えていない
- 「未公開」の記述（表24）: `generate_pro_pages.py` の docstring・`assets/pro.js:1`・`navbar.js:82`・`style.css` の節の見出し・`.github/workflows/check-assets.yml` の許可リストの注記・`docs/notes/pro-pages.md`・`docs/notes/static-generation.md`（「ページの一覧」の表と前書きの1行）。`docs/handover.md` の索引の行は「公開は #641」で未公開と書いていないので変えていない。`top_ask()` の「決定 K8 は公開の段」は PRP-04 で直す
- `docs/notes/cloudflare.md`「配信設定」: 付けないページから `pro/` を外し、`jpml_pros.html` の注記を「pro/ の公開で 301」に。付けているページに pro/ のトップ（クエリは表示の絞り込み）と個別ページ（クエリを読まない）を足し、件数を 1,664 → 2,763（pro/ 1,099）にした。K13 の「wayhome/ の各話と同じ例外」の言い方は、この節の今の基準（クエリを読まないページには付ける）の中に入れたので、例外とは書いていない
- `docs/notes/sitemap-lastmod.md`: 対象に `sitemap-mtsuku.xml`・`sitemap-exam.xml`（もとから抜けていた）・`sitemap-pro.xml` を足した
- テスト: `scripts/tests/test_pro_pages.py` に `PublishTest`（個別ページの noindex なし・title と og:title・canonical と og:url・画像、トップの canonical、`sitemap-pro.xml` の並びと lastmod）を足した。修正前の generate_pro_pages.py（origin/cloudflare）では3本とも失敗（2 FAIL・1 ERROR）、修正後は通る

### 3. 導線

- `navbar.js` の「連盟プロ」の行き先を `/pro/`、`index.html` のサイドメニューを `/pro/`（ルート相対）、`llms.txt` の行を「[連盟プロ](https://ryoei.pro/pro/)」に。個別ページの URL の形は、ほかの行（title/・houou/ の `https://ryoei.pro/title/<大会>/` など）に倣って書いた。`scripts/tests/test_navbar.py` の期待を `/pro/` に

### 4. 未マージのブランチとの衝突（止まる条件）

- `git merge-tree --write-tree --name-only HEAD <branch>` で、work/1029-lgn-page との取り込みが `docs/notes/static-generation.md` で衝突した（同じブランチと origin/cloudflare の間では衝突0件。こちらの変更で生じた衝突）。衝突は2か所: 「ページの一覧」の前書きの1行（256行目。こちらは pro/ の「noindex・メニュー未掲載」を消し、向こうは同じ行に log/ を足す）と、表の pro/ の行（280行目。向こうはその直後に log/ の行を足す）
- 止まる条件（同じ行を変えている・取り込みで衝突する）に当たるのはこのファイルだけなので、**`docs/notes/static-generation.md` の変更だけを戻して**（`git checkout origin/cloudflare -- docs/notes/static-generation.md`、コミット 7afdd6315・84b9d1983）ほかは進めた。戻した後の衝突は0件。pro/ の「未公開」の記述とサイトマップの説明は、公開のマージの時点（work/1029-lgn-page の後か前かに合わせて）で直す（判断が必要なことに書いた）
- work/1028-lgn（6件）・work/1017-lvp（926件）の衝突は、origin/cloudflare との間でも同じ件数で、こちらの変更による増減は無い

### 5. 生成と確かめ

- 新旧のコードの比べ: `regenerate-page.yml` を `target_page: all`・`dry_run: true`・`compare_ref: origin/cloudflare` で手動実行（run 41271108182、success）。中身の違うファイルは 1,100件で、内訳は `pro/<slug>/index.html` 1,098件（各8行: noindex の削除1・canonical 1・title 2・og:title 2・og:image 2）・`pro/index.html` 1件（4行: noindex 1・canonical 1・og:image 2）・`sitemap-pro.xml`（新しいファイル）。**pro/ 以外のページの生成物に差分は0件。** `jpml_pros.html` は差分に出ない（新しいコードの生成が、手で直した og:image の1行と同じものを書く）
- 生成: `regenerate-page.yml` を `target_page: pro_pages` で手動実行（run 41272458751、success）→ コミット 1ccb48144「chore: regenerate pro/ via GitHub Actions」（1,101ファイル: pro/ 1,099・`sitemap-pro.xml`・`sitemap-pages.xml` のトップページ `/` の lastmod 2026-10-29 → 2026-11-01〈`index.html` を変えたため、`--from-git`〉）
- origin/cloudflare を取り込んだ（93d665130、衝突なし）
- テスト: `python3 -m unittest discover -s scripts/tests` は 1,736件 OK（pro/ の再生成の前は `test_og_image` が旧パスの pro/ で1件失敗。再生成の後に通った）。足した `PublishTest` の3本は修正前の生成スクリプトでは失敗（2 FAIL・1 ERROR）
- 生成物の全件（1,099ページ）: noindex 0件・canonical と og:url の不一致 0件・title と og:title の不一致 0件・title の形の外れ 0件・og:image が `img/ogp/pro/` でないもの 0件・title の重複 0件。title の最短は「大和 | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」（32字、`pro/yamato.26/`）、最長は「スカルリーパーA-Ji | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」（41字、`pro/a-ji.skull-reaper.27/`）
- `sitemap-pro.xml`: URL 1,099件（pro/ のページ数と一致、集合も一致、重複0）、lastmod はすべて 2026-11-01（新しい URL は当日）。`sitemap.xml`・`sitemap-pages.xml` は XML として読める。サイトマップの本体に `jpml_pros` は0件
- プレビュー（版ごとの URL、93d665130 の check-run「Workers Builds: mj」は success）を headless Chromium で、トップ・`pro/yurika.aoi.24/`・`pro/a-ji.skull-reaper.27/`・`pro/taro.yamada.99/`（404）× 360px・1280px × 明暗の16画面:
  - 状態 200（404 は 404）。メタの robots は無し（404 だけ有り）。応答ヘッダの `x-robots-tag: noindex` は workers.dev のプレビューに付くもの（#114）で本番には付かない。canonical はトップ `/pro/`・個別は自分自身。navbar に `href="/pro/"` の「連盟プロ」がある
  - 横のはみ出し 0。基準を割るコントラスト 0
  - 並べ替えのボタン: 360px で 名前 112×44・所属 44×44・入会期 44×44・鳳凰 44×44・桜花 44×44、1280px で 名前 152×44・ほか 44×44（前は 24×18・36×18）。表の行のリンクは 40px のまま（決定⑦）
  - 404 のページの本文のリンク（98×17）は前からのもので、この変更とは関係しない
- 本番（ryoei.pro）へのリクエスト: 0件

### 6. 差分のレビュー（新しい文脈のサブエージェント、1回）

渡したもの: 指示文の目的と決定、`git diff origin/cloudflare...HEAD`（pro/ の生成物・ログ・`sitemap-pro.xml` を除く）と代表の生成物2ファイル、CLAUDE.md「コード規約」。サブエージェントもテスト 1,736件 OK・noindex 0件・canonical 1,099件・`sitemap-pro.xml` 1,099件を確かめた。

| 指摘 | 扱い |
|---|---|
| `style.css` の `.mj-pro-sort` の `min-width: 44px` で、360px の名前の列が細り名前が折り返す（中） | 直した（1e475482c「fix: keep pro/ sort buttons within their column width」）。手元で測ると、`?all=1` の 360px で折り返す名前は 旧 CSS 2名・`min-width` あり 17名・外した後 2名。外した後のボタンは高さ 44px・幅は列の幅（360px で 名前 152・所属 39・入会期 36・鳳凰 30・桜花 30、1280px で 172・45・41・35・35）。幅が 44px に届かない列があるので「判断が必要なこと」に書いた |
| `docs/handover.md` の #219 の行に「未公開」が残る（中） | 直した（PRP-02 で書いた行。「2026-10-31。公開は #641」に） |
| `docs/notes/static-generation.md` の OGP 画像のパス（468行目）が旧いまま（低）。256・280行目は意図して残したなら明記を | 468行目は直した（5718a463f）（work/1029-lgn-page と衝突しないことを確かめた）。256・280行目は「判断が必要なこと」に書いた |
| `llms.txt` のサイトマップの行に `sitemap-pro.xml` が無い（低） | 直した |
| `regenerate-sitemap.yml` の XML の確かめに `sitemap-pro.xml` が無い（mtsuku・exam も前から無い）（低） | 直した（3つとも足した）。作業ブランチで手動実行: run 41273040673 は success（8ファイルとも parse、「変更なし」） |
| `update_sitemap_lastmod.py` の docstring に pro/ が無い（低） | 直した（mtsuku・exam も） |
| `docs/notes/cloudflare.md` の「`jpml_pros.html` は pro/ の公開で 301」が済んだように読める（低） | 直した（「301 にする予定」） |

直した後のプレビュー（版ごとの URL、5718a463f の check-run は success）で、上の16画面をもう一度見た: はみ出し 0・コントラスト 0・canonical と navbar は同じ。`check-assets.yml` は 99d8df461・93d665130・5718a463f で success。テスト 1,736件 OK。2回目のレビューはしていない（直したのは指摘どおりの小さな変更で、上の測定で確かめた）

## 報告

- 状態: 判断待ち / 続き: CHAT-1101-PRP-04
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページはトップ・`pro/yurika.aoi.24/`・`pro/a-ji.skull-reaper.27/`・404（`pro/taro.yamada.99/`）。この版で見える印: トップの表の見出し（名前・所属…）の行が高くなった（44px）。navbar の「連盟プロ」が `/pro/` へ行く
- マージ: 未（指示のとおり。マージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に別の指示で）
- issue: #641（着手中のコメント）
- 判断が必要なこと:
  - 並べ替えのボタンの幅: 決定⑦は 44px の基準。高さは 44px にしたが、幅は見出しのセルいっぱい（360px で 鳳凰・桜花 30px・入会期 36px・所属 39px）。幅にも 44px を付けると 360px で名前の列が細り、折り返す名前が 2名から17名に増える（レビューの指摘）ので付けていない。今の形（高さだけ 44px）でよいか
  - `docs/notes/static-generation.md`「ページの一覧」の pro/ の記述（前書きの「noindex・メニュー未掲載」と表の pro/ の行）は、未マージの work/1029-lgn-page と同じ行・隣の行で衝突するため、このブランチでは直していない（止まる条件。このファイルの変更だけ戻して進めた）。公開のマージの時点で直す（PRP-04 か、公開のマージの指示に入れる）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f1d4a0a0）: https://github.com/retroeater/mj-logs/tree/main/guide/f1d4a0a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
