# CHAT-1023-SKP-01

- 着手日時: 2026-10-23
- 対象issue: #626（作成）・#627（公開）
- ブランチ: work/1023-skp
- 着手時HEAD: bb98660ed

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「個人成績」「ランキング」「AIコンシェルジュ」と新しい saikyo/ トップを自律開発で作る（未公開・プレビューで止める）
Chat-Ref: CHAT-1023-SKP-01
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-skp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦（saikyo/）に、houou/・ouka/・jwrc/・wrcr/ と同じ形の「個人成績」「ランキング」「AIコンシェルジュ（質問欄）」と、新しいトップ（メニューの面）を作る。進め方は自律開発（不明点は仮置きして最後まで作り、プレビューと仮置きの一覧で平野さんが確かめる）。この指示では公開しない（本番の /saikyo/ の見え方は変えない）。

### 決定（2026-10-23、平野さん）
- 最強戦の「個人成績」「ランキング」「AIコンシェルジュ」を自律開発で作る。saikyo/ のトップに鳳凰戦（houou/）のようなページを設け、今のトップには名前を付けてメニューの1つにする
- 今のトップ（歴代最強位＋選手名検索）のメニューの名前は「年度別」。選手名検索は個人成績へ移す（年度別と個人成績で検索を重ねない）
- 個人成績・ランキングの対象は全出場者（連盟の在籍者に限らない。ほかの団体の選手・著名人を含む）
- ランキングの部門は次の9部門（チャットで示した案のとおり）: 最強位・ファイナル出場・予選優勝・決勝進出・出場年度・出場卓・通過回数・通過率・連続出場。勝敗は対戦成績と同じく通過/敗退だけで数える
- 公開は全部を一度に行う: 個人成績・ランキング・コンシェルジュは未公開（noindex・どこからも辿れない）で作り、新しいトップはプレビューで確かめる。公開の指示で、トップの切り替えを対戦成績（/saikyo/vs/、#623）の公開と同時に行う

### 前提（チャット側。平野さんの決定ではない）
- 踏襲の元: houou/ の作り（docs/notes/houou-top.md）と、大会の差の扱い（docs/notes/ouka-top.md・jwrc-top.md・wrcr-top.md・league-pages-compare.md）、質問欄（docs/notes/ask-worker.md）。見た目の値は docs/notes/design.md。最強戦の仕組みは docs/notes/saikyo-page-design.md（対戦成績の節を含む）。決定は docs/decisions/saikyo.md・houou.md。食い違う点は最強戦のデータの形に合わせて仮置きし、一覧に書く
- 新しいトップの形の案: 題名「最強戦」→ 質問欄 → 1枚の面にメニュー4行（年度別「歴代最強位と各年度の対局・卓」／個人成績「選手ごとの出場・結果・部門別」／ランキング「最強位・通過率など9部門」／対戦成績「出場者同士の勝敗と同卓の記録」）→ 説明文。並び・文言は仮置き
- URL の案: 年度別 `/saikyo/years/`、個人成績 `/saikyo/players/?name=`、ランキング `/saikyo/ranking/<スラッグ>/`（部門ごとの静的なページ、`/saikyo/ranking/` は先頭の部門へ 301）。年度・対局・卓・対戦成績の URL は変えない。`/saikyo/?q=<名前>`（jpml_pros・卓のページの「すべて見る」の行き先）を個人成績へ移す方法は、公開の指示で入れる前提で案を一覧に書く
- 個人成績の案: houou/players/ の形。選手を探す部品（索引は全出場者、名前は「別名」で現在名に直す。読み・ローマ字の無い連盟外の人は名前だけで引ける形でよい）、名前の面（写真・名前・「最強位 N回」・「出場 N年度（N卓）」）、タブ「年度別」（今のトップの検索結果と同じ年度ごとの卓の面、卓のページへ）と「部門別」（9部門の値と順位、行からランキングへ）。対戦成績への導線（`/saikyo/vs/?name=<選手名>`）を置くかは仮置き
- 9部門の定義の案（仮置き。実データで確かめ、合わなければ直して一覧に書く）: 最強位＝`FINAL_MATCH_NAME` の決勝で通過した回数（今の歴代最強位の判定と同じ）／ファイナル出場＝ファイナルトーナメントに出た年度の数／予選優勝＝ファイナル以外の対局の決勝を通過した回数／決勝進出＝各対局の「決勝」の卓に入った回数（ファイナルを含む）／出場年度＝出場した年度の数／出場卓＝対局済みの卓の数／通過回数＝通過した卓の数／通過率＝通過回数 ÷ 出場卓（規定の下限は仮置き）／連続出場＝続けて出場した年度の数（出場しなかった年度で途切れる）。未対局の卓は数えない。載せる下限・上限は houou/ の `above_floor()`・`auto_cut()` の考え方に合わせて仮置き
- コンシェルジュの案: 同じ Worker（mj-ask）に `contest: "saikyo"` を足す（ouka・jwrc・wrcr と同じ形。データは `workers/ask/data-saikyo.json` を新しい生成スクリプトで作る）。1日の上限・IP ごとの上限はほかの大会と共有。答えに出た選手のカード（個人成績へ）も houou/ と同じ形。Worker の変更はマージで本番の Worker に入るが、`contest: "saikyo"` を送るページが公開されるまで使われない（要確認）。本番の質問欄での試しがプレビューからできなければ、その旨を一覧に書く（公開前の別の指示で行う）
- 新しいトップと年度別は、マージしても本番の `/saikyo/` が変わらない形で作り、平野さんがプレビュー（ブランチ名の別名 URL、または noindex の仮の URL）で見られるようにする。方法は Code が選び、ログに書く（仮の URL を使うなら、公開の指示で消すことを一覧に書く）
- 対戦成績（/saikyo/vs/、#372・#623）は HTH の系列のチャットが担当。今のトップの入口の行（`build_vs_entry()`）と `VS_PUBLIC` は変えない。新しいトップのメニューの対戦成績の行は、公開まで出ないようにする
- 同じ論点の issue があれば使い、無ければ作成の issue と公開の issue を分けて起票する（docs/new-page-checklist.md、#243）。公開（noindex を外す・サイトマップ・`llms.txt`・トップの切り替え）はこの指示では行わない

