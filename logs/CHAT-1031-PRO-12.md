# CHAT-1031-PRO-12

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1031-pro-sched
- 着手時HEAD: f797a06

## 指示

【Claude作成】Claude Code 向け指示：個別ページに「今後の予定」（放送対局・麻雀最強戦・道場部ゲスト）を作る（未公開・プレビューで止める）
Chat-Ref: CHAT-1031-PRO-12
マージ: 判断待ちで止まる
貼る時機: CHAT-1031-PRO-09（work/1031-pro の pro/ の作り直し）が cloudflare へマージされた後（同じ `scripts/generate_pro_pages.py`・`assets/pro.js` を変えるため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-sched を使う（同じ系列のほかの作業ブランチと分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-sched origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-09 の変更（pro/ の作り直し、個別ページから「いま」の面が無いこと）が origin/cloudflare に入っていることを確かめ、入っていなければ何もせず止まる。CHAT-1031-PRO-08 のログ（`docs/logs/CHAT-1031-PRO-08.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、確かめたらその状態の行の末尾に ` / 続き: CHAT-1031-PRO-12` を足す

## 目的
PRO-08 の調べと平野さんの回答に沿って、個別ページ `pro/<slug>/` に、その選手の今後の直近の予定（未放送の放送対局・未放送の麻雀最強戦・道場部ゲスト）を日付の近い順に出す。日が過ぎた予定が残らないように、毎朝の生成とページの JS で鮮度を保つ。この指示では公開しない（#641）。

### 決定（2026-10-31、平野さん）
- 3種類（放送対局・麻雀最強戦・道場部ゲスト）とも作る
- 放送対局は、対局者として名前のある枠に加えて、実況・解説として名前のある枠もその人の予定に出す（PRO-08 の時点の決定「対局者の名前が入っているものだけ」を広げる。名前の無い枠は出さない、は変わらない）
- データの読み方: 放送対局は公開カレンダー「mj_放送対局」を作る部品（`lib/live_calendar.py`）で /live のシートから作る。道場部ゲストはカレンダーの同期と同じ Calendar API で「mj_道場部ゲスト」を読む。最強戦は saikyo/ の生成物（対局前の卓）から読む。公開の iCal（calendar.google.com）には頼らない
- 出し方は PRO-08 の案どおり: 面の名前「今後の予定」、名前の面の次（成績の前）に置き、予定の無い選手は面を出さない。日付の近い順に5件（それより多ければ件数だけ出す）。1行は日付（「11月4日（水）16:00」。道場部は時刻なし）・種類のチップ（「放送」「最強戦」「道場部」）・件名。行き先は、放送は YouTube の枠の URL、最強戦は saikyo/ の卓のページ、道場部は連盟サイトの「本部道場ゲスト表」
- 鮮度: 毎朝の生成に `pro_pages` を足し、ページの JS で日本時間の今日より前の予定を隠す（両方）

### 前提（チャット側。平野さんの決定ではない。要確認）
- 実況・解説の枠の行には、種類のチップの横に役（「実況」「解説」）を小さく添える案（対局者の枠には添えない）。同じ枠で対局者と実況・解説の両方に名前があることは無い見込み（あれば対局者を優先して1行）
- 名前の照合は PRO-08 のとおり登録名と「別名」での現在名への直し。「大阪予選1位」「1st stage勝者」のような未定の名前は照合しないので出ない
- 放送の部品の切り出し: `lib/live_calendar.py` の、/live の【1】【2】【3】から公開カレンダーの予定（件名・開始・枠の URL・説明欄の【対局者】【実況】【解説】）を作る部分を、カレンダーへの書き込みと切り離して pro の生成からも呼べるようにする。カレンダーの同期の出力（予定の中身）が変わらないことをテストで確かめる（同じ入力で前後の予定の一覧が同じ）
- 道場部の読み取りの鍵: 今の `regenerate-page.yml` は `SHEETS_READER_SA_KEY` を渡している。「mj_道場部ゲスト」を読める鍵（同期が使う `GCP_SA_KEY` など）を pro の生成にだけ渡すか、カレンダーを sheets-reader に共有するか（平野さんの手作業）を実物で確かめ、手作業の要らない方を選んでログに書く。鍵が読めない・カレンダーが読めないときは生成を止めず、道場部の予定を出さずに警告を出す（ページの生成は止めない）。鍵の値はログに書かない
- 毎朝の生成: 毎朝の `update-live-channel.yml` が呼ぶ再生成（今は `target_page: live_pages title_pages`）に `pro_pages` を足す案。足すと毎朝のコミットに pro/ の差分が入る（予定の入れ替わりのある選手だけ変わる見込み）。呼ぶ側を `git grep` で洗い、呼ばれ方ごとの振る舞いを書く（docs/notes/github-actions.md「チャット側」）。着手時に同「ワークフローを変更したとき」を読み、作業ブランチで手動実行して確かめる
- JS で隠す: 各行に日付（日本時間の日）を持たせ、`assets/pro.js` が今日より前の行を隠す。隠した後に行が0件なら面ごと隠す。5件の上限は生成のときに今日以降で数えるので、JS で隠したときに5件を下回るのは受け入れる（翌朝の生成で補われる）
- 道場部の11月分は今カレンダーに入っていない（同期の規則で止まっている、#426。規則は CHAT-1031-PRO-13 で直す）。この指示の確かめは、カレンダーに入っている分だけでよい（11月分が入っていなければ、道場部の行が0件でも止まらない）
- 画面の確かめ: 360px・390px の明暗で、予定の多い選手（PRO-08 の一覧で放送の枠が多い HIRO柴田・二階堂亜樹など）・実況・解説だけの予定がある選手（三浦智博など）・最強戦の対局前の卓に出る選手・予定の無い選手を撮って `docs/logs/CHAT-1031-PRO-12/` に置く。端末の日付を変えて JS で過去の行が隠れることも1件確かめる
- 予定の一覧（選手ごとの件数と、5件を超える選手の名前）を `## 経過` に表で書く（PRO-08 の一覧と照合できるように）
- 本番への大量の取得はしない（Cloudflare のレート制限、docs/notes/cloudflare.md）
- 決定は完了時に docs/decisions/pros.md に足す（PRO-08 の時点の「対局者だけ」を置き換えた旨も）

