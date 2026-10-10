# CHAT-1008-DIC-16

- 着手日時: 2026-10-10
- 対象issue: #515（新規の issue は経過に書く）
- ブランチ: work/1008-dic
- 着手時HEAD: 0498c327

## 指示

【Claude作成】Claude Code 向け指示：iPhone 向けの辞書の形式を別の issue に起票する
Chat-Ref: CHAT-1008-DIC-16
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1008-DIC-15 は完了）
作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
DIC-15 の「判断が必要なこと」（iPhone 向けの辞書の形式を別の issue に起票するか）に平野さんが答えた。忘れないよう issue にしておく。この指示では実装しない。

### 決定（2026-10-10、平野さん）
- iPhone 向けの辞書の形式は、別の issue に起票する

### 前提（チャット側。平野さんの決定ではない）
- 2026-10-08 の「iPhone は採用保留」は変えない。新しい issue は保留中の検討として起票し、着手の時期は決めない
- issue の本文には、2026-10-08 の保留の決定（`docs/decisions/` の該当箇所）とその理由、#515 で作った3形式（Microsoft IME・Google 日本語入力・Gboard）との関係、関係するログ（CHAT-1008-DIC-15・16）を書く。ラベルは #515 に合わせる（「分野: データ」「対象: resource_dictionary」）。その他の書き方は CLAUDE.md の起票の規則のとおり

## 手順
1. 確かめる: CHAT-1008-DIC-15 のログの `## 報告` の状態が「判断待ち」なら、その末尾に ` / 続き: CHAT-1008-DIC-16` を足す。上の決定を `docs/decisions/` に足す。iPhone の辞書を扱う Open の issue が既に無いか確かめる。
2. 起票する: 前提のとおり issue を起票する。#515 に、起票した issue の番号をコメントする。`docs/handover.md` に辞書の行があれば、新しい issue を足す必要があるか確かめ、要るなら直す（追記先の今の内容を読んでから。容量は CLAUDE.md の更新ルールのとおり測る）。
3. 報告する: マージの行のとおり、完了を報告して cloudflare へ入れる。

## 止まる条件
- iPhone の辞書を扱う Open の issue が既にある
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
- マージは冒頭の「マージ:」の行のとおり（変更は docs/logs・docs/decisions・docs/handover.md だけ）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #515
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2df4424f）: https://github.com/retroeater/mj-logs/tree/main/guide/2df4424f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2df4424f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b813da90.md
