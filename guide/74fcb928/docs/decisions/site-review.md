# サイト全体の横断レビューと DESIGN.md

分野の決定の記録。書き方は [README.md](README.md)。

## 2026-10-06（CHAT-1006-SWP-01）

- レビューの目的は、現行サイトを直すことと、新サイト（#296）の要件を洗い出すことの両方。現行サイトで直すのは小さいものだけ
- DESIGN.md を作る。中身は既決の値を集めたものにする。不足している部分・不整合の洗い出しは別の issue にする
- 凍結・廃止予定・作り直し予定のページ（`books/`・`saikyo_mens.html`・ランキング3ページ・`index.html`）はレビューの対象外
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-02）

- 横断レビューの指摘は3段で直す。第1弾は手書きのファイルだけ（生成物に触らない）
- `rh_links.html` の「GitHub」のリンク（`retroeater/mj`。非公開のため訪問者には 404）は外す
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-03）

- DESIGN.md は `docs/notes/design.md` に置く（2026-10-06 の「DESIGN.md を作る」の置き場所）
- `live/` の濃色を、濃色の例外として記録する（`docs/new-site-design.md` §2 の「この1系統に限る」を「動画の2系統」に置き換えた）
- 既存の issue の範囲の指摘（#411・#250・#267・#202・#274 など）は今回は直さず、各 issue に所見をコメントする
- G1-10（写真の上の名前のコントラスト）は現行では見送り、デザインの不足・不整合の issue に入れる
- G4-04（ページ送りの後のスクロール位置）は見送る（#184 の意図どおり）
- G4-08（`title/` の大会の選択欄がキーボードの ↓ で即座に移動する）は別の issue に起票して後回しにする
- G5-05（帰り道の2ページで title と説明が同じ）は直す。呼び分けは未定で、この指示では起票だけ
- 横断レビューの指摘は3段で直す（第1弾は手書きのファイルだけ、第2弾は共通ナビとスキップリンク、第3弾は実機で再現したスマホの個別の崩れ）
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-04）

- `houou_race`（鳳凰戦「順位変動」）は公開されたので、横断レビューの対象に含める
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-05）

- `404.html` の右の余白（G3-10）は第2弾（`style.css` を触る段）で直す。`style.css` に `.mj-margin-text` の右の余白を足す形で、`404`・`jpml_links`・`rh_links`・`resource_dictionary` をまとめて直す
- `jpml_links.html` のアイコンの `alt` は空にする（どの団体の「公式サイト」かは見出しで分かる）
- `rh_links.html` に残すリンクは Bootstrap・Google Search Console・Google Sheets・PageSpeed Insights の4本だけ。ほかの11本は消す
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-06）

- work/1009-swp-fix のプレビュー（CHAT-1009-SWP-05 の後の状態）を見て OK。cloudflare へマージしてよい（横断レビューの第1弾: `404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js`）
- `rh_links.html` の廃止を、別の issue に起票する
- G5-07（`sitemap.xml`・`sitemap-pages.xml` の冒頭コメントの件数が古い）は、houou/（#518）の公開の issue で sitemap を触るときに直す
- CHAT-1009-SWP-03 の起票で自動で作られたラベル「対象: title」は残す
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-07）

- `rh_links.html` は転送（301）せず、404 にする
- #531（`rh_links.html` の廃止）には今すぐ着手する（鳳凰戦の新ページ〈#518〉の公開を待たない）
- 作業中の回答: なし

## 2026-10-09（CHAT-1009-SWP-09）

- work/1009-swp-rhl のプレビュー（CHAT-1009-SWP-07）を見て OK。cloudflare へマージしてよい（`rh_links.html` の廃止、#531）
- `scripts/apply_page_meta.py` に残る `rh_links.html` の項目は、今は触らず、このスクリプトの扱いを決める #517 に任せる
- 作業中の回答: なし
