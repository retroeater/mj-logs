# CHAT-1029-SEO-04

- 着手日時: 2026-10-29
- 対象issue: #13・#101・#219・#159・#362・#640・#296
- ブランチ: work/1029-seo4
- 着手時HEAD: 4aa1e3f28

## 指示

【Claude作成】Claude Code 向け指示：新サイトの要件のうち N1（トップの title・h1・description）と N5（構造化データ）を現行サイトに前倒しで入れ、プレビューで止める
Chat-Ref: CHAT-1029-SEO-04
マージ: 判断待ちで止まる
貼る時機: いつでも（SEO-02・SEO-03 とは別のセッションに貼る。SEO-02 が先にマージされていれば、取り込んでから始める）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo4〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo4 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo4 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo4 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1029-SEO-01 のレビューで「新サイトの要件」とした N1〜N5 のうち、今の作りで実装できる N1・N5 を現行サイトに前倒しで入れる。あわせて、新サイト送りの記録（docs/handover.md 4章・#13・#296）と、前倒ししない N2〜N4 の受け皿の issue を直す。プレビューで止め、平野さんの確認の後に別の指示でマージする。

### 決定（2026-10-29、平野さん）
- 新サイト（#296）の要件のうち、現行サイトでも実装できるものは前倒しで実装する
- 前倒しするのは N1（トップの title・h1・description）と N5（構造化データ）。N2（`jpml_pros.html?name=` の title）は前倒ししない
- N3（players/・race/ の本文が JS で描かれる）は #219 のまま、N4（video_live・resource_logs の大きな HTML）は live/（#362）・log/（#640）の置き換えに任せる
- 新サイトの要件 N1〜N5 は、新しい issue を作らず、受け皿の issue（#101・#219・#159・#13・#362・#640）にコメントで足す

### 前提（チャット側。平野さんの決定ではない）
- N5 の範囲（チャット側の案）: (a) 公開中の全ページに、見えているナビ・ディレクトリの階層に合う `BreadcrumbList` を入れる（今は動画の各話116件だけにある。docs/notes/video-mtsuku.md など） (b) トップ（`/`）に `WebSite`（name・url）を入れる（検索窓の `SearchAction` は入れない） (c) **ItemList を大会ランキングに入れる案は取り下げる**（Google のリッチリザルトの対象にならず、効果が見込めないため。チャット側の見直し） (d) `Person`（選手）は選手個別ページが無いので入れない（#13・#219 に残る） (e) `VideoObject` は入れない（#620・#629 の決定）
- 階層の決め方: navbar のメニュー（`navbar.js` の項目）とディレクトリの階層から、ページの種類ごとに「トップ → 区分 → …」の並びを決め、表にしてログに書く。title/ の見えているパンくずがある種類はそれと同じ並びにする。並びの名前は各ページの h1 か title の先頭の語に合わせる。どちらにも決まらない種類は仮置きして「判断が必要なこと」に挙げる
- 入れ方: 生成のページは生成スクリプト（`scripts/lib/page.py` など共通の部分を優先）で入れ、手書きのページは HTML を直接直す。JSON-LD は `<script type="application/ld+json">` で、今の動画の各話の書き方に合わせる
- N1 の文言（チャット側の案。プレビューで平野さんが見て決める）: 今の `<title>` は「ryoei.pro」、h1 は「Ryoei Hirano」（SEO-01 の②-5）。案は title「平野良栄 | 麻雀プロ（日本プロ麻雀連盟）| ryoei.pro」、h1「平野良栄」。meta description・og:title・og:description は今の文を読み、title と食い違う所だけを直す。見た目が変わるのは h1 の文字だけにし、テンプレート（iPortfolio）の英語のナビ・レイアウトは変えない（#101 の範囲）
- 新サイト送りの記録の直し: docs/handover.md 4章「現行サイトに作り込みすぎない」の「構造化データ（#13）は現行サイトでは見送り、新サイトで対応」を、前倒しした範囲（BreadcrumbList・WebSite）と残り（Person など。#13）に分けて書き直す（追記先の今の内容を読み、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」に従う）。#13 は #296 の sub-issue のまま残し（残りがあるため）、前倒しした範囲をコメントで書く。#296 の本文「新サイト送りにするとき・やめるとき」の手順に当たるかを読んで確かめ、当たるならその手順に従う
- 告知の期間: 最強戦は告知済みとみなしてよい（2026-10-28 の平野さんの決定）。ほかに告知の期間中のページ（docs/notes/page-announcement.md）がある場合は、そのページの生成のファイルの変更を外して進め、外したものを報告に書く
- 生成物の差分は、JSON-LD の追加（と N1 のトップの数行）だけになる見込み。公開中のページは 1,450件（SEO-01 の手順1(a)）。全ページの再生成の扱いは CLAUDE.md「判断・作業の原則」のとおり

