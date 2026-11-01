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

## 経過

### 着手前の確認

- 0: このログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット: なし（`git fetch --unshallow` の後に `git log --all --grep`）。`RDR` は 05 なので最初の指示の重複確認は対象外
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- #568: open
- 作業ブランチ: ローカル・リモートとも `work/1101-rdr-ua` が無かったため `git checkout -b work/1101-rdr-ua origin/cloudflare`（起点 6aac53d）
- 0章ゲート: 未マージの work/ ブランチで `scripts/fetch_redirect_counts.py`・テスト・`docs/notes/redirects.md`・`docs/decisions/cloudflare.md` を変えているものは無い。open issue（更新順の上位100件）に集計のボットの判定を扱うものは #568 のほかに無い。CLAUDE.md 23,768 バイト・handover.md 23,354 バイトで警告域（26KB）の外

### 手順1: 公式の資料

読んだ資料（いずれも developers.cloudflare.com）:

- https://developers.cloudflare.com/analytics/graphql-api/sampling/
- https://developers.cloudflare.com/analytics/sampling/
- https://developers.cloudflare.com/analytics/graphql-api/features/confidence-intervals/
- https://developers.cloudflare.com/analytics/faq/graphql-api-inconsistent-results/
- https://developers.cloudflare.com/analytics/graphql-api/migration-guides/graphql-api-analytics/
- https://developers.cloudflare.com/analytics/graphql-api/features/filtering/
- https://developers.cloudflare.com/analytics/graphql-api/limits/
- https://developers.cloudflare.com/analytics/graphql-api/features/data-sets/

(a) **`count` は抜き取りを補正した推定値（抜き取った行の数ではない）。止まる条件には当たらない。**

- 「Sampling」: "Cloudflare Analytics returns an estimate derived from the sampled value." 例として 5,000 行の抜き取り（1/10）から "Cloudflare will estimate 50,000 total events (5,000 × 10) and report this value in Analytics."
- FAQ: "Aggregated metrics (totals, averages, percentiles) are extrapolated based on the sample size, so reported metrics accurately represent the entire dataset."
- 「Confidence intervals」の例（`firewallEventsAdaptiveGroups`）: `"count": 42939, "avg": {"sampleInterval": 1.07…}, "confidence": {"count": {"estimate": 42939, …, "sampleSize": 40054}}`。`count` と推定値 `estimate` が等しく、抜き取った行の数は `sampleSize` として別に返る
- 移行の手引き: `httpRequestsAdaptiveGroups` の `count`（例 LHR 4350、`sampleInterval` 10）が、抜き取りの無い旧 API の `requests`（LHR 4404）とほぼ同じ。`count` を `sampleInterval` で掛け直すとは書かれていない（掛けると10倍になって合わない）
- 「`count` は推定値」と明言する1文は無い。上の例と記述からそう判断した。`sampleInterval` の意味を定める文も無く、例の値（10、1.07…）から「何件に1件を抜き取ったか」の平均と読んだ

(b) **`like`／`notlike` が大文字・小文字を区別するかは公式に書かれていない。** 「Filtering」は "The `like` operator is available for string comparisons and supports the `%` character as a wildcard." だけ。`_ilike` などの演算子の記載も無い。今の流儀（区別する前提で bot・crawl・spider・slurp の両方を書く）を残し、足す道具の UA は表記が決まっているためその表記だけ書いた。区別してもしなくても漏れない

(c) **filter の条件の数の上限は公式に無い。** 「Limits」にあるのはゾーンの数（10）・問い合わせの数・1回の問い合わせの欄の数・行の数・期間（`settings` で読む）と、5分で300回の割り当てだけ。候補9件を全部足した（条件は全部で17）

### 手順2: 実装（6d0c14f）

- `BOT_UA_PATTERNS` に `%curl%`・`%python-requests%`・`%Python-urllib%`・`%Go-http-client%`・`%HeadlessChrome%`・`%Playwright%`・`%node-fetch%`・`%axios%`・`%mj-internal%` を足した。候補の `python-urllib` は、Python の標準の UA が `Python-urllib/3.x`（先頭が大文字）なので `Python-urllib` と書いた（区別しないなら同じ）。`Playwright` は既定の UA には出ない（`HeadlessChrome` で当たる）が、候補どおり残した。`_` は LIKE の1文字の万能記号なので含めていない（テストで確かめる）
- 問い合わせに `avg { sampleInterval }` を足し、抜き取った行の数の見積もり（件数 / 間隔）の合計で件数の合計を割って、月の抜き取りの間隔の平均を出す（ボットの条件を付けない問い合わせの分）。#568 のコメントの注記の末尾に「件数は Cloudflare の抜き取り（adaptive sampling）から推した推定値。数十件以下は誤差が大きい（この月の抜き取りの間隔の平均: 約 N 件に1件）」を足した
- コメントの表の列名「ボットらしい UA を除く」は変えていない（表の変更は RDR-06 の範囲）
- テスト: 目印・道具の UA が問い合わせに入る、実物の形の UA に当たり人の UA には当たらない、`_` を含まない、間隔の平均の重み付け、注記の文面。`python3 -m unittest discover -s scripts/tests` は 1721 件 OK。修正前のスクリプトに新しいテストを当てると 4 件 FAIL・1 件 ERROR（#310 の確認）

