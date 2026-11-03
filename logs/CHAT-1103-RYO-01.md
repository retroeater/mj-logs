# CHAT-1103-RYO-01

- 着手日時: 2026-11-03
- 対象issue: （起票予定）
- ブランチ: work/1103-ryo
- 着手時HEAD: c1bc06de7

## 指示

【Claude作成】Claude Code 向け指示：「良栄」のトップ r/ を新サイトの基本の形で自律開発で作る（現行トップの内容を日本語で移す。未公開・プレビューで止める） Chat-Ref: CHAT-1103-RYO-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-ryo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-ryo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
メニュー「良栄」のトップとして `r/`（URL は `/r/`）を新しく作る。中身は、いまサイトのトップ `index.html`（iPortfolio テンプレート）にある平野良栄の自己紹介・出演・経歴を日本語で移し、新サイトの基本の形（houou/・pro/ と同じ面・白地のナビ・幅 400px）で作る。進め方は自律開発（不明点は仮置きして最後まで作り、プレビューと仮置きの一覧で平野さんが確かめる）。 サイトのトップ `index.html` はこの系列では触らない（トップの作り直しは #101、別のチャット〈系列 TOP〉が担当）。この指示では公開せず、`navbar.js`・サイトマップ・`llms.txt`・既存ページのリンクも変えない。公開は次の指示（公開の issue）で行う。
決定（2026-11-03、平野さん）

* 「良栄」の新しいページを `r/` に作り、「良栄」のトップにする。いまのサイトのトップ（`index.html`）の内容を `r/` へ移す。サイトのトップ `index.html` の作り直しは別のチャットで行う
* `r/` は新サイトの基本の形で作り直す（テンプレートの演出〈タイピング・数字のカウントアップ・フェードイン・ライトボックス・絞り込み〉と `index.css`・`assets/vendor` のテンプレート用ライブラリは引き継がない）
* 現行トップの内容は取捨選択せず、すべて日本語で移す（自己紹介・Facts・出演7件・成績の入賞6件・学歴と所属・鳳凰戦のリーグの履歴）。無くすものがあればプレビューの後に平野さんが決める
* 鳳凰戦のリーグの履歴・現在のリーグなど、サイトに既にデータがある部分は生成スクリプトでデータから作る（pro/ の部品を流用）。自己紹介・出演・経歴の手書きの部分はスクリプト内の定数にする
* ページは1枚。面を縦に並べる（名前の面 → 成績への行〈成績・成績詳細・牌譜・鳳凰戦の個人成績〉 → 出演 → 経歴とリーグの履歴）
* メニュー「良栄」の既存3ページ（`rh_results.html`・`rh_results_detail.html`・`rh_paifu.html`）は `r/` からリンクし、URL は今のまま（URL の移動は別 issue）
* 公開はサイトのトップの作り直しを待たず、`r/` を先に公開する（公開は別の指示。公開時はナビの「良栄」を `/r/` へのリンクにしてドロップダウンをやめる。この指示では変えない）
* 進め方は自律開発

前提（チャット側。平野さんの決定ではない）

