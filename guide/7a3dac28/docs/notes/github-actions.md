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
| `regenerate-page.yml` | 生成スクリプト・対応する`.js`・`scripts/lib/**`の変更のpushと、毎週月曜05:37 JST（`all`）。ページを再生成してコミットする（詳細は下の「regenerate-page.yml」） |
| `regenerate-saikyo.yml` | `check-image-links.yml`（「画像リンク切れの検知」）の実行が cloudflare で終わるたび（`workflow_run`。取り消された回を除く。毎朝 04:30 JST の検知の後など）と手動。`regenerate-page.yml` を `workflow_call` で呼び、saikyo_pages だけを再生成する。差分が無ければコミットしない（#537）。再生成の前にジョブ `abema` が ABEMA ブックの【2】を作り直す（`scripts/update_abema_book.py`、失敗しても再生成は動く。手動実行の入力 abema・regenerate、#425・`docs/notes/abema-book.md`） |
| `update-live-channel.yml` | 毎日04:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、#504）と、保険の予約実行（毎日06:43 JST。当日の Worker からの起動が成功済みなら動かないゲート付き、`docs/notes/scheduler-worker.md`「保険の予約実行のゲート（update-live-channel）」）と手動。/live の3層（#438）を毎日更新し、`regenerate-page.yml` を `workflow_call` で呼んで /live・title/ を作り直す（#404。手順・入力は `docs/notes/live-channel-write.md`「7. 毎日の取り込み」「チャット側の指示」） |
| `write-live-channel-candidate.yml` | 手動実行のみ（mode: check / limited / full）。層1から作った表を3層のスプレッドシートの「【2】自動変換後」へ書く（#438。【2】は毎朝の `update-live-channel.yml` も書き直すが、層2の規則を直した直後に使うため残す。`docs/notes/live-channel-write.md`「6. 書き込みの実装・手動実行での確認」） |
| `check-image-links.yml` | 毎日 04:30 JST（Worker `mj-scheduler` から、入力 `scheduled`）に `jpml_pros.html` の画像（ジョブ `check`）・最強戦の選手写真（ジョブ `saikyo`）・SNS ブックの実効の値の全行の画像（ジョブ `sns_book`、`check` の後に動く。X は `_400x400` だけ。`scripts/check_sns_book_images.py`）のリンク切れを確かめ、常設issueに書き出す（`check` と `sns_book` は「画像リンク切れの検知結果」の本文の自分の節だけを書き換え、全節で切れが無いと閉じる。`saikyo` は別の issue）。毎週月曜03:00 JST の予約実行（`schedule`）は `check` だけを動かす保険（外す予定は #504） |
| `check-saikyo-unregistered.yml` | 毎週月曜06:50 JST。最強戦の出場者で「プロ」「連盟プロ以外」から引けない人を常設issueに書く（#431、`docs/notes/saikyo-page-design.md`「8. 出場者の登録漏れの検知」） |
| `assets-check.yml` | push（`docs/`だけの push は除く。サイズを見る`docs/handover.md`・`docs/notes/chat-side-operations.md`は含む、#298）。`.assetsignore`の漏れ（#133）と CLAUDE.md・handover.md のサイズを検知し、chat-side-operations.md の純増（push は前の先頭、手動実行は cloudflare との merge-base と比べ、24,576 バイト超で増えていれば警告。job は成功のまま、#492）と配信の上限との比（`check_asset_limits.py`、#387）を出す |
| `sitemap-lastmod.yml` | HTMLを含むpush。sitemapのlastmodをgitの最終コミット日にそろえてコミットする（#265、`docs/notes/sitemap-lastmod.md`） |
| `check-leagues-dropped.yml` | 手動実行のみ。型Cで選択リストから漏れている選手を検知する（#168） |
| `cleanup-logs.yml` | 毎週月曜06:23 JST。7日を過ぎた作業ログを片付け、条件外のものを #357 に通知する |
| `check-meibo.yml` | 毎週月曜05:07 JST。連盟員名簿と「プロ」シートの在籍者の不一致を、題名で探して作る issue に書く（例: #445。#370 はクローズ済み）。あわせて最新の期の Ampai の URL が Ampai のブックに無ければ issue「Ampai の URL が無い期」に書く（`scripts/check_ampai.py`、#548） |
| `sync-birthday-calendar.yml` | 毎週月曜05:17 JST。名簿の誕生日をGoogleカレンダーへ同期する（#379、`docs/notes/birthday-calendar.md`） |
| `fetch-gsc.yml` | 毎月1日06:00 JST。Search Console の検索パフォーマンスを `docs/gsc/` に取り出し、robots.txt の差分を #304 に知らせる（#269） |
| `sync-dojo-calendar.yml` | 毎日04:15 JST に Worker `mj-scheduler` から（入力 `scheduled`、#504）と、保険の予約実行（毎日07:12 JST）。道場部ゲストの告知画像を読み、カレンダーへの追加分と新規ゲスト・当月誕生日を #426 に知らせる（同期が失敗したとき・最新の月に道場部ゲストの見出しが無いときもその旨を知らせる、#472・#390）。新しい月の書き込みは手動実行のときだけで、保存した読み取り結果を使う。書き込み済みの月の画像が差し替わったときは、当日以降を自動で直して知らせる（変更3件以上・照合できない名前があれば通知だけ）（#390、`docs/notes/dojo-guest-calendar.md`） |
| `sync-books-calendar.yml` | **2026-09-22 開発凍結にともない無効化（`gh workflow disable`）。** 元は毎週月曜05:27 JSTに「書籍」タブの発売日をGoogleカレンダーへ同期していた（#97、`docs/notes/books-calendar.md`・`docs/notes/books-freeze.md`） |
| `sync-logs.yml` | **2026-10-07 から停止中（`workflow_dispatch` のみ。push・予約・Worker からの起動を外した、#298）。写しは mj-logs 側の `sync-from-mj.yml`（下）。** 以下は止める前の動き: `docs/logs/**` を含む push（cloudflare は毎回、`work/**` はコミットのメッセージに`[sync-logs]`のある push だけ。無い push はジョブが skip、#298）・毎日05:30 JST の Worker `mj-scheduler` からの起動（入力 `scheduled`、#504）・毎日 08:29 JST の予約実行（保険）・手動実行（この3つは目印に関係なく走る、#498）。実行の時点の mj と mj-logs を突き合わせ、写っていない・古い作業ログを public の `retroeater/mj-logs` の `logs/` へ写し、cloudflare で削除されたログを消す（#440。書き込みはシークレット `MJ_LOGS_TOKEN`）。concurrency は `queue: max` で、続けて来た実行を取り消さずに1本ずつ順に動かす（#509）。取り消された実行（待ちが100本を超えたとき、`queue: max` を持たない古い分岐の作業ブランチの実行）の分は、次の cloudflare の実行で追いつく（`scripts/sync_logs.py`、#454）。使用済みの Chat-Ref 識別子の一覧も毎回集め直して `chat-ids/` に写す（`scripts/chat_ids.py`、#474）。毎回、各ワークフローの直近5回の実行（開始時刻・契機・ブランチ・結論・run 番号・所要時間、失敗したジョブ名とステップ名）を mj-logs の `actions/status.md` に上書きで書き出す（`scripts/actions_status.py`、`GITHUB_TOKEN` の `actions: read`。コミットの題とログの中身は書かない、#498） |
| `delete-merged-branches.yml` | 毎日07:53 JST と手動、Worker `mj-scheduler` からの予約の起動（毎日04:20 JST、入力 `scheduled`、#504。Worker が動くのは平野さんがつないだ後）。マージ済みで先頭が24時間より前の `work/*` を削除する（#440、`scripts/delete_merged_branches.py`） |
| `notify-build-failure.yml` | Workers Builds の check-run の完了（`check_run`、既定ブランチのファイルだけが働く）と手動（入力 check_run_id・dry_run）。名前が「Workers Builds:」で始まる failure で、コミットが cloudflare に入っていればジョブを失敗させて失敗通知メールで知らせる（`scripts/check_build_failure.py`、#561、`docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき」）。それ以外の起動はジョブが skipped で残る（1日約100回）。2026-10-13 に有効化（`NOTIFY_ENABLED: 'true'`。cloudflare での手動実行 run 38415024219・38415113000・38415117501 で判定とジョブの失敗を確かめた） |
| `update-sns-book.yml` | 毎日04:10 JST に Worker `mj-scheduler` から（入力 `scheduled`）と手動（mode: check / init / update、dry_run 既定）。SNS ブックの【1】【2】【3】を書き、【3】のページが使う値が変わった日は `regenerate-page.yml`（all）を呼ぶ（`scripts/update_sns_book.py`、#514、`docs/notes/sns-book.md`） |

