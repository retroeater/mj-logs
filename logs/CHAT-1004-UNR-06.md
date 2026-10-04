# CHAT-1004-UNR-06

- 着手日時: 2026-10-04
- 対象issue: #491
- ブランチ: work/1004-unr
- 着手時HEAD: d592a73f（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：放送対局カレンダーの説明文で、概要欄から足す名前にも「別名」の訂正をかける（「畑谷翔太」「畑谷翔大」の重複を消す）。件名が空の予定を #491 に記録する Chat-Ref: CHAT-1004-UNR-06 マージ: 承認済み（チャットで、2026-10-04）。条件: 手順2 の模擬で、説明文が変わる予定が「同じ人の表記の重複が消える」「名前の表記が『別名』の訂正どおりに直る」ものだけであること。それ以外の変化（名前が減る・別の名前が増える、件名・時刻が変わる、作る・消す予定が直す前後で変わる）が1件でもあれば、マージせず判断待ちで止まる 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/notes/yotei-sheet.md の「説明欄」と「手動実行」の節、CHAT-1003-UNR-03 のログ「模擬」の「A → B」の `FXtYzZBEtXA` の項を読む。

目的
公開カレンダー「mj_放送対局」の説明文で、同じ人が「別名」で直した表記と概要欄の誤記の両方で並ぶのをなくす。カレンダーを見る人に、いない人が1人多く見えるため。
決定（2026-10-04、平野さん）

* 第2期昇龍戦（メンバー限定版、動画ID `FXtYzZBEtXA`）の【対局者】に「畑谷翔太」と並んでいる「畑谷翔大」を消す
* 冒頭の「マージ:」の条件を満たせばマージし、手動実行ですぐカレンダーに反映する
* 動画ID `tMwcjumwz-o` の予定の件名が空である件は、#491 に記録する（直さない）

前提（チャット側。平野さんの決定ではない）

* 原因（CHAT-1003-UNR-03 のログ）: 【2】の値は「別名」の訂正で「畑谷翔太」になるが、`live_calendar.people()` は概要欄に対局者の行が2行以上あるとき概要欄の名前を後ろに足し、その名前には「別名」をかけないので、「畑谷翔大」が別の名前として残る
* 直し方の案: 概要欄から足す名前にも【2】と同じ名前の直し方（「別名」の訂正）をかけてから、重複の判定と追加を行う。どこにも登録の無い名前は今までどおりそのまま載せる（消さない）。/live と共用の `live_extract.extract_players_and_staff()` は変えず、直すのは `live_calendar.py` の側（docs/notes/yotei-sheet.md「説明欄」の決まり）。実物を読んで、より小さい直し方があればそれでよい
* チャット側がカレンダーを読んだ結果（2026-10-04）: `FXtYzZBEtXA` の予定（6/20、件名「第2期昇龍戦」）の【対局者】は 越後良太・齋藤豪・畑谷翔太・塩澤彰大・畑谷翔大・関本幸樹・山脇千文美・藤川まゆ・堀部雄太。`tMwcjumwz-o` の予定（2020-09-29 15:32〜17:20）は件名が空で、説明文は URL だけ
* 手動実行は `update-live-channel.yml` を cloudflare で、入力は `calendar_apply` だけ true（docs/notes/yotei-sheet.md「手動実行」）。この回の同期は、この変更以外の変化（新しい配信・時刻の変更など）も一緒に書く。分けて報告する
* #491 は「放送対局カレンダーの運用の残り」で Open、畑谷の件は CHAT-1003-UNR-04 がコメント済みと読んでいる。実物で確かめる

手順

1. 再現して直す: 今のコードと今のシートで `FXtYzZBEtXA` の説明文を作り、「畑谷翔太」と「畑谷翔大」が並ぶことを確かめる。`live_calendar.py` を直し、テストを足す（訂正のかかる名前が1つにまとまる、登録の無い名前は残る、【3】に値がある見出しは今までどおり【3】の値だけ）。直した関数を import・参照している所を洗い出して書く。
2. 模擬してマージ: カレンダーの同期の計画（書き込みなし）を直す前と後のコードで作り、説明文・件名・時刻・作る／消すの差を全件比べて、変わる予定を1件ずつ（動画ID・件名・説明文の前後の違い）書く。あわせて、計画の中で件名が空になる予定の件数と、その動画ID・YouTube の題名・空になる理由を書く（`tMwcjumwz-o` を含むはず。直さない）。冒頭の「マージ:」の条件を満たせば、docs/notes/yotei-sheet.md「説明欄」に規則を足し、docs/decisions/ の放送対局カレンダーの決定に1行足して、cloudflare へマージする。
3. 反映して記録: `update-live-channel.yml` を cloudflare で手動実行する（入力は `calendar_apply` だけ true）。待つ上限は15分で、超えたらその時点の状態を書き「未確認の項目」に回して先へ進む。実行ログから、作る・直す・消すの件数と、直した予定の一覧（この変更によるものと、それ以外に分ける）を書く。#491 にコメントする: (1) 名前の重複は直してカレンダーに反映したこと（件数は手順2・3の節から引用）、(2) 件名が空の予定の件数・例・理由（手順2の節から引用。直し方は決めていない、記録だけ）。

止まる条件

* 手順1 で再現しない（今のコードで「畑谷翔大」が並ばない。作った説明文を書いて止まる）
* `scripts/lib/live_calendar.py`・`scripts/sync_live_calendar.py` に触れる未マージの work/ ブランチがある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）、または #491 に他セッションの着手中コメントがある
* 手順2 の模擬が冒頭の「マージ:」の条件を満たさない（マージせず判断待ち。変わる予定の一覧を「判断が必要なこと」に書く。#491 への (2) の記録だけは行う）
* cloudflare への push、またはワークフローの手動実行が権限の判定で拒否された（別の手段を試さずに止まる）
* ワークフローのジョブが失敗した、または削除の上限で何も書かずに止まった（戻さずに、失敗したジョブ・ステップ・エラーを書く。#491 への記録は行ってから止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（カレンダーに反映されたかを実行ログでしか確かめていなければ、「未確認の項目」に「カレンダーの実物」を書く。チャット側がカレンダーを読んで確かめる）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-UNR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-UNR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1004-UNR-06"` は0件。`work/1004-unr` はローカル・リモートとも無く、`git checkout -b work/1004-unr origin/cloudflare`
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1004-unr
- ログ: https://github.com/retroeater/mj/blob/work/1004-unr/docs/logs/CHAT-1004-UNR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1004-unr
- 確認用URL: なし
- マージ: 未
- issue: #491
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 200ed2c4）: https://github.com/retroeater/mj-logs/tree/main/guide/200ed2c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/200ed2c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8cd42e0.md
