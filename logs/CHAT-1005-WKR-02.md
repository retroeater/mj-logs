# CHAT-1005-WKR-02

- 着手日時: 2026-10-05
- 対象issue: #504
- ブランチ: work/1005-wkr-02
- 着手時HEAD: 未取得（WKR-01 で HEAD の SHA を読むコマンドが分類器に拒否されたため、取りにいっていない。#493）。origin/cloudflare から作成

## 指示

【Claude作成】Claude Code 向け指示：#504 mj-scheduler の起動の入力 `scheduled` を、真偽値から文字列の "true" に直す（Worker のコード・テスト・文書） Chat-Ref: CHAT-1005-WKR-02 マージ: 承認済み（チャットで）。ただし「止まる条件」のどれかに当たったら、マージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1005-WKR-01 は完了・マージ済み。チャット側がログと mj-logs の actions/status.md で確かめた。同じセッションに貼ってもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1005-wkr-02〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-wkr-02 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1005-wkr-02 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-wkr-02 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: `workers/scheduler/` の Worker のコードとテスト、docs/notes/scheduler-worker.md、docs/decisions/、docs/logs/、#504 へのコメント。ワークフロー・`schedule.json`・`wrangler.jsonc`・サイトのファイルは変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
mj-scheduler が GitHub の起動の API に送る入力を、通ることを確かめてある形（文字列の "true"）にそろえる。最初の定時実行（04:20 JST）が、入力の型で拒まれる心配を先に無くす。
決定（2026-10-05、平野さん）

* この指示（起動の入力を文字列の "true" に直す）を出す。マージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない）

* 識別子 WKR は同じチャットの WKR-01 で使っている。識別子の確認で WKR-01 のコミットやログが見つかっても、同じチャットの指示なので重複ではない
* 今の作り（CHAT-1005-WKR-01 のログの経過「9」と docs/notes/scheduler-worker.md「動き」、mj-logs の guide/64aa604f の版）: Worker は起動の API に inputs `{"scheduled": true}`（真偽値）を送る。実物で確かめる
* 通ることを確かめてあるのは文字列の形だけ: WKR-01 の手動実行 run #14 は、MCP から `scheduled` を文字列の "true" で渡して success になった（同ログの経過「11」）。docs/notes/cloud-sessions.md「gh の代わりに GitHub MCP」も「入力はすべて文字列で渡す」と書いている
* 起動の API（`POST …/actions/workflows/<ファイル名>/dispatches`）が、inputs の値に真偽値を受け付けるかは未確認（チャット側は確かめていない）。公式の文書（REST の説明か OpenAPI の定義。docs.github.com に届かなければ github/docs の原稿）で、inputs の値の型の記述を確かめ、分かったことを報告に書く。記述がどうであっても、文字列の "true" に直す（実績のある形にそろえるため）
* 直すのは Worker が送る値だけ。ワークフロー側の入力 `scheduled`（真偽の型）と判定は変えない（文字列の "true" が真偽の入力として通ることは run #14 で確かめ済み）
* テストは先に直す: 送る inputs が文字列の "true" であることを確かめるテストに直し、直す前のコードでそのテストが落ちることを確かめてから、コードを直す（CLAUDE.md「判断・作業の原則」）
* WKR-01 で行った束ねの確認（scratchpad の esbuild で `src/index.mjs` を束ね、偽の fetch で 04:20 の回を呼ぶ）を、同じ方法でできれば行い、送る中身が `{"scheduled":"true"}` であることを確かめる。できなければ「未確認の項目」に書く（止まらない）
* docs/notes/scheduler-worker.md の「動き」など、送る値を書いている箇所を直す。#506 の本文に送る値の記述があれば（要確認）、それも直す
* マージの後: cloudflare への push で Workers Builds が動く見込み。どの check-run が付くかは、平野さんのダッシュボードの作業の進み具合で変わる（`mj-scheduler` をつなぐ前なら「Workers Builds: mj」だけ。つないだ後なら `mj-scheduler` のものが付く見込みで、名前は未確認。サイトの Worker の Exclude に `workers/*` が入った後なら「Workers Builds: mj」は付かない）。付いた check-run の名前と結論をそのまま書く。付かないことはエラーにしない。待つのは15分まで
* 平野さんは、この指示と並行して Cloudflare のダッシュボードの作業（docs/notes/scheduler-worker.md「マージの後に平野さんが行う作業」）を進めている。その進み具合は確かめなくてよい
* ログは public（mj-logs）。トークンや鍵の値は書かない

手順