- `scripts/sync_all_logs.py`（#298）: `origin/cloudflare` と未マージの `origin/work/**` のすべてについて、`sync-logs.yml` の「ログ・ガイド文書を写す・消す」と同じ手順で mj-logs の作業ツリーへ写す（commit・push はしない）。mj-logs 側の `sync-from-mj.yml` から呼ぶ
- mj-logs の `.github/workflows/sync-from-mj.yml`（#298）: Worker `mj-scheduler` から mj に push があった直後（`pushed_at` が3分以内の毎分の回、`docs/notes/scheduler-worker.md`「動き」）と、手動実行・毎日03:41 JST の予約実行（保険）で起動し、mj を blobless でクローンして `scripts/sync_all_logs.py` と `scripts/actions_status.py --repo retroeater/mj` を動かし、mj-logs 自身へ push する（mj を読むのは mj-logs の Secret `MJ_READ_TOKEN`）。mj の Actions の分を使わない。2026-10-07 から写しはこれだけ（本番。`sync-logs.yml` は停止中）。push が拒否されたら origin/main に戻して写す・書き出すをやり直す（3回まで）

## regenerate-page.yml

GitHub Actions (`.github/workflows/regenerate-page.yml`) が、`scripts/generate_*.py` / 対応する `.js` / `scripts/lib/**` の変更をcloudflareブランチへのpushで検知し、自動で再生成・コミットする（`chore: regenerate <ページ名>.html via GitHub Actions`）。検知はpushに含まれる全コミットの範囲（`github.event.before`〜`github.sha`）の差分で行う（#167）。手動実行（workflow_dispatch）も可能。毎週月曜05:37 JSTにも`all`を自動実行し、差分がなければコミットしない（#103）。`table.js`・`leagues.js`はルート直下の`*.js`に該当するためpushでワークフロー自体は起動するが、どのページ名にも一致せず対象0件で終わる（HTMLに焼き込まれないため実害なし）。`regenerate.py`は出力がディレクトリになるページ向けに`OUTPUT_OVERRIDES`（例: `wayhome_episodes` → `"wayhome/"`）を持ち、コミット・lastmod更新対象のパスとして返せる（#162）。ワークフローの`git add`は`-A --`で削除も拾い、`sitemap*.xml`をまとめて対象に含める。**1ページの生成が失敗したら、`regenerate.py`がそのページの途中の出力を戻して飛ばし、残りのページを生成する**（失敗の種類では分けない。生成の前の作業ツリーを一時の index で tree オブジェクトに取り、失敗したら増えたファイルを消して変わったファイルを戻す。前のページが変えた共有のファイルは前のページの出力に戻る）。コミット対象と`check_asset_limits`は成功したページだけで出し、飛ばしたページがあれば終了コード 3（`SKIPPED_EXIT`）を返して、飛ばしたページと理由（生成スクリプトの出力の最後の行）を stderr とジョブのサマリに出す。ワークフローは成功したページをコミット・push してから、最後のステップ「飛ばしたページを報告」で失敗にする（失敗通知メールを保つ。常設issueは作らない）。手動実行・週次・`workflow_call`（`update-live-channel.yml`・`regenerate-saikyo.yml`）・セッションの`regenerate.py all`も同じ扱い（#533）。**2026-09-22 開発凍結（`docs/notes/books-freeze.md`）以降、`regenerate.py`の`FROZEN_FROM_ALL`が`"all"`から`books_pages`を外し（ページ名を指定すれば個別には動く）、「楽天の書影を取得」ステップは`if: false`で止めている**

