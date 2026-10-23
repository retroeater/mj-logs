# CHAT-1023-SKV-02

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-skv
- 着手時HEAD: 2b6e0de9f

## 指示

【Claude作成】Claude Code 向け指示：docs/notes/abema-book.md の古い記述（gviz・04:30 JST・「リンクを知っている全員」）を今の仕組みに直す Chat-Ref: CHAT-1023-SKV-02 マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1023-SKV-01 は 2026-10-23 に完了・マージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-SKV-01 の着手前の確認で、docs/notes/abema-book.md が今の仕組みと食い違っていると分かった（同ログの `## 経過`「前提の照合」）。#557（2026-10-21 の Sheets API への切り替え・非公開化）と run-daily.yml（2026-10-16〜）の後の形に直す。
決定（2026-10-23、平野さん）

* docs/notes/abema-book.md の古い記述は、ほかの文書の整理を待たずに今直す

前提（チャット側。平野さんの決定ではない）

* 古いと見込む箇所（要確認）: 「ブック」の「『リンクを知っている全員』の閲覧者にしてある（生成が gviz で読むため）」、「毎朝の作り直し」の「毎朝 04:30 JST の写真の検知が cloudflare で終わった後など」と「【1】は生成と同じ gviz（`fetch_table()`）で読み」、「生成の読み方」の「【2】と【3】を gviz で読み」。ほかにもあれば同じく直す
* 正は実物（`scripts/lib/sheets.py`・`scripts/lib/abema_book.py`・`scripts/update_abema_book.py`・`scripts/generate_saikyo_pages.py`・`.github/workflows/run-daily.yml`・`regenerate-saikyo.yml`、docs/notes/sheets-reader.md・github-actions.md）。ブックの共有（専用フォルダ・制限付き・sheets-reader の閲覧者・live-channel-writer の編集者）は docs/notes/sheets-reader.md と CHAT-1021-PRV-07 のログによる。実物と文書のどちらでも確かめられない事実は書かず「未確認の項目」へ
* 記述は置き換える（追記型にしない。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。規則は足さない（事実の訂正だけ）

手順

1. docs/notes/abema-book.md を全部読み、上の箇所とほかの古い記述（`git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes/abema-book.md` を含む）を実物と照らす。食い違う箇所ごとに「前・後・根拠（ファイルと関数・行）」をログの `## 経過` に表で書く
2. 表のとおり直してコミットする。直した後の該当の節をログに引用する
3. 同じ種類の古い記述（gviz・04:30 JST・「リンクを知っている全員」）が docs/notes/ のほかの文書にもあるかを `git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes`（handover-archive-2026.md は除く）で探し、ファイル・行・今の文を表にしてログに書く。直さない（数が多ければ別の指示にする）

止まる条件

* 実物どうしが食い違い、どちらが正か決められない（その箇所は直さず、両方を引用して「判断が必要なこと」に書く。ほかの箇所は進めてよい）
* 未マージの work/ ブランチが docs/notes/abema-book.md の同じ節を変えている、または取り込みで衝突する（別の節の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（変更は docs/notes・docs/decisions・docs/logs のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 手順0: ログの「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミット: 0件。`SKV` は CHAT-1023-SKV-01 で使ったこのセッションの識別子（2本目の指示なので重複の確認は対象外）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。issue: 対象なし
- 作業ブランチ: ローカルの work/1023-skv は origin/cloudflare の祖先（origin/work/1023-skv もマージ済み）→ `git checkout work/1023-skv` のまま `git merge --ff-only origin/cloudflare`（既に最新、2b6e0de9f）
- 未マージの work/ ブランチで docs/notes/abema-book.md を変えているもの: なし

### 手順1 食い違いの表（docs/notes/abema-book.md）

