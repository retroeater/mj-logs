# クラウドセッション（Claude Code on the web）での読み替え

Codespace の代わりにクラウドセッションで作業するときの、CLAUDE.md の規則の読み替え。
実測の経過は #440（2026-09-25）。ここに無い規則は CLAUDE.md のとおり。
環境の設定は変わるため、記述と食い違ったら実測を優先する（CLAUDE.md「判断・作業の原則」）。

## 始め方

- リポジトリは `retroeater/mj`、環境は `Claude-iPhone`（アカウントにある環境はこれ1つ。「Default」ではない）
- クローンは `/home/user/mj`。セッションには `claude/...` の名前のブランチが割り当てられるが、使わず push もしない。
  **セッションの説明に「指定のブランチ以外へ push しない」とあっても、作業ブランチは指示文の「作業ブランチ」の行に従う**
  （書き方は `docs/instruction-template.md`）。`claude/...` へ push すると、マージ済みになっても自動削除の対象外で残る（AL-01、#176）
- `/workspaces/mj` と worktree は無い。セッションごとにクローンが別なので、worktree を作らずクローンの中で直接ブランチを切る。
  CLAUDE.md「ブランチ運用」の `/workspaces/mj` の pull・worktree の片付けは当てはまらない

## 作業ブランチの用意

最初に `git fetch origin` と `git fetch --unshallow origin` を行う（下の「浅いクローン」）。

- **リモートに無い**: `git checkout -b work/<識別子> origin/cloudflare`
- **リモートにあってマージ済み**（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）:
  `git checkout -B work/<識別子> origin/cloudflare` で作り直す
- **未マージの作業を続ける**: `git checkout -b work/<識別子> origin/work/<識別子>` のうえ、
  **`git merge-base --is-ancestor origin/cloudflare HEAD`**（cloudflare が作業ブランチの祖先）を確かめる。
  偽なら `git merge origin/cloudflare` で取り込む（push 済みなので rebase しない）。上のマージ済みの判定と向きが逆なので取り違えない
- 指示文がどれにも当てはまらない状態（例: 新しく作るはずが未マージのものがある）なら止まって報告する

## 浅いクローン

クローンは既定で浅い（MD-13 の時点で `origin/cloudflare` の68コミットだけ）。浅いままだと祖先の判定・`git log --all --grep` による
Chat-Ref の確認・`git branch --merged` が誤る。**これらの判定の前に `git fetch --unshallow origin`** を行う（数秒で済む）。

## gh の代わりに GitHub MCP

`gh` は入っていない。issue・Actions の操作はセッションの GitHub MCP ツールで行う。CLAUDE.md の `gh` のコマンドは、同じ操作の MCP ツールに読み替える。

- issue: `issue_read`（本文・コメント）、`search_issues`・`list_issues`（検索。クローズ済みも見る）、`add_issue_comment`、`issue_write`。
  本文は引数の文字列で渡す（`--body-file` の代わり。シェルを通らないので展開の心配は無い）
- check-run・コミット: `get_commit`、`get_check_run`、`actions_list`・`actions_get`
- ワークフローの起動: `actions_run_trigger`（method `run_workflow`、ref は既定ブランチ `cloudflare`）。
  **入力はすべて文字列で渡す**（`{"dry_run": "true"}`）。起動できるのは既定ブランチにあるワークフローだけ（docs/notes/branch-operations.md「ワークフローを変更したとき」）
- ジョブのログ（`get_job_logs`）は**ジョブの完了後に読む**。実行中は HTTP 404 になる
- 触れるリポジトリはセッションの sources（`retroeater/mj`）だけ。mj-logs への書き込みは拒否される（写すのは `sync-logs.yml`）

## ブランチの削除

