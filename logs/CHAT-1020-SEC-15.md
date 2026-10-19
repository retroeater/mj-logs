# CHAT-1020-SEC-15

- 着手日時: 2026-10-20
- 対象issue: #598
- ブランチ: work/1020-sec15
- 着手時HEAD: e7c8d9e08

## 指示

【Claude作成】Claude Code 向け指示：再点検（SEC-14）の Worker の指摘（低-10・低-4）を直し、SEC-14 の後の決定を記録する
Chat-Ref: CHAT-1020-SEC-15
マージ: 承認済み（チャットで）。条件は「止まる条件」「手順3」のとおり。変えるのは `workers/ask/src/`（低-10・低-4 の箇所だけ）、`workers/ask/test/`（足すテスト）、docs（`docs/decisions/security.md`・`docs/notes/security.md`・`docs/logs/`）。生成スクリプト・生成物・`assets/`・`_headers`・`.github/`・`workers/scheduler/` は変えない
貼る時機: いつでも（CHAT-1019-SEC-14 はマージ済み）。JST 03:45〜05:15 と月曜 05:00〜07:00 は cloudflare への push をしない（当たったら終わるまで待ってよい。CLAUDE.md の15分の上限の例外）。新しいセッション: 任意（SEC-14 の続きでも可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-sec15 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1020-sec15 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-sec15 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。続いて CHAT-1019-SEC-14 のログ（`docs/logs/CHAT-1019-SEC-14.md`）の `## 報告` の状態の末尾に ` / 続き: CHAT-1020-SEC-15` を足してコミットする（この指示は SEC-14 の判断待ちへの回答を含む）。

## 目的
再点検（CHAT-1019-SEC-14、#598 の issuecomment-6270152890）で出た Worker `mj-ask` の指摘2件（低-10・低-4）を直す。あわせて SEC-14 の「判断が必要なこと」への平野さんの回答と、ダッシュボード側の確認の結果を docs/decisions に記録する。生成とフロントの直し（中-3 の残り・低-6・低-7・低-8 後半）は次の指示（SEC-16）で行う。

### 決定（2026-10-20、平野さん）
- 直しは2本に分ける: (1) Worker（低-10・低-4）＝この指示、(2) 生成とフロント（中-3 の残り・低-6・低-7・低-8 後半）＝次の指示。低-3（CI の `packages:` の版の固定）は #564 か Dependabot の扱いと合わせて別に行う。低-5（答えの文の検証）は直さない（影響が質問した本人の画面だけのため）
- Claude Security は定期では回さない。`workers/` を大きく変えたときの Scan changes に加え、再点検（年1回か大きな変更の後）のときに1回回す。使用量はセッションから見えないので、次に回すときは平野さんが直前・直後に claude.ai の Settings → Usage のスクリーンショットを撮り、%の差で量る
- この指示のマージは承認済み（条件付き。下の止まる条件・手順3）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 平野さんのダッシュボード側の確認（2026-10-20）: Mozilla HTTP Observatory の取り直しは 105 点（前回 B 70/100、2026-10-18）。GitHub の Settings → Advanced Security の画面に Secret scanning／Push protection の項目は無い（個人の private リポジトリではまだ使えない。代わりは引き続き `security-check.yml` の履歴の走査）。Actions の Workflow permissions の既定は 2026-10-19 12:59 に読み取りだけにし、PR の作成・承認の許可を外した（平野さん。CHAT-1018-SEC-10 の予定の実施）。これらは決定ではなく確認の結果として decisions に書く
- 低-10（要確認、場所は SEC-14 のログ。WRR 系列が同じファイルを変えているので行番号はずれている見込み）: `workers/ask/src/ask.mjs` の「知らない大会」の検査で、`contest` に `__proto__` などを送ると検査を通る。大会の表を `Object.hasOwn` で引く（またはプロトタイプの無い表にする）。テストは `__proto__`・`constructor`・`toString` が「知らない大会」として扱われ、回数に数えず Claude API を呼ばないこと（今の挙動で1問と数えているなら、直した後は数えないことも確かめる）
- 低-4（要確認）: `ask.mjs` の `json()` が返す応答に `x-content-type-options: nosniff` を付ける（`_headers` は Worker の応答に効かない、`docs/notes/security.md` の (a)）。テストはヘッダが付くこと
- 本番で確かめるのは `docs/notes/security.md`「本番に送ってよい要求」の2つだけ。`/api/ask` に質問や `__proto__` を送らない（1問と数えるため）
- `docs/notes/security.md` は、Claude Security の「回し方」の行を上の決定に合わせて直し、(a) の行に低-10 の観点（大会名などの表をプロトタイプ経由で引かない）を一句足す

