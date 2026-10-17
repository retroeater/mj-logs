# CHAT-1017-AMP-06

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1017-amp
- 着手時HEAD: 98ff33c

## 指示

【Claude作成】Claude Code 向け指示：jpml_pros の鳳凰戦の列を減らす（「鳳凰出場」「鳳凰最高」を消し、「鳳凰43後」を見出し「鳳凰」の現在のリーグにして個人成績の期別へリンク）、プレビューで止まる Chat-Ref: CHAT-1017-AMP-06 マージ: 判断待ちで止まる（見た目を変えるため、平野さんがプレビューを見てから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1017-AMP-05 は完了。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-amp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-amp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-amp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#584 の残り（jpml_pros の分）。`jpml_pros` の表の鳳凰戦の列を1列にし、行き先を Ampai から個人成績の期別に変える。表が横にはみ出しているので、列を減らす方針の1歩目にする。
決定（2026-10-16〜17、平野さん）

* `jpml_pros` の「鳳凰出場」「鳳凰最高」の列を消す（2026-10-16）
* 「鳳凰43後」（最新の期）のリーグのリンクの行き先を、今の Ampai から個人成績の「期別」タブ（`houou/players/?name=`）に変える。Ampai へ行きたい人はそこから行く（2026-10-16）
* この列の見出しを「鳳凰」にし、値は個人成績の名前の横のチップと同じ「現在のリーグ」にする（前期の B1 で昇級して後期に出ない中村毅・吉野敦志は「B1」）（2026-10-17。期の名前を見出しに出さない）
* 桜花の3列（桜花出場・桜花21期・桜花最高）は今回は変えない。女流桜花の個人成績のページ（ouka/）ができてから同じ形にする（2026-10-17）
* `jpml_pros` の表は徐々に表示内容を減らし、1画面に収まる最小限の内容にする。網羅は新設の個人ページで行う（2026-10-16。AMP-01 で docs/decisions/pros.md に記録済みのはず〈要確認〉）

前提（チャット側。平野さんの決定ではない）

* 現在のリーグの決め方は、AMP-04 で `scripts/generate_houou_pages.py` に作った `current_league()`（個人成績の名前の面・質問欄のカードと同じ）を使い、`jpml_pros` の生成から呼ぶ（共有の部品に移すなら `scripts/lib/` へ）。在籍者全員で、`jpml_pros` の「鳳凰」の値と `houou/ask-players.json` のチップが一致することをテストで確かめる
* リンクは値があるセルだけ。行き先は既存の定数 `HOUOU_PLAYERS_URL`（`houou/players/?name=<選手名>`、`view` なし）を使う（要確認）。値が無い在籍者のセルは空欄でリンクなし
* 「鳳凰」の列の並べ替え（あれば）は、今の「鳳凰43後」の列と同じ並べ方（リーグの順）のまま。検索欄の placeholder・aria・説明文に「鳳凰43後」など期の名前が出ていれば、「鳳凰」の列に合わせて直す（どこを直したかを書く）
* 鳳凰戦の Ampai の URL は `jpml_pros` では使わなくなる。桜花の Ampai の URL（`lib/ampai.py`）と `check_ampai.py`・個人成績の期別のリンクはそのまま
* 変えないもの: 桜花・最強戦・放送対局などほかの列、houou/ のページ、`workers/ask/data.json`

手順