### 手順3: 作業ブランチでの手動実行（dry-run、month 2026-10）

- 1回目: run 41265041450（6d0c14f、成功）。#568 のコメントの本文は出たが、429 の行のボット以外の件数は CSV（ランナーの中だけ）にしか出ず、ログで見られなかった。標準出力に「同（ボットらしい UA を除く）」の状態コードごとの件数を出す1行を足した（104b817）
- 2回目: run 41265136755（104b817、成功）。トークン・権限のエラーなし。新しい条件（17個の `userAgent_notlike`）で GraphQL はエラーを返さなかった（(c) の上限に当たっていない）
- 集計の範囲: UTC 2026-10-01T02:35:53Z 〜 2026-10-31T15:00:00Z（保持期間で月の始まりから詰めた）。**10/29 は範囲に入っている**
- 月の抜き取りの間隔の平均: 約 10.1 件に1件（コメントの注記に出た）

**429 の4行の「ボット以外」は減らなかった（止まる条件に当たる）:**

| 転送元 | 429（今回） | 429・ボット以外（今の CSV） | 429・ボット以外（今回） |
|---|---|---|---|
| `/houou_leagues.html` | 360 | 360 | 360 |
| `/houou_results.html` | 300 | 300 | 300 |
| `/ouka_leagues.html` | 140 | 140 | 140 |
| `/ouka_results.html` | 210 | 210 | 210 |

**301・ボット以外（全件）:** どの行も今の値より増えていない（もう一つの止まる条件には当たらない）。10 行で減っており、除外の条件そのものは効いている。

| 転送元 | 301（今の CSV） | 301（今回） | 301・ボット以外（今の CSV） | 301・ボット以外（今回） | 差 |
|---|---|---|---|---|---|
| `/saikyo_results.html` | 142 | 141 | 20 | 20 | 0 |
| `/jpml_titles.html` | 673 | 673 | 321 | 311 | -10 |
| `/video_wayhome.html` | 30 | 30 | 10 | 10 | 0 |
| `/video_mtsuku.html` | 30 | 30 | 10 | 0 | -10 |
| `/jpml_test.html` | 20 | 20 | 20 | 0 | -20 |
| `/tanilog.html` | 20 | 20 | 20 | 20 | 0 |
| `/houou/ranking/` | 13 | 13 | 13 | 13 | 0 |
| `/ouka/ranking/` | 0 | 0 | 0 | 0 | 0 |
| `/jwrc/ranking/` | 10 | 10 | 10 | 0 | -10 |
| `/wrcr/ranking/` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/ranking/` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/ranking/qualifier-win/` | 20 | 20 | 20 | 0 | -20 |
| `/saikyo/ranking/qualifier-final/` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/ranking/qualifier-pass/` | 0 | 0 | 0 | 0 | 0 |
| `/houou_ranking.html` | 51 | 51 | 41 | 21 | -20 |
| `/houou_leagues.html` | 447 | 445 | 260 | 220 | -40 |
| `/houou_race.html` | 32 | 32 | 32 | 32 | 0 |
| `/houou_results.html` | 512 | 511 | 356 | 336 | -20 |
| `/ouka_ranking.html` | 14 | 14 | 0 | 0 | 0 |
| `/ouka_leagues.html` | 985 | 985 | 190 | 180 | -10 |
| `/ouka_results.html` | 1148 | 1148 | 120 | 120 | 0 |
| `/wrc_ranking.html` | 0 | 0 | 0 | 0 | 0 |
| `/wrc_results.html` | 12 | 12 | 0 | 0 | 0 |
| `/saikyo/2011.html` | 10 | 10 | 0 | 0 | 0 |
| `/saikyo/2012.html` | 10 | 10 | 0 | 0 | 0 |
| `/saikyo/2013.html` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/2014.html` | 1 | 1 | 0 | 0 | 0 |
| `/saikyo/2015.html` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/2016.html` | 10 | 10 | 0 | 0 | 0 |
| `/saikyo/2017.html` | 20 | 20 | 0 | 0 | 0 |
| `/saikyo/2018.html` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/2019.html` | 20 | 20 | 0 | 0 | 0 |
| `/saikyo/2020.html` | 21 | 21 | 0 | 0 | 0 |
| `/saikyo/2021.html` | 11 | 11 | 0 | 0 | 0 |
| `/saikyo/2022.html` | 12 | 12 | 0 | 0 | 0 |
| `/saikyo/2023.html` | 0 | 0 | 0 | 0 | 0 |
| `/saikyo/2024.html` | 31 | 30 | 20 | 10 | -10 |
| `/saikyo/2025.html` | 21 | 30 | 10 | 0 | -10 |
| `/saikyo/2026.html` | 81 | 81 | 30 | 30 | 0 |

