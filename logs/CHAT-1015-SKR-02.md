# CHAT-1015-SKR-02

- 着手日時: 2026-10-15
- 対象issue: #549（関係: #566・#425）
- ブランチ: work/1015-skr-pub
- 着手時HEAD: f6b567c44

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいページの公開（#549）の変更を作業ブランチで全部作り、プレビューで止める（マージしない）
Chat-Ref: CHAT-1015-SKR-02
マージ: 判断待ちで止まる（このプレビューから告知動画を撮り、動画の確定後に別の指示でマージ＝公開する）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr-pub の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-skr-pub を使う。リモートに無ければ origin/cloudflare から作り、未マージの origin/work/1015-skd-top を merge で取り込む（rebase しない。work/1015-skd-top 自身には push しない）。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。作業中に origin/cloudflare が進んでいたら merge で取り込んでよい。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く（CLAUDE.md「ブランチ運用」）。生成物でない文書の衝突は、両方の変更が両立するもの（追記どうし・隣り合う行）なら両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#549（最強戦の新しい年度・対局・卓のページの公開）の変更を、`work/1015-skd-top`（トップの作り直し、CHAT-1015-SKD-17〜21）と合わせて1つの作業ブランチで全部作り、Workers Builds のプレビューで止める。このプレビューから告知動画を撮り（次の指示）、動画の確定後にマージする（＝公開）。この指示では cloudflare へマージせず、issue の状態・ラベル・本文も変えない（着手中のコメントだけ書く）。

### 決定（2026-10-15、平野さん）
- 公開の順番: (1) #549 の変更を作業ブランチで全部作りプレビューで止める（この指示） (2) プレビューをチャット側と平野さんが確かめる (3) 同じプレビューから告知動画を撮る (4) 動画が確定したらマージ＝公開 (5) 平野さんが X に投稿する
- 告知動画（次の指示で作る）: 見本の選手は桑田憲汰。選手の写真・名前・成績はそのまま映してよい。曲は今までと同じ曲調 b。構成・テロップ・投稿文はチャット側の案のまま（docs/notes/page-announcement.md の型、約23秒）
- SKD-21 のプレビューへの回答: 順位が空の卓で通過の人を上、敗退の人を下に並べる形「この並べ方でよい」
- docs/decisions/saikyo.md の 2026-10-15（CHAT-1015-SKR-01）の決定 1・2・3・5・7・13 と、#549 のコメント（2026-10-15、CHAT-1015-SKR-01「公開の指示に入れること」）を、この指示の実装の範囲とする（ただし issue を閉じる・コメントするのはマージの指示で行う）

### 前提（チャット側。平野さんの決定ではない）
- #549 の本文の「段2のチェックリスト」の「変更」と「段2の作業」が実装の一覧の正（要確認: 着手時に読み直し、ログの経過に一覧を写して、この指示でやる・マージの指示でやる・平野さんの手作業、に分けて書く）。マージの指示へ回すもの: 公開後の確かめ全部、Cloudflare のリダイレクトルール（Single Redirects、平野さんの手作業）、#530・#425・#382・#383・#566・#549 へのコメントとクローズ、Search Console の作業
- og:image（年度・対局・卓のページ）と、ABEMA の回が無い卓のヒーローの画像は、ABEMA の作品ページの og:image（2026-10-15 に平野さんが PC で取った HTML では `https://image.p-c2-x.abema-tv.com/image/series/68-38/thumb.png?height=720&quality=75&version=1762941079&width=1280`）。この URL は生成スクリプトの定数1つにする。`version` は変わることがある。セッションから ABEMA の画像サーバに接続できないことがある（CHAT-1015-SKR-01 ではプロキシが拒否した）ので、取得の確かめはこの指示の必須にしない（できなければ「未確認の項目」に書く。チャット側が Chrome で確かめる）。画像のドメインは外部依存として許可済み（docs/handover.md）。トップ `saikyo/index.html` の og:image は変えない（決定の対象外）
- 新しいトップ・年度・対局のページは `body.mj-saikyo-n-page`。`body.mj-saikyo-page`・濃色の固定バー（`.mj-filterbar` の最強戦の分）・`.mj-saikyo-year`・`.mj-saikyo-top` 用の CSS・JS は、今の年度ページ `saikyo/<年度>.html` の生成をやめると使われなくなる見込み（CHAT-1015-SKR-01 で `mj-saikyo-page` を付けるのは今の年度ページだけと確かめた〈ログの経過〉。要確認: 消す前に `style.css`・`assets/*.js`・`navbar.js`・生成スクリプトの参照を洗い出し、live/・title/・wayhome/ など saikyo/ 以外も使う共有の部品〈`.mj-filterbar` の共通の値・写真カード `.mj-saikyo-card` など〉は消さない）
- 年度ページの JS の旧 URL の `#<卓のアンカー>`・`#match-…` の移動（`location.replace`）は残す（#549 の本文）
- 新しい年度ページのリンクは `_redirects` の行を足すまで `/saikyo/<年度>/index.html` の形（`html_handling: none`）。行を足した後に `/saikyo/<年度>/` へ変える（#549 の本文）
- 毎朝の `regenerate-saikyo.yml` で cloudflare の `saikyo/` が進む。マージの指示で取り込み直して生成し直すので、この指示の時点の生成物の差は参考の値でよい

