# CHAT-1021-PRV-05

- 着手日時: 2026-10-21
- 対象issue: #557
- ブランチ: work/1021-prv
- 着手時HEAD: ae0f84931

## 指示

【Claude作成】Claude Code 向け指示：専用フォルダへ移した全ブックが、ログインなしと sheets-reader の両方で読めるかを確かめる（#557） Chat-Ref: CHAT-1021-PRV-05 マージ: 承認済み（チャットで）。変わるのが PRV-04 で入れた手動実行のワークフロー（check-sheets-reader 等）と、それが呼ぶスクリプト・テスト・docs（docs/decisions/・docs/logs/ を含む）だけで、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも（なるべく早く。毎朝 04:00 JST の定期実行より前） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1021-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1021-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1021-PRV-04（#557）の続き。平野さんが本番のブックを専用フォルダへ移した。今の生成（ログインなしの gviz・htmlview・CSV）が止まっていないこと、切り替えの後に使う sheets-reader（Sheets API）でも全ブックを読めること、非公開のはずのブックが公開になっていないことを、今日のうちに確かめる。`lib/sheets.py` の置き換えはこの指示ではしない。
決定（2026-10-21、平野さん）

* 本番のブックを sheets-reader に共有する方法は、フォルダごと。ただし「Public」「Private」ではなく専用のフォルダを用意する
* 平野さんは専用フォルダ「【本番環境】」を用意し、一般的なアクセスを「リンクを知っている全員（閲覧者）」、`sheets-reader@…` を閲覧者にした（平野さんの共有画面で確かめた）。すべてのブックをこのフォルダへ移した（平野さん、2026-10-21 09:58 に完了の連絡）
* 切り替えの進め方は「一括＋差分0」: `lib/sheets.py` の中身を Sheets API に替え、全ページの再生成で差分0を確かめてマージする。最後に専用フォルダの一般的なアクセスを「制限付き」にして、中のブックをまとめて非公開にする
* PRV-04 の手動実行のワークフローを、コードが読む全ブックを対象に広げて cloudflare へマージしてよい（下の「止まる条件」に当たらないときに限る）

前提（チャット側。平野さんの決定ではない）

* 移したブックの一覧はチャット側は見ていない（要確認）。PRV-01 の表の 1〜15（コードの ID の定数）が対象の見込み。16 の連盟員名簿は平野さんの持ち物ではないので移していない見込み
* チャット側が 2026-10-21 の移動の後にログインなしで「R_その他」の「辞書」を gviz で読み、行が返ることだけは確かめた（1冊だけ）
* 「ryoei.pro_AIボット」（`ASK_SHEET_ID`、PRV-01 の表の 17）は「制限付き」だった。 「すべてのブック」に含まれて「リンクを知っている全員」の専用フォルダへ移っていれば、フォルダの公開リンクを受け継いで、ログインなしで読める状態になっている可能性がある。検証用の「R_検証_sheets-reader」（`check-sheets-reader` の定数）も同じ（要確認）
* 出すのはタブ名・行数・列数と、要求の成否（HTTP の状態・エラーの理由）だけ。セルの値・見出しの文字・メールアドレス・プロジェクト ID は出さない（ジョブのログと作業ログは public の mj-logs に写るため）

手順

1. PRV-04 のログ（docs/logs/CHAT-1021-PRV-04.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1021-PRV-05` を足す。#557 に着手中のコメントを残す。上の決定を docs/decisions/ の PRV の節に足し、docs/notes/sheets-reader.md「この後の段取り」を、専用フォルダ（「【本番環境】」）を使う今の段取り（移動は済み → Sheets API への一括の切り替え → フォルダを「制限付き」）に直す。
2. `check-sheets-reader` の対象を、コードが ID を持つ全ブック（PRV-01 の表の 1〜16 と、`ASK_SHEET_ID`・検証用のシート）に広げる（ID の洗い出しは PRV-01 の調べ方を使い、一覧をログに書く）。各ブックについて「ログインなしの gviz」「ログインなしの htmlview」「sheets-reader の spreadsheets.get」の成否と、タブの数・各タブの行数・列数を、ブックの題（定数名）ごとに実行サマリーへ表で出す。テストを回し、cloudflare へマージする。
3. マージ後にワークフローを1回起動し、結果をログの `## 経過` に表で全件書く（略さない）。#557 に結果をコメントする（このログの `## 経過` の表から引用する）。

止まる条件

