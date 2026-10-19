# データ・コンシェルジュの Worker（mj-ask、#542）

houou/ のトップで鳳凰戦のデータに言葉で質問できる欄。決定は `docs/decisions/houou.md`「2026-10-11（CHAT-1011-AIC-01）」。
実装は3つに分ける（grill Q13）: (1) データと検索〈済〉、(2) Worker と質問欄〈済、2026-10-11 から本番で動作〉、(3) 週次の cron〈済。ブックへの書き出しの最初の本番の実行は 2026-10-19（月）9:00 JST の見込み〉。
1日の上限は全体 200・IP ごと 10（2026-10-16。試行中の全体は 50 だった）。公開は #540。

## 構成

| ファイル | 役割 |
|---|---|
| `scripts/generate_ask_data.py` | 検索データ `workers/ask/data.json` を書く（`regenerate.py` の対象名は `ask_data`。ページは書かない） |
| `workers/ask/data.json` | 生成物。Worker に同梱する。`workers/` は `.assetsignore` にあり、サイト `mj` では配信しない（grill Q5） |
| `workers/ask/src/query.mjs` | 6つの型（鳳凰戦は `entry` を足した7つ）の検索（`runQuery(data, request)`）。数える・並べる・時点と出典と注記を付けるのはここ |
| `workers/ask/test/query.test.mjs` | `node --test 'workers/ask/test/*.test.mjs'`（Claude は呼ばない） |
| `scripts/tests/test_ask_data.py` | 生成のうちシートを読まない部分（`build()`・`finals_data()`）のテスト |
| `workers/ask/wrangler.jsonc` | Worker `mj-ask` の設定（ルート `ryoei.pro/api/*`、D1 `mj-ask`、vars） |
| `workers/ask/src/index.mjs` | 入口。POST `/api/ask`・GET `/api/ask/status`、ほかの `/api/*` は 404 |
| `workers/ask/src/ask.mjs` | 受付の本体（入力の検査・回数・Claude の2回の呼び出し・記録） |
| `workers/ask/schema.sql` | D1 の表（B2 で D1 の Console に貼る） |
| `workers/ask/src/weekly.mjs` | 週次の cron（ブックへの書き出し・90日の削除、下の「週次の cron」） |
| `workers/ask/test/ask.test.mjs`・`weekly.test.mjs` | 受付と週次のテスト。D1 は `test/fake-d1.mjs`（`node:sqlite` で `schema.sql` をそのまま使う作り物）、Anthropic・Google は偽の fetch |
| `assets/houou_ask.js`・`assets/houou_ask.css` | houou/ トップの質問欄の送信と表示・見た目。HTML は `generate_houou_pages.py` の `ask_box()`。答えに出た選手のカードのリーグのチップの色だけ `style.css` の `--lg-*` を使う |
| `houou/ask-players.json` | 答えに出た選手のカードの名簿（生成物。`generate_houou_pages.py` の `ask_roster()`、約66KB。答えが出たときに初めて読む） |
| `scripts/tests/test_houou_ask.py` | 突き合わせ（`pickPlayers`・`splitPlayers` を node で動かす）・名簿の値・書き出した名簿と名前の面の一致 |

## 女流桜花（ouka/、#519。未公開）

- `scripts/generate_ask_data_ouka.py`（`ask_data_ouka`）が `generate_ask_data.py` を設定 `OUKA` で動かし、`workers/ask/data-ouka.json` を書く（並びは data.json と同じ。決勝は title/search.json の「女流桜花」、放送対局は /live の「女流桜花」。大会の名前・リーグ・部門を `contest` に足す）。data.json には `contest` が無く、Worker は鳳凰戦の既定（`query.mjs` の `HOUOU`）で読む
- ouka/ の質問欄（`ask_box()` が `data-contest="ouka"`）は問いに `contest: "ouka"` を添える。`ask.mjs` は `deps.contests.ouka` のデータと女流桜花の指示（`OUKA_PLAN_SYSTEM`、出力の形のリーグ・部門も女流桜花）で答える。知らない `contest` は 400（数えない）。`contest` の無い問い（houou/）の答え方は変えない
- 1日の上限・IP ごとの上限は鳳凰戦と共有（2026-10-17）。記録の型は「ouka:league」の形（答えられなかった問いは「ouka:」。仮置き）
- テストは `workers/ask/test/ouka.test.mjs`。違いの全体は docs/notes/ouka-top.md

