# CHAT-1018-PRV-01

- 着手日時: 2026-10-18
- 対象issue: #557
- ブランチ: work/1018-prv
- 着手時HEAD: 0ae083e44

## 指示

【Claude作成】Claude Code 向け指示：スプレッドシートを非公開にする前の調べ（#557）。読み手の洗い出しと、認証付きの読み取り・Google Charts のページの案 Chat-Ref: CHAT-1018-PRV-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ryoei.pro が読むスプレッドシートを、すべて非公開（一般的なアクセスを「制限付き」）にしたい（#557）。非公開にすると、ログインせずに gviz・CSV エクスポートで読む今の生成と、ブラウザから直接シートを読むページが止まる。切り替えの前に、何がどのスプレッドシートを読んでいるかを全部洗い出し、認証付きで読む形と Google Charts のページの扱いの案を出す。この指示は調べだけで、コード・シート・共有の設定は変えない。
決定（2026-10-18、平野さん）

* ryoei.pro のスプレッドシートは、すべて非公開にして、非公開のフォルダへ移す（#557。2026-10-13 の CHAT-1013-AIC-05「基本は非公開のフォルダに置く。gviz の公開読み取りの扱いもあわせて決める」の続き）
* 非公開への切り替えで開発の進め方に問題が出る点は、先に手当てする。その調べをこの指示で始める

前提（チャット側。平野さんの決定ではない）

* 平野さんは、仕組みの切り替えが済むまで一般的なアクセスを変えない（チャット側からお願いした。要確認ではなく、この調べの間は今の共有のままとして扱う）
* 生成は `scripts/lib/sheets.py` からログインせずに gviz と CSV エクスポートで読む（docs/notes/static-generation.md）。「Google Charts 依存」のページが6つあり、ブラウザから直接シートを読む（同じ文書の表、#7）（数・名前は要確認）
* サービスアカウントは `live-channel-writer`・`birthday-calendar`・`gsc-export`・`mj-ask` などがある（docs/notes/ask-worker.md・live-channel-write.md・birthday-calendar.md）。鍵は GitHub Actions のシークレットにだけあり、コンテナには無い（docs/notes/cloud-sessions.md「鍵」）。「ryoei.pro_AIボット」はすでに「制限付き」で、Worker がサービスアカウントで読み書きしている
* 移行先のフォルダはチャット側も Code も読めない。どのスプレッドシートがどのフォルダにあるかは、平野さんが Drive で照合する前提で、こちらは題と ID 定数名の一覧を出す
* シートの ID・URL はログに書かない（CLAUDE.md「作業ログ」節）。定数名（`live.SPREADSHEET_ID` など）と題で呼ぶ

手順

1. 同じ主題の issue（#557 を含む。クローズ済みも）の本文・コメントを読み、決まっていることをログに書く。#557 に着手中のコメントを残す（本文・状態・ラベルは変えない）。未マージの work/ ブランチで `scripts/lib/sheets.py`・シートの ID の定数を変えているものを確かめて書く（`git branch -r --no-merged origin/cloudflare`）。
2. 読み手の洗い出し: scripts/・.github/workflows/・workers/・ブラウザで動く JS（ルートの `*.js`・`assets/` など）から、Google のスプレッドシートを読む・書く箇所を全部洗い出し、表にする（列: スプレッドシートの題〈htmlview の title で引く〉・ID の定数名・シート名・読む経路〈gviz／CSV エクスポート／htmlview／Sheets API／ブラウザの google.visualization〉・認証の有無とアカウント・呼び出し元〈スクリプト・ワークフロー・Worker・ページ〉・頻度〈定期実行・push・手動〉）。名簿（`lib/meibo.py`）など平野さんのものでないかもしれないスプレッドシートは、持ち主が分からない印を付ける。数は名前の一覧と一緒に書く（略さない）。
3. 案を出す（実装はしない）:
   * (a) 生成を認証付きで読む形。gviz・CSV エクスポートがサービスアカウントのアクセストークンで読めるか（Google の公式の文書で確かめ、出典を書く）、Sheets API に替える場合との違い（gviz の SELECT 構文・フィルタの検知 `check_not_filtered()`・数の型の扱いへの影響）。どのサービスアカウントを使うか（今のものを使うか、読み取り専用を新しく作るか）の案と、メリット・デメリット
   * (b) Google Charts 依存のページ（ブラウザから読むもの）を1つずつ、静的な生成に作り直すか・新しいページ（houou/ など）への転送で廃止するかの案
   * (c) 開発の進め方への影響: コンテナで直接シートを読めなくなった後、`--check`・新旧の生成物の比較・シートの照合をどう行うか（ワークフローの dry-run を足す等）の案。チャット側の読み取りへの影響も書く
   * (d) 切り替えの順番の案（1ファイルずつ非公開にして確かめる等）と、各段で止める条件 結果の要点を #557 にコメントする（このログの `## 報告` と `## 経過` の表から引用する）。

