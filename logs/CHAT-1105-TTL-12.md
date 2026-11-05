# CHAT-1105-TTL-12

- 着手日時: 2026-11-05
- 対象issue: #669（参照）
- ブランチ: work/1105-ttl
- 着手時HEAD: 275d81111

## 指示

【Claude作成】Claude Code 向け指示：title/ の全ページの配置を houou/ の基本の形（幅 400px の中央寄せ）にそろえ、配置のテストの例外から title/ を外す（プレビューまで） Chat-Ref: CHAT-1105-TTL-12 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-11 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SKN 系列（最強戦のリンク・配置の統一、#669）の伝言を受けて、title/（入口・大会ページ・期ページ）の配置を、一覧・成績のページの基本の形にそろえる。
決定（2026-11-05、平野さん）

* 一覧・成績のページは houou/ の基本の形（幅 400px の中央寄せ。docs/notes/houou-top.md「幅は全ページ 400px まで〈PC でも同じ〉」）にそろえる。title/ もその対象（SKN 系列で決めた決定。CHAT-1104-SKN-06・07）
* title/ の入口も 400px にそろえる（入口の写真カードは PC でも2列になる。それを受け入れる）
* マージはプレビューを見てから決める

前提（チャット側。平野さんの決定ではない）

* 直す差異は、mj-logs の `logs/CHAT-1104-SKN-06.md` の差異の表（title の3行: top・contest・term）にあるもの: 本文の列が 960px の中央寄せ／パンくずの行 44px・下 8px（基本は行 20px・下 4px、押せる 44px は負の余白で吸収）／説明文 `.mj-lead` の左右 4px（基本は列と同じ・左右 20px）／入口の題名が見えない（40px/500）／入口の検索欄が独自（`mj-saikyo-n-search-input`。基本は `.mj-houou-search`、列の中・高さ 44px）。表は TTL-05〜07 のマージの前後どちらの版で測ったか（要確認）。今の cloudflare で測り直し、もうそろっている項目は直さない（測った値を報告に書く）
* 入口の題名は「現タイトルホルダー ▾」の選ぶボタン（docs/notes/design.md「選ぶボタンを兼ねる題名」、28px 太字・▾ は紺）にそろえる案。houou/ の順位変動・最強戦の年度ページと同じ形（作りは任せる）
* 入口の写真カードは列の幅（400px から左右の余白を引いた幅）で2列にする。写真カードの文字の大きさの判定（`fitCardLabels()`・`--mj-title-taikai-em`）が新しい幅でも働くことを確かめる
* SKN-06 で足した配置のテスト（`scripts/tests/` の、新サイトの形のページの本文の列を確かめるもの）は title/ を一時の例外に入れている。そろえたら例外から外し、テストが通ることを完了の目安にする
* docs/notes/title-pages.md・docs/notes/design.md（「本文の列」の行の title/ の 960px など）を今の形に直す。docs/decisions/title.md に上の決定を書く

手順

1. 今の cloudflare の title/ の入口・大会ページ（`/title/wakajishi/`・`/title/houou/`）・期ページ（`/title/wakajishi/9.html`・`/title/pro-queen/24.html`）を、houou/ の基本の形と比べて測り直し、SKN-06 の表の項目ごとに「今の値／基本の値／直すか」を報告に書く
2. 差異を直し、配置のテストの例外から title/ を外す。docs を直す
3. 生成・テストを通し、360px と 1280px、明・暗で、入口（既定・`?year=2025`・`?q=白鳥`）・大会ページ・期ページを撮って、本文の列・パンくず・題名・検索欄・説明文の位置を基本の値と並べて報告に書く。行の状態（› の有り無し・写真の有り無し）ごとの順位の右端の差が 0px のままであることも確かめる（TTL-07 と同じ測り方）。プレビューのビルドが付くこと（docs だけの push では付かない）を確かめ、確認用 URL を出す

止まる条件

