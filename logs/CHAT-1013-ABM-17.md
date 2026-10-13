# CHAT-1013-ABM-17

- 着手日時: 2026-10-13
- 対象issue: #425
- ブランチ: work/1013-abm-cast
- 着手時HEAD: e676f8c2b058d0afe9ac7caf502739b52bc1d27a

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓のページに司会・実況・解説を出す（ABEMA ブックの【2】＋【3】から）。試作してプレビューに出す Chat-Ref: CHAT-1013-ABM-17 マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-abm-cast を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-abm-cast origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめる（ABM の系列のものは同じ系列）。

目的
ABEMA ブックの【2】最強戦（毎朝の作り直し）と【3】手動補正にある卓ごとの司会・実況・解説を、未公開の卓のページ（`saikyo/<年度>/<卓のアンカー>.html`）に出す。
決定（2026-10-13、平野さん）

* 出し方は /live の個別ページの「実況・解説」と同じ写真カード（docs/notes/live-page-design.md の「実況・解説はヒーローの…行をやめ、対局者の下の節『実況・解説』に同じ写真カードで出す。役割は写真の上端の帯で示す」）。最強戦では節を「司会・実況・解説」とし、並びは 司会 → 実況 → 解説
* 「プロ」「連盟プロ以外」「別名」に登録の無い人（局アナ・芸能人など）も、名前と仮の画像（灰色の人の形など、今の写真の無い人と同じ扱い）で出す。未登録の名前の一覧はログに書く（登録するかは平野さんが後で決める）
* 出すのは卓のページだけ。対局のページ・年度ページ・トップの検索（`?q=`）・各選手の他の対局は今のまま
* 【3】の司会・実況・解説の空でないセルは【2】を上書きする（URL と同じ。docs/notes/abema-book.md）

前提（チャット側。平野さんの決定ではない）

* 【2】の司会・実況・解説は、1つの役に複数の人がいれば「／」でつながっている（docs/notes/abema-book.md「司会・実況・解説の読み分け」）。CHAT-1012-ABM-13 の時点で、回の当たった卓 603 のうち 司会 584・実況 550・解説 553 卓に値がある
* 「司会・実況」と書かれた人は【2】の司会と実況の両方に入っている。同じ人が司会と実況の両方にいるときは、カードを2枚にせず1枚にし、帯を「司会・実況」とする案（実物を見て、より自然な形があれば理由を書いて選んでよい）
* 名前の解決（現在名・写真）は出場者の写真カードと同じ仕組みを使う。ABEMA の書き方（敬称・所属の括弧・空白など）が登録と違って当たらない名前は、読み方の規則（括弧を外すなど）で当てるか、当たらないまま未登録として出す。足した規則はログに書く
* 3つとも空の卓（回の当たらない卓など）は節を出さない。役の一部だけ空なら、ある役だけ出す
* 写真カードは押してもどこへも行かない（今の出場者のカードと同じ扱いにそろえる。出場者のカードがリンクなら、同じリンクにする）
* 生成物の変更は卓のページだけの見込み（値のある卓、見込み 600 前後）。対局のページ・年度ページ・今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・live/ は変わらない
* `scripts/generate_saikyo_pages.py` の `Table.video` のコメントに「動画のURL(M列)」が残っている（CHAT-1013-ABM-16）。同じファイルを直すので「ABEMA ブックの【2】＋【3】」に直す

手順

1. 確かめる: 【2】【3】を生成と同じ経路で読み、司会・実況・解説の値のある卓の数（役ごと）と、名前の種類の数を書く。名前を出場者と同じ仕組みで解決し、当たった名前・当たらない名前の数と、当たらない名前の一覧（出る卓の数つき）を書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`scripts/lib/abema_book.py`・`style.css` の同じ関数・同じ行を変えていないか確かめる
2. 作る: 上の決定と前提のとおり、卓のページに節「司会・実況・解説」を足す（ヒーロー → 出場者 → 司会・実況・解説 → 関連動画 → 各選手の他の対局）。読み込みは `abema_book` に足し、【3】の上書きを含める。テストを足し（節の並び・帯・同じ人の1枚化・未登録の人の仮の画像・空の役を出さない・3つとも空なら節が無い・【3】の上書き）、`python3 -m unittest discover -s scripts/tests` を通す。docs/notes/saikyo-page-design.md の卓のページの項と docs/notes/abema-book.md の「司会・実況・解説はまだ表示しない」を直す（今の内容を読んでから）。決定を docs/decisions/saikyo.md に足す
3. 確かめてプレビューに出す: 生成して、(a) 変わったのが卓のページと CSS だけで、その数（節のある卓の数と一致すること）、(b) 内部リンクがすべて実在すること、(c) id の重複 0・noindex と canonical が全ページ、(d) headless Chromium でスマホ幅（390）と PC 幅（1280）の見え方（帯が切れない・はみ出しが無い）をログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2026 超頭脳合戦 ベスト8 A卓（`saikyo/2026/20260920-s1-a.html`）、未登録の人が出る卓の例1つ、同じ人が司会と実況を兼ねる卓の例1つ（あれば）を並べる

