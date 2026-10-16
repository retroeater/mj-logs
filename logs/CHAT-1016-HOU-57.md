# CHAT-1016-HOU-57

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1016-hou2
- 着手時HEAD: f6b5731ca

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1016-HOU-56 の続き — 質問欄の歴代の鳳凰位の並べ替えを「10人を超えて切るときだけ新しい順、10人以下は古い順のまま」に直す（作業ブランチでテストまで） Chat-Ref: CHAT-1016-HOU-57 マージ: 判断待ちで止まる（HOU-54〜56 と一緒に、HOU-56 の PNG を見た後のマージの指示で入れる） 貼る時機: CHAT-1016-HOU-56 の判断待ちの後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou2 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-hou2 を続けて使う（HOU-54〜56 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-56 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-55 で、質問欄の歴代の鳳凰位（`champions`）を、10人以下でも新しい順に並べ替える作りにした。そのため「第1期から第10期の鳳凰位は？」の答えが第10期から逆にたどる順になる。期の範囲を聞く問いは古い順のままにする。
決定（2026-10-16、平野さん）

* 歴代の鳳凰位は、10人を超えて切るときだけ新しい順に並べ替えてから10人で切る。10人以下のときは並べ替えず、古い順（今までの検索の並び）のまま渡す（HOU-55 の「10人以下でも並べ替える」を置き換える）

前提（チャット側。平野さんの決定ではない）

* 直すのは `workers/ask/src/ask.mjs` の `answerInput()` の `champions` の並べ替えの条件だけ。ほかの型・AI への指示・画面は変えない
* テスト（Claude は呼ばない）: 11人以上は新しい順に10人・最新の鳳凰位が先頭・others と count が正しい（HOU-55 のまま）／10人以下（期で絞った6人、ちょうど10人）は古い順のまま・others なし。HOU-55 で直した「23期生で鳳凰位になった人は？」のテストは、2人なので古い順（吉田直・白鳥翔）に戻る見込み（要確認、食い違えば理由をログに書く）
* 手元で、HOU-55 の手順3と同じ3つの条件（条件なし／条件なし／第1期〜第10期）で AI に渡す名前・others・count を出してログに書く

手順

1. CHAT-1016-HOU-56 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-57` を足す。#542 に他のセッションの着手中のコメントが無いか確かめる。
2. 直しとテスト: 前提のとおりに直し、`node --test 'workers/ask/test/*.test.mjs'` と `python3 -m unittest discover -s scripts/tests` を通す。決定を `docs/decisions/houou.md` に足し（HOU-55 の決定の置き換えは docs/decisions/README.md のとおり）、`docs/notes/ask-worker.md` の該当の行を直す。手元で前提の3つの条件の結果を出してログに書く。

止まる条件

* CHAT-1016-HOU-56 のログの状態が判断待ちでない、または work/1016-hou2 がリモートに無い
* #542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 直しが `workers/ask/`・テスト・docs の外に及ぶ
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、HOU-54〜56 の判断待ちの点をそのまま引き継ぐ
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-57.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-57 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-56 と同じセッションで続けた。`CHAT-1016-HOU-57` のコミットは無し。`work/1016-hou2` はリモートにありローカルと一致（f6b5731ca）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b77586e6）: https://github.com/retroeater/mj-logs/tree/main/guide/b77586e6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b77586e6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
