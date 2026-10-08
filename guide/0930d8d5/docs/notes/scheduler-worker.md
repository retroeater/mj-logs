# 予約実行を起動する Worker（mj-scheduler、#504）

GitHub Actions の予約実行（`schedule`）は予定より2時間半〜5時間遅れる（#491）。そこで Cloudflare の Worker の定時実行（Cron Triggers）から、
ワークフローを `workflow_dispatch` で時刻どおりに起動する。設計・決定・段階は #504 の本文、起動時刻・依存関係の確かめは #505（2026-10-06 に済。結果は #505 のコメント）、通知先は #506。

**2026-10-05 の夜に平野さんがつなぎ、動いている。** 最初の定時の起動は 10/6 04:20 JST（delete-merged-branches の run #15、04:20:36 に作られ予定から 36 秒の遅れ、success）。設定は下の「ダッシュボードの設定（申告値）」。

## 構成

| ファイル | 中身 |
|---|---|
| `workers/scheduler/wrangler.jsonc` | Worker の名前 `mj-scheduler`・cron `* * * * *`（毎分、#298 で `*/5` から変えた）・秘密でない設定（`vars`）。`workers_dev` と `preview_urls` は `false`（fetch の入口を持たないので公開の URL は要らない） |
| `workers/scheduler/schedule.json` | 起動の表（下の「起動の表の直し方」） |
| `workers/scheduler/src/index.mjs` | 定時実行の入口（`scheduled`）。Secret が無ければ `console.error` に書いて終わる |
| `workers/scheduler/src/scheduler.mjs` | 判定（この回に起動する行・当日の行の状態・通知の文面）と GitHub API の呼び出し。時刻と `fetch` を引数で受ける |
| `workers/scheduler/test/scheduler.test.mjs` | `node --test` のテスト。GitHub の API は呼ばない（偽の `fetch`） |

- npm の依存は無い（`package.json` も無い）。`wrangler` もリポジトリに入れない。ビルドとデプロイは Workers Builds が行う（`npx wrangler deploy`）
- `workers/` は `.assetsignore` で配信の対象から外している（サイトの Worker は `assets.directory` が `./`）。外さないとコードと表が公開される
- `vars`: `GITHUB_REPOSITORY`（`retroeater/mj`）・`DISPATCH_REF`（`cloudflare`）・`NOTIFY_ISSUE`（`506`）。Secret: `GITHUB_TOKEN`（下の「トークン」）
- セッションから `wrangler deploy` はしない（CLAUDE.md「禁止事項」）。`wrangler dev` でも試さない（セッションに `wrangler` は無い）

## 動き

1. **起動**: cron の回ごとに、定時実行の予定時刻（`controller.scheduledTime`）を JST に直す。窓は「前の回の時刻より後〜この回の時刻まで」（1分）。
   その窓に予定の時刻が入る有効な行を、`POST /repos/retroeater/mj/actions/workflows/<ファイル名>/dispatches`（ref `cloudflare`、inputs `{"scheduled": "true"}`）で起動する。値は文字列で送る（真偽の入力に文字列の `"true"` が通ることを手動実行で確かめてある。REST の文書は inputs の値の型を決めていない）。
   窓は重ならないので、同じ行を二重に起動しない。取りこぼした回の埋め合わせはしない（朝の確かめに「未起動」で出る）
2. **起動の失敗**: API が 2xx 以外を返したら（つながらなかったときも）、その場で #506 にコメントする
3. **朝の確かめ（#504 の「層1」）**: 06:00 JST の回に、当日の有効な行のうち予定が 06:00 より前のものごとに、当日（JST）に作られた実行の一覧を引く（`created>=<当日 0 時>`）。
   すべて success なら何もしない。それ以外は #506 に1件コメントする。実行の一覧は `event=workflow_dispatch` で絞る（sync-logs は push の実行が1日に100件を超えることがあり、絞らないと1ページ目に予約の起動が載らない）
