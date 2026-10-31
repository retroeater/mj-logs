# CHAT-1101-MCK-23

- 着手日時: 2026-10-31
- 対象issue: #304
- ブランチ: work/1101-mck-304
- 着手時HEAD: 7aaff3045

## 指示

【Claude作成】Claude Code 向け指示：#304 月次運用チェック（2026-11 の回）の項目を読み出し、Code でできる項目を実施して、平野さん・チャット側が見る項目の手順を作る
Chat-Ref: CHAT-1101-MCK-23
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-304 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-304 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-304 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#304（月次運用チェック）の 2026-11 の回を始める。チャット側からは #304 の本文と10月の記録が読めない（mj-logs から CHAT-1010-MCK-01 のログが消えている）ので、まず Code が項目を読み出し、Code でできる項目は実施し、残りを平野さん（またはチャット側が平野さんの Chrome で）見る手順にする。#304 への記録（「2026-11 実施」のコメント）は、全項目がそろってから別の指示で書く。

### 決定（平野さん・各系列のチャット）

- 月次チェックは MCK 系列のチャットだけが行う（2026-10-13 の申送り）
- (13) のビルド1回の所要時間の伸びの目安は今は決めない。11月の回で基準を取る（2026-10-21、平野さん）
- 本番の同時取得の 429 の確認は、11月の回で一回限り行い、#304 の本文には足さない（2026-10-30、MCK のチャットの判断。SEO のチャットからの依頼）

### 前提（チャット側。平野さんの決定ではない）

- 本文の項目は (1)〜(13)（CHAT-1021-MCK-21・CHAT-1023-MCK-22 のログ）。11月の回で追加で見ること（カレンダーの予定の説明による）:
  - AI Crawl Control の 403 を10月と見比べる（CHAT-1012-ACF-01 の持ち越し。10月の値は #304 の「2026-10 実施」のコメント）
  - (13) は初回。直近のビルド1回の所要時間を控えて基準にする
  - 2026-10-29 05:50〜06:00 UTC ごろの 429（公開中の1,450ページに HEAD を同時4本で 683件、同時2本で172件、逐次は0件。出典は mj の docs/logs/CHAT-1029-SEO-01.md の `## 経過`「指摘の一覧（全件）」の①-7）が、どのルール・サービスから返ったか。docs/notes/cloudflare.md は Rate limiting を「Managed Challenge、30回/分」と書いていて食い違う
- #262（Core Web Vitals）・#230（AI 検索の言及）・デバイス比率の月次記録も #304 に集約されている（要確認）。#230 の固定クエリの計測は自動化の話があった（CHAT-1012-ACF-02。今どうなっているかは要確認）
- Cloudflare のダッシュボード・Search Console・Billing など画面でしか見られない項目は、チャット側が平野さんの Chrome（ログイン済み）から読める（docs/notes/cloudflare.md、2026-10-12 の BOT-02〜05）。Code は画面を見られないので、その項目は「平野さんの Chrome で、どの画面のどこを見るか」の手順にする
- 429 の確認は、Code が Cloudflare の API（GraphQL Analytics の firewall events など）で見られるなら Code が行う（`CF_ANALYTICS_TOKEN` の Secret がある。権限で見られなければ、画面の手順にする）

## 手順

1. #304 の本文の (1)〜(13) と、10月の記録（「2026-10 実施」のコメント）を読み、着手中のコメントを残す（本文・状態・ラベルは変えない）。各項目を表にする: 項目・見る場所（画面の名前と URL、または API・ワークフロー）・Code でできるか（できる／平野さんの画面）・10月の値（比べる項目は）
2. Code でできる項目を実施し、結果を表に入れる（例: ワークフローの実行状況、API で取れる使用量・件数、Synthetic monitoring の結果など）。前提の 429 の確認も、API で見られるなら行う（どのルール・サービスの 429 か、件数と時刻）。見られなければ理由を書く
3. 残りの項目を「平野さん（またはチャット側が平野さんの Chrome で）見る手順」として、画面の名前・直接開ける URL・見る値・10月の値をまとめてログに書く。「判断が必要なこと」に、10月と比べて大きく変わった値があれば書く

