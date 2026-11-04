# サイトのトップの新しい形 top/（#101）

サイトのトップ `index.html` を「サイトの入口」として作り直す（2026-11-03 の決定、`docs/decisions/top.md`）。
未公開の間は `top/`（URL は `/top/`、noindex・メニュー未掲載）に生成し、`r/` の公開（#656）と pro/ の公開（#641）の後の切り替えの指示で出力先を `index.html` に変える。

## 構成

| 項目 | 所在 |
|---|---|
| 生成 | `scripts/generate_top_page.py`（ページ名 `top_page`。`regenerate.py` の `OUTPUT_OVERRIDES` で `top/`、`RUN_LAST` で pro/ の後） |
| JS | `assets/top.js`（日付で変わる行の出し分けと新着の番組・誕生日の「もっと見る」、X の画像が読めないときの頭の1字、お気に入りの予定） |
| データ | `top/birthdays.json`（お気に入りの予定が読む在籍者の誕生日 `[[slug, 名前, "MM-DD", X の画像]]`。年は書かない。画像は誕生日の面と同じ `pro/search.json` の写真。生成が毎回書く。公開は 2026-11-04 の決定）、`pro/plans.json`（pro/ の生成物。PRP 系列の担当） |
| CSS | `assets/top.css`（誕生日の日付と写真・新着の番組の行の折り返し・お気に入りの予定の今日の行と名前のチップ）。ほかは houou/・pro/ の部品（`.mj-houou-card`・`.mj-pro-plans`・`.mj-pro-heading`）と、`style.css` の「質問欄の部品」の「もっと見る」の形（`.mj-houou-ask-more`）をそのまま使う |
| 配信 | `_redirects` の `/top` → `/top/`（301）・`/top/` → `/top/index.html`（200）、`check-assets.yml` の `allowed()` の `top` |
| ナビ | `navbar.js` の白地のナビの対象に `/top/`（`newNavCurrent = 'トップ'`。どの項目にも当たらないので印は付かない）。新しい形のページの共通ナビ（細い帯・≡ のメニュー・AI 検索のアイコンで帯の下に開く質問欄）は docs/notes/design.md「共通ナビ（新しい形）」 |
| 毎朝 | `update-live-channel.yml` の `regenerate` ジョブの `target_page` に `top_page`（pro/ の後） |
| 検査 | `scripts/tests/test_top_page.py` |

## 面（2026-11-03〜04 の決定の並び）

1. 上部 — ロゴ（h1「ryoei.pro」）だけ。AIコンシェルジュの入力欄は 2026-11-04 にやめた（入口は共通ナビの AI 検索のアイコン。下の「質問」）
2. お気に入りの予定 — 下の「お気に入りの予定」
3. 誕生日（#286） — 下の「誕生日」
4. 新着の番組 — 下の「新着の番組」
5. ページ末尾の説明文（`lib/page.py` の `.mj-lead`）

「各セクション」の導線の面は 2026-11-03 に消した（ナビゲーションは上の共通ナビに任せる、TOP-07）。「開催中・直近の大会」の面と一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」も 2026-11-03 にやめた（TOP-11）。

題は「日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro」、h1 は「ryoei.pro」、JSON-LD は `WebSite`（今の index.html と同じ）。

## お気に入りの予定（2026-11-04 の決定）

端末の「お気に入り」（pro/ の個別ページの ★、localStorage `mj:favorites`〈`[{slug, name}]`〉）の選手の今後の予定と誕生日を、日付の近い順に混ぜて1行1件で出す（`assets/top.js` の `favoriteRows()`）。お気に入りの中身は URL に載せない。

