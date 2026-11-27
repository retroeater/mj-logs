# Cloudflare の Search for Agents（2026-11-27、見送り）

**見送り（2026-11-27、平野さんの決定。`docs/decisions/aio.md`「2026-11-27（CHAT-1127-SEO-40）」）。** 有効にしていない。
調べた事実は CHAT-1127-SEO-39 の調査（2026-11-27）から移した。値はその日のもので、公式の記述は変わることがある。

**見直すきっかけ**（#304 の (9) の AI Crawl Control の行で月に1回見る）:

- AI Crawl Control で Agent の区分の要求が目に見えて増えた
- Cloudflare の公式に、AI Search の使用量の上限と、Search for Agents のやめ方（インスタンス・メタタグ・課金の扱い）が載った

見直すときは、下の「公式ドキュメント」の表を読み直してから案を作る（課金は 2026-12-01 から始まる）。

## 公式ドキュメント（読んだ日はすべて 2026-11-27）

読んだ URL（すべて 200）:

- 記事: https://blog.cloudflare.com/search-for-agents/
- how-to: https://developers.cloudflare.com/ai-search/how-to/search-for-agents/
- 料金・上限: https://developers.cloudflare.com/ai-search/platform/limits-pricing/
- AI Search の概要: https://developers.cloudflare.com/ai-search/
- 設定の一覧: https://developers.cloudflare.com/ai-search/configuration/
- Website のデータソース: https://developers.cloudflare.com/ai-search/configuration/data-source/website/
- parse type: https://developers.cloudflare.com/ai-search/configuration/data-source/website/parse-types/
- 同期: https://developers.cloudflare.com/ai-search/configuration/indexing/syncing/
- how-to の一覧: https://developers.cloudflare.com/ai-search/how-to/
- Markdown for Agents: https://developers.cloudflare.com/fundamentals/reference/markdown-for-agents/
- Workers の料金: https://developers.cloudflare.com/workers/platform/pricing/
- Workers の上限: https://developers.cloudflare.com/workers/platform/limits/
- Spend limits: https://developers.cloudflare.com/workers/platform/spend-management/
- Browser Run の料金: https://developers.cloudflare.com/browser-run/pricing/

届かなかった URL: https://developers.cloudflare.com/ai-search/platform/pricing/ （404。料金は limits-pricing のページにあった）

