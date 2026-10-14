# CHAT-1014-WFL-10

- 着手日時: 2026-10-14
- 対象issue: #564・#263・#132
- ブランチ: work/1014-wfl-s3
- 着手時HEAD: a31620feb

## 指示

【Claude作成】Claude Code 向け指示：段② 使わないワークフロー 3 本の削除と、regenerate-page の concurrency・入力の env 化 Chat-Ref: CHAT-1014-WFL-10 マージ: 判断待ちで止まる（`.github/` の変更。作業ブランチの手動実行の結果と差分をチャット側が見てから） 貼る時機: いつでも（CHAT-1014-WFL-09 は完了済み） 作業ブランチ: クラウドセッションで実行する。work/1014-wfl-s3 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl-s3 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む。

目的
#564 の段②。使わなくなったワークフロー 3 本を消し、`regenerate-page.yml` に concurrency を付け（#263）、手動実行の入力を `run:` に直接展開している所を env 経由にする（#132）。
決定（2026-10-14、平野さん）

* 役目を終えた `sync-books-calendar.yml`・`write-live-channel-candidate.yml`・`check-leagues-dropped.yml` を削除する（2026-10-14 の決定。`sync-logs.yml` も削除するが、#298 の予定〈10/21、`[sync-logs]` の規則・`MJ_LOGS_TOKEN` の削除と一緒〉に合わせるので、この指示では消さない）
* 段の順は「段②を先に済ませ、毎朝のワークフロー（update-live-channel・update-sns-book・sync-dojo-calendar・delete-merged-branches・regenerate-saikyo）の部品化は段③の `run-daily.yml` とまとめる。regenerate-page・sitemap-lastmod・fetch-gsc・notify-build-failure の部品化は段③の後」（2026-10-14、チャット側の推奨に平野さんが同意）

前提（チャット側。平野さんの決定ではない）

* 3 本の今の状態（2026-10-14 のチャット側の読み。要確認）: `sync-books-calendar.yml` は `gh workflow disable` 済み（書籍の開発凍結、最終実行 9/22）、`write-live-channel-candidate.yml` は手動だけ（最終実行 9/27。層2 の書き込みは `update-live-channel.yml` の手順に吸収済み）、`check-leagues-dropped.yml` は手動だけ（#168 のための調べで 9/12 に 3 回、#168 はクローズ済み）。スクリプト（`scripts/sync_books_calendar.py`・`scripts/write_live_channel_candidate.py`・`scripts/check_leagues_dropped.py`）は消さない（`write_live_channel_candidate.py` は update-live-channel が使う。ほかの 2 本は凍結の解除・再調査のときに戻す元）
* 3 本を参照している場所（WFL-01 の対応表。要確認、`git grep` で洗い直す）: docs/notes/github-actions.md の表（`scripts/tests/test_workflows_table.py` が実物との一致を見る）、docs/notes/books-calendar.md・books-freeze.md・live-channel-write.md、`scripts/tests/test_actions_status.py` の例の文字列（例なので消さなくてよい）。凍結の解除の手順（books-freeze.md）には「ワークフローは git の履歴から戻す（削除したコミット）」を書く
* GitHub 上は、消したワークフローも実行の履歴とともに Actions の一覧に残る（無効化されたものも同じ）。mj-logs の `actions/status.md` に古い節が残るかは WFL-01 の未確認の項目。マージ後に status.md を読んで、残るかをログに書く（直すかは判断待ち）
* `regenerate-page.yml` の concurrency（#263）: 呼ばれる側（`workflow_call`）でも動くよう、ワークフローの先頭ではなく job `regenerate` に付ける。組は実行ブランチごと（例 `regenerate-page-${{ github.ref }}`）、`cancel-in-progress: false`。呼ばれたときの `github.ref` は呼ぶ側のもの。呼ぶ側（update-live-channel・update-sns-book・regenerate-saikyo）の concurrency の組とは別の名前にする（同じ組だと詰まる）。WFL-01 の未確認の項目「呼ばれる側の concurrency が呼ぶ側と詰まらないか」をここで確かめる
* #132: 「対象ページを再生成」の `${{ inputs.target_page }}` と「変更をコミット・push」の `${{ steps.regen.outputs.files }}` を env 経由にする。`target_page` はスペース区切りで複数ページを受けるので、シェルの変数はクォートせずに単語分割を使う（#132 の本文。`set -f` を併用してワイルドカードの展開を止める）。ほかの `run:` に `${{ inputs.* }}`・`${{ steps.*.outputs.* }}`・`${{ github.event.* }}` の直接展開があれば、同じ形で env に移し、場所をログに書く
* regenerate-page の書く道筋（cloudflare への push）は作業ブランチの手動実行では作業ブランチに push される（docs/notes/github-actions.md「ワークフローを手動実行するとき」）。作業ブランチで `target_page` に軽いページ 1 つ（例 `jpml_links` など、実物で軽いものを選ぶ）と 2 ページのスペース区切りで手動実行し、生成物のコミットが作業ブランチに入ることを確かめる。そのコミットはマージ前に戻す（戻すコミットの差分がそのコミットの逆であること）か、作業ブランチに残してよいかは生成物の差分の有無で判断し、ログに書く
* concurrency の効き目: 作業ブランチで regenerate-page を 2 回続けて手動実行し、2 回目が 1 回目の終わりを待つこと（取り消されないこと）を run の時刻で確かめる

