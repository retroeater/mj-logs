# CHAT-1101-PRP-04

- 着手日時: 2026-11-01
- 対象issue: #641・#159・#139
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 34d74074e

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その2: 旧 URL の転送・選手のリンクの向け替え・検知の付け替え）を同じ作業ブランチに足し、プレビューで止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-04
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1101-PRP-03 の完了報告（判断待ち）の後。PRP-03 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-03 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に ` / 続き: CHAT-1101-PRP-04` を足して push する。

## 目的
pro/ の公開（#641）のうち、PRP-03 で残した転送・リンクの向け替え・検知の付け替えを、同じ作業ブランチに足してプレビューで確かめられる状態にする。番号は CHAT-1101-PRP-01 のログの `## 経過`「2. 棚卸しの表」（以下「表」）の番号。決定の丸数字は docs/decisions/pros.md の 2026-11-01 の決定（CHAT-1101-PRP-02 で記録）。

### 決定（2026-11-01、平野さん）
- PRP-03 の「判断が必要なこと」の並べ替えのボタンの幅: 今の形（高さだけ 44px、幅は列の幅）でよい
- 前の決定から（pros.md に記録済み）:
  - ③ `jpml_pros.html` は公開と同時に `/pro/` へ 301。X の画像のリンク切れの検知（#139、`check_image_links.py`）の対象も同時に pro/ へ移す。要らなくなるものの片付けは #645（この指示ではしない）
  - ④ `?name=` の旧 URL は Worker の 301。範囲は `jpml_pros.html?name=` だけ。旧名は「別名」の表から引き、在籍者でない名前は `/pro/` のトップへ
  - ⑨ 順位変動の `playerUrl()` と title/ の優勝者のリンクを、公開のときに pro/ の個別ページへ向け替える
  - K8: pro/ のトップの質問欄の答えに出た選手のカードの行き先を、pro/ の個別ページに（個別ページには質問欄が無いのでトップだけ。CHAT-1031-PRO-09 の読み替え）

### 前提（チャット側。平野さんの決定ではない）
- 表7・8・15・17・18 と PRP-01 のログの「入るリンク」の一覧に、今の場所がある（例: Worker `mj` は `wrangler.jsonc` に `main` が無く静的アセットだけ。名前→slug は `pro/search.json` の1つ目と9つ目の値。`assets/houou_ask.js:130` は `data-players-page` + `?name=`。`assets/league_race.js:65` の `playerUrl()` は X。`generate_title_pages.py:740-742` の優勝者は X。`scripts/check_image_links.py:31` の `TARGET_HTML`）。要確認。食い違えば実物に従ってログに書き、どこを変えるか決められなければ止まる
- Worker の 301 は、**Worker が `/jpml_pros.html` へのリクエストだけで動く形**（静的アセットの設定で Worker を先に通すパスを絞れるか。`docs/notes/cloudflare.md` の配信設定・`html_handling`・`_redirects` との順番）を先に調べる（要確認。チャット側は確かめていない）。全リクエストで Worker が動く形しか無いときは、作らずに止まり、Workers の課金と速さへの影響の見込みを報告する
- 名前→slug の対応表は、pro/ の生成と同じ流れで毎回作り直す（シートの変化に追いつくため）。旧名は「別名」の表から。在籍者でない名前・表に無い名前は `/pro/`。`?name=` の無い `jpml_pros.html` も `/pro/` へ 301（`_redirects` で行うか Worker で行うかは順番を調べて決め、`docs/notes/redirects.md` の一覧と `scripts/tests/test_redirects_inventory.py` に合わせる）
- title/ の優勝者・順位変動のアイコンは、在籍者（pro/ に個別ページがある人）だけを個別ページへ向け、ほかは今の行き先のまま（チャット側の案。`docs/decisions/title.md` の優勝者の行は PRP-02 が注記した）
- `houou_ask.js` は5大会の質問欄の共通部品。pro/ のトップ以外の質問欄の動き（行き先）は変えない
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）。転送の確かめはローカルの `wrangler dev`（CLAUDE.md「禁止事項」の起動の形）とプレビューで行う

