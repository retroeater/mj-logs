# CHAT-1014-SKD-15

- 着手日時: 2026-10-15
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 3cfeeb02e

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいページの直し3点（年度ページの「通過」の見出しを外す・対局のページの順位の金を外す・卓のページのパンくずを左上に）と比較ページの片付け — プレビューで止まる（#566） Chat-Ref: CHAT-1014-SKD-15 マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す） 貼る時機: いつでも（CHAT-1014-SKD-14 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-14 の比較ページを消すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SKD-14 の比較ページを平野さんが見た結果のうち、未公開の新しいページ（`saikyo/<年度>/`、#425、noindex・未リンク）だけに関わる直し3点を入れ、比較ページを消す。トップの作り直しはこの指示に入れない（次の指示で、#549 の公開まで本番に入れない形で行う）。
決定（2026-10-15、平野さん。SKD-14 の判断待ちへの回答を含む）

1. トップは B（歴代最強位を面の行にする）。A は採らない（トップの作り直しは次の指示）
2. 最強位が決まっていない年度（今の 2026）の行の名前の欄は空欄（チャット側の案のとおり写真の丸も出さず、年度と › だけの行）（次の指示）
3. トップの検索の結果を押したときの行き先は、新しい卓のページ（次の指示）
4. A の並び（2列・広い画面は4列）は今のままでよい（A は採らないので実装には関わらない）
5. 新しいトップの公開（本番への反映）は #549 の公開と同じ時にする。それまで公開済みのページ（今のトップ・今の年度ページなど）には影響を出さない
6. 年度ページで、対局名の行の日付の下の「通過」の見出しは要らない（すべての対局で外す。2026-10-14〈SKD-08〉の「『A卓 1位』の行がある対局だけに出す」を置き換える）
7. 対局のページで、「通過」のピルがある行の順位の数字を金の丸でハイライトしない（順位はすべて同じ灰色の数字にする。「優勝」の行も同じ）
8. 卓のページのパンくずは、ほかの段（年度・対局のページ）と同じく左上に置く（今はヒーローの中）
9. 選手名の検索の結果の画面も、年度ページと同じデザインにそろえる（次の指示）

前提（チャット側。平野さんの決定ではない）

* 決定8: 卓のページのパンくずをヒーローの上（共通ナビのすぐ下、本文の左右 16px）に出し、13px の1行にする。卓のページは濃色の例外のままなので、色は濃色のページの字の色（灰色の字は `--mj-v-fg-muted` などの今の濃色トークン、リンクは今の `.mj-live-breadcrumb` のリンクの色）にする。ヒーローの中のパンくずは消す。live/ の個別ページは同じ部品（`.mj-live-breadcrumb`）を使っているので変えない（卓のページだけに閉じる。SKD-01 `## 経過` 2-3）。ヒーローの高さ・題名の位置が崩れないこと
* 決定7: 対局のページの順位の金の丸（`build_rank_html()` などの見込み）をやめ、全行を灰色の数字にする。卓のページの写真カードの順位の三角と金（live/ と共有の部品）は変えない
* 決定6: SKD-08 の `.mj-saikyo-n-note` を出す条件を外し、見出しを出さない。右端の「A卓 1位」「通過」「優勝」のピルは今のまま
* 比較ページ `saikyo_top_compare.html` と `scripts/build_saikyo_top_compare.py` を消す（採用した B は次の指示で本体に作る。消す前に、B の作り〈検索の結果の行・空欄の行〉を次の指示で使えるよう、どの関数・CSS だったかをログに書く）。SKD-14 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-SKD-15` を足す
* 変えるのは新しい年度・対局・卓のページだけ。今のトップ・今の年度ページ・live/ は変えない。生成物の差は新しい年度ページ（見込み 16）・対局のページ（見込み 219）・卓のページ（件数は生成し直して数え、`saikyo-page-design.md` の 878 ページから年度・対局を引いた数と突き合わせる）だけ。ほかのページには X の画像の URL の変化とシートの変化以外の差が出ないこと
* 確かめ（DSN-07 以降の定型）: 2025 年度の年度ページ・`saikyo/2025/20250816.html`・`saikyo/2025/20250816-s1-a.html`（卓のページ）で、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。卓のページはパンくずが共通ナビの下の左上にあり、ヒーローと重ならないこと。live/ の個別ページ1枚の前後比較で差が無いこと
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 卓のページの左上、ヒーローの上に「最強戦 / 麻雀最強戦2025 / 政権抗争勃発 / ベスト8 A卓」。URL は書かない）
* 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/saikyo-page-design.md` の新しいページの記述を直す

手順

