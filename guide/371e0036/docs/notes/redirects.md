# 転送（リダイレクト）の一覧と四半期の棚卸し

ryoei.pro のすべての転送の一覧と、四半期ごとの棚卸しの手順。常設の issue は #568（棚卸しの記録はそのコメント）。

- 「棚卸しの対象」「別名」の表と `_redirects` の一致は `scripts/tests/test_redirects_inventory.py` が確かめる（表の1・2列目と、`_redirects` の 200 でも末尾スラッシュ付けでもなく `# live: generated` 〜 `# live: end` の外の行）。`_redirects` に旧 URL の 301 を足す・消すときは、同じコミットで表の行も足す・消す
- テストで拾えない転送（ダッシュボードのルール・JS の移動）は、足したときに下の表へ手で書く（docs/new-page-checklist.md の旧ページの項目）

## 常設（棚卸しの対象外）

| 種類 | 仕組み | 範囲 | 記録 |
|---|---|---|---|
| www → apex（308、クエリを保つ） | Cloudflare の Redirect Rule「www to apex」 | `www.ryoei.pro` の全パス | #115、docs/notes/cloudflare.md「www→apexのRedirect Rule」 |
| http → https | Always Use HTTPS | 全パス | docs/notes/cloudflare.md |
| 末尾スラッシュを付ける 301 | `_redirects` | `/saikyo`・`/title`・`/live`・`/books`・`/houou`・`/wayhome`・`/mtsuku` と `/title/<大会>`（20行。`test_title_redirects.py` が検査）、`/saikyo/<年度>`（16行。`test_saikyo_redirects.py` が検査） | #455・#549 |
| `/live` の URL 変更の 301 | `_redirects` の `# live: generated` 〜 `# live: end`（`scripts/generate_live_pages.py` が書く） | 2026-10-14 時点で `/live/teiou/3/b16.html`・`/live/teiou/4/b16.html` → 同じ期の `f.html` | docs/notes/live-page-design.md「3-4」。生成物なので行ごとに照らし合わせない。当面は消さない |
| 内部の書き換え（200） | `_redirects` | 28行（`/`、各ディレクトリの `index.html`、`/saikyo/<年度>/`〈16行〉、`/title/:slug/` など） | docs/notes/cloudflare.md「配信設定」。転送ではないので棚卸ししない |

ダッシュボードのルール: Redirect Rules は「www to apex」1本（Order 1、Active、308）。Bulk Redirects のリストは 0（0 out of 5）。Rules の Overview のほかの種類（URL Rewrite・Configuration・Origin・Request/Response Header Transform・Cache・Cache Response・Custom Error・Compression・Trace）はいずれも 0。Page Rules の欄は Overview に無かった。
2026-10-14 平野さんの画面で確認（申告値）。セッションからは検証できない。

## 棚卸しの対象

旧 URL の 301。四半期の棚卸しで「削除の基準」に当たれば行を消す（消すと 404）。

