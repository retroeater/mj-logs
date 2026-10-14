# CHAT-1015-HOU-41

- 着手日時: 2026-10-15
- 対象issue: #518
- ブランチ: work/1015-hou
- 着手時HEAD: 60631f287

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1015-HOU-40 の判断待ちへの回答 — 再生ボタンは (D) 名前の面・アイコンは名前の面の写真から落ちる・タブを替えたらタブの行を画面の上端へ
Chat-Ref: CHAT-1015-HOU-41
マージ: 判断待ちで止まる（動きと見た目の変化を、チャット側がプレビューで見てから決めるため）
貼る時機: CHAT-1015-HOU-40 の判断待ちの後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou を続けて使う（CHAT-1015-HOU-40 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-HOU-40 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

## 目的

CHAT-1015-HOU-40 の「判断が必要なこと」への平野さんの回答を、未公開の個人成績 `houou/players/`（#518）に入れる。

### 決定（2026-10-15、平野さん）

- 再生ボタンの位置は (D) 名前の面の右端にする（リーグのタブのときだけ出す）。グラフの上の 52px の行は「リーグ」「通算」とも無くす。(a)・(J) の案と比較ページ `houou/compare-play/` は消す
- 再生の最初の動き（アイコンがスタート地点に落ちる）は、名前の面の左のプロフィールアイコン（X の写真、写真が無い選手は頭文字の丸）から、グラフのスタート地点（最初に出た期の段）へ落ちてくる形にする（2026-10-09 の「左上のプロフィールアイコンから、左下のスタート地点にストンと落ちる」に戻す。2026-10-12 の HOU-30 で仮置きにし、2026-10-14 に仮置きの一括の決定に含まれた「グラフの上端〈スタート地点の真上〉から落ちる」を置き換える）
- タブを切り替えたとき、タブの行が画面の上に隠れていれば、タブの行が画面の上端に来る位置までスクロールする。タブの行が見えているときは動かさない（「期別」→「リーグ」でページの一番上に戻る振る舞いと、「期別」を下まで読んだ途中で「通算」に替えると変な位置に戻される振る舞いを置き換える）

### 前提（チャット側。平野さんの決定ではない）

- 落ちる動きの作りは任せる（例: 名前の面のアイコンの写しを画面の上の位置から、SVG の中のスタート地点の画面上の位置まで動かし、着いたら SVG の中のアイコンに置き換える）。今の重力のある落ち方・弾み（0.9秒）、落ちた後の寄り・山が生える・止める・登る以降の動きは変えない。落ちる間、名前の面のアイコンは元の場所に残す（消さない）でよい
- 再生を押した時点で、名前の面のアイコンまたはスタート地点が画面の外にあるときの扱い（そのまま落とす／グラフの上端から落とす 等）は受け手が決め、ログに書く
- 字を 150%・200% にすると (D) の名前の面がボタンの分だけ高くなり、リーグのグラフが通算より 29〜52px 下になる（HOU-40 の計測）。この指示では直さず、新しい形で測り直した値を報告に書く
- タブの行を上端へ動かすときは、上に固定のナビ（共通ナビ）があればその下に隠れない位置にする。動かすときの動き（なめらか／一瞬）は、動きを減らす設定を尊重する範囲で受け手が決めてよい
- ボタンの見た目・ラベルの移り変わり・動きを減らす設定では出さないこと・キーボードの操作は今のまま。(D) で名前とリーグのチップの折り返しの規則（名前の最後の1文字とチップを1組にする）は保つ

## 手順

1. 着手の確かめ: #518・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`style.css` の houou/ の節・`docs/notes/houou-top.md`・`docs/decisions/houou.md` の同じ箇所の変更があるかを確かめ、ログに書く。CHAT-1015-HOU-40 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-HOU-41` を足す。
2. 実装: (D) を本体に入れ、(a)・(J) のコードと比較ページを消す。落ちる動きとタブの切り替えのスクロールを決定のとおりに作る。テストを必要なだけ直し `python3 -m unittest discover -s scripts/tests` を通す。`python3 scripts/regenerate.py houou_pages` で生成する。
3. 確かめと文書:
   - headless Chromium で、375px・390px・430px × 明・暗で、「リーグ」「通算」を開いた直後のグラフの上端の y と再生ボタンの位置を表にする（白鳥翔・坪原光）。字 150%・200% の値も書く
   - 落ちる動き: 375px と 1920px で、始め（名前の面のアイコンの位置）・中ほど・着いたとき・寄った後・終わりのアイコンの画面内の座標を書く（docs/notes/houou-top.md「確かめ方（再生）」）。画面の外なら直す
   - スクロール: 「期別」を下まで送ってから「通算」「リーグ」へ、ページの上の方で「期別」→「リーグ」、の各場合で、切り替えた後のタブの行の画面上の y を書く
   - `docs/decisions/houou.md` に上の「決定」を足す（置き換えた決定の扱いは docs/decisions/README.md のとおり）。`docs/notes/houou-top.md` の「個人成績」「リーグのタブ」（再生ボタン・動きの落ち始め・上の行）・「比較ページ」の節を今の内容を読んでから直す。docs/notes/design.md の「再生のボタン（新しい形）」の置き場の記述が今と食い違えば直す

## 止まる条件

- CHAT-1015-HOU-40 のログの状態が判断待ちでない、または work/1015-hou がリモートに無い
- #518・#540 に他のセッションの着手中のコメントがある
- 取り込みで、上に書いた解き方で解けない衝突が出た
- 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- 生成の差分に、決定と前提で説明できない種類がある（見込み: `houou/players/` の並び、比較ページの削除、`assets/houou.js` と `style.css` の houou/ の節）。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
- 「期別」の見え方（要約・期の行）や、落ちた後の再生の動き（寄り・山・登る・演出）が変わってしまう
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「確認用URL」には、落ちる動きを見るページ（白鳥翔・坪原光のリーグのタブ）を挙げる（URL そのものは最終報告だけに書く）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-41.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-41 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-40 と同じセッションで続けた。`CHAT-1015-HOU-41` のコミットは無し。`origin/work/1015-hou` はリモートにありローカルと一致（60631f287）
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-40 のログの `## 報告` の状態は「判断待ち（再生のボタンの位置を比較ページで選ぶ）」。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1015-hou
- ログ: https://github.com/retroeater/mj/blob/work/1015-hou/docs/logs/CHAT-1015-HOU-41.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1854428a）: https://github.com/retroeater/mj-logs/tree/main/guide/1854428a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d12b1f2e.md
