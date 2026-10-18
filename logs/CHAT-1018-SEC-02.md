# CHAT-1018-SEC-02

- 着手日時: 2026-10-18
- 対象issue: #598、#300（コメントのみ）
- ブランチ: work/1018-sec
- 着手時HEAD: 82a05e9a1

## 指示

【Claude作成】Claude Code 向け指示：#598 の中-2・中-3 を直し、セキュリティ検査のワークフロー（手動実行のみ）を足す
Chat-Ref: CHAT-1018-SEC-02
マージ: 承認済み（チャットで）。ただし止まる条件に当たったとき・確認が1つでも通らないときはマージせずに報告する
貼る時機: いつでも（CHAT-1017-SEC-01 は cloudflare にマージ済み）。新しいセッション: 任意（続きでも可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-sec の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1018-sec を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-sec origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1017-SEC-01 の点検（#598）のうち、すぐ直せる中-2（書籍の JSON-LD のエスケープ）と中-3（シートの URL の scheme を確かめない `href`）を直す。あわせて、点検の道具（zizmor・pip-audit・履歴の鍵の走査）を GitHub Actions のワークフロー `security-check.yml`（手動実行のみ・結果を見せるだけ）にする。残りの論点（中-1・中-4・中-5・Observatory の採点・プラグイン）の決定を #598 と docs/decisions/security.md に記録する。

