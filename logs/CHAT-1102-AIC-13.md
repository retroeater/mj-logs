# CHAT-1102-AIC-13

- 着手日時: 2026-11-02（JST）
- 対象issue: #586
- ブランチ: work/1102-aic
- 着手時HEAD: 9789a6fb

## 指示

【Claude作成】Claude Code 向け指示：（AIC-12 の続き）質問欄の5大会の違いのうち2つをそろえる（入会期の一覧の型 `entry` を4大会に広げる、同率の名前を渡す人数を10人にそろえる）。cloudflare へマージする
Chat-Ref: CHAT-1102-AIC-13
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1102-AIC-12 のマージ〈26012fe3b〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。26012fe3b が origin/cloudflare の祖先であることを確かめる。AIC-12 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1102-AIC-13` を足し、その「判断が必要なこと」（5大会の違い）への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜12 で使用）。

## 目的

AIC-12 の照合で残した5大会の応答ルールの違いのうち、平野さんがそろえると決めた2つを直す。

### 決定（2026-11-02、平野さん）

AIC-12 の「違いの一覧」（docs/notes/ask-worker.md「5大会の応答ルール」）への回答。

| 違い | 決定 |
|---|---|
| 1. 「現在」「今」の期のリーグ・順位の一文（鳳凰戦・最強戦だけ） | 残す |
| 2. 型 `entry`（入会期の在籍選手の一覧、鳳凰戦だけ） | **そろえる**。女流桜花・WRC リーグ・WRC-R・最強戦にも広げる |
| 3. 期で絞っても通算の回数（total）を渡す処理（最強戦だけ） | 残す（統合のときに決め直す） |
| 4. 同率の名前を渡す人数（最強戦は10人と others、ほかは3人） | **そろえる**。5大会とも10人と others にする |
| 5. 最強戦の年度と期・型 | 残す |

### 前提（チャット側。平野さんの決定ではない）

- 2（`entry`）:
  - AIC-12 が残した理由: 大会のデータの在籍者は出場者だけで、その期生の全員にならない
  - 広げるときは、答えに「この大会に出場した選手だけです」などの注記を付ける。鳳凰戦の今の注記「入会期が分かるのは在籍中の選手だけです」と同じ仕組みで、大会ごとの文にする
  - 鳳凰戦の `entry` の並び（最後に出た期のリーグの上から）を、ほかの大会では何で並べるかは任せる（例: 最後に出た期の順位、通算スコア）。決めた形を表に書く
  - 入会期が分かるのは、`people` に入会期がある選手だけ（要確認）。退会者などで入会期が無い人は数に入らないことを注記に含めるかは任せる
  - 「N期後期生」の冒頭の文（AIC-08 の `entryHalfLead()`）と、1回目の指示の entry の読み方の一文も、4大会で同じ形にする
  - 鳳凰戦の `entry` の答えは変えない（テストで確かめる）
- 4（同率の名前）:
  - 最強戦の 10人と others は 2026-10-24 の決定（SKP-10）。その形に、鳳凰戦・女流桜花・WRC リーグ・WRC-R をそろえる
  - AIC-08 の `byTimes`（回数の段ごとに全員）とは別の処理。同率の順位の名前の渡し方を指す（要確認）
  - 鳳凰戦などで今3人と「ほか N人」と答えている問いは、答えが長くなる。答えの文の長さが極端にならないか、本番の問いで見る
- docs/notes/ask-worker.md「5大会の応答ルール」の表の 2・4 の行を「そろえた」に直し、1・3・5 の行に「残す（2026-11-02、平野さん）」を書く。docs/decisions/houou.md に上の「決定」を足す
- 本番の確かめは1日10問まで（同じ接続元から）。2026-11-02 は AIC-11・12 で問いを使っている。上限に当たったら送るのをやめ、送れなかった問いを「未確認の項目」に書く

## 手順

1. 直す:
   - 2 と 4 を、今の `workers/ask/` と data の JSON で確かめてから直す。大会ごとに違う所はログに表で書く
2. テスト:
   - 次をテストの例にする
     - 4大会の「N期生」（各大会に入会期のある選手がいる期を data から選ぶ）
     - 5大会の同率の問い（同率が4人以上になる問いを data から選ぶ）
   - 直す前のコードで新しいテストが落ちること、鳳凰戦の `entry` と最強戦の同率の答えが変わらないことを確かめる
   - `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
3. issue・マージ・本番:
   - #586 に、5大会の違いへの決定と直した結果をコメントする
   - cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   - 本番に送る問いは次の順（上限に当たったらそこでやめる）
     1. 4大会の「N期生」を各1問
     2. 同率の問いを2〜3問
     3. ask-worker.md「本番で送る固定の問い」から数問

## 止まる条件

- #586 に他セッションの着手中コメントがある
- 未マージの work/ ブランチが `workers/ask/` の同じ関数・同じ行を変えている、または取り込みで衝突する。同じファイルの別の箇所の変更では止まらず、ログに書く。大会ごとの直しが重なるときは、重なる大会だけ外して残りを進めてよい（外したものと理由を報告に書く）
- 2 で、ある大会のデータに入会期が無く、`entry` が成り立たない。その大会だけ外して進め、理由を報告に書く。全大会で成り立たないときは止まる
- 直しで、鳳凰戦の `entry` の答え、最強戦の同率の答え、ほかの型の答えが変わる（テストで確かめる）
- テストが通らない（マージせずに止まる）
- 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-12 の「未確認の項目」2つは、この指示の「未確認の項目」にも写す
  - 2026-11-09（月）03:00 JST の書き出しと、「現女流桜花」の引数
  - 地域で断られた問いの記録
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-AIC-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-AIC-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 368cfd61）: https://github.com/retroeater/mj-logs/tree/main/guide/368cfd61

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/368cfd61/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c3c97901.md
