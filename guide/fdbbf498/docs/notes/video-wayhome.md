# video_wayhome / wayhome エピソードページの実装記録

このファイルは完了済み作業の記録。現状とルールは docs/handover.md。

---

## 新しい回を追加する手順

シートに行を足したあと、手で実行するものが2つある（どちらも自動では走らない）。

1. **`YOUTUBE_API_KEY=... python3 scripts/fetch_youtube_meta.py`**
   → `data/youtube_meta.json` を更新してコミットする。
   これを忘れるとシートにあってJSONに無い動画があることになり、**ページ生成が止まる**（下の「#192 第2段」節）
2. **`python3 scripts/build_wayhome_ogp.py`**
   → 一覧ページのOGP画像 `img/ogp/wayhome/index-<最新話の公開日>.jpg`（最新4本のサムネイルを2×2に並べたもの）を作り直す（#339）。
   新しい名前で書き出し、古い `index*.jpg` は削除される（`/img/*` は immutable 配信のため同じURLで中身を変えない、`docs/notes/ogp.md`）。
   **作るのはこの1枚だけで、個別ページ用の画像は作らない**（個別の `og:image` はYouTubeのサムネイルをそのまま使う）。
   **同じ公開日の回を続けて足した場合は、同じ名前で中身が変わるためエラーで止まる。** 名前の付け方を手動で判断する
3. **`python3 scripts/regenerate.py video_wayhome` を実行し、画像の追加・削除と `video_wayhome.html`（og:image の1行）を同じコミットに入れる。**
   画像とページが別々に反映されると、og:image が削除済みの画像を指す時間ができる。
   **手順2を忘れてもページは壊れない。** 今の最新話の画像が無ければ、一覧の og:image は共通の `img/ogp.png` に戻る
4. エピソード個別ページなどほかの再生成は `regenerate-page.yml`（週次の `all`、または手動実行）

自動化（GitHub Actions での生成）は **#340** で判断する（文字を描かないため、フォントの導入は不要。Pillow のみ）。

OGP画像やタイトルを変えたあと X で確認するときは、URLに `?x=<未使用の数字>` を付けて貼る（X はカードの取得結果をキャッシュするため）。詳細は `docs/notes/ogp.md`。

---

### 前後エピソードの向き・一覧ヒーローの操作追加（#316 / #317、2026-09-14）

**#316 前後エピソードを公開日基準に。** `generate_wayhome_episodes.py` は
`sorted_by_date_desc()`（`publishedAt` 降順）で並べ、その1つ手前を「前」に
していたため、前＝より新しい回・次＝より古い回と日付と逆だった。前＝1つ後ろ
（より古い回）、次＝1つ手前（より新しい回）に入れ替えた。表示位置（左が前・
右が次）は維持したため、片側しかない2枚の欠ける側が入れ替わった（最新話
`UtxpVoWy2GY` は前のみ、最古 `mj3hZ_udzKM` は次のみ）。
- 着手前に、シートの行順が `publishedAt` 降順と一致すること（38本、同時刻
  なし）を確認した。生成はシート順ではなく `publishedAt` で並べるため、
  一致しなくても隣接の組み合わせは変わらない
- #215 のサムネイルはリンクと一緒に組み立てるため、全38枚でhrefと
  サムネイルの動画IDが一致することを生成物で確認した

**#317 一覧ヒーローに個別ページと同じ操作を追加。** `build_hero_html()` に
「決勝戦を見る」（H列 決勝動画URL、#193）と X・note アイコン（WH-53）を
追加した。組み立ては個別ページと同じ `lib/wayhome.py` の
`build_final_video_link_html()` / `index_player_links()` /
`build_player_links_html()` を使い、値が無ければ出さない扱いも同じ。
- データ元: 決勝動画URLは「帰り道」シートH列（2026-09-14時点で値があるのは
  最新話 `UtxpVoWy2GY` の1本のみ）。X（I列）・note（K列）は `jpml_pros` の
  「プロ」シート（WH-57 以降、帰り道シートB列は参照しない）
- 一覧で引くのは最新話の選手1名だけ（ほかの選手の照合警告を出さないため）
- 選手名の右にアイコンを並べるため、個別ページと同じ `.mj-video-hero-title`
  で h2 を包んだ。CSS の追加はない
- 幅1280/390/320px・740×360pxで、ボタン列（再生・共有・決勝戦を見る）が
  1行に収まり、ヒーロー内の文字ブロックが上端で切れないことを確認した
