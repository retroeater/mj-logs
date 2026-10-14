# CHAT-1014-WFL-08

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl-s2
- 着手時HEAD: baccec3a8

## 指示

【Claude作成】Claude Code 向け指示：段①の展開その2（check-image-links の部品化・commit-push の部品・cleanup-logs・部品のテストを assets-check で回す） Chat-Ref: CHAT-1014-WFL-08 マージ: 判断待ちで止まる（`.github/`・`scripts/` の変更。作業ブランチの手動実行の結果と差分をチャット側が見てから） 貼る時機: CHAT-1014-WFL-07 の完了の後 作業ブランチ: クラウドセッションで実行する。work/1014-wfl-s2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl-s2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-07 のログの `## 報告` を読み、完了していなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」「共通の部品」を読む。

目的
#564 段①の展開の 2 本目。(1) 部品のテストを `assets-check.yml` で push ごとに回す (2) `check-image-links.yml` の 3 ジョブを部品で書き直す (3) commit-push の部品を作り、`cleanup-logs.yml` を部品で書き直す。
決定（2026-10-14、平野さん）

* （WFL-07 の決定）部品のテストは `assets-check.yml` に 1 ステップ足して push ごとに回す
* （WFL-04 の決定）issue の本文の組み立ては github-script から各スクリプトへ移し、元の JS と同じ文面かを突き合わせてテストを足す。commit-push の部品は展開の指示で作る。入力 `scheduled` と `run-name` の `[scheduled]` は Worker の起動の表に無い本にも足す
* （WFL-05 の決定）常設 issue の型S は ACF-21 の作り（閉じた常設 issue を開き直す）にそろえる

前提（チャット側。平野さんの決定ではない）

* `check-image-links.yml` は ACF-21（efac1e4f）の後の版を元にする。`check` と `sns_book` は同じ issue（#352、題「画像リンク切れの検知結果」・常設）の自分の節だけを書き換える（`scripts/check_image_links.py --issue-section`）。`saikyo` は別の issue（題「最強戦の選手写真のリンク切れ検知結果」）を書く。`needs: check`（同じ issue を同時に書かない）・`saikyo_resolve_test` の入力・Worker からの 04:30 の起動（入力 `scheduled`）は保つ（要確認。着手時に cloudflare の版で読み、ACF-21 より後に変わっていたら変えた Chat-Ref をログに書く）
* 節の書き換えは upsert-issue の 1 回の呼び出しでは収まらない（今の本文を読む → Python で自分の節を差し替える → 書く）。WFL-01 の案のとおり、`mode: read`（今の本文を書き出す）と `mode: state` の 2 回の呼び出しにするか、`--issue-section` を部品の外に残すかは実物を見て決め、理由をログに書く。`check_image_links.py` の `pick_issue()`・`issue_action()` と部品の型S が同じ判定になることをテストで確かめ、片方に寄せられるなら寄せる（二重に持たない）
* `saikyo` の issue（題「最強戦の選手写真のリンク切れ検知結果」）を常設ラベルにするか（今 issue があるか・番号・状態）は、表にしてログに書き、判断待ちに入れる（この指示ではラベルを付けない）
* commit-push の部品の案は WFL-01 のログ「composite action の案（段①）」の 4（入力 `paths`・`message`・`body`・`message-file`・`branch`・`retry`〈`rebase`／`none`〉・`autostash`、出力 `committed`・`sha`）。この指示で使うのは `cleanup-logs.yml` だけ（`message-file` に一覧を入れる形）。`cleanup-logs.yml` の通知（題「作業ログ: 自動削除の条件外のログ」#357、常設、無ければ失敗させる）は upsert-issue の `mode: notice`・`create: 'false'` で書く。本文の組み立ては `scripts/cleanup_logs.py` へ移す
* `cleanup-logs.yml` は push する（docs/logs の削除）。作業ブランチでは `dry_run: true` で通し、commit-push の部品は「差分が無ければ何もしない」道筋と、作業ブランチへ空でないコミットを push する道筋（試験の 1 ファイルを作って消す、など。cloudflare には push しない）を確かめる。試験のコミットを戻すコミットの差分を確かめる
* `check-image-links.yml` の書く道筋（issue の書き換え）は作業ブランチでは通さない（dry-run の入力が無ければ、upsert-issue の `dry-run` を一時的に `'true'` に固定したコミットで手動実行し、戻すコミットの差分が 1 行だけであることを確かめる）。作業ブランチでの手動実行は古いデータで #352 の本文を上書きしうる（docs/notes/github-actions.md「ワークフローを手動実行するとき」）ので、書く道筋で手動実行しない
* `check-image-links.yml` の完了は `regenerate-saikyo.yml` を `workflow_run`（`name:` で参照、cloudflare だけ）で起動する。`name:` は変えない（改名は段③）
* 初回の予約実行: check-image-links は毎日 04:30 JST（Worker）、cleanup-logs は月曜 06:23 JST