| 節 | 前 | 後 | 根拠 |
|---|---|---|---|
| ブック | `live-channel-writer` を編集者で共有し、「リンクを知っている全員」の閲覧者にしてある（生成が gviz で読むため） | `live-channel-writer` を編集者で共有している。ほかの本番のブックと同じく専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」、`sheets-reader` はフォルダの閲覧者（2026-10-21〜、docs/notes/sheets-reader.md「段取りと今の状態」） | docs/notes/sheets-reader.md「段取りと今の状態」の決定（専用フォルダ・`sheets-reader` は閲覧者）と 3.（フォルダを「制限付き」）、`scripts/check_sheets_reader.py` の `FOLDER_PUBLIC = False`・`BOOKS` の `("R_ABEMA_最強戦", "scripts/lib/abema_book.py", "SPREADSHEET_ID", FOLDER)`、CHAT-1021-PRV-07 のログ（58f9f0a97）の表「R_ABEMA_最強戦: ログインなし 読めない（HTTP 401）・sheets-reader 成功」。編集者の共有は CHAT-1023-SKV-01 の update（run 39899739516）で「【2】最強戦 を書きました」 |
| 毎朝の作り直し | 毎朝 04:30 JST の写真の検知が cloudflare で終わった後など | 毎朝 04:00 JST に起動する `run-daily.yml` が、写真のリンク切れ検知〈ジョブ `images`〉の後に呼ぶ | `.github/workflows/run-daily.yml` のジョブ `saikyo`（`needs: images`、`uses: ./.github/workflows/regenerate-saikyo.yml`）と冒頭のコメント「毎日 04:00 JST に起動する（2026-10-16 から）」、`workers/scheduler/schedule.json` の `run-daily.yml` の行、`regenerate-saikyo.yml` 冒頭のコメント |
| 毎朝の作り直し | 【1】は生成と同じ gviz（`fetch_table()`）で読み | 【1】は生成と同じ `lib/sheets.py` の `fetch_table()`（Sheets API、`sheets-reader`）で読み | `scripts/update_abema_book.py` の `read_source()`（`from lib.sheets import fetch_table`）、`scripts/lib/sheets.py` の冒頭の docstring（Sheets API・`SHEETS_READER_SA_KEY`）と `fetch_table()`、`regenerate-saikyo.yml` のジョブ `abema` の env `SHEETS_READER_SA_KEY` |
| 生成の読み方 | 【2】と【3】を gviz で読み | 【2】と【3】を `lib/sheets.py` の `fetch_table()`（Sheets API）で読み | `scripts/generate_saikyo_pages.py` の `load_videos()`（`fetch_table(abema_book.SPREADSHEET_ID, tab)` を【2】【3】に） |

- ほかの節（タブ・割り当ての規則・司会・実況・解説の読み分け・【3】のライブ枠・2026-10-12 時点の数）に gviz・04:30・公開の前提の記述は無い。「2026-10-12 時点の【2】」は日付つきの事実なので変えない
- 実物どうしの食い違い: なし
- 範囲外で気づいたこと（直さない。docs/notes だけの指示のため）: `scripts/update_abema_book.py` の `read_source()` の docstring「【1】を生成と同じ gviz で読む」と、`scripts/lib/abema_book.py` の `date_text()` の docstring・定数 `GVIZ_DATE` に gviz の名が残る（`lib/sheets.py` が gviz の返し方を再現するので、動作としては誤りではない）

### 手順2 直した後の節（抜き書き。コミット「docs: update abema-book notes to Sheets API and run-daily」）

