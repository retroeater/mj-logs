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

## 報告

- 状態: 作業中
- ブランチ: work/1010-rev-limit
- ログ: https://github.com/retroeater/mj/blob/work/1010-rev-limit/docs/logs/CHAT-1010-REV-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rev-limit
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
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
