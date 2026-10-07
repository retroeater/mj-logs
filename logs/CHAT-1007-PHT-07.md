# CHAT-1007-PHT-07

- 着手日時: 2026-10-07
- 対象issue: #357
- ブランチ: work/1007-pht-logs
- 着手時HEAD: 1c5d349b

## 指示

【Claude作成】Claude Code 向け指示：7日以上たった自動削除の条件外の作業ログを、10/12 の通知を待たずに仕分けし、論点が片付いているものを削除する Chat-Ref: CHAT-1007-PHT-07 マージ: ドキュメントのみ（docs/logs/ の削除とこのログ・docs/notes/ の参照の差し替え・docs/decisions/）の変更なので、完了報告のうえ cloudflare へ入れてよい（状態が判断待ちでも入れる）。それ以外のファイルを変える必要が出たら、変えずに「判断が必要なこと」に書く 貼る時機: いつでも（CHAT-1007-PHT-08・CHAT-1006-PHT-06 とは別のセッションに貼る。どちらの完了も待たない） 作業ブランチ: クラウドセッションで実行する。work/1007-pht-logs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-pht-logs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-pht-logs の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 PHT は同じチャットの CHAT-1006-PHT-01〜06 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
自動削除の条件（cleanup-logs.yml）に当たらない作業ログが溜まり、2026-10-12 の週次実行で約280件が #357 に通知される見込み（CHAT-1006-PHT-05 の調査）。通知を待たずに、いま7日以上たっている分を CHAT-1006-PHT-03 と同じ手順で片付ける。
決定（2026-10-06、平野さん）

* 今あるログの仕分けは、10/12 の通知を待たずに始める（7日たった分を先に片付け、来週の通知を減らす）
* 問題ないもの（論点が片付いているもの）は削除する。迷うものは残して移し先の案を報告する（CHAT-1006-PHT-03 のときの決定。docs/decisions/operations.md）

前提（チャット側。平野さんの決定ではない）

* 対象: この指示の実行時点で、cloudflare の docs/logs/ にあり、最終コミットから7日以上たち、自動削除の条件に当たらないログ（`scripts/cleanup_logs.py --dry-run` が通知の対象にするもの）。#357 の通知の一覧は無いので、対象の一覧は実物から作る
* 件数の見込み: CHAT-1006-PHT-05 のログでは、2026-10-06 15:49 JST の時点で通知の対象が145件。最終コミットが 09-28 のログが97件、09-29 が77件、09-30 が52件（UTC の日付）。実行の時刻によって 140〜230件の見込み
* 手順は CHAT-1006-PHT-03（mj-logs のログで読んだ。159件を 甲 147・乙 9・丙 3 に仕分け、甲のうち scripts/ から参照されていた2件を除く145件を削除）と同じにする。甲・乙・丙の定義も同じ
* 残すログ: `CHAT-0929-GX-08.md` は、甲でも削除しない。平野さんのカレンダーの【R#298】の予定が「チャットの最初に送るログ」として、mj-logs のこのログを名指ししている（チャット側が 2026-10-07 にカレンダーで確かめた。ほかに名指しされているのは CHAT-1002-CLD-18・CHAT-1006-WKR-08 で、どちらも7日たっていない見込み）
* 論点の issue への移動・起票は、この指示では行わない（移し先は平野さんが報告を見て決める）
* cloudflare で削除したログは、sync-logs.yml が mj-logs の logs/ からも消す（CHAT-1006-PHT-03・PHT-04 で確認）
* 自動削除の条件と書く側の規則の見直しは CHAT-1007-PHT-08 で別に進める。この指示では条件・規則を変えない
* 使う skill は無い

手順

