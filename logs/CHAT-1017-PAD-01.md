# CHAT-1017-PAD-01

- 着手日時: 2026-10-17
- 対象issue: #428
- ブランチ: work/1017-pad
- 着手時HEAD: 6e1da5ac7

## 指示

【Claude作成】Claude Code 向け指示：#428 の残り — `:has()` の成立待ちで遅れて当たる上余白・固定・幅の洗い出しと計測（調査のみ）
Chat-Ref: CHAT-1017-PAD-01
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（新しいセッションに、この指示だけを貼る。PAD は新しい識別子で、このチャットの最初の指示）
作業ブランチ: クラウドセッションで実行する。work/1017-pad を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#428 の残り（最強戦の分は 2026-10-10 にマージ済み、2026-10-15 に対象から外した）として、`body:has(...)` / `html:has(...)` が本文のパースまで成立せず、上余白・固定・幅が遅れて当たってレイアウトがずれる形を、サイト全体で洗い出して計測する。直すのは結果を見て決めるので、この指示ではコード・CSS・生成物を変えない。

### 決定（2026-10-17、平野さん）

- 今日（10/17）の作業は「#428 の残り」と「`live/` の写真カードの iPhone の実機確認」とする（第3弾〈#525〉の実機確認は SWP-17 で済んでいる。`live/` の分だけが残っていた）
- #428 の洗い出しの範囲は、issue の3種類（`title/`・`/live`・表のページ〈`body:has(.mj-table)`〉）に、動画のページ（`.mj-video-page` / `.mj-video-list`。帰り道・Mつく・女流勉強会）を足した全部にする。直すのは計測でずれが出たものだけ（案A）

### 前提（チャット側。平野さんの決定ではない）

- チャット側が 2026-10-17 に origin/cloudflare の `style.css` を grep した結果（要確認）:
  - `position: fixed` ＋ `body` の `padding-top` を `:has()` で当てている: `body:has(.mj-table)`（`padding-top: var(--content-offset, 90px)`、`html:has(.mj-table)` の `scroll-padding-top`）、`body:has(.mj-title)`（`padding-top: calc(var(--mj-title-nav-h, …) + var(--mj-title-filter-h, 61px))`、幅で値が変わる）。#428 の本題と同じ形に見える
  - `position: sticky` を `:has()` で当てている: `body:has(.mj-video-page) nav.navbar`、`body:has(.mj-video-list, .mj-live) .mj-filterbar`。sticky は成立前も通常の流れの位置に置かれるので、上余白のずれは出にくいと見ている（見込み。計測で確かめる）
  - 幅を `:has()` で決めている: `body:has(.mj-live) .mj-lead`・`body:has(.mj-video-list) .mj-lead`・`body:has(.mj-table) .mj-lead`・`body:has(#pros_table)` など。最強戦で `.mj-lead` の幅が `:has()` 待ちで変わり CLS が増えた例がある（`style.css` の 1700 行目付近のコメント、CHAT-0921-SG-04）。これも同じ形として数える
  - `:has()` のうち、レイアウトを変えないもの（色・フォーカスの見た目、`a:has(> img)` など）は対象外
