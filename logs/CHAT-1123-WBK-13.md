# CHAT-1123-WBK-13

- 着手日時: 2026-11-23
- 対象issue: #684
- ブランチ: work/1123-wbk
- 着手時HEAD: cb88680f5

## 指示

【Claude作成】Claude Code 向け指示：「タイトル」の表示 N の行にも当たる検査を警告として足し（生成は止めない）、WBK-03 のログに続きを書く（WBK-12 の続き）
Chat-Ref: CHAT-1123-WBK-13
マージ: 承認済み（チャットで。下の「止まる条件」を1つも踏まないときに限る。範囲は scripts/generate_title_pages.py の検査と scripts/tests/ の試験、docs〈notes・decisions・ログ〉）
貼る時機: いつでも（CHAT-1123-WBK-12 のマージの後。マージは済んでいる）
セッション: 任意（クラウドのセッション。WBK-12 の続きでも可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1123-wbk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-wbk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1123-WBK-12（判断待ち）の続き。平野さんが「タイトル」に手で足した表示 N の行の書き違い（2026-11-23、足した4行の順位がすべて「1」）を、生成・`check` のたびに機械で拾えるようにする。あわせて、WBK-01〜03 のログが自動で消えるように、WBK-03 のログに続きを書く。シート・ページは変えない。

### 決定（2026-11-23、平野さん）
- WBK-12 の「表示 N の行の検査を足すか」は (a)（`generate_title_pages` の検査に、表示 N の行も含めた警告を足す。生成は止めない）
- WBK-12 の「WBK-01〜03 のログが自動で消えない」は (a)（WBK-03 の状態の末尾に「 / 続き: CHAT-1116-WBK-04」を足す。この指示に混ぜる）
- この指示のマージは、「止まる条件」を1つも踏まないことを条件に承認する

### 前提（チャット側。平野さんの決定ではない）
- 足す検査（WBK-12 の案 (a)。表示 Y・N のすべての行を対象にした警告で、生成は止めない）:
  - 同じタイトル（期）で順位が重なる（表示 Y・N を合わせて）。ただし、今の生成が正しい形として扱っている重なり（1位が2名の期〈王位戦 第26期〉など、同順位）と見分ける。見分け方は、今の表示 Y の行で当たる期を実物で見て決め、ログに書く（見分けられなければ止まる）
  - 表示 N の行の名前・順位が空
  - 表示 N の行の日付が docs/notes/title-pages.md「日付列の書式」に合わない
  - 表示の値が Y・N 以外
- 表示 Y の行に今ある検査（止める条件・警告）の中身と数は変えない。足す警告は、今ある警告と同じ出し方（docs/notes/title-pages.md「生成を止める条件と警告」）にそろえ、文面で見分けがつくようにする
- 今の読み取り（WBK-12 がコードで確かめた）: `load_periods()` は行ごとの最初に表示が Y でない行を飛ばす（scripts/generate_title_pages.py の `if shown != SHOWN: continue`）。試験は scripts/tests/test_title_check.py（表示 Y の行の検査だけ）（要確認）
- 今のシートの表示 N の行に何件当たるかは分かっていない。作業ブランチで `regenerate-page.yml` を入力 `check` で動かして数える（WBK-07 と同じく、マージ前に作業ブランチで動かす）
- 平野さんが足した第8期麻雀マスターズの4行（順位は 2〜5 に直した）は、足す検査に当たらない見込み（要確認。当たれば中身を書く）
- 警告が多いとき（20件を超える）は、マージせずに全件の表を書いて止まり、絞り方を平野さんと決める

## 手順
1. 準備
   - CHAT-1123-WBK-12 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1123-WBK-13` を足す。CHAT-1115-WBK-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1116-WBK-04` を足す（WBK-04 は WBK-03 の判断待ちの続き。ログがもう無ければ足さず、そう書く）。#684 に着手中のコメントを書く（本文・状態・ラベルは変えない）
   - docs/notes/title-pages.md「生成を止める条件と警告」と、警告の行き先（ジョブのログだけか、ジョブの失敗・issue への通知・朝の報告につながるか）を、コードとワークフローで確かめてログに書く
2. 検査と試験
   - 前提の検査を足し、試験を scripts/tests/test_title_check.py に足す（当たる形・当たらない形〈同順位の正しい重なり・表示 N で正しい行〉の両方。順位がすべて「1」の4行の形を1つ入れる）。`python3 -m unittest discover -s scripts/tests` を通し、自己レビュー（docs/notes/self-review.md）を受ける
   - 作業ブランチで `regenerate-page.yml` を入力 `check` で手動実行し、足した警告の全件の表（タイトル・行の中身の要約・当たった検査）と件数、今ある警告・止める条件の数が変わっていないこと、生成物（title/ など）に差が出ないことをログに書く
   - 止まる条件を踏まなければマージし、cloudflare の check-run と、次の `check`（手動で1回）の結果を確かめる
3. 記録
   - docs/notes/title-pages.md「生成を止める条件と警告」に足した警告を書き、WBK-12 が足した「表示が Y でない行の扱い」の記述を今の形に直す（今の内容を読んでから）。docs/decisions/automation.md の WBK-01 の節に、上の決定と結果（警告の件数）を追記する
   - 足した警告が1件以上あれば、「判断が必要なこと」に、当たった行の扱いの案（平野さんがシートを直す・検査から外す、など）をメリット・デメリット・推奨つきで書く。0件なら「なし」

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる。

## 止まる条件
- 足す警告が、ジョブの失敗・issue への通知・朝の報告につながる形で、今のシートで1件以上当たる（マージせずに、全件の表と、通知につなげない出し方の案を書いて止まる）
- 足した警告が今のシートで20件を超える（マージせずに全件の表を書いて止まる）
- 同順位の正しい重なりと書き違いを見分けられない
- 今ある警告・止める条件の数が変わる、または `check`・再生成の生成物に差が出る
- 試験が通らない、または自己レビューが2回で収まらず、残った指摘が検査の判定に関わる
- 差分が次の範囲を超える: scripts/generate_title_pages.py の検査、scripts/tests/ の試験、docs（notes・decisions・ログ）。ワークフロー・シート・生成物を変える必要が出た
- 追記先（docs/notes/title-pages.md・docs/decisions/automation.md）の今の内容と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
- 未マージの work/ ブランチが、変えるファイルの同じ行・関数・節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- マージ後の check-run・手動実行の失敗が今回の変更による（無関係な失敗なら、原因を報告に書いたうえで残りの手順を進めてよい。一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-WBK-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-WBK-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手時の確かめ: `CHAT-1123-WBK-13` のコミットは全ブランチに無い。ローカルの work/1123-wbk は origin/cloudflare と同じ（cb88680f5、WBK-12 でマージ済み）。
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・セッション・共通手順）はすべてある。
- 手順の順の違い: 実装を含む指示の0章ゲート（docs/notes/branch-operations.md）と、未マージのブランチ・警告の行き先のコードの読み取りを、このログの先行 push の前に行った（ログの先行 push を最初にする規則から外れた。読むだけで、変更はしていない）。

## 報告

- 状態: 作業中
- ブランチ: work/1123-wbk
- ログ: https://github.com/retroeater/mj/blob/work/1123-wbk/docs/logs/CHAT-1123-WBK-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1123-wbk
- 確認用URL: なし
- マージ: 未
- issue: #684
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cb88680f）: https://github.com/retroeater/mj-logs/tree/main/guide/cb88680f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb88680f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
