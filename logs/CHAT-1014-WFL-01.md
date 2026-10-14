# CHAT-1014-WFL-01

- 着手日時: 2026-10-14
- 対象issue: 親 issue（手順 2 で起票）・#263・#132
- ブランチ: work/1014-wfl
- 着手時HEAD: ec9e24c31

## 指示

【Claude作成】Claude Code 向け指示：GitHub Actions の整理（統合・共通化・改名・ダッシュボード）の調べと起票 Chat-Ref: CHAT-1014-WFL-01 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調査と起票だけの指示。`.github/`・`scripts/`・`workers/` は変えない） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1014-wfl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`.github/workflows/` の 19 本を、統合・共通化・改名・起動の表の一本化・ダッシュボードの生成の 5 段で整理する。この指示はその最初で、チャット側が集めた現状（下の「前提」）を実物で確かめ、親 issue を起こし、改名と統合の対応表（設計）をログに書く。コードは変えない。
決定（2026-10-14、平野さん）

* 役目を終えた 4 本（`sync-logs.yml`・`sync-books-calendar.yml`・`write-live-channel-candidate.yml`・`check-leagues-dropped.yml`）は削除する（`sync-logs.yml` は #298 の 10/21 の予定と合わせる）
* 毎朝の運行（Worker からの 5 本と、その連鎖の `regenerate-saikyo`）は 1 本のワークフロー（仮に `daily.yml`）にまとめ、先行関係は `needs:` で書く。各本は `workflow_call` 化して残し、手動実行は各本でできるようにする
* 週次・月次の起動も Worker `mj-scheduler` の起動の表（`workers/scheduler/schedule.json`）へ移し、GitHub 側の保険の `schedule` は外す（#504 の 11/30 を前倒し。ただし外す時機は #504 の観察の結果を見て別に決める。下の「前提」）
* ファイル名を `<動詞>-<対象>.yml` に統一する。ファイル名だけでなく、`name:`・`run-name`・入力の `description`・ファイル冒頭の説明コメント・docs の記述も同じ規則で改名して統一する
* ダッシュボードは案 A: mj-logs に静的ページ（`actions/index.html`）として生成し、GitHub Pages で開く（mj-logs の Pages の有効化は平野さんの手作業。再実行は GitHub の「Run workflow」画面へのリンク）
* 平野さんに複数の選択肢から選んでもらうときは、常に判断材料（比較）と推奨・その理由を添える（運用の指定。docs/decisions/operations.md に記録する）

前提（チャット側。平野さんの決定ではない）

* 現状の数値（チャット側が 2026-10-14 09:30 JST に GitHub API と cloudflare 522a4323 の実物から集めた。要確認）: ワークフロー 19 本・3,168 行・23 ジョブ。`workflow_call` を持つのは `regenerate-page.yml` だけで、呼ぶ側は `update-live-channel`・`update-sns-book`・`regenerate-saikyo` の 3 本。直近 7 日の実行 653 回（skipped 28）、1 分切り上げの推定で 811 分／週
* 同じ書き方の繰り返し（要確認。本数はチャット側の数え）: ①checkout → setup-python 3.12 → `pip install google-auth requests` → unittest が 8 本 ②「dry-run かどうかを決める」「実行するかどうかを決める」（`SCHEDULE_ENABLED`・`SCHEDULED`・`APPLY` の決め方）が 9 本 ③常設 issue を題名で探して作る／本文を書き換える／コメント／無ければ閉じる github-script が 10 か所（check-image-links ×3・check-meibo ×2・cleanup-logs・update-live-channel ×3・sync-dojo-calendar・regenerate-page の帰り道） ④`git config` → add → commit → push（失敗時 rebase か作り直しで 3 回）が 5 本 ⑤「実行サマリに表示」が 12 本 ⑥`python-version: '3.12'` の直書きが 19 か所
* 先行関係で時刻にしか頼っていないもの（要確認）: `update-sns-book`（04:10）→ `check-image-links` の `sns_book`・`saikyo`（04:30）、`update-sns-book` → `regenerate-saikyo`（今は検知の完了に連鎖。2026-10-14 は 04:30 の検知が起動せず最強戦の再生成も動かなかった。#506 の 10/14 のコメント）、`update-live-channel` の regenerate と `update-sns-book` の regenerate(all) の push の競合（#263。`regenerate-page.yml` に concurrency が無い）
* Worker の取りこぼしの埋め合わせは CHAT-1014-ACF-18 で実装済み・マージ済み（592334b3、#504。数日の観察中）。この系列（WFL）では Worker のコードを変えない。 `schedule.json` の行の追加（週次・月次）と GitHub 側の保険の `schedule` の削除は、#504 の観察の結果を平野さんが見てから、WFL の後の指示で行う
* `docs/notes/github-actions.md`（#492、CHAT-1014-GDS-01〜03）が Actions の記述の正で、`scripts/tests/test_workflows_table.py` が「ワークフローの一覧」の表と実物の一致を確かめる。ワークフローを足す・消す・改名する指示では、同じコミットで表を直す。未マージの work/1014-gds が `assets-check.yml` と `github-actions.md` を変えている（要確認）
* 改名の規則（チャット側の案。手順 3 で実物に合わせて確定する）: ファイル名は `<動詞>-<対象>.yml`、動詞は `check`（読むだけで結果は issue へ）・`sync`（外部へ差分を反映）・`update`（スプレッドシートを書く）・`fetch`（外部から取ってコミット）・`regenerate`（ページを作り直してコミット）・`cleanup`（リポジトリの片付け）の 6 つ。`name:` は「<対象>の<動作>」の名詞形（例: 「公開対象の検査」「マージ済みブランチの片付け」「本番ビルド失敗の検知」）。`run-name` の `[scheduled] …` は `name:` と同じ文字列。入力の `description` は「〜する（外すと〜だけ）」の形にそろえる。ファイル冒頭のコメントの 1 行目は `name:` と目的と issue 番号
* 改名の対応表（チャット側の案）: `assets-check.yml` → `check-assets.yml`、`sitemap-lastmod.yml` → `sync-sitemap-lastmod.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`。ほかはファイル名そのまま（`name:` の形だけそろえる）。新設: `daily.yml`（毎朝の運行）・`fetch-youtube.yml`（`regenerate-page.yml` から YouTube チャンネル・帰り道の取り直し・OGP・帰り道の知らせを分ける）・`sync-yotei.yml`（`update-live-channel.yml` の job `yotei` を分ける）
* 段の順（チャット側の案）: ①共通化の部品（composite action 4 つ＋ `.python-version`）→ ②削除 4 本・`regenerate-page` の job `regenerate` への concurrency・#132 の入力の env 化 → ③`daily.yml`・分割・改名（同時に 1 回）→ ④起動の表の一本化と保険の `schedule` の削除（#504 の観察後）→ ⑤ダッシュボードの生成（`scripts/actions_status.py` と同じデータから `actions/index.html`。流れ図の表は YAML の `on:`・`uses:`・`workflow_run` と `schedule.json` から起こす）
* ファイル名を変えると GitHub 上は別のワークフローになる（run 番号が 1 から、古い実行は旧名の下に残る）。`schedule.json`・`regenerate-saikyo.yml` の `workflow_run.workflows`（`name:` で参照）・`scripts/actions_status.py`・mj-logs の `sync-from-mj.yml`・Worker の朝の確かめ・docs の参照が追随の対象（要確認。手順 3 で洗う）
* 関係する既存の issue: #263（concurrency）・#132（入力の env 化）・#308（Node 24 対応版）・#538（ubuntu 26.04）・#539（Python 3.14）・#298（使用量）・#504（Worker）・#492（文書）・#563（morning.md）

