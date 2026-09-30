# CHAT-0930-BNG-01

- 着手日時: 2026-09-30
- 対象issue: #126（洗い出しのみ。コメントはしない）
- ブランチ: work/0930-bng
- 着手時HEAD: 0057ebeb

## 指示

【Claude作成】Claude Code 向け指示：Bing 関連の issue と、リポジトリ内の Bing 関連の実装・記録を洗い出す（読むだけ、判断待ちで止まる） Chat-Ref: CHAT-0930-BNG-01 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-bng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-bng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。識別子 BNG が使用済みなら止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Bing 関連の課題をまとめて片付けるため、まず #126 の中身と、Bing に関わる issue・実装・記録の全体をチャット側が読める形でログに書く。この指示では何も変えない（ログだけ）。
決定（2026-09-30、平野さん）

* Bing 関連の課題をまとめて片付ける。まず #126 に着手する。

前提（チャット側。平野さんの決定ではない）

* docs/handover.md「次にやること」では #126 は「Bing で title/ の登録を確認、2026-10-12 ごろ、手順は #126 のコメント（2026-09-28）」となっている。今すぐできることと 10/12 まで待つことの切り分けは、この洗い出しの後にチャット側で行う。
* Bing Webmaster Tools の画面は Claude Code から見られない前提。画面で確かめることは「平野さんの手作業」として分けて書く。

手順

1. #126 の本文と全コメント（特に 2026-09-28 の手順のコメント）を読み、要点（何を・いつ・どう確かめる手順か、Open/Closed、ラベル、blocked by/依存）をログに書く。文面をそのまま貼ってよい（issue は private のためチャット側は読めない）。他セッションの「着手中」コメントがあれば止まる。
2. issue を検索（open・closed の両方。`Bing`・`bingbot`・`IndexNow`・`Webmaster`・`MSN`・`Copilot` を本文・コメント・タイトルで）し、番号・タイトル・Open/Closed・要点1行・#126 との関係を表にする。GSC の #269・#142・#441（jpml_titles 廃止と 301）など、Bing に影響する隣接の issue も「隣接」として表に足す。
3. リポジトリ内で Bing に関わるものを洗い出す（読むだけ）: `robots.txt`（bingbot の扱い、AI 系ボットの拒否が Bing の Copilot 用ボットを含むか）、`sitemap*.xml` とその生成、`_headers`・`_redirects`、`.github/workflows/`・`scripts/` に Bing・IndexNow の実装があるか、`docs/`（site-findings.md・gsc/・cloudflare.md 等）の Bing の記録、Cloudflare の設定の記録（Crawler Hints / IndexNow・Bot Fight Mode・Rate Limiting #124 が Bingbot に及ぶか。記録は docs/notes/cloudflare.md の範囲で、ダッシュボードは見ない）。ファイルと行を示して表にする。

止まる条件

* 識別子 BNG が使用済み、または作業ブランチの条件を満たさない。
* #126 に他セッションの「着手中」コメントがある。
* 手順3で何かを変えたくなった（変えずに案として書く）。

完了条件

* 変更はこのログだけ。issue へのコメントはしない（「着手中」も付けない。着手は次の指示で決める）。
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、今すぐできること／10/12 まで待つこと／平野さんの手作業（Bing Webmaster Tools の画面）の3つに分けた案を書く。
* マージ: 判断待ち（マージしない。ログのみのため、次の指示で cloudflare へ入れるか決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。識別子 BNG は全ブランチのコミット・`docs/logs/` の履歴に無し（`git fetch --unshallow` 後に確認）。`origin/work/0930-bng` は無く、`origin/cloudflare`（0057ebeb）から作成。
- 指示欄の末尾（「この行が指示文の最後の行です。」）は指示文の最後の行と一致。
- 変更はこのログだけ。issue へのコメントはしていない。

### 手順1: #126 の中身

- 標題: **Bing Webmaster Toolsに登録しIndexNowを検討する**
- 状態: **Open**。ラベル: `状況: 待ち`・`分野: SEO/AIO`。作成 2026-09-11、最終更新 2026-09-28。コメント4件。sub-issue・親なし
- 依存: 本文は「Cache Rules の判断（別issue）の後に効果を確認する」だが、#123（Cache Rules）は却下クローズで解消済み（コメント 2026-09-12）。現在の待ち先は **#156**（エッジキャッシュの置き換わり確認、Open・`状況: 待ち`）。ただし 2026-09-28 に Crawler Hints を On にする方針へ変わっており、#156 の結果を待たずに試している
- **他セッションの「着手中」コメントは無い**（4件とも平野さんの記録か Claude の記録。着手中の書式のものは無し）

