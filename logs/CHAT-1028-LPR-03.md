# CHAT-1028-LPR-03

- 着手日時: 2026-10-28
- 対象issue: なし（LPR-01 の指摘 S1・S2・S3・S4・S5・S6・S9・S10・S11）
- ブランチ: work/1028-lpr-fix
- 着手時HEAD: 2c57b6ae0

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群の横断レビュー（LPR-01）の4大会の指摘9件を直す（S1・S2・S3・S4・S5・S6・S9・S10・S11）
Chat-Ref: CHAT-1028-LPR-03
マージ: 承認済み（チャットで）。条件は下の「止まる条件」。1つでも当たればマージせず止まる
貼る時機: いつでも（CHAT-1028-LPR-02 と並行してよい。別のセッションに貼る。LPR-02 は issue とログだけを変える）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr-fix〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-fix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-lpr-fix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr-fix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
LPR-01（docs/logs/CHAT-1028-LPR-01.md）の指摘のうち、4大会（houou/・ouka/・jwrc/・wrcr/）の9件を直す。画面の誤り（S1）、アクセシビリティの不具合（S2）、使われていないコード・CSS（S3〜S6・S9）、文書（S10）、テストの重複（S11）。各指摘の内容と証拠は LPR-01 のログ `## 経過`「指摘の一覧」の元の番号の行が正。

### 決定（2026-10-28、平野さん）
- 直すのは S1（①-12）・S2（③-1）・S3（②-1）・S4（②-2）・S5（②-3）・S6（②-4）・S9（②-8 の houou 側）・S10（②-13）・S11（②-14）
- マージは、テストが通り、再生成の差分が指摘で説明できる種類だけで、プレビューでの計測（S1 の文言、S2 のフォーカス）が通れば行ってよい
- 最強戦（saikyo/）は告知済みとみなしてよい（この指示では saikyo/ を変えないが、共通の部品を変えるときに告知の期間の守り方は当てない）

### 前提（チャット側。平野さんの決定ではない）
- 各件の場所（LPR-01 のログの記載。要確認: 実物と食い違えばその件だけ外して進め、外した件と理由を報告に書く）:
  - S1: scripts/generate_houou_pages.py 917・924行の固定文字列「期・リーグを選ぶ」。リーグの無い大会（`Contest.no_leagues`）では「期を選ぶ」にする。ボタンの文言は league_race.js:143 がすでに期だけにしている
  - S2: /race/ の期の選択シート（`aria-modal="true"`）を開いても、Tab でシートの外（背面の節のボタン・選手チップ・スキップリンク・ナビ）へ抜ける。開く関数で背面を inert にし（またはフォーカスを閉じ込め）、閉じたら戻す。Esc で閉じてフォーカスが `button.mj-houou-picker` に戻る今の振る舞いは残す
  - S3: generate_houou_pages.py:138 `MIN_ROWS, MAX_ROWS = HOUOU.rows`（未使用。行数の検査は load_rows() の `CONTEST.rows`）
  - S4: scripts/lib/ranking.py:22 `DEFAULT_DIVISION`（参照なし）
  - S5: lib/ranking.py の `rank(limit=True)` の経路・`Contest.limits`・`DEFAULT_RANK_LIMIT`・`MIN_CONSECUTIVE_PROMOTIONS`・`CONTESTS["特昇"]`。本番の呼び出し（generate_houou_pages.py:588・620・621）は全部 `limit=False`。テストは直す
  - S6: generate_houou_pages.py:167 `OG_IMAGE`（test_houou_pages.py:212 でしか使われない。テストは `CONTEST.og_image` を見るように直す）
  - S9: style.css の houou 側の未使用クラス6種（`mj-houou-card-icon`・`mj-houou-up`・`mj-houou-down`・`mj-houou-pyramid`・`mj-houou-section-title`・`mj-houou-trend-range`）。**LPR-01 の根拠は grep で文字列が見つからないことだけ。** JS・Python でクラス名を組み立てている（`'mj-houou-' + …`・テンプレート文字列・`classList.add(変数)` など）と grep では拾えない（saikyo 側では promo_video の capture.mjs が参照していた例があった）。組み立ての有無と、プレビューの DOM での出現（選手を選ぶ・タブを切り替えるなどの操作の後を含む）を確かめ、1つでも疑いがあればそのクラスは残す
  - S10: docs/notes/league-pages-compare.md 58行の noindex の行（wrcr を「あり」としているが実物は「なし」）と、同じ文書の4行の古い説明（「未公開で足した」）
  - S11: scripts/tests/test_houou_pages.py 369〜392行の current_league() と trend_marks() の、league.js を読む前半の約8行の重複をまとめる
