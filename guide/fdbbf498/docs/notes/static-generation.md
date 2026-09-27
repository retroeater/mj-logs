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
    `resource_logs`の名前セレクトボックス・タグリンクで使用)
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
  いる（データ不備ではなく修正不要、詳細は#127のコメント）
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

### ページの一覧

HTMLは26ページ + 書籍の一覧と個別ページ（`books/`、#97、noindex・メニュー未掲載。**2026-09-22 開発凍結、`docs/notes/books-freeze.md`**） + 「帰り道」エピソード個別ページ38枚（`wayhome/`、#162） + 最強戦のトップと年度ページ（`saikyo/`、#319/#355、2026-09-21 に公開） + タイトル戦の新構成（`title/`、#222、未公開） + 放送対局ページ（`live/`、#346、noindex・メニュー未掲載。正式公開は #362）。大きく4系統に分かれる。

| 系統 | ページ数 | 状態 |
|---|---|---|
| `index.html` | 1 | Webサイトテンプレート（iPortfolio）由来。`index.css` と11個のvendorライブラリを使う |
| ビルド時生成（型A・15列） | 1 | `jpml_pros.html`。独自の`generate_jpml_pros.py`のまま |
| ビルド時生成（型A・2列/3列） | 8 | `jpml_titles.html` / `jpml_test.html` / `resource_logs.html` / `video_live.html` / `video_en.html` / `rh_paifu.html` / `saikyo_mens.html` / `video_mtsuku.html`(3列)。`scripts/lib/page.py` + 共有JS `table.js` を使う（#7） |
| ビルド時生成（型A'・多列テキスト） | 2 | `rh_results.html` / `rh_results_detail.html`。画像列を持たないため`.mj-table-auto`を使う（#7、完了） |
| ビルド時生成（型D・静的SVG） | 1 | `resource_efficiency.html`。表を持たないため`render_content()`を使う。外部JS・外部ドメインへの依存が一切ない（#7/#128、完了） |
| ビルド時生成（型C・積み上げ棒+折れ線） | 2 | `houou_leagues.html` / `ouka_leagues.html`。積み上げ棒と既定選手の折れ線は静的SVG、`?name=`時の折れ線差し替えのみ`leagues.js`が担う（#7/#127、完了） |
| ビルド時生成（独自: 全画面ヒーロー+横スクロールカード列） | 1 | `video_wayhome.html`。`render_content()`+専用JS`video_wayhome.js`。新サイトのパイロット（`docs/notes/video-wayhome.md`） |
| ビルド時生成（サブディレクトリ、ヒーロー構成のエピソード個別ページ） | 38 | `wayhome/<動画ID>.html`。`scripts/generate_wayhome_episodes.py`（`docs/notes/video-wayhome.md`） |
| ビルド時生成（サブディレクトリ、最強戦のトップと年度ページ） | トップ1＋年度数 | `saikyo/index.html`・`saikyo/<年度>.html`。`scripts/generate_saikyo_pages.py`。2026-09-21 に一般公開し、navbar・`sitemap-saikyo.xml`・`llms.txt` に載る（#319・#348、`docs/notes/saikyo-page-design.md`） |
| ビルド時生成（サブディレクトリ、タイトル戦の新構成） | 入口1＋大会数＋期数 | `title/index.html`・`title/<slug>/index.html`・`title/<slug>/<期>.html`。`scripts/generate_title_pages.py`。noindex・メニュー未掲載（#222、`docs/notes/title-pages.md`） |
| ビルド時生成（サブディレクトリ、放送対局ページ） | 691（2026-09-18） | `live/index.html`・`live/<タイトル戦>/index.html` 以下。`scripts/generate_live_pages.py`。noindex・メニュー未掲載、正式公開は #362（`docs/notes/live-page-design.md`） |
| ビルド時生成（サブディレクトリ、書籍の一覧と個別ページ） | 一覧1＋190 | `books/index.html`・`books/<ISBN13>.html`。`scripts/generate_books_pages.py`。noindex・メニュー未掲載、`sitemap-books.xml` は `sitemap.xml` から未参照。**2026-09-22 開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。詳細は `docs/notes/books-freeze.md`** |
| Google Charts依存 | **6** | ブラウザから直接スプレッドシートを読む。#7の対象。型B3・ランキング系A3 |
| 静的なページ | 4 | `404.html` / `jpml_links.html` / `resource_dictionary.html` / `rh_links.html` |