* 踏襲の元: 面・字の大きさ・色は houou/・pro/（docs/notes/design.md、docs/decisions/design-renewal.md）。個別ページの焼き込みの形と名前の面は pro/（`scripts/generate_pro_pages.py` の `face_html()`・`results_panel()`・`panel()`・`row_link()`、docs/notes/pro-pages.md）。新しいページの作法は docs/new-page-checklist.md「作る段の確かめ」「段1」。OGP は docs/notes/ogp.md
* 作り方の案: 生成スクリプト `scripts/generate_ryoei_pages.py`（名前は案。ページ名は `ryoei_pages`）で `r/index.html` を焼き込む。`scripts/regenerate.py` の対象に足す（出力先がディレクトリなので `OUTPUT_OVERRIDES`。houou/ の生成物〈`houou/results/<選手名>.json` 等〉を読むなら pro/ と同じく `RUN_LAST`）。手書きの部分（自己紹介の値・Facts・出演・入賞・学歴と所属）はスクリプト内の定数。共有の部品（`lib/page.py`・`style.css`・pro/ の関数）に手を入れるときは参照を洗い出し、全ページを再生成して差分を確かめる
* `_redirects`: `/r/ /r/index.html 200` と `/r /r/ 301` を saikyo/ の行と同じ形で足す（`html_handling: none`、docs/notes/cloudflare.md「配信設定」。既存の検査 `scripts/tests/test_saikyo_redirects.py` の形で `r/` の検査を1本足す）。`.github/workflows/check-assets.yml` の `allowed()` に `r` を足す（最上位の新しいディレクトリ、#331。コメントに「未公開。公開は #<公開の issue>」）
* 面の案（上から。該当の無い行は出さない）:
   1. パンくずは付けない（houou/ のトップと同じ。`r/` は「良栄」のトップ）
   2. 名前の面（pro/ の `face_html()` の形）: 写真は `img/profile-img.webp`（72px の丸。X の画像は使わない）、名前「平野良栄」（h1、28px）、読み「ひらの りょうえい · Ryoei Hirano」（13px 灰色）、チップは所属と入会期（pro/ の値。押すと pro/ の同じ値の一覧へ、ただし pro/ は未公開なので、この指示では押せないチップ〈リンクなし〉にし、公開の issue に「pro/ の公開後にリンクにする」と書く）、SNS の行は現行トップの5つ（X `retroeater`・Facebook `ryoei`・LinkedIn `ryoei`・GitHub `retroeater`・IMDb `nm14435079`。pro/ の `sns_icon()` に無いものは同じ形のアイコンを足す。外部ドメインへの `fetch`・スクリプトは足さない）
   3. 自己紹介の面（行の形「見出し 13px 灰色 · 値 16px」）: 生年月日「1977年8月29日」／所在地「東京」／専門「校正」／所属「日本プロ麻雀連盟（2014年9月〜）」／鳳凰戦「C2」（データから。pro/ の `houou_now()`）／段位「四段」（手書き。pro/ は段位を出さない決定〈2026-10-30〉だが、r/ は本人のページなので現行どおり出す。仮置き）
   4. Facts の面（現行の3つの数字）: 「子ども 4人（9〜18歳）」「IQ 143（WAIS-IV）」「連盟プロ N名（データベース）」。N は在籍者の数をデータから（pro/ のトップと同じ数）。現行の `data-purecounter-end` の値（4・143・1102）が元。カウントアップの演出は付けない
   5. 成績の面（押せる行、右端に ›）: 「成績」→ `/rh_results.html`、「成績詳細」→ `/rh_results_detail.html`、「牌譜」→ `/rh_paifu.html`、「鳳凰戦の個人成績」→ `/houou/players/?name=平野良栄`（`name_query()` の形）。1行の要約（「最高 C1 · 出場 N期」）は pro/ の `results_panel()` と同じ値
   6. 出演の面（7件、新しい順。現行の並びのまま。1件1行: サムネイル 40px の角丸・題 16px・2行目に媒体 13px 灰色・右端 ›。行全体が外部リンク）: 題は現行の `title` 属性の文言をそのまま使う（「第3期WRC-Rリーグ」「第82回　平野 良栄 | 今を輝く同窓生たち」「ASICS | 'Mind Games – The Experiment' Full Documentary」「マインドアスリート 彼らが挑む実験ドキュメンタリー」「平野良栄､満貫ツモ!!【麻雀最強戦2018】」「【世界リーチ麻雀選手権2017インタビュー】土屋 幸弘プロ…」「第29期新人王戦」。長い題は実物から全文を写す）。リンク先・サムネイルの URL は現行の `index.html` の `portfolio-container` の値をそのまま（YouTube・icualumni.com・Amazon・`img/Mind_Games_…webp`。サムネイルは既存の外部ドメインで CSP の `img-src https:` の範囲）。ライトボックスは付けない
   7. 経歴の面: 入賞6件（「特昇リーグ 4位 · 2024年12月」「WRC-Rリーグ 4位 · 2024年6月」「世界リーチ麻雀選手権 110位 · 2017年10月」「特昇リーグ 3位 · 2017年7月」「チャンピオンズリーグ 4位 · 2015年9月」「新人王戦 2位 · 2015年8月」）、学歴と所属4件（「JAPAN MENSA · 2022年6月〜」「日本プロ麻雀連盟 · 2014年9月〜」「国際基督教大学 · 1997年4月〜2001年3月」「開成中学校・高等学校 · 1990年4月〜1996年3月」）。英語の原文「Tokusho League」の訳「特昇リーグ」・「Rookie Game」の訳「新人王戦」は仮置き
   8. 鳳凰戦のリーグの履歴の面: データから（houou/ の期ごとのリーグ。現行の8行〈D3 2014-09〜 … C2 2026-04〜〉と突き合わせ、食い違う期があれば行ごとに「判断が必要なこと」に書く）。見せ方は pro/ の個人成績への行で足りるなら面を置かず、リーグのタブ（`houou/players/?name=…&view=league`）へのリンクで代える（仮置き: 面を置かず行で代える）
   * title「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」（現行トップと同じ。公開のときにトップと重ならない形へ直すかは公開の issue に書く）、h1「平野良栄」、description は現行トップの meta description を写す。og:image は `img/ogp/site/index-white.png`（現行トップと同じ。`r/` 専用の画像は作らない）。構造化データは `Person`（pro/ の個別ページと同じ形）＋ `BreadcrumbList` は付けない。`noindex`（公開まで）。canonical は付けない（#113）
   * 文字の大きさは4段（28/16/13/12px）、面・チップの色は houou/・pro/ の値。暗い配色も pro/ と同じ扱い。共通ナビは `navbar.js` が `/pro/` のパスで白地の新しい形にするのと同じく `/r/` でも新しい形にする（`navbar.js` の対象パスの一覧に `/r/` を足す1行だけ。メニューの項目は変えない）
