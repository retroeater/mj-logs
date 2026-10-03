# CHAT-0930-CAL-18

- 着手日時: 2026-09-30 20:49（JST）
- 対象issue: #450・#453
- ブランチ: work/0930-cal-450
- 着手時HEAD: 77033776（origin/work/0930-cal-450 と同じ）

## 指示

【Claude作成】Claude Code 向け指示：#450（過去の放送もカレンダーに載せる）を cloudflare へ入れ、今夜のうちに層1の取り直しと初回の作成（約2,500件）を手動実行で行って見届ける Chat-Ref: CHAT-0930-CAL-18 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push、cloudflare へのマージ、書き込みありのワークフローの手動実行（下の手順のもの）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-cal-450 を続けて使う（CHAT-0930-CAL-16 の実装のコミットがあるため）。`git checkout -b work/0930-cal-450 origin/work/0930-cal-450` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる マージ: 承認済み（チャットで、2026-09-30。work/0930-cal-450 を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-16 のログの `## 報告` が「判断待ち」であることを確かめ、状態を「判断待ち → 続き: CHAT-0930-CAL-18」と直す。

目的
CAL-16 の実装を本番に入れ、CAL-16 の報告の順番の案（除外タブ → マージ → 層1の取り直し → 初回の作成）どおりに、明朝の毎朝の実行より前に初回の作成まで終える。
決定（2026-09-30、平野さん）

* CAL-16 の順番の案で、今夜すべて行う（マージ → 層1の取り直し → 初回の作成を手動実行で見届ける）。朝6時（JST）ころまでに終える。
* 除外のタブは、平野さんが CAL-16 の表の4件で作った。タブ名は「【4】カレンダー非掲載」に変えた（CAL-12・CAL-16 の「カレンダーの除外」を置き換え）。見出し（動画ID・参考:題名・理由）は CAL-16 の表のまま。
* #453 は、#450 の実装が cloudflare に入ったら閉じる（CAL-16 の指示の決定）。JPMLリーグは大会の一覧に足さない（CAL-16 のとおり）。

前提（チャット側。平野さんの決定ではない）

* CAL-16 の見込み: 層1の取り直しは 4,050行の追記、返らない 0本、層1 14,177 → 18,230行。初回の作成は、除外の後 2,503件（直す 0・消す 0）、約21分。載せる予定は 2,649件（枠 2,525・予定表 124）。大会名の追加で予定表の【2】が29行変わる（【3】は増減しない）。
* 毎朝の実行（cron 17:43 UTC＝02:43 JST、最近は 06:30〜07:40 JST に動く）と重ならないようにする。

手順

1. 除外タブの確かめとタブ名の変更: コードが読む除外のタブ名を「カレンダーの除外」から「【4】カレンダー非掲載」に変える（定数と、それを使う所・テスト・docs/notes/yotei-sheet.md など資料の記述をすべて。変えた所を挙げる）。テストを通してコミットする。そのうえで /live の3層のスプレッドシートに「【4】カレンダー非掲載」タブがあり、見出し（動画ID・参考:題名・理由）で読めて、動画IDが CAL-16 の表の4件（oc5eK9LEXu0・p4G1enKcSTw・2UQGDePTDl0・0PuFUIz_dk0）と同じであることを確かめる。無い、または違えば止まる（マージもしない）。新しいタブ名で書き込みなしの見込みを1回出し、除外が4件効いている（作る 2,503）ことを確かめる。
2. マージ: テストと `python3 scripts/check_asset_limits.py` を通し、差分が CAL-16 のもの（同期のコード・テスト・ワークフロー・資料・ログ）と手順1のタブ名の変更のほかに無いことを確かめて、CLAUDE.md の手順どおり cloudflare へ入れる。#453 に「#450 の実装が cloudflare に入った」旨を1件コメントして閉じる。
3. 層1の取り直し: `update-live-channel.yml` が実行中でないことを確かめ、cloudflare で `apply` と `backfill` を付け、`yotei_apply`・`calendar_apply` は外して起動する。成否、追記した行数、返らない本数、層1の行数、コミット（Actions のもの）の SHA、/live の再生成の成否を書く。CAL-16 の見込み（追記 4,050・返らない 0）と大きく違えば止まる。
4. 初回の作成: 手順3の完了を待ち、実行中の実行が無いことを確かめて、cloudflare で `calendar_apply` だけを付けて起動する（ほかは外す）。直前に同じ入力で書き込みなしの見込みを1回出してもよい。作成が上限などで途中で止まったら、何件目で・何時に・どんなエラーで止まったかを書いて、そこで止まる（翌朝の実行が続きから作る）。終わったら、作る／直す／消すの件数、カレンダーの予定の総数、#450 のコメント（ワークフローが残すもの）を書く。見込み（作る 2,503・直す 0 前後・消す 0）と大きく違えば止まる。
5. 確かめ: 公開 iCal などで「mj_放送対局」を読み、予定の総数と、過去の予定を年ごとに数件（2015年・2020年・2024年など）選んで、件名・開始・終了・説明欄が今日以降の枠の予定と同じ形であることを書く。除外の4件が載っていないことも確かめる。#450 に結果を1件コメントする（#450 は明朝の毎朝の実行を確かめるまで開けておく）。

