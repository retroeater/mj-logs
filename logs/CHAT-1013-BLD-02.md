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

## 報告

- 状態: 作業中
- ブランチ: work/1013-bld
- ログ: https://github.com/retroeater/mj/blob/work/1013-bld/docs/logs/CHAT-1013-BLD-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-bld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7557aba1）: https://github.com/retroeater/mj-logs/tree/main/guide/7557aba1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7557aba1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0186d17f.md
