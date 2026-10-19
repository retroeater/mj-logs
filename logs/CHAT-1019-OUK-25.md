# CHAT-1019-OUK-25

- 着手日時: 2026-10-19
- 対象issue: #582
- ブランチ: work/1019-ouk25（origin/cloudflare から作成）
- 着手時HEAD: d8b213689

## 指示

【Claude作成】Claude Code 向け指示：#582 の旧ページ（鳳凰戦4・女流桜花3・WRC 2）の生成を止めてファイルを消し、共有の旧 JS と旧ページ用のつなぎを片付ける Chat-Ref: CHAT-1019-OUK-25 マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが #582 を全部まとめて今行うことを決めた。見た目の変わるページは無い） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk25 の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-ouk25 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて `git branch -r --no-merged origin/cloudflare` を見て、未マージの work/ ブランチが下で消す・直すファイルを変えているかを書く（同じファイルの別の箇所なら止まらずログに書く。同じ行・同じ関数なら止まる）。 着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
#582 の後始末。houou/（#540）・ouka/（#591）・jwrc/（#599）の公開で、旧ページは `_redirects` で新しいページへ 301 になっている。残しているファイルと生成、旧ページだけが使う JS・関数を片付ける。#582 の本文と3件のコメント（CHAT-1017-HOU-71・CHAT-1019-OUK-16・CHAT-1019-WRC-12）が対象の一覧なので、まずそれを読む。
決定（2026-10-19、平野さん）

* 範囲は #582 の全部（鳳凰戦の旧4ページ・女流桜花の旧3ページ・WRC の旧2ページと、共有の旧 JS）を1回で行う
* 時期: 今行う。#582 の「Search Console で旧ページへの流入が 301 に移ったことを見てから」という時期の決定（2026-10-14、docs/decisions/houou.md）は改める。理由: 旧ページの URL はすでに 301 でファイルは配信されず、戻すときは git の履歴から戻せるため
* `_redirects` の旧ページの行（鳳凰戦4・女流桜花3・WRC 2）と、転送先の `ouka/leagues/` などの中継のページは残す（docs/notes/redirects.md の四半期の棚卸しで扱う）

前提（チャット側。#582 の一覧から。実物に合わせて変えてよい）

* 消す（#582 の一覧のとおり。実物で使われているかを grep で確かめてから消す）:
   * 鳳凰戦: `houou_ranking.html`・`houou_leagues.html`・`houou_race.html`・`houou_results.html`、`houou_leagues_data.json`・`houou_race/`・`houou_results.js`・`houou_race.js`
   * 女流桜花: `ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html`、`ouka_leagues_data.json`、旧ページだけが使う JS、`generate_ouka_leagues.py`（新しい ouka/ の生成が使っていなければ）
   * WRC: `wrc_results.html`・`wrc_ranking.html`・`wrc_results.js`
   * 共有: `league_ranking.js`・`leagues.js`（旧ページがすべて消えた後に、ほかに読むページが無いことを確かめてから。JWRC の分岐も一緒に消える）
