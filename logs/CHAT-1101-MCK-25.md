# CHAT-1101-MCK-25

- 着手日時: 2026-11-01
- 対象issue: #304
- ブランチ: work/1101-mck-record
- 着手時HEAD: 75e24890b

## 指示

【Claude作成】Claude Code 向け指示：#304 に「2026-11 実施」の記録を書き、docs/notes/cloudflare.md の Rate limiting の節に 10/29 の 429 の出どころを書き足す Chat-Ref: CHAT-1101-MCK-25 マージ: 承認済み（チャットで、2026-11-01。この指示単独で聞いた）。条件: cloudflare との差分が、docs/notes/cloudflare.md の「Rate limiting rules（2026-09-29 に導入、#124）」の節と、ログ・docs/decisions/operations.md の追記だけ 貼る時機: いつでも（CHAT-1101-MCK-24 と前後してよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-record の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-mck-record を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-record origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#304 の 2026-11 の回の値がそろった（Code の分は CHAT-1101-MCK-23、画面の分はチャット側が 2026-11-01 に平野さんの Chrome で読んだ）。#304 に「2026-11 実施」のコメントを書く。あわせて、10/29 の 429 の出どころが分かったので docs/notes/cloudflare.md に書き足す。
決定（2026-11-01、平野さん）

* 画面でしか見られない項目は、チャット側が平野さんの Chrome で読む
* #357 の片付けと (5) の深い棚卸しは今月行う（別の指示で出す。この指示では書かない）
* cloudflare.md の修正はマージしてよい（冒頭の「マージ:」）

前提（チャット側が画面で読んだ値。2026-11-01。平野さんの決定ではない）
10月の値は #304 の「2026-10 実施」のコメントを正とし、下の「（10月 …）」と食い違えば #304 の側で比べる。

