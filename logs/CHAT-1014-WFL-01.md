# CHAT-1014-WFL-01

- 着手日時: 2026-10-14
- 対象issue: 親 issue（手順 2 で起票）・#263・#132
- ブランチ: work/1014-wfl
- 着手時HEAD: ec9e24c31

## 指示

【Claude作成】Claude Code 向け指示：GitHub Actions の整理（統合・共通化・改名・ダッシュボード）の調べと起票 Chat-Ref: CHAT-1014-WFL-01 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調査と起票だけの指示。`.github/`・`scripts/`・`workers/` は変えない） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1014-wfl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`.github/workflows/` の 19 本を、統合・共通化・改名・起動の表の一本化・ダッシュボードの生成の 5 段で整理する。この指示はその最初で、チャット側が集めた現状（下の「前提」）を実物で確かめ、親 issue を起こし、改名と統合の対応表（設計）をログに書く。コードは変えない。
決定（2026-10-14、平野さん）

* 役目を終えた 4 本（`sync-logs.yml`・`sync-books-calendar.yml`・`write-live-channel-candidate.yml`・`check-leagues-dropped.yml`）は削除する（`sync-logs.yml` は #298 の 10/21 の予定と合わせる）
* 毎朝の運行（Worker からの 5 本と、その連鎖の `regenerate-saikyo`）は 1 本のワークフロー（仮に `daily.yml`）にまとめ、先行関係は `needs:` で書く。各本は `workflow_call` 化して残し、手動実行は各本でできるようにする
* 週次・月次の起動も Worker `mj-scheduler` の起動の表（`workers/scheduler/schedule.json`）へ移し、GitHub 側の保険の `schedule` は外す（#504 の 11/30 を前倒し。ただし外す時機は #504 の観察の結果を見て別に決める。下の「前提」）
* ファイル名を `<動詞>-<対象>.yml` に統一する。ファイル名だけでなく、`name:`・`run-name`・入力の `description`・ファイル冒頭の説明コメント・docs の記述も同じ規則で改名して統一する
* ダッシュボードは案 A: mj-logs に静的ページ（`actions/index.html`）として生成し、GitHub Pages で開く（mj-logs の Pages の有効化は平野さんの手作業。再実行は GitHub の「Run workflow」画面へのリンク）
* 平野さんに複数の選択肢から選んでもらうときは、常に判断材料（比較）と推奨・その理由を添える（運用の指定。docs/decisions/operations.md に記録する）

前提（チャット側。平野さんの決定ではない）

* 現状の数値（チャット側が 2026-10-14 09:30 JST に GitHub API と cloudflare 522a4323 の実物から集めた。要確認）: ワークフロー 19 本・3,168 行・23 ジョブ。`workflow_call` を持つのは `regenerate-page.yml` だけで、呼ぶ側は `update-live-channel`・`update-sns-book`・`regenerate-saikyo` の 3 本。直近 7 日の実行 653 回（skipped 28）、1 分切り上げの推定で 811 分／週
* 同じ書き方の繰り返し（要確認。本数はチャット側の数え）: ①checkout → setup-python 3.12 → `pip install google-auth requests` → unittest が 8 本 ②「dry-run かどうかを決める」「実行するかどうかを決める」（`SCHEDULE_ENABLED`・`SCHEDULED`・`APPLY` の決め方）が 9 本 ③常設 issue を題名で探して作る／本文を書き換える／コメント／無ければ閉じる github-script が 10 か所（check-image-links ×3・check-meibo ×2・cleanup-logs・update-live-channel ×3・sync-dojo-calendar・regenerate-page の帰り道） ④`git config` → add → commit → push（失敗時 rebase か作り直しで 3 回）が 5 本 ⑤「実行サマリに表示」が 12 本 ⑥`python-version: '3.12'` の直書きが 19 か所
* 先行関係で時刻にしか頼っていないもの（要確認）: `update-sns-book`（04:10）→ `check-image-links` の `sns_book`・`saikyo`（04:30）、`update-sns-book` → `regenerate-saikyo`（今は検知の完了に連鎖。2026-10-14 は 04:30 の検知が起動せず最強戦の再生成も動かなかった。#506 の 10/14 のコメント）、`update-live-channel` の regenerate と `update-sns-book` の regenerate(all) の push の競合（#263。`regenerate-page.yml` に concurrency が無い）
* Worker の取りこぼしの埋め合わせは CHAT-1014-ACF-18 で実装済み・マージ済み（592334b3、#504。数日の観察中）。この系列（WFL）では Worker のコードを変えない。 `schedule.json` の行の追加（週次・月次）と GitHub 側の保険の `schedule` の削除は、#504 の観察の結果を平野さんが見てから、WFL の後の指示で行う
* `docs/notes/github-actions.md`（#492、CHAT-1014-GDS-01〜03）が Actions の記述の正で、`scripts/tests/test_workflows_table.py` が「ワークフローの一覧」の表と実物の一致を確かめる。ワークフローを足す・消す・改名する指示では、同じコミットで表を直す。未マージの work/1014-gds が `assets-check.yml` と `github-actions.md` を変えている（要確認）
* 改名の規則（チャット側の案。手順 3 で実物に合わせて確定する）: ファイル名は `<動詞>-<対象>.yml`、動詞は `check`（読むだけで結果は issue へ）・`sync`（外部へ差分を反映）・`update`（スプレッドシートを書く）・`fetch`（外部から取ってコミット）・`regenerate`（ページを作り直してコミット）・`cleanup`（リポジトリの片付け）の 6 つ。`name:` は「<対象>の<動作>」の名詞形（例: 「公開対象の検査」「マージ済みブランチの片付け」「本番ビルド失敗の検知」）。`run-name` の `[scheduled] …` は `name:` と同じ文字列。入力の `description` は「〜する（外すと〜だけ）」の形にそろえる。ファイル冒頭のコメントの 1 行目は `name:` と目的と issue 番号
* 改名の対応表（チャット側の案）: `assets-check.yml` → `check-assets.yml`、`sitemap-lastmod.yml` → `sync-sitemap-lastmod.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`。ほかはファイル名そのまま（`name:` の形だけそろえる）。新設: `daily.yml`（毎朝の運行）・`fetch-youtube.yml`（`regenerate-page.yml` から YouTube チャンネル・帰り道の取り直し・OGP・帰り道の知らせを分ける）・`sync-yotei.yml`（`update-live-channel.yml` の job `yotei` を分ける）
* 段の順（チャット側の案）: ①共通化の部品（composite action 4 つ＋ `.python-version`）→ ②削除 4 本・`regenerate-page` の job `regenerate` への concurrency・#132 の入力の env 化 → ③`daily.yml`・分割・改名（同時に 1 回）→ ④起動の表の一本化と保険の `schedule` の削除（#504 の観察後）→ ⑤ダッシュボードの生成（`scripts/actions_status.py` と同じデータから `actions/index.html`。流れ図の表は YAML の `on:`・`uses:`・`workflow_run` と `schedule.json` から起こす）
* ファイル名を変えると GitHub 上は別のワークフローになる（run 番号が 1 から、古い実行は旧名の下に残る）。`schedule.json`・`regenerate-saikyo.yml` の `workflow_run.workflows`（`name:` で参照）・`scripts/actions_status.py`・mj-logs の `sync-from-mj.yml`・Worker の朝の確かめ・docs の参照が追随の対象（要確認。手順 3 で洗う）
* 関係する既存の issue: #263（concurrency）・#132（入力の env 化）・#308（Node 24 対応版）・#538（ubuntu 26.04）・#539（Python 3.14）・#298（使用量）・#504（Worker）・#492（文書）・#563（morning.md）