- 対象のページは、上の各セレクタのクラスを持つ HTML を grep で拾って種類ごとに代表を選ぶ（例: `title/` の入口・大会・期、`live/` の入口・大会・卓〈`live/ourai/3/f.html` など〉、表のページ数枚〈`rh_results_detail` など〉、`video_wayhome.html`・`wayhome/` の個別1枚・`mtsuku/`。`study/` は未公開なので origin/cloudflare の生成物で測る）
- 計測の方法は #428 の saikyo/ の分（CHAT-1010-SKS-01、#428 の 2026-10-10 のコメント）にそろえる: headless Chromium、幅 390px と 1100px、各ページ・各幅 10回。見るのは (1) rAF ごとに、`<body>` があるのに `:has()` で当てる値がまだ当たっていない（`padding-top` が 0、`.mj-lead` の幅が既定のまま など）フレームの数、(2) CLS の「0 でない回数」と中央値（1回ごとの最大値と「すべて 0」は条件にしない）。シフトがあれば動いた要素（`main#main` など）を書く。計測スクリプトは `docs/logs/CHAT-0919-HG-01/cdp.py` を使ってよい
- 環境は本番（`https://ryoei.pro/`、キャッシュ回避のクエリ付き）か `file://`（origin/cloudflare の生成物）のどちらか一方に決め、全ページを同じ環境で測る。本番に届かなければ `file://` にする（どちらにしたかと理由をログに書く）。外部の画像が届かないときは saikyo/ のときと同じ扱いでよい
- 直し方の候補の参考: saikyo/ は生成スクリプトで `<body class="mj-saikyo-page">` を付けて `:has()` をやめた（`scripts/lib/page.py` の `render_content()` には移さない、2026-10-10 の平野さんの決定）。表のページ・`title/`・`/live`・動画のページで同じ形がとれるか（生成スクリプトか手書き HTML か、どのファイルを変えるか）を、計測でずれが出た種類についてだけ案にする
- 決定の書き先は、第3弾の決定と同じ `docs/decisions/site-review.md` を想定（要確認。#428 の分野に合うファイルが別にあればそちら）
- 使う skill は無い

## 手順

1. 確かめる: #428 の本文と最新のコメント（2026-10-15、CHAT-1015-SKR-01 まで）を読み、他セッションの着手中コメント（未解除のもの）があれば止まる。#428 に着手中のコメントを書く（本文・題・ラベルは変えない）。上の前提の `style.css` の grep を origin/cloudflare で行い直し、`:has()` を使うセレクタを「レイアウトを変える／変えない」に分けた一覧をログの経過に書く（セレクタ・当てる値・持つページ・JS が後から同じ値を入れるか〈`assets/title.js` の `--mj-title-nav-h` など〉）。前提と食い違えば、食い違いを書いて実物に従う
2. 測る: 上の前提の方法で、レイアウトを変える種類ごとに代表のページを測り、種類 × ページ × 幅 の表にする（遅れたフレームの数、CLS の 0 でない回数／中央値、動いた要素）。JS 無効のときに本文が固定バーの下に隠れないかも、`position: fixed` の種類で見る
3. まとめる: ずれが出た種類だけ、直し方の案（変えるファイル・クラス名の案・ほかのページへの影響）を書く。ずれが出なかった種類は「直さない」理由を1行で書く。結果を #428 にコメントする（範囲を動画のページまで広げたこと〈2026-10-17、平野さんの決定〉を冒頭に書く。表はログの `## 経過` の計測の表から引用する。末尾に `Chat-Ref: CHAT-1017-PAD-01`）。直すかどうか・どの案にするかは「判断が必要なこと」に書く

## 止まる条件

- #428 に他セッションの未解除の着手中コメントがある
- `style.css` に `:has()` で上余白・固定・幅を当てるセレクタが1つも無い（前提が大きく食い違う）
- 計測の環境が途中で変わる（本番と `file://` が混ざる）ことになったら、混ぜずに止まり、そこまでの結果を書く
- 未マージの work/ ブランチが `style.css` の、上の一覧のセレクタと同じ行を変えている場合は止まらず、ブランチ名と行をログに書く（この指示はコードを変えないため）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs/ と docs/decisions/ だけの見込み。ほかが出たら入れずに止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow origin` の後、`PAD` のコミット・`docs/logs/` の履歴ともに0件。同じ Chat-Ref のコミットも0件
- 作業ブランチ: ローカル・リモートとも `work/1017-pad` が無いため `git checkout -b work/1017-pad origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致

### 1. 確かめたこと

- #428: 本文とコメント6件（最後は 2026-10-15 の CHAT-1015-SKR-01）を読んだ。他セッションの未解除の着手中コメントは無い。着手中のコメントを書いた（本文・題・ラベルは変えていない）
- 未マージの work/ ブランチ（`1009-swp-526`・`1011-swp-hnd`・`1011-swp-nav`・`1015-skd-notes`・`1016-amp`・`1017-hou`）は、どれも `style.css` を変えていない（`git diff --stat origin/cloudflare...<branch> -- style.css` が空）
- 本番と origin/cloudflare（6e1da5ac7）: `style.css`・`assets/title.js`・計測する HTML の数枚を `curl` で取って比べ、同一

