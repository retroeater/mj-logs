# CHAT-1017-HOU-68

- 着手日時: 2026-10-17
- 対象issue: #541・#542
- ブランチ: work/1017-hou
- 着手時HEAD: 0dee9eda7

## 指示

【Claude作成】Claude Code 向け指示：HOU-67 の続き — 部門別の表（場面8）を画面に収めて撮る動きを capture.mjs に足し、#541 へのコメント・質問欄のばらつきの issue を書いて、work/1017-hou を cloudflare へマージする
Chat-Ref: CHAT-1017-HOU-68
マージ: 承認済み（チャットで。2026-10-17 に平野さんが HOU-67 で「テストが通り、止まる条件に当たらないときだけ」マージを承認し、同日、止まった点〈部門別の表の検査〉に (a) を選んだ。下の「止まる条件」に当たればマージしない）
貼る時機: いつでも（CHAT-1017-HOU-67 は判断待ちで止まっている。続きなので同じセッションでも新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の push と、cloudflare へのマージの push と、issue の起票・コメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1017-hou を続けて使う（HOU-67 の docs と検査のコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。あわせて、このセッションが新しく始めたものか、HOU-67 の続きかをログに書く。

## 目的

CHAT-1017-HOU-67 が止まった点（撮影の枠の検査で、場面8 部門別の表 `.mj-houou-div-list` が画面に収まらない）を平野さんの判断どおりに直し、HOU-67 の残り（#541 へのコメント、質問欄のばらつきの issue）を行って、work/1017-hou を cloudflare へ入れる。

### 決定（2026-10-17、平野さん）

- 部門別の表の検査は (a): 部門別のタブを押した後に、表全体が画面に入るまで送る動きを `capture.mjs` に足す（次に撮るときから効く）。告知動画 v5 は撮り直さない
- マージは HOU-67 の承認のまま（テストが通り、止まる条件に当たらないときだけ）

### 前提（チャット側。平野さんの決定ではない）

- HOU-67 のログ「手順3」の記録: 部門別の表は上端 374・下端 886（高さ約 512）、画面は 390×664。送れば収まる見込み（要確認）。送る量は表の矩形から計算し、決め打ちの値にしない
- 送る動きは場面8 の中だけで足し、ほかの場面の動き（スクロール量・待ち時間）は変えない。部門別の表を見せる時間（今の 3.0 秒）も変えない。送る動きの長さ（秒）は既存の送りの書き方に合わせる
- 動きを足した後も、HOU-67 で足した `mustFit` の検査（全場面）が通ること。場面8 の「見せる物」は表全体のまま（上の行だけに絞らない）
- README の場面と要素の対応の表に、場面8 は送った後に表全体を見る旨を書く
- #541 へのコメントと、ばらつきの issue の中身は、CHAT-1017-HOU-67 のログの「指示」欄の「前提（チャット側…）」の「5 の issue」の項のとおり（HOU-67 の手順4 で重複は無いと確かめ済み。起票の直前にもう一度検索する）。#541 のコメントには、houou/ の公開は済んだので公開後に回す決定のとおり進め方を決める段階にあり、進め方は 2026-10-17 にチャットで決める旨を書く
- `docs/decisions/houou.md` に、上の「決定」を足す（HOU-67 で足した決定の後に）

## 手順

1. 着手の確かめ: work/1017-hou の先頭が HOU-67 のコミット（975c8c69c）を含むかを確かめる。#541・#542 に 2026-10-17 の HOU-67 以後に他のセッションの着手中のコメントが無いか確かめる。`git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/promo_video/houou/` の同じ関数・`docs/decisions/houou.md` の同じ節を変えていないか確かめ、ログに書く。
2. `capture.mjs` に場面8 の送りを足し、HOU-67 と同じ回し方（描画・音は回さない。質問欄は撮る側で答えを返し、本番へは送らない）で撮影の流れを最後まで流す。全場面の検査が通ること、`scenes.json` の場面8 以外の値が HOU-67 の確認の流し（v5 と同じ）と同じであることを確かめ、コマ数の変化と場面8 の表の矩形（送った後）をログに書く。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。README・`docs/decisions/houou.md` を直してコミットする。
3. #541 にコメントし、ばらつきの issue を起票する（コメント・本文の末尾に `Chat-Ref: CHAT-1017-HOU-68` の行）。CHAT-1017-HOU-67 のログの `## 報告` の「状態」の行の末尾に ` / 続き: CHAT-1017-HOU-68` を足す。push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` でマージする。マージで変わるのは撮影のスクリプト・README・docs・ログだけなので、ページの再生成は無い見込み（マージ後の自動処理で変わったものがあればログに書く）。

## 止まる条件

- work/1017-hou がリモートに無い、または HOU-67 のコミットを含まない
- #541・#542 に他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが `scripts/promo_video/houou/` の同じ関数・`docs/decisions/houou.md` の同じ節を変えている、または取り込みで衝突する（両立する衝突は上のとおり解いてよい）
- 送る動きを足しても部門別の表が画面に収まらない（表が画面より高い等）、または場面8 以外の場面の動き・`scenes.json` の値が変わる（変えずに止まって、数値をログに書く）
- 変更が `scripts/promo_video/houou/`・`docs/notes/ask-worker.md`・`docs/notes/static-generation.md`・`docs/new-page-checklist.md`・`docs/decisions/houou.md`・`docs/logs/` の外に及ぶ
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- ばらつきの issue の起票の直前の検索で、同じ主題の issue が見つかった（その issue にコメントし、起票はせずに進めてよい。止まらない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、起票した issue の番号（またはコメントした既存の番号）を書く
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-68.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-68 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。このセッションは HOU-67 と同じセッションの続き（新しく始めたものではない）。work/1017-hou はリモートにあり、ローカルと一致（0dee9eda7）、HOU-67 のコミット 975c8c69c を含む。雛形の行は揃っている。「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-hou
- ログ: https://github.com/retroeater/mj/blob/work/1017-hou/docs/logs/CHAT-1017-HOU-68.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-hou
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e61b4b99）: https://github.com/retroeater/mj-logs/tree/main/guide/e61b4b99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6e1da5ac.md
