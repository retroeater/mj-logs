# CHAT-1104-TOP-11

- 着手日時: 2026-11-04
- 対象issue: #101, #231
- ブランチ: work/1104-top
- 着手時HEAD: 68f0411e8

## 指示

【Claude作成】Claude Code 向け指示：新しいナビ（細い帯＋メニューのシート＋右上の質問）を白地の新しい形のページに入れ、新トップから大会日程と一行の説明を外す（#101） Chat-Ref: CHAT-1104-TOP-11 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-top を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
スマホの新しい面（白地・400px）と合っていない共通ナビを、細い帯（左にロゴ、右に「質問」と「≡」）と下から開くメニューのシートに作り直し、白地の新しい形のページに入れる。右上の「質問」は、決めてある「サイトのどこからでも呼び出せる質問欄の入口」にする。あわせて新トップ `top/` から「開催中・直近の大会」の面と一行の説明を外す。
決定（2026-11-03〜04、平野さん。TOP 系列のチャットで）

* 新トップから「開催中・直近の大会」の面を消す（2026-11-03。TOP-02 の大会日程の面〈#231 (a)〉を置き換え）
* 新トップの一行の説明「日本プロ麻雀連盟の選手・大会・放送対局のデータ」を消す（2026-11-03）
* 質問欄の送信ボタンは「↑」のまま（2026-11-04）
* 共通ナビを作り直す。形は案 A: 細い帯の左にロゴ、右に「質問」と「≡」だけ。≡ を押すと下からシートが開き、項目をまとまりごとに並べる（チャット側の比較ページの案 A。まとまりの例: 選手〈連盟プロ〉・大会〈鳳凰戦・女流桜花・WRC・WRC-R・最強戦・タイトル戦〉・動画〈放送対局・帰り道・プロテスト・Mつく〉・その他〈リソース・良栄〉）（2026-11-04）
* 新しいナビを出す範囲は、白地の新しい形のページ全部（`navbar.js` が白地の新しい形にしているページ。houou/・ouka/・jwrc/・wrcr/・saikyo/・title/・pro/・r/・top/ など）。古い表のページ（黒地の Bootstrap のナビのページ）は今のまま（2026-11-04）
* 右上の「質問」は、2026-11-03 の決定「大会別の質問欄は残したまま、サイト横断で常に画面の右上などから呼び出せる1つの入口。開いているページの大会を既定の文脈にでき、トップでは5大会を横断して答える」の入口にする

前提（チャット側。平野さんの決定ではない）

* 今の `navbar.js`: 白地の新しい形にするページはパスの一覧（`newNavCurrent` の if の連なり）で決まり、項目は今のナビと同じ（要確認。実物の項目・ドロップダウンの中身・行き先を `## 経過` に表で写してから作る。シートのまとまりは上の例を基に、実物の全項目が漏れなく入るように仮置きで分ける）。ロゴ「R」は `/index.html` へ
* 未マージの work/ ブランチが `navbar.js` を変えている見込み（TOP-01 の調べ: work/1029-lgn-page〈`/log/` を白地の対象に足す〉・work/1101-prp-pub〈「連盟プロ」を `/pro/` に〉、RYO 系列の公開〈「良栄」を `/r/` へのリンクにしてドロップダウンをやめる〉）。新しいナビの項目の一覧は、これらの変更が後から入っても1か所を直せば済む形にし（項目の表を1つの定数にする等）、`## 経過` に重なりの見込みと取り込むときの解き方を書く
* 「質問」を押すと、下から質問欄のシートが開く（仮置き）。中身は新トップの横断の質問欄（`assets/top_ask.js`、`contest: "all"`）。開いているページが houou/・ouka/・jwrc/・wrcr/・saikyo/ のときは `context` にその大会の id を渡す（パスから決める）。新トップ（top/）ではページ上部の入力欄にフォーカスを移すだけでもよい（仮置き。どちらにしたかを書く）。大会別の質問欄（各大会のトップの欄）は今のまま残す
* シートの部品は、順位変動・連盟プロの絞り込みの「下から出るシート」と同じ形・同じ動き（閉じ方・フォーカスの戻し方・背景を押すと閉じる・Esc）。既存の部品があればそれを使う
* PC 幅でも同じ帯でよい（仮置き。PC 幅でシートを右から出すなどの違いは付けない）
* 新しいナビの JS・CSS は新しいファイルにしてよい（例 `assets/nav.js`・`assets/nav.css`）。白地のページだけが読む。`navbar.js` の古いナビ（黒地）の部分は変えない
* 「開催中・直近の大会」を消すと、TOP-02 の大会日程の判定（`load_desired()` の読み込み）・`SCHEDULE_MAX`・関連のテストも要らなくなる。消したことで毎朝の `update-live-channel.yml` の `top_page` がシートを読む必要が無くなるなら、その見込みを書く（ワークフローの変更はこの指示ではしない）。title/search.json に記録0件の大会も出す案（a）は、大会日程を消したので要らなくなる（作らない）
* #231 は、トップの大会日程の面を消したので、この issue の (a) は第一弾に入らなくなった。#231 に1行コメントし、本文・状態は変えない（閉じるかは平野さんに聞く。「判断が必要なこと」に書く）