1. `python3 scripts/regenerate.py --check jpml_pros` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（CHAT-1017-AMP-06）を書く。今の `jpml_pros.html` の鳳凰戦の3列の作り（生成のどの関数・JS・CSS・説明文が関わるか）と、375px・1280px での表の幅（`scrollWidth`）を `## 経過` に書く
2. 上の「決定」「前提」のとおりに直してテストを足し、`jpml_pros` を生成し直す。`## 経過` に次を書く: 「鳳凰」の列の値が今の「鳳凰43後」と違う選手の名前の一覧（今 → 直した後。見込みは AMP-03・04 のチップの変化と同じ形で、43前のリーグが出ていた選手は 43後のリーグに、43後に出ていない選手は空欄に。中村毅・吉野敦志は B1）、`houou/ask-players.json` との食い違いの数（0 のはず）、生成物の差分の種類、表の幅の変化（375px・1280px）
3. プレビューで 375px 相当の明・暗と 1280px で、`jpml_pros` の表の先頭と、中村毅・末続ヒロトシ・白鳥翔の行を確かめ、「鳳凰」の列のリンクを押すと `houou/players/?name=` の期別が開くことを書く。docs/notes/static-generation.md（jpml_pros の成績の列）・docs/notes/houou-top.md（jpml_pros からのリンク）・docs/decisions/pros.md を直す。#584 に直した点のコメントを書く（本文・状態・ラベルは変えない）。判断が必要なことに「この見え方でマージしてよいか」を挙げる

止まる条件

* 手順1 の止まる条件、他セッションの着手中コメントがある
* 「鳳凰」の列の値と `houou/ask-players.json` のチップが1名でも食い違う
* 生成物の差分が `jpml_pros.html`（と生成スクリプト・JS・CSS・テスト・docs）と、シートの変化で説明できるもの以外に出る。houou/ のページ・`workers/ask/data.json` が変わる
* 未マージの work/ ブランチが `scripts/generate_jpml_pros.py`・`scripts/lib/pro_stats.py`・`scripts/generate_houou_pages.py` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* プレビューの「Workers Builds: mj」が付かない・失敗する（失敗が Cloudflare の一時的なエラーなら原因を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。`CHAT-1017-AMP-06` のコミットは無い。識別子 AMP はこのセッションのもの
- 雛形の行はあるが、冒頭の行（題〜作業ブランチ）が改行なしの1段落で貼られていた（指示欄はそのまま写した）
- work/1017-amp はローカル・リモートとも無かったため `git checkout -b work/1017-amp origin/cloudflare`（98ff33c）

### 1. 検査・今の作り

- `python3 scripts/regenerate.py --check jpml_pros`: 通る
- #584 に着手中のコメントを書いた。他セッションの着手中コメントは無し
- 未マージの work/ ブランチ: work/1017-pad が `scripts/generate_jpml_pros.py` の `PAGE_TEMPLATE`（`<body>` に `class="mj-table-page"` を足す）と `jpml_pros.html`・`style.css` を変えている。この指示で直す関数（`HEADERS`・`build_row_html()`・`build_page()`）とは別の箇所なので止まらない。マージの順によっては `jpml_pros.html`（生成物）が衝突しうる（生成し直して解く）

今の鳳凰戦の3列の作り:

| 列 | 生成（`scripts/generate_jpml_pros.py`） | 値の元 | リンク |
|---|---|---|---|
| 鳳凰出場 | `HEADERS`・`build_row_html()` の `get_houou_seasons()` | `lib/pro_stats.py` の `Stats.houou_seasons`（「表示」Y の行数） | `HOUOU_PLAYERS_URL?name=`（個人成績の期別） |
| 鳳凰43後 | 見出しは `HEADERS` の `{houou_period}`（`pro_stats.periods()` の最新の期）。値は `get_houou_latest_league()` | `Stats.houou_latest`（「表示」を問わない最新の (期, 前後) の行のリーグ） | Ampai（`pro_stats.ampai_urls()`。合う行が無ければリンクなし） |
| 鳳凰最高 | `get_houou_highest_league()` | `Stats.houou_highest` | `HOUOU_PLAYERS_URL?name=` |

