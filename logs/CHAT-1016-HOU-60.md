# CHAT-1016-HOU-60

- 着手日時: 2026-10-16
- 対象issue: #518・#540・#542
- ブランチ: work/1015-hou5
- 着手時HEAD: 771ce544e

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1015-HOU-48 の続き — 鳳凰戦 houou/ の告知動画を、選手カード・部門名の統一が入った本番で撮り直す（質問欄の答えは白鳥翔 → カードから個人成績へ） Chat-Ref: CHAT-1016-HOU-60 マージ: 判断待ちで止まる（平野さんが動画を見て、直すか確定かを決めるため。docs/notes/page-announcement.md「進め方」） 貼る時機: いつでも（CHAT-1016-HOU-59 は完了。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou5 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou5（HOU-48 の制作のスクリプト）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1015-HOU-48 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-48 の初版（houou-promo-v1.mp4）は、質問欄の答えが HIRO柴田で、質問欄から個人成績へは「個人成績」の項目と候補から進んでいた。その後、質問欄の選手カード（HOU-49〜52）と部門名の統一・ⓘ（HOU-54〜58）が本番に入った。答えを白鳥翔にし、カードから個人成績へ進む流れで撮り直す。
決定（2026-10-15〜16、平野さん）

* 質問欄の答えは白鳥翔にする（2026-10-15）
* 答えに出た選手のカードから個人成績へ進む流れにする（カードを作ったうえで撮り直す、2026-10-15 の Q9 (a)）
* そのほかは HOU-48 の決定のまま（30〜60秒・未公開の本番で撮る・見本の選手は白鳥翔・質問欄を映す・曲は今までと同じ）
* 公開（#540）と告知は同時に行う。動画が確定してから公開の指示を出す（2026-10-16）

前提（チャット側。平野さんの決定ではない）

* 構成は HOU-48 の初版（経過の場面の表）を土台に、場面3・4を次のように直す案（秒は実物に合わせてよい。60秒以内）:
   * 場面3 質問欄: 押す印 → 質問を1字ずつ → 送る → 答え → 白鳥翔のカードに枠（テロップ案「質問すると、データから答える」）
   * 場面4 選手を開く: カードを押す → 個人成績（期別）が開く（テロップ案「答えから選手の成績へ」）。初版の「個人成績の項目 → 鳳凰位の候補」は使わない
* 質問: 答えに白鳥翔だけが出て、カードが白鳥翔の1行になる問いを選ぶ（例「白鳥翔の最高到達は？」「第42期の鳳凰位は？」など。「現在の鳳凰位は？」は直前の鳳凰位も挙げてカードが2行になることがある）。本番の質問欄に送るのは5回まで。使える答えが出たら、その答えを撮る側で返して撮る（HOU-48 と同じ。撮るたびに本番へ送らない）。選んだ問いと答えの全文、送った回数と時刻をログに書く（試行のまとめから除くため）
* 場面8・9・10 は、部門名が略称になった今の画面で撮る（部門別のタブ・ランキングのチップと見出し）。ランキングで切り替える部門は ⓘ のある部門（期浮き率など）にし、ⓘ を押して規定の1行が開く所を短く入れてよい（入れるかは受け手が決め、ログに書く）
* 範囲のテロップは撮る日のトップの説明文から作る
* 形・最初のコマ・テロップの置き場・撮り方・締めは HOU-48 と docs/notes/page-announcement.md「告知動画の型」のまま。制作のスクリプトは `scripts/promo_video/houou/` を直す。動画・連番・素材はコミットしない
* 出力のファイル名は `houou-promo-v2.mp4`

手順

1. 着手の確かめ: CHAT-1015-HOU-48 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-60` を足す。#518・#540・#542 に他のセッションの着手中のコメントが無いか確かめる。本番の houou/ の各ページと `assets/` が cloudflare の最新の版で配られていることを確かめる。
2. 撮影と制作: 前提のとおりに構成を直して撮り、作る。リーグの再生のアイコンの座標（落ち始め・着地・寄った後・終わり）と、白いままのコマが無いことを HOU-48 と同じく確かめてログに書く。
3. 確かめと送付: mp4 の長さ・解像度・fps・ラウドネスと、場面ごとの秒とテロップの表をログに書く。最初のコマと各場面の1コマを PNG にして見て、テロップが見せたい所を隠していないこと・下の端から10%以上離れていることを確かめる。mp4 を `SendUserFile` で送る。`docs/notes/page-announcement.md` の実例の表と `docs/decisions/houou.md` は、確定の後のマージの指示で直す（この指示では制作のスクリプトと README だけ）。

止まる条件

* CHAT-1015-HOU-48 のログの状態が判断待ちでない、または work/1015-hou5 がリモートに無い
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 本番の houou/ が cloudflare の最新の版で配られていない
* 質問欄が5回までで白鳥翔だけの答えを返さない（このときは白鳥翔を含む答えで一番カードの行が少ないものを使い、理由を報告に書いて進めてよい）
* ページのコード・生成物・本番の設定を変えないと撮れない（変えずに止まり、何が要るかを報告する）
* 出来た動画が60秒を超える（詰めて60秒以内に収める。収まらなければ、詰め方の案を報告に書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、動画を確定するか直すか（見る物は送った mp4）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-60.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-60 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 「指示」欄の末尾と指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）が一致することを確かめた。CHAT-1015-HOU-48 のログの状態は「判断待ち（送った動画 houou-promo-v1.mp4 を見て、確定か直すかを決める）」
- Chat-Ref の重複なし（`git log --all --grep`）。work/1015-hou5 はリモートにある（771ce544e）。ローカルの同名ブランチは origin と同じ先頭

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dde77936）: https://github.com/retroeater/mj-logs/tree/main/guide/dde77936

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
