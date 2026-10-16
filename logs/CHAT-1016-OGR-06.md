# CHAT-1016-OGR-06

- 着手日時: 2026-10-16
- 対象issue: #581
- ブランチ: work/1016-ogr
- 着手時HEAD: 701a69219

## 指示

【Claude作成】Claude Code 向け指示：文字ベースの OGP 画像をメニュー最上位の4種類に絞ってマージする（OGR-05 の続き） Chat-Ref: CHAT-1016-OGR-06 マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-ogr を続けて使う（CHAT-1016-OGR-05 で判断待ちにした変更を直してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。生成物の衝突は docs/notes/branch-operations.md のとおり cloudflare 側を採って生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-OGR-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-OGR-06` を足す。

目的
CHAT-1016-OGR-05 の判断待ちへの回答。文字ベースの OGP 画像を、メニューの最上位でそれ自体がページになっているものに絞り、マージする。
決定（2026-10-16、平野さん）

* 文字ベースの OGP 画像（サムネイルなどが無いページで使うもの）は、メニューの最上位でそれ自体がページになっているものだけ作る。ryoei.pro（共通、`img/ogp/site/index-white.png`）・鳳凰戦・麻雀最強戦（今の「麻雀最強戦」のまま）の3つと、例外のタイトル戦（メニューでは「連盟」の下だが専用の画像にする）の、合わせて4種類。サブメニューで細かく分けない
* title/ は全ページ（入口・大会・期）で「タイトル戦」の画像を使う。大会ごとの画像は作らない（#232 の大会ごとの出し分けを置き換える）
* houou/ の全ページと、旧 `houou_*.html`（転送で消える予定のもの）は「鳳凰戦」の画像
* saikyo/ のトップは「麻雀最強戦」の画像。年度・対局・卓は今のまま ABEMA の画像
* 下位メニューしか無いグループ（連盟・女流桜花・WRC・番組・リソース・良栄）の下のページは、共通の ryoei.pro の画像（グループそのもののページが無いため画像は作らない）
* 最強戦の卓・カードで ABEMA の画像が読めないときの代わりの画像を「麻雀最強戦」の画像（`img/ogp/saikyo/index-white.png`）に替え、年度の画像16枚（`img/ogp/saikyo/<年度>-black.png`）を消す
* 動画のページ（サムネイルのあるもの）は今までどおり
* この内容で cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 4種類の画像は、OGR-05 で作った `img/ogp/site/index-white.png`（既存）・`img/ogp/title/index-white.png`・`img/ogp/title/houou-white.png`・`img/ogp/saikyo/index-white.png` と同じもの（チャット側が同じスクリプト・同じ指定で作り、鳳凰戦は画素まで一致を確かめた）。鳳凰戦の画像は houou/ のものなので、`img/ogp/houou/index-white.png` に置く案（title/ からは参照しなくなるため）。よりよい置き場所があれば変えてよい（理由をログに書く）
* 大会ごとの白地の画像20枚は本番に出ていない（OGR-05 で作ったまま未マージ）ので、消してよい
* 消す前の参照の確かめは OGR-05 の手順2と同じ（`git grep`、docs/logs・docs/decisions を除く）
* 残りの課題（#581 に控えとして書く）: 最強戦の卓の og:title が長く、X のカードの帯で「…麻雀最…」と切れる（2026-10-16 に平野さんが X で確認。docs/notes/ogp.md「og:title はカードの帯に出るので短くする」）。対応は後日

手順

1. title/ の全ページを「タイトル戦」の画像に、houou/ と旧 `houou_*.html` を「鳳凰戦」の画像に替え、大会ごとの画像20枚を消す。最強戦の代わりの画像を「麻雀最強戦」の画像に替えて、年度の画像16枚を消す。凍結中を除いて再生成し、テストを通す
2. 記録: #581 に上の「決定」の8点（2026-10-16）と、前提の「残りの課題」をコメントで書く。docs/decisions/seo-bing.md の OGR の節に足し、置き換わる決定（title.md の #232 の大会ごとの画像、houou.md、saikyo.md の年度の画像・代わりの画像）に「→ 置き換え: 2026-10-16（CHAT-1016-OGR-06）」を付け、docs/notes/ogp.md の規則の節と実例の行、関係する docs/notes（title-pages.md・houou-top.md・saikyo-page-design.md など）を今の形に直す
3. origin/cloudflare を取り込んで再検証（再生成・テスト）し、cloudflare へマージする。マージ後、本番の HTML の og:image が新しくなった（デプロイが済んだ）ことを確かめてから、4種類の画像の URL を `?v=<未使用の値>` 付きで開き 200・image/png を確かめ、`title/`・`title/houou/`・`houou/`・`saikyo/`・`houou_results.html`・最強戦の卓1つの og:image（卓は代わりの画像の指定も）をログに引用する（docs/notes/ogp.md「デプロイ直後の CDN」）

止まる条件

* 差分（cloudflare との差。docs/ を除く）が次の範囲を外れる:
   * title/（384件）・houou/（13件）・旧 `houou_*.html`（4件）・`saikyo/index.html`: og:image の行（と og:image:alt）だけ
   * 最強戦の卓のページ（OGR-05 のログで 643件）と、代わりの画像を指定しているほかのページ: 代わりの画像の指定だけ（件数をログに書く）
   * 画像の追加・削除、`build_ogp_image.py` と生成スクリプト・テストの変更
   * 取り込んだ cloudflare の変更・生成のたびに入るシートの変化で説明できる差分は、内容をログに書いて進めてよい。説明できなければ止まる
* 旧 `img/ogp.png`・`img/ogp/site/index-white.png`・帰り道の画像を消す・変える差分が出た。凍結中の books/ が変わった
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が、title/・houou/・saikyo/ の生成スクリプトの OGP・代わりの画像の部分や `build_ogp_image.py` の、同じ行・同じ関数を変えている、または取り込みで衝突する（重なる区分だけ外して残りを進めてよい。全部が重なるときだけ止まる）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（残した画像・消した画像の一覧を含める）
* マージは冒頭の「マージ:」の行のとおり（マージ後の自動の再生成〈regenerate-page.yml〉で変わる範囲も、上の差分の範囲で見る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1016-OGR-06` のコミット: 無し。作業ブランチ: ローカルの `work/1016-ogr` はリモート（701a69219）と一致、未マージ。`merge-base --is-ancestor origin/cloudflare HEAD` は偽 → `git merge origin/cloudflare`（衝突なし）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行は4行とも有る。OGR-05 の `## 報告` の状態を「判断待ち / 続き: CHAT-1016-OGR-06」にした
- 重なり: 未マージの `work/` 4本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes）は、title/・houou/・saikyo/ の生成スクリプトと `build_ogp_image.py`・`apply_page_meta.py` を変えていない