- `/saikyo/2025.html` の 301 の総数は、範囲が狭いのに 21 → 30 に増えた（条件を付けない問い合わせなので filter の誤りではない）。推定値は問い合わせごとに選ばれる抜き取りの段で揺れる（公式 FAQ「GraphQL API inconsistent results」）。数十件以下の誤差の例
- 429 が減らなかった理由は確かめていない。考えられること: (1) 前提「429 は 10/29 の `curl/8.5.0` の一括取得」が違う（429 の UA が curl でない） (2) 429 の行（レート制限などで Cloudflare が返した応答）では `userAgent` の値が `curl/…` の形で記録されていない。見分けるには、429 の行を `userAgent` でまとめる問い合わせを1回流せばよい（今のスクリプトには無い。この指示の範囲外なので足していない）
- 止まる条件に当たったため、docs/notes/redirects.md と docs/decisions/cloudflare.md の変更は作業ブランチにコミットしたまま（856c7a5）で、cloudflare へはマージしていない

### 差分の自己レビュー（新しい文脈のサブエージェント、1回）

| 指摘 | 扱い |
|---|---|
| 中: `avg { sampleInterval }` を本体の問い合わせに入れたので、返らないゾーンでは総数まで取れなくなる | 意図どおり（run 41265041450・41265136755 で返り、エラーなし。平均 10.1 が注記に出た） |
| 低: docs/notes/redirects.md の「2026-11-01 から」が紛らわしい（2026-10.csv は 11/01 に古いコードで作られた） | 直した（c8ca4d0。「2026-11 分から」、直近3か月の合計にも 2026-12 分まで混ざる旨） |
| 低: コメント「区別しなくても漏れない」が逆 | 直した（19196a8。「区別しても漏れない」） |
| 低: `avg` が無い行の分岐にテストが無い | 直した（19196a8。テスト1件を追加。1722 件 OK） |
| 低（任意）: Wget・python-httpx・aiohttp・okhttp・Java/・libwww-perl・Scrapy と、空の UA が漏れる | 見送り（指示の候補の外。`## 報告`「判断が必要なこと」に移した） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1101-rdr-ua
- ログ: https://github.com/retroeater/mj/blob/work/1101-rdr-ua/docs/logs/CHAT-1101-RDR-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-rdr-ua
- 確認用URL: なし（scripts/ と docs/ だけの変更。Workers Builds の対象外）
- マージ: 未（止まる条件に当たった）
- issue: #568（コメントはしていない。dry-run のみ）
- 判断が必要なこと:
  - 止まる条件に当たった: 10/29 が集計の範囲に入っているのに、429 の4行（houou_leagues 360・houou_results 300・ouka_leagues 140・ouka_results 210）の「ボット以外」が新しい条件でも1件も減らなかった。301 の「ボット以外」は10行で減っており、条件自体は効いている。前提「429 は 10/29 の `curl/8.5.0`」が違うか、429 の行の `userAgent` が curl の形で記録されていない可能性がある。次の手の候補: 429 の行を `userAgent` でまとめる問い合わせを1回だけ流して UA を確かめる（スクリプトかワークフローに一時の出力を足す指示が要る）
  - 作業ブランチの変更（`BOT_UA_PATTERNS` の追加・推定値の注記・抜き取りの間隔の平均・ボット以外の件数の出力・docs/notes/redirects.md・docs/decisions/cloudflare.md）はマージしていない。429 の件と切り離してマージしてよいかの判断
  - `count` は推定値と判断した（公式に明言の1文は無く、「Sampling」「Confidence intervals」の例と移行の手引きから。根拠は `## 経過` の手順1）
  - 道具の UA の追加の候補（Wget・python-httpx・aiohttp・okhttp・Java/・libwww-perl・Scrapy）と、空の UA を「ボット以外」に数えていることを扱うか
  - 目印の UA `mj-internal` を名乗る取得の仕組みはまだリポジトリに無い（規則は RDR-06 の範囲）
- 未確認の項目:
  - `like`／`notlike` が大文字・小文字を区別するか（公式に記載なし。区別しても漏れない書き方にした）
  - 429 の行の実際の User-Agent
- エラー: なし

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
