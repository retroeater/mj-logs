# CHAT-1023-SKV-05

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-skv
- 着手時HEAD: 9096ace14

## 指示

【Claude作成】Claude Code 向け指示：docs/notes のほかの文書の古い記述（gviz で読む・04:30 JST・「リンクを知っている全員」）を今の仕組みに直す Chat-Ref: CHAT-1023-SKV-05 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1023-SKV-02 は 2026-10-23 にマージ済み。SKV-03・SKV-04 と並行してよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-SKV-02 の続き。同ログの `## 経過`「手順3」の表（docs/notes の44件）のうち、今の仕組み（2026-10-21 からの Sheets API・専用フォルダ「【本番環境】」の制限付き、2026-10-16 からの run-daily.yml 04:00 JST）と食い違う記述を直す。
決定（2026-10-23、平野さん）

* SKV-02 で見つかったほかの docs/notes の古い記述は、各系列の作業を待たずに今まとめて直す

前提（チャット側。平野さんの決定ではない）

* SKV-02 の見立てで古い見込みが強いのは15件: sns-book.md:15・21・24・31、live-channel-write.md:29・223、live-page-design.md:33、saikyo-page-design.md:20・644、video-wayhome.md:91、yotei-sheet.md:83・143、static-generation.md:454、scheduler-worker.md:109（要確認）。残りの29件も1件ずつ見て、経緯・戻し方・過去の事実として正しい記述（「2026-10-21 までは」「gviz は…返さなかった」「戻し方」など）は変えない。gviz の性質を今の規則の理由として書いている箇所（フィルタで隠れた行・無いタブ名など）は、今の `lib/sheets.py` がその振る舞いを再現・防いでいるかを確かめ、今の規則として誤りになっている文だけ直す
* 行番号は SKV-02 の時点のもの。今の origin/cloudflare で同じ文を探す
* 正は実物（`scripts/lib/sheets.py`・`.github/workflows/run-daily.yml`・`workers/scheduler/schedule.json`・`scripts/check_sheets_reader.py` など、docs/notes/sheets-reader.md）。確かめられない事実は書かず「未確認の項目」へ。規則は足さない（事実の訂正だけ）。記述は置き換える（追記型にしない）
* docs/notes/ の範囲だけ。SKV-02 が範囲外として挙げたコードの docstring（`update_abema_book.py` の `read_source()`、`abema_book.py` の `GVIZ_DATE` 等）は直さない
* 古いブックの共有（例: yotei-sheet.md の「リンクを知っている全員が閲覧可」）の今の値が文書から決められないときは、docs/notes/sheets-reader.md と CHAT-1021-PRV-07 のログ（`check-sheets-reader.yml` の表）で確かめる。名簿（`meibo.SPREADSHEET_ID`）は専用フォルダの外で公開のまま（2026-10-18 の決定）

手順

