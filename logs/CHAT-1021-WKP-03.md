# CHAT-1021-WKP-03

- 着手日時: 2026-10-21
- 対象issue: #564
- ブランチ: work/1021-wkp
- 着手時HEAD: 0b519499b

## 指示

【Claude作成】Claude Code 向け指示：fetch-youtube を run-weekly に入れ、静的アセットの検査の上限を Paid の値に直し、Observability の料金の決定を記録する Chat-Ref: CHAT-1021-WKP-03 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1021-WKP-02 はマージ済み。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1021-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1021-WKP-01・02 の「判断が必要なこと」への回答。WKP-02 で外した fetch-youtube を run-weekly に入れ（初回 10/26 の予約の起動で確かめる）、WKP-01 で残した `scripts/check_asset_limits.py` の上限を Workers Paid の値に直し、Observability の料金の決定を記録する。GitHub の `schedule`（保険）はこの指示では外さない。
決定（2026-10-21、平野さん）

* fetch-youtube は dry-run の入力を足さず、そのまま run-weekly に入れる。確かめは 2026-10-26（月）02:00 JST の予約の起動に任せる（手動実行の run-weekly では `scheduled` が偽なら飛ばす）。失敗したときは #506 の通知と、GitHub に残る `schedule`（月曜 05:37 JST）が保険になる
* GitHub の `schedule`（週次 6 本・月次 2 本・毎日の保険 3 本）と update-live-channel の保険のゲートは、run-weekly（10/26）と run-monthly（11/1）の初回の成功を見てから、別の指示（CHAT-1021-WKP-04）で 1 回で外す。保険が残る間に #357・#568（差分があれば #304）へコメントが 2 回出ることは受け入れる
* `scripts/check_asset_limits.py` の `MAX_FILES`（20,000。Workers Free の上限）を Paid の上限 100,000 に直す。コメントの「プランは Free」も直す
* Workers Observability の新しい料金（月 2,000 万件まで含む・超過 $0.60/100 万件）への同意（ダッシュボードの「Agree」）は行わず「Review later」のままにする（2026-10-21）。同意しないと超過時は課金されずサンプリングになる（頭打ち）。今の量（月 5 万件程度）では差が出ない。2026-12-01 の切り替えで扱いが変わるかは 11/25 にダッシュボードで見る（カレンダーに予定あり）

前提（チャット側。平野さんの決定ではない）

* fetch-youtube.yml は WKP-02 で run-weekly から外したので、`workflow_call`・入力 `scheduled`・`run-name` が足されているかは不明（要確認。無ければ WKP-02 で他の本に足したのと同じ形で足す）。GitHub の `schedule`（`37 20 * * 0`）は残す
* run-weekly.yml にジョブ `youtube` を足す案: `if: ${{ inputs.scheduled }}`（手動実行では飛ばす。理由はコメントに書く）、`needs:` でほかのジョブの後（fetch-youtube は最後に regenerate-page を all で呼び重いため。前が失敗しても動かすなら `!cancelled()` と組み合わせる）。権限（contents: write、regenerate-page の job が求める issues: write など）と Secret（YOUTUBE_API_KEY ほか、入れ子の regenerate-page が使うもの）は fetch-youtube.yml と regenerate-page.yml を読んで決める（docs/notes/github-actions.md「権限と Secret の渡し方」）。入れ子は run-weekly → fetch-youtube → regenerate-page の 3 段（run-daily と同じ深さ）
* 作業ブランチで確かめられるのは、run-weekly の手動実行（`scheduled` 偽）が youtube を skipped にして success になることだけ。新規でないワークフローなので `gh workflow run run-weekly.yml --ref work/1021-wkp` で起動できる見込み（要確認。できなければ報告に書く）。fetch-youtube 単体の手動実行は本番に書く（コミット・再生成）ので行わない
* `check_asset_limits.py` の `MAX_FILES` を変えると `check-assets.yml`・`regenerate.py` の警告・エラーのしきい値も変わる。警告域が割合で決まっていればそのまま、固定値なら同じ割合になるよう直す。`scripts/tests/` に上限を見るテストがあれば合わせる。docs（static-generation.md など）にしきい値の数字があれば直す（`grep -rn "20,000\|20000" scripts/ docs/notes/ .github/`。docs/decisions・docs/logs の過去の行は直さない）
* 決定の書き場所: fetch-youtube と `schedule` を外す時機は docs/decisions/automation.md（#564 の段④の節に続ける）、`MAX_FILES` と Observability の料金は docs/decisions/cloudflare.md。docs/notes/cloudflare.md の Workers Logs の記述（WKP-01 で直した箇所）に「同意していないため超過時はサンプリング」の 1 行を足す
* docs/notes/github-actions.md「run-weekly.yml・run-monthly.yml」の節（WKP-02 で足した）に youtube のジョブと手動実行で飛ばすことを足す。「ワークフローの一覧」の表の fetch-youtube の行の契機の記述が「Worker（run-weekly）＋保険の schedule」の趣旨になるよう直す
* 未マージの work/1018-log-367-migrate が github-actions.md の表の別の行を変えている（別の箇所。追記どうしの衝突は両方を残して解いてよい）

手順

1. #564 に他セッションの着手中コメントが無いことを確かめ、着手中のコメントを書く。未マージの work/ ブランチに同じ目的の作業が無いことを確かめる（あれば止まる）
2. (a) fetch-youtube.yml に `workflow_call` 等が無ければ足し、run-weekly.yml にジョブ `youtube` を足す。作業ブランチで run-weekly を手動実行し、youtube が skipped・ほかが success（dry-run）になることを確かめる (b) `check_asset_limits.py` の上限を直し、`python3 -m unittest discover -s scripts/tests` を通す。`check-assets`（push で動く）が success になることを確かめる (c) docs と決定を書く
3. マージ（承認済み）。#564 に、youtube のジョブ・初回 10/26 で確かめること・`schedule` を外す時機（11/1 の後、WKP-04）をコメントする。#564 は閉じない

止まる条件

* fetch-youtube.yml の作りが、`workflow_call` 化すると手動実行や保険の `schedule` の動きが変わる形になっている（例: `schedule` の契機で `inputs` を読んでいて既定が無い）
* 作業ブランチの run-weekly の手動実行が今回の変更で失敗する（無関係な失敗なら原因を報告に書いて進めてよい）
* テスト・check-assets が今回の変更で失敗する（同上）
* 未マージの work/ ブランチが、run-weekly.yml・fetch-youtube.yml・check_asset_limits.py・上の docs の同じ行・同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（「予約実行の初回の日時」に run-weekly の youtube の初回 2026-10-26 02:00 JST を書く）。決定は docs/decisions/automation.md・cloudflare.md に足す
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-WKP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-WKP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1021-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1021-wkp/docs/logs/CHAT-1021-WKP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5175e9bd）: https://github.com/retroeater/mj-logs/tree/main/guide/5175e9bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5175e9bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
