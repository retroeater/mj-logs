# CHAT-1022-PRV-08

- 着手日時: 2026-10-22
- 対象issue: #557・#390
- ブランチ: work/1022-prv
- 着手時HEAD: eef127113

## 指示

【Claude作成】Claude Code 向け指示：非公開化の後の初回の毎朝の運行で「道場部ゲスト」の同期が失敗した原因を調べる（#557・#390） Chat-Ref: CHAT-1022-PRV-08 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1022-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1022-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1021-PRV-07（#557）の続き。専用フォルダを「制限付き」にした後の初回の毎朝の運行（`run-daily.yml`、2026-10-22 04:00 JST、run 39657805637、#15）が failure になった。失敗したのはジョブ「dojo / sync」のステップ「同期」だけ（チャット側が mj-logs の `actions/status.md` と job の API で確かめた）。#426 に「道場部ゲストの取り込みが失敗しました（同期: failure）」の知らせが出ている。原因が、2026-10-21 の Sheets API への切り替え（PRV-06）・非公開化（PRV-07）によるものか、それ以外（連盟サイト・カレンダー・一時的な不調）かを調べる。この指示では直さない。
決定（平野さん）

* 2026-10-21: 元の「Public」フォルダは平野さんが削除した（本番のブックは削除の前に専用フォルダへ移してあり、PRV-07 で全冊を sheets-reader で読めることを確かめた後）

前提（チャット側。平野さんの決定ではない）

* チャット側はジョブのログ本文を読めていない（ログの置き場所へ届かない）。ステップ「同期」の前の「テスト」「Google に認証」は success
* 前日（2026-10-21 12:14 JST）の手動の `run-daily.yml`（#14、書かない道筋）では `sync-dojo-calendar.yml`（apply false）が success だった（PRV-06 のログ）。今朝は予約の起動で、書き込む道筋。所要時間は 24分36秒（前日の予約 #12 は 16分40秒）
* 道場部ゲストの同期は、名簿（`meibo.SPREADSHEET_ID`、専用フォルダの外・リンクを知っている全員）と「R_選手」（専用フォルダ・制限付き）を読み、連盟サイトとカレンダーへ触れる（PRV-01 の表、要確認）
* 疑いの候補（チャット側の見立て。どれかに決めつけない）: (1) Sheets API の 1分あたりの上限（429）で待ち・失敗 (2) 書き込む道筋だけが読む経路に `SHEETS_READER_SA_KEY` が渡っていない (3) 連盟サイトの一時的な不調 (4) カレンダーの書き込みの失敗

手順

1. PRV-07 のログ（docs/logs/CHAT-1021-PRV-07.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1022-PRV-08` を足す。#557 と #390 に着手中のコメントを残す（本文・状態・ラベルは変えない）。
2. run 39657805637 のジョブ「dojo / sync」のログを `gh`（クラウドは GitHub MCP）で読み、失敗の理由（例外・HTTP の状態・最後の出力）を `## 経過` に引用する（値・個人名・メールアドレスは伏せる）。同じ朝の run-daily の他のジョブの所要時間を前日の予約 #12 と比べ、429 の待ちの出力があればその回数を数える。
3. 原因を上の候補のどれか（または別のもの）に分け、根拠を書く。切り替え・非公開化による原因なら、直し方の案（変えるファイル・範囲）をメリット・デメリットと推奨つきで「判断が必要なこと」に書く。一時的なものと判断できるなら、`sync-dojo-calendar.yml` を手動実行（apply は今朝の予約と同じ書き込む形でよい。書き込み済みの月の差分だけが反映される作り〈#426 の知らせのとおり〉、要確認）して成功するかを確かめてよい。#557・#390 に結果をコメントする（このログの `## 報告` から引用する）。

止まる条件

* コード・ワークフロー・シートの共有を変えないと原因を確かめられない（変えずに、確かめ方の案を書いて止まる）
* 手順3の手動実行が、今朝と同じ理由で再び失敗した（2回目は実行しない。結果を書いて止まる）
* 他のセッションの着手中のコメントが #390 にある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1022-PRV-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1022-PRV-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1022-PRV-08` のコミットは 0 件。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートとも `work/1022-prv` が無いため `git checkout -b work/1022-prv origin/cloudflare`
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1022-prv
- ログ: https://github.com/retroeater/mj/blob/work/1022-prv/docs/logs/CHAT-1022-PRV-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1022-prv
- 確認用URL: なし
- マージ: 未
- issue: #557・#390
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 10798599）: https://github.com/retroeater/mj-logs/tree/main/guide/10798599

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/10798599/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eef12711.md