- S4・S5 の `lib/ranking.py` は saikyo/ の生成（scripts/generate_saikyo_pages.py など）からも使われているかもしれない（要確認: `git grep` でリポジトリ全体〈scripts/・workers/・assets/〉を確かめる）。使われていても消す対象を使っていなければ進めてよい。使っていれば、その件だけ外す
- 共通の関数・定数を変えるので、全ページの再生成は CLAUDE.md「判断・作業の原則」のとおり。差分の比べ方: 同じセッションで、origin/cloudflare の状態と変更の後の状態をそれぞれ `python3 scripts/regenerate.py all` で生成して比べる（シートの変化を差分から除くため）。見込みの差分は jwrc/race・wrcr/race の HTML のシートの題（各2か所）だけ。CSS・JS・Python の削除と S2 の JS は生成物の HTML を変えない見込み
- プレビューは docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」のとおり。プレビューの URL はログ・issue に書かない（最終報告の「確認用:」の行だけ）
- マージの後、`regenerate-page.yml` が scripts の変更の push で走り、その時点のシートで再生成してコミットする（シートの変化で無関係なページが変わることがある）

## 手順
1. 着手前に S4・S5・S9 の確かめ（saikyo/ からの利用、クラス名の組み立て）を行い、表にしてログに書く。外す件があれば理由とともに書く
2. 9件を直す（外した件を除く）。テストを全部走らせる（`scripts/tests/` と、変えたものに関係する workers のテストがあればそれも）。origin/cloudflare と変更の後で全ページを生成して比べ、差分を種類ごとに全件ログに書く
3. 作業ブランチを push してプレビューで計測し、ログに書く: (a) jwrc/race・wrcr/race のシートの題が「期を選ぶ」、houou/race・ouka/race は「期・リーグを選ぶ」のまま (b) 4大会の /race/ でシートを開き Tab を40回押して、フォーカスがシートの外へ出ない。Shift+Tab でも同じ。Esc で閉じてフォーカスが期を選ぶボタンに戻り、閉じた後は背面を操作できる (c) S9 で消したクラスを持つ要素が、4大会の代表のページ（LPR-01 の手順1の表のトップ・個人成績〈選手を選んだ後〉・ランキング・順位変動）に0件。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げる。マージは冒頭の「マージ:」の行のとおり（push 直前の確かめは CLAUDE.md「ブランチ運用」）。マージ後、本番で (a) を `?v=<未使用の値>` 付きで確かめる。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- テストが1つでも落ちる（自分の変更で落ちると分かっているテストは直してよい。直した理由をログに書く）
- origin/cloudflare と変更の後の生成の差分に、jwrc/race・wrcr/race のシートの題（見込み: 2ファイル×2か所）以外がある（S5 でランキングの結果が変わった場合を含む）
- 手順3の (a)〜(c) のどれかが通らない
- 変えたファイルが、scripts/generate_houou_pages.py・scripts/lib/ranking.py・assets/league_race.js（S2 で別の JS を変える必要があれば、そのファイルとその理由をログに書けばよい）・style.css・scripts/tests/ の関係するテスト・docs/notes/league-pages-compare.md・jwrc/race と wrcr/race の生成物・docs/decisions/・docs/logs/ 以外に及ぶ
- saikyo/ の生成物が変わる
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が上のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。重なる件だけ外して進め、全部が重なるときだけ止まる）
- マージ後の check-run の失敗が今回の変更による（無関係な失敗なら原因をログに書いて、本番の確かめを進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 0章: 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 0件（`git log --all --grep="CHAT-1028-LPR-03"`）。`LPR` は同じセッションの続き
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。「貼る時機」は「別のセッションに貼る」としているが、LPR-01・02 と同じセッションに貼られた。LPR-02（work/1028-lpr-iss、判断待ち）とはファイルが重ならない（LPR-02 は docs/logs と docs/decisions/site-review.md だけ）ため、このまま進めた
- 作業ブランチ: work/1028-lpr-fix はローカル・リモートとも無し → `git checkout -b work/1028-lpr-fix origin/cloudflare`（2c57b6ae0）
- 指示文が着手・クローズの対象にする issue は無い
- 未マージの work/ ブランチとの重なり（`git branch -r --no-merged origin/cloudflare` の各ブランチと `git diff --name-only origin/cloudflare...<branch>`、対象は変える予定のファイル）: origin/work/1017-lvp が style.css を変えている。範囲は 1540〜3232行付近（saikyo・動画の節）で、S9 で消す 4094〜6012行付近とは離れている（同じ行・同じ規則ではない）。止まらずに進める

