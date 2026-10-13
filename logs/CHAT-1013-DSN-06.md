# CHAT-1013-DSN-06

- 着手日時: 2026-10-13（JST）
- 対象issue: #107（ほか #518）
- ブランチ: work/1012-dsn
- 着手時HEAD: 83ca4b3

## 指示

【Claude作成】Claude Code 向け指示：新サイトのデザインの基本の形を houou/ の本体に当てはめる（見本のとおり。共通ナビは houou/ だけ）— プレビューで止まる Chat-Ref: CHAT-1013-DSN-06 マージ: 判断待ちで止まる（平野さんが iPhone でプレビューを見て決める。マージは別の指示で出す） 貼る時機: CHAT-1013-DSN-05 が「判断待ち」で止まった後（2026-10-13 に止まっている） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-dsn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-dsn を続けて使う（DSN-01〜05 の見本と決定の記録があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（houou/ の HTML・JSON）が衝突したときは、生成し直して解いてよい

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-DSN-05 のログの `## 報告` を読み、状態が「判断待ち」であることを確かめる（違えば何もせず止まる）。このセッションが新しく始めたものかをログの経過に書く（新しいセッションを推奨しているが、続きでもよい）。

目的
DSN-01〜05 で決めた新サイトのデザインの基本の形を、見本（`houou/design-sample/`）のとおりに houou/ の本体（トップ・個人成績〈期別・通算・リーグ〉・ランキング・順位変動）へ当てはめ、work ブランチのプレビューで平野さんが確かめられるようにする。共通ナビの新しい形は houou/ のページだけに当てはめ、ほかのページは今のナビのままにする。マージはこの指示ではしない。
決定（2026-10-13、平野さん。DSN-05 の判断待ちへの回答）

* 見本の食い違い8点のうち、2・3・4・7・8（名前のカードを左寄せの1行、見出しの枠なし、期別の行を1枚の面、再生ボタンと選んだタブを紺、最近見た選手の見出しを小さく）は見本（基本の形）のとおり
* Q16 (a): リーグのグラフの選手の線は紺の1色（昇級・降級の区間の赤・青はやめる。点線〈出場していない期〉は残す）
* Q17 (a): グラフに触れたときの情報（3行）はグラフの下の決まった場所に出す（グラフの上に重ねない。形と文言は HOU-33 のまま）
* Q18 (a): 選手を選んだ後のリーグのグラフのリーグ名は、右端の書ける段すべて（四隅だけにするのは選ぶ前の山だけ）
* Q19 (a): 共通ナビの新しい形（Q7 白地＋下の細い線・今いる項目は紺、Q8 日本語1行）は、まず houou/ のページだけに当てはめる。ほかのページの `navbar` の見た目は変えない
* リーグの色は今の houou/ の色（DSN-05 の決定のまま）

前提（チャット側。平野さんの決定ではない）

* 当てはめで変えるのは、DSN-05 の報告の「当てはめの見込みの一覧」のファイル（`style.css` の houou/ と順位変動の節、`assets/houou.js`、`assets/houou_race.js`、`scripts/generate_houou_pages.py` と生成し直した `houou/` 一式、`docs/notes/houou-top.md`・`docs/notes/houou-race.md`、`docs/notes/design.md`）。実物に合わせて増減してよく、増減を報告に書く
* 共通ナビを houou/ だけ変える作り方は Code が決める（例: houou/ のページの `<body>` に印を付けて、`style.css` で houou/ のときだけナビを白地にする。`navbar.js` の中身を変えるならほかのページの見た目が変わらないことを確かめる）。項目名の日本語1行は、houou/ のページで表示するときだけにする。ほかのページの見た目が変わっていないことを、代表のページ（トップ・jpml_pros・title/・saikyo/・live/・帰り道）の 375px と 1280px の画面写真の前後比較で確かめる
* 動き（`houou_race.js` の帯の入り方・数え方・節の時間、リーグのタブの再生の流れ）は今の houou/ の決まりを変えない。見本で書き直した動きのコードは、今の本体の動きと同じなら写さなくてよい（見た目の値だけを当てはめる）。見本と本体の動きが違う所があれば、本体を正とし、違いを報告に書く
* `docs/decisions/houou.md` に「→ 置き換え: 2026-10-13（CHAT-1013-DSN-06）」の印を付けるのは、DSN-05 の報告の一覧のとおり（HOU-06 の P1、HOU-10 の T1、HOU-08 以前の比較ページの「浮き上がり」系のうち置き換え済みでないもの、HOU-05「アイコンと名前も1枚のカード」の見た目、HOU-33 の吹き出しの置き場所、選手の線の色の記述〈`docs/notes/houou-top.md`〉）。HOU-06 の C2・HOU-18・HOU-23 は付けない
* `docs/notes/design.md`: 当てはめで `style.css` に入った値（色・面・字の大きさ・選ぶ部品・明暗の組）を各表へ移す。出典は `style.css` の節（新しい値は「houou/ のみ」と適用範囲を書く）。「1. トーンと方針」の DSN-02 で足した1行は、当てはめ済みに合わせて直す
* 見本 `houou/design-sample/` は、当てはめが終わったら消す（マージに入れない。noindex の見本を本番に残さない）。見本を消すのはこの指示の中でよい。消す前に、本体と見本を同じ幅で並べた画面写真をログに残す
* 確かめは DSN-02〜05 と同じ（375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、`prefers-reduced-motion`、灰色の字の条件）を houou/ の全画面で行う。生成のテスト（`python3 -m unittest discover -s scripts/tests`）を流す
* 使う skill: `frontend-design`、`design:accessibility-review`（このセッションに無ければ DSN-02 と同じく手で確かめる）
* #107 に着手中・プレビュー確認待ちのコメントを書く（プレビューの URL は書かない）。houou/ の親 issue #518 にも、デザインを当てはめたことを1行コメントする
* 次の指示（マージ）では、マージ後の自動再生成（regenerate-page.yml）で変わる範囲を見込む必要がある。報告に「マージで変わるファイルの種類と数」と「再生成で触れる生成物」を書く

