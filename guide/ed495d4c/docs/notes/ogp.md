# OGP とカード表示の知見

OGP 画像・og:title を作る／変えるときに読む。#339（「帰り道」のOGP画像、2026-09-16）の振り返りから書いた。
観察はいずれも 2026-09-16 時点のもので、X・LINE の仕様は変わりうる。

## 置き場所の決まり

- ページ別の OGP 画像は `img/ogp/<区分>/` 以下に置く
  （本文は `docs/notes/static-generation.md`「メンテナンス用スクリプトの詳細」の `build_wayhome_ogp.py` の行と、
  `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- 全ページ共通の画像（`PageMeta.og_image` の既定）は `img/ogp/site/index-white.png`（白地・黒字の「ryoei.pro」、余白を広くとる。告知動画の締めの画面のイメージ、#581）。
  旧 `img/ogp.png`（白地、#78）は凍結中の books/ が指したまま残るので消さない
- 現在の実例（文字ベースの画像は4種類、#581）: 共通の `img/ogp/site/index-white.png`、`img/ogp/title/index-white.png`（title/ の全ページ、`docs/notes/title-pages.md`）、`img/ogp/houou/index-white.png`（houou/ の全ページと旧 `houou_*.html`、`docs/notes/houou-top.md`「共通」）、`img/ogp/saikyo/index-white.png`（最強戦のトップと、卓・カードの代わりの画像、`docs/notes/saikyo-page-design.md`）。ほかに帰り道の一覧の `img/ogp/wayhome/index-<最新話の公開日>.jpg`（`docs/notes/video-wayhome.md`）

## ページの種類ごとの og:image・og:type の規則

2026-10-16 の決定（#581、docs/decisions/seo-bing.md の CHAT-1016-OGR-02・03）。凍結中の books/ と 404.html は対象外。

| ページの種類 | og:image | og:type |
|---|---|---|
| 動画1本のページ（YouTube。帰り道・Mつく・女流勉強会の各話、live/ の卓・対局） | その動画のサムネイル（使える最大の画質: maxresdefault、無ければ sddefault、それも無ければ hqdefault。統一はしない） | video.other ＋ og:video（`lib.wayhome.embed_url()` の埋め込みの URL） |
| 動画の一覧ページ（Mつく・女流勉強会の一覧など） | 最新話のサムネイル。許可を得て合成画像を作れるもの（帰り道の一覧、#340）は合成画像 | website |
| 最強戦の卓（ABEMA） | ABEMA の回があればその回のサムネイル（ヒーローと同じ）、無ければ作品「麻雀最強戦」の画像 | website（ABEMA は埋め込みの URL を出していない） |
| 最強戦の年度・対局 | 作品「麻雀最強戦」の画像 | website |
| title/ の全ページ・houou/ の全ページと旧 `houou_*.html`・saikyo/ のトップ | それぞれ「タイトル戦」「鳳凰戦」「麻雀最強戦」の1枚。共通画像と同じ白地・黒字で、区分名を大きく（幅に合わせ、上限 200px）、その下に「ryoei.pro」（48px）。`build_ogp_image.py --text <区分名> --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03` | website |
| ほかのページ（下位メニューしか無いグループ〈連盟・女流桜花・WRC・番組・リソース・良栄〉の下のページなど） | 共通の `img/ogp/site/index-white.png`。ページの種類ごとの画像は作らない | website |

文字ベースの画像（サムネイルなどの無いページで使うもの）は、メニューの最上位でそれ自体がページになっているものだけ作る（ryoei.pro・鳳凰戦・麻雀最強戦と、例外のタイトル戦の4種類。サブメニューで細かく分けない、2026-10-16、#581）

- 全ページに og:locale `ja_JP`（#267）。生成のページは `lib/page.py` の `HEAD_TEMPLATE` と `generate_jpml_pros.py`、手書きのページは head に直接書く
- 規則は「埋め込みできる動画のページ（YouTube）は video.other ＋ og:video、それ以外は website」
- 許した og:image の形（文字ベースの画像の一覧・サムネイル・合成画像）は `scripts/tests/test_og_image.py` が全ページで確かめる。区分の画像を足すときは平野さんの決定を取り、テストの許可の一覧も直す
- 区分ごとの画像などの選択肢を出す前に、その区分がメニューでそれ自体がページか（`navbar.js`）を確かめる（下位メニューしか無いグループには画像を作らないため）
- 見本はチャット側でも同じ `build_ogp_image.py` で作れる（字体は `OGP_FONT` で差し替える。チャットのサンドボックスでは `/usr/share/fonts/opentype/noto/NotoSansCJK-Bold.ttc`）。字体のファイルが Code と違い文字によっては差が出るので、見本の用途に限る
- 残りの作業の行き先（#581 を 2026-10-18 に閉じた）: live/ のトップ・大会・期の og:image を最新話のサムネイルにする件（LVP の B9）は #362、ouka/ の「女流桜花」の OGP 画像（2026-10-17 の決定）は #591、女流勉強会の og:title・og:image の確かめは #576 のチェックリスト

## 中身を差し替える画像はファイル名を変える

- **中身を差し替える OGP 画像は、同じURLを使わず、ファイル名を変える。** `/img/*` は `_headers` で
  `Cache-Control: public, max-age=31536000, immutable` のため、同じURLのまま中身を変えると CDN・ブラウザ・X が古い画像を使い続けうる
- 名前はデータから決まる値にする（生成日にしない）。データが変わらなければ名前も中身も変わらず、差分が出ない
- 帰り道の実例（CHAT-0916-XC-06、#340）: 一覧用は `img/ogp/wayhome/index-<最新話の公開日 JST YYYYMMDD>.jpg`。
  名前は `scripts/lib/wayhome.py` の `list_og_image_name()` だけで決め、生成（`build_wayhome_ogp.py`）とページ（`generate_video_wayhome.py`）の両方が使う。
  古い `index*.jpg` は削除して1枚だけ置き、同じ名前で中身が変わる場合は上書きせずエラーで止める。
  自動生成（#340）でもこの関数で名前を決め、生成した画像とページを同じコミットに入れる
- 上書きする設計を決める前にキャッシュ設定を確認すること、404 にも1年のキャッシュが付くため画像とページを同じコミットに入れることは、
  画像の差し替え全般に関わるため `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」に書いた
- 意匠を作り直した実例（#581、2026-10-16）: 区分の画像を黒地から白地にしたとき、接尾辞を `-black` から `-white` に変えて新しい名前で作り、旧ファイルを削除して、画像とページを同じコミットに入れた。
  最強戦の年度の画像（`<年度>-black.png`、#343）は同じ時に、代わりの画像を「麻雀最強戦」の画像に替えて削除した

## X のカード（`summary_large_image`）で観察したこと

- **og:title が画像の左下に帯として重なる。og:description は表示されない。** 画像の下の方は帯で隠れることがある。帯は PC の X で確かめる（iPhone の X アプリの投稿画面には出ない）
- **画像の下に「<ドメイン>から」と表示される。** 画像に `ryoei.pro` を入れる必要はない
- **取得結果をキャッシュする。** 以前に貼ったURLは古い表示が残るので、確認では `?x=<未使用の数字>` を付けて貼る
- 投稿画面でカードがすぐ出ないことがある。少し待つか、番号を変えて貼り直してから原因を調べる
- 投稿画面の右下で回っている丸は文字数カウンターで、取得中の表示ではない

## LINE で観察したこと

- og:title、og:description、画像が出る。画像は右側に正方形に近い形で縮小表示される。画像は遅れて出ることがあり、出ていないと決める前に少し待って見直す
- **横長の画像は正方形に切り抜かれ、中央しか見えない。** 最強戦の年度ページ（1200×630、「麻雀最強戦」の段が幅いっぱい）では「雀最強」だけが見えた（2026-09-16）
- **OGP 画像は X のカード（横長）を基準に作る。LINE の正方形の切り抜きには合わせない**（X が主な用途で、LINE のサムネイルは小さいため。#353、平野さん判断）

## デプロイ直後の CDN

- Workers Builds の成功から1〜2分は、古い画像や削除済みのファイルが返ることがある
  （`/img/*` は `_headers` で `immutable`。CHAT-0916-OG-09 のログに実測がある）
- その間に X が取得すると、古い結果がキャッシュに残りうる。画像を差し替えたら数分おいてから、番号を変えて確認する
- **新しい画像の本番の URL は、本番の HTML の og:image が新しくなった（デプロイが済んだ）ことを確かめてから開く。**
  デプロイ前に開くと 404 が `cf-cache-status: HIT`・`immutable` で CDN に載る（#232 の DUP-08 で、確認の curl が `img/ogp/title/houou-black.png` の 404 を載せた）。確かめはプレビューの URL で行う

## タイトルの決め方（帰り道の実例）

- **og:title はカードの帯に出るので短くする**（例: 「<大会名> <選手名> | 帰り道 | ryoei.pro」）
- **`<title>` は検索結果に出るので、正式名など検索される語を残す**（例: 「… | 帰り道ついていってイイっすか | ryoei.pro」）
- 両者を分ける仕組みは `PageMeta.og_title`（`docs/notes/static-generation.md`）
- **共有ボタンの文言は `<title>` と同じになる作り（#191）。** `<title>` を変えると共有ボタンの文言も変わる
  （CHAT-0916-XC-02 で、差分が想定より1行多くなって止まった）

## デザインの教訓（#339）

- **素材（YouTube のサムネイルなど）に文字がすでに焼き込まれている場合、OGP 画像に文字を重ねると重複する。**
  見出しは X の帯（og:title）で補えるので、素材はそのまま大きく見せる方がよい
- **試作は比較用の画像だけで判断しない。** 1ページ分を本番に出し、X と LINE の投稿画面で確認してから、全ページに広げる。
  #339 では、6案から選んだ文字入りの額縁デザインで38枚を生成・マージした後に、X・LINE で見て作り直しになった