## 手順
1. 確かめる（ここで止まる条件に当たれば止まる）:
   - #549・#566・#425 の本文と最新のコメント、他セッションの着手中コメント（未解除のもの）。#549 に着手中のコメントを書く
   - `git branch -r --no-merged origin/cloudflare` の各ブランチ（`work/1015-skd-top` を除く）が、この指示で変えるファイル（`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css`・`navbar.js`・`_redirects`・`sitemap-saikyo.xml`・`llms.txt`・`saikyo/`・関係する docs）の同じ行・関数を変えていないか
   - 作業ブランチを用意し、`origin/work/1015-skd-top` を取り込んだ後に `python3 -m unittest discover -s scripts/tests` が通ること。SKD-21 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-SKR-02` を足す（このブランチ上のログで）
2. 作る（#549 の「変更」「段2の作業」のうち、マージの指示へ回すもの以外。docs/new-page-checklist.md「段2」に従う）:
   - noindex を外す（`NOINDEX_TAG`）。今の年度ページ `saikyo/<年度>.html` の生成をやめ、ファイルを消す。新しい年度ページを `saikyo/<年度>/index.html` とし、`_redirects` に `/saikyo/<年度>/` → `index.html` の 200 の行と、旧 `/saikyo/<年度>.html`・`/saikyo/<年度>` → `/saikyo/<年度>/` の 301 の行（#549 の本文のとおり年度ごとの静的な行。`generate_live_pages.py` も `_redirects` を書くので置き場所を確かめる。先頭の `/  /index.html  200` は消さない）
   - トップの検索結果・歴代最強位のカード・年度の切り替え・パンくずの行き先を新しい形へ。サイトマップ（`sitemap-saikyo.xml`、lastmod は手で書かない）・`llms.txt`・docs（`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/saikyo-page-design.md`・`docs/handover.md` の「未公開」）を直す
   - og:image とヒーロー（前提のとおり）。使われなくなる CSS・JS を消す（前提のとおり、共有の部品は残す）。テストを足し・直し、全件通す。共有の定数・関数を変えたら全ページを再生成して差分を確かめる（CLAUDE.md「判断・作業の原則」）
3. 確かめて止まる:
   - 生成物の差を種類に分けてログに表で書く（ページの種類ごとの件数: 消した今の年度ページ・新しい年度/対局/卓のページで noindex が消えたもの・og:image が変わったもの・ヒーローが変わった卓・それ以外の差）。「それ以外の差」は中身を書く
   - Workers Builds のプレビュー（check-run「Workers Builds: mj」）で、headless Chromium（390px と 1100px、明・暗）を使い、トップ・2025年度の年度ページ・対局のページ1枚・ABEMA の回がある卓と無い卓（例: `2026/20261004-s1-a`）を開き、崩れ・リンク切れ・旧 URL の 301 の行き先・noindex が無いことを確かめる。トップで「桑田」を検索した結果（告知動画の見本）も撮る。プレビューに接続できなければ「未確認の項目」に書く
   - ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 卓のページ `2026/20261004-s1-a` のヒーローが ABEMA の「麻雀最強戦」の画像、2025年度の年度ページのリンクが `/saikyo/2025/` の形）。URL はターミナルの最終報告にだけ書く
   - 決定（上の「決定」の4項目）を docs/decisions/saikyo.md と docs/decisions/design-renewal.md（SKD-21 の決定）、告知動画の決定は docs/decisions/page-release.md に足す

## 止まる条件
- 他セッションの着手中コメントが #549 にある
- 未マージの work/ ブランチ（`work/1015-skd-top` を除く）が、この指示で変えるファイルの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- `origin/work/1015-skd-top` の取り込みや `origin/cloudflare` の取り込みで、生成物でない文書・コードが両立しない形で衝突した
- 消そうとした CSS・JS を saikyo/ 以外のページ（live/・title/・wayhome/ など）が使っている（その部分は消さずに残し、ログに書いて進めてよい。全部が共有なら止まる）
- `_redirects` の行が 2,000 行の静的な上限や、先頭の行の規則（CLAUDE.md「禁止事項」）に触れる
- 生成物の差に、上の種類で説明できない差（saikyo/ 以外のページの変化を含む。毎朝の再生成のシートの変化で説明できるものは除く）がある
- cloudflare への push が求められる場面になった（この指示ではマージしない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: プレビューで公開してよい形か）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git log --all --grep=CHAT-1015-SKR-02` は0件。`origin/work/1015-skr-pub`・ローカルとも無いため `git checkout -b work/1015-skr-pub origin/cloudflare`（f6b567c44）
- #549・#566・#425 に未解除の他セッションの着手中コメントは無い。#549 に着手中のコメントを書いた
- 未マージの work/ ブランチ（`work/1015-skd-top` を除く）の重なり: `origin/work/JBK`（女流勉強会、CHAT-1015-JBK-01・02）だけが、この指示で変えるファイルのうち `_redirects`（mtsuku の行の後に2行。今回は `/saikyo/` の行の後に足すので別の箇所）・`style.css`（女流勉強会のヒーロー、1520行付近。別の箇所）・`docs/handover.md`（7章の表に1行。別の箇所）・`docs/notes/static-generation.md` を変える
  - static-generation.md は「ページの一覧」の段落の行（253行、ページの列挙の1行）が**同じ行**。止まる条件の「同じ行」に当たるため、この指示ではその行を変えずに進めた（行の中の「最強戦の新しい年度・対局・卓のページ（…noindex・メニュー未掲載、公開は #549）」の直しはマージの指示へ回す）。表の行は JBK が mtsuku の行の後に1行足し、今回は次の最強戦の2行を直す（隣り合う行）
