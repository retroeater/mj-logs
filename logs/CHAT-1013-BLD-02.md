# CHAT-1013-BLD-02

- 着手日時: 2026-10-13
- 対象issue: なし（起票する）
- ブランチ: work/1013-bld
- 着手時HEAD: 287861d4d

## 指示

【Claude作成】Claude Code 向け指示：本番ビルド（Workers Builds）の失敗を知らせる仕組みの起票と、check_run 方式の実現性の確認
Chat-Ref: CHAT-1013-BLD-02
マージ: ドキュメントのみ（docs/logs・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（issue の起票を含む。ワークフローは変えない）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1013-bld〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-bld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1013-bld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-bld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
本番（cloudflare）向けの Workers Builds が失敗しても誰にも届かない（2026-10-12・10-13 に起き、気づいたのは別の作業の途中だった）。失敗を知らせる仕組みを入れることが決まったので、issue を起票し、第一候補（GitHub Actions の `check_run` イベント）の実現性を調べて issue に書く。この指示ではワークフローを作らない。

### 決定（2026-10-13、平野さん）
- 本番ビルドの失敗を知らせる仕組みを入れる（方式の選定は調査の後）

### 前提（チャット側。平野さんの決定ではない）
- 同じ論点の issue が無いことは CHAT-1013-BLD-01 の `## 経過`「手順1」で確かめ済み（近いのは #118〈closed〉・#504・#291・#170）
- リポジトリの既定ブランチは `cloudflare`（チャット側が REST で確認）。`check_run`・`workflow_run` で動くワークフローは既定ブランチのファイルだけが対象（`regenerate-saikyo.yml` 冒頭のコメントと同じ）
- 事実の出所: 失敗の経緯と見分け方は BLD-01 のログの `## 経過`「手順2」「手順3」と、docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき（2026-10-13）」。issue にはそこから引用し、チャットの要約は書かない
- 方式の候補（issue の本文に併記する。選ぶのは平野さん）:
  - A. `on: check_run: types: [completed]` のワークフロー。`check_run.name` が `Workers Builds: mj`、`conclusion` が `failure`、`output.summary` に `Preview URL` が無い（＝本番）ときだけジョブを失敗させ、GitHub の失敗通知メールで知らせる（生成系と同じ「通知は失敗そのもの」の流儀。docs/notes/static-generation.md）。それ以外は何もしない
  - B. A と同じ条件で常設 issue（種類: 常設）にコメントする
  - C. Cloudflare の Workers Builds イベント購読（`cf.workersBuilds.worker.build.failed`）。Queues 経由で webhook などに送る仕組み。Queues の要るプランと料金は未確認（要確認）。A か B で足りるなら採らない
- A・B の要確認の点（手順2で調べる）: `check_run` の payload で本番とプレビューを見分けられる項目（`output.summary`・`check_suite.head_branch`・`details_url` など）／`check_run` は Actions 自身の check-run や mj-ask・mj-scheduler の分でも起動するので、1日あたりの起動回数と Actions の使用分（#298）への影響／ジョブの `if:` で絞ったときに「skipped の実行」が Actions の一覧と mj-logs の `actions/status.md` に残る量

## 手順
1. `gh issue list --state all --search "<語>"`（`gh` が無ければ REST で `repos/retroeater/mj/issues?state=all`）で「Workers Builds」「ビルド 失敗」「通知」を確かめ直し、BLD-01 の後に同じ論点の issue が増えていれば止まって報告する。未マージの work/ ブランチに `.github/workflows/` へ check_run のワークフローを足すコミットがあれば同じく止まる
2. 方式 A の実現性を調べ、結果をログの `## 経過` に書く:
   - 直近3日の cloudflare と work/ ブランチのコミットの check-runs（`repos/retroeater/mj/commits/<sha>/check-runs?filter=all`）から、1日あたりの completed の check-run の数を Worker・Actions 別に数える（= A のワークフローの起動回数の見込み）
   - 本番とプレビューを見分けられる payload の項目を GitHub の公式ドキュメント（`check_run` イベントの webhook payload）で確かめ、項目名を書く。既存の check-run の JSON で `output.summary` の `Preview URL` の有無と `check_suite.head_branch` を照合する
   - `check_run` で動くワークフローの制約（既定ブランチ、起動の条件、`GITHUB_TOKEN` で作られたイベントの扱い）を公式ドキュメントで確かめ、URL と該当の文を書く
3. issue を起票する。題は「本番の Workers Builds の失敗を知らせる」。ラベルは `分野: 自動化`・`分野: インフラ`。本文は「目的」「経緯（BLD-01 のログと cloudflare.md の該当節からの引用と、そこへのリンク）」「候補 A・B・C と、それぞれの要確認の点」「手順2の結果」「平野さんの判断が要ること（A か B か、mj-scheduler・mj-ask の失敗も対象にするか）」の順。本文の末尾に `Chat-Ref:` の行。ワークフローの実装は含めない