手順

1. 同じ論点の issue を検索し（`gh issue list --state all --search` で「ワークフロー 整理」「composite」「共通化」「改名」「daily」「統合」など。クローズ済みも含めて確認）、親 issue に相当するものがあれば止まって報告する。あわせて「前提」の数値と繰り返しの本数を実物（`.github/workflows/`、`git grep`）で数え直し、食い違いをログの `## 経過` に表で書く（食い違いで止まらない。数の正は実物）。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `.github/workflows/`・`workers/`・`docs/notes/github-actions.md` を変えているかを洗い、ブランチ名と変えるファイルをログに書く
2. 親 issue「GitHub Actions の整理（統合・共通化・改名・ダッシュボード）」を起票する（ラベル「分野: 自動化」「分野: 整理・保守」）。本文は「決定」をそのまま写し、5 段をチェックリストで書き、各段で関係する既存の issue（#263・#132・#308・#539・#298・#504・#492）を参照する。#263 と #132 には「親 issue の段②で扱う」とコメントする（クローズしない）。期日は書かない。着手中のコメントは親 issue に書く（本文・状態・ラベルは手順 2 で作った後は変えない）
3. 改名と統合の対応表を設計としてログの `## 経過` に書く: 19 本すべてについて「今のファイル名／`name:`／新しいファイル名／新しい `name:`／行き先（残す・`daily.yml` から呼ぶ・分ける・削除）／参照している場所（`git grep` で洗ったファイルと行。`schedule.json`・`workflow_run`・`scripts/`・mj-logs 側・docs）」の表。「前提」の対応表と規則をもとにし、実物を見て変えたほうがよい名前があれば代案と理由を書く（決めるのは平野さん。`## 報告` の「判断が必要なこと」に並べる）。あわせて、composite action に寄せる 4 つ（setup-python／resolve-mode／upsert-issue／commit-push）について、寄せる先のファイル名・入力・出力の案と、本ごとに違っていて寄せられない点を書く

止まる条件

* 手順 1 で、親 issue に相当する open の issue がある（クローズ済みは報告だけ）
* 親 issue に他セッションの着手中のコメントがある
* `docs/notes/github-actions.md`「ワークフローの一覧」の表と `.github/workflows/` の実物が一致しない（`python3 -m unittest scripts/tests/test_workflows_table.py` が失敗する）。直さずに報告する
* （未マージの work/ ブランチとの重なり）この指示は `.github/`・`scripts/`・`workers/` を変えないため、重なりでは止まらない。洗った結果をログに書く
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、改名の代案と、段①の指示を書くのに平野さんの判断が要る点を並べる
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、`git log --all --grep` と docs/logs の履歴）: CHAT-1014-WFL-* も識別子 WFL も未使用
- 作業ブランチ: work/1014-wfl はローカル・リモートとも無し → `git checkout -b work/1014-wfl origin/cloudflare`（ec9e24c31）
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば…この行が指示文の最後の行です。」と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

### 手順1: 同じ論点の issue

`gh` が無く、`/search/issues` もセッションのプロキシが拒否する（「sessions are bound to their configured repositories」）ため、
`GET /repos/retroeater/mj/issues?state=all` で全 issue（562件、最大 #563）を取り、題と本文を手元で検索した（MCP の `search_issues` も併用）。
キーワード: ワークフロー・composite・共通化・改名・daily・統合・Actions・workflow・workflow_call・needs:・daily.yml。

- 親 issue に相当するもの: **無し**（open・closed とも）
- 近いが別の論点（open）: #298（使用量の監視と削減）・#308（Node 24）・#538（ubuntu 26.04）・#539（Python 3.14）・#132（入力の env 化）・#430（WIF）・#468（session-network.md の改名。ワークフローではない）
- 近いが別の論点（closed）: #6（push トリガーの汎用化）・#305（checkout v5）・#310（常設 issue の探索の破綻）・#326（作業ブランチでの手動実行）・#498（実行結果の書き出し）
- `python3 -m unittest scripts/tests/test_workflows_table.py`: OK（3 tests）。表と実物は一致

