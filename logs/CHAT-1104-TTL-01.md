# CHAT-1104-TTL-01

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 8071c9bed

## 指示

【Claude作成】Claude Code 向け指示：タイトル戦（title/）全ページのレイアウトを、最強戦（saikyo/）・houou/ の新サイトの基本の形に合わせる（自律開発。比較ページつきのプレビューまで） Chat-Ref: CHAT-1104-TTL-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
title/（入口・大会ページ・期ページ）の見た目を、黒いナビ・濃色の固定バー・Material 寄りの形から、saikyo/・houou/ と同じ「新サイトの基本の形」（docs/decisions/design-renewal.md、docs/notes/design.md の適用範囲「houou/ のみ」の行と「最強戦の新しい年度・対局のページ」の行）にそろえる。自律開発（ryoei.pro の進め方: 事前の質問は済み → 不明点は仮置きで最後まで作る → プレビューと仮置きの一覧でまとめて確認）。
決定（2026-11-04、平野さん）

* ページの中身（現タイトルホルダー・年の切り替え〈`?year=`〉・選手名検索〈`?q=`〉・大会ページ・期ページの各節・URL）はそのまま。saikyo/ のような質問欄・メニューのトップには作り替えない。変えるのはレイアウト（共通ナビ・題名・パンくず・面・選ぶ部品など）
* 写真カード（今の大きな写真カード）を残す案と、最強戦の「年度別」のような面の中の行（写真 40px の丸・名前・›）に変える案の両方を作り、ページ内のラジオボタンで切り替える比較ページで見比べて決める
* 対象は title/ の全ページ（入口・大会ページ・期ページ）

前提（チャット側。平野さんの決定ではない）

* 写し元は saikyo/ の新しいページ（`body.mj-saikyo-n-page`、`--mj-n-*` の変数、白地のナビ `.mj-nav-new`）と houou/ の部品。docs/notes/saikyo-page-design.md「新しい年度・対局・卓のページ」「年度別・個人成績・ランキングと新しいトップ」、docs/notes/design.md の「部品」の表を先に読む。部品は既存の HTML の形・CSS を使い、title/ 専用の新しい部品はできるだけ作らない
* チャット側の仮置きの案（実物に合わせて変えてよい。変えたら仮置きの一覧に書く）:
   * 共通ナビは白地の新しい形（「タイトル戦」に印）。`navbar.js` の新しい形の判定に `/title/` を足す
   * 濃色の固定バーはやめる。年の切り替えは、最強戦の年度ページ・順位変動と同じ「選ぶボタンを兼ねる題名」（「現タイトルホルダー ▾」、押すと下からのシート）。選手名検索は houou/ トップの質問欄と同じ形の検索欄を題名の下に置き、検索の動き（結果の出し方・`?q=`・`?name=`・`/` と Esc）は変えない
   * パンくずは新しい形（13px 灰色、「タイトル戦 / 鳳凰戦」）。大会ページ・期ページの題名は 28px 太字で画面に出す（今の「パンくずを見出し代わり」はやめる）
   * 期ページの決勝ライブ・決勝動画・帰り道の節は、節の見出しを新しい形にし、動画のカード自体は変えない
   * 明るい配色・暗い配色の両方に対応する（`--mj-n-*`）
* 比較ページは docs/notes/design.md「7. チャット側の決め方」と docs/notes/title-pages.md の作り方に従う（noindex・どこからもリンクしない・sitemap に載せない）。写真カード案と行の案を、入口（現タイトルホルダーと年を選んだとき）・大会ページ・期ページの決勝メンバーで切り替えられるようにする。本体のページは比較が決まるまで、行の案か写真カード案のどちらかで作ってよい（どちらにしたかを報告に書く）
* 期ページの決勝メンバーは帯に順位があり押すと X へ行く形。行の案では順位を右端の灰色の数字（優勝は金のピル）にする（最強戦の対局のページと同じ）が仮置き
* 未マージの TOP 系列（新トップ #101）が `title/search.json` の生成を変えている可能性がある（要確認）。この指示では `search.json`・`years.json` の中身は変えない
* 公開済みのページの見た目の変更なので、noindex・sitemap・`llms.txt`・canonical・og:* は変えない