```
## ブック

- ID は `scripts/lib/abema_book.py` の `SPREADSHEET_ID`。持ち主は平野さん。`live-channel-writer` を編集者で共有している。ほかの本番のブックと同じく専用フォルダ「【本番環境】」にあり、一般的なアクセスは「制限付き」、`sheets-reader` はフォルダの閲覧者（2026-10-21〜、docs/notes/sheets-reader.md「段取りと今の状態」）

## 毎朝の作り直し（`scripts/update_abema_book.py`）

- `regenerate-saikyo.yml` のジョブ `abema` が、最強戦の再生成（ジョブ `regenerate`）の前に動く（毎朝 04:00 JST に起動する `run-daily.yml` が、写真のリンク切れ検知〈ジョブ `images`〉の後に呼ぶ。新しい予約実行は持たない）。`abema` が失敗しても再生成は今の【2】のまま動く
- 【1】は生成と同じ `lib/sheets.py` の `fetch_table()`（Sheets API、`sheets-reader`）で読み、「最強戦」シートは生成と同じ読み方（`generate_saikyo_pages.load_rows()`・`build_matches()`、名前は現在名）。書く直前に【2】を読み直して、読んだときから変わっていれば書かない。書いた後に読み直して同じことを確かめる

## 生成の読み方

`generate_saikyo_pages.load_videos()` が【2】と【3】を `lib/sheets.py` の `fetch_table()`（Sheets API）で読み、`abema_book.video_urls()` が【2】の URL を【3】の空でない URL で上書きする（卓の鍵は 年度・対局日・対局・ステージ・卓の英字）。年度が4桁の数字で読めない行は警告して飛ばす（`abema_book.table_cells()` が URL と司会・実況・解説を同じ鍵でまとめ、【3】は空でないセルだけ上書きする）。
```

### 手順3 docs/notes のほかの文書（`git grep -n -e gviz -e '04:30' -e 'リンクを知っている' -- docs/notes ':!docs/notes/handover-archive-2026.md'`、44件。直していない）

経緯として正しい記述（「2026-10-21 までは」「戻し方」「gviz は…返さなかった」など）も含めて全件を載せる。今の仕組みとして古い見込みが強いのは、sns-book.md:15・31・21・24、live-channel-write.md:29・223、live-page-design.md:33、saikyo-page-design.md:20・644、video-wayhome.md:91、yotei-sheet.md:83・143、static-generation.md:454、scheduler-worker.md:109（要確認）。件数が多いので別の指示にする