## 手順
1. 確かめる: #598 が Open、SEC-14 のログの `## 報告` の低-10・低-4 の記述。`git branch -r --no-merged origin/cloudflare` の各ブランチが `workers/ask/src/ask.mjs` の同じ関数（大会名の検査・`json()`）を変えているか（変えていれば止まる。同じファイルの別の関数ならログに書いて進む）。#598 に着手中のコメントを書く
2. 直す: 低-10・低-4 を直し、テストを `workers/ask/test/` に足す。直す前の src で足したテストが落ち、直した後に通ることを確かめる。`node --test 'workers/ask/test/*.test.mjs'`・`node --test 'workers/scheduler/test/*.test.mjs'`・`python3 -m unittest discover -s scripts/tests` を回す。docs（上の決定・前提の記録、`docs/notes/security.md` の2か所、`docs/notes/ask-worker.md` に応答のヘッダの記述があればそこも）を直す
3. マージと確かめ: origin/cloudflare を取り込み直してテストを回し直し、cloudflare との差分が `workers/ask/src/` の2か所・足したテスト・docs だけであることを確かめてマージする。Workers Builds の成否（`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」）を確かめ、本番の `GET /api/ask/status` が 200 で `x-content-type-options: nosniff` が付くこと、`POST /api/ask`（`Origin: https://example.invalid`・本文なし）が 403 でそのヘッダが付くことを確かめる。#598 に結果（直した2件・テスト・本番の確かめ）をコメントする

## 止まる条件
- 手順1の前提が食い違う（#598 が Closed、SEC-14 のログに低-10・低-4 が無い、指摘の箇所がもう直っている）
- 未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数（大会名の検査・`json()`）を変えている、または取り込みで衝突する（`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
- テストが1つでも落ちる（自分の変更で落ちると分かっている既存のテストは、直す理由をログに書いて直してよい）、または cloudflare との差分に上の範囲の外のものがある（マージせずに止まる）
- マージ後の Workers Builds が失敗した、または本番の確かめで 200／403 以外の状態コードが返った: 今回の変更による失敗なら、マージのコミットを revert して push・マージし（承認済み）、止まる。無関係な失敗なら原因をログに書いて残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、次の指示（SEC-16: 生成とフロント）を作るのに要る点（対象の箇所の今の行・再生成で差分が出そうなページの見込み）を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-SEC-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-SEC-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致。
- Chat-Ref 重複なし（`git log --all --grep`）。`work/1020-sec15` はローカル・リモートとも無し → `git checkout -b work/1020-sec15 origin/cloudflare`。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手 2026-10-20 01:39 JST（push を止める時間帯の外）。
- SEC-14 のログの状態を「判断待ち / 続き: CHAT-1020-SEC-15」にしてコミット（63292249e）。

### 手順1（確かめ）

- #598: Open。SEC-14 のログの `## 報告` に低-10（`ask.mjs:375`）・低-4（`ask.mjs:184-189` の `json()`）がある。今の行は `json()` が 185〜190、大会の検査が 375（まだ直っていない）
- `git branch -r --no-merged origin/cloudflare` の work/ ブランチで `workers/ask/src/ask.mjs` を変えているものは無い（`git diff origin/cloudflare...<各ブランチ> -- workers/ask/src/ask.mjs` が空）
- #598 に着手中のコメント（issuecomment-6272235817）