手順

1. 今の title/ の HTML・CSS（`style.css` の「タイトル戦(title/、#222)」節）・`assets/title.js`・`scripts/generate_title_pages.py` と、写し元の saikyo/・houou/ の部品を読む。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを洗い、`generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` を変えているものを報告に書く
2. 上の決定・前提で、入口・大会ページ・期ページを新しい形にし、比較ページ（写真カード案／行の案）を作る。決まった値（docs/notes/design.md の表）に無い値を使ったところと、前提の仮置きから変えたところは、すべて仮置きの一覧に書く
3. 確かめ: 生成が通ること・既存のテストが通ること（自分の変更で落ちたテストは直してよい）。headless Chromium で 360px と PC 幅、明・暗で、入口（既定・`?year=2025`・`?q=` で検索中）・大会ページ（鳳凰戦と王位戦）・期ページ（決勝が5名の期・決勝ライブと帰り道のある期）を撮り、はみ出し・切れ・重なりの件数を測って報告に書く（docs/notes/design.md の「最小の画面幅」「字体の見込み」「部品の状態ごとの高さ」の確かめ方で）。画像は作業ブランチの `docs/logs/CHAT-1104-TTL-01/` に置いてよい。プレビューに出して、確認用 URL を最終報告に書く

止まる条件

* 前提が実物と食い違い、title/ の中身（並び・データ・URL）を変えないと新しい形にできない（変えずに止まって報告する）
* 未マージの work/ ブランチが `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 生成物の差分に、title/ の HTML・`sitemap-title.xml` の lastmod・比較ページ・共通の CSS／JS 以外の変化（ほかのページの HTML、`search.json`・`years.json` の中身）が出た（想定外の行を全部書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、(1) 写真カード案と行の案のどちらにするか (2) 仮置きの一覧（1件ずつ、今の値と理由）を書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all -E --grep 'CHAT-[0-9]{4}-TTL-'` と `docs/logs/CHAT-*-TTL-*.md` の履歴はともに0件（同じ Chat-Ref・識別子 TTL の重複なし）。対象 issue の指定なし（#101 は経緯の参照）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- 作業ブランチ: ローカル・リモートとも `work/1104-ttl` が無かったため `git checkout -b work/1104-ttl origin/cloudflare`（8071c9bed）で作り、ログを先に push した
- 手順0: このログの「指示」欄の末尾の行は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 手順1（読んだもの）: `docs/notes/title-pages.md`、`docs/notes/design.md`、`scripts/generate_title_pages.py`（`breadcrumb_html`・`photo_card_html`・`member_card_html`・`period_body`・`taikai_body`・`index_body`・`filterbar_html`・`page_html`）、`title/houou/41.html`、`saikyo/2025/index.html`（`body.mj-saikyo-n-page` の年度ページ）、`style.css` の「最強戦(saikyo/、#319・#549)」節（`--mj-n-*` は `body:is(.mj-saikyo-n-page, .mj-live-n-page)` に置かれ、live/ も同じ部品を使っている）、`navbar.js`
- 手順1（未マージのブランチ）: `git branch -r --no-merged origin/cloudflare` の各ブランチと `git diff --stat origin/cloudflare...<branch> -- scripts/generate_title_pages.py assets/title.js style.css navbar.js title/search.json` で、変更があったのは次の3本
  - `origin/work/1101-prp-pub`（CHAT-1104-PRP-08 が作業中。#641）: **`generate_title_pages.py` の `member_card_html()`（期ページの写真カードの行き先を、個別ページのある在籍者は連盟プロの個別ページ〈pro/〉、無ければ X に変える）と `period_body()`（引数に `slugs` を足し、`cards = [...]` の行を変える）・`pages()`** を変えている（コミット 0ed406ea8「feat: link race icons and title/ final cards to pro/ pages」）。title/ の期ページの HTML 全部・`sitemap-title.xml` も再生成済み。`navbar.js` は「連盟プロ」の href と `/pro/` の判定のコメントの行、`style.css` は houou/ の節（5419〜5800 行付近）で、title/ の節ではない
  - `origin/work/1029-lgn-page`: `navbar.js` の `newNavCurrent` の判定の先頭（`if (location.pathname.indexOf('/houou/') === 0)` の行の前に `/log/` を足す）と `style.css` の末尾への追加。title/ の節・`generate_title_pages.py`・`assets/title.js` は変えていない
  - `origin/work/1104-top`: `navbar.js` の `if (newNavCurrent) {…}` の後ろへの追加と `style.css` の末尾への追加。`generate_title_pages.py`・`title/search.json`・`assets/title.js` は変えていない（前提の「TOP 系列が `title/search.json` の生成を変えている可能性」は、今の `work/1104-top` には無い。`docs/decisions/title.md` の 2026-11-03〈CHAT-1103-TOP-06〉には「TOP 系列の次の指示で、記録0件の大会名も `search.json` に出す」とあり、これから変わる予定）
- 止まる条件の判定: 期ページを新しい形にするには、`period_body()` の中身（パンくずの直後の `visually-hidden` の h1 を 28px の題名にする・写真カードの並びを行の案に切り替える）と、決勝メンバーの写真カード／行を作る `member_card_html()` を変える必要がある。この2関数は `work/1101-prp-pub` が変えている関数そのもので、止まる条件「未マージの work/ ブランチが `generate_title_pages.py` … の同じ行・同じ関数を変えている」に当たる。`breadcrumb_html()`・`page_html()` など別の関数だけで期ページの題名・行の案を作る方法（生成後の HTML の置換など）は、コードの形を崩すため採らなかった
  - あわせて、`work/1101-prp-pub` がマージされると期ページの決勝メンバーの行き先が X から pro/ の個別ページに変わり、前提の「押すと X へ行く形」も変わる（行の案の右端・リンクの形の判断に関わる）
  - `navbar.js` は、`/pro/` の判定の行（prp-pub）と先頭の `if`（lgn-page）、`if (newNavCurrent)` の後ろ（top）を避けて `/title/` を足せる見込み。`style.css` の title/ の節（2621〜3240 行付近）はどのブランチも変えていない
- 実装・生成・撮影はしていない（作業ブランチの差分はこのログと `docs/decisions/title.md` の追記だけ）

## 報告

- 状態: 中断（止まる条件に当たった） / 続き: CHAT-1104-TTL-02
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: なし（実装前に止まった）
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - 未マージの `work/1101-prp-pub`（CHAT-1104-PRP-08 作業中、#641）が `generate_title_pages.py` の `member_card_html()`・`period_body()` を変えており、期ページを新しい形にするにはこの2関数を変える必要がある（止まる条件）。進め方を選んでほしい: (A) `work/1101-prp-pub` のマージを待ってから、この指示を新しい Chat-Ref で貼り直す（期ページの決勝メンバーの行き先が pro/ になった後の形で作れる）／(B) 衝突を承知で今の `origin/cloudflare` の上で作る（後でマージする側が、2関数と title/ の期ページの HTML の取り込みを解く）／(C) 入口・大会ページ・共通ナビ・CSS だけを先に作り、期ページは (A) の後にする
  - 前提の「期ページの決勝メンバーは押すと X へ行く形」は、`work/1101-prp-pub` のマージ後は「個別ページのある在籍者は pro/ の個別ページ、無ければ X」に変わる
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8071c9be）: https://github.com/retroeater/mj-logs/tree/main/guide/8071c9be

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59c4b82d.md
