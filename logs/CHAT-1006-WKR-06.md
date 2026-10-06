# CHAT-1006-WKR-06

- 着手日時: 2026-10-06
- 対象issue: #505（関連 #504・#503）
- ブランチ: work/1006-wkr-06
- 着手時HEAD: 未取得（HEAD の SHA を読むコマンドは WKR-01 で分類器に拒否されたため取りにいっていない。#493）。origin/cloudflare から作成

## 指示

【Claude作成】Claude Code 向け指示：#505 予約実行の起動時刻・依存関係・並行実行の可否の包括的な確認（調査だけ。結果は #505 のコメントに書く。実装しない） Chat-Ref: CHAT-1006-WKR-06 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉。docs/logs/ 以外のファイルを変える必要が出たら、マージせず判断待ちで止まる 貼る時機: いつでも（CHAT-1006-WKR-05 は完了・マージ済み。チャット側がログで確かめた） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-wkr-06〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-wkr-06 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1006-wkr-06 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-wkr-06 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 変更の範囲: #505 へのコメント（着手・結果）と docs/logs/ だけ。コード・ワークフロー・`workers/`・ほかの文書・#504 の本文は変えない。ワークフローの手動実行もしない。#505 は閉じない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#504 の段階2（毎日の3本を Worker からの起動に移す）に進む前に済ませると決めてある #505 の確認を行い、段階2の指示を書くための材料と、平野さんが決める点をそろえる。CHAT-1006-WKR-04 は送る前に差し替えたため欠番（WKR-05 として出した）。
決定（平野さん）

* なし（この指示に新しい決定は無い。「起動時刻・依存関係・並行実行の可否の包括的な確認を別の issue で行う」「段階2の前に済ませる」は 2026-10-05 の決定で、docs/decisions/automation.md と #504・#505 にある）

前提（チャット側。平野さんの決定ではない）

* 識別子 WKR は同じチャットの WKR-01〜05 で使っている（04 は欠番）。それらのコミットやログが見つかっても、同じチャットの指示なので重複ではない
* やることと完了の条件の正は #505 の本文とコメント。 チャット側は #505 を読めていない（CHAT-1005-RUN-10 のログの「全16ワークフローの契機と concurrency の組の表、やること1〜3、#504 との関係の案、完了の条件」という記述と、CHAT-1005-RUN-11 で足したコメントの記述だけを知っている）。本文とこの指示が食い違えば本文に従い、食い違いを報告に書く
* 起動時刻の案は #504 の本文の表（04:00 update-live-channel、04:15 sync-dojo-calendar、04:20 delete-merged-branches、月曜の 04:25〜04:55、毎月1日の 05:00 fetch-gsc、05:30 sync-logs、06:00 朝の確かめ）。これを見直す
* 段階1で分かったこと（どれもログで確かめた）: Worker からの最初の起動は予定から 36 秒の遅れ（CHAT-1006-WKR-03）。今の予約実行も残してあり、10/6 は delete-merged-branches が 04:20（Worker）と 11:32（`schedule`、予定は 07:53）の2回動いた（mj-logs の `actions/status.md` 10/6 12:04 の版）。Build watch paths は cloudflare への push で効いている（CHAT-1006-WKR-05）
* #505 の本文のやることに加えて、チャット側が段階2の指示を書くために知りたいこと（本文と重なるものは1つにまとめてよい）
   1. 段階2で移す毎日の3本（update-live-channel・sync-dojo-calendar・sync-logs）ごとに、`schedule` の契機を見ている箇所の一覧（YAML の式、スクリプトの `GITHUB_EVENT_NAME` など。ファイルと行の内容）と、入力 `scheduled` を足したときに置き換える箇所。`workflow_call` で呼ばれる regenerate-page.yml に契機がどう伝わるか。今の入力の数（上限は 25）。delete-merged-branches.yml で入れた形（入力 `scheduled`・`run-name`・判定。docs/notes/scheduler-worker.md「予約の起動の見分け方」）をそのまま使えるか
   2. 時刻を早めることと外部との関係: update-live-channel を 02:43 から 04:00 にしたとき（シートの編集の時間帯の記録は docs/notes/live-channel-write.md、YouTube・予定表・公開カレンダーへの反映）、sync-dojo-calendar を 07:12 から 04:15 にしたとき（連盟サイトの告知画像が差し替わる時刻の実績、#503 のタイムアウト、`--auto-update`）。早めて困ること・変わらないこと
   3. 並行実行: concurrency の組ごとに、Worker からの起動（5分刻み）・push の契機・手動実行が重なったときに「待つ」のか「取り消す」のか。sync-logs の実行が取り消されたとき、その分が後の実行で必ず写るか（#505 のコメントの論点）。05:30 の Worker からの sync-logs と、その前後の push の契機の実行の関係
   4. 保険として残す予約実行: 「当日にすでに成功していれば何もしない」ゲートの作り方の案と、置く時刻の案（今の遅れを踏まえる）。ゲートなしで同じ日に2回動いたときの害を、ワークフローごとに（delete-merged-branches は今ゲートなしで2回動いている）
   5. 朝の確かめ（06:00）との関係: 所要の長いもの（update-live-channel）・途中で regenerate-page を呼ぶもの・push がぶつかって再試行するものが、06:00 までに終わる見込みか。終わらないときに「実行中」と通知されることの扱い
   6. 遅れと所要時間の実測: 直近7日ほどの各ワークフローの `schedule` の実行について、予定からの遅れと所要時間（Actions の API の `created_at`・`run_started_at`・`updated_at`）。表にする