## WRC リーグ（jwrc/、#594。未公開）

- 女流桜花と同じ形: `scripts/generate_ask_data_jwrc.py`（`ask_data_jwrc`）が `workers/ask/data-jwrc.json` を書き、jwrc/ の質問欄は `contest: "jwrc"` を添える。`ask.mjs` は `deps.contests.jwrc` と `JWRC_PLAN_SYSTEM`（リーグは「予選」だけ、部門は `JWRC_DIVISIONS`）で答える。記録の型は「jwrc:league」の形
- 決勝は title/search.json の「WRCリーグ」、放送対局は /live の「WRCリーグ」。テストは `workers/ask/test/jwrc.test.mjs`。違いの全体は docs/notes/jwrc-top.md

## WRC-R リーグ（wrcr/、#366。公開は #611）

- WRC リーグと同じ形: `scripts/generate_ask_data_wrcr.py`（`ask_data_wrcr`）が `workers/ask/data-wrcr.json` を書き、wrcr/ の質問欄は `contest: "wrcr"` を添える。`ask.mjs` は `deps.contests.wrcr` と `WRCR_PLAN_SYSTEM`（部門は `JWRC_DIVISIONS`）で答える。記録の型は「wrcr:league」の形
- 決勝は title/search.json の「WRC-Rリーグ」、放送対局は /live の「WRC-Rリーグ」。テストは `workers/ask/test/wrcr.test.mjs`。違いの全体は docs/notes/wrcr-top.md

## データ（data.json）

`generate_ask_data.py` が次を読んで1つにまとめる。週次の `regenerate.py all` で作り直す（push での作り直しは、この生成スクリプトか `scripts/lib/` が変わったとき）。

- 鳳凰戦リーグの成績: 「鳳凰」タブ（`generate_houou_pages.py` の `load_rows()`・`current_names()` をそのまま使う。改名は「別名」で現在名に名寄せ、HOU-13）。
  退会者を含む全員。`players` の1行は `[期, 期の番号(前期 1・後期 2・通期 0、#541), リーグ, 順位, 結果, 合計(, 入替戦に出た印 1)]`（最後の 1 は「入替戦」列が Y で「結果」が入替戦の後の結果の行だけ、2026-10-18）（`houou/results/<選手名>.json` の rows の先頭6列と同じ）
- ランキング9部門: `generate_houou_pages.py` の `build_rankings()`（houou/ranking/ と同じ表示対象・在籍者だけ）
- 決勝（鳳凰位）: `title/search.json`（`generate_title_pages.py` の生成物）の鳳凰戦の期。**鳳凰位を獲った期はここを正にする。**
  「鳳凰」タブの「鳳凰位」の行は鳳凰位として座っている期で、獲った期の次の期（例: 吉田直は第35期に獲り、第36期の行が「鳳凰位」）
- 放送対局: `generate_live_pages.py` の読み込み（`load_titles()`・`load_videos()`・`build_pages()`）で、タイトル戦が鳳凰戦のページ
- 読み・入会期: 名簿と「プロ」。**在籍者だけ**（grill Q3）。データに出てくる人の分だけ持つ
- 名前の辞書は「プロ」「連盟プロ以外」「別名」から作り、SNS ブック（X の画像）は読まない（`lib/houou_race.py` の `load_name_book()` から X を除いた形）
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
| `player`（選手の成績） | `name`（必須）・`term` | `{period, league, place, result, total}`（入替戦に出た期は `playoff: true`。答えの指示で「result はその入替戦の後の結果」と伝える） | 在籍者は `/houou/players/?name=`、それ以外は最後の期の `/houou/race/?term=&league=` |
| `league`（リーグの顔ぶれ・人数・順位） | `term`・`league`（必須）・`half`（前/後、1/2）・`rank`（鳳凰戦だけ。その順位の選手だけ、2026-10-19） | `{place, name, result, total}`（順位の順） | `/houou/race/?term=<期>-<1|2>&league=<リーグ>`（通期の A1・A2・鳳凰位は `?term=<期>`、#541） |
| `ranking`（記録の上位） | `division`（9部門の名前、必須。houou/ と同じ略称〈通算・期平均…〉で、旧い名前〈通算得点・通算得点/期…〉も `DIVISION_ALIASES` で受ける、2026-10-16）・`limit`・`rank`（鳳凰戦だけ。「N番目」の位置の順位の選手〈同率は全員〉、2026-10-19） | `{rank, name, value}` | その部門の `/houou/ranking/…/`（出典の名前は「鳳凰戦 ランキング（通算）」の形） |
| `live`（放送対局の一覧・回数） | `term` | `{period, stage, date, players, url}`（/live に載っている対局だけ。鳳凰戦は決定戦のページだけなので、回数は決定戦の数） | 各ページ（無ければ `/live/houou/`） |
| `entry`（入会期の在籍選手の一覧、鳳凰戦だけ、2026-10-19） | `entry`（必須） | `{name, league, period}`（最後に出た期のリーグ。上のリーグから、同じリーグは読みの順） | `/houou/players/` |

