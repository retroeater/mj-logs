# CHAT-1104-LGN-27

- 着手日時: 2026-11-04
- 対象issue: #663
- ブランチ: work/1104-lgn-policy（origin/cloudflare から作った）
- 着手時HEAD: dd0746383

## 指示

【Claude作成】Claude Code 向け指示：#663 サイト全体の利用規約・プライバシーポリシーのページ /policy/ を作る（案の文でプレビューまで） Chat-Ref: CHAT-1104-LGN-27 マージ: 判断待ちで止まる（平野さんが文を確かめてから、マージの指示を別に出す。マージがそのまま公開になる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-policy の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgn-policy を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-policy origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#663。Google Maps Platform の Places API を使うアプリは、Google の利用規約・プライバシーポリシーを組み込んだ利用規約・プライバシーポリシーを公開する必要がある（Places API の Policies。CHAT-1029-LGN-04 のログ `## 経過`「手順1」に引用）。ryoei.pro にはまだ無い。サイト全体の利用規約・プライバシーポリシーを1ページで作る。
決定（2026-11-04、平野さん）

* サイト全体で1ページにする（例 /policy/）。利用規約とプライバシーポリシーを並べる。Google Maps の規約に加え、サイト全体の扱いも書く
* 文を平野さんが確かめてから公開する。規約のページは隠す理由が無いので、未公開で入れる段（段1）は飛ばし、文を確かめた後のマージをそのまま公開にする（docs/new-page-checklist.md の段1・段2の例外。理由を docs/decisions に書く）

前提（チャット側。平野さんの決定ではない）

* 書く内容は、リポジトリの実物を調べて事実だけを書く（推測で書かない。調べた所と結果をログの表にする）。調べる候補:
   * アクセス解析: Cloudflare Web Analytics（`_headers` の CSP、Cookie を使わない見込み。要確認）
   * ブラウザに保存するもの: localStorage のキー（`mj:` で始まるもの。`git grep -n "localStorage"`）と、それぞれ何を保存するか
   * 現在地: log/ の「現在地から探す」（押したときだけ位置を聞き、送らず保存しない。`/log/*` の `Permissions-Policy`）
   * AI 検索（データ・コンシェルジュ、Worker `mj-ask`）: 質問の文が Worker を通って Claude API に送られること、Worker で何を記録しているか（docs/notes/ask-worker.md と workers/ask/ を読む）
   * 外部のサービス: Google マップへのリンク、Places API（裏側の処理で place_id と営業状況の判定に使う。ページの閲覧者の情報は送らない）、X・YouTube などの画像・埋め込み（`img-src` など）
   * 掲載している内容の出典: 連盟の公開情報・X の投稿・国土数値情報（CC BY 4.0）など
* Google の規約・プライバシーポリシーを組み込む書き方: 「Google マップの利用には Google の利用規約（https://maps.google.com/help/terms_maps/）と Google プライバシーポリシー（https://policies.google.com/privacy）が適用されます」の形（リンクの今の URL は Places API の Policies のページで確かめる。要確認）
* 運営者は個人（平野良栄）。問い合わせの窓口は、今サイトに載っている連絡先（無ければ X の @retroeater）を仮置きにする。施行日・改定日の書き方も仮置き
* 文は短く平易に（法的な書き方は最小限）。私（チャット側）も Code も弁護士ではないので、ページに法的な助言の意味を持たせない
* ページの形は新サイトの基本の形（白地のページ。新しい共通ナビの対象に入れるかは、TOP 系列の work/1104-top がマージ済みならその仕組みに足し、未マージなら `navbar.js` の白地の対象の if の連なりに `/policy/` を足す）
* 導線: log/ の出典の「(i)」の近くと、ナビのメニューの最後（「その他」または TOP 系列の新しいメニューのシートの末尾）。navbar の項目の名前は「利用規約・プライバシー」の案
* 段2のチェックリストのうち、公開でまとめて行うもの（サイトマップ・`llms.txt`・navbar・title/description/og）は、この指示で作っておき、noindex は付けない。マージ（公開）は平野さんが文を確かめた後
* docs: docs/notes/static-generation.md「ページの一覧」、docs/decisions、ページの設計メモ（短く）

手順

