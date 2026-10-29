# CHAT-1029-SEO-01

- 着手日時: 2026-10-29
- 対象issue: #486（コメントのみ）。棚卸しの issue は読むだけ
- ブランチ: work/1029-seo
- 着手時HEAD: 74d7dc08f

## 指示

【Claude作成】Claude Code 向け指示：ryoei.pro の SEO/AIO を、観点を分けたサブエージェント3人（読み取り専用）で包括的にレビューし、既存の issue の棚卸しとあわせて指摘の一覧をログに残す
Chat-Ref: CHAT-1029-SEO-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（新しいセッションを推奨。読む量が多いため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
ryoei.pro の SEO/AIO（検索エンジン・AI 検索での見つかりやすさ）を、技術・ページの中身・AIO の3つの観点で包括的にレビューし、現行サイトで直す小さな指摘と、新サイト（#296）の要件の候補を洗い出す。あわせて、SEO/AIO の既存の issue と、検討したのに issue になっていない点（下の B1〜B7）が今も当てはまるかを本番と実物で確かめ、古くなった issue の整理案を出す。この指示では何も直さず、issue の本文・状態・ラベルも変えない（例外は手順1の #486 へのコメントだけ）。直すもの・起票するもの・整理するものは平野さんが一覧から番号で選び、別の指示で行う。

### 決定（2026-10-29、平野さん）
- SEO/AIO の包括的なレビューを、観点で分けたサブエージェント3人（相互検証あり）で行う
- 目的は、現行サイトで直す小さいものと、新サイト（#296）の要件出しの両方
- 範囲は公開中の全ページ。books/（凍結中）と廃止予定のページは除く。技術 SEO は全ページを機械的に測り、ページの中身はページの種類ごとに代表を選んで読む
- 観点: ①技術 SEO（クロール・インデックス）②ページの中身（title・h1・description・OGP・内部リンク）③AIO（llms.txt・AI クローラー・説明文・構造化データ）
- 既存の issue と、issue になっていない点（B1〜B7）をレビューに入れ、本番で今も残っているかを測る。古い issue の整理案（閉じる・統合・題の直し）も結果に含める
- すぐ実施する。Search Console の数値は docs/gsc/ の記録（最新は 2026-10-09 の取得の見込み、要確認）を使い、11/1 の月次取得は待たない

### 前提（チャット側。平野さんの決定ではない）
- Bing の Recommendations（2026-10-29、平野さんのスマホの画面。「Top Recommendations」の上位5件。ページ数は画面に出ていない）: (1) IndexNow を設定せよ（赤）(2) meta description が短いページが多い (3) page title が短いページが多い (4) 中身（コンテンツ）の少ないページが多すぎる（SEO/GEO）(5) 質の高いドメインからの被リンクが足りない。h1 の無いページの指摘は上位5件には出ていない（全体の一覧から消えたかは画面からは分からない）。(1)(5) は対応しないと決まっている（docs/decisions/seo-bing.md BNG-03・04、#126）。(2)(3) は #5、(4) は #486 の受け皿
- canonical の原則は「付けない」（#113）。例外の基準は「`?name=` などのクエリで中身が変わる URL を持たず、Search Console にも `?` 付きの URL が出ていないページには付けてよい」（#113 の 2026-10-13 のコメント、#560）。saikyo/players/ には付けないと決まっている（#638、2026-10-28）。設定と経緯は docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」
- AI クローラーの扱いは docs/notes/cloudflare.md「AIクローラーの扱い（現在の設定）」（「Search・Agent は許可」、Training は Disallow）。robots.txt は AI Crawl Control が管理の内容を前置する（同文書）。**User-Agent を AI クローラーに偽った計測はしない**（検証済みのボットは IP で判定されるため、偽った結果は当てにならない。#554 の原因は、記録だけから分かる範囲を書く）
- OGP の規則は docs/notes/ogp.md「ページの種類ごとの og:image・og:type の規則」と docs/decisions/seo-bing.md（OGR の節）・docs/decisions/houou.md（WRC・WRC-R の画像）。動画のページの VideoObject は外す方向で決まっている（#620・#629）
- 新しいページは未公開（noindex）で入れ、公開の issue で llms.txt・navbar・サイトマップに載せる（CLAUDE.md、docs/new-page-checklist.md）。未公開のページ（要確認: study/〈#576〉・live/〈#362〉・log/〈#640〉など）は「公開のときの要件」として見るだけで、載っていないことを指摘にしない
- 「やらない」と決まっていること（再提案しない）: IndexNow の自前送信・被リンク対策（#126）、title を 50〜60 字にそろえる・404.html への description（BNG-04）、AI Labyrinth（#447）、AI ボットの Training を Block にする、twitter:player（#329）、動画のページへのプレーヤーの埋め込み（#620・#629）。これらに当たる指摘は「決定済み（対応しない）」と区分して1行で済ませる
- 「現行サイトに作り込みすぎない」（docs/handover.md 4章）。構造化データ（#13）・選手個別ページ（#219）・`?name=` からの 301（#159）・情報設計（#160）は新サイト送り。これらに当たる指摘は「新サイトの要件」に区分し、#296 の sub-issue の番号を添える
- 前回のレビュー（CHAT-1028-AGT-02）では、文字列の一致だけを根拠にした「重複」の指摘が17件中3件誤りだった。「重複」「不一致」の指摘には、両方の場所の役割を読んで比べたかを必ず書かせる
- 本番を読むときは URL に `?v=<未使用の値>` を付ける（古い版が返ることがあるため）。HTML の meta・リンク・ステータスは確率で揺れないので、1回の取得でよい（Core Web Vitals などの計測はこの指示では行わない）
- 読む量を絞る（前回は文書の読み込みだけで1人約20万トークン）。docs/logs・docs/notes/handover-archive-2026.md は読まない。大きい文書（docs/notes/cloudflare.md・saikyo-page-design.md など）は該当の節だけ grep で読む

### 棚卸し（チャット側が 2026-10-29 に issue の REST・mj-logs のログと文書から作った。状態は要確認）
- Open の SEO/AIO の issue: #638・#560・#486・#5・#283・#229・#266・#264・#227・#335・#554・#458・#620（ラベル無し）。計測: #142・#460・#230・#465。新サイト送り・保留: #296・#101・#13・#159・#160・#165・#219・#226。待ち: #173・#195・#289。ほか: #275・#282。SEO に効くラベル無しの issue: #532・#246・#362・#576・#640・#568・#304・#555・#529
- issue になっていない点:
  - B1: live/ は「トップを含む全ページに canonical を付ける」と決まっている（docs/decisions/live.md の LVP の節、未実装）。live/ のトップは `?q=` で中身が変わるので、#113 の例外の基準と食い違う（#638 と同じ型）
  - B2: live/ の動画の構造化データは「公開の後に別に扱う」のまま（同）。#629 の決定（各話から VideoObject を外す）を live/ に当てはめるかが未決
  - B3: 最強戦の旧年度ページの `?match=` を転送するルールを平野さんが作り、チャット側が後で確かめる、とされた（CHAT-1016-SKR-08、docs/notes/redirects.md）が、作った・確かめた記録が見つからない
  - B4: 辞書ページの h1「リソース 辞書」を #377 の作り直しで見直す予定だった（docs/decisions/seo-bing.md RVW-08）。#377 は閉じたが見直しの記録が見つからない
  - B5: 帰り道の一覧の /wayhome/ への移動（#559）と rh_links.html の廃止（#531）で llms.txt を直したかが、ログからは分からない
  - B6: WRC・WRC-R の文字の OGP 画像は決定済み（docs/decisions/houou.md）だが、docs/notes/ogp.md の規則の表が古い
  - B7: live/ のトップ・大会・期の og:image を最新話のサムネイルにする件は #362 に移したが、#362 の題から読めない