4. **mj-logs の同期（#298）**: 毎回 `GET /repos/retroeater/mj` の `pushed_at` を読み、この回の予定時刻から3分以内なら、mj-logs の `sync-from-mj.yml` を `workflow_dispatch`（ref `main`、inputs なし）で起動する（`dispatchSync()`、行き先は `scheduler.mjs` の定数 `SYNC`）。
   1回の push で後の2〜3回が起動するが、mj-logs 側は concurrency で1本ずつ動き、写すものが無ければコミットしない。失敗は `console.error` に書くだけで #506 には書かない（毎分の回で通知が増えるため）。朝の確かめの対象にも入れない。
   起動の表（`schedule.json`）の行ではない。トークンは同じ `GITHUB_TOKEN`（対象に mj-logs を足してある）

## 予約の起動の見分け方

実行の一覧の API には入力が出ない。そこで、起動するワークフローに `run-name` を足し、`scheduled` が真のときだけ題の先頭に `[scheduled]` を付ける。
Worker は「`event` が `workflow_dispatch` で、`display_title` が `[scheduled]` で始まる実行」を予約の起動とみなす。

```yaml
run-name: ${{ inputs.scheduled && '[scheduled] <ワークフローの name>' || '' }}
```

- `scheduled` が偽・`schedule` の契機では空になり、GitHub の既定の題になる（公式の文書: run-name が空なら契機ごとの既定の題）
- `inputs.scheduled` は真偽のまま、`github.event.inputs.scheduled` は文字列になる（公式の文書の `inputs` コンテキストの注記）。シェルでは `env` に `${{ inputs.scheduled }}` を渡し `"true"` と比べる
- `display_title` は mj-logs の `actions/status.md` には書かれない（`scripts/actions_status.py` は題を書かない）
- Worker から起動するワークフローを足すときは、入力 `scheduled`・`run-name`・「`schedule` か `scheduled` が真」の判定の3つを足す。`workflow_dispatch` の入力の上限は 25 個（公式の文書）

## 起動の表の直し方

`workers/scheduler/schedule.json` の `rows` に1行1オブジェクトで書く。

| キー | 中身 |
|---|---|
| `workflow` | ワークフローのファイル名（例 `delete-merged-branches.yml`） |
| `time` | JST の時刻 `HH:MM`（毎分の回で、その分に起動する） |
| `weekdays` | 省略可。JST の曜日の配列（0=日曜〜6=土曜）。省略は毎日 |
| `monthdays` | 省略可。JST の日の配列（例 `[1]`）。省略は毎日 |
| `enabled` | `false` の行は起動もしないし、朝の確かめでも見ない |

今の表（2026-10-07、#504 の段階2の先の回まで）は2行: `sync-dojo-calendar.yml` 毎日 04:15・`delete-merged-branches.yml` 毎日 04:20（どれも有効）。`sync-logs.yml` 毎日 05:30 の行は 2026-10-07 に消した（#298。mj の sync-logs.yml を止め、写しは mj-logs の `sync-from-mj.yml`。1日1回の保険は mj-logs 側の予約）。時刻の案は #504 の本文「起動時刻の案と範囲」。

- 表を変えて `cloudflare` に入ると、Workers Builds の `mj-scheduler` がデプロイする（check-run「Workers Builds: mj-scheduler」）。cron の変更の反映は最大15分。`workers/scheduler/` の外だけを変える push ではビルドされない（2026-10-06 に check-run で確かめた）
- 表に足すワークフローは、先に上の「予約の起動の見分け方」の3つを足しておく。足さないと、`scheduled` が知らない入力として 422 になる

## テスト

```
node --test 'workers/scheduler/test/*.test.mjs'
```

Node 22 で、引数にディレクトリを渡すと失敗する。パターンを引用符で囲んで渡す。定時実行の入口（`index.mjs`）は `schedule.json` を取り込むため、Node のテストでは読まない。

## 通知の読み方

#506 の本文に書いた（未起動・起動待ち・失敗・実行中・無効・確認できず、手動で成功済み）。すべて success の日は何も書かれない。
**Worker やトークンが止まると、#506 にも何も書かれない。** 試験の間は mj-logs の `actions/status.md` で、その日の起動があったかも見る（#504 の「層2」は段階3）。

## ログ（Workers Logs）