### 決定（2026-10-18、平野さん。チャット側の推奨をすべて採用）
- 中-1（`/api/ask` の守りが `Origin` と D1 の回数だけ）: リスクは受容する（費用の上限は 1日 200問×2呼び出しで固定済みで、最悪でも「その日使えない」だけ）。加えて平野さんが Cloudflare のダッシュボードで `/api/ask` への Rate limiting rule を足す（手作業。この指示では行わない）。Turnstile（#543）は再開しない
- 中-2: `generate_books_pages.py` の JSON-LD の `<` を逃がす直しを入れる。再生成の差分がその箇所だけなら本番の `books/` も更新する（凍結中でも可）。ほかに差分が出るならスクリプトの直しだけにする
- 中-3: http/https 以外の URL をリンクにしない共通の関数を `scripts/lib/` に置き、指摘の全箇所（JS 1か所を含む）に当てる。全ページの再生成で差分0を確かめる
- 中-4・中-5（第三者 action の SHA 固定・`secrets: inherit`・書き込み権限・`permissions` の無いワークフロー、zizmor の template-injection High 4件）: Actions の整理（WFL、#564）の段④が終わるまで待ち、別の指示（SEC-04）で行う
- CI の検査ワークフロー: zizmor・pip-audit・履歴の鍵の走査を、この指示で `workflow_dispatch` のみのワークフローとして作る。月1回の起動は WFL の段④の後に Worker のスケジュール（`workers/scheduler/schedule.json`）に足す（この指示では足さない）
- Security Guidance プラグインのコミット・停止時の LLM レビューは動かさない（クラウド環境に `ANTHROPIC_API_KEY` を置かない）。編集ごとのパターン照合だけで当面よしとする
- Claude Security プラグイン（深い診断）: 平野さんが claude.ai で有効にし、この指示の直しの後に別の指示（SEC-03）で1回だけ `workers/`＋`scripts/lib/` を対象に走らせる
- Mozilla HTTP Observatory の採点（2026-10-18、平野さんがブラウザで実施）: B（70/100、12項目中10合格）。減点は CSP 未実装（−25）と Subresource Integrity 未実装（−5）だけ。SRI は #9（CSP）と一緒に扱う。Cross-Origin-Opener-Policy・Cross-Origin-Embedder-Policy は減点なしで、静的サイトに入れる理由が薄いので見送る。HSTS の preload 登録も見送る

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- **`.github/dependabot.yml` は SEC-04 に回す（チャットの推奨から変えた点）。** 理由: SHA 固定の前に入れると、Dependabot はタグ（`@v4` → `@v5`）を上げる PR を作るだけで、`dependabot/…` のブランチと PR が work/ の運用の外にできる。効くのは SHA 固定と組み合わせたとき
- 中-3 の対象（#598 の表と SEC-01 のログ (b) による。行番号は着手時に実物で確かめる）: `scripts/lib/page.py:128`（`build_image_cell`、6ページ）、`generate_saikyo_pages.py:777`、`scripts/lib/wayhome.py:90`、`generate_resource_logs.py:196`、`generate_rh_paifu.py:66`、`generate_rh_results_detail.py:72`、`generate_jpml_pros.py:248`（Ampai）、`assets/houou.js:809`（Ampai）。grep（`href=`・`src=` に変数を入れる箇所）で漏れが無いかも見て、増えた箇所は同じ扱いにして報告に書く
- 関数の形の案: `scripts/lib/page.py`（または `scripts/lib/url.py`）に `safe_url(value) -> str | None`。前後の空白を除き、scheme が `http`・`https`（大文字小文字を問わない）のときだけ値を返し、それ以外（`javascript:`・`data:`・`vbscript:`・scheme 無し・空）は `None`。`None` のときはリンクにせず、今のリンクが無いときと同じ表示にする。`esc()` は今までどおり後で当てる。JS は同じ判定の小さな関数をそのファイルに置く（共通化は不要）。テストを `scripts/tests/` に足す（`javascript:`・`JaVaScRiPt:`・前後の空白・`data:`・相対・`//evil`・正常な http/https）
- 中-2: `json.dumps(...)` の結果の `<` を `<` に置き換える（`>`・`&` も置き換えてよい）。houou の3か所（`</` だけ逃がす）も同じ関数にそろえてよいが、生成物に差分が出るならそろえない（報告に書く）
- `books_pages` は `regenerate.py all` から外れている（`FROZEN_FROM_ALL`、docs/notes/books-freeze.md）。ページ名を直接指定して生成する。楽天の取得はしない
- `security-check.yml` の形の案:
  - 契機は `workflow_dispatch` だけ。トップレベル `permissions: {}`、ジョブは `contents: read` だけ。Secret を使わない
  - この新しいファイル自身は、`actions/checkout`・`actions/setup-python` を **SHA で固定**し（タグをコメントで残す）、`persist-credentials: false`、`fetch-depth: 0`（履歴の走査のため）。ほかのワークフローの直しは SEC-04 なので触らない
  - 道具は版を固定して入れる（docs/notes/security.md の実測の版: zizmor 1.31.1・pip-audit 2.10.1）。`.github/actions/setup-python` が入れる組は `security-check.yml` の中で requirements に書き出して `pip-audit -r` に渡す（組の正をどこに置くかは実物に合わせてよい。二重管理になるなら報告に書く）
  - 履歴の鍵の走査は `scripts/scan_secret_history.py`（docs/notes/security.md「道具と入れ方」の正規表現。追加行だけ、種類・SHA・ファイル名だけを出し**値は出さない**）。既知の当たり（#299 の `AIza` 2件、テストの PEM の枠、ログの指示欄の文字列）は SHA とファイル名で除外する一覧（例 `data/security/known-history-hits.txt`）に書く。テストを足す
  - 結果は Job summary に書くだけ。zizmor と pip-audit は見つかっても失敗にしない（今の zizmor は 150 件を超えて出るため）。履歴の走査は、除外の一覧に無い新しい当たりがあればジョブを失敗にする（そのときも値は出さない）
  - 新しいワークフローは cloudflare に無いので作業ブランチで `gh workflow run --ref` が 404 になる見込み（docs/notes/github-actions.md「ワークフローを変更したとき」）。副作用が無い（読むだけ・Secret なし・書き込みなし）ので、マージしてから cloudflare で手動実行して確かめる。セッションのトークンで起動できなければ、平野さんに GitHub の画面の「Run workflow」を頼む（最終報告に直接開けるリンク https://github.com/retroeater/mj/actions/workflows/security-check.yml を書く）
  - Actions の予算（10月 $10、#298）に対し、1回 2〜3 分の見込み（要確認。実測をログに書く）
- WFL（#564）が `.github/workflows/` を並行して変えている。この指示で触る workflows 側は新しいファイル1つと、docs/notes/github-actions.md「ワークフローの一覧」の表の1行（`scripts/tests/test_workflows_table.py`）だけ。運行表（mj-logs の actions/index.html）の「手動実行だけ」の区分に出るかは確かめ、出なければ報告に書く
- #300（秘密のコミットを検知する仕組み）は履歴の走査と重なる。#300 に「SEC-02 で履歴の走査を手動実行のワークフローにした。push のたびの検知（GitHub の Push protection 等）は #598 の平野さんの確認の結果を待つ」とコメントする（本文・状態・ラベルは変えない）