止まる条件

* #557 が閉じている、または同じ主題の open issue・未マージの work/ ブランチがあり、作業が重なる（重なるものを書いて止まる）
* 他のセッションの着手中のコメントが #557 にある
* 手順2で、読むたびに結果が変わる（シートの編集中の可能性）
* 調べの途中で、コード・シート・共有の設定を変えないと確かめられないことが出た（変えずに、確かめ方の案を「判断が必要なこと」に書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順3の (a)〜(d) で平野さんが選ぶことを、選択肢ごとのメリット・デメリットと推奨を添えて書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-PRV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-PRV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`--unshallow` 後）: `CHAT-1018-PRV-01` のコミット・`PRV` の識別子のコミット・`docs/logs/CHAT-*-PRV-*.md` の履歴ともに 0 件
- 作業ブランチ: ローカル・リモートとも `work/1018-prv` が無いため `git checkout -b work/1018-prv origin/cloudflare` で作成
- 0. 「指示」欄の末尾の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1: 同じ主題の issue・未マージのブランチ

- #557: open、コメント 0 件（着手中のコメントも無し）。ラベル「分野: セキュリティ」「分野: データ」。決まっていること（本文）:
  - 今はほぼすべてのブックが「リンクを知っている全員（閲覧者）」。平野さんの方針は「基本は非公開のフォルダ」（2026-10-12〜13）
  - 「ryoei.pro_AIボット」は 2026-10-13 に「制限付き」へ直した（#542）
  - 決める必要があること: どのブックを非公開にするか／生成の読み取りの経路／非公開のフォルダと新しいブックの作り方
  - 読み取りの経路の案（本文では未調査）: A. サービスアカウントで Sheets API／B. 生成が読むブックだけ公開／C. 公開用の別ブックに写す
  - 今回の指示の「決定」（すべて非公開にして非公開のフォルダへ移す）で、B・C は選ばない前提になった
- #557 に着手中のコメントを残した（issuecomment-6249172243）。本文・状態・ラベルは変えていない
- 同じ主題の検索（クローズ済みを含む）: #557 のほかに #467・#510（gviz の挙動の調べ、closed）だけで、重なる open issue は無い
- Google Charts のページの行き先の issue（重なりではなく、手順3 (b) の材料）: #591（ouka/ を公開、旧3ページの転送を含む、open）・#599（jwrc/ を公開、旧2ページの転送を含む、open）・#7（Google Charts 依存の解消、open）・#266（6ページの説明段落、open）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`、9本）: `work/1009-swp-526`・`work/1011-swp-hnd`・`work/1011-swp-nav`・`work/1015-skd-notes`・`work/1017-lvp`・`work/1018-log-367-migrate`・`work/1018-prv`（この作業）・`work/OUK`・`work/WRC`
  - `scripts/lib/sheets.py` を変えているもの: 無し
  - シートの ID の定数（`SPREADSHEET_ID = …` の定義）を変えているもの: 無し
  - 読み手を増やすもの（定数は既存のまま）: `work/OUK`（ouka/、「R_タイトル戦_成績」の「桜花」タブを houou/ の部品で読む）・`work/WRC`（jwrc/、同じブックの「JWRC」タブ）・`work/1018-log-367-migrate`（「R_ログ」の層3へ Sheets API で書く mode を足す）。いずれもこの調べ（コードを変えない）とは重ならない。認証付きへ切り替えるときは、これらのマージ後のコードが対象になる