- X・noteのリンク先を選手個別ページへ切り替える検討（#195）の対象に、
  一覧ヒーローも加わった（#195 にコメント済み）

### 個別ページのUI改善3件（#216 / #213 / #215、2026-09-14）

**#216 sticky navbar を個別ページへ拡張。** #188 のスコープ
`body:has(.mj-video-list)` を、一覧と個別38枚にだけ付く
`.mj-video-page` に変えた（`nav.navbar` と collapse の `max-height`
規則の2つ）。フィルタバー（#189）・ヒーローの `isolation`・全幅化は
一覧専用のため `.mj-video-list` のまま。`scroll-padding-top` は
`html:has(.mj-video-page)` を navbar 高さのみにし、一覧は後続の
`html:has(.mj-video-list)` でフィルタバー分を足して上書きする。
`--mj-nav-h` の実測は `wayhome_episodes.js` にも追加した（高さが
ブレークポイント・collapse 開閉で変わるため）。

**#213 一覧へ戻る導線をヒーロー左上の丸ボタンに。** ヒーロー内ボタン列の
「一覧へ戻る」を廃止し、`.mj-video-hero` 内に矢印アイコンのリンク
`.mj-video-back`（`aria-label="一覧に戻る"`、44×44px）を置いた。
ヒーロー基準の `position: absolute` で画像の左上（被写体の多い中央を
避ける）に重ねるため、流れの外にありレイアウトはずれない。
- `position: fixed` は使わない。モバイル幅でスクロール時に本文の左端へ
  重なるため。navbar 直下の全幅 sticky バーにする案も一度実装したが、
  38枚すべてでヒーローが約53px下がりファーストビューを恒久的に消費する
  ため撤回した（平野さん判断、CHAT-0914-WH-02）
- その結果、矢印はスクロールで画面外へ流れる。#213 本文の「常に見える」は
  意図的に捨てた（#213 にコメント済み）
- 画像の明るさは動画ごとに違うため、下地 `rgba(0,0,0,.6)`＋白い矢印。
  フォーカスリングは白の `outline` を黒の `box-shadow` で挟み、どの画像・
  下地の上でも埋もれないようにした
- z-index はヒーロー内の `.mj-video-hero-content`（1）より上の 2。
  navbar（sticky、1020）より下
- 個別ページの `scroll-padding-top` は navbar 高さのみ（#216 のまま）

ヒーロー内のシリーズ名リンク（パンくず相当）は、JSON-LD の
`BreadcrumbList` と対応する可視のパンくずとして残した。

**#215 前後エピソードにサムネイル。** `build_prevnext_link()` に
カードと同じ `medium`（320×180、`img.youtube.com`）を追加。
`loading="lazy"`・width/height 付き、`data-fallback` はカードと同じ
`img/125_arr_hoso.png`（定数 `THUMB_FALLBACK` に集約）。リンク文字列と
同内容のため `alt=""`。表示幅は `clamp(96px, 30%, 160px)`。最新話・最古の
回（リンク1つ）は全幅に伸びるだけでサムネイル幅は変わらない。

### 公開日・サムネイルをYouTube Data API由来に切り替え（#192 第2段、2026-09-13）

**公開日とサムネイルはAPI由来。** `scripts/fetch_youtube_meta.py`が
取得した`data/youtube_meta.json`を`scripts/lib/wayhome.py`の
`load_episodes()`が読み、一覧・エピソード個別ページの両方で使う。
シートの公開日（C列）・画像URL（F列）は生成に使わない（WH-57で取得対象からも外した）。
以下の節にある「C列で最新話判定」「HEADでmaxres確認」はこれで廃止。

- **公開日:** `snippet.publishedAt`（UTC）をJSTに変換して表示・並べ替えに使う。
  JST 0時台公開の回が1本あり、変換を漏らすとその回だけ1日ずれる。
  シートの日付とは38本すべて一致を確認済み。`uploadDate`は実際の公開時刻になった
  （旧: 日付＋00:00 JSTの近似）
- **サムネイルの有無はAPIの`thumbnails`のキーで判定する**（HEADリクエストや
  404には頼らない）。ヒーロー・og:imageは`maxres`→`standard`→`high`、
  カード（表示160×90）は`medium`（320×180）。width/heightはAPIの実寸
- **URLはAPIが返す`i.ytimg.com`ではなく`img.youtube.com`で組み立てる。**
  CSP導入（#9）に向けて依存ドメインを増やさないため（同じ画像が取れることは確認済み）
