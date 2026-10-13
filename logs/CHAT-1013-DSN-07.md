# CHAT-1013-DSN-07

- 着手日時: 2026-10-13（JST）
- 対象issue: #107
- ブランチ: work/1012-dsn
- 着手時HEAD: 2177d92

## 指示

【Claude作成】Claude Code 向け指示：houou/ への当てはめの直し（パンくず・質問欄・個人成績の期間と名前・グラフの端の名前・ランキングの行と部門の切り替え）— プレビューで止まる Chat-Ref: CHAT-1013-DSN-07 マージ: 判断待ちで止まる（平野さんが iPhone でプレビューを見て決める。マージは別の指示で出す） 貼る時機: CHAT-1013-DSN-06 が「判断待ち」で止まった後（2026-10-13 に止まっている） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-dsn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-dsn を続けて使う（DSN-06 の当てはめがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（houou/ の HTML・JSON）が衝突したときは、生成し直して解いてよい

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-DSN-06 のログの `## 報告` を読み、状態が「判断待ち」であることを確かめる（違えば何もせず止まる）。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN-06 のプレビューを平野さんが見た結果（2026-10-13、スクショ4枚）で、houou/ への当てはめを直す。マージはこの指示ではしない。
決定（2026-10-13、平野さん。DSN-06 の判断待ちへの回答）

* Q20 (a): ランキングに列名の行（順位・選手・部門の値の名前。13px 灰色）と、行の右端の ›（押すとその選手の個人成績へ。動きは今のまま）を付ける
* Q21 (a): 選手のリーグのグラフで、最後の期の選手のアイコンと重なる段のリーグ名は、アイコンの左に寄せて重ならないようにする
* Q22 (a): 「通算」のグラフに触れたときの情報も、グラフの下の決まった場所に出す（DSN-06 のまま）
* Q23 (a): パンくずリストを付ける。題名の上に「鳳凰戦 / 個人成績」のように 13px 灰色の1行（「鳳凰戦」は houou/ トップへのリンク〈紺〉）。区切りは「/」（title/・live/ と同じ）。サイトのトップは入れない（ナビの「R」で戻れる）。houou/ トップには付けない。個人成績・ランキング（部門ごとのページも）・順位変動に付ける
* Q24: 題名は左寄せのまま
* Q25 (a): トップの「AI の回答には間違いが含まれている場合があります」の ⓘ を文の左に置き、行のどこを押しても注意書き3行の全文が開くようにする
* Q26: ランキングの行を大きくする（行の高さ 56px・アイコン 40px。jpml_pros の行と同じ高さ）。PC（マウスで操作する端末）で部門の切り替えの帯の外にある部門へ移れない不具合を直す: (1) マウスで操作する端末（`@media (hover: hover) and (pointer: fine)`）だけ、帯の左右の端に「‹」「›」のボタン（44px 以上）を出して帯を送る、(2) 帯の端を薄く消して続きがあることを見せる（スクロールの位置に合わせて、端に来たら消す）、(3) 選んだ部門が帯の外にあるときは、表示のときに帯を送って見える位置に出す
* トップの質問の送信ボタン: 明るい配色で紺の丸の上の矢印が黒く見えにくい。矢印を白にする（暗い配色の組も確かめる）
* 個人成績のリーグのグラフ（選ぶ前の山を含む）: 左下・右下の段の名前（例「C3」「E3」）は、その段の棒の一番下の端に、字の下端をそろえる
* 個人成績の最近見た選手の行（例「白鳥翔」）にも、名前の後ろに「（東京・XX期）」を付ける（選手を選んだ後の名前の形と同じ）
* 個人成績: 名前の下に期間「第24期前期〜第43期前期」を出す。始まりはその選手が初めて出た期、終わりはデータのある最新の期（その選手が出場していなくても最新の期）。「通算」タブの期間の表示（例「第24期前期〜第41期」）と、「リーグ」タブの期間の表示（例「第24期前期〜第43期前期」）は消す

前提（チャット側。平野さんの決定ではない）

* 平野さんのスクショで見つけたこと（決定に入れた）: ランキングでスコアの右に大きな余白（› が無いため）、部門の帯の「期浮き率」が右で切れて PC では送れない、トップ（明）の送信ボタンの矢印、選ぶ前の山の左「C3」が棒の途中・右「E3」が棒の下端より上にある、最近見た選手の行に所属・期が無い
* パンくずの組み立ては Code が決める（`scripts/generate_houou_pages.py` の骨組みと、JS で中身が変わるページ）。構造化データ（BreadcrumbList）は新サイトで扱う方針（#13）なので今回は入れない。`<nav aria-label="パンくずリスト">` と `aria-current="page"` は付ける。パンくずの字の色は灰色の字の条件（面 7:1・ページの地 4.5:1）を満たすこと
* 個人成績の期間の「最新の期」は、生成のデータの最新の期（`houou/players/leagues.json` などの最後の期）から取る。選手ごとの値ではない
* ランキングの行を大きくした後も、名前とスコアの間が間延びしないこと（DSN-03 で詰めた形）を 375px・430px で確かめる
* DSN-06 と同じ確かめ（375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、`prefers-reduced-motion`、灰色の字の条件、ほかのページの見た目が変わっていないこと）を、変えた画面について行う。PC の部門の切り替えは 1280px の幅でマウスの操作（ボタン・ホイール・キーボードの Tab）で確かめる
* 文書（`docs/notes/houou-top.md`・`docs/notes/design.md`・`docs/decisions/design-renewal.md`）に、この指示の決定と値を足す。#107 に直したことと確認待ちをコメントする（プレビューの URL は書かない）

手順

1. DSN-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1013-DSN-07` を足す。#107 に着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` を取り直し、houou/ 系のファイルを変えている未マージのブランチと、変えている箇所をログに書く
2. 上の決定のとおりに直して生成し直し、push して版ごとのプレビューの URL で開けることを確かめる。上の確かめを行い、結果と代表の画面写真（`docs/logs/CHAT-1013-DSN-07/`。平野さんのスクショ4枚と同じ画面の直した後を含む）をログに書く
3. 文書を直し、#107 にコメントする

止まる条件

* 未マージの work/ ブランチが、この指示で変える houou/ 系のファイル（`style.css` の houou/ の層、`assets/houou.js`、`assets/houou_race.js`、`scripts/generate_houou_pages.py`、`navbar.js`）の同じ関数・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 直しで、今の houou/ の動き（順位変動の数え方・帯、リーグの再生、検索・最近見た選手・URL の状態、ランキングの行を押すと個人成績へ）が変わる、または生成のテストが落ちる（今回の変更で落ちると分かっているテストは直してよい）
* パンくずを付けると、ほかのページ（houou/ 以外）の見た目が変わる
* 灰色の字の条件を満たせない所が出る

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（直した点ごとの結果、マージで変わるファイルの種類と数、再生成で触れる生成物、画面写真の有無を含む）
* マージはしない（冒頭の「マージ:」のとおり判断待ちで止まる）。最終報告の「確認用:」の行に、houou/ の版ごとのプレビューの URL（トップ・個人成績〈`?name=平野良栄` のリーグのタブと、選ぶ前の画面〉・ランキング・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-DSN-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-DSN-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1012-dsn
- ログ: https://github.com/retroeater/mj/blob/work/1012-dsn/docs/logs/CHAT-1013-DSN-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-dsn
- 確認用URL: なし
- マージ: 未
- issue: #107
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7929efdc）: https://github.com/retroeater/mj-logs/tree/main/guide/7929efdc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7929efdc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