## 手順
1. 0章の確かめの後、放送の部品の切り出し（テストで同期の出力が変わらないこと）と、道場部の読み取りの鍵の確かめを行う
2. 「今後の予定」の面（生成・JS・CSS）と毎朝の生成への `pro_pages` の追加を作り、テストを足す（照合・並び・5件・役の添え方・過去の行を隠す日付の持ち方）。作業ブランチで `regenerate-page.yml`（pro_pages、check 真・偽）と `update-live-channel.yml` の再生成の呼び出しを手動実行して確かめる（書き込みをする実行〈/live の apply〉はしない。再生成の部分だけを確かめる方法が無ければ、dry-run と `regenerate-page.yml` の手動実行で代える）
3. 検査（`python3 -m unittest discover -s scripts/tests`・`--check`・`check_asset_limits.py`）と画面の確かめを流し、新しい文脈のサブエージェントに差分をレビューさせ、仮置きの一覧を書いて、ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- 0章の確かめが通らない
- 前提が実物と食い違い、決定のどれかが守れない（どれが・なぜを書いて止まる）
- 部品の切り出しで、公開カレンダーの同期の出力が変わる
- テスト・`--check`・`check_asset_limits.py`・手動実行が通らない（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 道場部の読み取りに、平野さんの手作業（カレンダーの共有など）が要り、鍵を渡すだけでは読めない（要ることを書いて、道場部を外した形で残りを進め、判断待ちで止まる）
- 未マージの work/ ブランチが `scripts/generate_pro_pages.py`・`assets/pro.js`・`scripts/lib/live_calendar.py`・`.github/workflows/regenerate-page.yml`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントが #219 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 同じ Chat-Ref のコミットなし（`git log --all --grep`）。`PRO` は PRO-01 から続く同じチャットの識別子。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。PRO-09 の変更は origin/cloudflare に入っている（`pro/` の個別ページに「いま」の面の見出しは0件）。PRO-08 のログ（origin/cloudflare）の `## 報告` の状態は「判断待ち」で、末尾に ` / 続き: CHAT-1031-PRO-12` を足した（417575808）
- 作業ブランチ: work/1031-pro-sched はリモートにもローカルにも無かったので origin/cloudflare（f797a06）から作った
- #219: 他セッションの着手中コメントなし。着手中のコメントを書いた（issuecomment-6544577887）。#219・#641 は open
- 0章ゲート: 未マージの work/ で `scripts/generate_pro_pages.py`・`assets/pro.js`・`scripts/lib/live_calendar.py`・`regenerate-page.yml`・`update-live-channel.yml` を変えているブランチは無い。`style.css` は止まる条件の対象外だが、work/1031-pro-nav（PRO-11、未マージ）が変えている（ナビの節と暗い配色の `@media`。こちらは pro の節に足しただけで別の箇所）
- CLAUDE.md 23,786 バイト・handover.md 23,201 バイト（警告域の手前）

