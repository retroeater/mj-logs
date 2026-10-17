# CHAT-1017-HOU-70

- 着手日時: 2026-10-17
- 対象issue: #541（関係: #586）
- ブランチ: work/1017-hou
- 着手時HEAD: 98ff33c91

## 指示

【Claude作成】Claude Code 向け指示：#541 の段1 — 鳳凰戦の A1・A2・鳳凰位を内部でも「通期」として持つ形に生成・画面・質問欄を直し、シートの「前後」が「後」でも「-」でも同じ結果になるようにする Chat-Ref: CHAT-1017-HOU-70 マージ: 承認済み（チャットで。2026-10-17 に平野さんが、この段1 を「見た目が変わらないことを確かめるだけ」の段として承認済みでマージする進め方に同意。ただしテストが通り、下の「止まる条件」に当たらないときだけ） 貼る時機: いつでも（新しいセッションは任意。HOU-69 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の作成と push と、cloudflare へのマージの push と、#541・#586 へのコメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
#541 を案2 で進める3段のうちの段1。「鳳凰」シートの A1・A2・鳳凰位（以下「通期のリーグ」）を、生成・成績データ・画面・質問欄の内部で「通期」として持つ形に直す。シートの C列「前後」が今の「後」でも、これから平野さんが書き換える「-」でも、同じ生成結果になるようにする。画面の見た目（文字）は変えない。
決定（2026-10-17、平野さん）

* #541 は案2: 内部も「通期」として持ち、成績データ・順位変動の期のキーと URL・質問欄・画面の JS を通期に合わせる。理由は、後で見たときに「なぜ後期扱いなのか」が分からなくなるのを避けるため（HOU-69 の案1〈読むときに「後」として扱う〉は採らない）
* 鳳凰位の行も、A1・A2 と同じく「-」にする（書き換えは A1 325・A2 421・鳳凰位 22 の 768 行。数は CHAT-1017-HOU-69 のログ「2. シートの今の値」）
* 旧ページ（旧リーグ推移 houou_leagues・旧順位変動 houou_race・`houou_results.js`・`check_leagues_dropped.py` など、#582 で後始末するもの）は案2 に合わせて直さない。読むときに「-」を「後」として扱うつなぎだけ入れ、出力は今と変えない
* 順位変動の古い URL（A リーグの `?term=42-2` の形）は読み替えない（公開したばかりで共有済みのリンクが無いため）
* 進め方は3段: 段1（この指示。「後」と「-」の両方を受け付け、内部は通期）をマージ → 段2（平野さんが 768 行を「-」に書き換える。1回でも分けてもよい）→ 段3（通期のリーグで「後」を受け付ける分岐と、画面の読み替え〈`assets/houou.js` の `termShort()`・`termLong()` ほか〉を外す）
* 段1 のマージは承認済み（冒頭の「マージ:」の行）

前提（チャット側。平野さんの決定ではない）

* 影響する所の一覧は CHAT-1017-HOU-69 のログ「3. 影響する所」の表が正。着手時の cloudflare で `git grep` し直し、増えた所があれば足す
* 内部の通期の印（成績 JSON の `half` の値、順位変動の期のキー〈`<期>-<1/2>` に対する通期の形〉、`workers/ask/data.json` の値）は実物に合わせて決めてよい。決めた形と理由をログに書き、`docs/notes/houou-top.md`（無ければ近い節）と `docs/decisions/houou.md` に書く
* 期を数える所（リーグのタブ・積み上げの人数・順位）では、今と同じく通期のリーグを前期・後期の両方に数える。今の `fill_front_half()`・`standings()`・`leaguePositions()` の「後期から前期へ写す」は、「通期を前期・後期の両方に出す」意味の名前と書き方にする（後期の行と誤解されない形）。数える結果は今と同じ
* jpml_pros の「鳳凰<最新の期>」の値と、Ampai のリンクは今と同じにする。Ampai のブックは通期のリーグも「前」「後」の2行を持つ（HOU-69 のログ「2.」）ので、通期のリーグは今と同じ「後」の行を引く（要確認。ほかに合う行の選び方が無いか確かめ、無ければこの形）
* 旧ページのつなぎは、旧ページだけが使う関数か、旧ページの生成の入口に置く。houou/ 側と共有する関数に「-」→「後」の変換を入れない（入れると内部が「後」に戻る）
* 通期のリーグの行で「前」が来たら止める（今のシートに0件。HOU-69）。「後」と「-」以外の値も止める
* `workers/ask/` の答えの期の書き方（`termLabel()`）は、データの通期の印から「第42期」と書く形にする。AI への指示（`ANSWER_SYSTEM`）を変える場合は、`docs/notes/ask-worker.md`「変更の指示を作るときの前提: 本番で送る固定の問い」の5問を、マージ後に本番で送って期待と比べる（送るのはこの5問だけ。1日の上限の範囲）。`ANSWER_SYSTEM` を変えない場合も、data.json の期の値が変わるので同じ5問を送る
* マージで生成スクリプトが変わるので、`regenerate-page.yml` が対象のページを作り直す。その時点のシート（名簿の退会など）の変化が生成物に混ざることがある

