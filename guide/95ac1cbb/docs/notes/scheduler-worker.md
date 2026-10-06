# 予約実行を起動する Worker（mj-scheduler、#504）

GitHub Actions の予約実行（`schedule`）は予定より2時間半〜5時間遅れる（#491）。そこで Cloudflare の Worker の定時実行（Cron Triggers）から、
ワークフローを `workflow_dispatch` で時刻どおりに起動する。設計・決定・段階は #504 の本文、起動時刻・依存関係の確かめは #505、通知先は #506。

**段階1（2026-10-05 マージ）の時点では、Worker はまだ動いていない。** 平野さんが下の「マージの後に平野さんが行う作業」を済ませると動き出す。

## 構成

| ファイル | 中身 |
|---|---|
| `workers/scheduler/wrangler.jsonc` | Worker の名前 `mj-scheduler`・cron `*/5 * * * *`・秘密でない設定（`vars`）。`workers_dev` と `preview_urls` は `false`（fetch の入口を持たないので公開の URL は要らない） |
| `workers/scheduler/schedule.json` | 起動の表（下の「起動の表の直し方」） |
| `workers/scheduler/src/index.mjs` | 定時実行の入口（`scheduled`）。Secret が無ければ `console.error` に書いて終わる |
| `workers/scheduler/src/scheduler.mjs` | 判定（この回に起動する行・当日の行の状態・通知の文面）と GitHub API の呼び出し。時刻と `fetch` を引数で受ける |
| `workers/scheduler/test/scheduler.test.mjs` | `node --test` のテスト。GitHub の API は呼ばない（偽の `fetch`） |

- npm の依存は無い（`package.json` も無い）。`wrangler` もリポジトリに入れない。ビルドとデプロイは Workers Builds が行う（`npx wrangler deploy`）
- `workers/` は `.assetsignore` で配信の対象から外している（サイトの Worker は `assets.directory` が `./`）。外さないとコードと表が公開される
- `vars`: `GITHUB_REPOSITORY`（`retroeater/mj`）・`DISPATCH_REF`（`cloudflare`）・`NOTIFY_ISSUE`（`506`）。Secret: `GITHUB_TOKEN`（下の「トークン」）
- セッションから `wrangler deploy` はしない（CLAUDE.md「禁止事項」）。`wrangler dev` でも試さない（セッションに `wrangler` は無い）

## 動き

1. **起動**: cron の回ごとに、定時実行の予定時刻（`controller.scheduledTime`）を JST に直す。窓は「前の回の時刻より後〜この回の時刻まで」（5分）。
   その窓に予定の時刻が入る有効な行を、`POST /repos/retroeater/mj/actions/workflows/<ファイル名>/dispatches`（ref `cloudflare`、inputs `{"scheduled": "true"}`）で起動する。値は文字列で送る（真偽の入力に文字列の `"true"` が通ることを手動実行で確かめてある。REST の文書は inputs の値の型を決めていない）。
   予定の時刻が5分刻みでなければ、過ぎて最初の回に起動する。窓は重ならないので、同じ行を二重に起動しない。取りこぼした回の埋め合わせはしない（朝の確かめに「未起動」で出る）
2. **起動の失敗**: API が 2xx 以外を返したら（つながらなかったときも）、その場で #506 にコメントする
3. **朝の確かめ（#504 の「層1」）**: 06:00 JST の回に、当日の有効な行のうち予定が 06:00 より前のものごとに、当日（JST）に作られた実行の一覧を引く（`created>=<当日 0 時>`）。
   すべて success なら何もしない。それ以外は #506 に1件コメントする

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
| `time` | JST の時刻 `HH:MM`。5分刻みを推す |
| `weekdays` | 省略可。JST の曜日の配列（0=日曜〜6=土曜）。省略は毎日 |
| `monthdays` | 省略可。JST の日の配列（例 `[1]`）。省略は毎日 |
| `enabled` | `false` の行は起動もしないし、朝の確かめでも見ない |

段階1の表は `delete-merged-branches.yml`・毎日・04:20・有効 の1行だけ。時刻の案は #504 の本文「起動時刻の案と範囲」。