### 生成スクリプトの構成（lib/page.py）

- `jpml_pros.html`は独自の`scripts/generate_jpml_pros.py`のまま。型A/A'の10ページは`scripts/lib/page.py`（HTMLテンプレート・行組み立て・画像セル・エスケープの共通処理）を使い、各`scripts/generate_<ページ名>.py`は「設定(`PageMeta`/`TableConfig`) + 行組み立て関数」だけを持つ（#7の共通化）。型C・型D・`video_wayhome.html`・`wayhome/`のエピソード個別ページは表を持たないため`lib/page.py`の`render_content()`を使う。いずれも`scripts/lib/sheets.py`経由でスプレッドシートのgvizエンドポイント（`google.visualization.Query`と同じSELECT構文）を叩く
- `lib/page.py`はサブディレクトリのページ（`wayhome/`配下）向けに`asset_prefix`引数を持つ（既定は空文字、#162）。head内のアセット参照（`style.css`・`assets/vendor/*`・`favicon.ico`・`navbar.js`・`table.js`）にこの接頭辞を付ける。`wayhome/`配下のページは`"../"`を渡す。あわせて`PageMeta`に`og_image`/`og_image_width`/`og_image_height`/`og_image_alt`/`canonical`を持たせ、ページごとに差し替えられるようにした（既定はそれぞれ`img/ogp.png`・1200×630・`"ryoei.pro"`・`None`=canonicalなし。#113の判断どおり）。サブディレクトリを増やす場合はこの仕組みを再利用できる
- **ページ別のOGP画像は`img/ogp/<区分>/`以下に置く（#339）。** 全ページ共通の`img/ogp.png`は動かさない。「帰り道」は一覧ページの`img/ogp/wayhome/index-<最新話の公開日>.jpg`1枚だけで、`scripts/build_wayhome_ogp.py`が書き出す。名前は`lib/wayhome.py`の`list_og_image_name()`で決め（immutable配信のため中身が変わるときは名前を変える、`docs/notes/ogp.md`）、参照は`resolve_list_og_image()`を通す。今の最新話の画像が未生成なら共通の`img/ogp.png`に戻るためページ生成は止まらない。**個別38ページの`og:image`はYouTubeのサムネイル（`resolve_hero_thumb()`、幅・高さはAPIの実寸）をそのまま使う。** 一度はページ別に焼いた画像を使ったが、サムネイルにシリーズ名・大会名・選手名が焼き込まれており、Xのカードでは`og:title`も画像に重なるため二重になる。2026-09-16に取りやめた。最強戦の年度ページは`img/ogp/saikyo/<年度>-black.png`（「麻雀最強戦」「<年度>」の二段組の16枚、`scripts/build_ogp_image.py --text`が書き出す。接尾辞は`generate_saikyo_pages.py`の`OGP_DESIGN`、意匠を変えたら名前も変える、`docs/notes/ogp.md`）で、参照は`og_image_for()`を通し、画像が無い年度は共通の`img/ogp.png`に戻る（#343）。**配色は共通の#212529・#ffffffの例外で、背景#000000、文字は最強戦のテーマカラー#EA5505（公式ポスターから採取）の単色（背景は白地・黒縁などのサンプルから平野さんが黒地・縁なしを選んだ、CHAT-0916-SY-07）。** 直感的に最強戦と分かるための色で、ロゴ・炎・質感は使わない。フォントは手元で最も太いゴシック体のNoto Sans JP Bold（Blackは無い）。1段目を幅1040pxいっぱい（212px）、2段目をその0.55倍、字間-3%、段の間隔は1段目の0.08倍（CHAT-0916-SY-04）
- `PageMeta`は`og_title`（既定`None`＝`<title>`と同じ）を持つ。SNSのカード見出しだけを短くしたいページで指定する。「帰り道」の個別ページは`<title>`にシリーズの正式名を残したまま、og:titleを「<大会名> <選手名> | 帰り道 | ryoei.pro」にしている（#339。短縮形は`lib/wayhome.py`の`SERIES_SHORT`）
- `wayhome/`のエピソード個別ページは`?name=`等のURL変種を持たないため、#113（canonicalなしの判断）の理由が当てはまらない例外として`<link rel="canonical">`を持つ（38ページのみ）。他27ページはcanonical無しのまま
- `lib/page.py`はh1直後・`#searchBoxes`手前にページ固有のHTMLを差し込む`content_before`スロット（#102第1段で追加）を持つが、現在どのページも使っていない。ページ固有HTMLをh1直後に差し込む汎用スロットとして残している
- `jpml_pros`のYouTubeアイコンだけはシートではなくYouTube Data API（channels.list）から取り、`data/youtube_channels.json`を経由する（#3。キーはActions secret `YOUTUBE_API_KEY`）。
  取得は週次`all`と`target_page`空/`all`の手動実行時のみ。失敗しても既存JSONでアイコンは維持され、ジョブだけ失敗扱いになる
