# CHAT-1019-WRC-17

- 着手日時: 2026-10-19
- 対象issue: なし（関係: #586）
- ブランチ: work/1019-wrc17 → cloudflare へマージ
- 着手時HEAD: 58fa24fff

## 指示

【Claude作成】Claude Code 向け指示：質問欄の答えの読み方（`ANSWER_LINES`）を鳳凰戦・女流桜花にも足し、3大会の仕様をそろえてマージする Chat-Ref: CHAT-1019-WRC-17 マージ: 下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんが依頼） 貼る時機: いつでも（CHAT-1019-WRC-16〈告知動画、work/1019-wrc14〉とは別のセッションで。Worker だけを変えるので WRC-16 とは重ならない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrc17 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-wrc17 を origin/cloudflare から作る。マージの前に `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git log origin/cloudflare --grep="CHAT-1019-WRC-15" --oneline` に 5c2333f99（`ANSWER_LINES` の追加）が含まれることを確かめる。無ければ止まる。 着手後は質問せず、迷う点は仮置きで進めて報告の「判断が必要なこと」に書く。

目的
CHAT-1019-WRC-15 で、jwrc/ の質問欄が「現タイトルホルダーは？」に「通算19期の獲得」と答えた誤り（優勝した期を回数と読んだ）を直すため、Worker の2回目の指示（答えを書く側）に WRC リーグだけの読み方の一文（`ANSWER_LINES`）を足した。同じ読み方を鳳凰戦・女流桜花にも足し、3大会の答え方の仕様をそろえる。
決定（2026-10-19、平野さん）

* `ANSWER_LINES` に当たる答えの読み方を、鳳凰戦（houou/）・女流桜花（ouka/）にも足す
* 理由: 質問欄（データ・コンシェルジュ）は将来、サイト全体で1つに統合する。今のうちから大会ごとの仕様をそろえておく

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 足す文は WRC リーグの一文（「champions の terms は優勝した期(第N期)。優勝の回数は terms の個数。entry は入会期(N期生)で、優勝の期ではない」）と同じ趣旨にし、大会ごとのデータの形に合わせて言い換える（鳳凰戦は期に前期・後期がある、女流桜花は期の数え方、など。データの実物で確かめ、記憶で決め打ちしない）
* 3大会の文が同じ型（同じ仕組み・同じ場所）になるようにする。大会ごとに違う所はログに表で書く
* 未マージの work/WRR（WRR-03、判断待ち）も `workers/ask/` を変えている。同じ関数・同じ行を変えていれば、その箇所をログに書く（止まらない。取り込みは WRR 側で行う）
* docs/notes/ask-worker.md の `ANSWER_LINES` の説明を3大会の形に直す

手順

1. 直し: 鳳凰戦・女流桜花の読み方の文を足す。Worker のテストで、3大会の2回目の指示の最後にそれぞれの文が入ることを確かめる（WRC-15 で足した「鳳凰戦・女流桜花の2回目の指示は変えない」のテストは、今回の決定に合わせて直す）。`node --test 'workers/ask/test/*.test.mjs'` と `python3 -m unittest discover -s scripts/tests` を回す
2. マージ: CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージし、Workers Builds（mj-ask）の成否を書く
3. 本番の確かめ: houou/ で「現鳳凰位は？」、ouka/ で「現女流桜花は？」を1回ずつ聞き、答えの全文をログに書く（優勝の期・回数・入会期を取り違えていないか）。jwrc/ の「現タイトルホルダーは？」の答えが WRC-15 のときと変わらないことも1回確かめる。上の「決定」を docs/decisions（データ・コンシェルジュの決定の置き場所）に足す

止まる条件

* 0章の確かめに当たる
* テストが落ちて直せない
* 本番の答えが期・回数・入会期を取り違えている（答えを書いて止まる。戻すかは平野さんの判断）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRC-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRC-17 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 58fa24ff）: https://github.com/retroeater/mj-logs/tree/main/guide/58fa24ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
