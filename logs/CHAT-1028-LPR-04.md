# CHAT-1028-LPR-04

- 着手日時: 2026-10-28
- 対象issue: なし（LPR-01 の指摘 S12・S13・S14・S16・S17）
- ブランチ: work/1028-lpr-sk
- 着手時HEAD: bf4ef24c8

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群の横断レビュー（LPR-01）の最強戦の指摘5件を直す（S12・S13・S14・S16・S17） Chat-Ref: CHAT-1028-LPR-04 マージ: 承認済み（チャットで）。条件は下の「止まる条件」。1つでも当たればマージせず止まる 貼る時機: CHAT-1028-LPR-03 のマージの後（style.css を同じく変えるため）。LPR-03 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr-sk〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-sk の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1028-lpr-sk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr-sk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1028-LPR-03 のコミットが origin/cloudflare に入っていること（`git log origin/cloudflare --grep 'CHAT-1028-LPR-03'` が1件以上で、docs/logs/CHAT-1028-LPR-03.md の `## 報告` の「マージ」が済）を確かめ、満たさなければ何もせず止まる。

目的
LPR-01（docs/logs/CHAT-1028-LPR-01.md）の指摘のうち、最強戦（saikyo/）の5件を直す。見出しの大会名（S12）、押せる大きさ（S13）、フォーカス枠（S14）、JS の写しの削除（S16）、検索の正規化のテストと対戦成績の絞り込みのかな・カナの区別（S17）。各指摘の内容と証拠は LPR-01 のログ `## 経過`「指摘の一覧」の元の番号の行が正。
決定（2026-10-28、平野さん）

* 直すのは S12（①-2）・S13（③-4）・S14（③-6）・S16（②-10）・S17（②-11）
* 最強戦（saikyo/）は告知済みとみなしてよい（docs/notes/page-announcement.md の告知の期間の守り方は当てない）
* マージは、テストが通り、再生成の差分が指摘で説明できる種類だけで、プレビューでの計測が通れば行ってよい

前提（チャット側。平野さんの決定ではない）

* 各件の場所（LPR-01 のログの記載。要確認: 実物と食い違えばその件だけ外して進め、外した件と理由を報告に書く）:
   * S12: saikyo/years/・saikyo/vs/ の h1 が「年度別」「対戦成績」。saikyo/players・ranking と4大会は「<大会> <ページ>」の形なので、「最強戦 年度別」「最強戦 対戦成績」にそろえる（title・パンくずは変えない）
   * S13: saikyo/players/?name=… の「対戦成績を見る ›」（`a.mj-houou-player-span`、inline）が約100×15px。24px 以上にする。`mj-houou-player-span` は4大会の個人成績でも使う名前なので、規則は最強戦の該当のリンクだけに当て、4大会の見た目を変えない
   * S14: saikyo/vs/・saikyo/years/ のパンくずの「最強戦」と vs/ の「もっと見る」ボタンのフォーカス枠が Chrome 既定（auto 1px）。他ページの focus-visible の規則（solid 2px #14459b）を当てる
   * S16: assets/saikyo.js 290〜305行の vs の節の el()・chevron() が、同じファイル46〜60行のトップレベルの関数と字句まで同じ。写しを消してトップレベルの関数を使う
   * S17: 検索の正規化（assets/saikyo.js:39・635、assets/league.js:65、scripts/lib/results.py:51〜54）に JS と Python の一致を見るテストが無い（saikyo.js の playerKey・playerMatches は test_saikyo_vs.py の JS_FUNCTIONS に無い）。また対戦成績の絞り込み（saikyo.js:251・273・514）は normalize だけでカタカナとひらがなを区別し、個人成績の検索は区別しない。絞り込みを個人成績の検索と同じ折り畳み（playerKey の側）にそろえ、一致のテストを足す（例: 「たろう」で「タロウ」が出る）
* 生成物の比べ方: 同じセッションで、origin/cloudflare の状態と変更の後の状態をそれぞれ `python3 scripts/regenerate.py saikyo_pages` で生成して比べる（シートの変化を除くため。共通の関数・定数を変えたら CLAUDE.md「判断・作業の原則」のとおり全ページ）。見込みの差分は saikyo/years/・saikyo/vs/ の HTML の h1（と、S14・S13 で HTML にクラスを足すならその属性）だけ
* プレビューは docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」のとおり。プレビューの URL はログ・issue に書かない
* マージの後、`regenerate-page.yml` が scripts の変更の push で走る。最強戦は毎朝 `regenerate-saikyo.yml` でも再生成される

手順

