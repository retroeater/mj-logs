# CHAT-1011-AIC-03

- 着手日時: 2026-10-11（JST）
- 対象issue: #542
- ブランチ: work/1011-aic
- 着手時HEAD: 7665e09e

## 指示

【Claude作成】Claude Code 向け指示：データ・コンシェルジュ（#542）の実装2。Worker `mj-ask`（workers/ask/）と houou/ トップの質問欄を作り、平野さんの手作業（B1〜B4）の手順を文書にして、cloudflare へマージする Chat-Ref: CHAT-1011-AIC-03 マージ: 承認済み（チャットで） 貼る時機: CHAT-1011-AIC-02 の完了の後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-AIC-02 のログの `## 報告` を読み、状態が「完了」で、origin/cloudflare に `workers/ask/src/query.mjs` と `workers/ask/data.json` があることを確かめる（無ければ何もせず止まる）。

目的
#542 の実装を3つに分けたうちの2つ目。Worker `mj-ask` と、未公開の houou/ のトップに置く質問欄を作る。Worker のデプロイは、平野さんの手作業 B1（Workers Builds のプロジェクトの作成）の後に始まる。この指示ではデプロイの確かめと本番での試しは行わない（次の指示で行う）。
決定（2026-10-11、平野さん）

* 実装2はマージ承認済みで出す（テストが通り、houou/ のトップ以外に差分が無いことを確かめたうえで cloudflare へ入れる）。本番での確かめと実装3（週次の cron）は、平野さんの B1〜B4 の後に次の指示で行う
* 手作業 A1 は済み: Console の組織「Ryoei's Individual Org」に、ワークスペース `ryoei-pro-ask`（ID `wrkspc_01U4E97MKXP9KPWnEK2FGqs8`）を作り、月の上限を $30 にした（2026-10-11、平野さんのスクリーンショットで確認）。ワークスペースの地域は「米国」のまま
* 手作業 A2 は済み: Cloudflare の D1 にデータベース `mj-ask` を作った。Database ID は `4a99bb4a-257c-4fc8-ade3-737c99424e68`（2026-10-11、平野さんのスクリーンショットで確認。秘密の値ではないので `wrangler.jsonc` に書いてよい）。表はまだ無い
* 仕様は docs/decisions/houou.md「2026-10-11（CHAT-1011-AIC-01）」のとおり（grill Q4・Q6〜Q8・Q10〜Q12 が実装2に関わる。文言4つと、試行中は全体で1日50問・同じ IP は1日10問）

前提（チャット側。平野さんの決定ではない）

* 作るもの・変えるものは、AIC-01 のログ「5. 実装の案」の「実装2」を出発点にする。実物に合わせて変えてよく、変えたら報告に書く
* 手作業 B1〜B4（チャット側が平野さんに渡した案。画面の名前は `docs/notes/scheduler-worker.md` の `mj-scheduler` の作成手順と公式の文書から取った）:
   * B1: Workers & Pages の「Create application」でリポジトリ `retroeater/mj` を選び、次のとおりに作る。作った後、Settings > Build の Build watch paths の Include を `workers/ask/**` だけにする
      * Project name: `mj-ask`
      * Build command: 空
      * Deploy command: `npx wrangler deploy`
      * Enable Preview builds: OFF
      * Advanced settings の Path: `/workers/ask`
      * API token: `mj build token`
   * B2: D1 の `mj-ask` の「Console」に `workers/ask/schema.sql` を貼って「Execute」を押す
   * B3: Console の API keys でキーを作る。名前 `mj-ask`、ワークスペース `ryoei-pro-ask`、本人に紐づけ、期限1年
   * B4: `mj-ask` の Settings > Variables and Secrets に、Secret `ANTHROPIC_API_KEY` と `IP_HASH_SALT` を足す