止まる条件

* CAL-16 の `## 報告` が「判断待ち」でない。
* 手順1で「【4】カレンダー非掲載」タブが無い・違う、または新しいタブ名で除外が効かない。手順2の差分に想定外のものがある、またはマージで衝突する。
* 手順3・4の結果が見込みと大きく違う、または失敗する（マージ済みのまま原因を書いて止まる）。
* 手順4を始める時点で 05:30 JST を過ぎている、または毎朝の実行が動いている（そのときは作成を始めず、翌朝の実行に任せたことを書いて止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、明朝の毎朝の実行の見込み（作成が途中で止まったなら続きの件数、止まらなかったなら作る 0・直す 数件・消す 0）を書く。決定は CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-18.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-18` は0件。ローカルの `work/0930-cal-450` は origin/work/0930-cal-450（77033776）と同じ（未コミットの変更なし）
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-16 の `## 報告` は「判断待ち（…マージは未承認）」だったので「判断待ち → 続き: CHAT-0930-CAL-18」に直した（このコミットに含める）

### 手順1: タブ名の変更と除外タブの確かめ

- `git merge origin/cloudflare` を実行した。衝突は無く、入ったのはログ等
- タブ名の変更（cc0ebc7d）: 「カレンダーの除外」を「【4】カレンダー非掲載」に置き換えた。変えた所:
  - `scripts/lib/live_layer3.py`: 定数 `EXCLUDE_SHEET_NAME` と `fetch_calendar_exclusions()` の docstring
  - `scripts/lib/live_calendar.py`: 冒頭の docstring、`build_desired()` の docstring、`delete_reason()` が返す理由の文字列
  - `scripts/tests/test_live_calendar.py`: `delete_reason` の期待値
  - `scripts/tests/test_sync_live_calendar.py`: 冒頭の docstring
  - `docs/notes/yotei-sheet.md`: 2か所。「カレンダーの除外」から改名したことを添えた
  - `sync_live_calendar.py` とワークフローは定数 `live_layer3.EXCLUDE_SHEET_NAME` を読むので、変えていない
  - テストは全件 OK
- `docs/decisions/broadcast-calendar.md` の CAL-12 Q11 の行は、書き方どおり消さずに、完了時に置き換えの印を付ける
- /live の3層のスプレッドシートの「【4】カレンダー非掲載」を `fetch_calendar_exclusions()` で読んだ:
  - 見出し（動画ID・参考:題名・理由）で読めた
  - 4行の動画ID は oc5eK9LEXu0・p4G1enKcSTw・2UQGDePTDl0・0PuFUIz_dk0 で、**CAL-16 の表と同じ**
  - 理由: テスト放送 2・1分未満の断片（15秒・51秒）2