## 手順
1. 調べる: #598 の本文と #9・#300・#564 の直近の状態、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチの変えたファイル）を確かめる。#598 に着手中のコメントを書く。中-3 の対象を実物の行で確かめ、grep で漏れを探す
2. 直す（節目ごとにコミットして push）: (a) `safe_url` とテスト → 中-3 の全箇所 → `assets/houou.js` → (b) 中-2 の JSON-LD → (c) `scripts/scan_secret_history.py` とテスト・除外の一覧 → `security-check.yml` → github-actions.md の表の行と docs/notes/security.md の「道具と入れ方」「次回の手順」（ワークフローがあることと使い方）。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す。scratchpad で zizmor・pip-audit・履歴の走査をワークフローと同じ組で1回動かし、ワークフローの見込みの出力と合っていることを確かめる
3. 確かめと記録: `python3 scripts/regenerate.py all` と `python3 scripts/regenerate.py books_pages` の後の生成物の差分を見る（見込み: `all` は0、`books_pages` は0か JSON-LD の箇所だけ）。マージ（冒頭の行のとおり）→ cloudflare で `security-check.yml` を手動実行し、Job summary の要点と所要時間をログに書く。#598 にコメント（中-2・中-3 は直した、中-1 は受容＋平野さんの Rate limiting rule 待ち、中-4・中-5 と dependabot.yml は SEC-04〈WFL 段④の後〉、Claude Security は SEC-03、Observatory の採点と扱い、プラグインの LLM レビューは動かさない）。直した項目を本文の表で「済（CHAT-1018-SEC-02）」にしてよい（本文の書き換えは表の状態の列だけ）。上の「決定」を `docs/decisions/security.md` に足す。#300 にコメント

## 止まる条件
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数（中-3 の各スクリプトの該当関数、`scripts/lib/page.py` の `build_image_cell`・`esc` の周り、`assets/houou.js` の Ampai の箇所、`generate_books_pages.py` の JSON-LD、docs/notes/github-actions.md の表）を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）。`work/OUK` は `assets/houou.js` を改名する見込みなので、重なるなら `assets/houou.js` の直しだけ外し、#598 に「OUK のマージ後に改名後のファイルへ当てる」と書く
- `regenerate.py all` の後に差分が出た（一覧〈ページ・行・元の URL の値〉を書いて、マージせずに止まる。http/https でない URL がシートにあった場合で、直すのはシートか関数かの判断が要るため）
- `books_pages` の差分が JSON-LD 以外にも出た（スクリプトの直しはマージしてよいが、`books/` の生成物はコミットしない。差分の一覧を書く）
- 既存のテストが通らなくなり、直しで戻せない
- 履歴の走査で、除外の一覧に無い**有効と思われる鍵**が見つかった（値を書かずに SHA とファイル名だけ報告して止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- 止まらないこと: cloudflare での `security-check.yml` の手動実行がセッションから起動できない（平野さんに頼むリンクを書いて完了にする。「未確認の項目」に書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、中-3 で増えた箇所・外した箇所、pip-audit の組の置き場所（二重管理の有無）、運行表への出方、`security-check.yml` の初回の結果（zizmor の件数・pip-audit・履歴の走査）を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-SEC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-SEC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の最後の行は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- `CHAT-1018-SEC-02` のコミットは無し。識別子 `SEC` はこのセッションの SEC-01 で使ったもの（続きの指示なので重複確認の対象外）
- `work/1018-sec` はローカル・リモートとも無く、`git checkout -b work/1018-sec origin/cloudflare`（82a05e9a1）で作成
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 1. 調べる

- issue: #598 open（コメント0 → 着手中を書いた）、#9 open（10/17 に SEC-01 のコメント）、#300 open（コメント0）、#564 open（10/16 に段⑤まで。段④は未）
- 未マージの work/ ブランチと触るファイルの重なり（`git diff --name-only origin/cloudflare...<b>`）:
  - `work/OUK`・`work/WRC`: `assets/houou.js` を削除（2158 行すべて。改名）→ **`assets/houou.js` の直しは外す**（#598 に書く）。ただし Ampai の URL は `scripts/lib/ampai.py` の取り込みで弾くので、houou の生成 JSON 経由で JS に届く値も守られる
  - `work/1018-log-367-station`: `scripts/generate_resource_logs.py` の `get_info_cell`（restaurant_url を href に入れる関数）の引数と本体を変えている → **`generate_resource_logs.py:196` の restaurant_url は外す**。同じページの `x_url` は `build_image_cell`（page.py）を通るので守られる。`docs/notes/github-actions.md` は `convert-x-posts.yml` の行を変えている（別の行。止まらない）
  - ほかのブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp）は重なり無し