## 手順
1. N5: 上の前提の「階層の決め方」で、ページの種類ごとの並びの表を作ってログに書く。生成スクリプト・手書きのページに BreadcrumbList を、トップに WebSite を入れ、テストを足し（JSON として読めること・並びの最後が自分自身の URL であること）、再生成する。公開中のページのうち JSON-LD の入ったページの数を種類ごとに数え、1,450件と突き合わせて、入らなかったページとその理由を全件書く
2. N1: `index.html` の title・h1・meta description・og:title・og:description を、上の前提の案で直す。前後をログに書く
3. 記録: docs/handover.md 4章を上の前提のとおり直す。#13 に前倒しした範囲をコメントし（#296 の手順に当たればそれに従う）、#101 に N1（トップの title・h1 の文言だけを前倒しし、英語のナビは残る）、#219・#159 に N2・N3（SEO-01 の②-10・③-10 を引用）、#362・#640 に N4（③-11 を引用）をコメントする（本文・状態・ラベルは変えない。引用は SEO-01 のログの `## 経過`「指摘の一覧（全件）」の行から）。本番と同じ形でプレビューを用意し（docs/notes/cloud-sessions.md「プレビュー」）、トップ・種類ごとの代表のページ（SEO-01 の手順1(a) の表）で JSON-LD を取り出して JSON として読めることを確かめ、ログに書く。最終報告の「確認用:」にプレビューの URL を書き、判断待ちで止まる

## 止まる条件
- 手順1で、ページの種類の半分以上で並びが決まらない（表を書いて止まる）
- 再生成の差分に、JSON-LD と N1 のトップの数行以外の変化が出た（種類ごとの件数と例を書いて止まる）
- 再生成で、シートの変化による差分が混ざって切り分けられない（書いて止まる）
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。重なる対象〈生成スクリプト・ページの種類〉だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
- docs/handover.md の追記先が、この指示の前提と矛盾していて、どちらが正か判断が要る

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 貼る時機: 指示文は「SEO-02・SEO-03 とは別のセッションに貼る」だが、SEO-01〜03 と同じセッションに貼られた。SEO-02 は判断待ちで止まり SEO-03 は完了していて、このセッションで並行して動く作業は無いので、そのまま進めた
- SEO-02（work/1029-seo2）は未マージ（`git merge-base --is-ancestor origin/work/1029-seo2 origin/cloudflare` が偽）なので取り込まずに origin/cloudflare から始めた。`index.html` の `lang` は SEO-02 が直すので、この指示では触らない
- 同じ Chat-Ref のコミット: なし。CLAUDE.md は起動時から変わっていない。work/1029-seo4 はローカル・リモートとも無く `git checkout -b work/1029-seo4 origin/cloudflare`（着手時 HEAD 4aa1e3f28）
- 未マージの work/ ブランチとの重なり（`git diff --name-only origin/cloudflare...<branch>`）: work/1028-lgn・work/1029-lgn-eki が `scripts/generate_resource_logs.py`・`navbar.js` を、work/1017-lvp が `scripts/lib/study.py`・`scripts/lib/members.py`・`navbar.js`・live/ を変えている。この指示で変えるのは `scripts/lib/page.py`・`generate_title_pages.py`・`generate_saikyo_pages.py`・`generate_houou_pages.py`・`index.html`・`docs/handover.md` で、どれとも重ならない（resource_logs.html は下の表のとおり単層で、もともと入れない）
- 告知の期間: docs/notes/page-announcement.md の「実例」の表は、最後の saikyo/ のリニューアル（2026-10-27）まで投稿済みか、告知済みとみなしてよい（最強戦、2026-10-28 の決定）。期間中のページは無い

### 手順1 N5: ページの種類ごとの並び