手順

1. `assets-check.yml` に部品のテストのステップを足す（`node --test` の 1 ステップ。`.github/actions/**` の変更を含む push で動くように `paths` も確かめる）。作業ブランチへの push で動き、success になることを確かめる
2. `check-image-links.yml` の 3 ジョブを部品（setup-python・resolve-mode・upsert-issue）で書き直す。本文の組み立てをスクリプトへ移し、元の JS と同じ文面かを突き合わせてテストを足す。「前提」の形で手動実行（`scheduled` なし・書かない）と予約の起動の道筋を確かめる。3 ジョブの結果（件数）が直前の cloudflare の実行と同じか、データの変化で説明できることを確かめる
3. commit-push の部品を作り、`cleanup-logs.yml` を部品（setup-python・resolve-mode・upsert-issue・commit-push）で書き直す。`SCHEDULE_ENABLED` を外し、入力 `scheduled` と `run-name` を足す。「前提」の形で手動実行を確かめる。docs/notes/github-actions.md の「共通の部品」と 3 本の記述を今の動きに直す。saikyo の issue の表をログに書く。#564 にコメントする。完了報告のうえ判断待ちで止まる

止まる条件

* WFL-07 が完了していない
* cloudflare の `check-image-links.yml` が ACF-21 より後に変わっていて、変更が「前提」の作り（節の書き換え・`needs: check`・issue の見つけ方）を変えている
* 元の JS と移した後の本文の文面が合わない（差を書いて止まる。直し方が明らかな差〈空白・改行の 1 か所〉は直してよい）
* 手動実行が起動できない（403・404）: 平野さんに GitHub の画面の「Run workflow」を頼む形で止まる
* 手動実行の結果が直前の cloudflare の実行と違い、データの変化で説明できない
* 試験のコミットが cloudflare へ push されそうになる（commit-push の部品の `branch` が実行ブランチ以外を指す）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-image-links.yml`・`cleanup-logs.yml`・`assets-check.yml`・`.github/actions/`・`scripts/check_image_links.py`・`scripts/check_sns_book_images.py`・`scripts/collect_saikyo_images.py`・`scripts/cleanup_logs.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（重なる本だけ外して進め、ログに書く。全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」にマージの可否と saikyo の issue の常設ラベルを書く。初回の予約実行の日時を「未確認の項目」に書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-08 は未使用
- CHAT-1014-WFL-07 のログ（cloudflare）の状態は「完了」
- 作業ブランチ: work/1014-wfl-s2 はローカル・リモートとも無し → `git checkout -b work/1014-wfl-s2 origin/cloudflare`（baccec3a8）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- docs/notes/github-actions.md「ワークフローを変更したとき」「共通の部品」を読んだ

### 前提の確かめ

- `efac1e4f..origin/cloudflare` で `check-image-links.yml`・`check_image_links.py`・`check_sns_book_images.py`・`collect_saikyo_images.py`・`cleanup-logs.yml`・`cleanup_logs.py`・`assets-check.yml` を変えたコミットは無い（ACF-21 の版のまま）。節の書き換え・`needs: check`・`saikyo_resolve_test`・入力 `scheduled` は前提どおり
- 未マージの work/ ブランチで `.github/`・上の scripts を変えるものは無い

### 設計の判断

- **#352 の節の書き換え**: upsert-issue の `read`（今の本文を書き出す）→ `check_image_links.py --issue-section`（自分の節を差し替え、どの節にも切れが無ければ閉じるコメントを `--close-comment` に書く）→ `state`（書く）の3段にした。`--issue-section` を部品の外に残すと、ACF-21 の github-script（issue の選び方・書き換え・開き直し・閉じる）をこの本だけ持ち続けることになり、型S を二重に持つ。部品に寄せると Python に残るのは「節の差し替え」だけになる
  - そのため部品を2点直した: (1) `read` を `state` と同じ選び方（常設を状態を問わず優先、無ければ開いているもの）にし、出力 `state` を足した（読む issue と書く issue を同じにする）。(2) `state` で閉じるとき、`body-file` があれば本文も書き換える（ACF-21 は閉じるときに自分の節を「切れなし」にした本文を書いていた）。check-meibo・check-saikyo-unregistered は閉じるとき `body.md` を作らないので動きは変わらない
  - `check_image_links.py` の `pick_issue()`・`issue_action()`・`--pick-issue`・`--state` を外した（部品の型S と同じ判定。`scripts/tests/test_check_sns_book_images.py` の `PickIssueTest` 4件・`IssueActionTest` 1件は外し、同じ観点のテストは WFL-05 で部品側〈`upsert.test.js`〉にある）。代わりに `WriteIssueBodyTest`（閉じるコメントを書く・別の節が切れていれば書かない）2件を足した