1. 上の候補を調べてログの表にする（項目・実物の場所・事実）。#663 に着手中のコメントを残す
2. /policy/ を作る（調べた事実だけで案の文を書く。導線・サイトマップ・`llms.txt` も）。検査（`scripts/tests/`）が要れば足す
3. 生成するなら `regenerate-page.yml` を作業ブランチの ref で手動実行する。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見る URL と、案の文の全文（ログの `## 経過` に）、仮置きの一覧（運営者の書き方・問い合わせの窓口・施行日など）を報告に書いて止まる

止まる条件

* 調べて、書くべき事実が分からない所がある（推測で書かず、「判断が必要なこと」に書いて、その所は空けて進めてよい）
* 作るのに、新しい依存・外部のスクリプト・CSP の変更が要る
* 未マージの work/ ブランチが、この作業で変える共通の部品（`navbar.js`・`style.css`・`sitemap*.xml`・`llms.txt`・log/ の生成）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。TOP 系列の work/1104-top との重なりは、どこが重なるかと、マージするときの解き方の案を書いて進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（平野さんが文を確かめるまで cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`CHAT-1104-LGN-27` のコミットなし（`CHAT-1104-LGN-26` のコミット・ログも無い。この指示文に LGN-26 の説明は無い）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1104-lgn-policy origin/cloudflare`（dd0746383）。#663 に着手中のコメント（他セッションの着手中コメントは無かった）
- TOP 系列の work/1104-top は cloudflare にマージ済み（`git merge-base --is-ancestor origin/work/1104-top origin/cloudflare` が真）。新しいナビ（帯＋メニューのシート）は、`navbar.js` の白地の対象（`newNavCurrent`）のページで、`document.write` の項目を `window.mjNavItems` に読んでシートに出す仕組み
- 調べた事実（手順1。書く内容はこの表の事実だけ）:

| 項目 | 実物の場所 | 事実 |
|---|---|---|
| アクセス解析 | `scripts/lib/page.py` の `HEAD_TEMPLATE`（169行目）・手書きのページ（index.html・404.html・jpml_pros.html ほか）・`_headers` の CSP（`script-src … https://static.cloudflareinsights.com`、`connect-src … https://cloudflareinsights.com`） | 全ページで Cloudflare Web Analytics のビーコン（`https://static.cloudflareinsights.com/beacon.min.js`、手動のスニペット）を読み込む。送り先は `https://cloudflareinsights.com/cdn-cgi/rum`（docs/notes/cloudflare.md）。Cookie を使うかはリポジトリからは確かめられない（ページの JS に `document.cookie` は無い） |
| サイトの Cookie・ほかの保存 | `git grep "document.cookie\|sessionStorage\|indexedDB"`（assets/vendor を除く） | どれも無い |
| ブラウザに保存するもの（localStorage） | `assets/league.js` 59〜60・442行目、`assets/saikyo.js` 697行目、`assets/pro.js` 9〜10行目、`assets/ryoei/world.js` 6行目、生成スクリプトの `recentKey` | `mj:recent-players`（鳳凰戦の最近見た選手）・`mj:recent-players:ouka`・`:jwrc`・`:wrcr`・`:saikyo`（女流桜花・WRC・WRC-R・最強戦の最近見た選手）、`mj:favorites`（連盟プロのお気に入り。`[{slug, name}]`）、`mj:ryoei-sound`（r/ の音のオン・オフ）。どれもブラウザの中だけで読み書きし、送る処理は無い |
| 現在地 | `assets/log.js`（`navigator.geolocation.getCurrentPosition` はボタンを押したときだけ）、`_headers` の `/log/*` の `Permissions-Policy: geolocation=(self)`、docs/notes/log-page.md | log/ の「現在地から探す」を押したときだけ位置を聞く。位置はブラウザの中で店の駅との距離の並べ替えにだけ使い、送らず保存しない（URL・localStorage にも入れない）。ほかのページは `geolocation=()` で使えない |
| AI の質問欄（データ・コンシェルジュ、Worker `mj-ask`） | `workers/ask/src/ask.mjs`（`MODEL = 'claude-haiku-5-5'`、`https://api.anthropic.com/v1/messages`、`questions` への INSERT 722行目、`ip_counts` 300〜311行目）、`workers/ask/schema.sql`、`workers/ask/src/weekly.mjs`（`RETENTION_DAYS = 90`）、docs/notes/ask-worker.md | 質問の文は `ryoei.pro/api/*` の Worker が受け、Anthropic の Claude API に送って答えを作る。記録は日時・質問文・結果・型・答えの本文・引数で、IP は書かない。週1回、運営者だけが見られるスプレッドシート（共有はオーナーとサービスアカウントだけ）に書き出し、90日を過ぎた行は D1 とスプレッドシートの両方から消す。1日の回数の上限のため、IP を「Secret の塩・日付・IP」の SHA-256 にして数え、前日以前の行は消す |
| Google マップ・Places API | `scripts/convert_x_posts.py`（Text Search の place_id だけ）、`scripts/lib/url.py` の `maps_url()`、`assets/log.js` の店名のリンク | 投稿の取り込み（GitHub Actions、サーバー側）で Places API から店の place_id を取り、log/ の店名から Google マップへリンクする（地図の埋め込みは無い）。閲覧者の情報を Places API へ送る処理は無い |
| 外部の画像・動画 | `_headers` の `img-src 'self' data: https:`・`frame-src https://www.youtube-nocookie.com`、生成スクリプトの画像の URL | X（`pbs.twimg.com`）・YouTube（`img.youtube.com`・`i.ytimg.com`）・ABEMA（`image.p-c2-x.abema-tv.com`）の画像を表示する。r/ は YouTube の動画を `youtube-nocookie.com` で埋め込む。表示するときブラウザがそれぞれのサービスに接続する |
| 出典 | 各ページの出典の表記、docs/notes/log-stations.md | 選手・成績は日本プロ麻雀連盟などの公開情報、ログは X の投稿、log/ の駅は国土数値情報（CC BY 4.0、「…を加工して作成」） |
| 運営者・連絡先 | `index.html` 62行目（`https://twitter.com/retroeater`）、`r/index.html`（`https://x.com/retroeater`） | 運営者は平野良栄（個人）。サイトに載っている連絡先は X の @retroeater だけ（メール・問い合わせの欄は無い） |
| Google の規約の要件 | Google Maps Platform Terms of Service（https://cloud.google.com/maps-platform/terms/、Last modified August 26, 2026、curl で取得）の 3.2.2(a)(i) | 「The Customer Application’s terms of service will (A) notify users that the Customer Application includes Google Maps features and content; and (B) state that use of Google Maps features and content is subject to the then-current versions of the: (1) Google Maps End User Additional Terms of Service at https://maps.google.com/help/terms_maps/ ; and (2) Google Privacy Policy at https://policies.google.com/privacy .」 |

  - 指示の前提の「リンクの今の URL は Places API の Policies のページで確かめる」: Places API の Policies のページ（https://developers.google.com/maps/documentation/places/web-service/policies）には、今は利用規約・プライバシーポリシーの要件とリンクが見当たらなかった（LGN-04 の 2026-10-29 の読みには「Applications using the Places API must provide publicly accessible Terms of Use and a Privacy Policy …」があった）。要件と URL は規約の本体 3.2.2(a)(i) で確かめた（上の表）。URL は前提の案と同じ