- `origin/work/1015-skd-top` の取り込み（447e86501）: `docs/decisions/design-renewal.md` だけが衝突。cloudflare 側の `## 2026-10-15（CHAT-1015-SKD-22）` と skd-top 側の SKD-17〜21 の節がどちらも末尾への追記だったので、両方を残した。解いた後の並び:

```
## 2026-10-15（CHAT-1015-SKD-17）
## 2026-10-15（CHAT-1015-SKD-18）
## 2026-10-15（CHAT-1015-SKD-19）
## 2026-10-15（CHAT-1015-SKD-20）
## 2026-10-15（CHAT-1015-SKD-21）
## 2026-10-15（CHAT-1015-SKD-22）
```

- 取り込み後に `python3 -m unittest discover -s scripts/tests` が通った（1191件）。SKD-21 のログの状態を「判断待ち / 続き: CHAT-1015-SKR-02」にした

### #549 の実装の一覧（本文の「変更」「段2の作業」）

| 項目 | 扱い |
|---|---|
| noindex を外して再生成（`NOINDEX_TAG`） | この指示（定数ごと消した） |
| navbar は変えない | この指示（変えていない） |
| サイトマップ（879件） | この指示 |
| `llms.txt` に URL の形 | この指示 |
| title・description・og:title・og:image を確かめる（og:image は決定5で ABEMA の作品の画像） | この指示 |
| ABEMA の回が無い卓のヒーロー | この指示（決定5） |
| 今の年度ページを 301 | この指示（`_redirects` の年度ごとの3行） |
| static-generation.md・saikyo-page-design.md・handover.md の「未公開」 | この指示（static-generation.md の段落の行だけはマージの指示。上の重なり） |
| cloudflare へのマージ | マージの指示（平野さんの確認の後） |
| 年度ページを `saikyo/<年度>/` へ、今の年度ページの生成をやめる（共有ボタン・`?match=`・選手名の絞り込み・`share.js` の読み込みも外す） | この指示 |
| `_redirects` の 200 と 301 の行 | この指示（静的な行 48 行＋説明の1行。静的 93/2,000） |
| トップの検索結果・歴代最強位の行き先 | この指示（`<年度>/` の形） |
| 年度のシート・パンくずの行き先を `/saikyo/<年度>/` へ | この指示（`../<年度>/`・`./`） |
| 新しい年度ページの JS の `#` の移動は残す | この指示（残した） |
| 使われなくなる CSS・JS を消す（決定1） | この指示 |
| Cloudflare のリダイレクトルール（`?match=`、Single Redirects） | 平野さんの手作業（公開のマージの直後） |
| #458 の対象の見直し・新しい URL の登録の確認（決定3） | マージの指示（公開から約2週間後の確認はチャット側がカレンダーに） |
| #530・#425・#382・#383・#566・#549 へのコメント・クローズ、#566 の本文の直し | マージの指示 |
| 公開後の確かめ全部・Search Console の作業 | マージの指示・平野さんの作業 |
| handover.md 5章の #549 の行 | マージの指示（公開と同時に消す） |