## 手順
1. 転送: Worker `mj` に `jpml_pros.html?name=` の 301 を足す（前提の調べを先に行い、`## 経過` に結果を書く）。対応表の生成とテスト（`node --test 'workers/<名前>/test/*.test.mjs'`、ほかに Python の生成のテスト）。`?name=` 無しの `jpml_pros.html` の 301。`docs/notes/redirects.md`・`docs/notes/cloudflare.md`・#159 の設計の記述を直す
2. 向け替え: K8（pro/ のトップの質問欄のカード）・`playerUrl()`（`lib/houou_race.py` のデータに slug が要るなら足す）・title/ の優勝者。関係するページを再生成し、pro/ と順位変動と title/ 以外の生成物に差分が出ないことを確かめる
3. 検知の付け替えと確かめ: `check_image_links.py`・`check-image-links.yml` の対象を pro/ に移す（ワークフローを変えるので、CLAUDE.md「ブランチ運用」のとおり作業ブランチで手動実行して確かめる）。そのうえで次を `## 経過` に書く:
   - テスト（`python3 -m unittest discover -s scripts/tests` と workers のテスト）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   - ローカルとプレビューで、`/jpml_pros.html`・`/jpml_pros.html?name=<在籍者>`・`?name=<旧名>`（別名の表から1件）・`?name=<在籍者でない名前>`・`?name=<存在しない名前>`・`?name=` に記号や空白を含む値の6通りの状態コードと行き先。Worker が `/jpml_pros.html` 以外のリクエストで動いていないこと
   - 向け替えた3か所で、在籍者は個別ページ・在籍者でない人は今の行き先になっている例を各1件以上（名前つき）
   - 生成物の差分を種類ごとに数えた表（pro/・houou/race/・title/ のほかに差分が出たら止まる）
   - 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

## 止まる条件
- 0章の確かめで、PRP-03 のログの状態が「判断待ち」でない
- Worker を `/jpml_pros.html` だけで動かす形が無い（作らずに見込みを報告する）
- 新しい依存（npm・pip のパッケージ、外部 API）が要る
- 前提の場所が食い違い、どこを変えればよいか決められない
- pro/・houou/race/・title/ 以外のページの生成物に、この指示の変更で説明できない差分が出た
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- ワークフローの手動実行が失敗した（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- CHAT-1101-PRP-03 のログの状態は「判断待ち」だった。末尾に「 / 続き: CHAT-1101-PRP-04」を足して push した（daf90ded1）
- 同じ Chat-Ref のコミット: なし。作業ブランチ `work/1101-prp-pub` はローカル・リモートとも 34d74074e で一致。`origin/cloudflare` は祖先でなかったので merge で取り込んだ（1a24eb9c4、衝突なし）
- #641 にほかのセッションの着手中は無い（最後は PRP-03 の判断待ち）。着手中のコメントを書いた
- 前提の実物の場所: `wrangler.jsonc` に `main` は無い（静的アセットだけ、`html_handling: none`・`not_found_handling: 404-page`）。`pro/search.json` の1つ目が名前・9つ目が slug。`assets/houou_ask.js:130` の `data-players-page` + `?name=`。`assets/league_race.js:65` の `playerUrl()` は X。title/ は `generate_title_pages.py:734-742` の `member_card_html()`（期ページの決勝に出た全員の写真カード）が X。`scripts/check_image_links.py:31` の `TARGET_HTML`。すべて前提どおり

### 1. 転送（Worker の 301）: 調べた結果、作らずに止めた

Cloudflare の公式の文書（2026-11-01 に読んだ: Static Assets の Configuration and Bindings・Worker script・Redirects・Headers・Billing and limitations・概要の Routing behavior）:

- `assets.run_worker_first` は経路の配列にできる（「You can also specify `run_worker_first` as an array of route patterns to selectively run the Worker script first only for specific routes.」）。`["/jpml_pros.html"]` で、そのパスだけ Worker を先に通せる
- **ただし `main`（Worker のコード）を持つと、どの静的アセットにも当たらない要求はすべて Worker に渡る**（「If no matching asset is found and a Worker script is present, the request will be processed by the Worker.」）。`run_worker_first` はアセットより先に通す経路を足すだけで、当たらない要求（404 になるもの）を Worker から外す書き方は文書に無い。今は `main` が無いので、これらは Worker を通らずに `404.html` が返っている
- 静的アセットへの要求は課金されない（「Requests to static assets are free and unlimited. Requests to the Worker script ... are billed according to Workers pricing.」）。`_redirects` と `_headers` は Worker が作った応答には当たらない（「Redirects defined in the `_redirects` file are not applied to requests served by your Worker code」「Custom headers defined in the `_headers` file are not applied to responses generated by your Worker code」）
- クエリ文字列が `run_worker_first` の照合に関わるかは文書に書かれていない（アセットの照合はパスだけ、とだけある）

結論: **Worker `mj` を `/jpml_pros.html` だけで動かす形は無い**（`/jpml_pros.html` に加えて、存在しないパスへの要求すべてで Worker が動く）。止まる条件のとおり、Worker のコード・`wrangler.jsonc`・`_redirects` の `jpml_pros.html` の行（`?name=` の転送と一緒に決めるため）は作っていない。`docs/notes/redirects.md`・`docs/notes/cloudflare.md`・#159 の記述も変えていない。見込みと案は `## 報告` の「判断が必要なこと」に書いた。