1. SKV-02 のログの表の44件を今の origin/cloudflare で確かめ、件ごとに「直す／直さない（理由）」をログの `## 経過` に表で書く。直すものは「前・後・根拠（ファイルと関数・行、または文書の節）」も書く
2. 表のとおり直して1コミットにする。直した文の前後を件ごとにログに引用する
3. 直した後に `git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes ':!docs/notes/handover-archive-2026.md'` を流し、残った件数と、残した理由が手順1の表にあることを確かめる。CHAT-1023-SKV-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1023-SKV-05` を足す

止まる条件

* 実物どうしが食い違い、どちらが正か決められない（その件は直さず、両方を引用して「判断が必要なこと」に書く。ほかは進めてよい）
* 未マージの work/ ブランチが同じ文書の同じ節を変えている、または取り込みで衝突する（重なる文書だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（変更は docs/notes・docs/decisions・docs/logs のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 手順0: ログの「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 0件（`SKV` はこのセッションの識別子、5本目）。雛形の行: 欠けなし。issue: 対象なし
- 作業ブランチ: ローカルの work/1023-skv は origin/cloudflare の祖先（origin/work/1023-skv もマージ済み）→ `git checkout work/1023-skv` のうえ `git merge --ff-only origin/cloudflare`（9096ace14）
- 今の origin/cloudflare での grep は 44件（SKV-02 と同数。行番号は saikyo-page-design.md が 644→648・738→742 にずれた）
- 未マージの work/ ブランチで docs/notes を変えているもの: work/1017-lvp（live-page-design.md）、work/1018-log-367-migrate（github-actions.md）、work/1023-skp（ask-worker.md・redirects.md・saikyo-page-design.md・static-generation.md）、work/1023-skv-detect（abema-book.md・github-actions.md）。直した後に `git merge-tree` で各ブランチと試し、新しい衝突は無い（1018-log-367-migrate の github-actions.md の衝突は origin/cloudflare との間にも元からあり、こちらの行〈38〉と別の箇所）。外した文書は無い

### 手順1 44件の表（今の origin/cloudflare の行番号）

根拠の要点: `scripts/lib/sheets.py`（冒頭の docstring「Sheets API・`SHEETS_READER_SA_KEY`」、`fetch_sheet()`・`fetch_records()`・`fetch_table()` はどれも `check_not_filtered()` を呼ぶ〈67・81・107 行〉、`check_not_filtered()` は隠れた行があれば `FilteredSheetError`、`check_sheet_exists()` は `_book()`〈Sheets API の `spreadsheets.get`〉のタブの一覧に無ければ `ValueError`）、`scripts/check_sheets_reader.py`（`FOLDER_PUBLIC = False`、`BOOKS` で R_選手_SNS・R_タイトル戦_マスタ・R_連盟_YouTube・R_連盟_予定表・R_番組 などは `FOLDER`、名簿は `True`）、`.github/workflows/run-daily.yml`（`images: needs: sns`・`saikyo: needs: images`、冒頭のコメント「毎日 04:00 JST に起動」）、`workers/scheduler/src/scheduler.mjs`（332 行 `{ event: 'catchup', planned: row.time }`）、`scripts/lib/sns.py`（74 行 `fetch_table(sb.SPREADSHEET_ID, tab)`）、`scripts/sync_live_calendar.py`（129・130 行 `fetch_records(yotei.SPREADSHEET_ID, …)`）、`.github/workflows/sync-yotei.yml`（`SHEETS_READER_SA_KEY` を渡す）

| # | ファイル:行 | 判断 | 理由／前→後 |
|---|---|---|---|
| 1 | ask-worker.md:290 | 直さない | 今の共有（制限付き）と、最初の値を直した経緯 |
| 2 | ask-worker.md:307 | 直さない | 手順の注意（新しいブックの既定）。今も正しい |
| 3 | cloud-sessions.md:98 | 直さない | 「2026-10-21 からは使わない」と今の形で書いてある |
| 4 | github-actions.md:26 | 直さない | 「それまでは Worker から 04:30 JST」は経緯 |
| 5 | github-actions.md:38 | **直す** | 期待どおりかの説明「生成が読むブックはログインなしで読める・非公開のはずのブックは読めない」→「専用フォルダのブックは `FOLDER_PUBLIC`〈2026-10-21 から偽〉に従いログインなしで読めない・名簿はログインなしで読める・検証用のブックは読めない」（根拠 `check_sheets_reader.py` の `FOLDER_PUBLIC`・`BOOKS`）。「ログインなし（gviz・htmlview）」は検査が今も gviz で試すので残る |
| 6 | github-actions.md:79 | 直さない | 戻し方の手順 |
| 7 | live-channel-write.md:29 | **直す** | 「『リンクを知っている全員が閲覧可』にしてある（生成が gviz で読むため）」→専用フォルダ・制限付き・読むのは `sheets-reader`（根拠 `BOOKS` の R_連盟_YouTube〈`live_layer3.py`〉が `FOLDER`、sheets-reader.md「段取りと今の状態」） |
| 8 | live-channel-write.md:78 | 直さない | CHAT-1012-ABM-13 の出来事の説明（`values.clear` が書式を残す）。今の `_select()` で同じ読み方になるかは確かめていない（追跡しない） |
| 9 | live-channel-write.md:88 | 直さない | 過去の確かめの記録 |
| 10 | live-channel-write.md:183 | 直さない | 過去の確かめの記録 |
| 11 | live-channel-write.md:223 | **直す** | 「gviz がフィルタで隠れた行を返さず、生成を止める」→「隠れた行があると生成が止まる（`lib/sheets.check_not_filtered()`、#432）」（規則〈フィルタを残さない〉は今も正しい。理由の文が今の仕組みと違った） |
| 12 | live-page-design.md:14 | **直す**（一部） | 「タブの一覧（`htmlview`）」→「タブの一覧（Sheets API の `spreadsheets.get`）」（根拠 `check_sheet_exists()`→`_book()`）。「gviz は存在しないタブ名にも先頭のタブを返すため」は 2026-10-13 の規則の理由として残す（gviz の性質の記述で、規則は今も正しい） |
| 13 | live-page-design.md:33 | **直す** | 「生成（gviz の読み取り）のため、リンクを知っている人が閲覧可にする（案 LV-02 設計(3)）」→「生成は Sheets API で読む。ブックは専用フォルダ・制限付き（…）。2026-10-21 まではリンクを知っている人が閲覧可（案 LV-02 設計(3)）」（根拠 `BOOKS` の R_タイトル戦_マスタ〈`live.py`〉が `FOLDER`） |
| 14 | saikyo-page-design.md:20 | **直す** | 「起動は時刻ではなく、毎朝 04:30 JST に Worker から動く…検知の完了（`workflow_run`）」→「毎朝 04:00 JST に Worker が起動する `run-daily.yml` が、検知の後に `workflow_call` で呼ぶ（2026-10-16 から。それまでは検知の完了の `workflow_run`）」（根拠 run-daily.yml の `saikyo: needs: images`） |
| 15 | saikyo-page-design.md:648 | **直す** | 「Worker `mj-scheduler` が毎日 04:30 JST に入力 `scheduled` 付きで起動し（docs/notes/scheduler-worker.md。」→「毎朝の運行（`run-daily.yml`。Worker `mj-scheduler` が毎日 04:00 JST に起動）が入力 `scheduled` 付きで呼び（docs/notes/github-actions.md「run-daily.yml」。」（根拠 run-daily.yml の `images`〈`scheduled: ${{ inputs.scheduled }}`〉） |
| 16 | saikyo-page-design.md:742 | **直す** | 「gviz はシートのフィルタで隠れた行を返さない。」→「返さなかった。」、節の最後の「読み取った件数が…フィルタを疑う。」→「今は `lib/sheets.check_not_filtered()` が、隠れた行のあるタブを読むと生成を止める（…）」（根拠 `check_not_filtered()`） |
| 17 | scheduler-worker.md:82 | 直さない | 「それまでの5行（…04:30）」は経緯 |
| 18 | scheduler-worker.md:109 | **直す** | ログの例 `{"event":"catchup","planned":"04:30","workflow":"check-image-links.yml",…}` → `{"event":"catchup","planned":"04:00","workflow":"run-daily.yml",…}`（今の起動の表の行。形は scheduler.mjs 332 行） |
| 19 | session-network.md:142 | 直さない | 今の状態（制限付きの後は読めない） |
| 20 | sheets-reader.md:71 | **直す** | 済んだ手順の中の「（今の『リンクを知っている全員』）」→「（この段の時点の『リンクを知っている全員』）」（「今の」が今は誤り） |
| 21 | sheets-reader.md:99 | 直さない | 経緯（2026-10-21 の画面の記録） |
| 22 | sheets-reader.md:100 | 直さない | 経緯 |
| 23 | sheets-reader.md:102 | 直さない | 経緯（切り替え前の注意） |
| 24 | sheets-reader.md:108 | 直さない | 決定の記録（その後の 3. で制限付きにしたと続く） |
| 25 | sheets-reader.md:112 | 直さない | 今の検査の説明（ログインなしの gviz で試す） |
| 26 | sheets-reader.md:122 | 直さない | 戻し方 |
| 27 | sns-book.md:15 | **直す** | 「gviz で読む（ログインなし）」→「`lib/sheets.py` の `fetch_table()`（Sheets API、`sheets-reader`）で読む」（根拠 `lib/sns.py` 74 行） |
| 28 | sns-book.md:21 | **直す** | 「毎日 04:30 の検知（`check-image-links.yml`）」→「毎朝の検知（`check-image-links.yml`。`run-daily.yml` が `update-sns-book` の後に呼ぶ）」（根拠 run-daily.yml の `images: needs: sns`） |
| 29 | sns-book.md:24 | **直す** | 「毎日の更新（04:10）」「写真の検知（…、04:30）」→「毎日の更新（`update-sns-book.yml`）」「写真の検知（…、更新の後）」（04:10 も同じ旧い起動の表の時刻なので一緒に直した） |
| 30 | sns-book.md:31 | **直す** | 「『リンクを知っている全員』の閲覧者にしてある（生成が gviz で読むため）」→専用フォルダ・制限付き（根拠 `BOOKS` の R_選手_SNS が `FOLDER`） |
| 31 | static-generation.md:105 | 直さない | 「gviz の`f`、今は Sheets API の`FORMATTED_VALUE`」と今の形で書いてある |
| 32 | static-generation.md:295 | 直さない | 「gviz と同じく数の列とみなして」は `_select()` が gviz の型の扱いを再現している説明で、今も正しい |
| 33 | static-generation.md:307 | 直さない | 経緯（「それまでは」） |
| 34 | static-generation.md:309 | 直さない | 今の規則（gviz の返し方を再現する） |
| 35 | static-generation.md:311 | 直さない | 今の規則の説明 |
| 36 | static-generation.md:320 | 直さない | 経緯（「返さなかった」）と今の規則の理由 |
| 37 | static-generation.md:354 | 直さない | 今の規則（「gviz は…返していた」は過去形） |
| 38 | static-generation.md:454 | **直す** | 「（毎日 04:30 JST、保険で毎週月曜03:00 JST）」→「（毎朝 `run-daily.yml` から〈04:00 JST 起動、`update-sns-book` の後〉、保険で毎週月曜03:00 JST）」 |
| 39 | title-pages.md:108 | **直す**（一部） | 「タブの一覧（`htmlview`）」→「タブの一覧（Sheets API の `spreadsheets.get`）」。「gviz は存在しないタブ名でも先頭のタブを返す」は gviz の性質の記述として残す |
| 40 | title-pages.md:130 | 直さない | 規則（見出しの不一致で止める）は今も正しい。理由の gviz の性質は規則を入れたときの理由で、規則を誤りにしない |
| 41 | title-pages.md:159 | 直さない | 同上（タブ名を変えるときは読む側を先に直す、は今も正しい） |
| 42 | video-wayhome.md:91 | **直す** | 2026-10-16 の節の「『リンクを知っている全員』の閲覧者」→「当時は『リンクを知っている全員』の閲覧者、2026-10-21 から専用フォルダ『【本番環境】』で制限付き」（根拠 `BOOKS` の R_番組〈`video_book.py`〉が `FOLDER`） |
| 43 | yotei-sheet.md:83 | **直す** | 「スプレッドシートは gviz で読む。」→「`lib/sheets.py` の `fetch_records()`（Sheets API、`sheets-reader`）で読む。」（根拠 `sync_live_calendar.py` 129・130 行、sync-yotei.yml が `SHEETS_READER_SA_KEY` を渡す） |
| 44 | yotei-sheet.md:143 | **直す** | 設定の手順の「『リンクを知っている全員が閲覧可』は今のまま（読むだけなら共有が無くても読める）」→「一般的なアクセスは変えない（専用フォルダ・制限付き…）」（根拠 `BOOKS` の R_連盟_予定表〈`yotei.py`〉が `FOLDER`） |

- 直す: 19件（文書11本）。直さない: 25件
- SKV-02 の見立ての15件はすべて直した。見立てに無かった5件（github-actions.md:38・live-page-design.md:14・saikyo-page-design.md:742・sheets-reader.md:71・title-pages.md:108）と、sns-book.md:24 の 04:10 も今の仕組みと食い違うので直した
- 実物どうしの食い違い: なし。範囲外で気づいたこと（直さない）: github-actions.md の check-image-links の行は「入力 `scheduled` ではジョブ `saikyo` だけが動く（#514）」、sns-book.md:21 は「`check`・`saikyo` は 2026-10-13 から両方とも毎日」と書き、読み方が食い違って見える（今回の範囲〈gviz・04:30・公開〉と別）。実物の `.github/workflows/check-image-links.yml` は、`check` が `inputs.saikyo_resolve_test == ''`、`saikyo` が `github.event_name != 'schedule'`、`sns_book` が `!cancelled() && github.event_name != 'schedule' && …` で、入力 `scheduled` の起動では3つとも動く。古いのは github-actions.md「ワークフローを手動実行するとき」の「入力`scheduled`（…）ではジョブ`saikyo`だけが動く（#514）」の方（直していない）

### 手順2 直した文の前後（コミット「docs: update notes to Sheets API, private folder and run-daily」、word-diff。[-前-]{+後+}）

    | `check-sheets-reader.yml` | 手動だけ（schedule・push・workflow_call を持たず、Worker の起動の表にも載せない）。コードが ID を持つ全ブック（`scripts/check_sheets_reader.py` の `BOOKS`）を、ログインなし（gviz・htmlview）と読み取り用サービスアカウント `sheets-reader`（Secret `SHEETS_READER_SA_KEY`、スコープ `spreadsheets.readonly`、Sheets API の [-`spreadsheets.get`・`values.get`）の両方で読み、ブックごとに成否・タブの数・各タブの行数と列数と、期待どおりか（生成が読むブックはログインなしで読める・非公開のはずのブックは読めない）を実行サマリーに出す（#557、`docs/notes/sheets-reader.md`）。出すのは題・タブ名・行数・列数と要求の成否だけ（値・見出し・メールアドレス・プロジェクト-]{+`spreadsheets.get`・`values.get`）の両方で読み、ブックごとに成否・タブの数・各タブの行数と列数と、期待どおりか（専用フォルダのブックは `FOLDER_PUBLIC`〈2026-10-21 から偽〉に従いログインなしで読めない・名簿はログインなしで読める・検証用のブックは読めない）を実行サマリーに出す（#557、`docs/notes/sheets-reader.md`）。出すのは題・タブ名・行数・列数と要求の成否だけ（値・見出し・メールアドレス・プロジェクト+} ID は出さない。認証の action は `export_environment_variables: false`）。結果にかかわらず成功で終わる（認証の情報を読めないときだけ失敗） |
    [-3層のスプレッドシートはサービスアカウントを編集者で共有し、「リンクを知っている全員が閲覧可」にしてある（生成が gviz で読むため）。-]{+3層のスプレッドシートはサービスアカウントを編集者で共有している。専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」（2026-10-21〜。読むのは `sheets-reader`、docs/notes/sheets-reader.md「段取りと今の状態」）。+}
    - [-タブに**フィルタをかけたままにしない**。gviz がフィルタで隠れた行を返さず、生成を止める（#432）。並べ替えは構わない-]{+タブに**フィルタをかけたままにしない**。隠れた行があると生成が止まる（`lib/sheets.check_not_filtered()`、#432）。並べ替えは構わない+}
    **シートの読み方（LV-13）**: /live が読むタブ（連盟プロ以外・別名・放送対局・連盟ch・タイトル戦）は、`lib/sheets.py` の `fetch_records()` で見出しの名前で列を読む。生成に使う列（`lib/live.py` の `*_HEADERS`）が欠けている・重複しているときだけ止め、ほかの列の追加・並べ替えでは止まらない（LV-29 で、並べ替え・未知の列は通り、欠け・重複では止まることを試した）。gviz [-は存在しないタブ名にも先頭のタブを返すため、タブの有無はタブの一覧（`htmlview`）で確かめ、無いタブ名は止める（`lib/sheets.check_sheet_exists()`。先頭のタブの名前には頼らない、2026-10-13）-]{+は存在しないタブ名にも先頭のタブを返すため、タブの有無はタブの一覧（Sheets API の `spreadsheets.get`）で確かめ、無いタブ名は止める（`lib/sheets.check_sheet_exists()`。先頭のタブの名前には頼らない、2026-10-13）+}
    - [-生成（gviz の読み取り）のため、リンクを知っている人が閲覧可にする（案-]{+生成は Sheets API で読む。ブックは専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」（2026-10-21〜。読むのは `sheets-reader`、docs/notes/sheets-reader.md「段取りと今の状態」）。2026-10-21 まではリンクを知っている人が閲覧可（案+} LV-02 設計(3)）
    [-起動は時刻ではなく、毎朝 04:30-]{+毎朝 04:00+} JST に Worker [-から動く写真のリンク切れ検知（`check-image-links.yml`）の完了（`workflow_run`）。-]{+が起動する `run-daily.yml` が、写真のリンク切れ検知（`check-image-links.yml`）の後に `workflow_call` で呼ぶ（2026-10-16 から。それまでは検知の完了の `workflow_run`）。+}
    - **毎日の検知**: `.github/workflows/check-image-links.yml` のジョブ `saikyo` [-を、Worker-]{+を、毎朝の運行（`run-daily.yml`。Worker+} `mj-scheduler` が毎日 [-04:30-]{+04:00+} JST [-に-]{+に起動）が+}
      入力 `scheduled` [-付きで起動し（docs/notes/scheduler-worker.md。この起動ではジョブ-]{+付きで呼び（docs/notes/github-actions.md「run-daily.yml」。この起動ではジョブ+} `saikyo` だけが動く）、`--json` で実行して
    **gviz [-はシートのフィルタで隠れた行を返さない。**-]{+はシートのフィルタで隠れた行を返さなかった。**+} 2026-09-21、「連盟プロ以外」にフィルタがかかったままの間、
    [-読み取った件数が直前の再生成のログと大きく違うときは、シートのフィルタを疑う。-]{+今は `lib/sheets.check_not_filtered()` が、隠れた行（フィルタ・非表示・折りたたみ）のあるタブを読むと生成を止める（docs/notes/static-generation.md「シートの読み方（Sheets API）」）。+}
    - 取りこぼしの埋め合わせの起動: [-`{"event":"catchup","planned":"04:30","workflow":"check-image-links.yml","status":204}`（`dispatch`-]{+`{"event":"catchup","planned":"04:00","workflow":"run-daily.yml","status":204}`（`dispatch`+} の行は書かない）
    [-**一般的なアクセス（今の「リンクを知っている全員」）は、この段では変えない。**-]{+**一般的なアクセス（この段の時点の「リンクを知っている全員」）は、この段では変えない。**+}
    - [-gviz で読む（ログインなし）。1回の実行で1度だけ読む。【2】で名前が重複していたら生成を止める-]{+`lib/sheets.py` の `fetch_table()`（Sheets API、`sheets-reader`）で読む。1回の実行で1度だけ読む。【2】で名前が重複していたら生成を止める+}
    - [-**毎日 04:30 の検知（`check-image-links.yml`）は3つのジョブで画像を見る。**-]{+**毎朝の検知（`check-image-links.yml`。`run-daily.yml` が `update-sns-book` の後に呼ぶ）は3つのジョブで画像を見る。**+} ジョブ `check` は `jpml_pros.html` の `<img>`（X・note・YouTube）、ジョブ `saikyo` は最強戦の写真（2026-10-13 から両方とも毎日。それまで `check` は週1回）、ジョブ `sns_book` はこのブックの実効の値（【3】に【4】を重ねた全行。ページに出ない行も見る）の画像（`scripts/check_sns_book_images.py`、2026-10-13 から）
    - **X API を呼ぶのは SNS [-ブックの毎日の更新（04:10）だけ。** 写真の検知（`collect_saikyo_images.py`、04:30）は-]{+ブックの毎日の更新（`update-sns-book.yml`）だけ。** 写真の検知（`collect_saikyo_images.py`、更新の後）は+} X API を呼ばず、最強戦の写真（SNS ブックの実効の値）を HEAD で確かめ、取得できない選手の状態を【3】の X状態と【4】の有無で検知の issue に出す
    - ID は `scripts/lib/sns_book.py` の `SPREADSHEET_ID`。持ち主は平野さん。`live-channel-writer` [-を編集者で共有し、「リンクを知っている全員」の閲覧者にしてある（生成が gviz で読むため）-]{+を編集者で共有している。専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」（2026-10-21〜。読むのは `sheets-reader`、docs/notes/sheets-reader.md「段取りと今の状態」）+}
    - `check_image_links.py` — `jpml_pros.html` [-内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎日 04:30 JST、保険で毎週月曜03:00-]{+内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎朝 `run-daily.yml` から〈04:00 JST 起動、`update-sns-book` の後〉、保険で毎週月曜03:00+} JST）
    - gviz [-は存在しないタブ名でも先頭のタブ（今は「リーグ」）を返す。生成はタブの一覧（`htmlview`）に無いタブ名を止めるので、旧名「プロ以外」で読むと止まる（`lib/sheets.check_sheet_exists()`、2026-10-13）-]{+は存在しないタブ名でも先頭のタブ（今は「リーグ」）を返す。生成はタブの一覧（Sheets API の `spreadsheets.get`）に無いタブ名を止めるので、旧名「プロ以外」で読むと止まる（`lib/sheets.check_sheet_exists()`、2026-10-13）+}
    - 平野さんが「帰り道」「Mつく」「女流勉強会」のタブを1つのブック（`lib/video_book.py` の [-`SPREADSHEET_ID`。「リンクを知っている全員」の閲覧者、サービスアカウントは編集者）にまとめた。-]{+`SPREADSHEET_ID`。当時は「リンクを知っている全員」の閲覧者、2026-10-21 から専用フォルダ「【本番環境】」で制限付き。サービスアカウントは編集者）にまとめた。+}
    カレンダーの読み書きは `GCP_SA_KEY`（カレンダー用のサービスアカウント。平野さんが「予定の変更」で共有済み）。スプレッドシートは [-gviz で読む。-]{+`lib/sheets.py` の `fetch_records()`（Sheets API、`sheets-reader`）で読む。+}
    3. [-「リンクを知っている全員が閲覧可」は今のまま（読むだけなら共有が無くても読める）-]{+一般的なアクセスは変えない（専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」（2026-10-21〜。読むのは `sheets-reader`、docs/notes/sheets-reader.md「段取りと今の状態」））+}

### 手順3 直した後の grep

- `git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes ':!docs/notes/handover-archive-2026.md'`: 32件（44件から、文が当たらなくなった12件が減った）
- 残った32件: 表の「直さない」25件（#1〜4・6・8〜10・17・19・21〜26・31〜37・40・41）と、直したが経緯・gviz の性質の語が残る7件（#5〈検査が今も gviz で試す〉・#12〈gviz の性質〉・#13〈「2026-10-21 までは」〉・#16〈「返さなかった」〉・#20〈「この段の時点の」〉・#39〈gviz の性質〉・#42〈「当時は」〉）。残した理由はすべて表にある
- CHAT-1023-SKV-02 のログの `## 報告` の状態に ` / 続き: CHAT-1023-SKV-05` を足した

### マージ

- 変更は docs/notes（11本）・docs/decisions/saikyo.md・docs/logs のみ（指示の「マージ:」どおり cloudflare へ入れる）
- 再fetch・`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1023-skv:cloudflare`（9096ace14..dd43b7585、fast-forward）

## 報告

- 状態: 判断待ち / 続き: CHAT-1023-SKV-07
- ブランチ: work/1023-skv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-SKV-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skv
- 確認用URL: なし
- マージ: 済（dd43b7585）
- issue: なし
- 判断が必要なこと:
  - 範囲外で見つけた古い記述: docs/notes/github-actions.md「ワークフローを手動実行するとき」の「入力`scheduled`（Worker からの起動用、手では付けない）ではジョブ`saikyo`だけが動く（#514）」。実物の check-image-links.yml では `scheduled` の起動で check・saikyo・sns_book の3つとも動く（`## 経過`「手順1」の表の後）。別の指示で直すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 560a6f35）: https://github.com/retroeater/mj-logs/tree/main/guide/560a6f35

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
