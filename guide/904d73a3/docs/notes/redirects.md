# 転送（リダイレクト）の一覧と四半期の棚卸し

ryoei.pro のすべての転送の一覧と、四半期ごとの棚卸しの手順。常設の issue は #568（棚卸しの記録はそのコメント）。

- 「棚卸しの対象」「別名」の表と `_redirects` の一致は `scripts/tests/test_redirects_inventory.py` が確かめる（表の1・2列目と、`_redirects` の 200 でも末尾スラッシュ付けでもなく `# live: generated` 〜 `# live: end` の外の行）。`_redirects` に旧 URL の 301 を足す・消すときは、同じコミットで表の行も足す・消す
- テストで拾えない転送（ダッシュボードのルール・JS の移動）は、足したときに下の表へ手で書く（docs/new-page-checklist.md の旧ページの項目）

## 常設（棚卸しの対象外）

| 種類 | 仕組み | 範囲 | 記録 |
|---|---|---|---|
| www → apex（308、クエリを保つ） | Cloudflare の Redirect Rule「www to apex」 | `www.ryoei.pro` の全パス | #115、docs/notes/cloudflare.md「www→apexのRedirect Rule」 |
| http → https | Always Use HTTPS | 全パス | docs/notes/cloudflare.md |
| 末尾スラッシュを付ける 301 | `_redirects` | `/saikyo`・`/title`・`/live`・`/books`・`/houou`・`/wayhome` と `/title/<大会>`（20行。`test_title_redirects.py` が検査） | #455 |
| `/live` の URL 変更の 301 | `_redirects` の `# live: generated` 〜 `# live: end`（`scripts/generate_live_pages.py` が書く） | 2026-10-14 時点で `/live/teiou/3/b16.html`・`/live/teiou/4/b16.html` → 同じ期の `f.html` | docs/notes/live-page-design.md「3-4」。生成物なので行ごとに照らし合わせない。当面は消さない |
| 内部の書き換え（200） | `_redirects` | 11行（`/`、各ディレクトリの `index.html`、`/title/:slug/` など） | docs/notes/cloudflare.md「配信設定」。転送ではないので棚卸ししない |

ダッシュボードのルール: Redirect Rules は「www to apex」1本（Order 1、Active、308）。Bulk Redirects のリストは 0（0 out of 5）。Rules の Overview のほかの種類（URL Rewrite・Configuration・Origin・Request/Response Header Transform・Cache・Cache Response・Custom Error・Compression・Trace）はいずれも 0。Page Rules の欄は Overview に無かった。
2026-10-14 平野さんの画面で確認（申告値）。セッションからは検証できない。

## 棚卸しの対象

旧 URL の 301。四半期の棚卸しで「削除の基準」に当たれば行を消す（消すと 404）。

| 転送元 | 転送先 | 仕組み | 入れた日 | issue | 最後の棚卸し |
|---|---|---|---|---|---|
| `/saikyo_results.html` | `/saikyo/` | `_redirects` 301 | 2026-09-21 | #350・#434（#568 に統合） | 未（初回 2027-01 上旬） |
| `/jpml_titles.html` | `/title/` | `_redirects` 301 | 2026-09-30 | #441・#485 | 未（初回 2027-01 上旬） |
| `/video_wayhome.html` | `/wayhome/` | `_redirects` 301 | 2026-10-13 | #559 | 未（初回 2027-01 上旬） |
| `/tanilog.html` | `/resource_logs.html?name=谷岡育夫` | `_redirects` 301 | 2026-09-07 | GitHub Pages からの移行 | 未（初回 2027-01 上旬） |

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
| `saikyo/` の年度ページの `#<卓のアンカー>`・`#match-…` | 新しい年度・対局・卓のページ | `assets/saikyo.js`（対応表は `#saikyo_anchor_map`、`generate_saikyo_pages.py` の `anchor_map_json()`） | #425 |
| `houou/ranking/` の `?division=` | その部門のページ | `assets/houou.js` | #518 |
| `houou/leagues/` | `houou/players/?view=league`（`?name=` を保つ） | `assets/houou.js`（2026-10-12） | #518 |

## 予定（公開時に増える）

| 転送元 | 予定 | issue |
|---|---|---|
| 鳳凰戦の旧4ページ | `houou/` の各ページへの 301 | #540 |
| 最強戦の年度ページ・`?match=` | 年度ページの 301 と、`?match=` の Single Redirect ルール（ダッシュボード。テストで拾えない） | #549 |
| `/houou/ranking/` | `/houou/ranking/total/` への 301（未マージの work/1014-hou にある。マージ時に「棚卸しの対象」か「常設」へ1行足さないとテストが落ちる） | #518 |

## 棚卸しの手順（四半期ごと、1・4・7・10月の上旬）

月次の Search Console の取得（`fetch-gsc.yml`、毎月1日）と流入の集計の後に行う。

1. 「棚卸しの対象」の各行について、直近90日の流入を見る
   - 転送の件数（ボット以外）: 月次の集計（下の「流入の測り方」。次の指示で作る）の直近3か月分の合計
   - Search Console の着地: `docs/gsc/<取得日>/page.csv` の転送元 URL（クエリ付きの変種を含む）の表示・クリック。ページ一覧から消えているかも見る