#### `style.css` の `:has()` の一覧（origin/cloudflare、82行に出る）

目印のクラスを持つ HTML（クラスの語で数えた。`docs/`・`scripts/` を除く）: `.mj-table` 9枚（`rh_results`・`rh_results_detail`・`rh_paifu`・`jpml_pros`・`jpml_test`・`resource_logs`・`saikyo_mens`・`video_live`・`video_en`）、`.mj-title` 384枚（title/）、`.mj-live` 1551枚（live/ 908・saikyo/ の対局 643）、`.mj-video-page` 1888枚（live/ 908・saikyo/ 643・books/ 191・mtsuku/ 62・wayhome/ 43・study/ 41）、`.mj-video-list` 3枚（wayhome/・mtsuku/・study/ の入口）、`#pros_table` 1枚（jpml_pros）、`.mj-bar-chart-*` 1枚（resource_efficiency）、`.mj-league-chart-*` 2枚（houou_leagues・ouka_leagues）、`.mj-houou`/`.mj-houou-race` 13枚（houou/）、`.mj-saikyo` 236枚（saikyo/）

**レイアウトを変える（上余白・固定・幅・並び）**

| 種類 | セレクタ（行） | 当てる値 | JS が後から入れる値 |
|---|---|---|---|
| 表 | `body:has(.mj-table) nav.navbar`（383） | `position: fixed`（固定） | なし |
| 表 | `body:has(.mj-table)`（455） | `padding-top: var(--content-offset, 90px)`（上余白） | `table.js`・`jpml_pros.js` が `--content-offset`（navbar＋検索欄の実測）を入れる |
| 表 | `body:has(.mj-table) .mj-lead`（865） | `max-width: none`（幅） | なし |
| 表 | `body:has(#pros_table)`（569）・`body:has(#pros_table) .mj-lead`（869） | `--mj-pros-table-width: 934px`・`max-width`（幅。jpml_pros のみ） | なし |
| 表 | `body:has(.mj-table) nav.navbar .navbar-collapse.show`（409、991px 以下） | メニューを開いたときの `max-height` | なし（開いたときだけ） |
| タイトル戦 | `body:has(.mj-title) nav.navbar`（2470） | `position: fixed` | なし |
| タイトル戦 | `body:has(.mj-title)`（2478・2487） | `padding-top: calc(var(--mj-title-nav-h, 80px/56px) + var(--mj-title-filter-h, 61px))` | `assets/title.js` が `--mj-title-nav-h`・`--mj-title-filter-h` を入れる |
| タイトル戦 | `body:has(.mj-title) .mj-filterbar`（2505・2523） | `position: fixed`・`top`・flex・padding | `top` は同上の変数 |
| タイトル戦 | `body:has(.mj-title) main`（2419） | `max-width: 960px`・`margin: 0 auto`・padding（幅） | なし |
| タイトル戦 | `body:has(.mj-title) .mj-lead`（2425）・`.mj-filterbar-count:empty`（2575） | `max-width: none`・`position: absolute` | なし |
| live・動画一覧 | `body:has(.mj-video-list, .mj-live) .mj-filterbar`（1422） | `position: sticky`・`top: var(--mj-nav-h, 56px)`・flex・padding | `assets/live.js`・`video_wayhome.js` が `--mj-nav-h`・`--mj-filter-h` |
| live・動画一覧 | `html:has(.mj-video-list, .mj-live)`（1388） | `scroll-padding-top`・`scrollbar-gutter: stable`（gutter は幅に効きうる） | 同上 |
| live | `body:has(.mj-live) .mj-lead`（3193）・`.mj-filterbar-count:empty`（3218） | `max-width: 1100px`・margin・padding／`position: absolute` | なし |
| 動画一覧 | `body:has(.mj-video-list) .mj-lead`（970）・`.mj-fullbleed`（1485）・`.mj-video-grid`（1542）・`.mj-video-episodes-summary`（1493）・`.mj-filterbar-count:empty`（1474） | 幅・全幅化・grid・flex・`display: none` | なし |
| 動画のページ | `body:has(.mj-video-page) nav.navbar`（1393） | `position: sticky`・`top: 0` | なし |
| 動画のページ | `body:has(.mj-video-page) nav.navbar .navbar-collapse.show`（1412） | メニューを開いたときの `max-height` | なし（開いたときだけ） |
| 動画のページ | `.mj-video-hero:has(> a.mj-video-back) .mj-video-hero-content`（1572） | `padding-top`（文字を大きくしてヒーローが伸びたときだけ見た目が変わる） | なし |
| 共通（data-search が off でないページ） | `.navbar:has(> a.btn[data-bs-toggle="collapse"])` ほか（422〜449、991px 以下） | navbar の中の並び（虫眼鏡を1行目に） | 目印は navbar 自身の子で、同じ `document.write` で入る |
| 範囲外: グラフ | `body:has(.mj-bar-chart-desktop)`（200）・`body:has(.mj-league-chart-desktop)`（237）・それぞれの `.mj-lead`（876〜889） | 変数・`.mj-lead` の `max-width` | なし |
| 範囲外: 鳳凰戦 | `body:has(.mj-houou) .mj-lead`・`body:has(.mj-houou-race) .mj-lead`（3837）・`body:has(.mj-houou, .mj-houou-race) .mj-lead`（5339） | `max-width: 400px`・margin・padding | なし |
| 範囲外: 最強戦 | `body:has(.mj-saikyo) .mj-lead`（1706） | `max-width`・padding | なし（10/15 に対象から外した） |

