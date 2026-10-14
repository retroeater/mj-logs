# CHAT-1015-HOU-40

- 着手日時: 2026-10-15
- 対象issue: #518
- ブランチ: work/1015-hou
- 着手時HEAD: 3cf17d8b3

## 指示

【Claude作成】Claude Code 向け指示：houou/ 個人成績 — タブを名前の面のすぐ下へ・要約は「期別」だけ・再生ボタンの位置の比較ページ（(a)・(J)・(D)） Chat-Ref: CHAT-1015-HOU-40 マージ: 判断待ちで止まる（再生ボタンの位置を平野さんが比較ページで選ぶため） 貼る時機: いつでも（CHAT-1014-HOU-39 はマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
未公開の個人成績 `houou/players/`（#518）で、「通算」「リーグ」のグラフをなるべく上に出す。タブを名前の面のすぐ下へ移し、要約3枚を「期別」のときだけにする。再生ボタンの置き場で空き行を無くせるかを、比較ページで平野さんが選ぶ。
決定（2026-10-14〜15、平野さん）

* 「期別」「通算」「リーグ」のタブを、名前の面のすぐ下に移す
* 要約（出場回数・通算成績・最高到達）は「期別」のときだけ出す（「通算」「リーグ」では出さない）。目的は「通算」「リーグ」のグラフの領域をなるべく広く、上に持ってくること
* 「通算」と「リーグ」は、タブを切り替えてもグラフの位置がそろう形を保つ（2026-10-13 の DSN-09 の「タブを替えてもグラフの位置と大きさが変わらない」のまま。「通算」だけ空き行を消して上げる案は採らない）
* 再生ボタンの位置は、次の3案の比較ページを作り、iPhone で見て選ぶ:
   * (a) 今の形: グラフの上の 52px の行の右端（「通算」にも同じ高さの空き行）
   * (J) 題名「鳳凰戦 個人成績」の行の右端。リーグのタブのときだけ出す
   * (D) 名前の面の右端。リーグのタブのときだけ出す
   * (J)・(D) では、グラフの上の 52px の行を「リーグ」「通算」とも無くす
* 中央のボタン（2026-10-10 にマウスオーバーと干渉して押せなかったためやめた）と、データの上に重ねる形（DSN の grill Q10-1）には戻さない。画面の右下に浮かせる案・グラフの下の情報欄に置く案は比較に入れない
* グラフ（SVG 380×540）の大きさは変えない

前提（チャット側。平野さんの決定ではない）

* 比較ページの作り方は docs/notes/design.md「7. チャット側の決め方」（ページ内のラジオボタンで切り替え、noindex・どこからもリンクしない・サイトマップに無い。採用後は使わない案と比較ページを消す）。比較ページで選手を選べる（または代表の選手〈例: 白鳥翔・坪原光〉で開ける）と見比べやすい
* タブの移動と要約の出し分けは、3案とも共通なので本体（`houou/players/`）にも入れてよい（未公開のため）。再生ボタンの位置は、決まるまで本体は (a) のまま
* (J)・(D) でボタンを出さないタブ（期別・通算）では、ボタンの場所を空けない（題名・名前の面の幅は変えない）。(D) で名前の面の右にボタンを置くとき、名前とリーグのチップの折り返しの規則（名前の最後の1文字とチップを1組にする）は保つ
* ボタンの見た目（押せる 44px、紺の 34px の丸）・ラベルの移り変わり（動きを再生 → 一時停止 → 続きを再生 → もう一度見る）・動きを減らす設定では出さないこと・キーボードの操作は今のまま（docs/notes/houou-top.md「リーグのタブ」、docs/notes/design.md「再生のボタン（新しい形）」）。(J)・(D) では、再生中にグラフへスクロールした状態でボタンが画面の外になることがあるが、グラフに触れる・Esc・Space で一時停止できる今の操作は残る
* 「期別」から「通算」「リーグ」へ切り替えたときにページの上下の位置が跳ばないか（要約の分だけ縮む）を確かめる。気になる跳びがあれば報告に書く（直し方は決めない）

手順

1. 着手の確かめ: #518・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`style.css` の houou/ の節・`docs/notes/houou-top.md`・`docs/decisions/houou.md` の同じ箇所の変更があるかを確かめ、ログに書く。
2. 実装: タブの移動と要約の出し分けを本体に入れる。再生ボタンの3案の比較ページを作る（本体は (a) のまま）。テストを必要なだけ直し `python3 -m unittest discover -s scripts/tests` を通す。`python3 scripts/regenerate.py houou_pages` で生成する。
3. 確かめ: headless Chromium で、375px・390px・430px × 明・暗で、3案それぞれの「リーグ」「通算」を開いた直後の、グラフの上端の y 座標（画面の上から）と、再生ボタンの位置（画面の中か）を表にする（白鳥翔・坪原光の2人）。今の本番（タブが要約の下）との差も書く。「期別」の見え方が変わっていないこと（要約・期の行）。文字の大きさ 150%・200% で切れ・重なりが無いこと。再生の始め・寄った後・終わりのアイコンの画面内の座標を 375px で書く（docs/notes/houou-top.md「確かめ方（再生）」）。決定を `docs/decisions/houou.md` に足し、`docs/notes/houou-top.md` の「個人成績」「リーグのタブ」「通算」のタブの並び・要約の記述を今の内容を読んでから直す（再生ボタンの位置は「比較中」と書く）。

止まる条件

* #518・#540 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 生成の差分に、決定と前提で説明できない種類がある（見込み: `houou/players/` のタブ・要約の並びと出し分け、比較ページの追加、`assets/houou.js` と `style.css` の houou/ の節）。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* 「期別」の見え方（要約・期の行）が変わってしまう
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、再生ボタンの位置 (a)・(J)・(D) のどれにするか（見る場所は比較ページ。URL は最終報告だけに書く）と、手順3のグラフの上端の表の要約を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-40.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-40 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-37〜39 と同じセッションで続けた。`CHAT-1015-HOU-40` のコミットは無し。`work/1015-hou` はローカル・リモートとも無かったので `origin/cloudflare`（3cf17d8b3）から作った
- 0. 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1015-hou
- ログ: https://github.com/retroeater/mj/blob/work/1015-hou/docs/logs/CHAT-1015-HOU-40.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 27fe15af）: https://github.com/retroeater/mj-logs/tree/main/guide/27fe15af

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/27fe15af/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/27fe15af.md