### 手順2: 読み手の洗い出し

調べ方: 全ファイル（`docs/` と `scripts/tests/` を除く）から44文字のブックの ID を拾い（33か所・18種）、各 ID の `htmlview` の title とタブの一覧を、ログインせずに読んだ（2回読んで同じ結果。読むたびに変わることは無かった）。
18種のうち1つ（`lib/live_calendar.py`）はカレンダーの ID の一部で、ブックではない（htmlview は 404）。`data/youtube_channels.json`・`jpml_pros.html` の英数字は YouTube の ID で、ブックではない。呼び出し元は import のたどりと `fetch_*`／`sheets_write` の呼び出しの grep で引いた。

**ブックは17冊**（ログインせずに読めるもの16冊＋すでに「制限付き」の1冊）。題は htmlview の title（「 - Google Drive」を除く）。
**題が今は「R_…」になっていて、コードのコメントの「ryoei.pro_選手」などと違う**（平野さんが題を付け替えたと見られる。ID は同じ）。

経路の略: gviz＝`/gviz/tq`（`lib/sheets.py` の `_query()`）、htmlview＝タブの gid の一覧（`_sheet_gids()`）、CSV＝`/export?format=csv&gid=`（`check_not_filtered()`・`fetch_csv()`）、API＝Sheets API v4（`lib/sheets_write.py`・Worker）、Charts＝ブラウザの `google.visualization.Query`。
頻度の略: 毎日＝`run-daily.yml`（Worker `mj-scheduler` が 04:00 JST に起動）の中の各本、週次 all＝`fetch-youtube.yml`（月曜 05:37 JST）が呼ぶ `regenerate-page.yml` の all、push＝`cloudflare` への push で変わったページの再生成。