- `wayhome_episodes`だけは出力が単一ページではなく`wayhome/`配下38枚になる（#162）
- `saikyo_pages`（`scripts/generate_saikyo_pages.py`）も出力が単一ページではなく、`saikyo/`配下の年度ページと、`sitemap-saikyo.xml`（年度ページのみ）になる（#319、`regenerate.py`の`OUTPUT_OVERRIDES`は`"saikyo/"`）。表を持たず`render_content()`と`assets/saikyo.js`を使う。設計は`docs/notes/saikyo-page-design.md`
- `title_pages`（`scripts/generate_title_pages.py`）は`title/`配下の入口・大会ページ・期ページと、`sitemap-title.xml`を書き出す（#222、`OUTPUT_OVERRIDES`は`"title/"`）。`sitemap-title.xml`は公開まで`sitemap.xml`から参照しない。仕組みは`docs/notes/title-pages.md`

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
  `regenerate-page.yml` が失敗すれば GitHub から通知が届く。フィルタは解除すれば直る一時的な状態なので、
  issue を残す意味が薄い
- **増える時間**: タブごとに CSV 1回＋`COUNT(A)` 1回、スプレッドシートごとに `htmlview` 1回。
  2026-09-21 の実測（Codespace から、1回ずつ）:

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
`generate_title_pages.py` はこの形で、title/ の第1段（CHAT-0916-TT-07〜TT-16）で止まったのはすべて実際の食い違いだった。新しい生成スクリプトでも同じ形にする。

- 読むタブの見出しの照合。gviz は存在しないタブ名でも先頭のタブを黙って返すため、これが無いと誤ったタブを読んだまま生成が通る
- タブのフィルタ。gviz の `SELECT COUNT(A)` と CSV エクスポートの A列が空でない行数を比べ、食い違えば止める
  （上の「シートのフィルタの検知」。`lib/sheets.py` が共通の入口で行うので、生成スクリプト側の実装は要らない。
  CSV を取れないときは警告を出して通す）
- 置換文字（U+FFFD）・制御文字。取り込みでの文字化けをページに出る前に捕まえる
- マスタのタブとの不整合。キー（大会名など）の欠け、組み立てた値と記録されている値の食い違い、出力の件数と入力の行数の一致検査

**マスタのタブは、チャットの指示と並行して平野さんが変えることがある。** 読み込んだ現在の値（行数、表示・区分・状態のように
表示対象が変わる列）を生成の出力に出し、指示文の前提と違えば手を入れずに報告する（CLAUDE.md「Chat-Ref」）。
title/ では「タイトル戦」タブの大会の改名が「タイトル」タブと合わずに生成が止まり（CHAT-0916-TT-14、TT-15 で解消）、
区分の変更・大会の追加でも表示対象と検査対象が変わった（CHAT-0916-TT-11・TT-15）。

- **読んでいる最中に平野さんがタブを貼り替えていることがある。** 貼り替えの途中を読むと、行数が急に減った状態が返る
  （CHAT-0916-LV-17 で「放送対局」3,115行が 1,254行に見えて中断。LV-23 も編集の途中の可能性で中断）。
  シートを読む作業では、行数が想定と違う・読み直すたびに変わるときに止める。大量の貼り替えを伴う作業では、平野さんが貼り終えたと伝えてから読む