決め方: 見えているパンくずのある種類はその並び（title/ の `mj-title-breadcrumb`、houou/ ほか4大会と saikyo/ のメニューの下のページの `mj-houou-crumbs`、saikyo/ の年度・対局・卓・vs の `mj-saikyo-n-crumb`）。名前は見えているパンくずの文字。最後の項目は自分自身の URL（og:url・canonical と同じ）。
**区分のトップ（navbar の項目そのもの）は並びが1項目になる。Google のパンくずのリッチリザルトは2項目以上が要るので、1項目のページには出さない**（`PageMeta.breadcrumb` が2つ以上のときだけ出す）。動画の各話（116件）は今の BreadcrumbList（一覧 → この回）のまま変えない。

| ページの種類 | 件数 | 並び（BreadcrumbList） | 決め方 |
|---|---|---|---|
| トップ `/` | 1 | （BreadcrumbList 無し）。代わりに `WebSite`（name「ryoei.pro」・url） | 指示の (b) |
| トップ階層の8ページ（jpml_pros・resource_dictionary・resource_efficiency・resource_logs・rh_*3・video_live） | 8 | 無し（1項目。navbar の「番組」「リソース」「良栄」はページを持たない項目で、並びに入れられない） | 単層 |
| 大会トップ（houou/・ouka/・jwrc/・wrcr/） | 4 | 無し（1項目） | 単層 |
| 大会の個人成績・順位変動（*/players/・*/race/） | 8 | 大会名（`/houou/` など）→ 個人成績／順位変動 | 見えているパンくず |
| 大会ランキング（*/ranking/<部門>/） | 35 | 大会名 → ランキング（自分自身の URL。部門名は入らない） | 見えているパンくず |
| title/ 入口 | 1 | 無し（1項目。入口に見えているパンくずは無い） | 単層 |
| title/ 大会ページ | 20 | タイトル戦（`/title/`）→ 大会名 | 見えているパンくず |
| title/ 期ページ | 363 | タイトル戦 → 大会名 → 第N期（対局日） | 見えているパンくず |
| saikyo/ トップ | 1 | 無し（1項目） | 単層 |
| saikyo/ vs・years・players | 3 | 最強戦（`/saikyo/`）→ 対戦成績／年度別／個人成績 | 見えているパンくず |
| saikyo/ ランキング | 6 | 最強戦 → ランキング（自分自身の URL） | 見えているパンくず |
| saikyo/ 年度 | 16 | 最強戦 → 麻雀最強戦<年度> | 見えているパンくず |
| saikyo/ 対局 | 205 | 最強戦 → 麻雀最強戦<年度> → 対局名 | 見えているパンくず |
| saikyo/ 卓 | 660 | 最強戦 → 麻雀最強戦<年度> → 対局名 → 卓の見出し | 見えているパンくず |
| 動画の一覧（wayhome/・mtsuku/・exam/） | 3 | 無し（1項目。ItemList は今のまま） | 単層 |
| 動画の各話 | 116 | 一覧 → この回（今のまま） | 既存 |

並びが決まらない種類: 無し（「無し」の種類は、決まらないのでなく1項目になるため）。BreadcrumbList が入る見込みは 8＋35＋20＋363＋3＋6＋16＋205＋660＋116 = 1,432件、入らないのは 1＋8＋4＋1＋1＋3 = 18件（合わせて 1,450件）。

### 手順1・2 の実装

