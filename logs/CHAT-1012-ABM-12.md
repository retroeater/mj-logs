# CHAT-1012-ABM-12

- 着手日時: 2026-10-12
- 対象issue: #425
- ブランチ: work/1012-abm-ui
- 着手時HEAD: dcd9d5b3218cabf9630fb13d42291b729410942f

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓のページを直す（固定バーと「すべての卓」を外す・関連動画のカードの文字・各選手の他の対局を足す）。試作してプレビューに出す Chat-Ref: CHAT-1012-ABM-12 マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-abm-ui を使う（ABEMA ブックの【2】を作る後の指示と並行することがあるため分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-abm-ui origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめる（ABM の系列のものは同じ系列）。

目的
本番に入った未公開の卓のページ（`saikyo/<年度>/<卓のアンカー>.html`、CHAT-1012-ABM-11 でマージ）を、平野さんが本番で見て出した直しのとおりに直し、各選手の他の対局を足す。実況・解説・司会はこの指示では足さない（ABEMA ブックの【2】ができてから別の指示で足す）。
決定（2026-10-12、平野さん）

* 卓のページの固定バー（年度プルダウン）は外す。左上のパンくずがあるため（CHAT-1012-ABM-09〜11 の「固定バーは /live と同じくヒーローの下の sticky」を置き換える）
* 関連動画の見出しの右の「<対局名>のすべての卓」のリンクは外す。下に卓が並んでいるため
* 関連動画のカードの文字は、1行の「2026年9月20日　ベスト8 B卓」をやめ、4行にする:
   * 1行目: 対局日（例「2026年9月20日」）
   * 2行目: 「麻雀最強戦<年度>」（例「麻雀最強戦2026」）
   * 3行目: 対局名（例「超頭脳合戦」）
   * 4行目: ステージと卓（例「ベスト8 B卓」。決勝は「決勝」）
* 各選手の他の対局を足す（2026-10-12 の決定。CHAT-1012-ABM-11 の決定の節）: /live の「〇〇の他の対局」と同じ横スクロールのカードで、最強戦のほかの卓のページを出す。見出しの右の「すべて見る」はトップの検索（`/saikyo/?q=<選手名>`）へ行く

前提（チャット側。平野さんの決定ではない）

* 各選手の他の対局の作りは、/live の「〇〇の他の対局」（docs/notes/live-page-design.md「2-6」、`generate_live_pages.py` の該当の部品）にそろえる: 選手ごとに見出し「<選手名>の他の対局（N件）」と右に「すべて見る」、その下に横スクロールのカード。カードは最新4件（対局日の降順。同じ日ならアンカーの降順）。今の卓は除く。他の対局が0件の選手は節を出さない。選手の見分けは今の写真カードと同じ名前の解決（現在名）
* 他の対局のカードも、関連動画と同じ4行の文字とサムネイル（ABEMA の URL があれば ABEMA の 640×360、無ければ年度の OGP 画像）にする。押すとその卓のページ
* 並びは、ヒーロー → 出場者 → 関連動画 → 各選手の他の対局（/live と同じく出場者の順）
* 固定バーを外したあとも、navbar の sticky と上余白が崩れないこと（`assets/saikyo.js` の `watchHeight()` などの扱いを見直す）
* 卓のページ以外（対局のページ・年度ページ・トップ・今の公開中のファイル・live/）は変えない

手順

1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の同じ関数・同じ行を変えていないか
2. 作る: 上の決定と前提のとおり、卓のページの生成を直す。テストを直し・足し（固定バーが無い・「すべての卓」が無い・カードの4行・他の対局の件数と並びと今の卓を除くこと・「すべて見る」の行き先）、`python3 -m unittest discover -s scripts/tests` を通す。docs/notes/saikyo-page-design.md の卓のページの項を直す（今の内容を読んでから）。決定を docs/decisions/saikyo.md に足す
3. 確かめてプレビューに出す: 生成して、(a) 変わったのが卓のページ（見込み 643。CHAT-1012-ABM-09 のログ「手順3」(a)。シートの更新で 2026年度が増えていれば +10 まで許す）と CSS・JS だけで、対局のページ・年度ページ・今の公開中のファイル・live/ に差分が無いこと、(b) 内部リンクがすべて実在すること、(c) id の重複 0・noindex と canonical が全ページ、(d) headless Chromium でスマホ幅（390）と PC 幅（1280）の見え方（固定バーが無いこと、カードの4行が切れずに出ること、横スクロールが効くこと、はみ出しの有無）をログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2026 超頭脳合戦 ベスト8 A卓（`saikyo/2026/20260920-s1-a.html`）と、他の対局が多い選手のいる卓の例を1つ並べる

止まる条件

* 決定（docs/decisions/saikyo.md）と食い違う作りが要る（作らずに報告する）
* 変わるファイルが見込みと違う（卓のページが 643 より少ない、または +10 を超えて多い。卓のページ以外の生成物に差分が出る。シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが上の3ファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-10-12）

- 同じ Chat-Ref のコミットは0件。`work/1012-abm-ui` はリモートに無く、origin/cloudflare（dcd9d5b3）から作った
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行は4つともある（1行目にまとめて書かれている）。#425 に ABM 以外の着手中コメントは無い

### 手順1: 未マージのブランチ

- `git branch -r --no-merged origin/cloudflare` は6本。3ファイルのどれかを変えているのは `work/1012-whs-links` の `style.css` だけで、変更は 2944 行付近の帰り道の回（`.mj-title-wayhome-*`）。今回の変更（1810 行付近の卓のページの節）とは別の箇所なので止まらない

### 手順2: 作る（0c92c702）