## 手順
1. 同じ論点の open issue（最強戦の個人成績・ランキング・質問欄・トップの作り直し）を検索し（クローズ済みも含めて確認）、あれば使う（着手中コメントを書く）。無ければ作成と公開の2件を起票する。上の「前提」の踏襲の元の文書と未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめる
2. 個人成績・ランキング（9部門）・コンシェルジュ（生成・Worker・質問欄・テスト）を作る。「最強戦」シートは2回読み、行数が読み直すたびに変わるなら件数を書いて止まる。ランキングは部門ごとに件数と上位10人（同率は全員）を名前つきで表に書く。同率・境目の値で、ページと質問欄の答えが食い違わないことを確かめる（league-pages-compare.md「新しい大会を足すとき」）
3. 新しいトップと年度別を、本番の /saikyo/ を変えない形で作る。`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`python3 scripts/regenerate.py --check saikyo_pages`（鍵が要ればワークフローの check）を流し、360px・390px の明暗で各画面（新トップ・年度別・個人成績の2タブ・ランキング・質問欄の答えと選手のカード）を撮って作業ブランチの `docs/logs/CHAT-1023-SKP-01/` に置く。仮置きの一覧（全件、1件1行、番号つき）を `## 報告` の「判断が必要なこと」に書き、判断待ちで止まる

## 止まる条件
- 前提が実物と食い違い、決定（上の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
- 「最強戦」シートの行数が読み直すたびに変わる
- テスト・`--check` が通らない（原因を書いて止まる）
- 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の最強戦の節・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントが同じ issue にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 無し（`git log --all --grep=CHAT-1023-SKP`・docs/logs）。`SKP` も未使用
- 作業ブランチ: ローカル・リモートとも無し → `git checkout -b work/1023-skp origin/cloudflare`
- 0章: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し

### 手順1（issue・未マージのブランチ・踏襲の元）

