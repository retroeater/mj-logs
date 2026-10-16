# 用語集

チャット側の指示文・Claude Code の作業ログ・docs で使う語をここに揃える（2026-10-16 平野さんの決定）。
**新しく書く文はこの語に従う。今ある docs・issue はその箇所を触るときに直す**（一括では置き換えない）。
**Git・GitHub・開発の用語は原語（英語、またはそのカタカナ）のまま書く**（例: マージ・push・cron・dry run。平野さんがエンジニアのため、和語に言い換えない）。
チャット側は docs/notes/chat-side-operations.md、Claude Code は CLAUDE.md からここを参照する。

## GitHub Actions・Worker

| 使う語 | 使わない語 | 意味 | 対応する英語・ファイル・入力 |
|---|---|---|---|
| ワークフロー | 本（「3本」など） | `.github/workflows/` の1ファイルが定める自動処理。数えるときは「3 つのワークフロー」 | workflow、`.github/workflows/<名前>.yml` |
| ジョブ | — | ワークフローの中の処理の単位。別のワークフローを呼んだときは `live / update` のように「呼んだ側のジョブ / 呼ばれた側のジョブ」の名前になる | job（`jobs:` の下のキー） |
| 実行 | — | ワークフローを1回動かしたもの。番号（`#3`）と ID で呼ぶ | run（`/actions/runs/<ID>`） |
| スケジュール | 起動の表 | Worker `mj-scheduler` が毎分見て、決まった時刻にワークフローを起動するための表 | `workers/scheduler/schedule.json` の `rows` |
| 定期実行 | 予約の起動・予約実行 | Worker がスケジュールの時刻に、入力 `scheduled` を真にして起動する実行。題の先頭に `[scheduled]` が付く | `workflow_dispatch` ＋ 入力 `scheduled: true` |
| cron 実行 | 予約実行 | GitHub の `schedule`（cron）が本来の起動であるワークフロー（週次の check-meibo・check-saikyo-unregistered・cleanup-logs など）の実行。フォールバック実行ではないもの | `on.schedule`（cron）、契機 `schedule` |
| フォールバック実行 | 保険の実行・保険の予約実行 | Worker からの定期実行が無かった日に備えて、GitHub の `schedule`（cron）で動く実行。その日の定期実行が成功済みなら何もせずに終わる | `on.schedule`（cron）、契機 `schedule` |
| 手動実行 | — | 人（またはセッション）が画面・API から起動する実行 | `workflow_dispatch`（`scheduled` なし） |
| 本実行 | 書く道筋 | スプレッドシート・カレンダー・issue・ブランチに書き込む動かし方 | 入力 `apply: true`・`dry_run: false` |
| ドライラン | 書かない道筋 | 読んで結果を出すだけで、書き込まない動かし方 | 入力 `apply: false`・`dry_run: true` |

- 「本実行」は書き込むかどうかの区別で、「本番」（`cloudflare` への反映）とは別の語
- 「定期実行」は Worker からの起動に限る。GitHub の `schedule` で動く実行は、フォールバックなら「フォールバック実行」、本来の起動なら「cron 実行」
- 毎朝の定期実行は `run-daily.yml`（毎朝の運行）が1回動き、その中で各ワークフローを呼ぶ（docs/notes/github-actions.md「run-daily.yml」）

## ブランチ・反映

| 使う語 | 使わない語 | 意味 | 対応する英語・ファイル・入力 |
|---|---|---|---|
| 作業ブランチ | — | セッションごとに切る `work/<識別子>` のブランチ（CLAUDE.md「ブランチ運用」） | `work/<識別子>` |
| マージ | 取り込み（git の意味） | 作業ブランチを `cloudflare` へマージすること（`git push origin <作業ブランチ>:cloudflare`。`cloudflare` に入ると本番反映される）と、`origin/cloudflare` を作業ブランチにマージすること（`git merge origin/cloudflare`） | merge（fast-forward の push・`git merge`） |
| 本番反映 | — | `cloudflare` への push を Cloudflare Workers Builds が検知して、サイト（ryoei.pro）や Worker をデプロイすること（docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」） | deploy、check-run「Workers Builds: mj」など |

- 「取り込み」はデータを外から持ってくる意味（ワークフローの名前「「連盟ch」の取り込み」「予定表の取り込み」、「層1の取り込み」など）にだけ使う。git の意味では「マージ」と書く（例: 「`origin/cloudflare` を作業ブランチにマージする」）

## Google スプレッドシート

| 使う語 | 使わない語 | 意味 | 対応する英語・ファイル・入力 |
|---|---|---|---|
| スプレッドシート | ブック | Google スプレッドシートの1ファイル（公式の表記） | spreadsheet（スプレッドシートの ID） |
| シート | タブ | スプレッドシートの中の1枚（画面の下のタブ）（公式の表記） | sheet（シート名） |

今ある docs の呼び名との対応（触るときに直す）:

| 今ある docs の呼び名 | 新しい呼び名 |
|---|---|
| SNS ブック（docs/notes/sns-book.md） | SNS スプレッドシート |
| ABEMA ブック（docs/notes/abema-book.md） | ABEMA スプレッドシート |
| 3層のスプレッドシート・予定表のスプレッドシート | そのまま |
| 「辞書」タブ・「タイトル戦」タブなど「〜タブ」 | 「辞書」シート・「タイトル戦」シートなど「〜シート」 |
| 【1】元データ・【2】ID など | そのまま（シートの名前） |

- 今ある docs の「シート」は、スプレッドシートの1ファイルを指していることがある（docs/instruction-template.md の「シート・SNS ブックなどデータが変わった後」など）。新しく書く文では、ファイルは「スプレッドシート」、中の1枚は「シート」と書き分ける
