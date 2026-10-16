# CHAT-1016-OGR-03

- 着手日時: 2026-10-16
- 対象issue: #581, #267
- ブランチ: work/1016-ogr
- 着手時HEAD: 4f9aee6cc

## 指示

【Claude作成】Claude Code 向け指示：共通の OGP 画像を黒地・白字にし、og:locale を全ページに足す（#581・#267、プレビューで止まる） Chat-Ref: CHAT-1016-OGR-03 マージ: 判断待ちで止まる（新しい共通画像の見本を平野さんがプレビューで見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
#581 の論点整理（2026-10-16）で決まったことのうち、共通の OGP 画像の作り直しと og:locale（#267）を入れ、プレビューで止める。あわせて論点整理の決定を記録する。
決定（2026-10-16、平野さん）

* (b-1) 動画の一覧ページは、最新話のサムネイルにする。ただし帰り道の一覧は、今の合成画像（三田さんの許可を得て作ったもの、#340 で自動生成）を残す。規則は「動画の一覧は最新話のサムネイル。許可を得て合成画像を作れるものは合成画像」
* (b-2) 共通の OGP 画像（今は白地に灰色の「ryoei.pro」、`img/ogp.png`）を、title/・houou/・saikyo/ のトップと同じ黒地・白字の型で1枚だけ作り直す（「ryoei.pro」を大きく入れる）。ページの種類ごとの画像は作らない。見本はプレビューで見て決める
* (c-1) 最強戦の卓（ABEMA の回あり）の og:type は website のまま。規則は「埋め込みできる動画のページ（YouTube）は video.other ＋ og:video、それ以外は website」
* (c-2) og:locale（`ja_JP`）を、(b-2) と同じ指示で全ページに足し、#267 を閉じる（全ページの再生成を1回で済ませるため）
* (d) 凍結中の books/ と 404.html は対象外。ほかはすべて今日の規則に従う。books/ が古い画像を指したまま残るので、旧 `img/ogp.png` は消さない
* (e) 確かめ方: 見本をプレビューで止め、平野さんが承認したらマージする。マージ後、平野さんが X と LINE の投稿画面で、旧ページ・live/ の卓・最強戦の回のある卓の3つの URL を見る

前提（チャット側。平野さんの決定ではない）

* 黒地・白字の画像は `build_ogp_image.py`（title/・houou/ で使った手で実行するスクリプト）で作れる見込み（要確認）。houou/ の画像（`img/ogp/houou/index-black.png`、1200×630、大きな区分名・副題・小さな「ryoei.pro」の3段）と同じ字体・余白にそろえ、「ryoei.pro」を区分名の位置に大きく1段で置く案（副題は無し）。違う形の方がよいと考えたら、2案まで作ってよい
* 新しい画像の名前と置き場所の案は `img/ogp/site/index-black.png`（docs/notes/ogp.md「置き場所の決まり」「中身を差し替える画像はファイル名を変える」。`/img/*` は immutable のため同じ URL に上書きしない）。よりよい名前があれば変えてよい（理由をログに書く）。既定の画像は `scripts/lib/page.py` の `PageMeta.og_image` 等の既定値（要確認）。og:image:alt は今と同じ「ryoei.pro」の案
* og:locale を足す先は #267 の本文にある3か所（`lib/page.py` の `HEAD_TEMPLATE`・`generate_jpml_pros.py`・手書きのページは `apply_page_meta.py`。2026-09-13 時点の記述で要確認）。#517（`apply_page_meta.py` を消すか）は決まっていないので、この指示では消さない
* 既定の画像を使うのは、CHAT-1016-OGR-01 の `## 経過`「手順2」の表の「共通画像」の行のうち books/ を除く 22＋165＝187件の見込み（その後に LVP の B9 などで live/ の一覧が変わっていれば減る）

手順

1. 記録: #581 に上の「決定」の6点（2026-10-16）をコメントで書く。docs/decisions/seo-bing.md の OGR の節に同じ内容を足し、docs/notes/ogp.md に「ページの種類ごとの og:image・og:type の規則」の節を足す（OGR-02 と今回の決定から。追記先の今の内容を読み、矛盾していて判断が要るときだけ止まる）
2. 新しい共通画像を作り（見本は最大2案）、既定の og:image をそれに替える。旧 `img/ogp.png` は残す。あわせて og:locale `ja_JP` を全ページに足す
3. 凍結中を除く全ページを再生成し、テストを通して push し、プレビューで止まる。最終報告とログに、見本の画像の URL（プレビュー）と、確かめ用のページ（旧ページ1つ・live/ の一覧1つ）を書く。#267 はこの指示では閉じない（マージの指示で閉じる）

止まる条件

* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）が、`page.py` の `PageMeta` の既定値・`HEAD_TEMPLATE`、`generate_jpml_pros.py` の head、`apply_page_meta.py` の、同じ行・同じ関数を変えている、または取り込みで衝突する（ほかのページの本文だけを変えているブランチでは止まらず、ログに一覧を書く）
* 差分（docs/ を除く）が次の範囲を外れる:
   * 凍結中・404.html を除く全ページ: og:locale の1行が足される（ページ数をログに書く）
   * 既定の画像を使うページ: og:image（と、変えたなら og:image:width/height・alt）の行が変わる。ページ数は見込み 187件（前提のとおり。減ったら理由を書く。理由が説明できなければ止まる）
   * 新しい画像のファイル（最大2つ）、スクリプト、sitemap の `<lastmod>`（変えたページの分）
   * ほかに出た差分が、生成のたびに入るシートの変化で説明できるなら、内容をログに書いて進めてよい。説明できなければ止まる
* 再生成で凍結中の books/ が変わった（変えずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（見本の画像の URL を含める）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。マージの指示は平野さんの承認の後に別に出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1016-OGR-03` のコミット: 無し（`OGR` は同じセッションの続き）
- 作業ブランチ: ローカル・リモートとも `work/1016-ogr` はマージ済みで origin/cloudflare の祖先。`git merge --ff-only origin/cloudflare`（→ 4f9aee6cc）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る
- 重なり（`git branch -r --no-merged origin/cloudflare`、着手時）: `page.py`・`generate_jpml_pros.py`・`apply_page_meta.py` を変えていたのは `work/1016-hou4` の `generate_jpml_pros.py` だけ。
  鳳凰戦の欄のリンク先（`HOUOU_PLAYERS_URL`、`get_houou_seasons()`・`get_houou_highest_league()`）で、head（`PAGE_TEMPLATE`）とは別の行。止まらない。
  作業中に hou4 が cloudflare に入った（下の「取り込み」）

### 前提の確かめ

- 既定の画像: `lib/page.py` の `PageMeta.og_image` の既定 `https://ryoei.pro/img/ogp.png`（width 1200・height 630・alt「ryoei.pro」）。前提どおり
- og:locale を足す先: `HEAD_TEMPLATE`・`generate_jpml_pros.py` の `PAGE_TEMPLATE`・手書きのページ。前提どおり。
  ただし **`apply_page_meta.py` は実行しない**（#517 のとおり、実行すると多くのページの description が古い文に戻る）。`build_meta()` の og:locale と画像の URL だけを直し（次に使うときに旧画像に戻さないため）、
  手書きの8ページ（index・jpml_links・houou_ranking・houou_results・ouka_ranking・ouka_results・wrc_ranking・wrc_results）は og:site_name の次に og:locale を入れ、og:image の URL を置き換えた（ほかの行は変えていない）
- `build_ogp_image.py`: `--text` の和文フォントは `~/.local/share/fonts/NotoSansJP-Bold.otf`（`OGP_FONT` で差し替え）。コンテナに無いため
  notofonts/noto-cjk の `Sans/SubsetOTF/JP/NotoSansJP-Bold.otf` を scratchpad に取得した。このフォントで houou/ と saikyo/ のトップの画像を作り直すと、
  コミット済みの PNG と**バイト単位で一致**した（`--check` で「一致」）。title/・houou/ と同じ字体であることを確かめた

### 見本（2案）

置き場所・名前は前提の案のまま `img/ogp/site/`（全ページ共通 = サイト全体の区分）。

- A `img/ogp/site/index-black.png`（19,580 バイト）: `--text ryoei.pro --color '#ffffff' --bg '#000000' --max-size 400 --tracking -0.03`。title/・saikyo/ のトップと同じ指定で、幅いっぱい（1040px）
- B `img/ogp/site/index-black-b.png`（15,356 バイト）: 同じで `--max-size 180`。余白を広くした形。LINE の中央の正方形の切り抜き（docs/notes/ogp.md、#353）では A は「oei.p」あたりしか残らず、B の方が多く残る（LINE には合わせない決まり〈#353〉なので参考）
- 既定（`PageMeta.og_image`・jpml_pros・手書き）は A にした。B を選ぶなら、B を `index-black.png` の名前にせず（immutable のため、プレビュー以外で A の URL が使われる前でも混乱を避ける）、参照を `index-black-b.png` に替えて再生成し、使わない方のファイルを消す
- og:image:width/height/alt は変えていない（1200×630・「ryoei.pro」）

### 再生成と差分