1. #566 に着手中のコメントを書く。未マージの work/ ブランチが、この指示で変える関数・CSS と同じ行を変えていないかを確かめる
2. 3点を直し、比較ページを消し、テストを足して `python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直して差を分類する。上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、卓のページ `saikyo/2025/20250816-s1-a.html` のプレビューの URL を書く

止まる条件

* 生成物の差が上の見込みを外れる（今のトップ・今の年度ページ・live/ に差が出る）
* 卓のページのパンくずを live/ の個別ページを変えずに動かせない
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 3点の直し（変えた関数・CSS）、比較ページを消したことと B の作りの控え、生成物の差の種類と件数、確かめの結果、確認用URL の印、決定と文書の記録先、SKD-14 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前

- 「指示」欄の末尾と指示文の最後の行が一致。`git log --all --grep="CHAT-1014-SKD-15"` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- ブランチ: origin/work/1014-skd（3cfeeb02e、SKD-14 の比較ページ入り・未マージ）を続けて使用。ログ先行 push 24a8f65d5（[sync-logs]）の後、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったため `git merge origin/cloudflare` で取り込んだ（RDR-03・PLB-05 などのコミット。衝突なし）
- #566 に着手中のコメント（6174957774）
- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-wfl-s3・1015-hou・MTK）: `scripts/generate_saikyo_pages.py`・`style.css`・`assets/saikyo.js`・`scripts/tests/test_saikyo_pages.py` を変えているものはなし

### 1. 直し3点

- 決定6（年度ページの「通過」の見出し）: `build_winner_rows()` から `has_place` と `<p class="mj-saikyo-n-note">` を外した。`style.css` の `.mj-saikyo-n-note` の規則を消した。右端の「A卓 1位」・「通過」「優勝」のピルは今のまま
- 決定7（対局のページの順位の金）: `build_rank_html()` の `is-pass` を外し、全行 `mj-saikyo-n-rank`（灰色の数字）。`style.css` の `.mj-saikyo-n-rank.is-pass` の規則を消した。卓のページの写真カードの三角と金（live/ と共有）は変えていない
- 決定8（卓のページのパンくず）: `build_table_hero()` からヒーローの中のパンくず（`.mj-live-breadcrumb`）を外し、`build_table_page_body()` で `<main>` の先頭（ヒーローの上）に年度・対局のページと同じ `build_breadcrumb()`（`.mj-saikyo-n-crumb`、13px の1行・区切り「/」・リンクは 44px）を置いた。使わなくなった `build_live_breadcrumb()` を消した（live/ の生成は `generate_live_pages.py` の別の関数で、`.mj-live-breadcrumb` の CSS も変えていない）
  - `style.css` に `.mj-saikyo-table-page` に限った規則を足した: 左右の余白は下の本文（`.mj-video-episodes`、`max-width: 1100px`・`padding: 0 clamp(16px, 5vw, 48px)`）にそろえた（指示の「左右 16px」と違うのは、390px で「出場者」の見出しと左端を合わせるため。375px では 18.75px、16px になるのは 320px 以下）。灰色の字は `--mj-v-fg-muted`（#a8adb3、地 #121212 で 8.6:1）、リンクは今のヒーローのパンくずのリンクと同じ白
- テスト: `test_no_pass_note_even_with_place`（`test_note_kept_when_some_rows_have_place` を置き換え）・`test_rows_show_place_and_pass_once` の直し・`test_match_page_has_no_video` の順位の直し・`test_breadcrumb_above_hero`（新規）。4件とも修正前のコード（HEAD の生成スクリプト）で失敗し、修正後は全件 OK（`python3 -m unittest discover -s scripts/tests`: 1111件 OK）

### 2. 比較ページの片付けと B の作りの控え

`saikyo_top_compare.html` と `scripts/build_saikyo_top_compare.py` を消した（中身は 17939bf92 にある。`git show 17939bf92:scripts/build_saikyo_top_compare.py`）。次の指示で本体に作るときの元:

- 歴代最強位の面の行（B）: `build_row_b()`。`<li><a class="mj-cmp-row" href="/saikyo/<年度>/index.html">` の中に 年度 `.mj-cmp-year`（13px 灰色・`min-width: 2.5rem`・等幅の数字）→ 写真 `.mj-saikyo-n-avatar`（40px の丸。写真の無い選手は頭の1字の `span`）→ 名前 `.mj-saikyo-n-name` → `.mj-saikyo-n-chev`。面は `.mj-cmp-face`（角丸 12px・行の区切り 1px）。行は `display:flex; gap:12px; min-height:56px; padding:8px 16px`、押したときの地 `--mj-n-pressed`
- 最強位が未定の年度の行: 比較ページは代替アバターと「開催中」（`.mj-cmp-undecided`）だった。決定2で「写真の丸も出さず、年度と › だけ」に変わる
- 検索欄: `.mj-cmp-search`（面の色・角丸 12px・左に虫眼鏡の svg `SEARCH_ICON`・`:focus-within` で紺 2px の枠）と `input.mj-cmp-search-input`（`min-height: 44px`・16px）
- 検索の結果: ページの中の JS（`SCRIPT`）。選手の行 `button.mj-cmp-player`（写真 40px・名前・右に「N卓」`.mj-cmp-count`・▾ `.mj-cmp-toggle`、`aria-expanded`）と、その下の卓の行 `ol.mj-cmp-tables > li > a.mj-cmp-trow`（名前の列に字下げ 68px、`.mj-cmp-trow-body` の1行目 対局名〈見出しの末尾の「（日付）」を外す〉・2行目 `.mj-cmp-trow-sub`「2025 · ベスト8 A卓」、右端に `.mj-saikyo-n-pill`〈通過・優勝〉か `.mj-saikyo-n-place`、›）。並び・一度に1人だけ開く・1人なら自動で開く・該当なしの文言・`?q=` は今のトップの `assets/saikyo.js` と同じ。行き先は今の年度ページのアンカーだったが、決定3で新しい卓のページにする（データ `build_top_data()` の卓のアンカーから `Match.table_slug()` の名前を引く必要がある）

### 3. 生成物の差（`python3 scripts/regenerate.py saikyo_pages`）

| 種類 | 差のあるページ | 全体 | 中身 |
|---|---|---|---|
| 新しい年度ページ | 4（2022〜2025） | 16 | 「通過」の見出しを外しただけ。見出しがあったのはこの4年度だけ（ほかの12年度は HEAD でも見出しが無い） |
| 対局のページ | 91 | 219 | 順位の `is-pass` を外しただけ。金の順位があったのはこの91ページだけ |
| 卓のページ | 643 | 643（878 − 16 − 219） | パンくずの位置と部品だけ |
| 今のトップ・今の年度ページ・ほか | 0 | | |

見込み（年度 16・対局 219）より少ないのは、変わる要素を持つページだけが変わるため。738ページすべてで、旧版から「通過」の見出し・`is-pass`・パンくずを取り除くと新版と一致することを確かめた（説明のつかない差は0）。X の画像の URL・シートの変化による差は無かった

### 4. 確かめ（ローカル、headless Chromium）

- 2025 年度ページ・`saikyo/2025/20250816.html`・卓のページ `saikyo/2025/20250816-s1-a.html` × 375/390/430 × 明・暗 × 標準・幅の広い字体2種・150%・200% の90通り: 横スクロール・はみ出し・44px 未満は0件。「通過」の見出しは0、金の順位は0。卓のページの transition 9件は出場者の写真カード（live/ と共有、変える前から同じ数）
- 卓のページのパンくず（390px）: 共通ナビ（sticky、56px）のすぐ下、ヒーローの上で重ならない。左端は「出場者」の見出しと同じ 20px。13px・#a8adb3。標準の字体で1行（高さ 44px）、幅の広い字体では最後の「ベスト8 A卓」が2行目に回る（64px）、150% で 88px、200% で 127px
- ヒーロー: 高さ 608px は前と同じ。中のパンくずが無くなった分、題名・日付・「▶ 再生」の塊は下端からの位置が前と同じ（題名の下端からヒーローの下端まで 106px）
- 前後比較（HEAD の `git archive` と作業ツリー、375・1280px × 明・暗、全ページのスクリーンショット）: `live/judan/43/b16-a.html`・今のトップ `saikyo/index.html`・今の年度ページ `saikyo/2025.html` がすべて同じ
- プレビュー: c9e5f702f の check-run「Workers Builds: mj」completed/success。別名 URL の `?v=c9e5f70` で `saikyo/2025/index.html`・`style.css` がローカルと一致、卓のページは1回目の取得で違い、取り直すとローカルとバイト単位で一致（1回目は配信の切り替わりの途中とみられる）。比較ページは 404。ブラウザでの見え方はここからは確かめていない

### 5. 記録

- 決定1〜9は `docs/decisions/design-renewal.md` の「2026-10-15（CHAT-1014-SKD-15）」
- 文書: `docs/notes/saikyo-page-design.md`（年度ページの「通過」・対局のページの順位・卓のページのパンくず）、`docs/notes/design.md`（「最強戦の新しい年度・対局のページ」の行）
- SKD-14 のログの状態を「判断待ち / 続き: CHAT-1014-SKD-15」にした

## 報告

- 状態: 判断待ち
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: 卓のページの左上、白字のナビの下・ヒーローの上に「最強戦 / 麻雀最強戦2025 / 政権抗争勃発 / ベスト8 A卓」の 13px の1行があり、ヒーローの中にはパンくずが無い。2025 年度ページの対局名の下に「通過」の見出しが無く、対局のページの順位はすべて灰色の数字（URL はターミナルの最終報告）
- マージ: 未（判断待ちで止まる）
- issue: #566
- 判断が必要なこと: (1) この版をマージしてよいか。(2) 卓のページのパンくずの左右は指示の 16px ではなく下の本文（「出場者」）の端にそろえた（375〜430px で約 19〜21px）。これでよいか。(3) 幅の広い字体ではパンくずの最後の項目が2行目に回る（年度・対局のページと同じ部品のふるまい）
- 未確認の項目: 実機（iPhone・PC）のブラウザでの見え方（セッションからは確かめられない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cca707c1）: https://github.com/retroeater/mj-logs/tree/main/guide/cca707c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b8500442.md
