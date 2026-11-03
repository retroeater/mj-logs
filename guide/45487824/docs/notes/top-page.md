# サイトのトップの新しい形 top/（#101）

サイトのトップ `index.html` を「サイトの入口」として作り直す（2026-11-03 の決定、`docs/decisions/top.md`）。
未公開の間は `top/`（URL は `/top/`、noindex・メニュー未掲載）に生成し、`r/` の公開（#656）と pro/ の公開（#641）の後の切り替えの指示で出力先を `index.html` に変える。

## 構成

| 項目 | 所在 |
|---|---|
| 生成 | `scripts/generate_top_page.py`（ページ名 `top_page`。`regenerate.py` の `OUTPUT_OVERRIDES` で `top/`、`RUN_LAST` で pro/・r/ の後） |
| JS | `assets/top.js`（日付で変わる行の出し分けだけ） |
| CSS | 追加なし。houou/・pro/ の部品（`.mj-houou-card`・`.mj-pro-plans`・`.mj-pro-heading`）をそのまま使う |
| 配信 | `_redirects` の `/top` → `/top/`（301）・`/top/` → `/top/index.html`（200）、`check-assets.yml` の `allowed()` の `top` |
| ナビ | `navbar.js` の白地のナビの対象に `/top/`（`newNavCurrent = 'トップ'`。どの項目にも当たらないので印は付かない） |
| 毎朝 | `update-live-channel.yml` の `regenerate` ジョブの `target_page` に `top_page`（pro/・r/ の後） |
| 検査 | `scripts/tests/test_top_page.py` |

## 面（2026-11-03 の決定の並び）

1. 質問欄 — 差し込み口 `data-top-slot="ask"` だけ（中身は TOP-04。トップでは横断 `contest: "all"` で答える）
2. お気に入りの今後の予定 — 差し込み口 `data-top-slot="favorites"` だけ（中身は TOP-03。JS が実行時に pro/ 側の予定の JSON を読み、localStorage のお気に入りで絞って5件と「ほかN件」。0件の人には「pro/ の個別ページの ★ でお気に入りに登録できます」の1行）
3. 開催中・直近の大会（#231） — 下の「大会日程の判定」
4. 今日が誕生日の選手（#286） — `lib/birthdays.py` の一覧（「プロ」の表示 Y × 名簿）。今日の前後 `BIRTHDAY_DAYS` 日分を焼き、今日でない行は `hidden`。名前は pro/ の個別ページ（slug は `pro/search.json`）へ、2行目は所属・入会期。今日の行が無ければ見出しごと隠す。行が1つも無ければ面を出さない
5. 新着の番組 — 下の「新着の番組」。面の位置は誕生日と各セクションの間（仮置き）
6. 各セクションへの導線 — 「各セクション」9行（連盟プロ `/pro/`・鳳凰戦・女流桜花・WRC リーグ・WRC-R・最強戦・タイトル戦・放送対局 `/live/`・良栄 `/r/`）。「番組」の行は置かない（navbar の「番組」のドロップダウンはそのまま。TOP-02 の「番組」の3行の面は 2026-11-03 にやめた）

題は「日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro」、h1 は「ryoei.pro」、JSON-LD は `WebSite`（今の index.html と同じ）。

## 大会日程の判定

- 元: 放送対局は `sync_live_calendar.load_desired()`（公開カレンダー「mj_放送対局」と同じ予定。シートを読む）、最強戦は `saikyo/players/` の対局前の卓（pro/ の `load_saikyo_plans()`）
- 大会: `yotei.event_of()` の大会名が `title/search.json` の `t` の大会名と一致するか、title/ の名前がその大会名で終わる（「小島武夫杯帝王戦」と「帝王戦」）。title/ に無い大会（鳳匠戦・JPMLリーグ・プロNo.1決定戦・地方プロリーグなど）は載せない
- 段（`is_late_stage()`）: ベストN があれば N ≤ 16。無ければ「決定戦」を含むか、「決勝」を含み「決勝トーナメント」でない。それ以外（第N節・予選・プレーオフ・入れ替え戦・段の語の無い件名）は載せない側に倒す
- 最強戦: 対局名が「ファイナル」で始まる卓（1st Stage〈ベスト16〉・2nd Stage〈ベスト8〉・ファイナルトーナメント〈決勝〉）。予選の大会の「ベスト8」「決勝」は載せない
- 行: pro/ の `plan_row()` の形（日付・大会のチップ・件名）。行き先は YouTube の枠、枠のまだ無い予定表の予定は title/ の大会のページ、最強戦は saikyo/ の卓のページ
- 今日以降の行を全部焼き、先頭5件（`SCHEDULE_MAX`）だけ見せる。`assets/top.js` が日本時間の今日より前の行を隠し、残りの先頭5件と「ほかN件」を数え直す。1件も無ければ面を出さない

