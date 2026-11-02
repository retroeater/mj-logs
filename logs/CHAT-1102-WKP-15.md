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

### 着手前の確かめ

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 作業ブランチ: work/1102-wkp はリモート・ローカルとも origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で ef69064fa に進めた
- 指示文の issue の state: 着手・コメント・クローズの対象の issue は無い
- 今の事実（cloudflare の実物）は前提と合った:
  - `workers/scheduler/schedule.json`: run-daily 04:00、run-weekly 03:00（`weekdays: [1]`）、run-monthly 02:00（`monthdays: [1]`）
  - run-daily の `uses:`: update-live-channel・update-sns-book・check-image-links・regenerate-saikyo・sync-dojo-calendar・cleanup-branches・check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-posts・convert-x-posts
  - run-weekly: cleanup-logs・fetch-youtube。run-monthly: fetch-gsc・fetch-redirect-counts
  - `on.schedule` を持つのは fetch-gsc.yml・fetch-redirect-counts.yml だけ
  - update-live-channel.yml のステップ `id: gate` は run・apply・verify を決めるだけで、外したゲート（当日の予約の起動の照会）は無い
- 未マージの work/ ブランチで直すファイルを変えているもの: work/1028-lgn（new-page-checklist.md 35行・github-actions.md 35・45・95・155行あたり）、work/1029-lgn-page（new-page-checklist.md 36行）、work/1101-prp-pub（github-actions.md 26行・redirects.md 5〜7・68行）。今回直す行（new-page-checklist.md 69・github-actions.md 54・111・191・redirects.md 119・124）とは重ならない

### 洗い出し

検索: `git grep -n -E '02:00|03:00|05:07|05:17|05:37|06:00|06:23|06:30|06:43|06:50|07:12|07:53|03:41|保険|フォールバック|ゲート|毎週月曜|週次|週1'`（リポジトリ全体。docs/decisions・docs/logs・handover-archive-2026.md と、*.html・*.json・*.csv・*.xml・docs/gsc・docs/redirect-counts・data・assets/vendor を除いた）。当たりは 304 件。分類は新しい文脈のサブエージェントが各ファイルの前後を読んで行い、私が「直す」の全件と直した文を確かめてから当てた。

扱いごとの件数: 今の事実と合う 131・無関係 100（CSS・テストのフィクスチャの時刻・0章ゲート・#170 のゲートなど）・経緯の記録 32・動きの行 28（cron・条件の式・Worker のテストの時刻の検査・run-name の式）・直す 12・生成物に出る 2。