- `.mj-lead` はどのページも本文の末尾（`</main>` の手前）にあり、目印（表・`.mj-title`・`.mj-live`・グラフ・`.mj-houou`）より後ろで出る。幅を `:has()` で決める規則は、`.mj-lead` が現れる前に `:has()` が成立している並び

**レイアウトを変えない（対象外）**

`a:has(> img)`（63、下線）、`body:has(.mj-video-page) .visually-hidden-focusable:focus`（401、色）、`html:has(.mj-video-page)`（918 `color-scheme`・1384 `scroll-padding-top`。スクロール位置だけ）、`body:has(.mj-video-page)`（922、色の変数と背景色・文字色）、`body:has(.mj-video-page) .mj-filter-input`・`.mj-lead`（946・964、色）、`body:has(.mj-video-list) .mj-video-hero`（1363、`isolation`・`z-index`）、`html:has(.mj-table)`・`html:has(.mj-title)`（467・2482・2491、`scroll-padding-top`）、`html:has(.mj-saikyo-table-page)`（2249、同）、`body:has(.mj-title) .mj-filterbar .mj-filter-input`（2528、色）、`:root:has(.mj-houou, .mj-houou-race)`・`body:has(.mj-houou, .mj-houou-race)`（5180・5202・5225、色）、`.mj-houou-term:has(.mj-houou-term-hit:active)`（4200・4625・5599、押したとき）、`.mj-houou-suggest-item.is-recent:has(.mj-houou-recent-badge)`（5485、目印が同じ項目の子）

**前提との食い違い**

- `body:has(.mj-video-page)` は動画の3つ（帰り道・Mつく・女流勉強会）のほか、live/・saikyo/ の対局ページ・books/ にも付いている（books/ 191枚は前提に無い）。books/ も1枚を参考に測る
- `.mj-live` は saikyo/ の対局ページ（643枚、`.mj-saikyo-table-page`）にも付いている。最強戦は 10/15 に対象から外したが、この形は live/ と同じ CSS なので、参考に1枚測る
- `video_wayhome.html` は無い（帰り道の一覧は `wayhome/index.html`）
- 前提に無い `:has()` で幅を当てる規則: グラフ（型C・型D）と鳳凰戦（houou/）の `.mj-lead`。決定の範囲（4種類）の外なので一覧に「範囲外」として載せ、参考に測る
- そのほかは前提のとおり

### 2. 計測

#### 環境と方法

