# CHAT-1117-AIC-50

- 着手日時: 2026-11-17（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: e395b7d9c

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1116-AIC-49 の続き）2回目のレビューの後の直し 2e1b3cd8a の差分だけレビューを受け、cloudflare へマージして本番の5問を確かめる
Chat-Ref: CHAT-1117-AIC-50
マージ: 承認済み（チャットで。2026-11-16、平野さん）。次の3つを1つでも満たさなければマージしない。マージ後の本番の確かめで3つ目を満たさなければ、戻す指示を待たずにこの指示のマージを revert して cloudflare へ入れ、止まる
* 2e1b3cd8a の差分のレビューで直しが出ない（出たら直さず、判断待ちで止まる）
* テストが通る
* マージ後の本番の5問の答えがデータと一致し、指示の文の数・期と内部の言葉が出ない
貼る時機: 2026-11-17 以降（2026-11-16 は本番の確かめの10問を使い切っている）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1116-aic を続けて使う（CHAT-1116-AIC-49 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。2e1b3cd8a が origin/work/1116-aic の祖先であることを確かめる。CHAT-1116-AIC-49 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1117-AIC-50` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜49 で使用。AIC-17・24・31 は欠番）。

## 目的

AIC-49（player の答えの最初の文をプログラムが書き、データに無い期が出た Claude の文を落とす）は、2回目のレビューの後に直し 2e1b3cd8a（seasons が 0 の選手で出場の数を書かない、コメント・テストの直し）を入れたため、マージせず止まった。この差分のレビューを受けてマージし、本番で確かめる。

### 決定（2026-11-16、平野さん。CHAT-1116-AIC-49 の「判断が必要なこと」への回答）

* 2e1b3cd8a は、差分だけのレビューを1回受けてマージする（外さない）
* この指示は、上の「マージ:」の行の条件でマージしてよい。マージ後に本番で外れたら Code がその場で戻す

### 前提（チャット側。平野さんの決定ではない）

* レビューは docs/notes/self-review.md の手順で、2e1b3cd8a の差分（`git show 2e1b3cd8a`）だけを対象に1回。直しの提案が出たら直さずに止まる（収束の規則）
* 本番の確かめは1日10問まで（同じ接続元から）
* 答えが変わってよい問い: AIC-49 と同じ（名前で1人に決まった player の答え、全大会、横断と大会別の欄）

## 手順

1. レビュー: 2e1b3cd8a の差分のレビューを1回受ける。提案が無ければ次へ。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数を変えていないか確かめる
2. マージ: テストを通し、cloudflare へマージする
3. 本番の確かめ（Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、横断・context 無し、UA に `mj-internal`）: 「hiro」「中田」「内田みこ」「白鳥翔の第40期の成績は？」「藤崎」の5問。答えがデータと一致するか、フルネームで始まるか、データに無い期・内部の言葉が出ないか、Claude の文が落ちたか（Workers Logs の `playerTextDropped`）を書く。外れたら、この指示のマージを revert して cloudflare へ入れ、Workers Builds の成功を確かめてから止まる。結果を #654 にコメントする（判定だけ）

## 止まる条件

* レビューで直しの提案が出た（直さずに止まる）
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の確かめで外れた（revert して止まる。上の手順3）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、2026-11-23 の書き出し（確認は 6:00 JST）で player の答えがフルネームで始まるか、データに無い期が出ていないか、`playerTextDropped` の件数を見ることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-AIC-50.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-AIC-50 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c77a96f2）: https://github.com/retroeater/mj-logs/tree/main/guide/c77a96f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c77a96f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