### 実装（167b4ec5e、c8cfbe4b1）

- `scripts/generate_saikyo_pages.py`: `NOINDEX_TAG`・`BODY_CLASS`・`MATCH_PARAM`・`TOP_HREF` と、今の年度ページだけの関数（`build_match_html`・`build_filterbar_html`・`build_year_select`・`build_year_body`・`build_year_nav`・`build_year_page`・`add_body_class`）を消し、`main()` を年度・対局・卓のページとトップだけにした。saikyo/ 直下の `index.html` 以外の `.html` は消す。サイトマップは `sitemap_urls()`。`_redirects` に年度の3行が無ければ警告（`warn_missing_redirects()`、`redirect_lines()`）
- og:image: 定数 `ABEMA_SERIES_IMAGE_URL`（`{w}`・`{h}` を入れる形。og:image は 1280×720 で、指示の URL と同じ文字列になる）。`series_og()` を年度・対局・卓のページに。トップは年度の画像のまま
- 卓のヒーロー・カード: 回の無い卓は作品の画像（ヒーロー 1280×720、カード 640×360）。読めないときの代わりは年度の OGP 画像（回のある卓も同じ）。**回の無い卓にも出典「画像: ABEMA」を出すようにした**（ABEMA の画像を出すため。指示に無い判断なので「判断が必要なこと」に書く）
- 卓のページの写真カードの `data-name`（今の年度ページの絞り込みだけが使っていた）を外した
- `assets/saikyo.js`: 固定バーの高さ・年度プルダウン・絞り込み・`?match=` の処理を消した。トップの検索結果の年度の見出しのリンクを `<年度>/` に
- `style.css`: 今の年度ページ・旧トップだけの規則を消した（431行減）。消したクラス: `.mj-saikyo-years`・`.mj-saikyo-match`・`.mj-saikyo-match-head`・`.mj-saikyo-match-title`・`.mj-saikyo-all`・`.mj-saikyo-siblings`・`.mj-saikyo-table`・`.mj-saikyo-table-title`・`body.mj-saikyo-page`（navbar の固定・上余白・`.mj-filterbar` の最強戦の分・`.mj-saikyo-year-select`・`.mj-filterbar-count:empty`）・`html:has(.mj-saikyo-year, .mj-saikyo-top)`・`.mj-saikyo-filter-search`・`.mj-saikyo-champion-year`・`.mj-saikyo-result*`・`.mj-saikyo-results*`。`:is(.mj-saikyo-year, .mj-saikyo-table-page)` は `.mj-saikyo-table-page` だけに。写真カード（`.mj-saikyo-card`・`.mj-saikyo-players` など live/・mtsuku/ と共有）と `.mj-filterbar` の共通の規則は残した。消す前に、style.css の `mj-saikyo` で始まるクラスをすべて、HTML・JS・Python（docs と tests を除く）で使われているかを洗い出した（使うのが JS だけのトップの検索結果の `.mj-saikyo-n-hit-*` などは残した）。title/ の CSS のコメントの写し元の参照は「#549 で廃止」と書き換えた
- `assets/share.js` の先頭のコメントから saikyo/ を外した
- `_redirects`: `/saikyo/  /saikyo/index.html  200` の後に説明の1行と 2011〜2026 の各3行。先頭の `/  /index.html  200` はそのまま。`check_asset_limits.py`: 静的 93/2,000・動的 4/100
- テスト: `scripts/tests/test_saikyo_redirects.py` を足した（年度ディレクトリごとに3行があるか、saikyo/ 直下がトップだけか）。`test_saikyo_pages.py` を直し・足した（回の無い卓のヒーローが作品の画像、パンくずの `./`、`series_og()`、`sitemap_urls()`）。`test_title_years.py` の共有ボタンの確かめから `saikyo/2025.html` を外した。`docs/notes/redirects.md` の「棚卸しの対象」に旧年度 URL 16行を足した（`test_redirects_inventory.py`）。全件通る（1196件）
- 共有の関数（`scripts/lib/`）は変えていない。ほかのスクリプトが読む `generate_saikyo_pages` の名前（`SEARCH_ICON_SVG`・`X_LARGE_SUFFIX`・`check_duplicate_ids`・`load_rows` など）は残っており、`generate_live_pages`・`generate_title_pages` などの import が通ることを確かめた。全ページの再生成はしていない（saikyo/ 以外の生成物は変わらない）
- 文書: `llms.txt`、`docs/handover.md`（最終更新の1行・共有ボタン・外部依存）、`docs/notes/static-generation.md`（表の2行と生成の説明。段落の行は上のとおり残した）、`docs/notes/saikyo-page-design.md`