手順

1. 着手の確かめ: #541 に他のセッションの着手中のコメントが無いか確かめ、着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが、HOU-69 の「3.」の表のファイルの同じ関数を変えていないか、取り込みで衝突しないかを確かめてログに書く。`docs/notes/ask-worker.md` の固定の5問の節を読む。CHAT-1017-HOU-69 のログの `## 報告` の「状態」の行の末尾に `/ 続き: CHAT-1017-HOU-70` を足す。
2. 直す: 生成（`scripts/` の HOU-69 の表のもの）・成績データ・`assets/houou.js`・`assets/houou_race.js` のうち houou/ 側・`workers/ask/` を、前提のとおり通期を内部の値として持つ形にする。旧ページのつなぎを入れる。テストは「後」の入力と「-」の入力の両方で同じ結果になるもの、通期のリーグの「前」・ほかの値で止まるものを足す（`scripts/tests/`・`workers/ask/test/`）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。
3. 確かめる（マージの前）:
   * 今のシート（「後」）で生成し、cloudflare の生成物との差を種類に分けてログに書く。許すのは、通期のリーグの期の値（`half` など）・期のキー・順位変動の A リーグの URL の形・ask の data.json の期の値・それに伴う部品の名前の変更だけ。旧ページの生成物は差が無いこと
   * 「鳳凰」シートの通期のリーグの「前後」を手元で「-」に置き換えた入力（シートは変えない。読み込んだ後の値を置き換えるテスト用の経路で）で生成し、上の「後」の生成物と同じであること（旧ページも同じであること）
   * 作業ブランチのプレビューを headless Chrome で開き、cloudflare の本番と画面の文字を比べる。見る所: houou/ トップ、個人成績の白鳥翔（鳳凰位）・A1 の選手1人・A2 の選手1人・B リーグの選手1人のそれぞれ「推移」「期別」タブと吹き出し、リーグのタブ（最新の期と第22期）、ランキングの3つ、順位変動の A1 の最新の期と B1 の最新の期。違いが URL の形だけであることを書く（選んだ選手の名前を書く）
   * jpml_pros の A1・A2・鳳凰位の選手の「鳳凰<最新の期>」と Ampai のリンクが cloudflare と同じこと
   * 直した後の docs（`docs/notes/houou-top.md`・`docs/decisions/houou.md`・必要なら `docs/notes/ask-worker.md`）をコミットする
   * push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` でマージする。マージ後の `regenerate-page.yml` の結果と、本番で上の見る所のうち白鳥翔と A1 の選手の画面を確かめる。固定の5問を本番の質問欄に送り、答えを `## 経過` に書く。#541 に段1 の完了と、段2（平野さんの書き換え）に入ってよいことをコメントする（コメントの末尾に `Chat-Ref: CHAT-1017-HOU-70` の行）

止まる条件