**本文（そのまま）**

> Search Console からインポートできるので登録は数分。麻雀という領域で Bing の比率は低いと思われるが、コストがほぼゼロで、GSC と独立した検証材料になる。
>
> ### IndexNow
> Cloudflare の Crawler Hints を On にすると IndexNow に自動通知が飛ぶ。ただしキャッシュ連動のため、Cache Rules の判断（別issue）の後に効果を確認する。
>
> ### 完了条件
> 登録後、Bing 側のインデックス数と GSC の22URLを突き合わせる。

**コメント1（2026-09-12）優先度**: SEO/AIO 施策10件中7番目。Bing のインデックスは Copilot や ChatGPT 検索の参照元になるため、AIO 観点では検索シェア以上の意味がある。

**コメント2（2026-09-12）登録完了（平野さんが実施）**

- GSC からのインポートで登録。所有権は自動で引き継がれ、手動の確認は不要
- Sitemap: `https://ryoei.pro/sitemap.xml`、Last submit / Last crawl 2026-09-12、Status Success、URLs discovered **25**、エラー/警告 0/0。25件は当時の `sitemap.xml` の `<loc>` 件数と一致。意図的に除外した5ページ（404.html / saikyo_mens.html / `_redirects` 転送の3件）も含まれない
- IndexNow（Crawler Hints）は保留。Crawler Hints はエッジキャッシュ上の変化を検知して IndexNow へ通知する仕組みで、デプロイがキャッシュエントリを実際に置き換えるかは #156 で検証待ち。未確認のまま有効にすると通知の有無を判定できないため、「#156 の結果が出てから判断する」
- 残作業（完了条件）: Bing 側のインデックス数と GSC の22URLの突き合わせ（クロール開始直後のため **2026-09-15〜16頃**に平野さんが実施）。あわせて `?name=` 付き URL が Bing でも個別にインデックスされるかを見る（Google はパラメータ付き14件を検索結果に出している。扱いが違えば #113〈canonicalなし〉の判断が検索エンジンごとに別の結果を生み、#159〈?name= からの301設計〉の前提に関わる）

**コメント3（2026-09-13、CHAT-0913-SP-01）**: IndexNow は #222（大会別ページ）と選手個別ページ（#219）の公開時に使う。大量の新 URL を一度にインデックスさせる局面が対象。

**コメント4（2026-09-28、CHAT-0924-TQ-28。#126 の手順のコメント）**

> ## IndexNow の方針と Search Console の作業（2026-09-28、平野さん）
> title/ の公開（#413、2026-09-28）に合わせて決めたこと・行ったこと。
>
> ### IndexNow
> - **Cloudflare の Crawler Hints を On にする方法（案 b）で試す。2026-09-28 に On にした**
> - Bing Webmaster Tools は登録済み
> - **2026-10-12 ごろ**に、Bing Webmaster Tools の URL 検査で `https://ryoei.pro/title/` を確かめる。登録されていなければ、自前の送信（案 c: キーのファイルと、デプロイ後の GitHub Actions からの送信）を検討する
> - Google カレンダーに登録済み: 2026-10-12「【ryoei.pro】【#126】Bing で title/ の登録を確認」
>
> ### Search Console
> - `sitemap.xml` を 2026-09-28 に再送信した（成功。検出されたページ数 79）
> - `https://ryoei.pro/title/` の URL 検査は「検出 - インデックス未登録」。インデックス登録をリクエスト済み
> - 公開の効果の比較は 2026-10-01 の月次取得の後（#413 の GC-20 の表と同じ語。カレンダー登録済み）
>
> 上の内容は平野さんの申告で、この環境（Claude Code）からは Cloudflare・Bing・Search Console の設定や画面を確かめられない。

**気づいたこと（#126 まわり）**

- 完了条件の「Bing のインデックス数と GSC の22URLの突き合わせ（9/15〜16頃）」の**実施記録は #126 に無い**（CHAT-0924-TQ-28 のログにも「実施の記録なし」とある）。10/12 の確認とまとめる案がそのログにある
- `?name=` 付き URL の Bing での扱いの確認も記録が無い。`?name=` は #441 の実装（`work/0930-olt-02`、未マージ）で `title.js` が扱うようになり、`jpml_titles.html` は `/title/` への 301 になる予定のため、前提が変わりつつある
- 9/28 の sitemap 再送信の「検出 79」は GSC の数。Bing 側は 9/12 の 25 が最後の記録