### 1. 放送の部品の切り出し（f62a855）

- `lib/live_calendar.py` の `build_desired()`（/live の【1】【2】【3】と予定表から公開カレンダーの予定を作る）は、もとからカレンダーへの読み書きを含まない。切り出したのは `sync_live_calendar.py` の `main()` の読み込みの部分（層1・/live の【2】【3】・予定表の【2】【3】・「【4】カレンダー非掲載」を読んで `build_desired()` に渡す）で、`load_desired(today, fix_name)` にした。`main()` はそれを呼ぶだけになり、`build_desired()`・`plan()`・`body_of()` は変えていない
- 名前の直し方（「別名」の訂正）は `name_fixer(book)` に分け、`load_name_fixer()` はそれを呼ぶ（pro の生成は live/ と同じ辞書を読み済みなので、2回読まない）
- 役ごとの名前は、予定の説明欄（`frame_description()` の形）を `people_of()` で読み戻す（`build_desired()` の予定に欄を足すと、カレンダーへの書き込みの比べに影響しうるので、予定そのものは変えない）
- テスト: `test_sync_live_calendar.LoadDesiredTest`（同じ入力で `load_desired()` と `build_desired()` の予定の一覧が同じ。修正前のコードには `load_desired` が無いので落ちる）・`test_live_calendar.test_people_of_reads_back_the_description`
- `check_unused_names.NAME_RESOLVERS`（名前を解決する関数の表、`test_name_resolvers` が見張る）の `sync_live_calendar.load_name_fixer.fix` を `name_fixer.fix` に直し、`generate_pro_pages.current_name` を足した（利用先は層1・【3】の名前で、検査がもう読んでいる）

### 2. 道場部の読み取りの鍵

- 「mj_道場部ゲスト」は公開カレンダー（docs/notes/dojo-guest-calendar.md）。Calendar API の events.list は、公開のカレンダーなら共有されていないサービスアカウントでも読める見込みなので、今 `regenerate-page.yml` が渡している `SHEETS_READER_SA_KEY`（sheets-reader）でスコープ `calendar.readonly` の資格を作って読む形にした（ワークフローに鍵を足さない・平野さんの手作業も要らない方）
- 実物: 作業ブランチの `regenerate-page.yml`（pro_pages、check 真、run 41210025439）で、道場部の読み取りは警告なしで通った（`今後の予定の道場部ゲスト: 0名・0件`）。読めなければ 403/404 で `gcal.request` が止まり警告になるので、sheets-reader で読めている。0件は11月分がカレンダーに入っていないため（指示の前提のとおり、#426）。読んだ件数を出す行を足した（88e94a8）
- カレンダー ID はコードの定数（`DOJO_CALENDAR_ID`。GitHub の変数 `DOJO_CALENDAR_ID` と同じ公開カレンダーの ID。`live_calendar.CALENDAR_ID` と同じ扱い）