* #541 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが HOU-69 の表のファイルの同じ関数を変えている、または取り込みで衝突する
* 「後」の生成物に、上で許す種類の外の差が出る（旧ページの差を含む）
* 「-」に置き換えた入力の生成物が、「後」の生成物と同じにならない
* 画面の文字が本番と違う（URL の形の違いを除く）。jpml_pros の値・Ampai のリンクが変わる
* Ampai のリンクを今と同じにする引き方が「後」の行以外に要る、または決められない
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の失敗は、今回の変更による失敗と無関係な失敗を分ける。無関係なら原因を報告に書き、残りの手順（本番の確かめ・固定の5問・#541 のコメント）を進めてよい。今回の変更による失敗なら、それ以上進めずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、決めた通期の印の形と、固定の5問で期待と違った答え（あれば）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-70.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-70 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。HOU-69 と同じセッション。origin/work/1017-hou はマージ済みで、ローカルの work/1017-hou も origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で進めた（既に最新）。雛形の行は揃っている。「指示」欄の末尾は指示文の最後の行と一致

### 1. 着手の確かめ

- #541 に 2026-10-17 02:30 UTC 以降のコメントは無い（他のセッションの着手中なし）。着手中のコメントを書いた
- 未マージのブランチ: work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes は HOU-69 の表のファイルを変えていない。work/1017-pad は `scripts/lib/page.py`（表に無い）だけ。**work/1017-amp（他のセッション、AMP-06、未マージ）が `scripts/generate_houou_pages.py` に新しい関数 `current_league_table()` を足し、jpml_pros の鳳凰戦の列を「現在のリーグ」の1列にしている**。表の関数そのものは変えていない（足すだけ）ので止まる条件には当たらないと判断した。新しい関数は `results.HALF_NUMBERS[r.half]` と `current_league()` を使うので、通期の印を `HALF_NUMBERS` に入れ（通期は 0）、`current_league()` も通期の行に合う形にして、後からマージされても動くようにした（マージの順で取り込みの衝突が出るかは、後でマージする側が確かめる）
- `docs/notes/ask-worker.md` の固定の5問の節を読んだ
- CHAT-1017-HOU-69 のログの状態の末尾に ` / 続き: CHAT-1017-HOU-70` を足した
- `git grep` で HOU-69 の表から増えた所: `generate_houou_pages.py` の `upcoming_term()`（AMP の進行中の期。「前後」で最新の期を決め、Ampai を引く）と `current_league()`・`champion_name()`、`assets/houou.js` の `currentLeague()`・`initRace()`、`assets/houou_race.js` の `normalize()`、`workers/ask/src/query.mjs` の `league()`

### 2. 直した所（5c56a4a9b）

決めた通期の形（`docs/notes/houou-top.md`「通期のリーグ」にも書いた）:

| 場所 | 形 | 理由 |
|---|---|---|
| 読む所 | `lib/houou_tab.py` の `term_half(リーグ, 前後)`: 通期のリーグ（鳳凰位・A1・A2）の「-」「後」は `SPAN`（「通」）、ほかは「前」「後」。通期のリーグの「前」・ほかのリーグの「-」などは None で、生成は止まる | 「後」と「-」を1か所で同じ値にする |
| 成績 JSON・`data.json` | 期の番号は 0（前期 1・後期 2）。`lib/results.py` の `HALF_NUMBERS`。並び（`HALF_ORDER`）は後期と同じ位置 | 1・2 と重ならない。行の順は前と同じ |
| 順位変動 | 期のキー `<期>-0`（`houou/race/42-0.json`、`data-index` の `"0"`）、URL `?term=42&league=A1` | 前期・後期と分け、URL は期だけで通期を表す |
| 数え方 | 前期・後期は前期・後期のリーグの行で決め、通期のリーグはその期の前期・後期の両方に数える | 結果は前と同じ |

直した関数:

- `lib/houou_tab.py`: `SPAN_LEAGUES`・`SPAN`・`SPAN_MARKS`・`HALVES`・`term_half()` を足した
- `lib/results.py`: `HALF_ORDER`・`HALF_NUMBERS` に通期。`standings()` を「通期を前期・後期の両方に数え、通期の行の値はその期の最後の単位（後期）の数」に書き直した（前の「前期に後期の単位を写す」をやめた）
- `lib/ranking.py`: `HALF_ORDER` に通期、`latest_period()` は通期の行で前期・後期を決めない
- `lib/pro_stats.py`: `fetch_sources()` で「鳳凰」の前後を `term_half()` に。`in_half()` を足し、`contest_stats()`・`_latest()` は通期の行が最新の期の前期・後期のどちらにも当たる形。`periods()` は通期の行で最新の期を決めない。Ampai は最新の期の前後の行を引く（今は 43後。Ampai のブックは通期のリーグも前・後の2行を持つので、ほかの引き方は要らない）
- `generate_houou_pages.py`: `load_rows()`（`term_half()`、止める理由の文言）、`load_groups()`、`term_range()`（通期で前期・後期を決めない）、`leagues_data()`（書き直し。`fill_front_half()` を使わず、通期を両方に数える）、`upcoming_term()`（最新の期は前期・後期のリーグの行で決め、通期の行はその期に当たる）、`champion_name()`（期だけで比べる）、`in_term()` を足して `current_league()` に使う、`render_race()`（説明文の期の範囲は通期のキーを除く）、`prepare()` は `load_periods(half_of=race_page.term_half)`
- `generate_houou_race.py`: `HALVES` に通期（0）。`term_half()`（houou/ 用）と `legacy_half()`（旧ページだけ。「-」を「後」に戻す）を足し、`build_periods()`・`load_periods()` は `half_of` を引数で受ける。`pick_default()` は通期のキーを後期と同じ位置で比べる。旧ページの `main()`・`run_check()` は `legacy_half`
- `generate_houou_leagues.py`: `legacy_load()`（旧ページだけ。`load()` の通期の「-」を「後」に戻す）を足し、`main()`・`run_check()` が使う。`check_leagues_dropped.py` も houou は `legacy_load`（`analyze()` に `load` の引数）
- `assets/houou.js`: `SPAN_HALF = 0`、`inTerm()` を足して `currentLeague()` に使う、`leaguePositions()` は通期の行をその期の後期と前期の両方に置く（前期の側は span の印。前の「後期の行を前期にも写す」の書き方を変えた）、`initRace()` は `?term=42` を通期として読み、A リーグの URL は `?term=<期>` にする。`termShort()`・`termLong()` は段3 まで残す（説明の行だけ直した）
- `assets/houou_race.js`（houou/ 側）: `normalize()` は通期（`'0'`）から前期・後期のリーグへ移るとき後期を先に選ぶ（前と同じ動き）。旧 `houou_race.js` は変えていない
- `workers/ask/src/query.mjs`: `SPAN_HALF = 0`。`raceUrl()` は通期を `?term=<期>`、`league()` は通期のリーグで前期・後期の推測の注記を出さない。`termLabel()` は前から期の番号 0 で「第42期」と書く。`ANSWER_SYSTEM` は変えていない

テスト（足した・直したもの）: `test_houou_tab.TermHalfTest`（「-」「後」→ SPAN、「前」・ほかは None）、`test_houou_check.PagesCheckTest.test_span_league_half_dash_and_back_are_the_same`（`load_rows()` で「後」と「-」が同じ行、「前」と B1 の「-」は止まる）、`test_houou_race` の `test_span_league_half_dash_and_back_are_the_same`・`test_span_league_front_half_stops`（houou/ は `42-0`、旧ページは `42-2` のまま、「後」と「-」で同じ）、`test_pro_stats.AmpaiTest.test_span_rows_match_any_half`（Ampai のリンク・最新の期のリーグが「後」と同じ）。期待値を直したもの: `test_houou_race` の通期のキー（`42-0`・`43-0`、進行中の止める文言の「第43期 A1」）、`test_houou_pages` の `standings()`（通期の行の値は後期の数）、`workers/ask/test/query.test.mjs`（通期の行の期の番号 0、URL `?term=43&league=A2`、注記なし）。足したテストは直す前のコードには無い関数・引数（`term_half`・`half_of`・SPAN）を使うので、直す前では通らない。
`python3 -m unittest discover -s scripts/tests` 1307件 OK、`node --test 'workers/ask/test/*.test.mjs'` 36件 pass（全体の1回目で `test_houou_ask.GeneratedRosterTest` が1回だけ ERROR、単独と2回目の全体では通った。生成物の書き出しと並んで node を呼んだときの一時的なものと見ている）

