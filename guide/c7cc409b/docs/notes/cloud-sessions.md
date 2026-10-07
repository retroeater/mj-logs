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
- **クラウドのセッションでは、リポジトリの `.claude/settings.json` の許可ルールは auto モードの分類器の拒否を防がない**
  （読み込まれないのか適用されないのかは未確定。ZK-01 で `Bash(git checkout -b work/*)` にそのまま当たる単独のコマンドが拒否された、2026-09-29、#298）。
  確認の画面が出ないことも、ルールが効いた証拠にしない（auto モード自身の判定でも出ず、区別できない）。
  ルールは、クラウド以外で動かすときに効く可能性があり害も無いため残す

## 作業ブランチの用意

最初に `git fetch origin` と `git fetch --unshallow origin` を行う（下の「浅いクローン」）。

**既存のブランチを付け替える `git checkout -B`・`git branch -f`・`git reset --hard` は使わない。**
**ブランチの作成・切り替え・進める操作は、`cd`・`;`・`|`・`&&` を付けない単独のコマンドで、クローンの中から `git -C` も付けずに実行する**
（読むだけのコマンドとつなぐと全体が破壊的な操作として判定され、拒否されることがある。許可ルール `Bash(git checkout -b work/*)` の形にも合わせる。#298）。

分類器に拒否されたら（`git checkout -b work/…` が「Modify Shared Resources」「Interfere With Workloads」で拒否された例がある。同じ操作が通る回もある）、別の手段（`claude/…` のブランチを使う、許可ルールを足すなど）を試さずに止まり、コマンドの全文と理由をログの `## 報告` の「エラー」に書く（#298）。平野さんがそのコマンドを許可する返答を貼ったら、同じコマンドを1回だけ実行し直し、また拒否されたら止まる（チャット側の返し方は `docs/notes/chat-side-operations.md`「Claude Code とのやり取り」）。

ローカルに `work/<識別子>` があるか（`git rev-parse --verify --quiet work/<識別子>`）を先に見る。

- **ローカルにあり `origin/cloudflare` の祖先**（`git merge-base --is-ancestor work/<識別子> origin/cloudflare` が真）:
  `git checkout work/<識別子>` のうえ `git merge --ff-only origin/cloudflare` で進める
- **ローカルにあり祖先でない**: 捨てずに止まって報告する。例外として、リモートに `origin/work/<識別子>` があり、ローカルがそれと一致するかその祖先
  （`git merge-base --is-ancestor work/<識別子> origin/work/<識別子>` が真）なら、`git checkout work/<識別子>` のうえ
  `git merge --ff-only origin/work/<識別子>` で進め、下の「未マージの作業を続ける」と同じく cloudflare が祖先かを確かめて、偽なら `git merge origin/cloudflare` で取り込む
- **ローカルに無く、リモートにも無い**: `git checkout -b work/<識別子> origin/cloudflare`
- **ローカルに無く、リモートにあってマージ済み**（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）:
  同じく `git checkout -b work/<識別子> origin/cloudflare`（リモートは cloudflare の祖先なので、push は fast-forward になる）
- **ローカルに無く、リモートにあり未マージの作業を続ける**: `git checkout -b work/<識別子> origin/work/<識別子>` のうえ、
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
- check-run・コミット: `get_commit`、`get_check_run`、`actions_list`・`actions_get`。**コミットの check-run の一覧を引く MCP ツールは無い。**
  環境変数のトークンで `curl -sS -H "Authorization: Bearer $GITHUB_TOKEN" https://api.github.com/repos/retroeater/mj/commits/<SHA>/check-runs` を引く
  （名前・状態・成否・ID と `.output.summary`。SH-02・SH-03 で実測。トークンの値は出力しない）
- ワークフローの起動: `actions_run_trigger`（method `run_workflow`、ref は既定ブランチ `cloudflare`）。
  **入力はすべて文字列で渡す**（`{"dry_run": "true"}`）。起動できるのは既定ブランチにあるワークフローだけ（docs/notes/branch-operations.md「ワークフローを変更したとき」）
- ジョブのログ（`get_job_logs`）は**ジョブの完了後に読む**。実行中は HTTP 404 になる
- **複数の issue の本文・題・ラベルをまとめて書き換えるときは、1件ごとに書き換える直前に `updated_at` を取り直し、取得時と違えばその issue は書き換えずに飛ばして報告する。**
  10〜15件ごとに「済」の番号をログに追記して push する。本文・題の部分置換とラベルの付け外しは REST（`PATCH /issues/{n}`・`/labels`）で通るが、
  state の変更とコメントの作成は REST だと HTTP 405 になるため MCP（`issue_write`・`add_issue_comment`）で行う（#304 の月次の棚卸しにも当てはまる）
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
- `www.ma-jan.or.jp`、`x.com`、`pbs.twimg.com`、`www.youtube.com`、`img.youtube.com`、`ryoei.pro`、`thumbnail.image.rakuten.co.jp`
  （`ron2.jp` は 2026-09-28 に外した。龍龍への依存の廃止で、生成・検査が接続しなくなったため）