1. 5件を直す。テストを全部走らせる（`scripts/tests/` と、変えたものに関係する workers のテストがあればそれも）。origin/cloudflare と変更の後で生成して比べ、差分を種類ごとに全件ログに書く
2. 作業ブランチを push してプレビューで計測し、ログに書く: (a) years/・vs/ の h1 が「最強戦 年度別」「最強戦 対戦成績」 (b) 375px と 1280px で「対戦成績を見る ›」の大きさが高さ24px 以上、かつ4大会の個人成績（LPR-01 の手順1の表の URL、選手を選んだ後）の `a.mj-houou-player-span`・`.mj-houou-player-span` の大きさが origin/cloudflare の本番と同じ (c) vs/・years/ のパンくずと「もっと見る」に Tab でフォーカスしたときの outline が solid 2px #14459b (d) 対戦成績の絞り込みでひらがなとカタカナの入力が同じ結果になる（使った入力と件数を書く）。あわせて、24px 未満のタップの対象が saikyo/ の代表のページ（LPR-01 の表）に増えていないことを書く
3. LPR-01 の決定の続きとして、上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げる。マージは冒頭の「マージ:」の行のとおり（push 直前の確かめは CLAUDE.md「ブランチ運用」）。マージ後、本番で (a) を `?v=<未使用の値>` 付きで確かめる。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

止まる条件

* 0章の確かめが食い違う
* テストが1つでも落ちる（自分の変更で落ちると分かっているテストは直してよい。直した理由をログに書く）
* origin/cloudflare と変更の後の生成の差分に、years/・vs/ の h1 と、S13・S14 のために足したクラス・属性以外がある
* 手順2の (a)〜(d) のどれかが通らない、または4大会の個人成績の見た目（(b) の大きさ）が変わる
* 変えたファイルが、scripts/generate_saikyo_pages.py・assets/saikyo.js・style.css・scripts/tests/ の関係するテスト・saikyo/ の生成物・docs/decisions/・docs/logs/ 以外に及ぶ（S17 で assets/league.js や scripts/lib/results.py を変える必要が出たら、変えずに止まって理由を報告する）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が上のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。重なる件だけ外して進め、全部が重なるときだけ止まる）
* マージ後の check-run の失敗が今回の変更による（無関係な失敗なら原因をログに書いて、本番の確かめを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。`git log origin/cloudflare --grep 'CHAT-1028-LPR-03'` は5件、docs/logs/CHAT-1028-LPR-03.md の `## 報告` の「マージ」は「済（7e8e5a5af）」
- 同じ Chat-Ref のコミット: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 作業ブランチ: work/1028-lpr-sk はローカル・リモートとも無し → `git checkout -b work/1028-lpr-sk origin/cloudflare`（bf4ef24c8）
- 未マージの work/ ブランチとの重なり: origin/work/1017-lvp が style.css を変えている。コミットの後に `git merge-tree --write-tree HEAD origin/work/1017-lvp` の衝突は6件（docs/decisions/live.md・live/ の生成物・scripts/lib/study.py・test_study.py）で、origin/cloudflare との間の衝突の数（6件）と同じ。style.css には衝突なし。止まらずに進めた

### 手順1: 直したこと（コミット 8b47ad958）

| 件 | 変えたもの |
|---|---|
| S12 | generate_saikyo_pages.py の `build_vs_body()`・`build_years_body()` の h1 を `f"{SECTION_NAME} {VS_TITLE}"`・`f"{SECTION_NAME} {YEARS_TITLE}"` に（「最強戦 対戦成績」「最強戦 年度別」）。title・パンくずは変えない。生成物 saikyo/vs/index.html・saikyo/years/index.html の h1 の1行ずつを、生成と同じ文字に手で直した（この環境ではシートを読めない。LPR-03 と同じ） |
| S13 | style.css に `.is-saikyo a.mj-houou-player-span { display: inline-flex; align-items: center; min-height: 44px }`。`a` の `mj-houou-player-span` を作るのは assets/saikyo.js（905行）だけで、4大会（league.js 657・660行）は `p` なので当たらない。高さはサイトの押せる大きさの慣例（パンくず・もっと見ると同じ 44px）にした |
| S14 | style.css に `body.mj-saikyo-n-page .mj-saikyo-n-crumb a:focus-visible, body.mj-saikyo-n-page .mj-saikyo-n-more:focus-visible { outline: 2px solid var(--mj-n-navy); outline-offset: 2px }`。`--mj-n-navy` は `body.mj-saikyo-n-page` の中だけで定義されているので、body のクラスで絞った（絞らないと、クラスの無い卓のページで変数が未定義になり outline が消える） |
| S16 | assets/saikyo.js の vs の節の `el()`・`chevron()`（16行）を消した。呼び出しはトップレベルの同じ関数に解決する |
| S17 | assets/saikyo.js の対戦成績の3か所（`vsRightChoices()` の絞り込み、選手の `key`、`applySuggest()` の `query`）を `normalize()` から `playerKey()`（カタカナをひらがなに寄せる）に。完全一致・前方一致の判定は key と query の両方が playerKey なので食い違わない。`?name=` の解決は元の名前の Map（byName）で、影響なし。assets/league.js・scripts/lib/results.py は変えていない |

