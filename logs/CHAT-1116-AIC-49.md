# CHAT-1116-AIC-49

- 着手日時: 2026-11-16（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: 1d3b51f17

## 指示

【Claude作成】Claude Code 向け指示：（player の答えの作り直し）名前で1人に決まった player の答えの最初の文（フルネーム・出場の期数・直近の期）をプログラムが書き、Claude の文に出る期がデータに無ければ Claude の文を落とす。例文は使わない。cloudflare へマージする Chat-Ref: CHAT-1116-AIC-49 マージ: 承認済み（チャットで。2026-11-16、平野さん）。次の3つを1つでも満たさなければマージしない。マージ後の本番の確かめで2つ目・3つ目を満たさなければ、戻す指示を待たずにこの指示のマージを revert して cloudflare へ入れ、止まる

* テストが通る
* 本番の問いの答えがデータと一致する（指示の文の数・期が答えに出ない）
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1116-AIC-48 のマージ〈2c3d81144〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。2c3d81144 が origin/cloudflare の祖先であることを確かめる。識別子 `AIC` はこのチャットの系列（AIC-01〜48 で使用。AIC-17・24・31 は欠番）。CHAT-1109-AIC-44 のログには手を入れない。

目的
player の答えで、名前・期の数・どの期を挙げるかが揺れ続けている。プログラムが最初の文を書き、Claude の文はデータで確かめられる範囲に限る。

* 2026-11-10「hiro」: 「検索語「hiro」の選手は…」（名前が無い。AIC-47 の目的）
* 2026-11-12「中田」: 「「中田」で検索された選手は1人で…」（同）
* AIC-47 で主語を Claude に書かせる一文（`SUBJECT_LINE`）に例の数を入れたところ、本番の「中田」が例の「第18期から」を写した（AIC-47 の報告。AIC-48 で戻した）
* AIC-48 の後の本番「hiro」: 「hiroさんは…最新の第42期前期は1位で決定戦に進み、第42期は2位で決定戦に進んだ…」（データは第42期 A1 1位・決定戦〈前期・後期の区別なし〉、2位で決定戦は第39期。AIC-48 のログの本番の確かめ）

決定（2026-11-16、平野さん）

* player の主語（フルネーム）と、どの期を挙げるかを、例文を使わずプログラムの文にする（AIC-47 の決定 1 の作り直し、AIC-48 の決定）
* 残りの2本のうち、この指示を先にする。大会別の要求を外すためのテストの書き直しはこの後の指示
* この指示は、上の「マージ:」の行の条件でマージしてよい。マージ後に本番で外れたら Code がその場で戻す

前提（チャット側。平野さんの決定ではない）

* 形の案（実物に合わせて変えてよい。理由をログに書く）:
   1. 最初の文はプログラムが書く。「<フルネーム>さんは<大会名>に<出場の期数>期出場しています。直近の第<N>期は<リーグ>で<順位>位、<結果>です。」のように、名前・期数（鳳凰戦・女流桜花は seasons）・直近の期の段・順位・結果だけを、`answerInput()` に渡しているのと同じデータから作る。前期・後期のある期は「第43期前期」、無い期は「第42期」のように、データの形のまま書く。WRC・WRC-R・最強戦も同じ考えで、各大会のデータで書ける範囲にする
   2. Claude には「最初の文は書かれている。その後に、データにある期だけを使って1〜2文を足す（無くてよい）。主語・名前・出場の期数・直近の期は書かない」と伝える。指示に数・期・名前の例は入れない
   3. Claude の文の中の「第N期」（前期・後期を含む）を取り出し、どれかがデータの items に無ければ Claude の文を落とし、プログラムの文だけを返す（Workers Logs に記録）。内部の言葉（`internalWords()`）が出たときも同じ
   4. タイトルの期の文（`withTitle()`）、迎えた期（`seatAnswer()`）、決勝だけの人（`finalsOnlyAnswer()`）など、今プログラムが書いている文は変えない
* 関数名・行番号（`answerSystem()`・`ANSWER_LINES`・`answerInput()`・`writeMany()`・`write()`・`nameLead()`・`withTitle()`・`internalWords()`）は docs/notes/ask-worker.md と AIC-47・48 のログから。（要確認）。開いた関数名と行番号をログに書く
* AIC-47 の2回目の自己レビューの提案2件（#654 に移したもの。`withSubject()` の書かれた名前の比較の正規化、`/得点[^。]*通算ではありません/` の当たり方）は、今の形で要るものだけ扱う（要らなければ理由を書いて閉じる）
* 答えが変わってよい問い: 名前で1人に決まった player の答え（全大会、横断と大会別の欄）
* 本番の確かめは1日10問まで（同じ接続元から）。2026-11-16 は AIC-47・48 で使っているので、届かなければ翌日に回さず、送れなかった問いを「未確認の項目」に書く

手順

1. 実物で確かめる: 上の関数と、player の結果の形（items・seasons・count・直近の期の取り方）を開いて確かめ、食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数を変えていないか確かめる
2. 作る: 上の形。テストを足す: 名字・略称（「hiro」「中田」の形）でフルネームで始まる、前期・後期の有る期と無い期で直近の期の書き方がデータの形のまま、Claude の文にデータに無い期があれば落ちる、内部の言葉が出たら落ちる、指示の文に数・期・名前の例が無い（指示の文を検査する）。docs/notes/ask-worker.md の表（5大会の表、「答えの事実を誰が書くか」の player の行を「プログラムが文を作っている箇所」へ移す）と docs/decisions/houou.md（上の「決定」）を直す。テストが通れば cloudflare へマージする
3. 本番の確かめ（マージ後、Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、横断・context 無し、UA に `mj-internal`）: 「hiro」「中田」「内田みこ」「白鳥翔の第40期の成績は？」「藤崎」の5問。答えがデータと一致するか、フルネームで始まるか、データに無い期・内部の言葉が出ないか、Claude の文が落ちたかを書く。外れたら、この指示のマージを revert して cloudflare へ入れ、Workers Builds の成功を確かめてから止まる。結果を #654 にコメントする（判定だけ）

止まる条件

* 前提（関数・結果の形）が実物と食い違う
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の確かめで外れた（revert して止まる。上の手順3）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、2026-11-23 の書き出し（確認は 6:00 JST）で player の答えがフルネームで始まるか、データに無い期が出ていないか、Claude の文が落ちた件数を見ることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-AIC-49.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-AIC-49 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 67eac94e）: https://github.com/retroeater/mj-logs/tree/main/guide/67eac94e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