### 手順2: Bing に関わる issue の表

検索の方法: GitHub の検索 API（`search_issues`）はキーワードで0件を返し（`Bing` でも0件）、途中でレート制限にもなったため、REST の一覧で **issue 482件（PR を除く。Open・Closed の両方）と全コメント1487件を取得し、手元で `bing|bingbot|indexnow|webmaster|msn|copilot|crawler hints`（大文字小文字を区別しない）を本文・コメント・タイトルから検索した**。`msn` は該当なし。`copilot` は #126 の本文外では issue に無し（docs/notes/cloudflare.md:375 に「GitHub Copilot 却下」の記述があるが、Microsoft の Copilot 検索とは無関係）。

**Bing・IndexNow を直接扱うもの**

| # | タイトル | 状態 | 要点（1行） | #126 との関係 |
|---|---|---|---|---|
| 126 | Bing Webmaster Toolsに登録しIndexNowを検討する | Open（`状況: 待ち`） | 上記。登録済み、Crawler Hints On（2026-09-28、申告）、10/12 ごろに `/title/` を確認 | 本件 |
| 156 | 次回データ更新後、エッジキャッシュのETagを比較して置き換わりを確認する | Open（`状況: 待ち`） | デプロイでエッジキャッシュが置き換わるか。Crawler Hints の効果判定の前提として #126 が依存（コメント 2026-09-12: 結果を #126 にも共有する）。女流桜花のデータ更新後に実施 | #126 の待ち先 |
| 413 | title/ を公開する | Closed | 公開時の IndexNow 送信を #126 に委ねた。コメント（2026-09-28）で Crawler Hints を On にする案を決める項目あり | #126 の 10/12 確認の対象（`/title/`）を作った |
| 222 | タイトル戦の新構成（title/ 配下…）を生成する | Open | 公開時の IndexNow は #126（本文・コメント） | #126 の IndexNow の想定利用先 |
| 114 | workers.devのプレビューURLをnoindexにする | Closed | 2026-09-12 に Google・Bing とも `site:` 検索で0件（本番・プレビュー両方）を確認。`_headers` に `X-Robots-Tag: noindex` | Bing の検索結果の実測が1回ある |
| 269 | Search Console のエクスポートを月次で自動取得する | Open | 本文: Bing Webmaster API による同様の取得は、#126 の完了後に Bing 経由の流入比率を見てから判断（この時点では対象外） | #126 の後続の判断 |

**Bing のクローラー（Bingbot）の扱いに関わるもの**

| # | タイトル | 状態 | 要点（1行） | #126 との関係 |
|---|---|---|---|---|
| 130 | Block AI botsトグル廃止に伴い、挙動ベースのAIボット制御に移行する | Closed | Training=`Disallow` に決定。Bingbot は Applebot・Googlebot と並ぶ mixed-use crawler で、`Disallow` なら検索用に通る。`Block` は Bingbot も止める。Bingbot の Disallow は0件、Security Events でブロック無し（2026-09-28、申告）。同記事の注記: Bing は Microsoft の対応（目標 2027年初頭）まで、`Disallow AI Training` が robots.txt 経由で Bing に「学習拒否」を伝えない | 隣接（Bingbot を通す前提） |
| 304 | 月次運用チェックリスト（平野さんの手作業） | Open | 月次で Training が `Block` に変わっていないか（Googlebot / Bingbot / Applebot が遮断される）を確認する項目 | 隣接 |
| 447 | Cloudflare の AI Labyrinth を使うかを再検討する | Open（`状況: 保留`） | 変更後に Security Events・AI Crawl Control で Googlebot・Bingbot・Applebot に影響が無いかを見る | 隣接 |
| 124 | Rate Limiting rulesを設定する | Open | 2026-09-29 に導入（60 req/分/IP、Managed Challenge）。条件式に `not cf.client.bot`（検証済みボットは数えない）。2026-10-02 に閾値を見直す | 隣接（Bingbot が対象外になる設計。下の手順3参照） |

**GSC・title/ の隣接**

