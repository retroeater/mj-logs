# データ・コンシェルジュの Worker（mj-ask、#542）

houou/ のトップで鳳凰戦のデータに言葉で質問できる欄。決定は `docs/decisions/houou.md`「2026-10-11（CHAT-1011-AIC-01）」。
実装は3つに分ける（grill Q13）: (1) データと検索〈済〉、(2) Worker と質問欄〈済、未デプロイ〉、(3) 週次の cron。
Worker のデプロイは平野さんの手作業 B1（下の「平野さんの手作業」）の後に始まる。それまでは `ryoei.pro/api/*` にサイトの Worker `mj` の 404 が返り、
質問欄は「いまは答えられません。」と出す。

## 構成

| ファイル | 役割 |
|---|---|
| `scripts/generate_ask_data.py` | 検索データ `workers/ask/data.json` を書く（`regenerate.py` の対象名は `ask_data`。ページは書かない） |
| `workers/ask/data.json` | 生成物。Worker に同梱する。`workers/` は `.assetsignore` にあり、サイト `mj` では配信しない（grill Q5） |
| `workers/ask/src/query.mjs` | 6つの型の検索（`runQuery(data, request)`）。数える・並べる・時点と出典と注記を付けるのはここ |
| `workers/ask/test/query.test.mjs` | `node --test 'workers/ask/test/*.test.mjs'`（Claude は呼ばない） |
| `scripts/tests/test_ask_data.py` | 生成のうちシートを読まない部分（`build()`・`finals_data()`）のテスト |
| `workers/ask/wrangler.jsonc` | Worker `mj-ask` の設定（ルート `ryoei.pro/api/*`、D1 `mj-ask`、vars） |
| `workers/ask/src/index.mjs` | 入口。POST `/api/ask`・GET `/api/ask/status`、ほかの `/api/*` は 404 |
| `workers/ask/src/ask.mjs` | 受付の本体（入力の検査・回数・Claude の2回の呼び出し・記録） |
| `workers/ask/schema.sql` | D1 の表（B2 で D1 の Console に貼る） |
| `workers/ask/test/ask.test.mjs` | 受付のテスト。D1 は `node:sqlite` で `schema.sql` をそのまま使う作り物、Anthropic は偽の fetch |
| `assets/houou_ask.js`・`assets/houou_ask.css` | houou/ トップの質問欄の送信と表示・見た目（`assets/houou.js`・`style.css` は変えていない）。HTML は `generate_houou_pages.py` の `ask_box()` |

## データ（data.json）

`generate_ask_data.py` が次を読んで1つにまとめる。週次の `regenerate.py all` で作り直す（push での作り直しは、この生成スクリプトか `scripts/lib/` が変わったとき）。

- 鳳凰戦リーグの成績: 「鳳凰」タブ（`generate_houou_pages.py` の `load_rows()`・`current_names()` をそのまま使う。改名は「別名」で現在名に名寄せ、HOU-13）。
  退会者を含む全員。`players` の1行は `[期, 前後(1/2), リーグ, 順位, 結果, 合計]`（`houou/results/<選手名>.json` の rows の先頭6列と同じ）
- ランキング9部門: `generate_houou_pages.py` の `build_rankings()`（houou/ranking/ と同じ表示対象・在籍者だけ）
- 決勝（鳳凰位）: `title/search.json`（`generate_title_pages.py` の生成物）の鳳凰戦の期。**鳳凰位を獲った期はここを正にする。**
  「鳳凰」タブの「鳳凰位」の行は鳳凰位として座っている期で、獲った期の次の期（例: 吉田直は第35期に獲り、第36期の行が「鳳凰位」）
- 放送対局: `generate_live_pages.py` の読み込み（`load_titles()`・`load_videos()`・`build_pages()`）で、タイトル戦が鳳凰戦のページ
- 読み・入会期: 名簿と「プロ」。**在籍者だけ**（grill Q3）。データに出てくる人の分だけ持つ
- 名前の辞書は「プロ」「連盟プロ以外」「別名」から作り、SNS ブック（X の画像）は読まない（`generate_houou_race.py` の `load_name_book()` から X を除いた形）
- 時点 `asof`: リーグは成績の最後の期（「第43期前期終了時点」）、決勝は最後の期（「第42期まで」）、放送対局は最後の配信日

`title/search.json` は `regenerate.py all` の中で `ask_data` より後に作られる（名前の順）。決勝の反映が1回遅れることがあるが、`title/` は毎日作り直されるので、次の週次で追いつく。

大きさは 628KB（2026-10-11、gzip で 144KB）。Workers の上限は Free・Paid とも圧縮前 64 MiB（公式の Limits「Worker size」、2026-10-11 に確認）。