- **タブの有無は gviz では判定できない。** 存在しないタブ名にも先頭のタブを黙って返す（CHAT-0916-LV-11 で判明）。
  タブ名の一覧はスプレッドシートの `htmlview` で確かめ、生成では見出しの照合を必ず行う。`lib/sheets.py` の `fetch_records()` は、
  先頭のタブと同じ中身が返ったら「タブが無い」として止める
- **列は見出しの名前で読む。** タブ名・列名・列順はシート側で変わる（LV-11「プロ以外」→「連盟プロ以外」、
  LV-29「全動画」→「連盟ch」・列「候補」→「放送対局」・列の並べ替え）。`fetch_records()` のように、生成に使う列の見出しが
  欠けている・重複しているときだけ止め、知らない列が増えても止まらない形にする（列記号の `SELECT A,I,J` は並べ替えで無言に別の列を読む）
- **作業の途中で平野さんがシートを直すことがある。** 最強戦の作業（CHAT-0918-SX）では、SX-08 の「連盟プロ以外」の見出しの改名
  （「備考」→「所属補足」）、SX-09 の「放送対局」の動画の移動があった。見出しの改名は、`lib/live.py` の `OTHER_HEADERS` と
  `generate_title_pages.py` の `EXPECTED_HEADERS` の照合で /live・/title・最強戦（作業ブランチの実装）の生成が止まり、壊れたページを書き出す前に拾えた
  （SX-08 で判明、/live・/title は SX-09、最強戦は SX-10 で見出しを合わせた）。
  平野さんはシートを直したらチャットに伝える運用にした（2026-09-19）

### 新しいページを作るとき（/live の実例、CHAT-0916-LV-33）

- **件数・容量・配信ファイル数を先に見積もる。** /live は、1回戦1ページなら約3,300ページという見積もり（CHAT-0916-LV-03）から、
  個別ページの単位を「同一ステージの同一卓」に変えた（LV-04、約960ページ）。Workers の静的アセットの上限（Free で 20,000 ファイル）、
  `_redirects` の上限（静的 2,000行）、埋め込むページの大きさも同時に見る
- **検査で外れる行の一覧を平野さんに返す流れを、最初から組む。** データを構造化すると、表のページでは表に出なかった誤りが出る。
  /live では動画IDの重複、卓の取り違え、対局者の欠落（他団体の選手を抜いていた時期がある）、概要欄の誤記が多数見つかった。
  外れた行は公開せずに警告し（ジョブのサマリにも出す）、行・理由・今の値・直し方の案を表にして返す（例: `docs/logs/CHAT-0916-LV-26.md`「2.」）

### サイトマップ

`sitemap.xml`（インデックス）が`sitemap-pages.xml`（25ページ、旧sitemap.xml。27ページのうち`404.html`〈noindex〉と`saikyo_mens.html`〈年1回の単発企画〉を意図的に除外）と`sitemap-wayhome.xml`（wayhome/38ページ、`generate_wayhome_episodes.py`が生成）と`sitemap-saikyo.xml`（saikyo/配下、`generate_saikyo_pages.py`が生成、#319）を束ねる方式（#162）。`robots.txt`のSitemap行は`sitemap.xml`のまま変更していない。lastmodは生成・非生成を区別せずgitの最終コミット日（JST）で、`scripts/update_sitemap_lastmod.py --from-git`が導出する。HTMLを含むpushでは`sitemap-lastmod.yml`、再生成では`regenerate-page.yml`が呼ぶ（#265）。手で書き換えない（`docs/notes/sitemap-lastmod.md`）

### ページ側のJS（jpml_pros.js / table.js / video_wayhome.js / leagues.js）

