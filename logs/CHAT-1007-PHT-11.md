# CHAT-1007-PHT-11

- 着手日時: 2026-10-07
- 対象issue: #513（通知先 #357）
- ブランチ: work/1007-pht-clean
- 着手時HEAD: 4e2413f2

## 指示

【Claude作成】Claude Code 向け指示：作業ログの自動削除の判定を直す（続き先が完了・取り下げなら削除、状態「取り下げ」、種類別の通知）と、#357 の本文の書き換え（#513 の実装①） Chat-Ref: CHAT-1007-PHT-11 マージ: 承認済み（チャットで、2026-10-07）。テストと、作業ブランチでの cleanup-logs.yml の手動実行（dry-run）が通ればマージしてよい。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1007-PHT-09・CHAT-1007-PHT-12 とは別のセッションに貼る。どちらの完了も待たない） 作業ブランチ: クラウドセッションで実行する。work/1007-pht-clean を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-pht-clean origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-pht-clean の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06・CHAT-1007-PHT-07〜10 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1007-PHT-08 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、scripts/cleanup_logs.py・.github/workflows/cleanup-logs.yml に触れているものがあれば止まる。

目的
#513（作業ログの自動削除の見直し）の実装の1本目。判定のコードを新旧両対応で直し、#357 の通知を種類別にする。規則の文書（CLAUDE.md・docs/notes/ ほか）は、この指示では変えない（次の指示②で行う。文書を先に入れると、新しい状態を判定が読めないため、この指示が先）。
決定（2026-10-07、平野さん）

* 判定と通知は、docs/decisions/operations.md「2026-10-07（CHAT-1007-PHT-08）」のとおりに作る（grill の決定。#513 のコメントにも同じ内容がある）。この指示に関係するのは次のもの
   * 今の条件（状態が完了で、3項目が「なし」。`なし（#NNN に移した）` のように先頭が「なし」なら「なし」。先頭が「なし」でも子の行が続けば「なし」でない）は変えない（grill Q2・Q4）
   * 続きは、状態の末尾の `/ 続き: CHAT-…` の形で読む（grill Q5）
   * 続き先が「完了」または「取り下げ」なら、続きを書いた側のログを自動で削除の対象にする。続き先が判断待ち・中断・読めない・存在しないなら削除しない。続き先が削除済みなら完了とみなす。何段も続くときは、続き先が完了または削除済みなら削除する（grill Q6）
   * 状態「取り下げ」のログは自動削除の対象にする。判断待ち・中断が自動で期限切れになることはしない（grill Q7）
   * 削除までの日数は、最終コミットから7日のまま（grill Q8）
   * #357 の本文を新しい条件に書き換える。通知は種類を分け、各表の前に件数を1行書く: ① 判断待ち・中断（続きなし）、② 完了なのに3項目が「なし」でない（書き方の違反）、③ 状態が読めない（旧形式・続き先が読めない）。②は削除しない（grill Q11）
   * 新旧両対応にし、ワークフローの変更なので作業ブランチで dry-run を手動実行してからマージする（grill Q12）
* テストと作業ブランチでの dry-run が通ればマージしてよい（2026-10-07、チャットで）

前提（チャット側。平野さんの決定ではない）

* CHAT-1007-PHT-08 のログ（mj-logs）で読んだこと: 今の判定は `scripts/cleanup_logs.py` の `judge`。作業の issue は #513。cleanup-logs.yml の手動実行は dry-run が既定で、週次実行は `SCHEDULE_ENABLED` が `'true'`（docs/notes/static-generation.md「ワークフローを手動実行するとき」）。実物で確かめる
* チャット側の読み（決定の文面から。違えば #513 のコメントの文面を正とし、食い違いを報告に書く）:
   * 「取り下げ」のログは、3項目の中身に関わらず、7日たてば削除の対象
   * 続き先が完了・取り下げで削除の対象になるログも、3項目の中身に関わらず、そのログ自身の最終コミットから7日たてば対象
   * 「存在しない」（一度も無い）と「削除済み」（履歴にあって今は無い）の区別には git の履歴が要る。cleanup-logs.yml の checkout の深さで履歴が読めるかを確かめ、読めなければ読める形にする
* 決定に無い点（実装で要るはずのもの。チャット側の案）: 規則の文書が入る日（D）より前に書かれたログは、新しい書き方を守っていない。通知②（書き方の違反）と効果の測定（grill Q12 の (1)(2)）は、D より後に書かれたログを区別できる必要がある。案: D を1か所（定数またはワークフローの変数）に持たせ、未設定のあいだは②を「旧い規則のログ」として件数だけ出す。D の値は指示②で入れる
* ほかにも決定に無い点が出たら: 消える件数に影響しないものは最小の形で実装し、選んだ形と理由を「判断が必要なこと」に書く。消える件数が変わるものは、実装せずに止まる
* 見込み: CHAT-1006-PHT-05 の調査では、続き先を読めるログのうち続き先が完了のものは27件（2026-10-06 の350件のとき）。その後の仕分け（CHAT-1006-PHT-03・04、CHAT-1007-PHT-07・10）で多くを削除したので、今の数は実物で測る
* docs/（docs/logs/・docs/decisions/ を除く）と CLAUDE.md は、この指示では変えない。docs/notes/branch-operations.md「作業ログの寿命」とコードの食い違いは、指示②までの間だけ残る
* マージの後に動く見込み: docs/ の外を含む push なので assets-check.yml・sync-logs.yml・Workers Builds が1回ずつ（表示は変わらない）。regenerate-page.yml は起動しない見込み（`scripts/lib/**` を変えないなら。要確認）。次の週次の cleanup-logs.yml（2026-10-12 06:23 JST）は、新しい判定と通知で動く
* 使う skill は無い

