# CHAT-1127-SEO-39

- 着手日時: 2026-11-27
- 対象issue: なし（手順1で確かめる）
- ブランチ: work/1127-seo
- 着手時HEAD: 890e95ee1

## 指示

【Claude作成】Claude Code 向け指示：Cloudflare「Search for Agents」を ryoei.pro で有効にするかの判断材料を集める（読むだけ。何も有効にしない） Chat-Ref: CHAT-1127-SEO-39 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1127-seo を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-seo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Cloudflare の新機能「Search for Agents」（ https://blog.cloudflare.com/search-for-agents/ ）を ryoei.pro のゾーンで有効にするかを平野さんが決めるための材料を集める。この指示では何も有効にせず、変わるのはログ（と完了時の docs/decisions/）だけ。
決定（2026-11-27、平野さん）

* Search for Agents を有効にするかを決める前に、読むだけの調査を SEO 系列で行う

前提（チャット側。平野さんの決定ではない）

* 記事の要点（2026-11-27 にチャット側が WebFetch で読んだ。小さいモデルの要約で、原文の全文は読んでいない。（要確認））:
   * ゾーンのトグル（ダッシュボードの Quick Actions）か API で有効にすると、Cloudflare が AI Search のインスタンスを自動で作り、サイトをクロールしてインデックスする
   * 検索のエンドポイント `/.well-known/search?q=` がサイトのドメインの下にできる。返るのは生成した回答ではなく、サイトの本文の Markdown
   * HTML のレスポンスの `<head>` に、検索の場所を知らせるメタタグが足される。Markdown for Agents が有効なら `Accept: text/markdown` の応答の YAML front matter にも同じ案内が入る
   * Free プランを含め全プランで使える。料金は AI Search の料金体系（取り込み・インデックスの保存・クエリ）で、Workers Free/Paid の枠と上限が適用される
   * 検索へのアクセスは、ボット管理・AI Crawl Control・WAF の設定に従う
* ryoei.pro の今の設定（mj-logs のガイド〈guide/cde56134〉で 2026-11-27 にチャット側が読んだ）:
   * プランは Workers Paid。Workers の Spend limits は Block usage が有効で、上限に達すると `mj-scheduler` を含む Worker の呼び出しが止まる（docs/notes/cloudflare.md「Workers の Spend limits（2026-11-19）」、docs/decisions/cloudflare.md）。値はここに写さないので、その節を読む
   * AI クローラーの扱いは docs/notes/cloudflare.md「AIクローラーの扱い（現在の設定）」（Search・Agent は Allow、Training は Disallow、Bot Preference Sync は ON）。「ボット系の設定は1つずつ変える」とある
   * `llms.txt` は手書きの静的ファイル1枚（CLAUDE.md「構成」、#161。生成対象にする issue は #227〈open〉）
   * サイトは静的 HTML で、ページ本体と同名の `.js` を分ける作りがある（CLAUDE.md「構成」）。どのページの本文が、配信される HTML に入っているか（ブラウザ側でデータを描くか）は、チャット側は確かめていない （要確認）
* 同じ論点の issue（2026-11-27 にチャット側が `gh api` の検索で確かめた）: 「Search for Agents」「Markdown for Agents」の語を含む issue は 0 件。「AI Search」の語では #304・#130（closed）・#230・#555 が出たが、本文は読んでいない （要確認）
* 自前の質問欄（Worker `mj-ask`）の仕様は docs/notes/ask-worker.md、決定は docs/decisions/houou.md
* この調査で禁じること: Search for Agents・Markdown for Agents・AI Search を有効にしない、インスタンスを作らない、Cloudflare の設定を書き換える API を呼ばない。理由は、有効にすると本番の全ページの HTML が書き換わり課金が始まるうえ、有効にするかは平野さんがまだ決めていないため。読むだけの API・本番のページの取得（`curl`。UA は CLAUDE.md「判断・作業の原則」のとおり）・公式ドキュメントの取得は禁じない

手順