### 手順2（直し）

- テストを先に `workers/ask/test/ask.test.mjs` に2本足した:
  - 「大会名がプロトタイプの名前(__proto__ など)なら知らない大会として受け付けず、数えない」: `__proto__`・`constructor`・`toString`・`hasOwnProperty` が 400 `{"status":"invalid","reason":"bad_request"}`、`daily_counts` が空（数えない）、fetch（Claude API）が0回
  - 「応答に x-content-type-options: nosniff が付く」: `status()` の 200 と `ask()` の 403
- 直す前の src で回すと2本とも落ちた（83 件中 81 通過・2 失敗。nosniff は `null !== 'nosniff'`）
- 直し（`workers/ask/src/ask.mjs`）:
  - 低-10: `deps.contests?.[body.contest]` を、`body.contest === undefined` なら `deps.data`、そうでなければ `deps.contests && Object.hasOwn(deps.contests, body.contest)` のときだけ `deps.contests[body.contest]` に。外れれば `data` が undefined のまま、既存の `if (!data)` で 400（回数を数える前）
  - 低-4: `json()` の headers に `'x-content-type-options': 'nosniff'`
- 直した後: ask 83・scheduler 44 件、`python3 -m unittest discover -s scripts/tests` 1450 件すべて通る
- 範囲の外として残したもの: `workers/ask/src/index.mjs` の 405・404 の応答（`new Response(null, …)`・`'Not Found'`）にも nosniff は無い（本文が空か短い文字列。指示の範囲は `json()` だけ）
- docs:
  - `docs/notes/security.md`: (a) の「入力の検査」に「大会名などの表をプロトタイプ経由で引かない、#598 低-10」を足し、Claude Security の「回し方」を 2026-10-20 の決定に直した
  - `docs/decisions/security.md`: `## 2026-10-20（CHAT-1020-SEC-15）` を足した（決定3つ＋確認の結果）。2026-10-18（CHAT-1018-SEC-07）の Claude Security の行に「→ 置き換え: 2026-10-20（CHAT-1020-SEC-15）」を付けた
  - `docs/notes/ask-worker.md` に応答のヘッダの記述は無い（`_headers` の語は Routes の説明だけ）ので変えていない
- コミット: 26b3804ea（fix）・310a707e3（docs）

### 手順3（マージ前）

- `git fetch origin cloudflare` の後 `git merge origin/cloudflare` は変化なし（cloudflare は e7c8d9e08 のまま）。`git merge-base --is-ancestor origin/cloudflare HEAD` は真
- cloudflare との差分: `docs/decisions/security.md`・`docs/logs/CHAT-1019-SEC-14.md`・`docs/logs/CHAT-1020-SEC-15.md`・`docs/notes/security.md`・`workers/ask/src/ask.mjs`・`workers/ask/test/ask.test.mjs` だけ（範囲内）
- テストを回し直して ask 83・scheduler 44・unittest 1450 件すべて通る


### 手順3（マージと本番の確かめ）

- 2026-10-20 01:44 JST に `git push origin work/1020-sec15:cloudflare`（e7c8d9e08..2ec24f239、fast-forward。push 直前に再 fetch して `merge-base --is-ancestor` が真、差分は上の6ファイル）
- check-runs（`gh api repos/retroeater/mj/commits/2ec24f239…/check-runs`）: 「Workers Builds: mj-ask」completed・success（summary に Preview の URL 無し＝本番）。「Workers Builds: mj」は success だが summary に Preview の URL があり、作業ブランチの push のプレビューのもの（サイトの静的ファイルは変えていない）。`check` success、`notify` skipped
- 本番（User-Agent を付けて各1回、01:54 JST）: `GET /api/ask/status` は 200・`content-type: application/json; charset=utf-8`・`x-content-type-options: nosniff`。`POST /api/ask`（`Origin: https://example.invalid`・本文なし）は 403・同じく nosniff が付く。`/api/ask` に質問や `__proto__` は送っていない
- #598 に結果のコメント（issuecomment-6272528897）

