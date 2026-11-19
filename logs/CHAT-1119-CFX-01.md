# CHAT-1119-CFX-01

- 着手日時: 2026-11-19
- 対象issue: なし
- ブランチ: work/1119-cfx
- 着手時HEAD: c2fd0dd91

## 指示

【Claude作成】Claude Code 向け指示：Cloudflare の API トークン・Available の製品・Spend limits の決定を docs に記録する
Chat-Ref: CHAT-1119-CFX-01
マージ: ドキュメントのみの変更（docs/decisions/・docs/notes/・docs/logs/）なので、完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1119-cfx を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-cfx origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-11-19 に平野さんがチャットで Cloudflare の設定を見直した結果（API トークン1本の扱い、Billing の Available の製品、Spend limits）を、docs/decisions/cloudflare.md と docs/notes/cloudflare.md に記録する。コードは変えない。

### 決定（2026-11-19、平野さん）
- User API Token `mj-builds-read`（権限 Account.Workers Builds Configuration、作成は約22日前）は削除しない。`check-build-failure.yml` が使う Secret `CF_BUILDS_TOKEN` の元のトークンとみられるため（下の前提を確かめる）
- Billing → Subscriptions → Available の7製品（Workers for Platforms・Calls・Images & Stream・Zero Trust Free・Tunnels for Platforms・Zaraz・Log Explorer）は、今はどれも有効にしない
- Workers の Spend limits（Billing → Spend limits、Beta）で Block usage を有効にし、上限（Spending limit）は $20 にする。Alert thresholds は足さない（通知は既存の Budget alert〈$5〉に任せる）。Durable Objects の行は触らない（使っていないため）。設定は平野さんが 2026-11-19 に保存済み（申告値。画面では Workers の行が「$0.00 / $20.00」、History に「Spending limit set to $20.00.」。上限到達のメールの宛先は平野さんの別のアドレス〈docs には書かない〉）

### 前提（チャット側。平野さんの決定ではない）
- `mj-builds-read` を `CF_BUILDS_TOKEN` とみた理由（チャット側の推定）: 権限が docs の「Workers CI Read」と同じ系統、作成時期が #634（Build ID で本番の失敗かを確かめる変更）の頃、最終使用が約15日前（Workers Builds の check-run が failure のときだけ API を呼ぶため）。トークン名は docs に無い（要確認）。`check-build-failure.yml` の実行の履歴で、11月の初め（約15日前）に Cloudflare のビルドの API を呼んだ実行があるかを確かめ、無ければ「判断が必要なこと」に書く（止まらない）
- このトークンを消すと、`check-build-failure.yml` が本番かプレビューかを確かめられず「確かめられないときは知らせる」側になり、プレビューの失敗でも通知が来る（#634 の前の誤報に戻る）
- Available の製品の判断の理由（チャット側の案。表の文言は合わせて直してよい）:
  - Workers for Platforms: 他人がデプロイするコードを動かす基盤で対象外
  - Calls: リアルタイムの音声・映像を使わず対象外
  - Images & Stream: 既存の「検討して見送った技術」の Image Transformations / Cloudflare Images と同じ理由
  - Zero Trust Free: Access でプレビューの `*.workers.dev` をログイン必須にできるが、クラウドセッションの curl とチャット側の WebFetch がプレビューを開けなくなり、今の確認の手順（chat-side-operations.md「確認用 URL」、cloud-sessions.md）が止まる。入れるきっかけは SDP の管理画面や非公開のページを作るとき（既存の表の「JWTのHttpOnly Cookie」の行と同じ考え）
  - Tunnels for Platforms: オリジンが無く対象外
  - Zaraz: 既存の表で却下済み（第三者のスクリプトが無い）
  - Log Explorer: HTTP のリクエストのログを SQL で調べられるが、有料の追加機能（無料版・試用なし。公式 https://developers.cloudflare.com/log-explorer/pricing/ ）で、攻撃のトラフィックも課金に数える。2026-11-02 に「上限なく課金されるより止まる方がよい」として Observability を Free にした決定と合わず、今の Analytics の GraphQL で足りている
