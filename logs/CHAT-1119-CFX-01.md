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