`wrangler.jsonc` の `observability` を `{"enabled": true}` にしている（2026-10-06、#504）。`head_sampling_rate` は省略で、既定は 1（すべての起動を残す）。
見る場所はダッシュボードの Workers & Pages > `mj-scheduler` > Observability。Events の一覧に時刻（JST）・Level・Message が並ぶ。毎回の起動は Message が cron の式（2026-10-07 までは `*/5 * * * *`）・Level が info で、Worker の書いた行は Level が空・Message に本文が出る（2026-10-07 の平野さんの画面。申告値）。保存は3日、Free の上限は1日 20 万件（この Worker は1日 1,440 回の起動。Worker の書く行は起動した回だけなので、合わせても上限より十分小さい）。

Worker が書く行（`console.log`。何もしない回は書かない。トークンは書かない）:

- 起動した回: `起動: delete-merged-branches.yml HTTP 204`
- mj-logs の同期を起動した回: `同期: sync-from-mj.yml HTTP 204`（起動しない回は書かない）
- 06:00 の朝の確かめの回: `朝の確かめ: 2026-10-06 予定 1・success 1・それ以外 0・#506 に書かない`
- 失敗（`console.error`）: `起動に失敗: …`・`同期の判定に失敗: …`・`同期の起動に失敗: …`・`確かめに失敗: …`・`issue へのコメントに失敗: …`・`Secret GITHUB_TOKEN が無いため…`

## トークン

- fine-grained の PAT。対象は `retroeater/mj` と `retroeater/mj-logs`（mj-logs は 2026-10-07 に平野さんが足した。同期の起動のため。申告値）。権限は Actions: Read and write（起動と実行の一覧）・Issues: Read and write（通知）。Metadata: Read は自動で付く。期限は 2027-10-05（2026-10-05 の夜に発行。決定は 366 日だが、画面で選んだのは 365 日）。名前は `mj-scheduler`
- 置き場所は `mj-scheduler` の Secret `GITHUB_TOKEN` だけ。リポジトリ・ログ・チャットには書かない
- 期限が切れると、起動も通知も止まる（#506 にも書かれない）。期限の1か月前（2027-09-05）に【R#504】の予定がカレンダーに入っている
- 差し替え: GitHub で新しいトークンを同じ条件で発行 → Cloudflare の `mj-scheduler` の Settings > Variables and Secrets で `GITHUB_TOKEN` の値を差し替える → 古いトークンを GitHub で消す。発行は差し替えの直前にする（値は一度しか表示されない）

## ダッシュボードの設定（申告値）

**平野さんの画面（スクリーンショット）の申告値で、セッションからは検証できない。** 時刻は JST。

