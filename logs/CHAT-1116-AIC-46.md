# CHAT-1116-AIC-46

- 着手日時: 2026-11-16（JST）
- 対象issue: #654
- ブランチ: work/1116-aic
- 着手時HEAD: dd0b5926b

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1116-AIC-45 の続き）見送った3件を直してマージし、Actions の「AIボットの週次書き出し」を2回動かして 11/09〜11/15 の行をブックに入れる Chat-Ref: CHAT-1116-AIC-46 マージ: 承認済み（チャットで。2026-11-16、平野さん）。次の4つを1つでも満たさなければマージしない（4つ目はマージ後の確かめで、満たさなければ戻さずに止まる）

* テストが通る（今の検査を含む）
* 質問欄の答え（`/api/ask` の応答）を作る処理に変更が無い（`/api/ask`・`/api/ask/status`・`ask.mjs` を変えない）
* 下の手順1の到達の確かめで、`POST https://ryoei.pro/api/ask/weekly` が WAF の 403 でなく Worker の応答になる
* マージ後の1回目の起動で `sheet.appended` が1以上・`until` が 2026-11-16T00:00:00+09:00、2回目の起動で `appended` が0 貼る時機: 平野さんが WAF のカスタムルール「Block non-GET/HEAD methods」に `/api/ask/weekly` の例外を足したとチャットで伝えた後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1116-aic を続けて使う（CHAT-1116-AIC-45 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-AIC-45 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-AIC-46` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜45 で使用。AIC-17・24・31 は欠番）。CHAT-1109-AIC-44 のログには手を入れない。

目的
CHAT-1116-AIC-45 で作った週次の書き出しの再試行と再実行の道（`POST /api/ask/weekly`・`ask-weekly.yml`）を、WAF の例外を足した後にマージし、11/16 に書き出せなかった 11/09〜11/15 の行を再実行でブックに入れる。
決定（2026-11-16、平野さん。CHAT-1116-AIC-45 の「判断が必要なこと」への回答）

* WAF の例外: カスタムルール「Block non-GET/HEAD methods」の式の末尾に `and not (http.request.uri.path eq "/api/ask/weekly")` を足す（平野さんのダッシュボード作業）
* 再試行の範囲（追記・行の削除は 429 だけやり直し、5xx・ネットワークの失敗ではやり直さない）: このままでよい
* 既存のテストの期待値を直したこと（cross.test.mjs の決勝だけの人数 9→8）: このままでよい
* 2回目の自己レビューで見送った提案3件（テストの名前・401 の検査が何も確かめていない・エラー文）: この指示で直す
* この指示は、上の「マージ:」の行の条件でマージしてよい

前提（チャット側。平野さんの決定ではない）

* GitHub Actions の Secret `ASK_WEEKLY_TOKEN` と Worker `mj-ask` の Secret `ASK_WEEKLY_TOKEN` は、平野さんのスクリーンショット（2026-11-16 09:40 JST）で両方あることをチャット側が見た（申告値。AIC-45 の「未確認の項目」の Secret の有無はこれで閉じてよい）
* docs/notes/cloudflare.md「カスタムルール『Block non-GET/HEAD methods』」の式の行は、平野さんが足した後の式（`… and not (http.request.uri.path eq "/api/ask") and not (http.request.uri.path eq "/api/ask/weekly")`、申告値）と、足した日・理由に直す
* 2回目の後の直しなので、直した部分にレビューを1回受けてからマージする（docs/notes/self-review.md「収束」）。そのレビューの後にさらに直したら、マージせず判断待ちで止まる

手順

1. 到達の確かめ: `POST https://ryoei.pro/api/ask/weekly`（Authorization なし、UA に `mj-internal`）を1回送り、WAF の遮断のページ（HTTP 403）でなく Worker の応答（今の本番は未マージなので 404 の見込み）になることを確かめる。403 なら止まる
2. 直す: 見送った3件を直し、直した部分のレビューを1回受ける。docs/notes/cloudflare.md の WAF の式を直す。docs/decisions/houou.md に上の「決定」を足す。テストが通れば cloudflare へマージする
3. 再実行: Workers Builds のビルドの成否を確かめ（docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」）、成功してから `ask-weekly.yml` を `actions_run_trigger`（ref `cloudflare`）で起動し、要約（appended・until・sheetDeleted・d1Deleted）を確かめる。もう1回起動し、`appended` が0であることを確かめる。結果を #654 にコメントする（件数と時刻だけ。質問文は書かない）

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる（並走すると Sheets API の上限〈429〉で失敗する、#662）
止まる条件

* 手順1で 403 が返る（WAF の例外がまだ効いていない）
* 未マージの work/ ブランチが `workers/ask/src/weekly.mjs`・`workers/ask/src/index.mjs` の同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 直した部分のレビューの後にさらに直した（マージせず判断待ち）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 起動が 401・404・403 を返した、1回目の `appended` が0、または2回目の `appended` が1以上（戻さずに止まり、要約と Workers Logs で分かる範囲をログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、次の月曜（2026-11-23 03:00 JST）の cron の回で `週次:` の `sheet` が null でないことを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-AIC-46.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-AIC-46 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 56d8d22f）: https://github.com/retroeater/mj-logs/tree/main/guide/56d8d22f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/56d8d22f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/56d8d22f.md
