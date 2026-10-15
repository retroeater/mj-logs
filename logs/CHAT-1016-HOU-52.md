# CHAT-1016-HOU-52

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1015-hou6
- 着手時HEAD: 4ed96cc22

## 指示

【Claude作成】Claude Code 向け指示：質問欄の選手カード（work/1015-hou6、HOU-49〜51）に「名前は10人まで」を足して cloudflare へマージし、本番で10問を確かめる。あわせて試行の記録から何が取れるかを調べる（houou/ は未公開のまま） Chat-Ref: CHAT-1016-HOU-52 マージ: 承認済み（チャットで。2026-10-16 に平野さんが、HOU-50・HOU-51 の PNG を見たうえで、カードを今日マージする進め方〈公開までの前倒し〉を選んだ。マージに含めるのは、HOU-49〜51 の変更と、同じ日に平野さんが決めた AI への指示の「名前は10人まで」の1行だけ） 貼る時機: CHAT-1016-HOU-51 の判断待ちの後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou6 の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou6 を続けて使う（HOU-49〜51 の続きとマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: work/1015-hou5 がマージされていれば `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-51 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
質問欄の選手カードを本番に入れ、AI への指示の変更を本番の10問で確かめる。あわせて、公開までの前倒し（下の決定）のために、試行の記録から何が取れるかを調べる。
決定（2026-10-16、平野さん）

* カードの見た目は HOU-50 の PNG のまま、「ほか N名」の右端に下向きの矢印（HOU-51）でよい
* 答えの文に書く選手の名前は10人まで。それより多いときは上位10人の名前と「ほか N人」、全体の人数を書く
* AI への指示の変更の確かめは、マージの後に本番で10問を送り、試行中の答えと比べる。悪くなっていれば、AI への指示の変更の行だけを元に戻す
* 公開までを前倒しする: カードのマージ → 本番での10問 → 平野さんの iPhone での最終形の確かめ → 今ある記録で試行をまとめ、1日の上限を決める → 告知動画の撮り直し → 公開と告知。10/20 まで1週間の試行を待たない（2026-10-13 の AIC-05 の「1週間の試行のまとめ」を置き換える。houou/ は未公開で、質問はほぼ平野さんと確かめの分だけのため）
* 週次の書き出し（月曜 9:00 JST の cron、最初の本番の実行は 2026-10-19 の見込み）の確かめは、公開の条件から外し、公開とは別に行う

前提（チャット側。平野さんの決定ではない）

* 「10人まで」は `workers/ask/src/ask.mjs` の `ANSWER_SYSTEM` の HOU-49 で足した行を直す形（行を増やさない案）。テストで確かめられる範囲（指示の文面）を足す
* 10問の選び方: HOU-49 のログの3問（「現在の鳳凰位は？」「通算得点の上位5人は？」「第1期から第10期の鳳凰位は？」）を必ず入れ、前の答えと並べる。残り7問は、選手が1人・2〜3人・4〜10人・11人以上になる問いと、選手の出ない問いを混ぜる（使った問いと答えの全文をログに書く。カードの行数・「ほか N名」が答えの文と合うかも書く）
* 「悪くなった」の目安: 前と同じ問いで事実の誤りが出た、答えられた問いが「答えられません。」になった、答えが「3文程度まで」を大きく超えた、のどれか。当たれば AI への指示の変更の行だけを戻すコミットを作り、テストを通して cloudflare へ入れてよい（カードの画面側は残す）。戻したことを報告に書く
* 10問は本番の1日の上限に数える（送った時刻をログに書く。試行のまとめから除けるように）
* 試行の記録から取れるもの（調べるだけ。まとめは次の指示）: 2026-10-11 以降の質問の件数・答えの種類（answered／unanswerable／失敗）・入出力トークン数・答えられなかった質問、の取り方（D1・Workers Logs・Console の使用額のどれで、このセッションから読めるか）。読めないものは、平野さんに頼む画面と手順の案を書く

手順

1. 着手の確かめ: #542・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、HOU-49 の手順1のファイルの同じ箇所の変更があるかを確かめ、ログに書く。CHAT-1016-HOU-51 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-52` を足す。
2. 「10人まで」の1行の直しとテスト。決定を `docs/decisions/houou.md` に足し（AIC-05 の「1週間の試行」と、週次の書き出しを公開の条件に含める扱いの置き換えは docs/decisions/README.md のとおり）、`docs/notes/ask-worker.md`・`docs/notes/houou-top.md` を今の内容を読んでから直す。
3. 取り込み・再検証・マージ: origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通し、`python3 scripts/regenerate.py houou_pages` で生成し直して差分（origin/cloudflare との比較）を種類に分けてログに書く。push の直前に再 fetch して確かめ、`git push origin work/1015-hou6:cloudflare` で入れる。HOU-49〜51 のログの `## 報告` の状態を、このマージで入ったことが分かる形に直す。
4. マージの後の確かめ: Workers Builds の本番の check-run（`mj` と `mj-ask` の両方）が success か。本番（`?v=<未使用の値>`）の `houou/` が 200 で noindex があり、`houou/ask-players.json` が配られていること。本番の質問欄で前提の10問を送り、答えとカードを確かめる（カードは本番の画面で撮って見る。PNG は送らなくてよい）。「悪くなった」に当たれば前提のとおり戻す。マージの後の `regenerate-page.yml` の結果をログに書く（待つのは15分まで）。
5. 試行の記録から何が取れるかを調べてログに書く（前提のとおり。まとめはしない）。#542 に、マージの SHA・10問の結果の要約・試行の記録の取り方の要約のコメントを書く（末尾に `Chat-Ref:` の行）。

