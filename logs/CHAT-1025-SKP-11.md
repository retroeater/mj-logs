# CHAT-1025-SKP-11

- 着手日時: 2026-10-25
- 対象issue: #626・#627
- ブランチ: work/1024-skp-ask
- 着手時HEAD: c30435985

## 指示

【Claude作成】Claude Code 向け指示：最強戦の質問欄の「選手の成績」の答えで、出場（年度の数）と対局数（卓の数）を分けて渡すように直し、本番で確かめてマージする（#626・#627）
Chat-Ref: CHAT-1025-SKP-11
マージ: 承認済み（チャットで。条件は「止まる条件」）。告知の期間中の変更を承認（2026-10-25、平野さん）
貼る時機: いつでも（CHAT-1024-SKP-10 のセッションに続けて貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp-ask の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-skp-ask を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp-ask origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1024-SKP-10 の本番の5問のうち、Q5「瀬戸熊直樹の最強戦の成績は？」だけが「出場は36回」と答えた（ページの「出場」は16回で、36は対局数）。最強戦の選手の成績の答えに、出場（年度の数）と対局数（卓の数）を分けて渡すように直し、本番で確かめてからマージを終える。

### 決定（2026-10-25、平野さん）
- Q5 の食い違いを直す。告知の期間中に Worker（mj-ask）へこの変更を入れてよい
- 直したら、本番で確かめてマージを終える

### 前提（チャット側。平野さんの決定ではない）
- 原因と直し方の案は SKP-10 の `## 報告`「判断が必要なこと」: `yearlyPlayer()` の count が卓の行の数で、答えの指示が count を回数として使わせる。最強戦の player の結果に出場（年度の数）・対局数（卓の数）を別に渡し、2回目の指示に「count は卓の数（対局数）で、出場の回数ではない」と書く
- 直すのは最強戦（年度で数える大会）だけ。ほかの大会の答え・選手のカードの出力は変えない
- 本番の問いの残りは IP ごとに1日10問から数える（SKP-10 の時点で残り7。日が変わっていれば10）

## 手順
1. CHAT-1024-SKP-10 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1025-SKP-11` を足す。上の「決定」を docs/decisions/saikyo.md に足す
2. 直す。テストを足し、`node --test 'workers/ask/test/*.test.mjs'`・`python3 -m unittest discover -s scripts/tests` を流す。ローカルの `runQuery` と答えの指示で、Q5 と「佐々木寿人の最強戦の成績は？」が、個人成績のページの値（出場・対局数・最強位・ファイナル）と合うことを確かめる
3. `git push origin work/1024-skp-ask:cloudflare` でマージする（CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」）。Worker `mj-ask` のビルドが成功したら、本番に Q5 と「佐々木寿人の最強戦の成績は？」を1回ずつ送り、答えの全文・選手のカードを `## 経過` に引用してページの値と突き合わせる。合えば、docs/notes/ask-worker.md「本番で送る固定の問い」に最強戦の5問（SKP-07 の5問）と見込みの答えを足す。#626・#627 にマージしたことをコメントする。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う

## 止まる条件
- テストが通らない、またはローカルで2問のどちらかがページの値と合わない（マージせずに止まる）
- 直しで、ほかの大会（houou/・ouka/・jwrc/・wrcr/）の答え・選手のカードの出力が変わる（テストで確かめる）
- origin/cloudflare との差分に、この直しで説明できないものがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の本番の2問のどちらかが、まだページの値と食い違う（どこがを書いて止まる。マージは戻さない）。1日の上限に当たったら、件数を書いて「未確認の項目」に回す
- マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順を進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1025-SKP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1025-SKP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1024-skp-ask
- ログ: docs/logs/CHAT-1025-SKP-11.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c3043598）: https://github.com/retroeater/mj-logs/tree/main/guide/c3043598

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3043598/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c3043598.md