| # | 題 | ID の定数名（同じ値の別名） | 読む・書くタブ | 読む経路 | 認証 | 書くもの | 呼び出し元 | 頻度 |
|---|---|---|---|---|---|---|---|---|
| 1 | R_タイトル戦_成績 | `houou_tab.SPREADSHEET_ID`（`pro_stats.SPREADSHEET_ID`・`generate_houou_race`／`generate_houou_leagues`／`generate_ouka_leagues`／`generate_saikyo_pages`／`generate_video_live` の `SPREADSHEET_ID`、ブラウザの JS 4本に直書き） | 対局・鳳凰・桜花・JWRC・最強戦（WRCR は読む所が無い） | gviz・htmlview・CSV、**Charts** | なし | なし | 生成: houou_pages・houou_race・houou_leagues・ouka_leagues・saikyo_pages・video_live・jpml_pros・ask_data。検査: check_ampai・check_saikyo_unregistered・check_unused_names・check_leagues_dropped。ページ: `ouka_ranking`・`ouka_results`・`wrc_ranking`・`wrc_results`（＋301済みの `houou_ranking`・`houou_results`） | 毎日（saikyo_pages）・週次 all・push・手動。Charts は閲覧のたび |
| 2 | R_タイトル戦_マスタ | `live.SPREADSHEET_ID`（`pro_stats.LEAGUE_SPREADSHEET_ID`・`generate_title_pages.SPREADSHEET_ID`） | リーグ・タイトル・タイトル戦 | gviz・htmlview・CSV。**`generate_title_pages.fetch_tab()` は `lib/sheets.py` を通さない独自の gviz** | なし | なし | live_pages・title_pages・jpml_pros・houou 系・check_ampai・check_unused_names | 毎日（update-live-channel の後の live_pages・title_pages）・週次 all・push |
| 3 | R_選手 | `pro_sheet.SPREADSHEET_ID`（`live.PLAYERS_SPREADSHEET_ID`） | プロ・連盟プロ以外・別名 | gviz・htmlview・CSV | なし | なし | ほぼ全ページの生成、update_sns_book・check_meibo・sync_birthday_calendar・sync_dojo_calendar・fetch_* ほか | 毎日・週次・push |
| 4 | R_選手_SNS | `sns_book.SPREADSHEET_ID` | 【1】元データ・【2】ID・【3】画像取得・【4】手動補正 | gviz・htmlview・CSV（`lib/sns.py`）、API（読み書き） | 書きは `live-channel-writer`（`LIVE_SHEETS_SA_KEY`） | `update_sns_book.py` が【1】〜【3】 | 生成（SNS の ID・画像）、update-sns-book、check-image-links | 毎日 |
| 5 | R_連盟_YouTube | `live_layer3.SPREADSHEET_ID`（`write_live_layer1`／`write_live_channel_candidate` の `SPREADSHEET_ID`） | 【1】元データ・【2】自動変換後・【3】手動補正・【4】カレンダー非掲載 | gviz・htmlview・CSV、API | 書きは `live-channel-writer` | 【1】【2】を書き、【3】に追記 | update-live-channel（write_live_layer1・write_live_channel_candidate・append_live_layer3_candidates）、live_pages・title_pages・sync_live_calendar・check_unused_names | 毎日 |
| 6 | R_連盟_予定表 | `yotei.SPREADSHEET_ID` | 【1】元データ・【2】自動変換後・【3】手動補正 | gviz（sync_live_calendar）、API（write_yotei_sheet） | 書きは `live-channel-writer`（カレンダーへの書き込みは別の `GCP_SA_KEY`） | 【1】【2】を書き、【3】を更新・削除・追記 | sync-yotei（update-live-channel から） | 毎日 |
| 7 | R_ABEMA_最強戦 | `abema_book.SPREADSHEET_ID` | 【1】元データ・【2】最強戦・【3】手動補正 | gviz・htmlview・CSV、API | 書きは `live-channel-writer` | 【2】を書き、【3】の見出し | regenerate-saikyo（update_abema_book・saikyo_pages）、collect_saikyo_images | 毎日 |
| 8 | R_番組 | `video_book.SPREADSHEET_ID`（`wayhome`／`mtsuku`／`study` の `SPREADSHEET_ID`） | 帰り道・Mつく・女流勉強会 | gviz・htmlview・CSV | なし | なし | video_wayhome・wayhome_episodes・mtsuku・study（各話を含む）・check_wayhome・fetch_youtube_meta・build_wayhome_ogp | 週次・push |
| 9 | R_その他 | `other_book.SPREADSHEET_ID` | 辞書・書籍 | gviz・htmlview・CSV | なし | なし | resource_dictionary・books_pages（凍結中）・fetch_rakuten_books・sync_books_calendar | 週次・push |
| 10 | R_Ampai | `ampai.SPREADSHEET_ID` | 成績表 | gviz・htmlview・**CSV で値も読む**（`fetch_csv()`） | なし | なし | jpml_pros・houou_pages・check_ampai（check-meibo） | 週次・push |
| 11 | R_ログ | `fetch_x_posts.SPREADSHEET_ID`（`generate_resource_logs.LAYER_SPREADSHEET_ID`・`convert_x_posts.SPREADSHEET_ID`） | 【1】元データ・【2】自動変換後・【3】手動補正・比較 | gviz・htmlview・CSV、API、Drive API（`drive.metadata.readonly` で編集できるかを確かめる） | 書きは `live-channel-writer` | 【1】に追記、【2】・比較を書く | fetch-x-posts・convert-x-posts（手動）、resource_logs | 手動・週次・push |
| 12 | mj_resource | `generate_resource_logs.SPREADSHEET_ID`（`generate_jpml_test`／`generate_video_en` の `SPREADSHEET_ID`） | テスト・英語・ログ（対局は読む所が無い） | gviz・htmlview・CSV | なし | なし | jpml_test・video_en・resource_logs・fetch_x_posts（ログ）・check_unused_names | 週次・push |
| 13 | mj_ryoei | `generate_rh_results.SPREADSHEET_ID`（`generate_rh_paifu`／`generate_rh_results_detail`） | 牌譜・成績・成績詳細 | gviz・htmlview・CSV | なし | なし | rh_paifu・rh_results・rh_results_detail | 週次・push |
| 14 | mj_saikyo_mens | `generate_saikyo_mens.SPREADSHEET_ID` | 選手 | gviz・htmlview・CSV | なし | なし | saikyo_mens | 週次・push |
| 15 | 20210430_「孤立牌」と「1メンツ+1」の比較 | `generate_resource_efficiency.SPREADSHEET_ID` | シート1 | gviz・htmlview・CSV | なし | なし | resource_efficiency | 週次・push |
| 16 | 20261016_連盟員名簿0.75 **（持ち主が分からない）** | `meibo.SPREADSHEET_ID` | 公開・【2】値貼付 | gviz・htmlview・CSV | なし | なし | jpml_pros・houou_pages・ask_data・check_meibo（週次）・sync_birthday_calendar（週次）・sync_dojo_calendar（毎日） | 毎日・週次・push |
| 17 | （htmlview は 401 で題を引けない。指示文と `workers/ask/wrangler.jsonc` のコメントでは「ryoei.pro_AIボット」） | `ASK_SHEET_ID`（Worker `mj-ask` の vars） | 最初のシート | API のみ | `mj-ask`（Worker の Secret `GOOGLE_SA_KEY`） | 週次で質問を書き出す | Worker `mj-ask` の週次の cron | 週次 |