- `scripts/lib/page.py`（1203c3c99）: `PageMeta.breadcrumb`（(名前, URL) の並び、既定は空）と `breadcrumb_json_ld()` を足し、`HEAD_TEMPLATE` の `{extra_head}` の直後に `{breadcrumb}` を置いた。2項目以上のときだけ `<script type="application/ld+json">` を出す（JSON は動画の各話と同じ `script_json()`）。空のページは出力が変わらない
- `scripts/generate_title_pages.py`: 大会・期の本文を作る関数で、見えているパンくずの並び（`crumbs`）を1か所で作り、`breadcrumb_html()` と `breadcrumb_meta()`（JSON-LD 用、href を `https://ryoei.pro` 付きに、最後を og:url に）の両方に渡した。期の対局日（`period_date()` は警告を足す）を2回呼ばないため、本文の関数の中で `meta.breadcrumb` に入れる（本文は render の前に作られる）
- `scripts/generate_houou_pages.py`: `meta()` に `here`（見えているパンくずの最後の項目）を足し、`breadcrumb()` が (大会名, 大会のトップ)・(here, 自分自身) を返す。個人成績「個人成績」・ランキング「ランキング」・順位変動「順位変動」（順位変動は `dataclasses.replace` に渡す）。ouka/・jwrc/・wrcr/ は同じスクリプトを設定で動かすので同じ形になる
- `scripts/generate_saikyo_pages.py`: `breadcrumb_meta(*items)`（最強戦のトップを先頭に足す）を足し、年度・対局・卓・vs の `PageMeta` と `menu_meta()`（`here` を足した。years・players・ranking）に渡した。トップは `here` 無し
- `index.html`（b162e0a0c）: `<title>`・og:title を「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」（指示の案の「）| ryoei.pro」は、ほかのページの区切りに合わせて「） | ryoei.pro」にした）、h1 を「Ryoei Hirano」→「平野良栄」、`WebSite` の JSON-LD を head に足した。meta description・og:description（「日本プロ麻雀連盟の麻雀プロ、平野良栄の公式サイト。…」）は title と食い違わないので変えていない。og:image:alt「ryoei.pro」も変えていない。英語のナビ・`.typed` の「I'm Mahjong Pro, Mind Athlete」・ヘッダの「Ryoei Hirano」（59行・104行・フッタ）は #101 の範囲なので残した
- テスト `scripts/tests/test_breadcrumb_json_ld.py`（8件）: 2項目以上で BreadcrumbList が出て position が 1..n・最後が自分自身・`<` が逃げる／1項目以下は出ない／title・houou・saikyo の並びの組み立て／生成済みの公開ページ（title/・saikyo/・houou/ ほか・動画）の BreadcrumbList が JSON として読め、2項目以上・最後が og:url と一致／トップの JSON-LD が WebSite の1つだけ。修正前のコードでは `breadcrumb` 引数・`breadcrumb_meta` が無く GeneratorTest・RenderTest が通らない（#310）。全体 1608 件 OK
- コンテナにはシートの鍵が無い（docs/notes/cloud-sessions.md「鍵」）ので、再生成は `regenerate-page.yml` を作業ブランチで起動して行う。先に `dry_run`・`compare_ref`（cloudflare の 4aa1e3f28）で、コード起因の違いだけを確かめる（run 40838845823）

### 再生成（regenerate-page.yml を work/1029-seo4 で起動）

- run 40838845823（`dry_run`・`compare_ref` 4aa1e3f28、target `title_pages saikyo_pages houou_pages ouka_pages jwrc_pages wrcr_pages`）: success。**新旧のコードで同じデータから生成した差は 1,316 ファイル・各1行**（houou/・ouka/・jwrc/・wrcr/ の個人成績・順位変動・ランキング 43、title/ の大会・期 383、saikyo/ の vs・years・players・ランキング・年度・対局・卓 890）。上の表の見込み 43＋383＋890 = 1,316 と一致し、title/ の入口・saikyo/ のトップ・大会トップは差に出ない
- run 40839653040（同じ target、書く）: success、コミット 6e0b84e24「chore: regenerate title/ saikyo/ houou/ ouka/ jwrc/ wrcr/ via GitHub Actions」。1,319 ファイル
- 差分の分類（`git diff -U0 HEAD origin/work/1029-seo4` を行ごとに分類）: 足した行 `<script type="application/ld+json">…BreadcrumbList…</script>` が 1,316（1ファイル1行）、ほかの変化は sitemap-pages.xml（44行）・sitemap-saikyo.xml（830行）・sitemap-title.xml（383行）の `<lastmod>` が 2026-10-29 になった 1,257 行だけ。lastmod はワークフローが git の最終コミット日から導く（`update_sitemap_lastmod.py --from-git`、手では書いていない）もので、ページが変わった分だけ動いた。**シートの変化による差分は無い**（本文・カード・リンクの行の変化は0）。止まる条件の「JSON-LD と N1 のトップの数行以外の変化」には、この lastmod を当てはめなかった（判断が必要なことに挙げた）
- 生成の後に `python3 -m unittest discover -s scripts/tests`: 1608 件 OK（`GeneratedPagesTest` が生成済みの 1,432 件の BreadcrumbList を読む）

### 公開中のページの JSON-LD（生成の後のリポジトリ、SEO-01 の pages.json の1,450件で数えた）