止まる条件

* CHAT-1016-HOU-51 のログの状態が判断待ちでない、または work/1015-hou6 がリモートに無い
* #542・#540 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 未マージの work/ ブランチが、HOU-49 の手順1のファイルの同じ行・同じ関数・同じ節を変えている（別の箇所の変更では止まらず、ログに書く）
* 取り込んだ後のテストが通らない
* 生成の差分に、HOU-49〜51 の決定・「10人まで」の1行・シートの変化で説明できない種類がある。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗・配られている版の食い違い。今回の変更による失敗と無関係な失敗を分け、無関係なら原因を報告に書いて残りの手順を進めてよい。今回の変更によるものなら、直さずに報告する（AI への指示の行を戻すのは、前提の「悪くなった」に当たったときだけ）
* 本番の質問欄が上限・失敗で10問を送り切れない（送れた分で報告する）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージの結果を書く docs/logs のみの追いの push は可）。「判断が必要なこと」に、10問の結果で気になる点と、試行のまとめに使える記録と取り方（平野さんに頼むことがあればその画面と手順）を書く
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-52.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-52 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-51 と同じセッションで続けた。`CHAT-1016-HOU-52` のコミットは無し。`work/1015-hou6` はリモートにありローカルと一致（4ed96cc22）

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-51 のログの `## 報告` の状態は判断待ち。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. #542・#540 の着手中は自分のもの（#542 は HOU-49、#540 は HOU-48）だけ。未マージの work/ ブランチで HOU-49 の手順1のファイルを変えるのは、`work/1015-hou5`（自分の HOU-48。`docs/decisions/houou.md` の末尾の追記）と、`work/1015-skd-top`・`work/1015-skr-pub`・`work/JBK` の `style.css`（差は 1522〜3367行あたりの saikyo・video・title の節。houou/ の節〈4000行〜〉ではない）。止まらない。HOU-51 のログの状態の末尾に `/ 続き: CHAT-1016-HOU-52` を足した
- 2. `workers/ask/src/ask.mjs` の `ANSWER_SYSTEM` の HOU-49 の行を「選手を挙げるときは名前を書く。名前は結果の順のまま10人まで。それより多いときは上位10人の名前と「ほか N人」、全体の人数を書く」に直した（行は増やしていない）。`ask.test.mjs` に文面のテストを1件足した（28件 OK）。`docs/decisions/houou.md` に決定を足し、2026-10-13（AIC-05）の4条件の行と 2026-10-14 の「#542 の残り…10/19・10/20」の行に置き換えの印を付けた。`docs/notes/ask-worker.md`（指示の行・費用の数字を見る時期）と `docs/notes/houou-top.md`（公開までの順）を直した
- 3. `origin/cloudflare` を merge で取り込んだ（衝突なし。`work/1015-hou5` はまだ cloudflare に入っておらず、decisions の衝突は起きなかった）。`python3 -m unittest discover -s scripts/tests` OK、`node --test 'workers/ask/test/*.test.mjs'` 28件 OK。`python3 scripts/regenerate.py houou_pages` の差分は無し。origin/cloudflare との差の種類:
  - `houou/ask-players.json`（新規）、`houou/index.html`（質問欄の2行）
  - `assets/houou_ask.js`・`assets/houou_ask.css`、`style.css`（houou/ の節のチップの色の選択子1行）
  - `workers/ask/src/ask.mjs`（指示1行）・`workers/ask/test/ask.test.mjs`
  - `scripts/generate_houou_pages.py`・`scripts/tests/test_houou_ask.py`
  - docs（decisions・notes 2・ログ4）
  - houou/ の noindex の行の増減・navbar・サイトマップ・`llms.txt` の差は無し
## 報告

- 状態: 作業中

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