止まる条件

* 決定（docs/decisions/saikyo.md）と食い違う作りが要る（作らずに報告する）
* 卓のページ以外の生成物に差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが上の3ファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ABM-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ABM-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-10-13）

- 同じ Chat-Ref のコミットは0件。`work/1013-abm-cast` はローカル・リモートとも無かったので `git checkout -b work/1013-abm-cast origin/cloudflare`（e676f8c2）
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4つともある（1行目にまとめて書かれている）。#425 のコメントは ABM の系列だけで、他セッションの着手中は無い
- #425 に着手中のコメント: https://github.com/retroeater/mj/issues/425#issuecomment-6128784925

### 手順1: 確かめる

- 【2】【3】（生成と同じ gviz の `fetch_table()`）: 【3】はデータ 0 行。【2】で司会・実況・解説の値のある卓は **596**（役ごと: 司会 584・実況 550・解説 553。CHAT-1012-ABM-13 と同じ）。セルを「／」で分けた名前の種類は 61
- 出場者と同じ仕組み（`NameBook.resolve()`、「プロ」「連盟プロ以外」「別名」）で、そのままでは 47 が当たり 14 が当たらなかった。当たらない14のうち、つながった2人以上（「小山剛志・篠原みなみ」「片山まさゆき・前田直哉・梶本琢程」「近藤誠一、馬場裕一」「桜井章一 森山茂和」「金太賢・小林剛・和久津晶・梶本琢程」）と飾り（「古橋崇志<解説>」「大村朋宏(トータルテンボス)」）が7つ
- 読み方の規則を足した後（下の「作る」）: 当たった名前 **48**・当たらない名前 **8**。当たらない名前の一覧（出る卓の数）:

| 名前 | 卓 | 例 |
|---|---:|---|
| 小山剛志 | 555 | 2026 超頭脳合戦 |
| 梶本琢程 | 449 | 2025 ファイナル 1st Stage |
| 篠原みなみ | 95 | 2025 ファイナル 1st Stage |
| 高見奈央 | 49 | 2023 ファイナルトーナメント |
| 竹内幸輔 | 13 | 2016 プレミアトーナメント代表決定戦 |
| 梶本琢 | 5 | 2017 ファイナルトーナメント（ABEMA の書き損じとみられる。「梶本琢程」なら【3】で直せる） |
| 独歩 | 3 | 2019 男子プレミアトーナメント 空中決戦 |
| 桜井章一 | 3 | 2014 近代麻雀特別予選 |

- 同じ人が2つの役にいる卓（帯「司会・実況」）: 122 卓
- 未マージのブランチ（6本）: `generate_saikyo_pages.py`・`scripts/lib/abema_book.py`・`style.css` を変えているものは無い

### 手順2: 作る（4791a154〈コードと生成物〉・51928adb〈文書〉）

- `scripts/lib/abema_book.py`: `table_cells()`（URL と司会・実況・解説を卓の鍵ごとにまとめ、【3】は空でないセルだけ上書き）。`video_urls()` はその URL だけを返す形にした。`cast_names()`・`standalone_names()`（名前の読み方、下）
- 名前の読み方の規則（足したもの）:
  1. セルを「／」で分け、`<…>`（役）と `(…)`・`（…）`（所属）の飾りを外す
  2. それでも登録に当たらず、【2】＋【3】のほかのセルに1人だけで書かれた名前でもないときは、「・」「、」・空白で分け、分けた1人でも当たる（登録にある・ほかで1人だけで書かれている）ときだけ分ける。登録の無い1人の名前（「ケイン・コスギ」のような）を誤って分けないため。「小山剛志・篠原みなみ」は2人とも未登録だが、ほかの卓に1人ずつ出ているので分けられる
  3. 名前は出場者と同じく「別名」で現在名に直す
