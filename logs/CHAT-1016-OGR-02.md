# CHAT-1016-OGR-02

- 着手日時: 2026-10-16
- 対象issue: #581
- ブランチ: work/1016-ogr
- 着手時HEAD: f03198fdf

## 指示

【Claude作成】Claude Code 向け指示：live/ の卓・対局の og:type と、最強戦の卓の og:image をそろえる（#581 の先行分） Chat-Ref: CHAT-1016-OGR-02 マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
#581（OGP 画像の横断レビュー）のうち、CHAT-1016-OGR-01 の `## 経過`「手順2」の「気づいた点」の3点を、論点整理の前に先に片付ける。あわせて 2026-10-16 の平野さんの判断を #581 と docs/decisions に記録する。
決定（2026-10-16、平野さん）

* 映画「マインドアスリート」の宣材写真で OGP 画像を作る案は取り下げる（見てみたらイメージと違った）
* live/ の卓・対局のページ（今は og:type website）を、帰り道の各話と同じ形（og:type video.other ＋ og:video、#288）にする。この系列（OGR）で直す
* YouTube のサムネイルの大きさは、今の規則（その動画で使える最大のもの: maxresdefault、無ければ sddefault、それも無ければ hqdefault）のままにする。統一はしない
* 最強戦の卓のページは、ABEMA の回がある卓だけ、og:image をその回のサムネイル（ヒーローと同じ画像）にする。回の無い卓と、年度・対局のページは、今のまま作品「麻雀最強戦」の画像にする（docs/decisions/saikyo.md の 5〈2026-10-15、SKR〉のうち「年度・対局・卓のページの og:image はすべて作品の画像」を、回のある卓について置き換える）
* この直しは、下の「止まる条件」の差分の範囲に収まればマージしてよい

前提（チャット側。平野さんの決定ではない）

* 仕組みはすでにある（チャット側が origin/cloudflare の scripts を REST で読んだ範囲。要確認）: `scripts/lib/page.py` の `PageMeta.og_type`・`og_video`（空なら og:video 系の4行を出さない）／帰り道の各話は `scripts/generate_wayhome_episodes.py` で `og_type="video.other"`・`og_video=wayhome.embed_url(video_id)`／live/ は `scripts/generate_live_pages.py` の `meta_for()` と `HERO_THUMBS`／最強戦の卓は `scripts/generate_saikyo_pages.py` の `table_image()`（回があれば `abema_thumb_url()`、無ければ `series_image_url()`）をヒーローに使っている
* og:video の URL は帰り道と同じ形（YouTube の埋め込みの URL）にそろえる。共通の関数にまとめるかは実物を見て決めてよい（決めたことをログに書く）
* 最強戦の卓の og:type は今は変えない（ABEMA は埋め込みの URL を出していないため。og:type の平仄は #581 の論点 (c) で扱う）
* live/ では LVP 系列が公開の準備を進めている（work/1016-lvp。B9「トップ・タイトル戦・期の OGP 画像をヒーローと同じサムネイルにする」はまだ実装されていない、docs/decisions/live.md）。この指示では B9 に手を出さず、卓・対局のページだけを変える
* 最強戦では SKR 系列のブランチ（work/1016-skr-* など）が動いている

手順

1. 記録: #581 に、上の「決定」の5点（2026-10-16）をコメントで書く。docs/decisions/seo-bing.md の OGR-01 の節に続けて同じ内容を足し、docs/decisions/saikyo.md の 5 には「→ 一部置き換え: 2026-10-16（CHAT-1016-OGR-02、回のある卓は回のサムネイル）」を、docs/decisions/live.md には卓・対局の og:type を足す（追記先の今の内容を読み、矛盾していて判断が要るときだけ止まる）
2. live/: 卓・対局のページの meta を og:type video.other ＋ og:video（帰り道と同じ形）にして再生成する。あわせて、live/ と study/ の各ページの og:image:width/height が、実際に使っている画質（maxres 1280×720・sd 640×480・hq 480×360）と合っているかを全件確かめ、件数をログに書く（食い違いがあれば直さずに名前の一覧を書く）
3. 最強戦: 卓のページの og:image（と width/height・alt）を、回のある卓ではヒーローと同じ回のサムネイルにして再生成する。回のある卓の数（見込み）を、生成前に `table_image()` が `abema_thumb_url()` を返す卓の数として数え、ログに書く。マージ後、本番で回のある卓・回の無い卓・live/ の卓（未公開）を各1ページ、URL に `?v=<未使用の値>` を付けて取得し、og:type・og:image・og:video の行をログに引用する

止まる条件

* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。名指しのブランチに限らない）が、`generate_live_pages.py` の卓・対局の meta の部分、`generate_saikyo_pages.py` の卓の meta・`table_image()`、`page.py` の `PageMeta`・`_render_og_video()` の、同じ行・同じ関数を変えている、または取り込みで衝突する。live/ と最強戦の片方だけが重なるなら、重なる方だけ外して残りを進めてよい（外したものと理由を報告に書く）。両方が重なるときだけ止まる
* 差分（docs/ を除く生成物とスクリプト）が次の範囲を外れる:
   * live/ の卓・対局のページ: og:type の1行と og:video 系の4行だけが変わる。ページ数は CHAT-1016-OGR-01 の `## 経過`「手順2」の表の live/ の卓・対局の行の合計（672＋71＝743）と同じ（±0。食い違えば数の理由を確かめ、説明できなければ止まる）
   * saikyo/ の卓のページ: og:image・og:image:width・og:image:height・og:image:alt の行だけが変わる。ページ数は手順3で数えた見込みと同じ（±0）
   * スクリプトの変更と、docs/（decisions・ログ）
   * ほかに出た差分が、生成のたびに入るシートの変化（ほかのページの再生成）で説明できるなら、内容をログに書いて進めてよい。説明できなければ止まる