- 生成後の絞り込み・並び替え・ページ送りはページ側の軽量JSに委譲する。`jpml_pros.js`は絞り込みと並び替え（ページ送りなし・全行表示）専用。型A・型A'の10ページは共通の`table.js`（絞り込み・ページ送り、並び替えなし）を使う。設定は`<table>`要素のdata属性（`data-page-size` / `data-name-mode` / `data-filter-param`）で渡し、属性省略時はページ送りなし・完全一致フィルターなしになる。ページ固有のUI（`resource_logs.html`の名前セレクトボックス等）はtable.jsとは別の小さなJSで補う。`video_wayhome.html`は`.mj-table`を持たないため`table.js`は読み込まず、専用の`video_wayhome.js`が絞り込み・画像フォールバック・共有ボタン等を担う（#102第2段）
- 型Cの2ページは`leagues.js`（共通JS）を使う。積み上げ棒と既定選手の折れ線は静的SVGに焼き込み済みで、`leagues.js`は`?name=`に応じて選手1名分の`<polyline>`と凡例ラベルだけを差し替える（選手ごとの折れ線データは`houou_leagues_data.json`/`ouka_leagues_data.json`をfetchして取得）。選手選択リストは「プロ」シートのY列="Y"かつ鳳凰最高/桜花最高列に値がある選手が対象（#127/#133）。**退会済みの選手は鳳凰/桜花シートにリーグの実データが残っていても選択リストに出ない。これは正しい挙動**（Y列="Y"が在籍・公開対象を表す。#168）

### regenerate-page.yml

GitHub Actions (`.github/workflows/regenerate-page.yml`) が、`scripts/generate_*.py` / 対応する `.js` / `scripts/lib/**` の変更をcloudflareブランチへのpushで検知し、自動で再生成・コミットする（`chore: regenerate <ページ名>.html via GitHub Actions`）。検知はpushに含まれる全コミットの範囲（`github.event.before`〜`github.sha`）の差分で行う（#167）。手動実行（workflow_dispatch）も可能。毎週月曜05:37 JSTにも`all`を自動実行し、差分がなければコミットしない（#103）。`table.js`・`leagues.js`はルート直下の`*.js`に該当するためpushでワークフロー自体は起動するが、どのページ名にも一致せず対象0件で終わる（HTMLに焼き込まれないため実害なし）。`regenerate.py`は出力がディレクトリになるページ向けに`OUTPUT_OVERRIDES`（例: `wayhome_episodes` → `"wayhome/"`）を持ち、コミット・lastmod更新対象のパスとして返せる（#162）。ワークフローの`git add`は`-A --`で削除も拾い、`sitemap*.xml`をまとめて対象に含める。**2026-09-22 開発凍結（`docs/notes/books-freeze.md`）以降、`regenerate.py`の`FROZEN_FROM_ALL`が`"all"`から`books_pages`を外し（ページ名を指定すれば個別には動く）、「楽天の書影を取得」ステップは`if: false`で止めている**

### ワークフローの一覧

実行の契機と内容。正は `.github/workflows/`（本数はここに書かない）。

| ワークフロー | 内容 |
|---|---|
| `regenerate-page.yml` | 生成スクリプト・対応する`.js`・`scripts/lib/**`の変更のpushと、毎週月曜05:37 JST（`all`）。ページを再生成してコミットする（詳細は上の「regenerate-page.yml」） |
| `check-image-links.yml` | 毎週月曜03:00 JST。画像のリンク切れ（最強戦の選手写真を含む）を確かめ、常設issueに書き出す |
| `check-ron2-images.yml` | 毎週月曜04:00 JST。龍龍の画像とサイトの表示が一致するか確かめる |
| `check-saikyo-unregistered.yml` | 毎週月曜06:50 JST。最強戦の出場者で「プロ」「連盟プロ以外」から引けない人を常設issueに書く（#431、`docs/notes/saikyo-page-design.md`「8. 出場者の登録漏れの検知」） |
| `assets-check.yml` | pushのたび。`.assetsignore`の漏れ（#133）と CLAUDE.md・handover.md のサイズを検知する |
| `sitemap-lastmod.yml` | HTMLを含むpush。sitemapのlastmodをgitの最終コミット日にそろえてコミットする（#265、`docs/notes/sitemap-lastmod.md`） |
| `check-leagues-dropped.yml` | 手動実行のみ。型Cで選択リストから漏れている選手を検知する（#168） |
| `cleanup-logs.yml` | 毎週月曜06:23 JST。7日を過ぎた作業ログを片付け、条件外のものを #357 に通知する |
| `check-meibo.yml` | 毎週月曜05:07 JST。連盟員名簿と「プロ」シートの在籍者の不一致を常設issueに書く（#370） |
| `sync-birthday-calendar.yml` | 毎週月曜05:17 JST。名簿の誕生日をGoogleカレンダーへ同期する（#379、`docs/notes/birthday-calendar.md`） |
| `fetch-gsc.yml` | 毎月1日06:00 JST。Search Console の検索パフォーマンスを `docs/gsc/` に取り出し、robots.txt の差分を #304 に知らせる（#269） |
| `sync-dojo-calendar.yml` | 毎日07:12 JST。道場部ゲストの告知画像を読み、カレンダーへの追加分と新規ゲスト・当月誕生日を #426 に知らせる。書き込みは手動実行のときだけ（#390、`docs/notes/dojo-guest-calendar.md`） |
| `sync-books-calendar.yml` | **2026-09-22 開発凍結にともない無効化（`gh workflow disable`）。** 元は毎週月曜05:27 JSTに「書籍」タブの発売日をGoogleカレンダーへ同期していた（#97、`docs/notes/books-calendar.md`・`docs/notes/books-freeze.md`） |
| `sync-logs.yml` | `docs/logs/**` を含む push（cloudflare・`work/**`）。その push で追加・更新された作業ログを public の `retroeater/mj-logs` の `logs/` へ写し、cloudflare で削除されたログを消す（#440。書き込みはシークレット `MJ_LOGS_TOKEN`） |
| `delete-merged-branches.yml` | 毎日07:53 JST と手動。マージ済みで先頭が24時間より前の `work/*` を削除する（#440、`scripts/delete_merged_branches.py`） |

