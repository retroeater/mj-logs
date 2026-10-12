# CHAT-1013-ABM-17

- 着手日時: 2026-10-13
- 対象issue: #425
- ブランチ: work/1013-abm-cast
- 着手時HEAD: e676f8c2b058d0afe9ac7caf502739b52bc1d27a

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の卓のページに司会・実況・解説を出す（ABEMA ブックの【2】＋【3】から）。試作してプレビューに出す Chat-Ref: CHAT-1013-ABM-17 マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-abm-cast を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-abm-cast origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめる（ABM の系列のものは同じ系列）。

目的
ABEMA ブックの【2】最強戦（毎朝の作り直し）と【3】手動補正にある卓ごとの司会・実況・解説を、未公開の卓のページ（`saikyo/<年度>/<卓のアンカー>.html`）に出す。
決定（2026-10-13、平野さん）

* 出し方は /live の個別ページの「実況・解説」と同じ写真カード（docs/notes/live-page-design.md の「実況・解説はヒーローの…行をやめ、対局者の下の節『実況・解説』に同じ写真カードで出す。役割は写真の上端の帯で示す」）。最強戦では節を「司会・実況・解説」とし、並びは 司会 → 実況 → 解説
* 「プロ」「連盟プロ以外」「別名」に登録の無い人（局アナ・芸能人など）も、名前と仮の画像（灰色の人の形など、今の写真の無い人と同じ扱い）で出す。未登録の名前の一覧はログに書く（登録するかは平野さんが後で決める）
* 出すのは卓のページだけ。対局のページ・年度ページ・トップの検索（`?q=`）・各選手の他の対局は今のまま
* 【3】の司会・実況・解説の空でないセルは【2】を上書きする（URL と同じ。docs/notes/abema-book.md）

前提（チャット側。平野さんの決定ではない）

* 【2】の司会・実況・解説は、1つの役に複数の人がいれば「／」でつながっている（docs/notes/abema-book.md「司会・実況・解説の読み分け」）。CHAT-1012-ABM-13 の時点で、回の当たった卓 603 のうち 司会 584・実況 550・解説 553 卓に値がある
* 「司会・実況」と書かれた人は【2】の司会と実況の両方に入っている。同じ人が司会と実況の両方にいるときは、カードを2枚にせず1枚にし、帯を「司会・実況」とする案（実物を見て、より自然な形があれば理由を書いて選んでよい）
* 名前の解決（現在名・写真）は出場者の写真カードと同じ仕組みを使う。ABEMA の書き方（敬称・所属の括弧・空白など）が登録と違って当たらない名前は、読み方の規則（括弧を外すなど）で当てるか、当たらないまま未登録として出す。足した規則はログに書く
* 3つとも空の卓（回の当たらない卓など）は節を出さない。役の一部だけ空なら、ある役だけ出す
* 写真カードは押してもどこへも行かない（今の出場者のカードと同じ扱いにそろえる。出場者のカードがリンクなら、同じリンクにする）
* 生成物の変更は卓のページだけの見込み（値のある卓、見込み 600 前後）。対局のページ・年度ページ・今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・live/ は変わらない
* `scripts/generate_saikyo_pages.py` の `Table.video` のコメントに「動画のURL(M列)」が残っている（CHAT-1013-ABM-16）。同じファイルを直すので「ABEMA ブックの【2】＋【3】」に直す

手順

1. 確かめる: 【2】【3】を生成と同じ経路で読み、司会・実況・解説の値のある卓の数（役ごと）と、名前の種類の数を書く。名前を出場者と同じ仕組みで解決し、当たった名前・当たらない名前の数と、当たらない名前の一覧（出る卓の数つき）を書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが `generate_saikyo_pages.py`・`scripts/lib/abema_book.py`・`style.css` の同じ関数・同じ行を変えていないか確かめる
2. 作る: 上の決定と前提のとおり、卓のページに節「司会・実況・解説」を足す（ヒーロー → 出場者 → 司会・実況・解説 → 関連動画 → 各選手の他の対局）。読み込みは `abema_book` に足し、【3】の上書きを含める。テストを足し（節の並び・帯・同じ人の1枚化・未登録の人の仮の画像・空の役を出さない・3つとも空なら節が無い・【3】の上書き）、`python3 -m unittest discover -s scripts/tests` を通す。docs/notes/saikyo-page-design.md の卓のページの項と docs/notes/abema-book.md の「司会・実況・解説はまだ表示しない」を直す（今の内容を読んでから）。決定を docs/decisions/saikyo.md に足す
3. 確かめてプレビューに出す: 生成して、(a) 変わったのが卓のページと CSS だけで、その数（節のある卓の数と一致すること）、(b) 内部リンクがすべて実在すること、(c) id の重複 0・noindex と canonical が全ページ、(d) headless Chromium でスマホ幅（390）と PC 幅（1280）の見え方（帯が切れない・はみ出しが無い）をログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2026 超頭脳合戦 ベスト8 A卓（`saikyo/2026/20260920-s1-a.html`）、未登録の人が出る卓の例1つ、同じ人が司会と実況を兼ねる卓の例1つ（あれば）を並べる

止まる条件

* 決定（docs/decisions/saikyo.md）と食い違う作りが要る（作らずに報告する）
* 卓のページ以外の生成物に差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが上の3ファイルの同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ABM-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ABM-17 を書く

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

ガイド文書（この版を写した時点の最新、mj 62fb5bbb）: https://github.com/retroeater/mj-logs/tree/main/guide/62fb5bbb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/62fb5bbb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3ce5d549.md