### 手順1: S4・S5・S9 の確かめ

| 件 | 確かめ | 結果 |
|---|---|---|
| S4 `DEFAULT_DIVISION` | `git grep` で scripts/・workers/・assets/ の .py・.js・.mjs | ranking.py:22 の定義だけ。進める |
| S5 `rank(limit=True)` の経路 | `ranking.rank(` の呼び出し | 本番は generate_houou_pages.py:588・620・621 の3か所で全部 `limit=False`。`limit` を既定のまま呼ぶのは test_houou_pages.py の `table()` だけ。進める |
| S5 `Contest.limits`・`DEFAULT_RANK_LIMIT` | `git grep` | ranking.py の定義と `rank()` の `limit` の分岐、テスト（test_houou_pages.py:84・134）だけ |
| S5 `MIN_CONSECUTIVE_PROMOTIONS` | `git grep` | ranking.py の定義・`consecutive_promotions()` の既定値・`rank()` の `limit` の分岐だけ |
| S5 `CONTESTS["特昇"]` | `git grep 特昇` | ranking.py:57 の定義だけ |
| S4・S5 の saikyo/ からの利用 | generate_saikyo_pages.py の `ranking.` | `ranking.to_fixed`（1436行）・`ranking.Entry`（1458行）・`ranking.auto_cut`（1459行）だけ。消す対象は使っていない |
| S9 クラス名の組み立て | league.js・league_race.js・houou_ask.js・saikyo.js・生成スクリプトで `'mj-houou-…' +`・テンプレート文字列・f 文字列・`className = 変数`・`classList.add(変数)` を grep | 組み立ては `'mj-houou-lg-' + pt.move`（league.js:1570。`mj-houou-lg-up`/`-down` を作る。消す `mj-houou-up`/`-down` とは別の名前）、`'mj-houou-div-rank' + ' is-short'` などの別のトークンの足し算、`'mj-houou-trend-dot ' + mark`（mark は `is-up`・`is-down`・`is-pass`）、`'mj-houou-trend-label ' + cls`（`is-top` など）、league_race.js の `mj-race-band ${b.cls}`（`up`/`down`。`mj-houou-` は付かない）、生成スクリプトの `mj-houou{cls}`（cls は ` is-<dir>`）。`className = 変数`（houou_ask.js:84・league.js:85・league_race.js:305）は `el()` の引数の受け渡しで、渡す値は上の文字列。6種を組み立てる箇所は無い |
| S9 ほかの参照 | 6種の名前でリポジトリ全体を `git grep`（style.css を除く） | docs/logs の LPR-01・LPR-03 だけ（scripts/promo_video・workers にも無い） |

外す件は無し。プレビューの DOM での出現（操作の後を含む）は手順3の (c) で確かめる。

### 手順2: 直したこと（コミット 2e71041fd）

