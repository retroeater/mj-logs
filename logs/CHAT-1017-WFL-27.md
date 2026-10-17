# CHAT-1017-WFL-27

- 着手日時: 2026-10-17
- 対象issue: #564
- ブランチ: work/1017-wfl-morning
- 着手時HEAD: 14ed9b71a

## 指示

【Claude作成】Claude Code 向け指示：morning.md の「予約実行の未実行」で、run-daily で済んだワークフローのフォールバック実行を待たない Chat-Ref: CHAT-1017-WFL-27 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき）。変えるのは `scripts/morning_report.py`（と要れば `scripts/actions_status.py` の呼び出し側）・`scripts/tests/`・docs だけ 貼る時機: CHAT-1017-WFL-26 と並行してよい。ただし JST 03:45〜05:15（run-daily の起動の間）はマージの push をしない 作業ブランチ: クラウドセッションで実行する。work/1017-wfl-morning を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-wfl-morning origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。ログは docs/notes/glossary.md の語で書く。

目的
mj-logs の morning.md「2. 予約実行の未実行」に、毎朝「遅れの可能性: update-live-channel.yml 予定 06:43 の後に…実行なし」が出る。その日の定期実行（run-daily）で連盟ch のジョブが成功していれば、フォールバック実行はゲートで止まるだけなので、待つ意味が無い。情報の無い行が毎朝出ると、本当の遅れを見落とすため、出さないようにする。
決定（2026-10-17、平野さん）

* 振り返りの申送りとして、morning.md の毎朝出る行を今直す（段④〈フォールバック実行を外す〉まで待たない。Fable 5.1 のレビューの推奨。この指示文を貼ることが回答）

前提（チャット側。平野さんの決定ではない）

* 今の判定（WFL-14 の調べ。要確認）: 「2. 予約実行の未実行」は `schedule.json` の行と各ワークフローの `schedule` の cron から予定を出し、予定の後の cloudflare の schedule・workflow_dispatch の実行を探す。フォールバック実行の cron を持つワークフローは update-live-channel（毎日 06:43）・sync-dojo-calendar（毎日 07:12）・cleanup-branches（毎日 07:53）・check-image-links（月曜 03:00）。morning.md の書き出しは 06:43 前後で、毎朝 update-live-channel の行が出ている（10/16・10/17 の morning.md）
* 直し方の案: run-daily から `uses:` で呼ばれるワークフロー（呼ばれる関係は `.github/workflows/run-daily.yml` から機械的に読む。手で表を書かない）について、その日（JST）の cloudflare の `[scheduled]` の run-daily の実行で、そのワークフローを呼ぶジョブ（例 `live / update`）が success なら、そのワークフローの cron の予定は「未実行」に数えない。run-daily のその日の実行が無い・失敗したときは今のまま数える（本当の遅れは今と同じに出す）。ジョブ一覧は `actions_status.py` が引いたもの（`jobs_by_run` など）を使い、API の呼び出しを増やすなら回数をログに書く
* 新しいワークフローの最初の手動実行を、作る前の cron の予定の「遅れて実行」と読む誤検知（10/17 の morning.md の fetch-youtube）は、ワークフローが作られた日時（API の `created_at`）より前の予定を見ないようにして直す（要確認。直すのが大きければ、この指示では扱わずに報告に書く）
* ACF のチャットの CHAT-1016-ACF-24 は `actions_status.py` の `get()` に取り直しを足した（マージ済み）。その後に `actions_status.py`・`morning_report.py` を変えている未マージの work/ ブランチが無いかを確かめる
* テスト: `scripts/tests/test_morning_report.py` に、run-daily のジョブが success なら数えない・run-daily が無い／失敗なら数える・作られる前の予定は見ない、の例を足す

手順

1. 「前提」の要確認を確かめてログに書く。`morning_report.py` を直し、テストを足す。テスト（unittest）が通ること。公開の API の実データで morning.md を作業ブランチで書き出し（mj-logs には push しない）、update-live-channel の行が出ないこと・ほかの行が変わらないことを、直す前の出力との差でログに書く
2. docs（morning.md を説明している docs/notes の箇所）に 1 行足す。上の「決定」を `docs/decisions/automation.md` に `## 2026-10-17（CHAT-1017-WFL-27）` として足す。cloudflare へマージする（push 直前に再 fetch と祖先の確認）。マージの後の mj-logs の同期が success で、次の morning.md の書き出しの日時を「未確認の項目」に書く

止まる条件

* 未マージの work/ ブランチが `scripts/actions_status.py`・`scripts/morning_report.py` を変えている、または取り込みで衝突する
* run-daily から呼ばれる関係を `run-daily.yml` から機械的に読めない（手で表を書く必要がある）: 判断待ち
* テストが失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 直す前と後の出力の差に、update-live-channel 以外のワークフローの行の増減が出た（意図した直しで説明できるもの〈sync-dojo-calendar・cleanup-branches の同じ型の行など〉は止まらずにログに書く）
* マージの後の mj-logs の同期が failure（戻さずに原因を書いて判断待ち。報告の先頭に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（用語集の語で書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WFL-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WFL-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 14ed9b71）: https://github.com/retroeater/mj-logs/tree/main/guide/14ed9b71

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