- 持ち主: htmlview では持ち主を引けない。16 は名簿で連盟（または他の人）のものの可能性がある。ほかの15冊（1〜15）も持ち主は確かめていない（平野さんが Drive で照合する前提）
- 読むもののうち、認証が要らない前提のものは 1〜16 の全部。認証付きで読んでいるのは 17 だけ
- 書くサービスアカウント: `live-channel-writer`（`LIVE_SHEETS_SA_KEY`）は 4・5・6・7・11 に編集者で共有されている。`GCP_SA_KEY`（`birthday-calendar`）はカレンダーだけでブックは読まない。`GSC_SERVICE_ACCOUNT_JSON`（`gsc-export`）は Search Console だけ
- 鍵を受け取らずにブックを読むワークフロー: `regenerate-page.yml`（Secret は楽天だけ）・`check-meibo.yml`・`check-saikyo-unregistered.yml`・`check-image-links.yml`（X だけ）・`fetch-youtube.yml`（YouTube だけ）。鍵を受け取るワークフロー（update-live-channel・update-sns-book・regenerate-saikyo・sync-yotei・fetch-x-posts・convert-x-posts）も、**読むのは gviz で、鍵は書き込みにしか使っていない**
- ブックを読む Worker: `mj-ask`（17 だけ）。`mj-scheduler` は読まない
- **非公開にしたときの壊れ方の違い**: gviz の読み取り（`_query()`）は応答が JSON でなくなり例外で止まる。一方 **htmlview と CSV の失敗は警告を出して通す**（`check_not_filtered()`・`check_sheet_exists()`・`fetch_csv()`。#472 で決めた外部の不調への備え）。
  gviz だけ認証が通り htmlview・CSV が通らない形になると、フィルタの検知（#432）と Ampai の組の確かめ（#548）が**黙って省かれる**

#### Google Charts 依存のページ（6、名前と数は文書どおり）

| ページ | JS | 読むタブ | 今の状態 |
|---|---|---|---|
| `houou_ranking.html` | `league_ranking.js`（`?sheet=鳳凰`） | 鳳凰 | `_redirects` で `/houou/ranking/total/` へ 301（#540、2026-10-16） |
| `houou_results.html` | `houou_results.js` | 鳳凰 | `/houou/players/` へ 301（同） |
| `ouka_ranking.html` | `league_ranking.js`（`?sheet=桜花`） | 桜花 | 公開中（navbar・llms.txt）。ouka/ の公開（#591、`work/OUK` が作業中）で転送の予定 |
| `ouka_results.html` | `ouka_results.js` | 桜花 | 同上 |
| `wrc_ranking.html` | `league_ranking.js`（`?sheet=JWRC`） | JWRC | 公開中。jwrc/ の公開（#599、`work/WRC` が判断待ち）で転送の予定 |
| `wrc_results.html` | `wrc_results.js` | JWRC | 同上 |

