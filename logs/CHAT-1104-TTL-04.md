# CHAT-1104-TTL-04

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 57f02a1a6

## 指示

【Claude作成】Claude Code 向け指示：TTL-03 の続き（入口は写真カード・大会ページと期ページは行の案に決め、比較ページと使わないコードを消して仕上げる。プレビューまで） Chat-Ref: CHAT-1104-TTL-04 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-03 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-ttl を続けて使う（TTL-02・03 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（title/ の HTML・sitemap の lastmod など）の衝突は docs/notes/branch-operations.md の解き方で生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-04` を足す。

目的
TTL-03 の「判断が必要なこと」(1)(2) への回答で title/ の形を決め、比較ページと使わないコードを消して仕上げる。マージはプレビューを見てから別の指示で行う。
決定（2026-11-04、平野さん）

* (1) 入口（現タイトルホルダーと、年を選んだときの各年の優勝者）は写真カード案。大会ページと期ページ（決勝メンバー）は行の案
* (2) TTL-03 の仮置きの一覧は、すべて今の値で決定（共通ナビの判定は body の class `mj-title-n-page`、期ページのパンくずの最後は「第41期」で対局日は題名の2行目、大会ページの題名の2行目に期の範囲、節の見出しは 13px 灰色＋上の余白 8px、節の間隔 16px、「成績」は面の中の押せる行、検索欄は題名の下、年のシートの題は「年を選ぶ」、navbar は固定しない、行の案の大会ページ・期ページの形、写真の無い選手は頭の1字の丸、検索結果の期のカードの形、など。行の案の入口〈写真・選手名・大会名・›〉は (1) で採らない）
* 2026-11-04（CHAT-1104-PRP-10 で記録される決定）: TTL（work/1104-ttl）を先に cloudflare へマージし、pro/ の公開のブランチ（work/1101-prp-pub）の側で重なりを解く。決勝メンバーの行き先を決める部分は `member_link()` の1つの関数のまま残し、TTL では pro/ の判定を入れない

前提（チャット側。平野さんの決定ではない）

* 暗い配色でメンバー限定のバッジ（#212529 の地）の縁が見えない件は、暗い配色だけ地の色を新サイトの暗い配色の値に寄せて直す案（直し方は任せる。直した値とコントラスト比〈WCAG 2.x の式で計算〉を報告に書く）
* 比較ページ `/title/compare-layout.html` は消す（noindex のため、転送は不要と見ている。要確認）。`LAYOUT` の切り替えと、使わない側（入口の行の案、大会ページ・期ページの写真カード案）のコード・CSS を消す。写真カードの部品は入口と検索結果で残る
* TOP 系列の伝言（2026-11-04）: 新しい共通ナビ（TOP-11、work/1104-top）は白地の対象のページ全部に出る。title/ はこの TTL で白地の対象に入れる。title/ の「質問」の文脈は横断のまま（`context` なし）。TOP-11 のマージとの前後は問わず、`navbar.js` の対象の判定の重なりは両方を残して解ける見込み（要確認）
* 文書: docs/notes/title-pages.md（入口・大会ページ・期ページ・固定バーと検索・入口の年の切り替えの節）と docs/notes/design.md（「共通ナビ（新しい形）」の適用範囲に title/ を足す、title/ の値の行）を今の形に直す。`scripts/promo_video/title/capture.mjs` は告知動画の作り直しまで直さない（注記だけ足す）

手順

1. 上の決定で、入口を写真カード、大会ページ・期ページを行の形に固定し、比較ページ・`LAYOUT` の切り替え・使わない側のコードと CSS を消す。メンバー限定のバッジの暗い配色を直す
2. 文書（docs/notes/title-pages.md・docs/notes/design.md・docs/decisions/title.md）を直す。追記先の今の内容を読み、同じ趣旨の記述は置き換える
3. 生成・テストを通し、TTL-02 の手順3と同じ確かめ（360px と PC 幅、明・暗。入口〈既定・`?year=2025`・`?q=白鳥`〉・大会ページ〈鳳凰戦・王位戦〉・期ページ〈第41期鳳凰戦・第42期十段戦・第26期王位戦〉）を行い、はみ出し・切れ・重なりの件数を報告に書く。比較ページの URL が 404 になることも確かめる。プレビューのビルドが付くこと（docs だけの push では付かない）を確かめ、確認用 URL を出す

止まる条件

* 生成物の差分に、title/ の HTML・`sitemap-title.xml`・比較ページの削除・共通の CSS／JS 以外の変化（ほかのページの HTML、`search.json`・`years.json` の中身）が出た（想定外の行を全部書いて止まる）
* 未マージの work/ ブランチ（`work/1101-prp-pub` は除く）が `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。`work/1104-top` は重なりを報告に書くだけで止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、決めずに置いた値があればその一覧と、マージのときの `work/1101-prp-pub`・`work/1104-top` との重なりの見込みを書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告に「確認用:」の行を書く。Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1104-TTL-04'` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし。対象 issue の指定なし
- 作業ブランチ: `work/1104-ttl`（ローカル・リモートとも 57f02a1a6、未マージ）を続けて使った。手順0: 指示欄の末尾の行は指示文の最後の行と一致。TTL-03 のログの状態に ` / 続き: CHAT-1104-TTL-04` を足し、このログと一緒に push（50785fda5）
- 未マージのブランチ（着手時）: `generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js` を変えているのは `work/1029-lgn-page`・`work/1101-prp-pub`・`work/1104-lvp`・`work/1104-top`。`work/1104-lvp` は navbar.js の「放送対局」の href の行（39行目付近）だけで、title/ の判定の行とは別の箇所。`work/1029-lgn-page` は navbar.js の判定の先頭と style.css の末尾
- `origin/cloudflare` の取り込み: 29コミット先行。`git merge-tree` で衝突なしを確かめて `git merge origin/cloudflare`（衝突なし）。docs 以外の変更は `data/youtube_meta.json`・`workers/ask/` の6本（title/ の生成は帰り道の再生時間などに `data/youtube_meta.json` を読む）
- 手順1（6b7349938・97e261695）:
  - 入口は写真カード（`holder_card_html()`・`photo_cards_html()`、年を選んだときの `[data-year-list]` も写真カード）、大会ページ・期ページは面の中の行（`Holder`・`row_html()`・`holders_html()`）に固定した
  - 消したもの: 比較ページ（`compare_body()`・`compare_meta()`・`COMPARE_*`、生成から外したので `remove_stale()` が `title/compare-layout.html` を消す）、`LAYOUT`・`LAYOUT_CARDS`・`LAYOUT_ROWS`・`data-layout`、`NOINDEX_TAG` と `render_page()`・`write_page()` の noindex、入口の行の案（`Holder.sub`）、大会ページ・期ページの写真カード（`members_html()`・`.is-members`・`.is-many`・`MEMBERS_PER_ROW`、`photo_card_html()` の「リンクなし」「新しいタブ」の分岐と `external` 引数）、CSS の比較ページの節と `.mj-title-holders.is-many`、`assets/title.js` の行の組み立て（`row()`）と比較ページのための分岐
  - 決勝メンバーの行き先は `member_link()` の1つの関数のまま（pro/ の判定は入れていない）
  - メンバー限定の印（`.mj-title-video-badge`）: 暗い配色だけ `body.mj-title-n-page` の下で地 `#3a3a3c`・字 `#f5f5f7` にした。WCAG 2.x の相対輝度で計算したコントラスト比: 字と地 10.42:1（元の明るい配色の #ffffff／#212529 は 15.43:1）、地とページの地 #000000 は 1.85:1（元の #212529 は 1.36:1）。#3a3a3c は新サイトの暗い配色の「選んだ1つ・残留のピル」の色
  - テスト: `test_title_years.RowsTest`（行の分岐・大会ページの2行・`member_link()`）と、生成物の確かめ（比較ページが無いこと、大会ページ・期ページが行で入口が写真カード）。生成物の確かめの2本は再生成の前のコードの生成物で落ちることを確かめた（FAIL 2件）
