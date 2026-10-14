# CHAT-1014-SKD-15

- 着手日時: 2026-10-15
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 3cfeeb02e

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいページの直し3点（年度ページの「通過」の見出しを外す・対局のページの順位の金を外す・卓のページのパンくずを左上に）と比較ページの片付け — プレビューで止まる（#566） Chat-Ref: CHAT-1014-SKD-15 マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す） 貼る時機: いつでも（CHAT-1014-SKD-14 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-14 の比較ページを消すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SKD-14 の比較ページを平野さんが見た結果のうち、未公開の新しいページ（`saikyo/<年度>/`、#425、noindex・未リンク）だけに関わる直し3点を入れ、比較ページを消す。トップの作り直しはこの指示に入れない（次の指示で、#549 の公開まで本番に入れない形で行う）。
決定（2026-10-15、平野さん。SKD-14 の判断待ちへの回答を含む）

1. トップは B（歴代最強位を面の行にする）。A は採らない（トップの作り直しは次の指示）
2. 最強位が決まっていない年度（今の 2026）の行の名前の欄は空欄（チャット側の案のとおり写真の丸も出さず、年度と › だけの行）（次の指示）
3. トップの検索の結果を押したときの行き先は、新しい卓のページ（次の指示）
4. A の並び（2列・広い画面は4列）は今のままでよい（A は採らないので実装には関わらない）
5. 新しいトップの公開（本番への反映）は #549 の公開と同じ時にする。それまで公開済みのページ（今のトップ・今の年度ページなど）には影響を出さない
6. 年度ページで、対局名の行の日付の下の「通過」の見出しは要らない（すべての対局で外す。2026-10-14〈SKD-08〉の「『A卓 1位』の行がある対局だけに出す」を置き換える）
7. 対局のページで、「通過」のピルがある行の順位の数字を金の丸でハイライトしない（順位はすべて同じ灰色の数字にする。「優勝」の行も同じ）
8. 卓のページのパンくずは、ほかの段（年度・対局のページ）と同じく左上に置く（今はヒーローの中）
9. 選手名の検索の結果の画面も、年度ページと同じデザインにそろえる（次の指示）

前提（チャット側。平野さんの決定ではない）

* 決定8: 卓のページのパンくずをヒーローの上（共通ナビのすぐ下、本文の左右 16px）に出し、13px の1行にする。卓のページは濃色の例外のままなので、色は濃色のページの字の色（灰色の字は `--mj-v-fg-muted` などの今の濃色トークン、リンクは今の `.mj-live-breadcrumb` のリンクの色）にする。ヒーローの中のパンくずは消す。live/ の個別ページは同じ部品（`.mj-live-breadcrumb`）を使っているので変えない（卓のページだけに閉じる。SKD-01 `## 経過` 2-3）。ヒーローの高さ・題名の位置が崩れないこと
* 決定7: 対局のページの順位の金の丸（`build_rank_html()` などの見込み）をやめ、全行を灰色の数字にする。卓のページの写真カードの順位の三角と金（live/ と共有の部品）は変えない
* 決定6: SKD-08 の `.mj-saikyo-n-note` を出す条件を外し、見出しを出さない。右端の「A卓 1位」「通過」「優勝」のピルは今のまま
* 比較ページ `saikyo_top_compare.html` と `scripts/build_saikyo_top_compare.py` を消す（採用した B は次の指示で本体に作る。消す前に、B の作り〈検索の結果の行・空欄の行〉を次の指示で使えるよう、どの関数・CSS だったかをログに書く）。SKD-14 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-SKD-15` を足す
* 変えるのは新しい年度・対局・卓のページだけ。今のトップ・今の年度ページ・live/ は変えない。生成物の差は新しい年度ページ（見込み 16）・対局のページ（見込み 219）・卓のページ（件数は生成し直して数え、`saikyo-page-design.md` の 878 ページから年度・対局を引いた数と突き合わせる）だけ。ほかのページには X の画像の URL の変化とシートの変化以外の差が出ないこと
* 確かめ（DSN-07 以降の定型）: 2025 年度の年度ページ・`saikyo/2025/20250816.html`・`saikyo/2025/20250816-s1-a.html`（卓のページ）で、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。卓のページはパンくずが共通ナビの下の左上にあり、ヒーローと重ならないこと。live/ の個別ページ1枚の前後比較で差が無いこと
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 卓のページの左上、ヒーローの上に「最強戦 / 麻雀最強戦2025 / 政権抗争勃発 / ベスト8 A卓」。URL は書かない）
* 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/saikyo-page-design.md` の新しいページの記述を直す

手順

1. #566 に着手中のコメントを書く。未マージの work/ ブランチが、この指示で変える関数・CSS と同じ行を変えていないかを確かめる
2. 3点を直し、比較ページを消し、テストを足して `python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直して差を分類する。上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、卓のページ `saikyo/2025/20250816-s1-a.html` のプレビューの URL を書く

止まる条件

* 生成物の差が上の見込みを外れる（今のトップ・今の年度ページ・live/ に差が出る）
* 卓のページのパンくずを live/ の個別ページを変えずに動かせない
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 3点の直し（変えた関数・CSS）、比較ページを消したことと B の作りの控え、生成物の差の種類と件数、確かめの結果、確認用URL の印、決定と文書の記録先、SKD-14 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cca707c1）: https://github.com/retroeater/mj-logs/tree/main/guide/cca707c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b8500442.md
