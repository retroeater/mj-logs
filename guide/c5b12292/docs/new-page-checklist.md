# 新しいページのチェックリスト（#243）

新しいページ・メニューを作るときと公開するときの確かめの一覧。
**作ることと公開することは別の issue にする**（平野さんの決定、2026-10-06、`docs/decisions/page-release.md`）。

| 段 | どの issue で | やること |
|---|---|---|
| 作る | ページを作る issue | 下の「作る段の確かめ」と「段1」 |
| 段1 未公開で入れる | ページを作る issue | noindex・どこからも辿れない形で本番に入れ、公開の issue を起票する |
| 段2 公開する | 公開の issue（時期は平野さんが決める） | 導線（navbar・サイトマップ・`llms.txt`）を付け、本番で確かめる |

段1・段2 の手順は、最強戦 `saikyo/`（#319・#348）・タイトル戦 `title/`（#222・#413）・放送対局 `live/`（#346・#362）・書籍 `books/`（#97・#429）の実例から書き起こした（実例の表は末尾）。

## 作る段の確かめ

- 手書きの HTML は、追加する前に `docs/notes/static-generation.md`「navbar.js と検索欄」を読む（href はルート相対〈#162〉、`data-search="off"`〈#163〉）
- リンクの形は `html_handling: none` を前提にする（`docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」）。`_redirects` に行が要るか（末尾スラッシュの有無の 301 など）を決める
- 新しいファイル・ディレクトリを公開してよいか確かめ、公開しないものは `.assetsignore` に足す（#133）。最上位に新しい項目を配信するときは `assets-check.yml` の許可リストも直す（#331）
- 外部ドメインへの依存を増やさない（CSP、#9）
- アクセシビリティ: #178〜#185 の指摘（select のラベルなど）を最初から避ける
- 表の上の説明文（#229）・更新日の表示（#239）を置くか決める
- ページとメニューの名前は、同じメニューの既存の項目と並べて、紛らわしくないかを確かめてから決める（houou_race は公開の翌日に「リーグ別成績推移」から「順位変動」へ改めた。隣が「リーグ推移」）
- title・meta description・h1・og:title・og:description・og:image を決める。og:title は X のカードの帯に出るので短くし、`<title>` は検索用に正式名を残す（分けるときは `PageMeta.og_title`。`docs/notes/ogp.md`）
- 見え方は、スマホの幅と PC の幅に加えて、高さのある PC 画面（1920×1080）でも確かめる（houou_race の再生ボタンは、画面の高さが約980px を超える PC でだけ、表を切り替えた直後に表の外へはみ出した。#508）
- 規約（下の3つ）に合わせる。新しい URL パラメータは表に足してから作る

### URL パラメータの規約

- `name`（選手名。完全一致か部分一致かは `data-name-mode`）/ `tag` / `place` / `league` / `ouka` / `sheet` / `page`（1始まり）/ `all`
- 比較ページ（#276）の2名は `name=A,B` か `a`・`b` かを決めて統一する

### UI 文言の規約

- 敬体（〜してください）の要否、ボタンは体言止め、句点の有無、数字は半角、「〜件」表記
- 既存の `result_count`（「○件を表示しています」）を基準にする

### localStorage の規約

- キーの接頭辞は `mj:`、値は JSON
- 保存する項目: 最近見た選手 #236 / 全件表示 #237 / マイ選手 #287 / クイズの自己最高点 #279。項目ごとに上限を決める
- 「保存データを消す」リンクを1か所に置く。同意の表示は設けない（#236 の決定）

## 段1 未公開で本番に入れる（ページを作る issue）

完了の条件: ページの URL を直接打てば見えるが、検索エンジン・メニュー・サイト内のどこからも辿れない。

- [ ] 全ページに `<meta name="robots" content="noindex">` を付ける（生成スクリプトでは `NOINDEX_TAG` の定数にし、外すのを段2の1か所の変更にする）
- [ ] navbar（`navbar.js`）に載せない
- [ ] サイトマップに載せない。サブディレクトリのページで専用のサイトマップ（`sitemap-<名前>.xml`）を書き出すなら、`sitemap.xml` から参照しない
- [ ] `llms.txt` に載せない
- [ ] 既存のページからリンクしない（旧ページのリンクは旧ページのまま）
- [ ] `docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く
- [ ] 公開の issue を起票する。本文は段2のチェックリストを写し、公開の条件・メニューの位置・置き換える旧ページ・「ページを作る issue を待つ」を書く。作る issue に公開の issue の番号をコメントする

## 段2 公開する（公開の issue）

着手の条件: 公開の issue に書いた条件が揃い、平野さんが公開を決めた。

着手前
- [ ] 公開の条件（平野さんの実機での最終確認、先に済ませる issue）が揃ったことを issue に書く
- [ ] 公開の効果を後で測るなら、公開前の Search Console の数字を残す（title/ は #413 の前に残した）

