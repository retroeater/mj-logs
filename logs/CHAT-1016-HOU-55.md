# CHAT-1016-HOU-55

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1016-hou2
- 着手時HEAD: c17889727

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1016-HOU-54 の続き — 質問欄で選手の一覧を10人で切る直し（HOU-53 で戻したもの）を、歴代の鳳凰位は新しい期から並べてから切る形でやり直す（作業ブランチでテストまで。マージは HOU-54 と一緒に別の指示で） Chat-Ref: CHAT-1016-HOU-55 マージ: 判断待ちで止まる（HOU-54 の見た目の確かめが済んだ後に、HOU-54 と一緒にマージの指示で入れる。本番の確かめはマージの後） 貼る時機: CHAT-1016-HOU-54 の判断待ちの後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou2 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-hou2 を続けて使う（HOU-54 と同じく `workers/ask/` を触るため、同じブランチで続ける）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-54 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-53 の直し（選手の一覧を AI に渡すときに10人で切り、「ほか N人」と全体の人数をプログラムで数えて渡す）は、数え違いと指示の言葉の漏れを直したが、歴代の鳳凰位の一覧（`champions`）が古い順のため、10人で切ると最新の鳳凰位（白鳥翔）が AI に届かず、「現在の鳳凰位は？」に答えられなくなって戻した。歴代の鳳凰位は新しい期から並べてから切る形でやり直す。
決定（2026-10-16、平野さん）

* HOU-53 の直し（10人で切る・「ほか N人」と全体の人数をプログラムで数えて渡す・AI に数えさせない）をやり直す
* 歴代の鳳凰位の一覧は、新しい期から並べてから10人で切る（チャット側の案 (a)。Code の案 (b)〈最新の鳳凰位を別の値で渡す〉・(c)〈この一覧だけ切らない〉は採らない）
* 期などで絞る問い（「第1期から第10期の鳳凰位は？」など）は、絞った後に並べて切る

前提（チャット側。平野さんの決定ではない）

* HOU-53 の直しのコミット（戻す前の 1447edda1..2ae48afbb の内容）を土台にする。戻しのコミットを打ち消す形でも、書き直しでもよい（どちらにしたかをログに書く）
* 並べ替えは `champions` の型だけ（新しい期が先）。ランキングなど、もともと上位から並ぶ型は今の順のまま切る。ほかに「古い順に並ぶ選手の一覧」の型があれば数えてログに書き、扱いの案を報告に書く（この指示では `champions` だけ直す）
* 答えの並び: 「全部で何人？」などで名前を挙げる順は新しい期からになる。AI への指示は HOU-53 の文面（items の名前を結果の順のまま書き、others があれば「ほか N人」と全体の人数、自分で数えない、指示の言葉を書かない）のまま
* テスト（Claude は呼ばない）: 歴代の鳳凰位が11人以上のとき、渡す10人に最新の期の鳳凰位が入ること・新しい期から並ぶこと・「ほか N人」と全体の人数が正しいこと、期で絞ったときは絞った中で同じこと、ランキングの型は今の順で切ること。HOU-53 で足したテストも通す
* HOU-54 の部門名の変更とぶつかる所があれば、HOU-54 の決定（略称が正式名・旧い名前は別名で受ける）に合わせる
* 本番には出さない（マージは別の指示）。マージの後の本番の確かめでは、「現在の鳳凰位は？」「鳳凰位になった人は全部で何人？」「第1期から第10期の鳳凰位は？」「通算得点の上位15人は？」を必ず入れる案（マージの指示で決める）

手順

1. CHAT-1016-HOU-54 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-55` を足す。#542 に他のセッションの着手中のコメントが無いか確かめる。
2. 直しとテスト: 前提のとおりに直し、`node --test 'workers/ask/test/*.test.mjs'` と `python3 -m unittest discover -s scripts/tests` を通す。決定を `docs/decisions/houou.md` に足し、`docs/notes/ask-worker.md` の該当の節を今の内容を読んでから直す。
3. 手元で、歴代の鳳凰位の検索の結果（AI に渡す形）を「現在の鳳凰位は？」「鳳凰位になった人は全部で何人？」「第1期から第10期の鳳凰位は？」の3つの条件で出し、渡す名前・others・count をログに書く（Claude は呼ばない）。

止まる条件

* CHAT-1016-HOU-54 のログの状態が判断待ちでない、または work/1016-hou2 がリモートに無い
* #542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 直しが `workers/ask/`・テスト・docs の外に及ぶ
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、HOU-54 の判断待ちの点をそのまま引き継ぎ、ほかに古い順の選手の一覧の型があればその扱いの案を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-55.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-55 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-54 と同じセッションで続けた。`CHAT-1016-HOU-55` のコミットは無し。`work/1016-hou2` はリモートにありローカルと一致（c17889727）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 374ad1fb）: https://github.com/retroeater/mj-logs/tree/main/guide/374ad1fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
