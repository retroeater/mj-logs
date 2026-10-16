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

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-49 のログの `## 報告` の状態は判断待ち。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. HOU-49 のログの状態の末尾に `/ 続き: CHAT-1016-HOU-50` を足した。#542 の着手中は自分の HOU-49 のものだけ（他のセッションのものは無し）。この指示はコメントを足していない（HOU-49 の着手中が続いているため）
- 2. 撮り方: プレビュー（ブランチの別名 URL、HOU-49 の版のまま）を幅 390px・3倍（1170px 幅）・明／暗で開き、HOU-49 のログの本番の答え（全文そのまま）を撮る側で `/api/ask` の POST に返した。本番の質問欄へ送った回数は 0。範囲は入力欄の上 12px からトップの3項目の頭（1行目の上 60px）まで。コード・生成物は変えていない。撮るスクリプトは `/home/user/ask-hou50/shot.mjs`（使い捨て。リポジトリに入れない）
  - 各画面で、カードの面が範囲の中に収まり（下端・右端とも）、横のはみ出しが無いこと、カードの写真がすべて読み終わっていること（`complete` かつ幅 80）を確かめた。4 の荒正義は写真が無く頭文字の丸
  - 送ったファイル（SendUserFile、9枚）:
    - ask-card-1-light.png・ask-card-1-dark.png（1人「現在の鳳凰位は？」→ 白鳥翔）
    - ask-card-2-light.png・ask-card-2-dark.png（5人「通算得点の上位5人は？」、開く前の3行＋「ほか 2名」）
    - ask-card-3-light.png・ask-card-3-dark.png（同じ答え、開いた後の5行）
    - ask-card-4-light.png・ask-card-4-dark.png（退会者を含む「第1期から第10期の鳳凰位は？」→ 荒正義・伊藤優孝の2行）
    - ask-card-5-light.png（字 200%、1人の答えとカード）

## 報告

- 状態: 判断待ち（送った PNG 9枚でカードの見た目を確かめて決める。マージは 10/20 の試行の判断の後に別の指示で） / 続き: CHAT-1016-HOU-51
- ブランチ: work/1015-hou6
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-HOU-50.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou6
- 確認用URL: なし（見る物は送った PNG 9枚。ask-card-1〜5）
- マージ: 済（CHAT-1016-HOU-52 のマージ、82bde6810..f003b3c74）
- issue: なし（#542 の HOU-49 の着手中が続いている）
- 判断が必要なこと（HOU-49 から引き継ぐ）:
  - カードの見た目と「ほか N名」の開き方（押すと残りの行に置き換わり、閉じない）でよいか
  - 何人まで名前を書かせるかの案: 10人まで名前を書き、それより多いときは上位10人の名前と「ほか N人」・全体の人数にする（実例は5人で約100字・6人で約110字。指示の「3文程度まで」に収まるのは10人前後まで）
  - AI への指示の変更の確かめ方の案: (a) マージの指示で、平野さんが `workers/ask/.dev.vars` に鍵を置いた環境で作業ブランチの Worker を `wrangler dev` で動かし、10問を変更の前後で比べる（本番の回数に数えない）／(b) マージの後に本番で10問を送って前の記録と比べる（1日の上限に数える）。(a) を推す
- 未確認の項目: 平野さんの iPhone での見え方（マージの後）。AI への指示の変更による答えの変化
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f003b3c7）: https://github.com/retroeater/mj-logs/tree/main/guide/f003b3c7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
