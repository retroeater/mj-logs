# サイトのトップの新しい形 top/（#101）

サイトのトップ `index.html` を「サイトの入口」として作り直す（2026-11-03 の決定、`docs/decisions/top.md`）。
未公開の間は `top/`（URL は `/top/`、noindex・メニュー未掲載）に生成し、`r/` の公開（#656）と pro/ の公開（#641）の後の切り替えの指示で出力先を `index.html` に変える。

## 構成

| 項目 | 所在 |
|---|---|
| 生成 | `scripts/generate_top_page.py`（ページ名 `top_page`。`regenerate.py` の `OUTPUT_OVERRIDES` で `top/`、`RUN_LAST` で pro/ の後） |
| JS | `assets/top.js`（日付で変わる行の出し分けと3つの面の「もっと見る」、X の画像が読めないときの頭の1字）、`assets/top_ask.js`（AIコンシェルジュの横断の入力欄。下の「上部」） |
| CSS | `assets/houou_ask.css`（質問欄）と `assets/top.css`（一行の説明・サムネイルの無い行の箱・誕生日の日付と写真）。ほかは houou/・pro/ の部品（`.mj-houou-card`・`.mj-pro-plans`・`.mj-pro-heading`）をそのまま使う |
| 配信 | `_redirects` の `/top` → `/top/`（301）・`/top/` → `/top/index.html`（200）、`check-assets.yml` の `allowed()` の `top` |
| ナビ | `navbar.js` の白地のナビの対象に `/top/`（`newNavCurrent = 'トップ'`。どの項目にも当たらないので印は付かない） |
| 毎朝 | `update-live-channel.yml` の `regenerate` ジョブの `target_page` に `top_page`（pro/ の後） |
| 検査 | `scripts/tests/test_top_page.py` |

## 面（2026-11-03 の決定の並び）

1. 上部 — ロゴ（h1「ryoei.pro」）→ 一行の説明 → AIコンシェルジュの入力欄（中に例「例：現鳳凰位は？」。下の「上部」）
2. お気に入りの今後の予定 — 差し込み口 `data-top-slot="favorites"` だけ（中身は後の指示。JS が実行時に pro/ 側の予定の JSON を読み、localStorage のお気に入りで絞って5件と「ほかN件」。0件の人には「pro/ の個別ページの ★ でお気に入りに登録できます」の1行）
3. 開催中・直近の大会（#231） — 下の「大会日程の判定」
4. 誕生日（#286） — 下の「誕生日」
5. 新着の番組 — 下の「新着の番組」
6. ページ末尾の説明文（`lib/page.py` の `.mj-lead`）

「各セクション」の導線の面は 2026-11-03 に消した（ナビゲーションは上の共通ナビに任せる、TOP-07）。

題は「日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro」、h1 は「ryoei.pro」、JSON-LD は `WebSite`（今の index.html と同じ）。

## 上部（AIコンシェルジュの入力欄）

- 一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」（`LEAD`）、入力欄の中の例「例：現鳳凰位は？」（`ASK_PLACEHOLDER`。大会別の欄の `ask_placeholder`〈「例：23期生の鳳凰位は？」、1行の入力欄に収まる短さ〉と同じ形。2026-11-03 の決定、TOP-09）
- 入力欄の HTML は大会別の欄と同じ型（`generate_houou_pages.ASK_TEMPLATE`、見た目は `assets/houou_ask.css`）。`data-contest="all"` で、`data-players-url` は空（大会の名簿を読まない）
- 送るのは `assets/top_ask.js`（`assets/houou_ask.js` は読まない）。要求は `{question, contest: "all", context: null}`（Worker の横断、docs/notes/ask-worker.md「横断」）。答えは大会ごとに `【大会名】` で始まる行の改行つなぎで、`.mj-houou-ask-answer` の `white-space: pre-line` で改行される。選手のカードは応答の `players[{name, contest, url}]` から作る（頭文字の丸・大会のチップ・名前。行き先は `url`、サイトの中の URL だけ。写真は出さない、`簡略化:`）。大会のチップの名前はページに焼いた `#top_ask_contests`（`ASK_CONTESTS`）
- 入力欄の下は大会別の欄と同じく注意書き（「AI の回答には…」と ⓘ）だけ。TOP-07 の質問の例のボタン（3つ）は 2026-11-03 にやめた（TOP-09）
- `top_ask.js` は `houou_ask.js` と同じ流れを写している（`簡略化:`。大会別の欄を同じ部品にするときに1つにまとめる）
- `ALLOWED_ORIGIN` が本番だけなので、プレビューでは答えを確かめられない。画面は応答の見本（Playwright で `/api/ask` を差し替え）で確かめる

## 大会日程の判定