- **`fhd`（1920×1080）は使わない。** APIは7本に返すが、実体は7本とも404だった
- 読み込み失敗時はインラインonerrorではなく既存の`data-fallback`で差し替える
  （ヒーローは`hqdefault`、カードは従来どおり`img/125_arr_hoso.png`）
- シートにあってJSONに無い動画があると生成を止める。新しい回をシートに足したら
  `fetch_youtube_meta.py`を先に実行すること（週次の自動取得は#194）

### 「帰り道」シートのB・C・F列を取得対象から外した（WH-57、2026-09-13）

- `lib/wayhome.py`の`COLUMNS`からB列（X ID）・C列（公開日）・F列（画像URL）を削除し、
  クエリを`SELECT A,D,E,H WHERE G = "Y"`にした。生成物は1バイトも変わらない
- 理由: いずれも他に正のデータがあり、シートの値は手で転記した写しだった
  （公開日・サムネイルはYouTube API＝`data/youtube_meta.json`、X IDは`jpml_pros`）。
  放置すればシートの値は必ず古くなる。行データから項目を消したので、誤って
  `row.published_date`等を書き戻すとAttributeErrorになり、二重管理が静かに戻らない
- シート側は平野さんが値を消す。**列そのものは残す**
- **【注意】コードは列を見出し名ではなく列記号（A・D・E・H、絞り込みはG）で指定している。**
  シートで列ごと削除すると右側の列記号がずれ、D列（タイトル）やE列（URL）に別の値が入る。
  `to_rows()`の列数チェックは、列数が変わらない形でずれた場合は検出できない。
  **将来列ごと削除する場合は、同時に`COLUMNS`の列記号を直すこと**

### 選手のX・noteアイコン（WH-50/WH-53、2026-09-13）

- **X IDとnote IDはjpml_pros（「プロ」シート）由来。** 「帰り道」シートのB列（X ID）は
  参照しない（WH-57で取得対象からも外した。C列・F列と同じ扱い）。一覧カードの絞り込み対象からも外した
- 「帰り道」シートを正とし、その選手名で「プロ」シートを引く（`generate_jpml_pros.py`と
  同じスプレッドシート・シート・クエリ、`lib/wayhome.py`の`index_player_links()`）。
  名前が1件だけ一致した選手のみ採用する
- **見つからない・同姓同名で複数一致する選手は、アイコンを出さず生成は止めない。**
  代わりに警告を出す（GitHub Actionsでは`::warning::`で実行サマリに出る）。
  打ち間違いも無言で通るため、警告に出た名前を確認すること。
  2026-09-13時点では「タマシュ・エルドス」（連盟所属外）が警告に出るのが正常
- アイコンは個別ページのヒーローの選手名の右にのみ置く（一覧カード・同じ選手の
  他のエピソードのカードには出さない。カード全体が個別ページへのリンクのため、
  中に外部リンクを入れると誤タップを誘発する）。アクション列からX @IDは外した
- note IDが無い選手（27名中17名）はnoteアイコンを出さない（正常系）

### 再生時間の表示（#192 第3段、2026-09-13）

- **閲覧数は掲載しない（平野さん判断）。** 選手への配慮と更新頻度の低減のため。
  `viewCount`は`data/youtube_meta.json`に保存するが表示には使わず、
  「YYYY年M月D日時点」の注記も出さない。復活させる場合は概数表示と
  注記の更新条件（#194の差分判定）を合わせて設計し直すこと
- 再生時間は`contentDetails.duration`（ISO 8601、時・分・秒が欠ける形あり）を
  `duration_seconds()`で秒にし、1時間未満はm:ss、以上はh:mm:ssで表示する
- カード（一覧・同じ選手の他のエピソード）はサムネイル右下に重ねる。
  ヒーローは日付の行に「日付・m:ss」で並べる。数字だけでは伝わらないため
  `<time datetime>`＋visually-hiddenの「再生時間」を付けている
- 一覧の見出し右隣に総再生時間（1分未満切り捨て、「約◯時間◯分」）
- 構造化データは`VideoObject`に`duration`（ISO 8601のまま）を追加した。
  `ItemList`の`ListItem`には`duration`プロパティが無いため付けない

### video_wayhome.html のヒーロー画像追加（#102 第1段、2026-09-12）

表形式一辺倒だった`video_wayhome.html`に、最新話のサムネイルを大きく
見せるヒーローを追加した（第2段の背景動画自動再生は別途判断、本issueは
第1段のみ対象）。実装は`lib/page.py`に新設した`content_before`スロット
（上記参照）を使う。

