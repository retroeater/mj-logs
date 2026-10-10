# CHAT-1010-REV-06

- 着手日時: 2026-10-10
- 対象issue: #492
- ブランチ: work/1010-rev-limit
- 着手時HEAD: 77cdcd82

## 指示

【Claude作成】Claude Code 向け指示：ガイド文書の容量上限を変える（CLAUDE.md 32KB → 28KB、docs/instruction-template.md に 16KB の上限を新設）。assets-check.yml と CLAUDE.md「更新ルール」を直し、判断待ちで止まる Chat-Ref: CHAT-1010-REV-06 マージ: 判断待ちで止まる（ワークフローの手動実行の結果を見てからマージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rev-limit の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rev-limit を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rev-limit origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-10 の圧縮（CHAT-1010-REV-03・04）で CLAUDE.md が 22,098 バイトになったので、上限を下げて増え戻りを防ぐ。あわせて、チャット側が毎回読む docs/instruction-template.md（12,667 バイト、上限なし）にも上限を置く。
決定（2026-10-10、平野さん）

* CLAUDE.md の上限を 32KB（警告域 30KB）→ 28KB（警告域 26KB）に下げる（#492 の11月中旬の見直しを待たない）
* docs/instruction-template.md に上限 16KB（警告域 14KB）を新設する

前提（チャット側。平野さんの決定ではない）

* 検査は `.github/workflows/assets-check.yml` と `scripts/check_asset_limits.py`（要確認: 上限の数値がどちらにあるか。両方なら両方を直す）。1KB = 1024 バイト
* handover.md（28KB／26KB）と chat-side-operations.md（28KB／26KB）の上限は変えない
* CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の上限の行を新しい数値に直し、「3文書とも整理する」を4文書に合わせて直す。「退避先には上限を置かない」はそのまま（instruction-template.md は退避先ではなく雛形）
* docs/notes/chat-side-operations.md 冒頭の「上限あり（警告26KB／失敗28KB）」は変わらないので触らない。ほかに上限の数値を書いている文書（`grep -rn '32KB\|30KB' docs CLAUDE.md`）があれば同じコミットで直す

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492・#421・#292・#301）、着手中のコメントがあれば止まって報告する。docs/notes/branch-operations.md「ワークフローを変更したとき」を読み、`git log -- .github/workflows/assets-check.yml` で過去の上限の変え方を確かめる
2. 上限の数値を直し（検査・CLAUDE.md・ほかの文書）、`python3 scripts/check_asset_limits.py` を通す。4文書の今のサイズ（`wc -c`）と新しい上限・警告域を表にしてログに書く
3. 作業ブランチで assets-check.yml を手動実行し（`gh workflow run` が使えなければ GitHub MCP か、平野さんに GitHub の画面の「Run workflow」を依頼して止まる）、結果（成功と、4文書の判定の出力）をログに引用する。#492 に、新しい上限と4文書のサイズをコメントする（閉じない）。判断待ちで止まる

止まる条件