手順2・3は Worker と独立しているので、続けて同じブランチに足した（Worker の判断が出るまで、このブランチは公開できない）。

### 2. 向け替え

- K8（pro/ のトップの質問欄のカード）: `assets/houou_ask.js` に `playerHref()`・`slugMap()` を足した。質問欄の `data-player-pages`（pro/ のトップだけが持つ。値は `search.json`）があれば索引を読み、名前に slug があれば `/pro/<slug>/`、無ければ今の `data-players-page` + `?name=`。索引が読めなくてもカードは出す。ほかの4大会と最強戦の質問欄は `data-player-pages` を持たないので行き先は変わらない（`generate_pro_pages.py` の `top_ask()` に属性を足した）
- 順位変動の `playerUrl()`: `scripts/lib/pro_search.py`（新しい共通の部品。`pro/search.json` から名前 → slug）を足し、`generate_houou_pages.py` の `add_pro_slugs()` が順位変動の表の選手に slug を足す。`assets/league_race.js` は slug があれば `/pro/<slug>/`（同じタブ、aria-label「<名前>の連盟プロのページ」）、無ければ今の X（新しいタブ）。`league_race.js` と `generate_houou_pages.py` は houou/・ouka/・jwrc/・wrcr/ の4大会の順位変動の共通の部品なので、4大会とも変わる（指示の手順3は「pro/・houou/race/・title/ のほかに差分が出たら止まる」、止まる条件は「この指示の変更で説明できない差分」。前提・ルールの側〈止まる条件〉を優先し、4大会の race/ の差分はこの変更で説明できるものとして扱う。判断が必要なことに書いた）
- title/ の優勝者: `member_card_html()` が期ページの写真カード（決勝に出た全員。優勝者だけのカードは無い）を、個別ページのある在籍者は `/pro/<slug>/`（同じタブ）、ほかは今の X（新しいタブ）、X も無ければリンクなし、にした。優勝者だけでなく決勝の全員に当たる（同じ並びのカードで行き先が分かれないように。判断が必要なことに書いた）。大会ページの優勝者のカードはもとから期ページへのリンクで、変えていない
- `簡略化:`（`lib/pro_search.py`）: pro/ は `RUN_LAST` でほかのページの後に作るので、順位変動と title/ は前の生成の `pro/search.json` を読む。新しく在籍した選手は次の生成まで今の行き先のまま
- 文書: `docs/notes/houou-race.md`・`houou-top.md`・`title-pages.md`・`pro-pages.md`

### 3. 検知の付け替え

- `scripts/check_image_links.py` の対象を `pro/index.html` の表の行の X の画像に（画像の alt は空なので、隣の名前の欄で「<名前> X」）。今の pro/ で 857件（pbs.twimg.com 845・abs.twimg.com 12）。前の `jpml_pros.html` は X・note・YouTube の画像（約1,140件）を見ていた。note・YouTube の画像はジョブ `sns_book`（SNS ブックの全行）が見ている。issue #352 の節の見出しを「### 連盟プロ」に
- `.github/workflows/check-image-links.yml` はコメントだけ直した。`docs/notes/github-actions.md` の一覧の行も直した

### 4. テスト

- `python3 -m unittest discover -s scripts/tests`: 1,747件 OK
- 足したテスト（`test_title_member_links.py` 4本・`test_houou_ask.PlayerHrefTest` 3本・`test_check_sns_book_images.ExtractProImagesTest` 2本・`test_houou_pages.ProSlugTest` 2本・`test_pro_pages.PublishTest.test_top_meta` の追加分）は、この指示の前のコード（HEAD の時点の scripts/・assets/ を一時ディレクトリに出し、新しいテストだけを置いた）で、それぞれ FAILED（errors=4・errors=3・failures=2・errors=1・failures=1）
- workers のテストは、workers/ を変えていないので回していない
- 未マージの `work/*` の各ブランチと、触ったファイルの重なりは0件。取り込みの衝突の件数も origin/cloudflare との間と同じ（増えていない）

### 5. 生成と確かめ