6ページとも 1（R_タイトル戦_成績）だけを読む。`league_ranking.js` は `?sheet=特昇` の分岐も持つが、リンクしているページは無い。

### 手順3: Google の公式の文書で確かめたこと

- gviz（`/gviz/tq`）: 「非公開のスプレッドシートは Google Visualization API でエンドユーザーの認証情報が要る。『リンクを知っている全員』なら要らない」「OAuth 2.0 の `access_token` を `/gviz/tq` の要求に付けられる」。例のスコープは `https://www.googleapis.com/auth/spreadsheets`、渡し方は クエリの `access_token=`。
  **サービスアカウントのトークンについての記述は無い**（例はエンドユーザーの OAuth）。出典: https://developers.google.com/chart/interactive/docs/spreadsheets （節「Authorization」「Example: Using OAuth to access /gviz/tq」）
- CSV エクスポート: 文書にあるのは Drive API の `files.export` で、スプレッドシートの CSV は「first-sheet only」、内容の上限は 10 MB、スコープは `drive`・`drive.file`・`drive.readonly` など。
  今使っている `/export?format=csv&gid=` と `htmlview` は公式の文書に無い（トークンで読めるかは文書では確かめられない）。出典: https://developers.google.com/workspace/drive/api/guides/ref-export-formats ・ https://developers.google.com/workspace/drive/api/reference/rest/v3/files/export
- Sheets API: 値の返し方は `FORMATTED_VALUE`（セルの表示形式どおり）・`UNFORMATTED_VALUE`（計算するが表示形式を付けない）・`FORMULA`。出典: https://developers.google.com/workspace/sheets/api/reference/rest/v4/ValueRenderOption
- Sheets API のシートの情報: `basicFilter`（そのシートのフィルタ）・`filterViews`、行の `hiddenByFilter`（フィルタで隠れている）・`hiddenByUser`（手で非表示）。出典: https://developers.google.com/workspace/sheets/api/reference/rest/v4/spreadsheets/sheets
- **サービスアカウントのトークンで gviz・htmlview・`/export` が読めるかは、文書では確かめられない。** 確かめるには、読み取り用のサービスアカウントの作成・鍵の Secret 登録・ブックへの共有（＝共有の設定の変更）と、それを使うワークフロー（＝コードの変更）が要るため、この指示では行わない（止まる条件の4つ目。確かめ方は「判断が必要なこと」の (a)）

### 手順3: 案

(a)〜(d) の選択肢・メリット・デメリット・推奨は `## 報告` の「判断が必要なこと」に書いた。補足:

- Sheets API に替えたときの差の見込み（実装時に1ページずつ差分で確かめる）:
  - SELECT 構文: `fetch_records()`・`fetch_table()` は今も `SELECT *` で、見出しで列を選ぶので影響が小さい。`fetch_sheet()` の `SELECT A,F,G WHERE J = "Y"` の形（型Aのページ・meibo・saikyo_pages など）は、列記号の選択と `WHERE`・`ORDER BY` を Python 側で行う形に書き直しが要る
  - フィルタの検知: `basicFilter` と行の `hiddenByFilter`・`hiddenByUser` で直接分かるため、gviz と CSV の行数を比べる今の形（#432・#510）が要らなくなる。`values.get` がフィルタで隠れた行も返すかは文書に明記が無く、要確認
  - 数の型: gviz は列ごとに型を決め、数の列の数でない値を空で返す（そのため `fetch_csv()` で確かめている、#548）。`values.get` はセルごとの値を返すので、この問題は無くなる見込み。代わりに日付が通し番号（`UNFORMATTED_VALUE`）になる、`formatted=True` のページは `FORMATTED_VALUE` で読む、などの置き換えが要る
  - 存在しないタブ: gviz は先頭のタブを返す（`check_sheet_exists()`）が、Sheets API は誤りを返す