- 生成（`favorites_panel()`）は見出し `#top_favorites` と面 `[data-top-favorites]` の枠だけを hidden で焼き、`top/birthdays.json`（`birthday_index()`）を書く。中身は JS が描く
- 予定: `pro/plans.json` のうち、お気に入りの slug で、日付が日本時間の今日以降の行（`assets/pro.js` の `isPastPlan()` と同じ基準。今日の行は時刻が過ぎても残る）。誕生日: `top/birthdays.json` から、今日から30日後（`FAVORITES_DAYS`、その日を含む）までに来る日。2/29 生まれは閏年の 2/29 だけ（`簡略化:`）
- 並び: 日付の順。同じ日は誕生日 → 時刻の無い予定（最強戦・道場部）→ 時刻の順
- 行: 誕生日・新着の番組の行と同じく行全体が1つのリンク（入れ子にしない）。左に顔写真（誕生日の面と同じ 40px の丸。`top/birthdays.json` の画像。無い・読めないときは頭の1字の丸。誕生日の無い在籍者は JSON に入らないので頭の1字、`簡略化:`）、1段目に日付（今日・明日はその語）と時刻・種類のチップ（放送・最強戦・道場部・誕生日）・役（実況・解説）、2段目に題、3段目に選手名（リンクにしない）、右に ›。行き先は予定の `href`（https の外部は新しいタブで、行の最後に読み上げ用の「（新しいタブで開く）」を必ず添える。`rowLink()`）、誕生日の行は個別ページ（題が「〇〇さんの誕生日」で選手名の段は無い）。今日の行は日付を紺の太字
- 名前は `plans.json`・`top/birthdays.json` の現在名（改名に追従）。どちらにも無い slug は `mj:favorites` の名前
- 件数: 最初は5件（`FAVORITES_FIRST`）、「もっと見る」で残りを全部（新着の番組と同じ `setupList()`）
- お気に入りが0人: 面を出し、案内「選手のページの ☆ で「お気に入り」に入れると、ここに放送対局や誕生日が出ます」と連盟プロ（`/pro/`）へのリンク
- お気に入りはいるが行が無い: 「お気に入りの選手の予定はまだありません」と選手名のチップ（個別ページへ）
- localStorage が使えない・`plans.json` が読めない: 面を出さない。`top/birthdays.json` だけ読めないときは誕生日の行を出さない（`簡略化:`）
- リンクにするのは https の URL とルート相対のパスだけ（`safeHref()`。`//`・`/\` で始まるものは外す）。slug は英小文字・数字・`.`・`-` の形だけ使う
- 検査は `scripts/tests/test_top_page.py` の `FavoritesPanelTest`・`FavoritesJsTest`（並び・30日の境目・今日と明日の語・年またぎ・改名・リンク・外部の行の「新しいタブで開く」・顔写真・0人・壊れた値・`assets/pro.js` の `parseFavorites()` と同じ読み方）。件数と「もっと見る」、localStorage が使えないときは画面で確かめた（#101）

## 質問（AIコンシェルジュ）

ページの中の質問欄は全ページで 2026-11-04 にやめ、入口を共通ナビの AI 検索のアイコン1つにした（docs/notes/design.md「共通ナビ（新しい形）」）。
トップで開くと例は「例：現鳳凰位は？」（欄の幅は本文の面と同じ）、要求は `{question, contest: "all", context: null}`（Worker の横断、docs/notes/ask-worker.md「横断」）。
TOP-07〜09 の上部の入力欄（`ASK_TEMPLATE` の型・`#top_ask_contests`）は外した。

## 新着の番組

帰り道・プロテスト・Mつくの3番組を横断し、公開日時の新しい順に12件（`NEW_EPISODES_MAX`）を焼き、最初は3件（`NEW_EPISODES_FIRST`）。「もっと見る」で12件まで開く（2026-11-03 の決定）。女流勉強会（`study/`、未公開 #576）は含めない。

| 番組 | 元（各番組の生成と同じ） | 公開日時 | 題 |
|---|---|---|---|
| 帰り道 | 「帰り道」シート（`wayhome.fetch_rows()`）と `data/youtube_meta.json`（`wayhome.collect_episodes()`） | YouTube API の `publishedAt` | 題と名前（シートの「タイトル」「名前」） |
| プロテスト | 「プロテスト」シート（`exam.fetch_tab_rows()`）と /live の層1（`exam.join_layer1()`・`study.load_layer1()`） | 層1の配信開始日時（無ければ公開日時） | `exam.heading()`（「タイトル（補正分）」があればそれ）。出演者の名前は足さない（`簡略化:`） |
| Mつく | 「Mつく」シート（`mtsuku.fetch_rows()`） | シートの「公開日」の 00:00 JST | 「チーム名」と作る人（シートの名前のまま、`簡略化:`） |