- 表を変えて `cloudflare` に入ると、Workers Builds の `mj-scheduler` がデプロイする（watch paths は `workers/scheduler/*`）。cron の変更の反映は最大15分
- 表に足すワークフローは、先に上の「予約の起動の見分け方」の3つを足しておく。足さないと、`scheduled` が知らない入力として 422 になる

## テスト

```
node --test 'workers/scheduler/test/*.test.mjs'
```

Node 22 で、引数にディレクトリを渡すと失敗する。パターンを引用符で囲んで渡す。定時実行の入口（`index.mjs`）は `schedule.json` を取り込むため、Node のテストでは読まない。

## 通知の読み方

#506 の本文に書いた（未起動・起動待ち・失敗・実行中・無効・確認できず、手動で成功済み）。すべて success の日は何も書かれない。
**Worker やトークンが止まると、#506 にも何も書かれない。** 試験の間は mj-logs の `actions/status.md` で、その日の起動があったかも見る（#504 の「層2」は段階3）。

## トークン

- fine-grained の PAT。対象は `retroeater/mj` だけ。権限は Actions: Read and write（起動と実行の一覧）・Issues: Read and write（通知）。Metadata: Read は自動で付く。期限は 366 日
- 置き場所は `mj-scheduler` の Secret `GITHUB_TOKEN` だけ。リポジトリ・ログ・チャットには書かない
- 期限が切れると、起動も通知も止まる（#506 にも書かれない）。期限の1か月前にカレンダーで知らせる（チャット側が予定を入れる）
- 差し替え: GitHub で新しいトークンを同じ条件で発行 → Cloudflare の `mj-scheduler` の Settings > Variables and Secrets で `GITHUB_TOKEN` の値を差し替える → 古いトークンを GitHub で消す。発行は差し替えの直前にする（値は一度しか表示されない）

## マージの後に平野さんが行う作業

#504 の本文「9. 平野さんの作業の一覧」のうちマージの後の分を、決まった名前で書き直したもの。

1. **Cron Triggers の数を確かめる**: Cloudflare のダッシュボード > Workers & Pages > 各 Worker > Settings > Triggers。Free はアカウントで5本まで（今は 0 本の見込み、未確認）
2. **Worker を作ってリポジトリをつなぐ**: Workers & Pages > Create > Import a repository で `retroeater/mj` を選ぶ
   - Worker の名前: **`mj-scheduler`**（`wrangler.jsonc` の `name` と同じ。違うとビルドが「The name in your Wrangler configuration file … must match the name of your Worker」で失敗する）
   - Production branch: `cloudflare`。Root directory: `workers/scheduler`。Deploy command は既定（`npx wrangler deploy`）のまま
   - Builds for non-production branches: **OFF を推す**（作業ブランチごとのプレビューは要らない。`preview_urls` も `false`）
3. **Build watch paths**: `mj-scheduler` > Settings > Build > Build watch paths の Include を `workers/scheduler/*` にする
4. **トークンを発行して Secret に登録する**: 上の「トークン」の条件で発行し、`mj-scheduler` > Settings > Variables and Secrets に Secret **`GITHUB_TOKEN`** を足す。発行した日付をチャットに伝える（期限の予定を入れるため）
5. **サイトの Worker の watch paths**: 既存の `mj` > Settings > Build > Build watch paths の Exclude に `workers/*` を足す（今の Exclude は `node_modules/**, .git/, docs/**`）。足すと scheduler の変更でサイトのビルドが走らない
6. **API トークンの確かめ**: My Profile > API Tokens で、接続で増えたトークンを見る。docs/notes/cloudflare.md「APIトークンの棚卸し」に足すよう Claude Code に頼む
7. 次の朝（04:20 JST）に起動されたかを、Actions の画面か mj-logs の `actions/status.md` で見る（題が `[scheduled] …` の実行）。06:00 に #506 にコメントが無く、04:20 台の実行が success なら通っている

## 未確認（段階1のマージの時点）

- 定時実行の入口を通した動き（Worker が動き出した最初の回で確かめる）。Cloudflare の Cron Triggers 自体の遅れ
- Workers Builds を2つ目につないだときの check-run の名前（`Workers Builds: mj-scheduler` の見込み）と、トークンの増え方