- Spend limits の仕組み（公式 https://developers.cloudflare.com/workers/platform/spend-management/ 。2026-11-19 にチャット側が読んだ）: 上限に達すると Workers の新しい呼び出し（HTTP リクエスト・Cron Triggers・Worker 同士の呼び出しなど）が拒否され、訪問者には error 1103。Worker を動かさずに配信される静的アセットは見られるまま。数えるのは全 Worker のリクエストと CPU 時間の、含まれる分を超えた料金だけ（月額は入らない）。止まるまで時間差があり請求の上限は保証されない。ブロックは次の請求期間の始めに自動で解ける（今の期間は Nov 9 – Dec 8）。上限を上げる・切ると数分で再開する
- ryoei.pro で上限に達したときに止まるもの（チャット側の推定。要確認）: `mj` の静的なページは見られるまま、`/api/ask` 系と `mj-scheduler` の cron（毎分）が止まる。`wrangler.jsonc`・`workers/*/wrangler.jsonc` で `run_worker_first`（Worker を先に通す設定）や、Worker のコードを通るパスがほかにあるかを確かめて書く

## 手順
1. 追記先の今の内容を読む（docs/decisions/cloudflare.md の末尾、docs/notes/cloudflare.md の「APIトークンの棚卸し」「DNS・メール・通知の設定」の通知の行、「検討して見送った技術(2026-09-11)」の表、Observability・Budget Alert の節）。上の決定と矛盾し、どちらが正か判断が要るときは止まる。同じ趣旨の記述があるだけなら、置き換え・拡張してよい（処理を報告に書く）
2. 前提の2点（`mj-builds-read` と `CF_BUILDS_TOKEN` の対応の痕跡、上限に達したときに止まるもの）を、ワークフローの実行の履歴と wrangler の設定で確かめる
3. 記録する:
   - docs/decisions/cloudflare.md に「## 2026-11-19（CHAT-1119-CFX-01）」の節を足し、上の決定3点と「作業中の回答: なし」を書く
   - docs/notes/cloudflare.md: 「APIトークンの棚卸し」に、今あるトークン（`mj build token`・`mj-builds-read` と、それぞれの用途・Secret 名。`CF_ANALYTICS_TOKEN` の元のトークンは User API Tokens の一覧に無く Account API Tokens 側にあるとみられる〈要確認。確かめられなければそう書く〉）と「`mj-builds-read` は消さない」を書く。「検討して見送った技術」の表（または新しい小節）に Available の7製品の判断を足す（既存の行と重なるものは既存の行を指す）。通知・Budget Alert の近くに Spend limits の設定（Workers、Block usage、$20、Alert thresholds なし、申告値）と、上限に達したときに止まるもの・止まらないもの、再開の手順（上限を上げる・切る、または次の請求期間）を書く

## 止まる条件
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
- 同じ論点（Spend limits・API トークンの削除）の open issue がある、または他セッションの着手中コメントがある
- 未マージの work/ ブランチが docs/decisions/cloudflare.md・docs/notes/cloudflare.md の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-CFX-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-CFX-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-11-19）

- 0.: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref・識別子 `CFX` の重複: `git fetch --unshallow` の後、`git log --all -E --grep 'CHAT-[0-9]{4}-CFX-'` と `docs/logs/CHAT-*-CFX-*.md` の追加の履歴ともに0件
- 作業ブランチ: `work/1119-cfx` はローカル・リモートとも無く、`git checkout -b work/1119-cfx origin/cloudflare` で作った
- 指示文が対象にする issue: なし（#634 は経緯の参照）
- 同じ論点の open issue: 検索（Spend limit・spend・mj-builds-read・APIトークン・CF_BUILDS_TOKEN・トークン 削除）で、Spend limits の issue は無し（#298 は GitHub Actions の使用量で別の論点）。
  API トークンでは #601（認証情報の一覧と棚卸し、open）があるが、論点は台帳の置き場所・項目・間隔で、`mj-builds-read` の削除ではない。着手中のコメントも無い。止まらない（下の「#601 との関係」）
- 未マージの work/ ブランチ: `docs/decisions/cloudflare.md`・`docs/notes/cloudflare.md` を変えているのは `origin/work/1101-prp-pub`（最終 2026-11-04）だけで、変えている箇所は「配信設定」の canonical の行と「本番反映（デプロイ）の仕組み」の Worker `mj-pros-redirect` の行。今回の追記先の節（APIトークンの棚卸し・見送った技術・ビルド時間の見積もり・通知の行）とは別の箇所なので止まらない

### 前提の確かめ（2026-11-19）

