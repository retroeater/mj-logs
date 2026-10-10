# CHAT-1011-AIC-01

- 着手日時: 2026-10-11（JST）
- 対象issue: 未定（起票予定。親 #518）
- ブランチ: work/1011-aic
- 着手時HEAD: a72aaab4

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の「データ・コンシェルジュ」（AI に言葉で質問できる欄）の実装の前に、起票と技術面の grill（/grill-me）を平野さんと行い、決定を docs/decisions に記録する（コードは変えない） Chat-Ref: CHAT-1011-AIC-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Claude API の月次クレジット（Max 5x、月 $100。Console の組織「Ryoei's Individual Org」に連携済み）を使い、houou/ のトップに、鳳凰戦のデータへ言葉で質問できる欄を置く。この指示では、起票と技術面の grill までを行う。実装は次の指示で行う。
決定（2026-10-10〜11、平野さん。チャットでの grill）

1. 置き場所: 未公開で開発中の houou/ の中で試作する（新サイト #296 送りにしない）。質問欄は houou/ トップのカード4枚の上に常に出す
2. 引いてよいデータ: houou/ の鳳凰戦のデータに加え、title/ と live/ の「鳳凰戦」のデータ。答えはデータにあることだけにし、出典のページへのリンクを付ける。データに無い評価や私生活には答えない
3. 作り方: Claude には「質問を検索条件に直す」ことと「プログラムが返した結果を文章にする」ことだけをさせる。数える・並べるのはプログラムが行う。検索条件に直せない質問には「答えられません」と返す。モデルは Claude Haiku 5.5
4. 上限: 全体で1日200問、同じ IP からは1日10問まで。上限に達したら翌0時（JST）まで受付を止め、その旨を表示する
5. 質問の記録: 質問文と結果（答えた／答えられなかった）だけを残す。IP は回数制限にだけ使い、残さない。記録は90日で消す。週に1回まとめて見られるようにする
6. 費用の囲い: Console の組織の月間支出上限は $100（2026-10-10 に平野さんが設定済み。メール通知は $50・$90）。この機能専用のワークスペースを作って上限 $30 を付け、そのワークスペースのキーを使う
7. 文言（平野さんが指定）:
   * 入力欄の中: 「鳳凰戦について質問してみてください（例：23期生で鳳凰位になった人は？）」
   * 正確さ: 「AI の回答には間違いが含まれている場合があります」
   * 個人情報: 「個人情報等は入力しないでください」
   * 保存: 「ご質問の内容はサービス改善のため利用させていただく場合があります」
8. 進め方: 技術面は Claude Code の /grill-me で平野さんと詰める。実装とマージは次の指示で行う

前提（チャット側。平野さんの決定ではない）

* 費用の見込み（チャット側の概算）: Haiku 5.5 の料金は、100万トークンあたり入力 $0.10・出力 $0.50（2026-10-10 の料金表、platform.claude.com/docs/en/about-claude/pricing）。1問で2回呼ぶと約 $0.002〜0.003 で、1日200問が毎日満杯でも月 $15〜20
* 月次クレジットの対象: API キーでの Claude API（Messages）の呼び出しは対象。ただし、連携した組織のキーに限る（support.claude.com の記事 17154008）。購入済みの残高は $4.81、自動チャージはオフ（2026-10-10 にチャット側が Console で確認）
* 既存のキー: Default ワークスペースの `mj-dojo-guest`（GitHub の Secret `ANTHROPIC_API_KEY`、道場部ゲストの取り込みで使う）。この機能には使い回さない（ワークスペースの上限で費用を囲うため）
* 学習: Anthropic は API の入出力を既定で学習に使わない（privacy.claude.com の記事 7996868）。文言には書かない
* データの範囲（チャット側が 2026-10-11 に origin/cloudflare で数えた。要確認）: `houou/results/*.json` の成績は第17期〜第43期前期で、そこで見える鳳凰位は第24期以降の11名。`houou/search.json` は692名で、入会期（例「40期」）を持つ。例の質問の答えは、吉田直（第36期）と白鳥翔（第42期）の2名。第23期以前の鳳凰位は title/ にある
* 「期」の意味が2つある: 鳳凰戦の「第N期」と、入会期の「N期生」。検索条件の形で区別する必要がある
* houou/ は HOU 系の指示（CHAT-1011-HOU-13 など）が今も直している。この指示はコードを変えないので重ならない。ただし次の実装の指示は、houou/ のトップで重なりうる
* 配信は Cloudflare Workers（Free）の静的アセット（`wrangler.jsonc`）。Worker のスクリプト（`workers/`）の有無と使い方は要確認。チャット側の考え: 外部ドメインを増やさない方針（CSP）があるので、ブラウザからは同じドメインの Worker だけを呼び、Anthropic への呼び出しは Worker から行う
* 起票: 同じ主題の issue が無ければ、鳳凰戦の新ページの親 #518 の下に起票する（親子の付け方は既存の sub-issue に合わせる）