* (1) Search Console の生成 AI 機能（28日、10/02〜10/29）: 表示回数 247・ページ 83（10月 64回・15ページ）。3か月では 295。Bing Webmaster Tools の AI Performance がある（引用 460、9/16〜10/29。うち 10/01〜10/29 が 432。上位の grounding query は「麻雀最強戦 2025 プロ対局」141・「麻雀 プロ 一覧」64・「麻雀最強戦2026」59）。Web Analytics の AI 参照元は、計測停止（下）のため読めない
* (2) Core Web Vitals（Web Analytics、30日。ほぼ 10/21 より前のデータ）: LCP good 99%（p50 256ms・p75 500ms・p90 860ms）、INP 95%/3%/1%、CLS 97%/2%/1%
* (3) デバイス比率: Cloudflare HTTP Traffic（30日、リクエスト）desktop 57.6%・mobile 41.8%・tablet 0.7%（10月 69.4%・30.1%・0.5%）。GSC（10/02〜10/29、クリック）は MCK-23 の値（mobile 77.9%・PC 20.4%・tablet 1.8%）
* (7) ページのインデックス: 登録 1,310（10月 425）、未登録 2,250（重複 1,010・代替 canonical 99・リダイレクト 17・soft 404 8〈新規〉・404 5・noindex 2〈新規〉・検出-未登録 860・クロール済み-未登録 247・5xx 1）
* (8) クロールの統計情報: 2,690 リクエスト・平均 104ms。ホスト ryoei.pro・www とも問題なし。200 90%・404 5%・301 4%・その他・304 は 1% 未満。403 なし
* (9) 設定と記録の照合（ダッシュボードの API を GET、docs/notes/cloudflare.md「Chrome からダッシュボードの API を読む」）: Speed 設定（HTTP/3・0-RTT・Always use HTTPS は on、Rocket Loader・Early Hints・Polish・WebP・Email Obfuscation・Speed Brain は off）、AI bot policies（ai_training=disallow・Bot Preference Sync on・crawler_protection〈AI Labyrinth〉disabled・SBFM の definitely automated は allow）、Rate limiting 2本（#124: managed_challenge・30/60秒、`/api/ask`: block・5/60秒・mitigation_timeout 600）、カスタムルール1本「Block non-GET/HEAD methods」（block）、Domain Registration（auto renew on・期限 2028-03-20・lock on）、Workers Builds の除外（mj 本番 `.git/`・`docs/**`・`node_modules/**`・`workers/**`、非本番は `workers/**` なし。mj-ask・mj-scheduler は path_includes のみ）は、cloudflare.md の記述と一致。違いは次の2点だけ: Rate limiting の数える単位は API では `ip.src` と `cf.colo.id`（cloudflare.md は「IP」）。カスタムルール「Block non-GET/HEAD methods」が cloudflare.md にあるかはチャット側で確かめていない（手順1で確かめる）
* (10) 手動による対策・セキュリティの問題: どちらも「問題は検出されませんでした」
* (11) Synthetic monitoring（Tokyo・Daily、10/31 実行）: /title/ 99・/live/ 99・/houou_results.html 100・/jpml_pros.html 99・/ 99（10月 98/95/62/99/99）
* (12) Search Console の分析情報（28日）: クリック 375（+287%）・表示 1.17万（+413%）
* (13) Billing（Oct 9–31）: ビルド 280/6,000分・Workers リクエスト 6.65k・CPU 32.13k ms・D1 読み 1.2k/書き 523・$0。ビルド1回の所要時間（Deployments）は 25〜38秒、1回だけ 1分33秒、失敗1回（36秒）。基準は約30秒（初回）
* AI Crawl Control（7日）: 合計 7k（-50.9%）・許可 6k・失敗 1k（-73.6%）。2xx 5.49k・4xx 1.35k（10月の 403 は 1.42k。4xx の内訳は画面では分からない）・3xx 479。参照 387（Google 334・Microsoft 50）
* 10/29 の 429（SEO からの依頼の一回限りの確認）: Security Events（2026-10-29 14:45〜15:05 JST）に 851件、すべて Service「Rate limiting rules」・Action Managed Challenge（#124）、UA curl/8.5.0、送信元 160.79.106.128〜137（AS396982 Google LLC）。件数は SEO の 683+172=855 とほぼ一致。#124 から返ったと見る。Managed Challenge が HEAD に 429 として返る対応は未検証
* Web Analytics の計測が 10/21 ごろから止まっている（ビーコンの送信が 503。調査は CHAT-1101-MCK-24）
* 10/31 11:00 JST ごろ、KDDI の IPv6 1つ（Chrome 156）が /pro/ に約1.18k回 Managed Challenge を受けた（平野さんご本人かは確認中。IP はコメントに書かない）
* OGP（X・LINE）の確認: 平野さんの手作業。2026-11-02 のカレンダーの予定。この指示では「確認待ち」と書く

手順

1. #304 の本文と「2026-10 実施」のコメントを読み、他セッションの着手中コメントが無いことを確かめる。docs/notes/cloudflare.md にカスタムルール「Block non-GET/HEAD methods」の記述があるかを確かめ、ログに書く（無くても足さない。「判断が必要なこと」に書く）
2. #304 に「2026-11 実施」のコメントを書く: 上の前提の値を、本文の (1)〜(13) の順に、10月の値と並べて書く（CHAT-1101-MCK-23 の Code の分の値も入れる）。冒頭に「画面の値は CHAT-1101 のチャット側が平野さんの Chrome で読んだ」「OGP は確認待ち」「Web Analytics 停止は CHAT-1101-MCK-24」と書く。(13) の基準（約30秒）を書く。本文・状態・ラベルは変えない
3. docs/notes/cloudflare.md の「Rate limiting rules（2026-09-29 に導入、#124）」の節で、10/29 の 429 の行（「原因は未確認」）を、前提の Security Events の結果（件数・時間帯・Service・Action・AS。IP の範囲は書かない）に直す。表の「数える単位」に「API では `ip.src`・`cf.colo.id`（2026-11-01、チャット側が API で読んだ）」を足す。決定を docs/decisions/operations.md に記録し、cloudflare へマージする（冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから）

止まる条件