| ファイル:行 | 今の文（要点、80字まで） | 扱い | 直した文（直すものだけ、要点） |
|---|---|---|---|
| .github/actions/resolve-mode/action.yml:4 | # 本ごとの判定(保険のゲート・曜日・予約の起動で外す入力など)は出力 scheduled を使って各本に残す | 直す | 「本ごとの判定(保険のゲート・曜日・予約の起動で外す入力など)」→「本ごとの判定(曜日・予約の起動で外す入力など)」 |
| .github/workflows/check-assets.yml:7 | # ゲートが別途必要(#170で検討中)。詳細は docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」。 | 無関係 |  |
| .github/workflows/cleanup-logs.yml:2 | # Worker からの予約の起動(scheduled が真)は題の先頭に目印を付ける(#504。週次は run-weekly.yml から呼ぶ、#564 の段… | 今の事実と合う |  |
| .github/workflows/convert-x-posts.yml:4 | # 毎日 04:00 JST に run-daily.yml から(入力 scheduled。2026-11-02 は毎週月曜 02:00 の run-week… | 経緯の記録 |  |
| .github/workflows/fetch-gsc.yml:47 | # 毎月1日 06:00 JST（前月末日 21:00 UTC）。cron に「月末」は書けないため、 | 今の事実と合う |  |
| .github/workflows/fetch-redirect-counts.yml:15 | # 予約は Worker mj-scheduler の起動の表の run-monthly.yml から fetch-gsc.yml の後に呼ぶ(#564 の段④… | 今の事実と合う |  |
| .github/workflows/fetch-redirect-counts.yml:53 | # 毎月1日 06:30 JST(前月末日 21:30 UTC)。fetch-gsc.yml(06:00 JST)の後。cron に「月末」は書けないため、 | 今の事実と合う |  |
| .github/workflows/fetch-x-posts.yml:4 | # 毎日 04:00 JST に run-daily.yml から(入力 scheduled。2026-11-02 は毎週月曜 02:00 の run-week… | 経緯の記録 |  |
| .github/workflows/fetch-youtube.yml:2 | # Worker からの予約の起動(scheduled が真)は題の先頭に目印を付ける(#504。週次は run-weekly.yml から呼ぶ、#564 の段… | 今の事実と合う |  |
| .github/workflows/fetch-youtube.yml:10 | # 週次の起動は Worker の起動の表の run-weekly.yml から(#564 の段④)。 | 今の事実と合う |  |
| .github/workflows/regenerate-page.yml:33 | # 週次の all は fetch-youtube.yml(YouTube の情報の取得)が取得の後に wayhome を真にして呼ぶ(毎週月曜 03:00 J… | 今の事実と合う |  |
| .github/workflows/regenerate-page.yml:93 | # 週次実行・push検知の挙動は変わらない。workflow_dispatch は画面で選んだブランチになり、 | 今の事実と合う |  |
| .github/workflows/regenerate-page.yml:112 | # YouTubeチャンネル情報と同じ扱い(週次のallと手動のallだけ、失敗しても既存の | 今の事実と合う |  |
| .github/workflows/regenerate-page.yml:114 | # 楽天の保存の規定(価格・在庫以外は3か月)は、この週次の取り直しで満たす。 | 今の事実と合う |  |
| .github/workflows/regenerate-saikyo.yml:3 | # 「最強戦」シートの更新(週1、月〜火が多い)を毎朝 saikyo/ に出す(#537 の案c)。 | 無関係 |  |
| .github/workflows/run-monthly.yml:11 | # Worker の起動の表(workers/scheduler/schedule.json)の1行で毎月1日 02:00 JST に起動する(2026-11-… | 今の事実と合う |  |
| .github/workflows/run-weekly.yml:1 | name: 週次の運行 | 今の事実と合う |  |
| .github/workflows/run-weekly.yml:4 | run-name: ${{ inputs.scheduled && '[scheduled] 週次の運行' \|\| '' }} | 動きの行run-name の式（morning_report が題で判定） |  |
| .github/workflows/run-weekly.yml:6 | # 週次の本を1本にまとめる(#564 の段④。run-daily.yml と同じ形)。 | 今の事実と合う |  |
| .github/workflows/run-weekly.yml:7 | # logs(cleanup-logs。毎日にすると #357 に同じ一覧の通知が毎日付くので週次に残す、CHAT-1102-WKP-09) | 今の事実と合う |  |
| .github/workflows/run-weekly.yml:9 | # ほかの週次の5本(check-meibo・sync-birthday-calendar・check-saikyo-unregistered・fetch-x-… | 経緯の記録 |  |
| .github/workflows/run-weekly.yml:12 | # Worker の起動の表(workers/scheduler/schedule.json)の1行で毎週月曜 03:00 JST に起動する。 | 今の事実と合う |  |
| .github/workflows/sync-yotei.yml:6 | # update-live-channel.yml の job yotei を切り出した(#564 の段③-3)。毎朝と保険の実行では update-live-… | 直す | 「毎朝と保険の実行では update-live-channel.yml が」→「毎朝は update-live-channel.yml が」 |
| .github/workflows/sync-yotei.yml:9 | # 呼ばれたときの github.event_name は呼ぶ側のもの(保険の実行では schedule)なので、予約の起動の判定は切り出す前と同じに働く。 | 直す | 「呼ぶ側のもの(保険の実行では schedule)なので」→「呼ぶ側のものなので」 |
| .github/workflows/update-live-channel.yml:8 | # (週1回は、見えなくなった動画に「取得不可」の行を、値が変わった動画に取り直した行も追記する)。scripts/fetch_live_channel_raw… | 今の事実と合う |  |
| .github/workflows/update-live-channel.yml:41 | description: '見えなくなった動画・値が変わった動画の確認(週1回の処理)も行う' | 今の事実と合う |  |
| .github/workflows/update-live-channel.yml:46 | description: '週1回の確認で、値が変わった動画が上限(200本)を超えても層1に追記する' | 今の事実と合う |  |
| CLAUDE.md:93 | - **着手前の確認**: 同じ Chat-Ref のコミット／`XXX` の重複（最初の指示）／0章ゲート（実装を含む指示、#265・#336）／指示文の i… | 無関係 |  |
| CLAUDE.md:138 | コミットの件名とログ）を確かめ、見つかったら作る前に止まって報告する**（0章ゲートは触るファイルの重なりを見るもので、目的の重なりは拾えない） | 無関係 |  |
| assets/live.js:3 | // - 画像の読み込み失敗時のフォールバック(data-fallback。video_wayhome.js と同じ) | 無関係 |  |
| docs/handover.md:12 | - **毎日・週次のフォールバック実行（GitHub の `schedule`）を外したので、2章の週次の再生成の記述を今の起動に合わせた** | 経緯の記録 |  |
| docs/handover.md:61 | **本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロ… | 無関係 |  |
| docs/handover.md:80 | 週次（#103）は毎週月曜03:00 JST の `run-weekly.yml` が `fetch-youtube.yml` の最後に`all`を呼ぶ（Git… | 今の事実と合う |  |
| docs/lighthouse-baseline.md:374 | フォールバック)をヒーローとして表に大きく配置した。**この計測のみ | 無関係 |  |
| docs/new-page-checklist.md:69 | - [ ] 準備の指示の着手時に、関係するページで `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_… | 直す | 「`check-meibo.yml` は週1回で、英字のタブ」→「`check-meibo.yml` は毎朝動くが、英字のタブ」 |
| docs/new-site-design.md:36 | スクリプト側の変更をpushする必要がある。定期実行（週次など）を | 経緯の記録 |  |
| docs/new-site-design.md:129 | - 本人が変更すれば変わる（だから週次の同期監視を作った） | 経緯の記録 |  |
| docs/new-site-design.md:789 | （上記の3段フォールバックは「URLをコピー」で使う） | 無関係 |  |
| docs/new-site-design.md:837 | - 共有ボタンの3段フォールバック方針（clipboard API → | 無関係 |  |
| docs/notes/ask-worker.md:4 | 実装は3つに分ける（grill Q13）: (1) データと検索〈済〉、(2) Worker と質問欄〈済、2026-10-11 から本番で動作〉、(3) 週次… | 経緯の記録 |  |
| docs/notes/ask-worker.md:20 | \| `workers/ask/src/weekly.mjs` \| 週次の cron（ブックへの書き出し・90日の削除、下の「週次の cron」） \| | 今の事実と合う |  |
| docs/notes/ask-worker.md:21 | \| `workers/ask/test/ask.test.mjs`・`weekly.test.mjs` \| 受付と週次のテスト。D1 は `test/fake-… | 今の事実と合う |  |
| docs/notes/ask-worker.md:57 | `generate_ask_data.py` が次を読んで1つにまとめる。週次の `regenerate.py all` で作り直す（push での作り直しは、… | 今の事実と合う |  |
| docs/notes/ask-worker.md:69 | `title/search.json` は `regenerate.py all` の中で `ask_data` より後に作られる（名前の順）。決勝の反映が1回… | 今の事実と合う |  |
| docs/notes/ask-worker.md:99 | ## 仮置き（実装2・週次のまとめを見て決め直す） | 今の事実と合う |  |
| docs/notes/ask-worker.md:160 | `ASK_SHEET_ID`（週次の書き出し先のブックの ID。空なら書き出しを飛ばす）。 | 今の事実と合う |  |
| docs/notes/ask-worker.md:212 | B1〜B4 の後、次の指示で本番の確かめ（`/api/ask/status` の応答、未公開の houou/ からの質問、費用の実測の始め）と実装3（週次の c… | 経緯の記録 |  |
| docs/notes/ask-worker.md:305 | ## 週次の cron（実装3、grill Q9） | 今の事実と合う |  |
| docs/notes/ask-worker.md:307 | `wrangler.jsonc` の `triggers.crons` に `0 18 * * 1`（UTC の日曜 18:00 = **月曜 3:00 JST… | 今の事実と合う |  |
| docs/notes/ask-worker.md:317 | 4. 鍵かブックの ID が無ければ、Workers Logs に「週次: 鍵(GOOGLE_SA_KEY)かブックの ID(ASK_SHEET_ID)が無いた… | 今の事実と合う |  |
| docs/notes/ask-worker.md:333 | - 最初の書き出しは 2026-10-19（月）9:00 JST の見込み。Workers Logs の `週次:` の行（`sheet` が `{append… | 経緯の記録 |  |
| docs/notes/ask-worker.md:334 | `sheet` が `null` なら、`週次: ブックへの書き出しに失敗: …`（Google の HTTP の状態）の行を見る | 今の事実と合う |  |
| docs/notes/ask-worker.md:358 | 5. 最初の書き出しは、`ASK_SHEET_ID` を入れた後の月曜（当時は 9:00 JST、2026-11-09 から 3:00 JST）。Workers… | 今の事実と合う |  |
| docs/notes/ask-worker.md:370 | ## 仮置き（実装2、本番の試しと週次のまとめを見て決め直す） | 今の事実と合う |  |
| docs/notes/birthday-calendar.md:112 | ## 毎日の同期（2026-09-20 に週1で有効にし、2026-11-03 から毎日） | 今の事実と合う |  |
| docs/notes/birthday-calendar.md:114 | 毎朝 04:00 JST に Worker から run-daily.yml の `birthday` として実行する（2026-11-03 から。2026-1… | 経緯の記録 |  |
| docs/notes/birthday-calendar.md:116 | `schedule` のときに `check-meibo.yml`（05:07）の10分後に置いたのは、名簿と「プロ」シートの不一致がその朝のうちに、題名で探し… | 経緯の記録 |  |
| docs/notes/books-calendar.md:7 | 「書籍」タブの発売日を「mj_書籍」Google カレンダーへ週1で同期する（凍結前の仕組み）。予定の説明欄から | 経緯の記録 |  |
| docs/notes/books-calendar.md:76 | ## 週1の同期 | 経緯の記録 |  |
| docs/notes/books-calendar.md:78 | - 毎週月曜 05:27 JST（日曜 20:27 UTC）。誕生日の同期（05:17）の後に置いている | 経緯の記録 |  |
| docs/notes/books-covers.md:9 | `https://github.com/retroeater/mj/blob/ea8b86a18fbe581b9000d7a6135a4717966d7b53/… | 今の事実と合う |  |
| docs/notes/books-covers.md:16 | - `regenerate-page.yml` の「楽天の書影を取得」ステップが**週次の `all` と手動の `all` のときだけ**実行する。 | 経緯の記録 |  |
| docs/notes/books-covers.md:19 | 週次の再生成が毎回コミット（＝本番反映）を起こすのを防ぐ（`data/youtube_channels.json` と同じ） | 経緯の記録 |  |
| docs/notes/books-covers.md:54 | 週次の再生成で画像URLを取り直すことで満たす | 経緯の記録 |  |
| docs/notes/books-freeze.md:36 | \| `sync-books-calendar.yml`（週次カレンダー同期） \| `gh workflow disable`（コードは変えていない）。2026-… | 経緯の記録 |  |
| docs/notes/books-freeze.md:43 | このままでは `regenerate-page.yml` の次回の `all`（次は生きていれば毎週月曜05:37 JST）と | 経緯の記録 |  |
| docs/notes/books-freeze.md:62 | - 主な作業ログ（`docs/logs/`、週次で消えるため詳細が要るときは早めに読む）: | 今の事実と合う |  |
| docs/notes/branch-operations.md:35 | ### 0章ゲート（指示文に実装が含まれるとき） | 無関係 |  |
| docs/notes/branch-operations.md:135 | `cleanup-logs.yml`が週次（月曜03:00 JST の run-weekly から）で片付ける。対象は`cloudflare`上の`docs/l… | 今の事実と合う |  |
| docs/notes/branch-operations.md:177 | （続き先が消える・完了になると、続き元が次の週次で自動で消えるため。上の条件4）。 | 今の事実と合う |  |
| docs/notes/branch-operations.md:181 | D から7日たった後の最初の週次（2026-10-19）と、その次の週次（2026-10-26）の2回で測る（#513）。見るのは、(1)新しい規則で書かれたロ… | 経緯の記録 |  |
| docs/notes/cloud-sessions.md:136 | - push したログは public の mj-logs に写る。Worker `mj-scheduler` が mj への push の直後（`pushed… | 今の事実と合う |  |
| docs/notes/cloudflare.md:577 | （ゲートを設けるかどうかは#170で検討中、保留） | 無関係 |  |
| docs/notes/cloudflare.md:806 | 止めたい場合はゲート（#170）が必要 | 無関係 |  |
| docs/notes/dojo-guest-calendar.md:42 | - 毎日 04:15 JST に Worker `mj-scheduler` が起動する（入力 `scheduled` を真にした手動実行の形。#504、`do… | 直す（04:15 は古い時刻。検索で 07:12 に当たった行） | 「毎日 04:15 JST に Worker が起動する（…）。2026-10-16 からは run-daily.yml の `dojo` から呼ばれる」→「毎日 04:00 JST に Worker が起動する run-daily.yml の `dojo` から呼ばれる（2026-10-16 から。それまでは Worker から 04:15 JST に直接）」 |
| docs/notes/github-actions.md:21 | \| `regenerate-page.yml` \| 生成スクリプト・対応する`.js`・`scripts/lib/**`の変更のpushと、手動実行・ほかのワー… | 今の事実と合う |  |
| docs/notes/github-actions.md:22 | \| `fetch-youtube.yml` \| 毎週月曜03:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-… | 今の事実と合う |  |
| docs/notes/github-actions.md:24 | \| `update-live-channel.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から… | 今の事実と合う |  |
| docs/notes/github-actions.md:26 | \| `check-image-links.yml` \| 毎朝 `run-daily.yml` から `update-sns-book` の後に（入力 `sche… | 今の事実と合う |  |
| docs/notes/github-actions.md:27 | \| `check-saikyo-unregistered.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-11… | 今の事実と合う |  |
| docs/notes/github-actions.md:30 | \| `cleanup-logs.yml` \| 毎週月曜03:00 JST に `run-weekly.yml` から（入力 `scheduled`、2026-1… | 今の事実と合う |  |
| docs/notes/github-actions.md:31 | \| `check-meibo.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-11-03 から。2026-10… | 今の事実と合う |  |
| docs/notes/github-actions.md:32 | \| `sync-birthday-calendar.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-11-03… | 今の事実と合う |  |
| docs/notes/github-actions.md:33 | \| `fetch-gsc.yml` \| 毎月1日02:00 JST に `run-monthly.yml` から（入力 `scheduled`、2026-11-… | 今の事実と合う |  |
| docs/notes/github-actions.md:34 | \| `fetch-redirect-counts.yml` \| 毎月1日02:00 JST に `run-monthly.yml` から（入力 `schedul… | 今の事実と合う |  |
| docs/notes/github-actions.md:35 | \| `convert-x-posts.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-11-03 から。202… | 今の事実と合う |  |
| docs/notes/github-actions.md:36 | \| `fetch-x-posts.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-11-03 から。2026-… | 今の事実と合う |  |
| docs/notes/github-actions.md:40 | \| `sync-dojo-calendar.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から。… | 今の事実と合う |  |
| docs/notes/github-actions.md:41 | \| `cleanup-branches.yml` \| 毎朝 `run-daily.yml` から（入力 `scheduled`、2026-10-16 から。それ… | 今の事実と合う |  |
| docs/notes/github-actions.md:45 | \| `run-weekly.yml` \| 毎週月曜03:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、202… | 今の事実と合う |  |
| docs/notes/github-actions.md:46 | \| `run-monthly.yml` \| 毎月1日02:00 JST に Worker `mj-scheduler` から（入力 `scheduled`、20… | 今の事実と合う |  |
| docs/notes/github-actions.md:49 | - mj-logs の `.github/workflows/sync-from-mj.yml`（#298）: Worker `mj-scheduler` から… | 今の事実と合う |  |
| docs/notes/github-actions.md:54 | GitHub Actions (`.github/workflows/regenerate-page.yml`) が、`scripts/generate_*.p… | 直す | 「`run-weekly.yml`（02:00 JST）」→「`run-weekly.yml`（03:00 JST）」 |
| docs/notes/github-actions.md:75 | - 呼ばれた本の実行は run-daily の実行の中のジョブ（`live / update` など）になり、各本のファイルには実行が付かない。各本の run-… | 今の事実と合う |  |
| docs/notes/github-actions.md:82 | **戻し方**（切り替えをやめて本ごとの起動に戻す）: 次の4つを戻して cloudflare に入れる（Workers Builds の `mj-schedu… | 今の事実と合う |  |
| docs/notes/github-actions.md:91 | 週次・月次の本を1本ずつにまとめたワークフロー（#564 の段④の前半、CHAT-1021-WKP-02）。run-daily と同じ形で、Worker の起動… | 今の事実と合う |  |
| docs/notes/github-actions.md:93 | - `run-weekly`: `logs`（cleanup-logs）の後に `youtube`。cleanup-logs は毎日にすると #357 に同じ一… | 今の事実と合う |  |
| docs/notes/github-actions.md:97 | - `run-weekly` の `youtube`（fetch-youtube → regenerate-page）: `logs` の後に `!cancel… | 今の事実と合う |  |
| docs/notes/github-actions.md:98 | - 週次の本の GitHub の `schedule`（保険）は 2026-11-01 に外した（CHAT-1101-WKP-05）。月次の2本（fetch-g… | 今の事実と合う |  |
| docs/notes/github-actions.md:102 | **戻し方**: `workers/scheduler/schedule.json` から2行を消し、`workers/scheduler/test/sched… | 今の事実と合う |  |
| docs/notes/github-actions.md:111 | 予約の起動の有効・無効は Worker の起動の表の `enabled` に任せ、`SCHEDULE_ENABLED` は使わない（部品に移した本から外す）。本… | 直す | 「本ごとの判定（保険のゲート・曜日・予約の起動で外す入力）」→「本ごとの判定（曜日・予約の起動で外す入力）」 |
| docs/notes/github-actions.md:191 | - `cleanup-logs.yml`（`scripts/cleanup_logs.py`、条件は `docs/notes/branch-operations… | 直す | 「週次の schedule は削除・通知する」→「予約の起動（run-weekly）は削除・通知する」 |
| docs/notes/github-actions.md:195 | - `sync-birthday-calendar.yml`（`scripts/sync_birthday_calendar.py`）: 予約の起動（run-d… | 今の事実と合う |  |
| docs/notes/github-actions.md:198 | schedule は28〜31日 21:00 UTC に起動し、JST で1日の回だけ本体が動く（毎月1日 06:00 JST。2026-09-21 に有効にし… | 今の事実と合う |  |
| docs/notes/glossary.md:17 | \| cron 実行 \| 予約実行 \| GitHub の `schedule`（cron）が本来の起動であるワークフロー（2026-11-01 に外すまでの週次の… | 今の事実と合う |  |
| docs/notes/glossary.md:18 | \| フォールバック実行 \| 保険の実行・保険の予約実行 \| Worker からの定期実行が無かった日に備えて、GitHub の `schedule`（cron）… | 直す | 定義の後に「（2026-11-01 にすべて外し、今は無い。月次の fetch-gsc・fetch-redirect-counts に残る保険の `schedule` は、成功済みでも動く cron 実行）」を足した |
| docs/notes/glossary.md:24 | - 「定期実行」は Worker からの起動に限る。GitHub の `schedule` で動く実行は、フォールバックなら「フォールバック実行」、本来の起動な… | 今の事実と合う |  |
| docs/notes/live-channel-write.md:105 | ワークフロー `.github/workflows/update-live-channel.yml` が毎日 04:00 JST に Worker `mj-sc… | 今の事実と合う |  |
| docs/notes/live-page-design.md:165 | - 「全動画」（現「連盟ch」）12,261行（2026-09-17 02:00 JST 取得）。2026-09-18 に LV-26 の TSV（14,058… | 経緯の記録 |  |
| docs/notes/live-page-design.md:183 | - 週1回（水曜）: 既知の全動画を取り直し（約280ユニット）、見えなくなった動画に、直前の行の写しに `"状態": "取得不可"` を足した行を追記する。 | 今の事実と合う |  |
| docs/notes/live-page-design.md:421 | - 属性の手動付与は週1程度（LV-01） | 無関係 |  |
| docs/notes/live-page-design.md:438 | - **毎日の流れ**（`.github/workflows/update-live-channel.yml`、毎日 04:00 JST に Worker から… | 今の事実と合う |  |
| docs/notes/live-page-design.md:461 | 変更があるときだけコミットする（2026-09-27 平野さんの決定、#438）。ほかに週次の `all` と手動実行（急ぐときの手順は `docs/notes… | 今の事実と合う |  |
| docs/notes/redirects.md:119 | **`fetch-redirect-counts.yml`**（毎月1日 06:30 JST、`fetch-gsc.yml` の後）が、`scripts/fet… | 直す | 「（毎月1日 06:30 JST、`fetch-gsc.yml` の後）」→「（毎月1日 02:00 JST に `run-monthly.yml` から、`fetch-gsc.yml` の後。保険として GitHub の schedule〈cron〉でも 06:30 JST に動く。02:00 の成功に関係なく動く）」 |
| docs/notes/redirects.md:124 | - **期間:** Adaptive のデータセットは「どのプランでも少なくとも31日」保持する（公式「GraphQL Analytics API — Limi… | 直す | 「1日 06:30 JST の実行で」→「1日 02:00 JST の実行で」 |
| docs/notes/saikyo-page-design.md:19 | シートの更新（週1、月〜火が多い）は、毎朝 `regenerate-saikyo.yml` が saikyo_pages だけを再生成して出す（#537）。 | 無関係 |  |
| docs/notes/saikyo-page-design.md:21 | 差分が無ければコミットしない。ほかに週次の `all` と手動実行（`regenerate-page.yml`）でも再生成される。 | 今の事実と合う |  |
| docs/notes/saikyo-page-design.md:426 | - **CSS のフォールバック（JS が実測を入れるまでの値）は幅ごとに持つ**（#381、CHAT-0921-SG-10）: | 無関係 |  |
| docs/notes/saikyo-page-design.md:431 | 436〜459px ではフォールバック（115px）が実際（61px）より大きく、JS の適用時に本文が上がる。境目を測り直すなら実機のフォントで | 無関係 |  |
| docs/notes/saikyo-page-design.md:432 | それまではフォールバックが1行分しか無く、JS の適用時に本文が 54px 下がって **CLS 0.055** になっていた | 無関係 |  |
| docs/notes/saikyo-page-design.md:678 | ゼロになればコメントしてクローズする（CHAT-0916-SK-44、#139）。毎週月曜の予約実行（`schedule`）は `jpml_pros.html`… | 経緯の記録 |  |
| docs/notes/saikyo-page-design.md:731 | 登録漏れに気づけるよう、同じ読み方で引けない人を毎日確かめ（2026-11-02 までは週1）、常設issueに書く。 | 今の事実と合う |  |
| docs/notes/saikyo-page-design.md:758 | 毎朝 04:00 JST に Worker からの run-daily.yml（2026-11-03 から。2026-10-26〜11-02 は毎週月曜 02:… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:33 | 3. **朝の確かめ（#504 の「層1」）**: 06:00 JST の回（時刻どおりの回だけ）に、当日の有効な行のうち予定が 06:00 より前のものごとに… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:35 | `head_branch` では絞らないので、作業ブランチで `scheduled` を付けた試験の成功も当日の success に数える。**直さない（202… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:36 | **06:15・06:30 の回で書き直す（2026-10-14 から）**: 06:00 の回が抜けた日のため、`GET /issues/506/commen… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:55 | ## 保険の予約実行のゲート（update-live-channel） | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:57 | **2026-11-01 に外した（#564 の段④、CHAT-1101-WKP-05）。** update-live-channel の保険の予約実行（`sc… | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:58 | 毎日・週次の本の保険の `schedule` も同時に外したので、Worker からの起動が抜けた日は、埋め合わせ（上の「動き」の 1.「起動」）と朝の確かめ（… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:72 | 今の表は3行: `run-daily.yml` 毎日 04:00（毎朝の運行。2026-10-16 から。update-live-channel・update-… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:101 | - 朝の確かめ（06:00、抜けたら 06:15・06:30）: `{"event":"morning_check","date":"2026-10-06","… | 今の事実と合う |  |
| docs/notes/scheduler-worker.md:149 | - Worker の実行と保険の予約実行が重なったときに、concurrency（組 `update-live-channel`）で保険の実行が待たされる動き（… | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:157 | - 06:00 の朝の確かめの回は動いている: 10/7 06:00:08 JST に「朝の確かめ: 2026-10-07 予定 1・success 1・それ以… | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:159 | - 10/8 は毎分の起動（cron `* * * * *`、#298）になってから最初の朝。sync-dojo-calendar の run #33 が 04… | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:161 | - 10/9 は update-live-channel が Worker から起動した最初の朝（段階2の後の回）。作られた時刻（API）は update-li… | 経緯の記録 |  |
| docs/notes/scheduler-worker.md:162 | - 同じ日の保険の予約実行: update-live-channel の run #79（schedule）が 10:28:00 に作られ（予定 06:43 か… | 経緯の記録 |  |
| docs/notes/site-findings.md:90 | /apple-touch-icon.png をフォールバックで取得する**（jpml_pros.html | 無関係 |  |
| docs/notes/sitemap-lastmod.md:12 | \| 再生成（push 検知・週次 all・手動実行） \| `.github/workflows/regenerate-page.yml` \| | 今の事実と合う |  |
| docs/notes/sitemap-lastmod.md:35 | - `table.js` のようにどのページにも一致しない変更と HTML を同時に push すると、どちらも lastmod を直さない。週次の all 実… | 今の事実と合う |  |
| docs/notes/sns-book.md:21 | - **毎朝の検知（`check-image-links.yml`。`run-daily.yml` が `update-sns-book` の後に呼ぶ）は3つの… | 今の事実と合う |  |
| docs/notes/static-generation.md:77 | - `saikyo_mens`: フォールバックを`abs.twimg.com`の既定アイコンから | 無関係 |  |
| docs/notes/static-generation.md:300 | 取得は週次`all`と`target_page`空/`all`の手動実行時のみ。失敗しても既存JSONでアイコンは維持され、ジョブだけ失敗扱いになる | 今の事実と合う |  |
| docs/notes/static-generation.md:378 | - 週次の all（`fetch-youtube.yml` が月曜に呼ぶ）で全部作り直されるので、急ぐときだけ `regenerate-page.yml` を対… | 今の事実と合う |  |
| docs/notes/static-generation.md:409 | - 生成後の絞り込み・並び替え・ページ送りはページ側の軽量JSに委譲する。`jpml_pros.js`は絞り込みと並び替え（ページ送りなし・全行表示）専用。型A… | 無関係 |  |
| docs/notes/static-generation.md:456 | - `check_image_links.py` — `jpml_pros.html` 内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎朝 `r… | 今の事実と合う |  |
| docs/notes/static-generation.md:459 | - `cleanup_logs.py` — `docs/logs/`の作業ログのうち、7日を過ぎて片付けてよいもの（完了で3項目「なし」・取り下げ・続き先が完了… | 今の事実と合う |  |
| docs/notes/static-generation.md:471 | - `build_wayhome_ogp.py` — 「帰り道」一覧ページのOGP画像`img/ogp/wayhome/index-<最新話の公開日 YYYYM… | 今の事実と合う |  |
| docs/notes/static-generation.md:472 | - `check_wayhome.py` — 「帰り道」の取り込みの知らせ（新しい回・YouTube の情報が無い回・見られなくなった回・決勝動画IDが空の回）… | 今の事実と合う |  |
| docs/notes/video-exam.md:37 | - 新しい回の検知は作らない（タブに行を足せば週次の再生成で反映。Mつくと同じ） | 今の事実と合う |  |
| docs/notes/video-mtsuku.md:12 | - **YouTube の情報は使わない**（2026-10-15 平野さんの決定。鍵も週次の取得も要らない）: 公開日は「公開日」列（並びは日付順。公開日の … | 今の事実と合う |  |
| docs/notes/video-mtsuku.md:13 | 再生時間は「再生時間」列（「m:ss」の文字か時刻の値。**空・「-」の回、1分未満・1時間以上〈読み違いとみなす〉の回があれば止める**。週次の all では… | 今の事実と合う |  |
| docs/notes/video-mtsuku.md:38 | - 「決勝戦を見る」・新しい回の検知と常設 issue・一覧の OGP 合成画像は無い。新しい回はシートに1行足せば、次の再生成（週次の all）で載る | 今の事実と合う |  |
| docs/notes/video-wayhome.md:9 | **シート（動画のタブをまとめたブック〈`lib/video_book.py` の `SPREADSHEET_ID`〉の「帰り道」タブ、#578）に1行貼るだけ… | 今の事実と合う |  |
| docs/notes/video-wayhome.md:12 | 1. 新しい回が公開されると、週次の再生成が常設 issue「帰り道の新しい回」に知らせる（/live の層1 `data/live_channel_raw.j… | 今の事実と合う |  |
| docs/notes/video-wayhome.md:18 | 3. 次の週次の `fetch-youtube.yml`（毎週月曜 03:00 JST の run-weekly から。急ぐときは `fetch-youtube… | 今の事実と合う |  |
| docs/notes/video-wayhome.md:21 | 週次の再生成が知らせること（前回の知らせと変わったときだけ。決定は `docs/decisions/wayhome.md`）: | 今の事実と合う |  |
| docs/notes/video-wayhome.md:51 | - 紹介文の列やタイトル列を直したら、`python3 scripts/regenerate.py video_wayhome wayhome_episodes… | 今の事実と合う |  |
| docs/notes/video-wayhome.md:206 | `fetch_youtube_meta.py`を先に実行すること（週次の自動取得は#194） | 今の事実と合う |  |
| docs/notes/video-wayhome.md:263 | 存在すれば1280×720、なければ`hqdefault.jpg`(480×360)にフォールバックする** | 無関係 |  |
| docs/notes/video-wayhome.md:315 | - **`table.js`を読まないページでは`data-fallback`の画像フォールバック | 無関係 |  |
| docs/notes/video-wayhome.md:317 | ハンドラで全画像のフォールバックをまとめて処理しているが、この | 無関係 |  |
| docs/notes/video-wayhome.md:321 | （画像フォールバック・`?name=`初期値・`--navbar-height`実測）を | 無関係 |  |
| docs/notes/video-wayhome.md:324 | フォールバックが効くことを実地で確認した） | 無関係 |  |
| docs/notes/video-wayhome.md:408 | フォールバック）。 | 無関係 |  |
| docs/notes/yotei-sheet.md:161 | \| `verify`・`allow_many_changes` \| update \| 週1回の確認を行う・その上限を外す \| 水曜だけ確認・上限あり \| | 今の事実と合う |  |
| docs/review-followup-instructions.md:75 | - handover は「saikyo_mens の移行で abs.twimg.com への依存は解消済み」としているが、それはフォールバックの話。スプレッドシ… | 無関係 |  |
| docs/review-followup-instructions.md:76 | - 対応案: (a) スプレッドシート側で該当セルを空にする（生成時にフォールバックへ落ちる）、(b) 生成スクリプト側で `abs.twimg.com/sti… | 無関係 |  |
| docs/review-followup-instructions.md:78 | - **この issue はコード変更せず起票のみ。** handover の該当箇所（「4. 押さえておくべき方針 → 外部ドメインへの依存を増やさない」の … | 無関係 |  |
| docs/review-followup-instructions.md:185 | - 「移行済みは14ページになった（起票時は5ページ）。14ページ分のデータが手動実行に依存しており、運用リスクとして最大。#7 の完了を待たず、週次 cron… | 経緯の記録 |  |
| scripts/actions_dashboard.py:461 | body += ['<h2>cron 実行（毎日・週次・月次）</h2>', | 生成物に出るmj-logs の actions/index.html に出る |  |
| scripts/actions_dashboard.py:462 | '<p class="lead">GitHub の schedule。毎朝の運行からも呼ばれるものは、その日の定期実行が成功済みなら何もしないフォールバック実行… | 生成物に出るmj-logs の運行表に出る。毎朝の運行から呼ばれる本の schedule は今は無い |  |
| scripts/build_ogp_image.py:10 | - SVGを経由せず Pillow で直接描画する。ImageMagickのsvgデリゲートは | 無関係 |  |
| scripts/build_ogp_image.py:12 | フォールバックしフォントの当たり方が不安定になるため | 無関係 |  |
| scripts/build_ogp_image.py:59 | # Pillow内蔵のビットマップフォントへ黙ってフォールバックさせない | 無関係 |  |
| scripts/build_wayhome_ogp.py:16 | (止めない。週次の再生成で動かすため、#340) | 今の事実と合う |  |
| scripts/check_wayhome.py:2 | """「帰り道」の取り込みの知らせを組み立てる(#194)。週次の再生成(regenerate-page.yml)が呼ぶ。 | 今の事実と合う |  |
| scripts/check_wayhome.py:36 | `.github/workflows/fetch-youtube.yml` の実行（`run-weekly.yml` から毎週月曜 03:00 JST。fetc… | 今の事実と合う |  |
| scripts/check_wayhome.py:42 | \| 新しい回（/live の層1の題名に「帰り道」か「ついていって」があり、帰り道シートに無い） \| 知らせの「貼る1行」を帰り道シートの末尾に貼る（シートの今… | 今の事実と合う |  |
| scripts/check_wayhome.py:45 | \| 再生成の失敗 \| 実行ログを見る。次の週次か手動実行で追いつく \| | 今の事実と合う |  |
| scripts/fetch_live_channel_raw.py:19 | - `--mode verify`(週1回): 既知の全動画を取り直し、見えなくなった動画に「状態: 取得不可」の行を追記する。 | 今の事実と合う |  |
| scripts/fetch_live_channel_raw.py:327 | help="new: 新着と配信予定・配信中の取り直し(毎日) / verify: 見えなくなった動画・値が変わった動画の確認(週1回)" | 今の事実と合う |  |
| scripts/fetch_rakuten_books.py:117 | # 週次の再生成が毎回コミット(=本番反映)を起こすのを防ぐ(youtube_channels.json と同じ) | 今の事実と合う |  |
| scripts/fetch_x_posts.py:341 | since_latest: 期間の初めを【1】のアカウントごとの最も新しい投稿日にする(毎日の取り込み。2026-10-28 に週次で入れ、2026-11 に毎… | 経緯の記録 |  |
| scripts/generate_jpml_pros.py:219 | JSONが無くても生成は止めない(全員フォールバック画像になる)。""" | 無関係 |  |
| scripts/generate_saikyo_mens.py:66 | 分岐を維持)。フォールバックは旧来の abs.twimg.com の既定アイコンから | 無関係 |  |
| scripts/generate_video_wayhome.py:9 | 何もしないため読み込まず、フィルター・画像フォールバック・検索欄の | 無関係 |  |
| scripts/lib/net_retry.py:3 | 予約実行が一時的なタイムアウトで止まり、週1の予約が1週間遅れていた(2026-09-28 の誕生日カレンダー・道場部)。 | 経緯の記録 |  |
| scripts/lib/page.py:126 | srcを空文字にせずフォールバック画像を直接指定する。src="" は | 無関係 |  |
| scripts/lib/wayhome_intake.py:3 | 週次の再生成(regenerate-page.yml)で scripts/check_wayhome.py が使う。入出力を持たない関数だけを置く。 | 今の事実と合う |  |
| scripts/morning_report.py:147 | """その日の定期実行で、file を呼ぶジョブが success か（フォールバック実行はゲートで止まるだけなので待たない）""" | 直す | 「（フォールバック実行はゲートで止まるだけなので待たない）」→「（success なら、その本自身の cron の予定を待たない）」 |
| scripts/tests/test_actions_dashboard.py:142 | self.assertEqual(d.cron_label('37 20 * * 0'), 'cron 毎週月曜 05:37') | 無関係 |  |
| scripts/tests/test_actions_dashboard.py:143 | self.assertEqual(d.cron_label('43 21 * * *'), 'cron 毎日 06:43') | 無関係 |  |
| scripts/tests/test_actions_status.py:36 | self.assertEqual(a.cron_jst('7 20 * * 0'), 'JST 05:07（月曜）') | 無関係 |  |
| scripts/tests/test_actions_status.py:42 | self.assertEqual(a.cron_jst('0 21 28-31 * *'), 'JST 06:00（日付・曜日は UTC の翌日）') | 無関係 |  |
| scripts/tests/test_actions_status.py:58 | self.assertIn('- schedule: `7 20 * * 0`（UTC） → JST 05:07（月曜）', md) | 無関係 |  |
| scripts/tests/test_actions_status.py:59 | self.assertIn('\| 2026-10-05 05:07 \| schedule \| cloudflare \| success \| #7（100） \| … | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:134 | "actualStartTime": "2026-10-01T07:02:00Z", "actualEndTime": "2026-10-01T13:10:00… | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:152 | self.assertEqual(r["配信開始日時"], "2026-10-01T07:02:00Z") | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:159 | old = self.old_record("a", 種別="ライブ", 配信開始日時="2020-01-01T03:00:00Z") | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:168 | old = self.old_record("a", 種別="ライブ", 配信開始日時="2026-10-01T07:02:00Z", 長さ="PT6H8M") | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:187 | latest = {"a": self.old_record("a", 種別="ライブ", 配信開始日時="2026-10-01T07:02:00Z", 長さ=… | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:194 | latest = {"a": self.old_record("a", 種別="ライブ", 配信開始日時="2026-10-01T07:02:00Z", 長さ=… | 無関係 |  |
| scripts/tests/test_fetch_live_channel_raw.py:226 | f.append_records(path, [self.old_record(v, 種別="ライブ", 配信開始日時="2026-10-01T07:02:00… | 無関係 |  |
| scripts/tests/test_live_calendar.py:174 | latest = {"v": video("v", "t", start="2026-09-27T03:00:00Z", end="2026-09-27T05:… | 無関係 |  |
| scripts/tests/test_live_calendar.py:183 | latest = {"v": video("v", "t", start="2026-09-27T03:00:00Z", end="2026-09-27T05:… | 無関係 |  |
| scripts/tests/test_live_calendar.py:267 | latest = {"f": video("f", "Focus M season13", scheduled="2026-10-06T03:00:00Z", … | 無関係 |  |
| scripts/tests/test_live_calendar.py:275 | latest = {"y": video("y", "Focus M season13", start="2026-09-27T03:00:00Z", end=… | 無関係 |  |
| scripts/tests/test_live_calendar.py:276 | "o": video("o", "第32期鳳凰戦 A1リーグ第5節", start="2015-08-03T03:00:00Z", end="2015-08-0… | 無関係 |  |
| scripts/tests/test_live_calendar.py:282 | latest = {"x": video("x", "【テスト放送】第32期鳳凰戦", start="2015-08-03T03:00:00Z", end="2… | 無関係 |  |
| scripts/tests/test_live_calendar.py:283 | "y": video("y", "Focus M season13", start="2026-09-27T03:00:00Z", end="2026-09-2… | 無関係 |  |
| scripts/tests/test_live_calendar.py:290 | "short": dict(video("short", "【メンバー限定】World Riichi", start="2022-08-13T03:00:00Z… | 無関係 |  |
| scripts/tests/test_live_calendar.py:302 | latest = {"m": dict(video("m", "第32期鳳凰戦", start="2016-01-01T03:00:00Z"), 長さ="PT6… | 無関係 |  |
| scripts/tests/test_live_calendar.py:307 | latest = {"g": dict(video("g", "第32期鳳凰戦", start="2016-01-01T03:00:00Z"), 状態="取得不… | 無関係 |  |
| scripts/tests/test_live_candidate.py:37 | "動画ID": "-G4RS4dl4XI", "公開日時": "2020-07-08T03:00:00Z", "種別": "動画", "限定": "N", | 無関係 |  |
| scripts/tests/test_live_candidate.py:64 | ("2026-04-19T03:00:00Z", "2026-04-19")): # 日本時間 12:00 | 無関係 |  |
| scripts/tests/test_live_candidate.py:102 | rows = lc.build_rows([CONFIDENT_CANDIDATE, self.live("L1", "2018-05-01T03:00:00Z… | 無関係 |  |
| scripts/tests/test_live_candidate.py:106 | rows = lc.build_rows([CONFIDENT_CANDIDATE, self.live("L1", "2018-05-01T03:00:00Z… | 無関係 |  |
| scripts/tests/test_live_candidate.py:107 | self.live("L2", "2018-05-08T03:00:00Z")]) | 無関係 |  |
| scripts/tests/test_live_candidate.py:111 | other = dict(self.live("L1", "2018-05-01T03:00:00Z"), タイトル="【麻雀】第３期JPML WRCリーグ~ベ… | 無関係 |  |
| scripts/tests/test_live_candidate.py:118 | dict(lc.build_row(self.live("L1", "2018-05-01T03:00:00Z")), ステージ="")] | 無関係 |  |
| scripts/tests/test_live_layer3.py:176 | scheduled = {"pub": "2026-10-01T02:00:00Z", "mem": "2026-10-01T02:00:00Z"} | 無関係 |  |
| scripts/tests/test_morning_report.py:60 | self.assertEqual(due, datetime.datetime(2026, 10, 13, 22, 12, tzinfo=UTC)) # JST… | 無関係 |  |
| scripts/tests/test_morning_report.py:102 | self.assertTrue(lines[0].startswith('- 遅れの可能性: sync-dojo-calendar.yml 予定 2026-10… | 無関係 |  |
| scripts/tests/test_morning_report.py:136 | """run-daily の定期実行で済んだワークフローのフォールバック実行を待たない（CHAT-1017-WFL-27）""" | 今の事実と合うcovered_by_daily の挙動のテスト |  |
| scripts/tests/test_morning_report.py:139 | NOW = datetime.datetime(2026, 10, 16, 21, 45, tzinfo=UTC) # JST 10/17 06:45、フォール… | 無関係 |  |
| scripts/tests/test_morning_report.py:243 | sched = run(id=5, secs=741, display_title='[scheduled] 週次の運行') | 今の事実と合う |  |
| scripts/tests/test_morning_report.py:265 | self.assertTrue(m.is_scheduled(run(display_title='[scheduled] 週次の運行'))) | 今の事実と合う |  |
| scripts/tests/test_morning_report.py:266 | self.assertFalse(m.is_scheduled(run(display_title='週次の運行'))) | 今の事実と合う |  |
| scripts/tests/test_title_broadcasts.py:124 | ("f1", "", "日", False, "2026-09-19T03:00:00Z", "決勝"), | 無関係 |  |
| scripts/tests/test_title_broadcasts.py:125 | ("g1", "", "回戦", True, "2026-09-19T03:00:00Z", "決勝"), | 無関係 |  |
| scripts/tests/test_title_broadcasts.py:133 | ("d1", "初日", "日", True, "2026-09-12T03:00:00Z", "十段位決定戦"), | 無関係 |  |
| scripts/tests/test_title_broadcasts.py:134 | ("d2", "二日目", "日", True, "2026-09-19T03:00:00Z", "決定戦"), | 無関係 |  |
| scripts/tests/test_title_broadcasts.py:135 | ("d3", "最終日", "日", True, "2026-09-26T03:00:00Z", "十段位決定戦"), | 無関係 |  |
| scripts/tests/test_wayhome_rows.py:53 | video = {"publishedAt": "2026-09-21T13:05:37Z", "thumbnails": {}, "duration": "P… | 無関係 |  |
| scripts/tests/test_yotei.py:126 | short = dict(video("s", "【メンバー限定】World Riichi", start="2022-08-13T03:00:00Z", me… | 無関係 |  |
| scripts/tests/test_yotei.py:129 | pub1, pub2 = video("a", "Focus M", start="2024-06-03T03:00:00Z"), video("b", "Fo… | 無関係 |  |
| scripts/tests/test_yotei.py:149 | "時間が重ならない": aired("p", "第一期鳳匠戦ベスト16C", "05:00", "06:00"), | 無関係 |  |
| scripts/tests/test_yotei.py:150 | "大会が違う": aired("p", "第24期プロクイーン~ベスト８Ａ卓~", "02:00", "03:00"), | 無関係 |  |
| scripts/tests/test_yotei.py:151 | "大会が判定できない": aired("p", "女流勉強会 part73【無料放送】", "02:00", "03:00"), | 無関係 |  |
| scripts/tests/test_yotei.py:152 | "放送前": video("p", "第一期鳳匠戦ベスト16C", scheduled="2026-10-01T02:00:00Z"), | 無関係 |  |
| scripts/tests/test_yotei.py:162 | aired("m1", "【メンバー限定】Focus M season8 第1部", "03:00", "04:00", members="Y"), | 無関係 |  |
| scripts/tests/test_yotei.py:163 | aired("m2", "【メンバー限定】FocusM season8", "03:30", "06:00", members="Y"), | 無関係 |  |
| scripts/tests/test_yotei.py:164 | aired("p", "Focus M season８", "03:00", "05:00"), | 無関係 |  |
| scripts/tests/test_yotei.py:207 | latest = history("第43期十段戦~ベスト16Ｃ卓~", 3, start_utc="03:00") | 無関係 |  |
| scripts/tests/test_yotei.py:278 | latest = history("【メンバー限定】第12期若獅子戦~ベスト８Ａ卓~", 3, start_utc="03:00") | 無関係 |  |
| style.css:401 | /* 固定したメニューの分だけ本文を押し下げる。フォールバックはJSの適用前・無効時の値で、 | 無関係 |  |
| style.css:946 | /* hqdefault(4:3)にフォールバックした回が最新話でも、object-fit: coverで | 無関係 |  |
| style.css:1333 | フォールバック値はJS実行前・失敗時の妥当な概算。 | 無関係 |  |
| style.css:1335 | バー幅ぶんの横溢れが出ないようにするための保険(下記参照)。 */ | 無関係 |  |
| style.css:1446 | 横溢れの保険としてoverflow-x: hiddenを祖先に置くのは避ける | 無関係 |  |
| style.css:2674 | title.jsが設定)に合わせる。フォールバックはJS無効時の概算(navbar lg以上80px・未満56px、バー61px) */ | 無関係 |  |
| video_wayhome.js:6 | // - 画像の読み込み失敗時のフォールバック(data-fallback) | 無関係 |  |
| video_wayhome.js:8 | // 移植を忘れると、このページだけ画像フォールバックが効かなくなる | 無関係 |  |
| video_wayhome.js:23 | // ---- 画像フォールバック(table.jsから移植。忘れるとこのページだけ壊れる) ---- | 無関係 |  |
| wayhome_episodes.js:7 | // - 画像の読み込み失敗時のフォールバック(data-fallback。table.js/ | 無関係 |  |
| wayhome_episodes.js:13 | // ---- 画像フォールバック ---- | 無関係 |  |
| workers/ask/src/index.mjs:3 | // 週次の cron(wrangler.jsonc の triggers)はブックへの書き出しと90日の削除(weekly.mjs) | 今の事実と合う |  |
| workers/ask/src/weekly.mjs:1 | // データ・コンシェルジュ(#542)の週次の cron(実装3、grill Q9)。 | 今の事実と合う |  |
| workers/ask/src/weekly.mjs:141 | console.log('週次: 鍵(GOOGLE_SA_KEY)かブックの ID(ASK_SHEET_ID)が無いため、ブックへの書き出しを飛ばす'); | 今の事実と合う |  |
| workers/ask/src/weekly.mjs:146 | console.error(`週次: ブックへの書き出しに失敗: ${e.message}`); | 今の事実と合う |  |
| workers/ask/src/weekly.mjs:152 | console.log(`週次: ${JSON.stringify(summary)}`); | 今の事実と合う |  |
| workers/ask/test/week3.test.mjs:150 | // (4) 週次の書き出しを月曜 3:00 JST(日曜 18:00 UTC)に | 今の事実と合う |  |
| workers/ask/test/week3.test.mjs:151 | test('wrangler.jsonc の週次の cron は日曜 18:00 UTC(月曜 3:00 JST、Cloudflare の曜日は 1 = 日曜)… | 今の事実と合う |  |
| workers/ask/wrangler.jsonc:13 | // 週次の cron(UTC)。日曜 18:00 UTC = 月曜 3:00 JST にブックへの書き出しと90日の削除(src/weekly.mjs)。 | 今の事実と合う |  |
| workers/ask/wrangler.jsonc:14 | // ほかの定期実行(月次 2:00・週次 3:00・日次 4:00 JST)にそろえた(2026-11-02。前は月曜 0:00 UTC = 9:00 JST… | 今の事実と合う |  |
| workers/ask/wrangler.jsonc:28 | // ASK_SHEET_ID は週次の書き出し先のブック「ryoei.pro_AIボット」の ID(秘密の値ではない。空なら書き出しを飛ばす)。 | 今の事実と合う |  |
| workers/scheduler/src/index.mjs:2 | // workflow_dispatch(入力 scheduled を真)で起動し、06:00 JST の回に朝の実行を確かめる(06:15・06:30 は書き… | 今の事実と合う |  |
| workers/scheduler/src/scheduler.mjs:20 | // 06:00 の回が抜けたときに備えて朝の確かめを書き直す回(JST の 0 時からの分) | 今の事実と合う |  |
| workers/scheduler/src/scheduler.mjs:161 | // この回が朝の確かめを書き直す回か(06:15・06:30。now は実際の時刻) | 今の事実と合う |  |
| workers/scheduler/src/scheduler.mjs:408 | // 06:15・06:30 の回の朝の確かめ。#506 に当日の確かめ(「<日付> の朝の予約実行」、TRUSTED_COMMENTERS が書いたもの)が既… | 今の事実と合う |  |
| workers/scheduler/src/scheduler.mjs:409 | // 06:00 の回が抜けた日に書くため。すべて success の日は確かめ直すだけで書かない | 今の事実と合う |  |
| workers/scheduler/test/catchup.test.mjs:159 | test('朝の確かめの書き直しは 06:15・06:30 の回', () => { | 今の事実と合う |  |
| workers/scheduler/test/catchup.test.mjs:161 | assert.equal(isCheckRetryTick(jst('2026-10-14T06:30:10')), true); | 動きの行 |  |
| workers/scheduler/test/catchup.test.mjs:162 | assert.equal(isCheckRetryTick(jst('2026-10-14T06:00:40')), false); | 動きの行 |  |
| workers/scheduler/test/catchup.test.mjs:193 | const report = await checkMorningRetry(fetchFn, CONFIG, ROWS, jst('2026-10-14T06… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:104 | test('朝の確かめは 06:00 の回だけ', () => { | 今の事実と合う |  |
| workers/scheduler/test/scheduler.test.mjs:105 | assert.equal(isCheckTick(jst('2026-10-05T06:00:00')), true); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:110 | test('朝の確かめの対象は 06:00 より前の有効な行', () => { | 今の事実と合う |  |
| workers/scheduler/test/scheduler.test.mjs:113 | { workflow: 'b.yml', time: '06:30', enabled: true }, | 無関係 |  |
| workers/scheduler/test/scheduler.test.mjs:116 | assert.deepEqual(morningRows(rows, jstParts(jst('2026-10-05T06:00:00')).day).map… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:121 | const day = jstParts(jst('2026-10-05T06:00:00')).day; | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:122 | const now = jst('2026-10-05T06:00:00'); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:139 | const day = jstParts(jst('2026-10-05T06:00:00')).day; | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:181 | const report = await checkMorning(fetchFn, CONFIG, rows, jst('2026-10-05T06:00:0… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:197 | const report = await checkMorning(fetchFn, CONFIG, rows, jst('2026-10-05T06:00:0… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:206 | const report = await checkMorning(fetchFn, CONFIG, rows, jst('2026-10-05T06:00:0… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:235 | await checkMorning(fakeFetch({ '/runs?': [200, { workflow_runs: [run()] }] }), C… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:241 | await checkMorning(fetchNg, CONFIG, rows, jst('2026-10-05T06:00:00')); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:263 | const report = await checkMorning(fetchFn, CONFIG, rows, jst('2026-10-05T06:00:0… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:318 | test('起動の表: 04:00 の回に run-daily を1本起動し、ほかの回では起動せず、月曜・1日でない日の 06:00 の確かめの対象は1行', … | 今の事実と合う |  |
| workers/scheduler/test/scheduler.test.mjs:323 | assert.equal(morningRows(schedule.rows, jstParts(jst('2026-10-09T06:00:00')).day… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:324 | assert.deepEqual(dueRows(schedule.rows, jst('2026-10-09T02:00:00')), []); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:325 | assert.deepEqual(dueRows(schedule.rows, jst('2026-10-09T03:00:00')), []); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:328 | test('起動の表: 1日 02:00 に run-monthly、月曜 03:00 に run-weekly を起動し、その日だけ朝の確かめの対象に入る',… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:332 | assert.deepEqual(due('2026-10-26T02:00:00'), []); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:333 | assert.deepEqual(due('2026-10-26T03:00:00'), ['run-weekly.yml']); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:334 | assert.deepEqual(checked('2026-10-26T06:00:00'), ['run-daily.yml', 'run-weekly.y… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:335 | assert.deepEqual(due('2026-11-01T02:00:00'), ['run-monthly.yml']); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:336 | assert.deepEqual(due('2026-11-01T03:00:00'), []); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:337 | assert.deepEqual(due('2027-02-01T02:00:00'), ['run-monthly.yml']); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:338 | assert.deepEqual(due('2027-02-01T03:00:00'), ['run-weekly.yml']); | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:339 | assert.deepEqual(checked('2026-11-01T06:00:00'), ['run-daily.yml', 'run-monthly.… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:340 | assert.deepEqual(checked('2027-02-01T06:00:00'), ['run-daily.yml', 'run-weekly.y… | 動きの行 |  |
| workers/scheduler/test/scheduler.test.mjs:346 | { workflow: 'fetch-gsc.yaml', time: '06:00', enabled: true }, | 無関係 |  |

### 直したこと

- 26e984f36: 直す 12 件（上の表の「直す」）。コメント・docs・docstring だけ。.github の2本はコメントの行だけで、`yaml.safe_load` の結果は変更の前後で同じ
- ca9e1c64a: 自己レビューの指摘で、月次の2本に残る schedule の呼び方を glossary・redirects.md・morning_report.py の docstring でそろえた（「今は無い」フォールバック実行と食い違わないよう、成功済みでも動く cron 実行と書いた）
- `python3 -m unittest discover -s scripts/tests` 成功（1,747 件）、`node --test 'workers/scheduler/test/*.test.mjs'` 47 件成功
- CLAUDE.md 23,981 バイト・docs/handover.md 23,633 バイト（どちらも変えていない。警告域 26,624 未満）
- 決定を docs/decisions/automation.md に足し、WKP-14 のログの状態に ` / 続き: CHAT-1102-WKP-15` を足した

### 自己レビュー（docs/notes/self-review.md、1回）

| 指摘 | 扱い |
|---|---|
| 変わった行は文言だけ。YAML の読み込み結果は前後で同じ、morning_report.py は docstring 1行。新しい文言は schedule.json・各ワークフローと合う | 意図どおり |
| redirects.md 119 の「保険の予約実行は 06:30 JST」が、glossary に足した「フォールバック実行（保険の予約実行）は今は無い」と食い違う。06:30 の cron は 02:00 の成功に関係なく動く | 直した（ca9e1c64a） |
| glossary 17・18 行の「保険」の指すものが割れる | 直した（ca9e1c64a。18行に、月次の2本は成功済みでも動く cron 実行だと足した。17行は「今は月次の…保険だけ」のままで、18行の補足と合う） |
| morning_report.py の docstring が「今は無い」とした語で呼んでいる | 直した（ca9e1c64a） |
| fetch-redirect-counts の 06:30 の cron は 02:00 の run-monthly が成功しても毎月もう一度動き、CSV のコミットと #568 へのコメントが二重になりうる | 意図どおり（既存の動きで文言の範囲外。CHAT-1021-WKP-03 で「保険が残る間に #568 へ2回出ることは受け入れる」と決めてある。12/1 の run-monthly の成功の後に月次の schedule を外す予定） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1102-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1102-WKP-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1102-wkp
- 確認用URL: なし（配信物は変えていない）
- マージ: 済（SHA は cloudflare の先頭〈このログを含むコミット〉）
- issue: なし
- 判断が必要なこと:
  - scripts/actions_dashboard.py 461〜462行の見出し「cron 実行（毎日・週次・月次）」とリード「毎朝の運行からも呼ばれるものは、その日の定期実行が成功済みなら何もしないフォールバック実行のことがある」が、今の事実（GitHub の schedule が残るのは月次の2本だけで、どちらもゲートを持たない）と合わない。サイトのページではなく mj-logs の actions/index.html（運行表）に出る文言で、生成物に出る扱いにして直していない。直すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7c48f517）: https://github.com/retroeater/mj-logs/tree/main/guide/7c48f517

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c48f517/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c3c97901.md
