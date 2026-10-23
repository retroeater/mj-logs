# CHAT-1023-OHA-08

- 着手日時: 2026-10-23
- 対象issue: #563
- ブランチ: work/1023-oha
- 着手時HEAD: 8255bda

## 指示

【Claude作成】Claude Code 向け指示：morning.md の所要時間の判定を絞り、失敗の行に飛ばしたページ名を添える Chat-Ref: CHAT-1023-OHA-08 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-oha の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-oha を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-oha origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#563 の残り（カレンダーの予定「段③のマージ後: morning.md の失敗の行に飛ばしたページ名を添える＋所要時間の閾値の見直し」）。mj-logs の morning.md の「3. 所要時間の異常」の誤検知を減らし、「1. 失敗など」で regenerate-page.yml がページを飛ばした回は、どのページを飛ばしたかを morning.md だけで読めるようにする。
決定（2026-10-23、平野さん）

* 3節の判定を2点直す（チャット側が示した案 A+B）
   * A: 「直近20回の中央値の3倍以上」の判定に、「中央値＋5分以上」も満たすことを加える（両方を満たしたときだけ出す）。「10分以上」の絶対値の判定（ほかの実行の中央値が10分未満のワークフローだけ、CHAT-1015-WFL-18）は今のまま
   * B: 3節に出すのは cloudflare の実行だけにする（作業ブランチ〈work/… など〉の実行は3節に出さない）
* 飛ばしたページ名を添える件は今回やる: regenerate-page.yml の「飛ばしたページを報告」の注釈（`::error::`）の1行目に飛ばしたページ名を入れ、morning_report.py は、失敗したステップが「飛ばしたページを報告」のとき、その check-run の annotations（`GET /repos/{repo}/check-runs/{job の id}/annotations`）の message の1行目を失敗の行に添える
* この指示のマージは承認済み（下の止まる条件つき）

前提（チャット側。平野さんの決定ではない）

