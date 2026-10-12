# AI 検索・AIO（#230 系）

AI 検索での言及の計測（#230）など。書き方は [README.md](README.md)。

## 2026-10-12（CHAT-1012-ACF-02）

指示文の決定:

- 「言及」の計測（月40回）は自動化する。10月分も自動化ができてから取る（#230）
- 記録先は 2026-10-12 に平野さんが作ったブック（タブ「言及」「流入」、見出しは #230 の 2026-10-10 のコメントの列定義どおり）。サービスアカウント `live-channel-writer` に編集権限を付与済み
- issue の更新は平野さんが直接行わず、Claude Code が書く

grill の決定（未実装。実装は次の指示）:

- 仕組み: Actions のワークフロー（Python）を Worker mj-scheduler（#504）が `workflow_dispatch` で起動する。ブックへの書き込みは `live-channel-writer` と `scripts/lib/sheets_write.py`（grill Q1）
- 実行日: 毎月1日 05:30 JST に固定。手動実行も残す（06:00 より前なので朝の確かめの対象に入る）（grill Q2）
- 知らせ先: 起動・ジョブの失敗は #506。結果のまとめ（ツール別の言及の回数・引用 URL）は #230 に毎月コメントする。一部のツールだけ失敗したら、残りを書いて #230 にその旨を書く（grill Q3）
- ブック: 「言及」「流入」とも毎月追記し、その月の行があれば書かずに止まる。`WRITABLE` に（このブック・「言及」「流入」）を足す。10月のひな形の40行は、手動実行の前に平野さんが消す（grill Q4・Q11・Q12）
- 列: 「言及」の I 列に「経路」を足す（API の答えはアプリと同じではないため。見出しは平野さんが入れる）。回答の全文と出典の一覧はジョブの成果物（artifact、90日）に残す（grill Q5）
- 判定: 出典の URL か本文に ryoei.pro（www. を含む）が出たら言及 `Y`。順位は出典の一覧（重複 URL を除いた API の順）で最初に出た位置、本文だけなら `本文`。URL もドメインも無いサイト名だけの言及は数えない（grill Q6）
- 10月分: 実装のマージ後すぐに手動実行する（日付は実行日。流入の期間は直近30日）（grill Q7・Q12）
- ツール: ChatGPT は OpenAI の Responses API の `web_search`（gpt-5.4-mini）、Perplexity は Agent API（sonar・fast）、Claude は Claude API の `web_search`（Haiku 5.5、Web 検索に非対応なら Haiku 4.5。`max_uses` 3）。Gemini は平野さんの手作業に残す（規約〈preview 版〉が検索結果の保存・分析を原則禁じるため。現行の規約を確かめてから API に移すかを決める）（grill Q8）
- キーと上限: Secret は `OPENAI_API_KEY`（Project の月の上限 $2）・`PERPLEXITY_API_KEY`（前払い $5、自動チャージはオフ）・`AIO_ANTHROPIC_API_KEY`（Console の専用 Workspace、上限 $2。既存の `ANTHROPIC_API_KEY` と分ける）。アカウント・課金・Secret の登録は平野さんの手作業（grill Q9）
- 流入: 自動にする。Cloudflare の GraphQL（`rumPageloadEventsAdaptiveGroups`）を権限「Account Analytics: Read」だけのトークン（Secret `CF_ANALYTICS_TOKEN`）で読む。`CLOUDFLARE_API_TOKEN` の禁止（二重デプロイの防止）には当たらないことを docs/notes/cloudflare.md に書く。期間は前月の暦月、5つのリファラを 0 件でも毎月書く（grill Q10・Q12）
- #230 の本文の「分担」「手順」を上に合わせて直す（grill Q13）
- 作業中の回答: 貼る時機の「ACF-01 とは別のセッション」と食い違い、同じセッションに貼られたことについて「このセッションで進める」