- 手順2（文書、4538cc41b・873bcc0e0）: docs/notes/title-pages.md（大会ページ・期ページ・新しい形・帯の色・年の切り替えの節を置き換え）、docs/notes/design.md（共通ナビの前置き・「開いた固定ナビのメニューの高さ」から title/ を外す・「パンくずの区切り」・「共通ナビ（新しい形）」の判定と適用範囲に title/、表に「タイトル戦（title/）の新しい形」の行を足した）、docs/decisions/title.md（この指示の決定）。`scripts/promo_video/title/capture.mjs` は注記だけ足した
- 生成（`regenerate-page.yml`、target_page=title_pages、作業ブランチで手動実行）: 2d7877c6c。差分は title/ の HTML（入口1・大会20・期364の385ページのうち変わったもの）と `title/compare-layout.html` の削除だけ（`git show --name-status 2d7877c6c` で D 1件・M 385件、すべて title/）。`sitemap-title.xml` は変化なし、`title/search.json`・`title/years.json` は origin/cloudflare と差分なし
  - 第3期達人戦の帰り道のカードに `#1` のタグが増えたのは、origin/cloudflare の生成物にも既にある（帰り道のデータの変化。この作業の変更ではない）
  - レビューの修正（97e261695、コメントと使わない分岐の削除）の後にもう一度手動実行した: 生成物の差分なし（コミットが作られなかった。run 41689091798 success）
