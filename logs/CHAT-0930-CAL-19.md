# CHAT-0930-CAL-19

- 着手日時: 2026-10-01 08:57（JST）
- 対象issue: #450
- ブランチ: work/1001-cal-title
- 着手時HEAD: 5fc5a5c2（origin/cloudflare から作った）

## 指示

【Claude作成】Claude Code 向け指示：カレンダーの件名から「【麻雀】」「【無料放送】」を外し、除外タブが無いときは同期を止めるように直して、cloudflare へ入れ、過去の予定の件名を書き込みありで直す Chat-Ref: CHAT-0930-CAL-19 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、cloudflare へのマージ、書き込みありのワークフローの手動実行（下の手順のもの）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1001-cal-title を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 承認済み（チャットで、2026-09-30。work/1001-cal-title を cloudflare へ。手順3の見込みが止まる条件に当たらない限り、確認を求めずにマージと書き込みありの実行まで進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-09（明朝の確認）のログがあり、状態が完了か判断待ちであることを確かめる。無い、または作業中なら止まる（CAL-09 の確認と、この指示の件名の直しが同じ朝の実行に重ならないようにするため）。

目的
CAL-18 の報告のとおり、/live の【3】に行が無い古い枠では、YouTube の題名の「【麻雀】」などが件名に残る。これを外す。あわせて、除外タブ（「【4】カレンダー非掲載」）の名前が変わるなどして読めないとき、除外が黙って効かなくなるのを防ぐ。
決定（2026-09-30、平野さん）

* カレンダーの件名から「【麻雀】」と「【無料放送】」を外す（今日以降の枠にも効かせる）。
* 除外タブが無いときは、除外なしで進めずに同期を止める（CAL-12 の「無ければ除外なし」を置き換え）。
* マージと、過去の予定の件名を直す書き込みありの実行を承認する（見込みが止まる条件に当たらなければ）。

前提（チャット側。平野さんの決定ではない）

* 件名を作るのは `lib/live_calendar.py` の `clean_title()`（今は【メンバー限定】だけを外す）。外すのは件名の中の「【麻雀】」「【無料放送】」の文字列で、ほかの【…】（【テスト放送】など）は外さない。外した後の前後の空白は落とす。
* 件名が変わる予定は、翌朝の実行を待たずに、手順4の手動実行で直す（明朝の毎朝の実行の差を小さくするため）。

手順

1. 確かめ: 今のカレンダーの予定（公開 iCal か、同期の見込みの一覧）で、件名に「【麻雀】」「【無料放送】」を含む予定の件数（年ごと）と、そのほかに件名に残っている【…】の種類と件数（上位10種類ほど）を書く。同じファイルを触る未マージのブランチが無いことを確かめる。
2. 実装: `clean_title()` で「【麻雀】」「【無料放送】」を外す。除外タブ（「【4】カレンダー非掲載」）が読めない（タブが無い、見出しが違う）ときは、カレンダーの同期のジョブを失敗させて止め、理由をジョブの出力に出す。タブがあって行が0件のときは除外なしで進む。定数・関数を変えるときは使う所をすべて挙げる。テストを足して全件通す。資料（docs/notes/yotei-sheet.md など）を直す。
3. 見込み（書き込まない）: 作業ブランチで `update-live-channel.yml` を apply・yotei_apply・calendar_apply をすべて外して起動し、作る／直す／消すの件数を書く。直すのが、手順1で数えた「【麻雀】」「【無料放送】」を含む予定の件数とほぼ同じで、作る・消すが 0（または新しい枠の分だけ）であることを確かめる。
4. マージと書き込み: 差分が手順2のものとログのほかに無いことを確かめて cloudflare へ入れる。実行中の実行が無いことを確かめ、cloudflare で calendar_apply だけを付けて起動する。作る／直す／消すの件数を書き、手順3の見込みと同じであることを確かめる。公開 iCal で、「【麻雀】」「【無料放送】」を含む件名が 0 件になったこと、手順1の例の予定（2015-08-08 のインターネット麻雀日本選手権2015 決勝戦など）の件名を書く。#450 に結果を1件コメントする。

止まる条件

* CAL-09 のログが無い、または作業中。
* 手順3で、直すが手順1の件数と大きく違う、または消すが出て理由が説明できない（マージしない）。
* 手順4の書き込みありの実行が失敗する、または件数が見込みと違う（マージ済みのまま原因を書いて止まる）。
* 実行が 05:30〜08:30 JST（毎朝の実行の時間帯）にかかる（そのときは手順4の書き込みを始めず、止まって報告する）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。決定は CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す（CAL-12 の「無ければ除外なし」に置き換えの印を付ける）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-19.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-19` は0件。`work/1001-cal-title` はローカル・リモートとも無いので、`git checkout -b work/1001-cal-title origin/cloudflare`（5fc5a5c2）
- 手順0: 指示欄の末尾は指示文の最後の行と一致
- 手順0: **CHAT-0930-CAL-09 のログが無い**
  - 全リモートブランチの `docs/logs/CHAT-0930-CAL-09.md`: 無い
  - `git log --all --grep=CHAT-0930-CAL-09`: 0件（2026-10-01 08:57 JST に fetch した後）
  - 止まる条件「CAL-09 のログが無い」に当たるので、手順1以降（確かめ・実装・見込み・マージ・書き込み）は行わず止めた
- 参考（読むだけ）: 今朝の毎朝の実行は run 36780007636（schedule、cloudflare）で、success
  - 06:31〜06:37 JST に動いた
  - 層1のコミット 5fc5a5c2 がある
  - カレンダーの結果（作る・直す・消す）は、CAL-09 の確認の範囲なので読んでいない

## 報告

- 状態: 中断（止まる条件「CAL-09 のログが無い」に当たった。作業はしていない）
- ブランチ: work/1001-cal-title（このログだけ）
- ログ: https://github.com/retroeater/mj/blob/work/1001-cal-title/docs/logs/CHAT-0930-CAL-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 未（作業していない）
- issue: #450（コメントしていない）
- 判断が必要なこと:
  - CAL-09（明朝の確認）の指示がまだ実行されていない（どのブランチにもログもコミットも無い）
    - CAL-09 を先に実行してから、この指示を同じ Chat-Ref のまま貼り直すか決めてほしい
    - この指示のコミットはこのログだけ。CLAUDE.md の再開の規則では、コミットが1件でもあれば新しい番号にする
  - 今朝の毎朝の実行（run 36780007636）は success で終わっている。件名の直しを CAL-09 の後に回しても、毎朝の実行とは重ならない
  - 今は 08:57 JST で、05:30〜08:30 の帯は過ぎている
- 未確認の項目: 今朝の毎朝の実行のカレンダーの結果（CAL-09 の範囲）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 064fa714）: https://github.com/retroeater/mj-logs/tree/main/guide/064fa714

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/064fa714/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