- 番組の元が読めない（シート・JSON・層1・Mつくの公開日の書式など）ときは、警告してその番組だけ外し、トップの生成は止めない（誕生日の毎朝の更新を止めないため。pro/ の道場部ゲストと同じ扱い）
- 公開前（生成の時刻より後）の回と、**各話のページ（`wayhome/<動画ID>.html` など）がまだリポジトリに無い回は出さない**。各話のページは週次の `all`（`fetch-youtube.yml`）などで作られ、トップは毎朝（`update-live-channel.yml`）作り直すので、ページより先にトップに出てリンク切れになるのを防ぐ
- 鮮度: 帰り道の公開日時（`data/youtube_meta.json`）は週次の取得（`fetch-youtube.yml`）、プロテストの層1は毎朝の `update-live-channel.yml` の手順1（トップの生成と同じ実行の前）で更新される。シートはトップの生成のたびに読む。トップに出るのは、各話のページができた翌朝から
- 行: 左にサムネイル（各話のページの動画ID の `mqdefault`、各番組の一覧のカードと同じ）、pro/ の予定の行と同じ並び（1段目に公開日「2026年8月27日」〈帰り道のカードと同じ書き方〉と番組のチップ、2段目に題）。行き先は各話のページ（サイトの中、新しいタブにしない）。1件も無ければ面を出さない

## 誕生日

- `lib/birthdays.py` の一覧（「プロ」の表示 Y × 名簿）。今日の1日前（`BIRTHDAY_BEFORE`）から8日後（`BIRTHDAY_AFTER`）までの日付ごとに、日付の見出し（`data-day-label`）と行（`data-day`）を焼く（生成が1日止まっても今日と7日分が出る範囲、仮置き）
- 見出しは「誕生日」で、横に今日の日付「11月3日（火）」（`assets/top.js` が日本時間の今日で書き直す）
- 最初は今日の人だけ。今日が0人の日は「今日の誕生日の選手はいません」の1行。「もっと見る」で明日から7日分（`BIRTHDAY_MORE_DAYS`）を日付の見出しごとに開く（開く分が無ければボタンを出さない）。焼く範囲に1人もいなければ面を出さない
- 行: X のプロフィール画像（`pro/search.json` の写真の列、pro/ の一覧と同じ 40px の丸。無い選手は頭の1字、読めない画像は JS が頭の1字に差し替える）、名前、2行目に所属・入会期。名前は pro/ の個別ページへ
- 2/29 生まれは閏年の 2/29 にだけ出る（`簡略化:`）

## 「もっと見る」

- 新着の番組と誕生日で同じ形のボタン（答えの選手の「ほか N名」と同じ `.mj-houou-ask-more`）。データは焼いてあり、押すとその場で行を開く。開いた件数を `#top_status`（`aria-live`）で読み上げる。全部出したらボタンを消し、開いた最初の行へフォーカスを移す

## 未公開の段の扱い

- 切り替えの指示でやること: `OUTPUT_DIR`・`ASSET_PREFIX`・`PAGE_URL` を `index.html` 向けにし、`NOINDEX_TAG` を外す。`OUTPUT_OVERRIDES` を `index.html` に、`_redirects` の `/top` の2行・`check-assets.yml` の `top`・`navbar.js` の `/top/` の分岐を消す（`/` は白地のナビの対象に）。`_redirects` 先頭の `/  /index.html  200` は消さない
- 切り替えの後に直す記述: docs/handover.md「新サイトの第一弾は index.html（#101）」、docs/new-site-design.md「最初に作るのは index.html」、docs/notes/static-generation.md の `index.html` の行、`scripts/lib/page.py` の「index.html以外はbootstrap.min.cssを読む」のコメント、`scripts/tests/test_breadcrumb_json_ld.py` の `test_top_has_website`（新トップも `WebSite` を出す）
- 片付け（切り替えの後の指示）: `index.css`・`index.js`・`assets/vendor` のテンプレート用ライブラリ