- **入力 `dry_run` を足した（既定 false = 今までどおり書く）**: check-image-links には書かない入力が無く、作業ブランチの試験のたびに一時のコミットが要るため。既定を変えないので Worker・予約の起動・手動の今の使い方は変わらない
- **commit-push の部品**: WFL-01 の案から `branch` の入力を外し、push 先を実行ブランチ（`github.ref_name`）に固定した（作業ブランチで動かして cloudflare へ push する道を作らないため。止まる条件「試験のコミットが cloudflare へ push されそうになる」を構造で防ぐ）。`retry` の既定は `none`（cleanup-logs の今の動き。sitemap-lastmod・regenerate-page の展開で `rebase` を使う）
- **notice の `create: 'false'`**: #357 が無くても、足すコメントが無ければ失敗にしない（元の github-script は通知対象が無ければ issue を探さずに終わっていた）。足すコメントがあって #357 が無ければ今までどおり失敗
- **本文の移し替え**: 最強戦の issue は `collect_saikyo_images.py` の `issue_text()`・`--issue-dir`、#357 へのコメントと削除のコミットメッセージは `cleanup_logs.py` の `issue_comment()`・`commit_message()`・`--issue-dir`。元の github-script（とワークフローの `python3 -c`）を node・python で動かし、最強戦3通り（切れなし・2件〈`|` の escape・X ID なし・状態が数値と文字〉・解決の文言あり）、#357 4通り（対象なし・①②③の全部・旧い違反だけ・新しい違反だけ）、コミットメッセージ2通りが一致することを確かめた。テスト: `test_collect_saikyo_images.py` に3件、`test_cleanup_logs.py` に3件

### 手順1: assets-check（403d42baf）

- 最後のステップに「共通の部品のテスト」（`node --test .github/actions/upsert-issue/test/*.test.js`）を足した。`on.push.paths` は `'**'` から `docs/**` を除く形で、`.github/actions/**` の変更を含む push で動く
- 作業ブランチへの push で run #3534（38514650092） https://github.com/retroeater/mj/actions/runs/38514650092 success
- 部品のテストは 18 件（WFL-05 の14件＋read 2件・閉じるときの本文1件・notice の create 1件）

### 手順2: check-image-links（403d42baf）

| run | 入力 | 結果 | 要点 |
|---|---|---|---|
| #37（38514656954） https://github.com/retroeater/mj/actions/runs/38514656954 | `dry_run: true` | success（3ジョブ） | check:「[dry-run] #352(closed) を開き直して本文を書き換える」、1141件中1件（`404` 上田稜 X の `_80x80`）。saikyo:「(無い) は閉じるものが無い」（切れ0）。sns_book:「#352(closed) は閉じるものが無い」（dry-run で check が書いていないため、読んだ #352 の本文は check の節が0件のまま。本番では check が開き直して書いた後に sns_book が読む） |
| #38（38515680429） https://github.com/retroeater/mj/actions/runs/38515680429 | `scheduled: true`・`dry_run: true`（一時のコミット cef5f54c7） | success（3ジョブ） | 題「[scheduled] 画像リンク切れの検知」。upsert-issue は試験の行で dry-run |

- 直前の cloudflare の実行（#35、38490119497、10/14 09:25 JST、ACF-21 の前の作り）は check で3件切れて #562 を作っていた（#562 の本文: 1141件中3件、上田稜を含む）。今回は1件（上田稜だけ残る）で、データの変化（2件が直った）で説明できる。#352 は closed・常設（ACF-21 で #562 の中身を移して閉じた）なので、今回の作りでは #352 を開き直すことになる
- 戻すコミット 8b963746c の差分は check-image-links の3行（各ジョブの upsert-issue の `dry-run` 1行ずつ）と cleanup-logs の2行（下）。戻した後の `.github/` は 403d42baf と同じ（`git diff 403d42baf -- .github` が空）

### 手順3: commit-push・cleanup-logs（403d42baf）

