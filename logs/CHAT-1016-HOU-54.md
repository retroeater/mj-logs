# CHAT-1016-HOU-54

- 着手日時: 2026-10-16
- 対象issue: #518
- ブランチ: work/1016-hou2
- 着手時HEAD: a9421dee9

## 指示

【Claude作成】Claude Code 向け指示：houou/ のランキング9部門の名前をメニューの略称に統一し（略称を正式名に）、規定のある部門の表の見出しに ⓘ（押すと見出しの下に規定の1行が開く）を付ける（プレビューまで） Chat-Ref: CHAT-1016-HOU-54 マージ: 判断待ちで止まる（見た目の変化を、チャット側が PNG とプレビューで見てから決めるため） 貼る時機: CHAT-1016-HOU-53 の完了（マージ）の後（同じセッションでよい。新しいセッションは任意）。`workers/ask/` を HOU-53 と続けて触るため、HOU-53 が終わる前に始めない 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-hou2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-53 のログの `## 報告` を読み、cloudflare へのマージが済んでいなければ何もせず止まる。

目的
平野さんの iPhone での確かめ（2026-10-16）で、ランキングの表の部門名（正式名）がメニューのチップ（略称）と違って読みにくかった。略称を正式名にして houou/ の中をそろえ、規定（出場期数など）の注釈を、メニューと表の間に行を足さずに見出しの ⓘ で出す。
決定（2026-10-16、平野さん）

* ランキング9部門の名前は、メニューの略称を今後の正式名称とする: 通算・期平均・期最高・期浮き率・期連続浮き・節最高・節浮き率・節連続浮き・連続昇級（2026-10-10 の「チップの `aria-label` と h1・title・description・表の見出しは正式な部門名」と、2026-10-15 の HOU-43 の「部門別のタブの部門名は正式名」を置き換える）
* 規定の注釈（「出場10期以上」など）は、表の部門名の見出しの横に ⓘ を置き、押すと見出しのすぐ下に1行が開く形にする（質問欄の ⓘ と同じ動き。チャット側の見本の A）。メニューと表の間には行を足さない

前提（チャット側。平野さんの決定ではない）

* 名前を変える範囲は houou/ の中すべての案: ランキングの各ページの title・h1・description・og の文言・表の見出し・チップの `aria-label`、個人成績の部門別のタブの部門名、トップ・個人成績の説明文に部門名があればそれ、質問欄（`workers/ask/data.json` の部門名・出典の名前〈例「鳳凰戦 ランキング（通算得点）」→「鳳凰戦 ランキング（通算）」〉）。URL のスラッグ（`total/` など）と `?division=` の旧い値の受け付けは変えない。変えた場所を一覧にしてログに書く
* 質問欄は、利用者が旧い名前（「通算得点」「期単位浮き率」など）で聞いても同じ部門が当たるよう、旧い名前を別名として残す（検索・AI への入力のどちらで受けるかは受け手が決め、ログに書く）。テストで、旧い名前と新しい名前の両方で同じ部門が当たることを確かめる
* ⓘ を付けるのは規定のある部門だけ。規定の中身（出場期数・節数）は `lib/ranking.py` の値から作る（文面を手で書かない）。文面の案:「出場10期以上の選手が対象です」（実物の規定に合わせる）。ⓘ は質問欄の ⓘ と同じ形・色、押す範囲は見出しの部門名と ⓘ の両方、開いた1行は見出しの下で表の幅いっぱい、もう一度押すと閉じる。読み上げは `aria-expanded` と、開く1行への `aria-controls`
* 個人成績の部門別の「規定未達」の表示はそのまま（ⓘ は足さない）
* `docs/decisions/houou.md` に決定を足し（置き換えの扱いは docs/decisions/README.md のとおり）、`docs/notes/houou-top.md` のチップの名前の行・ランキング・部門別の記述、`docs/notes/ask-worker.md` の部門名の記述を今の内容を読んでから直す

手順

1. 着手の確かめ: #518・#540・#542 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`scripts/lib/ranking.py`・`scripts/generate_houou_pages.py`・`scripts/generate_ask_data.py`・`assets/houou.js`・`assets/houou_ask.js`・`workers/ask/`・`style.css` の houou/ の節・`docs/notes/houou-top.md`・`docs/notes/ask-worker.md`・`docs/decisions/houou.md` の同じ箇所の変更があるかを確かめ、ログに書く。
2. 実装: 決定と前提のとおりに名前を変え、ⓘ を付ける。テストを足し・直し（少なくとも: 9部門の名前が略称になっていること、規定のある部門だけ ⓘ があり文面が規定の値と一致すること、質問欄で旧い名前と新しい名前の両方が同じ部門に当たること）、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。`python3 scripts/regenerate.py houou_pages` と `python3 scripts/regenerate.py ask_data` で生成する。
3. 確かめと送付: プレビュー（ブランチの別名 URL）で、375px・390px・430px × 明・暗、字 150%・200% の切れ・重なり。ⓘ の開閉（キーボードと読み上げを含む）。PNG を `SendUserFile` で送る（幅 390px・3倍）: 期浮き率のページの見出しと上位数行の ⓘ を閉じた状態と開いた状態（明・暗）、通算のページの見出し（ⓘ が無いこと、明）、白鳥翔の部門別のタブ（明）、字 200% の期浮き率の ⓘ を開いた状態（明）。

止まる条件

* CHAT-1016-HOU-53 のマージが済んでいない
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 生成の差分に、決定と前提で説明できない種類がある（見込み: `houou/ranking/` の9ページ、`houou/players/`、`houou/results/*.json` に部門名があればその文字、`workers/ask/data.json` の部門名・出典の名前、`assets/houou.js` と `style.css` の houou/ の節、docs とテスト）。ランキングの値・順位が変わったら止まる。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「確認用URL」には、期浮き率・通算のランキングと白鳥翔の部門別を挙げる（URL そのものは最終報告だけに書く）。「判断が必要なこと」に、名前を変えた場所の一覧で迷ったところと、ⓘ の見た目でよいかを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-54.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-54 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-53 と同じセッションで続けた。`CHAT-1016-HOU-54` のコミットは無し。`work/1016-hou2` はローカル・リモートとも無かったため `origin/cloudflare`（a9421dee9）から作った

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4fdda151）: https://github.com/retroeater/mj-logs/tree/main/guide/4fdda151

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fdda151/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