テスト（scripts/tests/test_saikyo_vs.py）:
- `JS_FUNCTIONS` に `playerKey` を足した
- `test_right_choices_fold_katakana`: 「たろう」「タロウ」で「タロウ」、「ハナコ」「はなこ」で「はなこ」が出る。変更前の saikyo.js（origin/cloudflare の worktree）で回すと FAIL（`[[],[],[2],[2]]`）、変更の後は通る
- `test_player_key_matches_league_js_and_python`: saikyo.js の `playerKey()`、league.js の `normalize()`（IIFE の中にあり export されないため、ソースから関数の文を正規表現で切り出して node で動かす。league.js は変えない）、lib/results.py の `normalize()` が、8つの標本（タロウ・たろう・全角英字と全角空白・山田 太郎・半角カナ・ヴァイオリン・ヶ月・ABC def）で同じ値を返す（変更前のコードでも通る。一致を見張る検査として足した）

テストの実行:
- `python3 -m unittest discover -s scripts/tests`: Ran 1599 tests、OK（LPR-03 の後の 1597件 + 2件）
- `node --test 'workers/ask/test/*.test.mjs'`: 109件 pass（workers は変えていないが、ask のテストが data-saikyo.json を読むため念のため）
- `node --check assets/saikyo.js`: 構文エラーなし

### 生成の比べ
- LPR-03 と同じく、この環境ではシートを読めないため、`regenerate-page.yml` を作業ブランチで手動実行（`target_page: saikyo_pages`・`dry_run: true`・`compare_ref: bf4ef24c8…`〈origin/cloudflare〉）。結果: `##[notice]compare: 差分なし`（check-run regenerate、job 122671314336、success）
- 読み方は LPR-03 と同じ: 新しいコードの生成が、手で直してコミットした saikyo/vs/index.html・saikyo/years/index.html と一致し、シートの変化で変わったファイルについて新旧のコードの生成は同じ
- よって origin/cloudflare と変更の後の生成の差分は、saikyo/years/・saikyo/vs/ の h1 の1行ずつだけ。S13・S14 は CSS だけで、HTML にクラス・属性は足していない

### 手順2: プレビューでの計測
- プレビュー: check-run「Workers Builds: mj」（id 122671464834、8b47ad958）success。プレビューの assets/saikyo.js に `playerKey(entry` が1件（新しい版）。URL はログに書かない
- 計測スクリプト: scratchpad の lpr3/check4.js（Playwright、375px・1280px）。同じスクリプトを先に本番（origin/cloudflare の版、`?v=prod4a`）で回し、比べの基準にした。プレビューは `?v=pv4a`

(a) h1（375・1280 とも）

| ページ | 本番（変更前） | プレビュー |
|---|---|---|
| saikyo/years/ | 年度別 | 最強戦 年度別 |
| saikyo/vs/ | 対戦成績 | 最強戦 対戦成績 |

(b) `.mj-houou-player-span` の大きさ（個人成績、仲田加南を選んだ後。375・1280 とも同じ値）

| 大会 | 本番（変更前） | プレビュー |
|---|---|---|
| saikyo | P 162.6×19.5（8回出場…）、A 99.6×15.0（対戦成績を見る ›） | P 162.6×19.5、**A 99.6×44.0** |
| houou | P 117.8×19.5 ×2 | P 117.8×19.5 ×2（同じ） |
| ouka | P 117.8×19.5 ×2 | P 117.8×19.5 ×2（同じ） |
| jwrc | P 122.1×19.5 ×2 | P 122.1×19.5 ×2（同じ） |
| wrcr | P 127.8×19.5 ×2 | P 127.8×19.5 ×2（同じ） |

(c) Tab でフォーカスしたときの outline（375・1280 とも同じ値）

| ページ・要素 | 本番（変更前） | プレビュー |
|---|---|---|
| vs/ パンくず「最強戦」 | auto 1px rgb(16, 16, 16) | solid 2px rgb(20, 69, 155) |
| vs/ 「もっと見る」 | auto 1px rgb(16, 16, 16) | solid 2px rgb(20, 69, 155) |
| years/ パンくず「最強戦」 | auto 1px rgb(16, 16, 16) | solid 2px rgb(20, 69, 155) |

(d) 対戦成績の左のカードの入力に打って出た候補（375px。件数と候補）

