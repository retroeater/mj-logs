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

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-s3
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s3/docs/logs/CHAT-1014-WFL-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s3
- 確認用URL: なし
- マージ: 未
- issue: #564・#263・#132
- 判断が必要なこと: なし
- 未確認の項目: なし
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