- 中身が古くなっていそうな issue: #266（Google Charts を読むページが0になり前提が消えた見込み）、#173（題の VideoObject が #629 と合わない）、#246 と #532（どちらも 404.html の資産パスで重複に見える）、#486（h1 の無かったランキング3ページは #582 で消えた）、#13・#165（現行サイトに BreadcrumbList・ItemList が入り、OGP の規則も #581 で決まった）

## 手順
1. 準備: (a) 対象のページの一覧を作る。`python3 scripts/regenerate.py --list`・`OUTPUT_OVERRIDES`・sitemap（`sitemap.xml` と配下）・リポジトリの HTML から、公開中（noindex 無し）のページを、ページの種類（トップ階層・houou/ などの大会・title/・saikyo/ の年度／対局／卓／players など・wayhome/ などの動画・静的ページ）ごとに件数で表にし、除いたもの（books/・廃止予定〈docs/decisions/site-review.md〉・未公開）を理由つきで書く。種類ごとに中身を読む代表のページ（1〜3件）を選び、本番 URL を全件ログに書く。(b) 上の「棚卸し」の issue の本文と最近のコメントを GitHub MCP の読み取りで確かめ、状態が棚卸しと違うものをログに書く（読むだけで副作用が無いため）。(c) #486 に、上の「前提」の Bing の Recommendations（2026-10-29）を、出典「平野さんの画面の申告（CHAT-1029-SEO-01）」としてコメントする（本文・状態・ラベルは変えない）
2. サブエージェント3人（モデル Sonnet 5.5、読み取り専用: ファイルの作成・編集、git コマンド、issue の操作をしない。Read・Grep・Glob と読み取りの Bash、`curl` と headless Chrome〈Playwright〉での本番の取得だけ。スクリプトは scratchpad に置き、コミットしない。スクリーンショットはコミットしない）を起動する。担当と読む文書:
   - ①技術 SEO: robots.txt（本番の実物）、sitemap（インデックスと配下の well-formed・lastmod〈#335〉・載っている URL が全件 200 で転送も noindex も無いか〈`curl -I`、同時4本まで〉・公開中なのに載っていないページ）、canonical（ページの種類ごとの有無と向き先を #113 の例外の基準・#560・#638・B1 と照らす）、noindex（公開中のページに残っていないか・未公開のページに付いているか）、転送（`_redirects` とダッシュボードのルール〈docs/notes/redirects.md の一覧〉の実際の応答・転送の連鎖・B3）、`.html` と拡張子なしの URL の扱い（html_handling）、www・workers.dev の扱い、404 の応答と 404.html の資産（#246・#532）、`<html lang>`（#264）。読む文書: docs/notes/cloudflare.md の上の3節・docs/notes/redirects.md・docs/notes/sitemap-lastmod.md・docs/decisions/site-review.md
   - ②ページの中身: 種類ごとの代表のページ（手順1）と、全ページの機械的な一覧（title・h1・meta description・og:title・og:description・og:image・og:type の文字数と有無、title の重複・h1 の無い／複数のページ）。短い title・短い description（Bing の (2)(3)、#5）、中身の少ないページ（Bing の (4)。本文の文字数を種類ごとに出し、少ないページを挙げる）、h1 と title の文言（#283・B4）、見出しの順、内部リンク（navbar・パンくず・サイト内のリンク切れ・どこからもリンクされていない公開ページ）、og:image が docs/notes/ogp.md と決定の規則どおりか（B6・B7）、画像の alt。Search Console の記録（docs/gsc/ の最新）から、表示が多いのにクリックの少ない着地先・`?name=` の着地を、ページの改善点の材料として使う。読む文書: docs/decisions/seo-bing.md・docs/notes/ogp.md・docs/notes/mj-lead.md・docs/gsc/README.md と最新の取得の要約
   - ③AIO: llms.txt（本番の実物と公開中のページの一覧の突き合わせ・件数の食い違い・B5・#227）、AI クローラーの扱い（robots.txt の前置の中身と docs の方針の照合。#554 は docs と issue の記録から分かる範囲で）、説明文（`.mj-lead` の有無と DOM 上の位置・大きい表の前に何 KB あるか、#229）、JS を動かさないと本文が出ないページ（`curl` の HTML と Chrome の描画後の本文の文字数の差）、構造化データ（ページの種類ごとの JSON-LD の型と、#620・#629・B2 の決定との食い違い・Search Console の警告の記録）、`<meta name="robots">`・`X-Robots-Tag` の AI 向けの指定の有無。読む文書: docs/decisions/aio.md・docs/notes/site-findings.md「SEO」・docs/notes/cloudflare.md「AIクローラーの扱い（現在の設定）」・docs/notes/mj-lead.md
   各指摘は「番号（①-1 など）／ページ（URL）またはファイル:行／内容／証拠（計測値・応答・コードの引用）／区分〈現行で直す小さいもの・新サイトの要件・決定済み（対応しない）・既存 issue の更新〈閉じる・統合・題や本文の直し〉・起票の候補〉／確かめ方〈機械的〈コマンド・計測〉・意味の照合をした・判断のみ〉／関係する issue・B の番号」の形で全件書く（「略」にしない）。上の「棚卸し」の issue と B1〜B7 は、担当の観点の人が全件について「今も当てはまる／解消済み／一部」を証拠つきで判定する
   相互検証: ①の指摘を②が、②を③が、③を①が読み、指摘ごとに「同意／異議（理由）／機械的に確認した」を返す。サブエージェントどうしは SendMessage で直接やり取りしてよい（CHAT-1028-AGT-02・LPR-01 で使えた）。使えなければリーダー経由で渡す。異議が残った指摘は両方の言い分を残す。終わったら3人を止める
3. ログの `## 経過` に、ページの種類ごとの件数と代表のページの表、棚卸しの issue と B1〜B7 の判定の表（全件）、指摘の一覧（全件。番号・場所・内容・証拠・区分・確かめ方・検証者・判定・issue の表）、実測（所要時間、3人のモデル名とトークン〈取れる範囲〉、相互検証の経路、本番へのリクエストの数、指示に反してファイルを変えた・git を使った事象の有無、権限の確認が出た回数）を書く。`## 報告` の「判断が必要なこと」には、判定が「同意」か「機械的に確認」の指摘を、区分ごと（現行で直す小さいもの／新サイトの要件／既存 issue の更新／起票の候補）に番号を振ってまとめる（平野さんが番号で選べる形。起票の候補には、同じ目的の既存の issue〈クローズ済みを含む。#529・#296 の sub-issue など〉を探した結果を添える）。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイル（docs/decisions/seo-bing.md か aio.md）に足し、ログを仕上げて `git push origin work/1029-seo:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 手順1で sitemap または `regenerate.py --list` が読めない（ページの一覧が作れない）
- 手順1(b)で、棚卸しの issue のうち、この指示と同じ目的（SEO/AIO の包括的なレビュー）の着手中の issue・他のセッションの着手中のコメントが見つかった
- 手順2で3人のうち1人でも起動できない、または同じエラーが3回続いた（別の手段を試さずに止まる）
- 手順2の後の `git status --porcelain` に docs/logs/CHAT-1029-SEO-01.md 以外の変更がある（戻さずに止まる。`git checkout .` は使わない）
- 本番に `curl` も headless Chrome も届かない（届かない状態をログに書いて止まる。片方だけ届くなら、届く方で進め、測れなかった項目を書く）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: なし。識別子 SEO の重複（全ブランチのコミットと docs/logs の履歴）: なし
- 作業ブランチ: work/1029-seo はローカル・リモートとも無かったので `git checkout -b work/1029-seo origin/cloudflare`（着手時 HEAD 74d7dc08f）
- 本番: `curl https://ryoei.pro/?v=seo01a` が 200。headless Chrome（Playwright）は読み込める
- 0章ゲート: 実装を含まない指示なので対象外。指示文の issue の state: コメントの対象 #486 は open