- 2つの「期」（grill Q2）: 鳳凰戦の期は `term`・`from`・`to`、入会期は `entry`。`assumedTerm: true`（どちらとも取れる「N期」を鳳凰戦の期と読んだ）なら `lead`（答えの冒頭に出す読み方）に「「N期」を鳳凰戦の第N期として答えます」
- 注記: 入会期を使ったら「入会期が分かるのは在籍中の選手だけです」（grill Q3）、「鳳凰位」の行が出たら座った期の説明、規定のある部門は「※出場10期以上のみ」など
- 名前: 完全一致 → 「別名」 → 正規化して一致（NFKC・空白なし・小文字・カタカナはひらがな、読みも見る） → 部分一致。複数なら `ambiguous_name` と候補（5名まで）。
  鳳凰戦は、候補を `nameCandidates()`（頭から一致 → 途中に含む、それぞれ在籍者が先、読みの順）で全員出して答える（下の「Worker」の5、2026-10-19）
- 鳳凰戦だけの型・引数（`entry`・`rank`）は `HOUOU.types` と `planSchema()` の `HOUOU_ARGS` に置き、ほかの大会の検索条件の形は変えない（`typesOf()`）
- 第43期の A1・A2・鳳凰位（通期のリーグ）は、期が終わるまで行が無く「答えられません」になる（2026-10-19 の時点。#586）

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
4. Claude（`claude-haiku-5-5`）の1回目: structured outputs（`output_config.format` の JSON schema `PLAN_SCHEMA`）で `{type, assumedTerm, args}` を返させる。型は query.mjs の6つ（鳳凰戦は `entry` を足した7つ）と `none`。鳳凰戦の指示の最後に、「現在」「今」をデータのある最新の期（`latestPeriod()`）で読ませる一文（`currentLine()`）を足す（2026-10-19）。WRC リーグの指示の最後には、「現タイトルホルダー」「今のチャンピオン」などを最新の決勝の期の `champions`（from・to にその期）で読ませる一文（`holderLine()`）を足す（2026-10-19。`PLAN_LINES`）
5. `none`、または `runQuery` が見つからない・名前が決まらない・引数が不正なら「答えられません。」（候補があれば添える）。2回目は呼ばない。
   鳳凰戦で名前が決まらない（名字だけ・名前の一部）ときは、プログラムが「「堀」に当たる選手はN人います: …（10人まで、ほか N人）。名前を足して、もう一度聞いてください。」と書き、`answered` で返す（記録の結果は `candidates`、2026-10-19）