手順

1. 今の `navbar.js` の白地のナビの対象パス・項目・行き先・ドロップダウンを `## 経過` に表で写す。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で `navbar.js`・`style.css`・`scripts/generate_top_page.py`・`assets/top*.js` を変えているものを一覧にし、重なりと解き方の見込みを書く。#101 に着手中コメント
2. 新しいナビ（帯・≡ のシート・右上の質問のシート）を作り、白地の新しい形のページに出す。新トップから「開催中・直近の大会」の面と一行の説明を外す（使われなくなる関数・テスト・CSS も消す）。テスト（ナビの項目の漏れ・対象パス・質問の `context` の決め方・トップの面の並び）、`docs/notes/top-page.md`・`docs/notes/design.md`（ナビの節があれば）・`docs/decisions/top.md` を直す。共有の部品を変えるので、全ページを再生成して差分を確かめる（白地のページだけが変わり、古い表のページは変わらないこと）
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行・画面の確かめ（360px・400px・PC 幅、明暗。白地のページの代表として top/・houou/・pro/・title/ のそれぞれでナビ・≡ のシート・質問のシートを開いた状態。古い表のページの代表として jpml_pros.html が変わっていないこと）。ブランチ名の別名 URL でプレビューできる状態にし、仮置きの一覧をログに書いて止まる

止まる条件

* 白地の新しい形のページを `navbar.js` の対象パスから決められない（どのページが白地か一覧にして止まる）
* 未マージの work/ ブランチが `navbar.js` の同じ行・同じ関数を変えていて、取り込みで両方を残して解けない（解ける重なり〈対象パスの if の連なりに行を足すもの、項目の行き先の1行〉は止まらず、解き方を書く）
* 古い表のページ（黒地のナビ）の生成物・見た目に差分が出た
* 本番の質問の Worker（`workers/ask/`）を変える必要が出た（TOP は変えない。必要なことを書いて止まる）
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件
- 作業ブランチ: `work/1104-top` はローカルにもリモートにも無かったので `git checkout -b work/1104-top origin/cloudflare`（着手時 HEAD 68f0411e8）

### 1. 今の `navbar.js`（origin/cloudflare 68f0411e8）

**白地の新しい形にするページ**（`newNavCurrent` の if の連なり。値が入ると `nav.navbar` を `.mj-nav-new` にし、項目名を日本語1行にして今いる項目に印）:

| 判定 | ページ | `newNavCurrent`（印の付く項目） |
|---|---|---|
| パス `/houou/` | 鳳凰戦の新ページ | 鳳凰戦 |
| パス `/ouka/` | 女流桜花 | 女流桜花 |
| パス `/r/` | 良栄のトップ（未公開） | 良栄 |
| パス `/top/` | 新トップ（未公開） | トップ（どの項目にも当たらない） |
| パス `/jwrc/` | WRC リーグ | WRC |
| パス `/wrcr/` | WRC-R リーグ | WRC-R |
| パス `/pro/` | 連盟プロ（未公開） | 連盟プロ |
| `<body class="mj-saikyo-n-page">` | 最強戦の新しい年度・対局のページ（saikyo/ のトップ・年度など） | 最強戦 |
| `<body class="mj-live-n-page">` | 放送対局 live/ のトップ・タイトル戦・期のページ | 番組 |