**`mj-builds-read` と `CF_BUILDS_TOKEN`:** `check-build-failure.yml` の実行（2026-10-13〜11-19）のうち skipped でないものは 9件。うち `check_run` の 8件が判定のステップで `CF_BUILDS_TOKEN` を渡し、Cloudflare のビルドの API を呼んで答えを得ていた（ログの行「Cloudflare のビルド: トリガー: …、ブランチ: …」・notice「本番のトリガーのビルドではない(トリガー: Deploy non-production branches、…)」）。

| 実行 ID | 作成（UTC） | 結果 | API の答え |
|---|---|---|---|
| 41657643690 | 2026-11-03 14:50 | success | プレビュー（work/1103-aic）と確かめて知らせず |
| 41663755493・41669679662 | 11-03 15:25・16:00 | success | （同じ型の success。ログは1件だけ読んだ） |
| 41671687021 | 11-03 16:11 | failure | `mj-ask`、ブランチ cloudflare（本番の失敗として知らせた） |
| 41671694707 | 11-03 16:11 | success | |
| 41676151633 | 11-03 16:37 | failure | `mj-ask`、ブランチ cloudflare |
| 41676147031 | 11-03 16:37 | success | |
| 42553376117 | 11-09 02:53 | success（workflow_dispatch） | 「Workers Builds の失敗ではない」で、API は呼ばない（`judge()` が失敗でない check-run では問い合わせない） |

- API を呼んだ最後の実行は 2026-11-03 16:37 UTC（11-04 01:37 JST）。11-19 から約15日前で、平野さんの画面の「最終使用 約15日前」（申告値）と合う。#634 のクローズは 2026-10-27 で、作成「約22日前」（10/28 ごろ）とも合う
- 2026-10-13〜11-02 に API を呼んだ実行は無い（skipped でない実行が無い）。トークン名とSecretの対応そのものは GitHub からは見えない（Secret の値・元のトークン名は読めない）ので、「痕跡が合う」までで、対応は申告値の画面と時期の一致による推定
- 実行ログに Cloudflare のアカウント ID が出る。docs・ログには書かない

**`CF_ANALYTICS_TOKEN` の元のトークン:** #601 の 2026-10-18 のコメント（平野さんの申告値の台帳）に「Cloudflare Account API token: Analytics Read だけのもの1本（GitHub の Secret `CF_ANALYTICS_TOKEN`）」とある。User API Tokens の一覧に無いことと合う。セッションからは Cloudflare の画面を見られないので、申告値として書く

**上限に達したときに止まるもの（wrangler の設定）:**

- `wrangler.jsonc`（`mj`）: `main` が無く `assets` だけ（`run_worker_first` も無い）。Worker のコードは動かない静的アセットだけの Worker（Cron Triggers を持てないとの既存の記録とも合う）→ ページは見られるまま
- `workers/ask/wrangler.jsonc`（`mj-ask`）: ルート `ryoei.pro/api/*`（`/api/ask`・`/api/ask/status`・`/api/ask/weekly`）と cron `0 18 * * 1`（月曜 03:00 JST の週次）→ 止まる
- `workers/scheduler/wrangler.jsonc`（`mj-scheduler`）: cron `* * * * *`、ルート無し → 止まる。予約実行の起動と、Worker の中の朝の確かめ（#506 への通知）も止まるので、#506 の通知では気づけない（docs/notes/scheduler-worker.md。保険の `schedule` は月次の2本だけ残っている）
- ほかに Worker を通るパスは無い（`workers/` は ask・scheduler の2つ）。未マージの `work/1101-prp-pub` が足す `mj-pros-redirect`（ルート `ryoei.pro/jpml_pros.html*`）が入れば、それも止まる側になる

### #601 との関係

#601 は台帳の置き場所（docs/notes が mj-logs の `guide/` に写り公開されること）を未決の論点にしている。今回 docs/notes/cloudflare.md に足すのはトークン名・権限・Secret 名で、同じ節に既に `mj build token`・`CF_ANALYTICS_TOKEN`・`CF_BUILDS_TOKEN` の名前と権限が書かれており、指示文もそこへ書くよう指定しているので、そのまま書く。#601 の台帳（コメント）への `mj-builds-read` の追加はこの指示の範囲外（コメントは書いていない）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c2fd0dd9）: https://github.com/retroeater/mj-logs/tree/main/guide/c2fd0dd9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c2fd0dd9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c2fd0dd9.md
