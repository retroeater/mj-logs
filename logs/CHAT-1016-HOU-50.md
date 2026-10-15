# CHAT-1016-HOU-50

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1015-hou6
- 着手時HEAD: e93fb7173

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1015-HOU-49 の続き — 質問欄の選手カードの画面を PNG で平野さんに送る（見た目の確かめ用） Chat-Ref: CHAT-1016-HOU-50 マージ: 判断待ちで止まる（HOU-49 のまま。マージは 10/20 の試行の判断の後に別の指示で） 貼る時機: CHAT-1015-HOU-49 の判断待ちの後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou6 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou6 を続けて使う（HOU-49 の続き）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。この指示ではコード・生成物を変えないので、origin/cloudflare は取り込まなくてよい。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1015-HOU-49 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-49 の選手カードは、プレビューのホストに質問欄の Worker が無いため、平野さんとチャット側がプレビューで送っても見られない。HOU-49 の確かめと同じ方法（本番の答えを撮る側で `/api/ask` に返す）で画面を撮り、PNG で平野さんに送る。
決定（2026-10-16、平野さん）

* カードの見た目は、Code が撮った画面の PNG で確かめる（マージ後の実機の確かめは別に行う）

前提（チャット側。平野さんの決定ではない）

* 撮る画面（幅 390px、iPhone 13 の幅・3倍の高解像度。明・暗の2通りずつ）:
   1. 1人: 「現在の鳳凰位は？」の答えと白鳥翔のカード（答えの面からトップの3項目の頭まで入る範囲）
   2. 5人・開く前: 「通算得点の上位5人は？」の答えと3行＋「ほか 2名」
   3. 5人・開いた後: 同じ答えで5行
   4. 退会者を含む: 「第1期から第10期の鳳凰位は？」の答えと2行（写真の無い頭文字の丸を含む）
   5. 字 200%（明だけ）: 1人の答えとカード
* 答えは HOU-49 のログにある本番の答えの全文をそのまま使い、本番の質問欄には送らない（送る回数 0）
* PNG はリポジトリに入れず、`SendUserFile` でまとめて送る（9枚。ファイル名は `ask-card-<番号>-<light|dark>.png` の形）

手順

1. CHAT-1015-HOU-49 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-50` を足す。#542 に他のセッションの着手中のコメントが無いか確かめる。
2. 前提の9枚を撮り、各画面でカードの面が切れていないこと・写真が読み終わっていることを確かめて送る。送ったファイル名の一覧をログに書く。

止まる条件

* CHAT-1015-HOU-49 のログの状態が判断待ちでない、または work/1015-hou6 がリモートに無い
* #542 に他のセッションの着手中のコメントがある
* 撮るためにコード・生成物を変える必要がある（変えずに止まり、何が要るかを報告する）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、HOU-49 の残り（カードの見た目と「ほか N名」の開き方・何人まで名前を書かせるか・AI への指示の変更の確かめ方）をそのまま引き継いで書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-50.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-50 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-49 と同じセッションで続けた。`CHAT-1016-HOU-50` のコミットは無し。`work/1015-hou6` はリモートにありローカルと一致（e93fb7173）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a94b0a64）: https://github.com/retroeater/mj-logs/tree/main/guide/a94b0a64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a94b0a64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a94b0a64.md