**title/ は対象に入っていない**（`title/index.html` は `<body data-search="off">` で目印が無く、パスの一覧にも無い。黒地のナビのまま）。決定は「`navbar.js` が白地の新しい形にしているページ全部」なので、title/ は新しいナビの対象に入れなかった（決定の例の「title/」とは食い違う。`## 報告` に書く）。

**項目**（`document.write` の1か所。白地のときは `data-short-label` か `<br>` の前の日本語だけを出す）:

| 項目 | 行き先 | ドロップダウンの中身 |
|---|---|---|
| 連盟プロ | `/jpml_pros.html` | — |
| タイトル戦 | `/title/` | — |
| 鳳凰戦 | `/houou/` | — |
| 女流桜花 | `/ouka/` | — |
| WRC（`data-short-label`。黒地では「JPML WRC」） | `/jwrc/` | — |
| WRC-R（同） | `/wrcr/` | — |
| 最強戦 | `/saikyo/` | — |
| 番組 | （ドロップダウン） | 放送対局 `/video_live.html`・帰り道 `/wayhome/`・プロテスト `/exam/`・Mつく `/mtsuku/` |
| リソース | （ドロップダウン） | カレンダー `/resource_calendar.html`（新しいタブ）・書籍 `/resource_books.html`（新しいタブ）・ログ `/resource_logs.html`・辞書 `/resource_dictionary.html`・牌効率 `/resource_efficiency.html` |
| 良栄 | （ドロップダウン） | 成績 `/rh_results.html`・成績詳細 `/rh_results_detail.html`・牌譜 `/rh_paifu.html` |

ロゴは「R」で `/index.html` へ。右端の虫眼鏡（`searchIconHtml`）は `data-search="off"` のページでは出ない（白地のページはどれも `off`）。

**未マージの work/ ブランチの重なり**（`git branch -r --no-merged origin/cloudflare`、2026-11-04）:

| ブランチ | ファイル | 中身 | 取り込むときの解き方の見込み |
|---|---|---|---|
| work/1029-lgn-page | `navbar.js` | if の連なりの先頭に `/log/`（印は「リソース」）を足す | if の連なりに行を足すだけ。新しいナビはこの連なりの値で出し分けるので、`/log/` も自動で新しいナビになる |
| work/1029-lgn-page | `style.css` | log/ の節（259行の追加） | 別の節。新しいナビの節はファイルの末尾に足すので、末尾どうしの追記の衝突なら両方を残す |
| work/1101-prp-pub | `navbar.js` | 「連盟プロ」の行き先 `/jpml_pros.html` → `/pro/`、pro/ の分岐のコメント | 項目の行き先の1行。新しいナビは項目を `document.write` の1か所から読むので、この1行が入ればシートにも反映される |
| work/1101-prp-pub | `style.css` | pro/ の節（+45/−1） | 別の節 |

RYO 系列の公開（「良栄」をドロップダウンから `/r/` へのリンクに）も、項目の1か所が変われば新しいナビに反映される（ドロップダウンでもリンクでも読めるように作る）。

### 2. 実装（bce74d878・1b0d46fce・5c44b015c）

