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

## 経過

### 着手前の確認（0章）

- ログの「指示」欄の末尾は指示文の最後の行と一致。39f873cd5 は origin/cloudflare の祖先
- 同じ Chat-Ref のコミット・ログ: なし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 作業ブランチ: origin/work/1116-aic はマージ済み（cloudflare の祖先）。ローカルの work/1116-aic も cloudflare の祖先だったので `git merge --ff-only origin/cloudflare`（docs/notes/cloud-sessions.md「作業ブランチの用意」のローカルにあり祖先の場合）。着手時 HEAD b2698d07e
- #654: open。着手コメントを残した

### 実物の確認（手順1）

- 関数（`workers/ask/src/` の着手時の行）: `nameLead()` query.mjs:136（部分一致の読み方の冒頭の文「「hiro」をHIRO柴田として答えます」。答えの本文の主語ではない）、`answerSystem()` ask.mjs:123、`ANSWER_LINES` ask.mjs:164、`answerInput()` ask.mjs:435、`write()` ask.mjs:543、`crossSystem()` ask.mjs:899、`writeMany()` ask.mjs:905、`INTERNAL_PHRASES` ask.mjs:512、`resolveName()` query.mjs:110
- **1 の原因**: 2回目に渡す結果（`answerInput()`）は items・count・seasons などだけで、選手名（`result.name`）を渡していない。名字・略称の問いでは Claude はフルネームを知らず、問いの文字で主語を書いた（「検索語「hiro」の選手は」）
- 未マージの work/ ブランチで `workers/ask/src/` を変えているもの: なし

### 2「内田みこ」の原因

- データ: 鳳凰戦 7行（D3〜、people あり）・女流桜花 7行・WRC リーグ 1行・最強戦 9行。WRC-R は無し。記録はある（止まる条件に当たらない）。質問の時点（2026-11-08 16:18 UTC）のデータにも4大会とも `players`・`people` にあった
- 質問の時点に本番で動いていたコードは fc2a6b735（2026-11-06。AIC-40 の作り直しより前）。この版の `crossAsk()` で、型 `all:houou:player`・答え「【鳳凰戦】答えられません。」になる道は、鳳凰戦の1回目が player で済み、**2回目（4大会をまとめた `writeMany()`）の欄が空**のときだけ（空の欄の大会は落とし、全部落ちると先頭の鳳凰戦を `answer || UNANSWERABLE` で出す）。1回目が失敗した大会は型が空になり、Claude の呼び出しの失敗は 503 になるので、この行にはならない。引数の記録（4大会とも name）も1回目が済んだことと合う
- 作り物の再現（`workers/ask/test/subject.test.mjs`「内田みこ: …」）: 1回目を4大会とも player（内田みこ）、2回目を4欄とも空にすると、今のコードでも作り直しが無ければ「【鳳凰戦】答えられません。」と型 `all:houou:player` になる（直す前の版で同じテストが落ちることを確かめた）
- 本番の今（2026-11-16 10:1x JST、1問）: 「内田みこ」は4大会の答えを返した（Claude の空の答えは毎回は起きない）。Workers Logs はセッションから見られないので、11/09 の2回目が空だった直接の証跡は無い（推定）
- 直し: 2回目の答えが空のときも、言語の混ざり・内部の言葉と同じく1回だけ作り直す（`redo()`。横断のまとめた2回目は空の欄の大会だけ、1大会の2回目〈`write()`〉も同じ）。呼び出しの数は今の作り直しと同じ枠（横断は `CROSS_RETRY_EXTRA` の1回、大会別は3回まで）

### 1 の直し方（選んだ形と理由）

- プログラムが付ける形を選んだ: `withSubject()` が player の答え（2回目に文にさせるもの）の頭に「<result.name>さんは」を付け、2回目の指示に `SUBJECT_LINE`（主語を書かずに述語から書く。「検索語」「検索された選手」と呼ばない）を player のときだけ足す。Claude が名前で書き始めたとき（指示に従わなかったとき）は重ねない
- 理由: Claude には名前を渡していないので「冒頭を確かめて付け直す」だけでは主語の置き換え（「検索語「hiro」の選手は」を外す）が文の解析になる。主語を書かせない形なら、プログラムは頭に付けるだけで済む。主語の残りの言い回しは内部の言葉の作り直しで拾う
- 範囲: player の答えのうち2回目に文にさせるもの（大会別の欄・context・横断の各大会）。プログラムが書く迎えた期の答え（`seatAnswer()`）・決勝だけの答え（元から「<名前>さんの」で始まる）・候補・案内は変えない
- 内部の言葉の一覧に「検索語」「検索された」「通算ではありません」を足した

### 3（大会別の要求を外す）は行わなかった

- 画面: origin/cloudflare の HTML・JS で `/api/ask` を送るのは `assets/top_ask.js`（204行目、`contest: root.dataset.contest`）だけで、その値は `assets/nav.js:263` の `'all'` だけ。大会別の要求を送るファイルは無い（前提の条件は満たす）
- ただし、`ask()` の大会別の分岐（`contest` 省略・大会の id）を外すと、Worker のテスト 215件のうち **79件** が落ちる（大会別の要求で1つの大会の流れ〈`settle()`・`write()`〉・指示・記録の型を確かめている。ask.test 24・cross 14・count 10・week3 7 ほか）。大会別の要求を「その大会を context にした横断」として扱う形でも 36件が落ちる（記録の型 `ouka:player` → `all:ouka:player`、ほかの大会に記録のある名前で答えが横断の形になる、テストの偽の Claude の呼び出しの並びが合わず 503 など）
- どちらも一時的に書き換えて測り、元に戻した。テストの書き換えはこの指示の直し（1・2）より大きく、答えの同等を確かめ直す作業になるので、この指示では行わず「判断が必要なこと」に回した（指示文の前提はテストへの影響を書いていない）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2cedda30）: https://github.com/retroeater/mj-logs/tree/main/guide/2cedda30

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2cedda30/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