* #304 が Closed、または他セッションの着手中コメントがある
* 冒頭の「マージ:」の条件を満たさない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-25"` は0件。`XXX` の重複は対象外。0章ゲートは実装を含まないため対象外。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 指示文の issue の state: #304 は Open
- CLAUDE.md: `git log HEAD..origin/cloudflare -- CLAUDE.md` は0件
- 作業ブランチ: work/1101-mck-record はローカル・リモートとも無く、`git checkout -b work/1101-mck-record origin/cloudflare` で作った

### 1. #304 とカスタムルールの記述

- #304 は Open・コメント22件。10-31 以降のコメントは MCK-23 の着手中（済み）だけで、他セッションの着手中は無い。本文の (1)〜(13) と「2026-10 実施」のコメントは MCK-23 で読んだものから変わっていない（本文の更新は 10-23 が最後）
- **カスタムルール「Block non-GET/HEAD methods」は docs/notes/cloudflare.md にある**（「#### カスタムルール「Block non-GET/HEAD methods」（2026-10-11 に記録）」の節。docs/notes/ask-worker.md・docs/decisions/houou.md にも経緯）。足すものは無い

### 2. #304 の「2026-11 実施」のコメント

- https://github.com/retroeater/mj/issues/304#issuecomment-6551825397
- 前提の値を (1)〜(13) の順に、10月の値と並べた。Code の分（MCK-23）: (3) Search Console のデバイス・(4)・(5)・(6)・(12) の API の参考値。冒頭に画面の値の出どころ・OGP は確認待ち・Web Analytics の停止（MCK-24）を書いた。(13) の基準は約30秒
- (5)・(6) は「今月別の指示で行う」（決定）と書いた
- 前提に無く書き足したこと: 月次の運行の `redirects` の失敗（MCK-23）のその後。保険の予約実行（`fetch-redirect-counts.yml`、2026-11-01T00:17Z の schedule、success）で `docs/redirect-counts/2026-10.csv` ができた（`git ls` で確認）
- 本文・状態・ラベルは変えていない

### 3. docs/notes/cloudflare.md

「Rate limiting rules（2026-09-29 に導入、#124）」の節の2行だけを直した（7576eed5b）:

- 表の「数える単位」: `IP` → `IP（API では `ip.src`・`cf.colo.id`。2026-11-01、チャット側が API で読んだ）`
- 10-29 の行: 「Managed Challenge の画面とは別の応答で、原因は未確認」→「出どころはこのルール: Security Events（2026-10-29 14:45〜15:05 JST）に 851件、すべて Service「Rate limiting rules」・Action Managed Challenge、UA `curl/8.5.0`、送信元は AS396982（Google LLC）。件数は SEO の 683＋172＝855 とほぼ一致（2026-11-01、チャット側が画面で読んだ）。Managed Challenge が HEAD に 429 で返る対応は未検証」（IP の範囲は書いていない）
- MCK-24 のログで「セッションの外向きの経路が返した 429 の可能性」と書いた見立ては、この結果で外れた（Cloudflare が返していた）

### 4. 決定の記録とマージ

- docs/decisions/operations.md に「2026-11-01（CHAT-1101-MCK-25）」を足した
- マージの条件（cloudflare との差分が cloudflare.md の Rate limiting の節・ログ・operations.md の追記だけ）を `git diff origin/cloudflare...HEAD --stat` で確かめてからマージする

## 報告

- 状態: 判断待ち
- ブランチ: work/1101-mck-record
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-MCK-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-mck-record
- 確認用URL: なし
- マージ: 未（このあと cloudflare へ入れる）
- issue: #304（「2026-11 実施」のコメント。本文・状態・ラベルは変えていない）
- 判断が必要なこと:
  - OGP（X・LINE）の確認の結果（2026-11-02 の平野さんの手作業）を #304 に足すか
  - 10-31 の KDDI の IPv6 への Managed Challenge 約1.18k回が平野さんご本人か（確認中）。ご本人なら #124 のルールの閾値・対象の見直しが要るか
  - Web Analytics の停止の直し方（CHAT-1101-MCK-24 の判断待ちのまま）
- 未確認の項目:
  - Managed Challenge が HEAD の要求に 429 で返る対応（Cloudflare の仕様として確かめていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f64aef0）: https://github.com/retroeater/mj-logs/tree/main/guide/4f64aef0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f64aef0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/27bafd9b.md