| 種類 | 件数 | JSON-LD | 無し |
|---|---|---|---|
| 動画の各話 | 116 | BreadcrumbList 116（前から） | 0 |
| 動画の一覧 | 3 | ItemList 3（前から） | 0 |
| 大会トップ | 4 | 0 | 4（houou/・ouka/・jwrc/・wrcr/。並びが1項目） |
| 大会の個人成績・順位変動 | 8 | BreadcrumbList 8 | 0 |
| 大会ランキング | 35 | BreadcrumbList 35 | 0 |
| トップ階層 | 9 | WebSite 1（`/`） | 8（jpml_pros・resource_dictionary・resource_efficiency・resource_logs・rh_paifu・rh_results・rh_results_detail・video_live。並びが1項目） |
| saikyo/ 卓・対局・年度 | 660・205・16 | BreadcrumbList 881 | 0 |
| saikyo/ トップ | 1 | 0 | 1（並びが1項目） |
| saikyo/ vs・years・players・ランキング | 3・6 | BreadcrumbList 9 | 0 |
| title/ 期・大会 | 363・20 | BreadcrumbList 383 | 0 |
| title/ 入口 | 1 | 0 | 1（並びが1項目） |

合計: BreadcrumbList 1,432（うち新しく足したもの 1,316）・WebSite 1・ItemList だけ 3・JSON-LD 無し 14（どれも区分のトップで、並びが1項目になるため）＝ 1,450。

### プレビュー

6e0b84e24 の check-run「Workers Builds: mj」は completed / success（URL は最終報告）。`?v=seo04a` を付け、同時1本・0.5秒あけて curl で23ページを取り、JSON-LD をすべて `json.loads` で読めた（23ページとも 200）。

| ページ | JSON-LD |
|---|---|
| `/` | WebSite。title・og:title「平野良栄 \| 麻雀プロ（日本プロ麻雀連盟） \| ryoei.pro」、h1「平野良栄」 |
| `/jpml_pros.html`・`/resource_dictionary.html`・`/houou/`・`/title/`・`/saikyo/` | 無し |
| `/houou/players/` | 鳳凰戦 > 個人成績 |
| `/jwrc/race/` | WRCリーグ > 順位変動 |
| `/houou/ranking/total/` | 鳳凰戦 > ランキング |
| `/title/houou/` | タイトル戦 > 鳳凰戦 |
| `/title/houou/42.html` | タイトル戦 > 鳳凰戦 > 第42期（2026年1月31日） |
| `/title/ourai/12.html` | タイトル戦 > 桜蕾戦 > 第12期（2026年9月4日） |
| `/saikyo/vs/`・`/saikyo/players/`・`/saikyo/ranking/champion/` | 最強戦 > 対戦成績／個人成績／ランキング |
| `/saikyo/2024/` | 最強戦 > 麻雀最強戦2024 |
| `/saikyo/2024/20240317.html` | 最強戦 > 麻雀最強戦2024 > 女流新スター決戦 |
| `/saikyo/2024/20240317-s1-a.html` | 最強戦 > 麻雀最強戦2024 > 女流新スター決戦 > ベスト8 A卓 |
| `/wayhome/`・`/mtsuku/` | ItemList（前から） |
| 動画の各話3件 | 一覧 > この回（前から） |

どれも最後の項目は自分自身の URL。最初は Python の urllib で取ったら 403 だった（取り方の違い。curl では 200）

### 記録（手順3）

- docs/handover.md 4章「現行サイトに作り込みすぎない」: 「構造化データ（#13）は現行サイトでは見送り、新サイトで対応」→「構造化データ（#13）: 見えているパンくずと同じ並びの `BreadcrumbList`（title/・saikyo/・houou/ ほか4大会・動画の各話）とトップの `WebSite` は現行サイトに前倒しした（2026-10-29、`lib/page.py` の `PageMeta.breadcrumb`）。選手の `Person` などは新サイトで対応（#13・#219）」。同じ節のほかの行は変えていない。handover.md は 22,735 バイト（警告域 26KB の外）
- #296 の本文「新サイト送りにするとき・やめるとき」: 新サイト送りにする・やめる issue は無いので当たらない（#13 は sub-issue のまま）
- コメント（本文・状態・ラベルは変えていない）: #13 https://github.com/retroeater/mj/issues/13#issuecomment-6491364229 ／ #101 https://github.com/retroeater/mj/issues/101#issuecomment-6491366037 ／ #219 https://github.com/retroeater/mj/issues/219#issuecomment-6491368298 ／ #159 https://github.com/retroeater/mj/issues/159#issuecomment-6491369965 ／ #362 https://github.com/retroeater/mj/issues/362#issuecomment-6491371633 ／ #640 https://github.com/retroeater/mj/issues/640#issuecomment-6491372995

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose、sonnet、88,481 トークン）に、指示の目的・決定、コードの差分（生成物・ログを除く）と生成物の代表の差分、CLAUDE.md「コード規約」を渡した。1回目で要修正は無し、任意3件。

