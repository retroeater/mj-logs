# CHAT-1016-HOU-58

- 着手日時: 2026-10-16
- 対象issue: #518・#542
- ブランチ: work/1016-hou2
- 着手時HEAD: 4032c9b38

## 指示

【Claude作成】Claude Code 向け指示：work/1016-hou2（CHAT-1016-HOU-54〜57）を cloudflare へマージし、本番の質問欄で4問を確かめる（houou/ は未公開のまま） Chat-Ref: CHAT-1016-HOU-58 マージ: 承認済み（チャットで。2026-10-16 に平野さんが、HOU-54・HOU-56 の PNG を見たうえで、HOU-57 が終わったら HOU-54〜57 をまとめてマージしてよいと回答） 貼る時機: CHAT-1016-HOU-57 の判断待ちの後（同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou2 の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-hou2 を続けて使う（HOU-54〜57 のマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-57 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-54〜57 の変更（ランキング9部門の名前を略称に統一・規定のある部門の見出しの ⓘ〈部門名の左、値と右そろえ〉・トップの説明「通算・期最高など9部門」・質問欄で選手の一覧を10人で切り「ほか N人」と全体の人数をプログラムで数えて渡す直しのやり直し・歴代の鳳凰位は10人を超えて切るときだけ新しい順）を cloudflare へ入れ、本番の質問欄で確かめる。houou/ は未公開のまま。
決定（2026-10-16、平野さん）

* work/1016-hou2（HOU-54〜57）をまとめてマージしてよい
* マージの後、本番の質問欄で4問を確かめる。前と同じ問いで悪くなっていれば、HOU-53 と同じく質問欄の直し（10人で切る・並べ替え）だけを戻す（部門名の統一と ⓘ は残す）

前提（チャット側。平野さんの決定ではない）

* 本番で送る4問: 「現在の鳳凰位は？」（白鳥翔と答える）／「鳳凰位になった人は全部で何人？」（23人・名前10人・ほか13人、最新の白鳥翔が入る）／「第1期から第10期の鳳凰位は？」（6人を古い順に）／「通算得点の上位15人は？」（名前10人・ほか5人・全15人）。答えの全文、カードの行と「ほか N名」が文と合うか、指示や仕組みの言葉（「結果に含まれる」「検索結果」など）が答えに出ないかを書く。送った時刻をログに書く（試行のまとめから除くため）
* 「悪くなった」の目安は HOU-52・HOU-53 と同じ（前と同じ問いで事実の誤り・「答えられません。」への変化・長さの大きな伸び）。仕組みの言葉が出るだけなら戻さず、報告に書く
* 戻すときは、質問欄の直しのコミットだけを戻すコミットを作り、テストを通して cloudflare へ入れる。戻しの push で `mj-ask` のビルドが走ったか（HOU-53 では走らなかった）を確かめ、走らなければ報告の「判断が必要なこと」に、平野さんに頼む画面（Cloudflare → Workers & Pages → `mj-ask` → Deployments）と操作を書く
* マージの後に `mj-ask` のビルドが走らない（check-run が付かない）ときも同じく報告に書き、4問は本番の Worker が新しい版になってから送る（待つのは15分まで）

手順

1. 取り込みと再検証: CHAT-1016-HOU-57 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-58` を足す。#518・#540・#542 に他のセッションの着手中のコメントが無いか確かめる。origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通し、`python3 scripts/regenerate.py houou_pages` と `python3 scripts/regenerate.py ask_data` で生成し直して、差分（origin/cloudflare との比較）を種類に分けてログに書く。
2. マージ: push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1016-hou2:cloudflare` で入れる。HOU-54〜57 のログの `## 報告` の状態を、このマージで入ったことが分かる形に直す。
3. マージの後の確かめ: Workers Builds の本番の check-run（`mj`・`mj-ask`）が success か。本番（`?v=<未使用の値>`）の `houou/ranking/term-plus-rate/` が 200 で noindex があり、見出しが「ⓘ 期浮き率」であること、トップの説明が「通算・期最高など9部門」であること。本番の質問欄で前提の4問を送って確かめる（「悪くなった」に当たれば前提のとおり戻す）。マージの後の `regenerate-page.yml` の結果をログに書く（待つのは15分まで）。#518 と #542 に、マージの SHA と入った中身・4問の結果の要約のコメントを書く（末尾に `Chat-Ref:` の行）。

止まる条件

* CHAT-1016-HOU-57 のログの状態が判断待ちでない、または work/1016-hou2 がリモートに無い
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 取り込んだ後のテストが通らない
* 取り込んだ後の生成の差分に、HOU-54〜57 の決定とシートの変化で説明できない種類がある。ランキングの値・順位が変わったら止まる。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗・配られている版の食い違い。今回の変更による失敗と無関係な失敗を分け、無関係なら原因を報告に書いて残りの手順を進めてよい。今回の変更によるものなら、直さずに報告する（質問欄の直しを戻すのは、前提の「悪くなった」に当たったときだけ）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージの結果を書く docs/logs のみの追いの push は可）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-58.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-58 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-57 と同じセッションで続けた。`CHAT-1016-HOU-58` のコミットは無し。`work/1016-hou2` はリモートにありローカルと一致（4032c9b38）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0112228f）: https://github.com/retroeater/mj-logs/tree/main/guide/0112228f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0112228f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