### 生成物の差

変更前のスクリプト（447e86501）で同じ時点のシートから生成したもの（作業ディレクトリの外にコピーして実行）と、変更後を比べた（`classify.py`。意図した変更を変更前に当てて、残る差が無いかを見る）。変更前のスクリプトの生成物はコミット済みの saikyo/ と一致した（シートの変化なし）。

| 種類 | 件数 |
|---|---|
| 消した今の年度ページ（`saikyo/<年度>.html`） | 16 |
| 年度・対局・卓のページで noindex が消えたもの | 878 |
| 同じく og:image が ABEMA の作品の画像になったもの | 878 |
| ヒーローが変わった卓（回の無い卓。年度の OGP 画像 → 作品の画像） | 40 |
| それ以外の差 | なし（残差 0件。上に当たらない差は、年度ページへのリンクの形〈`index.html` → ディレクトリ〉、回の無い卓のカードの画像、出典の追加、`data-name` の削除で、すべて同じページの中） |
| トップ `saikyo/index.html` | 歴代最強位の行のリンクが `<年度>/index.html` → `<年度>/` だけ |
| `sitemap-saikyo.xml` | 17件 → 879件（トップ・年度16・対局・卓。新しい URL の lastmod は生成スクリプトが当日の日付を入れる。手では書いていない） |
| saikyo/ 以外の生成物 | 変化なし |

## 報告

- 状態: 作業中
- ブランチ: work/1015-skr-pub
- ログ: https://github.com/retroeater/mj/blob/work/1015-skr-pub/docs/logs/CHAT-1015-SKR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skr-pub
- 確認用URL: なし
- マージ: 未
- issue: #549
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6b567c4）: https://github.com/retroeater/mj-logs/tree/main/guide/f6b567c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
