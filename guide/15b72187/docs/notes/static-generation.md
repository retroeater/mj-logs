# 静的生成（#7）の実装記録

このファイルは完了済み作業の記録。現状とルールは docs/handover.md。

---

### #7（型Aの静的化）で用意した共通部品

`jpml_titles.html` の移行(#7)で、型A(表とフィルターのみ)の残りページで
使い回せる汎用クラスを style.css に用意した: `.mj-table`(表の見た目)、
`.mj-pager`(ページ送りのUI)、`.mj-left`(列ごとの左寄せ)、`.mj-plain`
(リンクの下線を消す。`resource_logs.html`の移行で追加)。
ページ固有の列幅・列固定・行高(`contain-intrinsic-size`)などはIDセレクタ
側に残している。`.mj-sort`(ソート見出し用のbuttonスタイル)は
`jpml_pros.html`専用の機能のため`#pros_table`側に置き、`.mj-table`側には
汎用化していない。`jpml_test.html` / `resource_logs.html` /
`video_live.html` の移行で2〜4ページ目の適用例ができた。
`video_live.js`は`jpml_test.js`とテーブルidが違うだけでほぼ同一で、
型Aの実装が収束してきた最初の例。

**2026-09-11、上記4ページを`scripts/lib/page.py` + `table.js`に共通化し、
続けて5ページ（`video_wayhome` / `video_en` / `rh_paifu` / `saikyo_mens` /
`video_mtsuku`）を移行した。** 残りページを移行する人向けに仕組みを記録する。

**`scripts/lib/page.py`**（Python側の共通処理）

- `PageMeta`: head用の設定(title/description/og_url/h1/caption)
- `TableConfig`: テーブル・検索欄・ページ送りの設定。主な項目:
  - `table_id` / `headers`(リスト。2列とは限らない。`video_mtsuku`は3列)
  - `extra_table_class`(既定`"mj-table-2col"`。3列ページは`""`にして
    代わりに`.mj-table-3col`を`headers`の列数に応じて明示的に指定する)
  - `page_size`(既定100。`None`にするとページ送りなし。`video_mtsuku`が該当)
  - `name_mode`(`"exact"`で`?name=`をdata-nameの完全一致に使う。
    `jpml_titles`/`resource_logs`/`saikyo_mens`が該当)
  - `filter_param`(絞り込み欄の初期値に使うURLパラメータ。`"name"`か`"tag"`)
  - `filter_label` / `filter_placeholder`
  - `search_boxes_before` / `search_boxes_after`(ページ固有UIの差し込み。
    `resource_logs`の名前セレクトボックスで使用)
  - `extra_script`(ページ固有の小さなJSをheadにもう1本追加する)
  - `content_before`(h1直後・`#searchBoxes`手前に差し込むページ固有の
    HTMLブロック。既定は空文字で、空文字のときはテンプレート出力が
    バイト単位で無変化。`video_wayhome`のヒーロー画像で使用。#158の
    lead文もこのスロットの手前にPageMeta側で足す想定、#102)
- `build_image_cell(alt, url, image_url, css_class, width, height, fallback)`:
  画像セル共通処理。`url`が空なら`<a>`で包まず`<img>`のみを返す
  (`saikyo_mens`のXアカウントなし行で使う分岐)
- `generate(spreadsheet_id, sheet_name, query, output_path, meta, table_config,
  build_row_html, formatted=False, build_content_before=None)`:
  取得〜書き出しまでの`main()`相当。`build_content_before(raw_rows) -> str`を
  渡すと、取得済みの全行から`content_before`用HTMLを組み立てて`render()`に渡す
  (`video_wayhome`の最新話ヒーローが該当。取得済み行に依存しない静的な
  `content_before`は`TableConfig`側にそのまま渡せばよく、この引数は不要)
- 各`generate_<ページ名>.py`は「設定(`PageMeta`/`TableConfig`) + 行組み立て
  関数(`build_row_html`)」だけを持てばよい

**`table.js`**（JS側の共通処理。リポジトリ直下に配置）

- `<table>`要素の`data-page-size` / `data-name-mode` / `data-filter-param`
  属性を読んで動く。属性はテーブルに付けるため、table.js自体はページごとの
  設定を一切ハードコードしていない
- `data-page-size`を省略するとページ送りなし(`video_mtsuku`)。`.mj-pager`の
  `<nav>`自体をHTML側で出力しなければ、table.js側は`pagerEl`がnullになり
  何もしない
- 絞り込み対象の列を1列だけに絞りたい場合(`video_mtsuku`の3列目「選手」)は、
  table.js側に新しい属性は不要。`data-info`に検索対象にしたい文字列だけを
  入れれば、他の列の文言は自動的に検索対象から外れる(`resource_logs`が
  非表示の駅名・カテゴリを検索対象に含めているのと逆の応用)
- ページ固有のUIは共通化せず、`window.mjTable.getSearchParam`を最小限の
  フックとして公開している。`resource_logs.js`(26行に縮小)はこれを使って
  名前セレクトボックスの初期値・遷移だけを担当する

**移行時の個別事情**（`docs/lighthouse-baseline.md`の行数調査で判明した内容と合わせて）

- `rh_paifu`: 画像クラス`videos`がstyle.css未定義だったため、
  `img.rectangle`と同じ160×90を追加した。リンクは`videoUrl + '&t=' +
  videoStartTime + 's'`の形式を維持
- `saikyo_mens`: フォールバックを`abs.twimg.com`の既定アイコンから
  `img/avatar.svg`に差し替え、外部ドメイン依存を1つ解消した。Xアカウント
  なし行は`<img>`のみ(`<a>`で包まない)という分岐を維持。`?name=`(完全一致)
  と`?tag=`(絞り込み欄の初期値)を両方持つ、`jpml_titles`と同型の構成
- `video_mtsuku`: 3列(動画/概要/選手)。`.mj-table-2col`ではなく新設した
  `.mj-table-3col`を使う。ページ送りなし。絞り込み対象は3列目(選手)のみで、
  `data-info`には選手名・所属だけを入れ概要列の文言は含めない

**2026-09-11、型A'(多列テキストテーブル、画像列なし)を`rh_results`→
`rh_results_detail`の順に移行し完了した。** 型Bの表部分もこの共通部品を
使う想定。

- `TableConfig.show_filter`(既定`True`)を追加した。`False`にすると
  `#searchBoxes`ごと出力せず(`search_boxes_before`/`after`があればそれだけは
  出す)、`<table>`の`data-filter-param`属性も付けない。`rh_results`が
  最初の適用例
- `.mj-table-auto`(style.css)を新設した。`.mj-table-2col`/`.mj-table-3col`は
  1列目を画像168px固定にする前提のため、画像列を持たないページでは使えない。
  こちらは`width: 100%`のみを指定し、列幅は`table-layout: auto`の自動計算に
  任せる(旧Google Charts版の`options.width: '100%'`と同じ見た目になる)
- `table.js`のテーブル検出セレクタを`.mj-table[data-filter-param]`から
  `.mj-table`に変更した。絞り込み欄を持たないページでも
  `updateOffsets()`(`--navbar-height`/`--content-offset`の設定)は必要で、
  これが走らないとstickyヘッダーとbodyの`padding-top`が既定値90pxのまま
  固定されるため。`jpml_pros.html`は`table.js`を読み込まず`jpml_pros.js`を
  使うため、この変更の影響を受けない
- **`fetch_sheet()`に`formatted: bool = False`を追加した。** gvizの
  レスポンスは生の数値(`v`)とは別に表示用文字列(`f`)を持ち、シートの
  表示形式(`#,##0.0`等)が反映されている。旧Google Charts版の`Table`は
  `f`をそのまま描画していたため、`rh_results`のような小数・桁区切りを
  持つ数値列は`v`だけでは見た目が変わってしまう(`1861.4000000000012`の
  ような生の浮動小数になる)。`formatted=True`にすると、セルに`f`が
  あればそれを優先して使う。**既定は`False`のまま。** 選手IDやYouTube
  動画IDなどURL・HTML属性に埋め込む値では`f`の桁区切り("6,010")が
  リンクを壊すため(`_normalize()`がfloatの"6010.0"を防いでいるのと
  同じ問題)。`generate()`(page.py)も`formatted`引数をそのまま
  `fetch_sheet()`へ渡す(表示の設定ではなくデータ取得の設定のため
  `TableConfig`ではなく`generate()`の引数にした)。`rh_results`が最初の
  適用例。数値列を含む他のページを移行する際は、URL・属性に使う列が
  含まれていないことを確認したうえで`formatted=True`を使う
- **必要な列はQUERY側で最初から絞り込む。** 旧Google Charts版は
  `SELECT A,B,...V`のように全列を取得してから`view.setColumns([...])`で
  表示列を間引くことが多いが、Python移行では22列取得して後から捨てるより
  `SELECT A,C,E,G,I,R,S,T,V WHERE W = "Y"`のように必要な列だけを最初から
  クエリする。gvizのWHERE句はSELECTに含めない列も参照できるため、絞り込み
  専用の列(旧`W`列)をSELECTに含める必要はない(`rh_results_detail`で適用)
- **テキストの後ろにアイコンを添えるセルは`build_image_cell()`を使わない。**
  `build_image_cell()`は画像セル1つを丸ごと作る関数で、`rh_results_detail`の
  対局名+Xアイコンのように「テキスト + 条件付きでアイコン付きリンクを後置」
  という形には合わない。この場合はセルの組み立てをそのまま`build_row_html`
  内に書く。アイコンのalt属性は`f"{name} X"`のように「名前 サービス名」の
  形式にそろえる(`jpml_pros`の`get_x()`等と同じ慣習。旧版は`alt="Twitter"`
  固定だった)
- **セルの折り返しは列を選ばず全体に適用したほうが安全な場合がある。**
  `rh_results_detail`は当初、明らかに長い3列だけ`white-space: normal`に
  していたが、旧Google Charts版のTable chartを実レンダリングして比較した
  ところ、**全列を折り返しており**、短そうに見える列(団体・着順)にも
  実測すると幅を圧迫する例外的に長い値があった。列を選ばず
  `#<table_id> td { white-space: normal; overflow-wrap: anywhere; }`と
  指定するほうが、旧版との差分調査の手間も含めて安全

**2026-09-11、型D(静的SVG、表を持たない)として`resource_efficiency`を
移行した。** グラフ系6ページの中で唯一、URLパラメータに依存せずデータ量も
固定(34行)のため、完全に静的SVG化できた。

- `scripts/lib/page.py`に`render_content(meta, body_html, extra_head="")`を
  追加した。`render()`(表を持つページ用)と違い`table.js`は読み込まない。
  これに伴い`PAGE_TEMPLATE`から`<head>`部分を`HEAD_TEMPLATE`として切り出し、
  `PAGE_TEMPLATE`/`CONTENT_TEMPLATE`の両方がそれを取り込む形にした。
  既存11ページの出力が1バイトも変わらないことを確認済み
- `scripts/lib/chart.py`を新規作成し、横棒グラフのSVG生成
  (`horizontal_bar_chart()`)をまとめた。外部の描画ライブラリ
  (matplotlib等)は使わず、SVG文字列をPython側で直接組み立てる方式
  (scripts/配下は現在すべて標準ライブラリのみで完結している)。
  `#127`(型C)は積み上げ棒+選手の折れ線という別物のため、汎用化を
  狙いすぎず横棒グラフに限定した
- **ツールチップはJS/CSSなしで再現できる。** 各棒を`<g>`で包み内側に
  `<title>`を置くと、ブラウザが標準のホバーツールチップを表示する。
  この事実は#111/#127/#128の3issueすべてにコメントで追記した
  (3issueとも「ツールチップは失われる」を静的化のデメリットとして
  挙げていたが、これは誤りだったため)
- **「幅100%・高さ700pxの両立」はviewBoxのアスペクト比固定だけでは
  実現できなかった。** 幅に応じて高さも比例して変わるため、デスクトップ
  幅(1280px)でほぼ700pxになるよう設計したSVGは、375px幅では高さも
  文字サイズも同じ比率で縮み読めなくなった。CSSで文字サイズだけを
  引き上げる案は、バーの太さ・行間が連動せず文字が行をまたいで重なり
  失敗した。最終的にデスクトップ用・モバイル用で寸法設計を変えた2枚の
  SVGを両方埋め込み、`@media (max-width: 480px)`で表示を切り替える形に
  した。詳細な経緯は`scripts/lib/chart.py`のモジュールdocstring参照

〔2026-10-19: 型C の2ページと生成・`leagues.js` は #582 で消した。この節は移行当時の記録〕

**2026-09-11、型C(積み上げ棒+選手1名の折れ線)として`houou_leagues` /
`ouka_leagues`を移行した。** 方針は(c)静的SVG+折れ線だけクライアント描画の
ハイブリッド（ECharts等の導入は見送り）。着手前の事前調査で
egressポリシーに阻まれ実データを見られない状態が一度あり、そのときの
コード精読の結果（E列の正体・A1/A2補完・鳳凰位の扱い等）をissue #127の
コメントに残してから再開した。実データで検証し直したところ、いくつか
コードの精読だけでは分からなかった論点が見つかった。

- **選手選択リストの出典が不明だった。** 旧HTMLの`<option>`一覧
  （houou 695名・ouka 149名）は、鳳凰・桜花シートの参加経験者
  （1,296名・248名）の単純なサブセットではなく、出典を特定できな
  かった。検証の結果、**「プロ」シートのY列="Y"（公開対象）かつ
  鳳凰最高/桜花最高列に値がある選手**を採用した（houou 716名・
  ouka 178名）。この基準は`jpml_pros.html`が同じ列を使って
  `houou_leagues.html?name=`のリンクを生成しているのと同じ条件で、
  「URLパラメータの棚卸し」の内部リンク件数表（716/178件）と一致する。
  前原雄大・土田浩翔・阿部孝則の3名は「プロ」シートの鳳凰最高列が
  未記入のため候補から漏れるが、**2026-09-12に平野さんへ確認した結果、
  3名はいずれも現時点で連盟の所属プロではないと判明した。** 鳳凰位
  経験の有無にかかわらず非所属プロ（`jpml_pros`に存在しない選手）は
  選択肢に含めない方針を確定し、現在の生成条件は意図どおりに機能して
  いる（データ不備ではなく修正不要、詳細は#127のコメント）。
  〔2026-10-11 追記: 鳳凰最高/桜花最高列は廃止し、今は「鳳凰」「桜花」タブに1行でもある在籍者で判定する（同じ意味、#536）〕
  - 選定した候補選手のうち、鳳凰/桜花シートの実データに1件もヒットしない
    選手（houou 25名・ouka 14名）は生成時にスキップする（選んでも
    折れ線が出ない項目を作らないため）。**この25名/14名は表記ゆれでは
    なく、進行中の期（houou 43後・ouka 21期）が初参加である選手。**
    成績未確定のためこの期を積み上げ棒から除外している結果、実データが
    0件になっている。期が確定すれば自然に解消するが、**新しい期が
    始まるたびに、その期が初参加の選手が一時的にスキップされる現象は
    毎期発生する。** これに伴い、`jpml_pros`が生成するリンク数
    （716本/178本）と`<option>`の実数（691名/164名）が毎期ズレるが、
    **これは意図的なズレとして許容する**（2026-09-12判断、詳細は
    #127のコメント）
- **最新の進行中の期は積み上げ棒からも除外する。** houou 43後・ouka 21期は
  リーグ配属は決まっているが対局はこれからで、順位(F列)が全行空になる。
  「F値が1件もない期は除外する」規則（`lib/leagues.py`の
  `select_periods()`）で旧版と同じ見た目（houou52期・ouka20期）になる
- **y軸の最大値は独自にキリの良い値を設定する。** 旧版の実際の描画を
  実測（選手の折れ線の座標とその値を回帰）したところ、Google Chartsの
  自動スケーリングは単純な「最大値を丸める」ではなく、0にも実データ最大値
  にも揃わない独自のpaddingが乗っていた。忠実な再現は狙わず、その期の
  最大積み上げ合計を基準に自前で丸めた値を使う
- **鳳凰位はvalue=0として明示的に扱う。** 旧JSは鳳凰位を除外しておらず、
  `0(上位人数) + null(順位) = 0`というJSの暗黙変換でたまたま最上部に
  来ていた。桜花側の同様のプレースホルダ行（「桜花」、前期優勝者）は
  `WHERE F > 0`で最初から除外されるため特別扱い不要
- **凡例はページ送りJSをやめてflex-wrapにした。** 旧版はモバイル幅で
  13色/5色の凡例が`◀ 1/4 ▶`のようなページ送りUIになっていたが、
  `lib/chart.py`の`render_legend()`で単純なflex-wrapの凡例に変更し、
  ページ送りJS自体をなくした。色見本は`style`属性ではなく`<svg><rect
  fill="...">`にしている（#9のCSP前提。style属性はインラインスタイルとして
  弾かれうるが、SVGのfill属性はプレゼンテーション属性で対象外）
- `scripts/lib/chart.py`に`stacked_column_chart()`（積み上げ棒+折れ線の
  SVG生成）と`render_legend()`を追加。型D同様、デスクトップ用・モバイル用の
  2枚のSVGを生成し`@media (max-width: 480px)`で切り替える
- `scripts/lib/leagues.py`を新規作成。houou/ouka で異なるのは期の形
  （年+前後 / 期のみ）とzero_leagues（鳳凰位相当）の有無だけなので、
  集計処理（`select_periods` / `count_leagues` / `upper_counts` /
  `build_player_series`）を共通化した
- `leagues.js`（houou_leagues.html / ouka_leagues.html共通）を新規作成。
  `?name=`が無ければ何もしない（焼き込み済みの既定選手のまま）。あれば
  `houou_leagues_data.json` / `ouka_leagues_data.json`（選手ごとの
  折れ線データ、{名前: [[期のindex, value], ...]}）をfetchし、
  `<polyline>`のpoints属性と凡例ラベルを差し替える。SVG側は
  `data-plot-left`等のdata属性でプロット領域の座標・y軸最大値・期数を
  持っており、JSはそこから再計算する
  - JSONは別ファイルに分離した（hououの実データがgzip後36KB程度あり、
    `<script type="application/json">`でHTML本体に埋め込むには大きい
    ため）
  - 旧版の`onchange="javascript:location.href = this.value"`を廃止し、
    `leagues.js`側で`addEventListener('change', ...)`にした（#9の
    インラインハンドラ排除が2ページ分進んだ）
- 旧版の`curveType: 'function'`（スプライン）は再現せず、`<polyline>`の
  直線でつないでいる。実機比較で見た目の差は気にならない範囲だった

---

## 現行の仕組み（CLAUDE.md・docs/handover.md から移管、#336）

この節は完了記録ではなく、現行の仕様。CLAUDE.md「構成」「データの流れ」「メンテナンス用スクリプト」と
handover.md 5章から移した。ページの一覧は下の「ページの一覧」、件数の正は
`python3 scripts/regenerate.py --list`。
**2026-10-19 に鳳凰戦・女流桜花・WRC の旧9ページ（型C の2・順位変動の1・Google Charts 依存の6）を消し、下の「HTMLは24ページ」は15ページになった**（#582。旧 URL は `_redirects` で新しいページへ 301）。

### ページの一覧

HTMLは22ページ + 書籍の一覧と個別ページ（`books/`、#97、noindex・メニュー未掲載。**2026-09-22 開発凍結、`docs/notes/books-freeze.md`**） + 「帰り道」の一覧とエピソード個別ページ（`wayhome/`、#162・#559） + 最強戦のトップと年度ページ（`saikyo/`、#319/#355、2026-09-21 に公開） + 最強戦の新しい年度・対局・卓のページ（`saikyo/<年度>/`、#425、noindex・メニュー未掲載、公開は #549） + タイトル戦の新構成（`title/`、#222、2026-09-28 に公開、#413） + 放送対局ページ（`live/`、#346、noindex・メニュー未掲載。正式公開は #362） + 鳳凰戦の新ページ（`houou/`、#518、公開は #540。`houou/leagues/` だけ noindex） + Mつくの一覧と各話（`mtsuku/`、#569、2026-10-15 に公開、#570） + 女流勉強会の一覧と各話（`study/`、#575、noindex・メニュー未掲載、公開は #576） + 女流桜花の新ページ（`ouka/`、#519、noindex・メニュー未掲載、公開は #591） + WRC リーグの新ページ（`jwrc/`、#594、公開は #599） + WRC-R リーグの新ページ（`wrcr/`、#366、公開は #611） + プロテストの一覧と各話（`exam/`、#612、2026-10-19 に公開、#613）。大きく4系統に分かれる。

| 系統 | ページ数 | 状態 |
|---|---|---|
| `index.html` | 1 | Webサイトテンプレート（iPortfolio）由来。`index.css` と11個のvendorライブラリを使う |
| ビルド時生成（型A・15列） | 1 | `jpml_pros.html`。独自の`generate_jpml_pros.py`のまま |
| ビルド時生成（型A・2列/3列） | 5 | `resource_logs.html` / `video_live.html` / `video_en.html` / `rh_paifu.html` / `saikyo_mens.html`。`scripts/lib/page.py` + 共有JS `table.js` を使う（#7）。3列だった旧 `video_mtsuku.html` は廃止し `/mtsuku/` へ 301（2026-10-15、#570。下の節の `video_mtsuku` の記述は移行当時の記録）。旧表 `jpml_titles.html` は廃止し `/title/` へ 301（#441）。旧 `jpml_test.html` は廃止し `/exam/` へ 301（2026-10-19、#613） |
| ビルド時生成（型A'・多列テキスト） | 2 | `rh_results.html` / `rh_results_detail.html`。画像列を持たないため`.mj-table-auto`を使う（#7、完了） |
| ビルド時生成（型D・静的SVG） | 1 | `resource_efficiency.html`。表を持たないため`render_content()`を使う。外部JS・外部ドメインへの依存が一切ない（#7/#128、完了） |
| ビルド時生成（独自: 全カテゴリをまとめた辞書ファイルを組み立てる） | 1 | `resource_dictionary.html`。`scripts/generate_resource_dictionary.py` が「辞書」タブ（スプレッドシート「R_その他」、`lib/other_book.py`。「サブカテゴリ」列は読まない）と「プロ」タブから、カテゴリごとのデータ `dic/<スラッグ>.json` とページを書く。カテゴリは 一般用語 `mahjong`・連盟用語 `renmei`・連盟プロ `pros`（「プロ」タブから）・Mリーグ `mleague` の順（保存する行の順）。「辞書」タブに行の無いカテゴリは出さない。ページはカテゴリを選ばせず、形式のボタン（Microsoft IME・Google 日本語入力・Gboard）を押すと、`resource_dictionary.js` が全カテゴリを（読み, 語）が同じ行は先に並ぶカテゴリの行を残してまとめ、Microsoft IME 用（UTF-16LE・BOM 付き・CR+LF）・Google 日本語入力用（UTF-8・LF）・Gboard 用（見出し行 `# Gboard Dictionary version:1` と「読み TAB 語 TAB ja-JP」の `dictionary.txt` を無圧縮の zip に入れる）で保存させる。description と末尾の説明文に、同じ数え方の語数（`{count}`）を生成のたびに入れる。見た目は `resource_dictionary.css`（`style.css` には入れない。白いカード1枚に形式のボタンを縦に3つ〈最大 400px で中央〉。保存した後に、押したボタンのすぐ下に「辞書ファイルをダウンロードしました。」とその形式の登録方法を出す〈JS が `#dicDownload` の `data-saved` に形式を入れ、CSS が出し分ける〉、#522）（#377・#515） |
| ビルド時生成（サブディレクトリ、独自: 全画面ヒーロー+横スクロールカード列） | 1 | `wayhome/index.html`（URL は `/wayhome/`。2026-10-13 に `/video_wayhome.html` から移し、旧 URL は `_redirects` で 301、#559。ページ名は `video_wayhome` のまま）。`render_content()`+専用JS`video_wayhome.js`。新サイトのパイロット（`docs/notes/video-wayhome.md`）。個別ページ（下の行）と同じく、動画のタブをまとめたブック（`lib/video_book.py`、#578）の「帰り道」タブを見出しの名前で読み（`lib/wayhome.py` の `fetch_rows()`）、`data/youtube_meta.json` に無い回は外して生成する（#194） |
| ビルド時生成（サブディレクトリ、ヒーロー構成のエピソード個別ページ） | 39 | `wayhome/<動画ID>.html`。`scripts/generate_wayhome_episodes.py`（`docs/notes/video-wayhome.md`） |
| ビルド時生成（サブディレクトリ、Mつくの一覧と各話） | 一覧1＋61（2026-10-15） | `mtsuku/index.html`（URL は `/mtsuku/`）・`mtsuku/<動画ID>.html`。`scripts/generate_mtsuku.py`・`scripts/generate_mtsuku_episodes.py`（帰り道と同じ形、`docs/notes/video-mtsuku.md`）。2026-10-15 に公開（navbar・`sitemap-mtsuku.xml`・`llms.txt`、#570）。旧 `/video_mtsuku.html` は `_redirects` で 301 |
| ビルド時生成（サブディレクトリ、女流勉強会の一覧と各話） | 一覧1＋40（2026-10-15） | `study/index.html`（URL は `/study/`）・`study/<動画ID>.html`。`scripts/generate_study.py`・`scripts/generate_study_episodes.py`（帰り道・Mつくと同じ形、`docs/notes/video-study.md`）。noindex・メニュー未掲載、公開は #576 |
| ビルド時生成（サブディレクトリ、プロテストの一覧と各話） | 一覧1＋12（2026-10-19） | `exam/index.html`（URL は `/exam/`）・`exam/<動画ID>.html`。`scripts/generate_exam.py`・`scripts/generate_exam_episodes.py`（女流勉強会と同じ形、`docs/notes/video-exam.md`）。旧 `jpml_test.html` の YouTube の動画だけを移した。2026-10-19 に公開（navbar の「番組」・`sitemap-exam.xml`・`llms.txt`、#613）。旧 `/jpml_test.html` は `_redirects` で `/exam/` へ 301 |
| ビルド時生成（サブディレクトリ、最強戦のトップ） | 1 | `saikyo/index.html`。`scripts/generate_saikyo_pages.py`。2026-09-21 に一般公開し、navbar・`sitemap-saikyo.xml`・`llms.txt` に載る（#319・#348、`docs/notes/saikyo-page-design.md`）。旧年度ページ `saikyo/<年度>.html` は #549 で下の行へ移し、`_redirects` で 301 |
| ビルド時生成（サブディレクトリ、最強戦の年度・対局・卓のページ） | 年度数＋対局数＋卓数（878、2026-10-11） | `saikyo/<年度>/index.html`・`saikyo/<年度>/<対局識別子>.html`・`saikyo/<年度>/<卓のアンカー>.html`。`scripts/generate_saikyo_pages.py`（トップと同じスクリプト）。#549 で公開（`sitemap-saikyo.xml`・`llms.txt`。年度ページの URL は `/saikyo/<年度>/`、`_redirects` で `index.html` を返す）（#425、`docs/notes/saikyo-page-design.md`「新しい年度・対局・卓のページ」） |
| ビルド時生成（サブディレクトリ、タイトル戦の新構成） | 入口1＋大会数＋期数 | `title/index.html`・`title/<slug>/index.html`・`title/<slug>/<期>.html`（入口は年の切り替え〈`?year=`、#277〉を持つ）。`scripts/generate_title_pages.py`。navbar の「連盟 > タイトル」から入口へ（#413、`docs/notes/title-pages.md`） |
| ビルド時生成（サブディレクトリ、鳳凰戦の新ページ） | 13＋在籍者数（691、2026-10-11） | `houou/index.html`（トップ）・`houou/players/`（個人成績。リーグ推移はそのタブ）・ランキング9部門の `houou/ranking/<スラッグ>/`（通算は `total/`。`houou/ranking/` は `total/` へ 301）・`houou/leagues/`（個人成績のリーグのタブへ移すだけ、2026-10-12）・`houou/race/` の各 `index.html`。データは `houou/search.json`・`houou/players/leagues.json`（期ごとの各段の人数）・`houou/results/<選手名>.json`（在籍者だけ）・`houou/race/<期>-<1|2>.json`。`scripts/generate_houou_pages.py`（集計は `lib/ranking.py`・`lib/results.py`、順位変動は既存の生成スクリプトの部品）＋`assets/league.js`・`assets/league_race.js`（順位変動の写し。ouka/ と共有、2026-10-17 に改名）。公開は #540（`houou/leagues/` だけ noindex・サイトマップ外。旧4ページ〈`houou_*.html`〉は `houou/` へ 301。ファイルと生成は #582 で消した。#518・#520、`docs/notes/houou-top.md`） |
| ビルド時生成（サブディレクトリ、女流桜花の新ページ） | 13＋在籍者数（178、進行中の期だけの選手を含む、2026-10-18） | `ouka/index.html`（トップ）・`ouka/players/`・ランキング8部門の `ouka/ranking/<スラッグ>/`（`ouka/ranking/` は `total/` へ 301）・`ouka/leagues/`（リーグのタブへ移すだけ）・`ouka/race/`。データは houou/ と同じ並び。`scripts/generate_ouka_pages.py`（`generate_houou_pages.py` を設定 `OUKA` で動かす入口）＋`assets/league.js`・`assets/league_race.js`（houou/ と共有）。公開は #591（houou/ と同じく `ouka/leagues/` だけ noindex・サイトマップ外。旧3ページ〈`ouka_*.html`〉は `ouka/` へ 301。ファイルと生成は #582 で消した。#519・#516、`docs/notes/ouka-top.md`） |
| ビルド時生成（サブディレクトリ、WRC リーグの新ページ） | 13＋在籍者数（320、2026-10-18） | `jwrc/index.html`（トップ）・`jwrc/players/`（「リーグ」のタブが無く、期別・通算・部門別の3つ。名前の面に最高到達〈「タイトル」タブの決勝〉）・ランキング9部門の `jwrc/ranking/<スラッグ>/`（`jwrc/ranking/` は `total/` へ 301）・`jwrc/race/`。データは houou/ と同じ並び（`players/leagues.json` は無い）。`scripts/generate_jwrc_pages.py`（`generate_houou_pages.py` を設定 `JWRC` で動かす入口）＋`assets/league.js`・`assets/league_race.js`（houou/・ouka/ と共有）。公開は #599（noindex なし。旧2ページ〈`wrc_ranking.html`・`wrc_results.html`〉は `jwrc/` へ 301。ファイルと生成は #582 で消した。#594、`docs/notes/jwrc-top.md`） |
| ビルド時生成（サブディレクトリ、WRC-R リーグの新ページ） | 13＋在籍者数（184、2026-10-19） | `wrcr/index.html`（トップ）・`wrcr/players/`・ランキング9部門の `wrcr/ranking/<スラッグ>/`（`wrcr/ranking/` は `total/` へ 301）・`wrcr/race/`。jwrc/ と同じ並び。`scripts/generate_wrcr_pages.py`（`generate_houou_pages.py` を設定 `WRCR` で動かす入口）。公開は #611（noindex なし。旧ページは無い。#366、`docs/notes/wrcr-top.md`） |
| ビルド時生成（サブディレクトリ、放送対局ページ） | 691（2026-09-18） | `live/index.html`・`live/<タイトル戦>/index.html` 以下。`scripts/generate_live_pages.py`。noindex・メニュー未掲載、正式公開は #362（`docs/notes/live-page-design.md`） |
| ビルド時生成（サブディレクトリ、書籍の一覧と個別ページ） | 一覧1＋190 | `books/index.html`・`books/<ISBN13>.html`。`scripts/generate_books_pages.py` が「書籍」シート（「辞書」と同じ「R_その他」、`lib/other_book.py`）から書く。noindex・メニュー未掲載、`sitemap-books.xml` は `sitemap.xml` から未参照。**2026-09-22 開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。詳細は `docs/notes/books-freeze.md`** |
| 静的なページ | 1 | `404.html` |

ページを書かない生成対象が4つある: `ask_data`（`scripts/generate_ask_data.py`）と `ask_data_ouka`（`scripts/generate_ask_data_ouka.py`、女流桜花の分、#519）・`ask_data_jwrc`（`scripts/generate_ask_data_jwrc.py`、WRC リーグの分、#594）・`ask_data_wrcr`（`scripts/generate_ask_data_wrcr.py`、WRC-R リーグの分、#366）は Worker `mj-ask` が同梱する検索データ `workers/ask/data.json`・`data-ouka.json`・`data-jwrc.json`・`data-wrcr.json` だけを書く（配信しない。#542、`docs/notes/ask-worker.md`）。

### 生成スクリプトの構成（lib/page.py）

- `jpml_pros.html`は独自の`scripts/generate_jpml_pros.py`のまま。型A/A'の9ページは`scripts/lib/page.py`（HTMLテンプレート・行組み立て・画像セル・エスケープの共通処理）を使い、各`scripts/generate_<ページ名>.py`は「設定(`PageMeta`/`TableConfig`) + 行組み立て関数」だけを持つ（#7の共通化）。型C・型D・`video_wayhome.html`・`wayhome/`のエピソード個別ページは表を持たないため`lib/page.py`の`render_content()`を使う。いずれも`scripts/lib/sheets.py`経由でスプレッドシートのgvizエンドポイント（`google.visualization.Query`と同じSELECT構文）を叩く
- `lib/page.py`はサブディレクトリのページ（`wayhome/`配下）向けに`asset_prefix`引数を持つ（既定は空文字、#162）。head内のアセット参照（`style.css`・`assets/vendor/*`・`favicon.ico`・`navbar.js`・`table.js`）にこの接頭辞を付ける。`wayhome/`配下のページは`"../"`を渡す。あわせて`PageMeta`に`og_image`/`og_image_width`/`og_image_height`/`og_image_alt`/`canonical`を持たせ、ページごとに差し替えられるようにした（既定はそれぞれ`img/ogp/site/index-white.png`（#581）・1200×630・`"ryoei.pro"`・`None`=canonicalなし。#113の判断どおり）。サブディレクトリを増やす場合はこの仕組みを再利用できる
- **ページ別のOGP画像は`img/ogp/<区分>/`以下に置く（#339）。** 全ページ共通の画像は`PageMeta.og_image`の既定`img/ogp/site/index-white.png`（白地・黒字、#581。旧`img/ogp.png`は凍結中の books/ が使うため残す）。「帰り道」は一覧ページの`img/ogp/wayhome/index-<最新話の公開日>.jpg`1枚だけで、`scripts/build_wayhome_ogp.py`が書き出す。名前は`lib/wayhome.py`の`list_og_image_name()`で決め（immutable配信のため中身が変わるときは名前を変える、`docs/notes/ogp.md`）、参照は`resolve_list_og_image()`を通す。今の最新話の画像が未生成なら共通の画像に戻るためページ生成は止まらない。**個別39ページの`og:image`はYouTubeのサムネイル（`resolve_hero_thumb()`、幅・高さはAPIの実寸）をそのまま使う。** 一度はページ別に焼いた画像を使ったが、サムネイルにシリーズ名・大会名・選手名が焼き込まれており、Xのカードでは`og:title`も画像に重なるため二重になる。2026-09-16に取りやめた。最強戦のトップは`img/ogp/saikyo/index-white.png`（`og_image_for()`、卓・カードの代わりの画像も同じ、#343・#581）。title/ は全ページ`img/ogp/title/index-white.png`（`OG_IMAGE`、#232・#581）、houou/ と旧`houou_*.html`は`img/ogp/houou/index-white.png`。4枚とも共通画像と同じ白地・黒字（#212529・#ffffff）で、作り方は`docs/notes/ogp.md`「ページの種類ごとの og:image・og:type の規則」（2026-10-16 に最強戦の橙色・黒地〈CHAT-0916-SY-07〉と大会ごとの画像をやめた、#581）
- `PageMeta`は`og_title`（既定`None`＝`<title>`と同じ）を持つ。SNSのカード見出しだけを短くしたいページで指定する。「帰り道」の個別ページは`<title>`にシリーズの正式名を残したまま、og:titleを「<大会名> <選手名> | 帰り道 | ryoei.pro」にしている（#339。短縮形は`lib/wayhome.py`の`SERIES_SHORT`）
- `wayhome/`のエピソード個別ページは`?name=`等のURL変種を持たないため、#113（canonicalなしの判断）の理由が当てはまらない例外として`<link rel="canonical">`を持つ（39ページ）。他27ページはcanonical無しのまま
- `lib/page.py`はh1直後・`#searchBoxes`手前にページ固有のHTMLを差し込む`content_before`スロット（#102第1段で追加）を持つが、現在どのページも使っていない。ページ固有HTMLをh1直後に差し込む汎用スロットとして残している
- `jpml_pros`の英字の姓名・所属は「プロ」シート（C・D・E列）ではなく連盟員名簿のブック（`lib/meibo.py`。英字の姓名は「【2】値貼付」タブの「登録名英字姓」「登録名英字名」、所属は「公開」タブ）から登録名で引く。在籍者が名簿のどちらかのタブにいない・名簿の人数が1,000〜1,300を外れるときは生成を止める（#536 の段2。「プロ」の C・D・E 列は段4で消すまで残る）
- `jpml_pros`の鳳凰戦の列は「鳳凰」の1列（2026-10-17、#584。前の「鳳凰出場」「鳳凰<最新の期>」「鳳凰最高」の3列を置き換えた）。値は個人成績の名前の面のチップと同じ現在のリーグ（`generate_houou_pages.current_league_table()` が `current_league()` を当てる。進行中の期だけに出ている選手も個人成績に載せるので同じく値が付く、2026-10-17。個人成績のページの無い選手は空欄）で、行き先は個人成績の期別（`HOUOU_PLAYERS_URL`。鳳凰戦の Ampai へは期別の進行中の期の行から行く）。検索欄は placeholder「鳳凰」・label「鳳凰戦の現在のリーグで検索」。`houou/ask-players.json` との一致は `test_jpml_pros.py` の `GeneratedHououColumnTest` で確かめる
- `jpml_pros`の成績の列の元の8項目（鳳凰出場・鳳凰<最新の期>・鳳凰最高・桜花出場・桜花<最新の期>・桜花最高・最強出場・放送対局。鳳凰の3項目は表に出していない）は「プロ」シートの O〜U・W 列（数式）ではなく、`lib/pro_stats.py` が集計元のタブ（「鳳凰」「桜花」「リーグ」「最強戦」、旧「対局」）から生成時に数える。定義は数式と同じ（鳳凰出場は表示 Y の行数、桜花出場は表示を問わない行数、最高は全行のリーグキーの最小を「R_タイトル戦_マスタ」の「リーグ」タブでリーグ名に戻す、最強出場は表示 Y の対局の行数、放送対局は旧「対局」の対局者・実況・解説で登録名を部分一致で含むセルの数。#536 の段3）。最新の期は「鳳凰」の最大の (期, 前後)・「桜花」の最大の期で、見出し・検索欄の placeholder・aria の文言も生成時の期から作る。最新の期のリーグの Ampai の URL は「ryoei.pro_Ampai」ブック（`lib/ampai.py`。1行＝タイトル戦・期・前後・リーグ・組、「-」は空）から、選手の最新の期のタイトル戦・期・前後・リーグ・組（組は「鳳凰」の「組」の列、`lib/houou_tab.py`。どちらも 1・2・3…の数字で、「-」・空欄は組なし。それ以外は止める。Ampai の「組」は gviz が数の列とみなして数でない値を空で返すため、CSV エクスポートでも確かめる）で引く。合う行が無ければリンクを付けない（鳳凰位・桜花も付かない）。最新の期の行がブックに1行も無いときは生成のログに警告を出し、`check-meibo.yml` の週次の実行（`scripts/check_ampai.py`）が issue「Ampai の URL が無い期」に書く（#548。「プロ」の鳳凰Ampai・桜花Ampai 列と「鳳凰Ampai」「桜花Ampai」タブは読まない）。「リーグ」タブ（スプレッドシート「R_タイトル戦_マスタ」、`live.SPREADSHEET_ID`。2026-10-13 に正本から移した）は1行が タイトル戦・リーグ名・リーグID で、見出しで読む（`league_maps()`）。`generate_ouka_leagues.py` の選手候補も「桜花」タブに1行でもある在籍者で判定する（旧「桜花最高」が空でない人と同じ）
- `jpml_pros`ほかの X・note・YouTube の ID と画像の URL は「プロ」「連盟プロ以外」の列ではなく SNS ブックから `lib/sns.py` で引く（2026-10-11、#514、`docs/notes/sns-book.md`。旧列は数日後に空にする）。
- `jpml_pros`のYouTubeアイコンだけはシートではなくYouTube Data API（channels.list）から取り、`data/youtube_channels.json`を経由する（#3。キーはActions secret `YOUTUBE_API_KEY`）。
  取得は週次`all`と`target_page`空/`all`の手動実行時のみ。失敗しても既存JSONでアイコンは維持され、ジョブだけ失敗扱いになる
- `wayhome_episodes`だけは出力が単一ページではなく`wayhome/`配下39枚になる（#162）
- `saikyo_pages`（`scripts/generate_saikyo_pages.py`）も出力が単一ページではなく、`saikyo/`配下のトップ・年度・対局・卓のページと、`sitemap-saikyo.xml`（全部）になる（#319・#549、`regenerate.py`の`OUTPUT_OVERRIDES`は`"saikyo/"`）。表を持たず`render_content()`と`assets/saikyo.js`を使う。設計は`docs/notes/saikyo-page-design.md`
- `title_pages`（`scripts/generate_title_pages.py`）は`title/`配下の入口・大会ページ・期ページと、`sitemap-title.xml`を書き出す（#222、`OUTPUT_OVERRIDES`は`"title/"`）。`sitemap-title.xml`は公開（#413、2026-09-28）から`sitemap.xml`が参照する。仕組みは`docs/notes/title-pages.md`
- **複数のスクリプトが使う定数・関数（シートのクエリ・列の定義など）を変えるときは、その名前を import・参照している所を `git grep` ですべて洗い出し、マージの前に `python3 scripts/regenerate.py all` で全ページを再生成して差分を確かめる。** ほかのスクリプトが借りて列の位置で読んでいると、そちらのページが黙ってずれる

### シートのフィルタの検知（#432）

**gviz はフィルタで隠れた行を返さない。** `status` は `ok` のままで、応答（`version` / `reqId` / `status` / `sig` /
`table.cols` / `table.rows` / `table.parsedNumHeaders`）に隠れた行があることを示すものは無い。
気付かずに再生成すると、隠れた行の選手・対局がページから静かに消える
（2026-09-21、「連盟プロ以外」が732行のうち460行しか読めず、最強戦の選手154名が未登録に見えた。CHAT-0921-MT-04）。

**CSVエクスポートはフィルタの影響を受けない。** これを使って `lib/sheets.py` の `check_not_filtered()` が
タブごとに1回だけ行数を比べ、食い違えば `FilteredSheetError`（`ValueError` の派生）で生成を止める。
`fetch_sheet()`・`fetch_records()` の入口で呼ぶので、シートを読むスクリプトはすべてこの検査を通る。

- **数え方をそろえる**: gviz は `SELECT COUNT(A)`、CSV は「見出しを除き、A列が空でない行」。
  2026-09-21 に生成・検知が読む23タブすべてで一致を確認した（`SELECT *` の行数とは、A列が空の行があるタブでずれる。
  例: 「タイトル」は `SELECT *` 3,271行・`COUNT(A)` 3,122行）
- **gid が要る**: CSVエクスポート（`export?format=csv&gid=<GID>`）はタブを gid で指定する。`&sheet=<タブ名>` は効かず、
  別のタブが返る。gid は `htmlview` の `items.push({name: "...", pageUrl: "...gid=..."})` から読む（`_sheet_gids()`、
  スプレッドシートごとに1回）
- **gid が引けない・CSVを取れないときは、警告を出して通す。** 検査は事故を拾う追加の網で、
  外部の一時的な不調で今まで通っていた生成を止めないため。フィルタは人の操作で起きるまれな事故で、
  見逃しても次の実行で拾える
- **通知は生成の失敗そのもの**（常設issueは作らない）。見出しの照合・置換文字の検査と同じ「生成を止める条件」の扱いで、
  止まったページだけが飛ばされ（ほかのページは生成・コミットされる）、`regenerate-page.yml` が失敗して GitHub から通知が届く
  （飛ばしたページと理由はジョブのサマリ。`docs/notes/github-actions.md`「regenerate-page.yml」、#533）。フィルタは解除すれば直る一時的な状態なので、
  issue を残す意味が薄い
- **増える時間**: タブごとに CSV 1回＋`COUNT(A)` 1回、スプレッドシートごとに `htmlview` 1回。
  2026-09-21 の実測（Codespace から、1回ずつ）:
- **フィルタ以外で隠れた行も、gviz は返さない（2026-10-09 実測、#510）。** 手で非表示にした行も、折りたたんだグループの行も、
  フィルタと同じく gviz の `SELECT *`・`SELECT COUNT(A)` から消え、CSV には全行が出る。そのため `check_not_filtered()` が
  行数の食い違いで `FilteredSheetError` を出して生成を止める（メッセージは「フィルタ、または非表示・折りたたみの行があります」で、
  この3つのどれでも同じ。#510 の実測の後に文面を直した）。新しい検知は要らない。解くには、行の再表示（グループの展開）かフィルタの解除
  （平野さんが用意したテスト用タブでの実測。表は #510 のコメント）

  | 生成 | 検査あり | 検査なし |
  | --- | --- | --- |
  | `generate_jpml_pros.py` | 2.3 / 2.4秒 | 0.7秒 |
  | `generate_saikyo_pages.py` | 9.1 / 9.2秒 | 3.5秒 |
  | `generate_live_pages.py` | 17.5 / 16.6秒 | 9.3秒 |
  | `generate_title_pages.py` | 18.9 / 19.2秒 | 14.6秒 |

  キャッシュはプロセス内だけなので、`regenerate.py all` のようにスクリプトごとにプロセスが分かれる実行では、
  同じタブでも読み直す

### 生成を止める条件の設計（#222 の実例）

スプレッドシートを読む生成スクリプトは、入力の食い違いに気付かないまま壊れたページを書き出さないよう、次を「生成を止める条件」にする。
止まるのはそのページだけで、`regenerate.py` が途中の出力を戻して飛ばし、ほかのページは続ける（`docs/notes/github-actions.md`「regenerate-page.yml」、#533）。
`generate_title_pages.py` はこの形で、title/ の第1段（CHAT-0916-TT-07〜TT-16）で止まったのはすべて実際の食い違いだった。新しい生成スクリプトでも同じ形にする。

- 読むタブの見出しの照合。gviz は存在しないタブ名でも先頭のタブを黙って返すため、これが無いと誤ったタブを読んだまま生成が通る
- タブのフィルタ。gviz の `SELECT COUNT(A)` と CSV エクスポートの A列が空でない行数を比べ、食い違えば止める
  （上の「シートのフィルタの検知」。`lib/sheets.py` が共通の入口で行うので、生成スクリプト側の実装は要らない。
  CSV を取れないときは警告を出して通す）
- 置換文字（U+FFFD）・制御文字。取り込みでの文字化けをページに出る前に捕まえる
- マスタのタブとの不整合。キー（大会名など）の欠け、組み立てた値と記録されている値の食い違い、出力の件数と入力の行数の一致検査

**シート由来の止まる理由・警告には、動画ID（か行番号）と列名を含める**（名前だけでは直す側がセルを探し、直しが漏れる。Mつくで1セル漏れた）。

**一般の方の名前（女流勉強会の生徒など）は、生成のログ・警告・止まる理由に出さない。** Actions のログは残るため。Actions（環境変数 `CI`）では件数だけにし、名前の一覧は手元で生成するときだけ出す（`lib/study.py` の `student_report()`、2026-10-16）。公開しているプロの名前は今のまま出してよい

**マスタのタブは、チャットの指示と並行して平野さんが変えることがある。** 読み込んだ現在の値（行数、表示・区分・状態のように
表示対象が変わる列）を生成の出力に出し、指示文の前提と違えば手を入れずに報告する（CLAUDE.md「Chat-Ref」）。
title/ では「タイトル戦」タブの大会の改名が「タイトル」タブと合わずに生成が止まり（CHAT-0916-TT-14、TT-15 で解消）、
区分の変更・大会の追加でも表示対象と検査対象が変わった（CHAT-0916-TT-11・TT-15）。

- **読んでいる最中に平野さんがタブを貼り替えていることがある。** 貼り替えの途中を読むと、行数が急に減った状態が返る
  （CHAT-0916-LV-17 で「放送対局」3,115行が 1,254行に見えて中断。LV-23 も編集の途中の可能性で中断）。
  シートを読む作業では、行数が想定と違う・読み直すたびに変わるときに止める。大量の貼り替えを伴う作業では、平野さんが貼り終えたと伝えてから読む
- **タブの有無は gviz では判定できない。** 存在しないタブ名にも先頭のタブを黙って返す（CHAT-0916-LV-11 で判明）。
  タブ名の一覧はスプレッドシートの `htmlview` で確かめ、生成では見出しの照合を必ず行う。`lib/sheets.py` の `fetch_records()`・`fetch_table()` は、
  タブの一覧（`htmlview`、`check_sheet_exists()`）に無いタブ名なら止める。先頭のタブの名前には頼らないので、先頭のタブを消す・並べ替えても止まらない。
  一覧が読めないときだけ、先頭のタブと同じ中身が返ったら止める（2026-10-13）
- **列は見出しの名前で読む。** タブ名・列名・列順はシート側で変わる（LV-11「プロ以外」→「連盟プロ以外」、
  LV-29「全動画」→「連盟ch」・列「候補」→「放送対局」・列の並べ替え）。`fetch_records()` のように、生成に使う列の見出しが
  欠けている・重複しているときだけ止め、知らない列が増えても止まらない形にする（列記号の `SELECT A,I,J` は並べ替えで無言に別の列を読む）
- **「プロ」シートは `lib/pro_sheet.py` の `fetch_pros()` で読む**（#536）。ブックは「ryoei.pro_選手」（`pro_sheet.SPREADSHEET_ID`。「連盟プロ以外」「別名」も同じブックで、`lib/live.py` の `PLAYERS_SPREADSHEET_ID`・`spreadsheet_of()`、#567）。読む列は見出しの定数（`pro_sheet.NAME`・`SORT_KEY` など）の組で渡し、
  在籍は見出し「表示」が Y の行（旧 `WHERE Y = "Y"`）、並びは `sort=True` で「ソートキー」の順（旧 `ORDER BY B`）。
  「表示」が N の人（名簿にいて本番に出す時機を待つ人）は、本人のページ（jpml_pros・houou/ の在籍者・辞書・誕生日・SNS ブック）には出さず、
  ほかの人のページ（/live・title/・saikyo/・順位変動・帰り道・書籍）と検査（名簿・最強戦の未登録・帰り道）・道場部ゲストの照合では
  登録済みとして名前だけで扱う（写真・X は出さない）。後者は `fetch_pros(..., hidden=True)`（`lib/sns.py` の同名の関数は、その人の SNS の値を空にする）で読む（2026-10-13）。見出しはセル内改行を除いて比べ
  （「鳳凰\nAmpai」は `鳳凰Ampai`）、A列の「登録名\n0.74」は改行の前の「登録名」で引く（#467）。必要な見出しが無い・2つ以上の列に当たるときは止める。
  SNS の見出し（`X_ID` など `pro_sheet.SNS_HEADERS`）を渡すと止める（列は 2026-10-12 に廃止。値は `lib/sns.fetch_pros()` が SNS ブックから入れる、#514）。
  列記号で「プロ」を読む箇所は無い（`scripts/tests/test_pro_sheet.py` が `WHERE Y = "Y"`・`PRO_QUERY` などの再登場を検査する）。
  **廃止する11列（C・D・E・O〜U・W、`pro_sheet.RETIRED`）はどこも読まない**: 英字の姓名・所属・読みは名簿（`lib/meibo.py`。`check_pros()` が在籍者の欠けと人数を検査して止める）、
  成績の8列は `lib/pro_stats.py`、鳳凰戦の選手候補は `lib/houou_leagues.py` の `load_candidates()`（「鳳凰」タブに1行でもある在籍者）
- **在籍者（「プロ」シート）が変わったとき（退会・入会・「表示」の切り替え）に作り直す生成物**（2026-10-17、HOU-67。HOU-64 で退会者1名を除いたとき、指示の対象の外の生成物が残った）。
  ページ名は `regenerate.py` の対象名。読み元は `pro_sheet.fetch_pros()`（`lib/sns.py` の同名を含む）と `lib/meibo.py`。洗い出しは `grep -l "fetch_pros\|meibo\.fetch" scripts/generate_*.py scripts/lib/*.py`
  - 在籍者だけを出す（行・人数・順位が変わる）: `houou_pages`（`houou/search.json`・`houou/ask-players.json`・`houou/results/`・ランキング・説明文の人数）、`jpml_pros`（行と人数）、
    `ask_data`（`workers/ask/data.json` の `people`・ランキング）、`resource_dictionary`（`dic/pros.json`）。
    `scripts/sync_birthday_calendar.py`（誕生日のカレンダー、ページではない）も在籍者を読む
  - 登録済みの名前として読む（`hidden=True`。「プロ」から行が消えると写真・X が外れ、/live などでは未登録の名前になる。過去の記録の名前は残る）:
    `houou_pages`・`ouka_pages`・`jwrc_pages` の順位変動（部品は `lib/houou_race.py`）、`title_pages`、`live_pages`・`saikyo_pages`・`books_pages`（`lib/name_book.py`）、`wayhome_episodes`・`video_wayhome`
  - 週次の all（`fetch-youtube.yml` が月曜に呼ぶ）で全部作り直されるので、急ぐときだけ `regenerate-page.yml` を対象名で手動実行する
- **作業の途中で平野さんがシートを直すことがある。** 最強戦の作業（CHAT-0918-SX）では、SX-08 の「連盟プロ以外」の見出しの改名
  （「備考」→「所属補足」）、SX-09 の「放送対局」の動画の移動があった。見出しの改名は、`lib/live.py` の `OTHER_HEADERS` と
  `generate_title_pages.py` の `EXPECTED_HEADERS` の照合で /live・/title・最強戦（作業ブランチの実装）の生成が止まり、壊れたページを書き出す前に拾えた
  （SX-08 で判明、/live・/title は SX-09、最強戦は SX-10 で見出しを合わせた）。
  平野さんはシートを直したらチャットに伝える運用にした（2026-09-19）

#### 平野さんがシートを用意した・直したと言ったときのチャット側の指示

`docs/notes/chat-side-operations.md`「書く前に実物で確かめる」から移した（2026-10-13）。

- 平野さんがシート（タブ）を用意した・直したと言ったとき: 実装の指示の前に、生成と同じ経路で読ませ、見出し・件数・既存の公開物との差を報告させる（差は止まる条件にせず、どちらを正とするかを平野さんに聞いてから書く）。生成で止まるかどうかだけなら、先に `python3 scripts/regenerate.py --check <ページ>` を流させる（ファイルを書かず止まる理由と警告を全部出す。static-generation.md）。
  消す・直すを頼むときは済んだ後の状態（ブックごとのタブの数など）を書き、平野さんに数を返してもらってから次の指示を作る（申告だけで作ると、残りがあって止まる）
- タブ・列を変えたと言われたら、そのタブを読む処理（凍結中のページ・検査を含む）を `git grep` で洗わせる手順を次の指示に入れる（読む処理が残っていると本番の再生成で失敗する。2026-10-15 に旧 Mつくの生成が失敗した）

### 新しいページを作るとき（/live の実例、CHAT-0916-LV-33）

- **作る段の確かめ・未公開で本番に入れる手順・公開の手順は `docs/new-page-checklist.md`（#243）。** 公開は作る issue と別の issue で行う
- **件数・容量・配信ファイル数を先に見積もる。** /live は、1回戦1ページなら約3,300ページという見積もり（CHAT-0916-LV-03）から、
  個別ページの単位を「同一ステージの同一卓」に変えた（LV-04、約960ページ）。Workers の静的アセットの上限（Free で 20,000 ファイル）、
  `_redirects` の上限（静的 2,000行）、埋め込むページの大きさも同時に見る
- **検査で外れる行の一覧を平野さんに返す流れを、最初から組む。** データを構造化すると、表のページでは表に出なかった誤りが出る。
  /live では動画IDの重複、卓の取り違え、対局者の欠落（他団体の選手を抜いていた時期がある）、概要欄の誤記が多数見つかった。
  外れた行は公開せずに警告し（ジョブのサマリにも出す）、行・理由・今の値・直し方の案を表にして返す（例: https://github.com/retroeater/mj/blob/4be965e22e7e64c4ba0db6400a943ffcd17a99f2/docs/logs/CHAT-0916-LV-26.md 「2.」）

### サイトマップ

`sitemap.xml`（インデックス）が`sitemap-pages.xml`（24ページ、旧sitemap.xml。26ページのうち`404.html`〈noindex〉と`saikyo_mens.html`〈年1回の単発企画〉を意図的に除外）と`sitemap-wayhome.xml`（wayhome/39ページ、`generate_wayhome_episodes.py`が生成）と`sitemap-saikyo.xml`（saikyo/配下、`generate_saikyo_pages.py`が生成、#319）と`sitemap-title.xml`（title/配下、`generate_title_pages.py`が生成、#413）を束ねる方式（#162）。`robots.txt`のSitemap行は`sitemap.xml`のまま変更していない。lastmodは生成・非生成を区別せずgitの最終コミット日（JST）で、`scripts/update_sitemap_lastmod.py --from-git`が導出する。HTMLを含むpushでは`regenerate-sitemap.yml`、再生成では`regenerate-page.yml`が呼ぶ（#265）。手で書き換えない（`docs/notes/sitemap-lastmod.md`）

### ページ側のJS（jpml_pros.js / table.js / video_wayhome.js）

- 生成後の絞り込み・並び替え・ページ送りはページ側の軽量JSに委譲する。`jpml_pros.js`は絞り込みと並び替え（ページ送りなし・全行表示）専用。型A・型A'の10ページは共通の`table.js`（絞り込み・ページ送り、並び替えなし）を使う。設定は`<table>`要素のdata属性（`data-page-size` / `data-name-mode` / `data-filter-param`）で渡し、属性省略時はページ送りなし・完全一致フィルターなしになる。ページ固有のUI（`resource_logs.html`の名前セレクトボックス等）はtable.jsとは別の小さなJSで補う。`video_wayhome.html`は`.mj-table`を持たないため`table.js`は読み込まず、専用の`video_wayhome.js`が絞り込み・画像フォールバック等を担う（#102第2段。共有ボタンは #530 で外した）
- 鳳凰戦・女流桜花・WRC の旧ページの JS（型C の `leagues.js`、Google Charts 依存の `houou_results.js`・`ouka_results.js`・`wrc_results.js`・`league_ranking.js`、順位変動の `houou_race.js`）は #582 で消した（2026-10-19）。新しいページの JS は `assets/league.js`・`assets/league_race.js`

### regenerate-page.yml

`docs/notes/github-actions.md`「regenerate-page.yml」へ移した（#492）。

### ワークフローの一覧

`docs/notes/github-actions.md`「ワークフローの一覧」へ移した（#492）。

### ワークフローを手動実行するとき

`docs/notes/github-actions.md`「ワークフローを手動実行するとき」へ移した（#492）。

### style.css の共通クラス

型A（表とフィルターのみ）の他ページへ展開するための共通クラスを `style.css` に用意している: `.mj-table`（表の見た目）、`.mj-table-2col`/`.mj-table-3col`（画像列固定幅＋残り列の折り返し）、`.mj-table-auto`（画像列を持たない型A'向け、列幅は自動計算）、`.mj-pager`（ページ送りUI）、`.mj-left`（列ごとの左寄せ）、`.mj-plain`（リンクの下線を消す）。列幅・列固定・行高（`contain-intrinsic-size`）などページ固有の構造はIDセレクタ側に残す

### navbar.js と検索欄

- **navbar.jsの27本のページhrefはルート相対パス（先頭`/`）にしてある（#162）。** 実ページ25本＋`_redirects`で転送する`resource_calendar`・`resource_books`の2本。 `wayhome/`配下などサブディレクトリのページからも同じnavbar.jsがそのまま使えるようにするため。`#`・`#searchBoxes`（検索欄開閉用）は対象外
- **検索欄（`#searchBoxes`）を持たないページは `<body>` に `data-search="off"`
  を出す（#163）。** navbar.js はこの属性を見て、虫眼鏡アイコン（検索欄を
  開閉するリンク）をそもそも描画しない。属性が無いページは「検索欄あり」として
  扱われ、従来どおりアイコンが出る（＝既定。付け忘れは現状維持に倒れる）。
  生成物は `lib/page.py` が `_render_search_boxes()` の結果から自動で出す
  （`render_content()` を使うページだけ `has_search_boxes=False` を明示）。
  現在の対象は1521ページ（2026-09-29、`git grep -l 'data-search="off"' -- '*.html'`）。
  トップ階層の6ページ（`404` / `resource_dictionary` /
  `resource_efficiency` / `rh_results` / `rh_results_detail` /
  `video_wayhome`）と、`title/`・`live/`・`saikyo/`・`books/`・`wayhome/` の全ページ。
  トップ階層の生成物5ページ（`resource_dictionary` / `resource_efficiency` / `rh_results` /
  `rh_results_detail` / `video_wayhome`）とサブディレクトリの生成物は
  `has_search_boxes=False`の明示で自動的に出る（サブディレクトリの系統は
  絞り込み欄を本文の`.mj-filterbar`に持つか、検索欄を持たない）。`video_wayhome`は虫眼鏡アイコンで開閉する`#searchBoxes`を
  navbar直下の常時表示フィルタバー（`.mj-filterbar`）に置き換えたため対象に
  加わった（#189）。
  残り1ページ（手書きHTML: `404`）を新規に追加するときは手で付ける

### メンテナンス用スクリプトの詳細

`scripts/`は公開対象外（`.assetsignore` でCloudflareへの配信から除外）。多くは GitHub Actions から定期実行され、結果をissueに書き出す。
依存は、特記の無いものは標準ライブラリのみ（実行例: `python3 scripts/check_image_links.py --json result.json`）。
外部からの読み取り（`lib/sheets.py` のスプレッドシート〈gviz〉、`lib/dojo_guest.py` の連盟サイト、YouTube Data API の取得）は、タイムアウト・接続の失敗を `lib/net_retry.py` で2回まで繰り返す（5秒・15秒おき、HTTP の応答は繰り返さない）。
フィルタの検知の CSV の取得（`lib/sheets.py` の `_get`）は、失敗しても警告で生成を続けるため繰り返さない（CSV だけが長く落ちた日に再生成が延びないように）。
Google の API（`lib/gcal.py`・`lib/sheets_write.py`・`sync_birthday_calendar.py`）には接続10秒・読み取り60秒のタイムアウトを渡し、読み取り（GET）だけ同じく繰り返す。書き込みは、届いたかが分からず重複しうるため繰り返さない（#472）。

- `check_image_links.py` — `jpml_pros.html` 内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎日 04:30 JST、保険で毎週月曜03:00 JST）
- `collect_saikyo_images.py` — 最強戦の選手写真（SNS ブックの実効の値、#514）で取得できなくなった画像URLを見つけ、SNS ブックでの状態（【3】の X状態・【4】の有無）と一緒にCSV・検知の issue に出す（#333、手動実行＋`check-image-links.yml`から毎日`--json`実行）。X API は呼ばない（取り直しは `update_sns_book.py`）。`--resolve-test <X ID>`で X API を1件だけ呼んで診断を出す。使い方は`docs/notes/saikyo-page-design.md`「選手写真の更新」
- `update_sns_book.py` — SNS ブック（選手の X・note・YouTube の ID と画像の URL、#514）の【1】元データ・【2】ID・【3】画像取得を書く（`--check`・`--init`・毎日の更新・`--dry-run`。`update-sns-book.yml`から毎日）。X の画像を X API で取り直すのはここだけ。ページの生成は SNS の値を `lib/sns.py` から読む（2026-10-11 から）。仕組みは`docs/notes/sns-book.md`
- `cleanup_logs.py` — `docs/logs/`の作業ログのうち、7日を過ぎて片付けてよいもの（完了で3項目「なし」・取り下げ・続き先が完了／取り下げ／削除済み・論点の無い旧形式）を削除し、条件外のものを種類別（判断待ち・中断／書き方の違反／読めない）に一覧にする（`cleanup-logs.yml`から週1で実行、`--dry-run`で一覧のみ、`--new-rule-date`で規則を入れた日を試せる）。条件は`docs/notes/branch-operations.md`「作業ログの寿命」
- `delete_merged_branches.py` — マージ済み（`origin/cloudflare` の祖先）で先頭が24時間より前の `work/*` を削除し、ブランチ名と先頭の SHA を出力する（`cleanup-branches.yml`から毎日、`--dry-run`で一覧のみ。完全な履歴のクローンが要る）
- `check_meibo.py` — 連盟員名簿データ（`lib/meibo.py`）と「プロ」シートの在籍者を登録名で突き合わせ、名簿のみ・プロのみを一覧にする（#370、`check-meibo.yml`から週1、生成は止めない）。テストは CLAUDE.md「判断・作業の原則」
- `check_ampai.py` — 最新の期（集計元「鳳凰」「桜花」）の行が Ampai のブック（`lib/ampai.py`）に無いかを調べる（#548、`check-meibo.yml` から週1。無ければ issue「Ampai の URL が無い期」に書く。生成は止めない）
- `regenerate.py` — ページ再生成の共通入口。`scripts/generate_<ページ名>.py`が存在するページを「生成対象」とみなす。`--list`で対象ページ一覧、`all`で全ページ再生成、ページ名指定で単体再生成、`--changed`で変更ファイルから対象判定（`regenerate-page.yml`が使用）
- `regenerate.py --check <ページ名…|all>` — シートを直した直後、再生成（push・手動実行）の前に、各ページが生成で止まるかだけを確かめる（#551）。対象の選び方は `all` と同じ（`FROZEN_FROM_ALL` を除く）。`--check` を持たないページは**普通に生成してから作業ツリーをそのページの前に戻す**（#533 の `TreeSnapshot`。未コミットの変更も元に戻る。`.gitignore` のファイルは対象外）。生成と同じ経路なので、live_pages の img.youtube.com・帰り道の pbs.twimg.com への HEAD も回る（割り当ての要る API は呼ばない）。`scripts/lib/check.py` を使う生成スクリプトは `--check` を持つとみなし、生成の代わりに `--check` で呼ぶ（書かない。書いたら止まる理由にして戻す）。**2026-10-12 から、`regenerate.py --list` の全ページが書かない `--check` を持つ**（#551）。生成して戻す形は、`--check` を持たない生成スクリプトを足したときの受け皿として残る。どのページも、生成では最初の理由で止まる検査に `stop` を渡せる形（生成では `lib/check.py` の `fail()`・`guard()` が今までどおり止める）にして、`--check` では全部出す。生成では書き出しの後にある検査（title_pages の決勝メンバー行数の照合、houou_race の既定の表示、houou/race/ の枠と説明文、live_pages の `_redirects` の上限）も書く前に回す。ページごとの要点: 帰り道の2本は `lib/wayhome.py` の `check_inputs()` を共有し、写真の HEAD も回す。title 系の3本（title_pages・jpml_pros・jpml_test）は title の検査を同じ関数で通る。live_pages はヒーローの画像の HEAD を回すが `data/live_thumbs.json` は書かない。ask_data は /live で公開しなかった行を警告に出す（生成でも出る）。`lib/page.py` の `generate()` を使うページは `check_rows()`（1行ずつ組み立てて、列の数の違う行などを全部出す。0件は警告）。ouka_leagues は期が0件、resource_efficiency は値が数でない行を止まる理由にする。saikyo_pages は `main()` を書かずに回し（年度・対局・卓・トップの各ページとサイトマップの XML の検査を1つずつ）、生成が print する「警告: 」の行を `lib/check.py` の `warnings_from_print()` で集める。対局が0件なら止まる理由。最後に、ページごとの結果と、止まる理由（生成は失敗したときの出力の最後の行、`--check` は「検査: 止まります」の後の「- 」の行）と警告（出力の`警告: `・`::warning::`で始まる行。続く字下げの行は詳細として数える）を分けて出す。止まる理由があれば終了コード 1、警告だけなら 0。`check_asset_limits` とコミット対象の一覧は出さず、ワークフローからは呼ばない。全ページで約3分（2026-10-12）
- `generate_resource_dictionary.py --check` — 「辞書」タブと「プロ」タブを生成と同じ経路で読み、生成と同じ検査（見出し・知らないカテゴリ・品詞・不正な文字・読みと語の組の重複）を止めずに行い、止まる理由をすべてとカテゴリごとの語数を出す。ファイルは書かない（止まる理由があれば終了コード1。出力の形は `scripts/lib/check.py`）。シートを直した後、指示を書く前に回す
- `check_asset_limits.py` — 配信ファイル数・最大のファイル・`_redirects`（静的・動的）・`_headers`のルール数を Cloudflare の上限（Workers Free）との比で表にし、80% 以上で警告、100% 超でエラー終了する（#387）。`regenerate.py`の最後と`check-assets.yml`から呼ばれ、Actions ではジョブのサマリにも出す
- `<title>`・meta description・OGP の正は、生成ページは各生成スクリプトの `META`（`PageMeta`）、手書きのページは HTML そのもの（直すときは HTML を直接直す）。一括で書き換える `apply_page_meta.py`（#5）は 2026-10-18 に消した（#517）
- `build_ogp_image.py` — OGP画像を生成（#78、手動実行）。Pillowが必要。全ページ共通の1枚は `img/ogp/site/index-white.png`（白地・黒字の「ryoei.pro」、#581。作り方は `--text ryoei.pro --color '#212529' --bg '#ffffff' --max-size 180 --tracking -0.03`。色は旧 `img/ogp.png` と同じ）で、`lib/page.py` の `PageMeta` の既定・`generate_jpml_pros.py` のテンプレート・手書きのページの `og:image` に入っている。引数なしで書き出す旧 `img/ogp.png`（背景#ffffff）は凍結中の books/ だけが使う。生成したPNGもコミットする（生成環境のフォント差で再生成のたびに差分が出るのを避けるため）。`--check`でコミット済みのPNGと一致するか確認できる。`--text` と `--out` で任意の文字列・出力先の1枚も作れる（#343）。和文は `~/.local/share/fonts/NotoSansJP-Bold.otf`（`OGP_FONT`で差し替え可、無ければ失敗）を使い、横幅1040pxに収まるまで文字を小さくする。段を重ねるときは `--text` を繰り返し、`--scale`（1段目に対する比）・`--color`・`--max-size`・`--tracking`・`--line-gap` で調整する。背景は `--bg`（既定#ffffff）。`--footer <文字>` は最後に固定の大きさ（`FOOTER_SIZE` 48px・間隔 `FOOTER_GAP` 40px）の段を足す（区分の画像の「ryoei.pro」、#581）。文字ベースの画像は4種類だけで（`docs/notes/ogp.md`、#581）、年度・大会が増えても作らない。**引数なしの `--check` は、コミット時と環境のフォントが違うと、スクリプトを変える前から一致しない**（2026-09-16 の Codespace では FONT_CANDIDATES のうち DejaVuSans が使われ、変更前のスクリプトでも一致しなかった）。スクリプトを変えたときの回帰確認は、変更前後のスクリプトの `render()` の出力同士を比べる（CHAT-0916-SY-07/08）

- `build_wayhome_ogp.py` — 「帰り道」一覧ページのOGP画像`img/ogp/wayhome/index-<最新話の公開日 YYYYMMDD>.jpg`（1200×630、JPEG品質88）を書き出す（#339。週次の再生成〈`regenerate-page.yml`〉が作り直す、#340）。必要なのはPillowだけ（文字を描かないためフォントは要らない）。最新4本のサムネイルを2×2に並べ、切り抜かずに収める（全体が16:9なので高さが先に埋まり、左右の余りは`--mj-v-bg`=#121212のまま。サムネイル同士の区切りは8px）。実行のたびに作り直すが、最新4本が同じなら同じ名前・同じJPEGになり差分は出ない。ほかの`index*.jpg`は削除する。最新話と同じ公開日の回が2本以上なら名前に連番を付け（`index-<日付>-N.jpg`）、同じ名前で中身が変わる場合は上書きせず今のファイルを残す。生成したJPEGもコミットする。個別ページの og:image はYouTubeのサムネイルのまま。**ページ別のOGP画像は `img/ogp/<区分>/` 以下に置く。共通の `img/ogp.png` は動かさない。** 新しい回を足したときの手順は`docs/notes/video-wayhome.md`「新しい回を追加する手順」
- `check_wayhome.py` — 「帰り道」の取り込みの知らせ（新しい回・YouTube の情報が無い回・見られなくなった回・決勝動画IDが空の回）を組み立てる（#194、週次の再生成が呼び、常設 issue「帰り道の新しい回」にコメントする）。判定は `lib/wayhome_intake.py`。手順は`docs/notes/video-wayhome.md`「新しい回を追加する手順」

### #7 の期待値

2026-09-11、Lighthouse実測を受けて修正した。

- **「移行済みだから速い」は成り立たない。データ件数（DOM要素数）に依存する。** 件数の少ないページは改善し、多いページ（`jpml_pros` 等）は移行しても解決しない
- 件数の多いページの根本解決は表示件数を絞ること（#24の五十音タブ、新サイトで対応）
- #7 の目的は外部ドメイン依存の解消（#9 の前提）とインラインハンドラの排除でもあるため、件数に関わらず続ける
- DOM規模の最大の懸念は `houou_results`（15,416行。自前の `row.hidden` 方式に置き換えると全行がDOMに乗る）〔2026-10-19: houou/players/ の選手ごとのJSONへ移し、旧ページは #582 で消した〕
- 実測値・行数調査は `docs/lighthouse-baseline.md`、経緯の全文は `docs/notes/handover-archive-2026.md`

### 型Aの表の方針

列ヘッダのソートは `jpml_pros.html` 専用（他ページは必要と判断したときだけ追加）。2列ページは `.mj-table-2col`、3列の `video_mtsuku` は `.mj-table-3col`。経緯は `docs/notes/handover-archive-2026.md`