### 手順1: 「前提」の数え直し（cloudflare ec9e24c31、2026-10-14 11:36 JST）

| 項目 | 前提（チャット側） | 実物 | 備考 |
|---|---|---|---|
| ワークフローの本数 | 19 | 19 | |
| 行数 | 3,168 | 3,168 | `wc -l` |
| ジョブ数 | 23 | **25** | `check-image-links` 3・`update-live-channel` 3・`update-sns-book` 2・`regenerate-saikyo` 2・ほか15本が各1。呼び出しのジョブ（`uses:`）4つを含む |
| `workflow_call` を持つ | `regenerate-page.yml` だけ | 同じ | 呼ぶ側は `update-live-channel`（job `regenerate`）・`update-sns-book`（同）・`regenerate-saikyo`（同）の3本 |
| 直近7日の実行 | 653回（skipped 28） | **985回**（success 921・skipped 51・failure 13） | 窓は 10/07 11:36〜10/14 11:36 JST（前提より約2時間後ろ）。内訳: assets-check 579・sync-logs 134（10/07 の停止前）・regenerate-page 89・sitemap-lastmod 45・notify-build-failure 33・update-sns-book 23・update-live-channel 18・delete-merged-branches 15・check-image-links 15・sync-dojo-calendar 14・regenerate-saikyo 10・ほか10。差の原因は未確認（窓の端の sync-logs の量が大きい） |
| 推定分／週 | 811分 | **1,136分** | run の `run_started_at`〜`updated_at` を1分切り上げ、skipped を除いた和（run 単位。ジョブ単位の課金分とは違う） |
| ①checkout→setup-python→`pip install google-auth requests`→unittest | 8本 | **7本** | check-meibo・fetch-gsc・sync-birthday-calendar・sync-books-calendar・sync-dojo-calendar（pip に `anthropic==1.7.0` が加わる）・update-live-channel（job update）・write-live-channel-candidate。`pip install google-auth requests` だけ（unittest 無し）は ほかに regenerate-saikyo・update-sns-book・update-live-channel（job yotei）の3か所 |
| ②dry-run／実行の判定 | 9本 | **12本** | 判定の手順を持つ: check-meibo・check-saikyo-unregistered・cleanup-logs・delete-merged-branches・notify-build-failure（「dry-run かどうかを決める」5本）・fetch-gsc（「実行するかを判定」＋`COMMIT`）・sync-dojo-calendar・update-live-channel（「実行するかどうかを決める」）。env の式だけ: sync-birthday-calendar・sync-books-calendar（`APPLY`）・update-sns-book（`MODE`・`DRY_RUN`）・regenerate-saikyo（`MODE`）。`SCHEDULE_ENABLED` を持つのは6本 |
| ③常設 issue の github-script | 10か所 | **14か所（8本）** | check-image-links 3・check-meibo 2・update-live-channel 3・regenerate-page 2（前回の知らせを読む／書く）・check-saikyo-unregistered 1・cleanup-logs 1・sync-dojo-calendar 1・check-leagues-dropped 1（題で探さず番号へコメント）。ほかに fetch-gsc が `gh issue comment` で #304 へ（github-script ではない） |
| ④git config→commit→push | 5本 | **6本** | regenerate-page・update-live-channel（job update）: 失敗時 `pull --rebase` で3回／sitemap-lastmod: rebase せず作り直しで再試行／sync-logs: mj-logs へ、rebase で再試行（停止中）／cleanup-logs・fetch-gsc: 再試行なし |
| ⑤「実行サマリに表示」 | 12本 | **14本・17ステップ** | check-image-links 3・update-live-channel 2・ほか12本が各1。無いのは assets-check・notify-build-failure・regenerate-page・sitemap-lastmod・sync-logs |
| ⑥`python-version: '3.12'` | 19か所 | **21か所（18本）** | check-image-links 3・update-live-channel 2。notify-build-failure だけ setup-python が無い |

ほかに気づいた点（設計に効くもの）:

- `sync-dojo-calendar.yml`（07:12 JST）と `delete-merged-branches.yml`（07:53 JST）の保険の `schedule` には、`update-live-channel.yml` のような「当日の Worker の起動が成功済みなら何もしない」ゲートが無く、毎日2回動いている
- 実行回数の 59%（579/985）が `assets-check.yml`（作業ブランチと cloudflare の push ごと）
- `sync-books-calendar.yml` は 2026-09-22 から `gh workflow disable` で無効（github-actions.md の表）

### 手順1: 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）

| ブランチ | `.github/workflows/`・`workers/`・`docs/notes/github-actions.md` の変更 |
|---|---|
| work/1009-swp-526 | なし |
| work/1011-swp-hnd | なし |
| work/1011-swp-nav | なし |
| work/1013-acf-xsize | なし |
| **work/1014-gds** | `.github/workflows/assets-check.yml`（checkout に `fetch-depth: 0`、「chat-side-operations.md の純増を確認」の手順を足す）・`docs/notes/github-actions.md`（2行）。あわせて `docs/decisions/operations.md` も変える（この指示も同じファイルに追記するため、後でマージする側で末尾の衝突があり得る） |
| work/1014-wfl | この指示 |

この指示は `.github/`・`scripts/`・`workers/` を変えないため重なりでは止まらない。段①〜③の指示では `assets-check.yml` の改名・共通化が work/1014-gds の変更と重なる。

### 手順2: 親 issue