### 手順1(a) 対象のページの一覧

`python3 scripts/regenerate.py --list`（30件）・`OUTPUT_OVERRIDES`・sitemap（`sitemap.xml` 配下の6ファイル）・リポジトリの HTML（docs・scripts・workers・assets などを除く全 .html）から作った。
HTML のファイル数（noindex の数）: ルート11（1）・books 191（191）・exam 13（0）・houou 13（1）・jwrc 12（0）・live 923（923）・mtsuku 62（0）・ouka 12（1）・saikyo 891（0）・study 41（41）・title 384（0）・wayhome 44（0）・wrcr 12（0）。

| 種類 | 件数 | 代表（本番 URL） |
|---|---|---|
| トップ階層（index・型A/A'/D・辞書） | 9 | https://ryoei.pro/ ・ https://ryoei.pro/jpml_pros.html ・ https://ryoei.pro/resource_dictionary.html |
| 大会トップ（houou/ouka/jwrc/wrcr） | 4 | https://ryoei.pro/houou/ |
| 大会の個人成績・順位変動（*/players/・*/race/） | 8 | https://ryoei.pro/houou/players/ ・ https://ryoei.pro/jwrc/race/ |
| 大会ランキング（*/ranking/*/） | 35 | https://ryoei.pro/houou/ranking/total/ |
| title/ 入口 | 1 | https://ryoei.pro/title/ |
| title/ 大会ページ | 20 | https://ryoei.pro/title/houou/ |
| title/ 期ページ | 363 | https://ryoei.pro/title/houou/42.html ・ https://ryoei.pro/title/ourai/12.html |
| saikyo/ トップ | 1 | https://ryoei.pro/saikyo/ |
| saikyo/ vs・years・players | 3 | https://ryoei.pro/saikyo/vs/ ・ https://ryoei.pro/saikyo/players/ |
| saikyo/ ランキング | 6 | https://ryoei.pro/saikyo/ranking/champion/ |
| saikyo/ 年度 | 16 | https://ryoei.pro/saikyo/2024/ |
| saikyo/ 対局 | 205 | https://ryoei.pro/saikyo/2024/20240317.html |
| saikyo/ 卓 | 660 | https://ryoei.pro/saikyo/2024/20240317-s1-a.html |
| 動画の一覧（wayhome/mtsuku/exam） | 3 | https://ryoei.pro/wayhome/ ・ https://ryoei.pro/mtsuku/ |
| 動画の各話（wayhome/mtsuku/exam） | 116 | https://ryoei.pro/wayhome/aNSsJicbMlM.html ・ https://ryoei.pro/mtsuku/ZeFtFG2ov_Q.html ・ https://ryoei.pro/exam/-PoeE0Bm2sE.html |

除いたもの: books/（191、凍結・noindex）、saikyo_mens.html（廃止予定 #442、noindex 無し・sitemap 外）、404.html（noindex）、未公開（noindex）: live/（923、#362）・study/（41、#576）・houou/leagues/・ouka/leagues/（転送だけのページ）。log/ はリポジトリにまだ無い（#639 対応中）。
公開中の1450件は sitemap（sitemap.xml 配下の6ファイル、books を除く）の <loc> と1対1（ファイルから導いた URL と突き合わせて差0）。
公開中 URL の全件: このファイルと同じ場所の public_urls.txt。種類ごとの全件: pages.json。

文書との食い違い（記録のみ。直さない）: `scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントは ouka/・jwrc/・wrcr/ を「未公開」と書くが、3つとも noindex が無く sitemap-pages.xml に載る（公開は #591・#599・#611）。docs/notes/static-generation.md「ページの一覧」の前文も ouka/ を「noindex・メニュー未掲載」と書くが、同じ表の行は「公開は #591」。

### 手順1(b) 棚卸しの issue の状態

`list_issues`（open 全217件）で、棚卸しの39件がすべて open であることを確かめた（状態は棚卸しと同じ）。ラベルの補足: #640 は「状況: 待ち」、#460 は「状況:」無し、#142 は「状況: 待ち」、#230・#465 は「状況: 保留」、#620 はラベル無し（棚卸しどおり）、#532・#246 は「分野: UI/UX」（SEO のラベルは無い）。本文と最近のコメントの要旨・着手中のコメントの有無は下（読み取り専用のサブエージェントに読ませた結果）。

### 手順1(c) #486 へのコメント

https://github.com/retroeater/mj/issues/486#issuecomment-6489063736
（Bing の Recommendations 上位5件、出典「平野さんの画面の申告（CHAT-1029-SEO-01）」。本文・状態・ラベルは変えていない）

### 手順2 サブエージェントの起動（2026-10-29T05:49Z）

3人とも general-purpose、モデル指定 sonnet（Sonnet 5.5）、背景で起動。共通の約束（読み取り専用・出力の形・前提・棚卸し）は scratchpad の common.md に書いて読ませた（使い捨て）。名前と担当: seo-tech（①技術 SEO）・seo-content（②ページの中身）・seo-aio（③AIO）。
棚卸しの割り当て: ① #638・#560・#335・#458・#264・#246・#532・#568・#465・#159・B1・B3／② #486・#5・#283・#142・#460・#165・#160・#219・#101・#195・#226・#282・#304・#529・B4・B6・B7／③ #229・#266・#227・#554・#620・#173・#13・#230・#289・#275・#362・#576・#640・#555・#296・B2・B5。

### 手順1(b) の結果（棚卸しの issue の本文と最近のコメント）

読み取り専用のサブエージェント（general-purpose、sonnet）に、39件の本文と全コメント、参照用に closed の #113・#629・#581・#582・#377・#559・#531 を読ませた。

- **止まる条件の確認: 「SEO/AIO の包括的なレビュー」の着手中の issue・着手中のコメントは無い。** → 止まらずに進めた
- 完了・中断のコメントが無いまま残る「着手中」（この指示の目的とは別。記録のみ）: #362 の CHAT-1016-LVP-01・02、CHAT-1017-LVP-04・06（LVP-06 は work/1017-lvp が判断待ち）、#304 の CHAT-1023-MCK-22（本文は直っている）
- 状態が棚卸しと違うもの: 39件とも open で、状態の違いは無い。中身が古い点は下の判定の表に入れた（#266・#173・#246/#532・#486・#13 など）
- 読ませた結果で、レビューの3人が判定しなかった補足（未検証。参考として残す）: #219 の要件11が指す `houou_race.js` の `playerUrl()` は #582 で消えた。#576 の「`VideoObject.uploadDate` を決める」論点は #629 で study/ の各話から VideoObject を外したので無くなった見込み。#13 の本文は帰り道の VideoObject の先行実装を前提にしたまま（#620・#629 で外した）

### 手順2 相互検証の経路

- 3人の最初の報告は、リーダーに返った（SubagentHandback）。リーダーが要約を scratchpad に置き（report-tech.md・report-content.md・report-aio.md、使い捨て）、SendMessage で検証役に渡した（①→②、②→③、③→①）。**サブエージェントどうしの直接のやり取りは使わず、リーダー経由**にした（3人の報告がそろってから順序をそろえて渡すため）
- 検証の返答もリーダーに返った。異議が残ったのは①-3 の1件（下の表）。①-7 は検証役が再現していない（同時アクセスを避けたため）
- 3人とも最後の返答の後は待機の状態（実行中の作業なし）で、追加の指示を送っていない。止める操作が要る実行中のタスクは無かった

### 指摘の一覧（全件）

検証者: ①の指摘は②（seo-content）、②の指摘は③（seo-aio）、③の指摘は①（seo-tech）。「判定」は検証者の返答。「リーダー」は私が追加で確かめたこと。

#### ①技術 SEO（seo-tech、検証: seo-content）

| 番号 | 場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue・B |
|---|---|---|---|---|---|---|---|
| ①-1 | index.html:2（https://ryoei.pro/） | トップだけ `<html lang="en">`。公開1450件で ja 1449・en 1 | 全件集計・本番も同じ | 現行で直す小さいもの（1行） | 機械的 | 同意（機械的に確認した） | #264 |
| ①-2 | https://ryoei.pro/saikyo/players/ | canonical が本番に残る（#638 の決定は付けない、未実装）。saikyo/ は全891件に自己 canonical | 本番＝リポジトリ | 既存 issue の更新（#638 の実装時に saikyo/ のトップ・ranking/・vs/・years/ も決める） | 機械的 | 同意（機械的に確認した） | #638・#560・#113 |
| ①-3 | 自己 canonical のあるもの: title/ 384・saikyo/ 891・mtsuku/ の各話61・exam/ の各話12・wayhome/ 44（一覧を含む）。他は無し | docs/notes/cloudflare.md:486「例外は帰り道の各話と一覧だけ」と実態が合わない。wayhome/ の一覧には有り、mtsuku/・exam/ の一覧には無い。title/・saikyo/ はクエリで中身が変わる（`assets/title.js` の `?year=`・`?q=`、saikyo.js の `?name=`・`?view=`）ので #113 の例外の基準に当たらない | 全件集計とコード | 既存 issue の更新（#560。外すか基準を直すかを決め、cloudflare.md を実態に直す） | 意味の照合をした | **異議あり（残った）**: ②「`?year=` を持つのは title/ の入口だけで、期ページ363・大会ページ20は基準に当てはまる可能性が高い。title/ 全体が当たらないは言い過ぎ」。リーダー: `assets/title.js:331` は `#title_filter`（入口・大会・期の全 title/ ページにある）で `?q=`・`?name=` を読んで絞り込みの結果を出し、`?year=`（:469）は `#title_year` のある入口だけ。①は `?q=`・`?name=` の分で正しく、②は `?year=` の分で正しい。期ページ・大会ページが基準に当たるかは「`?q=` の絞り込みを中身の変化とみなすか」と Search Console に `?` 付きの title/ の URL が出ているか次第で、平野さんの判断 | #560・#113・#638 |
| ①-4 | #560 本文の表 | 2026-10-13 時点のまま（saikyo 895→891、houou/ 等は公開済み、mtsuku/・exam/ が増えた） | 集計 | 既存 issue の更新（表を直す） | 機械的 | 同意（機械的に確認した） | #560 |
| ①-5 | docs/notes/redirects.md:95（「予定」の表） | `?match=` の Single Redirect ルールは作成済みで動くのに「予定」のまま | `/saikyo/2024.html?match=20240317` → 301 → `/saikyo/2024/20240317.html`（1ホップ）。`?match=zzz` は 301 → 404（docs/decisions/saikyo.md:125 どおり） | 既存 issue の更新（docs。この指示では直さない） | 機械的 | 同意（機械的に確認した）。**補足（②が見つけた）**: `?match=20240317&utm_source=x` は `/saikyo/2024/20240317&utm_source=x.html`（存在しない URL）へ 301。リーダーも本番で同じ応答を確かめた | B3・#549・#568 |
| ①-6 | docs/notes/cloudflare.md:506 | 「HTML には ETag も Last-Modified も付かない」が古い。本番の HTML に ETag が付く（Last-Modified は付かない） | 本番のヘッダ | 既存 issue の更新（docs。この指示では直さない） | 機械的 | 同意（機械的に確認した。ETag の記述だけ古い） | #449・#258 |
| ①-7 | 本番のレート制限（#124） | 1450件の HEAD を同時4本で取ると約半数が 429（683件）、同時2本で172件、逐次0件。docs は「Managed Challenge、30回/分」だが応答は 429。未検証のボットの大量取得は 429 になり得る | scratchpad の head_all.tsv（使い捨て） | 判断のみ | 機械的 | 判断できない（②は同時アクセスを避け再現していない） | #124 |
| ①-8 | 末尾スラッシュの無い URL | redirects.md に載る分（`/saikyo/players` など）は 301 で1ホップ。載らない `/houou/players`・`/houou/ranking/total`・`/jwrc/race`・`/ouka/players`・`/wrcr/race`・`/saikyo/ranking/champion`・`/title/houou/42` は 404 | curl -I | 判断のみ（html_handling は none。サイト内リンクはスラッシュ付き） | 機械的 | 同意（機械的に確認した） | #89 |
| ①-9 | `/index.html`・`/saikyo/index.html`・`/houou/players/index.html` など | `index.html` 付きの別名が 200。canonical の無いトップ・houou/ouka/jwrc/wrcr は二重 URL（サイト内リンク・sitemap はスラッシュ側だけ） | curl | 判断のみ（#560 の観点に当たる） | 機械的 | 同意（機械的に確認した） | #560 |
| ①-10 | https://ryoei.pro/sitemap-books.xml | インデックスに載らないが本番 200 で、noindex の books/ 191件を載せる | 本番 | 判断のみ（`.assetsignore` に入れる／そのまま） | 機械的 | 同意（機械的に確認した） | #97・#429 |
| ①-11 | 本番 robots.txt | 管理部分の前置は仕様どおり。Sitemap 1本、Training 系約30は Disallow、Search・Agent は許可、`ai-input` の宣言無し | 本番 3333 バイト | 決定済み（対応しない。docs と食い違い無し） | 機械的 | 確認していない（③-7 で③も同じ結果） | cloudflare.md |
| ①-12 | 404 の応答 | ステータス 404。404.html の資産パスはすべてルート相対で、サブディレクトリの下でも資産 200・正しい MIME | curl 4 URL と資産4本 | 既存 issue の更新（#246・#532 は解消済み。閉じる） | 機械的 | 異議なし（部分的に確認: 404 のステータスだけ） | #246・#532 |
| ①-13 | 転送の連鎖 | `_redirects` の 301 全62行・年度分・クエリ付きの変種がすべて1ホップでクエリを引き継ぐ。http→https は 301 | curl | 問題なし（記録） | 機械的 | 部分的に同意（5本を確認） | #568 |
| ①-14 | www・workers.dev | サンドボックスから www は CONNECT 403、workers.dev は推定のホストで 404。確かめられない | curl | 判断のみ（平野さんの目視） | 機械的（届かず） | 確認していない | #114・#115 |
| ①-15 | sitemap.xml と配下 | 本番の7本（books を含む）はリポジトリと一致、well-formed、lastmod はすべて YYYY-MM-DD、重複0。インデックスに lastmod が無い（#335）。#335 の本文は「4本」、実際は6本 | cmp・minidom | 既存 issue の更新（#335 の本文） | 機械的 | 同意（機械的に確認した） | #335・#265 |
| ①-16 | sitemap の1450件 | 全件 200・転送なし。noindex の公開 URL 0。sitemap 外の公開ページは saikyo_mens.html（廃止予定）だけ | HEAD 全件と取り直し | 問題なし（#442） | 機械的 | 同意（機械的に確認した） | #442 |
| ①-17 | 未公開のページ | live/・study/ ほかは meta の noindex（X-Robots-Tag 無し）。live/ は canonical 無し | curl | 判断のみ（meta で足りる） | 機械的 | 確認していない（担当外） | B1・#362 |
| ①-18 | `/saikyo/2024/?match=20240317` | 転送されず 200（年度ページ本体） | curl | 判断のみ | 機械的 | 同意（機械的に確認した） | #425 |

#### ②ページの中身（seo-content、検証: seo-aio）

全ページの機械的な一覧（公開1450件、リポジトリの HTML）:

| 種類 | 件数 | title 最小/中央/最大 | description 最小/中央/最大 | description 50字未満 | 本文 最小/中央/最大 | 本文200字未満 |
|---|---|---|---|---|---|---|
| トップ階層 | 9 | 9/21/522 | 37/71/77 | 4 | 424/4039/174865 | 0 |
| 大会トップ | 4 | 26/28/31 | 71/75/76 | 0 | 206/211/223 | 0 |
| 個人成績・順位変動 | 8 | 33/35/38 | 48/59/91 | 3 | 107/113/133 | 8 |
| 大会ランキング | 35 | 39/43/47 | 66/68/69 | 0 | 383/738/1369 | 0 |
| title/ 入口 | 1 | 44 | 52 | 0 | 747 | 0 |
| title/ 大会ページ | 20 | 41/43/52 | 27/29/38 | 20 | 92/191/470 | 11 |
| title/ 期ページ | 363 | 34/35/44 | 15/41/65 | 355 | 66/158/449 | 236 |
| saikyo/ トップ | 1 | 17 | 62 | 0 | 189 | 1 |
| saikyo/ vs・years・players | 3 | 21/22/22 | 38/47/72 | 2 | 105/197/500 | 2 |
| saikyo/ ランキング | 6 | 28/29/31 | 54/55/57 | 0 | 212/539/678 | 0 |
| saikyo/ 年度 | 16 | 27 | 28 | 16 | 417/712/814 | 0 |
| saikyo/ 対局 | 205 | 39/46/67 | 35/42/63 | 182 | 137/262/390 | 18 |
| saikyo/ 卓 | 660 | 41/55/77 | 37/49/71 | 337 | 166/942/1189 | 2 |
| 動画の一覧 | 3 | 22/30/32 | 56/65/93 | 0 | 913/1693/2180 | 0 |
| 動画の各話 | 116 | 30/43/59 | 46/50/82 | 53 | 153/236/384 | 28 |

本文は navbar・footer・script を除いた可視テキストの字数（JS が描く部分を含まない）。og:title・og:description・og:image・og:type の欠けは0件。og:type は website 1334・video.other 116。リンク切れ0、301 を経由する静的リンク0、h1 の無い・複数のページ0、見出しの飛び0。どこからもリンクされない公開ページは静的リンクで7件あるが、navbar.js が全ページに描く。目に見えるパンくずは title/ だけ（動画の各話は JSON-LD だけ、saikyo/・houou/ は無し）。

| 番号 | 場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue・B |
|---|---|---|---|---|---|---|---|
| ②-1 | title/ の期ページ363・大会ページ20（例 https://ryoei.pro/title/oui/1.html） | description と本文が極端に短い。description は「第1期王位戦の優勝者は青木博。」（15字）などの生成の型で、期ページ355/363・大会ページ20/20が50字未満。本文は期ページの236件が200字未満（oui/1 は66字） | 全件集計。リーダーも oui/1・gpmax/1・houou/ の description を見た（データの量で文が変わる型） | 既存 issue の更新（#5 の短い description の対象に明記。description は生成の型を直せば足りる。本文の薄さはデータの無い期では解消できない） | 機械的・意味の照合をした | 同意（50字未満の件数は一致。本文200字未満は数え方で211件と差） | #5・#486・Bing(2)(4) |
| ②-2 | saikyo/ の年度16・対局205・卓660 | 年度の description は16件とも「麻雀最強戦<年>の各対局の勝ち上がりをまとめています。」（28字）。対局182/205・卓337/660 が50字未満。卓の description に出場者名が無い | 集計 | 既存 issue の更新（#5。卓に出場者・勝者、年度に最強位・対局数を入れる案） | 機械的・意味の照合をした（年度ごとに中身が違うので重複でなく情報不足） | 同意（意味の照合をした） | #5・#486・Bing(2) |
| ②-3 | 大会ランキング35（houou 9・ouka 8・jwrc 9・wrcr 9） | 大会ごとに全部門の description が同一（4グループ）。title・h1 は部門名で区別 | 集計 | 現行で直す小さいもの（生成の型に部門名を入れる） | 意味の照合をした | 同意（意味の照合をした） | #5・#283 |
| ②-4 | https://ryoei.pro/mtsuku/2FOsXsIqTAA.html と https://ryoei.pro/mtsuku/vSduV7-kLls.html | title が同一（「滝沢和典 \| Mリーグチームを作ろう！ \| ryoei.pro」）。h1 の重複は動画の各話に11グループ22ページ（同じ出演者が2回出た回）。description は区別あり | title の重複は全体でこの1組だけ | 現行で直す小さいもの（title か h1 に回の区別） | 意味の照合をした（別の動画の別ページ） | 同意（意味の照合をした） | #5・#283 |
| ②-5 | https://ryoei.pro/ | title「ryoei.pro」9字（最短）。h1「Ryoei Hirano」、英語のテンプレートのナビ、DB への内部リンク1本、navbar.js 無し | Chrome で描画 | 新サイトの要件（#101、#296 の sub-issue） | 機械的 | 同意（部分。h1 とナビは未確認） | #101・#5・Bing(3) |
| ②-6 | 短い title の12ページ（25字未満） | 型「ページ名 \| 大分類 \| ryoei.pro」で短い | 集計 | 決定済み（対応しない、BNG-04） | 機械的 | 同意（部分） | BNG-04・#5 |
| ②-7 | docs/notes/ogp.md「ページの種類ごとの og:image・og:type の規則」 | B6: 区分の文字の画像は houou/jwrc/wrcr/ouka/saikyo/title/pros/site の8種（写真の wayhome を除く）だが、ogp.md は「4種類と未公開の ouka/ の1枚」で jwrc/・wrcr/ が無い。saikyo/ の vs・years・players・ranking（10ページ）の画像も書いていない（実物は saikyo/index-white.png） | img/ogp/ と全ページの og:image | 既存 issue の更新（docs。この指示では直さない） | 機械的 | 同意（意味の照合をした: 規則の表と実際の割り当てを比べた） | B6・#581・#594 |
| ②-8 | 最強戦・動画の og:image | 規則どおり（卓612は回のサムネイル・48は作品画像、対局・年度は作品画像、動画の各話は maxresdefault）。外部ドメインの画像 | 本番で代表を取得して200 | 問題なし（記録） | 機械的（代表） | 同意（部分。画像の200は再確認せず） | B6・B7 |
| ②-9 | https://ryoei.pro/saikyo/players/ | ①-2 と同じ | 本番 | 既存 issue の更新（#638 で起票済み） | 機械的 | 同意（機械的に確認した） | #638 |
| ②-10 | Search Console（2026-09-09〜10-06、取得 2026-10-09。最新はこの回） | 総表示2641・クリック109。`?` 付きの着地が表示の86%。`jpml_pros.html?name=…` は title・description・h1 が全選手で同じ（JS が document.title を変えない）。`resource_logs.html?name=<人名>` 表示300・クリック1、`ouka_results.html?name=<選手>` 103・0 | docs/gsc/2026-10-09/page.csv | 新サイトの要件（#219・#159、#296 の sub-issue） | 機械的 | 同意（再計算はしていない） | #219・#159・#160・#142 |
| ②-11 | Search Console の着地の旧 URL | 着地はほぼ旧 URL。本番の 301 は `?name=`・`?sheet=` を引き継ぐ。`houou_leagues.html`（表示143）の 301 先 `/houou/leagues/` は noindex | page.csv・houou/leagues/index.html | 既存 issue の更新（#142 の 11/1 の確認で、houou_leagues.html の流入の移り先も見る） | 機械的 | 同意（補足: `/houou/leagues/` は個人成績のリーグのタブへ移す転送専用のページで、noindex は設計どおり。docs/notes/redirects.md:42・87） | #142・#540 |
| ②-12 | 画像の alt | alt 属性の無い img は0。空 alt は18,000超ですべて装飾 | 全件集計 | 問題なし（記録） | 機械的 | 同意（150件の標本で確認） | #486 |

#### ③AIO（seo-aio、検証: seo-tech）

| 番号 | 場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue・B |
|---|---|---|---|---|---|---|---|
| ③-1 | llms.txt:3・:16 | 「プロ1,100名超」「1,100名」が実数1,098名と合わない | jpml_pros.html の description「1,098名」 | 既存 issue の更新（#227。外すか丸めるかは未決） | 機械的 | 同意（機械的に確認した。役割も比べた） | #227 |
| ③-2 | llms.txt:57 | 放送対局「2,332本」、ページは2,331本（既知） | video_live.html の description | 既存 issue の更新（#227） | 機械的 | 同意（機械的に確認した） | #227 |
| ③-3 | llms.txt:64 | ログ「2,630件」、ページは3,159件（その後のデータの増加） | resource_logs.html の description | 既存 issue の更新（#227 に足す） | 機械的 | 同意（機械的に確認した） | #227 |
| ③-4 | llms.txt 全体 | 問題なし。本番とリポジトリ diff 0、固定の URL 34件すべて200、転送・未公開・廃止の URL 無し、公開中の全種類の入口あり | 突き合わせ | 指摘なし（記録） | 機械的 | 同意（一部のみ確認） | #227・B5 |
| ③-5 | llms.txt:77 と :59・:60 | 各話の URL の形（`<YouTube動画ID>.html`）は帰り道だけで、Mつく・プロテストに無い | 実在の URL は mtsuku/<ID>.html・exam/<ID>.html | 現行で直す小さいもの（1行。#227 の生成化に含めるなら不要） | 意味の照合をした | 同意（軽微） | #227 |
| ③-6 | llms.txt:5〜8 と本番 robots.txt | llms.txt「検索・回答での参照は可」。Content-Signal は `search=yes, ai-train=no, use=reference` で `ai-input` の宣言が無い | robots.txt の凡例・cloudflare.md | 判断のみ（直すなら llms.txt の文言だけ。軽微） | 意味の照合をした | 同意（判断のみで可。use=reference の解釈として矛盾しない） | #130・#230 |
| ③-7 | 本番 robots.txt の前置 | docs の方針どおり。Training 系は Disallow、Search・Agent は Disallow 無し | 142行 | 指摘なし（記録） | 機械的 | 同意（①-11 と整合） | #130・#304 |
| ③-8 | #554 | 10-12 の起票から更新なし。原因は未確認。セッションから Security Events に届かない | issue・docs | 既存 issue の更新（変更なし。11/1 の #304 で見る） | 判断のみ | 同意（判断のみ） | #554・#555・#304 |
| ③-9 | `.mj-lead` のある全ページ | `.mj-lead` は全ページで DOM の最後（`</main>` の直前）。#229 は未実装。#229 の「表が1.2MB」は古い（jpml_pros 922KB、video_live 1.7MB・resource_logs 2.3MB の方が大きい） | バイト位置、`scripts/lib/page.py:229` の `LEAD_TEMPLATE` | 既存 issue の更新（#229 の題・本文を今に合わせ、video_live・resource_logs を対象に足す） | 機械的 | 同意（機械的に確認した） | #229・#230 |
| ③-10 | */players/・*/race/ | 中身は JS が JSON から描く。curl の可視テキストは1文だけ（houou/players/ 113字→描画後168字、saikyo/players/ 94→150、houou/race/ 94→391） | curl と Chrome の比較 | 新サイトの要件（#219、#296 の sub-issue） | 機械的 | 異議なし（未検証） | #219・#296 |
| ③-11 | video_live.html・resource_logs.html | curl の可視テキスト約17万字、描画後4,000〜5,700字。#229 の懸念が当たる | 174,852→4,058、167,826→5,655 | 新サイトの要件（live/ #362・log/ #640 が置き換える） | 機械的 | 異議なし（ファイルの大きさは確認） | #229・#362・#640 |
| ③-12 | 構造化データ（公開1450件） | BreadcrumbList は動画の各話116、ItemList は動画の一覧3、VideoObject 0（#620・#629 どおり）、他に JSON-LD 無し。JSON の壊れ0 | 全件 parse | 新サイトの要件（#13） | 機械的 | 同意（一部のみ確認） | #13・#620・#629 |
| ③-13 | live/（B2） | docs/decisions/live.md:147「動画は公開の後に別に扱う（未実装）」のまま。live/ の JSON-LD は0件、og:type video.other と og:video はあり（VideoObject を外した他の各話と同じ形） | live/gpmax/10/b16-a.html | 既存 issue の更新（#362 に「VideoObject は入れない（#629 と同じ）」を決めて足す案） | 意味の照合をした | 同意（意味の照合をした） | B2・#362・#629 |
| ③-14 | meta robots・X-Robots-Tag | AI 向けの指定（noai・max-snippet など）無し。X-Robots-Tag は workers.dev 向けの noindex だけ | grep・HEAD | 決定済み（対応しない） | 機械的 | 同意（機械的に確認した） | cloudflare.md |
| ③-15 | Search Console の構造化データの警告の記録 | docs/gsc/ に記録無し。記録は #620 と decisions/mtsuku.md・exam.md だけ。#620 は「修正を検証」の結果待ち | grep | 既存 issue の更新（#620 は結果が出たら閉じる） | 機械的・判断のみ | 異議なし（未検証） | #620・#629 |
| ③-16 | 代表20ページ | curl と描画後の差は小さい（③-10・③-11 以外） | render.json | 指摘なし（記録） | 機械的 | 異議なし（未検証） | #266 |
| ③-17 | jpml_pros.html | 表は HTML に入っている（可視36,570字） | raw | 指摘なし（記録） | 機械的 | 同意 | ― |
| ③-18 | wayhome/ の各話・/ | `.mj-lead` 無し。各話は wayhome.md:149 の決定、トップは別系統 | render.json | 決定済み（対応しない） | 意味の照合をした | 異議なし（未検証） | #229 |
| ③-19 | rh_links.html（B5） | 本番404、llms.txt に無い。video_wayhome.html は 301 で /wayhome/ | curl・_redirects:118 | 指摘なし（記録） | 機械的 | 同意（機械的に確認した） | B5・#531 |
| ③-20 | docs/notes/mj-lead.md:12-16 | 消えた Google Charts の6ページを残すが、同じ行に消した旨あり | 文書 | 指摘なし（軽微） | 意味の照合をした | 同意（Google Charts を読む HTML は0件） | #266 |

