# CHAT-1103-TOP-09

- 着手日時: 2026-11-03
- 対象issue: #101
- ブランチ: work/1103-top
- 着手時HEAD: 0ea836001

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の質問欄の例をチップから入力欄の中の例示に替える（#101）
Chat-Ref: CHAT-1103-TOP-09
マージ: 判断待ちで止まる（TOP-07・TOP-08 と合わせてプレビューを平野さんが見て決める）
貼る時機: CHAT-1103-TOP-08 が判断待ちで止まった後（同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-07・TOP-08 の上に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1103-TOP-08 のログの `## 報告` の状態が「判断待ち」で、origin/work/1103-top が cloudflare に未マージであることを確かめる（違えば何もせず止まる）。TOP-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1103-TOP-09` を足す。

## 目的

TOP-07 で入力欄の下に置いた質問の例のボタン（チップ）3つをやめ、鳳凰戦などのトップの質問欄と同じく、入力欄の中の文言に例を1つ書く形にする。

### 決定（2026-11-03、平野さん。TOP 系列のチャットで。TOP-07 のプレビューを見て）

- 質問の例のボタン（チップ）はやめる
- 鳳凰戦などのページの質問欄のように、入力欄の中の文言に例を示す。例は「現鳳凰位は？」
- 2026-11-03 の決定のうち「入力欄の下に押すとそのまま送る例を3つ」を置き換える（TOP-07 で仮置きした3つ目の差し替え〈「女流桜花の歴代優勝者は？」〉も要らなくなる）

### 前提（チャット側。平野さんの決定ではない）

- 鳳凰戦のトップの入力欄の文言は「鳳凰戦について質問してみてください（例：23期生で鳳凰位になった人は？）」（docs/decisions/houou.md の 2026-10-10〜11 の決定）。トップの文言はこれにそろえて「選手や大会について質問してみてください（例：現鳳凰位は？）」を仮置きにする（360px で入力欄に収まらないなら、収まり方と案を報告に書く。大会別の欄で長い文言をどう扱っているかに合わせる）
- 例のボタンのために足した JS・CSS（`assets/top_ask.js` などの例の送信）は消す。注意書き（「AI の回答には…」と ⓘ）は入力欄の下に残す
- 消した後に使われなくなるクラス・関数・テストの検査を残さない

## 手順

1. 例のボタンと、そのための JS・CSS・HTML の生成・テストを消し、入力欄の文言を上の形にする。`docs/notes/top-page.md`・`docs/decisions/top.md`（上の決定を置き換えの形で）を直す。#101 に着手中コメント
2. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）・画面の確かめ（360px・400px、明暗）
3. ブランチ名の別名 URL でプレビューできる状態にして止まる。`## 報告` には TOP-07〜09 を合わせた cloudflare との差分の一覧（マージの判断に使う）を書く

## 止まる条件

- TOP-08 が判断待ちでない、または work/1103-top がマージ済み
- top/ 以外のページの生成物に差分が出た（見込みは0件）
- 未マージの work/ ブランチが `scripts/generate_top_page.py`・`assets/top_ask.js` の同じ行・同じ関数を変えている、または取り込みで衝突する
- #101 に他セッションの着手中コメントがある

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c129a98b）: https://github.com/retroeater/mj-logs/tree/main/guide/c129a98b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c129a98b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
