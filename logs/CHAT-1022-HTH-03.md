# CHAT-1022-HTH-03

- 着手日時: 2026-10-22
- 対象issue: #372、#623
- ブランチ: work/1022-hth（CHAT-1022-HTH-02 の続き）
- 着手時HEAD: 2d91d8914

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」の仮置きを決定に合わせて直す（URL を /saikyo/vs/ に、2人の記録の並び）（#372） Chat-Ref: CHAT-1022-HTH-03 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1022-HTH-02 の続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1022-hth を続けて使う（CHAT-1022-HTH-02 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1022-HTH-02 のプレビューを見た平野さんの判断で、仮置き（HTH-02 の `## 報告` の「判断が必要なこと」の仮置きの一覧 1〜16）を確定し、2点を直す。マージはもう一度プレビューを見てから決める。
決定（2026-10-22、平野さん）

* URL は `/saikyo/vs/`（仮置き1の `/saikyo/vs.html` から変える）
* 2人の記録の並びは、同じ対局の中も決勝を上にする（全体を新しい順にそろえる。仮置き12を変える）
* ページの大きさ（約255KB、データを埋め込む）はこのままでよい（仮置き4）
* 勝敗の表し方は「3勝1敗2分」（0の項目も出す）（仮置き6）
* オフシーズン（未対局の卓が無い時期）は「次の対局の見どころ」を節ごと隠す（仮置き9）
* 初期画面の並びは 見どころ → 対戦回数の多いペア → 相手の多い選手、それぞれ10行まで（仮置き10）
* 候補・行を押したらページを読み直す（`?name=` のリンク）（仮置き15）
* navbar には載せない（仮置き16）
* 最強戦のトップの入口は公開のとき（#623）に出す（仮置き5）

前提（チャット側。平野さんの決定ではない）

* 上に無い仮置き（2 パラメータ `?name=A,B`・3 題名・7 優勝の扱い・8 未対局の卓・11 同じ回数の並び・13 右端のピル・14 選手の選び方）は、HTH-02 の形のまま変えない（平野さんは仮置きの一覧を見たうえで、ほかを直すとは言っていない）
* `/saikyo/vs.html` は本番に入っていない（HTH-02 は判断待ちで未マージ）ので、転送は要らない（要確認。origin/cloudflare に `saikyo/vs.html` が無いことを確かめ、あれば止まる）
* `/saikyo/vs/` の配信は、年度ページ（`/saikyo/<年度>/`）と同じく `_redirects` の行が要る（`html_handling: none`。docs/notes/saikyo-page-design.md「新しい年度・対局・卓のページ」）。`/saikyo/vs` → `/saikyo/vs/` の 301 も年度ページと同じ形で置く（チャット側の案）
* チャット側は HTH-02 の画像（init-390・pair-ide-moriyama-360・player-sasaki-1920・edge-unplayed-final-unknown-390）を見た。崩れは見つからなかった

手順

1. CHAT-1022-HTH-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1022-HTH-03` を足す。origin/cloudflare に `saikyo/vs.html` が無いことを確かめる
2. URL を `/saikyo/vs/`（ファイル `saikyo/vs/index.html`）に変える。`_redirects` の行・リンク（パンくず・選手や候補の `?name=` のリンク・canonical・og:url・`VS_PUBLIC` で出すトップの入口）・`remove_stale_sub_pages()` などの片付け・テスト（`_redirects` の行が無いと落ちるもの）・docs（docs/notes/saikyo-page-design.md・docs/notes/static-generation.md「ページの一覧」）と #623 の本文の URL を直す。`vs.html` の名前で docs 全体（`docs/logs/` を除く）と #623 を検索し、出た所を全部直す
3. 2人の記録の並びを、同じ対局の中も決勝が上になるように直し、テストを直す。saikyo_pages を再生成し、前回（HTH-02 の最後のコミット）からの生成物の差分が「vs.html の削除・vs/index.html の追加・並びの変化」だけであることを確かめる。`?name=井出洋介,森山茂和`（2024年度「男の花道」）と `?name=清水香織,奥村知美` を 360px で撮り、`/saikyo/vs` の 301 と `/saikyo/vs/` の 200 をプレビューで確かめる。決定を docs/decisions/saikyo.md に足す

止まる条件

* 前提が実物と食い違う（origin/cloudflare に `saikyo/vs.html` がある、HTH-02 のブランチがリモートに無い 等）
* 生成物の差分に、上の3種類のほかの変化がある（種類と件数を書いて止まる。ただし origin/cloudflare の取り込みで入ったシートの変化による再生成の差分は、取り込み前と後に分けて書けば止まらない）
* 既存の saikyo/ のページ（トップ・年度・対局・卓）の生成物が変わる
* 未マージの work/ ブランチが `_redirects` の最強戦の行、または `generate_saikyo_pages.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（プレビューの確認の後に、平野さんがチャットで決める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1022-HTH-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1022-HTH-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確かめ（2026-10-22）