手順

1. 確かめる。#513 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。docs/decisions/operations.md の該当の節、#513 のコメント、scripts/cleanup_logs.py、そのテスト、.github/workflows/cleanup-logs.yml、`git log -- .github/workflows/cleanup-logs.yml`、docs/notes/branch-operations.md「ワークフローを変更したとき」「作業ログの寿命」を読む。直す前の `python3 scripts/cleanup_logs.py --dry-run` の結果（削除・通知・対象外の件数）をログに書く
2. 実装する。判定（続き・取り下げ・削除済みの続き先）と、種類別の通知（各表の前に件数）を入れ、unittest を足す（続き先が 完了／取り下げ／判断待ち／中断／読めない／存在しない／削除済み のそれぞれ、何段も続く場合、続きが自分自身や輪になっている場合、状態「取り下げ」、今の条件で消えるものが変わらず消えること、通知の3種類への振り分け）。直す前のコードでは新しいテストが通らないことも確かめる
3. 確かめて、マージする
   * 現物のログで `--dry-run` を実行し、直す前と比べる: 新しく削除の対象になるログの一覧（理由つき）、通知の種類ごとの件数。今の条件で消えるログが消えなくなっていないこと
   * 作業ブランチで cleanup-logs.yml を手動実行する（dry-run）。ジョブの結果と、#357 にコメントが付かないことを確かめる。セッションから作業ブランチで起動できないときは、起動できなかった事実を書き、平野さんが GitHub の画面で実行するための手順（ワークフロー名・ブランチ名・入力）を最終報告に書いて、マージせずに止まる
   * 止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージし、push で動いたワークフローと check-run の結果を書く
   * #357 の本文を新しい条件に書き換える（書き換える前の本文をログに控える。規則の文書は指示②で入ることを1行添える）。#513 に、実装の内容・dry-run の結果・決定に無かった点の扱いをコメントする

待ち方

* ワークフロー・check-run の完了を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書く（マージの前の手動実行が確かめられなかったら、マージしない）

止まる条件

* CHAT-1007-PHT-08 の状態が「完了」でない。未マージのブランチが同じ2つのファイルに触れている。#513 に他セッションの着手中コメントがある
* unittest（`python3 -m unittest discover -s scripts/tests`）が1件でも失敗する
* 現物の `--dry-run` で、今の条件で消えるログが新しいコードで消えなくなる（1件でも）。または、新しく削除の対象になるログに、続き先が完了・取り下げ・削除済みのもの、状態が「取り下げ」のもの、のどちらでもないログがある
* 作業ブランチでの手動実行が failure で終わった、または dry-run なのにログの削除・#357 へのコメントが起きた
* cloudflare に入る変更が、次で説明できる差分だけでない: scripts/cleanup_logs.py・そのテスト・.github/workflows/cleanup-logs.yml・docs/logs/・docs/decisions/
* 決定に無い点で、消える件数が変わるものが出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 直す前後の dry-run の比較、テストの結果（直す前に通らないことを含む）、作業ブランチでの手動実行の結果、マージのコミット、ワークフローと check-run の結果、#357 の本文の前後、#513 へのコメント、決定に無かった点の扱いがログにある
* 指示②（規則の文書）で文書に書くべき判定の事実（状態の読み方、続きの形、D の持たせ方）を、`## 経過` に箇条書きでまとめる
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-PHT-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-PHT-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep="CHAT-1007-PHT-11"` は0件。`work/1007-pht-clean` はリモートにもローカルにも無く、`git checkout -b work/1007-pht-clean origin/cloudflare`
- CHAT-1007-PHT-08 の `## 報告` の状態は「完了」
- 未マージのリモートブランチ（work/1006-rvw-377・work/1007-pht-photo）は、scripts/cleanup_logs.py・.github/workflows/cleanup-logs.yml に触れていない

## 報告

- 状態: 中断（着手直後。作業中）
- ブランチ: work/1007-pht-clean
- ログ: https://github.com/retroeater/mj/blob/work/1007-pht-clean/docs/logs/CHAT-1007-PHT-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-pht-clean
- 確認用URL: なし
- マージ: 未
- issue: #513
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c7cc409b）: https://github.com/retroeater/mj-logs/tree/main/guide/c7cc409b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c7cc409b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