- `*.cloudflare.com`（2026-09-28。公式ドキュメント `developers.cloudflare.com` と公式ブログ `blog.cloudflare.com` を読むため。CW-11 で両方 200 を確認）
- `*.workers.dev`（2026-09-28。本番の Worker と作業ブランチのプレビューの確認用。CW-14 で両方 200 を確認。URL はログに書かない）
- `calendar.google.com`（2026-09-28。放送対局の予定表の公開カレンダーを読むため、#448）
- `saikouisen.com`・`npm2001.com`・`rmu.jp`・`mu-mahjong.jp`（2026-10-05。最高位戦・協会・RMU・麻将連合の公式サイトの選手一覧・プロフィールを読むため、#500。4つとも 200 で読めた）

未追加: `openapi.rakuten.co.jp`（books は凍結中）。再生成は `jpml_titles` で確かめた（MD-15。旧表は #441 で廃止し、今は `regenerate.py` の対象に無い）。鍵の要るスクリプトはクラウドでは動かない（次節）。

## 鍵

鍵（`YOUTUBE_API_KEY`・楽天・`ANTHROPIC_API_KEY`・スプレッドシート書き込みの認証など）は環境の変数に置かない。
**GitHub Actions のシークレットにだけ置き、鍵の要る処理はワークフローを `actions_run_trigger` で起動して行う。**

## ローカル確認の代わりにプレビュー

`wrangler` は入っておらず、`wrangler dev` は使わない。表示の確認は、作業ブランチの push で Workers Builds が作るプレビューで行う
（仕組みは docs/notes/cloudflare.md「work/ ブランチのプレビュー」）。URL は check-run「Workers Builds: mj」の出力にある。

- **プレビューの URL は非公開の URL として扱い、ログ・issue・コミットに書かない。ターミナルの最終報告にだけ書く**（CLAUDE.md「作業ログ」節）
- セッションからはプレビューに接続できないことがある（MD-17 では拒否された。2026-09-28 に `*.workers.dev` を許可してからは接続できる）。そのときは表示を確かめていないことを「未確認の項目」に書く
- **Workers Builds の check-run が、報告が止まったまま `in_progress` で残ることがある**（SH-02 の 048b4d9 は数時間 `in_progress`、SH-03 の 3def8ba は1つが残り別の1つが success）。
  同じコミットに別の「Workers Builds: mj」があればそちらの成否を見る。無ければ、プレビューの別名 URL や本番が新しい版を返すか（生成物・`assets/` のファイルを取得して手元と比べる）で確かめる

## 作業ログ

- push したログは `sync-logs.yml` が public の mj-logs に写す。`work/**` への push では、コミットのメッセージに `[sync-logs]` のある push（着手と、完了・判断待ち・中断の最後の push）だけ写り、途中の節目の push はジョブが skip する（#298）。目印の付け方と書かない情報は CLAUDE.md「作業ログ」節
- **ガイド文書も mj-logs に写る。** cloudflare への push でガイド文書（`scripts/sync_guides.py` の `ALLOWED_PATTERNS`: CLAUDE.md・
  docs/handover.md・docs/instruction-template.md・docs/new-page-checklist.md・docs/logs/_template.md・docs/notes/ 直下の .md・docs/decisions/ 直下の .md〈決定の記録〉）が変わると、
  `guide/<mj の短い SHA>/` へパスを保って写す（新しい順に10個を残す。最新は `guide/HISTORY` の最後の行）。
  チャット側の取得の道具が一度読んだ URL をキャッシュから返すため、変わるたびに URL を変える。
- **使用済みの Chat-Ref 識別子の一覧も写る。** `sync-logs.yml` の実行のたびに `scripts/chat_ids.py` が全ブランチの `Chat-Ref:` トレーラと `docs/logs/` の履歴から集め、
  最新の版と違えば `chat-ids/<mj の短い SHA>.md` に書く（10個を残す。最新は `chat-ids/HISTORY` の最後の行、#474）。写したログの末尾からリンクする
  mj-logs に写したログの末尾には、その時点で最新のフォルダと CLAUDE.md・handover.md・instruction-template.md・chat-side-operations.md・cloudflare.md・decisions/README.md へのリンクが付く（mj の元のログは変えない）。
  ガイド文書に書かない情報はログと同じ。写す一覧は `python3 scripts/sync_guides.py --dest <任意> copy --after HEAD --list`
- **Actions の実行結果も書き出す。** `sync-logs.yml` の実行のたび（push に加えて、毎日 05:30 JST の Worker からの起動・08:29 JST の予約実行〈保険〉・手動実行）に、`scripts/actions_status.py` が
  各ワークフローの直近5回の実行を mj-logs の `actions/status.md` に上書きする（#498）。push 以外は `[sync-logs]` の目印に関係なく走る。
  セッションでも `GITHUB_REPOSITORY=retroeater/mj python3 scripts/actions_status.py --dest <任意>` で同じ表を手元に作れる（環境変数のトークンを使う）
- ログの `## 報告` などの書き換え方（最後の一致を相手にする）と、「ログ（公開）」の行を書く前の写しの確かめは CLAUDE.md「作業ログ」節
