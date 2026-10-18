# CHAT-1018-OUK-11

- 着手日時: 2026-10-18
- 対象issue: #519・#597
- ブランチ: work/OUK（CHAT-1018-OUK-10 の続き。本文は OUK-10 のログの「指示」欄）
- 着手時HEAD: 115263819

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1018-OUK-10 の続き（「桜花」の「結果」が貼られたので、OUK-10 の本文のとおり作業してプレビューで止まる） Chat-Ref: CHAT-1018-OUK-11 マージ: 判断待ちで止まる（平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: 平野さんが「桜花」の「結果」列（F列）に CHAT-1018-OUK-09 の `ouka_results.tsv` を F2 から貼った後（CHAT-1018-OUK-10 と同じセッションでよい）。0章で確かめる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: CHAT-1018-OUK-10 の「作業ブランチ:」の行のとおり（未マージの work/OUK を続けて使う。origin/cloudflare は merge で取り込む。rebase・force push はしない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1018-OUK-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1018-OUK-11` を足す。 続いて CHAT-1018-OUK-10 の0章の確かめをもう一度行う。「桜花」の「結果」は、「入替戦」が0行で、「入替戦」列が Y の49行が昇級25・残留18・降級6、OUK-09 の `ouka_results.tsv` と全行一致であること。ほかの確かめ（「桜花」の「入替戦」Y 49、「鳳凰」の「入替戦」Y 12・「結果」の「入替戦」12行）も OUK-10 のとおり。合わなければ止まる。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは OUK-10 の「止まる条件」に当たるときだけ。

目的・決定・前提・手順・止まる条件
CHAT-1018-OUK-10 のログの「指示」欄の本文（「## 目的」から「## 止まる条件」まで）のとおり。OUK-10 は0章で止まり、作業を始めていない（平野さんが「桜花」の「結果」をまだ貼っていなかったため）。この指示では、その本文の作業をすべて行う。issue へのコメントの末尾は `Chat-Ref: CHAT-1018-OUK-11` にする。
完了条件

* CHAT-1018-OUK-10 の「完了条件」のとおり（「判断が必要なこと」の書き方、「WRC への引き継ぎ」、確認用の URL）。ログはこの指示のログ（CHAT-1018-OUK-11）に書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OUK-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OUK-11 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1018-OUK-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ab126e4）: https://github.com/retroeater/mj-logs/tree/main/guide/8ab126e4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ab126e4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f5956855.md