- 手順2（9f2da07a7）: `scripts/generate_policy_page.py`（文はファイルの定数、シートは読まない。`render_content`・`PageMeta` の既存の形、白地 `mj-race mj-houou`）→ `policy/index.html`。再生成の対象 `policy_page`（`regenerate.py` の `OUTPUT_OVERRIDES`）。`_redirects` に `/policy → /policy/ 301` と `/policy/ → index.html 200`（log/ と同じ形）。navbar.js の最後の1段の項目「利用規約・プライバシー<br>Policy」と、`/policy/` の下で白地のナビにする分岐。log/ の出典の「(i)」の中にリンク（`SOURCE_HTML`）。sitemap-pages.xml に `/policy/`（56件。lastmod は `update_sitemap_lastmod.py --from-git` で差分なし＝導出と同じ、ElementTree で parse OK）。llms.txt に節「サイトについて」。style.css に `.mj-policy-*` の3つ。noindex なし
- テスト: `test_policy_page.py`（Google の2つの URL・head・noindex 無し・サイトマップと llms.txt）、`test_navbar.py`（最後の項目と白地の分岐）、`test_log_pages.py`（出典の中のリンク）。`test_top_page.py` の新しいナビのまとまりの期待に「利用規約・プライバシー」を足した（NAV_GROUPS に無い項目は最後に自分のまとまりになる、`assets/nav.js` の既存の動き）
- docs（7d52f0e64・9214a447e）: docs/notes/policy-page.md（新規、短い設計メモ）、static-generation.md「ページの一覧」（段落・表の行・sitemap-pages.xml の件数）、log-page.md（出典の「(i)」の中身）、handover.md 6章の表に policy-page.md、docs/decisions/operations.md
- 未マージの work/ ブランチとの重なり（`git branch -r --no-merged origin/cloudflare`、変える共通の部品のファイル）: work/1028-lgn（_redirects・navbar.js・generate_log_pages.py・style.css。2026-10-29 の古いブランチで、cloudflare とすでに衝突している。`git merge-tree` でこのブランチと比べて増える衝突は、このブランチで触っていない `generate_resource_logs.py` の modify/delete だけ）、work/1101-prp-pub（llms.txt・navbar.js・sitemap-pages.xml・style.css。navbar.js は連盟プロの行と pro/ の分岐のコメントで、同じ行ではない。増える衝突なし）、work/1104-top2（style.css。衝突なし）。止まる条件（同じ行・同じ関数）には当たらない
- 再生成: regenerate-page.yml を work/1104-lgn-policy で `target_page: log_pages`（run 41764291070、success）→ 728b9acb3 `chore: regenerate log/ …`（log/index.html の1行＝出典のリンク）。`target_page: policy_page`（run 41764490394、success、差分なし＝手元の生成と同じ）
- テスト: `python3 -m unittest discover -s scripts/tests` OK（1933件）、`node --test scripts/tests/js/*.test.js .github/actions/upsert-issue/test/*.test.js` pass 40・fail 0
- プレビュー（UA に `mj-internal`）: `/policy/` 200、`/policy` 301 → `/policy/`、`/log/` 200 で `href="/policy/"` 1件、navbar.js に `/policy/` 2件。`/policy/` の `x-robots-tag: noindex` は `_headers` の workers.dev のホストだけのルール（本番には付かない）。Playwright（390px）で白地のナビ、`window.mjNavItems` の最後が「利用規約・プライバシー」
- 旧（黒地）ナビの幅（rh_paifu.html、Playwright、ナビの高さ）: プレビューは 992px で 104px（最後の項目が3行に折り返す）、1100・1200・1400px で 80px。本番（cloudflare）は 992px でも 80px。→ 判断が必要なこと
- 自己レビュー（docs/notes/self-review.md、新しい文脈のサブエージェント1回）:

| 指摘 | 扱い |
|---|---|
| `test_handover_notes_table` が落ちる（policy-page.md が handover.md 6章の表に無い。最初のテストの実行は docs を足す前だった） | 直した（9214a447e） |
| AI の質問の記録は週次で運営者の Google スプレッドシートにも書き出す（`workers/ask/src/weekly.mjs`、90日で両方から消す） | 直した（46b78b2cd、「このサイトのサーバーと運営者の Google スプレッドシートに90日まで」） |
| 外部の画像は X・YouTube・ABEMA のほかに楽天ブックス・note・Ameba など | 直した（46b78b2cd、「X・YouTube・ABEMA・楽天ブックスなど、外部のサイトの画像」） |
| IP の値は「当日だけ」ではなく、次の質問のときに前日以前を消す（`ask.mjs` の `DELETE FROM ip_counts WHERE day < ?`） | 直した（46b78b2cd、「前日以前の値は消します」） |
| 未公開の pro/・r/ の機能（お気に入り・音・YouTube の埋め込み）を書いている | 判断が必要なことへ（URL では開けるので誤りではない） |
| 旧ナビの lg の境目の幅が未確認 | 確かめた（上の行）。判断が必要なことへ |
| `rel="noopener"` は既存の形に無い | 直した（46b78b2cd、外した） |
| 制定日の仮置きの直し忘れ | 判断が必要なことへ（仮置きの一覧） |

  - 2回目の往復はしていない（直したのは指摘の文をそのまま入れた文言と1行の表で、テスト OK・再生成の差分なしで確かめた）