## ワークフローを手動実行するとき

実行の契機と内容の一覧は上の「ワークフローの一覧」。手動実行の前に次を確かめる。

- `regenerate-page.yml` の checkout と push 先は実行ブランチ（#326）。古い作業ブランチから手動実行すると、そのブランチの状態で生成物がコミットされる
- `check-image-links.yml` の checkout の ref は `${{ github.ref }}`（#306）で、手動実行では選んだブランチがチェックアウトされる。
  古い作業ブランチから実行すると常設issue（#218）の本文がそのブランチのデータで上書きされる。schedule・Worker の起動は既定ブランチ（cloudflare）で走るため変わらない。
  ジョブ`saikyo`は最強戦の選手写真を別のissueに書き出す（`collect_saikyo_images.py --json`、#139）。画像は1,985枚（handover.md に書いていた時点の数）。
  ジョブ`saikyo`は予約実行（`schedule`）では動かず、入力`scheduled`（Worker からの起動用、手では付けない）ではジョブ`saikyo`だけが動く（#514）。
  入力`saikyo_resolve_test`（X IDを1つ）を入れると、ジョブ`saikyo`は X API で解決を1回だけ試して診断を出し、検知も issue の書き換えもしない（ジョブ`check`も動かない。#514）
- `cleanup-logs.yml`（`scripts/cleanup_logs.py`、条件は `docs/notes/branch-operations.md`「作業ログの寿命」）: 手動実行は dry_run が既定。週次実行は `SCHEDULE_ENABLED`（現在 `'true'`）が `'false'` なら dry-run
- `delete-merged-branches.yml`（`scripts/delete_merged_branches.py`）: 手動実行は dry_run が既定。毎日の実行と、入力 `scheduled` を真にした起動は `SCHEDULE_ENABLED`（現在 `'true'`）が `'false'` なら dry-run（`'true'` なら実際に削除する）。`scheduled` は Worker からの予約の起動用で、手では付けない（`docs/notes/scheduler-worker.md`）
- `update-live-channel.yml`・`sync-dojo-calendar.yml`・`sync-logs.yml`・`delete-merged-branches.yml`・`check-image-links.yml`・`update-sns-book.yml` の入力 `scheduled` は Worker `mj-scheduler` からの予約の起動用で、手では付けない（付けると予約実行と同じ動きになり、題に `[scheduled]` が付いて Worker の朝の確かめに数えられる。`docs/notes/scheduler-worker.md`）
- `check-meibo.yml`（`scripts/check_meibo.py`）: 手動実行は dry_run が既定。不一致があっても生成は止めない
- `sync-birthday-calendar.yml`（`scripts/sync_birthday_calendar.py`）: 週次の schedule（毎週月曜05:17 JST）は書き込みまで行う。手動実行の既定は差分を出すだけで、apply を選んだときだけ書き込む。予約実行と同じ動きは apply をオン・allow_many_deletes をオフ（`docs/notes/birthday-calendar.md`）
- `fetch-gsc.yml`（`scripts/fetch_gsc.py`、#269）: checkout と push 先は実行ブランチ。手動実行の既定はコミットしない（取得するだけ）。
  schedule は28〜31日 21:00 UTC に起動し、JST で1日の回だけ本体が動く（毎月1日 06:00 JST。2026-09-21 に有効にした）。
  `--plan` を付けるとAPIを呼ばずに期間と出力先だけ出せる