| 対象 | 値 |
|---|---|
| 作成（10/5） | Project name `mj-scheduler`、Build command 空、Deploy command `npx wrangler deploy`、Path `/workers/scheduler`、Enable Preview builds OFF、API token は既存の `mj build token`（「artifacts_read・artifacts_write が無い」の注意書きが出たが権限は変えていない） |
| 最初のビルド（10/5 22:51） | 成功、34 秒（Initializing 6 秒・Cloning 5 秒・Installing 0.1 秒・残りが Deploying）。「Manually deployed」、wrangler 4.147.0、Total Upload 10.07 KiB。ログに `Deployed mj-scheduler triggers`・`schedule: */5 * * * *` と vars の3つ |
| Settings > Build（10/6 11:20） | Build watch paths の Include は `workers/scheduler/**` の1つだけ。Exclude は `node_modules/**, .git/`。Build command None・Deploy command `npx wrangler deploy`・Root directory `/workers/scheduler`・ブランチ `cloudflare`。「Builds for Preview branches」は OFF。**10/5 23:00〜23:10 に Include の `*` を `workers/scheduler/*` に変えたつもりが保存されず、`*` が残っていた**（10/6 11:20 に直した） |
| Settings > Variables and Secrets（10/5 23:35） | Variable `DISPATCH_REF`・`GITHUB_REPOSITORY`・`NOTIFY_ISSUE`（`wrangler.jsonc` の vars と同じ値）。Secret `GITHUB_TOKEN` |
| GitHub のトークン（10/5 23:30 ごろ発行） | fine-grained、名前 `mj-scheduler`、Resource owner retroeater、Only select repositories で retroeater/mj、Actions・Issues が Read and write、Metadata が Read-only、期限 2027-10-05 |
| サイトの Worker `mj`（10/6 11:20） | Build watch paths の Exclude は `.git/`・`docs/**`・`node_modules/**`・`workers/**`（10/5 の深夜に足した `workers/*` を 10/6 11:20 に `workers/**` に置き換えた。詳しくは `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」） |
| Cron Triggers | つなぐ前は 0 本（アプリケーションは `mj` だけで、`mj` は静的アセットだけなので Triggers を持てない）。今は `mj-scheduler` の1本（Free は5本まで） |
| API トークン（10/6 00:00） | `mj build token (Workers Builds)` の1本のまま。**`mj-scheduler` の接続でトークンは増えなかった** |

## 作り直すときの手順

1. Workers & Pages >「Create application」→ リポジトリ `retroeater/mj` を選ぶ →「Set up your application」
   - Project name: **`mj-scheduler`**（`wrangler.jsonc` の `name` と同じ。違うとビルドが「The name in your Wrangler configuration file … must match the name of your Worker」で失敗する）
   - Build command は空、Deploy command は `npx wrangler deploy`。「Enable Preview builds」は OFF（`preview_urls` も `false`）
   - 「Advanced settings」の「Path」を `/workers/scheduler` にする（既定は `/`）。API token は既存の `mj build token` を選ぶ。ビルド用の変数の欄は空のまま
2. 作った Worker > Settings > Build > Build watch paths の Include を `workers/scheduler/**` の1つだけにする（既定の `*` を消し、保存されたことを画面で確かめる）
3. 上の「トークン」の条件で GitHub のトークンを発行し、Settings > Variables and Secrets に Secret **`GITHUB_TOKEN`** を足す。発行した日付をチャットに伝える（期限の予定を入れるため）
4. サイトの Worker `mj` の Exclude に `workers/**` があることを確かめる
5. 次の朝（04:20 JST）に、題が `[scheduled] …` の実行が success かを Actions の画面か mj-logs の `actions/status.md` で見る

## Build watch paths の確かめ（2026-10-06）

10/6 11:20 に直した後、cloudflare への push で次のとおりだった（push ごとの check-run は #504 の 2026-10-06 のコメント）。直す前は mj-scheduler の Include に `*` が残っていて、docs だけの push でも毎回ビルドされていた。

- `workers/scheduler/` の下（`src/`・`test/`・`wrangler.jsonc`）と docs を変える push: 「Workers Builds: mj-scheduler」success、「Workers Builds: mj」は付かない
- docs だけの push: どちらも付かない

## 未確認（2026-10-08 の時点）

- 作業ブランチへの `workers/` だけの push で、サイトの `mj` のプレビューがビルドされた（「Workers Builds: mj」success）。cloudflare への push では Exclude の `workers/**` が効いているのに、プレビューでは効いていない。理由は分からない（害はプレビューのビルドが1回増えることだけ）

## 動いた記録（2026-10-08 の時点）

- 06:00 の朝の確かめの回は動いている: 10/7 06:00:08 JST に「朝の確かめ: 2026-10-07 予定 1・success 1・それ以外 0・#506 に書かない」（Observability。平野さんの画面の申告値）
- 5分ごとだったときの回のログの時刻は予定から 8〜10 秒後。起動した実行が GitHub で作られた時刻は、10/6 が 04:20:36、10/7 が 04:20:10（API）
- 10/8 は毎分の起動（cron `* * * * *`、#298）になってから最初の朝。sync-dojo-calendar の run #33 が 04:15:21、delete-merged-branches の run #19 が 04:20:21 に作られた（どちらも予定から 21 秒、success。API）。道場部の同期の Worker からの起動はこの回が最初。06:00:21 JST に「朝の確かめ: 2026-10-08 予定 2・success 2・それ以外 0・#506 に書かない」。毎分の回は Message が `* * * * *` で、ログの時刻は毎分 20 秒ごろ（Observability。平野さんの画面の申告値）
- 作業ブランチで手動実行した sync-dojo-calendar が保存した前回の状態（Actions のキャッシュ）は、同じ作業ブランチの後の実行からは見えるが（10/7、run 32 が run 31 の分を復元した）、cloudflare の実行からは見えない（10/8 の run #33 は、作業ブランチの試験より前の cloudflare の run #30 の分を復元した）