1. 同じ論点の issue を検索する（クローズ済みとコメントの決定まで。検索語は「Search for Agents」「AI Search」「Markdown for Agents」「well-known」「エージェント」「llms.txt」）。出た issue の番号・状態・関係する決定を表でログの `## 経過` に書く。関係する open issue があれば着手中のコメントを書く（本文・状態・ラベルは変えない）。無ければ起票せず、その旨を報告に書く。あわせて上の「前提」の（要確認）を実物（記事の原文・リポジトリ）で確かめ、食い違いを書く
2. 公式ドキュメント（記事の原文、AI Search の料金・上限のページ、Workers の Spend limits のページ、Markdown for Agents のページ）を読み、次を出典の URL と読んだ日付きで表にする。届かなかった URL は「未確認」と書いて先へ進む（推測で埋めない）
   * (a) 料金の数え方（取り込み・保存・クエリの単位と単価）と、Workers Paid に含まれる枠
   * (b) その料金が Workers の Spend limits に数えられるか。数えられるなら、上限に達したとき `mj-scheduler`・`mj-ask` がどうなるか（docs/notes/cloudflare.md の同じ節の表と突き合わせる）
   * (c) クロールが JavaScript を実行して描いた後の本文を取るか、配信された HTML だけを読むか。クロールの頻度と、クロールの対象を絞る・外す方法（パス・sitemap・noindex のプレビューの扱い）
   * (d) `<head>` に足されるメタタグの中身と、足される範囲（全ページか）。Markdown for Agents は必須か、別に有効にするものか、料金は
   * (e) やめ方。無効にしたとき、インスタンス・インデックス・メタタグ・課金がどうなるか
   * (f) 検索のエンドポイントへのアクセスが、今の AI クローラーの設定（Search・Agent は Allow、Training は Disallow）・WAF でどう扱われるか。`robots.txt` の `Content-Signal` との関係
3. リポジトリと本番を読み、次を数えて案を出す
   * (a) ページの区分ごと（docs/notes/static-generation.md「ページの一覧」の区分）に、配信される HTML に本文（表・成績・名前）が入っているページと、ブラウザ側でデータを描くページを数える。区分・ページ数・どちらか・確かめたページの例の表を全件で書く（略さない）。sitemap の URL の数も書く
   * (b) 2 の (a) の単位に (a) の数を当てはめ、月の費用の見込みを式つきで出す（クエリの数は仮の値を2つ置き、置いた値を書く）
   * (c) 自前の質問欄（`mj-ask`）と Search for Agents の役割の違いを表にする（相手・返すもの・データの出どころ・費用を持つ側）。`llms.txt`（#161・#227）・AI 検索の言及の計測（#230）との関係も1行ずつ
   * (d) 案を「有効にする」「条件つきで有効にする（条件を書く）」「見送る」で並べ、それぞれの得・損と、推奨とその理由を `## 報告` の「判断が必要なこと」に書く。有効にする案には、平野さんの手作業の手順（画面の名前は公式ドキュメントで確かめたものだけ。直接開けるリンクを付ける）、有効にした後に動きで確かめる方法、やめる手順、外すきっかけを添える

止まる条件

* 同じ論点（Search for Agents・AI Search・Markdown for Agents を使う・使わない）の決定が、issue か docs/decisions/ に既にある（番号・ファイルと行を書いて止まる）
* 関係する issue に、他のセッションの着手中のコメントがある
* 手順2の (a)(b) の両方が「未確認」のまま（費用を出せないので、届かなかった URL を書いて止まる）
* 調べるうちに、何かを有効にしないと確かめられない点が出た（有効にせず、確かめられない点として報告に書く。これでは止まらない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-SEO-39.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-SEO-39 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0.: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット: 0件（`git log --all --grep='CHAT-1127-SEO-39'`）。`SEO` は既存の系列（最初の指示ではない）
- 作業ブランチ: `work/1127-seo` はローカル・リモートとも無く、`git checkout -b work/1127-seo origin/cloudflare`（起点 890e95ee1）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 指示文の issue の state: 着手・コメントの対象に名指しされた issue は無い（経緯の参照のみ）。0章ゲートは実装を含まないので対象外

### 手順1: 同じ論点の issue（2026-11-27、`gh api search/issues`、`repo:retroeater/mj is:issue`）

