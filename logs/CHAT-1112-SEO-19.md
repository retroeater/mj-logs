# CHAT-1112-SEO-19

- 着手日時: 2026-11-12
- 対象issue: #458, #680
- ブランチ: work/1112-seo19
- 着手時HEAD: 176104e87

## 指示

【Claude作成】Claude Code 向け指示：#458 の URL 検査 API で、サイトマップの全 URL の Google の登録状態を取る仕組みを作り、権限を1件で確かめて止まる（#680 の原因調べを兼ねる）
Chat-Ref: CHAT-1112-SEO-19
マージ: 判断待ちで止まる
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1112-seo19〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-seo19 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1112-seo19 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-seo19 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
サイトマップに載っている全 URL について、Google の登録状態・Google が選んだ正規 URL・最後のクロールの日時を URL ごとに取れるようにする（#458）。きっかけは #680: 男子の入口 `title/nihon-series/` を平野さんが URL 検査で見たところ「URL が Google に認識されていません」「参照元サイトマップが検出されませんでした」「参照元ページが検出されませんでした」だった（2026-11-12）。サイトマップには載っているので、title/ のほかのページも同じ状態の可能性がある。この指示では仕組みを作り、権限を1件で確かめ、サイトマップの読み込みの状態を取って止まる。全件の取得はマージの後の別の指示で行う。

### 決定（2026-11-12、平野さん）
- Google の登録状態を網羅的に調べる。#458（URL 検査 API）を今進める。最初は全件（サイトマップの URL、2026-11-12 の時点で 2,398件）を、1日の上限に合わせて日を分けて取り、結果を docs/gsc/ に表で残す。その後は月1回の定期取得にする

### 前提（チャット側。平野さんの決定ではない）
- サイトマップの件数（origin/cloudflare をチャット側が数えた、要確認）: pages 54・wayhome 45・saikyo 892・title 385・live 947・mtsuku 62・exam 13（計 2,398）。`sitemap.xml` はこの7つを並べたインデックス
- 平野さんの Search Console の「サイトマップ」の画面（2026-11-12、スマホ）: 送信済みは `sitemap.xml`（インデックス、送信 2026/11/04）・`sitemap-saikyo.xml`（2026/10/27）・`sitemap-pages.xml`（2026/09/12）・`sitemap-wayhome.xml`（2026/09/12）の4件。最終読み込み日時・状態・検出されたページ数の列は画面の幅で切れていて読めなかった。`sitemap-title.xml` は単独では送信されていない（インデックス経由だけ）
- 使う API と権限: URL 検査は `urlInspection.index.inspect`、サイトマップの状態は Search Console API の `sitemaps.list` / `sitemaps.get`（`lastDownloaded`・`isPending`・`errors`・`warnings`・`contents` の件数）。スコープは `webmasters.readonly`。サービスアカウント `gsc-export` の Search Console の権限は「制限付き」で、`searchanalytics.query` は通ることが実測済み。URL 検査が「制限付き」で呼べるかは未確認（#458 の本文）。上限は 2,000件/日・600件/分の見込み（#458 の本文。公式の本文と照合していない）。どれも公式のドキュメントで確かめてから書く（読めないときは「未確認」と書く）
- 鍵はコンテナに無いので、API はワークフローから呼ぶ（docs/notes/cloud-sessions.md「鍵」、月次の `fetch-gsc` と同じ経路）。新しいワークフローを足すか、既存のワークフローに手動の入口を足すかは、docs/decisions/automation.md と docs/notes/github-actions.md の決まりに従う（定期の起動は Worker からの run-daily・run-weekly・run-monthly に集めている。定期の取得への組み込みは、この指示では行わない）。作業ブランチからの手動実行ができない形なら、そう書いて止まる
- 作る仕組みの形（チャット側の案。実物に合わせて変えてよい）: サイトマップの URL を読み、URL 検査の結果を1行1URLの CSV（URL・`verdict`・`coverageState`・`indexingState`・`googleCanonical`・`userCanonical`・`lastCrawlTime`・`pageFetchState`・`robotsTxtState`・`sitemap`〈参照元サイトマップ〉・`referringUrls` の件数）で `docs/gsc/inspect/<取得日>.csv` に書く。1回の実行の件数の上限（既定 1,900）と、続きから取る（すでに取った URL を飛ばす）入口を持たせ、2日に分けられるようにする。サイトマップの状態は `docs/gsc/inspect/<取得日>-sitemaps.md` に書く
- 費用: URL 検査 API・Search Console API は無料の見込み（要確認）。Actions の使用量（#298）は、1件ごとの呼び出しの待ちで実行時間が延びるので、1回の見込みの分数を報告に書く
- #680 は、全件の取得の結果で原因を書く（この指示では、権限の確かめの1件に `https://ryoei.pro/title/nihon-series/` を使い、その結果を #680 にコメントする）