手順

1. 起票: 同じ主題（AI・質問・コンシェルジュ・Claude API）の issue を、クローズ済みも含めて検索する。あれば止まる。無ければ起票し、本文に上の「目的」と「決定」の要約を書き、着手中コメントを残す
2. grill: /grill-me（grilling skill。Cloudflare まわりは cloudflare skill も使う）で、実装に要る技術面を平野さんと1問1答で詰める。各問に推奨案を付ける。実物（コード・`wrangler.jsonc`・データ）で答えが分かることは、聞かずに調べる。少なくとも次を扱う: a. 検索条件の形と、用意する条件の種類。引くデータ（houou/ の成績と索引、title/ の鳳凰戦、live/ の鳳凰戦）、2つの「期」の区別、改名の名寄せ（HOU-13 の決定に合わせる）、答えに添えるデータの時点を含める b. Worker の形。既存の Worker に足すか新しく作るか、Workers Free の範囲で回数制限・1日の上限・質問の記録（D1・KV など）を置けるか、0時（JST）のリセットをどうするか c. キーの置き場所（Worker の Secret）。専用ワークスペースとキーの作成・登録を平野さんが行う手順（Console と Cloudflare ダッシュボードの画面の名前は、公式の文書で確かめて書く） d. 週に1回の質問のまとめをどこに出すか（常設 issue など） e. 試し方（未公開の houou/ での確かめ、費用の実測）と、実装の指示をいくつに分けるか
3. 記録: 上の「決定」と grill の答えを docs/decisions/houou.md に足す（docs/decisions/README.md のとおり）。実装の案（作るファイル、変えるファイル、平野さんの手作業の一覧）をログに書く。起票した issue に、決定の要点と、このログの SHA を固定した permalink をコメントする

止まる条件