* 予定の説明欄の (1)〜(5) のうち、(1) 失敗の行のジョブ名・(2) run-daily の「10分以上」の扱いは CHAT-1015-WFL-18、(3) 2節の保険の予約実行は CHAT-1017-WFL-27 で済み、残りの保険は WKP-04 で外れる見込み。チャット側が 2026-10-23 の origin/cloudflare の `scripts/morning_report.py`（`failed_jobs_note()`・`render()` の `too_long`・`covered_by_daily()`）と今朝の morning.md で確かめた。この指示ではこれらを変えない
* 今の注釈は固定の文（「生成に失敗したページを飛ばし…ジョブのサマリにあります(#533)」）で、ページ名はジョブのサマリ（`scripts/regenerate.py` の `report_skipped()`）にしか無い（チャット側が 2026-10-23 の origin/cloudflare で確かめた）。サマリは REST で読めないため注釈に入れる
* 試算（チャット側、mj-logs の actions/morning.md の 10/15〜10/23 の毎朝の版 9 回、3節の延べ 24 行）: A+B で残るのは 5 行（10/17 12:01 convert-x-posts 28分46秒・10/19 23:30 update-sns-book 7分14秒・10/21 12:04 / 12:28 / 13:32 regenerate-page 8分56秒 / 10分27秒 / 11分01秒）。消えるのは、中央値1分前後のワークフローの3〜5分の行（regenerate-page の push・update-sns-book・convert-x-posts 1分43秒）と作業ブランチの実行の行
* 注釈の1行目の形の案: 「生成に失敗したページを飛ばしました: <ページ名>, <ページ名>（理由はジョブのサマリ、#533）」。2行目以降に今の文を残してよい。ページ名の渡し方（`regenerate.py` が `GITHUB_OUTPUT` に出す等）は Code が選んでよい
* 失敗の行への添え方の案: 今のジョブ名の後に「：<注釈の1行目>」。annotations を引くのは、失敗したステップ名が「飛ばしたページを報告」のジョブだけ（ほかでは API を呼ばない）。引けなかったときは添えずに今の行のまま出す（morning.md の書き出しを止めない）
* `reusable` で呼ばれたとき（update-sns-book・fetch-youtube・regenerate-saikyo などからの regenerate-page）もジョブのステップ名は同じ（要確認）
* 閾値の 5 分は定数にする（`SLOW_MARGIN` など。名前は Code が選んでよい）。3節の見出しの文も判定に合わせて直す
* 置き場所の文書は docs/notes/github-actions.md の morning.md の説明（要確認）。決定は docs/decisions/automation.md

手順

1. #563・#564 の状態と着手中コメントを確かめ、#563 に着手中のコメントを書く（本文・ラベルは変えない）。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/morning_report.py`・`scripts/actions_status.py`・`scripts/regenerate.py`・`.github/workflows/regenerate-page.yml`・`scripts/tests/test_morning_report.py` の同じ関数・同じステップを変えていないか確かめる。上の「前提」の1つ目・2つ目を origin/cloudflare で確かめ、食い違えば止まる
2. 3節の判定（A・B）と、飛ばしたページ名の注釈・添え書きを実装する。`scripts/tests/` に検査を足す（A の境界〈3倍以上でも中央値＋5分未満なら出さない／両方満たせば出す〉、B〈作業ブランチの実行は3節に出さない。1節には今までどおり出る〉、注釈あり・なし・取得失敗の添え書き）。`python3 -m unittest discover -s scripts/tests` を通す。修正前のコードで新しい検査が落ちることも確かめる（CLAUDE.md「判断・作業の原則」）
3. 確かめ:
   * 今の API の応答で morning.md を作業ブランチのコードと origin/cloudflare のコードで1回ずつ作り（書き出し先は作業ファイルの置き場所。mj-logs には書かない）、3節の違いを全行ログの `## 経過` に書く。消えた行・残った行がすべて決定 A・B で説明できること
   * regenerate-page.yml の変更は docs/notes/github-actions.md「ワークフローを変更したとき」に従い、作業ブランチの手動実行で「飛ばしたページを報告」の注釈の1行目にページ名が出ることを確かめる。ページを飛ばすための試験の変更は cloudflare・本番に書かない形に限る（入力 `dry_run` で書かない道筋に「飛ばしたページを報告」が動くか〈要確認〉を先に確かめる。試験のためのコミットはマージの前に取り除き、origin/cloudflare との差分に残っていないことをログに書く。2026-10-10 の RGN-02 の試験のコミットが本番の sitemap に写った件〈docs/decisions/automation.md〉を繰り返さない）。その実行の annotations を API で引き、作業ブランチの morning_report.py の添え書きの行をログに引用する
   * 確かめが済んだら cloudflare へマージし、docs と docs/decisions/automation.md に決定を足し、#563 にこのログの `## 報告` の「結果」を引用してコメントする（#563 は閉じない。閉じるかはチャット側が決める）

止まる条件

* 前提が食い違う（済みのはずの (1)〜(3) が origin/cloudflare に無い、注釈が既にページ名を含む 等）
* #563・#564 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが上のファイルの同じ関数・同じステップを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3の1つ目で、決定 A・B で説明できない3節の違いが1行でもある
* ページを飛ばす試験が cloudflare・本番に書かずにはできない（試験をせずに止まり、案を「判断が必要なこと」に書く）。作業ブランチの手動実行が、試験で飛ばしたページ以外の理由で failure
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。マージ後の mj-logs の morning.md の初回の書き出し（次の同期）は「未確認の項目」に書いてよい
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-OHA-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-OHA-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（手順0・1）

- 手順0: このログの `## 指示` の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- Chat-Ref: `CHAT-1023-OHA-08` のコミットは全ブランチに無い。OHA は 1014-OHA-02〜07 の続きの番号で、最初の指示ではない
- ブランチ: `origin/work/1023-oha` もローカルも無かったので `git checkout -b work/1023-oha origin/cloudflare`（8255bda）
- #563: open、着手中のコメントは無し（コメントは CHAT-1015-MRF-01 の1件だけ）。着手中のコメントを書いた（issuecomment-6354149068）
- #564: 最後の着手中は CHAT-1021-WKP-03 で、同じ人がその後に完了のコメントを書いている。着手中のものは無し
- 未マージの `work/` ブランチ（7本）: `scripts/morning_report.py`・`scripts/actions_status.py`・`scripts/regenerate.py`・`.github/workflows/regenerate-page.yml`・`scripts/tests/test_morning_report.py` のどれも変えていない（`git diff --name-only origin/cloudflare...<b>` で0件）
- 前提1つ目: origin/cloudflare の `morning_report.py` に `failed_jobs_note()`（ジョブ名）・`render()` の `too_long`（ほかの実行の中央値が10分未満の本だけ絶対値で見る）・`covered_by_daily()` がある。食い違いなし
- 前提2つ目: 「飛ばしたページを報告」の `::error::` は固定の文で、ページ名は `regenerate.py` の `report_skipped()` がジョブのサマリにだけ書く。食い違いなし
- reusable のときのステップ名（要確認の件）: 過去の実行で「飛ばしたページを報告」が failure のジョブを API で探した。regenerate-page の push（`regenerate`）のほか、update-sns-book の `regenerate / regenerate`（run 38621492985・38335781712）でも同じステップ名。注釈は check-run（ジョブの id）の annotations に、`Process completed with exit code 1.` やほかのステップの warning と並んで出る。選ぶ注釈は message の先頭で見分ける
- `dry_run` の道筋（要確認の件）: 「飛ばしたページを報告」の `if` は `!cancelled() && steps.regen.outputs.skipped == 'true'` で、`dry_run` を見ていない。`dry_run: true` でも再生成のステップは動き、飛ばしがあれば報告のステップも動く（コミット・サイトマップの3ステップは `inputs.dry_run != true` で止まる）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 49b9ce27）: https://github.com/retroeater/mj-logs/tree/main/guide/49b9ce27

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8255bdad.md
