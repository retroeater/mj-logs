# CHAT-1103-TOP-08

- 着手日時: 2026-11-03
- 対象issue: #101
- ブランチ: work/1103-top
- 着手時HEAD: bedfbd0a2

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の面の直し（大会日程・新着の番組のサムネイルと「もっと見る」、誕生日、#101） Chat-Ref: CHAT-1103-TOP-08 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: CHAT-1103-TOP-07 が判断待ちで止まった後（同じセッションの続きで可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-07 の上部の直しの上に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1103-TOP-07 のログの `## 報告` の状態が「判断待ち」で、origin/work/1103-top が cloudflare に未マージであることを確かめる（違えば何もせず止まる）。TOP-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-TOP-08` を足す。

目的
新トップ `top/` の3つの面を、見て分かり、続きを見られる形にする: 開催中・直近の大会と新着の番組に動画のサムネイルと「もっと見る」、誕生日に選手の写真・今日の日付・明日以降の続き。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* 「開催中・直近の大会」: 各行に動画のサムネイルを出す。「ほかN件」をやめて「もっと見る」にする
* 「今日が誕生日の選手」: 見出しを「誕生日」にし、見出しの横に今日の日付（「11月3日（火）」の形）を出す。各行に X のプロフィール画像を出す。「もっと見る」を押すと明日以降の分を出す
* 「新着の番組」: 各行に動画のサムネイルを出し、「もっと見る」を付ける
* 「もっと見る」は、その場で続きを開く（同じページの中で行が増える。データは生成時に焼く）
* 件数: 開催中・直近の大会は最初5件、押すごとに10件ずつ増やす。新着の番組は最初3件、押すと12件まで。誕生日は最初は今日の人だけ、押すと明日から7日分を日付ごとに出す。今日が0人の日は「今日の誕生日の選手はいません」の1行と「もっと見る」
* サムネイルの無い行（YouTube の枠がまだ無い予定）は、灰色の箱に大会名の頭の文字を入れる（行の高さをそろえる）。X の画像が無い選手は pro/ と同じく名前の頭の1字

前提（チャット側。平野さんの決定ではない）

* サムネイルの元: 放送の枠は YouTube の動画 ID（予定の行き先の `watch?v=`）から `i.ytimg.com` の画像（live/ の旧カード・pro/ の放送対局の行と同じ取り方。CSP の `img-src https:` の範囲で、新しい外部ドメインは増えない見込み。要確認）。番組の回は各番組の一覧・各話のページと同じサムネイル。大きさ・形は live/・pro/ の行の値に合わせる（docs/notes/design.md）
* X のプロフィール画像は pro/ の名前の面と同じ元（SNS ブック、`_400x400` の扱い。docs/notes/sns-book.md・pro-pages.md）。大きさは pro/ の一覧の 40px の丸
* 誕生日の「明日から7日分」: TOP-02 で前後3日を焼いている（`BIRTHDAY_DAYS`）。7日分を出すため、焼く範囲を「今日の前日〜今日から8日後」程度に広げる（生成が1〜2日止まっても今日と7日分が出る範囲。仮置き）。日付の出し分けは JS が日本時間の今日で行う（今の形のまま）。今日の日付の表示も JS が出す
* 「もっと見る」の部品は1つの JS（`assets/top.js` など）で3つの面に使う。押した後の位置（ボタンが行の下に移る）・読み上げ（増えた件数を知らせる `aria-live` など）・すべて出したらボタンを消す、は仮置きで作る
* 新着の番組の12件・大会日程の全件をすべて焼く（HTML の大きさの増え方を報告する。目安として top/index.html が今の2倍を超えるなら、焼く件数を減らす案を「判断が必要なこと」に書く）
* 面の並び・上部・質問欄は TOP-07 のとおり。お気に入りの差し込み口はそのまま

手順

1. サムネイルと X の画像の取り方（live/・pro/ のどの関数・データを使うか、外部ドメインが増えないか）と、焼く件数で top/index.html がどれだけ大きくなるかの見込みを `## 経過` に書く。#101 に着手中コメント
2. 3つの面を直す（上の決定と前提）。「もっと見る」の JS、テスト（`scripts/tests/test_top_page.py`: 件数と並び・サムネイルの無い行の箱・誕生日の範囲と今日0人の行・新着の12件）、`docs/notes/top-page.md`・`docs/decisions/top.md` を直す
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）・画面の確かめ（360px・400px、明暗。端のデータ: 大会日程の全件を開いた状態・枠の無い予定の行・誕生日が今日0人の日〈JS の今日を変えて確かめる〉・X の画像の無い選手・いちばん長い題）を流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* TOP-07 が判断待ちでない、または work/1103-top がマージ済み
* サムネイル・X の画像のために新しい外部ドメイン（CSP の変更）が要る
* 未マージの work/ ブランチが `scripts/generate_top_page.py`・`assets/top.js`・`scripts/tests/test_top_page.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* top/ 以外のページの生成物に差分が出た（見込みは0件）
* #101 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45487824）: https://github.com/retroeater/mj-logs/tree/main/guide/45487824

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
