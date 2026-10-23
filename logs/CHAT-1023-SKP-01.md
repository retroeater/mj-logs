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

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1023-skp
- ログ: https://github.com/retroeater/mj/blob/work/1023-skp/docs/logs/CHAT-1023-SKP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skp
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e4e91496）: https://github.com/retroeater/mj-logs/tree/main/guide/e4e91496

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e4e91496/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bb98660e.md