## 止まる条件
- 手順1で同じ論点の issue か、check_run のワークフローを足す未マージの work/ ブランチが見つかった
- 前提の「既定ブランチは cloudflare」が実物と食い違う
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（issue の番号を「issue:」に書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-BLD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-BLD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1013-BLD-02` のコミット・ログなし。`BLD` は同じセッションの BLD-01 で使ったもの（最初の指示ではないので重複の確認は対象外）
- 0章: 「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: ローカルの `work/1013-bld` は `origin/cloudflare` の祖先（リモートもマージ済み）→ `git merge --ff-only origin/cloudflare`（ed93cb731..287861d4d）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順・作業ブランチが揃っている

### 手順1

- 既定ブランチ: REST `repos/retroeater/mj` の `default_branch` は `cloudflare`（前提と一致）。リポジトリは private
- BLD-01 の後に作られた issue は #558・#559 だけ（いずれも別の論点）。同じ論点の issue は増えていない
- 未マージの `work/` ブランチで `.github/workflows/` を変えるコミットは無い（check_run のワークフローも無い）。既存で `workflow_run` を使うのは `regenerate-saikyo.yml`・`update-live-channel.yml`、`check_run` を使うものは無い

### 手順2: 方式 A の実現性

**(a) 起動回数の見込み。** 2026-10-09 06:00Z 以降の全リモートブランチのコミット 1135 件の check-runs（`?filter=all`）を引き、completed_at が 2026-10-10 00:00〜10-13 00:00 JST の3日間の completed を数えた（重複は check-run の id で除いた）。

| check-run | 3日 | 1日あたり |
|---|---|---|
| Workers Builds: mj（`Preview URL` あり） | 203 | 67.7 |
| Workers Builds: mj（`Preview URL` 無し） | 83 | 27.7 |
| Workers Builds: mj-scheduler | 12 | 4.0 |
| Workers Builds: mj-ask | 1 | 0.3 |
| Actions のジョブ（check 296・regenerate 70・sync 28・update 21 ほか） | 442 | 147.3 |

- Actions のジョブの check-run は A を起動しない（下の (c) の「recursive」の文）。**A の起動は Workers Builds の completed の分だけで、1日約100回**
- このうち Actions の `GITHUB_TOKEN` で push されたコミット（作者 github-actions[bot]、`chore: regenerate` など）の分は 3日で 18 回（`Preview URL` 無し 12・あり 6）。下の (c) の文から、**これらは A を起動しない可能性がある（未検証）**
- Workers Builds の check-run は1回のビルドで2本（`in_progress` のまま残る1本と completed の1本）。`types: [completed]` なら起動は completed の1本だけ

**(b) 本番とプレビューを見分ける項目。** GitHub の webhook の `check_run.completed` の payload（github/rest-api-description の `webhook-check-run-completed` のスキーマで確認）の `check_run` の項目は app・check_suite・completed_at・conclusion・deployment・details_url・external_id・head_sha・html_url・id・name・node_id・output（annotations_count・annotations_url・summary・text・title）・pull_requests・started_at・status・url。`check_run.check_suite` に head_branch・before・after がある。既存の check-run と照合した結果:

- **`output.summary` の `Preview URL` は、成功したビルドにしか出ない。失敗したビルドは本番もプレビューも `Build ID` と `Script` の2行だけで同じ**（3日＋10/13 の Workers Builds の failure 10 件すべて）。`details_url` も本番・プレビューとも `.../services/view/mj/production/builds/<Build ID>` で同じ。**前提の A の条件（`Preview URL` が無い＝本番）は失敗の判定には使えない**
- **`check_suite.head_branch` はそのコミットを最初に push したブランチ**（check suite はアプリとコミットごとに1つ）。本番で失敗した 5cbc83eb の suite は `work/1013-snm-check`、`mj-scheduler` が本番で失敗した 86979e3d の suite は `work/1012-dsn`。本番の判定には使えない
- check-runs から読めた failure（Workers Builds: mj）は、BLD-01 の5回のほかに 10/12 の4回: f09571e4（07:46:18Z → 08:17:18Z、約31分。suite は `work/1012-whs-final`）・19b99cc9（13:08:27Z → 13:09:46Z、`work/1012-gcl`）・b0ee350e（13:22:34Z → 13:24:07Z、`work/1012-rvc`）・7ac2439e（14:50:07Z → 15:21:07Z、`work/1012-bot`）。どれが本番かは check-run からは決まらない。f09571e4 は BLD-01 の申告値（本番、約30分で失敗）と時間が合う。プレビューの失敗も 10/12 に起きていた可能性がある
- 確実に見分けられるのは Cloudflare 側の値: ビルドの API（`builds/builds/<Build ID>`）か、候補 C のイベントの `payload.buildTriggerMetadata.branch`（下の (d)）。A で本番だけに絞るには、Build ID から Cloudflare の API を引く（Cloudflare のトークンが Actions に要る）か、`head_sha` が今の `cloudflare` の先頭かを GitHub の API で見る（近似。同じコミットを作業ブランチと cloudflare に push したときのプレビューの失敗は区別できない）

**(c) `check_run` で動くワークフローの制約**（docs.github.com はセッションのプロキシで拒否〈CONNECT 403〉されたため、同じ文書の原稿 github/docs の raw で読んだ。公開ページは https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#check_run ）:

- 「This event will only trigger a workflow run if the workflow file exists on the default branch.」（`data/reusables/actions/branch-requirement.md`）
- 「To prevent recursive workflows, this event does not trigger workflows if the check run's check suite was created by GitHub Actions or if the check suite's head SHA is associated with GitHub Actions.」
- 活動の種類は created・rerequested・completed・requested_action。`GITHUB_SHA` は「Last commit on default branch」、`GITHUB_REF` は既定ブランチ。`check_run` には `branches` の絞り込みが無い（`workflow_run` と違う）ので、絞り込みはジョブの `if:` で行う
- `GITHUB_TOKEN`: 「events triggered by the `GITHUB_TOKEN` will not create a new workflow run」（`workflow_dispatch`・`repository_dispatch` は例外。`data/reusables/actions/actions-do-not-trigger-workflows.md`）。Workers Builds の check-run は Cloudflare のアプリ（`cloudflare-workers-and-pages`）が作るので、この制約には当たらない見込み。ただし上の「head SHA is associated with GitHub Actions」が、`GITHUB_TOKEN` で push したコミット（`chore: regenerate`）を指すかは文書から決まらない

**(d) skipped の実行と使用分。** ジョブの `if:` で絞ると、1日約100回の実行がジョブ skipped で Actions の一覧に残る。skipped のジョブはランナーを使わない。リポジトリは private なので Actions の分は枠を使うが、ジョブが走るのは失敗のときだけ。mj-logs の `actions/status.md` は各ワークフローの直近5回（`scripts/actions_status.py` の `RUNS_PER_WORKFLOW`）なので、この行はほぼ skipped で埋まり、失敗は1時間ほどで表から消える

**(e) 候補 C の公式ドキュメント**（developers.cloudflare.com、2026-10-13 に取得）:

- イベント `cf.workersBuilds.worker.build.failed` の payload に `source.workerName` と `payload.buildTriggerMetadata.branch`・`commitHash`・`buildOutcome` がある（https://developers.cloudflare.com/queues/event-subscriptions/events-schemas/ ）。本番かどうかを branch で確実に見分けられる
- Queues の料金: Workers Free は「10,000 operations/day included」、1通に約3 operations（https://developers.cloudflare.com/queues/platform/pricing/ ）。失敗のイベントだけなら枠の心配は無い見込み。Free で Event subscriptions が使えるか・消費する Worker（`mj-scheduler` に足すなど）の作りは未確認

### 前提との差

- 前提の A の条件「`output.summary` に `Preview URL` が無い（＝本番）」は、失敗したビルドでは成り立たない（上の (b)）。指示は調べて issue に書くことなので止めず、issue に事実として書き、見分け方の選択を平野さんの判断の項目に入れた
- BLD-01 で書いた docs/notes/cloudflare.md の「本番かプレビューかは `Preview URL` で見分ける」は成功したビルドに限る。「申告値: 10/12 の f09571e4 …（ここからは検証できない）」は check-run で約31分の failure を確かめられた。今回の指示の範囲（docs/logs・docs/decisions）外なので文書は直さず、#561 の「あわせて直すこと」に書いた

### 手順3: 起票

- #561「本番の Workers Builds の失敗を知らせる」（open、ラベル `分野: 自動化`・`分野: インフラ`）。本文は目的・経緯（BLD-01 のログと cloudflare.md の該当節の引用とリンク）・候補 A・B・C と要確認の点・調査の結果・平野さんの判断が要ること・あわせて直すこと・`Chat-Ref:` の順
- 起票前に同じ題の issue が無いことは手順1で確認済み

### 決定の記録

- docs/decisions/cloudflare.md に「2026-10-13（CHAT-1013-BLD-02）」として、指示の決定（失敗を知らせる仕組みを入れる、方式は調査の後、#561）を足した

### マージ

最後の push の前に `origin/cloudflare` が進んでいた（7557aba16）ため `git merge origin/cloudflare` で取り込み（衝突なし。差は docs/decisions/cloudflare.md と本ログのみ）、再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめ、`git push origin work/1013-bld:cloudflare` で fast-forward（7557aba16..2a8c1c69d）。

## 報告

- 状態: 完了
- ブランチ: work/1013-bld
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-BLD-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-bld
- 確認用URL: なし
- マージ: 済（2a8c1c69d）
- issue: #561（起票）
- 判断が必要なこと: なし（#561 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b53374d9）: https://github.com/retroeater/mj-logs/tree/main/guide/b53374d9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b53374d9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
