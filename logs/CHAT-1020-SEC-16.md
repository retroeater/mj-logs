# CHAT-1020-SEC-16

- 着手日時: 2026-10-20
- 対象issue: #598
- ブランチ: work/1020-sec16
- 着手時HEAD: 768cd082e

## 指示

【Claude作成】Claude Code 向け指示：再点検（SEC-14）の生成とフロントの指摘（中-3 の残り・低-6・低-7・低-8 後半）を直す
Chat-Ref: CHAT-1020-SEC-16
マージ: 承認済み（チャットで）。条件は「止まる条件」「手順3」のとおり（全ページの再生成の差分が、直す前の生成と比べて0件であること）。変えるのは下の「対象」のスクリプト・JS の箇所、足すテスト、docs（`docs/decisions/security.md`・`docs/notes/security.md`・`docs/logs/`）だけ。`workers/`・`_headers`・`.github/`・生成物（再生成で差分0のはず）は変えない
貼る時機: いつでも（CHAT-1020-SEC-15 はマージ済み）。JST 03:45〜05:15 と月曜 05:00〜07:00 は cloudflare への push をしない（当たったら終わるまで待ってよい。CLAUDE.md の15分の上限の例外）。新しいセッション: 任意（SEC-15 の続きでも可。headless Chrome を使うので Playwright が動くセッションで）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-sec16 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1020-sec16 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-sec16 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。続いて CHAT-1020-SEC-15 のログ（`docs/logs/CHAT-1020-SEC-15.md`）の `## 報告` の状態の末尾に ` / 続き: CHAT-1020-SEC-16` を足してコミットする（この指示は SEC-15 の判断待ちへの回答を含む）。

## 目的
再点検（CHAT-1019-SEC-14、#598）で残った生成スクリプトとフロントの JS の指摘を直す。今のデータでは生成物は変わらない見込みで、今後シートに危ない値が入ったときの守りを足す。

### 決定（2026-10-20、平野さん）
- 低-8 後半（`scripts/generate_jpml_pros.py` の選手リンクの `?name=`）は、`&`・`#`・`%`・`+`・空白だけを逃がし、日本語はそのまま残す（全部を `%E6…` の形にはしない。jpml_pros.html の 671 件のリンクを変えないため）
- `workers/ask/src/index.mjs` の 404・405 の応答に `x-content-type-options: nosniff` が無い件は、この指示に入れず、次に `workers/ask` を変える指示で一緒に直す（外すきっかけとして docs/decisions に書く）
- この指示のマージは承認済み（条件付き）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 対象と今の行（CHAT-1020-SEC-15 のログの `## 経過`「SEC-16 のための下調べ」。要確認）:
  - 中-3 の残り: `scripts/generate_resource_logs.py:165`（店名の URL に `scripts/lib/` の http/https 以外をリンクにしない共通の関数〈中-3 で作った `safe_url()` 等〉を通す）、`assets/league.js:856`（`hit.href = up.url` の前に `https?:` を確かめる）
  - 低-6: `scripts/generate_houou_pages.py:369`（`json_script()`）・`scripts/generate_video_wayhome.py:190`・`scripts/generate_wayhome_episodes.py:184`・`scripts/lib/mtsuku.py:426`（JSON-LD）で、埋め込む JSON の `<` をすべて `<` にする（`</` だけでなく）。共通の関数が既にあればそれにそろえ、無ければ `scripts/lib/` に1つ置いて4か所から使う
  - 低-7: `assets/houou_ask.js:229`（出典の判定を `new URL(u, location.origin).origin` で行う）・`assets/saikyo.js:73`（`decodeURIComponent` を try で囲む）・`assets/league_race.js:338`（CSS の `url()` に入れる値を `JSON.stringify` で埋める、または `https?:` を確かめる）
  - 低-8 後半: `scripts/generate_jpml_pros.py:178-180`（上の決定のとおり。`urllib.parse.quote(name, safe=…)` などで日本語を残す）
- 再生成の比べ方（シートの変化を混ぜないため）: 直す前の作業ツリーで `python3 scripts/regenerate.py all` を回して生成物を退避（コミットしない）し、直した後に同じく回して、2つの生成物どうしを比べる。この差分が0件であることをマージの条件にする。cloudflare との差分には、データの変化による生成物の差分が混ざってよい（ログに種類ごとの件数を書く）
- JS の確かめ: 作業ブランチのプレビュー（または `file://` でなくローカルの HTTP サーバ）で、houou/（質問欄の出典の表示）・saikyo.html・houou_race のページ・league.js を使う大会のページを headless Chrome で開き、コンソールのエラーが直す前と比べて増えないこと
- テスト: Python の直しには `scripts/tests/` に、危ない値（`javascript:` の URL、`</script>` や `<` を含む文字列、`&`・`#`・空白を含む名前）で期待どおりになるテストを足す