| run | 入力 | 結果 | 要点 |
|---|---|---|---|
| #9（38514659595） https://github.com/retroeater/mj/actions/runs/38514659595 | `dry_run: true` | success | 判定: 削除対象15件・通知対象137件。upsert-issue「[dry-run] #357 にコメントする」（①判断待ち・中断10件の表、②旧い違反127件は件数だけ） |
| #10（38515522199） https://github.com/retroeater/mj/actions/runs/38515522199 | `dry_run: true`（一時のコミット 8eaafb3c2 で commit-push の試験のステップを足した） | success | 差分なし → `committed=false`・「変更なし」。試験のファイルを足して push → 作業ブランチに 51ff7bbbb（bot）。消して push（`retry: rebase`）→ e0283c22d（bot）。2つのコミットの差し引きは空。push 先は `work/1014-wfl-s2`（cloudflare には push していない） |
| #11（38515683088） https://github.com/retroeater/mj/actions/runs/38515683088 | `scheduled: true`・`dry_run: true`（一時のコミット cef5f54c7） | success | 題「[scheduled] 作業ログの片付け」。コミットの手順（`if: dry-run != 'true'`）が動いたこと＝予約の起動で apply が真。判定は試験の行で dry-run なので「変更なし」、通知は試験の行で dry-run |

- 試験のステップを外したコミット 7df789c3c（`cleanup-logs.yml` の32行の削除だけ）。bot の2コミットは `git merge --ff-only` で取り込んでから外した
- 直前の cleanup-logs の success（#8、38188776148、10/12 の予約実行）とは日付が違い、ログの増減で件数は変わる（dry-run のため比べる基準は「同じ作りの判定」で、判定のスクリプトは変えていない）

### docs・#564

- docs/notes/github-actions.md: 一覧の check-image-links（入力 `dry_run`）・assets-check（部品のテスト）・cleanup-logs（commit-push・#357 が無ければ失敗）の行、「共通の部品」（使う本・upsert-issue の read・閉じるときの本文・notice の create・節の書き換えの形・テストを assets-check が回す・commit-push）、「ワークフローを手動実行するとき」の check-image-links・cleanup-logs・入力 `scheduled` の一覧を直した（7e0b9ef80）。`test_workflows_table.py` は OK
- `python3 -m unittest discover -s scripts/tests`: OK（1069件）。`node --test …`: 18 pass
- #564 にコメントした

最強戦の issue（題「最強戦の選手写真のリンク切れ検知結果」、REST の全 issue から題で探した）:

| 番号 | 状態 | ラベル | 閉じた日 |
|---|---|---|---|
| #351 | closed | 無し | 2026-09-18 |
| #423 | closed | 無し | 2026-09-21 |
| #444 | closed | 無し | 2026-09-28 |
| #499 | closed | 無し | 2026-10-06 |
| #534 | closed | 無し | 2026-10-11 |

切れが出るたびに新しい issue が作られてきた（5件）。案: (a) 最新の #534 に「種類: 常設」を付ける（次に切れが出たら #534 が開き直される）／(b) #351（最初）に付ける／(c) 付けない（次に作られる issue に常設のラベルが付き、以後はそれを開き直す）。
推奨 (a): 直近の経緯が #534 にあり、閉じた issue が増え続けるのを今止められる。(b) は経緯が古く、(c) でも新しい issue が1件増えるだけで以後は止まるが、#534 と分かれる

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-WFL-09
- ブランチ: work/1014-wfl-s2
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s2/docs/logs/CHAT-1014-WFL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s2
- 確認用URL: なし（`.github/`・`scripts/`・docs の変更で、ページは変えていない）
- マージ: 未（判断待ち。指示の「マージ:」の行のとおり）
- issue: #564（結果をコメント）
- 判断が必要なこと:
  - マージの可否: assets-check の部品のテスト・check-image-links の部品化（`check_image_links.py` の `pick_issue()`・`issue_action()` を外して部品に寄せた、入力 `dry_run` を足した）・commit-push の部品・cleanup-logs の部品化・upsert-issue の3点の直し（read の選び方と出力 `state`、閉じるときの本文、notice の `create: 'false'` でコメントが無いとき）・docs。作業ブランチの手動実行5回と assets-check すべて success
  - マージ後の最初の check-image-links（Worker の毎朝 04:30）で、#352（closed・常設）が開き直される見込み（今は jpml_pros.html の1件〈上田稜 X の `_80x80` が 404〉が切れているため）。ACF-21 の作りのままでも同じ動きで、今回の変更による違いではない
  - 最強戦の issue の常設ラベル（経過の表）: (a) #534 に付ける／(b) #351 に付ける／(c) 付けない。推奨 (a)
- 未確認の項目:
  - 初回の予約実行（マージがそれより前のとき）: check-image-links は毎日 04:30 JST（Worker、入力 `scheduled`）、cleanup-logs は 2026-10-19（月）06:23 JST。GitHub の予約は遅れることがある
  - 書く道筋（#352 の開き直し・本文の書き換え、#357 へのコメント、削除のコミット）は作業ブランチでは通していない（dry-run と試験のステップで確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f8f8ab2）: https://github.com/retroeater/mj-logs/tree/main/guide/6f8f8ab2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
