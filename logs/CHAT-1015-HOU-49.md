# CHAT-1015-HOU-49

- 着手日時: 2026-10-15
- 対象issue: #542
- ブランチ: work/1015-hou6
- 着手時HEAD: e9d5efe88

## 指示

【Claude作成】Claude Code 向け指示：質問欄（#542）の答えの下に、答えに出た選手のカードを出して個人成績へ進めるようにする（作業ブランチでプレビューまで。マージは 10/20 の試行の判断の後）
Chat-Ref: CHAT-1015-HOU-49
マージ: 判断待ちで止まる（見た目と動きをチャット側がプレビューで見てから決めるため。マージは 10/20 の試行の判断の後に、別の指示で行う）
貼る時機: いつでも（新しいセッションは任意。HOU のセッションの続きでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou6 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-hou6 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

今の質問欄では、答えの下の「出典」は答えの元になったデータのページ（歴代優勝者など）へのリンクで、答えに出てきた選手の個人成績へは進めない。答えに出てきた在籍の選手のカードを答えの下に出し、押すと個人成績へ進めるようにする。

### 決定（2026-10-15、平野さん）

- 形: 答えの面の下に別の面を置き、選手を1人1行で並べる（トップの3項目と同じ行の形。チャット側の見本の「1人 B」「複数 M1」）。1人のときは1行だけ
- 行の中身: 1行目は写真（無ければ頭文字の丸）・名前・今のリーグのチップ（個人成績の名前の面と同じ）。2行目は「最高到達 ○○ · 出場 ○期」（名前の面の2・3行目を1行にまとめたもの）。右端に ›
- 出す選手の決め方: 答えの文に出てくる選手名を、在籍の選手の名簿と突き合わせて拾う
- 並び順: 文に出てきた順。同じ選手が何度出ても1行。最初は3人まで出し、4人目からは「ほか N名」を押して開く
- 退会した選手（個人成績のページが無い選手）は出さない（在籍の選手の名簿だけと突き合わせる）
- 名前が重なるとき: 長い名前を優先する（文の同じ文字に当たった短い名前は出さない）
- 押した先: 個人成績の期別（`/houou/players/?name=<選手名>`、`view` なし）
- 答えを作る側（AI への指示）に「選手を挙げるときは名前を書く。多いときは文に出た順で並べ、人数も添える」を足す
- 時期: 今から作業ブランチで作ってプレビューまで進め、マージは 10/20 の試行の判断の後にする（試行中の質問欄の答え方・画面を変えないため）。告知動画は、カードが入った本番で撮り直す

### 前提（チャット側。平野さんの決定ではない）

- 突き合わせは画面側（`assets/houou_ask.js`）で行う案。名簿（在籍の選手の名前・写真・今のリーグ・最高到達・出場期数）は生成のときに小さな JSON にして配る（置き場と大きさは受け手が決めてログに書く。個人成績の名前の面と同じ集計から作り、値を一致させる）。こうすると、試行中の本番の Worker を変えずに、プレビューで本番の答えを使ってカードを確かめられる。Worker 側で選手の一覧を返すほうがよい理由があれば、変えてよい（理由をログに書く）
- AI への指示の変更は Worker（`workers/ask/`）の変更になる。マージまで本番に出さない。答えの質の確かめは、作業ブランチの Worker を本番以外の手段（`wrangler dev` など）で動かせるなら、10問程度で変更の前後を比べてログに書く（使った問いと答えの全文。本番の質問欄には送らない）。鍵などの都合で動かせなければ、確かめの方法の案を報告に書き、マージの指示で行う（ここでは止まらない）。何人まで名前を書かせるかは、答えの長さの実例を見て案を出す
- 名前の重なり: 在籍の選手の名簿で、ある名前が別の名前の一部になっている組を数えてログに書く
- 見た目はトップの3項目の行と個人成績の名前の面の値（docs/notes/design.md の houou/ の行）に合わせる。新しい色は作らない。「ほか N名」の開き方（開いた後に閉じるか等）は受け手が決めてログに書く
- 答えが「答えられません。」・上限・失敗のときはカードの面を出さない。選手に当たらない答えでも出さない
- 質問欄の注意書き（個人情報・利用目的）とプライバシーの扱いは変えない。突き合わせは画面の中だけで、新しい送信はしない

## 手順

1. 着手の確かめ: #542・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou_ask.js`・`workers/ask/`・`scripts/generate_ask_data.py`・`scripts/generate_houou_pages.py`・`style.css` の houou/ の節・`docs/notes/ask-worker.md`・`docs/notes/houou-top.md`・`docs/decisions/houou.md` の同じ箇所の変更があるかを確かめ、ログに書く（work/1015-hou5〈告知動画〉を含む）。
2. 実装: 決定のとおりにカードの面・名簿の JSON・突き合わせ・「ほか N名」・AI への指示の変更を入れる。テストを足し（少なくとも: 文に出た順・重複の除き・長い名前の優先・退会者を拾わない・当たらないときは出さない・「ほか N名」の境目〈3人と4人〉）、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。生成し直す。
3. 確かめと文書: プレビュー（ブランチ名の別名 URL）で、375px・390px・430px × 明・暗、字 150%・200% の切れ・重なり。次の答えでカードを確かめる: 1人（例「現在の鳳凰位は？」→ 白鳥翔）、4人以上（「ほか N名」が出る問い）、退会者を含む答え、選手に当たらない答え。カードを押して期別が開くこと。本番の質問欄へ送るのは合わせて5回まで（送った回数と時刻をログに書く。10/20 の集計から除けるように）。決定を `docs/decisions/houou.md` に足し、`docs/notes/ask-worker.md`（答えの表示・AI への指示・名簿）と `docs/notes/houou-top.md`（トップの質問欄）を今の内容を読んでから直す。

## 止まる条件

- #542・#540 に他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
- 生成の差分に、決定と前提で説明できない種類がある（見込み: 名簿の JSON の追加、`assets/houou_ask.js` と `style.css` の houou/ の節、`workers/ask/` の AI への指示、docs とテスト）。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
- 名簿の値（最高到達・出場期数・今のリーグ）が個人成績の名前の面と1件でも食い違う
- 質問欄の既存の表示（答え・注記・データ・出典・上限・失敗）や、トップの3項目の見た目が変わってしまう
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「確認用URL」には、トップ（質問欄）のプレビューを挙げる（URL そのものは最終報告だけに書く）。「判断が必要なこと」に、何人まで名前を書かせるかの案と、AI への指示の変更の確かめ方（手順2でできなかった場合）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-49.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-49 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-48 と同じセッションで続けた。`CHAT-1015-HOU-49` のコミットは無し。`work/1015-hou6` はローカル・リモートとも無かったため `origin/cloudflare`（e9d5efe88）から作った

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e9d5efe8）: https://github.com/retroeater/mj-logs/tree/main/guide/e9d5efe8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e9d5efe8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