| 項目 | 公式の記述 | 出典 |
|---|---|---|
| (a) 課金の開始 | 「AI Search billing begins on December 1, 2026.」 | limits-pricing |
| (a) 含まれる量（毎月、アカウントごと） | 取り込み 500万トークン・保存 10 GB-month・semantic のクエリ 1,000・full-text のクエリ 1,000 | limits-pricing |
| (a) 超えた分の単価 | 取り込み $0.75 / 100万トークン（画像の処理は +$0.50 / 100万トークン）、保存 $2.00 / GB-month、semantic・vector・hybrid のクエリ $0.75 / 1,000、full-text のクエリ $0.10 / 1,000 | limits-pricing |
| (a) 取り込みの数え方 | 最終の chunk を `cl100k_base` で数える。重なり（overlap）は各 chunk で数える | limits-pricing |
| (a) 下の製品 | 埋め込み・再ランクの Workers AI は AI Search の料金に含まれ、Workers AI の請求には出ない。保存・索引・クロールの R2・Vectorize・Browser Run も別の請求に出ない。生成・クエリの書き換え・要約・外部モデルは別料金 | limits-pricing |
| (a) インスタンスの数 | Workers Free 100。Workers Paid は既定の上限 5,000 まで含む、超えると1つ $0.10 / 月 | limits-pricing |
| (a) Workers Paid の枠（参考） | 1,000万リクエスト・3,000万 CPU ミリ秒 / 月を含む | workers/platform/pricing |
| (a) 上限 | 1インスタンスのファイル数: Free 100,000・Paid 100万（hybrid は 50万）。1回のクロールのページ数 100,000。1日のクロールのページ数: Free 500・Paid 無制限 | limits-pricing |
| (b) Spend limits に数えるもの | 「request and CPU charges after included usage」。「Durable Objects, Workers KV, R2, and other products have separate charges. A Workers spending limit does not apply to those charges.」 | spend-management・workers/platform/pricing |
| (b) AI Search が Spend limits に入るか | **AI Search の名は Spend limits のページに無い。** 上の範囲の書き方からは数えられない側と読めるが、明記は無い（未確認）。`/.well-known/search` の応答がアカウントの Worker の呼び出しとして数えられるかも書かれていない（未確認） | spend-management |
| (c) JavaScript | Website のデータソースに Rendering mode がある（Static: 「Downloads the raw HTML」、Rendered: ヘッドレスブラウザで描いた後）。**Search for Agents の既定の表に Rendering mode の行は無く、どちらで作られるかは書かれていない（未確認）** | data-source/website・how-to |
| (c) 頻度 | 既定の Sync interval 24時間（15分〜24時間で変えられる）。Crawl cache age 24時間。手動の同期は30秒に1回まで。31日間検索が無いと自動で一時停止（停止中も検索はできる） | how-to・syncing |
| (c) 既定のクロール | parse type `discover`（sitemap と リンクの両方）・100,000ページまで・リンクを5段まで・サブドメインは含めない。Hybrid 検索・埋め込み `@cf/qwen/qwen3-embedding-0.6b`・chunk 1,024トークン・重なり 10% | how-to |
| (c) 絞り方 | Path filtering（例 `**/blog/**`）。「Excluded pages are never fetched.」。設定は後から Settings（Parser options）で変えられるとある一方、設定の一覧では Website は作成後に変えられず Path filtering は変えられる、とあり、Rendering mode を後から変えられるかは読み切れない（未確認）。parse type を変えると全体を同期し直す | data-source/website・configuration・parse-types |
| (c) robots.txt | クローラーの UA は `Cloudflare-AI-Search`。robots.txt で拒否されたページは `blocked_by_robots_txt` で索引しない | parse-types |
| (c) noindex・プレビュー | noindex の扱いは書かれていない（未確認）。サブドメインは既定で含めないので、workers.dev のプレビューは対象外と読める（別のドメインのため） | how-to |
| (d) メタタグ | `<meta name="agent:instructions" content="Use this search API to find relevant content across this site …">`・`<meta name="agent:endpoint" content="https://<ドメイン>/.well-known/search?q={query}">`・`<meta name="agent:method" content="GET">`・`<meta name="agent:documentation" content="https://developers.cloudflare.com/ai-search/how-to/search-for-agents/">` | how-to |
| (d) 範囲 | 「HTML responses の `<head>`」に足す。記事は「every page of your site」 | how-to・記事 |
| (d) Markdown for Agents | 必須ではない別の機能（「If you also turn on Markdown for Agents…」）。有効にするのは **Agent Readiness** → **Markdown for Agents** → **Serve Markdown to AI agents**。Pro・Business・Enterprise で無料（Free は書かれていない）。`Accept: text/markdown` の要求だけを変換する。オリジンが Content-Signal を送らないと `Content-Signal: ai-train=yes, search=yes, ai-input=yes` を足す | markdown-for-agents |
| (e) やめ方 | **Search for Agents を切ったとき・インスタンスを消したときのインスタンス・索引・メタタグ・課金の扱いは、読んだどのページにも無い（未確認）。** 書かれているのは同期の一時停止と再開だけ | how-to・configuration・how-to の一覧・syncing |
| (f) アクセスの制御 | 検索のエンドポイントへの要求は「AI Crawl Control and WAF configurations」に従う。ボット管理の規則はクローラーにもかかる。クローラーを通すルールの例: Security rules → Create rule → Custom rules、Field「Bot Detection ID」equals `122933950`、action「Skip」、place「First」 | how-to・data-source/website |
| (f) Content-Signal | クローラーは `search` と `ai-input` の目的を名乗る。どちらかが `no` のサイトはクロールを断る | parse-types |
| (f) エンドポイントの区分 | `/.well-known/search` が AI Crawl Control の Search・Agent・Training のどれとして扱われるかは書かれていない（未確認） | how-to |

ryoei.pro に当てはめた所見:

