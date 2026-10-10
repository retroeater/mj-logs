# CHAT-1010-WHS-01

- 着手日時: 2026-10-10
- 対象issue: #530、#186、#283・#160（G5-09、要確認）
- ブランチ: work/1010-whs
- 着手時HEAD: 116fea10

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」（video_wayhome.html・wayhome/ の39ページ）の共有ボタンを外して cloudflare へマージし、#530 を更新する。あわせて G5-09 の決定の記録と、NVDA の確認（#186）を保留にする Chat-Ref: CHAT-1010-WHS-01 マージ: 承認済み（チャットで、2026-10-10。プレビューを見ずに本番に出してよい） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。この指示はセッションの最初の指示なので、識別子 WHS が他のセッションで使われていないかを CLAUDE.md「Chat-Ref」節のとおり確かめる。

目的
「帰り道」のページ全体の共有ボタンは、ブラウザの共有と同じ動きしかしないので外す（#530 のうち帰り道の分）。issue の整理を2件、同じ指示で行う。
決定（2026-10-10、平野さん）

* 帰り道（一覧 `video_wayhome.html`・各話 `wayhome/*.html`）の共有ボタンは外す。ブラウザの共有ボタンと効果が変わらないため
* このチャットで扱うのは帰り道だけ。live/ の共有ボタンと、共通部品（`scripts/lib/share.py`・`assets/share.js`）の扱い、#526 の G3-07・G4-09 は #530 で別に扱う
* G5-09（呼称のゆれ。`video_wayhome` は nav「帰り道」、title・h1「帰り道ついていってイイっすか」）は気にしない。連盟の公式がそう呼んでいるので、今の呼称をそれぞれ正式なものとみなす
* NVDA（読み上げソフト）での確認は、サイト全体で issue を1つにまとめて「保留」にする（やるときはまとめてやる）。保留の理由は優先順位の関係。#186 は #524 を待つ
* マージはプレビューを見ずに行ってよい

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-10 に cloudflare（0498c32 時点）で読んだ事実（実物と食い違えば止まる）:
   * 共有ボタンを出しているのは `scripts/generate_video_wayhome.py`（固定バーの `.mj-video-filter-search` の中に `share.build_share_button(...)`・`share.SHARE_STATUS_HTML`・`share.script_tag("")`）と、`scripts/generate_wayhome_episodes.py`（ヒーローの操作列に `share.build_share_button(..., style=share.STYLE_HERO)`・`SHARE_STATUS_HTML`・`share.script_tag(ASSET_PREFIX)`）
   * 帰り道だけが使っていそうなもの: `scripts/lib/share.py` の `STYLE_HERO`、`style.css` の `.mj-share-btn-hero`、`.mj-video-filter-search`（要確認。ほかのページ・live/ が使っていれば残す）
   * 帰り道の名前を書いたコメント: `assets/share.js` 2行目、`scripts/lib/share.py` 1行目、`style.css` の共有ボタンの節の先頭のコメント
   * `scripts/tests/test_title_years.py` の `test_no_share_button_on_title_pages` は、共通部品の利用の目印として `video_wayhome.html` に `mj-share-btn` が残っていることを確かめている。外すと落ちる
* 帰り道の一覧は `?name=` を受け付けず（#162）、表示中の状態を URL に持たない。各話も URL の変種を持たない。共有ボタンは正規の URL と `<title>` を渡すだけで、ブラウザの共有と同じ（CHAT-0929-SH-02 の実装）
* 未マージの `work/1010-sks`（別のチャット、#530 の最強戦の分、判断待ち）が、同じ `test_title_years.py` の行（`saikyo/index.html` を `saikyo/2025.html` に）と、`docs/handover.md` の共有ボタンの行と、`style.css`（`.mj-saikyo-*` の節）を変えている。こちらとは両立する
* #530 の本文の「洗い出しの候補」に帰り道の行がある。#530 のコメントには最強戦の分が表で書かれている（CHAT-1010-SKS-01）。帰り道の分も同じ形で書くとよい
* G5-09 は横断レビュー（CHAT-1006-SWP-01）の指摘で、CHAT-1009-SWP-03 で #283・#160 にコメントされた（要確認）
* NVDA の確認の issue は #186（「実機の支援技術でアクセシビリティを通し確認する」、Open、ラベルは「分野: UI/UX」「対象: 全ページ」）。手順は `docs/notes/a11y-manual-check.md`。#524 は共通ナビとスキップリンクの直し（横断レビューの第2弾）
* 帰り道の生成は Google スプレッドシートを読むため、再生成するとシートの変化（新しい回・表記の直し）が混ざることがある

