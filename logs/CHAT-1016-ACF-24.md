# CHAT-1016-ACF-24

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-acf-retry
- 着手時HEAD: 98839f385650452af4838469d0e2296d46994b27

## 指示

【Claude作成】Claude Code 向け指示：actions_status.py の GitHub API 呼び出しに取り直しを足し、mj-logs「mj の作業ログを写す」が一時的な 502 で failure になるのを止める Chat-Ref: CHAT-1016-ACF-24 マージ: テストが通れば完了報告のうえ cloudflare へ入れてよい（変えるのは scripts/actions_status.py とそのテスト・関係する docs だけ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-acf-retry の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16〜21・CHAT-1015-ACF-22〜23 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1016-acf-retry を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-acf-retry origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
mj-logs の「mj の作業ログを写す」（sync-from-mj.yml）が、2026-10-14 #2034（run 38556984009）と 2026-10-16 01:48 JST #2417（run 38772059290）で failure になった。2回とも step「写す・書き出す・push」の中で、mj の `scripts/actions_status.py` の `get()` が GitHub API から `HTTP Error 502: Bad Gateway` を受けて例外で止まった（`main()` の `all_runs = get(f"/repos/{repo}/actions/workflows/{wf['id']}/runs?per_page=…")`、168行付近）。GitHub 側の一時的な失敗で、次の回はどちらも success、写しそこねたログも次の回で入っている。`get()` に取り直しが無いため、1回の実行でワークフローの数（17件）以上ある API 呼び出しのうち1つでも 5xx が返ると、失敗の通知が来る。取り直しを足して止める。
決定（2026-10-16、平野さん）

* 案 (a): `get()` に取り直しを足す。5xx とタイムアウト（接続の失敗を含む）のときだけ、5秒・10秒あけて最大3回呼ぶ。4xx（401・403・404・422 など）は取り直さずにすぐ例外にする
* 何もしない案（月に数回の失敗の通知を受け入れる）は採らない

前提（チャット側。平野さんの決定ではない。2026-10-16 09:40 JST に REST・Chrome で確かめた）

* 2回の failure のログ（Chrome で読んだ）: #2417 は「写した 2 件・消した 0 件」の直後に Traceback（actions_status.py 168 → 148 `urllib.request.urlopen(req, timeout=30)` → `HTTPError: HTTP Error 502: Bad Gateway`）。#2034 は1回目の push が拒否されて（fetch first）、やり直しの2回目で同じ 502
* mj-logs の最近の100回は success 89・cancelled 10・failure 1（#2417）
* `actions_status.py` を呼ぶのは mj-logs の `.github/workflows/sync-from-mj.yml`（70行付近）だけ（mj の .github/workflows・workers/ には無い）。`scripts/morning_report.py` は `import actions_status as a` で関数を使うが、API は呼ばない見込み（着手前に確かめる）
* cloudflare の今の版は 1447edda。`actions_status.py` の直近の変更は 6309ec10（WFL「毎朝の起動を run-daily.yml に切り替える」）。今ある work/ ブランチ（work/1015-wfl-s5〜s7 を含む）に actions_status.py・morning_report.py の変更は無い（compare で確かめた）
* テストは `scripts/tests/test_actions_status.py`・`scripts/tests/test_morning_report.py`
* mj-logs の sync-from-mj.yml は変えない（push の拒否の3回のやり直しは今のまま）
* WFL のチャット（Actions の整理）には、この変更を伝言済み（2026-10-16）
* 使う skill は無い

手順

1. 同じ件を扱う issue・ログ（mj-logs の `logs/` を run 番号 38772059290・38556984009、「502」で検索）が無いことを確かめる。作業ブランチを用意した時点で、cloudflare と work/ ブランチに actions_status.py・morning_report.py の未マージの変更が無いことを確かめ直す
2. `get()` に取り直しを足す。
   * 取り直すのは `urllib.error.HTTPError` の 500〜599、`urllib.error.URLError`、`TimeoutError`（socket.timeout を含む）だけ
   * 待ちは 5秒・10秒、最大3回。3回とも失敗したら最後の例外をそのまま上げる（今と同じく step は failure になる）
   * 取り直すたびに1行（何回目・理由・パス）を標準エラーに出す。トークンは出さない
   * 待つ関数は差し替えられるようにして、テストでは実際に待たない