### 実装

- 画像: 4種類は OGR-04・OGR-05 で作ったものをそのまま使う。鳳凰戦は `img/ogp/title/houou-white.png` を `git mv` で **`img/ogp/houou/index-white.png`** に移した（前提の案のまま。houou/ と旧 `houou_*.html` だけが使い、title/ からは参照しないため）
- title/: `og_image_for()`・`ogp_design()`・`OGP_DIR`・`OGP_URL_BASE`・`OGP_DESIGN`・`OGP_DESIGN_BY_SLUG`・`OGP_INDEX_SLUG` をやめ、全ページ（入口・大会・期）で定数 `OG_IMAGE`（`img/ogp/title/index-white.png`、alt「タイトル戦」）を使う。
  テスト `test_title_ogp.py` の `OgImageTest` を、画像が実在すること・page_meta が使うこと・`img/ogp/title/` の画像が入口の1枚だけであることを確かめる形に書き直した
- 鳳凰戦: 指定を `generate_houou_race.py` の `OG_IMAGE` 1か所に置き（`generate_houou_pages.py`・`generate_houou_leagues.py` がどちらも読み込むため）、3つの生成で使う。手書きの `houou_results.html`・`houou_ranking.html` は og:image と alt（「ryoei.pro」→「鳳凰戦」）を直接直した
- 最強戦: `year_ogp_path()` が全年度でトップの画像（`../../img/ogp/saikyo/index-white.png`）を返すようにし、年度の画像用の `OGP_DESIGN` をやめた（`TOP_OGP_DESIGN` だけ残す）
- `build_ogp_image.py`: 使い方の例に年度の画像の名前（`2026-black.png`）が残っていたので、今の作り方の例に直した（消す前の `git grep` で見つかった参照）
- 再生成: `python3 scripts/regenerate.py title_pages houou_pages houou_leagues houou_race saikyo_pages`、取り込み後に `all`（凍結中を除く、差分0）。テスト OK（1279件）

