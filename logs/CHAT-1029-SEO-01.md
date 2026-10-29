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

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo/docs/logs/CHAT-1029-SEO-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo
- 確認用URL: なし
- マージ: 未
- issue: #486
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 74d7dc08）: https://github.com/retroeater/mj-logs/tree/main/guide/74d7dc08

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/74d7dc08/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