1. 今の作り（送っている inputs の形、テストの該当箇所）と、公式の文書の inputs の値の型の記述を確かめる。未マージの `work/` ブランチに `workers/scheduler/` を変えるものが無いことを確かめる
2. テストを直して直す前のコードで落ちることを確かめ、コードを直し、`node --test 'workers/scheduler/test/*.test.mjs'` がすべて通ることを確かめる。文書を直す
3. マージし、付いた check-run を確かめ、#504 に1行コメントする（末尾に Chat-Ref）。docs/decisions/automation.md に決定を足す

止まる条件
どれかに当たったら、マージせず、判断待ちで報告する。

* 今の作りが前提と違う（すでに文字列で送っている、など）。このときは何も変えずに、実物の形を報告に書く
* 未マージの `work/` ブランチに `workers/scheduler/` を変えるものがある
* `node --test` を実行できない、またはテストが通らない
* 直したテストが、直す前のコードでも通る（テストが送る値を見ていない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。次も書く: 公式の文書の inputs の値の型の記述（出典つき）、直す前後の送る中身、マージの後に付いた check-run の名前と結論
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-WKR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-WKR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

1. Chat-Ref の確認: `git log --all --grep="CHAT-1005-WKR-02"` は0件。リモート・ローカルに `work/1005-wkr-02` は無い → `git checkout -b work/1005-wkr-02 origin/cloudflare`。識別子 WKR は同じチャットの WKR-01 で使っている（指示の前提どおり）
2. 手順0: 指示欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
3. 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
4. 今の作り: `workers/scheduler/src/scheduler.mjs` の `dispatchDue` が `JSON.stringify({ ref: config.ref, inputs: { scheduled: true } })` を送る（真偽値）。テストは `scheduler.test.mjs` の「起動: 予定の回に scheduled を真で dispatch する」で `inputs: { scheduled: true }` と比べている。前提どおり
5. 未マージの `work/` ブランチ（`work/1002-cld`・`work/1005-lgr-01`・`work/1005-wkr-02`）で `workers/scheduler` を変えるものは無い（`git diff --name-only origin/cloudflare...<ブランチ> -- workers/scheduler` が空）
6. 公式の文書の inputs の値の型（docs.github.com はプロキシで拒否されるため原稿で読んだ）
   - REST の説明（github/docs `src/rest/data/fpt-2022-11-28/actions.json`、「Create a workflow dispatch event」）: `inputs` は `type: object`、「Input keys and values configured in the workflow file. The maximum number of properties is 25. Any default properties configured in the workflow file will be used when inputs are omitted.」。値の型の記述は無い
   - OpenAPI の定義（github/rest-api-description `descriptions/api.github.com/api.github.com.json`、`POST /repos/{owner}/{repo}/actions/workflows/{workflow_id}/dispatches` の requestBody）: `inputs` は `{"type": "object", "additionalProperties": true, "maxProperties": 25}`。値の型を決めていない
   - 「Manually running a workflow」（`content/actions/how-tos/manage-workflow-runs/manually-run-a-workflow.md`）の REST の節も、`inputs` と `ref` を本文に入れる、とだけ書く
   - 真偽値が拒まれるとも通るとも書いていない → 指示どおり文字列の `"true"` に直す
7. テストを先に直した: 期待を `inputs: { scheduled: 'true' }` にして直す前のコードで実行 → 16件中1件（「起動: 予定の回に scheduled を真で dispatch する」）が落ちた（`+ scheduled: true` / `- scheduled: 'true'`）。テストが送る値を見ていることを確かめた
8. コードを直した（`inputs: { scheduled: 'true' }`、理由のコメント1行）→ `node --test 'workers/scheduler/test/*.test.mjs'` 16件すべて通過
9. 束ねの確認: WKR-01 と同じ scratchpad の esbuild で `src/index.mjs` を束ね、偽の fetch で 04:20 の回を呼んだ → 送った本文は `{"ref":"cloudflare","inputs":{"scheduled":"true"}}`
10. 文書: docs/notes/scheduler-worker.md「動き」の inputs を `{"scheduled": "true"}` にし、文字列で送る理由を足した。#506 の本文は「入力 `scheduled` を真」とだけ書いていて型に触れていない（ワークフローの側では真になる）ので直さない。decisions/automation.md に決定

## 報告

- 状態: 作業中
- ブランチ: work/1005-wkr-02
- ログ: https://github.com/retroeater/mj/blob/work/1005-wkr-02/docs/logs/CHAT-1005-WKR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-wkr-02
- 確認用URL: なし
- マージ: 未
- issue: #504
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8c794c0f）: https://github.com/retroeater/mj-logs/tree/main/guide/8c794c0f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c794c0f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce0b3a1c.md