- `scripts/generate_saikyo_pages.py`:
  - `build_table_page_body()` から固定バーと「<対局名>のすべての卓」（`.mj-live-more`）を外した。第3引数を `years` から全年度の選手の索引 `by_player` に替えた
  - `build_related_card()` のカードの文字を4行に（`.mj-video-card-date` 対局日・`.mj-video-card-date.mj-saikyo-card-event`「麻雀最強戦<年度>」・`.mj-video-card-title` 対局名・`.mj-video-card-title` ステージと卓）。ほかの年度の卓へは `../<年度>/` を付ける（`table_href()`）
  - 各選手の他の対局: `index_tables_by_player()`（全年度・個別ページのある卓、対局日の降順・同じ日はアンカーの降順。名前は写真カードと同じ現在名）を `main()` で作って `write_sub_pages()` に渡し、`build_player_others()` が出場者の順に「<選手名>の他の対局（N件）」・「すべて見る」（`../?q=<選手名>`）・最新4枚（`OTHER_TABLES_LIMIT`）を出す。今の卓を除き、0件の選手は出さない
  - 卓のページのためだけに ABM-09 で足した `build_year_select()` の `select_class` 引数は使い手が無くなったので戻した
- `assets/saikyo.js`: 卓のページは `watchHeight('nav.navbar', '--mj-nav-h')` だけにし、固定バーの高さと `bindYearSelect()` を呼ばない（プルダウンが無いと `getElementById` が null で落ちるため）
- `style.css`: `html:has(.mj-saikyo-table-page)` の scroll-padding-top を navbar の高さだけにした（`.mj-live` の規則は固定バーの分 52px を足すため）。カードの2行目の上の余白（`.mj-saikyo-card-event`）
- テスト: `TablePageTest` を直し（固定バー・「すべての卓」・年度プルダウンが無い）、カードの4行と決勝・ほかの年度の行き先、`PlayerOthersTest`（件数・今の卓を除く・同じ日の並び・最新4枚・「すべて見る」の行き先・対局日の無い対局は入らない）を足した。`python3 -m unittest discover -s scripts/tests` 863件 OK。足したテストは修正前のコードでは関数・引数が無いため通らない
- docs/notes/saikyo-page-design.md の「卓のページ」の項を書き直した。docs/decisions/saikyo.md に「2026-10-12（CHAT-1012-ABM-12）」の節を足し、ABM-11 の固定バーの行に「→ 置き換え」を付けた

### 手順3: 確かめる

- `python3 scripts/regenerate.py saikyo_pages`: 警告なし、配信ファイル数 3,339 / 20,000
- (a) origin/cloudflare との差分: 卓のページ **643**（見込みとの差 0）、CSS/JS は `style.css`・`assets/saikyo.js`、ほかはコード・テスト・文書。今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・対局のページ・年度ページ・live/ は 0。シートの変化による差分は無し
- (b) 内部リンク: 878ページ・36,124本、実在しないもの 0
- (c) id の重複 0、noindex・canonical は 878ページすべて、`#` の対応表も問題なし
- 他の対局の節: 卓のページ 643 のすべてに1節以上ある。最多は `saikyo/2020/20200216-s1-a.html`（4人で計125件）。2026年度では `saikyo/2026/20261011-s1-b.html`（阿部孝則9・河野高志8・瀬戸熊直樹34・藤崎智32）
- 対局日があって結果がまだ無い（未対局の）卓で名前の入っているものが10卓あり（2026年度の義兄弟合戦・超技巧合戦・Mの乱など）、他の対局の先頭に出る（対局日の降順のため）。指示の前提に除く定めが無いので入れたまま（判断が必要なこと）
- (d) headless Chromium（ローカルの http.server。ABEMA の画像はセッションから届かないため年度の OGP 画像に差し替わった状態）で `20260920-s1-a`・`20261011-s1-b` を 390・1280 で見た:
  - 固定バー 0、navbar は sticky（下へスクロールしても top 0）、scroll-padding-top は navbar の高さ（390: 56px、1280: 80px）
  - カード（15枚・17枚）はすべて4行で、行の切れ 0（1行目の例「2026年9月20日／麻雀最強戦2026／超頭脳合戦／ベスト8 B卓」）
  - 横スクロール: 390 では4枚の列・2枚の列は `overflow-x: auto` で動く（scrollLeft が変わる）。1280 では4枚が1画面に収まりスクロールしない
  - ページの横のはみ出し 0
  - 並び: ヒーロー → 出場者 → 関連動画 → 各選手の他の対局
- push（0c92c702）。check-run「Workers Builds: mj」「check」success。プレビューの2ページと `assets/saikyo.js` はコミットした生成物とバイト一致（URL はターミナルの最終報告にだけ書く）

## 報告

- 状態: 判断待ち / 続き: CHAT-1012-ABM-14
- ブランチ: work/1012-abm-ui
- ログ: https://github.com/retroeater/mj/blob/work/1012-abm-ui/docs/logs/CHAT-1012-ABM-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-abm-ui
- 確認用URL: プレビュー（ターミナルの最終報告に書く）の `saikyo/2026/20260920-s1-a.html`（超頭脳合戦 ベスト8 A卓）と `saikyo/2026/20261011-s1-b.html`（瀬戸熊直樹34件・藤崎智32件など）
- マージ: 未（判断待ち）
- issue: #425（コメントなし）
- 判断が必要なこと: (1) この作りでマージするか。(2) 日付があって未対局の卓（今は10卓）を各選手の他の対局に入れるか（今は入れており、最新として先頭に出る。/live は放送済みだけ）
- 未確認の項目: ABEMA のサムネイルが載った状態でのカードの見え方（セッションから ABEMA の画像に届かないため、ローカルでは OGP 画像で見た）。実機のブラウザでの見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ff90f7ad）: https://github.com/retroeater/mj-logs/tree/main/guide/ff90f7ad

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff90f7ad/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
