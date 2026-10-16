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

- 0. 指示欄の末尾は指示文の最後の行と一致。CHAT-1016-HOU-53 のマージは済み（直し 1447edda1..2ae48afbb・戻し 6e8459065..dbdd37c8e・ログ decfa3da5 が origin/cloudflare の祖先）。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. #518・#540・#542 に他のセッションの着手中は無し（#540 の最新の着手中は自分の HOU-48）。#518 の着手中のコメントは実装・確かめの後に書いた（コードを触る前に書くべきところを落とした。HOU-49 と同じ落とし）。未マージの work/ ブランチで手順1のファイルを変えるのは `work/1015-hou5`（自分の HOU-48、`docs/decisions/houou.md` の末尾の追記）と `work/1016-wfl-s8`（`docs/notes/houou-top.md` の「段1」の行のワークフロー名だけ）。同じ行・節ではないので止まらない
- 2. 実装（受け手が決めたこと）:
  - 部門の内部の名前（`scripts/lib/ranking.py` の `SORT_DIVISIONS`・`CONTESTS` のキー）は変えない。桜花・JWRC・特昇と旧ページで共有しているため（変えると houou/ の外の生成物が変わる）。houou/ で出す名前を `generate_houou_pages.py` の `RANKING_SHORT` から取る形にした
  - 名前を変えた場所: ランキング9ページの title・og:title・meta description・og:description・mj-lead（「通算・期最高・節最高など9部門」）・h1 の読み上げ用の「（部門名）」・表の見出し。チップの `aria-label` は外した（字が部門名なので）。代わりにチップに `data-division="<旧い正式名>"` を足し、`?division=` の旧い値（例「期単位浮き率」）を `assets/houou.js` の `initRanking` で受ける（今の名前でも受ける）。個人成績の部門別のタブの部門名（`houou_divisions`）。トップの項目の説明「通算など9部門」。質問欄の `workers/ask/data.json` の部門名（キー）と、出典の名前（「鳳凰戦 ランキング（通算）」の形。`query.mjs` が部門名から作る）。URL のスラッグは変えていない
  - 質問欄の旧い名前: 検索の側（`workers/ask/src/query.mjs` の `DIVISION_ALIASES`）で旧い名前を今の名前に直す。AI への1回目の出力の形（`PLAN_SCHEMA`）の enum に今の名前と旧い名前の両方を入れ、指示（`PLAN_SYSTEM`）に「旧い名前の …… でもよい」を足した。質問欄の答えの下の注記（「※出場10期以上のみ」）は変えていない
  - ⓘ: 規定のある期平均・期浮き率・節浮き率だけ。表の部門名の見出しを `<button aria-expanded aria-controls="houou_floor">部門名ⓘ</button>` にし、押すと thead の2行目（`<tr id="houou_floor" hidden>`、表の幅いっぱいの1つの欄）を開閉する（`assets/houou.js`）。文面「出場10期以上の選手が対象です」「出場50節以上の選手が対象です」は `ranking.CONTESTS["鳳凰"]` の `min_seasons`・`min_sections` から作る。ⓘ の色・大きさは質問欄の ⓘ と同じ（紺、1rem、幅 20px）。前の表の上の行（`<p id="houou_floor">※出場10期以上のみ</p>`）と、その CSS（`.mj-houou-floor` の2か所）は消した。開いた1行のセルの上の線を消した（見出しの下の線が二重に見えたため、4ef3fafa8）
  - 「通算」のタブのグラフの読み上げ「通算得点の推移」（`assets/houou.js`）と docs の中の過去の記録の部門名は変えていない（部門名ではなくグラフの名前のため）
  - テスト: `scripts/tests/test_houou_pages.py` に2件（9部門の名前が略称・title・h1・部門別のタブ・トップの項目、規定のある部門だけ ⓘ と開く1行があり文面が規定の値と一致・質問欄の注記は前のまま）、`scripts/tests/test_ask_data.py` の部門名、`workers/ask/test/query.test.mjs` の部門名と1件（旧い名前9つと今の名前で同じ結果・出典の名前）。`python3 -m unittest discover -s scripts/tests` OK、`node --test 'workers/ask/test/*.test.mjs'` 29件 OK
  - 生成し直した差分（origin/cloudflare との比較）: `houou/ranking/` の9ページ（名前・説明・見出し・ⓘ・チップの属性。表の行〈値・順位・リンク〉は9ページとも前とバイト一致）、`houou/players/index.html`（`houou_divisions` の名前）、`houou/index.html`（項目の説明）、`workers/ask/data.json`（部門のキー）。`houou/results/*.json` には部門名が無く差分無し。houou/ の外の生成物・noindex・navbar・サイトマップ・`llms.txt` の差は無し
  - `docs/decisions/houou.md` に決定を足し、2026-10-10 の B4 の行と 2026-10-15（HOU-43）の部門別の行に置き換えの印を付けた。`docs/notes/houou-top.md`（title・トップの項目・部門別・作り・部門名・説明文・規定打席・列名）と `docs/notes/ask-worker.md`（`ranking` の行）を直した