- 新旧のコードの比べ: `regenerate-page.yml` を `all`・`dry_run`・`compare_ref: 1a24eb9c4`（この指示のコードの前）で手動実行（run 41275107904、success）。中身の違うファイル 434件の内訳: `houou/race/` 61・`ouka/race/` 20・`jwrc/race/` 19・`wrcr/race/` 7・`pro/index.html` 1・title/ の期ページ 326（gpmax 16・houou 40・joryu-nihon-series 11・judan 40・jwrc-r 7・jwrc 19・kouryu 4・masters 22・nihon-series 11・oui 37・ouka 20・ourai 12・pro-queen 23・ranwa 6・shinjinou 32・shouryu 2・tatsujin 3・teiou 5・wakajishi 12・wrc 4）。**pro/・4大会の race/・title/ のほかに差分は0件**
- 生成: `regenerate-page.yml` を `houou_pages ouka_pages jwrc_pages wrcr_pages title_pages pro_pages` で手動実行（run 41276465494、success）→ eebdfc793（435ファイル: 上の 434件と `sitemap-title.xml` の lastmod）
- 生成物の実データ:
  - 順位変動の JSON の選手 17,458件のうち slug あり 13,776・X だけ 41・どちらも無し 3,641。例: 在籍者 和久津晶 → `/pro/akira.wakutsu.23/`（`ouka/race/7-0.json`）、在籍者でない 藤川まゆ → X のまま（同じファイル）
  - title/ の写真カードのリンク: pro/ 938・X 164・期ページ（大会ページの優勝者のカード）364。例: 在籍者 優勝 夏目一花 → `/pro/ichika.natsume.38/`（`title/ourai/12.html`）、在籍者でない 2位 藤川まゆ → X（`title/shouryu/2.html`）
  - K8: `pro/index.html` の質問欄に `data-player-pages="search.json"`。実際の `pro/search.json` で `playerHref()` を動かすと、在籍者 白鳥翔 → `/pro/sho.shiratori.23/`、在籍者でない 藤川まゆ → `/houou/players/?name=…`（答えの文を出す `/api/ask` はプレビューに無いので、カードの画面は見ていない）
- プレビュー（版ごとの URL、464e4bafd の check-run は success）を headless Chromium で: `houou/race/` の既定の表で pro/ へのアイコン 16件（例「吉野敦志の連盟プロのページ」→ `/pro/atsushi.yoshino.26/`、同じタブ）、`ouka/race/?term=7-0` で 15件。`title/ourai/12.html` の4枚はすべて pro/ へ（同じタブ）。`/pro/sho.shiratori.23/` は 200
- 画像の検知: `check-image-links.yml` を作業ブランチで `dry_run: true` で手動実行（run 41275111941、check・saikyo・sns_book とも success）。ジョブ check は「857件の画像URLを確認します」「確認: 857件 / 失敗: 0件」、issue は書かずに本文だけ出した（節の見出し「### 連盟プロ」）。#352 は変わっていない（最後の更新 2026-10-31）
- テスト: `python3 -m unittest discover -s scripts/tests` 1,750件 OK。workers/ は変えていない（レビューのサブエージェントが `node --test 'workers/*/test/*.test.mjs'` も通ることを確かめた）
- 本番（ryoei.pro）へのリクエスト: 0件

### 6. 差分のレビュー（新しい文脈のサブエージェント、1回）

渡したもの: 目的・決定・`git diff 1a24eb9c4 HEAD`（ログを除く）・CLAUDE.md「コード規約」。高の指摘は無し。

| 指摘 | 扱い |
|---|---|
| `check_image_links.py` の `PRO_ROW` が生成の形に依存し、形が変わると0件で「切れなし」として常設 issue を閉じる（中） | 直した（0件なら終了コード1。テスト `test_no_images_is_failure`） |
| `ExtractProImagesTest` の HTML が手書きで、生成の変化を拾えない（中） | 直した（`test_pro_pages.test_image_check_reads_top_rows` が `top_row()` の出力に当てる） |
| 再生成した生成物が差分に無い（中） | 直した（上の 5. の生成・実データの確かめ） |
| 名前の照合が完全一致。`houou/race/38-2.json` の「水口 崚」（空白あり）は pro/ の「水口崚」と合わず X のまま（低） | 意図どおり（照合を緩めず、元のシートか「別名」で直すのが筋。判断が必要なことに書いた） |
| slug の位置 8 を3か所で持つ（低） | 直した（正を `lib/pro_search.py` の `NAME`・`SLUG` とし、`search_index()` の docstring で参照、`test_search_index_positions` で固定。JS の `PRO_SLUG` はコメントで `search_index()` を参照） |
| `/pro/<slug>/` の形を3か所で作る（低） | 意図どおり（Python と JS で分かれる。各所のコメントで決定⑨を参照） |
| `"search.json"` を2か所に書く（低） | 直した（`SEARCH_FILE`） |
| `data-player-pages` と `data-players-page` の名前が紛らわしい（低） | 見送り（必須ではない。今の名前で意味は通る） |
| `lib/pro_search.py` は索引が壊れていると例外で4大会と title/ の生成が止まる（低） | 意図どおり（自分の生成物が壊れたら止めて気づく方がよい） |
| `check_image_links.py`・`check_sns_book_images.py` の docstring が古い（低） | 直した |
| `docs/notes/title-pages.md` のデータの節に「X ID があれば X のリンク」が残る（低） | 直した |
| `playerUrl()` に JS のテストが無い（低） | 見送り（三項演算子だけ。データ側の `add_pro_slugs()` にテストがある） |

