# CHAT-1013-BLD-03

- 着手日時: 2026-10-13
- 対象issue: #561
- ブランチ: work/1013-bld
- 着手時HEAD: 9c18087e0

## 指示

【Claude作成】Claude Code 向け指示：本番ビルド（Workers Builds）の失敗を失敗通知メールで知らせるワークフローの実装（#561）
Chat-Ref: CHAT-1013-BLD-03
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1013-bld〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-bld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1013-bld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-bld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#561 を読み、他セッションの着手中コメントがあれば止まる。着手中コメントを #561 に書く。

## 目的
本番（cloudflare）向けの Workers Builds が失敗したら、GitHub Actions のワークフローを失敗させて失敗通知メールで知らせる（#561）。この指示ではワークフローと判定スクリプトを作業ブランチに入れ、スクリプトの検証まで行う。ワークフローの実行による確かめは、マージ後に cloudflare で行う（次の指示）。

### 決定（2026-10-13、平野さん）
- 方式は A: `check_run` の completed で起動し、該当すればジョブを失敗させて失敗通知メールで知らせる（常設 issue は作らない）
- 本番かどうかは「失敗した check-run の `head_sha` が `cloudflare` に入っているか」で近似する（同じコミットのプレビューだけが失敗したときに余計な通知が出るのは受け入れる）
- 対象は `Workers Builds: mj` に加えて `mj-scheduler`・`mj-ask`（名前が `Workers Builds:` で始まるもの）

### 前提（チャット側。平野さんの決定ではない）
- 事実の出所は #561 の本文と CHAT-1013-BLD-02 のログの `## 経過`「手順2」: 失敗した check-run は本番・プレビューとも `output.summary` が同じで見分けられない／`check_run` は既定ブランチのファイルだけが対象で、Actions の check-run では起動しない／`check_run` には `branches` の絞り込みが無く、ジョブの `if:` で絞る／起動は 1 日約 100 回で、ジョブが skipped ならランナーを使わない
- 新しいワークフローはマージ前に `gh workflow run --ref 作業ブランチ` が 404 になる（docs/notes/branch-operations.md「ワークフローを変更したとき」）。この指示では試さず、報告に「マージ前は実行で確かめられない」と書く
- 設計の案（実物に合わせて変えてよい）:
  - `.github/workflows/notify-build-failure.yml`（name は「本番ビルドの失敗の通知」）。`on: check_run: types: [completed]` と `workflow_dispatch`（入力 `check_run_id`: 判定する check-run の ID、`dry_run`: 既定 true）。`permissions` は `checks: read`・`contents: read`。ジョブの `if:` で `github.event_name == 'workflow_dispatch' || (startsWith(github.event.check_run.name, 'Workers Builds:') && github.event.check_run.conclusion == 'failure')` に絞る（それ以外は skipped）。`concurrency` は不要
  - 判定は `scripts/check_build_failure.py`（標準ライブラリのみ。`check_run` の JSON と、`repos/{owner}/{repo}/compare/cloudflare...<head_sha>` の `status` を受け取り、`identical`・`behind` なら「本番に入っているコミット」と判定）。ワークフローは `check_run` イベントでは `github.event.check_run` を、`workflow_dispatch` では `repos/.../check-runs/<id>` を `gh api` で取ってスクリプトに渡す。該当なら Build ID・Worker 名・コミット・ダッシュボードのリンク（check-run の `output.summary` の `Build ID` の行）をジョブのサマリに出し、`dry_run` でなければジョブを失敗させる。該当しなければ成功で終わる
  - テストは `scripts/tests/test_check_build_failure.py`。実物の check-run の JSON を固定値に使う: 本番の失敗（5cbc83eb の `Workers Builds: mj`）・プレビューの成功（86979e3d の `Preview URL` あり）・10/12 のプレビューの失敗（19b99cc9）・`mj-scheduler` の失敗（86979e3d）。compare の `status` は `identical`・`behind`・`ahead`・`diverged` の 4 通り
  - 文書: docs/notes/static-generation.md「ワークフローの一覧」に行を足す。docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき（2026-10-13）」は #561「あわせて直すこと」の 2 点（`Preview URL` で見分けられるのは成功したビルドだけ／10/12 の f09571e4 は check-run で約 31 分の failure を確かめたが本番かは決まらない）を直し、通知の仕組みができたことを足す（「ビルド失敗の通知は無い」の記述は置き換える）。skipped の実行が Actions の一覧と mj-logs の `actions/status.md` に残ることも書く