| 場所 | 中身 |
|---|---|
| `navbar.js` | 黒地のナビの部分（`document.write`・`newNavCurrent` の if の連なり・白地の書き換え）は変えない。末尾に `mjNavBand()` を足し、`newNavCurrent` があるときだけ、描いたナビの項目（`.navbar-nav > .nav-item`、ドロップダウンの中身も）を読み取って `window.mjNavItems` に置き、帯（ロゴ・「質問」・「≡」）に置き換えて `assets/nav.js` を読む。項目は `document.write` の1か所のまま（他のブランチの項目の変更がそのまま流れる） |
| `assets/nav.js`（新規） | まとまり（選手・大会・動画・その他）の定数と、項目をまとまりに分ける `groupItems()`（定数に無い項目は「その他」へ）、質問の context を決める `askContext()`。≡ のシートと質問のシートは `.mj-houou-sheet`・`.mj-houou-scrim` を共有し、背面の inert・Tab の循環・Esc／背景／閉じるで閉じる・押したボタンへフォーカスを戻す |
| `assets/top_ask.js` | 欄の中のクラスで部品を探す `initAsk(root)` に分け、トップの欄とナビの質問のシートの両方で動かす。context は欄の `data-context`（無ければ null） |
| `scripts/generate_top_page.py`・`assets/top.js`・`assets/top.css` | 「開催中・直近の大会」の面と一行の説明、それだけが使っていたコード・定数・CSS を消す（予定のシートの読み込みも消えた。誕生日・新着の番組のシートは読む） |
| `style.css` | 末尾に「新しいナビ」の節（帯・シートの行・質問のシートの中の欄） |
| `assets/ryoei/world.css` | r/ でシートが透けないよう、ナビが読む `--mj-n-page`・`--mj-n-muted`・`--mj-n-scrim` を r/ の夜の色で足す |
| `scripts/tests/test_top_page.py` | 大会日程の検査を消し、ナビの検査を足す（navbar.js の項目がすべてシートに出る・定数に無い項目は「その他」・今の項目の印・子の印・context・注意書きと大会名が生成と同じ・並び〈入力欄→お気に入り→誕生日→新着の番組〉） |
| docs | `docs/notes/top-page.md`・`docs/notes/design.md`（共通ナビ〈新しい形〉の行）・`docs/notes/static-generation.md`（top/ の行）・`docs/decisions/top.md` |

### 3. 検証

- `python3 -m unittest discover -s scripts/tests`: 1797件 OK（1b0d46fce の時点）。5c44b015c で `test_top_page.py` に1件足し OK
- 修正前のコードで通らないこと: 子の印の検査は、子の `current` を読まない前の `groupItems()` では落ちる形（子の行の印が常に false）
- `regenerate-page.yml` を作業ブランチで手動実行:
  - dry_run・all（run 41662800947、bce74d878）: 成功。変わったファイルの一覧はステップの要約にだけ出て、セッションから読めなかった（check-run の output・annotations にも無い）。代わりに、この差分で生成のコードが変わったのは `scripts/generate_top_page.py` だけ（共有の `scripts/lib/`・ほかの `generate_*.py` は変えていない。`regenerate.py` はコメントのみ）ことで、古い表のページの生成物が変わらないことを確かめた
  - 本実行・top_page（run 41665642757、1b0d46fce）: 成功。コミット 51d8a1456 は `top/index.html` だけ（+9/−51。大会日程の面と一行の説明が消えた）
- 画面（ローカルの静的サーバ＋Playwright、UA に mj-internal、`/api/ask` は差し替え）:

| ページ | 360・400・1280／明・暗 | 結果 |
|---|---|---|
| top/ | 6通り | 帯（高さ48px）。大会日程の面・一行の説明なし。「質問」は上部の入力欄へフォーカス（シートは開かず、aria-haspopup なし）。≡ のシートは4まとまり19行 |
| houou/ | 6通り | 帯。≡ で「鳳凰戦」に印。質問のシートは `{contest:"all", context:"houou"}` を送り、答え・選手のカードが出る。Esc・背景でフォーカスが押したボタンへ戻る |
| pro/ | 6通り | 帯。「連盟プロ」に印。context は null |
| saikyo/・live/・r/ | 360 明 | 帯。saikyo/ は「最強戦」に印・context saikyo。r/ はシートが不透明になった（修正前は透けていた） |
| title/ | 6通り | 黒地のナビのまま（白地の対象に無いため。`## 報告` に書く） |
| jpml_pros.html | 6通り | 黒地のナビのまま（高さ56px／PC 80px で修正前と同じ）。ページの JS エラーなし |

### 4. 自己レビュー（新しい文脈のサブエージェント、2回）