## 止まる条件

- #304 が Closed、または他セッションの着手中コメントがある
- 項目が (1)〜(13) でない（数を書いて、読み出しだけで止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-23"` は0件。`XXX` の重複は対象外（MCK 系列の続き）。0章ゲートは実装を含まないため対象外。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 指示文の issue の state: #304 は Open（2026-10-31 に GitHub の API で確認）
- CLAUDE.md: `origin/cloudflare` で2件変わっていた（5040b675c・bda28cd7e。「ガイド文とコードが食い違ったら既存のコードに従う」「コードなどを変えた指示ではサブエージェントのレビュー」）。この指示はコードを変えないので、レビューは対象外
- 作業ブランチ: work/1101-mck-304 はローカル・リモートとも無く、`git checkout -b work/1101-mck-304 origin/cloudflare` で作った
- #304 の着手中のコメント: https://github.com/retroeater/mj/issues/304#issuecomment-6550423970 。ほかの着手中は MCK-21・MCK-22（どちらも済み）だけで、今動いているものは無い。10-23 以降のコメントはこの着手中の1件だけ

### 1. 項目（本文の (1)〜(13)）と11月の追加

本文の「毎月の手作業」は (1)〜(13) の13項目（止まる条件に当たらない）。11月の回で追加で見るものは、指示文の前提の3つに加えて、#304 のコメント（CHAT-1012-ACF-05、2026-10-12 平野さんの決定）の2つ（Bing の AI Performance、title/ の OGP カード）がある。#447（AI Labyrinth、10月の記録に「11月の回」とある）は 2026-10-12 に Closed なので外した。

「Code」の列: ○＝Code が実施した、△＝一部だけ（残りは画面）、×＝平野さんの画面（Code は画面を見られない。Cloudflare のトークンはこのセッションに無い〈環境変数に無い。`CF_ANALYTICS_TOKEN` は Actions の Secret だけ〉）。