| 件 | 変えたもの |
|---|---|
| S1 | generate_houou_pages.py に `race_pick_label()` を足し（`CONTEST.no_leagues` なら「期を選ぶ」、ほかは「期・リーグを選ぶ」）、選ぶボタンの初めの文字（`race_head()`）とシートの題（`RACE_SHEET` の `{title}`）の両方に使う。テスト `test_jwrc_pages.test_race_picker_says_term_only`（JWRC・WRCR・HOUOU の3大会でボタンと題）を足した。このテストが変更前のコード（origin/cloudflare 2c57b6ae0 の worktree）で落ちることを確かめた（FAIL 1件） |
| S2 | assets/league_race.js: シートを開くと、シートから body までの祖先の兄弟要素（scrim を除く）を inert にし、閉じると戻す（`setBackInert()`）。あわせて、シートの中で Tab は最後から最初へ、Shift+Tab は最初から最後へ回す（keydown）。inert だけでは、最後の要素の次で Tab がブラウザの外（`document.activeElement` が BODY）へ出たため（ローカルで jwrc/race を Tab 8回: シートの2つ → BODY → シートの2つ…）。Esc・「完了」・scrim で閉じて選ぶボタンへフォーカスを戻す動きは変えていない |
| S3 | generate_houou_pages.py の `MIN_ROWS, MAX_ROWS = HOUOU.rows` と、その上のコメント行を消した |
| S4 | ranking.py の `DEFAULT_DIVISION` を消した |
| S5 | ranking.py の `rank()` から `limit` の引数と `limit=True` の分岐（`contest.limits` での切り詰め）を消し、連続の部門の下限を1にする置き換えを常に行う形にした（本番の `limit=False` と同じ動き）。`Contest.limits`・`DEFAULT_RANK_LIMIT`・`MIN_CONSECUTIVE_PROMOTIONS`（`consecutive_promotions()` の既定値は 1 に）・`CONTESTS["特昇"]` を消し、モジュールの説明の「閾値と表示件数」を「閾値」にした。呼び出し3か所（generate_houou_pages.py）と test_jwrc_pages.py の `limit=False` を外した |
| S6 | generate_houou_pages.py の `OG_IMAGE` と、その上のコメント行を消した。テストは `pages.CONTEST.og_image` を見る |
| S9 | style.css の6種の規則を消した（`.mj-houou-card-icon` の4規則、`.mj-houou-section-title`、`.mj-houou-candles .mj-houou-up`・`.mj-houou-down`、`.mj-houou-trend-range` の2規則、`.mj-houou-pyramid`。`.mj-houou-pyramid .mj-houou-lg-card` は `.mj-houou-lg-tab .mj-houou-lg-card` と同じ規則のセレクタの1つだったので、そのセレクタだけを外し、コメントを「個人成績のリーグのタブ」にした）。61行減 |
| S10 | league-pages-compare.md の `noindex` の行を「なし・なし・なし・なし」に、4行目を「2026-10-19 に足し〈#366〉、#611 で公開した」にした |
| S11 | test_houou_pages.py に `run_league_js(expr, scripts)` を足し、`current_league()` と `trend_marks()` はそれを呼ぶ形にした |
| 生成物 | jwrc/race/index.html・wrcr/race/index.html の2か所（ボタンの初めの文字・シートの題）を、生成と同じ置き換えで手で直した（この環境ではシートを読めないため。下の「生成の比べ」） |

#### 直したテスト（S5 の変更で落ちると分かっているもの）と理由
`limit` を消したため、`table()`（`limit` を既定の True で呼んでいた）が本番と同じ「最小の回数で絞らない」結果になる。期待値を本番の動きに合わせた。
- `test_consecutive_positive_sections_zero_continues_and_skips_rows_without_total`: `[(1,甲,3)]` → `[(1,甲,3),(2,丙,2),(3,乙,1),(3,甲,1)]`。丙が 2（進行中の行の節を数えると 3）であることで、合計の無い行を数えないことは引き続き確かめている
- `test_consecutive_promotions`: 2つ目の期待値を `[(1,甲,3),(2,甲,2)]` に（残留で途切れることを確かめる）
- `test_rank_without_limit_keeps_short_streaks` → `test_rank_keeps_short_streaks`（`limit=False` の呼び出しを消し、`table()` が 2回の連続を返すことを見る）
- `test_limit_cuts_even_on_tie`: 消した（`limits` が無くなった）
- `test_contest_thresholds_match_old_js`: `houou.limits` の行を消した（閾値の4つは残る）
- `test_og_image_is_houou_image`: `pages.OG_IMAGE` → `pages.CONTEST.og_image`
- test_jwrc_pages.py の `table()`: `limit=False` を外した

#### テスト
- 変更前（origin/cloudflare 2c57b6ae0 の worktree）: `python3 -m unittest discover -s scripts/tests` → Ran 1597 tests、OK
- 変更の途中（テストを直す前）: 1597件中 failures=3・errors=7（上の S5・S6 のテストと test_jwrc_pages の4件。`limit=False` の引数が無くなったため）
- 変更の後: Ran 1597 tests、OK（1件消し、1件足した）
- workers のテスト: 変えたファイルに workers/ は無いため対象外
- `node --check assets/league_race.js`: 構文エラーなし

#### league_race.js の S2 に検査（テスト）を置かなかった理由
league_race.js は読み込み時に DOM を組み立てて動く即時関数で、league.js のような `module.exports` の口が無く、`scripts/tests/` から node で動かす仕組みも無い（`git grep league_race scripts/tests workers` で0件）。テストのために口と仮の DOM を作るのは、この1か所のための仕組みになる（CLAUDE.md「コード規約」の、実装が1つだけの抽象化を作らない）。代わりに、手順3の計測スクリプト（scratchpad の lpr3/check.js。Playwright で4大会×2幅、Tab・Shift+Tab 各40回、Esc、閉じた後の操作）で確かめ、値を下に書いた。