- #564「GitHub Actions の整理（統合・共通化・改名・ダッシュボード）」を起票（ラベル「分野: 自動化」「分野: 整理・保守」）。本文に「決定」を写し、5段をチェックリストで、各段に関係する issue（#263・#132・#308・#539・#538・#298・#504・#492・#563）を書いた
- **「決定」の原文の日付2つ（`sync-logs.yml` の「#298 の 10/21 の予定」、「#504 の 11/30 を前倒し」）は本文に写さず、「#298 の予定」「#504 の予定を前倒し」とした。** 指示の「そのまま写す」と「期日は書かない」（CLAUDE.md「issueの着手ルール」: 期日はカレンダー「ryoei.pro」）が食い違ったため、ルールの側を優先した（CLAUDE.md「Chat-Ref」節）。本文に注記の1行を置いた
- 段の順は「チャット側の案。決定ではない」と書いた
- 着手中のコメント: #564（起票前の他セッションの着手コメントは無し。作ったばかり）
- #263・#132 に「親 issue #564 の段②で扱う」とコメント（クローズしない）

### 手順3: 改名と統合の対応表（設計）

規則は「前提」の案（動詞6つ、`name:` は「<対象>の<動作>」の名詞形、`run-name` は `name:` と同じ、入力の `description` は「〜する（外すと〜だけ）」）をもとにした。
「参照」の code は `git grep -n -F '<ファイル名>'` の結果（自分自身を除く。`.github/workflows/` の行はほとんどコメント）、docs は `*.md`（`docs/logs/` を除く）の該当件数。
mj-logs 側は `sync-from-mj.yml` を raw で読んだ（mj-logs は API ではこのセッションから読めない）。ワークフローのファイル名を参照するのはコメント1か所（`sync-logs.yml`、5行目）だけで、処理は mj の `scripts/sync_all_logs.py`・`scripts/actions_status.py` を呼ぶ。