* 生成物の差分に、title/ の HTML・CSS／JS・`sitemap-title.xml` の lastmod・docs 以外の変化（ほかのページの HTML、`search.json`・`years.json` の中身）が出た（想定外の行を全部書いて止まる）
* 直すために共通の CSS（`.mj-houou-*`・`.mj-saikyo-n-*` など）を変える必要があり、houou/・最強戦などほかのページの見た目が変わる（変えずに止まって報告する）
* 未マージの work/ ブランチ（`work/1101-prp-pub` を含む）が `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・配置のテストの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 配置のテストが title/ を例外から外した後に通らず、直し方が決まった値の表（docs/notes/design.md）に無い

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、決めずに置いた値（決まった値の表に無い値）の一覧を1件ずつ書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告に「確認用:」の行を書く。Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-TTL-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-TTL-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1105-TTL-12'` は0件。識別子 TTL は同じセッションの続き（TTL-01〜11）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- 作業ブランチ: ローカル・リモートとも `work/1105-ttl` が無かったため `git checkout -b work/1105-ttl origin/cloudflare` で作り、ログを先に push（e3ead5819）。手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 未マージのブランチ: `generate_title_pages.py`・`assets/title.js`・`style.css`・`scripts/tests/test_new_site_column.py` を変えているのは `work/1101-prp-pub`（`member_link()`・`member_holder()`・`period_body()`・`pages()` と import。変える `page_html()`・`search_html()`・CSS の配置の規則・配置のテストとは別の箇所）と `work/1104-lgn-policy`（style.css の末尾）。重なり・衝突なし
- 手順1（今の cloudflare〈e3ead5819 の時点の生成物〉を、ローカルの配信と headless Chromium で測った。左端・幅・上端・高さは px。基本の値は houou/players/）:

  | 項目 | 基本（houou/、1280px） | title/ の今の値（1280px） | 直すか |
  |---|---|---|---|
  | 本文の列 | 根 `.mj-race`、左 440・幅 400、内側の余白 16px 16px 28px | 根 `.mj-title`、main の 960px の中央寄せ（左 176・幅 928、余白 0）。360px では左 16・幅 328 | 直す |
  | パンくずの行 | 高さ 20・下 4px（上端 64） | 高さ 44・下 8px | 直す |
  | 題名 | 28px・700、上端 88（パンくずの下） | 大会ページ・期ページは 28px・700 で上端 116。入口は「現タイトルホルダー ▾」の選ぶボタン 28px・700 で見えている（h1 は読み上げ用） | 入口の題名はもうそろっている（SKN-06 の表の「見えない〈40px/500〉」は TTL 系列の作り直しの前の版の値）。上端はパンくずの行を直すとそろう |
  | 検索欄（入口） | `.mj-houou-search`、高さ 44・溝の色 rgb(233,233,238)・角丸 10px | `.mj-saikyo-n-search`、高さ 44・面の色の白・角丸 12px（列の中） | 直す（live/ のトップと同じ見た目に） |
  | 説明文 `.mj-lead` | 幅 400 の列・左右 20px | 列の中・左右 4px | 直す |

- 手順2（47dee7461）:
  - 本文の根を `<div class="mj-saikyo mj-title mj-title-…">` にし（`page_html()`）、`style.css` の「幅 400px の中央寄せ」の規則（`--mj-saikyo-max-w: 400px`・`.mj-saikyo` の余白と中央寄せ・`.mj-lead` の左右 20px・パンくずの行の高さ）のセレクタ `body:is(.mj-saikyo-n-page, .mj-live-n-page)` に `.mj-title-n-page` を足した（6規則。ほかのページには `mj-title-n-page` が無いので、houou/・最強戦・live/ の見た目は変わらない）。title/ の節の `body:has(.mj-title) main {max-width: 960px; …}` と `body:has(.mj-title) .mj-lead {max-width: none}` を消した
  - 入口の検索欄に `mj-title-search` を足し、live/ のトップの検索欄の規則（溝の色・角丸 10px・左右 12px・虫眼鏡 20px・フォーカスの枠）のセレクタに `body.mj-title-n-page .mj-title-search` を並べた
  - 配置のテスト（`scripts/tests/test_new_site_column.py`）: title/ の一時の例外を外し（`TEMPORARY_EXCEPTIONS = ()`）、`CENTERED_BODIES`・`CENTERED_SCOPE` に `mj-title-n-page` を足し、title/ の検索欄の見た目の期待値と `test_title_pages_are_centered` を足した。生成し直す前の HTML では `test_new_site_pages_use_centered_column`・`test_title_pages_are_centered` が FAIL、生成し直した後は 2032 tests OK
  - 文書（ea812093a・…）: docs/notes/design.md（「本文の列」の行の適用範囲、title/ の行）、docs/notes/title-pages.md、docs/notes/saikyo-page-design.md（960px のままと書いた行）、docs/decisions/title.md（この指示の決定）
