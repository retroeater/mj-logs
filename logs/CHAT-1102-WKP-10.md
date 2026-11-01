# CHAT-1102-WKP-10

- 着手日時: 2026-11-02
- 対象issue: #564
- ブランチ: work/1102-wkp
- 着手時HEAD: 93bd46fe5

## 指示

【Claude作成】Claude Code 向け指示：WKP-09 の続き（cleanup-logs は週次に残し、ほかの5本を run-daily へ移す。時刻の変更と案A の起票も行う） Chat-Ref: CHAT-1102-WKP-10 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる 貼る時機: CHAT-1102-WKP-09 が判断待ちで止まった後（WKP の同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1102-wkp を続けて使う（WKP-09 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうし、または docs/notes/github-actions.md の表の隣り合う行の衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1102-WKP-09 のログの `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、WKP-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-WKP-10` を足す。

目的
CHAT-1102-WKP-09 の判断待ちへの回答。止まった手順2・3（時刻の変更・移す・案A の起票・マージ）を、cleanup-logs を週次に残す形で行う。
決定（2026-11-02、平野さん）

* cleanup-logs は run-weekly に残す（WKP-09 の案 (1)）。ほかの5本（check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts → convert-x-posts）を run-daily へ移す。run-weekly に残るのは fetch-youtube と cleanup-logs
* WKP-09 の決定のほか（時刻: run-monthly は毎月1日 02:00、run-weekly は月曜 03:00、run-daily は 04:00 のまま。案A の issue の起票）はそのまま
* マージは、作業ブランチの手動実行がすべて success で、止まる条件に1つも当たらなければ行ってよい

前提（チャット側。平野さんの決定ではない）

* WKP-09 の手順1の表（同じログの `## 経過`）のとおり、移す5本は変わらない日に書き込みやコメントをしない。fetch-x-posts は毎日にすると X API の読み直しで月に約 $0.65 増える見込み（$1 未満で止まる条件に当たらない）。check-meibo・check-saikyo-unregistered は問題が続くあいだ issue の本文を毎日書き換えるが、通知ではない
* WKP-09 の指示文の手順2・3・止まる条件は、この指示で書き直したところ（cleanup-logs を移さない）のほかはそのまま有効。手順2の文書の洗い出しで、cleanup-logs の記述は「週次（run-weekly、月曜 03:00）」に直すだけにする
* 案A の issue の本文には、cleanup-logs を週次に残した理由（毎日だと #357 に同じ一覧のコメントが毎日付く。WKP-09 の手順1）と、全部を日次にするなら cleanup-logs の通知をどうするか（WKP-09 の案 (2)・(3)）も検討の点として書く
* この指示を書いた時点（2026-11-02 07:45 JST）で、mj-logs の actions/morning.md の1節に cloudflare の失敗は無い。例外にする既知の失敗は無い
* work/1028-lgn が convert-x-posts.yml を変えている（WKP-09 の着手時の確かめ）。今回は convert-x-posts.yml を変えない

手順

1. `git branch -r --no-merged origin/cloudflare` で、WKP-09 の後に増えたブランチを含めて重なりを見直す
2. WKP-09 の手順2を、上の決定の形で行う（schedule.json の時刻、run-weekly.yml は fetch-youtube と cleanup-logs、run-daily.yml に5本、文書・テスト、作業ブランチでの run-daily・run-weekly の手動実行がすべて success）
3. WKP-09 の手順3を行う（案A の起票、決定を docs/decisions/automation.md に足す〈WKP-09・WKP-10 の両方〉、#564 に結果をコメント、止まる条件に当たらなければ cloudflare へマージ、平野さんへの手作業〈Workers Builds の mj-scheduler のビルドが Ready か〉を最終報告に書く）

止まる条件

* WKP-09 のログの `## 報告` の状態が「判断待ち」でない
* WKP-09 の止まる条件のうち、手順1の (a)(b) のほかのもの（#564 が閉じている・未マージのブランチとの重なり・手動実行が success にならない・作業ブランチの外への書き込み・マージの差分の種類・push の拒否・マージの後の check-run の失敗の扱い）
* 手動実行で例外にする既知の失敗は無い（外のサイト・API の一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1102-wkp/docs/logs/CHAT-1102-WKP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 90df278d）: https://github.com/retroeater/mj-logs/tree/main/guide/90df278d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/90df278d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b1c2c3de.md