* 結果の置き場所: #505 のコメント（ログは定期削除の対象。表は Markdown。長ければ複数のコメントに分け、最初のコメントに目次を置く）。末尾に Chat-Ref。#504 の起動時刻の表を変える点があれば「案」として #505 に書く（#504 の本文は変えない）
* 事実と案を分ける: 実物（ワークフローのファイル・スクリプト・API の実測・公式の文書）で確かめたことと、推測・案を別の節にする。確かめられなかったことは「未確認」と書く
* 平野さんが決めること: 報告の「判断が必要なこと」に、番号を振り、選べる案と Code の推す案（理由を1行）を付けて書く。見込みでは「起動時刻の表をどう変えるか」「保険の予約実行の時刻とゲート」「段階2を3本まとめて移すか1本ずつか」が入る
* 着手の前に、#505 に他セッションの着手中のコメントが無いことを確かめ、着手中のコメントを残す
* ログは public（mj-logs）。人の個人情報・鍵の値は書かない

手順

1. #505（本文・コメント）と #504 の本文の起動時刻の表、#503 を読む。#505 が Open で、他セッションの着手中のコメントが無いことを確かめる
2. `.github/workflows/` の全ワークフローと、それが呼ぶスクリプトの契機の判定・push・外部への書き込みを読み、API で実測を取り、#505 のやることと上の 1〜6 をまとめる
3. 結果を #505 にコメントし、ログの報告を書いてマージする（docs/logs だけ）

止まる条件

* #505 が閉じている、または他セッションの着手中のコメントがある
* #505 の本文のやることが、この指示の範囲（調査だけ・コードを変えない）に収まらない（やれる範囲と残りを報告に書いて止まる）
* 調べる途中で、今の本番の動きに誤り（予約実行が壊れている、など）を見つけた（直さずに、報告の「判断が必要なこと」の先頭に書く。調査は続けてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「結果の要点」には、#505 のコメントの URL、起動時刻の表を変える案の要約（変えない場合はその旨）、段階2で直す箇所の数（ワークフローごと）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-WKR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-WKR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

1. Chat-Ref の確認: `git log --all --grep="CHAT-1006-WKR-06"` は0件。リモート・ローカルに `work/1006-wkr-06` は無い → `git checkout -b work/1006-wkr-06 origin/cloudflare`
2. 手順0: 指示欄の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
3. 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1006-wkr-06
- ログ: https://github.com/retroeater/mj/blob/work/1006-wkr-06/docs/logs/CHAT-1006-WKR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-wkr-06
- 確認用URL: なし
- マージ: 未
- issue: #505
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fad9eb53）: https://github.com/retroeater/mj-logs/tree/main/guide/fad9eb53

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96fa2201.md