- 書き込みなしの見込み: run 36711052871（work/0930-cal-450、cc0ebc7d、入力はすべて外した）。update・yotei success
  - 出力「「【4】カレンダー非掲載」: 4本」
  - 載せる予定 2,649（枠 2,525・予定表 124）
  - 放送済みの枠 2,507（2015年 2・2022年 409 と、除外の分だけ減った）
  - **作る 2,503**・直す 0・消す 0・そのまま 146
  - **除外が4件効いている**。予定表の【3】は 1,132行で、入れる・削除・足すとも 0

### 手順2: マージ

- テストは全件 OK。`python3 scripts/check_asset_limits.py` も OK
- origin/cloudflare との差分は17ファイルで、CAL-16 と CAL-18 のもののほかに無い:
  - 同期のコード: `sync_live_calendar.py`・`lib/live_calendar.py`・`lib/live_layer3.py`・`lib/sheets.py`・`lib/yotei.py`・`fetch_live_channel_raw.py`
  - テスト4本
  - ワークフロー: `update-live-channel.yml`
  - 資料: `yotei-sheet.md`・`live-channel-write.md`・`docs/decisions/broadcast-calendar.md`
  - ログ: CAL-12・CAL-16・CAL-18
- 再 fetch し `merge-base --is-ancestor` が真なのを確かめて push した: **675fa13e..4bd68b4b**（fast-forward、20:59 JST）
- #453: コメントした（5910807580）。closed（completed）にした。ラベルは「分野: 自動化」だけで、「状況:」ラベルは無い

### 手順3: 層1の取り直し

- 実行中の実行が無いことを確かめた（直前の 36711052871 は completed）
- cloudflare で apply・backfill を付け、yotei_apply・calendar_apply を外して起動した: run 36711916025。**update・yotei・regenerate すべて success**
  - `apply` を付けたため、yotei ジョブの予定表のシートへの書き込みも動いた（ジョブの `APPLY` は apply か yotei_apply）。毎朝と同じ書き込み
- 取り直し: 対象 4,050本。**追記 4,050行・変わらず 0・取れなかった 0・返らない 0本**・81ユニット。CAL-16 の見込みと同じ
- 新着 1本、配信予定・配信中の取り直し 3行
- 層1の行数: **14,177 → 18,231**。Actions のコミットは **71708296**（`chore: fetch live channel raw data`、+4,054行・削除 0）
- /live の【1】: 14,104行を書き、読み返して一致した。Sheets の読み取りで ReadTimeout が1回あったが、再試行で通った
- 予定表のシート: 【1】【2】 1,132行、【3】 1,132行（入れる・削除・足すとも 0）
  - 見込みの「【2】29行が変わる」は、この書き込みで入った（【2】は毎回置き換わる表）
- /live の再生成（ジョブ regenerate）: success
- 取り直しの後の層1: ライブ 4,091本のうち配信終了日時あり 4,060本。開始があって終了が無いものは 2本（配信中・直後の枠）

### 手順4の前の見込み（書き込みなし）

- run 36712588505（cloudflare 54c2563e、入力はすべて外した）。success
- **作る 2,503**・**直す 14**・消す 0・そのまま 132。API の回数 2,517回、約21分
- 直す14件の内訳:
  - 今日（09-30）の枠 2件: 実際の開始・終了が入った
  - 予定表由来の仮の予定 12件（格闘倶楽部プロNo.1 2・地方プロリーグ決勝 10）の開始が 13:00 になった
    - 大会名を足して分けが付き、【2】の開始(仮置き)が「全体の中央値 14:00」から「その分けの中央値 13:00」に変わったため（CAL-16 で見込んだ【2】の変化）
  - どちらも説明できる変化なので進めた

### 手順4: 初回の作成

- 始めた時刻は 21:08 JST（05:30 より前）。実行中の実行は無かった
- cloudflare で calendar_apply だけを付けて起動した: run 36712938984。**update・yotei success**、regenerate skipped
  - yotei ジョブは 12:10〜12:40 UTC（約30分）