### 3. 確かめ（マージの前）

**「後」の生成と cloudflare の差**: シートの変化を混ぜないよう、origin/cloudflare のコードを scratchpad に展開し（`git archive`）、同じ時刻に生成スクリプト（houou_pages・ask_data・jpml_pros・houou_race・houou_leagues）を直接流した（base）。base と HEAD のコミット済みの生成物の差は 0 件（シートの変化は無かった）。手元の生成と base の比較:

| 種類 | 件数 |
|---|---|
| 同じ（旧 houou_race・houou_race/・houou_leagues・houou_leagues_data.json、jpml_pros.html、ランキング9部門、houou/ のトップ・個人成績のページ、players/leagues.json、ask-players.json ほか） | 690 ファイル |
| `houou/results/*.json`: 通期の行の期の番号 2→0 だけ | 59 |
| `houou/search.json`: 最後の期が通期の選手の期のキー `-2`→`-0` だけ | 1（30名分） |
| `workers/ask/data.json`: 通期の行の期の番号 2→0 だけ | 1 |
| `houou/race/*.json`: `<期>-0` が20ファイル増え（41→61）、`<期>-0` を `<期>-2` の先頭に戻すと base と同じ | — |
| `houou/race/index.html`: `data-index` の通期の `"0"` だけ（`"0"` を `"2"` に戻すと同じ。`data-default` は同じ `43-1 B1`、ほかの HTML も同じ） | 1 |

旧ページの生成物（houou_race.html・houou_race/・houou_leagues.html・houou_leagues_data.json）と jpml_pros.html（「鳳凰<最新の期>」と Ampai のリンク）は base と同じ。

**「-」に置き換えた入力**: 手元の作業ツリーを scratchpad に写し（`git ls-files`）、「鳳凰」を読んだ直後に通期のリーグの「前後」を「-」に置き換える比較用の経路（`lib/sheets._query` を差し替える、scratchpad、コミットしない）で同じ5本を流した。置き換えは1回の読み込みごとに 768 行。「後」の生成物と比べて、813 ファイルすべて同じ（旧ページを含む）

**テスト**: `python3 -m unittest discover -s scripts/tests` 1307 件 OK、`node --test 'workers/ask/test/*.test.mjs'` 36 件 pass。

**画面の文字（プレビュー 5c56a4a9b と本番）**: headless Chromium で `main` の文字を集めて比べた（scratchpad の screens.cjs）。見た所: houou/ トップ、ランキング3つ（通算得点・期最高得点・節単位浮き率）、順位変動の B1 最新（`?term=43-1&league=B1`）と A1 最新（本番 `?term=42-2`、プレビュー `?term=42`）、個人成績の白鳥翔（鳳凰位）・佐々木寿人（A1）・二階堂亜樹（A2）・一井慎也（B1）の期別（期のカードを1枚ずつ開いた文字も）・通算（推移の吹き出しをマウスで横に押して集めた）・リーグのタブ（矢印キーで全部の期を送った吹き出し。第22期前期・後期と最新の43期〈進行中〉、aria-label を含む）・部門別。違いは順位変動 A1 の URL の形（`?term=42-2` と `?term=42`）だけで、ほかの文字・吹き出しはすべて同じ。ページのエラーは両方 0。プレビューで古い URL `?term=42-2&league=A1` を開くと `?term=42&league=A1` に移り、第42期 A1 を出す（houou-top.md の記述のとおり）

### 4. マージと本番