| # | タイトル | 状態 | 要点（1行） | #126 との関係 |
|---|---|---|---|---|
| 142 | title整備（#5）の効果をSearch Consoleで測る | Open（`状況: 待ち`） | GSC の時系列の推移を追う。Bing の記述は無い | 隣接（GSC 側の同種の測定） |
| 441 | jpml_titles.html を廃止する | Open | `/jpml_titles.html` → `/title/` の 301。実装は `work/0930-olt-02`（未マージ、CHAT-0930-OLT-03 で判断待ち）。廃止方式は 10/01 の GSC 取得後に決める（handover） | 隣接。Bing 側にも旧 URL の登録が残るなら 301 の影響を受ける（Bing の登録状況は未確認） |
| 458 | URL 検査 API で sitemap の URL の登録状態と正規 URL を定期取得する | Open | Google の URL 検査 API。Bing の記述は無い | 隣接（Bing 版が要るかは未定） |
| 465 | Search Console の BigQuery への一括データエクスポートを検討する | Open（`状況: 保留`） | Google 側のみ | 隣接（弱い） |
| 113 | canonicalの方針を決める | Closed | canonical なし。Bing での `?name=` の扱いが違えば判断が割れる（#126 コメント2） | 隣接 |
| 159 | ?name= 付きURLから選手個別ページへの301マッピングを設計する | Open（`状況: 保留`） | 同上（#126 コメント2） | 隣接 |
| 219 | 選手個別ページを作り、横断情報を集約する（新サイト） | Open（`状況: 保留`） | 公開時に IndexNow を使う想定（#126 コメント3） | #126 の IndexNow の想定利用先 |
| 123 | HTMLのエッジキャッシュ（Cache Rules）を検討する | Closed | 却下。HTML は既に `cf-cache-status: HIT`（cloudflare.md:25-27）。#126 の本文の前提が解消 | #126 の前提が解消 |

### 手順3: リポジトリ内の Bing 関連（読むだけ）

**コードとして Bing・IndexNow を扱うものは無い。** リポジトリ全体（ログ除く、`assets/vendor` 除く）の検索で `bing|indexnow|msnbot|crawler hints|copilot` に一致したのは、下の docs の記述と、無関係な2件（`resource_logs.html:2012` の店名「Cafe BingGo」、`.claude/skills/cloudflare/` 配下の「Subscribing」等の語）だけ。