* 平野さんが確かめる端の状態: 360px・390px の明暗、出演の題が2行に折り返す行、チップの無い状態（リンクなし）、houou/ のデータが無いときの成績の面（行ごと出さない）
* 検査: `python3 -m unittest discover -s scripts/tests`（`_redirects` の `r/` の検査と、手書きの定数の件数〈出演7・入賞6・学歴と所属4〉の検査を1本足す）、`python3 scripts/regenerate.py --check ryoei_pages`、`scripts/check_asset_limits.py`
* 画面の確かめ: 360px・390px の明暗で `r/` を headless Chrome で撮り、`docs/logs/CHAT-1103-RYO-01/` に置く。はみ出しと重なりを数えて報告に書く。ログに「この版で見えるはずの印」を1つ書く（チャット側が別名 URL で版を確かめるため）
* 同じ論点の issue: `r/` を作る issue は無い見込み（要確認）。無ければ起票する（題は「『良栄』のトップ r/ を作る（現行トップの内容を移す）」、ラベルは `分野: UI/UX`。対象のラベルは `対象: index` を付けず、無ければ付けずに報告に書く）。公開の issue も無ければ起票する（docs/new-page-checklist.md「段1」。内容: noindex を外す・`navbar.js` の「良栄」を `/r/` へのリンクにしてドロップダウンをやめる〈平野さんの決定〉・`sitemap-pages.xml`・`llms.txt`・チップのリンク〈pro/ の公開 #641 の後〉・title の重なり・`r/` と `index.html` の内容の重複の解消は #101 側）。#389（ポートフォリオ「校正」、`対象: index`）には「校正の一覧の置き場は r/ の面になる見込み」と1行コメントする（本文・状態・ラベルは変えない）
* 仮置きにした点はすべて「判断が必要なこと」に、画面のどこに効くかと一緒に一覧で書く（平野さんが1問1答で確定する）
* 決定の記録先: `docs/decisions/` に「良栄」の分野のファイルが無ければ `ryoei.md`（分野「良栄（r/・成績・成績詳細・牌譜、#389）」）を作り、README の表に行を足す

手順

1. 同じ論点の open issue（「良栄」「r/」「トップページ」「プロフィール」「ポートフォリオ」）をクローズ済みも含めて検索し、無ければ上の案で作る issue と公開の issue を起票して着手中コメントを書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）と、上の「前提」の踏襲の元（pro/ の関数名・`regenerate.py` の `OUTPUT_OVERRIDES`・`RUN_LAST`・`check-assets.yml` の `allowed()`・現行 `index.html` の値）を実物で確かめ、食い違えば止まる。現行 `index.html` から移す文言・リンク・数値を全件、`## 経過` に表で書く
2. 生成スクリプト・定数・CSS（要れば）・`_redirects`・`check-assets.yml`・検査を作り、`r/index.html` を生成する。`navbar.js` は新しい形の対象パスに `/r/` を足す1行だけ変える。`docs/notes/static-generation.md`「ページの一覧」に `r/` の行（「noindex・メニュー未掲載、公開は #<番号>」）を足す
3. 検査と画面の確かめを流し、仮置きの一覧と、公開の指示で要る変更の一覧（上の公開の issue の内容）をログに書く。ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 前提が実物と食い違い、決定（上の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
* 同じ目的の issue か未マージの work/ ブランチに `r/` を作る作業がある
* テスト・`--check`・`check_asset_limits.py` が通らない（原因を書いて止まる）
* 未マージの work/ ブランチが `scripts/regenerate.py`・`scripts/generate_pro_pages.py`・`scripts/lib/page.py`・`style.css`・`navbar.js`・`_redirects`・`.github/workflows/check-assets.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* 他セッションの着手中コメントが、作る issue（起票したもの）・#389 にある
* 新しい依存（npm・pip・CDN・外部 API）が要るとき（足さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo/docs/logs/CHAT-1103-RYO-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c1bc06de）: https://github.com/retroeater/mj-logs/tree/main/guide/c1bc06de

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c1bc06de.md