### SEC-16 のための下調べ（今の行と再生成の差分の見込み）

- 中-3 の残り: `scripts/generate_resource_logs.py:165`（`get_info_cell` の `esc(restaurant_url)`）。今の `resource_logs.html` の店名のリンク 3042 件はすべて `https:` なので、`safe_url()` を通しても差分0の見込み。`assets/league.js:856`（`hit.href = up.url`）は JS だけの変更で生成物は変わらない
- 低-6: `scripts/generate_houou_pages.py:369`（`json_script()`、houou/・ouka/・jwrc/・wrcr/ の `<script type="application/json">`）・`scripts/generate_video_wayhome.py:190`・`scripts/generate_wayhome_episodes.py:184`・`scripts/lib/mtsuku.py:426`（JSON-LD）。今の配信対象の HTML で、埋め込みの JSON（`application/json`・`ld+json`）に `<` を含むものは0件（全 HTML を走査）→ `<` を `\u003c` にそろえても差分0の見込み
- 低-7: `assets/houou_ask.js:229`（出典の判定）・`assets/saikyo.js:73`（`decodeURIComponent` の try なし）・`assets/league_race.js:338`（CSS の `url("${p.img}")`）。JS だけ
- 低-8 後半: `scripts/generate_jpml_pros.py:178-180`（`get_internal_link` の `?{param_name}={esc(param)}`）。リンクは 671 件で名前は日本語（例 `./houou/players/?name=會田亮介`）。`urllib.parse.quote` にすると全件が `%E6…` の形に変わり、jpml_pros.html の差分が大きくなる。`&`・`#`・`%`・`+`・空白を含む名前は今は0件。差分を避けるなら、それらの文字だけを逃がす（`quote(name, safe=…)` で日本語を残す）形を選ぶ

## 報告

- 状態: 判断待ち
- ブランチ: work/1020-sec15
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-SEC-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-sec15
- 確認用URL: なし（Worker `mj-ask` の変更。本番の `/api/ask/status` と 403 の応答で確かめた）
- マージ: 済（2ec24f239）
- issue: #598（結果のコメント issuecomment-6272528897）
- 判断が必要なこと:
  - SEC-16（生成とフロント）を作るときの点（詳細は `## 経過` の「SEC-16 のための下調べ」）:
    - 対象の今の行: `scripts/generate_resource_logs.py:165`・`assets/league.js:856`（中-3 の残り）、`scripts/generate_houou_pages.py:369`・`scripts/generate_video_wayhome.py:190`・`scripts/generate_wayhome_episodes.py:184`・`scripts/lib/mtsuku.py:426`（低-6）、`assets/houou_ask.js:229`・`assets/saikyo.js:73`・`assets/league_race.js:338`（低-7）、`scripts/generate_jpml_pros.py:178-180`（低-8 後半）
    - 再生成の差分の見込み: 中-3・低-6 は今のデータで差分0（店名の URL はすべて https、埋め込みの JSON に `<` は0件）。低-8 後半は `urllib.parse.quote` そのままだと jpml_pros.html の 671 件のリンクが全部変わる。日本語を残して `&`・`#`・`%`・`+`・空白だけを逃がすか、全件の変化を受け入れるかを決める
    - 範囲の外として残したもの: `workers/ask/src/index.mjs` の 405・404 の応答に nosniff が無い（SEC-16 に入れるか、そのままにするか）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2ec24f23）: https://github.com/retroeater/mj-logs/tree/main/guide/2ec24f23

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e7c8d9e0.md