- 3. 確かめ（プレビュー、ブランチの別名 URL。最初は Workers Builds が遅れ、同じ確かめをローカルの wrangler dev でも回した）: 375・390・430px × 明・暗 × 字 100・150・200% で、期浮き率・期平均・節浮き率・通算・期連続浮きの5ページと白鳥翔の部門別（108通り）
  - 横のはみ出し・ページのエラーは無し。ⓘ は規定のある3部門だけ。ボタンは見出しの欄の中に収まる。開いた1行の幅は表の幅と同じ
  - 開閉: 押すと `aria-expanded="true"` と1行が出る、もう一度で閉じる。キーボード（フォーカスして Enter で閉じ、Space で開く）も同じ。`aria-controls="houou_floor"`、ⓘ の字は `aria-hidden`、読み上げ用に「（対象の規定）」
  - `?division=期単位浮き率` で開くと期浮き率のページへ移る
  - 部門別のタブは9部門とも略称
  - 字 200% では、見出しの「期浮き率ⓘ」は1行に収まるが、表の「順位」と名前・チップは前と同じく折り返す（今回の変更によるものではない）
  - PNG（幅 390px・3倍）を SendUserFile で送った: rank-kiukirate-closed-light.png・rank-kiukirate-open-light.png・rank-kiukirate-closed-dark.png・rank-kiukirate-open-dark.png・rank-total-light.png・players-division-light.png・rank-kiukirate-open-200-light.png。撮るスクリプトは `/home/user/hou54/check.mjs`（使い捨て）

## 報告

- 状態: 判断待ち（送った PNG とプレビューで見た目を見て、マージを決める） / 続き: CHAT-1016-HOU-55
- ブランチ: work/1016-hou2
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-HOU-54.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-hou2
- 確認用URL: プレビューあり（URL は最終報告）。見るページ: 期浮き率のランキング（見出しの ⓘ を押す）、通算のランキング（ⓘ が無い）、白鳥翔の個人成績の部門別のタブ。この版で見える印: 表の見出しが「期浮き率 ⓘ」で、チップと表の間に「※出場10期以上のみ」の行が無い
- マージ: 済（CHAT-1016-HOU-58 のマージ、0112228f3..aabdd8d5f）
- issue: #518（着手中のコメント。実装の後に書いた）
- 判断が必要なこと:
  - ⓘ の見た目（見出しの部門名の右に紺の ⓘ、開くと見出しの下に灰色の小さい字の1行）でよいか
  - 名前を変えた場所で迷ったところ: (1) トップの項目の説明が「通算など9部門」になった（前は「通算得点など9部門」）。読みにくければ「通算得点など」は残す案もある (2) ランキングの説明文の「通算・期最高・節最高など9部門」 (3) 質問欄の答えの下の注記「※出場10期以上のみ」は今回変えていない（ⓘ の文面「出場10期以上の選手が対象です」とそろえるかは別に決める） (4) 「通算」のタブのグラフの読み上げ名「通算得点の推移」は部門名でないので変えていない (5) 質問欄の部門名の変更は Worker の変更なので、マージで本番の質問欄も変わる（「記録の上位」の出典の名前が「鳳凰戦 ランキング（通算）」になる）
- 未確認の項目: 平野さんの iPhone での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cde9da6f）: https://github.com/retroeater/mj-logs/tree/main/guide/cde9da6f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde9da6f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