| 検索語 | 件数 | 出た issue |
|---|---|---|
| "Search for Agents" | 0 | — |
| "Markdown for Agents" | 0 | — |
| "AI Search" | 4 | #304・#130・#230・#555 |
| well-known | 1 | #533（closed、regenerate.py の失敗の扱い。無関係） |
| エージェント | 3 | #677・#21・#598（使用量の節約・Astro 移行・セキュリティ点検。無関係） |
| llms.txt | 56 | 大半はページの公開の issue（llms.txt に載せる手順の語）。論点として関係するのは #161・#227 |

関係しうる issue（本文とコメントを語で検めた）:

| issue | 状態 | 関係する決定・内容 | Search for Agents の決定 |
|---|---|---|---|
| #130 | closed | AI ボットの扱い（Search・Agent は許可、Training は Disallow）。決定 2026-09-25 | 無し（「AI Search」は AI 検索〈Agent 区分〉の意味で、Cloudflare の製品ではない） |
| #230 | open（状況: 保留） | AI 検索での言及の月次計測。自動化は 2026-10-12 に保留の決定（docs/decisions/aio.md） | 無し |
| #304 | open | 月次運用チェック。(13) に Workers の Spend limits（$20・Block usage）を見る行（2026-11-19） | 無し |
| #555 | open | 月次チェックの取得の自動化（読み取り専用トークン）。決定 2026-10-12 | 無し |
| #161 | closed | llms.txt の設置。手書き1枚 | 無し |
| #227 | open | llms.txt を生成対象にする。件数のずれの解き方（2026-10-29） | 無し |

- `docs/decisions/`・`docs/notes/`・handover.md・CLAUDE.md・`_headers`・robots.txt に「Search for Agents」「Markdown for Agents」「AI Search」「well-known」「autorag」の語は無い（grep）。**止まる条件の「同じ論点の決定」は無い**
- 他セッションの着手中のコメント: #227（最後は 2026-10-29 の記録）・#230（2026-10-12 の着手中の後に「保留」の記録で閉じている）・#304（2026-11-19 の着手中の後に完了の記録）・#555（コメント無し）。**仕掛かりの着手中は無い**
- Search for Agents を論点にする open issue は無いので、着手中のコメントは書かず、起票もしない（指示のとおり）

前提の（要確認）の突き合わせ:

| 前提（チャット側） | 実物 | 食い違い |
|---|---|---|
| 有効化はダッシュボードの Quick Actions か API。AI Search のインスタンスを自動で作る | how-to: ドメインの **Overview** → **Quick Actions** → **Search for Agents** をオン、料金の案内を確かめて確定。インスタンス名は `search-for-agents-<ZONE_ID>`（`default` の名前空間、parse type は Discover）。API で有効にできるとあるが、エンドポイントは書かれていない | 無し（API の経路は未確認） |
| `/.well-known/search?q=`。返るのは本文の Markdown | GET の `q` と QUERY メソッド（本文が text/plain か JSON `{"q":…}`）。「生成した回答ではなく、Markdown の内容」。出典と関連度のスコアが付く | 無し |
| `<head>` にメタタグ。Markdown for Agents が有効なら YAML front matter にも | 4つの `<meta name="agent:…">`（下の表 (d)）。「サイトの全ページ」に出す。front matter は両方オンのとき | 無し |
| Free を含む全プラン。AI Search の料金、Workers Free/Paid の枠と上限 | 全プランでベータ（Free は制限つき）。**課金の開始は 2026-12-01**。含まれる量はアカウントごとで Free・Paid 同じ。上限（1日のクロールのページ数など）は Free と Paid で違う | **課金が 2026-12-01 から始まることが前提に無かった** |
| アクセスはボット管理・AI Crawl Control・WAF に従う | how-to のとおり。クローラーにも同じ規則がかかり、許可のルールの例（Bot Detection ID）がある | 無し |
| 配信される HTML に本文が入っているか（要確認） | 下の手順3 (a)。2,630 のうち、本文がブラウザ側で描かれる入口は14 | 確かめた |
| 「AI Search」で出た #304・#130・#230・#555（本文未読） | 上の表。どれも Cloudflare の AI Search の製品の論点ではない | 無し |
| プランは Workers Paid | Workers は Paid（アカウント）。**ゾーンのプランは Pro**（docs/notes/cloudflare.md「Rate limiting rules」の「Pro の上限は2本」など）。Markdown for Agents は Pro 以上で使える | 前提はゾーンのプランを書いていない（食い違いではない） |