- 0. ログの「指示」欄の末尾は指示文の最後の行と一致
- 識別子: `git log --all --grep 'CHAT-1022-HTH-03'` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 作業ブランチ: work/1022-hth はローカル・リモートとも 2d91d8914 で一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は真（取り込み不要）
- origin/cloudflare に `saikyo/vs.html`・`saikyo/vs/` は無い（`git ls-tree` で空）。転送は要らない
- 未マージの work/ ブランチで `_redirects`・`generate_saikyo_pages.py` を変えるものは無い
- 手順1: CHAT-1022-HTH-02 の `## 報告` の状態を「判断待ち / 続き: CHAT-1022-HTH-03」にした

### 直したこと

- URL を `/saikyo/vs/`（`saikyo/vs/index.html`）に: 定数 `VS_PAGE` を `VS_DIR = "vs"` に替え、`_redirects` に2行（`/saikyo/vs/  /saikyo/vs/index.html  200`・`/saikyo/vs  /saikyo/vs/  301`、`VS_REDIRECT_LINES`）。資産のパスは `../../`、パンくずの「最強戦」は `../`、ページの中の `?name=` のリンクは `./?name=…`、JS の卓のページへのリンクは `../<年度>/…`、canonical・og:url は `https://ryoei.pro/saikyo/vs/`、トップの入口（`VS_PUBLIC` のとき）は `vs/`、公開時のサイトマップの行も `/saikyo/vs/`
- 片付け: `remove_stale_sub_pages()` に `vs` を渡して消さないようにした。saikyo/ 直下の `*.html` はトップだけに戻した（`vs.html` は次の再生成で消える）
- 代替アバターの URL は階層で変わるため、埋め込みのデータでは空にして頭の1字の丸で出す（`data-avatar-fallback` は `../../img/avatar.svg`）
- 2人の記録の並び: `vs_tables()` が対局の中の卓を `stage_desc()`（ステージの進んだ順、同じステージは A卓 → B卓）で並べる
- テスト: `test_saikyo_vs.py` に並びの検査2件、`test_saikyo_redirects.py` に `/saikyo/vs/` の2行の検査を足し、年度の3行の検査から `vs` を外した。新しい並びの検査は HTH-02 のコード（2d91d8914）では落ちることを確かめた（修正前のコードで通らない）
- docs: `vs.html` で docs（`docs/logs/` を除く）を検索し、出た4か所（saikyo-page-design.md 2・static-generation.md 2）を直した。#623 の題と本文の URL を `/saikyo/vs/` に直した

## 報告

- 状態: 作業中
- ブランチ: work/1022-hth
- ログ: https://github.com/retroeater/mj/blob/work/1022-hth/docs/logs/CHAT-1022-HTH-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-hth
- 確認用URL: なし
- マージ: 未
- issue: #372、#623
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6a274c07）: https://github.com/retroeater/mj-logs/tree/main/guide/6a274c07

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a274c07/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a274c07.md
