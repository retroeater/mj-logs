# CHAT-1028-LOG-01

- 着手日時: 2026-10-28
- 対象issue: #367
- ブランチ: work/1028-log-367-weekly
- 着手時HEAD: 235fc4d99

## 指示

【Claude作成】Claude Code 向け指示：#367 の「ログ」を毎週月曜の朝に自動で取り込む（run-weekly に取得→変換を足す） Chat-Ref: CHAT-1028-LOG-01 マージ: 承認済み（チャットで。平野さんが「確かめが通ればマージまで」と言った、2026-10-28）。条件: テスト（`python3 -m unittest discover -s scripts/tests`・Worker のテスト〈変えた場合〉）が通る・作業ブランチで `run-weekly.yml` を手動実行し、足したジョブが書かずに（dry-run で）成功する・`fetch-x-posts.yml`・`convert-x-posts.yml` の手動実行の dry-run が成功する・cloudflare との差分が下の「見込み」で説明できる種類だけ・下の「止まる条件」に当たらない 貼る時機: いつでも（新しいセッションは任意。CHAT-1027-LOG-05 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-log-367-weekly の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1028-log-367-weekly を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-log-367-weekly origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」と「run-weekly.yml・run-monthly.yml」、docs/notes/scheduler-worker.md を読む。

目的
#367 の「ログ」のページを週に1回、月曜の朝に自動で更新する（2026-10-27 の決定）。毎週月曜 02:00 JST の `run-weekly.yml` に、X の新しい投稿の取得（`fetch-x-posts.yml`）と【2】への変換（`convert-x-posts.yml` の `mode=all`）を足し、その後の全ページの再生成（`youtube` のジョブ → `regenerate-page` の all）でページに出す。初回は 2026-11-02（月）。
決定（2026-10-27〜28、平野さん）

* 「ログ」は定期的な取り込みにする。頻度は週に1回、月曜の朝（全ページの再生成の前）
* 取り込みの対象は平野良栄（retroeater）・谷岡育夫（it_uesama）の2人。石立岳大は更新が終わっているので対象外
* 新しく取り込んだ行の「表示」は、判定が「飲食店」で要確認でない行は自動で Y、ほかは N（CHAT-1027-LOG-05 で実装済み）
* マージは、テストと作業ブランチでの手動実行（dry-run）の確かめが通れば行ってよい

前提（チャット側。平野さんの決定ではない）

* 作りの案（実物に合わせてよい。変えたら報告に書く）:
   * `run-weekly.yml` にジョブ `xposts` を足す: `fetch-x-posts.yml` → `convert-x-posts.yml`（`mode=all`）を `workflow_call` で順に呼ぶ。`youtube` のジョブ（fetch-youtube → regenerate-page の all）が `xposts` の後に動くよう `needs` に足し、`xposts` が失敗しても `!cancelled()` で動く（再生成は今あるデータで行う）
   * 予約の起動（`scheduled`）では書く、手動実行では dry-run（ほかの本と同じ `resolve-mode` の考え方）
   * 取得の期間の初めは、【1】の人ごとの最も新しい投稿日から（同じ投稿 ID は足さないので重なってよい）。人ごとに指定する入力を予約の起動でも渡せるよう、`fetch_x_posts.py` に「【1】の最も新しい投稿日から」の決め方を足す（案）。対象の2人は定数（またはワークフローの既定の入力）に持つ
   * 呼ばれる2本に `workflow_call` と入力 `scheduled` を足す。Worker の起動の表（`schedule.json`）は変えない（run-weekly の1行のまま）。GitHub 側の `schedule` は足さない
   * 失敗は run-weekly の失敗として、06:00 の朝の確かめ（#506）に出る。ほかの通知は足さない
* 費用の見込み: 週に X 20〜30件（約 $0.15）・Claude 約 $0.08・Places 50回前後（無料枠の内）。上限はスクリプトの定数のまま（X 1,200件・$6、Claude $3、Places 2,000回）
* `run-weekly.yml` は WKP 系列（Workers Paid の切り替え）が触っている。WKP-05（GitHub 側の `schedule` を外す指示）は 2026-11-01 の run-monthly の初回の後の予定（要確認）。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `run-weekly.yml`・`fetch-x-posts.yml`・`convert-x-posts.yml`・`schedule.json` の同じ行・ジョブを変えていないことを確かめる
* 見込み（cloudflare との差分の種類）: `.github/workflows/run-weekly.yml`・`fetch-x-posts.yml`・`convert-x-posts.yml`、`scripts/fetch_x_posts.py`（と `convert_x_posts.py`、要れば）とそのテスト、`docs/notes/github-actions.md`（表の3行と run-weekly の節）、mj-logs の運行表の元になるもの（あれば）、`docs/decisions/operations.md`（と automation.md、要れば）、`docs/logs/`。これ以外が出たら止まる

手順

1. #367 に他セッションの着手中コメントが無いことを確かめて着手中のコメントを残す（本文・状態・ラベルは変えない）。上の重なりを確かめ、作りを入れてテストを足し（期間の初めの決め方・対象の2人・予約の起動で書く／手動で書かない）、テストを通す
2. 作業ブランチで `fetch-x-posts.yml`・`convert-x-posts.yml` の手動実行（dry-run）と、`run-weekly.yml` の手動実行を行い、`xposts` のジョブが書かずに成功すること、`youtube` との順番が見込みどおりなことを確かめる（予約の起動の分岐は、判定の結果をログに出す作りにして、手動実行で判定の部分を確かめる）。docs と決定を書き、マージの条件を確かめて `git push origin work/1028-log-367-weekly:cloudflare` でマージする
3. マージ後の check-run を確かめ、#367 に結果のコメント（作り・初回 2026-11-02・費用の見込み・確かめ方）を残す。初回の予約の起動の確かめ方（mj-logs の `actions/status.md` の run-weekly の行と、【1】【2】の行数・本番の resource_logs.html の行数）をログに書く

止まる条件

* #367 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが上のファイルの同じ行・同じジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手動実行（dry-run）で、X・Claude・Places の API を呼ぶ、またはシートに書く見込みになった
* `schedule.json` を変える必要があると分かった（変えずに止まる）
* cloudflare との差分に見込みの外の種類がある、または cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて手順3を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LOG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LOG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1028-log-367-weekly
- ログ: https://github.com/retroeater/mj/blob/work/1028-log-367-weekly/docs/logs/CHAT-1028-LOG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-log-367-weekly
- 確認用URL: なし
- マージ: 未
- issue: #367
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj aee13b91）: https://github.com/retroeater/mj-logs/tree/main/guide/aee13b91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/aee13b91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/235fc4d9.md