* AIC-01 の調べ: Workers Builds の自動のトークンには D1 の権限が無い（要確認）。D1 のバインディングを持つ Worker を `mj build token` でデプロイできるか、権限が足りないときに平野さんが何をすればよいか（トークンの権限の追加など）を、公式の文書で確かめて B1 の手順に入れる
* `mj-ask` がデプロイされるまでは、質問欄から `/api/ask` へ送っても Worker が無く、サイトの Worker `mj` の応答（404 など）が返る見込み。その間は「いまは答えられません」と表示する
* houou/ のトップは `scripts/generate_houou_pages.py` の `render_top()` が作っている（チャット側が 2026-10-11 に確認。要確認）。マージの push で `regenerate-page.yml` が houou_pages を作り直す。前回の生成の後にシート側で変わった分も、その時に反映されうる
* 2026-10-11 の時点で、未マージの work/ ブランチは無かった（チャット側の確認。着手時に改めて確かめる）

手順

1. Worker: AIC-01 の「実装2」の案に沿って、`workers/ask/` に Worker `mj-ask` を作る。中身は次のとおり
   * 設定: `wrangler.jsonc`。ルート `ryoei.pro/api/*`、上の D1、vars（試行中の上限 50・IP 10）
   * 入口: POST `/api/ask`・GET `/api/ask/status`
   * 入力の検査: `Origin` の検査と 200字の上限
   * 回数: D1 で数える
   * Claude の呼び出し: 2回。モデル `claude-haiku-5-5`、1回目は structured outputs で型と引数を返させる。thinking・effort の設定は公式の文書で確かめて決める
   * 答えの組み立て: 答えの下の時点と出典は `query.mjs` が付ける
   * 記録: 日時（JST）・質問文・結果・型
   * 失敗のとき: 「いまは答えられません」を返す
   * 表の定義: `schema.sql`
   * テスト: Anthropic と D1 を差し替えた `node --test`。次を確かめる
      * 上限に達したときの応答
      * 型に当たらない質問への「答えられません」
      * 記録に IP・答えの本文が残らないこと
      * 例の質問で2名が返ること
`ryoei.pro/api/*` に今あるパスが無いことを確かめる
2. 質問欄: houou/ トップのカード4枚の上に置く。文言4つは決定どおりにする。答えの表示と、状態ごとの表示（上限に達したときの「翌0時まで」、使えないときの「いまは答えられません」）を作る。送信と表示の JS は、既存の `assets/houou.js` に足すか新しいファイルにする（どちらにしたか報告に書く）。外部ドメインを増やさない。`python3 scripts/regenerate.py houou_pages` の差分が、houou/ のトップと足した JS だけであることを確かめる
3. 文書・確かめ・マージ:
   * `docs/notes/ask-worker.md` に Worker の仕組み、B1〜B4 の手順（公式の文書で確かめた画面の名前で）、キーの差し替えの手順を書く
   * `python3 -m unittest discover -s scripts/tests` と `node --test` が通ることを確かめる
   * 通ったら cloudflare へマージし、マージ後の check-run と `regenerate-page.yml` の結果をログに書く
   * ログの `## 報告` の「判断が必要なこと」に、平野さんの次の手作業として B1〜B4 を順番どおりに書く（ドキュメントの節へのリンク付き）
   * #542 に進みをコメントする

止まる条件

* #542 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* `ryoei.pro/api/*` に既存のパスや設定があり、ルートを置くと既存の動きが変わる
* 決定（docs/decisions/houou.md の AIC-01 の節）どおりに作れない（例: structured outputs が Haiku 5.5 で使えない）。食い違いを書いて止まる
* テストが通らない、または `regenerate.py houou_pages` の差分に意図しない変更がある（マージせずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-AIC-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-AIC-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1011-AIC-03` のコミットなし
- 作業ブランチ: ローカルの `work/1011-aic` は `origin/cloudflare` の祖先（リモートもマージ済み）のため、`git merge --ff-only origin/cloudflare` で進めた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は指示文の最後の行と一致。AIC-02 の `## 報告` の状態は「完了」、origin/cloudflare に `workers/ask/src/query.mjs` と `workers/ask/data.json` がある

## 報告

- 状態: 作業中
- ブランチ: work/1011-aic
- ログ: https://github.com/retroeater/mj/blob/work/1011-aic/docs/logs/CHAT-1011-AIC-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-aic
- 確認用URL: なし
- マージ: 未
- issue: #542
- 判断が必要なこと: 未定
- 未確認の項目: 未定
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4790f6d2）: https://github.com/retroeater/mj-logs/tree/main/guide/4790f6d2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
