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

## 報告

- 状態: 作業中
- ブランチ: work/1105-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1105-ttl/docs/logs/CHAT-1105-TTL-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9c57896）: https://github.com/retroeater/mj-logs/tree/main/guide/d9c57896

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9c57896/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