2. **削除の基準: 直近90日で、ボット以外の転送が0件、かつ Search Console の着地も0件**（2026-10-14 の決定）。当たる行は `_redirects` と上の表から消す（同じコミット）。消すと SNS などの古いリンクが 404 になることは基準の中で受け入れている
3. 当たらない行は「最後の棚卸し」に日付と件数の要約を書く
4. 「別名」「JS の移動」「予定」の行が今も正しいか（navbar のリンク・JS・issue の状態）を見直す
5. 結果を #568 にコメントし、次回の日付をカレンダー「ryoei.pro」に入れる（チャット側）

## 流入の測り方（下調べ、2026-10-14）

公式文書（developers.cloudflare.com）と cloudflare の skill の参照文書だけで調べた。トークンは作らず、API も呼んでいない。

### (a) データセットと期間

- **データセット: GraphQL Analytics API の `httpRequestsAdaptiveGroups`（ゾーン単位）。** 次元 `clientRequestPath`（パス）と `edgeResponseStatus`（エッジが返した状態コード）で絞り・まとめられ、`count` で件数が取れる（skill の参照 `references/graphql-api/api.md`）。公式のチュートリアル「Querying HTTP events by hostname」も同じデータセットを `clientRequestPath` でまとめている
- **さかのぼれる期間: Adaptive のデータセットは「どのプランでも少なくとも31日」保持する**（「GraphQL Analytics API — Limits」）。Pro での正確な値（`notOlderThan`）と1回の問い合わせの幅（`maxDuration`）は文書に表が無く、**未確認**。トークンを作った後に `settings { httpRequestsAdaptiveGroups { notOlderThan maxDuration maxPageSize availableFields } }` で確かめる（「Settings」）
- このため、90日を一度に取ることは前提にしない。**月1回、前月分（31日以内）を取って issue に記録し、棚卸しでは直近3か月分を足す。** 1回の幅が31日より狭ければ、日ごとに分けて問い合わせる

### (b) ボットを除けるか

- Pro は Super Bot Fight Mode の範囲で、「likely automated」の判定は Pro では使えない。Bot Analytics は Business 以上（「Bots — Pro」「Bot Analytics」）。**Pro で `botScore` などのボットの次元が GraphQL で使えるかは未確認**（`availableFields` で確かめる）。使えない前提で設計する
- 代わり: **User-Agent でボットらしいもの（`bot`・`crawler`・`spider` などを含むもの）を除いた件数**を別に数える。`httpRequestsAdaptiveGroups` に User-Agent の次元があるかは文書の範囲では**未確認**（`availableFields` で確かめる）。無ければ、総数だけを記録し、0件かどうかの判断に使う（総数が0ならボット以外も0）
- `requestSource: "eyeball"` で絞ると、Cloudflare 内部（Worker のサブリクエストなど）の要求が除ける（チュートリアルの例）。ボットを除くものではない

### (c) `_redirects` の 301 がゾーンの集計に出るか

- Workers の静的アセットの `_redirects` は「静的アセットの応答に適用される」（「Workers — Redirects」）。ゾーンの HTTP の集計にどう出るかは文書に記述が無く、**未確認**
- 確かめ方: 次の指示の最初の手動実行で、`/saikyo_results.html` などの転送元のパスを `edgeResponseStatus` でまとめ、301 として出るかを見る。出なければ方式を考え直す（判断が必要なこととして報告する）

### (d) トークンの権限と Secret の名前

- 要る権限: **アナリティクスの読み取りだけ。** 公式文書は Account > Account Analytics > Read の例を示し（「Configure an Analytics API token」）、skill の参照はゾーンの HTTP には Zone > Analytics > Read を挙げる。Zone Resources を ryoei.pro の1ゾーンに絞る。どちらで足りるかはトークンを作るときに確かめる（**未確認**）
- Secret の名前の案: **`CF_ANALYTICS_TOKEN`**。CLAUDE.md「禁止事項」の `CLOUDFLARE_API_TOKEN` は、wrangler が読む名前で、`wrangler deploy` による二重デプロイを防ぐための禁止。別の名前・読み取りだけの権限なら wrangler に拾われず、デプロイもできないため、禁止事項の趣旨に触れない（登録は平野さんがダッシュボードで行う）

### (e) 月次のジョブの置き場（#564 との関係）

- 新しいワークフロー `fetch-redirect-counts.yml`（#564 の命名 `<動詞>-<対象>.yml`）を1本作り、毎朝の運行（`daily.yml`）には入れない。前月分を取るので月初に1回でよく、Search Console の取得と同じ月次の組に入る
- 起動は当面 GitHub の `schedule`（毎月1日、`fetch-gsc.yml` の後）と `workflow_dispatch`。#564 の段④（週次・月次を `workers/scheduler/schedule.json` へ移す）で `fetch-gsc.yml` と一緒に移す。共通部品（段①の setup-python・upsert-issue）ができていればそれを使う