## 手順
1. #458 の本文・コメントと、docs/gsc/README.md・`scripts/fetch_gsc.py`・月次の取得のワークフローを読み、上の前提の形で仕組みを作る（スクリプト・ワークフローの入口・テスト〈API を呼ばない部分: サイトマップの読み・続きからの判定・CSV の書き出し〉）。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. 作業ブランチで手動実行し、(a) 件数の上限を1にして `https://ryoei.pro/title/nihon-series/` を1件だけ URL 検査する (b) `sitemaps.list` と、7つの子サイトマップの `sitemaps.get` を取る。結果（返った項目の全部、エラーならメッセージ全文）をログに書く。結果のファイルは作業ブランチにコミットしてよい
3. #680 に (a) の結果を、#458 に (a)(b) の結果と仕組みの形をコメントする（本文・状態・ラベルは変えない）。ログを仕上げ、作業ブランチを push して判断待ちで止まる。`## 報告`「判断が必要なこと」に、権限の結果（呼べた／権限が足りない〈平野さんの作業: 権限を「フル」に上げるか〉）、全件の取得の日程（2日に分ける案）、マージの可否を書く

## 止まる条件
- 手順2の (a) が権限の不足で失敗した（権限を変えず、エラーの全文を書いて止まる。(b) は進めてよい）
- ワークフローの追加・変更が docs/decisions/automation.md の決まりと合わず、WFL 系列の判断が要る（作らずに、合わない点を書いて止まる）
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが、`scripts/fetch_gsc.py` や月次の取得のワークフローの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-SEO-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-SEO-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 同じ Chat-Ref のコミット・ログは無し。SEO 系列の既存の指示なので `XXX` の重複確認は対象外
- issue の state: #458・#680 とも open。#458 のコメントは期日と最強戦の旧 URL の扱いで、着手中のコメントは無し。#680 はコメント無し
- 0章ゲート: 未マージの work/ ブランチのうち `scripts/fetch_gsc.py`・`fetch-gsc.yml`・`run-monthly.yml`・`docs/gsc/README.md` を変えるものは無し。`docs/notes/github-actions.md` は work/1101-prp-pub・work/1103-ryo5 が別の行（check-image-links・fetch-x-posts の行）を変えていて、この作業の行（fetch-gsc の節）とは重ならない。CLAUDE.md 23,972B・handover.md 24,510B（警告域 26KB の手前）
- 作業ブランチ: ローカル・リモートとも無し → `git checkout -b work/1112-seo19 origin/cloudflare`（176104e87）

### 1. 公式ドキュメントで確かめたこと（2026-11-12 に developers.google.com の本文を読んだ）