1. 対象を作る。`scripts/cleanup_logs.py --dry-run` と cloudflare の docs/logs/ から対象の一覧を作り、件数と系列ごとの件数をログに書く。自動削除の条件に当たるログ（次の週次実行で消えるもの）は対象にしない。#357 に他セッションの着手中コメントが無ければ、着手中のコメントを残す。Open の issue の本文・コメントのうち、対象のログを「次の作業で読むログ」「チャットの最初に送るログ」として名指ししているものを、検索で拾える範囲で探し（検索語の例: `mj-logs/blob/main/logs/CHAT-`、`docs/logs/CHAT-`）、見つかったログは残すログに加える
2. 仕分ける。各ログを次の3つに分け、表（ファイル名・区分・一言）をこのログに書く。甲は系列ごとにまとめてよいが、乙・丙は全件を書く。判定には、そのログの本文、同じ系列の後のログ（「続き:」の先を含む）、ログが挙げる issue の状態（Open/Closed と最新コメント）、ブランチのマージ状況、docs/ の記述を使う
   * 甲: 論点が片付いている（判断が必要なこと・未確認の項目・エラーの各項目が、後のログ・issue・マージ・docs で解消済み、または記録済みと確かめられる）。どこで片付いたかを1行で添える
   * 乙: 論点が残っている（どこにも記録が無い、または issue が Open のまま未反映）。論点の要旨と、移し先の候補（既存の issue 番号、無ければ新規起票の候補）を添える。同じ論点は1つにまとめる
   * 丙: 判定できない（理由を添える）
   * 迷うもの（平野さんの本番・実機での目視が残る、数の食い違いの理由が書かれていない、未マージのブランチが残っている、など）は甲にせず、乙か丙にする
   * 状態が判断待ちで、続きの指示がまだ出ていないログ（平野さんの判断を待っている最中のもの）は、甲にしない
   * サブエージェントに読ませたときは、系列ごとに2件以上を自分で読み直して判定を確かめ、確かめたログの名前を書く
3. 甲を削除する
   * 先に、docs/（docs/logs/ を除く）・CLAUDE.md・scripts/・.github/ から、甲のログのファイルパス（`docs/logs/<ファイル名>`）を参照している箇所を洗い出す。docs/ と CLAUDE.md の中の参照は、削除前の cloudflare の SHA に固定した `https://github.com/retroeater/mj/blob/<SHA>/docs/logs/<ファイル名>` に差し替える（文の意味は変えない。Chat-Ref を文字として書いているだけの箇所は変えない）。scripts/・.github/ の参照は差し替えず、箇所を「判断が必要なこと」に書き、そのログは削除しない
   * 甲のログ（残すログを除く）を1コミットで削除する。削除したファイルの数と、そのコミットに docs/logs/ 以外のファイル・甲以外のログが入っていないこと（`git show --stat`）をログに書く
   * cloudflare へマージし、push で走ったワークフロー（sync-logs.yml ほか）の結果と、mj-logs の logs/ から甲の写しが消えたか（残った件数）を書く
   * #357 に、片付けの要約（対象の件数、削除した件数、残した件数と理由の内訳、2026-10-12 の通知の見込み）をコメントする
   * `## 報告` の「判断が必要なこと」に、乙・丙・残したログの全件（ファイル名・論点の要旨・移し先の候補）を表で書く。乙・丙が1件でもあれば状態は「判断待ち」にする

待ち方

* ワークフローの完了と mj-logs への反映を待つのは、それぞれ15分まで。超えたら待つのをやめ、その時点の状態をログに書き、確かめられなかったことを「未確認の項目」に回して先へ進む（上限の無い待機は使わない）

止まる条件

* 対象の件数が 120〜260 の外
* #357 に他セッションの着手中コメントがある
* 対象のログに、cloudflare へマージされていない版だけにある内容がある（そのログは丙にして残す。全体は止めない）
* 削除のコミットに docs/logs/ 以外のファイル、甲以外のログ、残すログが含まれる
* push の後にワークフローが失敗した（再実行は1回まで。sync-logs.yml が concurrency で cancelled になったときは再実行してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 対象の一覧の作り方と件数、区分ごとの件数、甲の根拠（系列ごと）、乙・丙の全件の表、差し替えた参照、削除のコミット、マージのコミット、ワークフローの結果、#357 へのコメントがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-PHT-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-PHT-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1007-PHT-07 のコミット無し。識別子 PHT は指示文のとおり同じチャットのもの。指示欄の末尾は指示文の最後の行と一致
- work/1007-pht-logs はリモート・ローカルとも無かったため origin/cloudflare から作成

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1007-pht-logs
- ログ: https://github.com/retroeater/mj/blob/work/1007-pht-logs/docs/logs/CHAT-1007-PHT-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-pht-logs
- 確認用URL: なし
- マージ: 未
- issue: #357
- 判断が必要なこと: なし
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 58198b2e）: https://github.com/retroeater/mj-logs/tree/main/guide/58198b2e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/58198b2e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1b684c6b.md