| # | 今のファイル | 今の `name:` | 新しいファイル | 新しい `name:` | 行き先 | 参照（code） | 参照（docs） |
|---|---|---|---|---|---|---|---|
| 1 | assets-check.yml | 公開対象を検査する | **check-assets.yml** | 公開対象の検査 | 残す（push 契機） | scripts/check_asset_limits.py:5 | CLAUDE.md 1・decisions/README 1・automation 1・publishing 3・new-page-checklist 1・notes/cloudflare 2・github-actions 2・handover-archive-2026 2・houou-race 1・houou-top 1・skills 1・static-generation 1。**未マージの work/1014-gds が本体を変える** |
| 2 | check-image-links.yml | 画像リンク切れの検知 | 同じ | 同じ | `daily` から呼ぶ（job check・saikyo・sns_book のまま） | regenerate-saikyo.yml:13（**`workflow_run.workflows` が `name:` で参照**）・:4・check-meibo.yml:90・regenerate-page.yml:54（コメント）・scripts/check_image_links.py:113・collect_saikyo_images.py:17・lib/x_images.py:7・**workers/scheduler/schedule.json:24**・workers/scheduler/test/catchup.test.mjs:11,76-105・scheduler.test.mjs:307 | decisions/automation 1・saikyo 1・notes/github-actions 4・saikyo-page-design 3・scheduler-worker 1・session-network 1・sns-book 1・static-generation 1 |
| 3 | check-leagues-dropped.yml | 型Cで漏れている選手の検知 | — | — | **削除** | なし（`scripts/check_leagues_dropped.py` が残る） | notes/github-actions 1・static-generation 1 |
| 4 | check-meibo.yml | 名簿と「プロ」シートの不一致の検知 | 同じ | 同じ（代案 A-4） | 残す（週次。段④で Worker の表へ） | sync-birthday-calendar.yml:21・scripts/check_ampai.py:6・generate_jpml_pros.py:396・tests/test_actions_status.py:14,53 | CLAUDE.md 1・decisions/operations 2・notes/birthday-calendar 1・github-actions 2・handover-archive-2026 2・static-generation 3 |
| 5 | check-saikyo-unregistered.yml | 最強戦の出場者の登録漏れの検知 | 同じ | 同じ | 残す（週次） | scripts/check_saikyo_unregistered.py:17 | decisions/pros 1・saikyo 2・notes/github-actions 1・saikyo-page-design 1 |
| 6 | cleanup-logs.yml | 作業ログの片付け | 同じ | 同じ | 残す（週次） | sync-logs.yml:14（削除予定） | decisions/operations 1・notes/branch-operations 3・github-actions 2・handover-archive-2026 1・static-generation 1・docs/logs/_template.md 1 |
| 7 | delete-merged-branches.yml | マージ済みの作業ブランチを削除する | **cleanup-branches.yml** | マージ済みブランチの片付け | `daily` から呼ぶ | scripts/delete_merged_branches.py:1・**schedule.json:19**・workers/scheduler/test/scheduler.test.mjs:150,152,155（`name:` の文字列も） | decisions/automation 1・notes/cloud-sessions 1・github-actions 3・scheduler-worker 3・static-generation 1 |
| 8 | fetch-gsc.yml | Search Consoleのエクスポート | 同じ | Search Console のエクスポート | 残す（月次。段④で Worker の表へ。「JST で1日か」のゲートは不要になる） | update-live-channel.yml:229（コメント） | docs/gsc/README 1・notes/github-actions 2 |
| 9 | notify-build-failure.yml | 本番ビルドの失敗の通知 | **check-build-failure.yml** | 本番ビルド失敗の検知 | 残す（check_run 契機） | scripts/check_build_failure.py:2 | decisions/cloudflare 1（`name:` の文字列）・notes/cloudflare 1・github-actions 1 |
| 10 | regenerate-page.yml | ページの再生成 | 同じ | 同じ | 残す＋**分ける**（YouTube チャンネル・帰り道の取り直し・OGP・帰り道の知らせ → `fetch-youtube.yml`。代案 A-3） | update-live-channel.yml:21,359,363（363 は `uses:`）・update-sns-book.yml:8,94,100（100 は `uses:`）・regenerate-saikyo.yml:5,18,97（97 は `uses:`）・check-meibo.yml:6・cleanup-logs.yml:51・fetch-gsc.yml:7・sitemap-lastmod.yml:4,9,46,49,51,62,74・scripts/check_wayhome.py:2,36・generate_saikyo_pages.py:1103・generate_wayhome_episodes.py:226・lib/wayhome_intake.py:3・regenerate.py:41,101・update_sitemap_lastmod.py:7。自身の 106・116 行が `workflow_ref` に自分のファイル名を書く | astro-migration-study 3・decisions/wayhome 1・handover 2・new-site-design 2・notes/abema-book 1・books-covers 1・books-freeze 4・branch-operations 1・cloudflare 1・github-actions 7・handover-archive-2026 1・live-channel-write 2・live-page-design 1・saikyo-page-design 3・session-network 1・site-findings 1・sitemap-lastmod 5・sns-book 1・static-generation 8・video-wayhome 6 |
| 11 | regenerate-saikyo.yml | 最強戦の再生成 | 同じ（代案 A-2） | 同じ | `daily` から呼ぶ（`workflow_run` を外す） | scripts/update_abema_book.py:4 | handover 1・notes/abema-book 1・github-actions 2・saikyo-page-design 2 |
| 12 | sitemap-lastmod.yml | サイトマップのlastmodを同期 | **sync-sitemap-lastmod.yml**（代案 A-1） | サイトマップの lastmod の同期 | 残す（push 契機） | fetch-gsc.yml:8・regenerate-page.yml:175（コメント） | notes/github-actions 1・saikyo-page-design 1・session-network 1・sitemap-lastmod 3・static-generation 1 |
| 13 | sync-birthday-calendar.yml | 誕生日カレンダーの同期 | 同じ | 同じ | 残す（週次） | fetch-gsc.yml:81（コメント） | notes/birthday-calendar 4・github-actions 2 |
| 14 | sync-books-calendar.yml | 書籍カレンダーの同期 | — | — | **削除**（今は `gh workflow disable` で無効） | scripts/tests/test_actions_status.py:15,59（テストの例の文字列） | notes/books-calendar 3・books-freeze 3・github-actions 1 |
| 15 | sync-dojo-calendar.yml | 道場部ゲストのカレンダー同期 | 同じ | 道場部ゲストのカレンダーの同期 | `daily` から呼ぶ | delete-merged-branches.yml:25・update-live-channel.yml:14,279（コメント）・**schedule.json:14**・scripts/tests/test_morning_report.py:16,17,84,88 | decisions/operations 1・notes/dojo-guest-calendar 4・github-actions 2・scheduler-worker 1 |
| 16 | sync-logs.yml | 作業ログを mj-logs へ写す | — | — | **削除**（#298 の予定に合わせる） | scripts/actions_status.py:1・chat_ids.py:1・sync_all_logs.py:3,84・sync_guides.py:1・sync_logs.py:1,4,21・tests/test_sync_logs.py:4,48・workers/scheduler/test/scheduler.test.mjs:253,261・mj-logs の sync-from-mj.yml:5（コメント） | decisions/automation 2・operations 1・notes/cloud-sessions 1・github-actions 4・scheduler-worker 1 |
| 17 | update-live-channel.yml | 「連盟ch」の毎日の取り込み | 同じ | 「連盟ch」の取り込み | `daily` から呼ぶ＋**分ける**（job yotei → `sync-yotei.yml`。代案 A-5） | regenerate-page.yml:16・scripts/check_unused_names.py:4,28・lib/yotei.py:497・**schedule.json:4**・catchup.test.mjs:9,119,120,142・scheduler.test.mjs:303。**自身の 143 行（保険のゲート）が API の URL に自分のファイル名を書く** | notes/chat-side-operations 1・github-actions 4・handover-archive-2026 1・live-channel-write 4・live-page-design 2・scheduler-worker 3（`name:` の文字列も）・yotei-sheet 3 |
| 18 | update-sns-book.yml | SNS ブックの更新 | 同じ | 同じ | `daily` から呼ぶ | check-image-links.yml:135・regenerate-saikyo.yml:65（コメント）・**schedule.json:9**・catchup.test.mjs:10,109,111,134,142・scheduler.test.mjs:305 | decisions/pros 1・wayhome 1・notes/cloud-sessions 1・github-actions 2・handover-archive-2026 1・scheduler-worker 1・sns-book 3・static-generation 1 |
| 19 | write-live-channel-candidate.yml | 「【2】自動変換後」への書き込み | — | — | **削除**（`scripts/write_live_channel_candidate.py` は update-live-channel が使うので残す） | なし | notes/github-actions 1・live-channel-write 2 |
| 新 | — | — | **daily.yml**（代案 A-6） | 毎朝の運行 | Worker から 04:00 に1回。下の「daily の形」 | — | — |
| 新 | — | — | **fetch-youtube.yml** | YouTube の情報の取り直し | 週次（段④で Worker の表へ）。下の A-3 | — | — |
| 新 | — | — | **sync-yotei.yml** | 予定表の取り込み | `daily` から（update-live-channel の後）。入力 `yotei_apply`・`calendar_apply`・`calendar_max_delete` を移す | — | — |

参照の追随で効くもの（ほかはコメントと docs の書き換え）:

- `workers/scheduler/schedule.json` の `workflow`（Worker は行のファイル名で起動・埋め合わせ・朝の確かめを行う。`scheduler.mjs` の `/actions/workflows/${row.workflow}/…`）
- `regenerate-saikyo.yml` の `workflow_run.workflows: ['画像リンク切れの検知']`（`daily` に入れると不要になる）
- `update-live-channel.yml` の保険のゲート（`…/workflows/update-live-channel.yml/runs`）と `regenerate-page.yml` の `contains(github.workflow_ref, '/regenerate-page.yml@')`
- `scripts/actions_status.py`・`scripts/morning_report.py` はファイル名を持たず、API の `path` と `schedule.json` から作る（改名に追随する）。ただし改名・削除の後、古い名前のワークフローが API の一覧（`/actions/workflows`）に残るかは未確認（残れば status.md に古い行が出る）
- Worker のテスト（`workers/scheduler/test/*.mjs`）はファイル名を例の文字列として持つだけで、改名しても通る（直すかは任意）
- `name:` の文字列を参照するのは `regenerate-saikyo.yml`（`workflow_run`）・`workers/scheduler/test/scheduler.test.mjs`・`docs/decisions/cloudflare.md`・`docs/notes/scheduler-worker.md` だけ。`scripts/check_image_links.py:113` などの「画像リンク切れの検知結果」は常設 issue の題で、ワークフローの `name:` ではない（変えない）

