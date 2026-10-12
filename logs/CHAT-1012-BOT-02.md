# CHAT-1012-BOT-02

- 着手日時: 2026-10-12
- 対象issue: #304
- ブランチ: work/1012-bot
- 着手時HEAD: 0408731f6

## 指示

【Claude作成】Claude Code 向け指示：#304 に「2026-10 実施」のコメントを書き、AI クローラーへの 403 の調査と、月次チェックの自動取得の2件を起票する（コード・設定は変えない） Chat-Ref: CHAT-1012-BOT-02 マージ: ドキュメントのみ（docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-bot を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-bot origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#304 が Open で、#304 に「2026-10 実施」のコメントがまだ無いことを確かめる（あれば何もせず止まる）。

目的
#304（月次運用チェック）の 2026-10 の回の結果を記録する。あわせて、その中で見つかった AI クローラーへの 403 の調査と、月次チェックの取得の自動化を起票する。
決定（2026-10-12、平野さん）

* AI クローラーへの 403（下の (9) の AI Crawl Control）は、原因の調査を起票する。設定は変えない
* 月次チェックの自動化:
   * Cloudflare は、読み取り専用で、デプロイできない、`CLOUDFLARE_API_TOKEN` とは別名のトークンを作って API で取ってよい
   * Search Console は、取れるものは API（URL 検査 API など。#458）で代わりにし、API で取れない部分（「ページ」の件数・クロールの統計情報など）は Claude in Chrome で読む
* （参考: 2026-10-12 に BOT-01 で決めた Super Bot Fight Mode・AI Labyrinth・security.txt の見送りは記録済み）

前提（チャット側。平野さんの決定ではない）

* (9) の値は、チャット側が 2026-10-12 に Claude in Chrome で平野さんのダッシュボードを開き、ダッシュボードの画面と、同じ画面から読める API（GET のみ）で読んだもの。設定は何も変えていない
* (1)〜(3)・(7)・(8)・(10)・(11) は平野さんのスクショ・書き出し（2026-10-12）をチャット側が読んだ値
* 403 の原因の見立て（要確認）: レート制限のルール（#124、managed_challenge、`not cf.client.bot`）でチャレンジに答えられない AI クローラーが 403 になっている、または Cloudflare managed ruleset。どちらかは未確認
* 自動化の起票の材料（要確認、Code が公式ドキュメントで確かめて本文に書く）: ゾーン設定（`/zones/{id}/settings`）・ボット設定（`/zones/{id}/bot_management`、権限 Bot Management Read）・レート制限とカスタムルール（rulesets の phase entrypoint）・Crawler Hints（`/zones/{id}/flags/products/cache/changes`）・Registrar（`/accounts/{id}/registrar/domains/ryoei.pro`）・Workers Builds のトリガー・GraphQL Analytics。Search Console API は検索の数字・sitemap・URL 検査だけで、「ページ」の件数とクロールの統計情報は無い見込み
* CLAUDE.md「`CLOUDFLARE_API_TOKEN` を GitHub Secret に登録しない」（二重デプロイの防止）は変えない。新しいトークンは別名で、デプロイの権限を持たないので、この規則と両立する見込み（起票の本文にその旨を書く。CLAUDE.md の書き換えは実装の指示で行う）

手順

1. 確かめる: #304 の本文と直近のコメント（雛形は CHAT-1010-MCK-01 のログ「『2026-10 実施』のコメントの雛形」）、#458・#124・#91・#447 の本文。「AI クローラー 403」「月次 自動」「API トークン 読み取り」の語で open/closed の issue を検索する。同じ論点の open issue があれば止まる
2. #304 に下の「コメントの本文」をそのまま書く（Code が確かめて食い違った値は、直さずに報告に書いて止まる）。決定を `docs/decisions/` の合う分野（cloudflare.md・operations.md の見込み）に足す
3. 起票する（どちらも「状況:」のラベルは付けない。分野・対象のラベルは `gh label list` から合うものを選ぶ）:
   * A「AI クローラーへの 403 の原因を調べる」: 本文に下の (9) の AI Crawl Control の数値（コメントの本文から引用）・見立て・「Search・Agent は許可」の方針（docs/notes/cloudflare.md「AIクローラーの扱い」）との食い違いの可能性・調べ方の案（Security Events をクローラー名・ルールで絞る）を書く
   * B「月次チェック（#304）の取得を自動化する（Cloudflare は読み取り専用の別名トークン、Search Console は API と Chrome）」: 本文に上の決定と材料、#458 との関係、最初の実装で「そのトークンで各 API を1件ずつ呼んで通る」ことを確かめる手順を書く

