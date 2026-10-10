# CHAT-1008-DIC-24

- 着手日時: 2026-10-10
- 対象issue: #515・#522
- ブランチ: work/1008-dic
- 着手時HEAD: e2cd255f

## 指示

【Claude作成】Claude Code 向け指示：DIC-23 の続き。告知動画の最初の画面の2行目を直して v2 を作る。判断待ちで止まる Chat-Ref: CHAT-1008-DIC-24 マージ: 判断待ちで止まる（制作のスクリプトはマージしない） 貼る時機: いつでも（CHAT-1008-DIC-23 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-23 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-23 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
DIC-23 の初版（`dictionary-promo-v1.mp4`）の最初の画面の2行目「スマホ・PC の変換に登録」は、日本語として不自然で不完全な印象がある。文言だけを直して v2 を作る。
決定（2026-10-10、平野さん）

* 最初の画面（0〜2.5秒）の2行目を「麻雀用語をスマホ・PCで一発変換」にする

前提（チャット側。平野さんの決定ではない）

* 投稿文の1行目の箇条も同じ言い回しに揃える: 「・麻雀用語・連盟・Mリーグの1,870語をスマホ・PCで一発変換」（語数は今の値に合わせる）。平野さんが投稿時に直すことがある
* 文言のほかは v1 のまま（秒・画面・テロップ・曲・締め）。最初のコマからタイトルの2行をはっきり出すこと（フェードインで薄いコマを作らない）は v1 と同じ。2行目が長くなるので、1行に収まらなければ文字の大きさを下げてよく、改行するなら「麻雀用語を／スマホ・PCで一発変換」で切る。どうしたかを報告する
* 語数は撮り直す時点の `dic/` から数える（v1 と同じ仕組み）。変わっていれば報告する

手順

1. 確かめる: CHAT-1008-DIC-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-24` を足す。上の決定を `docs/decisions/` に足す（DIC-23 の決定が未記録なら合わせて足す）。未マージの work/ ブランチが `scripts/promo_video/` の共有の部品の使う箇所を変えていないか確かめる（同じファイルの別の箇所の変更では止まらず、ログに書く）。
2. 作る: 2行目の文言を直して書き出し、`dictionary-promo-v2.mp4` とする。最初のコマ・最後のコマ・各場面の代表のコマを画像で確かめる（特に最初のコマで2行がはっきり読めること）。形式（1080×1920・30fps・H.264・yuv420p・AAC・-16 LUFS 前後）が v1 と同じであることを確かめる。
3. 送る: mp4 を `SendUserFile` で送る。ログに v1 からの変更点と投稿文（前提のとおり直したもの）を書き、判断待ちで止まる。

止まる条件

* CHAT-1008-DIC-23 の状態が「判断待ち」でない
* 未マージの work/ ブランチが `scripts/promo_video/` の共有の部品の使う箇所を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 語数を数える段階で `dic/` とページの説明文の語数が食い違う（v1 の仕組みのとおり止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（制作のスクリプトはマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #515・#522
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5697aa0c）: https://github.com/retroeater/mj-logs/tree/main/guide/5697aa0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5697aa0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19111d74.md