| 項目 | 見る場所 | Code | 10月の値（2026-10 実施のコメント） | 11月（Code の結果） |
|---|---|---|---|---|
| (1) AI 検索での言及（#230） | Search Console の生成 AI 機能・Web Analytics のリファラ・AI Crawl Control の紹介 | × | 生成 AI 機能 3か月 64回・15ページ。AI のリファラ5つとも 0 | 固定クエリの計測の自動化は #230 で 2026-10-12 から「状況: 保留」（再開の手順は #230 のコメント）。11月も画面で見る |
| (2) Core Web Vitals（#262） | Web Analytics → Core Web Vitals | × | LCP 良好 98%・p75 624ms。INP 良好 92%。CLS 良好 97% | なし（画面） |
| (3) デバイス比率 | Cloudflare の HTTP Traffic（Top device types）・Search Console のデバイス別 | △ | Cloudflare desktop 69.4% / mobile 30.1% / tablet 0.5%。Search Console（09-09〜10-06、表示回数）モバイル 76.3% / PC 22.4% / タブレット 1.3% | Search Console（10-02〜10-29、表示回数 11,653）: モバイル 77.9% / PC 20.4% / タブレット 1.8%（`docs/gsc/2026-11-01/` の device.csv から計算）。Cloudflare の側は画面 |
| (4) robots.txt の差分 | #304 への自動のコメント（`fetch-gsc.yml`） | ○ | 10-01 の通知は保存分の違いによるもの。一覧は変わっていない | 10-01 の後、差分の通知は付いていない。`docs/gsc/2026-11-01/robots.txt`（10-31 18:00 UTC の取得）は `docs/gsc/2026-10-09/robots.txt` と同じ（`diff` で差なし） |
| (5) issue の棚卸し | GitHub の issue | △ | 10-02〜10-03 の INV で実施 | 下の「(5) の結果」 |
| (6) #357 の通知 | #357 のコメント（`cleanup-logs.yml`） | ○ | 10-05 159件（片付け済み）・10-12 101件 | 10-12 の後の通知は3件で、どれにも片付けのコメントが付いていない: 10-18（残り212件・削除130件・①判断待ち中断73件）、10-25 17:01 UTC（198件・272件・155件）、10-25 23:21 UTC（165件・35件・122件） |
| (7) インデックスの登録（ページ） | Search Console → ページ | × | 登録済み 425 / 未登録 1,510（重複 811・代替 40・404 7・リダイレクト 7・検出未登録 436・クロール済み未登録 211・5xx 1） | なし（API で取れない） |
| (8) クロールの統計情報の 403 | Search Console → 設定 → クロールの統計情報 | × | 403 の行は無し | なし（画面） |
| (9) Cloudflare の設定と記録の照合 | ダッシュボードの各画面と `docs/notes/cloudflare.md` | × | 食い違い無し（補足: 非本番の Builds の除外は受け入れ済み） | 10月の後に記録に足された設定: Rate limiting の2本目 `Throttle /api/ask (#598)`（10-18 導入、Block 10分・429）。照合の対象に入れる |
| (10) ホストの状態・手動による対策・セキュリティの問題 | Search Console | × | 3つとも問題なし | なし（画面） |
| (11) Synthetic monitoring | Speed → Synthetic monitoring | × | / 98・jpml_pros 95・houou_results 62・live/ 99・title/ 99（10-11） | なし（画面） |
| (12) 分析情報 | Search Console → 分析情報 | △ | クリック 124（+1,671%）・表示 2,920 | 参考（API の page.csv・query.csv、10-02〜10-29 と 09-09〜10-06 の比較）: クリックの合計 109 → 375、表示 2,582 → 11,653。クリックの伸びが大きいページは `/houou/ranking/total/`（0 → 27）・選手個別ページ（名前付きの URL）3件（0 → 18・16・11）・`/title/`（0 → 14）・`/saikyo/2026.html`（0 → 10）。画面の「分析情報」の値は画面で控える |
| (13) Workers Paid の使用量・ビルド1回の所要時間 | Billing・Workers & Pages → mj → Deployments | △ | 初回（10-21 のビルド1回は 3〜4 分、BIL の記録） | 下の「(13) の結果」。Billing は画面 |
| 追加: AI Crawl Control の 403 | AI Crawl Control → Overview・Security | × | 直近7日 失敗 2k（+199%）、403 1.42k。クローラー別の許可/失敗は10月のコメントの表 | なし（画面）。#554（原因の調査）は 10-12 から動いていない |
| 追加: 10-29 の 429 | Security Events・HTTP Traffic（または GraphQL Analytics） | × | — | 下の「429 の結果」 |
| 追加: Bing の AI Performance／Copilot の項目の有無 | Bing Webmaster Tools | × | — | なし（画面） |
| 追加: title/ の OGP カードを X・LINE で本番確認 | X・LINE のシェアの表示 | × | 記録は見本の2枚だけ | なし（画面） |

#### (5) の結果（Code が一覧にできる分）

- Open の issue は 214件（2026-10-31、GitHub の API）。うち「状況: 保留」は 51件
- 90日以上コメントも更新も無いもの: **0件**（最も古い更新は #105 の 2026-09-11）。30日以上（10-01 より前）の更新なしは 47件
- 「保留の理由が解消しているもの」: 保留の 51件の本文で、保留・再判断・完了後・待つ などを含む行が挙げる issue を機械的に調べた。挙げた issue がすべて Closed なのは #465 だけで、#465 の行は「#269 の API 取得で足りている」という見送りの理由で、待ちの条件ではない。**候補は0件**（機械的な照合の範囲。待ちの条件を issue 番号で書いていない保留は拾えない）
- 「ほかの issue の完了で不要になったもの」「#296 の子で新サイト送りをやめたほうがよいもの」は中身の判断が要るので、この指示では一覧にしていない（必要なら別の指示で）