手順

1. 3 本のワークフローを消し、参照している docs を直す（github-actions.md の表の行を消す。books-freeze.md に戻し方を書く。ほかは「削除した（2026-10-14、#564）」の 1 行に置き換える）。`python3 -m unittest discover -s scripts/tests`（`test_workflows_table.py` を含む）が通ること
2. `regenerate-page.yml` の job `regenerate` に concurrency を付け、#132 の直接展開を env に移す。作業ブランチで「前提」の形の手動実行を 3 回（1 ページ・2 ページ・続けて 2 回）行い、生成・コミット・concurrency の待ちを確かめる。run の番号と結果をログに書く
3. #263・#132 に「#564 の段②で直した（work/1014-wfl-s3、未マージ）」とコメントする（クローズはマージの後の指示で）。docs/notes/github-actions.md の regenerate-page の節を今の動きに直す。完了報告のうえ判断待ちで止まる

止まる条件

* 3 本のいずれかが「前提」と違う（直近 7 日に実行がある、ほかのワークフローから `uses:`・`workflow_run` で呼ばれている、Worker の起動の表にある）
* 手動実行が起動できない（403・404）: 平野さんに GitHub の画面の「Run workflow」を頼む形で止まる
* 手動実行の結果が failure、または生成物に「前提」で選んだページ以外の差分が出る
* concurrency で 2 回目の実行が取り消された（`cancel-in-progress` の扱いが見込みと違う）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `regenerate-page.yml`・消す 3 本・docs/notes/github-actions.md の regenerate-page の節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」にマージの可否と、作業ブランチに入った生成物のコミットの扱いを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-10 は未使用
- 作業ブランチ: work/1014-wfl-s3 はローカル・リモートとも無し → `git checkout -b work/1014-wfl-s3 origin/cloudflare`（a31620feb）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ

### 前提の確かめ

| ワークフロー | GitHub 上の状態 | 直近の実行 | 呼ばれ方 |
|---|---|---|---|
| sync-books-calendar.yml | `disabled_manually` | #10 2026-09-22（work/0922-bp、手動） | 無し（`uses:`・`workflow_run`・`schedule.json` に無い） |
| write-live-channel-candidate.yml | active | #5 2026-09-27（work/0922-ut、手動） | 無し |
| check-leagues-dropped.yml | active | #3 2026-09-12（cloudflare、手動） | 無し |

- 3本とも直近7日に実行は無い（前提どおり）
- 参照（`git grep`、`docs/logs/` を除く）: github-actions.md の表、books-calendar.md・books-freeze.md・live-channel-write.md・static-generation.md、`scripts/tests/test_actions_status.py`（例の文字列。消さない）、docs/decisions/automation.md（決定の記録。変えない）
- 未マージの work/ ブランチで `.github/`・`docs/notes/github-actions.md` を変えるものは無い