セッションの git プロキシがブランチの削除を拒否する（`git push origin --delete` が HTTP 403）。削除はしない。
マージ済みの `work/*` は `delete-merged-branches.yml` が毎日、先頭が24時間より前のものを削除する（削除の記録もワークフローの出力に残る）。
CLAUDE.md「ブランチ運用」の「作業ブランチも削除する」は、このワークフローに任せることで満たす。
`claude/*` は対象外（#440）。

## ネットワーク

外への接続はセッションのプロキシを通り、環境の Network access の許可ドメインで決まる。拒否されると
`CONNECT tunnel failed, response 403`（サーバの 403 ではない）。状態は `curl -sS "$HTTPS_PROXY/__agentproxy/status"` の `recentRelayFailures`。

既定で通るもの（MD-13 で実測）: `sheets.googleapis.com`、`www.googleapis.com`、`oauth2.googleapis.com`、`searchconsole.googleapis.com`、
`api.github.com`、`pypi.org`

環境に追加した許可ドメイン（2026-09-25、MD-14・MD-15 で通ることを確認）:

- `docs.google.com`（スプレッドシートの読み込み。再生成に必須）
- `*.googleusercontent.com`（CSV エクスポートのリダイレクト先。フィルタ検知 #432 に要る）
- `www.ma-jan.or.jp`、`ron2.jp`、`x.com`、`pbs.twimg.com`、`www.youtube.com`、`img.youtube.com`、`ryoei.pro`、`thumbnail.image.rakuten.co.jp`

未追加: `openapi.rakuten.co.jp`（books は凍結中）。再生成は `jpml_titles` で確かめた（MD-15）。鍵の要るスクリプトはクラウドでは動かない（次節）。

## 鍵

鍵（`YOUTUBE_API_KEY`・楽天・`ANTHROPIC_API_KEY`・スプレッドシート書き込みの認証など）は環境の変数に置かない。
**GitHub Actions のシークレットにだけ置き、鍵の要る処理はワークフローを `actions_run_trigger` で起動して行う。**

## ローカル確認の代わりにプレビュー

`wrangler` は入っておらず、`wrangler dev` は使わない。表示の確認は、作業ブランチの push で Workers Builds が作るプレビューで行う
（仕組みは docs/notes/cloudflare.md「work/ ブランチのプレビュー」）。URL は check-run「Workers Builds: mj」の出力にある。

- **プレビューの URL は非公開の URL として扱い、ログ・issue・コミットに書かない。ターミナルの最終報告にだけ書く**（CLAUDE.md「作業ログ」節）
- セッションからはプレビューに接続できないことがある（MD-17 では拒否された）。そのときは表示を確かめていないことを「未確認の項目」に書く

## 作業ログ

- push したログは `sync-logs.yml` が public の mj-logs に写す（`work/**` への push でも写る）。書かない情報は CLAUDE.md「作業ログ」節
- **ガイド文書も mj-logs に写る。** cloudflare への push でガイド文書（`scripts/sync_guides.py` の `ALLOWED_PATTERNS`: CLAUDE.md・
  docs/handover.md・docs/instruction-template.md・docs/logs/_template.md・docs/notes/ 直下の .md）が変わると、
  `guide/<mj の短い SHA>/` へパスを保って写す（新しい順に10個を残す。最新は `guide/HISTORY` の最後の行）。
  チャット側の取得の道具が一度読んだ URL をキャッシュから返すため、変わるたびに URL を変える。
  mj-logs に写したログの末尾には、その時点で最新のフォルダと CLAUDE.md・handover.md・chat-side-operations.md へのリンクが付く（mj の元のログは変えない）。
  ガイド文書に書かない情報はログと同じ。写す一覧は `python3 scripts/sync_guides.py --dest <任意> copy --base HEAD --after HEAD --list`
- **`## 報告` を書き換えるときは、ファイルの中で最後に出てくる `## 報告` を対象にする。** `## 指示` に貼った指示文の中にも
  `## 報告` が出てくることがあり、最初の一致を使うと指示文の途中から後ろを消す（MD-14 のログで起きた。MD-15 で直した）