リーダーが見つけたもの（検証なし、記録のみ）:

| 番号 | 場所 | 内容 | 区分 |
|---|---|---|---|
| L-1 | scripts/regenerate.py の `OUTPUT_OVERRIDES` のコメント（ouka/・jwrc/・wrcr/）・docs/notes/static-generation.md「ページの一覧」の前文（ouka/） | 「未公開」「noindex・メニュー未掲載」と書くが、3つとも noindex 無しで sitemap-pages.xml に載る（公開は #591・#599・#611） | 既存の記述の直し（docs とコードのコメント。この指示では直さない） |

### 棚卸しの issue と B1〜B7 の判定（全件）

| 番号 | 判定者 | 判定 | 証拠 | 検証 |
|---|---|---|---|---|
| #638 | ① | 今も当てはまる | ①-2 | ② 同意 |
| #560 | ① | 今も当てはまる（本文の表が古い） | ①-3・①-4 | ② 同意 |
| #486 | ② | 一部（h1 の無いページは0。ランキング3ページは #582 で消えた。残りは 10/30 の Bing の再確認と (4)） | ②-1・②-2・②-12 | ③ 同意 |
| #5 | ② | 一部（title の重複は②-4 の1組だけ。短い description は②-1〜②-3） | 集計 | ③ 同意 |
| #283 | ② | 一部（②-4 が残る） | ②-4 | ③ 同意 |
| #229 | ③ | 今も当てはまる（未実装。題・本文が古い） | ③-9 | ① 同意 |
| #266 | ③ | 解消済み（対象の6ページは #582 で消え、Google Charts を読む HTML は0） | ③-20・#582 のコメント | ① 同意 |
| #264 | ① | 今も当てはまる | ①-1 | ② 同意 |
| #227 | ③ | 一部（件数のずれ③-1〜③-3、生成化は未着手。CLAUDE.md「構成」は「llms.txt は手書きで生成スクリプトを持たない」と書き、#227 の構想と食い違う〈今のコードどおりの記述〉） | ③-1〜③-5 | ① 同意 |
| #335 | ① | 今も当てはまる（本文の「4本」は6本） | ①-15 | ② 同意 |
| #554 | ③ | 今も当てはまる（原因は未確認。セッションから検証できない） | ③-8 | ① 判断を保留 |
| #458 | ① | 今も当てはまる（新しい URL の登録の確認は 2026-10-30） | issue | ② 確認していない |
| #620 | ③ | 一部（一覧・各話とも VideoObject 0 を確認。残りは Search Console の検証） | ③-12・③-15 | ① 判断を保留（矛盾はしない） |
| #142 | ② | 今も当てはまる（次の取得は 11/1） | ②-10・②-11 | ③ 同意（未検証） |
| #460 | ② | 今も当てはまる（国×クエリは未取得） | docs/gsc/2026-10-09/ | ③ 同意（未検証） |
| #230 | ③ | 今も当てはまる（保留） | issue | ① 判断を保留 |
| #465 | ① | 今も当てはまる（保留） | issue | ② 確認していない |
| #296 | ③ | 今も当てはまる（sub-issue 37件） | issue | ① 判断を保留 |
| #101 | ② | 今も当てはまる（保留） | ②-5 | ③ 同意（未検証） |
| #13 | ③ | 今も当てはまる（保留・新サイト送り。本文は帰り道の VideoObject の先行実装を前提にしたままで古い） | ③-12 | ① 同意 |
| #159 | ① | 今も当てはまる（新サイト送り） | `/jpml_pros.html?name=` は 200 のまま | ② 確認していない |
| #160 | ② | 今も当てはまる（保留） | パンくずは title/ だけ | ③ 同意（未検証） |
| #165 | ② | 一部（規則は #581 で実装済み。残りは選手個別ページの画像） | ②-8 | ③ 同意（部分） |
| #219 | ② | 今も当てはまる（保留） | ②-10・③-10 | ③ 同意（未検証） |
| #226 | ② | 今も当てはまる（保留。/en/・hreflang 無し） | 実物 | ③ 同意（未検証） |
| #173 | ③ | 一部（題・本文の「VideoObject に反映」は #629 で無くなった。出演選手カードは残る） | ③・リーダー: 題と本文が `build_video_object()` を挙げ、scripts/ に `build_video_object` は無い | ① 判断を保留。リーダーが機械的に確認 |
| #195 | ② | 一部（本文の依存先が #101。選手個別ページは #219。#195 は #296 の sub-issue でない） | issue | ③ 同意（部分） |
| #289 | ③ | 今も当てはまる | issue | ① 判断を保留 |
| #275 | ③ | 今も当てはまる（feed 無し） | 実物 | ① 判断を保留 |
| #282 | ② | 今も当てはまる | issue | ③ 同意（部分） |
| #532 | ① | 解消済み（#246 と同じ内容） | ①-12 | ② 異議なし（部分） |
| #246 | ① | 解消済み | ①-12 | ② 異議なし（部分） |
| #362 | ③ | 一部（未公開のまま。og:image の件は 10-17 のコメントにあるが題から読めない） | ③-13 | ① 判断を保留 |
| #576 | ③ | 今も当てはまる（noindex のまま） | 実物 | ① 判断を保留 |
| #640 | ③ | 今も当てはまる（log/ はまだ無い） | 実物 | ① 判断を保留 |
| #568 | ① | 今も当てはまる（常設。redirects.md の「予定」表が古い） | ①-5・①-13 | ② 確認していない |
| #304 | ② | 今も当てはまる | issue | ③ 同意（部分） |
| #555 | ③ | 今も当てはまる | issue | ① 判断を保留 |
| #529 | ② | 一部（jpml_links・rh_links の廃止で G2-08 の一部が消えた） | issue | ③ 同意 |
| B1 | ① | 今も当てはまる（live/ は canonical 無し、docs/decisions/live.md:144 は未実装） | ①-17 | ② 同意 |
| B2 | ③ | 今も当てはまる | ③-13 | ① 同意 |
| B3 | ① | 解消済み（ルールは動く。redirects.md の記録が未更新。他のクエリが付くと壊れる〈①-5 の補足〉） | ①-5 | ② 同意（解消済み） |
| B4 | ② | 今も当てはまる（h1「リソース 辞書」のまま。見直しの記録なし） | resource_dictionary.html | ③ 同意（機械的に確認した） |
| B5 | ③ | 解消済み | ③-4・③-19 | ① 同意 |
| B6 | ② | 今も当てはまる | ②-7 | ③ 同意 |
| B7 | ② | 今も当てはまる（ogp.md に行き先 #362。live/ は未公開で確認できない） | ogp.md・#362 | ③ 同意 |