手順

1. DSN-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1013-DSN-06` を足す。#107・#518 に着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` を取り直し、houou/ 系のファイル（上の一覧）を変えている未マージのブランチ（HOU 系など）の一覧と、変えている箇所をログに書く
2. 当てはめて生成し直し、push して版ごとのプレビューの URL で開けることを確かめる。上の確かめ（houou/ の全画面と、ほかのページの前後比較）を行い、結果と代表の画面写真（`docs/logs/CHAT-1013-DSN-06/`）をログに書く。見本のフォルダを消す
3. 決定の記録・文書（houou.md の印、design.md、houou-top.md・houou-race.md、design-renewal.md）を直し、#107・#518 にコメントする

止まる条件

* 未マージの work/ ブランチが、この指示で変える houou/ 系のファイル（`style.css` の houou/ と順位変動の節、`assets/houou.js`、`assets/houou_race.js`、`scripts/generate_houou_pages.py`）の同じ関数・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 共通ナビの見た目を houou/ だけにする方法が無く、ほかのページの見た目が変わってしまう
* 当てはめで、今の houou/ の動き（順位変動の数え方・帯、リーグの再生、検索・最近見た選手・URL の状態）が変わってしまう、または生成のテストが落ちる（今回の変更で落ちると分かっているテストは直してよい）
* 灰色の字の条件（面の白地で 7:1・ページの地で 4.5:1）を満たせない所が出る
* `git diff --stat` で、houou/ の生成物以外のページの生成物が変わっている

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（変えたファイルの一覧、houou.md に付けた印の一覧、ほかのページの前後比較の結果、マージで変わるファイルの種類と数、再生成で触れる生成物、画面写真の有無を含む）
* マージはしない（冒頭の「マージ:」のとおり判断待ちで止まる）。最終報告の「確認用:」の行に、houou/ の版ごとのプレビューの URL（トップ・個人成績〈`?name=平野良栄` のリーグのタブ〉・ランキング・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-DSN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-DSN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 「指示」欄の末尾は指示文の最後の行と一致。DSN-05 のログの `## 報告` の状態は「判断待ち」（確認済み）
- **このセッションは新しく始めたものではない。** DSN-01〜05 を実行したセッション（同じ Claude-Session）に続けて貼られた（作業ツリー・scratchpad は残っていた）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある（貼られた文は改行と見出しの記号が落ちて1段落になっていた）
- Chat-Ref の確認: `CHAT-1013-DSN-06` のコミットなし。識別子の重複確認はこのセッションの最初の指示ではないので対象外
- 作業ブランチ: `work/1012-dsn` はローカル・リモートとも 83ca4b3（一致）。`origin/cloudflare` は祖先でない → ログを push した後に merge で取り込む
- 取り込み: `git merge --no-edit origin/cloudflare`。衝突なし

### 手順1

- DSN-05 のログの `## 報告` の状態を「判断待ち / 続き: CHAT-1013-DSN-06」にした
- #107 に着手中のコメント（issuecomment-6135339487）、#518 に着手中のコメント（issuecomment-6135340998）。#518 に他セッションの着手中は無い（HOU-30 の着手中は HOU-32 で解除済み）
- 未マージの work/ ブランチ: work/1009-swp-526・work/1011-swp-hnd・work/1011-swp-nav・work/1012-dsn・work/1013-snm。houou/ 系のファイル（`style.css`・`assets/houou.js`・`assets/houou_race.js`・`scripts/generate_houou_pages.py`・`navbar.js`・`houou/`・`docs/notes/houou-top.md`・`docs/notes/houou-race.md`・`docs/notes/design.md`・`docs/decisions/houou.md`・`docs/decisions/design-renewal.md`）を変えているのは work/1013-snm だけで、`docs/decisions/houou.md` の末尾に節を2つ足している（CHAT-1013-SNM-11・12）。この指示で印を付ける行とは別の箇所で、`git merge-tree` で衝突なし（止まらない）

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1012-dsn
- ログ: https://github.com/retroeater/mj/blob/work/1012-dsn/docs/logs/CHAT-1013-DSN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-dsn
- 確認用URL: なし
- マージ: 未
- issue: #107、#518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8e07d685）: https://github.com/retroeater/mj-logs/tree/main/guide/8e07d685

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e07d685/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2c5fedab.md
