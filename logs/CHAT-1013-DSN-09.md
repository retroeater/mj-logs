# CHAT-1013-DSN-09

- 着手日時: 2026-10-13（JST）
- 対象issue: #107
- ブランチ: work/1013-dsn
- 着手時HEAD: 5cfee13

## 指示

【Claude作成】Claude Code 向け指示：houou/ のマージ後の直し（トップの注意書き・人数、個人成績の最初の表示とタブ、ランキングの見出し、順位変動の最初の表示）と、部門の切り替え・順位変動の選ぶ部品の比較ページ — プレビューで止まる Chat-Ref: CHAT-1013-DSN-09 マージ: 判断待ちで止まる（平野さんがプレビューと比較ページを見て決める。マージは別の指示で出す） 貼る時機: いつでも（CHAT-1013-DSN-08 は完了・マージ済み〈86979e3d8〉。0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-dsn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-dsn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-dsn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-DSN-08 のログの `## 報告` を読み、状態が「完了」でマージが「済」であることを確かめる（でなければ何もせず止まる）。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN-08 でマージした houou/ のデザインについて、平野さんが本番を見て出した直しを入れる。決まっている直しは本体に入れ、見た目を見比べて決める2つ（ランキングの部門の切り替え、順位変動の選ぶ部品）は比較ページで3案ずつ見せる。マージはこの指示ではしない。
決定（2026-10-13、平野さん）
本体に入れる直し:

1. トップ: 注意書きの重複をなくす。1行目「AI の回答には間違いが含まれている場合があります」は今のまま常に見せ、ⓘ を押して開く中身からこの一文を消して、残りの2行（個人情報・利用目的）だけにする（案1）
2. トップの説明文の「選手1,295名」は、在籍の選手の数（個人成績・ランキングに出る範囲。今は 691名前後）にする。数は生成のたびにデータから数える
3. ランキング: 列名の行の「選手」を、写真の左ではなく名前の左端にそろえる。行の右端の › の列には見出しを付けない
4. 順位変動: 対象のリーグを選んだとき（最初の表示を含む）は、そのリーグの最終成績と帯（ボーダー）の状態を出す（全員 0.0 の暫定順位から始めない）。再生ボタンで第1節から動かす
5. 個人成績: トップから移った時点（選手を選ぶ前）はグラフを出さない。選手を選んだら「期別」のタブを出す。「通算」と「リーグ」のグラフの領域の大きさを同じにする。名前の右のリーグのチップ（例「C2」）の字を中央にそろえる 比較ページで見比べて決める（本体は変えない）:
6. ランキングの部門の切り替え: (a) 3段×3列の格子で9部門を全部並べる、(b) プルダウン「通算得点 ▾」、(c) 「◀ 通算得点 ▶」の送り
7. 順位変動の選ぶ部品: (a) 題名そのものを「第43期前期 B1 ▾」にする（ページ名はパンくずの「鳳凰戦 / 順位変動」に任せる）、(b) 面の1行「期・リーグ　第43期前期・B1 ›」（iPhone の設定の行の形）、(c) 灰色の溝の中の大きなボタン（17px・太字）

前提（チャット側。平野さんの決定ではない）

* 比較ページは `houou/` の下に置き、noindex・どこからもリンクしない・sitemap と `llms.txt` に載せない（`docs/notes/houou-top.md`「比較ページ」の扱い）。ページの中のラジオで (a)〜(c) を切り替える。6 と 7 は同じページでも別のページでもよい。`_redirects` に吸われない置き場所にする（DSN-02 の見本と同じ確かめ）。見比べたあと、採らない案と比較ページは次の指示で消す
* 比較ページの各案は、本番と同じ CSS・字の大きさ・明暗・375px の幅で、実際に押して動く形にする（格子・プルダウン・送り・シート）。各案で、375px の最初の表示に表の何行目までが入るかをログに書く
* 2 の在籍の数の数え方は、個人成績の説明文（今「出場選手691名」）と同じ数え方にそろえる。トップと個人成績の数が食い違わないこと
* 4 は今の順位変動の動き（帯の入り方・数え方・節の時間）を変えない。最初の表示を最後の節の終わりの状態にし、再生で第1節から始める形にする。節のボタンで途中の節を選べる今の動きは残す
* 5 の「選手を選んだら期別」は、URL でタブを指定して開いたとき（`?name=…&view=league` など、HOU-30 の決定）はそのタブを優先する
* 確かめは DSN-07 と同じ（375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、`prefers-reduced-motion`、灰色の字の条件、ほかのページの見た目が変わらないこと）を、変えた画面と比較ページについて行う。生成のテストを流す
* 文書（`docs/notes/houou-top.md`・`docs/notes/houou-race.md`・`docs/decisions/design-renewal.md`）に、この指示の決定を足す。#107 に着手中・確認待ちのコメントを書く（プレビューの URL は書かない）

手順

1. #107 に着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` を取り、houou/ 系のファイル（`style.css` の houou/ の層、`assets/houou.js`、`assets/houou_race.js`、`assets/houou_ask.css`、`scripts/generate_houou_pages.py`）を変えている未マージのブランチと、変えている箇所をログに書く
2. 決定の 1〜5 を本体に入れて生成し直し、決定の 6・7 の比較ページを作る。push して版ごとのプレビューの URL で開けることを確かめ、上の確かめの結果と代表の画面写真（`docs/logs/CHAT-1013-DSN-09/`。比較ページは各案の 375px の明・暗）をログに書く
3. 文書を直し、#107 にコメントする

止まる条件

* 未マージの work/ ブランチが、この指示で変える houou/ 系のファイルの同じ関数・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 直しで、今の houou/ の動き（順位変動の数え方・帯・節のボタン、リーグの再生、検索・最近見た選手・URL の状態、ランキングの行を押すと個人成績へ）が変わる、または生成のテストが落ちる（今回の変更で落ちると分かっているテストは直してよい）
* 2 の在籍の数がトップと個人成績で食い違う
* 灰色の字の条件を満たせない所が出る

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（直した点ごとの結果、比較ページの置き場所と各案の最初の表示に入る行数、マージで変わるファイルの種類と数、画面写真の有無を含む）
* マージはしない（冒頭の「マージ:」のとおり判断待ちで止まる）。最終報告の「確認用:」の行に、版ごとのプレビューの URL（トップ・個人成績〈選ぶ前と `?name=平野良栄`〉・ランキング・順位変動・比較ページ）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-DSN-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-DSN-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1013-dsn
- ログ: https://github.com/retroeater/mj/blob/work/1013-dsn/docs/logs/CHAT-1013-DSN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-dsn
- 確認用URL: なし
- マージ: 未
- issue: #107
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5cfee13c）: https://github.com/retroeater/mj-logs/tree/main/guide/5cfee13c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5cfee13c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