### 3. 「今後の予定」の面（d95c019）

- 生成（`scripts/generate_pro_pages.py`）: `load_plans()` が3種類を読み、名前ごとに日付・時刻の順に並べる。どれかが読めなければその種類を出さずに警告（`load_plans` のテスト）
  - 放送: `load_desired()` の予定のうち種類「枠」で今日以降。説明欄の【対局者】【実況】【解説】の名前を、名前の辞書で現在名に直して照合。役は対局者 > 実況 > 解説 で1人1行、実況・解説だけ役を添える。行き先は説明欄の配信の URL（YouTube、新しいタブ）。仮の予定（予定表）は名前が無いので出ない
  - 最強戦: `saikyo/players/index.html` の `saikyo_players_data` の、順位も結果も空の今日以降の卓。件名「<対局名> <卓>」、行き先は卓のページ `/saikyo/<年度>/<卓>.html`（全卓のページがあることを確かめた）
  - 道場部: 「mj_道場部ゲスト」の終日の予定「<登録名>（道場部）」の今日以降。件名「本部道場 ゲスト」、行き先は `https://www.ma-jan.or.jp/dojo_guest.html`（新しいタブ）
- 面: 名前の面の次（成績の前）に「今後の予定」。5件、多ければ「ほか N件」の1行（押せない）。1行は1段目に日付（「11月4日（水）16:00」、最強戦・道場部は時刻なし）・種類のチップ・役、2段目に件名（1行で入らない分は「…」）。各行に `data-date`（日本時間の日）
- JS（`assets/pro.js`）: 日本時間の今日（端末の時刻帯によらず UTC+9 で計算）より前の `data-date` の行を隠し、残らなければ見出しごと隠す
- CSS（`style.css` の pro の節）: 種類のチップは新サイトの残留のピルの地（`--mj-n-chip`）に 12px 太字、役は 12px の灰色
- テスト（`test_pro_pages.PlansTest`）: 照合（「別名」・未定の名前）・過ぎた枠と仮の予定を出さない・役の添え方と優先・最強戦は日付の過ぎた結果待ちの卓を出さない・道場部の件名の読み方・並び（時刻の無い予定はその日の先頭）・5件と「ほか N件」・`data-date` と `<time>` の値・面を出さない場合・読めない種類の警告
- 簡略化（コードに `簡略化:` で残した）: `sync_live_calendar.py` は `generate_*.py` でも `lib/` でもないので、それだけを変えても push の再生成は pro/ を作り直さない（毎朝の生成で出る）

### 4. 毎朝の生成（9037121）

- `update-live-channel.yml` の job `regenerate` の `target_page` を `live_pages title_pages pro_pages` にした。`regenerate.py` の `RUN_LAST` で pro_pages は最後に作られる
- 呼ぶ側（`git grep`）と振る舞い:
  - `run-daily.yml`（Worker `mj-scheduler` が毎朝 04:00 JST に起動、`workflow_call`）: job `regenerate` は apply（予約の起動は常に真）のときだけ動くので、毎朝 pro/ も作り直してコミットする
  - `update-live-channel.yml` の保険の schedule（06:43 JST、当日の予約の起動が成功済みなら動かない）: 同じ
  - 手動実行: apply を付けたときだけ再生成が動く（付けないときは件数を出すだけで、pro/ は作らない）
  - `workers/scheduler/test/catchup.test.mjs` はワークフローの名前を見るだけで、中身の変更の影響なし
- docs/notes/github-actions.md の一覧の行・docs/notes/live-channel-write.md の「5.」・regenerate-page.yml の注釈を /live・title/・pro/ に直した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
