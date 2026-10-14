# CHAT-1014-WFL-03

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl-parts
- 着手時HEAD: be98b1fc3

## 指示

【Claude作成】Claude Code 向け指示：段① 共通化の部品（composite action）を作り、2 本で試す Chat-Ref: CHAT-1014-WFL-03 マージ: 判断待ちで止まる（`.github/` の変更。作業ブランチでの手動実行の結果と差分をチャット側が見てから） 貼る時機: CHAT-1014-WFL-02 の完了の後 作業ブランチ: クラウドセッションで実行する。work/1014-wfl-parts を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl-parts origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-02 のログの `## 報告` を読み、完了していなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。

目的
#564 の段①。繰り返しの書き方を `.github/actions/` の composite action に寄せ、まず `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` の 2 本で置き換えて、作業ブランチの手動実行で確かめる。残りの本への展開は次の指示。
決定（2026-10-14、平野さん）

* CHAT-1014-WFL-02 の決定のとおり: 部品への置き換えは `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` の 2 本で試してから広げる。`SCHEDULE_ENABLED` はやめ、予約の起動の有効・無効は Worker の起動の表の `enabled` に任せる。`.python-version`（`3.12`）を入れる。#308（アクションの版）は同時に上げない

前提（チャット側。平野さんの決定ではない）

* 部品の設計は CHAT-1014-WFL-01 のログの `## 経過`「composite action の案（段①）」のとおり（置き場所 `.github/actions/<名前>/action.yml`、checkout は各本に残す）。この指示で作るのは setup-python・resolve-mode・upsert-issue の 3 つ。commit-push は 2 本とも使わないため作らない（次の指示）
* 2 本の呼ばれ方（要確認。`git grep` で洗い直す）: どちらも `schedule`（月曜 06:50 JST・05:17 JST）と `workflow_dispatch` だけ。`uses:` で呼ぶワークフロー・`workflow_run`・Worker の起動の表（`schedule.json`）には無い。`check-saikyo-unregistered` は常設 issue（題で探す、型S）を書き、`sync-birthday-calendar` は Google カレンダーに書く
* 段④で週次を Worker の表へ移すとき、2 本とも入力 `scheduled`・`run-name` の `[scheduled]`・「`schedule` か `scheduled` が真」の判定が要る（docs/notes/scheduler-worker.md「予約の起動の見分け方」）。resolve-mode に寄せるついでに、この 2 本に入力 `scheduled` と `run-name` を今の 6 本と同じ形で足してよい（表には足さない。表に無ければ Worker は起動しない）
* `python-version: '3.12'` の `python-version-file: .python-version` への置き換えは、この指示では部品と 2 本だけ。残りの本は展開の指示で行う（1 回のコミットで全本を触らない）
* `check-saikyo-unregistered.yml` の `SCHEDULE_ENABLED` は `'true'` で、やめても動きは変わらない（要確認）。`sync-birthday-calendar.yml` には無い
* docs: docs/notes/github-actions.md に「共通の部品（`.github/actions/`）」の節を足し、各部品の入力・出力と「寄せられない点」の扱いを書く（「ワークフローの一覧」の表は変えない。`scripts/tests/test_workflows_table.py` が通ること）
* マージ前の確かめ（docs/instruction-template.md の「本番に書き込むワークフローの変更をマージ前に試すとき」の形）: 手動実行は書き込まない入力（`dry_run: true`・`apply: false`）で行う。予約の起動の道筋（`scheduled: true` → apply）は、スクリプトの呼び出しに `--dry-run` を強制する 1 行を足したコミットで `scheduled: true` を手動実行して resolve-mode の出力（`scheduled: true / apply: true`）を確かめ、その行を戻すコミットの差分がその 1 行だけであることを確かめる

手順