* 同じ主題の issue がある。または、その issue に他セッションの着手中コメントがある
* 「前提」の事実と実物が大きく食い違い、決定どおりに作れない（例: Workers Free では回数制限や記録を置けない）。食い違いを書いて止まる（調べ終えた所までログに書く）
* ログと docs/decisions 以外（コード・ワークフロー・設定・シート・生成物）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（実装の判断が要る点は「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-AIC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-AIC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`--unshallow` の後）: `CHAT-1011-AIC-01` のコミットなし、識別子 `AIC` のコミット・ログなし
- 作業ブランチ: ローカル・リモートとも `work/1011-aic` が無いため `git checkout -b work/1011-aic origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致

### 1. 起票

- 同じ主題の issue の検索: セッションの GitHub 検索 API は使えない（「sessions are bound to their configured repositories」）ため、
  `GET /repos/retroeater/mj/issues?state=all` で全 540 件（PR を除く。#1〜#541）を取り、題と本文を `AI|質問|コンシェルジュ|Claude API|Anthropic|Haiku|LLM|生成AI|チャットボット|問い合わせ` で照合。
  題の一致は #130・#447（AI ボット対策）・#161・#227（llms.txt）・#360（トークン削減）だけで、同じ主題（サイトで AI に質問する欄）は無い
- 未マージの `work/` ブランチ（7本）のコミットの件名とログ: 同じ目的のものは無い。`work/1011-hou`（HOU-13）が `docs/decisions/houou.md` に追記しており、この指示の追記とはマージ時に末尾どうしで衝突しうる（両立する追記）
- 起票: #542「鳳凰戦のデータ・コンシェルジュ（houou/ のトップで AI に言葉で質問できる欄）を作る」。#518 の sub-issue に登録（`parent_issue_number`、#519・#520 と同じく本文末尾に「親: #518」）、ラベル「分野: UI/UX」。着手中コメントを残した

### 2. 実物の確認（grill の前に、聞かずに分かること）

- 配信: `wrangler.jsonc` の Worker `mj` は静的アセットだけ（`main` 無し）。`workers/scheduler/`（Worker `mj-scheduler`、#504）は cron だけで fetch の入口を持たない（`workers_dev: false`）。ゾーンは Cloudflare Pro、Workers は Free（docs/notes/scheduler-worker.md・docs/handover.md）
- `houou/search.json` は 692 名（在籍者だけ）。1件は [名前, 読み, ローマ字, 最後の期, 最後のリーグ, 支部, 入会期, 画像]。**入会期は在籍者にしか無い**（退会・物故の鳳凰位は入会期で引けない）
- `houou/results/*.json`（692 ファイル、2.8MB）: 第17期後期〜第43期前期。rows の「鳳凰位」の行（16行・11名）は**その期に鳳凰位として座っている**ことを表し、獲った期はその前の期（例: 吉田直の行は第36期後期、`title/houou/` では第35期の優勝）
- `title/houou/`: 第1期〜第42期の決勝（優勝〜4位）と決勝ライブの日付。JSON は無く HTML に焼き込み（`scripts/generate_title_pages.py`）
- `live/houou/`: 第41期の決定戦の1本だけ（`scripts/generate_live_pages.py`）
- 例の質問「23期生で鳳凰位になった人は？」の答え: 吉田直（第35期）と白鳥翔（第41期・第42期）。**前提の「吉田直（第36期）・白鳥翔（第42期）」は rows の座った期で、獲った期とは1期ずれる**（白鳥翔は第41期も獲っている）。決定どおりに作ることは妨げないため止まらない
- 改名の名寄せ: HOU-13（未マージ、`work/1011-hou`）で「別名」によりコード側で名寄せする決定（grill Q3 を置き換え）

### 3. 公式文書で確かめたこと（2026-10-10、サブエージェントが取得）

Cloudflare（developers.cloudflare.com）:

- Workers Free: 1日 10万リクエスト（00:00 UTC にリセット）、CPU 10ms／リクエスト（fetch の待ちは数えない）、壁時計の上限なし、サブリクエスト 50／回、Cron Triggers はアカウントで5本（`mj-scheduler` が1本使用）。静的アセットへのリクエストは無料・無制限（workers/platform/limits/、workers/static-assets/billing-and-limitations/）
- 既存の `mj` に `main` を足す形: `assets.run_worker_first: ["/api/*"]` で `/api/*` だけスクリプトを先に通す。`_redirects`・`_headers` は Worker が返す応答には効かない（ヘッダはスクリプトで付ける）。他のパスは今のまま（workers/static-assets/binding/、…/redirects/、…/headers/）
- 別の Worker をゾーンのルート `ryoei.pro/api/*` に置く形: ルートは同じホスト名の Custom Domain より優先される（workers/configuration/routing/routes/）
- 保存: D1 Free は1日 500万行読み・10万行書き・5GB、強い整合（リードレプリカを使わない限り）。KV Free は書き込み1日1,000回・結果整合で数え上げに向かない。Durable Objects は Free で SQLite 版のみ使える。Rate Limiting バインディングは期間が 10秒か60秒だけで「正確な集計には使わない」と明記（d1/platform/、kv/platform/、durable-objects/platform/pricing/、workers/runtime-apis/bindings/rate-limit/）
- Cron Triggers は UTC だけ（0時 JST = `0 15 * * *`）
- Secret: Workers & Pages → Worker → Settings → Variables and Secrets → Add（型 Secret）。Workers Builds の「Build variables and secrets」はビルド時だけで実行時には読めない（workers/configuration/secrets/、workers/ci-cd/builds/configuration/）。ダッシュボードで足した Secret が Builds のデプロイで残ることの明文は見つからず（`mj-scheduler` は実際に残っている）
- D1 は先にダッシュボード（D1 SQL database → Create Database）で作り、`database_id` を `wrangler.jsonc` に書く。Builds の自動のトークンには D1 の権限が無い
- 利用者の IP は `CF-Connecting-IP`

Anthropic（platform.claude.com、support.claude.com）:

- ワークスペースの作成は組織の管理者だけ: Settings > Workspaces → Create workspace。上限はワークスペースを選んだ画面の Spend limits（月単位。組織の上限より低くだけ設定できる。Default Workspace には付けられない）。到達すると HTTP 400 `invalid_request_error`（manage-claude/workspaces、api/rate-limits）
- キー: Settings → API keys → Create key。名前・期限（Never 可）・紐づけ先（本人か service account）を選び、ワークスペースに限定できる（manage-claude/authentication）
- Haiku 5.5: `claude-haiku-5-5`。10万トークン以下の入力で 100万トークンあたり入力 $0.10・出力 $0.50（前提の料金は正しい）。structured outputs（`output_config.format`）に対応。adaptive thinking が既定で有効で、`temperature` 等は送らない（models/haiku-5-5/overview、about-claude/pricing、build-with-claude/structured-outputs）
- Max の API クレジットは連携した組織のキー全部が同じ残高から使う（support の 17154008）

### 4. grill（1問1答。回答は平野さん）

- Q1 検索条件の形 → A: 決まった型から選ばせる（structured outputs で型と引数を返させる）。型は6つ: 鳳凰位の一覧・決勝の結果・選手の成績・リーグの顔ぶれ・記録の上位（ranking の9部門）・放送対局。数は結果の件数で答える。型に当たらなければ「答えられません」。型は週1回のまとめを見て足す
- Q2 2つの「期」 → A: 項目を分ける（鳳凰戦の期 `term`、入会期 `entry`）。「N期生」「N期入会」は入会期、「第N期」等は鳳凰戦の期。曖昧なときは鳳凰戦の期として扱い、答えの冒頭に読み方を書く
- Q3 入会期が在籍者だけ → A: 在籍者だけで答え、入会期を使った答えには「入会期が分かるのは在籍中の選手だけです」と添える。鳳凰位の期は title/houou/（決勝）を正にする
- Q4 Worker の形 → B: 新しい Worker `mj-ask`（`workers/ask/`、ゾーンのルート `ryoei.pro/api/*`、Workers Builds の別プロジェクト）。`mj` は変えない。保存は D1 を1つ
- Q5 データの置き場所 → A: 生成スクリプトが `workers/ask/data.json` に集めて書き、Worker が import する（公開対象は増えない）
- Q6 時点と出典 → A: 使ったデータの分だけ、プログラムが答えの下に時点と出典のリンクを付ける（Claude に書かせない）
- Q7 回数の数え方 → A: JST の暦日を鍵に D1 で原子的に数える（cron 不要）。IP は「日付＋Secret の塩」とのハッシュで当日だけ持ち、翌日の最初の問いで前日以前を消す。受け付けた問いは「答えられません」も数え、入力の検査で弾いた問いは数えない
- Q8 記録の項目 → B: 日時（JST）・質問文・結果・Claude が選んだ型。答えの本文と、受け付けなかった問いは残さない

## 報告

- 状態: 作業中
- ブランチ: work/1011-aic
- ログ: https://github.com/retroeater/mj/blob/work/1011-aic/docs/logs/CHAT-1011-AIC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-aic
- 確認用URL: なし
- マージ: 未
- issue: 未定
- 判断が必要なこと: 未定
- 未確認の項目: 未定
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce5677d0）: https://github.com/retroeater/mj-logs/tree/main/guide/ce5677d0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5677d0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce5677d0.md
