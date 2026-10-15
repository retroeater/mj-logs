# CHAT-1015-JBK-04

- 着手日時: 2026-10-15
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 60f8598f82e0465b104f0221f1f3334f2517ae12

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（見出しを「partN」だけに、他のエピソードの節をやめる）
Chat-Ref: CHAT-1015-JBK-04
マージ: 判断待ちで止まる（平野さんがプレビューで仕様を確かめてから決める）
貼る時機: CHAT-1015-JBK-03 を貼ったのと同じセッションに、JBK-03 の実行中でも貼ってよい（JBK-03 の最終報告の後に取りかかる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1015-JBK-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1015-JBK-03 のログの `## 報告` があり、プレビューまで出せていることを確かめる（無い・止まっていれば何もせず止まる）。JBK-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JBK-04` を足す。
   **平野さんは不在のことがある。質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。**

## 目的
JBK-02 の報告の仮置き1・3・4 に平野さんの答えが出たので反映する。

### 決定（2026-10-15、平野さん）
- 見出し（一覧の最新話・各話のヒーロー）は「part73」のように回の部分だけにする（シリーズ名「女流勉強会」は見出しに繰り返さない）
- 「〇〇の他のエピソード」の節は出さない（前後のエピソードだけにする）
- MC の写真カードの節は出す（JBK-02 のまま。MC「大和」の確認は平野さんが後日行う）

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 見出しの作り方: YouTube の題名から【】の札とシリーズ名「女流勉強会」を外した残り（例「part73」）。題名がこの形に当てはまらない回があれば、題名から【】の札だけを外したものにし、その回を報告に書く
- `<title>`・og:title・前後のエピソードのリンク・`VideoObject.name` に見出しを使っているところは、シリーズ名が無いと何の回か分からなくなるので「女流勉強会 part73」の形のまま残す（今の形を確かめ、変えたところを報告に書く）
- 一覧のカードの題名の行も見出しと同じ「part73」にそろえる案（カードの並びは一覧の中なのでシリーズ名は要らない）。見比べて悪ければ今のままでよい
- 写真の規則・`.github/workflows/` を変えないこと・帰り道・Mつく・title/ の生成物の差分0は JBK-01〜03 のまま

## 手順
1. 上の決定・前提で見出し・カードの題名・「他のエピソード」の節を直し、テストを直す。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 375px・PC 幅で一覧・最新話・最古の回を headless Chrome で見て所見をログに書き、プレビュー（確認用 URL）を出す
3. 決定を `docs/decisions/`（JBK の分野のファイル）に、変わった仕様を JBK の実装記録に書く（今の内容を読み、古い記述は置き換える）。#575 に進み具合をコメントする

## 止まる条件
- 0章の JBK-03 の報告が無い・プレビューまで出せていない
- 帰り道・Mつく・title/ の生成物に差分が出て、共通化の直しで消せない
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、JBK-03 から引き継いだ残りの仮置きとこの指示の仮置きを1つの一覧にし、平野さんの手作業（JBK-03 の報告にあるもの）を引き継いで書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JBK-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JBK-04 を書く

平野さんは不在のことがあるため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1015-JBK-04"` と `docs/logs/` に無し
- 作業ブランチ: ローカル work/JBK はリモートと一致（60f8598f8）。`origin/cloudflare` は祖先でない → 取り込む

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1015-JBK-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 08943b5e）: https://github.com/retroeater/mj-logs/tree/main/guide/08943b5e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/08943b5e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