| 転送元 | 転送先 | 仕組み | 入れた日 | issue | 最後の棚卸し |
|---|---|---|---|---|---|
| `/saikyo_results.html` | `/saikyo/` | `_redirects` 301 | 2026-09-21 | #350・#434（#568 に統合） | 未（初回 2027-01 上旬） |
| `/jpml_titles.html` | `/title/` | `_redirects` 301 | 2026-09-30 | #441・#485 | 未（初回 2027-01 上旬） |
| `/video_wayhome.html` | `/wayhome/` | `_redirects` 301 | 2026-10-13 | #559 | 未（初回 2027-01 上旬） |
| `/video_mtsuku.html` | `/mtsuku/` | `_redirects` 301 | 2026-10-15 | #570 | 未（初回 2027-01 上旬） |
| `/tanilog.html` | `/resource_logs.html?name=谷岡育夫` | `_redirects` 301 | 2026-09-07 | GitHub Pages からの移行 | 未（初回 2027-01 上旬） |
| `/houou/ranking/` | `/houou/ranking/total/` | `_redirects` 301（クエリを引き継ぐ。`?division=` は下の「JS の移動」） | 2026-10-14 | #518・#540（通算得点を `total/` に移した開発中の入口） | 未（初回 2027-01 上旬） |
| `/saikyo/2011.html` | `/saikyo/2011/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2012.html` | `/saikyo/2012/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2013.html` | `/saikyo/2013/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2014.html` | `/saikyo/2014/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2015.html` | `/saikyo/2015/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2016.html` | `/saikyo/2016/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2017.html` | `/saikyo/2017/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2018.html` | `/saikyo/2018/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2019.html` | `/saikyo/2019/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2020.html` | `/saikyo/2020/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2021.html` | `/saikyo/2021/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2022.html` | `/saikyo/2022/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2023.html` | `/saikyo/2023/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2024.html` | `/saikyo/2024/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2025.html` | `/saikyo/2025/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |
| `/saikyo/2026.html` | `/saikyo/2026/` | `_redirects` 301 | #549 の公開の日 | #549 | 未（初回 2027-01 上旬） |

`/jpml_titles.html` の行を消すときは `assets/title.js` の `?name=` の受け取りも消す（#485 の「終えるときの作業」）。

## 別名（流入に関係なく残す）

サイトのリンク（navbar など）が今も使っている転送。リンクを差し替えるまでは棚卸しの対象にしない。
`/resource_calendar.html`・`/resource_books.html` をここに置くのはチャット側の案で、平野さんの決定ではない。

| 転送元 | 転送先 | 仕組み | 入れた日 | issue | 最後の棚卸し |
|---|---|---|---|---|---|
| `/resource_calendar.html` | `https://calendar.google.com/calendar/embed?…` | `_redirects` 301 | 2026-09-07 | GitHub Pages からの移行。`navbar.js` の href | 対象外 |
| `/resource_books.html` | `https://booklog.jp/users/retroeater` | `_redirects` 301 | 2026-09-07 | GitHub Pages からの移行。`navbar.js` の href。books/ への切り替えの予定は docs/new-page-checklist.md | 対象外 |

## JS の移動（エッジで数えられない）

ページの JS が `location.replace` で移すもの。サーバには 200 で届くため Cloudflare の集計では転送として数えられない。ページの作り直しと一緒に消す。

| 移動元 | 移動先 | 場所 | issue |
|---|---|---|---|
| 旧年度ページ（`/saikyo/<年度>.html`、301 で `/saikyo/<年度>/` へ来る）の `#<卓のアンカー>`・`#match-…` | 対局・卓のページ | `assets/saikyo.js`（対応表は `#saikyo_anchor_map`、`generate_saikyo_pages.py` の `anchor_map_json()`） | #425 |
| `houou/ranking/total/` の `?division=`（`/houou/ranking/?division=` は 301 で `total/` へ来る） | その部門のページ | `assets/houou.js` | #518 |
| `houou/leagues/` | `houou/players/?view=league`（`?name=` を保つ） | `assets/houou.js`（2026-10-12） | #518 |

## 予定（公開時に増える）

| 転送元 | 予定 | issue |
|---|---|---|
| 鳳凰戦の旧4ページ | `houou/` の各ページへの 301 | #540 |
| 最強戦の旧年度ページの `?match=` | `?match=` の Single Redirect ルール（ダッシュボード、#549 の公開の直後に平野さんが作る。テストで拾えない） | #549 |

## 棚卸しの手順（四半期ごと、1・4・7・10月の上旬）

月次の Search Console の取得（`fetch-gsc.yml`、毎月1日）と流入の集計の後に行う。

1. 「棚卸しの対象」の各行について、直近90日の流入を見る
   - 転送の件数（ボット以外）: #568 の月次のコメント（下の「流入の測り方」）の直近3か月の合計
   - Search Console の着地: `docs/gsc/<取得日>/page.csv` の転送元 URL（クエリ付きの変種を含む）の表示・クリック。ページ一覧から消えているかも見る
2. **削除の基準: 直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件**（2026-10-14 の決定）。当たる行は `_redirects` と上の表から消す（同じコミット）。消すと SNS などの古いリンクが 404 になることは基準の中で受け入れている
3. 当たらない行は「最後の棚卸し」に日付と件数の要約を書く
4. 「別名」「JS の移動」「予定」の行が今も正しいか（navbar のリンク・JS・issue の状態）を見直す
5. 結果を #568 にコメントし、次回の日付をカレンダー「ryoei.pro」に入れる（チャット側）

