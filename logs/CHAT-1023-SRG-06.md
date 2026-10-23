# CHAT-1023-SRG-06

- 着手日時: 2026-10-23
- 対象issue: #624
- ブランチ: work/1023-srg
- 着手時HEAD: c56c1a784

## 指示

【Claude作成】Claude Code 向け指示：「R_Ampai」の廃止を確かめて #624 を閉じる Chat-Ref: CHAT-1023-SRG-06 マージ: 承認済み（チャットで）。変わるのが docs（docs/decisions/・docs/logs/ を含む）だけのときに限る 貼る時機: 平野さんが「R_Ampai」をゴミ箱からも完全に削除した後（0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1023-srg を続けて使う（SRG-05 の中断のログが載っているため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ origin/cloudflare から作り、SRG-05 のログが無ければ「続き」の追記は SRG-04 のログだけにする（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで、表・追記の続く文書に両方の変更が両立する衝突（追記どうし・隣り合う行）が出たら、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、廃止した「R_Ampai」（SRG-04 の前の `ampai.SPREADSHEET_ID` の値。git の履歴から引く）を sheets-reader で読めない（404・403）ことを、手動実行のワークフロー（`check-sheets-reader.yml` の作業ブランチの ref など、SRG-04 と同じ形。値は出さない）で確かめる。読めたら何もせず止まる（平野さんの廃止がまだ）。

目的
CHAT-1023-SRG-04（#624）の続き。CHAT-1023-SRG-05 は0章で「R_Ampai」をまだ読めたため中断した（ゴミ箱に入っていただけの見込み）。平野さんがゴミ箱からも完全に削除した後の再開。読む先を「R_タイトル戦_マスタ」の「Ampai」シートに切り替えた後、平野さんが「R_Ampai」をブックごと廃止した。廃止の後も読み手が動くことを確かめ、#624 を閉じる。
決定（2026-10-23、平野さん）

* 「R_Ampai」をブックごと廃止した。SRG-05 の中断の後、ゴミ箱からも完全に削除した（貼る時機の前提。0章で確かめる）

前提（チャット側。平野さんの決定ではない）

* SRG-04 で、読み手（jpml_pros・houou_pages・check_ampai）の読む先は切り替え済みで、`check_sheets_reader.py` の対象からも外してある（SRG-04 のログ）
* コード・ワークフロー・docs に旧ブックの ID・「R_Ampai」「ryoei.pro_Ampai」が残っていないかは、手順1で grep する（docs/decisions/・docs/logs/ の過去の記録は書き換えない）

手順

1. SRG-04 のログ（docs/logs/CHAT-1023-SRG-04.md）と SRG-05 のログ（docs/logs/CHAT-1023-SRG-05.md、中断。作業ブランチにだけある見込み）の `## 報告` の状態の末尾に `/ 続き: CHAT-1023-SRG-06` を足す。#624 に着手中のコメントを残す。旧ブックの ID と「R_Ampai」「ryoei.pro_Ampai」を、docs/decisions/・docs/logs/ を除く全体で grep し、残りをログに書く（今の状態を書く文に残っていれば直す。コードに残っていれば止まる）。
2. `check-meibo.yml` を dry_run で手動実行し、ampai・meibo の両ジョブの成否を書く。上の決定を docs/decisions/ に記録する。
3. #624 に結果（このログの `## 報告` から引用する）を書いて閉じる。cloudflare へマージする。

止まる条件

* 0章で旧ブックを読めた
* 手順1で、コード・ワークフローに旧ブックの ID が残っている
* 手順2の `check-meibo.yml` が失敗した（一時的な接続の失敗は、失敗したジョブだけを1回再実行して成功なら止まらない）
* 差分に docs 以外の変更が含まれる
* 未マージの work/ ブランチが、変える文書の同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SRG-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SRG-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1023-SRG-06` のコミットは 0 件。`SRG` は SRG-05 の続き
- 作業ブランチ: ローカル・リモートの `work/1023-srg` は c56c1a784（SRG-05 の中断のログを含む、未マージ）。origin/cloudflare（6040c7fca）は祖先でないので、このログの push の後に merge で取り込む

## 報告

- 状態: 作業中
- ブランチ: work/1023-srg
- ログ: https://github.com/retroeater/mj/blob/work/1023-srg/docs/logs/CHAT-1023-SRG-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-srg
- 確認用URL: なし
- マージ: 未
- issue: #624
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 94c95e8b）: https://github.com/retroeater/mj-logs/tree/main/guide/94c95e8b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/94c95e8b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bb98660e.md