### 手順1: 3本の削除（9897585c8）

- `git rm` で3本を消した。スクリプト（`sync_books_calendar.py`・`write_live_channel_candidate.py`・`check_leagues_dropped.py`）は残した
- docs: github-actions.md の表の3行を消した。books-freeze.md の「止めたもの」の表に「2026-10-14 にファイルごと削除した（#564）」、再開の手順5を「git の履歴から戻す（`git log --diff-filter=D --oneline -- .github/workflows/sync-books-calendar.yml` で削除したコミットを探し、`git checkout <そのコミット>^ -- …` で戻す）」に置き換えた。books-calendar.md・live-channel-write.md・static-generation.md は削除した旨の1行（または括弧書き）に置き換えた。live-channel-write.md の「このワークフローは残す（2026-09-28 平野さんの判断）」は、2026-10-14 の削除の決定で置き換わったので消した
- `python3 -m unittest discover -s scripts/tests`（`test_workflows_table.py` を含む）: OK

### 手順2: regenerate-page（1a7d7a36c・39acc9b5a）

- job `regenerate` に concurrency（組 `regenerate-page-${{ github.ref }}`・`cancel-in-progress: false`）を付けた（1a7d7a36c）
- #132: 「対象ページを再生成」の `inputs.target_page`・`github.event.before`（あわせて `github.event_name`・`github.sha`）を env（`TARGET`・`BEFORE`・`EVENT_NAME`・`AFTER`）で受けた。`TARGET` は `set -f` の下でクォートせずに単語分割して渡す。「変更をコミット・push」の `steps.regen.outputs.files`（あわせて `github.ref_name`）を env（`FILES`・`BRANCH`）で受け、`read -r -a` で配列にして `git add` に渡した（`sitemap*.xml` のシェルのグロブは残すため `set -f` にはしない）。`wayhome/index.html` の有無は `[[ … ]]` で見る
- ほかに `run:` へ直接展開しているのは「帰り道の取り込みを調べる」の `--run-url "${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"` だけで、`inputs.*`・`steps.*.outputs.*`・`github.event.*` ではないため変えていない
- **`queue: max` を足した（39acc9b5a）**: concurrency の既定では待ちは1本で、3本目が来ると待っていた本が取り消される（`cancel-in-progress: false` でも）。毎朝 update-live-channel（04:00）・update-sns-book（04:10、`all`）・regenerate-saikyo（04:30 の検知の後）が続けて呼ぶので、2本目が取り消されうる。sync-logs.yml（#509）と同じ `queue: max` で来た順に待たせる
- 生成物のコミットの道筋を通すため、手元で `resource_dictionary resource_efficiency` を生成して差分が無いことを確かめたうえで、試験のコミット cf54ea34e で `resource_dictionary.html` の末尾に1行（HTML のコメント）を足した

作業ブランチの手動実行:

| run | 入力 | 結果 | 要点 |
|---|---|---|---|
| #316（38519491803） https://github.com/retroeater/mj/actions/runs/38519491803 | `target_page: resource_dictionary`（concurrency の組だけの版 cf54ea34e） | success | 生成物のコミット df1ddabaa（bot、「chore: regenerate resource_dictionary.html dic/ via GitHub Actions」）が作業ブランチに入った。中身は試験の1行の削除と `sitemap-pages.xml` の resource_dictionary の lastmod（2026-10-10 → 2026-10-14）だけ |
| #317（38519612607） https://github.com/retroeater/mj/actions/runs/38519612607 | `target_page: resource_dictionary resource_efficiency`（`queue: max` の版 39acc9b5a） | success | `FILES: resource_dictionary.html dic/ resource_efficiency.html`。差分なし（「変更なし」） |
| #318（38519614692） https://github.com/retroeater/mj/actions/runs/38519614692 | `target_page: resource_efficiency`（#317 の直後に起動） | success | 差分なし |
| #319（38519617304） https://github.com/retroeater/mj/actions/runs/38519617304 | `target_page: resource_dictionary`（#318 の直後に起動） | success | 差分なし |
| regenerate-saikyo #11（38519804715） https://github.com/retroeater/mj/actions/runs/38519804715 | `abema: dry_run`・`regenerate: true` | success | 呼ぶ側の組 `regenerate-saikyo` と呼ばれる側の組 `regenerate-page-<ref>` で詰まらない（abema 06:01:22〜06:01:55、regenerate 06:01:57〜06:02:39 UTC）。差分なし |