## 検索（query.mjs）

`runQuery(data, { type, args, assumedTerm })` が `{ type, found, count, items, sources, asof, notes, lead }` を返す。
答えられないときは `{ type, found: false, error, message, candidates }`（`error` は `unknown_type`・`bad_args`・`not_found`・`ambiguous_name`）。
出典の URL はルート相対（同じドメイン）。

| 型 | 引数 | items | 出典 |
|---|---|---|---|
| `champions`（鳳凰位の一覧） | `entry`（入会期の数）・`from`・`to`（鳳凰戦の期）・`name` | `{name, entry, terms}`（人ごと、最初に獲った期の順） | `/title/houou/` |
| `final`（決勝の結果） | `term`（必須） | `{rank, name}` | `/title/houou/<期>.html` |
| `player`（選手の成績） | `name`（必須）・`term` | `{period, league, place, result, total}` | 在籍者は `/houou/players/?name=`、それ以外は最後の期の `/houou/race/?term=&league=` |
| `league`（リーグの顔ぶれ） | `term`・`league`（必須）・`half`（前/後、1/2） | `{place, name, result, total}`（順位の順） | `/houou/race/?term=<期>-<1|2>&league=<リーグ>` |
| `ranking`（記録の上位） | `division`（9部門の名前、必須）・`limit` | `{rank, name, value}` | その部門の `/houou/ranking/…/` |
| `live`（放送対局） | `term` | `{period, stage, date, players, url}` | 各ページ（無ければ `/live/houou/`） |

- 2つの「期」（grill Q2）: 鳳凰戦の期は `term`・`from`・`to`、入会期は `entry`。`assumedTerm: true`（どちらとも取れる「N期」を鳳凰戦の期と読んだ）なら `lead`（答えの冒頭に出す読み方）に「「N期」を鳳凰戦の第N期として答えます」
- 注記: 入会期を使ったら「入会期が分かるのは在籍中の選手だけです」（grill Q3）、「鳳凰位」の行が出たら座った期の説明、規定のある部門は「※出場10期以上のみ」など
- 名前: 完全一致 → 「別名」 → 正規化して一致（NFKC・空白なし・小文字・カタカナはひらがな、読みも見る） → 部分一致。複数なら `ambiguous_name` と候補（5名まで）

## 仮置き（実装2・週次のまとめを見て決め直す）

- 型の名前（英字）・引数の名前・返す形
- 注記の文面（「入会期が分かる…」以外）。座った期の説明「成績の「鳳凰位」は、前の期に鳳凰位を獲って座っている期です」、前期・後期を補った「第N期前期として答えます」
- 「記録の上位」の既定の件数 10・上限 30
- 名前の候補の上限 5
- `league` で前期・後期の指定が無いときは、データのある新しいほう（後期→前期）で答える
- 退会者の「選手の成績」の出典は最後の期の順位変動（個人成績のページは在籍者だけのため）

## Worker（mj-ask）

`ryoei.pro/api/*` のルートで動く別の Worker（grill Q4）。サイトの Worker `mj`（静的アセット）は変えない。ルートは同じホスト名の Custom Domain より優先される
（公式の Routes のページ）。`/api/` は 2026-10-11 の時点で使われておらず（`/api/ask`・`/api/` とも 404、`_redirects`・`_headers` に行なし）、
Rate limiting rules の条件（`.html` か `/` で終わるパス）にも当たらない。

POST `/api/ask`（本文 `{"question": "…"}`）の流れ:

1. `Origin` が `ALLOWED_ORIGIN`（`https://ryoei.pro`）でなければ 403。Secret・D1・上限の設定が無ければ 503（`unavailable`）
2. 空・200字（`QUESTION_MAX`、コードポイントで数える）を超える問いは 400（`invalid`）。数えない
3. 回数（grill Q7）: JST の暦日を鍵に、全体（`daily_counts`）と IP（`ip_counts`。IP は「Secret の塩・日付・IP」の SHA-256 だけ）を読み、上限なら 429（`limit`、`scope` は `daily` か `ip`、`resetAt` は翌0時 JST）。
   受け付けるときに両方を `INSERT … ON CONFLICT DO UPDATE … RETURNING n` で1つ足し、前日以前の `ip_counts` を消す（1回の batch）。同時に来て上限を越えたら 429
