# CHAT-0930-CAL-08

- 着手日時: 2026-09-30（JST）
- 対象issue: #479
- ブランチ: work/0930-cal-full
- 着手時HEAD: 2c0f2361

## 指示

【Claude作成】Claude Code 向け指示：連盟の予定表を過去分を含めてすべて取り込み、予定表の【3】を予定表と完全一致させる（予定IDが消えた行は削除。マージはしない） Chat-Ref: CHAT-0930-CAL-08 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal-full を使う（work/0930-cal は CAL-04・CAL-06 が使っている）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-cal-full origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。origin/cloudflare に CHAT-0930-CAL-07 のマージ（#479 の実装、acb02c12 を含む）が入っていなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-07 のログの `## 報告` を読み、状態が「完了」でも「判断待ち」でもなければ止まる。

目的
予定表の取り込みを今日以降だけから、連盟の【一般公開】予定表の全期間に広げ、【3】を予定表と予定IDで完全に一致させる（予定表に無い予定IDの行は削除する）。これで、改名・作り直し・消えた予定を【3】に残して扱う必要がなくなる。
決定（2026-09-30、平野さん）

* 連盟の【一般公開】予定表は、過去分を含めてすべて取り込む。
* 予定表から予定IDが消えたら、【1】【2】【3】から削除する。以降は予定表と完全一致させる。
* これは #479 の決定6（予定表から消えた【3】の行は残し、印も付けない）を置き換える。

前提（チャット側。平野さんの決定ではない。報告の「判断が必要なこと」で確かめる）

* 今の取り込みは今日以降〜`READ_UNTIL`（2027-04-01）。【1】【2】は毎朝その範囲で丸ごと書き直している。
* 過去分を足すと【3】に大量の行が追記される。その行の掲載は空欄のまま（平野さんが後で付ける）。初回の知らせ B（【3】に新しい行を足した）は、全部を並べず件数だけにするのがよい。
* 放送対局のカレンダー（mj_放送対局）に載せる範囲は変えない（過去の放送は #450 の課題）。【3】に過去の掲載 Y が増えても、カレンダーの過去の予定を作り直したり消したりしない形にするのがよい。
* 【3】の行を削除すると、その行の手動の列（掲載・時刻など）も消える。掲載 Y の行が消えるときは、今までどおり知らせ A で #481 に出す。
* 予定IDが空欄の【3】の行（改名前の WRC-R 4行など）は、全期間の取り込みの後、(日付, 前後の空白を落とした件名) で【2】に一致すれば予定IDを入れ、一致しなければ削除の対象になる。

手順

1. 量の確かめ（書き込まない）: 予定表の全期間の予定を、今の取り込みと同じ API で読み、年ごとの件数・いちばん古い予定の日付・合計を書く。今の【3】の行のうち、全期間の予定表に予定IDが無い行の数（掲載 Y・N・空欄の内訳と、そのうち過去／今日以降）、予定IDが空欄で (日付, 件名) で全期間の【2】に一致する行の数を書く。シート全体のセル数が Google スプレッドシートの上限に対してどれくらいになるかも書く。合計が 5,000 件を超える、または削除される掲載 Y の行が 10 行を超えるときは、ここで止まる。
2. 実装: 取り込みの範囲を全期間にする（`READ_UNTIL` の扱いは、全期間に合わせて要るかどうかを判断し、理由を書く）。【3】の更新を「予定表に無い予定IDの行を削除・予定IDが空欄の行は一致すれば予定IDを入れ、しなければ削除・新しい予定IDの行を追記」にする。削除は行の値（予定ID）で探し、行番号を記憶して使わない。同じ予定IDが2行以上なら今までどおり止める。知らせ: 削除した掲載 Y の行は A、追記は B（多いときは件数だけ）。カレンダーの同期は、載せる範囲を今までどおりにし、過去の予定を作る・直す・消すことがないことをテストで確かめる。定数・関数を変えるときは使う所をすべて挙げる。テストを足して全件通す。docs/notes/yotei-sheet.md を直す（CAL-04 が足した1行には触れない）。#479 に決定の置き換えを1件コメントする。
3. 見込み: 今のシートの値で、初回の実行の見込み（【1】【2】の行数、【3】に追記・削除・予定IDを入れる行数と、削除する掲載 Y の行の一覧、知らせの文、カレンダーの作る／直す／消す）を書き込まずに出す。作業ブランチで `update-live-channel.yml` を apply・yotei_apply・calendar_apply をすべて外して起動し、同じ見込みになることを確かめる。

止まる条件

* CAL-07 のマージが cloudflare に入っていない。
* 手順1の件数の上限を超える。
* 手順3でカレンダーの「消す」「直す」が出て、理由が説明できない。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、上の「前提」の各項目（過去の行の掲載・初回の知らせの形・カレンダーの範囲・削除される掲載 Y の行）をどう実装したかと、マージの順番（毎朝の実行の前後）を書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-08.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-08` は0件。`work/0930-cal-full` はローカル・リモートとも無いので `git checkout -b work/0930-cal-full origin/cloudflare`（2c0f2361）。origin/cloudflare は acb02c12 を含む（CAL-07 のマージ済み）
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-07 の `## 報告` の状態は「完了（次の毎朝の実行の確認待ち）」

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal-full
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-full/docs/logs/CHAT-0930-CAL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-full
- 確認用URL: なし
- マージ: 未
- issue: #479
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d9e54148）: https://github.com/retroeater/mj-logs/tree/main/guide/d9e54148

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9e54148/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9e54148/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9e54148/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9e54148/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d9e54148/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbb55c8c.md