変更（1つの作業ブランチで）
- [ ] noindex を外して再生成する。「公開していない」と書いたコメント・docstring・生成物の説明も直す
- [ ] navbar に載せる（位置は公開の issue のとおり。旧ページへのリンクを差し替えるときは、旧ページへの導線を外すかを決める）
- [ ] サイトマップに載せる（`sitemap.xml` から専用のサイトマップを参照する、または `sitemap-pages.xml` に足す）。lastmod は手で書かない（`docs/notes/sitemap-lastmod.md`）。push 前に `xml.etree.ElementTree` で parse する（`sitemap.xml`・`llms.txt` だけの変更ではワークフローが走らない）
- [ ] `llms.txt`（手書き、#161）に入口の行を足す
- [ ] title・description・h1・og:title・og:image・共有ボタンを確かめる。canonical は既定で出さない（#113）。出すときは `saikyo/`・`title/` と同じ形で og:url と同じ値にする
- [ ] 置き換える旧ページがあれば、残すか 301 で転送するかを決める。決めかねるなら残して別の issue にする（saikyo_results.html は #350、jpml_titles.html は #441 で後から転送した）
- [ ] `docs/notes/static-generation.md`「ページの一覧」・ページの設計メモ・`docs/handover.md` の「未公開」の記述を直す
- [ ] `cloudflare` へのマージは平野さんの確認の後（navbar を変えると表示が変わる）

公開後の確かめ
- [ ] Workers Builds の check-run が success（これだけで本番反映とはしない、`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」）
- [ ] 本番の HTML（`curl`）: ページが 200 で robots の noindex が無い（応答ヘッダの `x-robots-tag` も）。本番の `navbar.js`・サイトマップ・`llms.txt` に URL がある。`_redirects` の転送が期待どおり
- [ ] X と LINE の投稿画面に URL を貼り、カード表示（画像・og:title）を確かめる。X は `?x=<未使用の数字>` を付け、デプロイ直後の1〜2分は避ける（`docs/notes/ogp.md`）
- [ ] 平野さんの実機: navbar から開けて、見え方・操作が崩れていない（セッションから確かめられるのは本番の HTML まで）
- [ ] 平野さんの作業: Search Console で `sitemap.xml` を再送信し、入口を URL 検査でインデックス登録をリクエスト
- [ ] 親の issue・作る issue に、公開した日とマージの SHA をコメントする
- [ ] X で告知するときは `docs/notes/page-announcement.md` の型で（投稿文と告知動画）

## 実例の表

○ 行った／× 行っていない／− 該当なし。2026-10-06 時点（houou_race の列は 2026-10-07 時点）。

| 項目 | saikyo/（#348） | title/（#413） | live/（#362） | books/（#429） | houou_race（#508） |
|---|---|---|---|---|---|
| 段1: noindex | ×（付けず、導線を外すだけで伏せた） | ○ | ○ | ○ | ○ |
| 段1: navbar に載せない | ○（旧ページ `saikyo_results.html` のまま） | ○ | ○ | ○（booklog への 301 のまま） | ○ |
| 段1: サイトマップに載せない | ○（`sitemap.xml` の参照をコメントで無効化） | ○（`sitemap-title.xml` は書き出し、未参照） | ○ | ○（`sitemap-books.xml` は書き出し、未参照） | ○ |
| 段1: `llms.txt` に載せない | ○（載せていた行を削除、CHAT-0915-SK-27） | ○ | ○ | ○ | ○（作る途中で足した行を戻した） |
| 段1: 未公開と文書に書く | 未確認 | ○（handover.md） | ○ | ○ | ○（static-generation.md「ページの一覧」） |
| 段1: 公開の issue | 作る途中で起票 | 作る issue（#222）の残課題から後で分けた | ○ | ○ | 作る途中で分けた（#507 から #508） |
| 段2: 公開の条件 | 先に済ませる issue（#333・#384・#355）と平野さんの実機確認 | #356・#412 | #348・#413・#412 と平野さんの最終確認 | 残課題と平野さんの判断 | 24後 A1 のシートの直し（平野さん）と、本番での確かめ（iPhone の Safari を含む） |
| 段2: noindex を外す | − | ○ | 予定 | 予定 | ○ |
| 段2: navbar・サイトマップ・`llms.txt` | ○ | ○ | 予定 | 予定 | ○ |
| 段2: canonical | 作る段で付けていた | 公開で付けた | − | − | ×（既定どおり出さない） |
| 段2: 旧ページ | 残した（転送は #350） | 残した（転送は #441） | 残してから転送する予定 | 転送する予定（booklog → books/） | − |
| 段2: 公開前の基準値 | × | ○（Search Console） | − | − | ×（残さないと平野さんが決めた） |
| 公開後: 本番の HTML（`curl`） | ○ | ○ | − | − | ○ |
| 公開後: ブラウザ | ○（headless Chromium で navbar から） | ×（平野さんに依頼） | − | − | ○（headless Chromium で navbar から。1920×1080 も） |
| 公開後: Search Console | × | ○（平野さんの作業） | − | − | ○（平野さんの作業、2026-10-07） |
| 公開後: OGP のカード表示 | × | ×（OGP は公開後の #232） | − | − | ○（X・LINE、平野さん、2026-10-07。X は1回目に画像が出ず、`?x=` の数字を変えた2回目で出た） |

実例の違い: saikyo/ は noindex を付けずに伏せた。title/ は公開の issue を後から分けた。どちらも、上の手順では noindex を付けて公開の issue を作る段で起票する形に揃えた。