- **最新話の判定はC列（公開日）が最大の行。** シートの並び順（通常は
  新しい順）に依存しない実装にした。同日が複数ある場合はシート順で
  先に出てくる行を採用する（`max()`のタイブレーク仕様に依存せず、
  明示的にループで比較している）
- **サムネイルはビルド時に`maxresdefault.jpg`へHEADリクエストを送り、
  存在すれば1280×720、なければ`hqdefault.jpg`(480×360)にフォールバックする**
  （`scripts/check_image_links.py`のHEAD処理と同じ方針。標準ライブラリのみ、
  タイムアウト・例外は握りつぶさずログに出す）。現データ(38件)はF列の
  URLがすべて`img.youtube.com`のため、動画IDの抽出も含めこの経路のみで
  完結する。issue本文にあった「外部依存はi.ytimg.com」は誤りで、
  実際は既存表と同じ`img.youtube.com`のみ（#9のCSPは変更不要）
- **`aspect-ratio`だけでは16:9に収まらない落とし穴があった。**
  `.mj-hero-image`に`aspect-ratio: 16/9`のみ指定し`height`を明示しなかった
  ところ、CLS対策で付けている`<img>`の`height`属性（maxres=720、hq=360）が
  aspect-ratioより優先され、幅100%のまま縦長に伸びる不具合が起きた。
  `height: auto`を明示して解消（style.cssにコメントを残してある）。
  スクリーンショットだけでは気づきにくく、Chrome DevTools Protocol経由で
  `getBoundingClientRect()`/`getComputedStyle()`を直接確認して原因を
  特定した。同じ落とし穴を踏まないよう記録しておく
- ヒーローのクラス名は`.mj-hero`系（`.mj-hero` / `.mj-hero-heading` /
  `.mj-hero-link` / `.mj-hero-image` / `.mj-hero-info`）。左端は
  `.mj-table`と同じくマージンなしで揃え、中央寄せ（`margin: 0 auto`）には
  していない。アニメーションは入れず、ホバーは`opacity`の即時変化のみ
  （`prefers-reduced-motion`の分岐が不要になる）
- Lighthouseのローカル計測（`wrangler dev`、本番反映前）では、LCPが表の
  1行目サムネイル(160×90)からヒーローのmaxresdefault(1280×720)に変わり
  0.5〜0.7秒程度悪化したが、accessibility/best-practices/seoは変更前後で
  同点、CLSも変化なし（想定どおりで許容範囲）。詳細は
  `docs/lighthouse-baseline.md`の「video_wayhome.html ヒーロー画像追加」節

**この節の`.mj-hero`系クラス・表ベースの構成は、下記「video_wayhome の
全面リデザイン」（#102第2段、同日）で置き換えられ現存しない。** 最新話
判定・サムネイルHEAD確認の仕組み自体は第2段にそのまま引き継いだ
（その後#192第2段でAPI由来に置き換え、上記）。

### video_wayhome の全面リデザイン（#102 第2段、新サイトのパイロット、2026-09-12）

video_wayhome.html を「新サイト（docs/new-site-design.md）の先取り
パイロット」として、表形式をやめ全画面ヒーロー+横スクロールの
エピソード列に作り変えた。全27ページの中で最も影響が小さいページという
判断。現行サイトの「作り込みすぎない」方針は、このページとこのページ
専用のCSS/JSに限り今回だけ踏み越えている。詳細な判断材料（カラー
トークン、Bootstrap 5.3ダークモードの検証結果、トーンについて実装して
分かったこと、共有ボタンの方針、構造化データの検証結果、新サイトへ
持ち越せる部分/捨てる部分）は**docs/new-site-design.md「12. パイロット:
video_wayhome」に集約した**（このファイルには実装の要点のみ記録する）。

- **`scripts/generate_video_wayhome.py`は`TableConfig`/`render()`を
  やめ、`render_content()`（型D等と同じ）に切り替えた。** `content_before`
  スロット（第1段で追加）はこのページではもう使わない。`#158`が
  引き続き使う想定でlib側はそのまま残している
- **行の並びはPython側で公開日(C列)の降順に明示ソートする**
  （`sorted(raw_rows, key=lambda row: row[2] or "", reverse=True)`）。
  シートの並び順に依存しない。Pythonの`sorted`は安定ソートで
  `reverse=True`でも同値の相対順は保たれるため、同日が複数ある場合は
  シート順で先に出てくる行が結果でも先に来る（第1段の`max()`ループと
  同じ規則を、ここでは安定ソートの性質で満たしている）