- 元: 放送対局は `sync_live_calendar.load_desired()`（公開カレンダー「mj_放送対局」と同じ予定。シートを読む）、最強戦は `saikyo/players/` の対局前の卓（pro/ の `load_saikyo_plans()`）
- 大会: `yotei.event_of()` の大会名が `title/search.json` の `t` の大会名と一致するか、title/ の名前がその大会名で終わる（「小島武夫杯帝王戦」と「帝王戦」）。title/ に無い大会（鳳匠戦・JPMLリーグ・プロNo.1決定戦・地方プロリーグなど）は載せない
- 段（`is_late_stage()`）: ベストN があれば N ≤ 16。無ければ「決定戦」を含むか、「決勝」を含み「決勝トーナメント」でない。それ以外（第N節・予選・プレーオフ・入れ替え戦・段の語の無い件名）は載せない側に倒す
- 最強戦: 対局名が「ファイナル」で始まる卓（1st Stage〈ベスト16〉・2nd Stage〈ベスト8〉・ファイナルトーナメント〈決勝〉）。予選の大会の「ベスト8」「決勝」は載せない
- 行: 左にサムネイル（YouTube の枠は pro/ の放送対局の行と同じ `img.youtube.com/vi/<動画ID>/mqdefault.jpg`、96×54。枠の無い予定表の予定・最強戦は同じ大きさの灰色の箱に大会名の頭の文字）、pro/ の予定の行の並び（日付・大会のチップ・件名）。行き先は YouTube の枠、枠のまだ無い予定表の予定は title/ の大会のページ、最強戦は saikyo/ の卓のページ
- 今日以降の行を全部焼き、先頭5件（`SCHEDULE_FIRST`）だけ見せる。「もっと見る」を押すごとに10件（`SCHEDULE_STEP`）ずつ開き、全部出したらボタンを消す（2026-11-03 の決定）。`assets/top.js` が日本時間の今日より前の行を隠して数え直す。1件も無ければ面を出さない

## 新着の番組

帰り道・プロテスト・Mつくの3番組を横断し、公開日時の新しい順に12件（`NEW_EPISODES_MAX`）を焼き、最初は3件（`NEW_EPISODES_FIRST`）。「もっと見る」で12件まで開く（2026-11-03 の決定）。女流勉強会（`study/`、未公開 #576）は含めない。

| 番組 | 元（各番組の生成と同じ） | 公開日時 | 題 |
|---|---|---|---|
| 帰り道 | 「帰り道」シート（`wayhome.fetch_rows()`）と `data/youtube_meta.json`（`wayhome.collect_episodes()`） | YouTube API の `publishedAt` | 題と名前（シートの「タイトル」「名前」） |
| プロテスト | 「プロテスト」シート（`exam.fetch_tab_rows()`）と /live の層1（`exam.join_layer1()`・`study.load_layer1()`） | 層1の配信開始日時（無ければ公開日時） | `exam.heading()`（「タイトル（補正分）」があればそれ）。出演者の名前は足さない（`簡略化:`） |
| Mつく | 「Mつく」シート（`mtsuku.fetch_rows()`） | シートの「公開日」の 00:00 JST | 「チーム名」と作る人（シートの名前のまま、`簡略化:`） |

- 番組の元が読めない（シート・JSON・層1・Mつくの公開日の書式など）ときは、警告してその番組だけ外し、トップの生成は止めない（大会日程・誕生日の毎朝の更新を止めないため。pro/ の道場部ゲストと同じ扱い）
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

- 3つの面で同じ形のボタン（答えの選手の「ほか N名」と同じ `.mj-houou-ask-more`）。データは焼いてあり、押すとその場で行を開く。開いた件数を `#top_status`（`aria-live`）で読み上げる。全部出したらボタンを消し、開いた最初の行へフォーカスを移す

## 未公開の段の扱い

- 切り替えの指示でやること: `OUTPUT_DIR`・`ASSET_PREFIX`・`PAGE_URL` を `index.html` 向けにし、`NOINDEX_TAG` を外す。`OUTPUT_OVERRIDES` を `index.html` に、`_redirects` の `/top` の2行・`check-assets.yml` の `top`・`navbar.js` の `/top/` の分岐を消す（`/` は白地のナビの対象に）。`_redirects` 先頭の `/  /index.html  200` は消さない
- 切り替えの後に直す記述: docs/handover.md「新サイトの第一弾は index.html（#101）」、docs/new-site-design.md「最初に作るのは index.html」、docs/notes/static-generation.md の `index.html` の行、`scripts/lib/page.py` の「index.html以外はbootstrap.min.cssを読む」のコメント、`scripts/tests/test_breadcrumb_json_ld.py` の `test_top_has_website`（新トップも `WebSite` を出す）
- 片付け（切り替えの後の指示）: `index.css`・`index.js`・`assets/vendor` のテンプレート用ライブラリ