4. Claude（`claude-haiku-5-5`）の1回目: structured outputs（`output_config.format` の JSON schema `PLAN_SCHEMA`）で `{type, assumedTerm, args}` を返させる。型は query.mjs の6つと `none`
5. `none`、または `runQuery` が見つからない・名前が決まらない・引数が不正なら「答えられません。」（候補があれば添える）。2回目は呼ばない
6. 2回目: 質問と検索の結果（型・件数・items・注記）だけを渡して文章にさせる。答えの下の注記・時点・出典は Worker が `runQuery` の結果から付け（grill Q6）、Claude には書かせない
7. 記録（決定5・grill Q8）: `questions` に日時（JST）・質問文・結果（`answered` / `unanswered`）・型。IP と答えの本文は書かない
8. Claude の失敗（HTTP エラー・ワークスペースの上限の 400・拒否 `refusal`・打ち切り）は 503（`unavailable`）。この問いは数えたまま、記録は `unanswered`

Claude の呼び出しの設定（公式の文書と claude-api skill で確かめた。2026-10-11）:

- SDK は使わず `fetch` で `https://api.anthropic.com/v1/messages` を呼ぶ（`x-api-key`・`anthropic-version: 2023-06-01`）。`workers/` に npm の依存とビルドの工程を持たないため（`mj-scheduler` と同じ）
- Haiku 5.5 は adaptive thinking が既定で有効で、effort の既定は `medium`。考える量は `output_config.effort: "low"`（簡単な問いは考えを省く）で抑え、`thinking` は送らない。`temperature` などは送らない（既定以外は 400）
- thinking も `max_tokens` に数えるので、2回とも `max_tokens` は 2048。本文は `content` の `text` ブロックだけを読む（先頭に `thinking` ブロックが来る）
- `stop_reason` が `end_turn` でなければ失敗として扱う。Haiku 5.5 の拒否にはサーバー側の代わりのモデルは無い
- 費用の実測用に、1回ごとの `input_tokens`・`output_tokens` だけを Workers Logs に書く（質問文は書かない、grill Q12）

GET `/api/ask/status`: `{"status": "open", "remaining": n, "max": 200}` か `{"status": "limit", "scope": …, "resetAt": …}`。質問欄は開いた時点で上限なら受付を止める。

質問欄の状態ごとの表示（`assets/houou_ask.js`）: 答え（読み方 → 本文 → 注記 → 「データ: …」 → 「出典: …」のリンク）、「答えられません。」、上限（全体「本日の受付は上限に達しました。翌0時（日本時間）から受け付けます。」・IP「本日ご質問いただける回数の上限に達しました。…」）、
それ以外（Worker が無い・失敗）は「いまは答えられません。」。答えは `textContent` で入れ、出典はルート相対の URL だけをリンクにする。

vars（`wrangler.jsonc`。Wrangler のデプロイで上書きされるので、変えるときはこのファイルを直す）: `ALLOWED_ORIGIN`、`DAILY_LIMIT`（試行中は 50。公開の指示で 200 に戻す、grill Q12）、`IP_DAILY_LIMIT`（10）。
Secret（ダッシュボードで登録。デプロイでは消えない）: `ANTHROPIC_API_KEY`・`IP_HASH_SALT`。

## 平野さんの手作業（B1〜B4）

A1（Console のワークスペース `ryoei-pro-ask`、月の上限 $30）と A2（D1 `mj-ask` の作成、Database ID は `wrangler.jsonc`）は 2026-10-11 に済み。
画面の名前は公式の文書（developers.cloudflare.com の Workers Builds・D1・Secrets、platform.claude.com の API keys。2026-10-10〜11 に取得）と `docs/notes/scheduler-worker.md` の `mj-scheduler` の作成の記録から。
B1 の最初のビルドで `ryoei.pro/api/*` に Worker が付くが、B2〜B4 が済むまでは質問欄は「いまは答えられません。」を出すだけで、Claude は呼ばれない。

### B1: Workers Builds のプロジェクト `mj-ask` を作る

1. Workers & Pages → **Create application** → **Import a repository** の **Get started** → Git のアカウントを選び、リポジトリ `retroeater/mj` を選ぶ
2. 設定（`mj-scheduler` と同じ形）:
   - Project name: **`mj-ask`**（`wrangler.jsonc` の `name` と同じ。違うとビルドが失敗する）
   - Build command: 空
   - Deploy command: `npx wrangler deploy`（既定のまま）
   - Root directory（`mj-scheduler` の作成では「Advanced settings」の「Path」）: `/workers/ask`
   - API token: 既存の `mj build token` を選ぶ
   - プレビュー（Enable Preview Builds）が出ていれば OFF（`wrangler.jsonc` の `preview_urls` も `false`）