6. 2回目: 質問と検索の結果（型・件数・items・注記）だけを渡して文章にさせる。答えの下の注記・時点・出典は Worker が `runQuery` の結果から付け（grill Q6）、Claude には書かせない。
   選手の並ぶ結果（`champions`・`final`・`league`・`ranking`）が10人（`NAMES_MAX`）を超えたら、上位10人だけを渡し、残りの人数を `others`（`count` − 10）として添える（`answerInput()`。Claude に人数を数えさせない、2026-10-16）。歴代の鳳凰位（`champions`）は検索では最初に獲った期の古い順に並ぶので、10人を超えて切るときだけ、渡す前に新しい期（最後に獲った期）から並べ替える（10人以下は古い順のまま。期で絞った問いは絞った後の人数で決める。2026-10-16 の HOU-57。切ると最新の鳳凰位が渡らず「現在の鳳凰位は？」に答えられなくなったため、HOU-53 で一度戻した）。ほかの型は検索の順のまま切る。指示（`ANSWER_SYSTEM`）は「items の名前を結果の順のまますべて書く。「ほか N人」と全体の人数（count）は、一覧や人数を聞かれたときだけ添える。人数は自分で数えず、この指示の言葉は答えに書かない」（人数を添える条件は 2026-10-16 の HOU-59。「現在の鳳凰位は？」に聞かれていない人数が付いたため）。指示の最後には大会ごとに、`champions` の読み方の一文（`ANSWER_LINES`。terms は優勝した期・回数は terms の個数・entry は入会期で優勝の期ではない）を足す。WRC リーグで「通算19期の獲得」と答えたため足し（2026-10-19）、質問欄をサイト全体で1つにする予定に合わせて鳳凰戦・女流桜花・WRC-R リーグにも同じ型で置く（鳳凰戦は決定戦が期ごとに1回で前期・後期が無いことも書く。文面は `jwrc.test.mjs`・`wrcr.test.mjs` で確かめる）（答えの下の選手のカードは文の名前から作るため。文面と切り方・並べ方は `ask.test.mjs` で確かめる）
   同率（#618、2026-10-19）: `ranking` の先頭の順位（1位か、問われた順位 `rank`）に2人以上が並ぶときだけ、検索の結果に `tie`（`rank`・`value`・`count`・`names`〈先頭の `TIE_NAMES` = 3人、仮置き〉）を足し、`answerInput()` がそのまま渡す。
   `limit` で1人に絞った問いでも全員を数える。同率は表示と同じ順位（丸めた値）で決まり、表は下限で外した後なので外れた人は数えない。指示（4大会共通の `answerSystem`）は「その順位を1人だけのように書かず、『4回が最多で、9人が並んでいます(大和さんほか)』の形で値・人数・名前を添える」。
   並ばないとき・`ranking` 以外（`champions` など）は `tie` を付けず、渡す形は前と同じ。WRC-R の「通過回数がいちばん多いのは？」に9人の同率1位のうち1人だけを「最も多い」と答えたため（WRR-06 の本番）。テストは `tie.test.mjs`
7. 記録（決定5・grill Q8 を 2026-10-19 に置き換え）: `questions` に日時（JST）・質問文・結果（`answered` / `unanswered` / `candidates`）・型・答えの本文（`answer`、返した文言）・型の引数（`args`、null を除いた JSON の文字列。型が無い・引数が無いときは空）。IP は書かない。
   答えの本文は `max_tokens` 2048 で数千字までなので、ブックのセルの上限（5万字）に収まる
8. Claude の失敗（HTTP エラー・ワークスペースの上限の 400・拒否 `refusal`・打ち切り）は 503（`unavailable`）。この問いは数えたまま、記録は `unanswered`、答えの本文は「失敗: 」と理由（例「失敗: Claude plan: HTTP 529」）。
   2026-10-18 までは失敗も `none` も型が空で記録され、区別できなかった（「23期生の鳳凰位は？」が 2026-10-16 22:10 JST ごろに2回続けて答えられなかった原因を記録から決められなかった。CHAT-1019-AIC-07）

Claude の呼び出しの設定（公式の文書と claude-api skill で確かめた。2026-10-11）:

- SDK は使わず `fetch` で `https://api.anthropic.com/v1/messages` を呼ぶ（`x-api-key`・`anthropic-version: 2023-06-01`）。`workers/` に npm の依存とビルドの工程を持たないため（`mj-scheduler` と同じ）
- Haiku 5.5 は adaptive thinking が既定で有効で、effort の既定は `medium`。考える量は `output_config.effort: "low"`（簡単な問いは考えを省く）で抑え、`thinking` は送らない。`temperature` などは送らない（既定以外は 400）
- thinking も `max_tokens` に数えるので、2回とも `max_tokens` は 2048。本文は `content` の `text` ブロックだけを読む（先頭に `thinking` ブロックが来る）
- `stop_reason` が `end_turn` でなければ失敗として扱う。Haiku 5.5 の拒否にはサーバー側の代わりのモデルは無い
- 費用の実測用に、1回ごとの `input_tokens`・`output_tokens` だけを Workers Logs に書く（質問文は書かない、grill Q12）

GET `/api/ask/status`: `{"status": "open", "remaining": n, "max": 200}` か `{"status": "limit", "scope": …, "resetAt": …}`。質問欄は開いた時点で上限なら受付を止める。

質問欄の状態ごとの表示（`assets/houou_ask.js`）: 答え（読み方 → 本文 → 注記 → 「データ: …」 → 「出典: …」のリンク）、「答えられません。」、上限（全体「本日の受付は上限に達しました。翌0時（日本時間）から受け付けます。」・IP「本日ご質問いただける回数の上限に達しました。…」）、
それ以外（Worker が無い・失敗）は「いまは答えられません。」。答えは `textContent` で入れ、出典はルート相対の URL だけをリンクにする。