| 入力（ひらがな／カタカナ） | 本番（変更前） | プレビュー |
|---|---|---|
| れい／レイ | 0件／1件（川上レイ） | 1件／1件（同じ候補） |
| じょーじ／ジョージ | 0件／1件（森川ジョージ） | 1件／1件（同じ候補） |
| まぎー／マギー | 0件／1件（マギー審司） | 1件／1件（同じ候補） |
| ぱた／パタ | （本番は「はた／ハタ」で測り 0件／0件。入力を変えた） | 1件／1件（パタ☆ロッソ、同じ候補） |

24px 未満のタップの対象（a・button・input・select・summary、見えているもの、スキップリンクを除く）。saikyo/ の代表の5ページ（トップ・個人成績〈仲田加南〉・ranking/appearances/・vs/・years/）、375・1280 とも:
- 本番（変更前）: 個人成績の「対戦成績を見る ›」1件だけ、ほかの4ページは0件
- プレビュー: 5ページとも0件（増えていない）

### 差分の自己レビュー（docs/notes/self-review.md。新しい文脈の general-purpose 1人、1往復）
渡したもの: 指示文の目的と決定、`git diff bf4ef24c8 8b47ad958`（docs/logs を除く）、コード規約の要点。結果: 直すべき指摘なし。

| 指摘 | 扱い |
|---|---|
| S14 の規則は body に `mj-saikyo-n-page` が付く年度・対局のページ（例: saikyo/2019/index.html、saikyo/2019/20190630-2.html）のパンくずにも効き、フォーカス枠が Chrome 既定から紺の 2px に変わる。クラスの無い卓のページは既定のまま（任意） | 意図どおり（同じ部品のフォーカス枠をそろえる変更で、悪い変化ではない。変数が未定義の卓のページに当てないため body のクラスで絞った。範囲が vs/・years/ より広いことを報告に書く） |
| S13 は 44px を当てたので、名前の面の文字の段が約29px 高くなる（任意） | 意図どおり（押せる大きさのサイトの慣例。4大会には当たらない。計測で4大会の大きさが同じことを確かめた） |
| S17 の DOMContentLoaded の中の2か所（key・query）はテストで見ていない（任意） | 意図どおり（同じ関数への置き換え。プレビューの (d) で確かめた） |

### マージ
- push 直前に `git fetch origin` のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真。変えたファイルは指示の一覧の内（assets/saikyo.js・saikyo/vs/index.html・saikyo/years/index.html・scripts/generate_saikyo_pages.py・scripts/tests/test_saikyo_vs.py・style.css・docs/decisions/site-review.md・docs/logs/CHAT-1028-LPR-04.md）
- `git push origin work/1028-lpr-sk:cloudflare` の出力の範囲: `bf4ef24c8..546e4052b`
- マージの後の check-run（546e4052b）: Workers Builds: mj success・sync success・notify skipped・regenerate success（06:40〜06:42 UTC）
- regenerate-page.yml のコミット 7f9e605cb（chore: regenerate workers/ask/data-saikyo.json saikyo/）の差分は sitemap-saikyo.xml の2行だけ（saikyo/vs/ の lastmod 2026-10-27 → 2026-10-28、saikyo/years/ 2026-10-26 → 2026-10-28。h1 を変えたことによる）。HTML・data-saikyo.json の差は無し
- 本番の (a)（`?v=lpr4prod<ページ>` 付きで curl）: saikyo/years/ の h1「最強戦 年度別」、saikyo/vs/ の h1「最強戦 対戦成績」。本番の assets/saikyo.js に `playerKey(entry` が1件（新しい版）
- 作業ブランチの片付け（docs/notes/branch-operations.md「ブランチを削除するとき」を読んだ）: この追記を cloudflare へ入れた後、`git branch --merged origin/cloudflare` に含まれることを確かめ、HEAD を origin/cloudflare（detached）へ移して `git branch -d work/1028-lpr-sk` でローカルだけ消す。削除直前の先頭はこの追記のコミット（最終報告の「ログ（公開）」の `?v=` と同じ）。リモートの origin/work/1028-lpr-sk は消さない（文書にリモートの削除の手順が無い。cloudflare の祖先）
- 生成の比べと失敗の確かめに使った origin/cloudflare の worktree（scratchpad）は `git worktree remove` で外した

## 報告

- 状態: 完了
- ブランチ: work/1028-lpr-sk（マージ後にローカルを削除、リモートは残す）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1028-LPR-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lpr-sk
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは saikyo/ のトップ・個人成績・ランキング・対戦成績・年度別と4大会の個人成績。この版で見える印: saikyo/years/ の題名が「最強戦 年度別」
- マージ: 済（546e4052b）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7f9e605c）: https://github.com/retroeater/mj-logs/tree/main/guide/7f9e605c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