- 検索欄: `PAGE_TEMPLATE` の `league_filter`（placeholder「鳳凰43後」、label「鳳凰戦43期後期の所属リーグで検索」）。行の `data-league`（`houou_latest`）を `jpml_pros.js` が部分一致で絞る（URL の `?league=` も）
- 並べ替え: `jpml_pros.js` は列の位置（`headerCells.indexOf`）で並べ、`data-sort`（`get_sort_key()`）を比べる。並べない列は 2〜4（X・note・YouTube）で、鳳凰の列より前なので列を消しても変わらない
- CSS: `style.css` の `#pros_table` は `table-layout: fixed`、幅は `--mj-pros-table-width: 934px`（コメントは「15列」「名前 150px + それ以外14列 × 56px」。今の列は14列で、幅の式は15列の時のまま）
- 説明文（`DESCRIPTION`）には期の名前は無い
- 表の幅（`scrollWidth`、headless Chromium、ローカルの生成物）: 375px・1280px とも表 934px（14列）。375px ではページが 934px で横にはみ出す

### 2. 直し

実装（007fcf4）:

- `scripts/generate_houou_pages.py`: `current_league_table(names, book, ampai_table, stop, warn)` を足した。houou/ の生成と同じ読み方（`load_rows()`・`current_names()`・`upcoming_term()`・`leagues_data()`→`league_json()`→`current_leagues()`）で、名前ごとに `current_league()` を当てる。対象は「プロ」の名前のうち「表示」Y の行がある選手（個人成績のページがある選手）で、それ以外は空
- `scripts/generate_jpml_pros.py`: 見出しを「鳳凰」の1列にし（`HEADERS`）、値は `current_league_table()` の現在のリーグ（行の末尾 `houou_now`）、行き先は `HOUOU_PLAYERS_URL?name=`（`get_houou_now()`）。消した列の関数（`get_houou_seasons`・`get_houou_latest_league`・`get_houou_highest_league`）は消した。`data-league`（検索欄の絞り込み）も現在のリーグにした。並べ替えのキーは前の「鳳凰43後」と同じ `get_sort_key(リーグ)`
- 検索欄（`PAGE_TEMPLATE` の `league_filter`）: placeholder「鳳凰43後」→「鳳凰」、label「鳳凰戦43期後期の所属リーグで検索」→「鳳凰戦の現在のリーグで検索」。説明文（`DESCRIPTION`）には期の名前が無いので変えていない
- `style.css`: `--mj-pros-table-width` を 934px → 766px（名前 150px + 11列 × 56px、12列）、コメントも直した
- `lib/pro_stats.py` の鳳凰の3項目（`Stats`）と鳳凰戦の Ampai の URL（`ampai_urls()`）は数えたまま使わない（桜花の分と `check_ampai.py` が同じ部品を使うため、この指示では消していない）
- テスト: `test_jpml_pros.py` の列の位置・行のテストを「鳳凰」の1列に直し、現在のリーグが無い在籍者は空欄でリンクなしのテストと、書き出した `jpml_pros.html` の「鳳凰」の値が `houou/ask-players.json` のチップと一致するかのテスト（`GeneratedHououColumnTest`）を足した。全体 1304件 OK（6回続けて OK。その前の1回だけ `test_houou_ask` の `test_matches_name_card` が ERROR になったが、内容を取る前に消え、以後は再現しなかった）

「鳳凰」の列の値（前の「鳳凰43後」→ 直した後。行 1,098（退会者を含む「プロ」の全員）・値のある行 593 → 570）:

- 値が変わる 0名: 
- 空欄から付く 2名: 中村毅（B1）、吉野敦志（B1）
- 空欄になる 25名: 中村朝海（E3）、中村泰詩（E3）、中馬雄志（E3）、久野智子（E3）、内山藤次朗（E3）、和田聡一隆（E3）、大島琉雅（E3）、大崎菜緒（E3）、安井駿（E3）、安部みゆ（E3）、寺下達規（E3）、小倉慶一郎（E3）、小川慈瑛（E3）、小松崎舞（E3）、小松心音（E3）、小笠原樹（E3）、山本駿（E3）、朝比奈優斗（E3）、木村圭吾（E3）、田中羚（E3）、田辺ゆい（E3）、白石ひより（E3）、神代陽向（E3）、船田大斗（E3）、鈴木駿平（E3）
- 前の「鳳凰43後」も 43後（「表示」を問わない最新の期）の行のリーグだったため、43前→43後 で値が変わる選手はいない
- 空欄になる25名は、43後が初めての期の選手（「表示」Y の行が無く、個人成績のページ〈`houou/results/`〉も質問欄の名簿も無い）。リンク先の個人成績に出ないため、この形にした（判断が必要なことに挙げた）
- 最初の版は「プロ」の全員を対象にしたため、この25名に E3 が付いて名簿と食い違った（593名）。個人成績のページのある選手に絞って 568名（AMP-01 と同じ）にした
- `houou/ask-players.json` との食い違い: 0名（`GeneratedHououColumnTest`）

