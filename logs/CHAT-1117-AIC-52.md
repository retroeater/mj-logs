# CHAT-1117-AIC-52

- 着手日時: 2026-11-17（JST）
- 対象issue: #654
- ブランチ: work/1117-aic
- 着手時HEAD: aeaf0fb52

## 指示

【Claude作成】Claude Code 向け指示：（質問欄の整理）Worker のテストのうち大会別の要求を使うものを横断＋context に書き直し、大会別の要求を Worker から外す。AIC-50 で見送ったテストの提案2件も直す。cloudflare へマージする
Chat-Ref: CHAT-1117-AIC-52
マージ: 承認済み（チャットで。2026-11-17、平野さん）。次の3つを1つでも満たさなければマージしない。マージ後の本番の確かめで3つ目を満たさなければ、戻す指示を待たずにこの指示のマージを revert して cloudflare へ入れ、止まる
* テストが通る
* 外す前のテストが確かめていた中身（流れ・指示・記録の型・答えの文）を、書き直した後のテストも確かめている（対応表をログに書く）
* マージ後の本番の問いの答えがデータと一致する
貼る時機: いつでも（CHAT-1117-AIC-51 のマージ〈261e00317〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1117-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1117-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。261e00317 が origin/cloudflare の祖先であることを確かめる。識別子 `AIC` はこのチャットの系列（AIC-01〜51 で使用。AIC-17・24・31 は欠番）。

## 目的

質問欄は横断（`contest: "all"`）にまとめた。大会別の要求は古い JS のために Worker に残していたが、週次の記録で `all:` で始まらない型の行は 2026-11-02〜08 と 11-09〜15 の2週続けて0行。画面は `assets/nav.js` の `'all'` だけを送っている（AIC-47 の報告）。AIC-47 は、Worker のテスト79件（ask 24・cross 14・count 10・week3 7 ほか）が大会別の要求で1つの大会の流れ・指示・記録の型を確かめているため、外すのを見送った。

### 決定（2026-11-16〜17、平野さん）

* Worker のテストを横断＋context に書き直して、大会別の要求を外す（2026-11-16、AIC-47 の「判断が必要なこと」への回答）
* AIC-50 で見送ったテストの書き方の提案2件（`player.test.mjs` のコメントの括弧書きの食い違い、seasons 0 の検査を全員に広げる。#654 に移したもの）は、この指示で直す（2026-11-17）
* この指示は、上の「マージ:」の行の条件でマージしてよい。マージ後に本番で外れたら Code がその場で戻す

### 前提（チャット側。平野さんの決定ではない）

* 外した後に `contest` が `all` でない要求が届いたときの扱いの案: その大会を context とした横断として答える（古い JS が残っていても答えは返る）。400 で断る形にするなら理由をログに書く。どちらにしても、記録の型は `all:` で始まるようにする
* 外す範囲: 大会別の要求だけが使う入口・分岐・指示・関数。横断の中で各大会の答えを作るのに使っている関数は残す（どれが大会別だけかは（要確認）。開いた関数名と行番号をログに書く）
* テストは件数を合わせるのではなく、確かめていた中身（各大会の流れ・指示・記録の型・答えの文）を横断＋context で確かめる形に書き直す。大会別の要求でしか確かめられない中身があれば、書き直せない理由と一緒に止まる
* 画面のファイル（assets/、TOP 系列の受け持ち）は変えない
* docs/notes/ask-worker.md「横断」（「大会別の要求は古い JS のため残す」の行）と、記録の型の説明を直す。docs/decisions/houou.md に上の「決定」を足す
* 答えが変わってよい問い: `contest` が `all` でない要求（上の扱いの案のとおりに変わる）。横断の要求の答えは変わらない
* 本番の確かめは1日10問まで（同じ接続元から）

## 手順

1. 実物で確かめる: 大会別の要求の入口・分岐と、それを使うテスト（ファイルごとの件数）を開いて確かめ、上の前提と食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/` の同じ関数・同じテストを変えていないか確かめる
2. 書き直す: テストを横断＋context に書き直す（外す前と後の対応表をログに書く）。提案2件を直す。大会別の要求を外す。テストが通り、対応表で漏れが無ければ cloudflare へマージする
3. 本番の確かめ（Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、UA に `mj-internal`）: 横断・context 無しで「hiro」「白鳥翔の第40期の成績は？」、横断・context 鳳凰戦で「今期の首位は？」、`contest: "houou"`（大会別）で「藤崎」の4問。答えがデータと一致するか、大会別の要求が上の扱いのとおりになるかを書く。外れたら、この指示のマージを revert して cloudflare へ入れ、Workers Builds の成功を確かめてから止まる。結果を #654 にコメントする（判定だけ）

## 止まる条件

* 前提（入口・分岐・テストの件数）が実物と食い違う
* 大会別の要求でしか確かめられない中身があり、書き直せない
* 未マージの work/ ブランチが `workers/ask/` の同じ関数・同じテストを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 画面のファイル（assets/）を変えないと外せない
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の確かめで外れた（revert して止まる。上の手順3）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、2026-11-23 の書き出し（確認は 6:00 JST）で `all:` で始まらない型の行が0であることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-AIC-52.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-AIC-52 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 261e0031）: https://github.com/retroeater/mj-logs/tree/main/guide/261e0031

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/261e0031/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