答えに出た選手のカード（2026-10-15、決定は `docs/decisions/houou.md`「2026-10-15（CHAT-1015-HOU-49）」）: 答えが出たら名簿 `houou/ask-players.json`
（在籍の選手だけ。`[名前, 写真, 現在のリーグ, 最高到達, 出場期数]`。値は個人成績の名前の面と同じ作り。現在のリーグは `current_league()` で、名前の面の `currentLeague()` と同じ決め方〈進行中の期のリーグ。前期の B1 で昇級して後期に出ない選手は B1、2026-10-17、#584〉。生成した名簿と名前の面の JS を node で突き合わせるテストは `test_houou_ask.py` の `GeneratedRosterTest`）を読み、答えの文と突き合わせる（`pickPlayers`）。
長い名前を先に当て、当たった文字に重なる短い名前は拾わない（在籍者の名簿で、ある名前が別の名前の一部になっている組は 2026-10-15 時点で0組）。
文に出た順に、同じ選手は1行。答えの面の下の別の面（`#houou_ask_players`、トップの3項目と同じ行の形）に、写真（無ければ頭文字の丸）・名前と現在のリーグのチップ /
「最高到達 ○○ · 出場 ○期」/ › の1人1行。最初は3人、4人目からは「ほか N名」を押して残りを開く（開いた後は閉じない。ボタンは消え、4人目にフォーカスを移す）。
押すと個人成績の期別（`players/?name=`、`view` なし）。「答えられません。」・上限・失敗・選手に当たらない答え・名簿が読めないときは出さない。突き合わせは画面の中だけで、新しい送信はしない。

vars（`wrangler.jsonc`。Wrangler のデプロイで上書きされるので、変えるときはこのファイルを直す）: `ALLOWED_ORIGIN`、`DAILY_LIMIT`（200。2026-10-16、試行中は 50）、`IP_DAILY_LIMIT`（10）。
`ASK_SHEET_ID`（週次の書き出し先のブックの ID。空なら書き出しを飛ばす）。
Secret（ダッシュボードで登録。デプロイでは消えない）: `ANTHROPIC_API_KEY`・`IP_HASH_SALT`・`GOOGLE_SA_KEY`（C3 で登録）。

## 平野さんの手作業（B1〜B4）

**2026-10-11 にすべて済み**（実際の結果は下の「B1〜B4 の実際の結果」）。手順は作り直すときのために残す。
A1（Console のワークスペース `ryoei-pro-ask`、月の上限 $30、地域は「米国」のまま）と A2（D1 `mj-ask` の作成、Database ID は `wrangler.jsonc`）は 2026-10-11 に済み。
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

### B1〜B4 の実際の結果（2026-10-11 23:30〜23:45 JST、平野さんのスクリーンショットをチャット側が確認。申告値）

- B1: プロジェクト `mj-ask` の最初のビルドは成功（D1 のバインディング `env.DB (mj-ask)`、ルート `ryoei.pro/api/*`、Version ID `a1192b33-919b-4a98-995b-1b7c049d23cb`）。
  **`mj build token` に D1 の権限を足す必要は無かった**（既にある D1 を `database_id` で結ぶだけなら要らない）。Build watch paths の Include は `workers/ask/**` だけ
- B2: `schema.sql` の表を1行にまとめた SQL を Console で実行した。Tables に `daily_counts`・`ip_counts`・`questions`・`sqlite_sequence`（AUTOINCREMENT が作る表）が出た
- B3: キー `mj-ask`（ワークスペース `ryoei-pro-ask`、本人に紐づけ、期限 **2027-10-11 0:00 JST**）。期限を 2027-10-12 0:00 JST にすると「このリクエストは完了できませんでした」で作れなかった（1年を超えると作れない見込み）。
  作成の前に「ID 連携」を勧めるダイアログが出て、「API キーで続ける」を選んだ。差し替えの予定は ryoei.pro カレンダーの 2027-09-28