- **`table.js`を読まないページでは`data-fallback`の画像フォールバック
  処理も止まる。** `table.js`は`error`イベントのキャプチャフェーズ
  ハンドラで全画像のフォールバックをまとめて処理しているが、この
  ハンドラは`.mj-table`が無いページには効かない（`table.js`自体が
  何もしないため）。`video_wayhome.js`に同じ処理を移植して対応した。
  **表を持たない新しいページを作る際は、`table.js`前提の仕組み
  （画像フォールバック・`?name=`初期値・`--navbar-height`実測）を
  個別に確認し、必要なら移植すること。忘れると気づきにくい形で
  壊れる**（今回はCDP経由で意図的に壊れた画像URLを読み込ませて
  フォールバックが効くことを実地で確認した）
- **【規約】検索欄を持たないページは `<body data-search="off">` を出す（#163）。**
  navbar.js の虫眼鏡アイコンは `#searchBoxes` を開閉するリンクなので、検索欄が
  無いページでは押しても何も起きない。navbar.js は `document.write` で描画され
  その時点でページ本体は未パースのため、DOMから `#searchBoxes` の有無を
  調べられない。そこでページ側が `<body>` の data属性で先に伝える
  （`<body>` は navbar.js の `<script>` より前にパース済みなので描画の瞬間に読める）。
  **属性が無ければ「検索欄あり」＝従来どおり出す、が既定。** 目印を「無い」側に
  だけ付けているのは、手書きHTMLで付け忘れたときに現状維持へ倒すため。
  **表を持たない新しいページを作る際は、`render_content()` に
  `has_search_boxes=False` を渡すかどうかを必ず判断すること**（既定は「あり」）。
  手書きHTMLを追加するときは `<body>` に手で付ける。対象は現在7ページ
  （`404` / `jpml_links` / `resource_dictionary` / `resource_efficiency` /
  `rh_links` / `rh_results` / `rh_results_detail`）
- **`<main class="mj-video-page">`でページ全体を包み、Lighthouse
  accessibilityの`landmark-one-main`指摘を解消した。** `navbar.js`を
  触らずに済む範囲でこのページ限りの改善として反映。残る指摘は
  `navbar.js`の検索アイコンリンクの`link-name`（全ページ共通の既知の
  問題、navbar.jsは触らない方針のため未解決のまま）
- JSON-LD（`VideoObject`+`ItemList`）を`extra_head`経由で出力（#13先行
  実装）。値に`</`を含みうるため`json.dumps()`後に`"</"` → `"<\\/"`へ
  置換している
- Lighthouseのローカル計測は第1段からほぼ横ばい（performance
  0.91〜0.94、LCP 3.0〜3.2s）だが、**accessibilityが0.89→0.94〜0.96に
  改善、CLSが0.005→0.000に改善**（上記landmark修正とページ送り撤廃が
  効いている）。詳細は`docs/lighthouse-baseline.md`
- **【2026-09-12 決定】このページは濃色固定にした。** OSのカラーモード
  設定に関係なく常にダークで表示する（`@media (prefers-color-scheme: dark)`
  を廃し、ダーク側の値を既定に。`color-scheme: dark`を
  `html:has(.mj-video-page)`に指定）。新サイト全体のトーン（静か・白基調）は
  維持し、**動画セクションだけを濃色の例外とする**という整理。トークンの
  構造（機能名の7つ）は両モード前提のまま残してある。決定と理由・確認結果は
  `docs/new-site-design.md`の §2「デザイン方針 > トーン」と
  §12「パイロット: video_wayhome」の両方に記載（片方だけ読んで矛盾しないため）

### #162 エピソード個別ページ38枚（新サイトの選手個別ページのパイロット、2026-09-12）

video_wayhomeパイロット（#102第2段）の延長として、「帰り道」のエピソード
38本を個別ページ（`wayhome/<動画ID>.html`）として静的生成した。新サイトの
選手個別ページ（#101、1,000名超）で必要になるURL設計・canonical・
sitemap分割を、1/30程度の規模で先に検証する狙い。実装は
`scripts/generate_wayhome_episodes.py`（`render_content()`を使用）。
最新話判定・サムネイル解決（maxres→hqのHEAD確認）・`VideoObject`組み立ては
`generate_video_wayhome.py`（一覧）と共有するため`scripts/lib/wayhome.py`に
切り出した。