- 案の文の全文（46b78b2cd の policy/index.html。リンクは [文](URL)）:

  > #### 利用規約・プライバシーポリシー
  > ##### 利用規約
  > - このサイトは、平野良栄が個人で運営しています。
  > - 掲載している選手・成績などは、日本プロ麻雀連盟などの公開情報や X の投稿をもとに運営者がまとめたものです。正確さ・新しさは保証しません。
  > - 「ログ」（/log/）は Google マップの機能・内容（店の Google マップの ID と、Google マップへのリンク）を含みます。Google マップの機能・内容の利用には、そのときどきの [Google マップ／Google Earth 追加利用規約](https://maps.google.com/help/terms_maps/)と [Google プライバシーポリシー](https://policies.google.com/privacy)が適用されます。
  > - AI の質問欄の答えは AI（Anthropic の Claude）が作ります。誤りを含むことがあります。
  > - 「ログ」の駅・駅の位置・都道府県は、国土数値情報（国土交通省、CC BY 4.0）を加工して作成しています。
  > ##### プライバシーポリシー
  > - アクセス解析: ページの閲覧を数えるため、Cloudflare Web Analytics を使っています。
  > - ブラウザに保存するもの: 最近見た選手（鳳凰戦・女流桜花・WRC リーグ・WRC-R リーグ・最強戦）、連盟プロのお気に入り、「良栄」のページの音のオン・オフを、お使いのブラウザ（localStorage）にだけ保存します。サイトへは送りません。ブラウザのサイトデータを消すと消えます。
  > - 現在地: 「ログ」で「現在地から探す」を押したときだけ、ブラウザが位置の利用の許可を求めます。位置はお使いの端末の中で近い駅の順に並べるためだけに使い、送ったり保存したりしません。
  > - AI の質問欄: 質問の文はこのサイトのサーバーを通って Anthropic の Claude API に送られ、答えを作るのに使われます。質問の文と答えは、このサイトのサーバーと運営者の Google スプレッドシートに90日まで記録し、その後に消します。IP アドレスは記録しません（1日の回数の上限を数えるため、IP アドレスを元に戻せない形に変えた値を使い、前日以前の値は消します）。
  > - 外部のサービス: X・YouTube・ABEMA・楽天ブックスなど、外部のサイトの画像を表示し、一部のページで YouTube の動画を埋め込んでいます。表示するとき、お使いのブラウザはそれぞれのサービスに接続します。
  > ##### 問い合わせ
  > X の [@retroeater](https://x.com/retroeater)へ
  > 2026年11月4日 制定
- 公開対象の検査（check-assets.yml の check）: 9214a447e で失敗（「許可リストに無い最上位の項目…: policy」）。allowed に `policy` を足した（d60d968cf。log・exam・r と同じ形の1行）。work/ への push で走る同じワークフローで、d60d968cf の check は success、Workers Builds: mj も success

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-lgn-policy
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-policy/docs/logs/CHAT-1104-LGN-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-policy
- 確認用URL: プレビューあり（URL は最終報告）。`/policy/`・`/log/`（出典の「(i)」の中のリンク）・ナビの最後の項目。この版の印: `/policy/` の「外部のサービス」に「楽天ブックスなど」
- マージ: 未（指示のとおり、平野さんが文を確かめてからのマージの指示を待つ。マージがそのまま公開）
- issue: #663
- 判断が必要なこと: (1) 案の文（`## 経過` の全文）の確認。仮置きの一覧: 運営者の書き方（「平野良栄が個人で運営」）、問い合わせの窓口（X の @retroeater。サイトに載っている連絡先はこれだけ）、制定日（「2026年11月4日 制定」、`ESTABLISHED`。公開の日にそろえる）、ナビの項目の名前と位置（「利用規約・プライバシー」を最後の1段の項目に。新しいナビのシートでは最後に1つのまとまり）、免責の書き方（正確さ・新しさを保証しない、AI の答えは誤りを含む） (2) Cloudflare Web Analytics が Cookie を使わないことはリポジトリからは確かめられないので、文に書いていない（書くなら平野さんが Cloudflare の説明で確かめる） (3) 未公開の pro/（連盟プロのお気に入り）・r/（音のオン・オフ、YouTube の埋め込み）の機能を書いている。公開前の名前を出してよいか (4) 旧（黒地）ナビは 992px 前後で最後の項目が3行に折り返し、ナビが 80px → 104px に高くなる（1100px 以上は変わらない）。名前を短くする（例「規約」）か、このままか (5) 指示の前提の「リンクの URL は Places API の Policies のページで確かめる」は、そのページに要件が無くなっていたので、Google Maps Platform Terms of Service 3.2.2(a)(i) で確かめた（`## 経過`） (6) 質問欄の注意書き（「ご質問の内容はサービス改善のため利用させていただく場合があります」）の「改善のため」は、実物で使い方を確かめられないので文に入れていない
- 未確認の項目: 実機（iPhone）での見え方（Playwright の 390px では崩れなし）
- エラー: 公開対象の検査の失敗（allowed への追加で解消、`## 経過`）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5a8971e1）: https://github.com/retroeater/mj-logs/tree/main/guide/5a8971e1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