1. `.github/actions/setup-python`・`resolve-mode`・`upsert-issue` を WFL-01 の案のとおり作る（upsert-issue のスクリプトは `.github/actions/upsert-issue/upsert.js`。この指示で使うのは型S だけだが、型N・型D の入力も案のとおり持たせ、使わない型はテストの代わりに `dry-run` の手動実行の出力で形を確かめる）。`.python-version` を置く。`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` を部品で書き直す（`SCHEDULE_ENABLED` を外す。入力 `scheduled`・`run-name` を足す。動きは今と同じ: 手動の既定は書かない、予約の起動は書く、`allow_many_deletes` は手動だけ）。置き換えの前後で各ステップの対応表（今のステップ → 部品の呼び出し）をログに書く
2. 作業ブランチで手動実行して確かめる: (a) `check-saikyo-unregistered` を `dry_run: true` で、`sync-birthday-calendar` を `apply: false` で実行し、success と、サマリに resolve-mode の 1 行と今までと同じ結果の出力があること。(b) 「前提」の形で `scheduled: true` の道筋を確かめ、戻すコミットの差分が 1 行だけであること。(c) `python3 -m unittest discover -s scripts/tests` が通ること。run の番号・URL・サマリの要点をログに書く。失敗で止まる見込みはない（止まったら原因をログに書く。失敗通知が届けば「対応不要」と最終報告に書く）
3. docs/notes/github-actions.md に「共通の部品」の節を足し、2 本の記述（「ワークフローを手動実行するとき」の `SCHEDULE_ENABLED` の記述など）を今の動きに直す。#564 の段①のチェック項目に「2 本で試した（run の番号）」をコメントする。完了報告のうえ判断待ちで止まる

止まる条件