### ワークフローを手動実行するとき

実行の契機と内容の一覧は上の「ワークフローの一覧」。手動実行の前に次を確かめる。

- `regenerate-page.yml` の checkout と push 先は実行ブランチ（#326）。古い作業ブランチから手動実行すると、そのブランチの状態で生成物がコミットされる
- `check-image-links.yml` の checkout の ref は `${{ github.ref }}`（#306）で、手動実行では選んだブランチがチェックアウトされる。
  古い作業ブランチから実行すると常設issue（#218）の本文がそのブランチのデータで上書きされる。schedule は既定ブランチ（cloudflare）で走るため週次実行は変わらない。
  ジョブ`saikyo`は最強戦の選手写真を別のissueに書き出す（`collect_saikyo_images.py --json`、#139）。画像は1,985枚（handover.md に書いていた時点の数）
- `cleanup-logs.yml`（`scripts/cleanup_logs.py`、条件は `docs/notes/branch-operations.md`「作業ログの寿命」）: 手動実行は dry_run が既定。週次実行は `SCHEDULE_ENABLED`（現在 `'true'`）が `'false'` なら dry-run
- `delete-merged-branches.yml`（`scripts/delete_merged_branches.py`）: 手動実行は dry_run が既定。毎日の実行は `SCHEDULE_ENABLED`（現在 `'false'`）が `'false'` なら dry-run
- `check-meibo.yml`（`scripts/check_meibo.py`）: 手動実行は dry_run が既定。不一致があっても生成は止めない
- `sync-birthday-calendar.yml`（`scripts/sync_birthday_calendar.py`）: 週次の schedule はコメントアウト中。既定は差分を出すだけで、apply を選んだときだけ書き込む（`docs/notes/birthday-calendar.md`）
- `check-ron2-images.yml`（`scripts/check_ron2_images.py`）: checkout の ref は `cloudflare` が直書きで、どのブランチを指定して dispatch しても cloudflare の内容で走る。844人分を1秒間隔で取りに行くため、**週次実行（月曜04:00 JST）の直後に手動実行すると龍龍側に届かないことがある。**2026-09-21、週次実行の約4時間半後に実行したところ1件目から `URLError` が続き、連続20件で中断して failure になった（照合できた選手は0人。原因は龍龍側の事情のため確認できていない）。途中で落ちると「結果をissueに反映」まで進まず、常設issue（#424 の「龍龍画像の同期確認」）は本文も状態も変わらない。修正の確認だけなら、選手1人分を直接問い合わせるほうが相手の負担が小さい（CHAT-0921-RN-03）
- `fetch-gsc.yml`（`scripts/fetch_gsc.py`、#269）: checkout と push 先は実行ブランチ。手動実行の既定はコミットしない（取得するだけ）。
  schedule は28〜31日 21:00 UTC に起動し、JST で1日の回だけ本体が動く（毎月1日 06:00 JST。2026-09-21 に有効にした）。
  `--plan` を付けるとAPIを呼ばずに期間と出力先だけ出せる