## 報告

- 状態: 判断待ち / 続き: CHAT-1101-PRP-05
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページは `houou/race/`・`ouka/race/?term=7-0`・`title/ourai/12.html`・`pro/sho.shiratori.23/`。この版で見える印: 順位変動の選手のアイコンを押すと連盟プロの個別ページへ同じタブで移る（前は X を新しいタブ）
- マージ: 未（指示のとおり。マージが pro/ の公開になる）
- issue: #641（着手中のコメント）
- 判断が必要なこと:
  - **`jpml_pros.html?name=` の 301 を作っていない（止まる条件）。** Cloudflare の文書では、サイトの Worker `mj` に `main` を足すと、`run_worker_first: ["/jpml_pros.html"]` でそのパスを先に通せるが、どの静的アセットにも当たらない要求（存在しないパス・404 になるもの全部）も Worker に渡る（「If no matching asset is found and a Worker script is present, the request will be processed by the Worker.」）。`/jpml_pros.html` だけで動かす形は無い。見込み: 静的アセットへの要求は無料のまま、Worker に渡る要求（`/jpml_pros.html` と 404 の全部）だけが Workers の要求として数えられる（Workers Paid は月1,000万件が含まれ、超えた分は100万件あたり $0.30。今の 404 の件数はダッシュボードで見る必要があり、ここからは測れない）。速さは、その要求だけに Worker の実行の分がのる。加えて、Worker が作った応答には `_headers`（CSP などのセキュリティのヘッダ）と `_redirects` が当たらないので、Worker が 404 を返す道でヘッダが抜けないかの確かめが要る。案:
    - A. `mj` に `main` と `run_worker_first: ["/jpml_pros.html"]` を足す（上の 404 の件とヘッダの確かめが要る）
    - B. mj-ask（`ryoei.pro/api/*` のルートだけで動く別の Worker）と同じ形で、ルート `ryoei.pro/jpml_pros.html*` だけで動く小さな Worker を別に作る（`workers/<名前>/`。サイトの `mj` は静的アセットだけのまま。Workers Builds への接続は平野さんのダッシュボードの作業。名前 → slug の表は生成で `workers/<名前>/` のデータに書く）。Code はこの案を推す
    - C. saikyo/ の `?q=`（#627）と同じく `jpml_pros.html` に JS の移動を残す（301 ではないので決定④と違う）
    どれにするかと、`?name=` の無い `/jpml_pros.html` の 301（`_redirects` で行うか、上の Worker で行うか）を決めてほしい。決まるまで、このブランチは公開できない（`jpml_pros.html` はサイトマップから外れ、navbar からも外れているが、ページは今のまま残っている）
  - 順位変動の向け替えは houou/ だけでなく ouka/・jwrc/・wrcr/ の順位変動にも当たる（`league_race.js` と生成が4大会の共通の部品）。指示の手順3「pro/・houou/race/・title/ のほかに差分が出たら止まる」と止まる条件「この指示の変更で説明できない差分」が食い違うため、ルールの側（止まる条件）を優先して4大会とも変えた。よいか
  - title/ の向け替えは、期ページの写真カード（決勝に出た全員）に当てた。優勝者だけのカードは無く、同じ並びのカードで行き先が分かれないようにした。優勝者だけにするなら直す
  - 名前の表記の揺れ: `houou/race/38-2.json` の「水口 崚」（空白あり。pro/ は「水口崚」）は slug が付かず X のまま。照合は完全一致のままにした。「鳳凰」シートか「別名」で直すかを決めてほしい
  - `docs/notes/static-generation.md`「ページの一覧」の pro/ の記述は、PRP-03 の判断待ちのとおり公開のマージの時点で直す
- 未確認の項目:
  - `jpml_pros.html` と `?name=` の6通りの状態コードと行き先（Worker を作っていないため）
  - pro/ のトップの質問欄の答えのカード（`/api/ask` はプレビューに無い。関数は実データで確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f1d4a0a0）: https://github.com/retroeater/mj-logs/tree/main/guide/f1d4a0a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