| # | ファイル:行 | 今の文 |
|---|---|---|
| 1 | docs/notes/ask-worker.md:290 | 一般的なアクセスは「**制限付き**」（最初は「リンクを知っている全員（閲覧者）」になっていたのを、2026-10-13 01:44 に直した。質問文が入るので公開しない） |
| 2 | docs/notes/ask-worker.md:307 | 3. 同じ「共有」の画面の「一般的なアクセス」を「**制限付き**」にする（新しいブックの既定が「リンクを知っている全員」のことがある。質問文が入るので公開しない） |
| 3 | docs/notes/cloud-sessions.md:98 | - `docs.google.com`（スプレッドシートの gviz・htmlview。2026-10-21 からの生成は Sheets API〈`sheets.googleapis.com`〉で読むので使わない。`check-sheets-reader.yml` のログインなしの比べと、非公開にする前の確かめだけ） |
| 4 | docs/notes/github-actions.md:26 | \| `check-image-links.yml` \| 毎朝 `run-daily.yml` から `update-sns-book` の後に（入力 `scheduled`、2026-10-16 から。それまでは Worker から 04:30 JST） `jpml_pros.html` の画像（ジョブ `check`）・最強戦の選手写真（ジョブ `saikyo`）・SNS ブックの実効の値の全行の画像（ジョブ `sns_book`、`check` の後に動く。X は `_400x400` だけ。`scripts/check_sns_book_images.py`）のリンク切れを確かめ、常設issueに書き出す（`check` と `sns_book` は「画像リンク切れの検知結果」の本文の自分の節だけを書き換え、全節で切れが無いと閉じる。閉じた後に切れが出たら、新しく作らずに常設の #352〈ラベル `種類: 常設`〉を開き直す〈2026-10-14 から〉。`saikyo` は別の issue）。手動実行の入力 `dry_run`（既定は書く）で issue を書かずに試せる。毎週月曜03:00 JST の予約実行（`schedule`）は `check` だけを動かす保険（外す予定は #504） \| |
| 5 | docs/notes/github-actions.md:38 | \| `check-sheets-reader.yml` \| 手動だけ（schedule・push・workflow_call を持たず、Worker の起動の表にも載せない）。コードが ID を持つ全ブック（`scripts/check_sheets_reader.py` の `BOOKS`）を、ログインなし（gviz・htmlview）と読み取り用サービスアカウント `sheets-reader`（Secret `SHEETS_READER_SA_KEY`、スコープ `spreadsheets.readonly`、Sheets API の `spreadsheets.get`・`values.get`）の両方で読み、ブックごとに成否・タブの数・各タブの行数と列数と、期待どおりか（生成が読むブックはログインなしで読める・非公開のはずのブックは読めない）を実行サマリーに出す（#557、`docs/notes/sheets-reader.md`）。出すのは題・タブ名・行数・列数と要求の成否だけ（値・見出し・メールアドレス・プロジェクト ID は出さない。認証の action は `export_environment_variables: false`）。結果にかかわらず成功で終わる（認証の情報を読めないときだけ失敗） \| |
| 6 | docs/notes/github-actions.md:79 | 1. `workers/scheduler/schedule.json` を5行（update-live-channel 04:00・update-sns-book 04:10・sync-dojo-calendar 04:15・cleanup-branches 04:20・check-image-links 04:30）に戻す |
| 7 | docs/notes/live-channel-write.md:29 | 3層のスプレッドシートはサービスアカウントを編集者で共有し、「リンクを知っている全員が閲覧可」にしてある（生成が gviz で読むため）。 |
| 8 | docs/notes/live-channel-write.md:78 | （`values.clear` は値だけを消して書式を残すため、手で作った下書きの書式が残ると gviz で年度が日付として読まれる、CHAT-1012-ABM-13）。 |
| 9 | docs/notes/live-channel-write.md:88 | `fetch_records()`（ページ生成と同じ、gvizの経路）でも別途タブを読んで突き合わせた。 |
| 10 | docs/notes/live-channel-write.md:183 | 実行後に gviz（`fetch_records()`）で別に読み、本番の「放送対局」3,495行・「連盟ch」14,058行が実行前と同じこと、 |
| 11 | docs/notes/live-channel-write.md:223 | - タブに**フィルタをかけたままにしない**。gviz がフィルタで隠れた行を返さず、生成を止める（#432）。並べ替えは構わない |
| 12 | docs/notes/live-page-design.md:14 | **シートの読み方（LV-13）**: /live が読むタブ（連盟プロ以外・別名・放送対局・連盟ch・タイトル戦）は、`lib/sheets.py` の `fetch_records()` で見出しの名前で列を読む。生成に使う列（`lib/live.py` の `*_HEADERS`）が欠けている・重複しているときだけ止め、ほかの列の追加・並べ替えでは止まらない（LV-29 で、並べ替え・未知の列は通り、欠け・重複では止まることを試した）。gviz は存在しないタブ名にも先頭のタブを返すため、タブの有無はタブの一覧（`htmlview`）で確かめ、無いタブ名は止める（`lib/sheets.check_sheet_exists()`。先頭のタブの名前には頼らない、2026-10-13） |
| 13 | docs/notes/live-page-design.md:33 | - 生成（gviz の読み取り）のため、リンクを知っている人が閲覧可にする（案 LV-02 設計(3)） |
| 14 | docs/notes/saikyo-page-design.md:20 | 起動は時刻ではなく、毎朝 04:30 JST に Worker から動く写真のリンク切れ検知（`check-image-links.yml`）の完了（`workflow_run`）。 |
| 15 | docs/notes/saikyo-page-design.md:644 | - **毎日の検知**: `.github/workflows/check-image-links.yml` のジョブ `saikyo` を、Worker `mj-scheduler` が毎日 04:30 JST に |
| 16 | docs/notes/saikyo-page-design.md:738 | **gviz はシートのフィルタで隠れた行を返さない。** 2026-09-21、「連盟プロ以外」にフィルタがかかったままの間、 |
| 17 | docs/notes/scheduler-worker.md:82 | 今の表は3行: `run-daily.yml` 毎日 04:00（毎朝の運行。2026-10-16 から。update-live-channel・update-sns-book・check-image-links・regenerate-saikyo を順に、sync-dojo-calendar・cleanup-branches を並行に呼ぶ。`docs/notes/github-actions.md`「run-daily.yml」、#564）、`run-weekly.yml` 月曜 02:00（週次の運行。初回は 2026-10-26）、`run-monthly.yml` 1日 03:00（月次の運行。初回は 2026-11-01。2つは `docs/notes/github-actions.md`「run-weekly.yml・run-monthly.yml」、#564 の段④）。それまでの5行（update-live-channel 04:00・update-sns-book 04:10・sync-dojo-calendar 04:15・delete-merged-branches 04:20・check-image-links 04:30）と戻し方は同じ節。`sync-logs.yml` 毎日 05:30 の行は 2026-10-07 に消した（#298）。時刻の案は #504 の本文「起動時刻の案と範囲」。 |
| 18 | docs/notes/scheduler-worker.md:109 | - 取りこぼしの埋め合わせの起動: `{"event":"catchup","planned":"04:30","workflow":"check-image-links.yml","status":204}`（`dispatch` の行は書かない） |
| 19 | docs/notes/session-network.md:142 | 専用フォルダ「【本番環境】」を「制限付き」にした後は、ログインなしの gviz・CSV エクスポート・htmlview では読めない。Claude Code の環境には鍵が無いので、 |
| 20 | docs/notes/sheets-reader.md:71 | **一般的なアクセス（今の「リンクを知っている全員」）は、この段では変えない。** |
| 21 | docs/notes/sheets-reader.md:99 | 親の「Public」フォルダが「リンクを知っている全員 → 制限付き」と表示された（平野さんは取り消した）。 |
| 22 | docs/notes/sheets-reader.md:100 | この1冊の「リンクを知っている全員」はファイルの設定ではなく、「Public」フォルダから受け継いでいる。ほかのブックも同じかは確かめていない（要確認）。 |
| 23 | docs/notes/sheets-reader.md:102 | - ファイルを「Private」のフォルダへ移すと公開リンクで読めなくなり、**移した時点で今の生成（gviz・htmlview・CSV エクスポート）が止まる** |
| 24 | docs/notes/sheets-reader.md:108 | 2026-10-21 の平野さんの決定: sheets-reader への共有はフォルダごと。専用フォルダ「【本番環境】」（一般的なアクセスは「リンクを知っている全員（閲覧者）」、`sheets-reader@…` は閲覧者）を用意し、本番のブックをすべて移した（09:58）。 |
| 25 | docs/notes/sheets-reader.md:112 | ログインなしの gviz・htmlview と、sheets-reader の `spreadsheets.get`・各タブの `values.get` の成否を実行サマリーに表で出す（題・タブ名・行数・列数と成否だけ）。 |
| 26 | docs/notes/sheets-reader.md:122 | - 戻し方: フォルダの一般的なアクセスを「リンクを知っている全員」に戻す（生成は Sheets API で読むので、戻しても止まらない）。コードを戻すとき（gviz に戻す）は、切り替えのコミット（8bcb3a6e2 と 095f36810）を revert する（フォルダが公開に戻っていることが前提） |
| 27 | docs/notes/sns-book.md:15 | - gviz で読む（ログインなし）。1回の実行で1度だけ読む。【2】で名前が重複していたら生成を止める |
| 28 | docs/notes/sns-book.md:21 | - **毎日 04:30 の検知（`check-image-links.yml`）は3つのジョブで画像を見る。** ジョブ `check` は `jpml_pros.html` の `<img>`（X・note・YouTube）、ジョブ `saikyo` は最強戦の写真（2026-10-13 から両方とも毎日。それまで `check` は週1回）、ジョブ `sns_book` はこのブックの実効の値（【3】に【4】を重ねた全行。ページに出ない行も見る）の画像（`scripts/check_sns_book_images.py`、2026-10-13 から） |
| 29 | docs/notes/sns-book.md:24 | - **X API を呼ぶのは SNS ブックの毎日の更新（04:10）だけ。** 写真の検知（`collect_saikyo_images.py`、04:30）は X API を呼ばず、最強戦の写真（SNS ブックの実効の値）を HEAD で確かめ、取得できない選手の状態を【3】の X状態と【4】の有無で検知の issue に出す |
| 30 | docs/notes/sns-book.md:31 | - ID は `scripts/lib/sns_book.py` の `SPREADSHEET_ID`。持ち主は平野さん。`live-channel-writer` を編集者で共有し、「リンクを知っている全員」の閲覧者にしてある（生成が gviz で読むため） |
| 31 | docs/notes/static-generation.md:105 | (gviz の`f`、今は Sheets API の`FORMATTED_VALUE`)。旧Google Charts版の`Table`は |
| 32 | docs/notes/static-generation.md:295 | - `jpml_pros`の成績の列の元の8項目（鳳凰出場・鳳凰<最新の期>・鳳凰最高・桜花出場・桜花<最新の期>・桜花最高・最強出場・放送対局。鳳凰の3項目は表に出していない）は「プロ」シートの O〜U・W 列（数式）ではなく、`lib/pro_stats.py` が集計元のタブ（「鳳凰」「桜花」「リーグ」「最強戦」、旧「対局」）から生成時に数える。定義は数式と同じ（鳳凰出場は表示 Y の行数、桜花出場は表示を問わない行数、最高は全行のリーグキーの最小を「R_タイトル戦_マスタ」の「リーグ」タブでリーグ名に戻す、最強出場は表示 Y の対局の行数、放送対局は旧「対局」の対局者・実況・解説で登録名を部分一致で含むセルの数。#536 の段3）。最新の期は「鳳凰」の最大の (期, 前後)・「桜花」の最大の期で、見出し・検索欄の placeholder・aria の文言も生成時の期から作る。最新の期のリーグの Ampai の URL は「R_タイトル戦_マスタ」の「Ampai」シート（`lib/ampai.py`、`live.SPREADSHEET_ID`。2026-10-23 に「R_Ampai」の「成績表」から移した、#624。1行＝タイトル戦・期・前後・リーグ・組、「-」は空）から、選手の最新の期のタイトル戦・期・前後・リーグ・組（組は「鳳凰」の「組」の列、`lib/houou_tab.py`。どちらも 1・2・3…の数字で、「-」・空欄は組なし。それ以外は止める。Ampai の「組」は gviz と同じく数の列とみなして数でない値を空で返すため、表示の値〈`fetch_csv()`〉でも確かめる）で引く。合う行が無ければリンクを付けない（鳳凰位・桜花も付かない）。最新の期の行がブックに1行も無いときは生成のログに警告を出し、`check-meibo.yml` の週次の実行（`scripts/check_ampai.py`）が issue「Ampai の URL が無い期」に書く（#548。「プロ」の鳳凰Ampai・桜花Ampai 列と「鳳凰Ampai」「桜花Ampai」タブは読まない）。「リーグ」タブ（スプレッドシート「R_タイトル戦_マスタ」、`live.SPREADSHEET_ID`。2026-10-13 に正本から移した）は1行が タイトル戦・リーグ名・リーグID で、見出しで読む（`league_maps()`）。`generate_ouka_leagues.py` の選手候補も「桜花」タブに1行でもある在籍者で判定する（旧「桜花最高」が空でない人と同じ） |
| 33 | docs/notes/static-generation.md:307 | それまではログインせずに gviz・htmlview・CSV エクスポートで読んでいた。鍵の用意と、専用フォルダ「【本番環境】」の非公開化の段取りは `docs/notes/sheets-reader.md`。 |
| 34 | docs/notes/static-generation.md:309 | - **gviz の返し方をそのまま再現する**（`_select()`。生成物を変えないため。2026-10-21 に全タブで gviz と CSV エクスポートを突き合わせて規則を確かめ、全ページの新旧の生成の差分0でマージした）: |
| 35 | docs/notes/static-generation.md:311 | 日付は表示の文字で返す（gviz は `Date(y,m,d)` だったが、日付の列は `formatted=True` でしか読んでいない） |
| 36 | docs/notes/static-generation.md:320 | gviz はフィルタで隠れた行・手で非表示にした行・折りたたんだグループの行を返さなかった（2026-09-21、「連盟プロ以外」が732行のうち460行しか読めず、 |
| 37 | docs/notes/static-generation.md:354 | - **無いタブ名は止める。** `lib/sheets.py` は Sheets API のタブの一覧（`check_sheet_exists()`）に無いタブ名なら止める（gviz は先頭のタブを黙って返していた、CHAT-0916-LV-11）。 |
| 38 | docs/notes/static-generation.md:454 | - `check_image_links.py` — `jpml_pros.html` 内の画像URL全件にHEADリクエストを送りリンク切れを検知（毎日 04:30 JST、保険で毎週月曜03:00 JST） |
| 39 | docs/notes/title-pages.md:108 | - gviz は存在しないタブ名でも先頭のタブ（今は「リーグ」）を返す。生成はタブの一覧（`htmlview`）に無いタブ名を止めるので、旧名「プロ以外」で読むと止まる（`lib/sheets.check_sheet_exists()`、2026-10-13） |
| 40 | docs/notes/title-pages.md:130 | - 止める: 4タブの見出しの不一致（「タイトル」「連盟プロ以外」は必要な見出しの欠け・重複。gviz は存在しないタブ名でも先頭のタブを返すため）、置換文字（U+FFFD）・制御文字、slug の重複・不正・空欄（記録の無い大会の空欄だけは警告）、 |
| 41 | docs/notes/title-pages.md:159 | - **タブ名を変えるときは、読み込み側（`generate_title_pages.py` のタブ名の定数）を先に直す。** gviz は存在しないタブ名でも先頭のタブを黙って返すため、 |
| 42 | docs/notes/video-wayhome.md:91 | - 平野さんが「帰り道」「Mつく」「女流勉強会」のタブを1つのブック（`lib/video_book.py` の `SPREADSHEET_ID`。「リンクを知っている全員」の閲覧者、サービスアカウントは編集者）にまとめた。 |
| 43 | docs/notes/yotei-sheet.md:83 | カレンダーの読み書きは `GCP_SA_KEY`（カレンダー用のサービスアカウント。平野さんが「予定の変更」で共有済み）。スプレッドシートは gviz で読む。 |
| 44 | docs/notes/yotei-sheet.md:143 | 3. 「リンクを知っている全員が閲覧可」は今のまま（読むだけなら共有が無くても読める） |

### マージ

- 変更は docs/notes/abema-book.md・docs/decisions/saikyo.md・docs/logs のみ（指示の「マージ:」どおり cloudflare へ入れる）
- 再fetch・`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1023-skv:cloudflare`（2b6e0de9f..64e0c9ebe、fast-forward）

## 報告

- 状態: 判断待ち / 続き: CHAT-1023-SKV-05
- ブランチ: work/1023-skv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-SKV-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skv
- 確認用URL: なし
- マージ: 済（64e0c9ebe）
- issue: なし
- 判断が必要なこと:
  - docs/notes のほかの文書に同じ種類の記述が44件ある（`## 経過`「手順3」の表）。今の仕組みとして古い見込みが強いのは sns-book.md・live-channel-write.md・live-page-design.md・saikyo-page-design.md・video-wayhome.md・yotei-sheet.md・static-generation.md・scheduler-worker.md の15件。別の指示で直すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5bb0e235）: https://github.com/retroeater/mj-logs/tree/main/guide/5bb0e235

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bb0e235/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