- `scripts/generate_saikyo_pages.py`:
  - `load_videos()` は卓ごとの `{列: 値}` を返す（テストが差し替える名前は変えない）。「司会・実況・解説のある卓: N」も出す
  - `attach_videos()` が URL と司会・実況・解説を卓に付ける。司会・実況・解説があるときだけ名前の辞書を読む（`load_name_book()` は `functools.lru_cache` で `load_rows()` と同じものを使う）
  - `build_cast()`: 並びは 司会 → 実況 → 解説、同じ人が2つの役にいれば1枚にして帯を「司会・実況」（前提の案のとおり。122卓が当たる）。`cast_player()`: 写真と X は出場者と同じく「プロ」「連盟プロ以外」から（`_400x400`、1x は `_200x200`）。登録の無い人は代替アバター・リンクなし
  - `build_player_html()` に `role` を足し、写真の上端の帯（/live と同じ `.mj-live-role`）と alt「<役> <名前>」を出す。カードは出場者と同じく、X ID のある人だけ X へのリンク
  - `build_table_page_body()`: 出場者の下に節「司会・実況・解説」（`ul.mj-saikyo-players.mj-live-players.mj-live-staff`）。3つとも空の卓は節を出さない
  - `Table.video` のコメントを「ABEMA ブックの【2】＋【3】」に直した
- CSS は変えていない（`.mj-live-role` は /live のものがそのまま効く）
- テスト: `test_saikyo_pages.py` に `CastTest`（並びと節の位置・帯・同じ人の1枚化・登録の人の写真と X・未登録の人の代替アバターとリンクなし・空の役を出さない・3つとも空なら節なし・セルの書き方・対局のページに出ない）、`test_abema_book.py` に【3】の上書き（空でないセルだけ）・`cast_names()`・`standalone_names()`。`python3 -m unittest discover -s scripts/tests` 953件 OK。足したテストは修正前のコードには関数が無いため通らない
- 文書: docs/notes/saikyo-page-design.md の「卓のページ」に節を足し、docs/notes/abema-book.md の「司会・実況・解説はまだ表示しない」を、表示することと名前の読み方に書き直した。docs/decisions/saikyo.md に「2026-10-13（CHAT-1013-ABM-17）」の節を足した

### 手順3: 確かめる

- `python3 scripts/regenerate.py saikyo_pages`: 「動画URLのある卓: 603」「司会・実況・解説のある卓: 596」、警告なし
- (a) origin/cloudflare との差分の生成物は卓のページ **596** だけ（節「司会・実況・解説」のある卓のページの数 596 と一致）。CSS の変更なし。対局のページ・年度ページ・今の公開中のファイル・live/ は 0
- (b) 内部リンク 878ページ・実在しないもの 0
- (c) id の重複 0、noindex・canonical は全ページ、`#` の対応表も問題なし
- (d) headless Chromium（ローカル。ABEMA の画像はセッションから届かない）で 2026 超頭脳合戦 ベスト8 A卓（3枚）・2011 鉄人プロ代表決定戦 ベスト8 A卓（「司会・実況」と「解説」の2枚）・2023 最高勝率決戦 ベスト8 A卓（5枚、未登録の高見奈央など）を 390・1280 で見た: 帯はすべて1行でカードの中に収まり切れ 0、名前の切れ 0、ページの横のはみ出し 0、見出しの並びは 出場者 → 司会・実況・解説 → 関連動画 → 各選手の他の対局
- push（51928adb）。プレビューの確認用の3ページはコミットした生成物とバイト一致（URL はターミナルの最終報告にだけ書く）。51928adb の check-run は GitHub に1件も出ていない（Workers Builds の結果は GitHub からは見えなかった。プレビューの中身は一致）

## 報告

- 状態: 判断待ち / 続き: CHAT-1013-ABM-18
- ブランチ: work/1013-abm-cast
- ログ: https://github.com/retroeater/mj/blob/work/1013-abm-cast/docs/logs/CHAT-1013-ABM-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-abm-cast
- 確認用URL: プレビュー（ターミナルの最終報告に書く）の `saikyo/2026/20260920-s1-a.html`（2026 超頭脳合戦 ベスト8 A卓）・`saikyo/2023/20230326-s1-a.html`（未登録の人: 高見奈央・小山剛志・梶本琢程）・`saikyo/2011/20110625-s1-a.html`（司会・実況を兼ねる小山剛志）
- マージ: 未（判断待ち）
- issue: #425（着手中のコメント）
- 判断が必要なこと:
  - プレビューを見て、この作りでマージするか
  - 未登録の名前8つ（経過「手順1」の表。小山剛志 555卓・梶本琢程 449卓など）を「連盟プロ以外」などに登録するか。「梶本琢」（5卓）は ABEMA の書き損じとみられ、【3】で「梶本琢程」に直せる
- 未確認の項目: ABEMA の画像と X の写真が載った状態のブラウザでの見え方（セッションから届かない）。51928adb の Workers Builds の check-run（GitHub に出ていない。プレビューの中身は一致を確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f81ee0dd）: https://github.com/retroeater/mj-logs/tree/main/guide/f81ee0dd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f81ee0dd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2c5fedab.md
