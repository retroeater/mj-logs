# 連盟プロの新ページ `pro/`（トップ・選手ごとの個別ページ）

`jpml_pros.html` の後継（#219。未公開で、公開は #641）。決定は docs/decisions/pros.md の 2026-10-30・31。
選手ごとの URL・title・description を持ち、中身を HTML に焼き込む（players/ のように JS で描かない。#219 の N2・N3）。
見た目と部品は houou/ のもの（docs/notes/houou-top.md「共通」「選手を探す部品」、値は docs/notes/design.md）。

## 構成

| URL | 中身 |
|---|---|
| `pro/` | h1「連盟プロ」・選手を探す部品（候補を押すと個別ページ）・切り口のタブ（五十音・支部・入会期・リーグ）と値・在籍者の表（名前・支部·期・鳳凰・桜花。行全体が個別ページへのリンク、見出しで並べ替え）。既定は「あ」行 |
| `pro/<slug>/` | パンくず・名前の面・いま・成績・放送対局・関連する選手・質問欄（下の「個別ページ」） |

- slug（2026-10-30・31 の決定）: `<名>.<姓>.<入会期>`（名・姓は名簿の「【2】値貼付」の英字、小文字）。英字名の無い選手（芸名1語）だけ `<姓>.<入会期>`。語の間の空白・「-」は「-」1つ（例 `jun-young.lee.40`）。英字・数字・「-」以外が残る・英字姓か入会期が無い・重複するときは生成を止める（`slug_problems()`、全件に当てる）
- 切り口の状態は URL のクエリ: `?kana=か`・`?place=東京`・`?joined=30`・`?league=C2`・`?all=1`（`&by=<切り口>`）。既定（あ行）はクエリなし
- `_redirects`: `/pro` → `/pro/`（301）・`/pro/` と `/pro/:slug/` → `index.html`（200）

## 個別ページ

上から。該当の無い面・行は出さない。

1. パンくず「連盟プロ / <名前>」
2. 名前の面: X の画像（48px の丸。無ければ頭の1字）・名前（28px の h1）・読みとローマ字（13px 灰色）・支部と入会期のチップ・誕生日（「3月5日」）・SNS（X・note・YouTube）・「リンクをコピー」（`assets/pro.js`、`navigator.clipboard`）
3. いま: 鳳凰戦・女流桜花の現在のリーグ（各大会の `ask-players.json` の値＝個人成績の名前の面のチップ）と期（進行中の期に出ていればその期）、終わった期の結果（成績 JSON の最後の行。「43期前期 20位（最終節 ▲45.7）」、2026-10-31 の決定 B。進行中の期の節の速報は #223 の後）
4. 成績: リーグ戦・大会（鳳凰戦・女流桜花・WRC・WRC-R・最強戦。要約の値は各ページの名前の面と同じで、書き方は「最高 ◯ · 出場 N期」〈最強戦は「出場 N回」〉にそろえる。決定 A）とタイトル戦の決勝（`title/search.json` の大会ごとの優勝・決勝の回数）。行は各大会の個人成績（`?name=`）・`/title/?q=` へ
5. 放送対局: `live/index.html` のカードの検索語（対局者・実況・解説の現在名と放送時点の名前）に名前を含むカード（live/ の検索 `?q=` と同じ部分一致、決定 K3）を、新しい順に3件と「すべて（N件）」（`/live/?q=`）
6. 関連する選手: 同期・同じ支部・同じリーグ（鳳凰戦の現在のリーグ）をそれぞれ5人まで（ソートキーの順）と「ほか N名」（`pro/` の切り口へ）
7. 質問欄: houou/ の部品。送る文の先頭に「<名前>について: 」を付けて鳳凰戦のデータで答える（`data-ask-prefix`。Worker は変えていない）

- title「<名前> | 連盟プロ | ryoei.pro」、description は要約から作る。構造化データは `Person`（name・alternateName〈読み〉・affiliation・sameAs・url）と `BreadcrumbList`。canonical は付けない
- OGP は選手別の画像（docs/notes/ogp.md「選手（pro/）」）

## データと生成

- `scripts/generate_pro_pages.py`。在籍者（「プロ」の「表示」が Y）・読み・英字・支部・入会期・誕生日はシートから（`lib/sns.fetch_pros()`・`lib/meibo.py`）。大会ごとの値は各ページの生成物から読む（houou/・ouka/・jwrc/・wrcr/ の `ask-players.json`・`results/`・`players/index.html` の `houou_current`・`houou_upcoming`、`saikyo/ask-players.json`、`title/search.json`、`live/index.html`）。そのため `regenerate.py` は `pro_pages` を最後に作る（`RUN_LAST`）
- 出力は `pro/`（`index.html`・`search.json`・`<slug>/index.html`）と `data/pro_ogp.json`。在籍者でなくなった選手のディレクトリは消す
- 検査は `scripts/tests/test_pro_pages.py`（slug の規則・値の書き方・放送対局のカードの読み取り）
- コンテナには鍵が無いので、生成・検査は `regenerate-page.yml` を作業ブランチで手動実行する（target_page `pro_pages`）