- concurrency の待ち（UTC）: #317 の job 05:59:27〜06:00:00、#318 は 05:59:26 に作られて 06:00:01 に開始（#317 の終了を待った）、#319 は 05:59:28 に作られて 06:00:25 に開始（#318 の終了 06:00:23 を待った）。3本とも取り消されていない（`queue: max` が効いた。指示の「続けて2回」を3回にして、3本目で待ちが取り消されないことも見た）
- WFL-01 の未確認の項目「呼ばれる側の concurrency が呼ぶ側と詰まらないか」: 組の名前が違えば詰まらない（regenerate-saikyo #11）
- 生成物のコミット: 作業ブランチの差し引き（1a7d7a36c → 39acc9b5a）で、残る差分は `sitemap-pages.xml` の resource_dictionary の lastmod（2026-10-10 → 2026-10-14）だけ。`resource_dictionary.html` の中身は元どおり（判断待ちへ）

### 手順3: docs・#263・#132

- docs/notes/github-actions.md の「regenerate-page.yml」節に concurrency（`queue: max`、呼ばれたときも効く）と #132 の受け方を足した（3433fb8b2）
- #263・#132 にコメントした（クローズしていない）: https://github.com/retroeater/mj/issues/263#issuecomment-6162022289 ・ https://github.com/retroeater/mj/issues/132#issuecomment-6162022979

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-WFL-11
- ブランチ: work/1014-wfl-s3
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s3/docs/logs/CHAT-1014-WFL-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s3
- 確認用URL: なし（`.github/` と docs の変更。試験で触った resource_dictionary.html は元の中身に戻っている）
- マージ: 未（判断待ち。指示の「マージ:」の行のとおり）
- issue: #564・#263・#132（#263・#132 にコメント。クローズしていない）
- 判断が必要なこと:
  - マージの可否: 3本の削除と docs、regenerate-page の concurrency（`queue: max` を含む。指示の案に無かったもの。経過「手順2」）と入力の env 化。作業ブランチの手動実行5回すべて success
  - 作業ブランチに入った生成物のコミット（試験の cf54ea34e と bot の df1ddabaa）: `resource_dictionary.html` の中身は元どおりだが、このファイルを触るコミットが今日付けで入ったため、`sitemap-pages.xml` の lastmod が 2026-10-10 → 2026-10-14 になっている（`--from-git` は最終コミット日から作るので、戻すコミットを足しても今日付けのまま。lastmod は手で書き換えない規則）。
    比較: (a) このままマージする（中身の変わらないページの lastmod が1件だけ新しくなる。害は小さい）／(b) cloudflare から新しいブランチ（例 work/1014-wfl-s3b）を作り、試験と bot の2コミットを除いた変更だけを入れ直してマージする（lastmod は動かない。手動実行の run は今のブランチのコミットに結び付いたまま。ブランチ名の承認が要る）。
    推奨 (b): 変えていないページの lastmod を検索エンジンに出さずに済み、入れ直す変更は `.github/`・docs だけで差し引きが確かめやすい
- 未確認の項目:
  - 消したワークフローが mj-logs の `actions/status.md` に古い節として残るか（マージ後に読む。WFL-01 からの未確認の項目）
  - 毎朝の3本（update-live-channel・update-sns-book・regenerate-saikyo）から続けて呼ばれたときに来た順に動くかは、マージ後の朝（2026-10-15 04:00〜05:00 JST）の実行で確かめる（今回は手動の3本で確かめた）
  - regenerate-page の週次の `all`（月曜 05:37 JST）の初回は 2026-10-19（月）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
