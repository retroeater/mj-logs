# CHAT-1016-HOU-62

- 着手日時: 2026-10-16
- 対象issue: #540
- ブランチ: work/1015-hou5
- 着手時HEAD: 436f57f50

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1016-HOU-61 の続き — 告知動画のランキングの切り替えを「期平均」→「期最高」にし、通算のタブのグラフを全体が映るまでスクロールして映し、houou-promo-v4.mp4 を作る（ほかは v3 で確定） Chat-Ref: CHAT-1016-HOU-62 マージ: 判断待ちで止まる（制作のスクリプトのマージは、動画の確定の後、公開〈#540〉の指示で入れる） 貼る時機: いつでも（CHAT-1016-HOU-61 は判断待ち。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou5 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou5 を続けて使う（HOU-48・HOU-60・HOU-61 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-61 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-61 の houou-promo-v3.mp4 への回答で、平野さんは2か所の直しを求め、ほかは確定とした。その2か所を直した版を作る。
決定（2026-10-16、平野さん）

* 動画の最後のランキングの切り替えは「期浮き率」ではなく、「期平均」→「期最高」と映して終わりにする（「期浮き率」は ⓘ を見せるためのもので、ⓘ を外したため）
* 「通算」のタブのグラフが見切れているので、グラフの全体が映るところまでスクロールして映す
* ほかは houou-promo-v3.mp4 のままで確定

前提（チャット側。平野さんの決定ではない）

* 場面6（通算）: 通算のタブを開いた後、グラフの全体（上の端から下の端・軸の目盛りまで）が実画面の枠に入るところまでスクロールし、そこで止めて映す。スクロールの動きは他の場面と同じ滑らかさ。テロップ「通算得点の推移」はそのまま。グラフの全体が1画面に入らない大きさなら、入らない旨と映した範囲をログに書いて進めてよい
* 場面9（ランキング）: 通算の行 → 白鳥翔の行 → 上へ、までは v3 のまま。その後「期平均」のチップを押す → 表 → 「期最高」のチップを押す → 表。ⓘ は押さない（期平均の見出しに ⓘ が出るのはそのままでよい）
* 場面10（範囲）: 期最高の表で止め、範囲のテロップ（v3 と同じ「第17期後期〜第43期前期・691名」）を出す
* 質問欄の答えは HOU-60 で取った答えを撮る側で返す（本番の質問欄へは送らない）
* ほかの場面・テロップ・曲・形は v3 のまま。長さは60秒以内（切り替えが1つ増える分、長くなってよい）
* 出力のファイル名は `houou-promo-v4.mp4`。動画・連番・素材はコミットしない

手順

1. 着手の確かめ: CHAT-1016-HOU-61 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-62` を足す。#518・#540・#542 に他のセッションの着手中のコメントが無いか確かめる。
2. 直しと制作: 前提のとおりに `scripts/promo_video/houou/` を直して作る。リーグの再生のアイコンの座標と白いままのコマが無いことを HOU-61 と同じく確かめてログに書く。決定を `docs/decisions/houou.md` に足す（CLAUDE.md の判断待ちの決まりのとおり）。
3. 確かめと送付: mp4 の長さ・解像度・fps・ラウドネスと、場面ごとの秒とテロップの表をログに書く。場面6のスクロールの後のコマ（グラフの上下の端が枠の中にあることを px で書く）、場面9の期平均・期最高の表、場面10の1コマを PNG にして見る。mp4 を `SendUserFile` で送る。

止まる条件

* CHAT-1016-HOU-61 のログの状態が判断待ちでない、または work/1015-hou5 がリモートに無い
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 撮るために本番の質問欄へ送る必要が出た、またはページのコード・生成物・本番の設定を変えないと撮れない（変えずに止まり、何が要るかを報告する）
* 出来た動画が60秒を超える（詰めて60秒以内に収める。収まらなければ、詰め方の案を報告に書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、v4 で確定してよいか（見る物は送った mp4）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-62.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-62 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 「指示」欄の末尾と指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）が一致することを確かめた。CHAT-1016-HOU-61 のログの状態は「判断待ち（送った動画 houou-promo-v3.mp4 で確定してよいかを決める）」
- Chat-Ref の重複なし（`git log --all --grep`）。work/1015-hou5 はリモートにある（436f57f50、ローカルと同じ）
- CHAT-1016-HOU-61 のログの状態に ` / 続き: CHAT-1016-HOU-62` を足した

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f03198fd）: https://github.com/retroeater/mj-logs/tree/main/guide/f03198fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