- 生成（`regenerate-page.yml` target_page=title_pages、run 41973685940 success、723733fc3）: 386 ファイル。title/ の HTML 385（どれも根の class の1行の書き換えだけ。入口だけ、さらに検索欄の class の1行）と `sitemap-title.xml`（lastmod の 385 行だけ）。`search.json`・`years.json`・ほかのページは変化なし
- 手順3（直した後、ローカルの配信、headless Chromium）:

  | 項目 | 基本（houou/players/） | title/（入口・大会ページ・期ページ） |
  |---|---|---|
  | 本文の列（1280px） | 左 440・幅 400、余白 16px 16px 28px | 全ページ 左 440・幅 400、余白 16px 16px 28px |
  | 本文の列（360px） | 左 0・幅 360 | 全ページ 左 0・幅 360 |
  | パンくずの行 | 左 456（16）・上端 64・高さ 20・下 4px | 大会ページ・期ページとも同じ（入口はパンくずなし） |
  | 題名 | 上端 88・28px・700 | 大会ページ・期ページ 上端 88・28px・700（期ページは2行目に対局日で高さ 55）。入口は選ぶボタン 上端 64・28px・700（live/ のトップと同じ） |
  | 検索欄 | 高さ 44・溝の色・角丸 10px（上端 139、パンくずあり） | 入口 高さ 44・溝の色・角丸 10px・左 456（上端 124、パンくずなし） |
  | 説明文 | 幅 400 の列・左右 20px | 全ページ 幅 400 の列・左右 20px |

  - 撮影: 入口（既定・`?year=2025`・`?q=白鳥`）・大会ページ（鳳凰戦・王位戦・若獅子戦）・期ページ（第41期鳳凰戦・第42期十段戦・第26期王位戦・第9期若獅子戦・第24期プロクイーン）× 360px・1280px × 明・暗 = 44 通り。はみ出し・切れ・重なり 0件（`docs/logs/CHAT-1105-TTL-12/measure.json`、画像は 360px の22枚と 1280px の3枚）
  - 行の状態ごとの順位の右端（TTL-07 と同じ測り方、360px、全 385 ページ）: 期ページ 364・1492 行、› の有り無しが混ざる期ページ 178、右端の差の最大 0px。検索欄は入口の1つだけ
  - 入口の写真カード: 360px・1280px とも2列（カードの幅 160px・180px）。`--mj-title-taikai-em` は 12、帯の字は 14px・14.33px（下限 14px）で、最も長い大会名（女流プロ麻雀日本シリーズ）は帯が2行（下限で収まらない長い大会名だけ2行になる、の今の規則どおり）。`fitCardLabels()` の `.is-text-large` は付かない（ルートの文字 200% でも付かない。帯と名前の字は px と cqi で決まり、ルートの文字の大きさに連動しないため。前と同じ）
  - 期ページの決勝ライブ・決勝動画・帰り道の動画の並び（`minmax(240px, 1fr)`）は、400px の列で1列になる（1280px で前は3列）
- レビュー（新しい文脈の general-purpose、1回目）: 正しさの指摘なし（共有のセレクタに足した `.mj-title-n-page` は title/ の外に無い、`.mj-saikyo` の根で title/ に新しく当たるのは `.mj-saikyo` と `body:has(.mj-saikyo) .mj-lead` だけで後ろの規則が上書きする、消した main の規則に頼る部品は無い）。古いコメント・記述の指摘（style.css の title/ の節の前置き・写真カードの列のコメント、テストのコメント、docs/notes/title-pages.md・saikyo-page-design.md）は直した（da6c97ed9 ほか）。`test_title_pages_are_centered` が全体のテストと重なる点は、live/ のテストと同じ形なので残した
- プレビュー: 47dee7461・723733fc3・da6c97ed9 の「Workers Builds: mj」は success。最後は da6c97ed9（コメントだけの変更、見た目は 723733fc3 と同じ）。プレビューの 360px で入口・鳳凰戦の列・パンくず・説明文の位置がローカルと同じことを確かめた（URL は最終報告）

## 報告

- 状態: 判断待ち / 続き: CHAT-1105-TTL-13
- ブランチ: work/1105-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1105-ttl/docs/logs/CHAT-1105-TTL-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-ttl
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: 入口（既定・?year=2025・?q=白鳥）・鳳凰戦・王位戦・若獅子戦・第9期若獅子戦・第24期プロクイーンほか。この版の印: PC 幅でも入口の写真カードが2列で、本文が幅 400px の中央に寄る
- マージ: 未（プレビューを見てから決める。平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - マージの可否（プレビューを見て）
  - 決めずに置いた値（決まった値の表〈docs/notes/design.md〉に無い値）: なし。使った値は houou/ の基本の形（400px・余白 16px 16px 28px・説明文 20px・パンくずの行 20px／下 4px）と live/ のトップの検索欄の値（溝の色・角丸 10px・左右 12px）だけ
  - 見た目の変わり方（決定の帰結として書く）: 期ページの決勝ライブ・決勝動画・帰り道の動画の並びは、PC 幅でも1列になる（前は 1280px で3列）。最も長い大会名（女流プロ麻雀日本シリーズ）の写真カードの帯は PC 幅でも2行になる
- 未確認の項目:
  - 実機（iPhone）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 57988657）: https://github.com/retroeater/mj-logs/tree/main/guide/57988657

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/57988657/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