生成物の差分: `jpml_pros.html` だけ（列・検索欄・値）。`regenerate.py houou_pages` も回して houou/ の差分なし。`workers/ask/data.json` は変わっていない

表の幅（ローカル・プレビューとも）: 375px・1280px とも 934px → 766px（12列）。375px ではまだ横にはみ出す（ページ 766px）

### 3. プレビュー

- 007fcf4 の「Workers Builds: mj」success（02:44 開始 → 02:48 に完了を確認）。別名 URL（`?v=007fcf4`）で placeholder「鳳凰」の版が返る
- headless Chromium・375px の明・暗と 1280px: 見出し「名前|所属出身地|X|note|YouTube|鳳凰|桜花出場|桜花21期|桜花最高|最強出場|決勝進出|放送対局」。中村毅 B1・末続ヒロトシ D1・白鳥翔 鳳凰位で、リンクは `./houou/players/?name=<名前>`・`target="_blank"`
- 1280px で末続ヒロトシの「鳳凰」の D1 を押すと、新しいタブで `houou/players/?name=末続ヒロトシ` が開き、期別のタブ・名前の横 D1・先頭の行「43期後期 D1」
- PNG（1280px の表の先頭と3人の行、375px の明・暗の表の先頭）はセッションのチャットに送った。375px では「鳳凰」の列は横に送ると見える位置（表 766px）
- #584 に直した点のコメントを書いた

### 4. 文書

- docs/notes/static-generation.md: jpml_pros の「鳳凰」の列（値・行き先・検索欄・テスト）を足し、成績の8項目の行に鳳凰の3項目は表に出していないことを書いた
- docs/notes/houou-top.md: jpml_pros からのリンクを「鳳凰」の列に直した
- docs/decisions/pros.md: 2026-10-17（AMP-06）の決定を足した。2026-10-16 の決定（鳳凰出場・鳳凰最高を消す、行き先を期別へ、表を減らす方針）は AMP-01 で記録済みだったので足していない

## 報告

- 状態: 判断待ち / 続き: CHAT-1017-AMP-07
- ブランチ: work/1017-amp
- ログ: https://github.com/retroeater/mj/blob/work/1017-amp/docs/logs/CHAT-1017-AMP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-amp
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `jpml_pros.html`（中村毅・末続ヒロトシ・白鳥翔の行）。この版の印: 表の見出しに「鳳凰」の1列があり、「鳳凰43後」「鳳凰出場」「鳳凰最高」が無い
- マージ: 未（平野さんの判断待ち）
- issue: #584（コメント2件: 着手中・直した点）
- 判断が必要なこと:
  - この見え方でマージしてよいか
  - 43後が初めての期の25名（全員 E3）は、個人成績のページがまだ無いため「鳳凰」を空欄にした（前は E3 と Ampai へのリンク）。E3 を文字だけ（リンクなし）で出す形にもできる
  - マージの順: work/1017-pad（未マージ）も `jpml_pros.html`・`style.css`・`generate_jpml_pros.py` の別の箇所を変えている。後に入る側で `jpml_pros.html` を生成し直す
- 未確認の項目: 実機での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9857cf32）: https://github.com/retroeater/mj-logs/tree/main/guide/9857cf32

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2922a722.md