止まる条件

* #304 が Closed、または「2026-10 実施」のコメントが既にある
* 同じ論点の open issue がある（A・B それぞれ）
* 決定の追記先に、この決定と矛盾する記述がある（同じ趣旨なら止めず、置き換え・拡張してよい。どう処理したかを報告に書く）
* 未マージの work/ ブランチが `docs/decisions/` の同じファイルの末尾を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

コメントの本文（#304 にそのまま書く）

```markdown
### 2026-10 実施（2026-10-12）

#### 記録

- (1) AI 検索での言及（#230）
  - 生成 AI 機能（期間: 3か月、検索タイプ: ウェブ）: 合計表示 64。jpml_pros.html?name=白銀紗希 27 / resource_dictionary.html 14 / houou_ranking.html?sheet=鳳凰 9 / jpml_pros.html 3 / jpml_test.html 2 / / 1 / houou_leagues.html 1 / houou_ranking.html 1（09-28 は 8ページ・最多 20）
  - Web Analytics のリファラ（期間: 2026-09-12〜10-12、ボット除外）: chatgpt.com 0 / perplexity.ai 0 / gemini.google.com 0 / claude.ai 0 / copilot 0。その他: None (direct) 123 / t.co 33 / search.yahoo.co.jp 5 / www.bing.com 4 / www.google.com 3 / note.com 2
  - 参考: AI Crawl Control の AI Referral Traffic（直近7日）は Google 101 / Microsoft 19（copilot.microsoft.com 1 を含む）/ Baidu 2 / DuckDuckGo 1
  - 固定クエリの計測: 未実施（記録用のブック待ち）
  - 注記: Web Analytics の Hosts に work/ ブランチのプレビュー（`work-…-mj.hirano-530.workers.dev`）が計 30 訪問入っている（全体 170）
- (2) Core Web Vitals（#262、Web Analytics、期間: 2026-09-12〜10-12、ボット除外。初回）: LCP 良好 98% / 改善 1% / 不良 2%（P50 287ms・P75 544ms・P90 1,187ms）。INP 良好 92% / 6% / 2%。CLS 良好 97% / 1% / 2%。ページ別の上位は houou_race・jpml_pros・title/。目立った要素: INP は navbar の開閉ボタン（svg）744ms（50回）、CLS は houou_race の #main 0.917（30回）・#myChart 0.357（30回）
- (3) デバイス比率: Cloudflare Web Analytics（訪問、2026-09-12〜10-12）: Mobile 95 / Desktop 74 / Tablet 1。Security Analytics（リクエスト、ボットを含む、10-05〜10-12）: Mobile 56.73k / Desktop 39.65k / Tablet 340。Search Console（09-09〜10-06、表示回数）: モバイル 76.3% / PC 22.4% / タブレット 1.3%
- (4) robots.txt: 10-01 の通知は、09-21 の保存分に AI Crawl Control の管理部分が付いていなかったことによる差。10-01・10-09・10-10 の取得は同じ。`Disallow: /` の 32件→31件は数え方の違い（Code が確認）
- (5) issue の棚卸し: 10-02〜10-03 の CHAT-1002-INV-01・CHAT-1003-INV-03・INV-04 で実施済み
- (6) #357: 10-05 の通知は片付け済み（PHT-03・PHT-04）。次は 10-12
- (7) ページ: 登録済み 442 / 未登録 1,500（理由7）: 重複（ユーザーが正規ページを選択していない）816 / 代替ページ（適切な canonical タグあり）47（10-05 から。title/?name= 等・saikyo/?match= の例）/ クロール済み未登録 217 / 検出未登録 404（saikyo/ の年度ページ等）/ 404 7 / リダイレクト 8（jpml_titles.html?name= の 301〈#441〉等）/ 5xx 1（ouka_league_by_class.html、最後のクロール 08-04、検証 09-21 開始）。09-21 比: 重複 805→816、クロール済み未登録 213→217、検出未登録 42→404、404 8→7、リダイレクト 2→8、5xx 1→1。404・5xx は増えていない
- (8) クロールの統計情報（レスポンス別、書き出しは 09-06〜10-09）: 200 83.4% / 404 10.3% / 301 4.7% / 移動（その他）0.7% / その他の 4XX 0.5% / 304 0.4%。403 の行 無し。その他の 4XX の例は4件で最後は 09-21（09-28 の基準と同じ）
- (9) Cloudflare の設定と記録（2026-10-12、チャット側が Claude in Chrome でダッシュボードと同じ画面の API〈GET のみ〉で読んだ値）:
  - 一致: AI bot policies（ai_training=disallow・ai_search=disabled・ai_user=disabled・bot_preference_sync=on。AI Labyrinth〈crawler_protection〉=disabled）/ Speed Brain off・Polish off・Rocket Loader off・HTTP/3 on・0-RTT on・Always use HTTPS on・Early Hints off / Crawler Hints on（2026-09-28 適用）/ Email Address Obfuscation off（2026-09-28 変更）/ Rate limiting「Throttle rapid HTML fetches (#124)」有効・60秒30回・managed_challenge / カスタムルール「Block non-GET/HEAD methods」有効・block / Workers Builds の本番（cloudflare）トリガー: `npx wrangler deploy`、除外 `.git/`・`docs/**`・`node_modules/**`・`workers/**`、キャッシュ無効 / Registrar: 自動更新 on・期限 2028-03-20
  - 食い違い（判断待ち）: Workers Builds の非本番トリガー（Deploy non-production branches、`npx wrangler versions upload`）の除外は `.git/`・`docs/**`・`node_modules/**` で、`workers/**` が無い。記録（docs/notes/cloudflare.md）は除外を4つ書いていて、どのトリガーかを分けていない
  - 記録の無かった行（Security → Settings、今回の値を起点にする）: Client side abuse: Continuous script monitoring off / Email Address Obfuscation off / Hotlink Protection off。Bot traffic: Configure AI bot policies・Super Bot Fight Mode（設定あり）・AI Labyrinth off。Web application exploits: Cloudflare managed ruleset on / OWASP Core off。DDoS: HTTP DDoS attack protection 常時有効 / Browser integrity check on。Fraud: Leaked credentials detection off。Precursor off
  - AI Crawl Control（直近7日、10-06〜10-12）: AI クローラーのリクエスト 4.26k（+5.2%）、許可 約2k（−39%）、失敗 約2k（+198%）。ステータス: 200 1.74k / 403 1.42k / 404 837 / 3xx 261。4xx の上位クローラー: OAI-SearchBot 194 / Perplexity-User 183 / Baidu 170 / MistralAI-User 160 / GPTBot 154 / 他 1.4k。Google・Microsoft・Apple は 4xx の上位に無し（許可: Microsoft 743・Apple 638・Google 232）→ 403 の調査を起票（#<A の番号>）
- (10) ホストの状態: ryoei.pro・www.ryoei.pro とも「問題ありません」（クロールの統計情報の書き出しのホスト表）。手動による対策: 問題は検出されませんでした。セキュリティの問題: 問題は検出されませんでした
- (11) Synthetic monitoring（最新の実行日: 2026-10-12）: / 99 / jpml_pros 87 / houou_results 63 / live/ 99 / title/ 100（09-29: 98・88・61・99・100。10点以上の低下なし）
- 分析情報（過去28日間）: クリック 124（+1,671%）/ 表示 2,916（+1,202%）。クリックの上位: jpml_pros.html?name=白銀紗希 57 / houou_ranking.html?sheet=鳳凰 30 / jpml_pros.html?name=三田不二夫 7 / jpml_pros.html 4 / / 3
- （参考）Cloudflare からの通知「Unexpected increase in bot traffic」（10-11）は設定を変えず、Super Bot Fight Mode・AI Labyrinth・security.txt は見送り（CHAT-1012-BOT-01、#91・#447）

#### 決めたこと

- #4・#230・#262・#365・#367・(12) の追加: 2026-10-10 に決定済み（CHAT-1010-MCK-02）
- AI クローラーへの 403: 原因の調査を起票（#<A の番号>）。設定は変えない（2026-10-12、平野さん）
- 月次チェックの自動化: Cloudflare は読み取り専用・デプロイ不可の別名トークンで API から取る。Search Console は API で取れるものは API（#458）、取れない部分は Claude in Chrome（#<B の番号>、2026-10-12、平野さん）
- (9) の食い違い（非本番トリガーの除外に `workers/**` が無い）: 判断待ち（記録を直すか設定を直すか）

Chat-Ref: CHAT-1012-BOT-02

```

（`#<A の番号>`・`#<B の番号>` は手順3で起票した番号に置き換える。コメントは起票の後に書いてよい）
完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-BOT-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-BOT-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-bot
- ログ: https://github.com/retroeater/mj/blob/work/1012-bot/docs/logs/CHAT-1012-BOT-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-bot
- 確認用URL: なし
- マージ: 未
- issue: #304
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0408731f）: https://github.com/retroeater/mj-logs/tree/main/guide/0408731f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0408731f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