| 指摘 | 扱い |
|---|---|
| 動画の各話は BreadcrumbList を自前で組む `build_json_ld()` を3か所に持ち、`page.py` の `breadcrumb_json_ld()` が4つ目になる | 見送り（各話の出力を変えないため今回は触らない。寄せる余地は「判断が必要なこと」に書いた） |
| title/ の本文の関数が `meta.breadcrumb` を書き換える（head が本文の後に描かれる順に頼る副作用） | 意図どおり（期の対局日の `period_date()` は警告を足すので2回呼ばない。手順はログの「実装」に書いた。順序が変われば `GeneratedPagesTest` の「見えているパンくずと同じ」で落ちる） |
| テスト: `</script>` の検査が何も検出しない／見えているパンくずとの名前の突き合わせが無い／og:url が無いと読みにくく落ちる | 直した（2264ef6e3「test: compare breadcrumb JSON-LD with the visible breadcrumbs」。見えているパンくずのある 1,316 ページで名前の並びが一致することを確かめる。8件・全体 1608 件 OK） |

2回目は、直したのがテストだけ（生成物・ページの出力は変わらない）なので行っていない。

## 報告

- 状態: 判断待ち
- ブランチ: work/1029-seo4
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo4/docs/logs/CHAT-1029-SEO-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo4
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `/`・`/houou/players/`・`/title/houou/42.html`・`/saikyo/2024/20240317-s1-a.html` ほか23ページ（経過の「プレビュー」）。この版で見える印は、トップの見出し（h1）が「平野良栄」になっていること
- マージ: 未（平野さんの判断待ち。マージは別の指示）
- issue: コメントした #13・#101・#219・#159・#362・#640（本文・状態・ラベルは変えていない）
- 判断が必要なこと:
  - マージしてよいか。N1 の文言（トップの `<title>`・og:title「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」、h1「平野良栄」）をプレビューで見て決める。案の「）| ryoei.pro」はほかのページの区切りに合わせて「） | ryoei.pro」にした
  - BreadcrumbList を区分のトップ（並びが1項目になる14ページ: トップ階層8・大会トップ4・title/ の入口・saikyo/ のトップ）に入れなかった（Google のパンくずは2項目以上が要るため）。「公開中の全ページ」の指示と違うので、これでよいか
  - 大会と最強戦のランキングのページの並びの最後は、見えているパンくずどおり「ランキング」（部門名は入らない。URL は部門のページ自身）
  - 再生成で sitemap の `<lastmod>` 1,257 行（3ファイル）が 2026-10-29 になった。ワークフローが git から導く値で、ページが変わった分だけ動いたものなので、止まる条件の「JSON-LD 以外の変化」に当てはめずに進めた
  - #296 の本文の子の表の #13 の行（「`Person` / `ItemList` / `WebSite` / `BreadcrumbList`」）を、マージの後に「`Person` など」に直すか（この指示では #296 の本文を変えていない）
  - 動画の各話の3つの `build_json_ld()`（帰り道・Mつく・プロテスト。女流勉強会も同じ形）を `page.py` の `breadcrumb_json_ld()` に寄せるか（出力は同じ形。今回は触っていない）
  - マージの順: SEO-02（work/1029-seo2）も `index.html`（2行目の `lang`）と `docs/decisions/seo-bing.md`（末尾の追記）を変えている。この指示は `index.html` の別の行と同じファイルの末尾を変えたので、後からマージする方で取り込み、衝突すれば decisions は両方を残す
- 未確認の項目:
  - Search Console・Bing がこのパンくずを読むか（リッチリザルトのテスト・URL 検査はセッションから届かない）
  - トップの h1 の見た目（日本語の字形・大きさ）。プレビューで平野さんが見る
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
