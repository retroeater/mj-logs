# CHAT-1021-PRV-07

- 着手日時: 2026-10-21
- 対象issue: #557
- ブランチ: work/1021-prv
- 着手時HEAD: 0b519499b

## 指示

【Claude作成】Claude Code 向け指示：専用フォルダを「制限付き」にした後、全ブックが非公開になり sheets-reader で読めることを確かめる（#557） Chat-Ref: CHAT-1021-PRV-07 マージ: 承認済み（チャットで）。変わるのが check-sheets-reader のスクリプト・テストと docs（docs/decisions/・docs/logs/ を含む）だけで、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: 平野さんが専用フォルダ「【本番環境】」の一般的なアクセスを「制限付き」にした後（0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、`other_book.SPREADSHEET_ID`（R_その他）をログインせずに読めない（gviz・htmlview が 401・403・ログイン画面）ことを確かめる。読めたら何もせず止まる（平野さんの「制限付き」がまだ）。

目的
CHAT-1021-PRV-06（#557）の続き。生成の読み取りは Sheets API（sheets-reader）に切り替わった。平野さんが専用フォルダを「制限付き」にした後、専用フォルダのブックがすべて非公開になり、sheets-reader では読めることを確かめ、記録する。
決定（平野さん）

* 2026-10-21: 専用フォルダ「【本番環境】」の一般的なアクセスを「制限付き」にした（平野さん。貼る時機の前提。0章で確かめる）
* 2026-10-21: この指示の変更（check-sheets-reader の期待値・テスト・docs）を cloudflare へマージしてよい（下の「止まる条件」に当たらないときに限る）

前提（チャット側。平野さんの決定ではない）

* PRV-06 の報告のとおり、`scripts/check_sheets_reader.py` の `FOLDER_PUBLIC` を偽にすると、専用フォルダのブック（R_AIボットを含む）の期待が「ログインなし: 読めない」「sheets-reader: 成功」になる。名簿（`meibo.SPREADSHEET_ID`）は専用フォルダの外で「ログインなし: 読める」「sheets-reader: 成功」が期待。検証用のシートは「ログインなし: 読めない」「sheets-reader: 成功」
* 毎朝 04:00 JST の `run-daily.yml` の初回の確かめは、翌朝にチャット側が行う（この指示では行わない）
* 出すのはタブ名・行数・列数と要求の成否だけ（PRV-04 以来の形）

手順

1. PRV-06 のログ（docs/logs/CHAT-1021-PRV-06.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1021-PRV-07` を足す。#557 に着手中のコメントを残す。`FOLDER_PUBLIC` を偽にし、テストを直して回す。上の決定を docs/decisions/ の PRV の節に足し、docs/notes/sheets-reader.md「この後の段取り」を今の状態（専用フォルダは制限付き・生成は Sheets API・残りは翌朝の定期実行の確かめ）に直す。cloudflare へマージする。
2. マージ後に `check-sheets-reader.yml` を手動実行し、結果をログの `## 経過` に表で全件書く（略さない）。続けて `regenerate-page.yml` を dry_run・all で手動実行し、成否を書く（ログインなしで読めないブックを、生成が sheets-reader で読めることの確かめ）。
3. #557 に結果をコメントする（このログの `## 経過` の表から引用する）。#557 を閉じるかどうかは判断待ちとし、閉じる前に残る作業（翌朝の定期実行の確かめ、Sheets API の 429 の待ちの様子見、元の「Public」フォルダの扱いなど）を「判断が必要なこと」に書く。

止まる条件

* 0章で R_その他をログインせずに読めた
* 手順2で、期待と違う行がある（ブックの題〈定数名〉と、ログインなし・sheets-reader の結果を書いて止まる。共有を変えようとしない）
* 手順2の `regenerate-page.yml` が失敗した（失敗したページと理由を書いて止まる。一時的な接続の失敗は、失敗したジョブだけを1回再実行して成功なら止まらない）
* 差分に、check-sheets-reader のスクリプト・テスト・docs 以外の変更が含まれる
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-PRV-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-PRV-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1021-PRV-07` のコミットは 0 件。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートの `work/1021-prv` は cloudflare と同じ位置（0b519499b、マージ済み）
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1021-prv
- ログ: https://github.com/retroeater/mj/blob/work/1021-prv/docs/logs/CHAT-1021-PRV-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-prv
- 確認用URL: なし
- マージ: 未
- issue: #557
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 80000815）: https://github.com/retroeater/mj-logs/tree/main/guide/80000815

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/80000815/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0b519499.md