- テスト: `python3 -m unittest discover -s scripts/tests` → 1817 tests OK
- 確かめ（headless Chromium、UA に mj-internal。スクリプトは `docs/logs/CHAT-1104-TTL-02/shoot.js` の対象を指示どおりに直したもの）:
  - 8ページ（入口の既定・`?year=2025`・`?q=白鳥`、大会ページ 鳳凰戦・王位戦、期ページ 第41期鳳凰戦・第42期十段戦・第26期王位戦）× 360px・1280px × 明・暗 = 32通り
  - ローカルの配信: はみ出し・切れ・重なり 標準の字体で 32/32 とも 0件、幅の広い字体（IPA ゴシック・WenQuanYi Zen Hei）でも 32/32 とも 0件（`docs/logs/CHAT-1104-TTL-04/measure-*.json`、360px の画像16枚も同じ場所）
  - プレビュー（版 c5fac061、2d7877c6c のビルド）: 360px の明・暗 16通りで 0件。ローカルの16枚と画素で比べて全部一致（差のある範囲なし）
  - `/title/compare-layout.html` はプレビューで 404（c5fac061・e9f5f566 とも）。noindex の一時のページでどこからもリンクしていなかったため、転送は置いていない
- レビュー（新しい文脈の general-purpose に目的・決定・差分・コード規約を渡した。1回目）:

  | 指摘 | 扱い |
  |---|---|
  | `photo_card_html()` に使わない分岐（リンクなし・新しいタブ）と古い docstring が残る | 直した（97e261695） |
  | 消した `MEMBERS_PER_ROW` のコメントが残る | 直した（97e261695） |
  | 写真カード・帯の古いコメント（generate_title_pages.py の定数の上、style.css の live/ の役割の帯） | 直した（97e261695） |
  | 入口の帯の字数を全大会の名前から計算している（前は表示するカードの帯） | 直した（97e261695、表示するカードの帯に戻した。生成物は同じ） |
  | 第3期達人戦の帰り道に `#1` が増えた | 意図どおり（データの変化。origin/cloudflare にも既にある） |
  | 暗い配色の印の字を `var(--mj-n-text)` にもできる | 見送り（値は同じ。地と字を同じ所で直書きのまま） |
  | `assets/title.js` の `photo()` の引数が1通りになった | 見送り（害なし） |

  2回目の往復はしていない（指摘の範囲だけを直し、テストと再生成〈差分なし〉で確かめた）
- 取り込みの見込み（`git merge-tree --write-tree HEAD <branch>`、2d7877c6c の時点）:
  - `work/1104-top`: `docs/notes/design.md` の2か所だけが衝突（共通ナビの前置きの1行と「共通ナビ（新しい形）」の行を、両方が書き換えた）。navbar.js・style.css は衝突なし
  - `work/1101-prp-pub`: `scripts/generate_title_pages.py`・`docs/notes/title-pages.md`・`sitemap-title.xml`・title/ の HTML 327ページが衝突
  - `work/1029-lgn-page`・`work/1104-lvp`: 衝突なし
- プレビュー: 6b7349938・873bcc0e0・2d7877c6c・97e261695 の「Workers Builds: mj」がすべて success。最後は 97e261695（版 e9f5f566）。確認用 URL は最終報告にだけ書く

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: 入口（既定・?year=2025・?q=白鳥）・鳳凰戦・王位戦・第41期鳳凰戦・第42期十段戦・第26期王位戦、比較ページの 404。この版の印: 大会ページ（鳳凰戦）の優勝者が面の中の行（左に灰色の「第42期」、右に ›）で、入口は写真カードのまま
- マージ: 未（平野さんの判断待ち。マージは別の指示）
- issue: なし
- 判断が必要なこと:
  - 決めずに置いた値: なし（仮置きはすべて TTL-04 の決定どおり。暗い配色のメンバー限定の印は前提の案どおり地 #3a3a3c・字 #f5f5f7〈10.42:1〉にした）
  - マージのときの重なりの見込み:
    - `work/1104-top`: 取り込みで衝突するのは `docs/notes/design.md` の2か所（共通ナビの前置きの1行と「共通ナビ（新しい形）」の行。どちらも両方が同じ行を書き換えた）。解き方の案: top の文面を残し、適用範囲に title/ の全ページ（`<body>` の `mj-title-n-page`、「タイトル戦」に印）を足す。navbar.js は衝突しない（top の帯は `newNavCurrent` が入るページ全部に出るので、title/ も新しい帯になる）
    - `work/1101-prp-pub`: TTL を先にマージすると、prp-pub の側で `scripts/generate_title_pages.py`（`member_card_html()` はもう無く、行き先は `member_link()` に移す。`period_body()` の引数に `slugs` を足し `member_holder()` → `member_link()` へ渡す）・`docs/notes/title-pages.md`・`sitemap-title.xml`・title/ の HTML 327ページが衝突する見込み。HTML と sitemap の lastmod は解いた後のスクリプトで生成し直す
    - `work/1029-lgn-page`・`work/1104-lvp`: 衝突なし
- 未確認の項目:
  - 実機（iPhone）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bec7039a）: https://github.com/retroeater/mj-logs/tree/main/guide/bec7039a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