### 生成の比べ
- この環境では `python3 scripts/regenerate.py all` がシートを読めない（`SHEETS_READER_SA_KEY がありません`。origin/cloudflare の worktree で実行して確かめた。鍵はワークフローにだけある〈docs/notes/sheets-reader.md〉）。同じ環境で新旧を生成して比べる代わりに、`regenerate-page.yml` を作業ブランチで手動実行し、入力 `dry_run: true`・`compare_ref: 2c57b6ae0680d6b7bdc46bb5c8a43da3a53dc803`（origin/cloudflare）で、同じ実行の中で新旧のコードを続けて生成して比べた（ワークフローの機能、#557）
- 実行: regenerate-page.yml run 40629533497（workflow_dispatch、作業ブランチ、head 2e71041fd）。conclusion success。「対象ページを再生成」03:57〜04:08 UTC、「生成物の差を実行サマリに出す」04:08〜04:17 UTC。ログの結び: `##[notice]compare: 差分なし`（compare_ref: 2c57b6ae0…）
- 読み方: この比べは、新旧それぞれの生成で「コミット済みの内容から変わったファイル」の和集合について、新旧の中身を比べる。差分なしは次の2つを意味する
  - 新しいコードの生成が、手で直してコミットした jwrc/race/index.html・wrcr/race/index.html と一致した（一致しなければ「変わったファイル」に入り、旧コードの生成〈「期・リーグを選ぶ」〉と違うものとして出る）。houou/race・ouka/race も新しいコードで「期・リーグを選ぶ」のまま
  - シートの変化で変わったファイルについて、新旧のコードの生成が同じ（S5 でランキングの結果が変わっていない、saikyo/ も変わらない）
- よって、origin/cloudflare と変更の後の生成の差分は、コミット済みの jwrc/race/index.html・wrcr/race/index.html の各2か所（ボタンの初めの文字・シートの題）だけ。見込みのとおり
- ワークフローのログに出た wrcr のランキングの表示対象（部門・件数・切り方）は新しいコードのもの

### 手順3: プレビューでの計測
- プレビュー: check-run「Workers Builds: mj」（id 122622503421、2e71041fd）success。URL はログに書かない（最終報告だけ）。プレビューの assets/league_race.js に `setBackInert` が2件あり（本番は0件）、新しい版を返すことを確かめた
- 計測スクリプト: scratchpad の lpr3/check.js（Playwright、375px・1280px、URL に `?v=pv2e71` を付けた）。コミットしない。同じスクリプトをローカルの `python3 -m http.server`（作業ツリー）でも先に回し、同じ結果だった

(a) シートの題と選ぶボタンの文字（375px。JS の描画の後のボタンは期・リーグを表示する）

| 大会 | シートの題 | ボタン（描画の後） |
|---|---|---|
| houou | 期・リーグを選ぶ | 第43期前期 B1 |
| ouka | 期・リーグを選ぶ | 第20期 A |
| jwrc | 期を選ぶ | 第19期 |
| wrcr | 期を選ぶ | 第7期 |

(b) シートを開いて Tab・Shift+Tab を各40回（シートの外へ出た回数／止まった要素の種類）、閉じた後

| 大会・幅 | Tab | Shift+Tab | 開いている間の inert の数 | Esc の後のフォーカス・シート・inert・aria-expanded | 閉じた後に節のボタンを押せる | 閉じた後に Tab で背面へ進む |
|---|---|---|---|---|---|---|
| houou 375 | 0／9 | 0／9 | 10 | racePicker・隠れる・0・false | はい | はい |
| houou 1280 | 0／9 | 0／9 | 10 | racePicker・隠れる・0・false | はい | はい |
| ouka 375 | 0／5 | 0／5 | 10 | racePicker・隠れる・0・false | はい | はい |
| ouka 1280 | 0／5 | 0／5 | 10 | racePicker・隠れる・0・false | はい | はい |
| jwrc 375 | 0／2 | 0／2 | 10 | racePicker・隠れる・0・false | はい | はい |
| jwrc 1280 | 0／2 | 0／2 | 10 | racePicker・隠れる・0・false | はい | はい |
| wrcr 375 | 0／2 | 0／2 | 10 | racePicker・隠れる・0・false | はい | はい |
| wrcr 1280 | 0／2 | 0／2 | 10 | racePicker・隠れる・0・false | はい | はい |

