# CHAT-1010-RGN-05

- 着手日時: 2026-10-10
- 対象issue: #538
- ブランチ: work/1010-rgn
- 着手時HEAD: 5697aa0c

## 指示

【Claude作成】Claude Code 向け指示：#538 の対応。ランナーの python3 で動く4本のワークフローに setup-python 3.12 を足し、Ubuntu 26.04 への移行（10/19〜）の前に入れる Chat-Ref: CHAT-1010-RGN-05 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#538 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

目的
ubuntu-latest が 2026-10-19 から Ubuntu 26.04（既定の Python 3.14）へ移り始める。`setup-python` を使わずランナーの `python3` で `scripts/` を動かすワークフローを、ほかと同じ 3.12 に固定して、移行で Python の版が変わらないようにする。
決定（2026-10-10、平野さん）

* ubuntu-latest のまま移行を受ける（`ubuntu-24.04` などに固定しない）
* ランナーの `python3` で動く4本（mj の `assets-check.yml`・`delete-merged-branches.yml`・停止中の `sync-logs.yml`、mj-logs の `sync-from-mj.yml`）に `setup-python` の 3.12 を足し、ほかの14本とそろえる
* #538 の期日は 2026-10-18（移行の始まる前日）。カレンダーには登録済み
* この指示のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* 対象の4本と行は CHAT-1010-RGN-04 のログの「手順2」のとおり（要確認: `assets-check.yml` 131行・`delete-merged-branches.yml` 71行・`sync-logs.yml` 76〜103行、mj-logs の `sync-from-mj.yml`）。ほかの14本と同じ書き方（`actions/setup-python@v5`・`python-version: '3.12'`）にそろえる。actions の版上げ（Node 24 対応）は #308 で扱い、この指示ではしない
* mj-logs は別のリポジトリ（public）。このセッションから mj-logs へ push できるか（権限）を先に確かめる。できなければ mj-logs の分は変えず、平野さんが GitHub の画面で直せるように、変える行と差分をログの `## 報告` の「判断が必要なこと」に書く（mj の3本はそのまま進めてマージしてよい）
* `sync-logs.yml` は停止中（`workflow_dispatch` のみ）。書き換えるだけで、手動実行はしない（mj-logs への写しが二重に動くのを避けるため。要確認: 停止の扱いは docs/notes/static-generation.md「ワークフローの一覧」）
* mj-logs の `sync-from-mj.yml` を変えたときは、mj-logs で手動実行して success を確かめる（写しの本番なので、失敗したら直前の版に戻す）

手順

1. 確かめる: 上の「前提」の（要確認）を実物で確かめる。docs/notes/branch-operations.md「ワークフローを変更したとき」を読む。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、対象の3本の同じ行を変えている、または取り込みで衝突するものが無いか確かめる（別の行の変更〈例: work/1008-hou の `assets-check.yml` の許可するディレクトリの1行〉は止まる理由にしない。重なりの内容はログに書く）。mj-logs へ push できるかを確かめる。
2. 直して試す: mj の3本に `setup-python` の 3.12 を足す。作業ブランチで `assets-check.yml`（`workflow_dispatch` が無ければ、作業ブランチへの push で動いた実行）と `delete-merged-branches.yml`（手動実行の既定の dry_run）を動かし、ログで Python が 3.12 であること（`python3 --version` などを1行足してよい）と success を確かめる。`sync-logs.yml` は書き換えだけ。docs/notes/static-generation.md など、ランナーの `python3` に頼っていると書いた所があれば直す。mj-logs へ push できるなら `sync-from-mj.yml` も同じく直し、mj-logs で手動実行して success と 3.12 を確かめる。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と、push で動くワークフロー（assets-check など）の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。#538 に経過をコメントし、本文に「期日: 2026-10-18」を足す。上の「決定」を `docs/decisions/automation.md` に足す。mj-logs の分まで済み、移行の後に確かめることが残らなければ #538 を閉じる（「状況:」ラベルがあれば外す）。残るなら閉じず、残ることを #538 に書く。

止まる条件

* #538 が Closed、または他セッションの着手中コメントがある
* 対象の行が RGN-04 のログと違い、ランナーの `python3` で動くワークフローが4本のほかにもある（数が違えば、一覧を書いて止まる）
* 未マージの work/ ブランチが対象の3本の同じ行を変えている、または取り込みで衝突する
* 作業ブランチでの実行が success にならない、または Python が 3.12 にならない
* mj-logs の `sync-from-mj.yml` を変えた後の手動実行が success にならない（直前の版に戻したうえで止まる）
* マージ後のワークフローが、今回の変更による理由で失敗した（無関係な失敗なら、原因をログに書いたうえで残りの手順を進めてよい）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare・mj-logs への push が権限判定で拒否された（別の手段を試さずに止まる。mj-logs の分は上の前提のとおり平野さんに回す）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RGN-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RGN-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 識別子: `git log --all --grep="CHAT-1010-RGN-05"` は0件
- ブランチ: ローカルの `work/1010-rgn` は origin/cloudflare と同じ 5697aa0c（マージ済み）。`git merge --ff-only origin/cloudflare` は「Already up to date」
- #538: Open、コメント0件（他セッションの着手中コメント無し）。着手中のコメントを残した（issuecomment-6098733221）

