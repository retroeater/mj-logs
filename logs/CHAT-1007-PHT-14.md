# CHAT-1007-PHT-14

- 着手日時: 2026-10-07
- 対象issue: #513（実装②）。通知先 #357
- ブランチ: work/1007-pht-doc
- 着手時HEAD: e3c5b6f5

## 指示

【Claude作成】Claude Code 向け指示：作業ログの書き方の規則を文書に入れ、続きとして読む形を広げる（#513 の実装②） Chat-Ref: CHAT-1007-PHT-14 マージ: 承認済み（チャットで、2026-10-07）。テストと試し実行（dry-run）が通り、文書の容量が上限内ならマージしてよい。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1007-PHT-12・CHAT-1007-PHT-13 とは別のセッションに貼るか、同じセッションなら前の指示が終わってから貼る。どちらの完了も待たない） 作業ブランチ: クラウドセッションで実行する。work/1007-pht-doc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-pht-doc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-pht-doc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06・CHAT-1007-PHT-07〜13 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1007-PHT-11 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、CLAUDE.md・docs/notes/branch-operations.md・docs/logs/_template.md・docs/instruction-template.md・scripts/cleanup_logs.py に触れているものがあれば、ブランチ名と触れているファイルを書いて止まる。

目的
#513（作業ログの自動削除の見直し）の実装の2本目。判定のコード（CHAT-1007-PHT-11 で cloudflare に入った）に合わせて、作業ログの書き方の規則を文書に入れる。あわせて、続きとして読む形を広げ、規則を入れる日（D）を設定する。
決定（2026-10-07、平野さん）

* 規則の中身と書く場所は、docs/decisions/operations.md「2026-10-07（CHAT-1007-PHT-08）」の grill の決定のとおり（Q1〜Q9・Q11）。書く場所は Q9: CLAUDE.md「作業ログ」節の「ログの寿命」の1行を核心1行に置き換える（目安 +200 バイト）／判定の正は docs/notes/branch-operations.md「作業ログの寿命」を書き直す／docs/logs/_template.md の「## 報告 の各項目の書き方」を直す／docs/instruction-template.md に続きの指示の定型を1行足す／docs/notes/chat-side-operations.md は変えない
* 続きとして読む形を広げる: 決まった形（` / 続き: CHAT-…`）に加えて、`（続き: CHAT-…）` と `→ 続き: CHAT-…` の形も続きとして読む。これから書く形は、決まった形のまま（grill Q5）。すでにあるログは書き直さない（grill Q10）
* テストと試し実行が通り、文書の容量が上限内ならマージしてよい

前提（チャット側。平野さんの決定ではない）

* CHAT-1007-PHT-11 のログ（mj-logs）で読んだこと:
   * 判定の事実は、同ログの `## 経過`「指示②（規則の文書）で書くべき判定の事実」にまとめてある。文書はこれとコードの実物に合わせる
   * 続きの形が決まった形でないために読まれないログが約40件あり（`判断待ち → 続き: …` が約20件、`判断待ち（続き: …）` が10件ほか）、そのうち続き先が完了のものが十数件
   * D は `scripts/cleanup_logs.py` の定数 `NEW_RULE_DATE`（今は空）。値を入れると、最初にコミットされた日が D 以後のログの「書き方の違反」だけを一覧にする
   * 直した後の試し実行: 対象外 163／削除対象 11／通知対象 33（判断待ち・中断 11、違反 21、読めない 1）。2026-10-07 の時点
* D の値の案: マージする日の翌日（JST）。マージの当日に、新しい規則を読む前に始まったログを「違反」に数えないため。マージが日をまたいだら、実際のマージの翌日に直す
* 文書の今の状態（チャット側が mj-logs のガイド ccdefd6b で読んだ。実物で確かめる）:
   * CLAUDE.md は 25,577 バイト（上限 32KB・警告域 30KB。最終目標は 20KB 前後）。「作業ログ」節の該当の行は「ログの寿命: `cleanup-logs.yml`が週次で片付ける。削除の条件と #357 の通知を見たときの扱い（マージ→論点の移動→削除の順）はdocs/notes/branch-operations.md「作業ログの寿命」」
   * docs/logs/_template.md の「状態」の書き方は「完了 / 判断待ち / 中断（エラー）のいずれか」。「判断が必要なこと」「未確認の項目」「エラー」は「箇条書き。無ければ『なし』」
   * docs/notes/branch-operations.md「作業を再開するとき」は、すでに「状態: 中断 / 続き: <新しいChat-Ref>」の形
* 文書に書くこと（決定の要点。文面は Code が書く。規則と理由の一句だけにし、事例は書かない）:
   * 「完了」は、平野さんの判断待ちも、移していない論点も無いときだけ書く。残るなら状態は「判断待ち」
   * 完了のログの「判断が必要なこと」「未確認の項目」は「なし」だけ。移した論点は「issue」の項目に番号を書く（`なし（#NNN に移した）` と書いてもよい）。「エラー」は、作業を止めた・結果に影響した未解決のものだけ。追跡しない確認と解決済みのエラーは `## 経過` に書く。先頭が「なし」でも子の行を続けると「なし」と読まれない
   * 続きは、状態の末尾に `/ 続き: CHAT-…` と書く。書くのは続きの指示を受けた Claude Code で、続きのログを作るときに前のログの状態に足す
   * 判断待ちのまま続きが出ないログは、平野さんが取り下げと決めたら、決定を docs/decisions に1行書いて状態を「取り下げ」にする
   * issue・カレンダーがログを名指しするときは、SHA を固定した permalink で書く
   * 自動で削除される条件と、#357 の通知の3種類