- マージの前に origin/cloudflare を2回取り込んだ（衝突なし。ほかのセッションの docs・live/・title/ の生成と scripts の変更）。取り込んだ後のコードで houou_pages・houou_race・houou_leagues・jpml_pros・ask_data を生成し直した。差は 0 件だった。テストは 1328 件 OK、node は 36 件 pass
- 6f7b8b944 を `git push origin work/1017-hou:cloudflare` でマージした（直前に再 fetch し、`merge-base --is-ancestor` を確かめた）
- `regenerate-page.yml` は success。続きのコミット 0d31d5ca4（GitHub Actions の regenerate）は live/ ほかを変えたが、houou/・jpml_pros.html・workers/ask は変えていない
- Workers Builds:
  - `mj` は 0d31d5ca4 で success
  - `mj-ask` は f80880a67（ほかのセッションのマージ。今回の変更を含む）で success
- 03:55 UTC に本番の `houou/race/42-0.json` が 200 を返した
- 本番の画面を screens.cjs で確かめた（白鳥翔・佐々木寿人、トップ・ランキング3つ・順位変動 A1/B1 を含む）。プレビューの文字とすべて同じで、ページのエラーは 0。順位変動 A1 の URL は `?term=42&league=A1`

**固定の5問（本番の質問欄、2026-10-17 UTC）**:

| 時刻 | 問い | 答え | 期待との比べ |
|---|---|---|---|
| 03:57:11 | 現在の鳳凰位は？ | 現在の鳳凰位は白鳥翔さん(23期)です。第41期と第42期の鳳凰位を獲得しています。 | 期待どおり（ほかの人・人数は付かない） |
| 03:57:15 | 第1期から第10期の鳳凰位は？ | 島田昭（第1期・第4期）、安藤満（第2期・第5期・第6期・第10期）、ロッキー堀江（第3期）、椎名大介（第7期）、荒正義（第8期）、伊藤優孝（第9期）の6人 | 期待どおり（6人、古い期の順） |
| 03:57:19 | 鳳凰位になった人は全部で何人？ | 23人。白鳥翔、佐々木寿人、HIRO柴田、藤崎智、吉田直、前原雄大、勝又健志、前田直哉、瀬戸熊直樹、荒正義の10人で、ほか13人 | 期待どおり |
| 03:57:22 | 通算得点の上位15人は？ | 1位 HIRO柴田（2074.4）〜10位 福光聖雄（1315.8）、ほか5人を含め全15人。出典「鳳凰戦 ランキング（通算）」 | 期待どおり |
| 03:57:26 | 白鳥翔の第40期の成績は？ | A2リーグで1位となり昇級しています。獲得ポイントは278.8です。 | 期待どおり（期は「第40期」、人数は付かない） |

- #541 にコメントした: 段1 の完了と、段2 に入ってよいこと（https://github.com/retroeater/mj/issues/541#issuecomment-6229342886）

## 報告

- 状態: 完了
- ブランチ: work/1017-hou
- ログ: https://github.com/retroeater/mj/blob/work/1017-hou/docs/logs/CHAT-1017-HOU-70.md
- 比較URL: https://github.com/retroeater/mj/compare/6f7b8b944~1...6f7b8b944
- 確認用URL: なし
- マージ: 済み（6f7b8b944、regenerate-page.yml は success）
- issue: #541（段1 完了のコメント済み。issue は開いたまま。段2 は平野さんのシートの書き換え）
- 判断が必要なこと:
  - 通期の印の形（決めたもの）:
    - 内部では「通」（`houou_tab.SPAN`）
    - 成績の JSON では期の番号 0（前期 1・後期 2）
    - 順位変動では期のキーを `<期>-0`、URL を `?term=<期>` にした
  - 古い URL `?term=42-2&league=A1` について:
    - 読み替える処理は足していない
    - 開くと、今の既定の選び方で `?term=42&league=A1` に移る
  - 固定の5問で期待と違った答え: なし
- 未確認の項目:
  - 段2（シートの 768 行を「-」に書き換える）の後の本番の生成。シートは「-」に置き換えた入力で同じになることだけ確かめた
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f3633dc8）: https://github.com/retroeater/mj-logs/tree/main/guide/f3633dc8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