- 中-3 の対象を実物で確かめた（行番号は SEC-01 のとおり）。grep（`href="{…}"`）で増えた箇所: `generate_books_pages.py:383` の楽天の URL（API の応答のキャッシュ由来）→ 同じ扱いにした。ほかの変数の href は内部のパス・固定のホスト（`https://x.com/`・`WATCH_URL` など）＋ID・`rel()` で、対象外。mtsuku・study は `WATCH_URL.format(video_id)` で組み直しているので安全

### 2. 直す

- `scripts/lib/url.py` に `safe_url(value) -> str | None`（前後の空白を除き、scheme が http・https で netloc があるときだけ返す）。page.py ではなく別ファイルにしたのは、`lib/ampai.py` から page.py を読むと依存が増えるため
- 当てた箇所:
  - `scripts/lib/page.py` `build_image_cell`（video_live・video_en・jpml_test・rh_paifu の動画・resource_logs の X・saikyo_mens の6ページ）
  - `scripts/lib/ampai.py` `build`（`safe_url(text(url)) or ""`。jpml_pros と houou の生成 JSON の両方）
  - `scripts/lib/wayhome.py` `video_id_from_watch_url`（http・https でなければ動画 ID 無し＝今までの「視聴URLから動画IDを取り出せません」で生成が止まる。帰り道の「再生」・JSON-LD の contentUrl はすべてこの検査を通った URL）
  - `scripts/generate_saikyo_pages.py` `build_table_hero` の `elif`（回の URL の形でない値）。回の URL の分岐は `https://abema.tv/video/episode/` の前方一致で守られている
  - `scripts/generate_rh_paifu.py` の牌譜、`scripts/generate_rh_results_detail.py` の X、`scripts/generate_books_pages.py` の楽天
- 中-2: `generate_books_pages.py` の `build_json_ld` で `json.dumps(...).replace("<", "\\u003c")`（saikyo と同じ形）。houou・wayhome・mtsuku の3＋1か所（`</` だけ逃がす）はそろえなかった（範囲外で、触る行を増やさないため。#598 の低-6 のまま）
- テスト: `scripts/tests/test_safe_url.py`（7件）と `test_books_pages.py` に `SecurityTest`（2件）。**修正前のコードでは呼び出し側の6件が失敗し**（cloudflare の scripts を scratchpad に展開し、新しいテストと url.py だけを置いて実行）、修正後はすべて通る
- `python3 -m unittest discover -s scripts/tests`: 1369 件 OK。`node --test 'workers/ask/test/*.test.mjs'`: 36 pass
- コミット e9860c291

### 3. 再生成の確かめ（1回目）

- `python3 scripts/regenerate.py all`（5分33秒、rc=0）の後、差分は `jpml_pros.html`（瀬戸熊さんの最強戦 35→36回、宮内さんの桜花 20→21回）・`ouka_leagues_data.json`・`resource_logs.html`（489 行の追加）・`workers/ask/data.json` の4つ
- 原因の切り分け: origin/cloudflare の木を scratchpad に展開し、**修正前のコードで同じ4ページを生成**（`generate_jpml_pros.py`・`generate_ouka_leagues.py`・`generate_resource_logs.py`・`generate_ask_data.py`）→ 4ファイルとも作業ツリー（修正後のコード）の出力とバイト単位で一致。**差分はシート側の更新によるもので、修正による差分は0**。リンクが消えた箇所は無い。4ファイルは `git checkout -- <ファイル>` で戻した（この指示の範囲外。予約の再生成が拾う）
- `python3 scripts/regenerate.py books_pages`: 「書籍」シートの行数が想定外（95 行。`MIN_BOOKS` 150〜`MAX_BOOKS` 300）で止まり、生成できなかった（途中の出力は戻された）。今の `books/*.html` の JSON-LD に `<` を含むものは0件なので、直しを当てても差分は出ない見込み。`books/` はコミットしない（スクリプトの直しだけ）

## 報告

- 状態: 作業中
- ブランチ: work/1018-sec
- ログ: https://github.com/retroeater/mj/blob/work/1018-sec/docs/logs/CHAT-1018-SEC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-sec
- 確認用URL: なし
- マージ: 未
- issue: #598
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82a05e9a）: https://github.com/retroeater/mj-logs/tree/main/guide/82a05e9a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a05e9a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f218a9a2.md
