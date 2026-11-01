# CHAT-1101-RDR-07

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-ua
- 着手時HEAD: 94fb9bf

## 指示

【Claude作成】Claude Code 向け指示：RDR-05 の続き（429 の件を切り離し、道具の UA の候補と空の UA を足して確かめ、cloudflare へマージする） Chat-Ref: CHAT-1101-RDR-07 マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が scripts/fetch_redirect_counts.py・scripts/tests/test_fetch_redirect_counts.py・docs/notes/redirects.md の「流入の測り方」の節・docs/decisions/cloudflare.md の追記・ログだけ 貼る時機: いつでも（CHAT-1101-RDR-05 と同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-ua の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-rdr-ua を続けて使う（CHAT-1101-RDR-05 の変更をそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-RDR-05 のログの `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。

目的
RDR-05 の「判断が必要なこと」への回答。429 の件は切り離し、道具の UA の候補と空の UA を足したうえで、RDR-05 の変更と一緒に cloudflare へ入れる。
決定（2026-11-01、平野さん）

* RDR-05 の作業ブランチの変更は、429 の件と切り離してマージする
* 道具の UA の候補（Wget・python-httpx・aiohttp・okhttp・Java/・libwww-perl・Scrapy）を `BOT_UA_PATTERNS` に足す。空の UA も「ボット以外」に数えない
* 旧 URL への 429（2026-10 分で houou_leagues 360・houou_results 300・ouka_leagues 140・ouka_results 210、どれも今の条件で「ボット以外」）の出どころは、今は調べない。理由: 削除の基準と #568 の表は 301 だけを数え、429 は Rate limiting が止めているため
* `count` は推定値という RDR-05 の判断はそのまま採る

前提（チャット側。平野さんの決定ではない）

* RDR-05 の指示文の前提「429 は 10/29 の `curl/8.5.0` の一括取得」は誤りだった（チャット側の誤り。CHAT-1101-MCK-25 の 851件は公開中のページへの取得で、旧 URL への 429 とは別と見られる）。手順3の止まる条件の 429 の項は、この指示では使わない
* 空の UA の除外の書き方（`userAgent_neq: ""` など）が GraphQL で通るかは未確認（要確認）。通らなければ、空の UA の件数を別に数えて差し引くなど、実物に合わせてよい

手順

1. CHAT-1101-RDR-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1101-RDR-07` を足す。`BOT_UA_PATTERNS` に決定の候補を足し（RDR-05 の書き方にそろえる）、空の UA を「ボット以外」から外す。テストを足す・直す
2. 作業ブランチで `fetch-redirect-counts.yml` を手動実行する（dry_run: 真、month: 2026-10）。全行の「301・ボット以外」を RDR-05 の手動実行の値と並べた表（全件、略さない）をログに書く。docs/notes/redirects.md「流入の測り方」の「ボット」の項に、足した候補・空の UA・429 を調べない決定（理由の一句）を書き（読んでから直す。同じ趣旨の記述は置き換えてよい）、決定を docs/decisions/cloudflare.md に足す
3. 冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

止まる条件

* どれかの行の「301・ボット以外」が、RDR-05 の手動実行の値より増えた（filter の書き方の誤りを疑う。推定値の揺れで ±10 以内なら止めず、ログに書く）
* 空の UA の除外が問い合わせで通らず、差し引く方法でも数えられない（その項だけ外して残りを進め、ログに書く。全部が通らないときだけ止まる）
* 未マージの work/ ブランチが `scripts/fetch_redirect_counts.py` の `BOT_UA_PATTERNS`・問い合わせを作る関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手動実行がトークン・権限で失敗した（別の手段を試さずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d356d1bb）: https://github.com/retroeater/mj-logs/tree/main/guide/d356d1bb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d356d1bb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