- 出力:
  - 「作る: 2503件…/ 直す: 14件 / 消す: 0件 / そのまま: 132件」
  - 進み具合の行は 25行（100/2503 〜 2500/2503）
  - **「書き込みました: 作る 2503・直す 14・消す 0」**
- 途中で止まらなかった（「作成は止まっていません」）。Calendar の上限にも当たらず、#450 への止まった所のコメントも無い
  - 約2,500件を約30分で作れた。1件あたり約0.7秒で、見込みの 0.5秒より遅い
- 見込み（作る 2,503・直す 14・消す 0）と同じ

### 手順5: 確かめ

- 公開 iCal（calendar.google.com の basic.ics）で「mj_放送対局」を読んだ:
  - **予定 2,649件**。載せる予定（枠 2,525＋予定表 124）と同じ
  - 年ごと: 2015年 2・2020年 134・2021年 374・2022年 409・2023年 438・2024年 419・2025年 417・2026年 390・2027年 66
- 除外の4本（oc5eK9LEXu0・p4G1enKcSTw・2UQGDePTDl0・0PuFUIz_dk0）は説明欄のどこにも無い（載っていない）
- 過去の予定を見た:
  - 2015-08-04 第32期鳳凰戦 A2リーグ第5節
  - 2015-08-08 【麻雀】インターネット麻雀日本選手権2015 決勝戦
  - 2020-11-03 Focus M season4
  - 2020-08-31 第37期鳳凰戦 A1リーグ第7節A卓
  - 2024-10-08 Focus M season10
  - 2024-11-18 第19期女流桜花 AリーグプレーオフB卓
- どれも、開始・終了は実際の時刻（取り直した配信終了日時）で、説明欄は1行目が URL、次に【対局者】【実況】【解説】の見出しごとに1人1行。**今日以降の枠の予定と同じ形**
  - 気づいた点: /live の【3】に行が無い古い枠は、件名に YouTube の題名の「【麻雀】」がそのまま残る（例 2015-08-08）。`clean_title()` は【メンバー限定】だけを外す作りで、今日以降の枠と同じ扱い。直すかは下の「判断が必要なこと」に書いた
- #450 に結果をコメントした（5911511598）。#450 は開けたまま
- 決定の記録: `docs/decisions/broadcast-calendar.md` に CAL-18 の決定を足した。CAL-12 Q11 の行に置き換えの印を付けた

## 報告

- 状態: 完了（#450 は明朝の毎朝の実行を確かめるまで open）
- ブランチ: work/0930-cal-450（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-18.md
- 比較URL: https://github.com/retroeater/mj/compare/675fa13e...4bd68b4b
- 確認用URL: なし（カレンダーは公開 iCal で確かめた）
- マージ: 済（4bd68b4b、fast-forward）
- issue:
  - #450: 結果をコメントした。open
  - #453: コメントしてクローズした
- 判断が必要なこと:
  - **明朝の毎朝の実行の見込み**: 作成は途中で止まらなかったので、続きは無い
    - 作る 0（新しい枠や、枠ができた予定があればその分）
    - 直す 数件（放送翌朝の実際の時刻など）
    - 消す 0（今日 09-30 の仮の予定は無いので、日付が過ぎて消えるものも無い）
    - 予定表の【3】の追記・削除 0、#481 への知らせなし（今夜の実行で【1】【2】【3】がそろったため）
    - 層1は毎日の取り直しだけ（backfill は入力が無いと動かない）
  - /live の【3】に行が無い古い枠の件名に「【麻雀】」などの接頭辞が残る（2015年ごろの題名）。直すなら `live_calendar.clean_title()` で外す規則を足す（今日以降の枠にも効く）。今はそのまま
- 未確認の項目:
  - 明朝の毎朝の実行の結果（#450 を閉じる前に確かめる）
  - Google カレンダーの画面での見え方（確かめたのは公開 iCal の中身まで）
- エラー: なし。取り直しの実行で /live の【1】の読み返しに ReadTimeout が1回あったが、再試行で通った

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77c35579）: https://github.com/retroeater/mj-logs/tree/main/guide/77c35579

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