- `assets-check.yml` は Cloudflare へのアクセスを要しない検査専用

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
  現在の対象は8ページ（`404` / `jpml_links` / `resource_dictionary` /
  `resource_efficiency` / `rh_links` / `rh_results` / `rh_results_detail` /
  `video_wayhome`）。うち生成物4ページ（`resource_efficiency` / `rh_results` /
  `rh_results_detail` / `video_wayhome`）は`has_search_boxes=False`の明示で
  自動的に出る。`video_wayhome`は虫眼鏡アイコンで開閉する`#searchBoxes`を
  navbar直下の常時表示フィルタバー（`.mj-filterbar`）に置き換えたため対象に
  加わった（#189）。
  残り4ページ（手書きHTML: `404` / `jpml_links` / `resource_dictionary` /
  `rh_links`）を新規に追加するときは手で付ける

### メンテナンス用スクリプトの詳細

`scripts/`は公開対象外（`.assetsignore` でCloudflareへの配信から除外）。多くは GitHub Actions から定期実行され、結果をissueに書き出す。
依存は、特記の無いものは標準ライブラリのみ（実行例: `python3 scripts/check_image_links.py --json result.json`）。

- `check_image_links.py` — `jpml_pros.html` 内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎週月曜03:00 JST）
- `check_ron2_images.py` — 龍龍(ron2.jp)側の現在の画像と `jpml_pros.html` に埋め込み済みの画像が一致しているか確認（毎週月曜04:00 JST）
- `collect_ron2_images.py` — 龍龍から全選手の150x150画像URLを収集しCSV出力（スプレッドシート更新用、手動実行）
- `collect_saikyo_images.py` — 最強戦の選手写真（「プロ」J列・「連盟プロ以外」X画像URL、#384）で取得できなくなった画像URLを見つけ、Xハンドルから現在のURLを解決してCSV出力（#333、手動実行＋`check-image-links.yml`から週1で`--json`実行、ヘッドレスChromiumが必要）。生成時に全件は解決しない。使い方と理由は`docs/notes/saikyo-page-design.md`「選手写真の更新」
- `cleanup_logs.py` — `docs/logs/`の作業ログのうち、7日を過ぎて片付けてよいものを削除し、条件外のものを一覧にする（`cleanup-logs.yml`から週1で実行、`--dry-run`で一覧のみ）。条件は`docs/notes/branch-operations.md`「作業ログの寿命」
- `delete_merged_branches.py` — マージ済み（`origin/cloudflare` の祖先）で先頭が24時間より前の `work/*` を削除し、ブランチ名と先頭の SHA を出力する（`delete-merged-branches.yml`から毎日、`--dry-run`で一覧のみ。完全な履歴のクローンが要る）
- `check_meibo.py` — 連盟員名簿データ（`lib/meibo.py`）と「プロ」シートの在籍者を登録名で突き合わせ、名簿のみ・プロのみを一覧にする（#370、`check-meibo.yml`から週1、生成は止めない）。テストは CLAUDE.md「判断・作業の原則」
- `regenerate.py` — ページ再生成の共通入口。`scripts/generate_<ページ名>.py`が存在するページを「生成対象」とみなす。`--list`で対象ページ一覧、`all`で全ページ再生成、ページ名指定で単体再生成、`--changed`で変更ファイルから対象判定（`regenerate-page.yml`が使用）
- `apply_page_meta.py` — 全ページの`<title>`・meta description・OGPタグを一括書き換え（#5）。`--dry`でプレビューのみ
- `check_leagues_dropped.py` — 型C（`houou_leagues` / `ouka_leagues`）で、リーグの実データがあるのに選手選択リストから漏れている選手を検知（#168、手動実行）。**出力は警告ではなく参考情報。退会者が並ぶのは正常で、在籍中の選手が現れたときだけ「プロ」シートの入力漏れを疑う。**`generate_*_leagues.py` から定数と `period_of()` / `fill_front_half()` をimportし、集計は `lib/leagues.py` を生成時と同じ引数で呼ぶ。生成側で `build_player_series()` の引数を変えたときはこちらも直すこと。`.github/workflows/check-leagues-dropped.yml` からworkflow_dispatchで実行でき、結果を実行サマリと指定issueへのコメントに出す
- `build_ogp_image.py` — OGP画像 `img/ogp.png`（1200×630、背景#ffffff、「ryoei.pro」の文字のみ）を生成（#78、手動実行）。Pillowが必要。全ページ共通の1枚で、`lib/page.py` / `generate_jpml_pros.py` のテンプレートと静的ページに `og:image` として入っている。生成したPNGもコミットする（生成環境のフォント差で再生成のたびに差分が出るのを避けるため）。`--check`でコミット済みのPNGと一致するか確認できる。`--text` と `--out` で任意の文字列・出力先の1枚も作れる（#343）。和文は `~/.local/share/fonts/NotoSansJP-Bold.otf`（`OGP_FONT`で差し替え可、無ければ失敗）を使い、横幅1040pxに収まるまで文字を小さくする。段を重ねるときは `--text` を繰り返し、`--scale`（1段目に対する比）・`--color`・`--max-size`・`--tracking`・`--line-gap` で調整する。背景は `--bg`（既定#ffffff）。最強戦の年度ページのコマンドは `docs/notes/saikyo-page-design.md`（`<年度>-black.png`、#343）にあり、年度が増えたときはこれを1回実行して生成したPNGをコミットする。**引数なしの `--check` は、コミット時と環境のフォントが違うと、スクリプトを変える前から一致しない**（2026-09-16 の Codespace では FONT_CANDIDATES のうち DejaVuSans が使われ、変更前のスクリプトでも一致しなかった）。スクリプトを変えたときの回帰確認は、変更前後のスクリプトの `render()` の出力同士を比べる（CHAT-0916-SY-07/08）

