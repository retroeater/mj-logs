# GitHub Actions（ワークフロー）

GitHub Actions のワークフローの一覧・動き・手動実行と変更の手順・チャット側の扱い（#492）。
**正は `.github/workflows/`。本数はここに書かない。** 下の「ワークフローの一覧」の表と実物の一致は `scripts/tests/test_workflows_table.py` が確かめる
（ワークフローを足す・消すときは、同じコミットで表の行も足す・消す）。

ここに置かないもの（各文書を見る）:

- 予約の起動を行う Worker `mj-scheduler`（入力 `scheduled`、保険の予約実行のゲート）: `docs/notes/scheduler-worker.md`
- 本番反映（Workers Builds）とビルドの失敗: `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」「ビルドが Cloning で失敗したとき」
- 自動化の決定: `docs/decisions/automation.md`
- 各ワークフロー固有の手順: 表の各行が挙げる文書（`docs/notes/live-channel-write.md`・`docs/notes/sns-book.md`・`docs/notes/sitemap-lastmod.md` など）
- クラウドセッションでの起動（GitHub MCP の `actions_run_trigger`）: `docs/notes/cloud-sessions.md`「gh の代わりに GitHub MCP」

## ワークフローの一覧

実行の契機と内容。

| ワークフロー | 内容 |
|---|---|
| `regenerate-page.yml` | 生成スクリプト・対応する`.js`・`scripts/lib/**`の変更のpushと、手動実行・ほかのワークフローからの呼び出し（毎週月曜05:37 JST の `all` は `fetch-youtube.yml` から）。ページを再生成してコミットする。シートは Sheets API（Secret `SHEETS_READER_SA_KEY`、#557）で読み、手動実行の入力 `dry_run`・`compare_ref`・`check` で書かずに確かめる（詳細は下の「regenerate-page.yml」） |
| `fetch-youtube.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-10-26 から。ほかの4本の後）と、保険の予約実行（毎週月曜05:37 JST。2026-10-16 に `regenerate-page.yml` から移した、#564 の段③-4）と手動実行（書かない道筋は無い）。YouTube のチャンネル情報（`scripts/fetch_youtube_channels.py`→`data/youtube_channels.json`、#3）と帰り道の動画の情報（`scripts/fetch_youtube_meta.py`→`data/youtube_meta.json`、#194）を取得してコミットし、`regenerate-page.yml` を `all`（入力 `wayhome` を真）で呼ぶ。取得が失敗しても既存のデータで生成し、最後のジョブで失敗にする。帰り道の一覧の OGP 画像と帰り道の知らせは `regenerate-page.yml` が行う（#340） |
| `regenerate-saikyo.yml` | 毎朝 `run-daily.yml` から `check-image-links` の後に（`workflow_call`、2026-10-16 から。それまでは検知の完了の `workflow_run`）と手動。`regenerate-page.yml` を `workflow_call` で呼び、saikyo_pages だけを再生成する。差分が無ければコミットしない（#537）。再生成の前にジョブ `abema` が ABEMA ブックの【2】を作り直す（`scripts/update_abema_book.py`、失敗しても再生成は動く。手動実行の入力 abema・regenerate、#425・`docs/notes/abema-book.md`）。ジョブ `abema` は ABEMA の新しい回が出たかも確かめ、予約の起動〈run-daily が渡す `scheduled`〉では JST の土曜だけ動いて #572 に知らせる（手動実行は知らせずに実行サマリへ、#572・`docs/notes/abema-book.md`「新しい回の検知」） |
| `update-live-channel.yml` | 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から。それまでは Worker `mj-scheduler` から 04:00 JST）と、保険の予約実行（毎日06:43 JST。当日の予約の起動〈run-daily のジョブ `live / update`、または update-live-channel.yml を直接起動した実行〉が成功済みなら動かないゲート付き、`docs/notes/scheduler-worker.md`「保険の予約実行のゲート（update-live-channel）」）と手動。/live の3層（#438）を毎日更新し、`regenerate-page.yml` を `workflow_call` で呼んで /live・title/ を作り直し、最後に `sync-yotei.yml` を呼んで予定表と放送対局の公開カレンダーを更新する（#404。手順・入力は `docs/notes/live-channel-write.md`「7. 毎日の取り込み」「チャット側の指示」） |
| `sync-yotei.yml` | `update-live-channel.yml` から層1の取り込みの後に（`workflow_call`、毎朝の run-daily と保険の実行の両方。2026-10-16 に update-live-channel の job yotei を切り出した、#564 の段③-3）と手動（入力 `apply`・`calendar_apply`・`calendar_max_delete`、既定は書かない）。連盟の予定表を予定表のスプレッドシートの【1】【2】【3】へ取り込み、放送対局の公開カレンダーへ同期する（#448、`docs/notes/yotei-sheet.md`。カレンダーに書くのは予約の起動と `calendar_apply` のときだけ） |
| `check-image-links.yml` | 毎朝 `run-daily.yml` から `update-sns-book` の後に（入力 `scheduled`、2026-10-16 から。それまでは Worker から 04:30 JST） `jpml_pros.html` の画像（ジョブ `check`）・最強戦の選手写真（ジョブ `saikyo`）・SNS ブックの実効の値の全行の画像（ジョブ `sns_book`、`check` の後に動く。X は `_400x400` だけ。`scripts/check_sns_book_images.py`）のリンク切れを確かめ、常設issueに書き出す（`check` と `sns_book` は「画像リンク切れの検知結果」の本文の自分の節だけを書き換え、全節で切れが無いと閉じる。閉じた後に切れが出たら、新しく作らずに常設の #352〈ラベル `種類: 常設`〉を開き直す〈2026-10-14 から〉。`saikyo` は別の issue）。手動実行の入力 `dry_run`（既定は書く）で issue を書かずに試せる。毎週月曜03:00 JST の予約実行（`schedule`）は `check` だけを動かす保険（外す予定は #504） |
| `check-saikyo-unregistered.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-10-26 から）と、保険の予約実行（毎週月曜06:50 JST）。最強戦の出場者で「プロ」「連盟プロ以外」から引けない人を常設issueに書く（#431、`docs/notes/saikyo-page-design.md`「8. 出場者の登録漏れの検知」） |
| `check-assets.yml` | `work/**` への push（`docs/`だけの push は除く。サイズを見る`docs/handover.md`・`docs/notes/chat-side-operations.md`は含む。`cloudflare` では走らせない。マージは検査済みの SHA の fast-forward のため、#298）と手動実行。`.assetsignore`の漏れ（#133）と CLAUDE.md・handover.md のサイズを検知し、chat-side-operations.md の純増（push は前の先頭、手動実行は cloudflare との merge-base と比べ、24,576 バイト超で増えていれば警告。job は成功のまま、#492）と配信の上限との比（`check_asset_limits.py`、#387）を出す。最後に共通の部品のテスト（`node --test`、#564）を回す |
| `regenerate-sitemap.yml` | HTMLを含むpush。sitemapのlastmodをgitの最終コミット日にそろえてコミットする（#265、`docs/notes/sitemap-lastmod.md`） |
| `cleanup-logs.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-10-26 から）と、保険の予約実行（毎週月曜06:23 JST）。7日を過ぎた作業ログを片付け（削除は `commit-push` の部品でコミット）、条件外のものを #357 に通知する（無ければ失敗） |
| `check-meibo.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-10-26 から）と、保険の予約実行（毎週月曜05:07 JST）。連盟員名簿と「プロ」シートの在籍者の不一致を常設 issue「名簿と「プロ」シートの不一致」に書く（ジョブ `meibo`）。あわせて最新の期の Ampai の URL が Ampai のブックに無ければ常設 issue「Ampai の URL が無い期」に書く（ジョブ `ampai`、`scripts/check_ampai.py`、#548）。どちらも閉じた常設 issue を開き直す（下の「共通の部品」の `upsert-issue`） |
| `sync-birthday-calendar.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-10-26 から）と、保険の予約実行（毎週月曜05:17 JST）。名簿の誕生日をGoogleカレンダーへ同期する（#379、`docs/notes/birthday-calendar.md`） |
| `fetch-gsc.yml` | 毎月1日03:00 JST に `run-monthly.yml` から（入力 `scheduled`、2026-11-01 から）と、保険の予約実行（毎月1日06:00 JST）。Search Console の検索パフォーマンスを `docs/gsc/` に取り出し、robots.txt の差分を #304 に知らせる（#269） |
| `fetch-redirect-counts.yml` | 毎月1日03:00 JST に `run-monthly.yml` から（入力 `scheduled`、2026-11-01 から）と、保険の予約実行（毎月1日06:30 JST）。どちらも `fetch-gsc.yml` の後。ほかに手動（入力 `dry_run` 既定真・`month`）。`docs/notes/redirects.md`「棚卸しの対象」の転送元ごとの件数を Cloudflare の GraphQL Analytics API から前月分だけ取り、`docs/redirect-counts/<YYYY-MM>.csv` にコミットして #568 にコメントする（`scripts/fetch_redirect_counts.py`、Secret `CF_ANALYTICS_TOKEN`・変数 `CF_ZONE_ID`）。ファイル冒頭の `ENABLED` が `'true'` でないあいだは予約実行も dry-run |
| `convert-x-posts.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-11-02 から。`mode=all`・既定のモデル・取得の後）と手動（GitHub の schedule は持たない）。3層のブックの【1】元データ（X の投稿）から【2】自動変換後を作る（#367、`scripts/convert_x_posts.py`、Secret `ANTHROPIC_API_KEY`・`GOOGLE_PLACES_API_KEY`〈無ければ Places を飛ばす〉・`LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`）。入力 `mode`（all: 【1】のうち【2】に無い投稿を【2】へ／stations: 【2】の既存の行の「駅」「駅の候補」「要確認」「要確認の理由」だけを、今の「駅」と理由に書かれた駅から計算し直して書き換える〈どの API も呼ばない。駅の一覧を取り直したときに使う〉）。Places は Text Search の place_id だけを取り（項目 `places.id`、2026-10-29 から）、駅・駅の候補・都道府県は本文の駅を国土数値情報の駅の一覧で確かめて決める（Google の駅名・座標は使わない。docs/notes/log-stations.md）。【2】の「Places の店名」の列は 2026-10-29 に消し、既存の行の駅は Claude Haiku で読み直した本文の駅で書き直した（一度きりの mode の reread・relist、CHAT-1029-LGN-07・08。使い終えて外した）・`models`（既定 `claude-sonnet-5-5`）・`limit`・`dry_run`（既定真: どの API も呼ばず、シートにも書かず、鍵・タブ・見出しと予定の回数・費用だけを出す）。名前は SNS ブックの X の ID から引き、【1】のアカウントのうち無い人がいれば止まる。上限はスクリプトの定数（Claude $3・Places 2,000回〈1件1回〉）で、届くか Places が失敗したら止まる。all は50行ごとに【2】へ書き、足す行の「表示」は判定が飲食店で要確認でなければ Y、ほかは N（2026-10-27 から。既存の行は変えない）。【1】【3】には書かない。旧「ログ」を読む compare・log-layer3・relookup は 2026-10-21 に外した。本文はジョブのログに出さない |
| `fetch-x-posts.yml` | 毎週月曜02:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-11-02 から。既定の2人〈it_uesama・retroeater〉を【1】の人ごとの最も新しい投稿日から今まで）と手動（GitHub の schedule は持たない）。本人の写真付きの投稿を X API の全期間検索 `from:<handle> has:images -is:retweet -is:reply` で `next_token` が無くなるまで読み、書き込み先のブック（3層）の【1】元データに API の値のまま足す（同じ投稿 ID は足さない。X API を呼ぶ前に見出しを書いて権限を確かめる）（#367、`scripts/fetch_x_posts.py`、Secret `X_BEARER_TOKEN`・`LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`）。入力 `handles`（`handle@YYYY-MM-DD` で人ごとの期間の初め）・`start_time`・`end_time`（now 可）・`since_latest`（期間の初めを【1】の人ごとの最も新しい投稿日にする）・`dry_run`（既定真: X API を呼ばず、シートにも書かず、鍵・ブック・【1】の見出しと予定の件数・費用だけを出す）。件数・費用の上限はスクリプトの定数（1,200件・$6、次のページは残りの件数までに縮める）。名前は SNS ブックの X の ID から引き、無い人がいれば止まる。旧「ログ」を読む ids・timeline・both・log は 2026-10-21 に外した。【2】【3】には書かない。本文はジョブのログに出さない |
| `security-check.yml` | 手動だけ（schedule を持たず、Worker の起動の表にもまだ載せない。月1回の起動は #564 の段④の後に足す）。zizmor（ワークフローの設定）・pip-audit（CI の Python の依存。組は `scripts/ci_requirements.py` がワークフローの `packages:`・`pip install` の行から集める）・履歴の鍵の走査（`scripts/scan_secret_history.py`、既知の当たりは `data/security/known-history-hits.txt`）を Job summary に書く。読むだけで Secret を使わない。履歴に新しい当たりがあるときだけ失敗にする（値は出さない）（#598、`docs/notes/security.md`） |
| `check-sheets-reader.yml` | 手動だけ（schedule・push・workflow_call を持たず、Worker の起動の表にも載せない）。コードが ID を持つ全ブック（`scripts/check_sheets_reader.py` の `BOOKS`）を、ログインなし（gviz・htmlview）と読み取り用サービスアカウント `sheets-reader`（Secret `SHEETS_READER_SA_KEY`、スコープ `spreadsheets.readonly`、Sheets API の `spreadsheets.get`・`values.get`）の両方で読み、ブックごとに成否・タブの数・各タブの行数と列数と、期待どおりか（専用フォルダのブックは `FOLDER_PUBLIC`〈2026-10-21 から偽〉に従いログインなしで読めない・名簿はログインなしで読める・検証用のブックは読めない）を実行サマリーに出す（#557、`docs/notes/sheets-reader.md`）。出すのは題・タブ名・行数・列数と要求の成否だけ（値・見出し・メールアドレス・プロジェクト ID は出さない。認証の action は `export_environment_variables: false`）。結果にかかわらず成功で終わる（認証の情報を読めないときだけ失敗） |
| `research-candidates.yml` | 手動だけ（schedule・push・workflow_call を持たず、Worker の起動の表にも載せない）。Claude API の Web 検索・Web 取得で出典付きの候補を作り、R_候補 の「候補」タブに追記する（CHAT-1031-RSC-02、`scripts/research_candidates.py`、Secret `ANTHROPIC_API_KEY`・`LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`）。入力 `kind`（今は A: 「連盟プロ以外」の名前かな、#628）・`targets`・`limit`・`model`（Haiku 5.5・Sonnet 5.5）・`max_cost`（$5 以下）・`recheck`・`dry_run`（既定真: API を呼ばず、シートにも書かず、対象と見込みの費用だけを出す）。仕組みは docs/notes/research-candidates.md |
| `sync-dojo-calendar.yml` | 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から。それまでは Worker から 04:15 JST）と、保険の予約実行（毎日07:12 JST）。道場部ゲストの告知画像を読み、カレンダーへの追加分と新規ゲスト・当月誕生日を #426 に知らせる（同期が失敗したとき・最新の月に道場部ゲストの見出しが無いときもその旨を知らせる、#472・#390）。新しい月の書き込みは手動実行のときだけで、保存した読み取り結果を使う。書き込み済みの月の画像が差し替わったときは、当日以降を自動で直して知らせる（変更3件以上・照合できない名前があれば通知だけ）（#390、`docs/notes/dojo-guest-calendar.md`） |
| `cleanup-branches.yml` | 毎日07:53 JST（保険）と手動、毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から。それまでは Worker から 04:20 JST）。マージ済みで先頭が24時間より前の `work/*` を削除する（#440、`scripts/delete_merged_branches.py`） |
| `check-build-failure.yml` | Workers Builds の check-run の完了（`check_run`、既定ブランチのファイルだけが働く）と手動（入力 check_run_id・dry_run）。名前が「Workers Builds:」で始まる failure で、コミットが cloudflare に入っていて、Cloudflare のビルドの API でブランチが cloudflare（本番のトリガー）ならジョブを失敗させて失敗通知メールで知らせる。確かめられないときも知らせる（`scripts/check_build_failure.py`、Secret `CF_BUILDS_TOKEN`、#561・#634、`docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき」）。それ以外の起動はジョブが skipped で残る（1日約100回）。2026-10-13 に有効化（`NOTIFY_ENABLED: 'true'`。cloudflare での手動実行 run 38415024219・38415113000・38415117501 で判定とジョブの失敗を確かめた） |
| `update-sns-book.yml` | 毎朝 `run-daily.yml` から `update-live-channel` の後に（入力 `scheduled`、2026-10-16 から。それまでは Worker から 04:10 JST）と手動（mode: check / init / update、dry_run 既定）。SNS ブックの【1】【2】【3】を書き、【3】のページが使う値が変わった日は `regenerate-page.yml`（all）を呼ぶ（`scripts/update_sns_book.py`、#514、`docs/notes/sns-book.md`） |
| `run-daily.yml` | 毎日04:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、2026-10-16 の朝から）と手動（`scheduled` は Worker 用で、手では付けない）。毎朝の本を1本にまとめ、`update-live-channel` → `update-sns-book` → `check-image-links` → `regenerate-saikyo` を `needs` で順に（後の3つは前が失敗しても `!cancelled()` で動く）、`sync-dojo-calendar`・`cleanup-branches` を並行に `workflow_call` で呼ぶ（#564 の段③）。最後にジョブ `logs` が mj-logs の同期を起動する（#563）。手動実行は書かない道筋だけ（各本に件数だけ・dry-run の入力を渡す。`regenerate-saikyo` だけは再生成し、差分があればコミットする）。どれか1本が失敗すると run-daily 全体が failure になり、Worker の朝の確かめ（#506）は run-daily の1行で知らせる（どの本かは mj-logs の status.md・morning.md のジョブ名で見る）。詳細と戻し方は下の「run-daily.yml」 |
| `run-weekly.yml` | 毎週月曜02:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、2026-10-26 から）と手動（`scheduled` は手では付けない）。週次の本を1本にまとめ、`check-meibo`・`sync-birthday-calendar`・`cleanup-logs`・`check-saikyo-unregistered` を並行に、`fetch-x-posts` → `convert-x-posts`（「ログ」の取り込み、#367）を順に、その後に `fetch-youtube` を `workflow_call` で呼ぶ（#564 の段④）。手動実行は書かない道筋だけ（`fetch-youtube` は飛ばす）。詳細と戻し方は下の「run-weekly.yml・run-monthly.yml」 |
| `run-monthly.yml` | 毎月1日03:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、2026-11-01 から）と手動（`scheduled` は手では付けない）。月次の本を1本にまとめ、`fetch-gsc` → `fetch-redirect-counts` を `needs` で順に `workflow_call` で呼ぶ（後は前が失敗しても `!cancelled()` で動く、#564 の段④）。手動実行は `fetch-gsc` を飛ばし、`fetch-redirect-counts` を dry-run で動かす。詳細と戻し方は下の「run-weekly.yml・run-monthly.yml」 |

- `scripts/sync_all_logs.py`（#298）: `origin/cloudflare` と未マージの `origin/work/**` のすべてについて、ログ・ガイド文書を mj-logs の作業ツリーへ写す・消す（commit・push はしない）。mj-logs 側の `sync-from-mj.yml` から呼ぶ
- mj-logs の `.github/workflows/sync-from-mj.yml`（#298）: Worker `mj-scheduler` から mj に push があった直後（`pushed_at` が6分以内の毎分の回、`docs/notes/scheduler-worker.md`「動き」）と、手動実行・毎日03:41 JST の予約実行（保険）で起動し、mj を blobless でクローンして `scripts/sync_all_logs.py` と `scripts/actions_status.py --repo retroeater/mj` を動かし、mj-logs 自身へ push する（mj を読むのは mj-logs の Secret `MJ_READ_TOKEN`）。mj の Actions の分を使わない。2026-10-07 から写しはこれだけ（mj の `sync-logs.yml` は 2026-10-21 に削除した）。push が拒否されたら origin/main に戻して写す・書き出すをやり直す（3回まで）
- `scripts/actions_status.py` の GitHub API の呼び出し（`get()`）は、5xx・タイムアウト（接続の失敗を含む）を5秒・10秒あけて最大3回取り直す。4xx はすぐ失敗にする（CHAT-1016-ACF-24）

## regenerate-page.yml

GitHub Actions (`.github/workflows/regenerate-page.yml`) が、`scripts/generate_*.py` / 対応する `.js` / `scripts/lib/**` の変更をcloudflareブランチへのpushで検知し、自動で再生成・コミットする（`chore: regenerate <ページ名>.html via GitHub Actions`）。検知はpushに含まれる全コミットの範囲（`github.event.before`〜`github.sha`）の差分で行う（#167）。手動実行（workflow_dispatch）も可能。毎週月曜05:37 JSTの`all`は`fetch-youtube.yml`が YouTube の情報を取得した後に呼び、差分がなければコミットしない（#103）。`regenerate-page.yml`を直接`all`で手動実行したときは YouTube の情報を取得しない（取得するときは`fetch-youtube.yml`を手動実行する）。帰り道の一覧の OGP 画像と帰り道の知らせは、直接の`all`と`fetch-youtube.yml`からの呼び出し（入力`wayhome`）のときだけ動く。帰り道の一覧の OGP 画像の Pillow は `scripts/requirements-ogp.txt` の版とハッシュで入れる（`--require-hashes`、2026-10-31）。`table.js`・`leagues.js`はルート直下の`*.js`に該当するためpushでワークフロー自体は起動するが、どのページ名にも一致せず対象0件で終わる（HTMLに焼き込まれないため実害なし）。`regenerate.py`は出力がディレクトリになるページ向けに`OUTPUT_OVERRIDES`（例: `wayhome_episodes` → `"wayhome/"`）を持ち、コミット・lastmod更新対象のパスとして返せる（#162）。ワークフローの`git add`は`-A --`で削除も拾い、`sitemap*.xml`をまとめて対象に含める。**1ページの生成が失敗したら、`regenerate.py`がそのページの途中の出力を戻して飛ばし、残りのページを生成する**（失敗の種類では分けない。生成の前の作業ツリーを一時の index で tree オブジェクトに取り、失敗したら増えたファイルを消して変わったファイルを戻す。前のページが変えた共有のファイルは前のページの出力に戻る）。コミット対象と`check_asset_limits`は成功したページだけで出し、飛ばしたページがあれば終了コード 3（`SKIPPED_EXIT`）を返して、飛ばしたページと理由（生成スクリプトの出力の最後の行）を stderr とジョブのサマリに出す。ワークフローは成功したページをコミット・push してから、最後のステップ「飛ばしたページを報告」で失敗にする（失敗通知メールを保つ。常設issueは作らない）。そのステップの注釈（`::error::`）の1行目は「生成に失敗したページを飛ばしました: <ページ名>, …」で、ページ名は`regenerate.py`がステップの出力`skipped_pages`に出したもの（サマリは REST で読めないため。mj-logs の morning.md が失敗の行に添える、#563）。手動実行・週次・`workflow_call`（`update-live-channel.yml`・`regenerate-saikyo.yml`）・セッションの`regenerate.py all`も同じ扱い（#533。手動実行の `dry_run` だけは警告の注釈で返して失敗にしない、#635）。**2026-09-22 開発凍結（`docs/notes/books-freeze.md`）以降、`regenerate.py`の`FROZEN_FROM_ALL`が`"all"`から`books_pages`を外し（ページ名を指定すれば個別には動く）、「楽天の書影を取得」ステップは`if: false`で止めている**

**同じ実行ブランチへの再生成は1本ずつ動く**（job `regenerate` の concurrency、組は `regenerate-page-<ref>`、`cancel-in-progress: false`・`queue: max`、#263）。`workflow_call` で呼ばれたときも効く（`github.ref` は呼ぶ側のもの。呼ぶ側の組〈`update-live-channel`・`update-sns-book`・`regenerate-saikyo`〉とは名前が違うので詰まらない）。`queue: max` が無いと待ちは1本で、3本目が来ると待っていた本が取り消される。入力 `target_page`・`github.event.before`・再生成したページの一覧は env で受け、`run:` に直接展開しない（#132。複数ページは `set -f` の下の単語分割と配列で渡す）

**シートは Sheets API で読む**（2026-10-21、#557。鍵は Secret `SHEETS_READER_SA_KEY`、`workflow_call` で呼ぶ側はすべて渡す。下の「権限と Secret の渡し方」）。Claude Code の環境には鍵が無いので、生成の確かめは手動実行の入力で行う:

- `dry_run`: 生成して、変わったファイルの名前と行数（`git diff --numstat`）だけを実行サマリに出す。サイトマップ・コミット・帰り道の OGP 画像と知らせは動かさない（値は出さない）
- `compare_ref`（`dry_run` のときだけ）: そのブランチ・コミットのコードでも、同じジョブの中で続けて同じ対象を生成し（`git worktree`）、両方の生成で変わったファイルのうち中身の違うものの名前と行数を出す（新旧の比べ。2026-10-21 の Sheets API への切り替えで、`compare_ref: origin/cloudflare` 相当で全ページの差分0を確かめた）
- `check`: `regenerate.py --check` で、各ページが生成で止まるかだけを確かめる（書かない、#551）

**確かめの結果は注釈で返し、ジョブは成功にする**（確かめ用の実行で失敗通知メールを出さない、#635。`scripts/regen_annotations.py`）。注釈の1行目は「compare: 差分あり N 件」「compare: 差分なし」（`compare_ref` の生成が失敗したら「、compare_ref の生成の終了コード N」が付く）・「check: 止まるページ N 件: <ページ名>, …」「check: 止まるページなし」で、2行目以降にファイル・止まる理由が続く。`dry_run` で飛ばしたページは「dry_run: 生成に失敗したページ: …」の警告になる。
読み方は `gh api repos/retroeater/mj/check-runs/<job_id>/annotations`（`job_id` は実行のジョブの ID）か、GitHub MCP の `get_job_logs` のログの `##[warning]`・`##[notice]` の行。`compare_ref` の参照が無い（例: 素の `cloudflare`。ランナーにあるのは `origin/cloudflare`）・ページ名の誤りなど、確かめ以前の失敗は今までどおりジョブを失敗にする

## run-daily.yml

毎朝の本を1本にまとめたワークフロー（#564 の段③。設計は CHAT-1015-WFL-14 のログ）。2026-10-15 に Worker の起動の表（`workers/scheduler/schedule.json`）を5行から run-daily の1行に切り替えた（初回は 2026-10-16 04:00 JST）。

- 並び: `live`（update-live-channel）→ `sns`（update-sns-book）→ `images`（check-image-links）→ `saikyo`（regenerate-saikyo）を `needs` で直列にし、後の3つは前が失敗しても `!cancelled()` で動く。`dojo`（sync-dojo-calendar）・`branches`（cleanup-branches）は並行。入れ子は最大3段（run-daily → update-live-channel・update-sns-book・regenerate-saikyo → regenerate-page）
- 権限は `permissions: {}` と job ごと（呼ばれる側は呼ぶ側の job を超えられない）、Secret は job ごとに呼ぶ本（入れ子の下を含む）が使うものだけを明示して渡す（下の「権限と Secret の渡し方」）。`concurrency` の組は `run-daily`（呼ばれる側の組とは名前が違い、詰まらないことを 2026-10-15 の手動実行で確かめた）
- 手動実行（`scheduled` なし）は書かない道筋だけ。各本に件数だけ・dry-run の入力を渡す（`regenerate-saikyo` だけは再生成し、差分があれば実行ブランチにコミットする）。書く手動実行は各本の画面で行う
- 最後のジョブ `logs` は、ほかの本の後に（失敗しても `!cancelled()` で）mj-logs の `sync-from-mj.yml` を `workflow_dispatch`（ref `main`）で起動し、actions/morning.md を毎朝の運行の後に書き直す（#563。Worker の起動は mj に push があった直後の回だけのため）。トークンは Secret `MJ_LOGS_DISPATCH_TOKEN`（mj-logs の Actions の Read and write だけの fine-grained）を env で受ける。手動実行（作業ブランチを含む）でも起動する（同期は何度動いても同じ結果になる）。起動が失敗するとこのジョブと run-daily 全体が failure になる（ほかの本のジョブの結論は変わらない）
- 呼ばれた本の実行は run-daily の実行の中のジョブ（`live / update` など）になり、各本のファイルには実行が付かない。各本の run-name・保険の `schedule` は、各本の手動実行・保険の実行のために残す（保険の `schedule` を外すのは段④）
- update-live-channel の保険のゲートは、当日・cloudflare・`[scheduled]` の (1) update-live-channel.yml の success の実行か、(2) run-daily.yml の実行のジョブ `live / update` の success のどちらかがあれば、保険の実行は何もしない（run-daily 全体の結論は見ない。ほかの本が落ちた日に保険が二重に動かないため）
- mj-logs の morning.md は、失敗の行に失敗したジョブ名を書き（見る範囲の26時間に1節へ出る実行は、`actions_status.py` が引く各ワークフローの直近5回に入らなくても `morning_report.write()` がジョブを引く。#563）、所要時間の絶対値（600 秒）の判定は、ほかの実行の中央値が 600 秒未満の本だけに効かせる（run-daily は中央値との比で見る。`scripts/morning_report.py`）
- morning.md の「4. 初回成功」は cloudflare の実行だけで、それ以前の cloudflare の実行に success が無いものを出す（作業ブランチで成功してからマージした本も、cloudflare での最初の成功が出る、#563）
- morning.md の「3. 所要時間の異常」は cloudflare の実行だけを見る。中央値の基準（比・絶対値とも）は、判定する実行と同じ種類（cloudflare で、予約の実行〈題の先頭が `[scheduled]` か `event` が `schedule`〉かどうかがそろったもの）の直近20回だけから取る（手動の試しや作業ブランチの実行で予約の実行の基準が下がらないように、#563）。比の判定は「直近20回の中央値の3倍以上」と「中央値＋5分（`SLOW_MARGIN`）以上」の両方を満たすときだけ出す（中央値1分前後の本の3〜5分を出さない、#563）
- 失敗の行のジョブのうち、失敗したステップが regenerate-page の「飛ばしたページを報告」のものは、そのジョブのログ（`GET /repos/{repo}/actions/jobs/{job_id}/logs`、Actions の Read）から `##[error]生成に失敗したページを飛ばしました:` で始まる行（注釈の1行目。`echo` の行は拾わない）を引いて「：」の後に添える（ほかのジョブでは API を呼ばない。引けなければ添えない、#563）。check-run の annotations は Checks の権限が要り、mj-logs の `sync-from-mj.yml` の `MJ_READ_TOKEN`（fine-grained、Checks を選べない）では引けないため使わない
- morning.md の「2. 予約実行の未実行」は、その日の run-daily の定期実行（題が `[scheduled]`）で呼ぶジョブ（`live` など。呼ぶ関係は run-daily.yml の `uses:` から読む）が success のワークフローの cron の予定を数えない（フォールバック実行はゲートで止まるだけ）。ワークフローが作られる前（API の `created_at`）の予定も見ない（CHAT-1017-WFL-27）

**戻し方**（切り替えをやめて本ごとの起動に戻す）: 次の3つを1コミットで戻して cloudflare に入れる（Workers Builds の `mj-scheduler` がデプロイする）。update-live-channel のゲートは両方の和なので戻さなくてよい。

1. `workers/scheduler/schedule.json` を5行（update-live-channel 04:00・update-sns-book 04:10・sync-dojo-calendar 04:15・cleanup-branches 04:20・check-image-links 04:30）に戻す
2. `regenerate-saikyo.yml` の `on.workflow_run`（`workflows: ['画像リンク切れの検知']`・`types: [completed]`・`branches: [cloudflare]`）と、ジョブ abema・regenerate の `if:` の「取り消された回は動かない」条件を戻す（切り替えの前の版は `git log -- .github/workflows/regenerate-saikyo.yml` で探す）
3. `workers/scheduler/test/scheduler.test.mjs` の起動の表のテスト（`schedule.json` の中身を期待値に持つもの）を5行の表に戻す

## run-weekly.yml・run-monthly.yml

週次・月次の本を1本ずつにまとめたワークフロー（#564 の段④の前半、CHAT-1021-WKP-02）。run-daily と同じ形で、Worker の起動の表に1行ずつある（`run-weekly.yml` 月曜 02:00・`run-monthly.yml` 1日 03:00。run-daily の 04:00 より前で、06:00 の朝の確かめの対象）。

- `run-weekly`: `meibo`（check-meibo）・`birthday`（sync-birthday-calendar）・`logs`（cleanup-logs）・`saikyo`（check-saikyo-unregistered）を並行に呼ぶ（互いに依存しない）。手動実行は各本に dry-run（sync-birthday-calendar は差分だけ）を渡す
- `run-weekly` の `xfetch`（fetch-x-posts）→ `xconvert`（convert-x-posts の all）: 「ログ」の取り込み（#367、2026-11-02 から）。4本と並行に動き、`xconvert` は `xfetch` が失敗しても `!cancelled()` で動く（【1】にある分を変換する）。対象は `fetch_x_posts.py` の既定の2人（it_uesama・retroeater。石立岳大は対象外）、期間は【1】の人ごとの最も新しい投稿日から今まで（`--since-latest`。同じ投稿 ID は足さない）。新しい行の表示は、判定が飲食店で要確認でなければ Y。手動実行（`scheduled` なし）は両方とも dry-run（X・Claude・Places を呼ばず、シートに書かない。書くかどうかは各本の `resolve-mode` が実行サマリに出す）。費用の見込みは週に X 20〜30件（約 $0.15）・Claude 約 $0.08・Places 20〜30回（Text Search の place_id だけ、2026-10-29 から）
- `run-monthly`: `gsc`（fetch-gsc）→ `redirects`（fetch-redirect-counts）。手動実行では `gsc` を飛ばす（commit を外しても robots.txt に差分があれば #304 にコメントするため）。`redirects` は dry-run
- `run-weekly` の `youtube`（fetch-youtube → regenerate-page）: ほかの本（4本と `xfetch`・`xconvert`）の後に `!cancelled()` で動く（最後に regenerate-page を all で呼び重いため。取り込んだ「ログ」もここでページに出る）。fetch-youtube に書かない道筋が無いので、手動実行（`scheduled` なし）では飛ばす。確かめは 2026-10-26 の予約の起動（CHAT-1021-WKP-03）
- 呼ばれる本の GitHub の `schedule` は保険として残る（外すのは CHAT-1021-WKP-03）。残るあいだは Worker からの回と保険の回の両方が動く。cleanup-logs の #357 への通知・fetch-redirect-counts の #568 へのコメント・fetch-gsc の robots.txt の差分の #304 へのコメントは2回になりうる
- `concurrency` の組は `run-weekly`・`run-monthly`（呼ばれる側の組と名前が違う）

**戻し方**: `workers/scheduler/schedule.json` から2行を消し、`workers/scheduler/test/scheduler.test.mjs` の起動の表のテストを1行の表に戻して cloudflare に入れる（Workers Builds の `mj-scheduler` がデプロイする）。各本の `workflow_call` と `scheduled` は残してよい（保険の `schedule` がまだあれば、それで元の時刻に動く）

## 共通の部品（`.github/actions/`）

繰り返しの書き方を寄せた composite action（#564 の段①）。ローカルの部品は checkout の後でないと読めないため、checkout は各本に残し、部品はその後で呼ぶ。
今は `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml`・`check-meibo.yml`・`check-image-links.yml`・`cleanup-logs.yml` が使う（残りの本への展開は #564）。

- `setup-python`: `.github/.python-version`（リポジトリ直下に置くと Workers Builds が毎回 Python を入れるため、#298。`scripts/tests/test_root_runtime_files.py`）の版で `actions/setup-python` を呼ぶ。入力 `packages`（pip で入れるもの）・`test`（`'true'` で `scripts/tests` の unittest）、出力 `python-version`。Python の版を上げるときは `.python-version` の1行を変える（#539）
- `resolve-mode`: 予約の起動（`schedule` か入力 `scheduled` が真）は書く、手動実行は入力 `manual-apply` に従う（`apply` はそのまま、`dry_run` は `!inputs.dry_run` を渡す）。出力 `scheduled`・`apply`・`dry-run`。ログと実行サマリに「event / scheduled / apply」の1行を出す。
  予約の起動の有効・無効は Worker の起動の表の `enabled` に任せ、`SCHEDULE_ENABLED` は使わない（部品に移した本から外す）。本ごとの判定（保険のゲート・曜日・予約の起動で外す入力）は出力 `scheduled` を使って各本に残す
- `upsert-issue`（中身は `upsert.js`）: 常設 issue を題（PR を除く・全ページ）か番号で探して書く。
  `mode` が `state`（型S）は、同じ題の issue を状態を問わず探し、ラベル `label`（既定「種類: 常設」）の付いたもの（番号の小さい方）、無ければ開いているものに書く（CHAT-1014-ACF-21 の作り）。
  問題あり（`close` が `'false'`）は、開いていれば本文を書き換え、閉じていれば開き直して書き換え、無ければ `label` を付けて作る。問題なし（`close` が `'true'`）は、開いていればコメントして閉じ（`body-file` があれば本文も書き換える）、閉じていれば何もしない。
  `read`（今の本文と `marker` を含む最後のコメントをファイルに書き出し、状態を出す）は `state` と同じ選び方。`notice`（開いている issue にコメントを足す。無ければ本文で作る。`create: 'false'` なら作らずに失敗、ただし足すコメントが無ければ何もしない）・`comment`（番号へコメント）は開いている issue だけを探す。
  入力 `body-file`・`comment-file`・`create`・`labels`・`label`・`dry-run`（書かずに、見つけた issue の番号・状態とすることと内容をログと実行サマリに出す。探すのは行う）・`run-url-footer`、出力 `number`・`action`（created / updated / reopened / commented / closed / read / none）・`body-path`・`comment-path`・`state`。
  本文の組み立ては各スクリプトに置く（例: `check_saikyo_unregistered.py`・`collect_saikyo_images.py --issue-dir`、`check_meibo.py`・`check_ampai.py --issue-dir --run-url`、`cleanup_logs.py --issue-dir`）。
  #352 のように節に分かれた本文は、`read` で今の本文を取り、スクリプトで自分の節を差し替え（`check_image_links.py --issue-section … --close-comment`）、`state` で書く。
  テストは `node --test .github/actions/upsert-issue/test/*.test.js`（`github` は偽物）。`check-assets.yml` が作業ブランチへの push ごとに回す
- `commit-push`: 入力 `paths`（`git add -A --` に渡す）・`message`・`body`・`message-file`（渡したら件名と本文の全体）・`retry`（`none` / `rebase`〈`pull --rebase` で3回まで〉）・`autostash`、出力 `committed`・`sha`。bot の名前でコミットし、**実行ブランチ（`github.ref_name`）にだけ push する**（push 先を選ぶ入力は持たない）。差分が無ければ何もしない

寄せられない点（各本に残す）: checkout の入力（`ref`・`fetch-depth`）、update-live-channel の保険のゲート・水曜の verify・予約の起動で外す `ALLOW_*`、update-sns-book の `mode` の上書き、sync-dojo-calendar の `--auto-update`、fetch-gsc の「JST で1日か」、regenerate-page の Pillow と `test_title_redirects.py`、sitemap-lastmod の push の再試行（rebase せず作り直す。`commit-push` の `retry` に無い）、regenerate-page の `img/ogp/wayhome` を足すかの判定（`paths` を呼ぶ側で組む）。

## 権限と Secret の渡し方

2026-10-18 から（#598 の中-4・中-5・低-1・低-2）。zizmor（`security-check.yml`）がこの形から外れたものを拾う。

- **`permissions`**: どのワークフローもトップレベルは `permissions: {}`、job ごとに必要な最小を書く（読むだけは `contents: read`、push は `contents: write`、issue を書くなら `issues: write`、実行の一覧を引くなら `actions: read`）。
  リポジトリの既定（Settings → Actions → Workflow permissions）は読み取りだけにする前提なので、**新しいワークフロー・job にも必ず書く**（書かないと push・issue の書き込みが 403 で落ちる）
- **入れ子**: `workflow_call` で呼ばれる側の job は、呼ぶ側の job の権限を超えられない（超えると起動の時点で失敗する）。呼ぶ側の job には、呼ばれる側のどの job よりも広い権限を与える。
  `regenerate-page.yml` の job は `contents: write`・`issues: write`（帰り道の知らせ）なので、呼ぶ側の job はすべて両方を持つ（ほかの本からの呼び出しでは知らせは動かない）
- **Secret**: `secrets: inherit` は使わない。呼ばれる側は `on.workflow_call.secrets` に受け取るものを宣言し、呼ぶ側は `NAME: ${{ secrets.NAME }}` で1つずつ渡す。入れ子の下で使う Secret は途中の段でも渡し直す
- **`run:`**: `${{ }}` を直接書かない。`github.*` は既定の環境変数（`$GITHUB_EVENT_NAME`・`$GITHUB_REF_NAME`・`$GITHUB_SHA`・`$GITHUB_SERVER_URL`・`$GITHUB_REPOSITORY`・`$GITHUB_RUN_ID`）、それ以外（`inputs.*`・`steps.*`・`github.event.*`）はステップの `env:` で受ける（#132）
- **`actions/checkout`**: push しない job は `persist-credentials: false`。push する job（checkout の認証で `git push` する）は付けない
- **第三者の action**: 40桁の SHA で固定し、`# v4.4.0` の形で版をコメントに残す。版を上げるのは下の「Dependabot」の PR（手で上げるときも同じ形。SHA は `git ls-remote --tags https://github.com/<owner>/<repo>` の、そのタグの行〈注釈付きタグは `^{}` の行〉）

今の渡し方（job → 権限 / Secret）:

| 呼ぶ側の job | 呼ぶ本（その下） | 権限 | 渡す Secret |
|---|---|---|---|
| `run-daily` の `live` | `update-live-channel`（→ `regenerate-page`・`sync-yotei`） | contents: write・issues: write・actions: read | `YOUTUBE_API_KEY`・`LIVE_SHEETS_SA_KEY`・`GCP_SA_KEY`・`SHEETS_READER_SA_KEY`（`GCP_SA_KEY` は `sync-yotei` の分） |
| `run-daily` の `sns` | `update-sns-book`（→ `regenerate-page`） | contents: write・issues: write | `LIVE_SHEETS_SA_KEY`・`X_BEARER_TOKEN`・`SHEETS_READER_SA_KEY` |
| `run-daily` の `images` | `check-image-links` | contents: read・issues: write | `SHEETS_READER_SA_KEY`（`X_BEARER_TOKEN` は手動実行の確かめ用だけ） |
| `run-daily` の `saikyo` | `regenerate-saikyo`（→ `regenerate-page`） | contents: write・issues: write | `LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY` |
| `run-daily` の `dojo` | `sync-dojo-calendar` | contents: read・issues: write | `ANTHROPIC_API_KEY`・`GCP_SA_KEY`・`SHEETS_READER_SA_KEY` |
| `run-daily` の `branches` | `cleanup-branches` | contents: write | なし |
| `run-daily` の `logs` | なし（mj-logs の `sync-from-mj.yml` を API で起動） | なし | `MJ_LOGS_DISPATCH_TOKEN` |
| `run-weekly` の `meibo` | `check-meibo` | contents: read・issues: write | `SHEETS_READER_SA_KEY` |
| `run-weekly` の `birthday` | `sync-birthday-calendar` | contents: read | `GCP_SA_KEY`・`SHEETS_READER_SA_KEY` |
| `run-weekly` の `logs` | `cleanup-logs` | contents: write・issues: write | なし |
| `run-weekly` の `saikyo` | `check-saikyo-unregistered` | contents: read・issues: write | `SHEETS_READER_SA_KEY` |
| `run-weekly` の `xfetch` | `fetch-x-posts` | contents: read | `LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`・`X_BEARER_TOKEN` |
| `run-weekly` の `xconvert` | `convert-x-posts` | contents: read | `LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY`・`ANTHROPIC_API_KEY`・`GOOGLE_PLACES_API_KEY` |
| `run-weekly` の `youtube` | `fetch-youtube`（→ `regenerate-page`） | contents: write・issues: write | `YOUTUBE_API_KEY`・`SHEETS_READER_SA_KEY` |
| `run-monthly` の `gsc` | `fetch-gsc` | contents: write・issues: write | `GSC_SERVICE_ACCOUNT_JSON` |
| `run-monthly` の `redirects` | `fetch-redirect-counts` | contents: write・issues: write | `CF_ANALYTICS_TOKEN` |
| `check-build-failure` の `notify` | なし（`check_run`・手動で直接起動） | checks: read・contents: read | `CF_BUILDS_TOKEN`（判定のステップの `env:`） |
| `update-live-channel` の `regenerate`・`update-sns-book` の `regenerate`・`regenerate-saikyo` の `regenerate`・`fetch-youtube` の `regenerate` | `regenerate-page` | contents: write・issues: write | `SHEETS_READER_SA_KEY`（楽天の2つは宣言だけ。取得の再開のときに `fetch-youtube` から渡す） |
| `update-live-channel` の `yotei` | `sync-yotei` | contents: read・issues: write | `GCP_SA_KEY`・`LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY` |

シートを読むワークフロー（2026-10-21、#557）は、読むスクリプトを動かすステップの `env:` で `SHEETS_READER_SA_KEY` を受ける（`lib/sheets.py` が環境変数から読む。認証の action の ADC は書き込みの鍵〈`LIVE_SHEETS_SA_KEY`〉やカレンダーの鍵に使うので、読み取りの鍵は ADC にしない）。

**呼ばれるワークフローに新しい Secret を足すとき**:

1. 使うワークフローの `on.workflow_call.secrets` に宣言する（手動実行・push で直接動くときはリポジトリの Secret がそのまま見える）
2. 上の表で、そのワークフローを呼ぶ job を全部たどり、各段の `secrets:` に `NAME: ${{ secrets.NAME }}` を足す。`regenerate-page.yml` なら `update-live-channel`・`update-sns-book`・`regenerate-saikyo`・`fetch-youtube` の `regenerate` と、その上の `run-daily` の `live`・`sns`・`saikyo`
3. 新しい権限が要るなら、呼ぶ側の各段の job にも足す
4. この表を直し、作業ブランチで `run-daily.yml` を `scheduled` なしで手動実行して（ドライラン）確かめる

## Dependabot

`.github/dependabot.yml` が、ワークフローと `.github/actions/*/action.yml` の action の版の更新を月1回、`cloudflare` 向けの PR で出す（`package-ecosystem: github-actions`、公開から7日は待つ `cooldown`）。
**受けるのはマイナー版・パッチ版だけ**（`ignore` の `update-types: version-update:semver-major`、2026-10-18 の平野さんの決定）。メジャー版の上げは、認証情報の台帳の四半期の棚卸し（#601）でまとめて見る（初回の実行が出したメジャー版の PR #602〜#608 は閉じた）。
PR は SHA と版のコメントを書き換える。Dependabot security updates（Settings）は Off のまま（2026-10-18）。

- PR のブランチは `dependabot/github_actions/…`。`work/*` ではないので、`cleanup-branches.yml`（マージ済みの `work/*` だけを消す）は触らない。Dependabot は閉じた PR のブランチを消す（手で閉じた #602〜#607 のブランチも数分のうちに消えた、2026-10-18）。マージした PR のブランチが残るかはリポジトリの「Automatically delete head branches」の設定しだい（未確認）。残ったら平野さんが消す（セッションからは削除が拒否される）
- PR のマージは `cloudflare` へのマージ（＝本番反映）なので、CLAUDE.md「ブランチ運用」の承認の規則に従う。セッションは PR のブランチへ push しない
- Dependabot が起こすワークフローの実行は Actions の Secret ではなく Dependabot の Secret を使う（今は無い）。`check-assets.yml` は `work/**` の push だけで動くので、PR では走らない
- **マイナー版・パッチ版の PR のマージの仕方（案。決めていない）**: Dependabot の PR はチャット経由で Code に確かめさせてからマージする。確かめる点は、差分が `uses:` の SHA と版のコメントだけか、新しい SHA が上流のそのタグを指すか（`git ls-remote`）、リリースノートに破壊的な変更が無いか、`security-check.yml` の zizmor の件数が増えないか、PR のブランチで `run-daily.yml` を `scheduled` なしで手動実行して全ジョブが success か

## ワークフローを手動実行するとき

実行の契機と内容の一覧は上の「ワークフローの一覧」。手動実行の前に次を確かめる。

- `regenerate-page.yml` の checkout と push 先は実行ブランチ（#326）。古い作業ブランチから手動実行すると、そのブランチの状態で生成物がコミットされる
- `check-image-links.yml` の checkout の ref は `${{ github.ref }}`（#306）で、手動実行では選んだブランチがチェックアウトされる。
  古い作業ブランチから実行すると常設issue（#218）の本文がそのブランチのデータで上書きされる（入力 `dry_run` を付ければ書かずに、することと本文を実行サマリに出す）。schedule・Worker の起動は既定ブランチ（cloudflare）で走るため変わらない。
  ジョブ`saikyo`は最強戦の選手写真を別のissueに書き出す（`collect_saikyo_images.py --json`、#139）。画像は1,985枚（handover.md に書いていた時点の数）。
  予約実行（`schedule`）ではジョブ`check`だけが動き（`saikyo`・`sns_book`は動かない）、入力`scheduled`（Worker からの起動用、手では付けない）ではジョブ`check`・`saikyo`・`sns_book`の3つとも動く（#514）。
  入力`saikyo_resolve_test`（X IDを1つ）を入れると、ジョブ`saikyo`は X API で解決を1回だけ試して診断を出し、検知も issue の書き換えもしない（ジョブ`check`も動かない。#514）
- `cleanup-logs.yml`（`scripts/cleanup_logs.py`、条件は `docs/notes/branch-operations.md`「作業ログの寿命」）: 手動実行は dry_run が既定（削除・コミット・通知をせず、することと本文を実行サマリに出す）。週次の schedule は削除・通知する
- `cleanup-branches.yml`（`scripts/delete_merged_branches.py`）: 手動実行は dry_run が既定。毎日の実行と、入力 `scheduled` を真にした起動は `SCHEDULE_ENABLED`（現在 `'true'`）が `'false'` なら dry-run（`'true'` なら実際に削除する）。`scheduled` は Worker からの予約の起動用で、手では付けない（`docs/notes/scheduler-worker.md`）
- `run-daily.yml`・`update-live-channel.yml`・`sync-dojo-calendar.yml`・`cleanup-branches.yml`・`check-image-links.yml`・`update-sns-book.yml`・`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml`・`check-meibo.yml`・`cleanup-logs.yml` の入力 `scheduled` は Worker `mj-scheduler` からの予約の起動用で、手では付けない（付けると予約実行と同じ動きになり、題に `[scheduled]` が付いて Worker の朝の確かめに数えられる。`docs/notes/scheduler-worker.md`）
- `check-meibo.yml`（`scripts/check_meibo.py`・`scripts/check_ampai.py`）: 手動実行は dry_run が既定（issue を書かず、することと本文を実行サマリに出す）。週次の schedule は issue を書く。名簿と Ampai はジョブを分けてあり、片方が落ちても片方は書く。不一致があっても生成は止めない
- `sync-birthday-calendar.yml`（`scripts/sync_birthday_calendar.py`）: 週次の schedule（毎週月曜05:17 JST）は書き込みまで行う。手動実行の既定は差分を出すだけで、apply を選んだときだけ書き込む。予約実行と同じ動きは apply をオン・allow_many_deletes をオフ（`docs/notes/birthday-calendar.md`）。allow_many_deletes は手動実行だけで効く（入力 `scheduled` のときは無視）
- `check-saikyo-unregistered.yml`（`scripts/check_saikyo_unregistered.py`）: 手動実行は dry_run が既定（issue を書かず、することと本文を実行サマリに出す）。週次の schedule は issue を書く
- `fetch-gsc.yml`（`scripts/fetch_gsc.py`、#269）: checkout と push 先は実行ブランチ。手動実行の既定はコミットしない（取得するだけ）。
  schedule は28〜31日 21:00 UTC に起動し、JST で1日の回だけ本体が動く（毎月1日 06:00 JST。2026-09-21 に有効にした）。
  `--plan` を付けるとAPIを呼ばずに期間と出力先だけ出せる
- `check-assets.yml` は Cloudflare へのアクセスを要しない検査専用
- **待機は上限15分。** ワークフロー・check-run・ビルド・デプロイなど外部の状態を、sleep を挟んで繰り返し確かめる「待機」は、始めてから15分で打ち切る（until ループなど上限の無い待機は使わない）。打ち切ったらその時点の状態をログに書き、確かめられなかったことを「未確認の項目」に回して先へ進む。テスト・生成・取得など、自分のコマンドが処理を進めている「実行」はこの上限の対象ではない（進み具合が出力に出るようにする）

## ワークフローを変更したとき

- **着手時に、同種の既存ファイルの履歴（`git log -- .github/workflows/<file>`）と関連する過去のログ・issueを見て、
  GitHubの仕様で初回だけ手順が違う点が無いか確かめる**（CHAT-0918-HT-06）
- **`.github/workflows/`配下を変更したら、マージ前に作業ブランチでワークフローを手動実行し結果を確認すること。**
  `gh workflow run <ファイル名> --ref <作業ブランチ>`で起動し、`gh run list --workflow=<ファイル名>`で
  runを特定して`gh run view <run-id> --log`を見る。省くと一度も実行されていないワークフローがマージされる。
  セッションのトークンで起動が403になる場合は、平野さんにGitHub画面の「Run workflow」を依頼する。
  **`workflow_dispatch`を持たないワークフローはマージ前に検証できないため、その旨を報告し判断を仰ぐこと**
- 新規追加のワークフローは既定ブランチに無いため`gh workflow run --ref <作業ブランチ>`が404になり、マージ前に実行できない。
  その場合は副作用の無い状態（dry-run 既定、schedule も dry-run で走る等）でマージし、`cloudflare`で手動実行して確認してから有効化する（CHAT-0918-HT-06/08）
- ワークフローを足す・消す・名前を変えるときは、上の「ワークフローの一覧」の表の行も同じコミットで直す（`scripts/tests/test_workflows_table.py`）

## Actions の使用量（2026-10 の実測、#298）

規則ではなく実測。数字は平野さんが取った Billing の usage report の CSV（10/1〜10/20。申告値で、セッションからは検証できない）。

- mj（private）: 3,203 分。10/19 に含まれる 3,000 分を超え、請求は 10/19 $0.978・10/20 $0.222（計 $1.20）
- mj-logs（public）: 約 6,800 分。CSV の discount_amount が gross と同じ（割引 100%）で請求 $0。含まれる 3,000 分にも数えられていない（mj が 3,000 分を超えた日に請求が始まった）
- mj の分は、API でジョブごとに1分へ切り上げて足した見積もり（3,198 分）と CSV の差が 5 分。mj の分は API の集計で足り、CSV が要るのは public のリポジトリの割引を確かめるときだけ
- Billing の画面のグラフ（Metered usage）は割引前の金額で、mj-logs の分を含む（10/20 までで約 $59）。画面の Group by にリポジトリは無く（None・SKUs だけ）、リポジトリ別の内訳は CSV（「Get usage report」）の repository の列で見る

## チャット側

`docs/notes/chat-side-operations.md` から移した（#492）。

- **実行結果の確かめ方:** mj-logs の `actions/status.md`（各ワークフローの直近5回。冒頭の書き出した時刻を見る、#498）。予約実行は2〜3時間遅れる（#504）。動いたかは契機・開始時刻で見て、ジョブのログは Code に確かめさせる。ジョブのサマリ（`GITHUB_STEP_SUMMARY`）は API に無いため Chrome で実行のページを開いて読む（annotations は Code が check-run の API で読める）
- **運行表:** mj-logs の `actions/index.html`（全ワークフローのつながり〈Worker・cron・push → ワークフロー → ジョブ → 呼ぶワークフロー〉と直近5回の結果。`.github/workflows/` と `schedule.json` から機械的に起こし、status.md と同じ同期で書く。`scripts/actions_dashboard.py`、#564 の段⑤）
- **ワークフローを変える指示:** そのワークフローを呼ぶ側（`uses:` で呼ぶワークフロー・schedule・`workflow_dispatch`）を `git grep` で洗って「前提」に列挙し、呼ばれ方ごとの振る舞いを「決定」に入れる（呼び出し元の見落としは、呼ばれた側で初めて出る）
- **失敗通知メールは1件ずつ扱う。** 本番稼働済みの Actions のエラーは、基本的に Actions のフォローアップのチャットで解決する（2026-10-12 平野さんの決定）。
  原因を調べる前に、その題材（ワークフロー・実行したブランチ）を直し中のチャットがあるかを確かめ、あればそこへ引き継ぐ。並行するチャットで同じ系列の Chat-Ref を出さない
  朝の確かめで見るのは mj の Actions・**mj-logs の Actions**（「mj の作業ログを写す」。Worker の朝の確かめは mj の行しか見ないので #506 に出ない）・Worker の起動（毎朝の運行が1回だけか）・#506・届いた失敗のメール。work/ ブランチの失敗はその系列のチャットへ伝言し、cloudflare の失敗は ACF のチャットが扱う
