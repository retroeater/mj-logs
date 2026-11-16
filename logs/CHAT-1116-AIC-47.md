# CHAT-1116-AIC-47

- 着手日時: 2026-11-16（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: b2698d07e

## 指示

【Claude作成】Claude Code 向け指示：（週次の直し、2026-11-16 の書き出しから）質問欄の3点。player の答えの冒頭のフルネームをプログラムが書く、「内田みこ」に答えられなかった原因を直す、画面の統合が済んでいれば大会別の要求を Worker から外す。cloudflare へマージする Chat-Ref: CHAT-1116-AIC-47 マージ: 承認済み（チャットで。2026-11-16、平野さん）。次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1116-AIC-46 のマージ〈39f873cd5〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。39f873cd5 が origin/cloudflare の祖先であることを確かめる。識別子 `AIC` はこのチャットの系列（AIC-01〜46 で使用。AIC-17・24・31 は欠番）。CHAT-1109-AIC-44 のログには手を入れない（持ち越しの一覧は 11/23 の書き出しで2週目を見る）。

目的
2026-11-16 の週次の書き出し（再実行で入れた 11/09〜11/15 の52行。うち7行は AIC-40・42 の本番の確かめ、来訪者の問いは45行）を、チャット側が平野さんの CSV で読んだ。来訪者の問いから次の3点を直す。

1. player の答えに内部の言葉が残り、フルネームを書いていない（来訪者の問い）
   * 2026-11-10 22:34「hiro」（`all:houou:player`）: 「検索語「hiro」の選手は、第18期から第42期までの28期に出場しています。…第42期の得点は185.5で、これはその期の得点であり通算ではありません。第39期の鳳凰位です。」（HIRO柴田さんの名前が無い）
   * 2026-11-12 08:50「中田」（`all:houou:player`）: 「「中田」で検索された選手は1人で、出場は10期です。…」（フルネームが無い）
   * 名前の扱いの揺れは「鮎川」（AIC-40）に続く2回目。docs/notes/ask-worker.md「運用の学び」の「同じ所で2回揺れたら、プログラムが文を作る形に移す」に当たる
2. 2026-11-09 01:18「内田みこ」（型 `all:houou:player`、引数 `{"houou":{"name":"内田みこ"},"ouka":{"name":"内田みこ"},"jwrc":{"name":"内田みこ"},"saikyo":{"name":"内田みこ"}}`）の答えが「【鳳凰戦】答えられません。」。平野さんによると、内田みこさんは鳳凰戦・女流桜花に出場している
3. 大会別の要求（型が `all:` で始まらない行）は、2026-11-02〜08（AIC-40 の目的「来訪者の問いは、すべて横断から来ている」）と 2026-11-09〜15 の2週続けて0行

決定（2026-11-16、平野さん）

* 1: player の答えの冒頭の「〇〇（フルネーム）さんは」をプログラムが書く（持ち越さない）
* 2: 原因を調べて直す
* 3: 画面の統合が済んでいれば、大会別の要求を Worker から外す（docs/notes/ask-worker.md「横断」の「消すのは画面の統合の後、週次の記録で `all:` で始まらない型の行が0になった週の後」）
* この指示は、上の「マージ:」の行の条件でマージしてよい
* 次の週次の確認は 2026-11-23 6:00 JST（朝一の確認は 6:00 にそろえる）

前提（チャット側。平野さんの決定ではない）

* 1 の形の案: 名前で1人に決まった player の答え（横断の各大会と大会別の欄）で、最初の文の主語「<フルネーム>さんは」をプログラムが付け、Claude には主語の後から書かせる。または Claude の文の冒頭を確かめ、フルネームで始まっていなければ付け直す。どちらにするかは実物に合わせて決めてよい（理由をログに書く）。今の冒頭の文を作る関数は `nameLead()`、2回目の指示は `answerSystem()`・`ANSWER_LINES`、横断の2回目は `writeMany()`（docs/notes/ask-worker.md の表。関数名・行番号は（要確認）。開いた関数名と行番号をログに書く）
* 1 とあわせて、「検索語」「検索された」「通算ではありません」を内部の言葉の一覧（`INTERNAL_PHRASES`、（要確認））に足す
* 2 の原因は分からない（名前の読み・表記の寄せ、横断の1回目の選び方、`resolveName()` など（要確認））。データで内田みこさんの鳳凰戦・女流桜花の記録を確かめ、記録が無ければ止まる
* 3 は、画面のファイル（assets/、TOP 系列の受け持ち）が大会別の要求（`contest` が `all` でない要求）をもう送っていないことを、origin/cloudflare のすべての HTML・JS で確かめられたときだけ行う。1つでも送っていれば 3 は行わず、送っているファイルと行をログと #654 のコメントに書く（画面は変えない）
* 答えが変わってよい問い: (a) 名前で1人に決まった player の答えの冒頭（フルネームの主語）と、内部の言葉が出ていた答え (b) 内田みこさんの答え (c) 3 を行ったとき、大会別の要求（`contest` が `all` でない要求）への応答
* 本番の確かめは1日10問まで（同じ接続元から）

手順

1. 実物で確かめる: 上の前提（関数・一覧・内田みこさんのデータ・画面の要求の送り方）を開いて確かめ、食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/src/` の同じ関数を変えていないか確かめる。2 は、作り物の1回目で今週の引数を再現して原因を書く
2. 直す: 1・2（と、条件を満たせば 3）。テストを足す: 「hiro」「中田」の形（名字・略称で1人に決まる）でフルネームで始まる、内部の言葉の一覧で今週の言い回しが見つかり今の固定の問いの正しい答えでは当たらない、内田みこさんの横断の答えが記録を返す、3 を行ったなら大会別の要求の扱い。docs/notes/ask-worker.md の該当の行と「答えの事実を誰が書くか」の表、docs/decisions/houou.md（上の「決定」）を直す。テストが通れば cloudflare へマージする
3. 本番の確かめ（マージ後、Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、横断・context 無し、UA に `mj-internal`）: 「hiro」「中田」「内田みこ」「鮎川」「白鳥翔の第40期の成績は？」の5問。答えがデータと一致するか、フルネームで始まるか、内部の言葉が出ないかを書く。結果を #654 にコメントする

止まる条件

* 前提（関数・一覧・データ）が実物と食い違う。内田みこさんの記録がデータに無い
* 未マージの work/ ブランチが `workers/ask/src/` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 画面のファイル（assets/）を変えないと直せない（TOP 系列に回す内容を #654 にコメントして止まる）
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、2026-11-23 の書き出しで「名字・略称の player の答えがフルネームで始まるか」「内部の言葉が残っていないか」を見ることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-AIC-47.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-AIC-47 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ae7af4d）: https://github.com/retroeater/mj-logs/tree/main/guide/8ae7af4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ae7af4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8ae7af4d.md
