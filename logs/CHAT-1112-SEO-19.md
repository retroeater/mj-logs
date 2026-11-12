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