- **環境は本番**（`https://ryoei.pro/`、URL に `?pad=<時刻>` を付けてキャッシュを避けた）。セッションの Chromium からプロキシ経由で届いたため。本番の HTML・CSS・JS は origin/cloudflare（6e1da5ac7）と同一（上の「確かめたこと」）。`study/` も本番で 200 を返し origin/cloudflare と同一なので、全ページを本番で測った（`file://` は混ぜていない）
- 計測: Playwright 1.56.1・Chromium 1194（headless）。幅 390px と 1100px（高さ 844px）、各ページ・各幅10回。1回ごとに新しい context（キャッシュなし）。ページを1周ずつ交互に回した（20ページ × 2幅 × 10回 = 420回、エラー0）
- 記録: `addInitScript` で rAF ごとに `body` の `padding-top`・`nav.navbar` の `position`・`.mj-filterbar` の `position`・`main` と `.mj-lead` の `max-width`・目印の有無を取り、`layout-shift`（`hadRecentInput` を除く）を合計した。読み込み完了から 1.5秒待って止めた
- 「遅れたフレーム」: `<body>` があるのに、`:has()` で当てる値が当たる前の値（`padding-top: 0px`・`position: relative/static`・`max-width: none/720px`）のフレーム。初回の描画（FCP）より後かも分けた
- シフトの原因の分け方: 各シフトの直前と直後のフレームを比べ、`:has()` で当てる値が「当たる前 → 当たった後」に変わっていれば `:has()` 由来、それ以外（JS が変数を実測値に替えたなど）は「他」とした
- 外部の画像（`yt3.ggpht.com`・`assets.st-note.com` など、許可していないドメイン）は届かない。saikyo/ のときと同じく、そのまま測った
- スクリプトは scratchpad の `measure.mjs`・`analyze.py`・`nojs.mjs`（リポジトリには入れない）

#### 結果（種類 × ページ × 幅、各10回）