- B4: 画面の名前は「Runtime variables and secrets」（公式の文書の「Variables and Secrets」と違った）。Secret の `ANTHROPIC_API_KEY`・`IP_HASH_SALT` を足した。変数 `ALLOWED_ORIGIN`・`DAILY_LIMIT`（50）・`IP_DAILY_LIMIT`（10）も出ている
- 本番の最初の1問（2026-10-11 23:45 JST ごろ、平野さんのブラウザ）: 「鳳凰位の最多獲得は誰ですか？」に「安藤満（第2・5・6・10期）と前原雄大（第12・25・33・34期）の4回で並び、獲得者は全部で23名」、
  「データ: 決勝 第42期まで」と出典「鳳凰戦 歴代優勝者」が付いた（チャット側が `data.json` の finals で数えて一致を確かめた）

## 本番の POST の 403（解決、2026-10-11）

CHAT-1011-AIC-03 で、マージの後に本番へ POST すると Worker に届く前に Cloudflare の遮断のページ（「Attention Required! | Cloudflare」、403）が返った。
原因は ryoei.pro の WAF のカスタムルール「Block non-GET/HEAD methods」（GET・HEAD 以外を遮断。`/cdn-cgi/` は除外）。チャット側が Security Events で確かめた（直近24時間で POST 123件を遮断）。
対処として、平野さんが式の末尾に `and not (http.request.uri.path eq "/api/ask")` を足した（2026-10-11 23:30 JST ごろ）。式の全体は docs/notes/cloudflare.md「カスタムルール「Block non-GET/HEAD methods」」。
`/api/ask` 以外への POST は今も遮断される（2026-10-12 にセッションから `/houou/` への POST が 403 を確かめた）。POST を受けるパスを足すときは、この式にも例外を足す。

## 本番の確かめ（2026-10-12、CHAT-1011-AIC-04）

セッションから確かめたこと（Claude を呼ぶ問いは送っていない。費用と上限を使わないため）:

- GET `/api/ask/status` → 200、`{"status":"open","remaining":10,"max":200}`、`cache-control: no-store`
- POST `/api/ask`（Origin 無し）→ 403 `{"status":"forbidden"}`（Worker の応答。WAF の遮断のページではない）
- POST `/api/ask`（Origin 付き、空の質問）→ 400 `{"status":"invalid","reason":"empty"}`（数えない）
- GET `/api/x` → 404

セッションからは届かないもの（D1・Workers Logs・Console の使用額）は、平野さんに見てもらう。D1 の Console で次を実行すると、記録に IP が無いこと・答えの本文と型の引数が入ったことを確かめられる:

```sql
SELECT name FROM pragma_table_info('questions');
SELECT id, asked_at, question, result, type, answer, args FROM questions ORDER BY id DESC LIMIT 20;
SELECT day, n FROM daily_counts ORDER BY day;
SELECT day, length(ip_hash) AS hash_length, n FROM ip_counts;
```

期待する結果: `questions` の列は `id`・`asked_at`・`question`・`result`・`type`・`answer`・`args` の7つ（`answer`・`args` は 2026-10-19 に ALTER TABLE で足した。前の行は空）。`result` は `answered`・`unanswered`・`candidates`。`ip_counts` の `hash_length` は 64（IP そのものではない）。

1問あたりの費用: Workers & Pages → `mj-ask` → Observability の Events に、1問ごとに `{"call":"plan",…}` と `{"call":"answer",…}` の2行（`input_tokens`・`output_tokens`）が出る。
費用は（入力の合計 × $0.10 ＋ 出力の合計 × $0.50）÷ 100万（Haiku 5.5、入力が10万トークン以下のとき）。Console の Cost の画面のワークスペース `ryoei-pro-ask` の額と突き合わせる。
2026-10-12 の時点では、この数字はセッションから見られない。試行のまとめは 10/20 まで待たず、今ある記録で行う（2026-10-16 の決定。取り方は CHAT-1016-HOU-52 のログ）。

## 変更の指示を作るときの前提: 本番で送る固定の問い（2026-10-17、HOU-67）

AI への指示（`ANSWER_SYSTEM`）・一覧の切り方・並べ替えを変える指示では、マージの後に本番へこの5問を送り、直しの前と比べる。
答えは Claude を呼ばずにテストできないため、毎回同じ問いで見る（「10人で切る」直しは HOU-52→53→55→57→59 の5往復で、毎回ちがう問いで次の問題が出た）。
送った問いは1日の上限（全体200・IP 10）に数える。期待は HOU-58・59 の本番の答え（#542 のコメント）。