### 消した画像（cloudflare との差で39枚）と残した画像

- 消した: `img/ogp/title/` の黒地21枚（cloudflare にあるもの）、`img/ogp/houou/index-black.png`、`img/ogp/saikyo/index-black.png`、`img/ogp/saikyo/2011-black.png`〜`2026-black.png` の16枚。
  OGR-05 で作った大会ごとの白地の画像19枚（`img/ogp/title/<slug>-white.png`、本番に出ていない）も消した（cloudflare との差には出ない）
- 消す前の `git grep`（docs/logs・docs/decisions を除く）: 大会ごとの白地の画像は参照なし。年度の画像は再生成の後は `build_ogp_image.py` の使い方の例だけ（上で直した）。
  消した後に残る参照は docs/notes/ogp.md の `img/ogp/title/houou-black.png` の1件で、#232 の DUP-08 で 404 が CDN に載った**過去の事例の記述**なので残した
- 残した文字ベースの画像（4種類）: `img/ogp/site/index-white.png`（ryoei.pro、変更なし）・`img/ogp/title/index-white.png`（タイトル戦）・`img/ogp/houou/index-white.png`（鳳凰戦）・`img/ogp/saikyo/index-white.png`（麻雀最強戦）。旧 `img/ogp.png`・帰り道の画像は変更なし

### origin/cloudflare との差（docs/ を除く）

- og:image の行: **402ページ**（title/ 384・houou/ 13・旧 `houou_*.html` 4〈leagues・race・ranking・results〉・`saikyo/index.html` 1）。og:image:alt: 387ページ（title/ の大会・期 383〈入口は元から「タイトル戦」〉と旧 `houou_*.html` 4）
- 代わりの画像の指定（`data-fallback`）: **saikyo/ の卓のページ 643件**（10,915か所が `../../img/ogp/saikyo/index-white.png` に）。ほかに代わりの画像を指定しているページは無かった。
  643件それぞれで、`data-fallback` の値をならした前後の HTML が一致する（ほかの行は変わっていない）ことを確かめた
- 画像の追加3（移動した鳳凰戦を含む）・削除39、スクリプト6つとテスト1つ。シートの変化による差分は無し。books/・旧 `img/ogp.png`・`img/ogp/site/index-white.png`・帰り道の画像は変化なし

### 記録

- #581 に決定の8点と残りの課題（最強戦の卓の og:title が X の帯で切れる）をコメントした
- docs/decisions/seo-bing.md に OGR-06 の節を足し、OGR-05 の1点目に一部置き換えの印。title.md（出し分けは入口と大会ページ・大会ページの画像の名前の2行）、houou.md（2026-10-06 の「og:image はサイト共通の画像」に一部置き換え）、
  saikyo.md（2026-10-15 の 5 に一部置き換え: 年度の画像を消し、代わりの画像は「麻雀最強戦」）に印を付けた。houou.md の 2026-10-14 の黒地・見本 A の2行は OGR-05 で印済み
- docs/notes: ogp.md（実例の行・規則の表・4種類の原則・意匠を作り直した実例）、title-pages.md（og:image・作り方・新しい大会と改称のとき）、houou-top.md、saikyo-page-design.md（トップ・年度の画像・代わりの画像）、static-generation.md（OGP の段落と `build_ogp_image.py` の行）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 43e4695b）: https://github.com/retroeater/mj-logs/tree/main/guide/43e4695b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/18c52275.md