- `python3 scripts/regenerate.py all`（凍結中の books_pages を除く）で成功（失敗・飛ばし無し）。テスト `python3 -m unittest discover -s scripts/tests` OK（1280件）、`workers/ask/data.json` が変わったため `node --test 'workers/ask/test/*.test.mjs'` も OK
- シートの変化による差分: 「プロ」シートから1名が外れ、鳳凰戦の JSON（houou_race/・houou/results/・houou/search.json 等）・辞書（dic/pros.json）・質問欄のデータ（workers/ask/data.json）と、
  人数の入った description（691名→690名、1,099名→1,098名、1,870語→1,869語）・houou/ のランキングの行が変わった。JSON だけを別のコミット（9d39f3117）に分けた
- **取り込み**: push 前に cloudflare に hou4（houou/ の公開）が入ったため `git merge origin/cloudflare`。衝突は生成物の `jpml_pros.html` だけで、
  branch-operations.md のとおり cloudflare 側を採って（`checkout --theirs`）全ページを生成し直した。生成し直した後の jpml_pros.html の cloudflare との差は og:locale の追加と og:image の2行だけ（hou4 のリンク先の変更は残る）。
  houou/ の JSON などシートの変化は cloudflare 側の自動の再生成で既に入っていたため、取り込み後の差分から消えた
- 取り込み後の origin/cloudflare との差（docs/ を除く、HEAD d2c5a4969）:
  - og:locale の追加: **2,352ページ**（公開の HTML 2,544 − books/ 191 − 404.html 1）。og:locale が無いのは books/ 191 と 404.html だけ
  - og:image の置き換え: **187ページ**（ルート直下 22・live/ の一覧・大会・期 165）で見込みと同じ。width/height/alt は変化なし
  - ほか: houou_leagues.html・resource_dictionary.html の description（シートの変化）、houou_race/ 13・houou_leagues_data.json・dic/pros.json（同）、新しい画像2つ、スクリプト3つ
  - books/・404.html: 変化なし。sitemap: 変化なし（`<lastmod>` はマージ後の自動の再生成で出る）
- ドキュメント: docs/notes/ogp.md に「ページの種類ごとの og:image・og:type の規則」の節を足し、「置き場所の決まり」の共通画像の記述を置き換えた。
  static-generation.md・live-page-design.md・title-pages.md・saikyo-page-design.md の「共通の `img/ogp.png`」を新しい画像に置き換え、live-page-design.md の個別ページに og:type video.other（OGR-02 の分）を書いた
- #581 に決定の6点をコメントした。#267 はこの指示では閉じていない

### プレビュー（d2c5a4969、Workers Builds: mj success）

- 見本 A・B とも 200・image/png、A は手元のファイルと一致
- 確かめ用のページ（旧ページ `jpml_links.html`、live/ の一覧 `live/index.html`）: og:locale `ja_JP` と og:image `https://ryoei.pro/img/ogp/site/index-black.png` を返す
- og:image は本番の絶対 URL（`https://ryoei.pro/...`）なので、プレビューのページを X・LINE に貼っても画像は出ない（本番にはまだ無い）。
  **本番の画像の URL はマージ・デプロイの前に開かない**（404 が immutable で CDN に載る、docs/notes/ogp.md「デプロイ直後の CDN」）。この作業でも開いていない
- プレビューの URL はターミナルの最終報告にだけ書く

## 報告

- 状態: 判断待ち / 続き: CHAT-1016-OGR-04
- ブランチ: work/1016-ogr
- ログ: https://github.com/retroeater/mj/blob/work/1016-ogr/docs/logs/CHAT-1016-OGR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-ogr
- 確認用URL: プレビューあり（URL は最終報告）。見本の画像 `img/ogp/site/index-black.png`（A、既定にした方）・`img/ogp/site/index-black-b.png`（B）、確かめ用のページ `jpml_links.html`（旧ページ）・`live/index.html`（live/ の一覧）。この版の印: 確かめ用のページの og:image が `…/img/ogp/site/index-black.png`
- マージ: 未（平野さんの判断待ち）
- issue: #581（決定のコメント）、#267（マージの指示で閉じる）
- 判断が必要なこと:
  - 見本 A（幅いっぱい）と B（余白を広く）のどちらにするか。A のまま承認ならそのままマージでき、B なら参照を替えて再生成する（`## 経過`「見本（2案）」）
  - マージの承認（承認後、X と LINE で旧ページ・live/ の卓・最強戦の回のある卓の3つの URL を見る、(e)）
  - (b-1) によれば live/ のトップ・タイトル戦・期も「動画の一覧」で最新話のサムネイルになるが、この指示では指示の見込みどおり共通の画像にした（LVP の B9 で入る予定、docs/decisions/live.md）
- 未確認の項目: X・LINE での見え方（マージ後に平野さんが見る）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 065b75e3）: https://github.com/retroeater/mj-logs/tree/main/guide/065b75e3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/065b75e3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