- **(b) 費用の上限が無い**: AI Search の料金は Workers の Spend limits の外と読める（明記は無い）。**$20 の Block usage は AI Search の課金を止めない見込み**で、逆に AI Search の使用で `mj-scheduler`・`mj-ask` が止まることも無い見込み（docs/notes/cloudflare.md「Workers の Spend limits（2026-11-19）」の表の3つの Worker は、Workers のリクエスト・CPU の超過でだけ止まる）。気づく手段は Budget alert（$5）だけ
- **(f) Content-Signal**: ryoei.pro の robots.txt は `Content-Signal: search=yes,ai-train=no,use=reference`（2026-11-27 に取得。`ai-input` の宣言は無い）。`search` も `ai-input` も `no` ではないので、クロールは断られない。`Cloudflare-AI-Search` は robots.txt の `Disallow: /` の UA に無い
- **(f) Rate limiting rules**: `Throttle rapid HTML fetches (#124)`（`.html` か `/` で終わるパス、`not cf.client.bot`、IP ごと1分30回で Managed Challenge）。`/.well-known/search?q=` は対象のパスに当たらない。**クローラーが `cf.client.bot`（検証済みボット）に入らなければ、2,630ページのクロールが1分30回を超えた所でチャレンジにかかり、クロールが欠ける見込み**（入るかは未確認）。2026-11-27 の区分の取得（下の表）でも、4本並列で取った所 361 件の後に 2,269 件が HTTP 429 になった（間隔を3秒にして取り直した）
- **現状（有効にする前の印）**: `https://ryoei.pro/.well-known/search?q=test` は 404、トップの HTML に `agent:` のメタタグは無い、`Accept: text/markdown` で `/title/` を取ると `content-type: text/html`（Markdown for Agents は無効）。いずれも 2026-11-27 に取得

## ページの区分ごとの本文の有無（2026-11-27）

取り方: 本番の `sitemap.xml` の7つの sitemap から URL を集め（**2,630 URL**: pages 54・saikyo 903・title 387・live 1,165・wayhome 46・mtsuku 62・exam 13）、UA `mj-internal survey (CHAT-1127-SEO-39)` で取得して、`<script>`・`<style>`・`<nav>`・`<header>`・`<footer>` を除いた `<body>` の文字数と `<tr>` の数を測った（377 ページ。区分ごとの例は下の表）。navbar は navbar.js が描くので HTML には入っていない。「本文あり」は表・成績・名前が HTML に入っているもの、「JS で描く」は説明文と部品だけで、成績を `fetch` で取って描くもの（`assets/league.js`・`league_race.js`・`title.js`・`log.js`・`resource_dictionary.js` の `fetch(`）。