- 上限（Usage Limits）: URL 検査はサイト単位 **2,000件/日・600件/分**、プロジェクト単位 10,000,000件/日・15,000件/分。sitemaps など「その他」はユーザー単位 20件/秒・200件/分。#458 の本文の値と一致
- 費用（Pricing）: 「All use of Google Search Console API is free of charge」。上限だけがある
- `urlInspection.index.inspect`: `POST https://searchconsole.googleapis.com/v1/urlInspection/index:inspect`、本文は `inspectionUrl`・`siteUrl`・`languageCode`。スコープは `webmasters` か `webmasters.readonly`
- 返る項目（`IndexStatusInspectionResult`）: `sitemap[]`・`referringUrls[]`・`verdict`（PASS／NEUTRAL＝除外／FAIL）・`coverageState`・`robotsTxtState`・`indexingState`・`lastCrawlTime`・`pageFetchState`・`googleCanonical`・`userCanonical`・`crawledAs`
- `sitemaps.list`・`sitemaps.get`: スコープは `webmasters.readonly` で可。項目は `path`・`lastSubmitted`・`isPending`・`isSitemapsIndex`・`type`・`lastDownloaded`・`warnings`・`errors`・`contents[]`（`indexed` は非推奨）
- サイトマップの件数（origin/cloudflare）: pages 54・wayhome 45・saikyo 892・title 385・live 947・mtsuku 62・exam 13、計 2,398（前提と一致。`sitemap-books.xml` はインデックスに無い）

### 2. 作った仕組み（cf0eaa8de）

- `scripts/inspect_gsc_urls.py`（新規）: `sitemap.xml` の子サイトマップをリポジトリのファイルから読み（本番は取りに行かない）、URL ごとに URL 検査を呼んで `docs/gsc/inspect/<取得の開始日>.csv` に1行ずつ足す
  - 列: `url`・`listed_in`（載っている子サイトマップ）・`verdict`・`coverageState`・`indexingState`・`googleCanonical`・`userCanonical`・`lastCrawlTime`・`pageFetchState`・`robotsTxtState`・`crawledAs`・`sitemap`（Google が知る参照元サイトマップ）・`referringUrls_count`・`referringUrls`・`inspected_at`
  - `--limit`（既定 1,900）・`--resume`（いちばん新しい `<日付>.csv` に続きを足す。同じファイルにある URL は飛ばす）。1行ずつ書き出すので、途中で止まっても取った分は残る。呼び出しの間は 0.11秒以上空ける（600件/分）
  - `--url`（指定した URL だけを検査。`<取得日>-urls.csv` に書き、全件の続きには混ぜない）・`--print-json`（応答をそのまま出す）
  - `--sitemaps`: `sitemaps.list` とインデックス＋7つの子の `sitemaps.get` を `<取得日>-sitemaps.md` に表と JSON で書く（取れないものは止めずにエラーの文を残す）
  - 認証・再試行・403 の扱いは `fetch_gsc.py` の `_authorized_session()`・`_request()` をそのまま使う
- `scripts/tests/test_inspect_gsc_urls.py`（新規、9件）: サイトマップの読み（重複・サイト外）・続きからの判定・CSV の書き出し（見出し1回・追記）・サイトマップの表
- `.github/workflows/fetch-gsc.yml`: 手動実行の入力 `task`（export／inspect、既定 export）と `inspect_limit`・`inspect_urls`・`inspect_resume`・`inspect_sitemaps` を足した。`TASK` は `workflow_dispatch` のときだけ入力を見て、ほか（schedule・run-monthly からの `workflow_call`）は常に export。「取得」と「robots.txtの差分を知らせる」は export のときだけ、新しい「URL 検査」は inspect のときだけ動く。コミットの件名は inspect のとき「chore: fetch Search Console URL inspection」
  - 新しいワークフローにしなかった理由: 新規のワークフローは既定ブランチに無いため作業ブランチから手動実行できない（docs/notes/github-actions.md「ワークフローを変更したとき」）。月次の取得と同じ鍵・同じ経路の既存の本に手動の入口を足した。定期の取得への組み込み（run-monthly）はしていない
- 文書: `docs/gsc/README.md`「API で取れないもの」の URL 検査の行、`docs/notes/github-actions.md` の fetch-gsc の項に1行
- `python3 -m unittest discover -s scripts/tests`: 2,122件 OK（ローカル。ワークフローの「テスト」でも成功）

### 3. 作業ブランチでの手動実行（run 43163808026、success）