* 生成を止める: `scripts/regenerate.py` の `houou_leagues`・`houou_race`・`ouka_leagues` の対象。`--changed` の対応表・`SHARED_GENERATORS` などの参照も直す
* 旧ページ用のつなぎを消す: `generate_houou_race.py` の `legacy_half()` と呼び出し、`generate_houou_leagues.py` の `legacy_load()` と呼び出し（CHAT-1017-HOU-71 のコメント）
* 共有の関数: `generate_houou_pages.py` が `generate_houou_race.py`・`generate_houou_leagues.py` の部品を使っている。モジュールの移動（`scripts/lib/` へ）は、旧ページの入口（`main()` など）を消した結果ファイルが部品だけになる場合に限り、最小で行う。移すか残すかと理由をログに書く（「プロ」タブの列記号の読み方の件は、今回触らずにログに残すだけでよい）
* 整理: `scripts/apply_page_meta.py`・`scripts/check_leagues_dropped.py` の旧ページの行（`check_leagues_dropped.py` が旧ページにしか意味が無いなら、新しいページの生成物で同じ確かめができるか見て、できなければ消す。どちらにしたかを書く）、`style.css` の旧ページだけの規則（`.mj-race` のうち `.mj-houou-race` でないもの、`houou_leagues.html / ouka_leagues.html` の節など。新しいページが使っていないことを確かめてから）、テストの旧ページの参照、サイトマップ・`llms.txt`・サイト内のリンク（旧ページへのリンクが残っていれば新しいページへ替える）
* 文書: `docs/notes/static-generation.md`（ページの一覧の件数、`wrc_results.js`・`league_ranking.js` の記述）、`docs/notes/houou-race.md`、`docs/notes/ouka-top.md`、`docs/decisions/houou.md`（ファイルを残す決定と時期の決定を、今日の決定に改める）を直す
* `.github/workflows/` は変えない（変える必要があれば止まって書く）

手順