| 対象 | 場所 | 内容 | Bing への影響 |
|---|---|---|---|
| robots.txt（リポジトリ） | `robots.txt:1-12` | 12行。`Sitemap: https://ryoei.pro/sitemap.xml`（12行目）だけ。User-agent・Allow は Cloudflare が前置（1-3行のコメント）。Bingbot への言及なし | Bingbot への制限なし |
| robots.txt（本番、`curl` で取得、2026-09-30） | `https://ryoei.pro/robots.txt` | `User-agent: *` に `Content-Signal: search=yes,ai-train=no,use=reference`（31行目付近）。`Disallow: /` は31件のUA。**Bingbot・msnbot・BingPreview・Microsoft 系の名前は Disallow に無い**。AI 系の拒否は Amazonbot・Applebot-Extended・Bytespider・CCBot・ClaudeBot・Diffbot・Google-Extended・GPTBot・omgili・anthropic-ai・Claude-Web・cohere-ai・MistralAI-Training・meta-externalagent・GoogleOther・Baiduspider・PetalBot・AwarioRssBot・Google-CloudVertexBot・QualifiedBot・Cotoyogi・ICC-Crawler・atlassian-bot・FishBot・BorderxBot・NavuBot・SemrushBot-SWA・WARDBot・magpie-crawler・KimiBot・CitibotSiteCrawler | **Copilot 用のボットは拒否の対象外**（Microsoft の名前が無い）。ただし `Content-Signal ai-train=no` は Bing に伝わらない（Microsoft の対応待ち、docs/notes/cloudflare.md:228 の周辺と #130） |
| sitemap インデックス | `sitemap.xml`（16・20・24・28行目の `<loc>`） | `sitemap-pages.xml`（24件）・`sitemap-wayhome.xml`（39件）・`sitemap-saikyo.xml`（17件）・`sitemap-title.xml`（384件）の4本。`robots.txt` の Sitemap 行はこれを指す | Bing は 2026-09-12 に `sitemap.xml` を登録済み（25件検出。当時の件数）。以降の再送信・件数の記録は Bing 側に無し（GSC は 9/28 に再送信、検出79） |
| sitemap（書籍） | `sitemap-books.xml`（191件） | ヘッダのコメントで「公開を決めるまで sitemap.xml からは参照しない」。`scripts/generate_books_pages.py` が生成 | Bing の登録対象外（書籍は凍結中、docs/notes/books-freeze.md） |
| sitemap の生成・lastmod | `docs/notes/sitemap-lastmod.md`、`.github/workflows/sitemap-lastmod.yml`、`scripts/update_sitemap_lastmod.py` | lastmod は git から導出。Bing 固有の処理は無い | lastmod は Bing も参照しうる |
| `_headers` | `_headers`（workers.dev の行〔`https://:version.:subdomain.workers.dev/*` → `X-Robots-Tag: noindex`〕）、`/*` のセキュリティヘッダ | ryoei.pro にはタグが付かない（ホスト指定）。Bing を特別扱いする記述は無い | プレビュー URL の noindex は Bing にも効く（#114 の実測で Bing の `site:` は0件） |
| `_redirects` | `_redirects:5-27`（`/title` と大会ページの末尾スラッシュ 301）、`:6`・`:39`（`/title/` と `/title/:slug/` の 200） | title/ の URL の正規化。Bing 固有の記述は無い | `/title/` が Bing に登録されるかが #126 の 10/12 の確認事項 |
| ワークフロー | `.github/workflows/`（`fetch-gsc.yml` など16本） | **IndexNow・Bing への送信・Bing Webmaster API のワークフローは無い**（`indexnow|bing|ping` の検索で該当なし） | 案 c（自前の送信）は未実装 |
| スクリプト | `scripts/` | Bing・IndexNow の実装なし。IndexNow のキー用ファイル（`<key>.txt`）もリポジトリ直下に無い（`BingSiteAuth.xml` も無い。所有権は GSC 経由の自動引き継ぎ） | 案 c の実装は新規になる |
| llms.txt | `llms.txt` | Bing の記述なし | — |
| `docs/handover.md` | `:195`（「次にやること」の表） | #126: Bing で `title/` の登録を確認、2026-10-12 ごろ、手順は #126 のコメント（2026-09-28） | Bing の記述はここだけ（`docs/site-findings.md` は無く、`docs/gsc/` に Bing の記述は無い） |
| `docs/notes/cloudflare.md` | `:228`（Applebot・Bingbot・Googlebot は "Accountable mixed-use crawlers"）、`:249`（robots.txt で Bingbot が Disallow されていないことの確認手順）、`:251-252`（AI Crawl Control の Overview で Microsoft の許可・失敗を見る）、`:375`（GitHub Copilot 却下、無関係） | Training=`Disallow`・Search=Allow・Agent=Allow（2026-09-28、申告）。Bingbot のブロックは無し | Bingbot は通る設定 |
| `docs/notes/cloudflare.md`（Rate limiting） | `:257-274`（#124） | 条件式に `not cf.client.bot`。60 req/分/IP、Managed Challenge、Security rules の1本目（Pro の上限2本）。10/02 に見直し | 検証済みボット（Bingbot を含む）は数えない設計。Bingbot を名乗る非検証のアクセスは対象になる。**Bing の実機での確認記録は無い** |
| `docs/notes/cloudflare.md`（Crawler Hints） | **記載なし** | `Crawler Hints` の語は docs/notes・handover に無い（あるのは Early Hints〈:119-151〉・Smart Hints〈:409〉だけ）。On にした事実（2026-09-28、申告）は #126 のコメントと `docs/logs/CHAT-0924-TQ-28.md` にだけある | **設定の記録が docs/notes に無い**（Cloudflare の設定表〔`:119` の Speed 設定の現状など〕に行が無い） |
| Bot Fight Mode・Super Bot Fight Mode | `docs/notes/cloudflare.md:377`（Super Bot Fight Mode 却下、#91）、`:403` | 却下（Pro では Definitely automated のみ遮断可）。Bot Fight Mode の現況の記録は未確認（#91 を読んでいない） | Bingbot（検証済みボット）は対象外（Cloudflare の仕様）。このリポジトリの記録に Bingbot への影響の実測は無い |
| Cache Rules | `docs/notes/cloudflare.md:25-27`、#123 | HTML はすでに `cf-cache-status: HIT`。Cache Rules は却下 | Crawler Hints が使うエッジキャッシュの変化の検知は、デプロイでの置き換わり（#156）に依存 |

（`docs/logs/` には Bing の記述が多数あるが、上の表はログを除く。#130 のログ群にある Bingbot の確認は #130 の行に集約した。）

**手順3で「変えたくなったこと」（変えていない。案）**