- 同じ論点の issue: 検索（クローズ済みを含む）で無し。近いのは #372・#623（対戦成績、HTH の系列）、#355・#549（今のトップ）。作成 #626・公開 #627 を起票し、#626 に着手中のコメント
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1018-log-367-migrate はログ・docs のみ。1017-lvp が `style.css` の最強戦の節の既存の行（`body.mj-saikyo-n-page` のセレクタを `body:is(.mj-saikyo-n-page, .mj-live-n-page)` に）を変えている。この指示では最強戦の節の既存の行は変えず、新しい規則を別の場所に足す（同じファイルの別の箇所なので止まらない）。`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`workers/ask/` を変えるブランチは無し
- 踏襲の元: league-pages-compare.md・wrcr-top.md・houou-top.md（構成・個人成績・ランキング）・ask-worker.md（構成・Worker）・saikyo-page-design.md（1〜3章）と、生成物の jwrc/ のトップ・個人成績・ランキングの HTML、`assets/league.js`・`houou_ask.js`、`workers/ask/src/` を読んだ
- シートはこのセッションから読めない（鍵 `SHEETS_READER_SA_KEY` が無い）。開発は今の `saikyo/index.html` に焼き込まれた検索のデータ（219対局・440名）から行を組み立てて行い、実データの生成と2回の読み込みは作業ブランチでワークフロー（`regenerate-page.yml`）を動かして確かめる

### 手順2・3（作ったもの）

- 生成 `scripts/generate_saikyo_pages.py`（`write_menu()` ほか）: 年度別 `saikyo/years/`・個人成績 `saikyo/players/`・ランキング9部門 `saikyo/ranking/<スラッグ>/`・新しいトップ（仮の URL `saikyo/next/`）・質問欄の名簿 `saikyo/ask-players.json`。定数 `MENU_PUBLIC = False` の間は全部 noindex・サイトマップに無し・どこからもリンクなし。今のトップ `saikyo/index.html` とサイトマップは変わらない（生成のコミット a42ae9555 の差分に無いことを確かめた）
- 部品は houou/ の HTML の形と CSS を使う（`generate_houou_pages.py` の雛形を読む。`regenerate.py` の `SHARED_GENERATORS` に足した）。個人成績の動きは `assets/saikyo.js` の末尾の節（年度別のタブはトップの検索の結果と同じ部品を関数に出して共有）、ランキングの行の動きは `assets/league.js` をそのまま読む。`assets/houou_ask.js` は名簿の2行をそのまま出す `facts` と、カードの行き先 `data-players-page` の2点だけ足した（ほかの大会の出力は変わらない）
- `style.css` は末尾に規則を2つ足しただけ（`body.mj-saikyo-n-page main a` の紺より houou/ の字の色を優先）。最強戦の節の既存の行は変えていない（work/1017-lvp との兼ね合い）
- 質問欄: `scripts/generate_ask_data_saikyo.py` → `workers/ask/data-saikyo.json`（約23万バイト）。`query.mjs` に年度で数える大会（`contest.yearly`）の読み方と型 `year`、`ask.mjs` に `SAIKYO_PLAN_SYSTEM`・`saikyoLines()`、`index.mjs` に `saikyo`
- `_redirects` に9行（年度別・個人成績・ランキングの入口の 301・`/saikyo/ranking/:slug/`・仮のトップ）。`docs/notes/redirects.md` に `/saikyo/ranking/` の行
- テスト: `scripts/tests/test_saikyo_menu.py`（集計・下限・同率・質問欄のデータとページのランキングの一致）・`test_saikyo_redirects.py`（行の検査）・`test_saikyo_check.py`（名簿の読み込みを差し替え）・`workers/ask/test/saikyo.test.mjs`。どれも新しい機能のテストで、変更前のコードには関数・型が無く通らない
- 文書: `docs/notes/saikyo-page-design.md`「年度別・個人成績・ランキングと新しいトップ」・`docs/notes/ask-worker.md`「最強戦」・`docs/notes/static-generation.md`「ページの一覧」・`docs/decisions/saikyo.md`

### 実データでの確かめ（ワークフロー regenerate-page.yml、work/1023-skp）

- 検査（`check: true`、run 39889772810）: saikyo_pages・ask_data_saikyo とも「通る」（警告6件は既存の ABEMA の回の URL でない卓。今回の変更と無関係）
- 「最強戦」シートの行数: 検査の saikyo_pages で 2580件、ask_data_saikyo で 2580件（2回読んで同じ。生成の run 39889866367 でも同じシートを読んで生成）
- 生成（run 39889866367）がコミット a42ae9555 を作った（14ファイル。saikyo/index.html・sitemap-saikyo.xml・年度・対局・卓のページは変わらない）
- 手元: `python3 -m unittest discover -s scripts/tests`（1527件 OK）・`node --test 'workers/ask/test/*.test.mjs'`（91件 pass）・`python3 scripts/check_asset_limits.py`（_redirects 静的126・動的11、上限内）
- `python3 scripts/regenerate.py --check saikyo_pages` は手元では鍵が無いので、ワークフローの check で行った（上）

### ランキング（実データ、2026年10月18日の対局まで。対象は全出場者438名〈対局済み〉）

| 部門 | 載せた件数 | 上位10人（同率は全員） |
|---|---|---|
| ファイナル出場 | 53 | 1位 瀬戸熊直樹 9年度、2位 近藤誠一 8年度、3位 猿川真寿 6年度、3位 鈴木たろう 6年度、3位 鈴木大介 6年度、6位 佐々木寿人 5年度、6位 前原雄大 5年度、6位 藤崎智 5年度、9位 多井隆晴 4年度、9位 片山まさゆき 4年度 |
| 予選優勝 | 37 | 1位 猿川真寿 7回、2位 瀬戸熊直樹 5回、2位 鈴木大介 5回、4位 片山まさゆき 4回、5位 二階堂亜樹 3回、5位 仲林圭 3回、5位 佐々木寿人 3回、5位 前原雄大 3回、5位 岡田紗佳 3回、5位 茅森早香 3回、5位 近藤誠一 3回 |
| 出場卓 | 53 | 1位 佐々木寿人 38卓、2位 瀬戸熊直樹 36卓、3位 森山茂和 33卓、3位 猿川真寿 33卓、3位 藤崎智 33卓、3位 近藤誠一 33卓、7位 鈴木たろう 32卓、8位 二階堂亜樹 29卓、9位 前原雄大 28卓、10位 滝沢和典 27卓、10位 茅森早香 27卓、10位 鈴木優 27卓、10位 鈴木大介 27卓、10位 高宮まり 27卓 |
| 出場年度 | 45 | 1位 二階堂亜樹 16年度、1位 佐々木寿人 16年度、1位 瀬戸熊直樹 16年度、1位 藤崎智 16年度、5位 森山茂和 15年度、5位 鈴木たろう 15年度、7位 前原雄大 14年度、7位 村上淳 14年度、7位 沢崎誠 14年度、7位 猿川真寿 14年度、7位 茅森早香 14年度、7位 荒正義 14年度、7位 魚谷侑未 14年度 |
| 最強位 | 12 | 1位 桑田憲汰 3回、2位 瀬戸熊直樹 2回、3位 前田直哉 1回、3位 多井隆晴 1回、3位 板川和俊 1回、3位 水沼利晃 1回、3位 沢崎誠 1回、3位 藤田晋 1回、3位 近藤千雄 1回、3位 近藤誠一 1回、3位 金太賢 1回、3位 鈴木大介 1回 |
| 決勝進出 | 42 | 1位 佐々木寿人 13回、1位 森山茂和 13回、3位 瀬戸熊直樹 11回、3位 猿川真寿 11回、3位 近藤誠一 11回、3位 鈴木大介 11回、7位 多井隆晴 10回、7位 鈴木たろう 10回、9位 二階堂亜樹 9回、9位 前原雄大 9回、9位 荒正義 9回、9位 藤崎智 9回、9位 鈴木優 9回、9位 高宮まり 9回 |
| 通過回数 | 45 | 1位 瀬戸熊直樹 20回、2位 猿川真寿 19回、2位 鈴木大介 19回、4位 近藤誠一 18回、5位 森山茂和 17回、6位 佐々木寿人 16回、7位 多井隆晴 14回、8位 二階堂亜樹 12回、8位 前原雄大 12回、8位 片山まさゆき 12回、8位 藤崎智 12回、8位 鈴木たろう 12回、8位 鈴木優 12回、8位 高宮まり 12回 |
| 通過率 | 53 | 1位 桑田憲汰 1.000、2位 鈴木大介 0.704、3位 水口美香 0.636、4位 小宮悠 0.615、5位 渡辺洋香 0.600、5位 近藤千雄 0.600、7位 藤田晋 0.583、8位 猿川真寿 0.576、9位 醍醐大 0.571、10位 瀬戸熊直樹 0.556 |
| 連続出場 | 43 | 1位 二階堂亜樹 16年度、1位 佐々木寿人 16年度、1位 瀬戸熊直樹 16年度、1位 藤崎智 16年度、5位 森山茂和 15年度、5位 鈴木たろう 15年度、7位 前原雄大 14年度、7位 沢崎誠 14年度、7位 猿川真寿 14年度、7位 荒正義 14年度、7位 魚谷侑未 14年度 |

- 同率・境目: 質問欄の `rankings` はページと同じ関数（`build_division_rankings()`）の表で、`test_saikyo_menu.py` の `test_rankings_match_pages` が9部門の一致を見る。質問欄の同率（`tie`）は表の同じ順位の行数で数え（`saikyo.test.mjs`、出場年度の1位4人）、ページの表示（4人とも1位）と食い違わない。上限の境目: 予選優勝・決勝進出・出場年度・通過回数・連続出場は50件目の同率の群れが55件を超えるので群れごと外し（37〜45件）、ファイナル出場・出場卓・通過率は群れごと出した（53件）

### 画面（`docs/logs/CHAT-1023-SKP-01/`、360px・390px の明暗）

- 新トップ `top-*`・質問欄の答えと選手のカード `ask-*`・年度別 `years-*`・個人成績の検索の候補 `players-search-*`・年度別のタブ `players-years-*`・部門別のタブ `players-division-*`・ランキング `ranking-*`（最強位）・`ranking-rate-*`（通過率、`?name=桑田憲汰` で行に色）
- 撮り方: 作業ブランチの生成物を手元の `python3 -m http.server` で配信し、headless Chromium（Playwright）で撮った。長いページは上から 2400px まで。横のはみ出しは全画面 0、JS のエラーなし
- 質問欄の答えは `/api/ask` を差し替えて撮った（答えの文は作り物。Worker は呼んでいない）。候補は連盟の選手の読み「さわざき」で撮った（連盟の外の選手は名前だけで引ける）

## 報告

- 状態: 判断待ち / 続き: CHAT-1023-SKP-02
- ブランチ: work/1023-skp
- ログ: https://github.com/retroeater/mj/blob/work/1023-skp/docs/logs/CHAT-1023-SKP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skp
- 確認用URL: プレビューあり（URL は最終報告）。確認するページ: /saikyo/next/・/saikyo/years/・/saikyo/players/・/saikyo/ranking/champion/。この版で見える印: /saikyo/next/ の題名「最強戦」の下の質問欄の例「例：2025年度の最強位は？」
- マージ: 未（平野さんの判断待ち）
- issue: #626（作成、着手中）・#627（公開、起票）
- 判断が必要なこと:（仮置きの一覧）
  1. 新しいトップは仮の URL `/saikyo/next/`（noindex）に置いた。公開（#627）で `MENU_PUBLIC = True` にして `saikyo/index.html` に書き、`next/` と `_redirects` の next の2行を消す
  2. 新トップのメニューの並び・文言: 年度別「歴代最強位と各年度の対局・卓」→ 個人成績「選手ごとの出場・結果・部門別」→ ランキング「最強位・通過率など9部門」。対戦成績の行は `VS_PUBLIC` まで出さない（公開で4行目「出場者同士の勝敗と同卓の記録」）
  3. 新トップの質問欄の例「例：2025年度の最強位は？」と説明文「麻雀最強戦（2011～2026）の歴代最強位と各年度の対局、出場した440名の個人成績・9部門のランキングをまとめています。」
  4. 年度別は題名「年度別」・パンくず「最強戦 / 年度別」・「歴代最強位」の面だけ（今のトップから検索を外した形）
  5. 年度ページの年度のシートの先頭「歴代最強位」の行き先は今のトップのまま。公開で年度別 `/saikyo/years/` へ替える案
  6. `/saikyo/?q=<名前>`（jpml_pros・卓のページの「すべて見る」）を個人成績へ移す案: 公開で新しいトップの JS が `?q=` を受けて `players/?name=` へ `location.replace`（`_redirects` は行き先にクエリを書くと元のクエリが落ちるため）、卓のページの「すべて見る」は生成で `../players/?name=` に直す
  7. ファイナル出場: 名前が「ファイナル」で始まる対局（「〜 1st Stage」「〜 2nd Stage」、2025年度の「ファイナル 1st Stage」を含む）に出た年度の数
  8. 予選優勝: ファイナル以外の対局のステージ「決勝」の通過。決勝の無い対局（2016〜2019年度のプレミアトーナメントの予選、2011年度の敗者復活戦。その決勝は別の対局「敗者復活戦決勝」で数える）は数えない
  9. 決勝進出: ステージ「決勝」の対局済みの卓の数（ファイナルを含む）
  10. 出場年度・連続出場は進行中の2026年度の対局済みの卓も数える。連続出場は暦の年度が続くか（出場しなかった年度で途切れる）
  11. 通過回数は決勝の通過（優勝）を含む
  12. 通過率の規定は出場10卓以上（88名が対象、載せたのは53件）
  13. 載せる下限は houou/ の考え方（回数は1以上・連続出場は2以上・通過率は0より大きい）、表示の上限は50件・同率の群れは55件まで（jwrc/ と同じ）
  14. 同じ値は同じ順位、並びは名前の文字コード順（houou/ と同じ）
  15. ランキングの時点の書き方「2026年10月18日の対局まで」（対局済みの卓のある最新の対局日）
  16. 部門名「ファイナル出場」は 360px の選ぶ部品の中で2行に折り返す（390px では1行）。名前を短くするか、このままか
  17. 名前の面: 「最強位 N回」は1回以上の選手だけ出し、「出場 N年度（N卓）」は対局済みの卓のある選手だけ
  18. 部門別のタブ: 下限・上限の外の値も出して順位は空欄、通過率の規定（10卓）未満は「規定未達」（houou/ と同じ）
  19. 個人成績から対戦成績（`/saikyo/vs/?name=`）への導線は置いていない
  20. 選手を探す部品: 読み・ローマ字・「（東京・3期）」は名簿にいる連盟の在籍者だけ（156名）。連盟の外の人は名前だけで引ける。候補の右は最後に出た年度（「2026年度」）
  21. 最近見た選手のキーは `mj:recent-players:saikyo`、無いときの1件は最新の最強位（桑田憲汰、印「最強位」）
  22. 未対局の卓だけの選手（2名）も個人成績に載せる（年度別は未対局の卓、部門別は「データがありません」）
  23. 個人成績のデータはページに焼き込む（約28万バイト。今のトップ・対戦成績と同じやり方。houou/ のような選手ごとの JSON にはしない）
  24. 年度別のタブの決勝の通過は「優勝」のピル（予選の決勝の通過も「優勝」と出る。今のトップの検索の結果と同じ）
  25. 質問欄の型は champions・final・player・year（年度の対局と勝ち上がり者、最強戦だけ）・ranking。放送対局（live）・リーグ（league）は無い
  26. 質問欄の champions の出典の名前は「麻雀最強戦 歴代優勝者」（行き先は年度別）、final は「麻雀最強戦2025 決勝結果」（決勝の卓のページ）
  27. Worker の変更はマージで本番の mj-ask に入る。`contest: "saikyo"` を送るのは noindex の `/saikyo/next/` だけ。プレビューからは `/api/ask` に届かないので、本番の質問欄での試しは公開前の別の指示で行う
  28. 質問欄の名簿 `saikyo/ask-players.json` は連盟の外を含む全出場者（440名）。短い名前が答えの文の一部に当たる誤りの見込みは確かめていない
  29. マージしてよいか（判断待ち。マージすると `/saikyo/years/` などが noindex のまま本番に入り、Worker に saikyo が入る）
- 未確認の項目:
  - 実機（iOS Safari など）での見え方と操作（headless Chromium だけで確かめた）
  - 本番の質問欄での答え（Worker の saikyo は本番で試していない。上の27）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 560a6f35）: https://github.com/retroeater/mj-logs/tree/main/guide/560a6f35

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