### 起票の候補の既存 issue の検索

`search_issues`（クローズ済みを含む）で探した。

- `?match=` に他のクエリが付くと存在しない URL へ転送される（①-5 の補足）: 同じ目的の issue は無い。近いのは #549（closed、公開）・#568（常設の棚卸し）・#638
- 一括取得で 429（①-7）: #124（closed、Rate limiting rules の設定）だけ
- `sitemap-books.xml` が公開されている（①-10）: #429（open、書籍ページの公開）・#97（保留）だけ
- description の生成の型（②-1〜②-3）: #5（open）が受け皿。#283・#229・#266・#517（closed）は別の目的

### 実測

- 所要時間: ログの最初の push 05:45Z → 相互検証の完了 約06:00Z（サブエージェントの所要は ① 約8分＋検証 約1分、② 約6.5分＋検証 約1分、③ 約7.5分＋検証 約1.5分。issue を読む補助 約3.5分）。ログの仕上げはその後
- モデルとトークン（Agent の usage。最初の報告と検証の合計）: seo-tech（sonnet）148,172、seo-content（sonnet）196,014、seo-aio（sonnet）183,352。issue を読む補助（sonnet）293,717。sonnet の指定が Sonnet 5.5 に当たったかは usage に出ない（モデル名の実物は未確認）
- 相互検証の経路: リーダー経由（SendMessage で検証役に要約のファイルの場所を渡した）
- 本番へのリクエスト: ① 約2,595（全件 HEAD 1450・429 の取り直し約855 ほか、同時最大4本）＋検証0、② 約32＋検証11、③ 約62＋検証0、リーダー 2。合計 約2,700。①の同時4本の全件 HEAD で 429 が出たため、検証では同時1本・0.5秒以上の間隔にさせた
- 指示に反してファイルを変えた・git を使った事象: ファイルの変更は無し（手順2の後の `git status --porcelain` は空）。**git の使用1件**: seo-aio が `git status --short` を1回実行した（読み取りのみ、出力は空。約束の「git コマンドを使わない」に反する）。ほかに seo-aio がバックグラウンドの grep を `pkill` で止めた（ファイルへの影響なし）
- 権限の確認が出た回数: 0（3人とも0と報告。リーダー側も0）
- 使ったスクリプト・中間データ: scratchpad の inv.py・cat.py・pages.json・public_urls.txt・reps.md・common.md・report-*.md と各担当のフォルダ（seo-tech/・seo-content/・seo-aio/）。**すべて使い捨て**（後の指示で使うなら、この表と指摘の一覧から作り直す）

