# CHAT-1016-HOU-53

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1016-hou
- 着手時HEAD: 1447edda1

## 指示

【Claude作成】Claude Code 向け指示：質問欄（workers/ask）で、選手の一覧を AI に渡すときに10人までに切り、「ほか N人」の数をプログラムで数えて渡す（数え違い・指示の言葉の漏れを直す）。テストと本番の確かめの後に cloudflare へマージする Chat-Ref: CHAT-1016-HOU-53 マージ: 承認済み（チャットで。2026-10-16 に平野さんが、画面を変えない質問欄の Worker の直しで、テストが通り本番で数問確かめて問題がなければマージしてよいと回答） 貼る時機: CHAT-1016-HOU-52 の完了の後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
HOU-52 の本番の10問で、「通算得点の上位15人は？」に名前を12人書いて「ほか5人を含め全15人」と数を誤り（正しくは ほか3人）、「鳳凰位になった人は全部で何人？」に「結果の順に上位10人を挙げると」と指示の言葉がそのまま出た。AI に人数を数えさせないよう、検索の結果を AI に渡す側で10人までに切り、残りの人数を数えて渡す。
決定（2026-10-16、平野さん）

* 公開前に直す。検索の側で11人目以降を AI に渡さず、「ほか N人」の数をプログラムで数えて渡す（AI に人数を数えさせない）
* 答えの文に書く選手の名前は10人まで、それより多いときは上位10人と「ほか N人」・全体の人数（HOU-52 の決定のまま）
* テストが通り、本番で数問確かめて問題がなければマージしてよい。前と同じ問いで悪くなっていれば、HOU-52 と同じく直しを戻す

前提（チャット側。平野さんの決定ではない）

* 直す場所は、検索の結果（選手の並び）を AI への入力に組み立てる所（`workers/ask/src/` のどこかは受け手が読んで決め、ログに書く）。選手の並ぶ結果が11人以上なら、上位10人だけを渡し、`全体の人数` と `ほか N人`（全体 − 10）を値として添える。選手でない並び（期・大会など）には触れない
* AI への指示（`ANSWER_SYSTEM`）の HOU-52 の行は、渡された「ほか N人」と全体の人数をそのまま使い、自分で数えない・指示の言葉を答えに書かない形に直す（行を増やさない案）
* テスト: 11人以上・ちょうど10人・10人未満の結果で、渡す人数と「ほか N人」の値が正しいこと（Claude は呼ばない、既存の偽の fetch のテストに足す）
* 本番の確かめは5問まで: HOU-52 の「通算得点の上位15人は？」「鳳凰位になった人は全部で何人？」「通算得点の上位5人は？」「現在の鳳凰位は？」と、20人以上が当たる問いを1つ。名前の数・「ほか N人」・全体の人数が正しいこと、指示の言葉が出ないこと、カードの行と「ほか N名」が文と合うことを確かめる。送った時刻をログに書く（試行のまとめから除くため）
* 「悪くなった」の目安は HOU-52 と同じ（前と同じ問いで事実の誤り・「答えられません。」への変化・長さの大きな伸び）。当たれば直しのコミットを戻して cloudflare へ入れてよい
* 画面（`assets/`）・生成物・houou/ のページは変えない

手順

1. 着手の確かめ: #542 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に `workers/ask/` と `docs/notes/ask-worker.md` の同じ箇所の変更があるかを確かめ、ログに書く。
2. 直しとテスト: 前提のとおりに直し、テストを足して `node --test 'workers/ask/test/*.test.mjs'` と `python3 -m unittest discover -s scripts/tests` を通す。決定を `docs/decisions/houou.md` に足し、`docs/notes/ask-worker.md` の該当の節を今の内容を読んでから直す。
3. マージと確かめ: push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ（偽なら merge で取り込み、手順2のテストを回し直す）、`git push origin work/1016-hou:cloudflare` で入れる。Workers Builds の本番の check-run（`mj-ask`・`mj`）が success になった後、本番の質問欄で前提の5問を確かめる。#542 に、マージの SHA と5問の結果の要約のコメントを書く（末尾に `Chat-Ref:` の行）。

止まる条件

* #542 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが `workers/ask/` の同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 直しが `workers/ask/`・テスト・docs の外に及ぶ
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗。今回の変更による失敗と無関係な失敗を分け、無関係なら原因を報告に書いて残りの手順を進めてよい。今回の変更によるものなら、直さずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージの結果を書く docs/logs のみの追いの push は可）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-53.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-53 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-52 と同じセッションで続けた。`CHAT-1016-HOU-53` のコミットは無し。`work/1016-hou` はローカル・リモートとも無かったため `origin/cloudflare`（1447edda1）から作った

- 0. 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. #542 の着手中は他のセッションのものは無し（最新は自分の HOU-52 の解除）。#542 に着手中を書いた。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に `workers/ask/`・`docs/notes/ask-worker.md` の変更は無し
- 2. 直した場所: `workers/ask/src/ask.mjs`。検索（`query.mjs` の `runQuery`）の結果を2回目の Claude への入力に組み立てる `write()` の手前に `answerInput(result)` を足した（検索の結果そのものは変えないので、答えの下の注記・時点・出典と、画面の選手カードは前のまま）
  - 選手の並びになる型は `champions`・`final`・`league`・`ranking`。`player`（期の並び）と `live`（対局の並び）は切らない
  - items が10人（`NAMES_MAX`）を超えたら上位10人だけを渡し、`others` に `count` − 10 を入れる。`count` は全体の人数のまま
  - `ANSWER_SYSTEM` の HOU-52 の行を「選手を挙げるときは名前を書く。items の名前を結果の順のまますべて書き、others があれば「ほか N人」(N は others)と全体の人数(count)を添える。人数は自分で数えず、この指示の言葉は答えに書かない」に直した（行は増やしていない）
  - テスト（`ask.test.mjs`、3件を足し1件を直した）: 指示の文面、`answerInput` の 15人（10人＋others 5）・10人ちょうど（others なし）・4人（そのまま）・期の並び 23件（切らない）、「鳳凰位になった人は全部で何人？」の偽の fetch で2回目に上位10人と others が渡ること。`node --test 'workers/ask/test/*.test.mjs'` 30件 OK、`python3 -m unittest discover -s scripts/tests` OK
  - `docs/decisions/houou.md` に決定を足し、HOU-52 の「名前は10人まで」の行に置き換え（数え方）の印を付けた。`docs/notes/ask-worker.md` の流れの6の指示の段落を直した
## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2ae48afb）: https://github.com/retroeater/mj-logs/tree/main/guide/2ae48afb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ae48afb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