#### daily の形（段③の案）

```
daily.yml（name: 毎朝の運行）
on: workflow_dispatch（入力 scheduled）     ← Worker から 04:00 に1回
permissions: contents: write / issues: write / actions: read   ← 呼ばれる側の和（呼ばれる側は呼ぶ側を超えられない）
jobs:
  live:     uses update-live-channel.yml   （内で regenerate-page を呼ぶ。入れ子は3段）
  yotei:    needs live;  uses sync-yotei.yml
  sns:      needs live;  if !cancelled();  uses update-sns-book.yml   ← regenerate の push を live の後に並べる（#263）
  images:   needs sns;   if !cancelled();  uses check-image-links.yml ← sns_book・saikyo は SNS ブックの更新の後に読む
  saikyo:   needs sns;   if !cancelled();  uses regenerate-saikyo.yml ← 今の「検知の完了に連鎖」をやめ、SNS ブックの後に直接
  dojo:     uses sync-dojo-calendar.yml   （先行なし）
  branches: uses cleanup-branches.yml     （先行なし）
```

- 呼ばれる側の `run-name` は使われない（題は `daily` の `run-name` だけ）。呼ばれる側は `workflow_call.inputs` に `scheduled` を持ち、`daily` が `scheduled: true` を渡す。呼ばれる側の `github.event_name` は呼ぶ側のもの（`workflow_dispatch`）
- 先行の失敗の扱い: `needs` は既定で先行が成功のときだけ動く。シートの反映を止めない今の方針に合わせ、`yotei` 以外は `if: ${{ !cancelled() }}` を付ける（`yotei` は層1のコミットを読むので live の成功が要る。今と同じ）
- 呼ばれる側の workflow レベルの `concurrency` は、呼ぶ側と同じ group だと詰まることがある（要確認）。group はファイルごとに別の名前のままにし、#263 の concurrency は job `regenerate` に置く
- `daily` の実行1回の中で1つの job が失敗すると、Worker の朝の確かめは `daily.yml` の1行が failure と出るだけになる（どの部分かは `actions/status.md`・`morning.md` の失敗したジョブ・ステップで見る）

#### 代案（決めるのは平野さん。`## 報告` の「判断が必要なこと」に並べた）

- **A-1 `sitemap-lastmod.yml` の新しい名前**: 案の動詞 `sync` は「外部へ差分を反映」だが、このワークフローは外部へ出さず、リポジトリの `sitemap*.xml` を作り直してコミットする。
  比較: (a) `sync-sitemap-lastmod.yml`（チャット案。`sync` の定義を「差分を反映」に広げる必要がある）／(b) `regenerate-sitemap.yml`（`regenerate` を「生成物を作り直してコミット」と読めば定義に合う。`regenerate-page.yml` が同じ処理を内で呼んでいるのとも並ぶ）。
  **推奨 (b)**: 動詞の定義を崩さずに済み、並べたときに `regenerate-*` で生成物を作るものがまとまる
- **A-2 `regenerate-saikyo.yml`**: 中身は「ABEMA ブックの【2】を書く（job abema）」＋「`regenerate-page` を saikyo_pages で呼ぶ」。`daily` に入れると後者は `daily` から `regenerate-page` を直接呼べる。
  比較: (a) そのまま残す（チャット案。手動の「最強戦の再生成」が1つの画面で済む）／(b) `update-abema-book.yml`（ABEMA ブックの【2】だけ。`update` = スプレッドシートを書く、に合う）に改め、再生成は `daily` の job で `regenerate-page` を呼ぶ。
  **推奨 (a)**: 手動で「【2】を作り直してから再生成」を1回で行う使い方（#425・#537）が残り、変える量が少ない。(b) は名前の規則には合うが、手動では2本を順に動かすことになる
- **A-3 `fetch-youtube.yml` の切り出し方**: 帰り道の一覧の OGP 画像は `wayhome/index.html` と同じコミットに入れる決まり（#340）で、OGP の名前は最新話の公開日から決まる。取り直しと生成を別のワークフローにすると、この「同じコミット」が保てない。
  比較: (a) `fetch-youtube.yml` は YouTube のチャンネル情報（`data/youtube_channels.json`）と帰り道の情報（`data/youtube_meta.json`）を取ってコミットし、最後に `regenerate-page` を `all` で呼ぶ。OGP の作成と帰り道の知らせは `regenerate-page` に残す（`workflow_ref` の判定は「all のときだけ」に置き換える）／(b) OGP・知らせも含めて全部を `fetch-youtube.yml` に移し、`regenerate-page` の生成とコミットを `fetch-youtube.yml` の中で1回にまとめる（`regenerate-page` を呼ばず、生成の手順を2か所に持つ）。
  **推奨 (a)**: #340 の「同じコミット」を `regenerate-page` の中で保てる。取り直しのコミットと再生成のコミットが2つに分かれるが、どちらも bot のコミットで本番の見え方は変わらない。
  あわせて、凍結中の楽天の書影の取得（`if: false`）の行き先も決めが要る（削除／`fetch-youtube.yml` に入れず `regenerate-page` に残す）。推奨は「`regenerate-page` に残す」（books の凍結の解除のときに戻す元がそこにあるため、docs/notes/books-freeze.md）