* 手順1で同じ論点の進行中の作業（着手中のコメントがある等）が見つかった
* 新しい上限・警告域に今の文書が当たる（直さずに報告）
* ワークフローをマージ前に実行できない（workflow_dispatch が無い、起動が 403 等）。その旨を報告して止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。状態は「判断待ち」
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-REV-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-REV-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1010-REV-06` のコミットなし。`REV` は同じセッションの REV-01〜05 だけ
- 作業ブランチ: リモート・ローカルとも `work/1010-rev-limit` が無いため `git checkout -b work/1010-rev-limit origin/cloudflare`
- 0章: 「指示」欄の最後の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている（貼られた文面では冒頭の行の改行が失われていた。内容は欠けていない）

### 手順1

- issue: #492（open。コメント3件はこのセッションの REV-01〜04 のもの）・#421（closed。最後のコメントは 2026-09-21 の締めくくり）・#292・#301（open、コメント0件）。他セッションの着手中のコメントは無い
- docs/notes/branch-operations.md「ワークフローを変更したとき」を読んだ。assets-check.yml は既存で `workflow_dispatch` を持つため、マージ前に作業ブランチで手動実行できる
- `git log -- .github/workflows/assets-check.yml`: 前回の上限の変更は 950eb352（2026-09-21、chat-side の上限の新設と handover の引き下げ）。ワークフローの `check` の行・コメントと、CLAUDE.md「更新ルール」・chat-side の冒頭を同じコミットで直していた。今回も同じ形にした
- 上限の数値の置き場所: `.github/workflows/assets-check.yml` の「ガイド文書のサイズを確認」の `check` の行だけ。`scripts/check_asset_limits.py` は配信の上限（ファイル数など）だけで、ガイド文書の数値は無い
- `grep -rn '32KB\|30KB\|32768\|30720' docs CLAUDE.md .github scripts`（docs/logs と archive を除く）: CLAUDE.md の上限の行と assets-check.yml の `check` の行だけ（ほかの一致は 232KB の別の話）

### 手順2: 変えたもの（329a8b40）

- `.github/workflows/assets-check.yml`:
  - CLAUDE.md の `check` を `30720 32768` → `26624 28672`
  - `docs/instruction-template.md` の `check` を新設（`14336 16384`）と `TEMPLATE=$(wc -c < docs/instruction-template.md)`
  - **push の `paths` に `docs/instruction-template.md` を足した**（指示に無い変更。`'!docs/**'` で除外されているため、足さないと instruction-template.md だけを変える push で検査が走らず、新設した上限が働かない）。冒頭と検査の前のコメントの「2文書」「3つ」を「3文書」「4つ」に直した
- CLAUDE.md「CLAUDE.md / handover.md の更新ルール」: 上限の行を「CLAUDE.md 28KB（警告域26KB）・…・docs/instruction-template.md 16KB（警告域14KB）」に、「3文書とも整理する」を「4文書とも整理する」に直した。「退避先には上限を置かない」と「この3文書には出典としてのChat-Refを書かない」はそのまま
- chat-side-operations.md の冒頭は変えていない（数値が変わらない）
- `python3 scripts/check_asset_limits.py` OK、`python3 -m unittest discover -s scripts/tests` OK、YAML の読み込み OK

| 文書 | サイズ（`wc -c`） | 警告域 | 上限 | 判定 |
|---|---:|---:|---:|---|
| CLAUDE.md | 22,153 | 26,624 | 28,672 | 外 |
| docs/handover.md | 22,541 | 26,624 | 28,672 | 外 |
| docs/notes/chat-side-operations.md | 26,306 | 26,624 | 28,672 | 外（残り 318） |
| docs/instruction-template.md | 12,625 | 14,336 | 16,384 | 外 |

### 手順3: 手動実行

- GitHub MCP の `actions_run_trigger`（run_workflow、ref `work/1010-rev-limit`）で起動。run 38042668323（workflow_dispatch、329a8b40）は completed / **success**。同じコミットの push の run 38042664973 も success
- 「ガイド文書のサイズを確認」の出力（job 114185861240 のログから引用。警告・エラーの行は無い）:

  ```
  CLAUDE.md: 22153 bytes (警告域 26624 / 上限 28672)
  docs/handover.md: 22541 bytes (警告域 26624 / 上限 28672)
  docs/notes/chat-side-operations.md: 26306 bytes (警告域 26624 / 上限 28672)
  docs/instruction-template.md: 12625 bytes (警告域 14336 / 上限 16384)
  ```
- ジョブのログを curl で取ろうとしたら、ログの置き場所への転送がセッションのプロキシで拒否された（`CONNECT tunnel failed, response 403`）。GitHub MCP の `get_job_logs` で読めた
- #492 にコメント（閉じない）: https://github.com/retroeater/mj/issues/492#issuecomment-6096275634
- 決定（この指示の「決定」2件）を `docs/decisions/operations.md` に足した

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-rev-limit
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev-limit/docs/logs/CHAT-1010-REV-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev-limit
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち。成果物は 329a8b40）
- issue: #492（コメント）
- 判断が必要なこと:
  - マージの可否。手動実行（run 38042668323）は success で、4文書とも新しい警告域の外
  - 指示に無い変更として、assets-check.yml の push の `paths` に `docs/instruction-template.md` を足した（足さないと新設した上限が instruction-template.md だけの push では検査されない）。外すかの判断
  - chat-side-operations.md は 26,306 バイトで警告域（26,624）まで残り 318 バイト（今回は上限を変えていない）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9e1c29eb）: https://github.com/retroeater/mj-logs/tree/main/guide/9e1c29eb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9e1c29eb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
