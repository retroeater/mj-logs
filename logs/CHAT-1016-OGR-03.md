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

ガイド文書（この版を写した時点の最新、mj 64ee77ff）: https://github.com/retroeater/mj-logs/tree/main/guide/64ee77ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