**最初の発見: 相対パス前提は1,000ページ規模で確実に壊れる。** これまでの
27ページはすべてリポジトリ直下にあり、`style.css`・`navbar.js`・
`img/...`といった相対パスがどのページからも同じ深さで解決できていた。
`wayhome/`のようにサブディレクトリへ1階層でも降りると、この前提は
即座に崩れる。選手個別ページ（#101）は1,000名超をサブディレクトリで
持つ可能性が高く、同じ問題が起きる規模がはるかに大きい。今回38枚の
段階で気づけたことが最大の収穫。対応は以下の2点:

- `lib/page.py`に`asset_prefix`引数を追加した（既定は空文字、既存27ページの
  出力は1バイトも変わらないことを`regenerate.py all`のdiffで確認済み）。
  head内のアセット参照（`style.css`・`assets/vendor/*`・`favicon.ico`・
  `navbar.js`・`table.js`）にこの接頭辞を付ける。`wayhome/`配下は`"../"`
- `navbar.js`の28本のページhrefをルート相対パス（先頭`/`）に変更した。
  相対パスのままだと`wayhome/xxx.html`から見た`jpml_pros.html`は
  `wayhome/jpml_pros.html`という存在しないパスに解決されてしまう
  （`#`・`#searchBoxes`は対象外）

新サイト（Astro等を想定）ではルーティングの機構自体がこの種の問題を
吸収する可能性が高いが、**現行サイトの延長で選手個別ページを作る場合は
この2点を再利用できる**。

**カード・ItemListのリンク先も個別ページへ変更した。** 一覧
（video_wayhome.html）のカードはYouTube直リンクから個別ページへ変更し
（YouTube直リンクはヒーローの「再生」ボタンにのみ残す）、`ItemList`の
`itemListElement.url`も自サイトURLに変更した。#13の本番検証で、
`ItemList`がリッチリザルトの対象に現れなかったのは`url`が外部サイト
（youtube.com）を指していたためと見ており、個別ページ（自サイトURL）が
できたことで初めてカルーセルの候補になりうる（#162コメント参照）。

**canonicalは#113の例外として38ページにだけ付けた。** #113は「現行
サイトにはcanonicalを付けない（Googleの正規化任せ）」と判断したが、
理由は`?name=`付きURLの検索流入14件を正規化で失うことだった。個別
ページは`?name=`等のURL変種を持たないため、この懸念が当てはまらない。
`lib/page.py`の`PageMeta`に`canonical`（既定`None`）・`og_image`/
`og_image_width`/`og_image_height`/`og_image_alt`（既定は全ページ共通の
`img/ogp.png`・1200×630）を追加し、ページごとに差し替えられるようにした。
og:imageは各エピソードのサムネイル（一覧のヒーローと同じmaxres→hq
フォールバック）。

**sitemapはサイトマップインデックス方式にした。** `sitemap.xml`を
インデックスに変え、既存25件は`sitemap-pages.xml`（旧sitemap.xml）へ
そのまま移し、`sitemap-wayhome.xml`（38件）は生成スクリプトが書き出す。
新規URLは当日日付、既存分は`lastmod`を保持し
`scripts/update_sitemap_lastmod.py`が実際に差分の出たページだけ
更新する規則を維持した（ページパスから`wayhome/`配下かどうかで
対象サイトマップを判定するよう拡張）。`robots.txt`のSitemap行は
`sitemap.xml`のまま変更不要。`regenerate.py`は出力がディレクトリに
なるページ向けに`OUTPUT_OVERRIDES`（`wayhome_episodes` → `"wayhome/"`）を
追加し、`regenerate-page.yml`の`git add`は`-A --`で削除も拾えるように
した（シートから消えた動画IDのページを削除するため）。

**thin content・重複コンテンツの懸念は残る。** 各ページの本文は
`VideoObject`の`description`と`.mj-lead`が同一文言で、ページごとの
独自テキストは実質的にタイトル戦名・選手名・公開日のみ。1,000ページ超の
選手個別ページで同じ構成を使う場合、レーダーチャートや成績詳細など
（docs/new-site-design.md「3. 画面構成」）でページ固有の情報量を
増やす設計が要る。今回は検証目的の38ページのみのため対応していない。

**シートに列を追加する運用は次のissueに送った。** 説明文・尺（`duration`）
といった追加列は平野さんが後日シートに追加する前提で、個別issue
「帰り道シートに個別ページ用の列を追加し、ページとVideoObjectに反映する」
を#162に関連付けて起票した（本issueのスコープ外）。

