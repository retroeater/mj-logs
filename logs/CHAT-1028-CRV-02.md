# CHAT-1028-CRV-02

- 着手日時: 2026-10-28（JST）
- 対象issue: なし
- ブランチ: work/1028-crv
- 着手時HEAD: 2acf2fbec

## 指示

【Claude作成】Claude Code 向け指示：CRV-01 のガイド文書の追記（自己レビュー・マージを戻すとき・続きの指示の見直し）をマージする
Chat-Ref: CHAT-1028-CRV-02
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1028-CRV-01 の作業ブランチ work/1028-crv が未マージでリモートにあること。0章で確かめる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-crv の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1028-crv を続けて使う（CRV-01 の成果物をそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、origin/work/1028-crv が origin/cloudflare にマージ済みでないことを確かめ、マージ済みなら何もせず止まる。

## 目的

CHAT-1028-CRV-01 で work/1028-crv に足したガイド文書の変更（コミット 5040b675c）を、下の決定のとおり1行直して cloudflare へマージする。

### 決定（2026-10-28、平野さん）
- CRV-01 の3点の文面（CRV-01 のログ `## 経過`「手順3」の差分）を採ってマージする
- (2)「マージを戻すとき」は CRV-01 で変えた形（前後の版の差分を逆向きに当てる1コミット）でよい
- 戻しのコミットの件名の接頭辞は `revert:` でよい
- `/code-review` は試し直さない（Code の実行環境はクラウドセッションのみ。サブエージェントのレビューで行う）

### 前提（チャット側。平野さんの決定ではない）
- `docs/notes/self-review.md`「`/code-review` を使わない理由」の試した日「2026-10-27」は、CRV-01 のログの着手日（JST 2026-10-28）と食い違う（UTC で書いたと思われる。要確認: CRV-01 のログのヘッダの着手日時）。JST の日付に直す。ほかの記述は変えない

## 手順
1. 前のログ `docs/logs/CHAT-1028-CRV-01.md` の `## 報告` の状態の末尾に ` / 続き: CHAT-1028-CRV-02` を足す（CLAUDE.md「作業ログ」節の書き換えの注意のとおり）
2. `docs/notes/self-review.md` の「2026-10-27」を CRV-01 のログの着手日（JST）に直す。CRV-01 のログの着手日が 2026-10-27 なら直さず、その旨を `## 経過` に書く
3. origin/cloudflare を取り込んだ後（必要なら）、`python3 -m unittest discover -s scripts/tests` を回し、CLAUDE.md「ブランチ運用」「マージするとき」（docs/notes/branch-operations.md、push の出力の範囲 `<前>..<後>` をログに書く、を含む）のとおり cloudflare へマージする。マージ後、CRV-01 のログの状態を直す必要があれば CLAUDE.md「作業ログ」節のとおり直す

## 止まる条件
- 0章の確かめに当たる（work/1028-crv がリモートに無い・マージ済み）
- origin/cloudflare の取り込みで、`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうし以外の衝突が出る。ただし CLAUDE.md・`docs/handover.md`・`docs/notes/branch-operations.md`・`docs/notes/chat-routines.md` で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ前の `git diff --stat origin/cloudflare...HEAD` に、CRV-01 の6ファイル（CLAUDE.md・docs/handover.md・docs/notes/self-review.md・docs/notes/branch-operations.md・docs/notes/chat-routines.md・docs/decisions/operations.md）と docs/logs/ 以外のファイルが出る
- テストが失敗する（docs のみの変更なので、失敗したら原因を書いて止まる）
- 変更後の CLAUDE.md・docs/handover.md が警告域（26KB=26624 バイト）を超える
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
- マージ後、作業ブランチ work/1028-crv の片付けは docs/notes/branch-operations.md「ブランチを削除するとき」のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-CRV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-CRV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dd51fd5a）: https://github.com/retroeater/mj-logs/tree/main/guide/dd51fd5a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dd51fd5a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ef29ee9e.md