| 区分（static-generation.md「ページの一覧」） | sitemap の URL 数 | どちらか | 確かめたページの例（本文の文字数） |
|---|---|---|---|
| `index.html`（トップ） | 1 | 本文あり（紹介文） | `/`（1,044） |
| 型A・15列 `jpml_pros.html` | 1 | 本文あり（表 1,099行） | `/jpml_pros.html`（44,333） |
| 型A・2列/3列 `saikyo_mens.html` | 0（sitemap に無い） | — | — |
| 型A'・多列テキスト `rh_results`・`rh_results_detail` | 2 | 本文あり（表） | `/rh_results.html`（489、13行）・`/rh_results_detail.html`（23,564、322行） |
| 型D・静的SVG `resource_efficiency.html` | 1 | 本文あり | `/resource_efficiency.html`（1,868） |
| 辞書 `resource_dictionary.html` | 1 | 本文あり（説明。辞書の中身は JSON をダウンロードする） | `/resource_dictionary.html`（543） |
| 帰り道の一覧 `wayhome/` | 1 | 本文あり | `/wayhome/`（2,002） |
| 帰り道の各話 `wayhome/<動画ID>.html` | 45 | 本文あり（題名・出演・説明。短い） | `/wayhome/sAx65O6q8x4.html`（165。45件の平均 約210） |
| Mつくの一覧と各話 `mtsuku/` | 62 | 本文あり | `/mtsuku/`（2,477）・`/mtsuku/ZeFtFG2ov_Q.html`（287） |
| 女流勉強会 `study/` | 0（noindex・未公開） | — | — |
| プロテストの一覧と各話 `exam/` | 13 | 本文あり | `/exam/`（970）・`/exam/-PoeE0Bm2sE.html`（211） |
| 最強戦のトップ `saikyo/` | 1 | JS で描く（説明文だけ） | `/saikyo/`（74） |
| 最強戦の年度・対局・卓 `saikyo/<年度>/` | 893 | 本文あり（卓の顔ぶれ・通過/敗退） | `/saikyo/2026/`（613）・`/saikyo/2026/20261122.html`（496）・`/saikyo/2015/20151212.html`（418） |
| 最強戦の対戦成績 `saikyo/vs/` | 1 | 本文あり（多いペア）。任意の組は `?name=` で JS | `/saikyo/vs/`（455） |
| 最強戦の年度別・個人成績・ランキング | 8 | years・ranking は本文あり、players は JS で描く | `/saikyo/years/`（216）・`/saikyo/ranking/champion/`（236、13行）・`/saikyo/players/`（91） |
| タイトル戦 `title/` | 387 | 本文あり（入口は年の切り替えを JS で。現タイトルホルダーは HTML） | `/title/`（834）・`/title/houou/`（509）・`/title/houou/42.html`（348）・`/title/jwrc/11.html`（206） |
| 鳳凰戦 `houou/` | 12 | ランキング9は本文あり（表）、トップ・players・race の3は JS で描く | `/houou/ranking/total/`（1,651、101行）・`/houou/`（87）・`/houou/players/`（110）・`/houou/race/`（95） |
| 女流桜花 `ouka/` | 11 | ランキング8は本文あり、3は JS で描く | `/ouka/ranking/total/`（869）・`/ouka/`（84）・`/ouka/race/`（92） |
| WRC リーグ `jwrc/` | 12 | ランキング9は本文あり、3は JS で描く | `/jwrc/ranking/pass-rate/`（856）・`/jwrc/`（90）・`/jwrc/race/`（85） |
| WRC-R リーグ `wrcr/` | 12 | ランキング9は本文あり、3は JS で描く | `/wrcr/ranking/total/`（864）・`/wrcr/`（93）・`/wrcr/race/`（88） |
| 連盟プロ `pro/` | 0（noindex・未公開、1,099ファイル） | — | — |
| ログ `log/` | 1 | 本文あり（新着）。検索は JS（`log/search.json`） | `/log/`（1,020） |
| 放送対局 `live/` | 1,165 | 本文あり | `/live/`（61,330）・`/live/houou/`（315）・`/live/teiou/2/b16-a.html`（794）・`/live/jwrc/14/b16-d.html`（1,838） |
| 書籍 `books/` | 0（noindex・未公開、191） | — | — |
| 良栄のトップ `r/` | 0（noindex・未公開） | — | — |
| サイトのトップの新しい形 `top/` | 0（noindex・未公開） | — | — |
| 静的 `404.html` | 0 | — | — |

- 合計: sitemap の 2,630 URL のうち **JS で描く入口は 14**（houou・ouka・jwrc・wrcr のトップ・players・race の12、saikyo/ と saikyo/players/）、**残りの 2,616 は本文が HTML に入っている**。Rendering mode が Static でも、索引から欠けるのは14の入口の成績（それぞれのランキングのページと title/・live/ には同じ選手名・成績が HTML で入っている）
- sitemap の外: 配信される HTML は 3,967 ファイル（`git ls-files '*.html'` から `.assetsignore` の対象を除く）。noindex は books 191・pro 1,099・study 41 など。公開ページの HTML から pro/・books/・study/・r/・top/ へのリンクは無い（pro/ へは noindex の top/ からだけ）。Discover はリンクをたどるが、navbar.js が描くリンクは Static では見えないので、届くのは sitemap とその HTML のリンクの先と見込む（noindex の扱いは未確認）

## 月の費用の見込み（式と置いた値）