- **A-4 `check-meibo.yml` の `name:`**: 今は Ampai の URL の検知（#548）も同じワークフローで行うが、`name:` は名簿だけを言う。
  比較: (a) そのまま「名簿と「プロ」シートの不一致の検知」／(b) 「名簿と Ampai の URL の検知」。**推奨 (a)**: Ampai は付け足しで、ファイル冒頭のコメントに書いてあれば足りる。題が長くなると一覧で読みにくい
- **A-5 `sync-yotei.yml` の名前**: job yotei は「予定表を読む → 予定表のスプレッドシートに書く（`update`）→ 放送対局の公開カレンダーへ同期（`sync`）」で、動詞が2つ当たる。
  比較: (a) `sync-yotei.yml`（チャット案。最後の外部への反映で名付ける）／(b) `sync-live-calendar.yml`（同期する先の「放送対局の公開カレンダー」で名付ける）。**推奨 (a)**: docs/notes/yotei-sheet.md・#448 の呼び名「予定表」と合い、スクリプト名（`fetch_yotei.py`・`write_yotei_sheet.py`）とも並ぶ
- **A-6 `daily.yml` の名前**: 決定の規則 `<動詞>-<対象>.yml` に合わない。
  比較: (a) `daily.yml`（規則の例外として認める。束ねるだけで動詞が当たらない）／(b) `run-daily.yml`（動詞 `run` = 「ほかのワークフローを束ねて動かす」を7つ目に足す）。
  **推奨 (b)**: 規則に例外を作らずに済み、段④で週次・月次の束（`run-weekly.yml` など）を作るときも同じ形で並ぶ

#### composite action の案（段①）

置き場所は `.github/actions/<名前>/action.yml`（ローカルの composite action は checkout の後でないと読めないため、checkout は各本に残す。checkout の入力〈`ref: github.ref`・`fetch-depth: 0`〉が本ごとに違うことも理由）。

1. **setup-python**（`.github/actions/setup-python/action.yml`）
   - 入力: `packages`（pip で入れるもの。既定は空。例 `google-auth requests`、dojo は `anthropic==1.7.0 google-auth requests`）・`test`（`'true'` で `python3 -m unittest discover -s scripts/tests`。既定 `'false'`）
   - 中身: `actions/setup-python` を `python-version-file: .python-version` で呼ぶ → pip → unittest
   - 出力: `python-version`
   - 寄せられない点: `regenerate-page` の Pillow は OGP の手順の中で条件付きで入れる（`packages` に入れると毎回入る）。`notify-build-failure` は Python を使わない。`regenerate-page` の unittest は `test_title_redirects.py` だけを失敗の文面付きで回す（`test` では寄せない）
2. **resolve-mode**（`.github/actions/resolve-mode/action.yml`）
   - 入力: `scheduled`（`inputs.scheduled`）・`manual-apply`（手動の「書く」入力。`apply`・`commit` はそのまま、`dry_run` は `!inputs.dry_run` を渡す）
   - 判定: 予約の起動 = `github.event_name == 'schedule'` か `scheduled` が真。予約の起動は書く、手動は `manual-apply` に従う
   - 出力: `scheduled`・`apply`・`dry-run`（`apply` の否定）。サマリに「event / scheduled / apply」の1行
   - 寄せられない点: (1) `SCHEDULE_ENABLED` が偽のときの動きが本で違う（check-meibo・check-saikyo-unregistered・cleanup-logs・delete-merged-branches は「dry-run で走る」、sync-dojo-calendar・update-live-channel は「走らない」）。6本とも `'true'` なので、**`SCHEDULE_ENABLED` をやめて Worker の表の `enabled` に任せる**案を推す（判断が要る）／(2) update-live-channel の保険のゲート（API で当日の起動を引く）と水曜の verify、予約の起動で `ALLOW_*` を外す処理／(3) update-sns-book の `mode` の上書き（予約は `update`）、sync-dojo-calendar の予約時の `--auto-update`・手動の入力を空にする処理／(4) fetch-gsc の「JST で1日か」（段④で Worker の月次の行にすれば不要）。これらは出力 `scheduled` を使って各本に残す
   - 注意: `workflow_call` で呼ばれたときに入力が渡らないと `!inputs.dry_run` が真（書く）になる。`daily` から呼ぶ本は `scheduled` を必ず渡す
3. **upsert-issue**（`.github/actions/upsert-issue/action.yml`。中身は `actions/github-script` で、スクリプトは `.github/actions/upsert-issue/upsert.js`）
   - 入力: `title`（題で探す。open・PR を除く・全ページ・番号の小さい方、重複は warning）または `number`（番号で直接。#450・#304 用）・`mode`（`state` = 本文を書き換え、0件なら閉じる／`notice` = コメントを足す、無ければ本文で作る）・`body-file`・`comment-file`（空ならコメントしない）・`close`（`'true'` で閉じる。`state` のとき）・`close-comment`・`create`（無いときに作るか。既定 `'true'`）・`labels`・`dry-run`（内容を出すだけ）・`run-url-footer`（実行ログの1行を足すか）
   - 出力: `number`・`action`（created / updated / commented / closed / none）・`body-path`（今の本文を書き出したファイル。節の書き換え用）
   - 3つの型に分かれる: **型S**（本文を書き換え、切れが0なら閉じる）check-image-links ×3・check-meibo ×2・check-saikyo-unregistered／**型N**（変化があればコメント、無ければ作る）update-live-channel ×2（未登録の名前・予定表の変化）・sync-dojo-calendar・regenerate-page（帰り道）・cleanup-logs／**型D**（番号へコメント）update-live-channel（#450）・fetch-gsc（#304、今は `gh issue comment`）・check-leagues-dropped（削除予定）
   - 寄せられない点: check-image-links は書き換える直前に本文を取り直し、Python（`--issue-section`）で自分の節だけを差し替える（`body-path` を出して2回呼ぶ形になる）／cleanup-logs は題の issue が無ければ作らずに失敗させる（`create: 'false'` で表せる）／sync-dojo-calendar は1回の実行で結果ごとに複数のコメントを書く（1件にまとめるか、呼ぶ回数を増やすか）／regenerate-page の帰り道と update-live-channel の #475 は「前回のコメント（印付き）」を読んで比べる（読むだけの `mode: read-last-comment` が別に要る）／notice JSON（`applied`・`comment`・`body`）の形は各スクリプトのもの
