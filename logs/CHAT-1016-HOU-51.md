# CHAT-1016-HOU-51

- 着手日時: 2026-10-16
- 対象issue: #542
- ブランチ: work/1015-hou6
- 着手時HEAD: d17b5f9e1

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1016-HOU-50 の続き — 質問欄の選手カードの「ほか N名」に開く印（下向きの矢印）を足し、PNG で送る
Chat-Ref: CHAT-1016-HOU-51
マージ: 判断待ちで止まる（HOU-49 のまま。マージは 10/20 の試行の判断の後に別の指示で）
貼る時機: CHAT-1016-HOU-50 の判断待ちの後（同じセッションでよい。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou6 の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1015-hou6 を続けて使う（HOU-49・HOU-50 の続き）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。origin/cloudflare は取り込まなくてよい（マージの指示で取り込む）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-50 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

## 目的

HOU-50 の PNG で、「ほか N名」は青い字でリンクとは分かるが、押すと下に開くことが見た目で伝わらなかった。開く印を足す。

### 決定（2026-10-16、平野さん）

- カードの見た目は HOU-50 の PNG のままでよい。ただし「ほか N名」の右に、開く印（下向きの矢印）を足す
- 写真の丸の縁（明るい配色で白地の写真の縁が見えない点）は、この指示では直さない

### 前提（チャット側。平野さんの決定ではない）

- 印は既存の行の › と同じ線の太さ・色の系統の下向きの矢印（新しい色・アイコンの読み込みは作らない。houou/ ですでに使っている矢印の描き方があればそれに合わせる）。置き場は「ほか N名」の文字のすぐ右（行の右端の › の位置にそろえる案もある。どちらにしたかと理由をログに書く）
- 押して開いた後は、今のとおり「ほか N名」の行は消えて残りの行に置き換わる（印も一緒に消える）
- 読み上げ（`aria-expanded` など）が今の作りにあれば保つ。無ければ足すかは受け手が決めてログに書く

## 手順

1. CHAT-1016-HOU-50 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1016-HOU-51` を足す。#542 に他のセッションの着手中のコメントが無いか確かめる。
2. 印を足し、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通し、生成し直す。HOU-50 と同じ撮り方（本番の答えを撮る側で返す。本番へ送る回数 0）で、`ask-card-2-light.png`・`ask-card-2-dark.png`（5人・開く前）と、字 200% の5人・開く前（明だけ、`ask-card-6-light.png`）の3枚を撮って `SendUserFile` で送る。

## 止まる条件

- CHAT-1016-HOU-50 のログの状態が判断待ちでない、または work/1015-hou6 がリモートに無い
- #542 に他のセッションの着手中のコメントがある
- 直しが「ほか N名」の部品の外（カードの行・答えの面・トップの3項目）の見た目に及ぶ
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、HOU-49 の残り（何人まで名前を書かせるか・AI への指示の変更の確かめ方）をそのまま引き継いで書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-51.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-51 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手。HOU-50 と同じセッションで続けた。`CHAT-1016-HOU-51` のコミットは無し。`work/1015-hou6` はリモートにありローカルと一致（d17b5f9e1）

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-50 のログの `## 報告` の状態は判断待ち。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 1. HOU-50 のログの状態の末尾に ` / 続き: CHAT-1016-HOU-51` を足した（このコミットを成果物のコミット b36463fab に混ぜてしまった。ログと成果物を分ける規則から外れた）。#542 の着手中は自分の HOU-49 のものだけ
- 2. 印: `assets/houou_ask.js` の「ほか N名」のボタンに、行の › と同じ線（太さ 2・丸い端・`.mj-houou-card-chev` の灰色 `--mj-n-muted`）を下向きにした矢印（14×8px）を足した。新しい色・アイコンの読み込みは無し。houou/ の期のカードの開閉の印（`TOGGLE_CHEVRON`、22px・太さ 2.4）ではなく、この面の行の › に合わせた（同じ面に並ぶ印の太さ・色をそろえるため）
  - 置き場は行の右端（› と同じ列。幅 14px の中心を › の中心にそろえるため右に 3px 寄せた）。理由: 期別の期のカードも開く印を右端に置いていて、同じ面の › と縦に並ぶほうが「この行も押せる」と読めるため
  - 押した後は今のとおり「ほか N名」の行（印も）が残りの行に置き換わる。`aria-expanded` は今の作りに無く、足さなかった（押すとボタン自体が消えて残りの行に置き換わり、フォーカスが4人目へ移るので、開いた状態を読み上げる対象が残らないため）
  - 変えたのは「ほか N名」の部品だけ（`assets/houou_ask.js` の `showCards` の中と `assets/houou_ask.css` の `.mj-houou-ask-more-chev`）。カードの行・答えの面・トップの3項目は変えていない
  - `python3 -m unittest discover -s scripts/tests` OK、`node --test 'workers/ask/test/*.test.mjs'` 27件 OK。生成し直した差分は無し
  - 撮影: HOU-50 と同じ撮り方（プレビュー、幅 390px・3倍、HOU-49 のログの本番の答えを撮る側で返す。本番へ送った回数 0）。撮るスクリプトは `/home/user/ask-hou51/shot.mjs`（使い捨て）。カードの面は範囲の中・横のはみ出し無し・写真は読み終わっている。送ったファイル（SendUserFile、3枚）: ask-card-2-light.png・ask-card-2-dark.png（5人・開く前）、ask-card-6-light.png（字 200%、5人・開く前）

## 報告

- 状態: 判断待ち（送った PNG 3枚で「ほか N名」の印を確かめて決める。マージは 10/20 の試行の判断の後に別の指示で）
- ブランチ: work/1015-hou6
- ログ: https://github.com/retroeater/mj/blob/work/1015-hou6/docs/logs/CHAT-1016-HOU-51.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou6
- 確認用URL: なし（見る物は送った PNG 3枚。ask-card-2-light・ask-card-2-dark・ask-card-6-light）
- マージ: 未（判断待ち）
- issue: なし（#542 の HOU-49 の着手中が続いている）
- 判断が必要なこと:
  - 「ほか N名」の印（行の右端、› と同じ線の下向きの矢印）でよいか
  - （HOU-49 から引き継ぐ）何人まで名前を書かせるかの案: 10人まで名前を書き、それより多いときは上位10人の名前と「ほか N人」・全体の人数にする（実例は5人で約100字・6人で約110字。指示の「3文程度まで」に収まるのは10人前後まで）
  - （HOU-49 から引き継ぐ）AI への指示の変更の確かめ方の案: (a) マージの指示で、平野さんが `workers/ask/.dev.vars` に鍵を置いた環境で作業ブランチの Worker を `wrangler dev` で動かし、10問を変更の前後で比べる（本番の回数に数えない）／(b) マージの後に本番で10問を送って前の記録と比べる（1日の上限に数える）。(a) を推す
- 未確認の項目: 平野さんの iPhone での見え方（マージの後）。AI への指示の変更による答えの変化
- エラー: なし（HOU-50 のログの1行を成果物のコミットに混ぜた。内容は正しく、コミットの分け方だけの問題）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8e48fa5f）: https://github.com/retroeater/mj-logs/tree/main/guide/8e48fa5f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a94b0a64.md