本文の文字数の見込み: 測った区分の平均 × URL 数 = live 1,212×1,165 ≈ 141万・saikyo 821×903 ≈ 74万・pages 1,955×54 ≈ 11万・title 202×387 ≈ 8万・wayhome 251×46 ≈ 1万・mtsuku 約300×62 ≈ 2万・exam 約250×13 ≈ 0.3万 → **約 237万字**（live は 10 ページ、title は 8 ページの平均で、ほかより粗い）。

置いた値:

- トークン数 = 字数 × 係数。Markdown のリンクの URL などを足して 1.5 倍、日本語の `cl100k_base` を 1字 1〜1.5 トークンとして、**低 = 237万 × 1.0 × 1.1（重なり 10%）≈ 260万、高 = 237万 × 1.5 × 1.5 × 1.1 ≈ 590万トークン**（1回の全取り込み）
- 毎日の同期（既定 24時間）で変わらないページが再び数えられるかは書かれていない（未確認）ので、2通り置く: 甲「変わったページだけ」（初月に全取り込み1回、以降は月に数%）、乙「毎日すべて数え直す」（30回）
- クエリ（hybrid は semantic の単価）: 仮の値 A = 月 300、B = 月 30,000

| 項目 | 式 | 見込み |
|---|---|---|
| 取り込み 甲 | max(0, 260万〜590万 − 500万) × $0.75/100万 | $0〜$0.68（初月）、以降 $0 |
| 取り込み 乙 | (7,800万〜1億7,700万 − 500万) × $0.75/100万 | **$55〜$129 / 月** |
| 保存 | HTML 約 53 MB（測った平均 × URL 数）＋ ベクトル（590万 ÷ 1,024 × 1.1 ≈ 6,300 chunk × 1,024次元 × 4 B ≈ 26 MB、次元は 0.6B のモデルの一般の値で未確認）≈ 0.1 GB < 10 GB | $0 |
| クエリ A | max(0, 300 − 1,000) × $0.75/1,000 | $0 |
| クエリ B | (30,000 − 1,000) × $0.75/1,000 | $21.75 / 月 |
| インスタンス | 1つ（5,000 まで含む） | $0 |

- **合計: 甲・A で $0、乙・B で約 $77〜$151 / 月。** Workers の Spend limits（$20）では止まらない見込みで、上限は無い（上の表の (b)）
- 費用を決める未確認の点は「毎日の同期で数え直すか」の1つ。有効にしないと確かめられない（ダッシュボードの使用量で見る）

## 質問欄（mj-ask）との役割の違い

| | 質問欄（`mj-ask`） | Search for Agents |
|---|---|---|
| 相手 | サイトを見る人（共通ナビの AI 検索のアイコン） | AI のエージェント（メタタグを読んだもの） |
| 返すもの | Haiku 5.5 が書いた答えの文（数える・並べるはプログラム、時点・出典つき） | 本文の Markdown の抜粋（生成した答えではない。出典と関連度のスコア） |
| データの出どころ | 生成時にシートから作った `workers/ask/data*.json`（数字の正本はシート） | 本番の HTML をクロールして作る索引（24時間ごと） |
| 費用を持つ側 | ryoei.pro（Anthropic の API、1日の上限 全体200・IP ごと10。Workers の Spend limits の対象） | ryoei.pro（AI Search の料金。Spend limits の外の見込み。回数の上限の設定は読んだページに無い） |

- `llms.txt`（#161・#227）: llms.txt はページの索引を AI に渡す手書きの1枚で、Search for Agents は中身の検索の入口。重ならず、#227 の生成対象化の判断には影響しない
- AI 検索の言及の計測（#230、保留）: 測るのは ChatGPT 等の答えでの言及と流入。Search for Agents を有効にしても、その答えに使われたかは #230 の計測では見分けられない（`/.well-known/search` の要求数は別に見る必要がある）

## 案（2026-11-27 の調査で並べたもの。案3 を採った）

