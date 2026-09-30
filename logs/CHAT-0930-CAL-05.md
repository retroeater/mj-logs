# CHAT-0930-CAL-05

- 着手日時: 2026-09-30（JST）
- 対象issue: #479
- ブランチ: work/0930-cal-479
- 着手時HEAD: 73cc1506

## 指示

【Claude作成】Claude Code 向け指示：#479（予定表の【3】を予定IDで結び付け、件名の変化を知らせる）を実装し、移行の Apps Script を用意する（マージはしない） Chat-Ref: CHAT-0930-CAL-05 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal は CHAT-0930-CAL-04・CAL-06（「カレンダー」の埋め込み、未マージ）が使っているので使わず、work/0930-cal-479 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-cal-479 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-03 のログの `## 報告` が「判断待ち」であることを確かめ、状態を「判断待ち → 続き: CHAT-0930-CAL-05」と直す。

目的
#479 の決定（CAL-03 のログの `## 報告`「決定」1〜10 と #479 の本文）どおりに、予定表の【3】を予定IDで結び付ける形に直し、件名の変化を常設 issue で知らせる。既存の【3】を移す Apps Script も用意する。cloudflare へ入れるのは、平野さんの Apps Script の実行と確認の後、別の指示で行う。
決定（2026-09-30、平野さん）

* #479 の決定1〜10（CAL-03 のログのとおり。ここには写さない。#479 の本文と食い違えば止まる）。
* 改名で外れた掲載 Y の4件（12-03・12-04・12-18・12-24）は、次の毎朝の実行で追記される新しい件名の4行に平野さんが Y を付ける。

前提（チャット側。平野さんの決定ではない。実物と食い違えば止まる）

* CAL-01 の調べでは、`scripts/write_yotei_sheet.py` は【3】の見出しを位置（`header[:7] == LAYER3_HEADERS`）で照合し `zip` で読む。列を足す場所によっては、今の cloudflare のコードの毎朝の実行が止まる。移行（Apps Script の実行）とマージの順番のどちらが先でも毎朝の実行が止まらない形にするのがよい。
* CAL-03 の「未確認の項目」: 改名後の予定ID そのものは鍵が要り未確認。毎朝の実行で【1】が書き換わったら、CAL-03 の経過の控え（改名前の予定ID 12件）と比べて直接確かめられる。

手順

1. 確かめ: #479 の本文を読み、CAL-03 の決定1〜10と食い違いが無いことを書く。09-30 の毎朝の実行（`update-live-channel.yml` の schedule）が済んでいれば、その結果（予定表の件数・【3】に足した行・先頭に空白のある行の数・カレンダーの作る／直す／消す）と、【1】の WRC-R 12件の予定IDが CAL-03 の控えと同じかを書く。済んでいなければそう書き、控えとの比較は「未確認の項目」に回す（待たない）。予定IDが控えと違っていたら、決定1の前提が崩れるので止まる。未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）で同じファイルを触るものが無いことも確かめる。
2. 実装: `lib/yotei.py`・`write_yotei_sheet.py`・`lib/live_calendar.py`・`sync_live_calendar.py`（と関係するもの）を、決定1〜8のとおりに直す。【3】は見出しの名前で読み書きする形にそろえ、「予定ID」列の位置は、今の cloudflare のコードでも毎朝の実行が止まらない所にする（止まらないことをテストか今のコードの実行で確かめる）。「予定ID」列が無いときは、新しいコードは結び付けをせず止まってエラーにする。知らせの常設 issue を起票し、A・B・C のあった日だけコメントする処理を足す（ワークフローに要る権限〈`issues: write` など〉・シークレット・共有をすべて列挙し、足りないものはログに書く）。定数・関数を変えるときは、それを import・参照している所をすべて挙げる。テストを足して全件通す。docs/notes/yotei-sheet.md に新しい結び付けと知らせを書く。
3. 移行の Apps Script と見込み: `scripts/apps_script/` に、決定9どおり1本の Apps Script（「予定ID」列が無ければ足す、今の【2】と（日付, 前後の空白を落とした件名）で一致する【3】の行に予定IDを入れる、【3】の件名の前後の空白を落とす、変えたセルを記録のタブに残す、記録のタブがあれば実行済みとして止まる、実行前に件数の確認を出す）を作る。行は見出しの名前と値で探し、行番号で指定しない。今のシートの値で計画の部分を動かし、予定IDを入れる行数・空欄のまま残る行数（掲載 Y のものは内訳）・空白を落とす行数・同じ予定IDが2行以上になる組の数を書く。同じ予定IDが2行以上になる組があれば止まる。新しいコードで、移行後の値を見なした読み込みから `build_desired()` を書き込まずに動かし、カレンダーの作る／直す／消すの見込みを書く。

止まる条件

* CAL-03 の `## 報告` が「判断待ち」でない。#479 の本文と CAL-03 の決定が食い違う。
* 手順1で予定IDが控えと違う。同じファイルを触る未マージのブランチがある。
* 手順3で同じ予定IDの組がある、またはカレンダーの「消す」が予定表側で改名の4件より多く、理由が説明できない。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* 「判断が必要なこと」に、(1) 平野さんの Apps Script の実行手順（Windows のブラウザで、開く場所とメニューの順、見込みの件数）、(2) Apps Script の実行・平野さんの Y の記入・マージ・毎朝の実行の順番（どの順なら毎朝の実行が止まらないか）、(3) 常設 issue の番号、を書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-05.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-05` は0件。`work/0930-cal-479` はローカル・リモートとも無いので `git checkout -b work/0930-cal-479 origin/cloudflare`（73cc1506）。
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-03 の `## 報告` は「判断待ち（実装の指示を待つ）」だったので「判断待ち → 続き: CHAT-0930-CAL-05」に直した（このコミットに含める。CAL-03 のログは cloudflare に入っているので、この変更はこのブランチのマージで入る）。

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal-479
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-479/docs/logs/CHAT-0930-CAL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-479
- 確認用URL: なし
- マージ: 未
- issue: #479
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 73cc1506）: https://github.com/retroeater/mj-logs/tree/main/guide/73cc1506

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/73cc1506/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/73cc1506/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/73cc1506/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/73cc1506/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/73cc1506/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbb55c8c.md