3. **Save and Deploy**。最初のビルドが走る
4. 作った Worker → Settings → Build → **Build watch paths** の Include を `workers/ask/**` の1つだけにする（既定の `*` を消し、保存されたことを画面で確かめる）。
   サイトの Worker `mj` の Exclude に `workers/**` があることも確かめる（2026-10-06 の申告では入っている）
5. ビルドの結果（Deployments → View build history）を見る。成功なら、ログに `ryoei.pro/api/*` のルートと D1 のバインディング `DB` が出る

**ビルドが API トークンの権限で失敗したとき**（ログに認証のエラー〈例: code 10000〉や D1・ルートの権限の不足が出たとき）: Workers Builds が作るトークンの既定の権限は Workers Scripts・KV・R2・Workers Routes などで、**D1 は入っていない**（公式の Builds の Configuration「API token」）。
既にある D1 を `database_id` で結ぶだけのデプロイで D1 の権限が要るかは、公式の文書に書かれていない（未確認）。足りなかったときは:

1. My Profile → **API Tokens** → `mj build token` の **Edit token**（公式の「Create API token」のページ。作った後も権限を変えられる）
2. Account の権限に **D1 : Edit** を足す（Zone の **Workers Routes : Edit** が ryoei.pro に効いていることも確かめる）→ 保存
3. `mj-ask` の Build history から同じコミットをもう一度ビルドする（画面に再実行のボタンが無ければ、次の `workers/ask/` の変更の push で走る）。
   トークンを作り直したときは、Settings → Build の API token で新しいトークンを選び直す（古いトークンが一覧に残ることがある、公式の Troubleshooting「Stale API token」）

### B2: D1 に表を作る

1. Cloudflare のダッシュボード → **D1 SQL database** → `mj-ask` → **Console**
2. `workers/ask/schema.sql` の中身をすべて貼り、**Execute**（複数の文を1回で実行できる。何度実行してもよい）
3. **Tables** に `daily_counts`・`ip_counts`・`questions` が出ることを確かめる

### B3: Anthropic のキーを作る

1. Claude Console → Settings → **API keys** → **Create key**
2. 名前 `mj-ask`、ワークスペース **`ryoei-pro-ask`**（この機能専用。月の上限 $30 が効く。Default のワークスペースのキー `mj-dojo-guest` は使い回さない）、Linked account は本人、期限は1年（grill Q10）
3. 表示された値をそのまま B4 で貼る（値は一度しか表示されない。チャット・ログ・リポジトリには書かない）。期限の前（1年後の1〜2週間前）にカレンダーに予定を入れる

### B4: Worker の Secret を登録する

1. Workers & Pages → `mj-ask` → Settings → **Variables and Secrets** → **Add**
2. Type **Secret**、Variable name `ANTHROPIC_API_KEY`、Value に B3 の値
3. もう1つ **Add**: Type **Secret**、Variable name `IP_HASH_SALT`、Value に無作為の長い文字列（例: パスワード管理のツールで作った32文字以上。どこにも控えなくてよい）
4. **Deploy**。Secret は以後の Workers Builds のデプロイでも消えない（公式の Wrangler の Configuration「Source of truth」）

B1〜B4 の後、次の指示で本番の確かめ（`/api/ask/status` の応答、未公開の houou/ からの質問、費用の実測の始め）と実装3（週次の cron）を行う。

## キーの差し替え（期限の前・漏れたとき）

1. Claude Console → Settings → API keys → Create key で、同じ条件（ワークスペース `ryoei-pro-ask`・本人・1年）の新しいキーを作る
2. `mj-ask` → Settings → Variables and Secrets → `ANTHROPIC_API_KEY` の値を新しいキーに替えて **Deploy**
3. houou/ のトップから1問送って答えが返ることを確かめる
4. Console の API keys で古いキーを消す（漏れたときは 2 の前に消す。その間は質問欄が「いまは答えられません。」になる）
5. 次の期限の予定をカレンダーに入れ直す

`IP_HASH_SALT` を替えると、その日の IP ごとの数がいったん0に戻る（全体の数は戻らない）。

## 仮置き（実装2、本番の試しと週次のまとめを見て決め直す）

- 上限・使えないときの表示の文面（上の「質問欄の状態ごとの表示」）、ボタンの「質問する」、読み上げ用の見出し「鳳凰戦について AI に質問する」（画面には出さない）
- 「答えられません。」のときの候補の出し方（「候補: A、B」）
- Claude の失敗した問いも1問と数え、記録は `unanswered`（決定5の「答えた／答えられなかった」の2つに合わせた）
- effort `low`・`max_tokens` 2048・待ちの上限 25秒
- 2回目に渡す結果は items をすべて（ランキングは最大30件、リーグは1組40名前後）

