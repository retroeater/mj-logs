# CHAT-1102-WKP-15

- 着手日時: 2026-11-02
- 対象issue: なし
- ブランチ: work/1102-wkp
- 着手時HEAD: ef69064fa

## 指示

【Claude作成】Claude Code 向け指示：古い時刻と外したフォールバック実行の記述を、リポジトリ全体でまとめて洗い直して直す Chat-Ref: CHAT-1102-WKP-15 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおりで、1つでも当たればマージせずに止まる 貼る時機: いつでも（WKP の同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1102-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1102-WKP-14 の判断待ち（docs/notes/github-actions.md の「run-weekly（02:00 JST）」）への回答。WKP-10〜14 は、直してよい差分の範囲を毎回狭くしたため、1行ずつ直し漏れの往復が続いた。最後に1本、古い時刻と外したフォールバック実行の記述をリポジトリ全体でまとめて洗い、文言なら全部直す。
決定（2026-11-02、平野さん）

* 古い時刻（run-weekly 02:00・run-monthly 03:00・各本のフォールバック実行の時刻など）と、2026-11-01 に外したフォールバック実行（保険の予約実行）・update-live-channel のゲートの記述を、リポジトリ全体でまとめて洗い、文書・コメント・docstring・テストの期待値なら全部直す（WKP-14 の1行だけでなく）
* マージは、単体テストと Worker のテストがすべて通り、差分が古い時刻・外した保険の文言（文書・コメント・docstring・テストの期待値）だけで、生成物や動きの変わる行が無ければ行ってよい

前提（チャット側。平野さんの決定ではない）

* 今の事実（WKP-05・06・10 のログと、cloudflare の workers/scheduler/schedule.json で確かめ直す）:
   * Worker の起動の表: run-daily 毎日 04:00・run-weekly 月曜 03:00・run-monthly 毎月1日 02:00 JST（2026-11-02 の WKP-10 から）
   * run-daily: update-live-channel・update-sns-book・check-image-links・regenerate-saikyo・sync-dojo-calendar・cleanup-branches、2026-11-03 から check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts → convert-x-posts
   * run-weekly: cleanup-logs → fetch-youtube（→ regenerate-page の all）
   * run-monthly: fetch-gsc → fetch-redirect-counts
   * GitHub の `schedule`（フォールバック実行）: 毎日・週次の9本と update-live-channel のゲートは 2026-11-01 に外した（WKP-05・06）。残るのは fetch-gsc・fetch-redirect-counts の月次2本だけ（2026-12-01 の run-monthly の成功の後に外す予定）
* 洗い出しの検索の例（実物に合わせて足してよい）: `git grep -n -E '02:00|03:00|05:07|05:17|05:37|06:00|06:23|06:30|06:43|06:50|07:12|07:53|03:41|保険|フォールバック|ゲート|毎週月曜|週次|週1'`。範囲はリポジトリ全体（CLAUDE.md・docs/handover.md・docs/notes・.github・scripts・workers を含む）。除外は docs/decisions・docs/logs・docs/notes/handover-archive-2026.md の過去の行と、日付つきで経緯を書いた行（「2026-11-01 に外した」など、今の事実と合うもの）、止めたワークフローの記録（books-calendar.md・books-freeze.md など）
* 直す範囲は、古い時刻・外した保険・移した本の起動元の記述だけ。動きの変わる行（cron・条件の式・`github.event_name == 'schedule'` の判定など、WKP-05 で意図して残したもの）は変えない。生成物（ページ）に出る文言なら直さずに「判断が必要なこと」に書く
* WKP-14 の判断待ちの1行: docs/notes/github-actions.md 54行あたり（regenerate-page の説明）の「毎週月曜の`all`は`run-weekly.yml`（02:00 JST）の`fetch-youtube.yml`が」
* 未マージの work/1101-prp-pub が scripts/generate_jpml_pros.py の86行を変えている（WKP-13 の着手時の確かめ）。同じ行は触らない
* この指示を書いた時点で、mj-logs の actions/morning.md の1節に cloudflare の失敗は無い（06:24 JST の書き出しの時点）

手順

1. 今の事実を cloudflare の実物（schedule.json・run-daily.yml・run-weekly.yml・run-monthly.yml・各本の `on:`）で確かめ、上の前提と食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、直すファイルの同じ行を変えている未マージのブランチを見る（重なる行だけ外して進める）
2. 上の検索でリポジトリ全体を洗い、当たりを全件の表にする（ファイル・行・今の文・扱い〈直した・今の事実と合う・経緯の記録・動きの行・生成物に出る〉・直した文）。直すものを直し、テストの期待値も合わせる。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/scheduler/test/*.test.mjs'` を通す
3. 決定を docs/decisions/automation.md に足し、WKP-14 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-WKP-15` を足す。止まる条件に当たらなければ cloudflare へマージする

止まる条件

* 手順1の今の事実が前提と食い違う
* テストが通らない（自分の変更で落ちると分かっているテストの期待値は直してよい）
* マージの差分に、古い時刻・外した保険・移した本の起動元の文言とテストの期待値・docs/decisions・docs/logs のほか（動きの変わる行・生成物）が出る
* 直す文言が生成物（ページ）に出る（その箇所だけ直さずに「判断が必要なこと」に書き、残りは進めてよい。全部が当たるときだけ止まる）
* 未マージの work/ ブランチが同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なる行だけ外して進め、全部が重なるときだけ止まる）
* CLAUDE.md・docs/handover.md が警告域（26KB）を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗は、今回の変更によるものなら止まり、無関係なら原因をログに書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順2の表は全件で書き、「略」にしない（「今の事実と合う」で直さなかった行も載せる）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-WKP-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-WKP-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/work/1102-wkp/docs/logs/CHAT-1102-WKP-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ef69064f）: https://github.com/retroeater/mj-logs/tree/main/guide/ef69064f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ef69064f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c3c97901.md