- 案1「有効にする」: 得 = AI のエージェントがサイトの中を検索できる入口ができる（全ページの `<head>` に案内のメタタグ）。損 = 費用の上限が無く、乙の場合は月 $55〜$129 ＋クエリ。全ページの HTML が変わる。切り方が公式に無い。Rate limiting rules（#124）がクロールを欠かせるおそれ。`/.well-known/search` を読みに来るエージェントがどれだけあるか分からない（ryoei.pro での需要の材料が無い）
- 案2「条件つきで有効にする」: 条件 = (i) 公式に切り方（インスタンス・メタタグ・課金）が書かれる、(ii) 毎日の同期の数え方が分かる、または AI Search に使用量の上限の設定ができる、(iii) 有効にする前に Path filtering で `live/` の各話（1,165、本文の約6割）を外すかを決める。得 = 案1 の得を、費用の桁が分かってから取れる。損 = 時期が遅れる。条件 (i)(ii) が満たされたかを誰かが見る手間
- 案3「見送る」: 得 = 費用・HTML の変化・設定の手間が無い。AI の検索・回答は今の設定（Search・Agent は Allow）と llms.txt で引き続き届く。損 = エージェント向けの入口が無いまま
- **推奨: 案3（見送る）。理由: ryoei.pro で使われる見込みの材料が無いのに、費用の上限が無く、切り方も公式に無い。** 見直すきっかけ = AI Crawl Control で Agent の区分の要求が目に見えて増えたとき、または公式に切り方・使用量の上限が載ったとき（#304 の (9) に足した。2026-11-27）

## 有効にするときの手順・確かめ方・やめる手順・外すきっかけ

- 案1・2 を選ぶときの平野さんの手順（画面の名前は how-to で確かめたもの）: (1) Cloudflare ダッシュボード https://dash.cloudflare.com/ で ryoei.pro のゾーンを選び **Overview** → **Quick Actions** → **Search for Agents** をオン → 料金の案内（pricing notice）を読んで確定。(2) 作られた AI Search のインスタンス `search-for-agents-<ZONE_ID>` の **Settings** で Path filtering・Sync interval を見る（Rendering mode が見えたら値を控える）。(3) クローラーが Rate limiting rules にかかるなら、how-to の例のとおり Security rules → Create rule → Custom rules に Bot Detection ID `122933950` を Skip する規則を First に置く（Pro の Custom rules の枠と、Skip が Rate limiting rules を飛ばせるかは未確認）
- 有効にした後に動きで確かめること: `curl -sS -A 'mj-internal …' 'https://ryoei.pro/.well-known/search?q=鳳凰戦'` が 404 でなく Markdown を返す（今は 404）／トップの HTML に `<meta name="agent:endpoint"` が出る（今は無い）／翌日以降、インスタンスの索引のページ数が 2,616 前後になる／Billing で AI Search の取り込みトークンが毎日増えるか（乙かの判定）
- やめる手順（公式に無いので推定。未確認）: Quick Actions の Search for Agents をオフ → トップの HTML から `agent:` のメタタグが消えたことを `curl` で確かめる → AI Search の画面でインスタンスが残っていれば消す → 翌月の請求で AI Search の行が止まったかを見る
- 外すきっかけ: Budget alert（$5）が AI Search で鳴った、Billing で AI Search の額が月 $5 を超えた、Search Console のクロールの統計情報に異常が出た（#304 (8)(10)）、要求の数が月 1,000 未満のまま3か月続いた（需要が無い）

## 未確認の項目

有効にしないと確かめられない点:

- 毎日の同期で取り込みのトークンが数え直されるか（費用の桁が決まる）
- Search for Agents のインスタンスの Rendering mode（Static か Rendered か）
- 切ったとき・インスタンスを消したときのメタタグ・索引・課金の扱い
- クローラーが `cf.client.bot` に入り、Rate limiting rules にかからないか
- `/.well-known/search` が AI Crawl Control のどの区分で数えられるか、Workers の呼び出しとして数えられるか

そのほか:

- AI Search の料金が Workers の Spend limits の外か（読める書き方だが明記は無い）
- 費用の式の本文の字数は live 10・title 8 ページの平均からの外挿。トークンの係数（1字 1〜1.5、Markdown 1.5倍）とベクトルの次元は置いた値
- 手順の直接のリンクは https://dash.cloudflare.com/ の入口だけ（ゾーンの Overview を直接開く URL の形は公式のページで確かめていない）