| 種類 | ページ | 幅 | 遅れたフレームがある回 | うち初回の描画（FCP）の後 | CLS 0でない回数 / 中央値 | うち `:has()` 由来 | うち他（JS の値の差し替えなど） | 動いた要素・変化 |
|---|---|---|---|---|---|---|---|---|
| 表 | `rh_results_detail.html` | 390 | 10/10（最大 2） | 1/10 | 10/10 / 0.0376 | 1/10 / 0.0000 | 10/10 / 0.0376 | `main#main`（他: padding-top 90px→56px ×10、`:has()`: 0px→90px・nav relative→fixed ×1） |
| 表 | `rh_results_detail.html` | 1100 | 10/10（最大 3） | 0/10 | 10/10 / 0.0082 | 0/10 / 0.0000 | 10/10 / 0.0082 | `main#main`（他: 90px→80px ×10） |
| 表 | `jpml_pros.html` | 390 | 10/10（最大 15） | 0/10 | 10/10 / 0.0376 | 0/10 / 0.0000 | 10/10 / 0.0376 | `main#main`（他: 90px→56px ×10） |
| 表 | `jpml_pros.html` | 1100 | 10/10（最大 12） | 4/10 | 10/10 / 0.0082 | **4/10** / 0.0000 | 10/10 / 0.0082 | `main#main`（他: 90px→80px ×10、`:has()`: 0px→90px・nav relative→fixed ×4） |
| 表 | `video_live.html` | 390 | 10/10（最大 13） | 4/10 | 10/10 / 0.0376 | **4/10** / 0.0000 | 10/10 / 0.0376 | `main#main`・`img.rectangle`（他: 90px→56px ×10、`:has()`: 0px→90px・nav relative→fixed ×4） |
| 表 | `video_live.html` | 1100 | 10/10（最大 14） | 1/10 | 10/10 / 0.0082 | **1/10** / 0.0000 | 10/10 / 0.0082 | `main#main`（他: 90px→80px ×10、`:has()` ×1） |
| 表 | `jpml_test.html` | 390 | 10/10（最大 15） | 0/10 | 10/10 / 0.0376 | 0/10 / 0.0000 | 10/10 / 0.0376 | `main#main`（他: 90px→56px ×10） |
| 表 | `jpml_test.html` | 1100 | 10/10（最大 7） | 0/10 | 10/10 / 0.0082 | 0/10 / 0.0000 | 10/10 / 0.0082 | `main#main`（他: 90px→80px ×10） |
| 表 | `rh_results.html` | 390 | 10/10（最大 7） | 0/10 | 10/10 / 0.0202 | 0/10 / 0.0000 | 10/10 / 0.0202 | `main#main`（他: 90px→56px ×10） |
| 表 | `rh_results.html` | 1100 | 10/10（最大 6） | 0/10 | 10/10 / 0.0041 | 0/10 / 0.0000 | 10/10 / 0.0041 | `main#main`（他: 90px→80px ×10） |
| タイトル戦 | `title/` | 390 | 10/10（最大 5） | 0/10 | 10/10 / 0.0551 | 0/10 / 0.0000 | 10/10 / 0.0551 | `main#main`・`span.mj-title-holder-taikai`（他: padding-top 117px→171px ×10） |
| タイトル戦 | `title/` | 1100 | 10/10（最大 5） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| タイトル戦 | `title/judan/` | 390 | 10/10（最大 3） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| タイトル戦 | `title/judan/` | 1100 | 10/10（最大 4） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| タイトル戦 | `title/judan/42.html` | 390 | 10/10（最大 4） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| タイトル戦 | `title/judan/42.html` | 1100 | 10/10（最大 3） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/` | 390 | 5/10（最大 2） | 5/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/` | 1100 | 5/10（最大 2） | 5/10 | 5/10 / 0.0001 | **5/10** / 0.0001 | 0/10 | `li.mj-video-card`（`:has()`: nav relative→sticky・`.mj-lead` の幅。1回 0.0002） |
| live | `live/judan/` | 390 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/judan/` | 1100 | 9/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/judan/43.html` | 390 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/judan/43.html` | 1100 | 6/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/ourai/3/f.html` | 390 | 9/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| live | `live/ourai/3/f.html` | 1100 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `wayhome/` | 390 | 7/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `wayhome/` | 1100 | 5/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `wayhome/0OPca1Bl5xk.html` | 390 | 7/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `wayhome/0OPca1Bl5xk.html` | 1100 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `mtsuku/` | 390 | 7/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `mtsuku/` | 1100 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `study/` | 390 | 5/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 動画 | `study/` | 1100 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 参考: books | `books/9784046013460.html` | 390 | 5/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 参考: books | `books/9784046013460.html` | 1100 | 9/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 参考: 最強戦の対局 | `saikyo/2019/20190223-s1-a.html` | 390 | 8/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 参考: 最強戦の対局 | `saikyo/2019/20190223-s1-a.html` | 1100 | 9/10（最大 1） | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 範囲外: グラフ | `resource_efficiency.html` | 390 | 0/10 | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 範囲外: グラフ | `resource_efficiency.html` | 1100 | 0/10 | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 範囲外: グラフ | `houou_leagues.html` | 390 | 0/10 | 0/10 | 10/10 / 0.0199 | 0/10 | 10/10 / 0.0199 | `p.mj-lead`（記録した計算値は変わらない。`:has()` ではない） |
| 範囲外: グラフ | `houou_leagues.html` | 1100 | 0/10 | 0/10 | 10/10 / 0.0056 | 0/10 | 10/10 / 0.0056 | `p.mj-lead`（同上） |
| 範囲外: 鳳凰戦 | `houou/` | 390 | 0/10 | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |
| 範囲外: 鳳凰戦 | `houou/` | 1100 | 0/10 | 0/10 | 0/10 / 0.0000 | 0/10 | 0/10 | — |

