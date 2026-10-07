# CHAT-1005-RVW-21

- 着手日時: 2026-10-07
- 対象issue: #298（実装3）
- ブランチ: work/1007-rvw-sync3
- 着手時HEAD: 187e5246

## 指示

【Claude作成】Claude Code 向け指示：#298 の実装3。Worker の cron を1分ごとにし、mj に push があった直後だけ mj-logs の同期を起動する。あわせて actions/status.md を中身が変わったときだけ書き出す。マージして並走を始める Chat-Ref: CHAT-1005-RVW-21 マージ: 承認済み（チャットで） 貼る時機: CHAT-1005-RVW-20 の後（mj-logs の `sync-from-mj.yml` が置かれ、手動実行で確かめ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-sync3 の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-sync3 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-sync3 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-RVW-18 の「2. 設計」と RVW-20 のログを読む。docs/notes/scheduler-worker.md を読む。

目的
作業ログを mj-logs へ写す仕組みを mj-logs 側で動かす（#298 の根本策）。その実装3として、Worker `mj-scheduler` から mj-logs の `sync-from-mj.yml` を、mj に push があった直後だけ起動するようにする（W1'）。これで mj 側の `sync-logs.yml` と並走が始まる。`sync-logs.yml` はまだ変えない。
決定（2026-10-07、平野さん）

* 起動の間隔は W1': Worker の cron を1分ごとにし、mj の `pushed_at` が直近数分以内のときだけ mj-logs の同期を起動する
* RVW-18 の設計のほかの点（すべて写す。並走 → mj 側を止める → 後で消す。Worker のトークンの対象に mj-logs を足す〈済み〉。公開の実行ログにブランチ名を出さない）
* 実装3はマージまで進めてよい

前提（チャット側。平野さんの決定ではない）

* `actions/status.md` の扱い（RVW-20 が #298 に移した論点。チャット側の案で進め、平野さんがこの指示を貼ることで認める）: `actions_status.py` は、「書き出した時刻」の行以外に変わりが無ければ書き出さない（ファイルを変えない）。時刻は mj-logs のコミットの時刻で分かる。これで、1分ごとの起動でもログの写しや Actions の結果が変わったときだけコミットができる。mj の sync-logs.yml も同じスクリプトを使うので同じ動きになる（害は無い）。テストを足す
* Worker の変更（`workers/` の `mj-scheduler`。docs/notes/scheduler-worker.md）:
   * cron を `*/5 * * * *` から `* * * * *` にする。今の `dispatchDue()` が、5分の幅で「due」を判定していれば、1分ごとの起動で同じ行が最大5回送られる。判定が「その分だけ」になっているか、または直近の起動から重複しない形になっているかを実物で確かめ、要れば直す（要確認。テストがあれば足す）
   * 毎回の起動で: `GET /repos/retroeater/mj`（トークンは今の `GITHUB_TOKEN`。mj の metadata read で読める）の `pushed_at` を読み、今から3分以内なら mj-logs の `sync-from-mj.yml` に `workflow_dispatch`（ref `main`）を送る。3分より前なら送らない。送った・送らなかった・失敗したを、今の Worker のログの出し方に合わせて出す（1分ごとに出ると Workers Logs の1日の上限〈scheduler-worker.md の記録〉に近づかないか、要確認。送らなかったときは出さない案）
   * 起動先が mj と mj-logs の2つになるので、`schedule.json` の行に任意の `repository` を持たせる形（RVW-18 の案）か、同期の起動だけを別の関数にする形か、小さい方を選ぶ。`vars`・secret の名前は変えない（トークンは1つ。対象に mj-logs が足されている）
   * 1分ごとの起動で Free プランのリクエスト数（1日10万）に対し 1,440 回。`GET /repos` が毎回1回、dispatch が push の直後だけ。GitHub API の上限（トークンあたり毎時5,000）に対しても小さい
   * 朝の確かめ（#506）の対象に同期の起動を入れない（回数が多い）。mj-logs の最新のコミットの時刻で足りる
* mj-logs の `sync-from-mj.yml` は変えない（`workflow_dispatch` を受けるだけ）。mj-logs への push は要らない
* 並走の確かめ（手順3）: マージの後、Worker が Workers Builds で配備される（check-run「Workers Builds: mj-scheduler」）。配備の後、この指示のログの push（mj への push）で、2〜3分以内に mj-logs に `sync: mj …` のコミット（`workflow_dispatch` 契機の実行）ができることを、mj-logs の実行の一覧と commits で確かめる。mj 側の sync-logs.yml の写し（`sync: cloudflare …`・`sync: work/… …`）も続く（並走）
* 文書: docs/notes/scheduler-worker.md に、1分ごとの起動と同期の起動の仕組みを書く。docs/notes/static-generation.md の mj-logs 側のワークフローの行を「Worker から起動（並走中）」に直す。CLAUDE.md は変えない
* 取り込みの衝突の扱い: `docs/decisions/operations.md` の追記どうし、`docs/notes/` の隣り合う行で両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

手順

1. 確かめる: 未マージの `work/` ブランチが `workers/`・`scripts/actions_status.py`・`.github/workflows/` を変えていないか。Worker の今の `dispatchDue()` の due の判定の幅（上の要確認）、ログの出し方、テストの有無。`actions_status.py` の書き出しの作り（時刻の行の位置、比較のしやすさ）
2. 作る: (a) `actions_status.py` の「変わりが無ければ書かない」とテスト (b) Worker の cron・due の判定・同期の起動とテスト。`python3 -m unittest discover -s scripts/tests` と Worker のテスト（あれば）を通す。文書を直し、決定を docs/decisions/operations.md に足す
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおりマージし、check-run（Workers Builds: mj-scheduler・assets-check など）を待つ（上限15分）。配備の後、ログの push で mj-logs に `workflow_dispatch` 契機の同期の実行とコミットができることを確かめ、push から mj-logs のコミットまでの時間を測る（2〜3回）。結果を表でログに書き、docs/logs のみの追いの push で入れる。#298 に「実装3 済み（並走開始、確かめた遅れ）」をコメントする（末尾に Chat-Ref の行）

止まる条件

* 未マージの `work/` ブランチが `workers/`・`actions_status.py`・`.github/workflows/` を変えている
* due の判定が1分ごとの起動で重複する作りで、直し方が大きい（直さずに報告に書いて止まる。cron は変えない）
* mj-logs の `sync-from-mj.yml` や mj の `sync-logs.yml` を変える必要が出た（変えずに報告に書く）
* check-run が失敗した。配備の後、ログの push から10分たっても mj-logs に同期の実行が起きない（原因を1回だけ読む。Worker の直しで済む明らかな誤りなら直して再度マージしてよい。それでも起きなければ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、push から mj-logs のコミットまでの時間の表を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-21 のコミットなし。work/1007-rvw-sync3 はローカル・リモートとも無く、origin/cloudflare（187e5246）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている。RVW-18・RVW-20 のログは同じセッションで書いたもので、読み直した。scheduler-worker.md は読んだ

## 報告

- 状態: 作業中
- ブランチ: work/1007-rvw-sync3
- ログ: https://github.com/retroeater/mj/blob/work/1007-rvw-sync3/docs/logs/CHAT-1005-RVW-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-sync3
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 187e5246）: https://github.com/retroeater/mj-logs/tree/main/guide/187e5246

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/187e5246/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