#### (13) の結果（ビルド1回の所要時間の基準）

- check-run「Workers Builds: mj」は `started_at` と `completed_at` が同じ時刻で、所要時間が出ない。代わりに **コミットの時刻から check-run の完了までの秒数**を、cloudflare の first-parent の 2026-10-29〜31 の本番ビルド 25回で測った: **最短 31秒・中央値 59秒・最長 109秒**（push の待ちと Builds の待ちを含むので上限の目安。人が push したコミットはコミットから push までの時間も入る）。Actions のコミット（push はコミットの直後）は 31〜72秒
- 10-21 の「3〜4 分」より短い。Deployments の画面の所要時間で確かめてから基準にする（下の手順）
- 測り方: `gh api repos/retroeater/mj/commits/<SHA>/check-runs` の completed_at と `git log -1 --format=%cI`。集計は使い捨て（scratchpad）

#### 429 の結果（2026-10-29 05:50〜06:00 UTC ごろ）

- **このセッションからは調べられなかった。** Cloudflare のトークンがセッションに無い（Actions の Secret `CF_ANALYTICS_TOKEN` だけ）。ワークフローを足す・変えると「ドキュメントのみ」の範囲を超えるので、この指示ではしていない
- 調べる道筋は見つかった: 同じトークンで `fetch-redirect-counts.yml`（10-31 18:01 UTC の実行）が GraphQL Analytics の `httpRequestsAdaptiveGroups` を状態コード別に引けている（ログに「/houou_leagues.html 403: 102」などの行）。集計の範囲は「保持期間のため」2026-09-30T18:11Z から始まっていて、**保持期間はおよそ31日**。10-29 の分は **11-28 ごろまで**この API で状態コード 429 の件数・時刻・パスを引ける見込み（Security Events の保持期間は確かめていない）
- 食い違いの見立て（未検証）: 記録では #124 のルールは Managed Challenge（チャレンジの画面、429 ではない）で、429 を返すのは2本目の `/api/ask` のルール（Block、POST の `/api/ask` だけ）。HEAD の取得はどちらの条件とも合わない。SEO-01 の取得はクラウドセッションから送られており、**セッションの外向きの経路（プロキシ）が返した 429 の可能性**もある。Cloudflare の集計に 10-29 05:50〜06:00 UTC の 429 が無ければ、Cloudflare 以外が返したと分かる

#### ほかに見つかったこと

- **月次の運行（`run-monthly.yml`）が 10-31 18:00 UTC（11-01 03:00 JST）に失敗した。** `gsc` は成功（`docs/gsc/2026-11-01/`、35901cd54）。`redirects` は集計まで済んだが「コミットして push」で `rejected (fetch first)` になり、`docs/redirect-counts/2026-10.csv` と #568 へのコメントが無い（直前に `gsc` が cloudflare へ push していたため、と見られる）。保険の予約実行（`fetch-redirect-counts.yml`、10-31 21:30 UTC の枠）は 23:17 UTC の時点でまだ走っていない（GitHub の予約実行はこれまでも3時間ほど遅れて走っている: 10-29〜31 の 00:56〜01:14 UTC）
- 毎朝の運行（`run-daily.yml`）も 10-31 19:00 UTC に失敗した。`sns / update` が「「プロ」「連盟プロ以外」で名前が重複しています」で止まった（#304 の項目ではないので記録だけ）

### 3. 平野さん（またはチャット側が平野さんの Chrome で）見る手順

URL は画面の場所の目安（Cloudflare は `?to=/:account/...` の形、Search Console はプロパティ `sc-domain:ryoei.pro`）。開けなければ画面の名前でたどる。記録は全項目がそろってから「2026-11 実施」のコメントに書く（別の指示）。