- `:has()` 由来のシフトの1回の値: `jpml_pros` 1100px 0.0446（4回とも）、`video_live` 390px 0.0376（4回）・1100px 0.0082、`rh_results_detail` 390px 0.0376、`live/` 1100px 0.0002（5回）。表のページは FCP の 140〜430ms 後に `padding-top` が 0→90px になり `main#main` が下がる
- 表のページでは、`.mj-table` が DOM にあるのに `padding-top` が 0px のままのフレームがあった（例: `jpml_pros` 1100px で t=605ms に `.mj-table` あり・0px、t=770ms に 90px）。目印が早く出ても、HTML が大きい（`jpml_pros` 1.2MB・`video_live` 1.7MB）ページではパースの途中で描画が入り、`:has()` の成立が遅れて見える
- タイトル戦・live（入口以外）・動画・参考のページの「遅れたフレーム」は、すべて FCP より前（`<body>` があって `<main>` がまだ無い1〜数フレーム）で、描画に出ない
- **`:has()` と別の、毎回出るずれ（他）**:
  - 表のページ: `padding-top` のフォールバック `90px` を、`table.js`・`jpml_pros.js` が実測値（390px で 56px、1100px で 80px）に替えるときに `main#main` が上がる。10/10 で 0.0041〜0.0376
  - `title/`（入口）390px: フォールバック 117px（56＋61）を `title.js` が 171px（年のプルダウンでバーが2行、115px）に替えるときに本文が下がる。10/10 で 0.0551。大会・期のページはバーが1行で、ずれない
  - `houou_leagues.html`: `p.mj-lead` が毎回動く（0.0199／0.0056）。記録した計算値に変化は無く、`:has()` の成立待ちではない（原因は調べていない。範囲外）

#### JS 無効（`position: fixed` の種類）

幅 390・460・1100px、スタイルシートの読み込みを待ってから測った。

| ページ | 幅 | `body` の padding-top | 固定のバーの下端 | 本文の先頭 | 隠れる |
|---|---|---|---|---|---|
| `rh_results_detail.html`・`jpml_pros.html`・`video_live.html` | 390・460・1100 | 90px | なし（navbar は JS で描くため出ない） | 90 | いいえ |
| `title/judan/`・`title/judan/42.html` | 390・460 | 117px | 117（`.mj-filterbar`、1行） | 133 | いいえ |
| 同上 | 1100 | 141px | 141 | 157 | いいえ |
| `title/`（入口） | 390・460 | 117px | **171**（2行） | 133 | **はい（38px）** |
| `title/`（入口） | 1100 | 141px | 141 | 157 | いいえ |

- `title/` の入口は、JS 無効だと狭い幅で本文（`.mj-title-content`）の先頭 38px が固定バーの下に入る。`:has()` ではなく、フォールバックがバー1行分（61px）しかないため（最強戦の #381 と同じ形）
- 表のページは JS 無効では固定のものが無く、上に 90px の空きが出るだけ

### 3. まとめ

#### ずれが出た種類: 表のページ（`body:has(.mj-table)`）

`:has()` 由来のシフトが `jpml_pros` 1100px 4/10（0.0446）、`video_live` 390px 4/10（0.0376）・1100px 1/10、`rh_results_detail` 390px 1/10。中央値はどれも 0 だが、HTML の大きいページで出る。

直し方の案（saikyo/ と同じ形。`<body>` にクラスを付けて `:has()` をやめる）:

- 案T1（推奨）: `scripts/lib/page.py` の `PAGE_TEMPLATE`（`render()` だけが使う、表を持つページ専用のテンプレート）の `<body{body_attrs}>` に `class="mj-table-page"` を足し、`scripts/generate_jpml_pros.py` の自前の `PAGE_TEMPLATE`（`<body>`）にも同じクラスを足す。`style.css` の `body:has(.mj-table) nav.navbar`（383）・`… .navbar-collapse.show`（409）・`body:has(.mj-table)`（455）・`body:has(.mj-table) .mj-lead`（865）を `body.mj-table-page` に置き換える。`html:has(.mj-table)`（467、スクロール位置だけ）は残す
  - 変わるページ: 表の9枚（`render()` を通る8枚と `jpml_pros`）だけ。`render_content()`（`CONTENT_TEMPLATE`）は変えないので、型C・型D・title/・live/・動画などの出力は変わらない。`scripts/lib/page.py` は全ページ共通のファイルなので、触ってよいかは判断が要る（2026-10-10 の決定は「`render_content()` に移さない」）
