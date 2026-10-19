# CHAT-1020-LNK-11

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-lnk
- 着手時HEAD: c9d6875f2

## 指示

【Claude作成】Claude Code 向け指示：LNK-09 の申送りを、2つとも chat-routines.md に書く（LNK-09 の判断待ちへの回答）
Chat-Ref: CHAT-1020-LNK-11
マージ: ドキュメントのみ（docs/ と docs/decisions/・docs/logs/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-lnk の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1020-lnk を続けて使う（CHAT-1020-LNK-09 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1020-LNK-09 は、docs/instruction-template.md が容量の警告域まで残り 11 bytes のため止まった。申送りの2つを、雛形ではなく docs/notes/chat-routines.md（容量の上限なし）に書く。

### 決定（2026-10-20、平野さん）
- 1つ目の申送り（本番の確かめを「未確認の項目」に回す）は、雛形ではなく chat-routines.md「指示の種類ごとの確かめ」の「マージ・クローズの指示」の行に書く（LNK-09 の判断待ちへの回答。雛形の整理はしない）
- 2つ目（振り返りの「前回の約束」の確かめ方）も同じ指示で書く

### 前提（チャット側。平野さんの決定ではない）
- 書く中身と「知見 → 層 → 理由」の表は LNK-09 の指示文の「前提」のとおり。1つ目の置き場所だけ docs/instruction-template.md から docs/notes/chat-routines.md「指示の種類ごとの確かめ」の「マージ・クローズの指示」の行に変える（理由: マージの指示を書くときにチャット側が読む節で、容量の上限が無い）
- 1つ目の文の案: 「本番の確かめが要るマージでは、15分で本番が古いままなら判断待ちにせず『未確認の項目』に回して完了にする、と指示文に書き、チャット側が send_later で確かめる（PAD-09）」。既存の行への統合でよい
- 2つ目の置き場所は LNK-09 のとおり（「振り返り」の観点のチェックリストの「前回の振り返り・レビューで立てた約束…」の行）

## 手順
1. LNK-09 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1020-LNK-11` を足す。追記先の2行の今の内容を `## 経過` に引用する。
2. 2つを規則と理由の一句だけで書く。docs/decisions/operations.md に「2026-10-20（CHAT-1020-LNK-09・LNK-11）」として、申送りと置き場所の決定を書く。
3. 変えた箇所の前後を `## 経過` に引用し、cloudflare へ入れる。

## 止まる条件
- 追記先と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止まらず、置き換え・拡張してよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-LNK-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-LNK-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2ec24f23）: https://github.com/retroeater/mj-logs/tree/main/guide/2ec24f23

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e7c8d9e0.md