- `generate_title_pages.fetch_tab()` は `lib/sheets.py` を通らないため、どちらの案でも別に直す必要がある
- ブラウザの Charts の4ページは、認証付きにする手段が「閲覧者が Google にログインしてブックの権限を持つ」しかなく、公開ページでは使えない（上の出典）

## 報告

- 状態: 判断待ち
- ブランチ: work/1018-prv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1018-PRV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-prv
- 確認用URL: なし
- マージ: 済（7c7df7484、fast-forward。ドキュメントのみのため完了報告のうえ入れた）
- issue: #557（着手中のコメントと結果の要点のコメント）
- 判断が必要なこと:
  - (a) 生成を認証付きで読む形
    - a1. gviz・htmlview・CSV の URL はそのままで、サービスアカウントのアクセストークンを付ける（`lib/sheets.py` の `_get()`・`_read()` と `generate_title_pages.fetch_tab()` だけを直す）。
      メリット: 変更が小さい。SELECT 構文・フィルタの検知・数の型の扱いが今のまま。デメリット: gviz にトークンを付ける形は公式の文書にあるがエンドユーザーの例だけで、サービスアカウントと htmlview・`/export` は文書に無い。非公開の後も文書に無い経路に頼り続ける
    - a2. Sheets API（`values.get`・`spreadsheets.get`）に替える。メリット: 公式の経路。フィルタ・非表示の行・存在しないタブ・数の列の空の値（#432・#510・#548）を直接扱える。デメリット: `fetch_sheet()` の SELECT 文の書き直し、日付・表示形式の扱いの置き換え、全ページの差分の確認で作業が大きい
    - **推奨: a1 で切り替え、a2 は別 issue で後から。** ただし a1 の前に、トークン付きで gviz・htmlview・`/export` が読めるかをワークフローで確かめる（下の確かめ方）。どれかが読めなければ a2 にする
    - 確かめ方の案（共有の設定とコードを変えるため、平野さんの判断が要る）: 読み取り用のサービスアカウントを作り、まだ「制限付き」の17に閲覧者で足すか、影響の小さい 15 を「制限付き」にしてそのサービスアカウントにだけ共有し、作業ブランチで手動実行するだけのワークフロー（gviz・htmlview・`/export` をトークン付きで1回ずつ読み、応答の形と行数だけを出す。値は出さない）で確かめる
  - (a) どのサービスアカウントで読むか
    - s1. 新しい読み取り専用のサービスアカウント（スコープ `spreadsheets.readonly`、1〜16 に閲覧者で共有、Secret を新しく1つ）。メリット: 書き込み用と分かれ、鍵が漏れても読めるだけ。`live-channel-writer` の「使い回さない」決定（docs/notes/live-channel-write.md）に沿う。デメリット: 平野さんの作業（作成・鍵・16冊の共有・Secret 登録）が要る
    - s2. 今の `live-channel-writer` で読む。メリット: 鍵が増えない。デメリット: 書き込み専用の決定に反する。読むだけのワークフロー（regenerate-page など）にも書ける鍵を渡すことになる。4・5・6・7・11 以外の11冊にも共有が要る
    - s3. `GCP_SA_KEY`（`birthday-calendar`）を使う。デメリット: カレンダー用で、用途が混ざる
    - **推奨: s1**
    - どれでも、`ryoei.net`（Workspace）の Drive の外部共有の設定でサービスアカウントへの共有が止められることがある（docs/notes/live-channel-write.md「4. スプレッドシートをサービスアカウントに共有する」）。最初の1冊で確かめる
  - (b) Google Charts 依存のページ
    - houou_ranking・houou_results: すでに 301。**推奨: 何もしない**（ファイルの削除は #540 で残すと決めたまま）
    - ouka_ranking・ouka_results: b1. ouka/ の公開（#591）で転送して廃止（推奨）／b2. 静的な生成に作り直す（ouka/ と二重になるので勧めない）
    - wrc_ranking・wrc_results: b1. jwrc/ の公開（#599）で転送して廃止（推奨）／b2. 同上
    - **推奨: 4ページとも転送で廃止し、「R_タイトル戦_成績」（1）を非公開にするのは #591・#599 の転送の後にする。** 転送の前に非公開にすると、4ページが閲覧者の画面で空になる
  - (c) 開発の進め方（コンテナで直接読めなくなった後）
    - c1. 読むだけのワークフローを足す（推奨）: `regenerate-page.yml` に `dry_run`（生成して差分の要約を実行サマリーに出し、コミットしない）と `check`（`regenerate.py --check`）の入力を足し、作業ブランチを ref にして起動する。シートの照合は、タブ・行数・見出しだけを出すワークフローで行う（値はジョブのログに出さない。作業ログは public の mj-logs に写るため、非公開のブックの値をログに書かない）
    - c2. コンテナの環境の変数に読み取りの鍵を置く。メリット: 今と同じ速さで読める。デメリット: 「鍵は Actions のシークレットにだけ置く」（docs/notes/cloud-sessions.md「鍵」）に反する。勧めない
    - 影響: シートを直した後の確かめ（`--check`、static-generation.md の「平野さんがシートを用意した・直したと言ったとき」）は、1回ごとにワークフローの起動と待ち（数分）が入る。新旧の生成物の比較は、作業ブランチの dry_run の実行サマリーで行う
    - チャット側: 非公開の後は、チャット側も gviz・htmlview で直接は読めない。シートの中身を知りたいときは、Claude Code に c1 のワークフローで読ませ、件数・見出しなどの要約をログで受け取る形になる
  - (d) 切り替えの順番（推奨。各段で、平野さんが「制限付き」にしてから次の予約実行か手動実行の成功を見て次へ）
    - 段0（公開のまま）: s1 の用意、`lib/sheets.py` と `generate_title_pages.fetch_tab()` をトークン付きにし、**認証の失敗（401・403・ログイン画面）は警告で通さず止める**形にする。読むワークフロー全部（上の表の「鍵を受け取らずに読む」5本と、鍵を受け取る6本）に読み取りの Secret を渡す。全ページを再生成して差分 0 を確かめる
    - 段1: 15（読み手1ページ・更新されない）を「制限付き」→ resource_efficiency を dry_run で生成し差分 0
    - 段2: 読み手の少ないブック 13・14・12・9・8・10 を1冊ずつ
    - 段3: 書き込みのあるブック 4・5・6・7・11（毎日の run-daily の成功を1回見てから次）
    - 段4: 横断のブック 3・2（週次 all の成功を見る）
    - 段5: 1（#591・#599 の転送の後）
    - 段6: 16（名簿）は持ち主を確かめてから。平野さんのものでなければ共有は変えられないので、扱いを別に決める
    - 各段で止める条件: 生成物の差分が出た／ワークフローが失敗した／フィルタの検知・CSV の確かめの「警告」が出た（認証の失敗の素通り）／Charts のページの表示が空になった。戻し方は一般的なアクセスを「リンクを知っている全員」に戻す
    - フォルダへの移動は、各段の「制限付き」の後でよい（移動で ID は変わらない見込み。要確認）
  - 名簿（16）の持ち主の確認と、題の「R_…」への付け替えが意図どおりか（コードのコメントは旧名のまま）
- 未確認の項目:
  - サービスアカウントのトークンで gviz・htmlview・`/export` が読めるか（文書に無い。(a) の確かめ方で確かめる）
  - Sheets API の `values.get` がフィルタで隠れた行を返すか（文書に明記が無い）
  - 1〜15 の持ち主と、移行先のフォルダとの照合（平野さんが Drive で行う）
  - ブックをフォルダへ移したときに ID・共有が変わらないか
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5a267bc8）: https://github.com/retroeater/mj-logs/tree/main/guide/5a267bc8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a267bc8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