| 問い | 見る点 |
|---|---|
| 現在の鳳凰位は？ | 白鳥翔（第41期・第42期）。聞いていない人数・「ほか N人」が付かない |
| 第1期から第10期の鳳凰位は？ | 6人を古い期の順に。事実が前と同じ |
| 鳳凰位になった人は全部で何人？ | 23人。名前は10人（新しい期から、白鳥翔が先頭）と「ほか13人」。指示の言葉が答えに出ない |
| 通算得点の上位15人は？ | 名前10人・「ほか5人」・全15人。出典は「鳳凰戦 ランキング（通算）」 |
| 白鳥翔の第40期の成績は？ | A2 で1位・昇級・278.8。人数は付かない |

5問とも、答えの下のカードの行と「ほか N名」が、答えの文に出た在籍の選手と一致すること。前と同じ問いで事実が悪くなったら直しを戻す（HOU-53 の目安）。

## 週次の cron（実装3、grill Q9）

`wrangler.jsonc` の `triggers.crons` に `0 0 * * 2`（UTC の月曜 0:00 = **月曜 9:00 JST**）。**Cloudflare の Cron Triggers の曜日は 1 = 日曜・2 = 月曜**（公式の Cron Triggers のページ「Days of the week go from 1 = Sunday to 7 = Saturday」。ほかの cron の 0 = 日曜と違う）。
2026-10-11〜13 は誤って `0 0 * * 1`（日曜 9:00 JST）になっていた（CHAT-1013-AIC-05 で直した）。Cron Triggers はアカウントで5本まで（Free）で、`mj-scheduler`（毎分）と合わせて2本。
入口は `index.mjs` の `scheduled`、本体は `src/weekly.mjs`。

1. **ブックへの書き出し**（`GOOGLE_SA_KEY` と `ASK_SHEET_ID` があるとき）: 専用のブックの最初のシートに、1行目の見出し（日時・質問文・結果・型・答え・引数。空のブックなら最初に足し、行があれば毎回書き直す〈4列だったブックを6列にする、2026-10-19〉）に続けて、
   D1 の `questions` のうち「ブックの最後の行の日時より後」かつ「今週の月曜 0 時（JST）より前」かつ「90日以内」の行を日時の順に足す（`values:append`、`valueInputOption=RAW` で質問文を式として扱わせない）。
   前の週の行が足され、取りこぼした週があれば次の回に拾い、同じ回が2度動いても同じ行を二度足さない。結果は「答えた」「答えられなかった」「候補を出した」、型・答え・引数が無ければ空
2. **ブックの90日を過ぎた行の削除**: 2行目から日時の古い順に並んでいる前提で、先頭から続く90日より前の行を消す（`batchUpdate` の `deleteDimension`）。ブックの行を手で並べ替えると、古い行が残ることがある
3. **D1 の90日を過ぎた行の削除**: `DELETE FROM questions WHERE asked_at < <90日前>`。鍵が無いときや Google の呼び出しが失敗したときも行う
4. 鍵かブックの ID が無ければ、Workers Logs に「週次: 鍵(GOOGLE_SA_KEY)かブックの ID(ASK_SHEET_ID)が無いため、ブックへの書き出しを飛ばす」と書き、1・2 を飛ばして正常に終わる。最後に `週次: {cutoff, until, sheet, d1Deleted}` の1行を書く

Google の認証: サービスアカウントの JSON の鍵の `client_email`・`private_key` で JWT を RS256（WebCrypto）で署名し、`https://oauth2.googleapis.com/token` でアクセストークンに替える（スコープ `https://www.googleapis.com/auth/spreadsheets`。読み書きできるのは共有されたブックだけ）。
Worker から出るのは `oauth2.googleapis.com`・`sheets.googleapis.com` だけで、ブラウザの外部ドメインは増えない。

## 平野さんの手作業（C1〜C3）

**2026-10-13 にすべて済み**（01:42 JST、平野さんのスクリーンショットをチャット側が確認。申告値）。手順は作り直すときのために残す。
画面の名前は docs/notes/live-channel-write.md（`live-channel-writer` の作成の記録）と同じ。既存のサービスアカウント（`birthday-calendar`・`gsc-export`・`live-channel-writer`）は使い回さない（grill Q9「この機能専用」）。

実際の結果:

- C1: サービスアカウント `mj-ask`（`mj-ask@<Google Cloud のプロジェクト ID>.iam.gserviceaccount.com`。プロジェクト ID の値は Google Cloud Console で確かめる）。Google Sheets API は有効
- C2: ブック「ryoei.pro_AIボット」（ID は `workers/ask/wrangler.jsonc` の `ASK_SHEET_ID`）。共有は平野さん（オーナー）とサービスアカウント（編集者）だけ。
  一般的なアクセスは「**制限付き**」（最初は「リンクを知っている全員（閲覧者）」になっていたのを、2026-10-13 01:44 に直した。質問文が入るので公開しない）
- C3: `mj-ask` の Secret `GOOGLE_SA_KEY` を登録した。ブックの ID は CHAT-1013-AIC-05 で `wrangler.jsonc` の `ASK_SHEET_ID` に入れた（そのデプロイで、ダッシュボードの空の Variable `ASK_SHEET_ID` も置き換わる見込み）
- 最初の書き出しは 2026-10-19（月）9:00 JST の見込み。Workers Logs の `週次:` の行（`sheet` が `{appended, sheetDeleted}`）と、ブックの1行目の見出し・2行目からの行で確かめる。
  `sheet` が `null` なら、`週次: ブックへの書き出しに失敗: …`（Google の HTTP の状態）の行を見る

### C1: サービスアカウントを作り、JSON の鍵を発行する

1. https://console.cloud.google.com/ で `live-channel-writer` と同じプロジェクトが選ばれていることを確かめる（Google Sheets API は有効にしてある。無効なら「API とサービス」→「ライブラリ」→「Google Sheets API」→「有効にする」）
2. 「IAM と管理」→「サービス アカウント」→「サービス アカウントを作成」→ 名前 **`mj-ask`** →「作成して続行」→ ロールは付けずに「完了」
3. 一覧に出たメールアドレス（`mj-ask@<プロジェクト ID>.iam.gserviceaccount.com` の形）を控える
4. そのサービスアカウントを開き、「鍵」タブ →「鍵を追加」→「新しい鍵を作成」→「JSON」→「作成」。JSON ファイルがダウンロードされる（C3 で貼ったら PC から消す）
   - 「鍵の作成が組織のポリシーで無効」と出たら、ここで止めて Claude Code に伝える

### C2: 専用のブックを作り、サービスアカウントに共有する

1. Google ドライブで新しいスプレッドシートを作る（今のブックは「ryoei.pro_AIボット」）。最初のシートに書き出す（1行目の見出しは Worker が書くので、空のままでよい。シート名は「シート1」のままでよい）
2. 右上の「共有」→「ユーザーやグループを追加」→ C1 の3で控えたメールアドレス → 権限「**編集者**」→「送信」
3. 同じ「共有」の画面の「一般的なアクセス」を「**制限付き**」にする（新しいブックの既定が「リンクを知っている全員」のことがある。質問文が入るので公開しない）
   - 共有できない・「制限されています」と出たら、ここで止めて Claude Code に伝える（docs/notes/live-channel-write.md「4.」の注記と同じ理由の見込み）
4. ブックの URL の `https://docs.google.com/spreadsheets/d/<ここ>/edit` の `<ここ>` がブックの ID

### C3: 鍵を Secret に登録し、ブックの ID を伝える

1. Workers & Pages → `mj-ask` → Settings → **Runtime variables and secrets**（B4 と同じ画面）→ **Add**
2. Type **Secret**、Variable name **`GOOGLE_SA_KEY`**、Value に C1 の4の JSON ファイルの中身を全部（メモ帳で開いて Ctrl+A → Ctrl+C）→ **Deploy**
3. ダウンロードした JSON を PC から消す（ごみ箱も空にする）
4. ブックの ID（C2 の4）をチャットに伝える。ID は秘密の値ではないので、指示で `wrangler.jsonc` の `ASK_SHEET_ID` に書く（ダッシュボードで変数を足しても、次のデプロイで `wrangler.jsonc` の値に上書きされるため）
5. 最初の書き出しは、`ASK_SHEET_ID` を入れた後の月曜 9:00 JST。Workers Logs の `週次:` の行と、ブックの行で確かめる

## キーの差し替え（期限の前・漏れたとき）

1. Claude Console → Settings → API keys → Create key で、同じ条件（ワークスペース `ryoei-pro-ask`・本人・1年。1年を超える期限は作れなかった）の新しいキーを作る。今のキーの期限は 2027-10-11 0:00 JST、差し替えの予定は 2027-09-28
2. `mj-ask` → Settings → Runtime variables and secrets → `ANTHROPIC_API_KEY` の値を新しいキーに替えて **Deploy**
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