1. **(13) Billing**: https://dash.cloudflare.com/?to=/:account/billing → 使用量（Workers Paid、期間 10-09〜11-09）。見る値: Workers Builds のビルド分 / 6,000 分・Workers Logs の件数 / 2,000 万件・Workers のリクエスト・D1 の読み書き。枠の 8 割を超えた項目があれば #298 にコメントする
2. **(13) ビルド1回の所要時間**: https://dash.cloudflare.com/?to=/:account/workers/services/view/mj/production/deployments （または Workers & Pages → mj → Deployments／Builds）。直近の本番のビルド3回ほどの所要時間を控える（Code の上限の目安は中央値 59秒。10-21 は 3〜4 分）
3. **10-29 の 429**: Security → Events（または Analytics → Security）で期間を 2026-10-29 05:45〜06:05 UTC（14:45〜15:05 JST）にし、Service ごとの件数（特に Rate limiting rules）とアクションを見る。あわせて Analytics → Traffic で同じ期間の状態コード 429 の有無。どちらにも無ければ Cloudflare 以外が返した見込み。**保持期間が短い場合に備えて早めに見る**
4. **追加: AI Crawl Control の 403**: AI Crawl Control → Overview（直近7日の全・許可・失敗、ステータス別）と Security（クローラー別の許可/失敗）。10月の値（失敗 2k・403 1.42k・クローラー別の表）と見比べる
5. **(9) 設定の照合**: AI bot policies・Speed（Speed Brain・Polish・HTTP/3・0-RTT・Always use HTTPS・Early Hints）・Security rules（Rate limiting 2本〈#124 と `/api/ask`〉・カスタムルール・Managed Ruleset）・Workers mj の Builds（本番の除外 `.git/`・`docs/**`・`node_modules/**`・`workers/**`）・Domain Registration・Client side abuse を `docs/notes/cloudflare.md` と照らす
6. **(3) Cloudflare のデバイス**: Analytics → Traffic（HTTP Traffic）の Top device types（直近30日）。10月は desktop 69.4% / mobile 30.1% / tablet 0.5%
7. **(2) Core Web Vitals**: Web Analytics → ryoei.pro → Core Web Vitals（直近30日、ボット除く）。LCP・INP・CLS の良好の割合と LCP の p50・p75・p90
8. **(1) Web Analytics のリファラ**: 同じ画面の Referers（直近30日）で chatgpt.com・perplexity.ai・gemini.google.com・claude.ai・copilot.microsoft.com。AI Crawl Control の AI からの紹介も
9. **(11) Synthetic monitoring**: Speed → Synthetic monitoring → Browser tests の5本の最新のスコア（10月: 98・95・62・99・99）
10. **(1) 生成 AI 機能**: https://search.google.com/search-console/performance/search-analytics?resource_id=sc-domain%3Aryoei.pro → 検索の外観などの「生成 AI」（3か月）。合計の表示回数とページ数（10月: 64回・15ページ）
11. **(7) ページ**: https://search.google.com/search-console/index?resource_id=sc-domain%3Aryoei.pro 。登録済み・未登録の理由別の件数
12. **(8) クロールの統計情報**: https://search.google.com/search-console/settings/crawl-stats?resource_id=sc-domain%3Aryoei.pro 。レスポンス別に 403 の行が無いか。あわせて (10) の「ホストの状態」
13. **(10) 手動による対策・セキュリティの問題**: https://search.google.com/search-console/manual-actions?resource_id=sc-domain%3Aryoei.pro と https://search.google.com/search-console/security-issues?resource_id=sc-domain%3Aryoei.pro
14. **(12) 分析情報**: Search Console → 分析情報（既定の比較）。伸びたページ・クエリを1〜3件（Code の参考値は上の表）
15. **追加: Bing**: Bing Webmaster Tools に AI Performance／Copilot に関する項目があるか（有無だけ）
16. **追加: OGP**: title/ の大会・期のページ（見本の2枚以外）を X・LINE でシェアの下書きに貼り、カードが出るか
17. **(5) の判断**: 上の「(5) の結果」を見て、棚卸しの判断を #304 にコメントで残す（候補は0件。中身の判断が要る2つの観点を今月やるか）
18. **(6)**: #357 の 10-18・10-25 の通知3件の片付けをどうするか

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