* ほかに直す見込みの所（要確認。同じ趣旨の古い記述があれば、消して置き換える）: docs/notes/static-generation.md の `cleanup_logs.py`・`cleanup-logs.yml` の説明、#357 の本文の「規則の文書は実装②で書き直す」の1行、docs/handover.md「期限付き・確認待ちタスク」（#513 の効果の確認の行を足す）
* 効果の確かめ方（grill Q12）: D から7日たった後の最初の週次と、その次の週次の2回で測る。日付はこの指示の報告に書く（チャット側がカレンダーに入れる）
* 使う skill は無い

手順

1. 確かめる。#513 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。CHAT-1007-PHT-11 のログ、docs/decisions/operations.md の該当の節、scripts/cleanup_logs.py とそのテスト、直す文書の今の内容（CLAUDE.md「作業ログ」節・「CLAUDE.md / handover.md の更新ルール」、docs/notes/branch-operations.md「作業ログの寿命」「作業を再開するとき」、docs/logs/_template.md、docs/instruction-template.md）を読む。直す前の `python3 scripts/cleanup_logs.py --dry-run` の結果と、文書ごとのバイト数をログに書く
2. コードを直す。続きとして読む形を広げ（`（続き: CHAT-…）`・`→ 続き: CHAT-…`）、unittest を足す（2つの形が読めること、CHAT の番号が無い「続き: なし」などは読まないこと、文中の「続きは CHAT-…」のような別の書き方は読まないこと、直す前のコードでは通らないこと）。`NEW_RULE_DATE` に D を入れる。現物のログで `--dry-run` を実行し、直す前と比べて、新しく削除の対象になるログの一覧（続き先とその状態つき）と、通知の種類ごとの件数を書く
3. 文書を直して、マージする
   * 決定の書く場所のとおりに文書を直す。CLAUDE.md は「作業ログ」節の1行の置き換えだけにする。直した後の文書ごとのバイト数を書く
   * コードの条件と文書の記述を1項目ずつ突き合わせ、食い違いが無いことを表で書く
   * 作業ブランチで cleanup-logs.yml を手動実行する（dry-run）。結果が手元の dry-run と一致することと、#357 にコメントが付かないことを確かめる
   * 止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージし、push で動いたワークフローと check-run の結果を書く
   * #357 の本文の1行を直し、#513 に、入れた規則の要点・D の値・効果を測る2回の週次の日付・新しく消える見込みのログの数をコメントする。CHAT-1006-PHT-05 と CHAT-1007-PHT-08 のログは完了しているので触らない

待ち方

* ワークフロー・check-run の完了を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書く（マージの前の手動実行が確かめられなかったら、マージしない）

止まる条件

* CHAT-1007-PHT-11 の状態が「完了」でない。未マージのブランチが同じファイルに触れている。#513 に他セッションの着手中コメントがある
* unittest（`python3 -m unittest discover -s scripts/tests`）が1件でも失敗する
* 現物の `--dry-run` で、直す前に消えるログが消えなくなる（1件でも）。新しく削除の対象になるログに、続き先が完了・取り下げ・削除済みでないものがある。新しく削除の対象になるログが30件を超える（一覧を書いて止まる）
* 作業ブランチでの手動実行が failure で終わった、または dry-run なのにログの削除・#357 へのコメントが起きた
* 直した後の CLAUDE.md が 30KB（30,720 バイト）以上、または CLAUDE.md の変更が「作業ログ」節の1行の置き換えの外に及ぶ。docs/handover.md が 26KB 以上になる
* 直す先の文書に、決定と矛盾していて、どちらが正か判断が要る記述がある（同じ趣旨の記述があるだけなら止まらず、置き換え・拡張して、どう処理したかを報告に書く）
* cloudflare に入る変更が、次で説明できる差分だけでない: scripts/cleanup_logs.py・そのテスト・CLAUDE.md・docs/（notes/branch-operations.md・notes/static-generation.md・logs/_template.md・instruction-template.md・handover.md・decisions/・logs/）
* .github/workflows/ を変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 直す前後の dry-run の比較、テストの結果、文書ごとのバイト数（前後）、コードと文書の突き合わせの表、手動実行の結果、マージのコミット、D の値、効果を測る2回の週次の日付、#357・#513 の更新がログにある
* CLAUDE.md・docs/logs/_template.md・docs/instruction-template.md に入れた文面（変えた行）を、`## 経過` にそのまま貼る（チャット側が読んで、以後の指示文の書き方を合わせるため）
* ログの「## 報告」を、この指示で入れた新しい規則のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-PHT-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-PHT-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep="CHAT-1007-PHT-14"` は0件。`work/1007-pht-doc` はローカルにもリモートにも無く、`git checkout -b work/1007-pht-doc origin/cloudflare`
- CHAT-1007-PHT-11 の `## 報告` の状態は「完了」。`git branch -r --no-merged origin/cloudflare` に、CLAUDE.md・docs/notes/branch-operations.md・docs/logs/_template.md・docs/instruction-template.md・scripts/cleanup_logs.py に触れているブランチは無い。#513 に他セッションの着手中コメントは無い

## 報告

- 状態: 中断（着手直後。作業中）
- ブランチ: work/1007-pht-doc
- ログ: https://github.com/retroeater/mj/blob/work/1007-pht-doc/docs/logs/CHAT-1007-PHT-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-pht-doc
- 確認用URL: なし
- マージ: 未
- issue: #513
- 判断が必要なこと: 着手直後のため、まだ無い
- 未確認の項目: 着手直後のため、まだ無い
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e3c5b6f5）: https://github.com/retroeater/mj-logs/tree/main/guide/e3c5b6f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