* WFL-02 が完了していない
* 2 本の呼ばれ方が「前提」と違う（`uses:`・`workflow_run`・`schedule.json` に現れる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml`・`docs/notes/github-actions.md` の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。2 本のうち重なる本だけ外して進め、両方が重なるときだけ止まる）
* 手動実行が起動できない（403・404）: 平野さんに GitHub の画面の「Run workflow」を頼む形で止まる
* 手動実行の結果が今までの実行と違う（検知の件数・差分の件数が直前の success の実行と違う）: 原因を書いて止まる（データの変化で説明できるときはその旨を書いて進んでよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」にマージの可否と、展開の指示で決めが要る点（部品の入力で足りなかったもの）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-03 は未使用
- CHAT-1014-WFL-02 のログ（cloudflare）の状態は「完了」
- 作業ブランチ: work/1014-wfl-parts はローカル・リモートとも無し → `git checkout -b work/1014-wfl-parts origin/cloudflare`（be98b1fc3）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ（作業ブランチで手動実行して確かめてからマージ）

### 前提の確かめ

- 2本の呼ばれ方: `git grep` で `.github/`・`workers/`・`scripts/` を洗い、ほかから参照するのは `fetch-gsc.yml:81`（コメント）と `scripts/check_saikyo_unregistered.py:17`（docstring）だけ。`uses:`・`workflow_run`・`schedule.json` には無い（前提どおり）
- 未マージの work/ ブランチで `.github/`・`docs/notes/github-actions.md` を変えるものは無い（work/1014-gds はマージ済みになっていた）
- `check-saikyo-unregistered.yml` の `SCHEDULE_ENABLED` は `'true'`（やめても動きは変わらない）。`sync-birthday-calendar.yml` には無い（前提どおり）

### 手順1: 部品と2本の書き直し（3d7fa4db2）

作ったもの:

- `.github/actions/setup-python/action.yml`: 入力 `packages`・`test`、出力 `python-version`。`actions/setup-python@v5` を `python-version-file: .python-version` で呼ぶ（版は上げていない。#308 は別）
- `.github/actions/resolve-mode/action.yml`: 入力 `scheduled`・`manual-apply`、出力 `scheduled`・`apply`・`dry-run`。ログと実行サマリに「event: … / scheduled: … / apply: …」の1行
- `.github/actions/upsert-issue/action.yml`＋`upsert.js`: 入力 `mode`（state / notice / comment / read）・`title`・`number`・`body-file`・`comment-file`・`close`・`create`・`labels`・`marker`・`dry-run`・`run-url-footer`、出力 `number`・`action`・`body-path`・`comment-path`。WFL-01 の案に、型N・#475 の「前回の印付きコメントを読む」用の `read`（`marker`）を足した
- `.python-version`（`3.12`）。リポジトリ直下の新しいファイルで公開対象になるため `.assetsignore` に足した（#133）
- `scripts/check_saikyo_unregistered.py` に `issue_text()` と `--issue-dir`（`body.md` か `close.md` を書く）を足し、github-script にあった本文の組み立てを移した。元の JS と同じ文面になることを node で4通りのデータ（未登録・重複・対局日が空・何も無し、`|` の escape を含む）を突き合わせて確かめ、`scripts/tests/test_saikyo_unregistered.py` に3件のテストを足した
- `upsert.js` は、モックの `github` で 12 通り（state の更新・作成・閉じる・閉じる相手が無い・dry-run、notice のコメント・空・作成・作らずに失敗、comment、read、不正な mode）を手元で動かして確かめた

ステップの対応表（今 → 部品）:

| 本 | 今のステップ | 書き直した後 |
|---|---|---|
| check-saikyo-unregistered | dry-run かどうかを決める（`SCHEDULE_ENABLED`・`INPUT_DRY_RUN`） | リポジトリをチェックアウト（先頭へ移す。部品は checkout の後でないと読めない）→ dry-run かどうかを決める = `resolve-mode`（`scheduled`、`manual-apply: !inputs.dry_run`） |
| | リポジトリをチェックアウト | （上へ移した。`ref: github.ref` はそのまま） |
| | Pythonをセットアップ（`python-version: '3.12'`） | `setup-python`（入力なし） |
| | 登録漏れを確認 | 同じ（`--issue-dir issue` を足す） |
| | 実行サマリに表示 | 同じ |
| | 結果をissueに反映（github-script 約80行、dry-run なら skipped） | `upsert-issue`（mode state、題「最強戦の出場者の登録漏れ」、`close` は `body.md` が無いとき、`dry-run` は resolve-mode の出力。dry-run でも探すところまで動き、することを出す） |
| sync-birthday-calendar | リポジトリをチェックアウト | 同じ |
| | （env の `APPLY: schedule \|\| inputs.apply`） | 書くかどうかを決める = `resolve-mode`（`scheduled`、`manual-apply: inputs.apply`） |
| | Pythonをセットアップ・依存をインストール・テスト | `setup-python`（`packages: google-auth requests`、`test: 'true'`） |
| | Google に認証 | 同じ |
| | 同期（env の `APPLY`・`ALLOW_MANY_DELETES`） | 同じ。`APPLY` は resolve-mode の出力、`ALLOW_MANY_DELETES` は予約の起動でないときだけ入力を使う |
| | 実行サマリに表示 | 同じ（`apply:` は resolve-mode の出力） |

両方に入力 `scheduled`（「手では付けない」）と `run-name`（`[scheduled] <name:>`）を今の6本と同じ形で足した。Worker の起動の表には足していない。

### 手順2 (a): 書かない入力での手動実行（3d7fa4db2）

| ワークフロー | run | 結果 | 要点 | 直前の success |
|---|---|---|---|---|
| check-saikyo-unregistered（`dry_run: true`） | #9（38506817102） https://github.com/retroeater/mj/actions/runs/38506817102 | success | 「event: workflow_dispatch / scheduled: false / apply: false」。`Resolved .python-version as 3.12`。出場者440名・引けない人0名・対局日が空0件。upsert-issue「[dry-run] 「最強戦の出場者の登録漏れ」は無いので閉じるものが無い」 | #8（38207464466、10/12）: 「登録漏れなし」（0名）→ 件数は同じ |
| sync-birthday-calendar（`apply: false`） | #11（38506819071） https://github.com/retroeater/mj/actions/runs/38506819071 | success | 引数 `--compare`、`APPLY: false`・`ALLOW_MANY_DELETES: false`。名簿1099名・載せる人1072名・カレンダーの予定1072件、追加0・更新0・削除0 | #10（38185202120、10/12 の予約実行、apply）。その後に差分0なのは、予約実行が書いた状態のままという意味で整合 |

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-parts
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-parts/docs/logs/CHAT-1014-WFL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-parts
- 確認用URL: なし
- マージ: 未
- issue: #564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be98b1fc）: https://github.com/retroeater/mj-logs/tree/main/guide/be98b1fc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