## 流入の測り方

**`fetch-redirect-counts.yml`**（毎月1日 06:30 JST、`fetch-gsc.yml` の後）が、`scripts/fetch_redirect_counts.py` で「棚卸しの対象」の表の転送元（表から読む。手で二重に持たない）について前月分（JST）の件数を取り、
`docs/redirect-counts/<YYYY-MM>.csv`（列 `path`・`status`・`count`・`count_non_bot`。公開しない、`.assetsignore` の `docs`）にコミットし、#568 に前月分と直近3か月の合計の表をコメントする。
ファイル冒頭の `ENABLED` が `'true'` でないあいだは予約実行も dry-run。手動実行は入力 `dry_run`（既定は真）と `month`（YYYY-MM、空なら前月）。

- **データセット: GraphQL Analytics API の `httpRequestsAdaptiveGroups`（ゾーン単位）。** `clientRequestPath_in` で転送元に絞り、`clientRequestPath`・`edgeResponseStatus` でまとめた `count`。CSV には出た状態コードをすべて書き、#568 のコメントは 301 の件数
- **期間:** Adaptive のデータセットは「どのプランでも少なくとも31日」保持する（公式「GraphQL Analytics API — Limits」）。スクリプトは毎回 `settings` の `notOlderThan`・`maxDuration` を読み、月の始まりが保持期間より古ければ取れる所まで詰め（コメントに範囲を書く）、`maxDuration` ごとに分けて問い合わせる。31日の月は、1日 06:30 JST の実行で月の始まりの数時間が保持期間の外になりうる。90日は一度に取らず、月ごとの集計3つを足す
- **ボット:** Pro では bot score を使えない（「likely automated」は Pro では不可、Bot Analytics は Business 以上。公式「Bots — Pro」「Bot Analytics」）。代わりに `availableFields` に `userAgent` があれば、`userAgent_notlike` で `bot`・`crawl`・`spider`・`slurp`（先頭が大文字のものも）を含む User-Agent を除いた件数を `count_non_bot` に書く。無ければ空にし、総数で判断する（総数が0ならボット以外も0）
- **トークン:** 読み取りだけ（Analytics Read、ゾーン ryoei.pro だけ）。Secret 名は `CF_ANALYTICS_TOKEN`（CLAUDE.md「禁止事項」の `CLOUDFLARE_API_TOKEN` とは別の名前・別の権限。2026-10-14 の決定）。ゾーンの ID はリポジトリの変数 `CF_ZONE_ID`（秘密ではない）、無ければスクリプトが名前から引く
- **#564 との関係:** 毎朝の運行（`daily.yml`）には入れない。#564 の段④で `fetch-gsc.yml` と一緒に Worker `mj-scheduler` の起動の表へ移す

### 実測（cloudflare での手動実行、2026-10-15 JST）

run 38611255688・38611403811（dry-run）。

- **API は通る。** `settings` の `httpRequestsAdaptiveGroups`: `enabled` 真、`notOlderThan` 2,678,400 秒（31日）、`maxDuration` 2,592,000 秒（30日）、`maxPageSize` 10,000。31日の月は2回に分けて問い合わせる
- ゾーンの ID は `GET /zones?name=ryoei.pro` で引けた（変数 `CF_ZONE_ID` は未設定のまま）。`/user/tokens/verify` は 401（アカウントのトークンのため。スクリプトは続ける）
- **`_redirects` の 301 はゾーンの集計に 301 として出る。** 2026-10-01〜10-15 で `/jpml_titles.html` 301 が487件・403 が93件、`/saikyo_results.html` 301 が88件・403 が26件など（403 は Super Bot Fight Mode などの遮断と思われる。未確認）
- `availableFields` のボット・User-Agent に関わる次元: `dimensions_userAgent`・`dimensions_userAgentBrowser`・`dimensions_userAgentOS`・`dimensions_requestSource`・`dimensions_botManagementDecision`・`dimensions_verifiedBotCategory`。`userAgent_notlike` による除外は通り、2026-09（09-13 以降）で `/saikyo_results.html` は 301 が120件・ボットらしい UA を除くと11件だった。`verifiedBotCategory` も使えるが、今は使っていない