### 手順1 確かめる

- `setup-python` を使わずに python を動かすワークフローは mj に3本（`assets-check.yml` 131行・`delete-merged-branches.yml` 71行・`sync-logs.yml` 76〜103行）で、RGN-04 のログのとおり。mj-logs のワークフローは `sync-from-mj.yml` の1本だけで、62・63行で `python3` を使う。合わせて4本
- `sync-logs.yml` は停止中（docs/notes/static-generation.md「ワークフローの一覧」: 2026-10-07 から `workflow_dispatch` のみ）
- 「ワークフローを変更したとき」を読んだ。`assets-check.yml` は `work/**` への push で動く（`docs/` 以外の変更）。`delete-merged-branches.yml` は `workflow_dispatch`（既定 dry_run）
- 未マージの work/ ブランチで対象の3本を変えるのは `work/1008-hou` だけ（`assets-check.yml` 74行の許可するディレクトリに `houou` を足す1行）。`git merge-file` で今回の変更と3方向で合わせ、衝突しないことを確かめた
- mj-logs: `add_repo`（push）で session に足し、`/home/user/mj-logs` にクローンした。`GET /repos/retroeater/mj-logs` の permissions は push: true

### 手順2 直して試す

- mj（9ad608dc）: 3本に `actions/setup-python@v5`（`python-version: '3.12'`）のステップを足した（assets-check はチェックアウトの直後、delete-merged-branches はチェックアウトの後、sync-logs は mj-logs のチェックアウトの後）。assets-check と delete-merged-branches の python を呼ぶ run に `python3 --version` を1行足した。sync-logs は書き換えだけで実行していない
- assets-check（run 38061293918、#3168、作業ブランチへの push）: success。「Pythonをセットアップ」success、ログに `Python 3.12.15`
- delete-merged-branches（run 38061297637、#25、手動実行 dry_run）: success。`Successfully set up CPython (3.12.15)`・`Python 3.12.15`、対象の一覧は dry-run で出ただけ
- docs/notes などにランナーの `python3` に頼ると書いた所は無かった（grep）
- mj-logs（910ca2c、main）: `sync-from-mj.yml` の「mj をクローン」の前に同じステップを足し、写すステップに `python3 --version` を1行足した。push の直前に `git fetch` と `git rebase` をした（写しのボットが毎分 push するため）
- mj-logs の手動実行を起動した。Worker からの起動も同じ workflow_dispatch で、910ca2c を含む 59cef398 で動いた run 38061698534（#743）: success、「Pythonをセットアップ」success、ログに `Python 3.12.15`、写しは「変更なし」で正常に終わった。#741 以前の版に戻す必要は無かった
- 並行のセッション: 実行中に `work/1010-rgn-py`（CHAT-1010-RGN-06、Python 3.14 への上げを起票するドキュメントだけの指示）が作られていた。ワークフローは変えないので止まる理由にはしない（`docs/decisions/automation.md` の追記が重なりうる）

### 取り込み

- マージの前に再 fetch したら、origin/cloudflare に RGN-06 の3コミット（1209291e〜706da098）が入っていた。`git merge origin/cloudflare` で `docs/decisions/automation.md` が衝突した（どちらも末尾への追記）。止まる条件の例外（追記どうし）のとおり両方を残して解いた。解いた後:

```
## 2026-10-10（CHAT-1010-RGN-05）

- ubuntu-latest のまま移行を受ける（`ubuntu-24.04` などに固定しない）（#538）
- ランナーの `python3` で動く4本（…）に `setup-python` の 3.12 を足し、ほかの14本とそろえる（#538）
- #538 の期日は 2026-10-18（移行の始まる前日）
- この指示のマージは承認済み（止まる条件つき）

## 2026-10-10（CHAT-1010-RGN-06）

- Python 3.14 へ上げる件を、#538 とは別の issue に起票する（#539）
- 対応は 2026-10-19 以降に行う
```

## 報告

- 状態: 対応中
- ブランチ: work/1010-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1010-rgn/docs/logs/CHAT-1010-RGN-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rgn
- 確認用URL: なし
- マージ: 未
- issue: #538
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 36af3d35）: https://github.com/retroeater/mj-logs/tree/main/guide/36af3d35

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/36af3d35/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/36af3d35.md
