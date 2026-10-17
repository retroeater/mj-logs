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

## 経過

- Chat-Ref の重複確認: CHAT-1017-WFL-27 のコミット・ログは無い。`WFL` はこのセッションの識別子
- 作業ブランチ: work/1017-wfl-morning はローカル・リモートとも無かったので `git checkout -b work/1017-wfl-morning origin/cloudflare`（14ed9b71a）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- 着手 JST 12:25（土曜）

### 手順1: 要確認の確かめ

- 未マージの work/ ブランチで `scripts/actions_status.py`・`scripts/morning_report.py` を変えるもの: なし（ACF-24 の `get()` の取り直しは cloudflare にある）
- 今の判定は前提どおり: `last_due()` が `schedule.json` の行と cron から now 以前で最も新しい予定を出し、予定の後に作られた cloudflare の schedule・workflow_dispatch の実行を探す。無ければ予定から 3 時間以内は「遅れの可能性」、超えれば「未実行」。あれば最初の1件の開始が予定から 3 時間を超えたとき「遅れて実行」
- 呼ばれる関係は run-daily.yml から機械的に読める: WFL-24 の `actions_dashboard.parse_workflow()`（テスト済み）でジョブの `uses:` を読む。今は update-live-channel ← `live`、update-sns-book ← `sns`、check-image-links ← `images`、regenerate-saikyo ← `saikyo`、sync-dojo-calendar ← `dojo`、cleanup-branches ← `branches`。止まる条件に当たらない
- API の workflow の `created_at` は実行の時刻と形が違う（`2026-09-07T21:08:50.000+09:00`）。最初の比較で `actions_status.parse_time` が読めずに落ちたので、`datetime.fromisoformat` で読み、読めなければ None（見ない）にした。小さい直しなので、この指示で扱った

### 手順1: 実装（9471ca2b8）

- `scripts/morning_report.py`:
  - `daily_callers(rows)`: Worker の表（schedule.json）のワークフローのファイルを読み、`uses:` のジョブから {呼ばれる側: [(呼ぶ側, ジョブ名)]} を作る（手で表を持たない）
  - `covered_by_daily()`: 予定の JST の日の cloudflare の定期実行（題が `[scheduled]` で始まる）で、呼ぶジョブ（`live` とその下の `live / update`・`live / regenerate / regenerate` など）をまとめた結論が success なら真。ジョブが1つでも失敗なら偽（今のまま数える）。その日の定期実行が無い・手動実行だけ・ジョブを引けていないときも偽
  - 「2.」の判定: 予定がワークフローの `created_at` より前なら見ない。`covered_by_daily()` が真なら見ない（「遅れて実行」も出さない。フォールバック実行はゲートで止まるだけなので）
  - `write()` に `fetch_jobs` を足し、Worker の表のワークフロー（今は run-daily）の今日・昨日の定期実行のジョブを引く
- `scripts/actions_status.py`: ジョブの取得を `fetch_jobs()` にまとめ、引いた分を `jobs_by_run` に貯めて morning.md と運行表で共有する（同じ実行のジョブを二度引かない）
- **API の呼び出し:** morning.md は同期 1 回につき最大 2 回（run-daily の今日・昨日の定期実行）。運行表が run-daily の直近 5 回のジョブを引くので、共有した分だけ運行表の回数が減り、合計は増えない（実データで運行表の取得が 20 回 → 18 回、morning.md が 2 回）
- テスト `scripts/tests/test_morning_report.py` に 10 件: run-daily のジョブが success なら数えない・ジョブが失敗なら数える・定期実行が無い／手動実行（題に `[scheduled]` が無い）／前日の定期実行／ジョブを引けていない なら数える・run-daily.yml から呼ぶ関係が読める・write が今日の定期実行のジョブを引く・作られる前の予定は見ない・`created_at` の 2 つの形を読める。直す前の morning_report.py では 10 件とも落ち（9 件は新しい引数・関数が無いためのエラー、作られる前の予定の 1 件は判定の違いで失敗）、直した後は unittest 全件 OK
- docs/notes/github-actions.md「run-daily.yml」の節の morning.md の項の次に1行足した

### 手順1: 直す前と後の出力の差（公開の API の実データ、同じデータを両方の版に渡した）

- 今の時刻（JST 12:27）: 違いは「2.」の1行だけ。直す前は「遅れて実行: 2026-10-16 11:40 fetch-youtube.yml …予定 2026-10-12 05:37 → 開始 2026-10-16 11:40（＋102:03）」、直した後は「なし」（fetch-youtube は 10/16 に作られ、10/12 の予定はそれより前）
- 10/17 06:45 JST の模擬（その時刻より後に作られた実行を除いて渡した）: 違いは「2.」の2行だけ。直す前は上の fetch-youtube の行と「遅れの可能性: update-live-channel.yml 予定 2026-10-17 06:43 の後に schedule・workflow_dispatch（cloudflare）の実行なし」、直した後は「なし」（10/17 04:00 の run-daily #4 の `live / update` が success）
- ほかのワークフローの行の増減は無い（止まる条件に当たらない）
- 作業ブランチで `actions_status.py` を通しで実行（mj-logs には push しない）: status.md・morning.md・index.html を書き出し、morning.md の「2.」は「なし」

### 手順2: docs・決定

- docs/decisions/automation.md に `## 2026-10-17（CHAT-1017-WFL-27）` を足した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 16fd668c）: https://github.com/retroeater/mj-logs/tree/main/guide/16fd668c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/16fd668c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