4. **commit-push**（`.github/actions/commit-push/action.yml`）
   - 入力: `paths`（`git add -A --` に渡す。空白区切り）・`message`（件名）・`body`（本文。既定は「Generated by <ワークフロー>（run: <URL>）」）・`message-file`（cleanup-logs のように一覧を本文に入れるとき）・`branch`（既定 `github.ref_name`）・`retry`（`rebase` = `pull --rebase` で3回／`none`）・`autostash`
   - 中身: bot の `git config` → add → 差分が無ければ終わる → commit → push（失敗時 `retry`）
   - 出力: `committed`（true/false）・`sha`
   - 寄せられない点: sitemap-lastmod は push を拒否されたら rebase せずに最新を取り直して計算し直す（`retry` に `recompute` を足すか、この本だけ残す）／regenerate-page は再生成できたページによって `img/ogp/wayhome` を足すか決める（`paths` を呼ぶ側で組む）／sync-logs は mj-logs へ push（削除予定なので対象外）

あわせて `.python-version`（中身 `3.12`）を置き、21か所の `python-version: '3.12'` を `python-version-file` に替える。#539（3.14）はこの1行の書き換えになる。

## 報告

- 状態: 判断待ち
- ブランチ: work/1014-wfl
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-WFL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl
- 確認用URL: なし
- マージ: 済（ドキュメントのみ。SHA は経過の末尾）
- issue: #564（起票）、#263・#132（段②で扱う旨をコメント）
- 判断が必要なこと:
  - 改名の代案（比較と推奨は経過「代案」）: A-1 `sitemap-lastmod.yml` → (a) `sync-sitemap-lastmod.yml`／(b) `regenerate-sitemap.yml`、推奨 (b)。A-2 `regenerate-saikyo.yml` → (a) 残す／(b) `update-abema-book.yml` に改め再生成は daily から、推奨 (a)。A-3 `fetch-youtube.yml` の切り出し → (a) 取り直しとコミットだけを移し OGP・帰り道の知らせは `regenerate-page` に残す／(b) 全部移す、推奨 (a)（#340 の「同じコミット」を保つ）。凍結中の楽天の書影の取得は `regenerate-page` に残すのを推奨。A-4 `check-meibo.yml` の `name:` → 推奨はそのまま。A-5 `sync-yotei.yml` →(a) そのまま／(b) `sync-live-calendar.yml`、推奨 (a)。A-6 `daily.yml` → (a) 規則の例外／(b) `run-daily.yml`（動詞 `run` を足す）、推奨 (b)
  - 段①に要る判断: (1) `SCHEDULE_ENABLED`（6本、すべて `'true'`）をやめて Worker の表の `enabled` に任せるか。偽のときの動きが本で違い（dry-run で走る4本／走らない2本）、残すと resolve-mode に分岐が要る。推奨: やめる (2) 部品への置き換えの範囲: (a) 全本を1回で／(b) 1〜2本（例: check-saikyo-unregistered・sync-birthday-calendar）で試してから広げる。推奨 (b)（作業ブランチで手動実行して確かめる本数が少なく、失敗の範囲が狭い） (3) sync-dojo-calendar の結果ごとの複数コメントを1件にまとめてよいか（upsert-issue を1回で呼べる。推奨: まとめる） (4) `.python-version` は段①で `3.12` のまま入れ、#539 はその1行の変更にするか（推奨: そうする）。#308（Node 24 の版）は部品に入れるときに同時に上げず別にする（推奨）
  - 段③に効く判断（先に知らせる）: `daily` にまとめると Worker の起動の表の5行が `daily.yml` の1行になり、update-live-channel の保険のゲート（自分のファイル名で当日の起動を引く）も `daily` に移す必要がある。(a) 段③で `schedule.json` の行の置き換えも同時に行う／(b) 段③を #504 の観察の後に回す。推奨 (a)（行の置き換えは Worker のコードを変えない。表の行を残したまま各本の `run-name` が消えると、朝の確かめが毎日 failure を出す）
  - sync-dojo-calendar・delete-merged-branches の保険の `schedule` に当日の起動を見るゲートが無く、毎日2回動いている。(a) 段④で `schedule` を外すまで置く／(b) 段②で先に外す。推奨 (a)（段④の時機は #504 の観察で決める決定のため）
  - 報告: 親 issue の本文に「決定」の原文の日付2つ（10/21・11/30）を写さず「#298 の予定」「#504 の予定」とした（期日を書かない規則を優先、経過「手順2」）。直す場合は指示を
- 未確認の項目:
  - 直近7日の実行回数が前提（653回）と実物（985回）で違う原因（窓が約2時間ずれ、10/07 の停止前の sync-logs の量が大きい）
  - 改名・削除の後、古い名前のワークフローが API の一覧（`/actions/workflows`）に残り `actions/status.md` に古い行が出るか
  - 呼ばれる側の workflow レベルの `concurrency` が呼ぶ側と詰まらないか、入れ子（daily → update-live-channel → regenerate-page の3段）が上限内か（段③で公式の文書と手動実行で確かめる）
  - work/1014-gds も `docs/decisions/operations.md` の末尾に追記している。この指示のマージの後に work/1014-gds をマージするとき、末尾で衝突し得る（両方を残せば解ける）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9ec82bb7）: https://github.com/retroeater/mj-logs/tree/main/guide/9ec82bb7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ec82bb7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