## 手順
1. 確かめる: SEC-15 のログの下調べと今の行が合うか（ずれていれば今の行をログに書いて進む。箇所自体が無い・もう直っていれば、その項目だけ外してログに書く）。`git branch -r --no-merged origin/cloudflare` の各ブランチが対象のファイルの同じ関数・同じ行を変えているか。#598 に着手中のコメントを書く。直す前の生成物を退避する（上の前提）
2. 直す: 中-3 の残り・低-6・低-7・低-8 後半を直し、テストを足す。`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'` を回す。直した後の生成物を作り、直す前と比べる（差分0）。JS は上の前提のとおり headless Chrome で確かめる。docs（上の決定、`docs/notes/security.md` の該当の観点〈(b)・(c)〉に今回の形を一句ずつ）を直す
3. マージと確かめ: origin/cloudflare を取り込み直し、テストと生成の比べをやり直して差分0を確かめてからマージする。Workers Builds の成否（`docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」）と、本番の houou/・jpml_pros.html・resource_logs.html が 200 で開くことを確かめる（URL に `?v=<値>`）。#598 に結果（直した箇所・テスト・差分0の確かめ）をコメントし、SEC-14 の指摘の表で残りが無くなれば、#598 を閉じてよいかを「判断が必要なこと」に書く（閉じない）

## 止まる条件
- 未マージの work/ ブランチが対象のファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（重なる項目だけ外して残りを進めてよく、外したものと理由をログに書く。全部が重なるときだけ止まる。`docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
- 直す前と直した後の生成物の比べで差分が1件でも出た（マージせずに、差分の種類・件数・例をログに書いて止まる）
- テストが1つでも落ちる（自分の変更で落ちると分かっている既存のテストは、直す理由をログに書いて直してよい）
- headless Chrome でコンソールのエラーが直す前より増えた（マージせずに止まる）
- マージ後の Workers Builds が失敗した、または本番のページが 200 で開かない: 今回の変更による失敗なら、マージのコミットを revert して push・マージし（承認済み）、止まる。無関係な失敗なら原因をログに書いて残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-SEC-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-SEC-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致。
- Chat-Ref 重複なし（`git log --all --grep`）。`work/1020-sec16` はローカル・リモートとも無し → `git checkout -b work/1020-sec16 origin/cloudflare`。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手 2026-10-20 08:15 JST（push を止める時間帯の外）。
- SEC-15 のログの状態を「判断待ち / 続き: CHAT-1020-SEC-16」にしてコミット（ecdc6c16d）。

### 手順1（確かめ）

- 対象の今の行は SEC-15 の下調べと一致: `scripts/generate_resource_logs.py:165`・`assets/league.js:856`・`scripts/generate_houou_pages.py:369`・`scripts/generate_video_wayhome.py:190`・`scripts/generate_wayhome_episodes.py:184`・`scripts/lib/mtsuku.py:426`・`assets/houou_ask.js:229`・`assets/saikyo.js:73`・`assets/league_race.js:338`・`scripts/generate_jpml_pros.py:178-182`。どれもまだ直っていない
- `<` を `<` にする共通の関数は無かった（`generate_books_pages.py:465`・`generate_saikyo_pages.py:697,1041` はその場で `.replace`）
- 未マージの work/ ブランチで対象のファイル（上の10と `scripts/lib/url.py`）を変えているものは無い（`git diff --name-only origin/cloudflare...<各ブランチ>` が空）
- #598 に着手中のコメント（issuecomment-6279562947）
- このセッションからシートを読めるかを `regenerate.py resource_logs` で確かめた（8 秒、成功）。`docs/notes/sheets-reader.md` の Sheets API への切り替えの後もまだ読めている
- 直す前の生成: ecdc6c16d で `python3 scripts/regenerate.py all`（約 7 分、終了コード 0）。`git status` は空（生成物が HEAD と一致）。`books_pages` は凍結中で `all` から外れる（`FROZEN_FROM_ALL`。今回の直しは books に触れない）。ログの「表示の値が想定外」の警告 3770 行は既存のデータの警告（ask_data・live_pages）。作業ツリー全体（`.git` を除く、152 MB）を scratchpad の `sec16/before/` に退避した

### 手順2（直し）