入力: `commit=true`・`task=inspect`・`inspect_limit=1`・`inspect_urls=https://ryoei.pro/title/nihon-series/`・`inspect_sitemaps=true`。結果は bot のコミット cf61dba16（`docs/gsc/inspect/2026-11-12-urls.csv`・`2026-11-12-sitemaps.md`）。「取得」と robots の通知は skipped。

(a) URL 検査（「制限付き」の権限で**呼べた**）。応答の `indexStatusResult` の全項目:

| 項目 | 値 |
|---|---|
| verdict | NEUTRAL |
| coverageState | クロール済み - インデックス未登録 |
| robotsTxtState | ALLOWED |
| indexingState | INDEXING_ALLOWED |
| lastCrawlTime | 2026-11-12T05:41:41Z |
| pageFetchState | SUCCESSFUL |
| googleCanonical | https://ryoei.pro/title/nihon-series/ |
| userCanonical | https://ryoei.pro/title/nihon-series/ |
| sitemap | https://ryoei.pro/sitemap.xml |
| crawledAs | MOBILE |
| referringUrls | （無し） |

ほかに `mobileUsabilityResult.verdict` = VERDICT_UNSPECIFIED（非推奨の項目）と `inspectionResultLink`（Search Console の画面へのリンク。ログには写さない）。

- 平野さんの画面（同日）の「URL が Google に認識されていません」から変わり、検査の8分前にクロールされていた。画面のライブテスト・インデックス登録のリクエストの後か否かはここからは分からない
- `title/nihon-series/` は 2026-09-17 から `sitemap-title.xml` にあり、インデックス経由で公開（#413、2026-09-28）

(b) サイトマップの状態（全項目は `docs/gsc/inspect/2026-11-12-sitemaps.md`）:

| サイトマップ | 送信 | lastDownloaded | isPending | errors | warnings | submitted(web) |
|---|---|---|---|---|---|---|
| sitemap.xml（インデックス） | 2026-11-04 | 2026-11-09 | false | 0 | 0 | 2,398 |
| sitemap-pages.xml | 2026-09-12 | 2026-11-10 | false | 0 | 0 | 54 |
| sitemap-wayhome.xml | 2026-09-12 | 2026-11-07 | false | 0 | 0 | 45 |
| sitemap-saikyo.xml | 2026-10-27 | 2026-11-11 | false | 0 | 0 | 892 |
| sitemap-title.xml | （単独では未送信） | 2026-11-07 | false | 0 | 0 | 385 |
| sitemap-live.xml | （単独では未送信） | 2026-11-09 | false | 0 | 0 | 947 |
| sitemap-mtsuku.xml | （単独では未送信） | 2026-11-04 | false | 0 | 0 | 62 |
| sitemap-exam.xml | （単独では未送信） | 2026-11-08 | false | 0 | 0 | 13 |

- `sitemaps.list` は送信済みの4件（平野さんの画面と一致）。インデックス経由の4件も `sitemaps.get` で取れ、どれも Google が読んでいてエラー・警告は0。`contents[].indexed` は全件 0（公式で非推奨の項目）
- → title/ のサイトマップは読まれている。「サイトマップに載っているのに認識されない」はサイトマップの読み込みの失敗ではない

### 4. 所要時間の見込み（Actions の使用量、#298）

- この実行のジョブは 51秒（準備・テスト 40秒、URL 検査の手順 10秒〈サイトマップ 9件の取得を含む〉）。検査1件は 6.43秒（初回の接続を含む1件だけの値）
- 1回 1,900件では、1件 1〜6.4秒として 約32〜205分。月次の 2,398件では 約40〜260分。1件あたりの実際の値は全件の取得の初回で測る

## 報告

- 状態: 対応中
- ブランチ: work/1112-seo19
- ログ: https://github.com/retroeater/mj/blob/work/1112-seo19/docs/logs/CHAT-1112-SEO-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-seo19
- 確認用URL: なし
- マージ: 未
- issue: #458, #680
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 176104e8）: https://github.com/retroeater/mj-logs/tree/main/guide/176104e8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/176104e8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fde93af5.md