## 新着の番組

帰り道・プロテスト・Mつくの3番組を横断し、公開日時の新しい順に3件（`NEW_EPISODES_MAX`、2026-11-03 の決定）。女流勉強会（`study/`、未公開 #576）は含めない。

| 番組 | 元（各番組の生成と同じ） | 公開日時 | 題 |
|---|---|---|---|
| 帰り道 | 「帰り道」シート（`wayhome.fetch_rows()`）と `data/youtube_meta.json`（`wayhome.collect_episodes()`） | YouTube API の `publishedAt` | 題と名前（シートの「タイトル」「名前」） |
| プロテスト | 「プロテスト」シート（`exam.fetch_tab_rows()`）と /live の層1（`exam.join_layer1()`・`study.load_layer1()`） | 層1の配信開始日時（無ければ公開日時） | `exam.heading()`（「タイトル（補正分）」があればそれ）。出演者の名前は足さない（`簡略化:`） |
| Mつく | 「Mつく」シート（`mtsuku.fetch_rows()`） | シートの「公開日」の 00:00 JST | 「チーム名」と作る人（シートの名前のまま、`簡略化:`） |

- 番組の元が読めない（シート・JSON・層1・Mつくの公開日の書式など）ときは、警告してその番組だけ外し、トップの生成は止めない（大会日程・誕生日の毎朝の更新を止めないため。pro/ の道場部ゲストと同じ扱い）
- 公開前（生成の時刻より後）の回と、**各話のページ（`wayhome/<動画ID>.html` など）がまだリポジトリに無い回は出さない**。各話のページは週次の `all`（`fetch-youtube.yml`）などで作られ、トップは毎朝（`update-live-channel.yml`）作り直すので、ページより先にトップに出てリンク切れになるのを防ぐ
- 鮮度: 帰り道の公開日時（`data/youtube_meta.json`）は週次の取得（`fetch-youtube.yml`）、プロテストの層1は毎朝の `update-live-channel.yml` の手順1（トップの生成と同じ実行の前）で更新される。シートはトップの生成のたびに読む。トップに出るのは、各話のページができた翌朝から
- 行: pro/ の予定の行と同じ並び（1段目に公開日「2026年8月27日」〈帰り道のカードと同じ書き方〉と番組のチップ、2段目に題）。行き先は各話のページ（サイトの中、新しいタブにしない）。1件も無ければ面を出さない

## 未公開の段の扱い

- 切り替えの指示でやること: `OUTPUT_DIR`・`ASSET_PREFIX`・`PAGE_URL` を `index.html` 向けにし、`NOINDEX_TAG` を外す。`OUTPUT_OVERRIDES` を `index.html` に、`_redirects` の `/top` の2行・`check-assets.yml` の `top`・`navbar.js` の `/top/` の分岐を消す（`/` は白地のナビの対象に）。`_redirects` 先頭の `/  /index.html  200` は消さない
- 切り替えの後に直す記述: docs/handover.md「新サイトの第一弾は index.html（#101）」、docs/new-site-design.md「最初に作るのは index.html」、docs/notes/static-generation.md の `index.html` の行、`scripts/lib/page.py` の「index.html以外はbootstrap.min.cssを読む」のコメント、`scripts/tests/test_breadcrumb_json_ld.py` の `test_top_has_website`（新トップも `WebSite` を出す）
- 片付け（切り替えの後の指示）: `index.css`・`index.js`・`assets/vendor` のテンプレート用ライブラリ