## 手順
1. docs/notes/branch-operations.md「ワークフローを変更したとき」を読み、`git log -- .github/workflows/regenerate-saikyo.yml` と CHAT-0918-HT-06 のログ（残っていれば）で初回だけ違う手順が無いか確かめる。`scripts/check_build_failure.py` とテストを書き、`python3 -m unittest discover -s scripts/tests` を通す。実物の check-run の JSON を `gh api`（無ければ REST）で取って固定値にする
2. ワークフローと文書を書く。ワークフローは `actionlint` があれば通し、無ければ YAML として読めることを確かめる。`.assetsignore` で `scripts/` が公開対象外であることを確かめ、新しいファイルが公開対象に増えないことを `python3 scripts/…`（assets-check.yml と同じ判定）か `git ls-files` で確かめる
3. ログの `## 報告` に、判定の条件（ジョブの `if:` とスクリプトの判定）・テストの結果・マージ前に確かめられないこと（ワークフローの起動と失敗メール）・マージ後の確かめの手順案（cloudflare で `workflow_dispatch` を 5cbc83eb の失敗の check-run の ID で dry_run → dry_run を外して 1 回失敗させ、メールが届くことを平野さんが見る）を書いて止まる

## 止まる条件
- #561 に他セッションの着手中コメントがある
- 未マージの work/ ブランチが `.github/workflows/` に check_run のワークフローを足している、または docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」の節を変えている（別の節の変更では止まらず、ログに書く）
- テストが通らない。`check_run` の payload に前提の項目（`name`・`conclusion`・`head_sha`・`output.summary`）が無い
- 公開対象（assets-check.yml の `allowed`）に新しい項目が要る

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-BLD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-BLD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1013-BLD-03` のコミット・ログなし
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。#561 のコメントは0件（他セッションの着手中コメントなし）
- ブランチ: ローカルの `work/1013-bld` は `origin/cloudflare` の祖先（リモートもマージ済み）→ `git merge --ff-only origin/cloudflare`（9c18087e0）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順・作業ブランチが揃っている
- #561 に着手中のコメントを書いた（https://github.com/retroeater/mj/issues/561#issuecomment-6145383033 ）
- 重なり: 未マージの `work/` ブランチで `.github/workflows/` か docs/notes/cloudflare.md を変えているものは無い

### 手順1: 初回だけの手順・スクリプト・テスト

- docs/notes/branch-operations.md「ワークフローを変更したとき」: 新規のワークフローはマージ前に `--ref <作業ブランチ>` で起動できない（404）。
  「副作用の無い状態（dry-run 既定、schedule も dry-run で走る等）でマージし、cloudflare で手動実行して確認してから有効化する（CHAT-0918-HT-06/08）」。
  `regenerate-saikyo.yml`（`workflow_run`）の履歴は2コミットで、初回だけ違う手順の記述は無い。CHAT-0918-HT-06 のログは削除済み（同じ規則が上の節に残る）
- これに合わせ、既存の `check-saikyo-unregistered.yml` の `SCHEDULE_ENABLED` と同じ形で **`NOTIFY_ENABLED: 'false'`** を置き、check_run の起動も dry-run で走らせる（指示の設計の案に無い追加。失敗の副作用は通知メールだけだが、規則に従った）
- `scripts/check_build_failure.py`（標準ライブラリのみ）: `judge(check_run, compare_status)` が「名前が `Workers Builds:` で始まる」かつ「conclusion が failure」かつ「compare の status が identical・behind」のとき知らせる。知らせるときは dry-run でなければ終了コード 1。ジョブのサマリ（`GITHUB_STEP_SUMMARY`）に check-run の名前と ID・結論・コミットと compare の status・`output.summary` の `Build ID` の行（ダッシュボードのリンク）を書く
- `scripts/tests/test_check_build_failure.py`: 実物の check-run を REST（`check-runs/<id>`）で取り、判定に使う項目だけを固定値にした（アカウント ID とプレビューのサブドメインは伏せた）。本番の失敗 5cbc83eb（id 115128041224）・プレビューの成功 86979e3d（115142932713、`Preview URL` あり）・10/12 の失敗 19b99cc9（114826373160）・`mj-scheduler` の失敗 86979e3d（115146087345）と、Actions の check-run（`check`）。compare の status は identical・behind・ahead・diverged の4通り。ほかに cancelled・timed_out など、終了コードとサマリの書き出し
- 結果: `python3 -m unittest discover -s scripts/tests` で 1006 件 OK（新規 11 件）
- 修正前のコードでも通らないかの確かめ: 判定を壊した版（status に ahead を足す／名前と結論の条件を or にする）を作業用の場所にコピーして同じテストを流し、それぞれ 4 件・8 件が失敗した
- payload の項目: webhook の `check_run.completed` のスキーマ（BLD-02 で確認）に `name`・`conclusion`・`head_sha`・`output.summary` がある（止まる条件に当たらない）

### 手順2: ワークフロー・文書

- `.github/workflows/notify-build-failure.yml`（name「本番ビルドの失敗の通知」）: `on: check_run: types: [completed]` と `workflow_dispatch`（入力 `check_run_id`〈文字列、数字だけ受け付ける〉・`dry_run`〈既定 true〉）。`permissions` は `checks: read`・`contents: read`。ジョブの `if:` は指示の案のとおり。手順は dry-run の決定 → チェックアウト（`scripts` だけ）→ check-run を取る（check_run は `toJSON(github.event.check_run)`、手動は `gh api repos/$REPO/check-runs/<id>`）→ `gh api repos/$REPO/compare/cloudflare...<head_sha> --jq .status` → スクリプト。compare が失敗したらジョブが失敗する（取りこぼすより通知を選んだ）。`concurrency` は置かない。Python のセットアップは置かない（ランナーの python3 で足りる）
- actionlint: セッションに無かったため、作業用の venv に `actionlint-py`（1.7.12）を入れて流し、指摘なし。shellcheck が無いため `run:` の中のシェルの検査は行われていない。YAML としても読めることを確かめた
- 手元での模擬: 上の3つの check-run を REST で取り、compare の status（3つとも今は behind）を REST で取ってスクリプトに渡した。本番の失敗と mj-scheduler の失敗は dry-run で 0・dry-run なしで 1、プレビューの成功は 0
- 公開対象: `.assetsignore` に `scripts` と `.github` がある。新しいファイルはその下だけで、最上位の項目は増えない（assets-check.yml の `allowed` の変更は要らない）
- 文書: docs/notes/static-generation.md「ワークフローの一覧」に `notify-build-failure.yml` の行（未有効化であること）を足した。docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」は、`Preview URL` で見分けられるのは成功したビルドだけ（`check_suite.head_branch` も使えない）を足し、「失敗を知らせる通知は無い」を通知の仕組み・skipped が一覧と `actions/status.md` に残ること・有効化の状態の参照に置き換え、10/12 の f09571e4 の申告値の行を check-run で確かめた事実に直した（ほかの 10/12 の3件の failure も添えた）
- 決定: docs/decisions/cloudflare.md に「2026-10-13（CHAT-1013-BLD-03）」として指示の決定3点を足した
- 作業ブランチの push（コードを含む）で Workers Builds のプレビューのビルドが走る。サイトのファイルは変えていないので確認は不要

## 報告

- 状態: 判断待ち
- ブランチ: work/1013-bld
- ログ: https://github.com/retroeater/mj/blob/work/1013-bld/docs/logs/CHAT-1013-BLD-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-bld
- 確認用URL: なし（サイトのファイルは変えていない）
- マージ: 未（平野さんの判断待ち）
- issue: #561（着手中のコメント）
- 判断が必要なこと:
  - cloudflare へのマージの可否。入るもの: `.github/workflows/notify-build-failure.yml`・`scripts/check_build_failure.py`・`scripts/tests/test_check_build_failure.py`・docs/notes/cloudflare.md・docs/notes/static-generation.md・docs/decisions/cloudflare.md・本ログ
  - 判定の条件: ジョブの `if:` は `github.event_name == 'workflow_dispatch' || (startsWith(github.event.check_run.name, 'Workers Builds:') && github.event.check_run.conclusion == 'failure')`。スクリプトは名前が `Workers Builds:` で始まり、conclusion が failure、`compare/cloudflare...<head_sha>` の status が identical・behind のとき知らせる（dry-run でなければジョブを失敗させる）
  - 指示の設計に足したこと: `NOTIFY_ENABLED: 'false'`（新規ワークフローの規則。マージした時点では check_run の起動も dry-run で、通知は出ない）。有効化は `'true'` にする1行のコミットで行う
  - テストの結果: 1006 件 OK（新規 11 件）。判定を壊した版では 4 件・8 件が失敗
  - マージ前に確かめられないこと: ワークフローの起動（`check_run`・`workflow_dispatch` とも既定ブランチにしか無い）、ジョブの失敗で失敗通知メールが届くこと、`GITHUB_TOKEN` で push したコミット（`chore: regenerate`）のビルドで起動するか（#561 の調査の 3）
  - マージ後の確かめの手順案: (1) cloudflare で `workflow_dispatch` を check_run_id `115128041224`（5cbc83eb の本番の失敗）・dry_run true で起動し、サマリに「本番に入っているコミット…」と Build ID の行が出て成功で終わることを見る (2) 同じ ID で dry_run false で起動し、ジョブが失敗して失敗通知メールが届くことを平野さんが見る (3) プレビューの成功の ID `115142932713` で起動し「知らせない」で成功することを見る (4) `NOTIFY_ENABLED` を `'true'` にする（差分はその1行）。その後、次の Workers Builds の完了でジョブが skipped の実行が付くことを Actions の一覧で見る
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9c18087e）: https://github.com/retroeater/mj-logs/tree/main/guide/9c18087e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9c18087e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