* og:image:width/height の確かめで食い違いがあっても、止まらずに一覧を書く（直すかは #581 で決める）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順（本番の確かめ）を進めてよい。自分の変更で落ちると分かっているテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（マージの後の自動の再生成〈regenerate-page.yml〉で変わる範囲も、上の差分の範囲で見る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `CHAT-1016-OGR-02` のコミット: 無し。識別子 `OGR` は同じセッションの続き（OGR-01）なので重複の確認の対象外
- 作業ブランチ: ローカルの `work/1016-ogr` は origin/cloudflare の祖先、リモートもマージ済み。cloud-sessions.md「作業ブランチの用意」の「ローカルにあり祖先」のとおり `git checkout work/1016-ogr`（既に上）のうえ `git merge --ff-only origin/cloudflare`（371e00365 → f03198fdf）
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有る
- 重なり: 未マージの `work/` 7本のうち、3ファイル（`generate_live_pages.py`・`generate_saikyo_pages.py`・`lib/page.py`）を変えているのは `work/1016-skr-rank` の `generate_saikyo_pages.py` だけ。
  変えているのは選手カードの帯（`BAND_CLASSES`・`Player.rank_label`→`result_text`・`build_player_html()`）で、卓の meta・`table_image()` とは別の関数・別の行。止まる条件に当たらない

### 前提の確かめ

- チャット側の前提どおり: `PageMeta.og_type`・`og_video`（空なら4行を出さない `_render_og_video()`）、帰り道・Mつく・女流勉強会の各話は `og_video=wayhome.embed_url(video_id)`、live/ の個別ページは `build_individual()` の `meta_for()` で `HERO_SIZES`/`thumb_url()`、最強戦の卓は `table_image()`
- og:video の URL: 共通の関数を新しく作らず、Mつく・女流勉強会と同じく `lib.wayhome.embed_url()` を `generate_live_pages.py` から使う（既に3か所が使う共通の関数のため）

### 変更前の再生成（修正前でも差分が出ないか）

- 変更前のコードで `python3 scripts/regenerate.py live_pages saikyo_pages` → `git status` は差分なし（シートの変化による差分は無い）
- 回のある卓の見込み: 卓のページ 643件のうち、ヒーローの画像（`table_image(match, table, 2)` の出力）が ABEMA の回のサムネイル（`/image/programs/`）の卓が **606**、作品の画像（`/image/series/`）が 37

### 実装

- `generate_live_pages.py` `build_individual()`: `meta_for()` の後に `og_type = "video.other"`・`og_video = embed_url(first.video_id)`（「▶ 再生」・og:image と同じ最初の動画）
- `generate_saikyo_pages.py`: `table_og(match, table, alt)` を足す。回があれば `table_image(…, 2)` の URL・幅・高さと alt「<対局名（日付）> <卓>（ABEMA）」、無ければ `series_og()`。卓のページだけ `**og` を `**table_og(...)` に替えた。年度・対局のページは `series_og()` のまま

### 変更後の再生成と差分

- 変わったファイル: live/ 743・saikyo/ 606・scripts 2（docs を除く）。見込みと ±0
- 変わった行（`git diff -U0` を全件数えた）:
  - live/: `-og:type` 743・`+og:type` 743・`+og:video`/`:type`/`:width`/`:height` 各743。1ファイルあたり +5/-1。ほかの行は無し
  - saikyo/: `-/+og:image` 各606・`-/+og:image:alt` 各606。1ファイルあたり +2/-2。width/height は変わる前後とも 1280×720 のため行は変わらない
- 例（live/gpmax/10/b16-a.html）: og:type website → video.other、og:video `https://www.youtube.com/embed/oTObdvrRxuw`
- 例（saikyo/2019/20190223-s1-a.html）: og:image `…/image/programs/68-38_s19_p1/thumb001.png?height=720&quality=75&width=1280`、alt「女流プレミアトーナメント 華麗な技（2019年2月23日） ベスト32 A卓（ABEMA）」。回の無い卓 saikyo/2019/20190901-s1-b.html は変化なし
- テスト: `python3 -m unittest discover -s scripts/tests` 1274件 OK

### live/・study/ の og:image:width/height の確かめ（全件）

| 場所 | 画質 | 件数 | width/height |
|---|---|---|---|
| live/ | maxresdefault（1280×720） | 672 | すべて一致 |
| live/ | sddefault（640×480） | 71 | すべて一致 |
| live/ | YouTube 以外（共通の img/ogp.png） | 165 | 対象外 |
| study/ | maxresdefault | 39 | すべて一致 |
| study/ | hqdefault（480×360） | 2 | すべて一致 |

食い違いは無し。

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

ガイド文書（この版を写した時点の最新、mj 9901aa99）: https://github.com/retroeater/mj-logs/tree/main/guide/9901aa99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