（参考: 変更の前の版〈inert だけを足した途中の版〉をローカルで測ると、Tab 40回で外へ出た回数は houou 4・ouka 6・jwrc 13・wrcr 13 で、外は BODY〈ブラウザの外〉だった。Tab の回し込みを足して0になった。LPR-01 の本番の計測では、シートの9個の後に背面の要素へ抜けていた）
シートの中の選択も動く: houou/race でシートの中のリーグのボタンを押すと、ボタンの文字が「第43期前期 B1」から「第43期前期 B2」に変わり、シートは開いたまま（ローカルで確認）

(c) 消した6種のクラス（`mj-houou-card-icon`・`mj-houou-up`・`mj-houou-down`・`mj-houou-pyramid`・`mj-houou-section-title`・`mj-houou-trend-range`）を持つ要素の数（`getElementsByClassName` の合計）

| 大会・幅 | トップ | ランキング（total/） | 順位変動 | 個人成績（仲田加南。開いた直後、切り替えのボタンを順に押した後、期の開閉を押した後） |
|---|---|---|---|---|
| houou 375・1280 | 0 | 0 | 0 | 0・0・0・0・0（切り替え4つ） |
| ouka 375・1280 | 0 | 0 | 0 | 0・0・0・0・0（切り替え4つ） |
| jwrc 375・1280 | 0 | 0 | 0 | 0・0・0・0（切り替え3つ） |
| wrcr 375・1280 | 0 | 0 | 0 | 0・0・0・0（切り替え3つ） |

### 差分の自己レビュー（docs/notes/self-review.md。新しい文脈の general-purpose 1人、1往復）
渡したもの: 指示文の目的と決定、`git diff 2c57b6ae0 2e71041fd`、コード規約の要点。結果: 止まる指摘なし。

| 指摘 | 扱い |
|---|---|
| league_race.js の `setBackInert()` のループと Tab の回し込みの分岐に検査（テスト）が無い（直すべき、または置けない理由と確かめの手順をログに） | 意図どおり（上の「league_race.js の S2 に検査（テスト）を置かなかった理由」と手順3 (b) の計測に書いた） |
| コメント・docstring の「CHAT-1028-LPR-01 の ③-1／①-12」はログの番号で、ログが消えると行き先が無くなる（任意） | 意図どおり（既存のコードも Chat-Ref を出典に書いている〈CHAT-1008-HOU-02 など〉。禁止は CLAUDE.md・handover.md・chat-side-operations.md の3文書） |
| シートを開いた後に背面へ兄弟として足された要素は inert にならない（任意、今は該当なし） | 意図どおり（league_race.js が要素を足すのは inert にした祖先の内側だけで、inert を引き継ぐ） |

レビューが確かめたこと（抜粋）: S5 で本番の経路（旧 `limit=False`）の結果は変わらない、RACE_SHEET の波括弧は `{controls}`・`{title}` だけ、6種のクラスは JS・Python・HTML のどこにも無い、テスト 1597件 OK。

### マージの前の確かめ
- 変えたファイル（`git diff --name-only origin/cloudflare...HEAD`）が、指示の止まる条件の一覧に収まる: assets/league_race.js・docs/notes/league-pages-compare.md・jwrc/race/index.html・scripts/generate_houou_pages.py・scripts/lib/ranking.py・scripts/tests/test_houou_pages.py・scripts/tests/test_jwrc_pages.py・style.css・wrcr/race/index.html・docs/decisions/site-review.md・docs/logs/CHAT-1028-LPR-03.md。saikyo/ の生成物は変えていない
- docs/decisions/site-review.md: LPR-02（work/1028-lpr-iss、未マージ・判断待ち）も同じファイルの末尾に節を足している。マージするときは末尾への追記どうしの衝突になり、CLAUDE.md「ブランチ運用」のとおり両方を残して解ける

## 報告

- 状態: 完了
- ブランチ: work/1028-lpr-fix
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1028-LPR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lpr-fix
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは4大会の /race/・トップ・個人成績・ランキング（total/）。この版で見える印: jwrc/race・wrcr/race の期の選択シートの題が「期を選ぶ」
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7e8e5a5a）: https://github.com/retroeater/mj-logs/tree/main/guide/7e8e5a5a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