- 案T2: saikyo/ と同じく、9つの生成スクリプトそれぞれで出力の `<body` にクラスを足す（`page.py` を触らない）。同じ処理が9か所に要る
- 別件（`:has()` ではない）: `body:has(.mj-table)` の `padding-top` のフォールバック `90px` が実測（992px 未満 56px・以上 80px）と合わず、**毎回** 0.0041〜0.0376 のずれが出る。title/ と同じく幅ごとのフォールバック（56px／80px）にすると消える見込み（検索欄を開いた状態の値は JS が入れる）。#428 で一緒に直すか、別の issue にするかは判断が要る

#### ずれが出なかった種類（直さない）

- タイトル戦（`body:has(.mj-title)`）: `:has()` 由来 0/60。目印 `.mj-title` が `<main>` の直後にあり、遅れたフレームはすべて初回の描画より前
- live（`.mj-live`・`.mj-video-list, .mj-live`）: 入口の 1100px で 5/10 出たが 1回 0.0002（良好の目安 0.1 の 1/500）。ほかの live のページ・最強戦の対局は 0/10。直さない案（判断が要る）
- 動画のページ（`.mj-video-page`・`.mj-video-list`）: 0/80（books/ は 0/20）。sticky は成立前も通常の位置にあり、目印が `<main>` の直後
- 範囲外のグラフ・鳳凰戦の `.mj-lead` の幅: `.mj-lead` が目印より後ろにあり、遅れたフレームは 0

#### 別件（`:has()` ではない、範囲外）

- `title/` の入口 390px: フォールバック（バー1行の 61px）とバーの実際（2行 115px）の差で、**毎回** 0.0551 のずれと、JS 無効で本文の先頭 38px がバーの下に隠れる。saikyo/ の #381 と同じ形（年のプルダウンのある入口だけ）。直すなら幅ごとのフォールバックで、2行になる幅の境目を測る必要がある
- `houou_leagues.html`: `p.mj-lead` が毎回 0.0199（390px）／0.0056（1100px）動く。原因は調べていない

### 4. issue・文書

- #428 に結果をコメントした（範囲を動画のページまで広げたことを冒頭に書いた）。閉じない。ラベルは変えていない
- 決定を `docs/decisions/site-review.md` に足した（#428 の分野のファイルは saikyo.md もあるが、今回の決定は第3弾の作業の範囲の決定なので site-review.md にした）
- 変えたファイルは `docs/logs/CHAT-1017-PAD-01.md` と `docs/decisions/site-review.md` だけ

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-PAD-02
- ブランチ: work/1017-pad
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-PAD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad
- 確認用URL: なし
- マージ: 済（fast-forward、cloudflare 9e8c4c175）
- issue: #428
- 判断が必要なこと:
  - 表のページ（`body:has(.mj-table)`）の `:has()` 待ちのずれを直すか。直すなら案T1（`scripts/lib/page.py` の表のページ専用の `PAGE_TEMPLATE` と `generate_jpml_pros.py` のテンプレートの `<body>` に `class="mj-table-page"`、`style.css` の4規則を置き換え。変わるのは表の9枚だけ）か案T2（9つの生成スクリプトでそれぞれ足す）か。案T1 は全ページ共通の `page.py` を触る
  - 表のページの `padding-top` のフォールバック 90px と実測（56px／80px）の差による毎回のずれ（0.0041〜0.0376、`:has()` ではない）を #428 で一緒に直すか、別の issue にするか
  - `title/` の入口の 390px のずれ（毎回 0.0551）と JS 無効で本文の先頭 38px が固定バーの下に隠れる件（フォールバックがバー1行分、`:has()` ではない）を issue にするか
  - `live/` の入口の 1100px の `:has()` 由来のずれ（5/10、1回 0.0002）を直さないでよいか
- 未確認の項目:
  - iPhone の実機（Safari）での見え方は確かめていない（headless Chromium だけ）
  - `houou_leagues.html` の `p.mj-lead` が毎回動く原因（範囲外。調べていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 98ff33c9）: https://github.com/retroeater/mj-logs/tree/main/guide/98ff33c9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
