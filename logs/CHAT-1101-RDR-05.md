# CHAT-1101-RDR-05

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-ua
- 着手時HEAD: 6aac53d

## 指示

【Claude作成】Claude Code 向け指示：転送の件数の集計で、自分たちのアクセス（curl 等・目印の UA）を「ボット以外」から外し、件数が推定値かを確かめて表に注記する Chat-Ref: CHAT-1101-RDR-05 マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が scripts/fetch_redirect_counts.py・scripts/tests/test_fetch_redirect_counts.py・docs/notes/redirects.md の「流入の測り方」の節・docs/decisions/cloudflare.md の追記・ログだけ 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-ua の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-rdr-ua を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-rdr-ua origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#568 の月次の集計（`fetch_redirect_counts.py`）の「ボットらしい UA を除く」に、自分たちの一括アクセス（curl など）が人として入っている。これを外し、件数が抜き取り（サンプリング）からの推定値かどうかを確かめて、#568 の表の読み方を正しくする。
決定（2026-11-01、平野さん。チャットで推奨どおりと決めた）

* ボットとみなす UA に、よく使われる道具の UA と、自分たちの目印の UA `mj-internal` を足す
* `httpRequestsAdaptiveGroups` の `count` が抜き取りを補正した推定値かを公式の資料で確かめ、表に注記する（注記だけで済ませず、意味を確かめる）
* 月初の数時間の欠け（保持期間31日）は受け入れる。集計の時刻は変えない
* 文書と表の変更（入れた日の直し・判定できる日の列・目印の UA の規則）は次の指示 CHAT-1101-RDR-06 で行う。Search Console の着地の列の自動化は今は行わず、2027-01 の棚卸しはチャット側が page.csv を見る

前提（チャット側。平野さんの決定ではない）

* 2026-10 分（`docs/redirect-counts/2026-10.csv`）の 429 の行は、houou_leagues 360・houou_results 300・ouka_leagues 140・ouka_results 210 で、どれも count と count_non_bot が同じ。出どころは 10/29 の SEO の一括取得（UA `curl/8.5.0`、CHAT-1101-MCK-25 で確定）
* 今の `BOT_UA_PATTERNS` は bot・crawl・spider・slurp を大文字・小文字の2通りで並べている。`userAgent_notlike` が大文字・小文字を区別するかは未確認（要確認）
* 足す候補: `curl`・`python-requests`・`python-urllib`・`Go-http-client`・`HeadlessChrome`・`Playwright`・`node-fetch`・`axios`・`mj-internal`。filter の AND の条件の数に上限があるかは未確認（要確認）
* 件数の多くが 10 の倍数（10・20・30…）で、抜き取りが 1/10 前後と見られる。スクリプトは `count` をそのまま足しており、`sampleInterval` を読んでいない
* run-monthly の push の衝突（`retry: rebase`）は WKP の系列で直してある。この指示では `.github/workflows/` を変えない

手順

1. Cloudflare の公式の資料（GraphQL Analytics API の sampling の説明・`httpRequestsAdaptiveGroups`・filter の `_notlike`）で次を確かめ、出典の URL とともにログに書く: (a) `count` が抜き取りを補正した推定値か、抜き取った行の数か (b) `_notlike` が大文字・小文字を区別するか (c) filter の条件の数の上限。(a) が「抜き取った行の数」なら、ここで止まって報告する（過去の CSV と #568 の数の意味が変わるため）
2. `BOT_UA_PATTERNS` に上の候補を足す（(b) の結果に合わせて書き方を今の流儀にそろえるか、区別しないなら重複を整理する）。(c) の上限に当たるなら、効きの大きい順（curl・python-requests・HeadlessChrome・mj-internal を優先）に絞り、外したものと理由をログに書く。#568 のコメントの末尾の注記に「件数は Cloudflare の抜き取り（adaptive sampling）から推した推定値。数十件以下は誤差が大きい」の趣旨の1行を足す（(a) の結果に合わせて文面を直してよい）。`sampleInterval` を問い合わせに足すのが簡単なら、月の平均の抜き取りの間隔もコメントの注記に出す（難しければ出さず、理由をログに書く）。テスト（`scripts/tests/test_fetch_redirect_counts.py`）を足す・直す
3. 作業ブランチで `fetch-redirect-counts.yml` を手動実行する（dry_run: 真、month: 2026-10）。出力で、429 の4行（houou_leagues・houou_results・ouka_leagues・ouka_results）の「ボット以外」が、今の 2026-10.csv の値と比べて大きく減ったかを見る（集計の範囲は保持期間で詰まるので、10/29 が範囲に入っていることを確かめる）。全行の「301・ボット以外」を今の CSV の値と並べた表（全件、略さない）をログに書く。docs/notes/redirects.md「流入の測り方」の「ボット」の項を今の内容に合わせて直し（読んでから直す。同じ趣旨の記述は置き換えてよい）、決定を docs/decisions/cloudflare.md に足して、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

止まる条件

* 手順1の (a) が「抜き取った行の数」だった
* 手順3で、429 の4行の「ボット以外」が今の値の半分より多く残る（10/29 が範囲に入っているのに）。または、どれかの行の「301・ボット以外」が、範囲が狭いのに今の値より増えた（filter の書き方の誤りを疑う）
* 同じ論点（集計のボットの判定）の open issue・未マージの work/ ブランチが `scripts/fetch_redirect_counts.py` の `BOT_UA_PATTERNS`・問い合わせを作る関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手動実行がトークン・権限で失敗した（別の手段を試さずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cc449e20）: https://github.com/retroeater/mj-logs/tree/main/guide/cc449e20

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