手順

1. 確かめる: #530 が Open で、他セッションの着手中コメントが無い。`git branch -r --no-merged origin/cloudflare` の各ブランチが、手順2で変えるファイルの同じ行・同じ関数を変えていないか、または取り込みで衝突しないかを確かめる（`work/1010-sks` は上の前提のとおり両立する。それ以外で重なれば止まる）。上の「前提」の事実を確かめる。#530 に着手中のコメントを残す。
2. 外して記録する:
   * 一覧・各話の生成から、共有ボタン・トースト・`share.js` の読み込みをやめる。帰り道だけが使っていた CSS・定数（`STYLE_HERO`・`.mj-share-btn-hero` など）は消す。ほかでも使っているものは残す。固定バーとヒーローの操作列の並びが崩れないこと（一覧の固定バーは検索欄だけになる）
   * 共通部品のコメントから帰り道を外す（`assets/share.js` は帰り道を外す行だけ直す。動きは変えない）。テストは、帰り道の全ページ（一覧と39話）に共有ボタン・`share.js` が無いことを足し、共通部品の利用の目印からは `video_wayhome.html` を外す
   * 帰り道の2ページを再生成する。文書（`docs/notes/video-wayhome.md`、`docs/handover.md` の共有ボタンの行、`docs/notes/design.md` の共有ボタンの行など、帰り道の共有ボタンに触れている所。今の内容を読んでから直す）を直す。決定を `docs/decisions/` の合う分野に足す（README のとおり）
   * issue: #530 の本文の帰り道の項目にチェックを入れ、最強戦の分と同じ形の表でコメントする。G5-09 の決定を、G5-09 をコメントした issue（#283・#160、要確認）にコメントする。#186 に「状況: 保留」のラベルを付け、理由（優先順位の関係。NVDA での確認はサイト全体でここにまとめ、やるときはまとめて行う。#186 は #524 を待つ）をコメントする
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおりマージし、check-run（Workers Builds）を待つ（上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番（`curl`、URL に `?v=<未使用の値>`）で、`/video_wayhome.html` と各話1枚（例 `/wayhome/atD2e-NgnKw.html`）が 200 で共有ボタン・`share.js` が無く、`/live/` と `/saikyo/2025.html` には共有ボタンが残ることを確かめる。#530 は閉じない（live/ などが残る）。作業ブランチを片付ける

止まる条件

* 上の「前提」の事実と実物が食い違う（ただし行番号や関数名の小さな違いは、意図が同じなら進めてよい。どう読み替えたかをログに書く）
* #530 に他セッションの着手中コメントがある
* 未マージのブランチ（`work/1010-sks` を除く）が、手順2で変える同じ行・同じ関数を変えている、または取り込みで衝突する
* 取り込みで生成物でない文書・テスト・CSS が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行・`test_title_years.py` の目印のリストのように両方の変更を合わせられる行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ前の差分に、決定とシートの変化で説明できない変更がある（見込み: 生成スクリプト2本・`scripts/lib/share.py`・`assets/share.js`（コメントのみ）・`style.css`・テスト・`video_wayhome.html`・`wayhome/` の39枚（共有ボタン・トースト・`share.js` の読み込みが消える。シートの変化による行は許す。変わった画像 URL があれば 200 を確かめる）・sitemap の lastmod・文書・docs/decisions・docs/logs）。ページの数が変わったら止まる
* 自分の変更で落ちると分かっているテスト（上の `test_title_years.py`）以外のテストが落ちる
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら、原因を報告に書いて残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow origin` の後、全ブランチのコミットに `WHS` は 0 件、`docs/logs/` の履歴にも無し。`work/1010-whs` はローカル・リモートとも無し → `git checkout -b work/1010-whs origin/cloudflare`（116fea10）
- 指示欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも揃っている
- 前提の cloudflare は 0498c32 時点、着手時は 116fea10（以降のコミットで前提が変わっていないかは次で確かめる）

## 報告

- 状態: 作業中
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 未
- issue: #530
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 116fea10）: https://github.com/retroeater/mj-logs/tree/main/guide/116fea10

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/116fea10/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/116fea10.md
