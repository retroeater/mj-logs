# CHAT-1026-OHA-14

- 着手日時: 2026-10-26
- 対象issue: #563
- ブランチ: work/1026-oha
- 着手時HEAD: 59f3c4838

## 指示

【Claude作成】Claude Code 向け指示：morning.md の3節で、中央値の基準を「3節に載せる実行と同じ種類」に絞る（#563、MRP-01 への返事） Chat-Ref: CHAT-1026-OHA-14 マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件をすべて満たすときだけ。1つでも外れたらマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-oha の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1026-oha を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-oha origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#563 の CHAT-1026-MRP-01 のコメント（2026-10-26）の「頼むこと」1・2 に、平野さんの決定で答える（CHAT-1026-OHA-01 は識別子の重複の確認で、ログを作る前に止まったので欠番。OHA の系列の続きとしてこの番号で出し直す）。morning.md の「3. 所要時間の異常」で、比べる基準（中央値）を、3節に載せる実行と同じ種類の実行だけから取るようにする。
決定（2026-10-26、平野さん）

* MRP-01 の「頼むこと」1（中央値の母集団）: 3節に載せる実行と同じ種類の実行だけを基準にする。cloudflare の実行で、かつ予約の実行かどうか（題の先頭が `[scheduled]` か）が、判定する実行とそろっているものに絞る
* MRP-01 の「頼むこと」2（run-weekly のように中身が変わる週次の束ね）: 3節の対象のままにする。外さない。基準値をワークフローごとに変える仕組みも作らない。上の絞り込みで直す
* この直しは今入れる。#563 は 10/28 の5日分の確認の後に閉じる（この直しのための別の issue は立てない）

前提（チャット側。平野さんの決定ではない）

* チャット側が cloudflare の `scripts/morning_report.py`（2026-10-26 09:00 JST ごろの版）を REST で読んだ。基準は `write()` の中の `durations = ... runs[:MEDIAN_RUNS]`（比の判定の中央値）と、`others = ... runs[:MEDIAN_RUNS] if o is not r`（絶対値の判定の中央値）の2か所。どちらもブランチで絞っていない。チャット側の案は、両方を同じ条件（cloudflare、かつ「予約の実行か」が判定する実行と同じ）で絞ること。比の中央値は判定する実行ごとに変わってよい（要確認）
* 予約の実行かどうかの見分け: run-daily・run-weekly は、Worker からの起動の題の先頭に `[scheduled]` が付いている（チャット側が REST で、両方の直近の実行の `display_title` を見て確かめた）。`[scheduled]` の目印が無いワークフローでは、`event == 'schedule'` を予約の実行とみなす案がある。既存の `SCHEDULED_EVENTS` などとの合わせ方は実物を見て決めてよい。決め方はログに書く
* `write()` に渡る実行のデータに `display_title` が入っているか（要確認）。入っていなければ、取得のところで足してよい
* 見込み: 10/26 の run-weekly の実行（run 40214364620、cloudflare・[scheduled]、所要 12分21秒）は、同じ種類のほかの実行が無い。今の作りの「ほかの実行が無ければ絶対値で見る」によって、「10分以上」の判定で3節に残る。来週からは 12分台どうしで比べるので出なくなる見込み
* 3節の見出しは「cloudflare の予約の実行だけ」の趣旨に直す。文面は実物に合わせてよい（今の見出しは「cloudflare の実行だけ。直近20回の中央値の…」）
* docs/decisions/automation.md にある 3節の判定の既存の決定（「中央値＋5分以上」の追加、#563。「ほかの実行の中央値が10分未満の本だけ絶対値で見る」）は変えない。基準を取る実行の範囲が変わるだけ

手順

1. #563 に着手中のコメントを書く（本文・ラベル・状態は変えない）。`git branch -r --no-merged origin/cloudflare` で、`scripts/morning_report.py` の `write()` の3節の判定か、そのテストを変えている未マージのブランチが無いかを確かめる。そのあと、上の「前提」の2か所の作りが今もそのとおりかを確かめ、違えば止まる
2. 中央値の基準（比・絶対値の両方）を、判定する実行と同じ種類（cloudflare、かつ予約の実行かどうかが同じ）の実行だけから取るように直す。3節の見出しも直し、テストを足す。テストには少なくとも次の2つを入れる。(a) run-weekly の今の5件の形（cloudflare・[scheduled] が1件で 12分21秒、ほかは手動の 0分台）で、その1件が「10分以上」で出ること。(b) 予約の実行が十分にあるワークフローで、cloudflare の手動の実行や作業ブランチの実行が基準に混ざらないこと
3. 同じ API の応答で、新旧のコードの morning.md を作って比べる。3節の差を全件、表にしてログに書く（行・ワークフロー・新旧の中央値・差の理由）。1節・2節・4節・5節と、status.md・運行表に差が無いことも確かめる。マージの条件を満たせば cloudflare へ入れる。そのあと、#563 に、平野さんの決定と結果を書く。中身はログの `## 経過` の「結果」から引用する。マージ後の最初の mj-logs の同期の書き出し（actions/morning.md）の3節もログに引用する。同期を待つのは CLAUDE.md の待機の上限まで。それまでに書き出されなければ「未確認」と書く

止まる条件

* 「前提」の2か所の作りが今と違う。または、未マージの work/ ブランチが `write()` の3節の判定か、そのテストの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #563 に、他のセッションの着手中のコメントで、まだ完了のコメントが無いものがある
* マージの条件（すべて満たすときだけマージする。1つでも外れたら、マージせずにここまでの結果をログに書いて止まる）
   * 既存と追加のテストがすべて通る
   * 手順3の比較で、3節の差がすべて、基準の実行の範囲を絞ったこと（cloudflare・予約の実行かどうか）で説明できる。説明できない差が1行でもあれば止まる
   * 10/26 の run-weekly（run 40214364620）の行が、新しいコードでも3節に残る（「10分以上」の判定で）
   * 1節・2節・4節・5節と、status.md・運行表に差が無い
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら、原因を報告に書いたうえで残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-OHA-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-OHA-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

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

ガイド文書（この版を写した時点の最新、mj 59f3c483）: https://github.com/retroeater/mj-logs/tree/main/guide/59f3c483

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/59f3c483/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdf27d95.md
