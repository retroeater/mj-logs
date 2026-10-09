# CHAT-1002-CLD-21

- 着手日時: 2026-10-09
- 対象issue: #491
- ブランチ: work/1002-cld
- 着手時HEAD: d2ff5f93（= origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：#491 の本文を今の状態に直し、概要欄だけから作る予定の名前にも「別名」の訂正をかける（マージは判断待ち） Chat-Ref: CHAT-1002-CLD-21 マージ: 判断待ちで止まる（コードの変更を模擬の結果とともにチャット側が読み、マージの指示を出す）。#491 の本文の書き換えは承認済み（チャットで、2026-10-09）。 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push、#491 の本文の書き換えを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-CLD-20 のログの `## 報告` の状態に「 / 続き: CHAT-1002-CLD-21」を足す（このログと一緒に push する）。#491 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

目的
CHAT-1002-CLD-20 の棚卸しを受けて、#491 の本文を今の状態に合わせ、残りの1件（概要欄だけから作る予定の名前に「別名」の訂正がかかっていない件、CHAT-1005-UNR-08 のコメント）を直す。直しがマージされたら、続きの指示で #491 をクローズする。
決定（2026-10-09、平野さん）

* チャット側の問い「#491 の本文の `READ_UNTIL` の行を不要として済にし、『親子関係』の行と題の『（毎朝の実行の遅れ・READ_UNTIL の延長）』も今の状態に合わせて直すか」に「直す」
* チャット側の問い「概要欄だけから作る予定の名前に『別名』の訂正がかからない件（F）を、記録のままにするか、今のうちに直すか」に「直す」（チャット側の推しは「記録のまま」だったが、直すと決めた）
* チャット側の問い「残りが #488 の待ちだけになったら、#491 を #488 の親として開けておくか、クローズして #488 を親なしで待たせるか」に「b」（#491 をクローズし、#488 は親なしで待つ。#497 と同じ扱い）

前提（チャット側。平野さんの決定ではない）

* CHAT-1002-CLD-20 の報告: `READ_UNTIL` は 9b877ae0（2026-09-30、CHAT-0930-CAL-08）で取り除かれている。#488 の付け替えは 2026-10-09（CHAT-1002-CLD-19）に済。毎朝の実行の遅れは #504 で追う。`tMwcjumwz-o`・`FXtYzZBEtXA` はカレンダー上で直っている
* F の中身（CHAT-1005-UNR-08 のコメントの要旨）: /live の【2】【3】のレコードが無い動画で、概要欄だけから作る予定の名前には「別名」の訂正をかけていない。そのときの計画で、この形の重複は出ていなかった。概要欄から足す名前への訂正は 8a764a98（CHAT-1004-UNR-06）で入っている。直す場所はそこに近いと見ている（要確認）
* 直しの結果、今の計画で変わる予定は0件か、少ない見込み（要確認）
* #491 のクローズと #488 の親子関係の解除は、この指示ではしない（F の直しのマージの後、続きの指示で行う）
* 決定の記録は docs/decisions/broadcast-calendar.md に足す。作業ブランチのコードの変更と一緒にマージを待つ

手順

1. #491 の本文を直す（書き換える前の本文をログに貼る）。
   * 題: 「（毎朝の実行の遅れ・READ_UNTIL の延長）」を外し、今の残りが分かる題にする（案: 「放送対局カレンダーの運用の残り」）
   * 「残り」: `READ_UNTIL` の行を済（不要）にし、理由（2026-09-30 の全期間の取り込み〈9b877ae0、CHAT-0930-CAL-08〉で取り除いた）を書く。F を「[ ] 概要欄だけから作る予定の名前に『別名』の訂正をかける（CHAT-1002-CLD-21 で直す）」として足す
   * 「親子関係」: #488 は 2026-10-09 に #448 から付け替えたこと、#491 のクローズ時に #488 を親なしにすること（平野さんの決定、2026-10-09）を書く
   * 書き換えた後の本文をログに貼る
2. F を直す。
   * 概要欄だけから予定を作る経路と、名前に「別名」の訂正をかける経路（8a764a98）を、関数名とコードの引用で書く
   * 概要欄だけから作る予定の名前にも、/live のレコードのある予定と同じ「別名」の訂正をかける。テストを足す（訂正がかかる例・かからない例）
   * 模擬（書き込みなし）で、cloudflare の今のコードと直した後のコードの計画を比べ、変わる予定（日付・動画ID・件名・変わる前と後の【対局者】【実況】【解説】）と、追加・更新・削除の件数を書く。削除の件数を `MAX_DELETES` と比べる。変わる予定が0件なら、そう書き、訂正がかかる形の予定が今あるかも書く
3. docs/decisions/broadcast-calendar.md に、上の3つの決定を1節足す（README の書き方に合わせる）。
4. 判断待ちで止まる。作業ブランチは片付けずに残す。

止まる条件

* 0章で #491 が Closed、または他セッションの着手中コメントがある
* 模擬で、「別名」の訂正のほかに変わる予定がある（件名の変化、名前の訂正と関係の無い説明欄の変化、予定の追加・削除など）。一覧を書いて止まる
* 削除の件数が `MAX_DELETES` を超える見込み
* 直すために、ワークフロー・シート・カレンダーを書き換える必要が出た。書き込みありの実行もしない
* docs/decisions/ の既存の決定と矛盾していて、どちらが正か平野さんの判断が要る
* 作業ブランチへの push や #491 の書き換えが権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、#491 の本文の前後、F の経路（関数名とコードの引用）、変えたファイルと差分の要旨、テストの結果、模擬の結果（変わる予定の一覧と件数、`MAX_DELETES` との比較）、docs/decisions/ に足した文面、本番で確かめられていないこと（次の毎朝の同期での反映）を入れる
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-21` は無し
- 作業ブランチ: リモートの `work/1002-cld`（c125a19d）は `origin/cloudflare` の祖先（マージ済み）。ローカルも同じだったため `git merge --ff-only origin/cloudflare`（d2ff5f93）
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- CHAT-1002-CLD-20 のログの `## 報告` の状態に「 / 続き: CHAT-1002-CLD-21」を足した（`## 指示` 欄が変わっていないことを確かめた）

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: #491
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2ff5f93）: https://github.com/retroeater/mj-logs/tree/main/guide/d2ff5f93

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2ff5f93/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