1. 消す・止める・直す。消す前に、各ファイルを読むページ・スクリプト・テストを grep し、ログに表で書く（ファイル → 読んでいた所 → どうしたか）
2. `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、生成物の差分が「消したファイル」と「サイトマップ・`llms.txt` の旧ページの行（あれば）」だけで、houou/・ouka/・jwrc/・jpml_pros ほかのページの差分が0であることを確かめる。容量の検査（`check-assets.yml` と同じもの）を手元で回す
3. プレビューで、houou/・ouka/・jwrc/ の各トップ・個人成績・ランキング・順位変動（jwrc/ はあるページ）を 375px で開き、JS のエラーが0であることを headless Chrome で確かめる
4. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く
5. 本番の確かめ: 旧9ページの URL（`?name=` 付きのものを含む。docs/notes/redirects.md の例）が今までどおり 301 で新しいページへ移ること、新しい3大会のページが 200 で JS のエラーが0であることを書く
6. #582 に結果をコメントしてクローズする（末尾に `Chat-Ref: CHAT-1019-OUK-25`）

止まる条件

* 消すつもりのファイルを、新しいページ（houou/・ouka/・jwrc/・jpml_pros ほか）が読んでいる、かつ読まない形に直せない
* 新しいページの生成物に差分が出て直しで消せない。テストが落ちて直せない
* 未マージの work/ ブランチが、消す・直すファイルの同じ行・同じ関数を変えている
* マージ後に本番の houou/・ouka/・jwrc/ や旧ページの 301 が壊れたときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、消したファイルの数と一覧、止めた生成、移した・残した部品とその理由、マージのコミットの SHA、本番の確かめの結果を書く。「判断が必要なこと」に、仮置きしたこと（`check_leagues_dropped.py` の扱い、部品の移動など）を書く
* 「WRC への引き継ぎ」に、WRC の旧2ページと `league_ranking.js` の JWRC の分岐を消したこと（コミットの SHA）を書く（平野さんが WRC のチャットへ伝える）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-25.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-25 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし（`git log --all --grep`・`docs/logs/`）。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている（1段落にまとまって貼られた）
- work/1019-ouk25 を origin/cloudflare（d8b213689）から作った
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）と、この作業で消す・直すファイルの重なり:

| ブランチ | 重なるファイル | 同じ行・同じ関数か |
|---|---|---|
| work/WRR | `scripts/regenerate.py`・`scripts/generate_houou_pages.py`・`scripts/generate_ask_data.py`・`style.css`・`docs/handover.md`・`docs/notes/static-generation.md`・`docs/notes/ask-worker.md`（ほかに `docs/decisions/houou.md`） | コードは別の箇所（`git merge-tree` で自動で混ざる）。`static-generation.md` の「ページの一覧」の件数の段落だけ同じ行に WRR が追記していたため、その行は変えず、件数の注記を見出しの前の段落に置いた（下の「文書」）。`docs/decisions/houou.md` の末尾の衝突は origin/cloudflare と WRR の間ですでにある（この作業とは別） |
| work/1017-lvp | `style.css` | 別の箇所（saikyo/・video の節）。自動で混ざる |
| ほか6本 | なし | — |

- WRR とは意味の上の重なりがある: WRR の `regenerate.py` の `SHARED_GENERATORS` の `wrcr_pages` に `"houou_race"` が入っている。この作業で `houou_race` は生成の対象でなくなる（部品は `scripts/lib/houou_race.py`）ので、WRR を取り込むときに直す必要がある（報告の「WRC への引き継ぎ」）

### 1. 消す前の grep（ファイル → 読んでいた所 → どうしたか）

| ファイル | 読んでいた所 | どうしたか |
|---|---|---|
| `houou_ranking.html`・`houou_results.html` | `_redirects`（301 の行）、文書、`docs/gsc/` の CSV。`houou_results.html` は `generate_houou_pages.py`・`lib/results.py` のコメント | 消した。`_redirects` の行は残す |
| `houou_leagues.html` | `check_leagues_dropped.py`・`generate_houou_leagues.py`・`regenerate.py`、`test_jpml_pros.py`（無いことの確かめ）、`style.css` | 消した。生成と `regenerate.py` の対象を消した |
| `houou_race.html` | `assets/league_race.js`（コメント）、`generate_houou_pages.py`・`generate_houou_race.py`、`scripts/promo_video/houou_race/`、`regenerate.py`、`style.css`（コメント） | 消した。`assets/*.js` のコメントは変えない（ページの参照のハッシュが変わり、新しいページの生成物に差分が出るため）。告知動画の制作のスクリプトは当時の記録として残した |
| `houou_leagues_data.json`・`ouka_leagues_data.json` | `check-assets.yml` の allowed、`_headers`、生成、`regenerate.py` | 消した。`_headers` の Cache-Control の規則を消した。`check-assets.yml` の allowed の行は許すだけで害が無く、ワークフローは変えない指示のため残した |
| `houou_race/`（41ファイル） | `generate_houou_race.py`・`regenerate.py`、告知動画の撮影のコメント | 消した |
| `houou_results.js` | `generate_houou_pages.py` のコメント（由来） | 消した。コメントは由来として残した |
| `houou_race.js` | `assets/league.js`・`assets/league_race.js`・`style.css` のコメント、`generate_houou_race.py` の `render_page()` の既定値 | 消した。`style.css` のコメントは `assets/league_race.js` に直した。`render_page()` の既定値は呼ぶ側が必ず渡すので残した |
| `ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html`・`ouka_results.js` | `_redirects`、文書、`test_houou_pages.py`（`_redirects` の行の確かめ） | 消した |
| `generate_ouka_leagues.py` | `regenerate.py`・`test_simple_pages_check.py`・`test_ouka_columns.py`・`test_league_candidates.py` | 消した（新しい ouka/ の生成は使っていない）。テストの該当の分を消した |
| `wrc_results.html`・`wrc_ranking.html`・`wrc_results.js` | `_redirects`、文書 | 消した |
| `league_ranking.js` | 旧ランキングの3ページだけが読む。`lib/ranking.py`・`generate_houou_pages.py`・テストのコメント（移植の由来） | 消した（JWRC の分岐も一緒に消えた）。コメントは由来として残した |
| `leagues.js` | `houou_leagues.html`・`ouka_leagues.html`、生成、`lib/chart.py` のコメント。新しいページは読まない（grep の当たりは `players/leagues.json`） | 消した |
| `check_leagues_dropped.py` | 手動実行だけ（ワークフローは前に削除済み） | 消した（下の「判断」） |
| `scripts/apply_page_meta.py` | — | 53d574b0d（#517）で消してあった。対象なし |

### 部品の移動・つなぎ

- `generate_houou_race.py` は `main()`・`legacy_half()`・`write_data()`・`run_check()` と出力先の定数を消すと部品（読み込み・累計・帯・`render_page()`・名前の辞書）だけになるため、`scripts/lib/houou_race.py` へ移した（`git mv`、R088）
- `generate_houou_leagues.py` は `main()`・`legacy_load()`・SVG の組み立てを消すと部品（`period_of`・`period_label`・`load`・`current_name`・`current_rows`・`load_candidates`）だけになるため、`scripts/lib/houou_leagues.py` へ移した
- 移した理由: `regenerate.py` の `known_pages()` は `scripts/generate_*.py` をページとして数えるため、部品だけのファイルを残すと `--list`・`all` に出る。`lib/` の変更は全ページを選ぶ（`SHARED`）ので `--changed` の取りこぼしも無い
- import を直した: `generate_houou_pages.py`・`generate_jpml_pros.py`・`generate_jwrc_pages.py`・`generate_ouka_pages.py`・`check_unused_names.py`（`NAME_RESOLVERS` のキーも `lib.houou_leagues.current_name` などに）とテスト5本
- `lib/leagues.py` の `upper_counts()`・`build_player_series()`、`lib/chart.py` の `stacked_column_chart()`・`render_legend()`（旧型C だけが使う）を消した
- 「プロ」タブの列記号の読み方の件は触っていない（指示のとおり）

### 生成を止めた・regenerate.py

- 対象から消した: `houou_race`・`houou_leagues`・`ouka_leagues`（`--list` は27ページ）。`OUTPUT_OVERRIDES` の3件を消し、`SHARED_GENERATORS` の `ouka_pages`・`jwrc_pages` から `houou_race` を外した（部品は `lib/` にあり全ページが選ばれる）

### style.css・テスト・サイト内のリンク

- `style.css`: 旧型C の節（`.mj-league-chart-*`・`.mj-chart-legend*`）と、説明文の幅を合わせる `body:has(.mj-league-chart-*) .mj-lead` を消した（サイトの HTML・JS のどこにも無いことを確かめた）
- `.mj-race` の節は消していない: 節の中のクラスはすべて `houou/race/`・`ouka/race/`・`jwrc/race/` か `assets/league_race.js` が使っている（旧ページだけが使う規則は見つからなかった。`.mj-houou-race` で上書きされる規則の整理は見た目の確かめが要るため今回はしない）。旧ページを指すコメントだけ直した
- テスト: 旧ページの生成のテスト（`LeaguesCheckTest`・`OukaCheckTest`・`OukaLeaguesTest`、`test_leagues_data_json_is_committed` など）を消し、`test_regenerate.py` の `--changed` の確かめを新しい形にした
- サイトマップ・`llms.txt` に旧ページの行は無かった（公開の時点で外してあった）。サイト内の HTML から旧ページへのリンクも無かった

### 文書

- `docs/notes/static-generation.md`: ページの一覧の行（型C・順位変動・Google Charts 依存）を消し、件数（24 → 15ページ）は見出しの前の段落に書いた（WRR が件数の段落に追記しているため。上の0章）。「ページ側のJS」の旧 JS の記述を1行にまとめ、`check_leagues_dropped.py` の行を消した。型C の移行の記録の節は冒頭に「当時の記録」の1行を足した
- `docs/notes/houou-race.md`: 冒頭に旧ページを消したことと今の置き場所を書き、ファイルの表を今の形にした
- `docs/notes/ouka-top.md`・`houou-top.md`・`ask-worker.md`・`mj-lead.md`・`a11y-manual-check.md`: 旧ファイル名・旧ページの記述を直した
- `CLAUDE.md`「データの流れ」: 型C の選択リストと Google Charts 依存の6ページの行を消した（どちらのページも無くなったため）
- `docs/handover.md`: ページ構成の系統、外部依存（Google Charts は依存するページが0）、#7 の行と「#7 の進め方」の現状を直した
- `docs/decisions/houou.md`: 今日の決定の節を足し、2026-10-14（公開の後もしばらく残す）・2026-10-16（2026-10-23 の Search Console の後）・OUK・WRC の「旧ページを残す」の行に「→ 置き換え」を付けた

### 2. テスト・再生成の比較・容量

- `python3 -m unittest discover -s scripts/tests` 1,423件 OK、`node --test 'workers/ask/test/*.test.mjs'` 47件 pass（取り込みの後は 63件 pass。増えたのは cloudflare の AIC-07 のテスト）
- `python3 scripts/regenerate.py all` を作業ブランチ（b545ff9b5 の後）と cloudflare のクローン（d8b213689）で同時に回した（どちらも終了コード 0）。2つの作業ツリーを `diff -rq` で比べた差は、消したファイル（旧9ページ・旧 JS・旧 JSON・`houou_race/` の41ファイル）と、手で直した `CLAUDE.md`・`_headers`・`style.css`（と `docs/`・`scripts/`）だけ。houou/・ouka/・jwrc/・jpml_pros・workers/ask の data*.json ほか、生成物の差は0。サイトマップ・`llms.txt` の差も0（旧ページの行はもともと無い）
- 再生成で作業ブランチに出た差（jpml_pros・live・mtsuku・title の12ファイル）は cloudflare のクローンでも同じに出たシートの取得時点の差で、この作業の差ではないため戻した
- 容量の検査（`check-assets.yml` の「ガイド文書のサイズを確認」と同じ判定）: CLAUDE.md 24,972 → 24,786、handover.md 24,006 → 23,900、chat-side-operations.md 22,155（変えていない）、instruction-template.md 14,325（変えていない）。どれも警告域の手前。`check_asset_limits.py` は配信ファイル数 3,965（19.8%）ほか全部 OK

### 3. プレビュー

- Workers Builds の作業ブランチのビルドは、この作業の push（06:45Z）だけでなく work/1019-aic（05:56Z）・work/WRR（06:24Z）の push も queued のまま進まず、15分の上限を超えた（07:01Z 時点。cloudflare の push は 06:21Z に success）。プレビューでの確かめは「未確認の項目」に回した
- 代わりに、作業ブランチの作業ツリーをローカルの HTTP で配信し、headless Chrome（375px）で houou/・ouka/・jwrc/ のトップ・個人成績・ランキング（通算）・順位変動、`houou/players/?name=`、`ouka/leagues/`、`jpml_pros.html` を開いた。JS のページのエラー（pageerror）は全ページ0。コンソールのエラーは、外部の画像など手元から届かないもの（ERR_TUNNEL_CONNECTION_FAILED）と、手元に無い質問欄の Worker（`/api/ask/status` の 404）だけ
- 判断（仮置き）: 新しいページの生成物の差が0で、本番の確かめ（手順5）で同じ確かめを行い、壊れていれば戻す前提で、プレビューを待たずにマージへ進めた

### 4. 取り込み・マージ

- push 直前の再 fetch で origin/cloudflare が 4bd3592b8 に進んでいた（AIC-07: `workers/ask/` と docs）。`docs/decisions/houou.md` の末尾が AIC-07 の節と同じ位置への追記で衝突するため、衝突を解かず、こちらの OUK-25 の節を先に外してから（1コミット）merge で取り込み（衝突なし、563d67955）、AIC-07 の節の後ろに同じ節を足し直した（中身は変えていない。衝突の印 0）
- 取り込んだ変更は `workers/ask/`（コードとテスト）と docs だけで、`scripts/`・生成物は変わらないため、再生成の比較はそのまま使える。取り込み後のテスト: unittest 1,423件 OK、node 63件 pass

- push 直前の再 fetch で `git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/1019-ouk25:cloudflare`（4bd3592b8..d5b59e675、早送り）
- check-assets（「公開対象の検査」）は d5b59e675 で success（run 39213568587）。「ページの再生成」（run 39213571384、`scripts/lib/` の変更で全ページ）は success で、6f0d999b1 を push した。差はシートの取得時点の12ファイル（jpml_pros・live・mtsuku・title）と sitemap の lastmod だけで、houou/・ouka/・jwrc/・`_redirects` の差は無い
- Workers Builds の mj: d5b59e675 の success（07:07Z）は作業ブランチのプレビューのビルド（遅れて動いた）。本番は 6f0d999b1 で success（07:16Z）。その前（07:07〜07:16Z）の本番は、消したファイルが 200・style.css も前の版のままだった（本番のビルドの前だったため）

### 5. 本番の確かめ（6f0d999b1 の本番のビルドの後）

- 旧9ページの URL は 301 で新しいページへ移る（`?name=` を引き継ぐ）:

| 旧 URL | 行き先 |
|---|---|
| `/houou_ranking.html` | `/houou/ranking/total/` |
| `/houou_leagues.html?name=白鳥翔` | `/houou/leagues/?name=白鳥翔` |
| `/houou_race.html` | `/houou/race/` |
| `/houou_results.html?name=白鳥翔` | `/houou/players/?name=白鳥翔` |
| `/ouka_ranking.html` | `/ouka/ranking/total/` |
| `/ouka_leagues.html?name=清水香織` | `/ouka/leagues/?name=清水香織`（JS で `/ouka/players/?name=清水香織&view=league` へ移ることも確かめた） |
| `/ouka_results.html?name=清水香織` | `/ouka/players/?name=清水香織` |
| `/wrc_ranking.html` | `/jwrc/ranking/total/` |
| `/wrc_results.html?name=紺野真太郎` | `/jwrc/players/?name=紺野真太郎` |

- 消したファイル（`/league_ranking.js`・`/leagues.js`・`/houou_race.js`・`/houou_race/43-1.json`・`/houou_leagues_data.json`・`/ouka_results.js`・`/wrc_results.js`）は 404。`style.css` は新しい版（旧型C の規則が無い）
- houou/・ouka/・jwrc/ のトップ・個人成績（`?name=` 付きを含む）・ランキング（通算）・順位変動は 200。375px の headless Chrome で JS のページのエラー（pageerror）は全ページ0。コンソールのエラーは Cloudflare Web Analytics（`static.cloudflareinsights.com`、このセッションの proxy で届かない）だけ。遅れて動いたプレビューでも同じく pageerror 0（質問欄の Worker の `/api/ask/status` だけ 404）

### 6. issue

- #582 に結果をコメントしてクローズした（completed。「状況:」ラベルは付いていなかった）

## WRC への引き継ぎ

- WRC の旧2ページ（`wrc_results.html`・`wrc_ranking.html`）と `wrc_results.js`、`league_ranking.js`（JWRC の分岐を含む）を消した（b545ff9b5、cloudflare へは d5b59e675 でマージ）。旧 URL は `_redirects` で jwrc/ へ 301 のまま
- `generate_houou_race.py`・`generate_houou_leagues.py` は `scripts/lib/houou_race.py`・`scripts/lib/houou_leagues.py` に移った（import は `from lib import houou_race as race_page` の形）。work/WRR の `regenerate.py` の `SHARED_GENERATORS` の `wrcr_pages` にある `"houou_race"` は対象でなくなったので、取り込むときに外す（`ouka_pages`・`jwrc_pages` は `("houou_pages",)` にした）
- work/WRR を取り込むとき、`docs/decisions/houou.md` の末尾が衝突する（cloudflare の AIC-07・OUK-25 の節と WRR の節、どちらも末尾への追記）

## 報告

- 状態: 完了
- ブランチ: work/1019-ouk25
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-OUK-25.md
- 比較URL: https://github.com/retroeater/mj/compare/4bd3592b8...d5b59e675
- 確認用URL: https://ryoei.pro/houou/ ・ https://ryoei.pro/ouka/ ・ https://ryoei.pro/jwrc/ （旧 URL の例: https://ryoei.pro/houou_results.html ）
- マージ: 済み（d5b59e675。Workers Builds の mj は本番 6f0d999b1〈再生成のコミット〉で success、check-assets は success）。消したファイルは60件:
  - 鳳凰戦: `houou_ranking.html`・`houou_leagues.html`・`houou_race.html`・`houou_results.html`、`houou_leagues_data.json`、`houou_race/`（41ファイル）、`houou_results.js`・`houou_race.js`
  - 女流桜花: `ouka_results.html`・`ouka_ranking.html`・`ouka_leagues.html`、`ouka_leagues_data.json`、`ouka_results.js`
  - WRC: `wrc_results.html`・`wrc_ranking.html`・`wrc_results.js`
  - 共有: `league_ranking.js`・`leagues.js`
  - スクリプト: `scripts/generate_ouka_leagues.py`・`scripts/check_leagues_dropped.py`
  - 止めた生成: `houou_race`・`houou_leagues`・`ouka_leagues`（`regenerate.py --list` は27ページ）
  - 移した部品: `generate_houou_race.py` → `scripts/lib/houou_race.py`、`generate_houou_leagues.py` → `scripts/lib/houou_leagues.py`（入口とつなぎ〈`legacy_half()`・`legacy_load()`〉を消すと部品だけになり、`regenerate.py` が `generate_*.py` をページとして数えるため）。旧型C だけの `lib/leagues.py`・`lib/chart.py` の関数を消した
  - 残したもの: `_redirects` の旧9ページの行、`ouka/leagues/` などの中継のページ、`check-assets.yml` の allowed の旧名（ワークフローは変えない）、`assets/league.js`・`league_race.js` と `scripts/promo_video/houou_race/` の由来のコメント、`style.css` の `.mj-race` の節（新しい順位変動が使う）
  - 本番の確かめ: 旧9ページの URL（`?name=` 付きを含む）は 301 で新しいページへ移る。消したファイルは 404。houou/・ouka/・jwrc/ のトップ・個人成績・ランキング・順位変動は 200 で、375px の JS のエラーは0
- issue: #582 にコメントしてクローズ
- 判断が必要なこと:
  - 仮置き: `check_leagues_dropped.py` は消した（旧型C の選手選択リストにしか意味が無く、新しいページの生成物で同じ確かめをするには新しいコードが要るため）。残すべきだったなら git の履歴から戻せる
  - 仮置き: 部品を `scripts/lib/houou_race.py`・`scripts/lib/houou_leagues.py` へ移した（理由は上）。`lib/` の変更は `--changed` で全ページを選ぶので、今後この2つを直すと再生成が全ページになる
  - 仮置き: 作業ブランチのプレビューが Workers Builds で15分を超えて動かなかった（ほかのブランチも同じ）ため、手元での headless の確かめで代え、プレビューを待たずにマージした。プレビュー（遅れて動いた）と本番でも同じ確かめが通った
  - Google Charts に依存するページが0になった。#7（Google Charts 依存の解消）を閉じるか、#9（CSP）を gstatic なしで書けるかの判断（handover.md の #7 の行と「外部ドメインへの依存」は事実だけ直した）
  - `CLAUDE.md`「データの流れ」の型C の選択リストと Google Charts 依存の6ページの行を消した（ページが無くなったため。指示の文書の一覧には無かった）
- 未確認の項目: なし
- エラー:
  - 着手時に #582 へ「着手中」のコメントを残していなかった（完了のコメントだけ）
  - 誤って `git stash create` を1回実行した（stash の参照・作業ツリーは変わっていない。直後の `git stash list` は hook が止めた）。1回、コミットメッセージを `git commit -m` の二重引用符で渡した（85f6c1831。`$` やバッククォートは含まない）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d5b59e67）: https://github.com/retroeater/mj-logs/tree/main/guide/d5b59e67

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
