# CHAT-1010-REV-08

- 着手日時: 2026-10-10
- 対象issue: #492
- ブランチ: work/1010-rev-limit
- 着手時HEAD: 17e8cf89

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1010-REV-06 の上限の変更（work/1010-rev-limit）を cloudflare へマージする
Chat-Ref: CHAT-1010-REV-08
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rev-limit の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1010-rev-limit を続けて使う（CHAT-1010-REV-06 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1010-REV-06（判断待ち）の assets-check.yml と CLAUDE.md の変更を、そのまま cloudflare へ入れる。

### 決定（2026-10-10、平野さん）
- REV-06 の変更を、Code が足した「検査の起動条件（push の `paths`）に docs/instruction-template.md を足す」変更を含めてマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 成果物は REV-06 のログの報告にある 329a8b40 まで（要確認: それ以降が REV-06 のログの追いの push だけであること。成果物の追加のコミットがあれば止まる）
- 取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

## 手順
1. 前提を確かめ、必要なら origin/cloudflare を取り込む。取り込んだら `python3 scripts/check_asset_limits.py` を通し、4文書のサイズが新しい警告域の外であることを確かめる
2. cloudflare へマージする（CLAUDE.md「ブランチ運用」のマージの手順）。CLAUDE.md を含むため Workers Builds が1回走る見込み（表示は変わらない）。マージ後の assets-check の check-run が success であることを確かめる（待つのは上限15分。超えたら「未確認の項目」に回して進む）
3. CHAT-1010-REV-06 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1010-REV-08` を足す（マージに含めてよい）。#492 にマージの結果（SHA と check-run の結果）をコメントする（閉じない）。作業ブランチの片付けは CLAUDE.md「ブランチ運用」のとおり（クラウドセッションで消せなければ delete-merged-branches.yml に任せる）

## 止まる条件
- work/1010-rev-limit に REV-06 の成果物より後の成果物のコミットがある
- 取り込みで、両立しない衝突が出た
- 取り込み後に4文書のどれかが新しい警告域に当たる（マージせず報告）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-REV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-REV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1010-REV-08` のコミットなし。`REV` は同じセッションの REV-01〜07 だけ
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: ローカル・リモートとも work/1010-rev-limit（17e8cf89）。`git checkout work/1010-rev-limit`
- 前提: `git log 329a8b40..origin/work/1010-rev-limit` は 17e8cf89（docs: finish log and record decisions for CHAT-1010-REV-06）の1件だけで、変更は `docs/logs/CHAT-1010-REV-06.md` と `docs/decisions/operations.md`（REV-06 の決定の記録）。成果物の追加のコミットは無い

### 取り込み

- `git merge origin/cloudflare` で `docs/decisions/operations.md` が衝突した。両側とも同じ日付の節を末尾に足しただけ（cloudflare 側「2026-10-10（CHAT-1010-MCK-02）」、こちら「2026-10-10（CHAT-1010-REV-06）」）で両立するため、両方を残して解いた（マージコミット 89c59f08）。解いた後の該当箇所:

  > ## 2026-10-10（CHAT-1010-MCK-02）
  >
  > - #230: 固定クエリ5本は…（cloudflare 側の6行をそのまま）
  >
  > ## 2026-10-10（CHAT-1010-REV-06）
  >
  > - CLAUDE.md の上限を 32KB（警告域 30KB）から 28KB（警告域 26KB）に下げる。#492 の11月中旬の見直しを待たない（未マージ）
  > - docs/instruction-template.md に上限 16KB（警告域 14KB）を新設する（未マージ）
- ほかの衝突は無い
- `python3 scripts/check_asset_limits.py` OK。assets-check.yml の「ガイド文書のサイズを確認」と同じシェルを手元で実行（rc=0、警告なし）:

  ```
  CLAUDE.md: 22153 bytes (警告域 26624 / 上限 28672)
  docs/handover.md: 22604 bytes (警告域 26624 / 上限 28672)
  docs/notes/chat-side-operations.md: 26306 bytes (警告域 26624 / 上限 28672)
  docs/instruction-template.md: 12625 bytes (警告域 14336 / 上限 16384)
  ```
- CHAT-1010-REV-06 のログの状態の末尾に ` / 続き: CHAT-1010-REV-08` を足した（マージに含める）

## 報告

- 状態: 作業中
- ブランチ: work/1010-rev-limit
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev-limit/docs/logs/CHAT-1010-REV-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev-limit
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4e7e9361）: https://github.com/retroeater/mj-logs/tree/main/guide/4e7e9361

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4e7e9361/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