- 低-6: `scripts/lib/page.py` に `script_json(data, separators=None)`（`json.dumps(…, ensure_ascii=False)` の後 `<` をすべて `<`）を置き、`generate_houou_pages.py` の `json_script()`・`generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`lib/mtsuku.py` の `json_ld_html()` から使う。4ファイルとも既に `lib.page` を読み込んでいた。使われなくなった `import json` を3ファイルから外した
- 中-3 の残り: `generate_resource_logs.py` の `get_info_cell()` で `restaurant_url = safe_url(restaurant_url)`（http・https 以外はリンクにせず店名だけ）。`assets/league.js` は `if (up.url)` を `if (/^https?:\/\//i.test(up.url || ''))` に（外れれば今までの「URL が無い」側の表示）
- 低-7: `assets/houou_ask.js` に `sameOrigin()`（`new URL(url, location.origin).origin === location.origin`、例外は false）を足し、出典の判定を `startsWith('/') && sameOrigin(…)` に。`assets/saikyo.js` の `decodeURIComponent(location.hash…)` を try で囲む（壊れたハッシュは移さない）。`assets/league_race.js` の `url("${p.img}")` を `url(${JSON.stringify(p.img)})` に
- 低-8 後半: `generate_jpml_pros.py` に `QUERY_ESCAPES`（`str.maketrans`、`%`・`&`・`#`・`+`・空白・タブ・改行を `%XX` に）を置き、`get_internal_link()` の `?{param_name}=` の値に当てる。日本語は変えない（`會田亮介` はそのまま、`A&B #1 50%+x` は `A%26B%20%231%2050%25%2Bx`）
- テストを足した: `scripts/tests/test_script_json.py`（新規。`</script>`・`<!--<script>`・`a<b`・日本語で `<` が残らず `json.loads` で同じ値に戻ること、separators、`json_script()`・`json_ld_html()` の出力）、`test_safe_url.py` に resource_logs の `javascript:` をリンクにしない／https はリンクにするテスト、`test_jpml_pros.py` に `InternalLinkTest`（日本語はそのまま・`&#%+空白` を逃がす・HTML の逃がしは残る）
- 直す前のコードで新しいテストを回した（退避した `before/scripts` に新しいテストを写して実行）: test_script_json は `script_json` が無く読み込みで失敗、resource_logs と jpml_pros の `&#` のテストは値が違って失敗。直した後は通る
- テスト: `python3 -m unittest discover -s scripts/tests` 1457 件 OK、`node --test 'workers/ask/test/*.test.mjs'` 83 件すべて通る。JS 4ファイルは `node --check` で構文 OK
- 直した後の生成: `regenerate.py all`（約 6 分、終了コード 0）。退避した直す前の作業ツリーと `diff -rq` で比べ、違うのは自分が変えたスクリプト・JS・テストの14ファイルと新しいテスト1つだけ。**生成物の差分は0件**（`git status` も変えたソースだけ）
- headless Chrome（Playwright 1.56.1、`/opt/pw-browsers/chromium-1194`）で、直す前の写し（127.0.0.1:8801）と直した後（:8802）を `python3 -m http.server` で配り、同じページを開いてコンソールのエラーを数えた。外部への要求は Playwright で止め（abort）、`/api/ask`・`/api/ask/status` は Playwright の中で偽の応答を返した（本番には送っていない）
  - エラーの数（前 → 後）: `/houou/` 1→1・`/ouka/` 1→1・`/jwrc/` 1→1・`/wrcr/` 1→1・`/houou/race/` 16→16・`/ouka/race/` 17→17・`/saikyo/2026/20260816-s1-a.html` 24→24・同じページの `#%E0%A4%A`（壊れたハッシュ）25→24（`pageerror: URI malformed` が消えた）・`/jpml_pros.html` 44→44・`/resource_logs.html` 27→27。増えたものは無い（残りは止めた外部の画像・スクリプトの読み込みの失敗で、前後で同じ文）
  - 出典の表示（質問欄に偽の答えを返し、出典に `/houou/`・`/\evil.example/`・`//evil.example/`・`javascript:alert(1)` を入れた）: 直す前は `/houou/` と `/\evil.example/` の2つがリンクになり、直した後は `/houou/` だけ（4大会とも同じ）
  - league.js の Ampai のリンク（`houou/players/?name=HIRO柴田`・`阿久津翔太`）: 前後とも `a.mj-houou-term-hit` が1つで `https://app.ampai.jp/…` を指し、エラーは前後とも2
  - Security Guidance の hook が確かめの scratchpad の script（Playwright の `page.evaluate`）に eval の注意を出したが、ブラウザの中でテストの手順を動かす API でリポジトリのコードではない
- docs: `docs/notes/security.md` の (b) に `safe_url()`・`script_json()`・クエリの名前の逃がし方を、(c) にデータ由来の URL の確かめ方と `decodeURIComponent` の try を一句ずつ足した。`docs/decisions/security.md` に `## 2026-10-20（CHAT-1020-SEC-16）` を足した
- コミット: f12d67359（fix）・9f0913d70（docs）

## 報告

- 状態: 作業中
- ブランチ: work/1020-sec16
- ログ: https://github.com/retroeater/mj/blob/work/1020-sec16/docs/logs/CHAT-1020-SEC-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-sec16
- 確認用URL: なし
- マージ: 未
- issue: #598
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 768cd082）: https://github.com/retroeater/mj-logs/tree/main/guide/768cd082

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/768cd082/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e7c8d9e0.md