手順

1. 同じ論点の issue を検索し（`gh issue list --state all --search` で「ワークフロー 整理」「composite」「共通化」「改名」「daily」「統合」など。クローズ済みも含めて確認）、親 issue に相当するものがあれば止まって報告する。あわせて「前提」の数値と繰り返しの本数を実物（`.github/workflows/`、`git grep`）で数え直し、食い違いをログの `## 経過` に表で書く（食い違いで止まらない。数の正は実物）。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `.github/workflows/`・`workers/`・`docs/notes/github-actions.md` を変えているかを洗い、ブランチ名と変えるファイルをログに書く
2. 親 issue「GitHub Actions の整理（統合・共通化・改名・ダッシュボード）」を起票する（ラベル「分野: 自動化」「分野: 整理・保守」）。本文は「決定」をそのまま写し、5 段をチェックリストで書き、各段で関係する既存の issue（#263・#132・#308・#539・#298・#504・#492）を参照する。#263 と #132 には「親 issue の段②で扱う」とコメントする（クローズしない）。期日は書かない。着手中のコメントは親 issue に書く（本文・状態・ラベルは手順 2 で作った後は変えない）
3. 改名と統合の対応表を設計としてログの `## 経過` に書く: 19 本すべてについて「今のファイル名／`name:`／新しいファイル名／新しい `name:`／行き先（残す・`daily.yml` から呼ぶ・分ける・削除）／参照している場所（`git grep` で洗ったファイルと行。`schedule.json`・`workflow_run`・`scripts/`・mj-logs 側・docs）」の表。「前提」の対応表と規則をもとにし、実物を見て変えたほうがよい名前があれば代案と理由を書く（決めるのは平野さん。`## 報告` の「判断が必要なこと」に並べる）。あわせて、composite action に寄せる 4 つ（setup-python／resolve-mode／upsert-issue／commit-push）について、寄せる先のファイル名・入力・出力の案と、本ごとに違っていて寄せられない点を書く

止まる条件

* 手順 1 で、親 issue に相当する open の issue がある（クローズ済みは報告だけ）
* 親 issue に他セッションの着手中のコメントがある
* `docs/notes/github-actions.md`「ワークフローの一覧」の表と `.github/workflows/` の実物が一致しない（`python3 -m unittest scripts/tests/test_workflows_table.py` が失敗する）。直さずに報告する
* （未マージの work/ ブランチとの重なり）この指示は `.github/`・`scripts/`・`workers/` を変えないため、重なりでは止まらない。洗った結果をログに書く
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、改名の代案と、段①の指示を書くのに平野さんの判断が要る点を並べる
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、`git log --all --grep` と docs/logs の履歴）: CHAT-1014-WFL-* も識別子 WFL も未使用
- 作業ブランチ: work/1014-wfl はローカル・リモートとも無し → `git checkout -b work/1014-wfl origin/cloudflare`（ec9e24c31）
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば…この行が指示文の最後の行です。」と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl/docs/logs/CHAT-1014-WFL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 592334b3）: https://github.com/retroeater/mj-logs/tree/main/guide/592334b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/592334b3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