3. テストを足す。
   * 502 → 成功で値が返る
   * 3回とも 502 で例外になり、呼び出しが3回
   * 404 は1回で例外（取り直さない）
   * タイムアウト → 成功
   * 既存のテスト（test_actions_status.py・test_morning_report.py）がすべて通ることも確かめる
4. 関係する docs（actions/status.md の書き出しを説明している docs/notes の箇所。どこかは着手時に grep して決める）に「GitHub API の 5xx・タイムアウトは最大3回取り直す（CHAT-1016-ACF-24）」を1行足す。決定を docs/decisions/automation.md に足す。cloudflare へマージする
5. マージ後、mj-logs の次の「mj の作業ログを写す」の回（mj への push の直後に Worker が起動する）が success であることを、REST で確かめてログに書く（run 番号）

止まる条件

* 同じ件を扱う issue・ログがある
* cloudflare か work/ ブランチに、actions_status.py・morning_report.py の未マージの変更がある
* `morning_report.py` が API を直接呼んでいて、取り直しを足す場所が `get()` だけでは済まないと分かった（呼んでいる場所を報告して止まる）
* 差分が scripts/actions_status.py・scripts/tests/・docs/ 以外に出た
* テストが通らない（ネットワークに頼るテストの揺れは1回だけ流し直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（変えた行・テストの結果・マージ後の確かめの run 番号）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-ACF-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-ACF-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- Chat-Ref の重複: `git log --all --grep="CHAT-1016-ACF-24"` は 0 件。`work/1016-acf-retry` はローカル・リモートとも無く、`git checkout -b work/1016-acf-retry origin/cloudflare`（98839f385。指示文の前提の 1447edda より新しい）
- 手順1: cloudflare の docs/logs を run 番号 38772059290・38556984009 で検索して当たりなし。「502」「Bad Gateway」「actions_status」で当たったログに同じ主題のもの（取り直し）は無い（CHAT-1005-RUN-10 の「check-run の取得で HTTP 502 が1回」は別の件）。mj の issue（更新順の100件）の題・本文にも同じ主題は無い。mj-logs の `logs/` は cloudflare の docs/logs の写しなので、同じ検索で足りるとした
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）に `scripts/actions_status.py`・`scripts/morning_report.py` の変更は無い。cloudflare での直近の変更は 6309ec101
- `scripts/morning_report.py` に `urllib`・`requests` の使用は無く、API を直接呼ばない（URL は表示用の定数だけ）。取り直しは `get()` だけで足りる
- 手順3（テストを先に書いた）: `scripts/tests/test_actions_status.py` に `GetRetryTest` 5件（502→成功／502が3回で例外・呼び出し3回・待ち [5, 10]／404 は1回で例外／タイムアウト→成功／URLError→成功）。`urlopen` を差し替え、待ちは `sleep` 引数に `list.append` を渡して実際には待たない。標準エラーにパスが出てトークンが出ないことも確かめる。直す前のコードで5件とも落ちる（`FAILED (failures=5)`）ことを確かめた
- 手順2: `scripts/actions_status.py`
  - `RETRY_WAITS = (5, 10)` を足した
  - `retryable(e)`: `HTTPError` は 500〜599 だけ真。ほかは `URLError`・`TimeoutError`（socket.timeout は同じもの）なら真
  - `get(path, token, sleep=time.sleep)`: 最大3回呼ぶ。取り直すたびに「取り直し N回目の失敗（例外）: パス。W秒後に再び呼ぶ」を標準エラーへ。3回目の失敗と取り直さない例外はそのまま上げる
- テスト: `python3 -m unittest discover -s scripts/tests` は 1245件 OK。test_actions_status.py・test_morning_report.py も OK。実 API に `get('/repos/retroeater/mj/actions/workflows?per_page=100')` を1回呼んで 21件が返ることも確かめた
- 手順4: docs/notes/github-actions.md の sync-from-mj.yml の項目の直後に1行（5xx・タイムアウトは5秒・10秒あけて最大3回取り直す。4xx はすぐ失敗）。決定を docs/decisions/automation.md に足した
- 差分は scripts/actions_status.py・scripts/tests/test_actions_status.py・docs/ だけ（成果物は 45d6811de）

## 報告

- 状態: 完了
- ブランチ: work/1016-acf-retry
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-ACF-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-acf-retry
- 確認用URL: なし
- マージ: 済（SHA は下の追記）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6e845906）: https://github.com/retroeater/mj-logs/tree/main/guide/6e845906

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e845906/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
