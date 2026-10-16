# CHAT-1016-OGR-05

- 着手日時: 2026-10-16
- 対象issue: #581
- ブランチ: work/1016-ogr
- 着手時HEAD: 0bead3344

## 指示

【Claude作成】Claude Code 向け指示：title/・houou/・saikyo/ のトップの OGP 画像を白地・黒字（区分名＋ryoei.pro）にそろえ、使われていない画像を消す（#581、プレビューで止まる） Chat-Ref: CHAT-1016-OGR-05 マージ: 判断待ちで止まる（23枚の見本を平野さんがプレビューで見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
CHAT-1016-OGR-04 で共通の OGP 画像を白地・黒字にしたのに合わせ、区分ごとの画像（今は黒地）も白地・黒字にそろえる。使われていない OGP 画像を消す。プレビューで止める。
決定（2026-10-16、平野さん）

* title/（大会ごと20枚＋入口1枚）・houou/（1枚）・saikyo/ のトップ（1枚）の OGP 画像を、白地に黒い字で、区分名（「鳳凰戦」「タイトル戦」「麻雀最強戦」「WRC-Rリーグ」など今の画像の文字）と「ryoei.pro」を表示するだけの形にそろえる（OGR-04 の共通画像と同じ型）
* houou/ の画像の副題（「個人成績・ランキング・順位変動」）は消す。houou/ のリニューアルにともない、2026-10-14 の見本 A の決定（docs/decisions/houou.md）を置き換える
* 使われていない OGP 画像は消す
* X のライトモードで白い画像の縁が背景に溶けるおそれ（チャット側の懸念）は、ひとまず受け入れ、見てから考える
* （OGR-03 の (b-2)・OGR-04 で決めた「区分ごとの画像は黒地・白字のまま」をこの決定で置き換える）

前提（チャット側。平野さんの決定ではない）

* 色・字体は OGR-04 の共通画像と同じ（背景 #ffffff・文字 #212529、`build_ogp_image.py`・NotoSansJP-Bold）。最強戦のトップの今の橙色の字も黒にする（白地に黒で統一、のため）
* 配置の案: 区分名を大きく1段、その下に「ryoei.pro」を小さく1段（今の houou/ の画像から副題を抜いた形）。区分名の大きさは、いちばん長い名前（「女流日本シリーズ」など）が収まる大きさを全部で共通にするか、名前ごとに幅に合わせるかを実物で見て決めてよい（決めたことと理由をログに書く）。`build_ogp_image.py` が2段に対応していなければ、対応させてよい
* 名前の案: 今の名前の `-black`（`jwrc-black2` などの `-black2` を含む）を `-white` にする（`img/ogp/title/<slug>-white.png`・`img/ogp/houou/index-white.png`・`img/ogp/saikyo/index-white.png`）。意匠の接尾辞が定数（title/ の生成・`generate_saikyo_pages.py` の `OGP_DESIGN` など）なら、その定数を変える。title/houou と houou/ が同じ文字の画像になるなら、1枚を共用してよい
* 使われていない画像の候補: saikyo/ の年度ごとの16枚（`img/ogp/saikyo/<年度>-black.png`。OGR-02 以降どのページも使わない）と、今回置き換える黒地の23枚。旧 `img/ogp.png` は消さない（books/ と告知動画の `scripts/promo_video/*/build.sh` が使う）
* houou/ は公開済み（2026-10-16 時点、平野さん）。houou/・saikyo/ では HOU・SKR 系列のブランチが動いている

手順

1. 白地・黒字の画像を作り（title/ 21枚・houou/ 1枚・saikyo/ のトップ 1枚）、生成スクリプトの参照を替えて、該当するページを再生成する。テストを通す
2. 使われていない OGP 画像を消す。消す前に、各ファイルの名前がリポジトリのどこにも参照されていないこと（`git grep`。docs/logs と docs/decisions の記録は除く）を確かめ、参照が残るものは消さずに一覧を書く
3. 記録: #581 に上の「決定」の5点（2026-10-16）をコメントで書く。docs/decisions/seo-bing.md の OGR の節に足し、docs/decisions/title.md・houou.md・saikyo.md の置き換わる決定に「→ 置き換え: 2026-10-16（CHAT-1016-OGR-05）」を付け、docs/notes/ogp.md の規則の節と実例の行（`img/ogp/title/<slug>-black.png` など）を新しい名前に直す。push してプレビューで止まる。ログに、作った画像の一覧（名前・文字）と確かめ用のページ（title/ の入口・大会1つ・houou/ の入口・saikyo/ のトップ）を書く

止まる条件

* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）が、title/・houou/・saikyo/ の生成スクリプトの OGP の部分（画像の名前・意匠の定数・`og_image_for()` など）や `build_ogp_image.py` の、同じ行・同じ関数を変えている、または取り込みで衝突する。重なる区分だけ外して残りを進めてよい（外したものと理由を報告に書く）。全部が重なるときだけ止まる
* 差分（docs/ を除く）が次の範囲を外れる:
   * title/（OGR-01 の表で 384件）・houou/（13件）・saikyo/index.html（1件）の og:image の行（と、変えたなら og:image:alt）だけが変わる。ページ数がこの数と違えば理由を書く（説明できなければ止まる）
   * 画像の追加（最大23枚）と削除、`build_ogp_image.py` と生成スクリプトの変更
   * ほかに出た差分が、生成のたびに入るシートの変化で説明できるなら、内容をログに書いて進めてよい。説明できなければ止まる
* 旧 `img/ogp.png`・`img/ogp/site/index-white.png`・帰り道の画像を消す・変える差分が出た

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（作った画像・消した画像の一覧を含める）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。マージの指示は平野さんの承認の後に別に出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-05 を書く

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

ガイド文書（この版を写した時点の最新、mj 0bead334）: https://github.com/retroeater/mj-logs/tree/main/guide/0bead334

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0bead334/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6a497a74.md