- `assets-check.yml` は Cloudflare へのアクセスを要しない検査専用
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

## チャット側

`docs/notes/chat-side-operations.md` から移した（#492）。

- **実行結果の確かめ方:** mj-logs の `actions/status.md`（各ワークフローの直近5回。冒頭の書き出した時刻を見る、#498）。予約実行は2〜3時間遅れる（#504）。動いたかは契機・開始時刻で見て、ジョブのログは Code に確かめさせる。ジョブのサマリ（`GITHUB_STEP_SUMMARY`）は API に無いため Chrome で実行のページを開いて読む（annotations は Code が check-run の API で読める）
- **ワークフローを変える指示:** そのワークフローを呼ぶ側（`uses:` で呼ぶワークフロー・schedule・`workflow_dispatch`）を `git grep` で洗って「前提」に列挙し、呼ばれ方ごとの振る舞いを「決定」に入れる（呼び出し元の見落としは、呼ばれた側で初めて出る）
- **失敗通知メールは1件ずつ扱う。** 本番稼働済みの Actions のエラーは、基本的に Actions のフォローアップのチャットで解決する（2026-10-12 平野さんの決定）。
  原因を調べる前に、その題材（ワークフロー・実行したブランチ）を直し中のチャットがあるかを確かめ、あればそこへ引き継ぐ。並行するチャットで同じ系列の Chat-Ref を出さない