### マージと片付け

- push 直前に `git fetch origin cloudflare` し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1029-seo:cloudflare`。出力の範囲: `74d7dc08f..7fff49695`（変更は docs/logs/CHAT-1029-SEO-01.md と docs/decisions/seo-bing.md だけ）
- この記録（件名「docs: record the merge of CHAT-1029-SEO-01」、7fff49695 の次のコミット）も同じ手順で cloudflare へ入れ、その後に作業ブランチを消す
- ブランチの削除の記録（docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから）: work/1029-seo ／ 削除直前の先頭 2e114b341 ／ マージ済み（`git branch -r --merged origin/cloudflare` で確かめた）。ローカルは HEAD を origin/cloudflare に移して `git branch -d` で消した。リモートの `git push origin --delete work/1029-seo` は HTTP 403（セッションの git プロキシが削除を拒否する。docs/notes/cloud-sessions.md「ブランチの削除」に書かれているとおりで、読む順を誤った）。リモートは `cleanup-branches.yml` に任せる
- この記録を足すため、work/1029-seo を origin/cloudflare（2e114b341）から作り直して push し、同じ手順で cloudflare へ入れた

## 報告

- 状態: 判断待ち（下の一覧から、直す・起票する・整理するものを番号で選んでもらう） / 続き: CHAT-1029-SEO-02
- ブランチ: work/1029-seo（マージ済み。リモートの削除は cleanup-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1029-SEO-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo
- 確認用URL: なし
- マージ: 済（7fff49695。範囲 74d7dc08f..7fff49695）
- issue: #486（Bing の Recommendations をコメント）。ほかの issue は読んだだけ
- 判断が必要なこと:
  - 判定が「同意」か「機械的に確認」の指摘を区分ごとに並べた（番号で選ぶ。詳細は経過の「指摘の一覧」）
  - 現行で直す小さいもの:
    - S1. `index.html` の `<html lang="en">` を `ja` にする（①-1、#264）
    - S2. 大会ランキング35ページの description に部門名を入れる（生成の型、②-3、#5）
    - S3. Mつくの同じ出演者の2回（`mtsuku/2FOsXsIqTAA.html`・`mtsuku/vSduV7-kLls.html` ほか h1 の11組）の title・h1 に回の区別を足す（②-4、#5・#283）
    - S4. `llms.txt` に Mつく・プロテストの各話の URL の形を1行ずつ足す（③-5。#227 の生成化に含めるなら不要）
    - S5. 文書の食い違いを直す（docs のみ。この指示では直していない）: (a) `docs/notes/redirects.md` の `?match=` を「予定」から作成済みへ（①-5・B3） (b) `docs/notes/cloudflare.md` の canonical の「例外は帰り道だけ」を実態に（①-3。S5(b) は #560 の結論と一緒でもよい） (c) 同 `cloudflare.md` の「HTML に ETag が付かない」を直す（①-6） (d) `docs/notes/ogp.md` の規則の表に jwrc/・wrcr/・公開済みの ouka/・saikyo/ の下位ページを足す（②-7・B6） (e) `scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと `docs/notes/static-generation.md`「ページの一覧」の前文の ouka/・jwrc/・wrcr/ の「未公開」を直す（L-1、リーダーのみの確認）
  - 新サイトの要件（#296 の sub-issue に足すか、既存の sub-issue に寄せるか）:
    - N1. トップの title「ryoei.pro」9字・h1「Ryoei Hirano」・英語のテンプレートのナビ（②-5 → #101）
    - N2. `?name=` の着地が表示の86%で、`jpml_pros.html?name=…` は title・description・h1 が全選手で同じ（②-10 → #219・#159）
    - N3. */players/・*/race/ は JS が JSON から描き、取得したままの HTML には1文しかない（③-10 → #219）
    - N4. video_live.html・resource_logs.html は HTML の可視テキストが約17万字（③-11 → #362・#640 の置き換えで扱う）
    - N5. 構造化データは BreadcrumbList（動画の各話）・ItemList（動画の一覧）だけ（③-12 → #13）
  - 既存 issue の更新:
    - U1. #266 を閉じる（対象の6ページは #582 で消え、Google Charts を読むページは0。①③同意）
    - U2. #246 と #532 を閉じる（404.html の資産パスはすでにルート相対で、サブディレクトリの下でも読める。2件は同じ内容。①-12）
    - U3. #5 に、短い description の対象として title/ の期ページ・大会ページ（②-1）と saikyo/ の年度・対局・卓（②-2）を、生成の型の直し方の案とともに書く。本文の薄さ（Bing の (4)）は #486 にも同じ材料を書く
    - U4. #227 に llms.txt の件数のずれ（プロ 1,100名超↔1,098名、放送対局 2,332↔2,331、ログ 2,630↔3,159）を足す（③-1〜③-3）。件数を外すか生成にするかの判断が要る
    - U5. #229 の題・本文を今に合わせる（jpml_pros は 922KB、video_live 1.7MB・resource_logs 2.3MB も対象。③-9）
    - U6. #335 の本文の「4本」を6本に（①-15）
    - U7. #560 の本文の表を今に合わせ、`index.html` 付きの別名（①-9）と末尾スラッシュの無い URL の 404（①-8）も観点に足す（①-4）。**title/ の期ページ・大会ページが例外の基準に当たるかは異議が残った**（①-3。①: `?q=`・`?name=` の絞り込みで中身が変わるので当たらない／②: `?year=` は入口だけなので当たる見込み。リーダー: `?q=`・`?name=` は title/ の全ページで読む。決め手は「絞り込みを中身の変化とみなすか」と Search Console に `?` 付きの title/ の URL があるか）
    - U8. #638 の実装のとき、saikyo/ のトップ・ranking/・vs/・years/ の canonical も決める（①-2・②-9）
    - U9. #362 に、B1（live/ は「全ページに canonical」の決定が #113 の例外の基準と食い違う。トップは `?q=` で中身が変わる）・B2（live/ にも VideoObject を入れない〈#629 と同じ〉と決めるか、③-13）・B7（og:image を最新話のサムネイルにする件を本文のチェックリストに書く）を足す
    - U10. #173 の題・本文から「VideoObject に反映」を外す（#629 で `build_video_object()` が消えた。出演選手カードは残る）
    - U11. #486 の本文に、h1 の無かったランキング3ページは #582 で消えたことと alt は当たらないこと（②-12）を書く
    - U12. #195 の依存先を #101 から #219 に直す（②の判定）
    - U13. #142 の 11/1 の確認に、`houou_leagues.html`（表示143）の流入の移り先（`/houou/players/`）を見る項目を足す（②-11）
  - 起票の候補（既存の issue の検索結果つき）:
    - K1. 最強戦の旧年度ページの `?match=` に他のクエリ（`&utm_source=` など）が付くと、存在しない URL（`/saikyo/2024/20240317&utm_source=x.html`）へ 301 する（①-5 の補足、②が発見・リーダーが本番で確認）。ダッシュボードのルールの式の直し。同じ目的の issue は無い（近いのは #549〈closed〉・#568〈常設〉）
    - K2. `sitemap-books.xml` が本番で公開されている（noindex の books/ 191件を載せる。①-10）。`.assetsignore` に入れるか、そのままにするか。同じ目的の issue は無い（近いのは #429・#97）
  - 決定済み（対応しない）に当たった指摘: ②-6（短い title、BNG-04）・③-14（AI 向けの meta robots）・③-18（帰り道の各話の `.mj-lead`）・①-11（robots.txt）
- 未確認の項目:
  - ①-7（全件の同時取得で約半数が 429。docs の「Managed Challenge」と応答が違う）は検証役が再現していない。問題か仕様かは平野さんの判断（#124 は closed）
  - ①-14 www.ryoei.pro・workers.dev の転送・X-Robots-Tag はセッションから届かず未確認
  - #554（AI クローラーへの 403）の原因・Search Console の数値（最新は 2026-10-09 の取得）・Bing の Recommendations（平野さんの画面の申告値。ここからは検証できない）
  - 3人のモデルが Sonnet 5.5 だったかは usage に出ず未確認（指定は sonnet）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4aa1e3f2）: https://github.com/retroeater/mj-logs/tree/main/guide/4aa1e3f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
