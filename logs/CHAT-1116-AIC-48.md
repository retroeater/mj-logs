# CHAT-1116-AIC-48

- 着手日時: 2026-11-16（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: f533d3bfd

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1116-AIC-47 の続き）本番の「中田」の答えが指示の例文の数を写した件で、AIC-47 の player の主語の変更を戻す。cloudflare へマージする Chat-Ref: CHAT-1116-AIC-48 マージ: 承認済み（チャットで。2026-11-16、平野さん）。次の2つを1つでも満たさなければマージしない（2つ目はマージ後の確かめで、満たさなければ止まる）

* テストが通る
* 戻した後の本番で「中田」の答えがデータと一致し、`SUBJECT_LINE` の例の数・期（「第18期から第42期まで28期」など）が出ない 貼る時機: いつでも（CHAT-1116-AIC-47 のマージ〈203e4538b〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。203e4538b が origin/cloudflare の祖先であることを確かめる。CHAT-1116-AIC-47 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-AIC-48` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜47 で使用。AIC-17・24・31 は欠番）。

目的
AIC-47 のマージ後、本番の「中田」の答えがデータと合わなくなった（鳳凰戦「中田花奈さんは第18期から第43期まで…」、データは第38期から。女流桜花・最強戦「中田花奈さんはx」）。2回目の指示の一文 `SUBJECT_LINE` の例の数を写したと見られる（AIC-47 の報告）。誤った答えを本番に置かないよう、主語の変更をまず戻す。
決定（2026-11-16、平野さん。CHAT-1116-AIC-47 の「判断が必要なこと」への回答）

* 「中田」の誤り: まず戻す（AIC-47 の案 (2)）。player の主語をプログラムが書く形は、例文を使わない設計で別の指示で作り直す（AIC-47 の決定 1 は取り下げず、作り直しを待つ）
* 大会別の要求を外す件: Worker のテストを横断＋context に書き直して外す（別の指示。この指示ではしない）
* 2回目の自己レビューの提案2件（#654 に移したもの）: 主語の作り直しの指示で扱う（この指示ではしない）
* この指示は、上の「マージ:」の行の条件でマージしてよい

前提（チャット側。平野さんの決定ではない）

* 戻す範囲: 203e4538b の `workers/ask/src/ask.mjs` の変更のうち、player の主語に関わる部分（`SUBJECT_LINE`・`withSubject()` とその呼び出し、主語のための作り直し。関数名は AIC-47 の報告から。行番号は（要確認））。「内田みこ」の直し（2回目が空のときの作り直し）と内部の言葉の一覧への語の追加は、主語の部分と切り分けて残せ、テストが通るなら残す。切り分けられなければ ask.mjs の変更をまとめて戻し、何を戻したかをログに書く。テストも戻した変更に合わせる
* docs/notes/ask-worker.md の主語の記述と「答えの事実を誰が書くか」の表、docs/decisions/houou.md（上の「決定」）を直す
* 答えが変わってよい問い: player の答え（名前で1人に決まったもの）の冒頭の主語。戻した後は AIC-47 より前の答え方になる
* 本番の確かめは1日10問まで（同じ接続元から）。届かなければ、送れなかった問いを「未確認の項目」に書く

手順

1. 実物で確かめる: 203e4538b の差分（`git show 203e4538b -- workers/ask/`）を読み、主語の部分とほかの部分を分ける。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数を変えていないか確かめる
2. 戻す: 主語の部分を戻し（上の前提）、テストを通す。文書を直す。テストが通れば cloudflare へマージする
3. 本番の確かめ（マージ後、Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、横断・context 無し、UA に `mj-internal`）: 「中田」「hiro」「内田みこ」の3問。答えがデータと一致するか、例の数・期が出ないか、内部の言葉が出ないかを書く。結果を #654 にコメントする（件数と判定だけ）

止まる条件

* 203e4538b の差分が AIC-47 の報告と食い違う
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の「中田」の答えがデータと合わない（戻した後もずれる。止まって、答えと期待をログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-AIC-48.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-AIC-48 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8b20a20f）: https://github.com/retroeater/mj-logs/tree/main/guide/8b20a20f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b20a20f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