* 差分に、`check-sheets-reader` のワークフロー・それが呼ぶスクリプト・テスト・docs 以外の変更が含まれる。既存の生成・`lib/sheets.py` を変える必要が出た
* 実行サマリー・ジョブのログにセルの値・見出しの文字・メールアドレス・プロジェクト ID が出る作りになっている（出さない形に直してから進める）
* 共有の設定を変える必要が出た（Code は共有を変えない。結果を書いて止まる）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ節を変えている、または取り込みで衝突する。表・追記の続く文書（github-actions.md の表、decisions）で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら、原因を報告に書いたうえで手順3へ進めてよい）
* 手順3の結果は、どれが読めなくても止まらずに全件を書き切る（一時的な接続の失敗は、失敗したジョブだけを1回再実行してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」の先頭に、次のどちらかがあれば平野さんの手作業として書く: (1) 生成が読むブックのうち、ログインなしで読めないもの（今の生成が止まる。フォルダか共有の見直しが要る） (2) 「ryoei.pro_AIボット」・検証用のシートがログインなしで読めるもの（専用フォルダの外へ戻すなどの対処が要る）。続けて、`lib/sheets.py` の一括の切り替えに進む前に平野さんが決めることがあれば書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1021-PRV-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1021-PRV-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1021-PRV-05` のコミットは 0 件。`PRV` は同じセッションの続き
- 作業ブランチ: ローカル・リモートの `work/1021-prv` はどちらも cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で進めた
- 0. 「指示」欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1

- #557 のコメントは PRV 系列と SEC-04 の注意だけで、他のセッションの着手中のコメントは無い。着手中のコメントを残した（issuecomment-6305645412）
- PRV-04 のログの状態を「判断待ち / 続き: CHAT-1021-PRV-05」にした
- 決定を `docs/decisions/security.md`「2026-10-21（CHAT-1021-PRV-05）」に足した
- `docs/notes/sheets-reader.md`「この後の段取り」を専用フォルダの段取り（移動は済み → Sheets API への一括の切り替え → フォルダを「制限付き」）に書き直した。「Public」フォルダの節は、平野さんの画面で見た記録として残し、段取りの節に「置き換わった」と書いた

### 手順2: ID の洗い出し（PRV-01 の調べ方）

`docs/`・`tests` を除く `*.py`・`*.js`・`*.mjs`・`*.jsonc` から44文字の ID を拾った（前後に英数字が続かないもの。`lib/live_calendar.py` のカレンダーの ID は外れる）。**17冊**:

| # | 題（PRV-01 の題） | ID の定数（読む元のファイル） | 同じ値を持つ所 |
|---|---|---|---|
| 1 | R_タイトル戦_成績 | `houou_tab.SPREADSHEET_ID` | `lib/houou_race.py`・`lib/pro_stats.py`・`generate_saikyo_pages.py`・`generate_video_live.py` |
| 2 | R_タイトル戦_マスタ | `live.SPREADSHEET_ID` | |
| 3 | R_選手 | `pro_sheet.SPREADSHEET_ID` | |
| 4 | R_選手_SNS | `sns_book.SPREADSHEET_ID` | |
| 5 | R_連盟_YouTube | `live_layer3.SPREADSHEET_ID` | |
| 6 | R_連盟_予定表 | `yotei.SPREADSHEET_ID` | |
| 7 | R_ABEMA_最強戦 | `abema_book.SPREADSHEET_ID` | |
| 8 | R_番組 | `video_book.SPREADSHEET_ID` | |
| 9 | R_その他 | `other_book.SPREADSHEET_ID` | |
| 10 | R_Ampai | `ampai.SPREADSHEET_ID` | |
| 11 | R_ログ | `fetch_x_posts.SPREADSHEET_ID` | `generate_resource_logs.LAYER_SPREADSHEET_ID` |
| 12 | mj_ryoei | `generate_rh_results.SPREADSHEET_ID` | `generate_rh_paifu.py`・`generate_rh_results_detail.py` |
| 13 | mj_saikyo_mens | `generate_saikyo_mens.SPREADSHEET_ID` | |
| 14 | 20210430_「孤立牌」と「1メンツ+1」の比較 | `generate_resource_efficiency.SPREADSHEET_ID` | |
| 15 | 20261016_連盟員名簿0.75（連盟のもの） | `meibo.SPREADSHEET_ID` | |
| 16 | ryoei.pro_AIボット | `ASK_SHEET_ID`（`workers/ask/wrangler.jsonc`） | |
| 17 | R_検証_sheets-reader | `check_sheets_reader.VERIFY_ONLY_SPREADSHEET_ID` | |

- PRV-01 の表との違い: 12 の **mj_resource は、コードに ID が無くなった**（`jpml_test.html` は /exam/ へ移り〈3fb048f0b〉、`video_en.html` は廃止〈7cea96680〉、`generate_resource_logs.py` は「ログ」を R_ログから読む形になった〈f9da0388c〉）。対象から外した
- ブラウザの JS からも ID が無くなった（Google Charts のページは `ouka_ranking`・`ouka_results`・`wrc_ranking`・`wrc_results` とも `_redirects` で ouka/・jwrc/ へ 301 済み。`google.visualization` を使うページは残っていない）

### 手順2: 作り

- `scripts/check_sheets_reader.py`: 対象を `BOOKS`（題・ID の定数のあるファイル・定数名・ログインなしで読めるはずか）に広げた。ID は各ファイルの定数を `ast`（`.jsonc` は正規表現）で読む（生成のスクリプトを import すると重い依存を読むため）。
  各ブックで、ログインなしの gviz（`SELECT * LIMIT 0`、`setResponse` と `"status":"ok"` があれば読める）・htmlview（タブの一覧の定義 `items.push({name:` があれば読める）・sheets-reader の `spreadsheets.get`（題・タブ・グリッド）と各タブの `values.get`（行数・列数を数えるだけ）を呼ぶ。`values.get` の間を 1.1 秒あける（読み取りの上限に掛からないため）
- 判定の列: 生成が読むブック（1〜15）は「ログインなしで gviz・htmlview とも読める」、非公開のはずのブック（16・17）は「どちらも読めない」が期待。外れたら「要対処: …」と書く。sheets-reader で読めなければその旨
- 結果にかかわらず成功で終わる（全件を書き切るため。認証の情報を読めないときだけ失敗）。ワークフローの `timeout-minutes` を 10 に上げた
- 題が今の題（API の `properties.title`）と違えば「（今の題: …）」を添える（題は値ではないので出す）
- テスト（`scripts/tests/test_check_sheets_reader.py`）: コードの ID の集合と `BOOKS` の集合が一致すること（ブックを足して `BOOKS` に足し忘れたら落ちる。mj_resource を残したままでは定数が読めず落ちることを確かめた）、定数の読み取り、非公開のはずのブックが2冊であること、判定、gviz・htmlview の見分け。`python3 -m unittest discover -s scripts/tests` は通った
- 事前の確かめ（コンテナから、ログインなしの部分だけ。値は読んでいない）: 1〜15 は gviz・htmlview とも読める。**16「ryoei.pro_AIボット」がログインなしで gviz・htmlview とも読める**。17 の検証用は HTTP 401
- 取り込みとの重なり: 未マージの work/ ブランチとの `git merge-tree` で、この作業による新しい衝突は無い（`work/1017-lvp` は cloudflare との間ですでに衝突している、この作業と無関係）


### マージと手順3

- 作業ブランチの先頭 f511430f1 の check-run: 「Workers Builds: mj」success・「check」success
- マージの前に `origin/cloudflare` が進んでいた（CHAT-1021-SEC-19）。`git merge origin/cloudflare` で `docs/decisions/security.md` の末尾への追記どうしが衝突し、両方を残して日付の順に解いた（CLAUDE.md「ブランチ運用」）。解いた後の該当箇所:

  ```
  ## 2026-10-21（CHAT-1021-SEC-19）

  - mj の Actions の Secret `MJ_LOGS_TOKEN` と GitHub の fine-grained PAT `mj-logs-sync` を削除…
  - 作業中の回答: なし

  ## 2026-10-21（CHAT-1021-PRV-05）

  - 本番のブックを sheets-reader に共有する方法は、フォルダごと。…
  ```
- テストは取り込み後も通った。`git push origin work/1021-prv:cloudflare`（be1733f8d、fast-forward）。差分は `check-sheets-reader` のワークフロー・スクリプト・テストと docs だけ
- `actions_run_trigger`（ref `cloudflare`、入力なし）で起動: run 39527526499（job 118985583553）、success。ジョブのログにセルの値・見出し・メールアドレス・プロジェクト ID が出ていないことを確かめた

#### ブックごと（全17冊）

| # | 題（定数） | ログインなし gviz | ログインなし htmlview | sheets-reader spreadsheets.get | タブの数 | 判定 |
|---|---|---|---|---|---|---|
| 1 | R_タイトル戦_成績（`houou_tab.SPREADSHEET_ID`） | 読める | 読める | 成功 | 6 | 期待どおり |
| 2 | R_タイトル戦_マスタ（`live.SPREADSHEET_ID`） | 読める | 読める | 成功 | 4 | 期待どおり |
| 3 | R_選手（`pro_sheet.SPREADSHEET_ID`） | 読める | 読める | 成功 | 3 | 期待どおり |
| 4 | R_選手_SNS（`sns_book.SPREADSHEET_ID`） | 読める | 読める | 成功 | 4 | 期待どおり |
| 5 | R_連盟_YouTube（`live_layer3.SPREADSHEET_ID`） | 読める | 読める | 成功 | 5 | 期待どおり |
| 6 | R_連盟_予定表（`yotei.SPREADSHEET_ID`） | 読める | 読める | 成功 | 4 | 期待どおり |
| 7 | R_ABEMA_最強戦（`abema_book.SPREADSHEET_ID`） | 読める | 読める | 成功 | 3 | 期待どおり |
| 8 | R_番組（`video_book.SPREADSHEET_ID`） | 読める | 読める | 成功 | 4 | 期待どおり |
| 9 | R_その他（今の題: R_辞書・書籍）（`other_book.SPREADSHEET_ID`） | 読める | 読める | 成功 | 2 | 期待どおり |
| 10 | R_Ampai（`ampai.SPREADSHEET_ID`） | 読める | 読める | 成功 | 1 | 期待どおり |
| 11 | R_ログ（`fetch_x_posts.SPREADSHEET_ID`） | 読める | 読める | 成功 | 5 | 期待どおり |
| 12 | mj_ryoei（`generate_rh_results.SPREADSHEET_ID`） | 読める | 読める | 成功 | 3 | 期待どおり |
| 13 | mj_saikyo_mens（`generate_saikyo_mens.SPREADSHEET_ID`） | 読める | 読める | 成功 | 3 | 期待どおり |
| 14 | 20210430_「孤立牌」と「1メンツ+1」の比較（`generate_resource_efficiency.SPREADSHEET_ID`） | 読める | 読める | 成功 | 2 | 期待どおり |
| 15 | 20261016_連盟員名簿0.75（連盟のもの）（`meibo.SPREADSHEET_ID`） | 読める | 読める | 成功 | 5 | 期待どおり |
| 16 | ryoei.pro_AIボット（今の題: R_AIボット）（`wrangler.ASK_SHEET_ID`） | 読める | 読める | 成功 | 1 | **要対処: ログインなしで読める（非公開のはず）** |
| 17 | R_検証_sheets-reader（`check_sheets_reader.VERIFY_ONLY_SPREADSHEET_ID`） | 読めない（HTTP 401） | 読めない（HTTP 401） | 成功 | 1 | 期待どおり |

#### タブごと（sheets-reader。グリッドの行数・列数 → values.get で取れた行数・列数）

| # | タブ |
|---|---|
| 1 | 対局（2339・10 → 2339行・10列）／鳳凰（16012・24 → 16012行・24列）／桜花（1720・18 → 1720行・18列）／JWRC（1678・12 → 1678行・12列）／WRCR（1000・12 → 573行・12列）／最強戦（2745・12 → 2745行・12列） |
| 2 | リーグ（21・3 → 21行・3列）／タイトル（3111・9 → 3111行・9列）／タイトル戦（32・5 → 32行・5列）／Ampai（44・7 → 44行・7列） |
| 3 | プロ（1099・4 → 1099行・4列）／連盟プロ以外（756・4 → 756行・4列）／別名（34・4 → 34行・4列） |
| 4 | 【1】元データ（1865・3 → 1854行・3列）／【2】ID（2345・8 → 1855行・8列）／【3】画像取得（2365・15 → 1855行・15列）／【4】手動補正（1000・16 → 1行・16列） |
| 5 | 【1】元データ（14605・15 → 14213行・15列）／【2】自動変換後（14605・26 → 14213行・25列）／【3】手動補正（4839・21 → 4339行・21列）／シート16（5335・26 → 4334行・4列）／【4】カレンダー非掲載（1000・3 → 10行・3列） |
| 6 | 【1】元データ（1633・26 → 1133行・12列）／【2】自動変換後（1633・24 → 1133行・12列）／【3】手動補正（2132・8 → 1133行・8列）／【3】予定ID列の記録（1000・26 → 355行・6列） |
| 7 | 【1】元データ（677・14 → 677行・14列）／【2】最強戦（1212・15 → 713行・13列）／【3】手動補正（7・10 → 7行・10列） |
| 8 | Mつく（62・14 → 62行・14列）／帰り道（43・4 → 43行・4列）／女流勉強会（41・5 → 41行・5列）／プロテスト（13・3 → 13行・3列） |
| 9 | 辞書（792・5 → 792行・5列）／書籍（96・10 → 96行・10列） |
| 10 | 成績表（44・7 → 44行・7列） |
| 11 | 【1】元データ（5283・13 → 3419行・13列）／【2】自動変換後（5077・20 → 2732行・20列）／【3】手動補正（6250・20 → 3141行・20列）／比較（1091・26 → 91行・23列）／ログ（2709・11 → 2709行・11列） |
| 12 | 牌譜（58・12 → 58行・12列）／成績（13・6 → 13行・6列）／成績詳細（322・28 → 322行・28列） |
| 13 | 選手（91・12 → 91行・11列）／2026-1（143・10 → 143行・10列）／2025（187・10 → 187行・10列） |
| 14 | シート1（61・5 → 61行・5列）／詳細（295・6 → 295行・6列） |
| 15 | 公開（2000・6 → 1099行・6列）／【4】変換後（2000・6 → 1099行・6列）／【3】変換表（11・2 → 11行・2列）／【2】値貼付（2000・11 → 1099行・11列）／【1】0.75（2000・11 → 1099行・11列） |
| 16 | シート1（1141・4 → 141行・4列） |
| 17 | シート1（1000・26 → 6行・2列） |

- 1〜15（生成・検査が読むブック）は、ログインなしの gviz・htmlview とも読める。**今の生成は止まっていない**
- 1〜17 のすべてを sheets-reader で読めた（`spreadsheets.get` と全タブの `values.get` が成功）。切り替えの後の読み手として足りる
- **16「R_AIボット」（質問文が入るブック）がログインなしで読める。** sheets-reader でも読めることから、専用フォルダ「【本番環境】」へ移って、フォルダの「リンクを知っている全員」と sheets-reader の閲覧者を受け継いだと見られる（フォルダの中身は Code からは見えない。平野さんの Drive で確かめる）
- 17 の検証用は、ログインなしで読めず sheets-reader で読める（専用フォルダには入っていない見込み）
- 15 の名簿が専用フォルダに入ったかは、この結果からは分からない（もとから「リンクを知っている全員」で、sheets-reader は共有なしでも読めるため）
- 題が今の題と違うもの: 9 は「R_辞書・書籍」、16 は「R_AIボット」（どちらもコードのコメントの名前とも違う。2026-10-18 の決定のとおり、切り替えの作業でコメントを直す）

## 報告

- 状態: 判断待ち / 続き: CHAT-1021-PRV-06
- ブランチ: work/1021-prv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1021-PRV-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1021-prv
- 確認用URL: なし
- マージ: 済（be1733f8d、fast-forward。承認済み・check-sheets-reader のワークフロー・スクリプト・テストと docs だけ）
- issue: #557（着手中のコメント issuecomment-6305645412・結果のコメント issuecomment-6305784473）
- 判断が必要なこと:
  - **(2) 平野さんの手作業（急ぎ）: 「R_AIボット」（`ASK_SHEET_ID`、質問文が入るブック）がログインなしで読める（gviz・htmlview とも。run 39527526499）。** 専用フォルダ「【本番環境】」へ移ったためと見られる。フォルダの外（「制限付き」の場所）へ戻し、共有の画面で一般的なアクセスが「制限付き」に戻り、`mj-ask` が編集者のまま残っていることを確かめる。済んだら `check-sheets-reader.yml` を「Run workflow」で起動すれば、16 の判定が「期待どおり」になるかで確かめられる（sheets-reader は 16 を読む必要が無いので、16 の sheets-reader が失敗になっても問題ない）
  - (1) 生成が読むブックでログインなしで読めないもの: なし（1〜15 とも読める）
  - 一括の切り替え（`lib/sheets.py` を Sheets API に替える）に進む前に:
    - 名簿（15）を専用フォルダに入れたか（入れていれば、最後にフォルダを「制限付き」にしたとき名簿も非公開になる。名簿は対象外の決定なので、入れていれば外す）
    - 「R_AIボット」を「制限付き」に戻すと、sheets-reader は読めなくなる。`check-sheets-reader` の 16 の期待を「ログインなしで読めない」だけにする（sheets-reader の失敗を許す）かどうか。推奨: 許す形にする（Code が次の指示で直す）
- 未確認の項目:
  - 専用フォルダ「【本番環境】」に入ったブックの一覧（Code からは見えない。平野さんの Drive で確かめる。結果からは 16 が入り、17 が入っていない見込み、15 は分からない）
  - 「R_AIボット」がログインなしで読めた間に、外から読まれたか（Code からは確かめられない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 035e5984）: https://github.com/retroeater/mj-logs/tree/main/guide/035e5984

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/035e5984/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/495cef69.md
