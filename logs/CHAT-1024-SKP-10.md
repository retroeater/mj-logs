# CHAT-1024-SKP-10

- 着手日時: 2026-10-24
- 対象issue: #626・#627
- ブランチ: work/1024-skp-ask
- 着手時HEAD: afc3145d4

## 指示

【Claude作成】Claude Code 向け指示：最強戦の質問欄の答えの誤り3点を直し、「予選出場」を「出場」に戻して、本番で5問を送り直してからマージする（#626・#627）
Chat-Ref: CHAT-1024-SKP-10
マージ: 承認済み（チャットで。条件は「止まる条件」）。告知の期間中の変更を承認（2026-10-24、平野さん）
貼る時機: いつでも（CHAT-1024-SKP-07・SKP-09 のセッションに続けて貼ってよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp-ask の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-skp-ask を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp-ask origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1024-SKP-07 は、本番の質問欄の5問のうち3問の答えに誤りがあり、動画を撮る前に止まった。その3点を直し、SKP-09 の調べを受けて平野さんが決めた部門の定義（「予選出場」→「出場」）を入れ、本番で5問を送り直して答えが合うことを確かめてからマージする。告知動画は次の指示（この指示の完了の後）で撮る。

### 決定（2026-10-24、平野さん）
- 部門「予選出場」は、ファイナルだけに出た年度（70年度）も数えたまま、名前を「出場」に戻す（CHAT-1024-SKP-06 の「出場年度 → 予選出場」の名前を置き換える。数え方・値は変えない）
- 「予選通過」は、予選の決勝の通過（予選優勝の164回）を含めたまま
- 「出場」と「連続出場」は両方残す（9部門・3×3 の並びはそのまま。「予選出場」の位置に「出場」）
- 質問欄の答えの誤り3点（SKP-07 の `## 報告`「判断が必要なこと」(1)〜(3)）を直す。直し方: 最強位の答えに通算の回数を渡す（年度で切った範囲の回数を通算として書かない）。同率の名前は全員を挙げ、多いときは「ほか N名」を付ける。上位の件数の外の人を「ほかに」と書かない
- 告知の期間中に Worker（mj-ask）へこの変更を入れてよい。直した後に本番で同じ5問を送り直し、全部合うことを条件にマージする

### 前提（チャット側。平野さんの決定ではない）
- 原因の見込みは SKP-07 の `## 報告`（`ANSWER_LINES.saikyo`・`TIE_NAMES = 3` など。ローカルの再現からの推定）
- 部門のスラッグ `appearances/` は名前が変わっても使えるので、変えなくてよい（要確認。変えるなら `_redirects`・テスト・文書をそろえる）
- 同率の名前の上限を変えるとき、ほかの大会（houou/・ouka/・jwrc/・wrcr/）の答えも変わりうる。変えるのは最強戦だけにする
- 本番で送る問いは IP ごとに1日10問まで。SKP-07 で5問使った（日が変われば戻る）。送り直しは5問

## 手順
1. CHAT-1024-SKP-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1024-SKP-10` を足す。上の「決定」を docs/decisions/saikyo.md に足す。SKP-07 の3点の原因を、コードを読んで確かめて `## 経過` に書く
2. 直す: 3点の答え（最強戦だけ）と、部門名「予選出場」→「出場」（ページ・部門別のタブ・質問欄の部門名と問いの言い換え〈「予選出場」「出場年度」→ 出場〉・文書）。テストを足し、`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`regenerate-page.yml` の check（saikyo_pages・ask_data_saikyo）を流す。ローカルの `runQuery` と答えの指示で、SKP-07 の5問が正しくなることを確かめる
3. origin/cloudflare を取り込み（要れば）、作業ブランチで生成し直し、差分を種類ごとに数えて書いたうえで `git push origin work/1024-skp-ask:cloudflare` でマージする（CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」）。Worker `mj-ask` のビルドが成功したら、本番に SKP-07 と同じ5問を送り、答えの全文・出典・選手のカードを `## 経過` に引用して、ページの値と突き合わせる。docs/notes/ask-worker.md「本番で送る固定の問い」の最強戦の行の見込みを、送り直した答えに合わせて直す。#626・#627 にマージしたことをコメントする。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う

## 止まる条件
- テスト・check が通らない、またはローカルで5問のどれかが正しくならない（マージせずに止まる）
- origin/cloudflare との差分に、この指示の直しで説明できないものがある。公開済みのページ（`saikyo/index.html`・年度・対局・卓・`saikyo/vs/`）と `sitemap-saikyo.xml` が、シートの変化以外の理由で変わっていたら止まる
- 直しで、ほかの大会（houou/・ouka/・jwrc/・wrcr/）の答え・選手のカードの出力が変わる（テストで確かめる）
- 未マージの work/ ブランチが `workers/ask/`・`scripts/generate_saikyo_pages.py`・`scripts/generate_ask_data_saikyo.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の本番の5問のどれかが、まだページの値と食い違う（どの問いの・どこがを書いて止まる。マージは戻さない）。1日の上限に当たったら、件数を書いて「未確認の項目」に回す
- マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順を進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SKP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SKP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1024-skp-ask
- ログ: docs/logs/CHAT-1024-SKP-10.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj afc3145d）: https://github.com/retroeater/mj-logs/tree/main/guide/afc3145d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/066dfde6.md