- `build_wayhome_ogp.py` — 「帰り道」一覧ページのOGP画像`img/ogp/wayhome/index-<最新話の公開日 YYYYMMDD>.jpg`（1200×630、JPEG品質88）を書き出す（#339、手動実行）。必要なのはPillowだけ（文字を描かないためフォントは要らない）。最新4本のサムネイルを2×2に並べ、切り抜かずに収める（全体が16:9なので高さが先に埋まり、左右の余りは`--mj-v-bg`=#121212のまま。サムネイル同士の区切りは8px）。実行のたびに作り直すが、最新4本が同じなら同じ名前・同じJPEGになり差分は出ない。ほかの`index*.jpg`は削除する。同じ名前で中身が変わる場合（同じ公開日の回が追加されたとき）は上書きせずエラーで止まる。生成したJPEGもコミットする。個別ページの og:image はYouTubeのサムネイルのまま。**ページ別のOGP画像は `img/ogp/<区分>/` 以下に置く。共通の `img/ogp.png` は動かさない。** 新しい回を足したときの手順は`docs/notes/video-wayhome.md`「新しい回を追加する手順」、Actionsでの自動化は#340

### #7 の期待値

2026-09-11、Lighthouse実測を受けて修正した。

- **「移行済みだから速い」は成り立たない。データ件数（DOM要素数）に依存する。** 件数の少ないページは改善し、多いページ（`jpml_pros` 等）は移行しても解決しない
- 件数の多いページの根本解決は表示件数を絞ること（#24の五十音タブ、新サイトで対応）
- #7 の目的は外部ドメイン依存の解消（#9 の前提）とインラインハンドラの排除でもあるため、件数に関わらず続ける
- DOM規模の最大の懸念は `houou_results`（15,416行。自前の `row.hidden` 方式に置き換えると全行がDOMに乗る）
- 実測値・行数調査は `docs/lighthouse-baseline.md`、経緯の全文は `docs/notes/handover-archive-2026.md`

### 型Aの表の方針

列ヘッダのソートは `jpml_pros.html` 専用（他ページは必要と判断したときだけ追加）。2列ページは `.mj-table-2col`、3列の `video_mtsuku` は `.mj-table-3col`。経緯は `docs/notes/handover-archive-2026.md`