- `docs/notes/cloudflare.md` に「Crawler Hints（On、2026-09-28、申告）」を追記する案。Speed 設定の表か、ボット系の節の近く。#126 の記録と重複するため、書くなら参照1行にする
- `docs/handover.md:195` の #126 の行に、#156 への依存（Crawler Hints の効果判定）と、未実施の「22URL突き合わせ」の扱いを足す案
- IndexNow の自前の送信（案 c）は、キーのファイル（ルートに `<key>.txt`）とデプロイ後の GitHub Actions の送信が要る。`.assetsignore`・sitemap・`_redirects` の 200 の扱いとの整合を確かめる必要がある（実装する場合の論点）

## 報告

- 状態: 判断待ち
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし（ログのみ）
- マージ: 未（平野さんの判断待ち。ログのみのため、次の指示で cloudflare へ入れるか決める）
- issue: #126（読んだだけ。コメントはしていない。「着手中」も付けていない）。他に読んだのは #114・#124・#130・#142・#156・#222・#269・#304・#413・#441・#447・#458・#465 など（表は「経過」の手順2）
- 判断が必要なこと:
  - **今すぐできること（Claude Code）**
    1. `docs/notes/cloudflare.md` に Crawler Hints の設定記録（申告値として。2026-09-28 On、出典 #126）を足す。#126 の記録にしか無いため
    2. `docs/handover.md:195` の #126 の行に、依存（#156）と「22URL突き合わせ（9/15〜16頃）は記録なし。10/12 にまとめる案」を足す
    3. 案 c（IndexNow の自前送信）の設計だけ先に作る（キー生成は平野さん、ファイルの置き場所・`.assetsignore`・送信のワークフロー・対象 URL〈`sitemap-title.xml` の差分など〉。実装・マージは 10/12 の結果を見てからでもよい）
    4. 10/01 の GSC 取得と #441（`jpml_titles.html` → `/title/` の 301、`work/0930-olt-02` 未マージ）の順序を確認し、Bing 側の旧 URL の登録有無が 301 の判断に関わるかを整理する
  - **10/12 ごろまで待つこと**
    1. Bing で `https://ryoei.pro/title/` が登録されているかの判断。Crawler Hints が効くかを見る期間（On にしたのは 9/28）。#156（女流桜花のデータ更新後の ETag 比較）の結果が出れば、Crawler Hints が動く条件（デプロイでキャッシュが置き換わる）の裏付けになる
    2. 未登録なら案 c を実装するか決める
    3. #269 の Bing Webmaster API による取得は #126 の完了後に判断（Bing 経由の流入比率を見てから）
  - **平野さんの手作業（Bing Webmaster Tools の画面。Claude Code から見られない）**
    1. 未実施のまま残っている「Bing のインデックス数と GSC の22URLの突き合わせ」（もとは 9/15〜16頃）。今すぐ見てもよい
    2. `?name=` 付き URL が Bing で個別にインデックスされているか（`site:ryoei.pro` か URL 検査。#126 コメント2の残作業）
    3. 現在の Bing の Sitemap の状態（Last submit・URLs discovered。9/12 の 25 が最後の記録。4本の sitemap に増えたはず）。必要なら `sitemap.xml` の再送信
    4. `/title/` の URL 検査を10/12 より前に1回見て、現状（未登録・クロール待ち等）を記録する
    5. Crawler Hints が On のままか（記録は申告のみ）
    6. Bing Webmaster Tools の AI Performance／Copilot 関連の項目の有無は未確認（この洗い出しでは扱っていない）
  - 前提のずれ: 指示文の「#126 の手順のコメント」は 2026-09-28 のコメント4。決めたのは案 b（Crawler Hints）で、案 c は未定。9/12 のコメントの「#156 の結果が出てから判断する」は 9/28 に変更され、#156 の結果を待たずに On にしている（#126 に理由の記述は無い）
- 未確認の項目:
  - Cloudflare の Crawler Hints の現在の設定、Bing Webmaster Tools の現状（登録数・サイトマップの状態・`/title/` の登録）、Bot Fight Mode／Rate limiting が Bingbot に及ぶ実測。すべてダッシュボード・画面の情報で、この環境から確かめられない（申告値または記録なし）
  - GitHub の検索 API は0件を返して使えなかったため、手元でのキーワード検索（482 issue・1487 コメント）に切り替えた。取得は 2026-09-30 時点。それ以降のコメントは含まない
  - `msn` に一致する issue は無い（存在しないことの確認まで）
- エラー: `search_issues` がキーワード検索で0件を返し（`Bing` でも）、`Copilot`・`MSN` は HTTP 403（レート制限）。上のとおり REST の一覧で代替した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e8e763b3）: https://github.com/retroeater/mj-logs/tree/main/guide/e8e763b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