| 指摘 | 扱い |
|---|---|
| シートの Tab の循環に `summary`（注意書き）が入らない（nav.js） | 直した（1b0d46fce） |
| r/ でシートが透ける（world.css に `--mj-n-page`・`--mj-n-muted`・`--mj-n-scrim` が無い） | 直した（1b0d46fce） |
| ドロップダウンの子に今の項目の印が付かない | 子の行き先と今のパスが同じなら印を付けた（1b0d46fce）。2回目: 子の行き先はどれも黒地のページなので帯の上では付かない → 親だけが今の項目（live/ の「番組」・r/ の「良栄」）のときは印を出さない上限として `簡略化:` を残し、検査を足した（5c44b015c）。仮置きに書く |
| top_ask.js を読み込む前に送れる | 読み込むまで送るボタンを押せなくした（1b0d46fce）。読み込みに失敗したときは押せないまま（`簡略化:`、5c44b015c） |
| top/ の「質問」がダイアログと読み上げられるのにシートを開かない | top/ では aria-haspopup・aria-expanded を外す（`簡略化:`、1b0d46fce）。2回目: 入力欄を nav.js の読み込み時に1回だけ探すと、本文の解析前に動いたとき見つからない → 解析の後と押したときに探す（5c44b015c） |
| 大会日程を消した後の古いコメント（regenerate.py・generate_top_page.py・top-page.md）、コード中の Chat-Ref | 直した（1b0d46fce） |
| r/ でシートの地と行のまとまりが同じ色 | 意図どおり（r/ の夜の色に合わせた。差を付けるかは見た目の判断として仮置きに書く） |

### 5. 仮置きの一覧（プレビューで決めてもらうもの）

- PC 幅でも同じ細い帯（≡ のシートは中央に最大幅 560px）
- top/ の「質問」はシートを開かず、上部の入力欄へ移る
- まとまりの行の補い: 「その他」のように出どころが2つ以上のまとまりだけ、行に出どころの名前（リソース・良栄）を添える
- 今の項目の印は行の文字の色。ドロップダウンの中の行は、その行き先のページにいるときだけ（live/・r/ では印が出ない）
- r/ のシートの色は r/ の夜の色（地と行のまとまりが同じ色）
- 帯の「質問」は文字、「≡」はアイコン（読み上げは「メニュー」）

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-TOP-12
- ブランチ: work/1104-top（指示文の指定どおり。セッションに割り当てられた claude/… は使っていない）
- ログ: https://github.com/retroeater/mj/blob/work/1104-top/docs/logs/CHAT-1104-TOP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-top
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: top/・houou/・pro/・title/・jpml_pros.html。この版の印: top/ に「開催中・直近の大会」の面が無く、上の細い帯の右に「質問」と「≡」
- マージ: 未（平野さんの判断待ち。プレビューを見て決める） → 済（e27f2c52a、CHAT-1104-TOP-14 で TOP-11〜14 をまとめてマージ）
- issue: #101, #231（1行コメントのみ。本文・状態は変えていない）
- 判断が必要なこと:
  - title/ は `navbar.js` の白地の対象に入っていない（パスの一覧に無く、body の目印も無い）ため、新しいナビにしていない。決定の例「title/」と食い違う。title/ を白地の対象に足すか（足すと title/ の見た目も白地のナビの形になる）
  - #231 を閉じるか（トップの大会日程の面をやめたので、(a) は第一弾に入らなくなった）
  - 仮置きの一覧（`## 経過`「5. 仮置きの一覧」）: PC 幅も同じ帯／top/ の「質問」は入力欄へ移る／その他の行の補い／ドロップダウンの中の行の印（live/・r/ では出ない）／r/ のシートの色／「質問」は文字・「≡」はアイコン
- 未確認の項目:
  - dry_run・all で変わったファイルの一覧（ステップの要約にだけ出て、セッションから読めない。生成のコードが変わったのは generate_top_page.py だけであることで代えた。Actions の run 41662800947 の要約で見られる）
  - 本番の質問の Worker に横断の質問を送る確かめ（プレビュー・本番とも送っていない。ローカルでは差し替えた応答で確かめた。Worker は変えていない）
  - ブラウザでの見え方（プレビューは HTML・JS の版までを確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7323386）: https://github.com/retroeater/mj-logs/tree/main/guide/a7323386

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