### 手順2: 公式ドキュメント（読んだ日はすべて 2026-11-27。web-fetch のサブエージェントに読ませ、URL ごとに引用させた）

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
- **(f) Rate limiting rules**: `Throttle rapid HTML fetches (#124)`（`.html` か `/` で終わるパス、`not cf.client.bot`、IP ごと1分30回で Managed Challenge）。`/.well-known/search?q=` は対象のパスに当たらない。**クローラーが `cf.client.bot`（検証済みボット）に入らなければ、2,630ページのクロールが1分30回を超えた所でチャレンジにかかり、クロールが欠ける見込み**（入るかは未確認）。今回の手順3の取得でも、4本並列で取った所 361 件の後に 2,269 件が HTTP 429 になった（間隔を3秒にして取り直した）
- **現状（有効にする前の印）**: `https://ryoei.pro/.well-known/search?q=test` は 404、トップの HTML に `agent:` のメタタグは無い、`Accept: text/markdown` で `/title/` を取ると `content-type: text/html`（Markdown for Agents は無効）。いずれも 2026-11-27 に取得

### 手順3 (a): ページの区分ごとの本文の有無（2026-11-27）

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

### 手順3 (b): 月の費用の見込み（式）

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

- **合計: 甲・A で $0、乙・B で約 $77〜$151 / 月。** Workers の Spend limits（$20）では止まらない見込みで、上限は無い（上の (b)）
- 費用を決める未確認の点は「毎日の同期で数え直すか」の1つ。有効にしないと確かめられない（ダッシュボードの使用量で見る）

### 手順3 (c): `mj-ask` と Search for Agents の違い

| | 質問欄（`mj-ask`） | Search for Agents |
|---|---|---|
| 相手 | サイトを見る人（共通ナビの AI 検索のアイコン） | AI のエージェント（メタタグを読んだもの） |
| 返すもの | Haiku 5.5 が書いた答えの文（数える・並べるはプログラム、時点・出典つき） | 本文の Markdown の抜粋（生成した答えではない。出典と関連度のスコア） |
| データの出どころ | 生成時にシートから作った `workers/ask/data*.json`（数字の正本はシート） | 本番の HTML をクロールして作る索引（24時間ごと） |
| 費用を持つ側 | ryoei.pro（Anthropic の API、1日の上限 全体200・IP ごと10。Workers の Spend limits の対象） | ryoei.pro（AI Search の料金。Spend limits の外の見込み。回数の上限の設定は読んだページに無い） |

- `llms.txt`（#161・#227）: llms.txt はページの索引を AI に渡す手書きの1枚で、Search for Agents は中身の検索の入口。重ならず、#227 の生成対象化の判断には影響しない
- AI 検索の言及の計測（#230、保留）: 測るのは ChatGPT 等の答えでの言及と流入。Search for Agents を有効にしても、その答えに使われたかは #230 の計測では見分けられない（`/.well-known/search` の要求数は別に見る必要がある）

### 有効にしないと確かめられない点（止まらずに記録）

- 毎日の同期で取り込みのトークンが数え直されるか（費用の桁が決まる）
- Search for Agents のインスタンスの Rendering mode（Static か Rendered か）
- 切ったとき・インスタンスを消したときのメタタグ・索引・課金の扱い
- クローラーが `cf.client.bot` に入り、Rate limiting rules にかからないか
- `/.well-known/search` が AI Crawl Control のどの区分で数えられるか、Workers の呼び出しとして数えられるか

## 報告

- 状態: 対応中
- ブランチ: work/1127-seo
- ログ: https://github.com/retroeater/mj/blob/work/1127-seo/docs/logs/CHAT-1127-SEO-39.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1127-seo
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 890e95ee）: https://github.com/retroeater/mj-logs/tree/main/guide/890e95ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/890e95ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/890e95ee.md
