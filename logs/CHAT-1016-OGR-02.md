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

ガイド文書（この版を写した時点の最新、mj f03198fd）: https://github.com/retroeater/mj-logs/tree/main/guide/f03198fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f03198fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
