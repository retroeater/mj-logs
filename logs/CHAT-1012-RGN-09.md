# CHAT-1012-RGN-09

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: 915bafa2

## 指示

【Claude作成】Claude Code 向け指示：#551 の1本目。シートを読む全ページについて、今の検査（止まる条件）と書き出しを洗い出し、`--check` の共通の作りの案を比べて報告する（調べるだけ。コードは変えない） Chat-Ref: CHAT-1012-RGN-09 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。判断の要る点は報告の「判断が必要なこと」に書き、状態は CLAUDE.md「作業ログ」節のとおり「判断待ち」にする 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

目的
#551（シートを読むページに、ファイルを書かず生成と同じ検査だけを回す手段 `--check` を持たせる）を、シートを読む全ページで行う。対象が多いので、1本目は実物の洗い出しと共通の作りの案の比較だけにし、作りを平野さんと決めてから実装の指示を出す。
決定（2026-10-12、平野さん）

* `--check` は、シートを読む全ページに作る（辞書は済み）
* 帰り道の「JSON に無い回」（今は止めずに外して警告）は、`--check` では「止まる理由」と「警告」を分けて両方を全部出す。警告だけなら終了コード 0（今の生成と同じ扱い）
* 実装の指示のマージは承認済み（止まる条件つき）。この1本目は調べるだけ

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* 2026-10-11 の決定（#551 の本文）: 帰り道の `--check` は `lib/wayhome.py` の `load_episodes()` と写真の確かめ（`check_player_photos()`）の両方を回し、途中で止めずに全部の理由を出す
* 辞書の `--check` は `scripts/generate_resource_dictionary.py` の `run_check()`（CHAT-1012-RGN-08 のログでは 269〜286行）。同名の `--check` を持つ別の意味のスクリプト（`build_ogp_image.py`・`update_sns_book.py`・`write_live_channel_candidate.py`）がある（要確認）
* 共通の作りの案（チャット側の案。実物を見て足し引きしてよい）:
   * (A) 各生成スクリプトに `--check` を足し、理由を集める共通の部品（例: `scripts/lib/` に、止まる理由と警告を集めて最後にまとめて出すもの）を使う。全部の理由を出せるが、スクリプトごとに直しが要る
   * (B) `regenerate.py --check <ページ|all>` が、書き出しを止める指定（環境変数など）で各生成スクリプトを動かす。`lib/page.py` の書き出しなどを止める。直しは少ないが、最初の理由で止まるスクリプトはそのまま
   * (C) (B) を入口にし、(A) の部品は止まった実績のあるページから順に入れる（段階的）
* 気をつけること: `--check` が外部の API（YouTube Data API、X API など）を呼ぶと、割り当て・課金に響く。どのページが何を呼ぶかを洗い出す

手順

1. 洗い出す（読むだけ）: `python3 scripts/regenerate.py --list` の全ページについて、生成スクリプトごとに次を表にする: 読むシート（ブック・タブ）、外部の API の呼び出し（YouTube・X・楽天など）、止まる条件（`raise`・`SystemExit`・`FilteredSheetError` など。ファイルと行）と、最初の1つで止まるか全部を集めるか、警告で続けるもの、書き出すファイル（`lib/page.py` を通るか、直接書くか）、`--check` の既存の有無。凍結中のページ（`FROZEN_FROM_ALL`）は表に載せ、対象から外すかを判断が必要なことに書く。
2. 案を比べる: 上の (A)〜(C) と、手順1で見つかったほかの案について、直すファイルの数の見積もり、全部の理由を出せるページ、外部の API の扱い（呼ばない・割り当てを使う）、`regenerate.py` の今の作り（#533 の飛ばす作り）との関係、試験の作り方を表にする。チャット側が平野さんと決めるための推奨を1つ、理由とともに書く。指示の分け方の案（1本3項目程度。何本で、どの順か）も書く。
3. 記録する: 上の「決定」を `docs/decisions/automation.md` に足す。#551 に、決定と、この調べのログへの permalink（SHA を固定）をコメントする（#551 は閉じない）。

止まる条件

* #551 が Closed、または他セッションの着手中コメントがある
* ログ・`docs/decisions/` 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（推奨と指示の分け方は「判断が必要なこと」に書き、状態は「判断待ち」）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-RGN-09"` は0件（浅いクローンは RGN-08 で解消済み）。`RGN` は同じチャットの連番
- 作業ブランチ: ローカルの work/1012-rgn は `origin/cloudflare` の祖先（RGN-08 でマージ済み）、リモートもマージ済み → docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git checkout`（既に上）のうえ `git merge --ff-only origin/cloudflare`（73f88804 → 915bafa2）
- 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- #551 は Open、コメントは0件（他セッションの着手中なし）。着手中コメントを残した: https://github.com/retroeater/mj/issues/551#issuecomment-6116667683

### 1. 洗い出し（cloudflare 915bafa2、読むだけ。スクリプトは実行していない）

読み方: `regenerate.py --list` の23ページを4つに分けて読み取り専用のサブエージェントで洗い出し、要の箇所（title の `fetch_tab`、live_pages の HEAD、`page.generate()` を使うページ、書き出し後の検査など）は自分で読み直した。

#### 1-1. 全ページに共通すること

- **シートの読み**は `scripts/lib/sheets.py` の `fetch_sheet()`（20行）・`fetch_records()`（53行）・`fetch_table()`（89行）。どれも先に `check_not_filtered()`（120行、タブごとに1回）を通り、htmlview（gid）・CSV エクスポート・gviz の COUNT を取る。止まる条件は、タブが無い（67・96行）、見出しの欠け・重複（73行、欠け・重複は全部を1つの例外に列挙）、フィルタ（143行 `FilteredSheetError`）、応答の形（201行）、gviz のエラー（208行 `RuntimeError`）。**いずれも最初のタブの最初の問題で止まる**。gid が引けない・CSV が取れないときは「警告:」を出して検知を省く（131・135行）
- **例外**: `scripts/generate_title_pages.py` の `fetch_tab()`（255〜279行）は sheets.py を通らず、gviz を直接読む（再試行なし、`check_not_filtered()` なし）。title_pages・jpml_pros（380行 `final_counts()`）・jpml_test（71行「別名」）がこれを使う。**この3ページの title 側のタブ（「タイトル」「タイトル戦」「連盟プロ以外」「別名」）はフィルタの検知（#432）が掛かっていない**
- **API キー**: 生成スクリプトとその lib はどれも API キーを読まない。YouTube Data API（`fetch_youtube_meta.py`・`fetch_youtube_channels.py`、`YOUTUBE_API_KEY`）と楽天（`fetch_rakuten_books.py`）は別のスクリプトで、生成は結果の JSON（`data/youtube_meta.json`・`data/youtube_channels.json`・`data/rakuten_books.json`）を読むだけ
- **シート以外の通信**（割り当て・課金は無い。相手のサーバへの負荷と時間だけ）:
  - live_pages: img.youtube.com への HEAD（`generate_live_pages.py` 432〜441行 `head_status()`、8並列）。結果は `data/live_thumbs.json` にキャッシュし、未知の動画IDだけ確かめる。`resolve_hero_thumbs(save=True)`（397行、1091行で呼ぶ）がキャッシュを書く。`save=False` の引数は既にある
  - video_wayhome・wayhome_episodes: pbs.twimg.com への HEAD（`lib/wayhome.py` 369行 `photo_status()`、382行 `check_player_photos()`）。X ID のある選手ごとに最大3回
- **書き出し**: `lib/page.py` の `generate()`（418〜448行、書くのは 448行の1か所）を使うのは9ページ（jpml_test・resource_logs・rh_paifu・rh_results・rh_results_detail・saikyo_mens・video_en・video_live・video_mtsuku）だけ。**残りの14ページは各スクリプトが直接書く**（`write_text`・`unlink`・`rmdir`。複数ファイル・古いファイルの削除・sitemap・`_redirects`・`data/*.json` を含む）
- **既存の `--check`**: 生成スクリプトでは resource_dictionary だけ（298行、`sys.argv[1:] == ["--check"]` の完全一致。argparse は無い）。ほかの `--check` は意味が違う: `build_ogp_image.py`（165行。書き換えず今の画像と一致するか）、`update_sns_book.py`（452行。読むだけ。タブ・見出し・件数・編集できるか）、`write_live_channel_candidate.py`（215行。書き込まずタブの見出し・行数を返す）
- **書き出しの後に止まる所**（今の生成でも途中の出力が残る。#533 の作りが戻している）: title_pages 1070行（決勝メンバー行数の照合。ページ・sitemap・search.json・years.json の後）、houou_race 392→393行（JSON を書いた後の `render_page()` の `pick_default()` 315行）、houou_pages 622→623行（race の JSON を書いた後の `render_race()` 514・522行）、books_pages 489行（個別ページを書いた後の sitemap の XML 検査）、saikyo_pages 1052行・wayhome_episodes の各回の書き出しの途中

#### 1-2. ページごと

凡例: 「止まる条件」は sheets.py の共通分（上）を除いたもの。【全】は問題を全部集めてから1つの例外にする、【初】は最初の1件で止まる。

| ページ | 読むシート（ブック: タブ）と入力 | シート以外の通信 | 止まる条件 | 警告で続けるもの | 書き出し | `--check` |
|---|---|---|---|---|---|---|
| ask_data | 旧（10g_）: 連盟プロ以外・別名・タイトル戦／プロ（1h4）: プロ・鳳凰／名簿（16Y4）: 公開／3層（1_H3L）: 【2】【3】／`title/search.json` | なし | `generate_ask_data.py` 103・110・120・122行【初】（GenerationError、195行で exit 1）。houou_pages の `load_rows()`（112〜119行）【初】、live.py 159行・live_pages 288行【全】、live_layer3 95行【初】。sheets・pro_sheet・meibo の ValueError は捕まえず traceback | NameBook・名簿の警告を print（61・70行）。live_pages の Warnings を作るが `report()` を呼ばない（128行。除外した行は出ない） | 直接: `workers/ask/data.json`（186行）1つ | なし |
| books_pages（凍結） | 旧: 書籍・連盟プロ以外・別名／プロ／SNS ブック【2】【3】【4】／`data/rakuten_books.json` | なし（楽天は別スクリプト） | `check_rows()` 161〜184行（カテゴリごとに【初】、カテゴリ内は【全】）、sns.py 56行【初】・75行、pro_sheet 54行【全】、489行 sitemap の XML 検査（個別ページを書いた後） | 名前・書影なし・校正・著者の照合（集めて最後に一覧 549〜552行） | 直接: `books/<ISBN>.html`（513〜524行）・古いものを unlink（526〜530行）・`books/index.html`・`sitemap-books.xml` | なし |
| houou_leagues | プロ（1h4）: 鳳凰（2回）・プロ | なし | 本体に raise なし。pro_sheet 54行【全】、pro_stats 75行【初】。157行 `periods[0]` が期0件で IndexError | なし（スキップ数を print） | 直接: `houou_leagues.html`（239行）・`houou_leagues_data.json`（241行） | なし |
| houou_pages | プロ: 鳳凰（3経路で再読）・プロ／名簿: 公開・【2】値貼付／旧: 連盟プロ以外・別名／SNS ブック | なし | `load_rows()` 112・116・118行【初】、580行【全】、597〜598行【初】、`render_race()` 514・522行【初】（書き出しの途中）、meibo 78行【初】・81行【全】（ValueError は捕まえない）、houou_race の `load_periods()` | 名簿の警告を print | 直接: `houou/` 配下（index・search.json・results/*.json と古いものの unlink・players・ranking 9部門・leagues・race/*.json と unlink・race/index）。`write()` 566行 | なし |
| houou_race | プロ: 鳳凰（2経路）・プロ／旧: 連盟プロ以外・別名／SNS ブック | なし | 184・197行【初】、217・236・247行【全】、242行【初】、368行【初】、315行（書き出しの後） | NameBook の警告、band_mismatch などを print | 直接: `houou_race/<期>.json` と unlink（336〜346行）→ `houou_race.html`（393行） | なし |
| jpml_pros | プロ（1h4）: プロ・リーグ・鳳凰・桜花・最強戦・対局・鳳凰Ampai・桜花Ampai／SNS ブック／名簿: 公開・【2】値貼付／旧（title の `fetch_tab`）: タイトル・タイトル戦・別名／`data/youtube_channels.json` | なし（YouTube は別スクリプト） | 本体に raise なし（try も無い）。pro_sheet 54行【全】、meibo 78行【初】・81行【全】、pro_stats 75・83・88・98行【初】、title の GenerationError（266・269・287行【初】、292行【全】、329行【初】、358行【全】、361行【初】、434行【全】） | 「警告:」を stderr に（名簿 359行、Ampai 369〜371行、JSON が無い 203行、画像の無いチャンネル 384〜389行） | 直接: `jpml_pros.html`（415行）1つ | なし |
| jpml_test | 1y8x: テスト／旧（title の `fetch_tab`）: 別名／プロ／SNS ブック | なし | title 287行（「別名」の見出しの完全一致）・329行【初】、pro_sheet 54行【全】、sns.py 56行【初】・75行 | なし | `page.generate()` → `jpml_test.html` | なし |
| live_pages | 旧: 連盟プロ以外・別名・タイトル戦／プロ／SNS ブック／3層: 【2】【3】／`title/search.json`・`data/live_pages.json`・`data/live_thumbs.json`・`_redirects` | img.youtube.com の HEAD（未知の動画IDだけ。キャッシュあり） | live.py 146〜159行【全】、288行【全】、live_layer3 95行・86行【初】、47行の assert（import 時）、sns.py 56・75行 | Warnings（224〜249行）に集めて最後に `report()`（1123行、ジョブのサマリにも）。行の除外・同じ URL・`_redirects` の上限など | 直接: `live/**.html`（1049〜1054行）・`remove_stale()`（1057〜1064行）・`_redirects` のマーカーの間（994〜1044行）・`data/live_pages.json`・`data/live_thumbs.json`（キャッシュ） | なし |
| ouka_leagues | プロ: 桜花（2回）・プロ | なし | 本体に raise なし。pro_sheet 54行【全】、pro_stats 72〜76行【初】。101行 `periods[0]`・114行 `max()` が空で例外 | なし | 直接: `ouka_leagues.html`（161行）→ `ouka_leagues_data.json`（165〜167行） | なし |
| resource_dictionary | 旧: 辞書／プロ | なし | 153行（`check_rows()` は集めて最初の1件だけ投げる）、173行（知らないカテゴリ、全部の名前）、191行、pro_sheet 54行 | 「辞書」にカテゴリの行が無いと print して飛ばす（187〜189行） | 直接: `dic/<slug>.json` と unlink（246〜256行）→ `resource_dictionary.html`（293行） | **あり**: `run_check()` 269〜286行。`fetch()` の ValueError は1件で止めて出し、`check_all()`（198〜214行）は全部を集める。終了コード 0/1。RuntimeError・通信の例外は捕まえない |
| resource_efficiency | 1O8I: シート1 | なし | 本体に検査なし。`lib/chart.py` 99行 `max()` が空で例外 | なし | 直接: `resource_efficiency.html`（131行） | なし |
| resource_logs | 1y8x: ログ | なし | 本体に検査なし（10列の unpack） | なし | `page.generate()` → `resource_logs.html` | なし |
| rh_paifu | 1WxX: 牌譜 | なし | 本体に検査なし（10列の unpack） | なし | `page.generate()` → `rh_paifu.html` | なし |
| rh_results | 1WxX: 成績 | なし | 本体に検査なし | なし | `page.generate()` → `rh_results.html` | なし |
| rh_results_detail | 1WxX: 成績詳細 | なし | 本体に検査なし（9列の unpack） | なし | `page.generate()` → `rh_results_detail.html` | なし |
| saikyo_mens | 1_xu: 選手 | なし | 本体に検査なし（6列の unpack） | なし | `page.generate()` → `saikyo_mens.html` | なし |
| saikyo_pages | プロ: 最強戦（2回）・プロ／旧: 連盟プロ以外・別名／SNS ブック | なし | 349行 assert【初】、1006行 `min(years)`（対局0件）、1052行 sitemap の XML 検査（ページの後）、sns.py・pro_sheet・sheets の ValueError | **データの不備はすべて警告で続ける**（267・305・343・356・530・544・556・847・901行、NameBook） | 直接: `saikyo/<年>/` の各ページ（821〜832行）・`saikyo/<年>.html`・`saikyo/index.html`・古いものの unlink と rmdir（866〜877・1148〜1150行）・`sitemap-saikyo.xml` | なし |
| title_pages | 旧（`fetch_tab`、フィルタ検知なし）: タイトル・タイトル戦・連盟プロ以外・別名／プロ／SNS ブック／3層: 【2】【3】 | なし | GenerationError（137行、1096〜1100行で exit 1）: 266・269・287行【初】、292行【全】、307・314行【全】、329行【初】、358行【全】、361行【初】、389・434行【全】、1021行（3層の ValueError を変換）【初】、**1070行（書き出しの後）**。捕まえないもの: `fetch_tab` の通信の例外・sns・pro_sheet・live_layer3 95行。606行 `max()` が空で例外 | warnings に集めて最後に出す（363〜385・678・759・1087・1089行） | 直接: `title/` 配下（`write_page()` 904〜913行）・`remove_stale()`（941〜948行）・`sitemap-title.xml`・`title/search.json`・`title/years.json` | なし |
| video_en | 1y8x: 英語 | なし | 本体に検査なし | なし | `page.generate()` → `video_en.html` | なし |
| video_live | 1h4: 対局 | なし | 本体に検査なし | なし | `page.generate()` → `video_live.html` | なし |
| video_mtsuku | 1y8x: Mつく | なし | 本体に検査なし | なし | `page.generate()` → `video_mtsuku.html` | なし |
| video_wayhome | 1h4: 帰り道（`fetch_records`）・プロ／SNS ブック／`data/youtube_meta.json` | pbs.twimg.com の HEAD | `load_episodes()` 118行【初】（動画IDが取れない）、169・177・190・263行【初】、`check_player_photos()` 403行【全】、149行【初】。`youtube_meta.json` が無い・壊れているときは捕まえない（112行） | JSON に無い回は外して `::warning::`（128〜130行、全部を1行に）、プロに居ない・同姓同名（363・365行） | 直接: `video_wayhome.html`（280行）1つ。書くのは全検査の後 | なし |
| wayhome_episodes | video_wayhome と同じ | pbs.twimg.com の HEAD | video_wayhome と同じ＋266行（動画IDが取れない行を【全】）・269行（重複）・241行（sitemap の XML 検査、書き出しの前） | video_wayhome と同じ | 直接: `wayhome/<ID>.html`（295行）・古いものの unlink（300〜303行）・`sitemap-wayhome.xml`（242行） | なし |

ブックの ID は表では先頭の文字だけ書いた（全体は各スクリプトの定数と `lib/live.py`・`lib/pro_sheet.py`・`lib/sns_book.py`・`lib/meibo.py`・`lib/live_layer3.py`）。

#### 1-3. 洗い出しで分かったこと（案の比較に効くもの）

- **「シートを読むページ」は23ページすべて**（シートを読まないページは無い）。シートの内容を検査するのは11ページ（ask_data・books_pages・houou の3つ・jpml_pros・live_pages・resource_dictionary・saikyo_pages・title_pages・帰り道の2つ）。残りの12ページは sheets.py の共通の検査（タブ・見出し・フィルタ）しか無く、行の形が崩れると unpack や `max()` の例外で止まる
- **止まる理由を全部出せていない所**は、sheets.py の共通の検査（タブごとに最初の1つ）、`load_episodes()` 118行、title の大半、houou 系の【初】の検査。逆に全部を集める形は title（307・314・358・389・434行）・houou_race（217・236・247行）・live_pages（Warnings）・`check_player_photos()`・辞書の `check_all()` に既にあり、作りがページごとにばらばら（GenerationError を捕まえて exit 1 にするページと、ValueError を traceback のまま落とすページがある）
- 外部の割り当て・課金に響く API は生成のどのページも呼ばない。`--check` で気をつけるのは、HEAD の2種（live_pages・帰り道）と、live_pages のキャッシュの書き込み（`save=False` がある）
- 書き出しを止める口が1か所に無い（`page.generate()` は9ページだけ）。案 (B) の「`lib/page.py` の書き出しを止める」で止まるのは9ページだけで、残り14ページは書き出しの箇所ごとに分岐が要る

### 2. 案の比較

案 (D) を足した: **`regenerate.py --check` が、今の生成スクリプトをそのまま動かし、終わったら #533 の `TreeSnapshot` で作業ツリーを動かす前に戻す**（`regenerate.py` 105〜143行。失敗したページの途中の出力を戻す仕組みを、成功したページにも使う）。生成スクリプトは変えない。

| | (A) 各スクリプトに `--check`＋共通の部品 | (B) `regenerate.py --check` が書き出しを止める指定で動かす | (C) (B) を入口にし (A) を順に入れる | (D) `regenerate.py --check` が普通に生成して戻す | (E) (D) を入口にし (A) を順に入れる |
|---|---|---|---|---|---|
| 直すファイル（試験を除く） | 生成22本（辞書は載せ替え）＋ `lib/check.py`（新）＋ `lib/page.py` ＋ `regenerate.py` ≈ 25本 | `regenerate.py`・`lib/page.py`＋直接書く14本の書き出し箇所すべて ≈ 16本（1本あたりの直しは小さいが、書き出しの箇所は数十） | 1段目は (B) と同じ、以後ページごとに (A) | `regenerate.py` 1本 | 1段目は `regenerate.py` 1本、以後ページごとに (A) |
| ファイルを書かないか | 書かない | 書かない（漏れがあると書く。14本の書き出しを全部塞いだかを試験で確かめにくい） | 書かない | **書く（終わったら戻す）**。#551 の題の「ファイルを書かず」には合わない | 1段目は書いて戻す。(A) を入れたページは書かない |
| 全部の理由を出せるページ | 全ページ（作り次第） | 今と同じ（最初の理由で止まるページはそのまま） | (A) を入れたページから | 今と同じ | (A) を入れたページから |
| 書き出しの後の検査（title 1070行・houou_race 315行・houou_pages 514/522行など） | 検査を書き出しの前に出す直しが要る（漏れると `--check` が通って生成が止まる） | 書き出しを止めると、その後の検査まで届くかはページ次第（届かない形もある） | 同左 | **そのまま回る**（生成と同じ経路なので、生成で止まるものは必ず止まる） | (A) を入れるまでは (D) と同じ |
| 外部への通信 | HEAD を回すかをページごとに選べる（帰り道は決定のとおり回す。live_pages は `save=False`） | 生成と同じ（live_pages のキャッシュの書き込みは止める指定が要る） | 同左 | 生成と同じ（HEAD を回す。キャッシュも書いて戻す）。割り当ての要る API は無い | 同左 |
| #533 の飛ばす作りとの関係 | 別の入口（各スクリプト）。`regenerate.py` からまとめて呼ぶ口は別に要る | `regenerate.py` の対象の選び方・出力の流し方をそのまま使える | 同左 | **同じ `TreeSnapshot` を使う**。飛ばす作りの試験（`test_regenerate.py` の5件）と同じ形で試験できる | 同左 |
| 試験の作り方 | 各スクリプトの `check()` に作った行を渡す単体試験（辞書の `test_resource_dictionary.py` 126行〜の形）＋共通の部品の試験 | 書き出しを止めたとき作業ツリーが変わらないことを、ページごとに確かめる試験 | 両方 | `test_regenerate.py` に、成功・失敗のどちらでも作業ツリーが元に戻り、終了コードが 0/1 になる試験を足す | 両方 |
| 作業ツリーに手を入れている最中の扱い | 影響なし | 影響なし | 影響なし | `TreeSnapshot` が未コミットの変更ごと戻す（追跡しないファイルは対象外。今の再生成と同じ） | 1段目は (D) と同じ |

推奨: **(E)**。理由:

- 決定（全ページに作る）を、`regenerate.py` 1本の直しで最初の1本から全23ページについて満たせる（止まる条件・止まる経路が生成と一致し、書き出しの後の検査も漏れない）
- その上で、止まる理由を全部出したいページ（決定済みの帰り道、止まった実績のある辞書、検査の多い title・houou 系）から (A) の部品を入れる。部品を入れたページは `regenerate.py --check` が生成の代わりにそのページの `--check` を呼ぶ（書かない）
- (B) は、書き出しが1か所に無い（14本が直接書く）ため「直しが少ない」という前提が実物では成り立たない。漏れを試験で確かめにくい
- 気になる点: 1段目の (D) は「ファイルを書かず」に合わない（書いて戻す）。これを許すかが平野さんの判断。許さないなら推奨は (A)（入口は `regenerate.py --check`、部品を入れていないページは「`--check` なし」と出す）

指示の分け方の案（(E) のとき。1本3項目程度、6本）:

1. 入口と共通の部品: `regenerate.py --check <ページ|all>`（(D)、終了コードと出力の形）／`scripts/lib/check.py`（止まる理由と警告を集めて最後に出す。警告だけなら 0、止まる理由があれば 1）／辞書の `run_check()` を部品に載せ替え、`regenerate.py --check` が `--check` を持つページはそれを呼ぶ形
2. 帰り道（決定済み）: `video_wayhome`・`wayhome_episodes` の `--check`（`load_episodes()` の視聴URLの検査を全部集める形にし、JSON に無い回は警告、`check_player_photos()` も回す）／共通の前段（`fetch_rows()`・`index_player_links()`）を2本で共有／試験
3. title 系: `title_pages` の `--check`（1070行の照合を書き出しの前に出す）／`fetch_tab()` を使う jpml_pros・jpml_test／（判断次第で）`fetch_tab()` へのフィルタ検知
4. houou 系: houou_race（315行の既定表示の検査を書き出しの前に）・houou_pages（514・522行）・houou_leagues・ask_data
5. live_pages・saikyo_pages（警告の多いページ。`save=False`、sitemap の検査を書き出しの前に）
6. 残り: `page.generate()` を使う9ページ（`lib/page.py` に検査だけの口を1つ足せば9本まとめて済む）と ouka_leagues・resource_efficiency（空のデータの例外を止まる理由にする）

(A) にする場合も分け方は同じで、1本目の (D) が無くなり、各本が受け持つページの `--check` が入るまで、そのページは `regenerate.py --check` で「なし」と出る。

### 3. 記録

- `docs/decisions/automation.md` に「決定」節の3項目を足した
- #551 にコメントした（決定・要点・このログの permalink 358186e8）: https://github.com/retroeater/mj/issues/551#issuecomment-6116726743

### マージ

- `git merge origin/cloudflare`（衝突なし）→ push 直前に再fetchして `git merge-base --is-ancestor origin/cloudflare HEAD` が真 → `git push origin work/1012-rgn:cloudflare`（28649cd5..7baad9a3）

## 報告

- 状態: 判断待ち
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-RGN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 済（7baad9a3）
- issue: #551（コメントのみ。閉じない）
- 判断が必要なこと:
  - 共通の作りの推奨は (E): `regenerate.py --check` を入口にし、1本目は今の生成スクリプトをそのまま動かして #533 の `TreeSnapshot` で戻す形（(D)。`regenerate.py` 1本の直しで全23ページ）、その後、止まる理由を全部出したいページから各スクリプトの `--check`＋共通の部品 `lib/check.py`（(A)）を入れる。理由と比較は `## 経過` の「2. 案の比較」
  - (E) の1段目は「書いて戻す」ため、#551 の題の「ファイルを書かず」に合わない。これを許すか。許さないなら推奨は (A)（入口は同じ `regenerate.py --check`、未対応のページは「なし」と出す）
  - 案 (B)（書き出しを止める指定）は、`lib/page.py` の `generate()` を使うのが9ページだけで、残り14ページが直接書くため、前提の「直しが少ない」が実物では成り立たない。外すか
  - 指示の分け方の案（6本）: 1 入口と共通の部品・辞書の載せ替え → 2 帰り道 → 3 title 系（title_pages・jpml_pros・jpml_test）→ 4 houou 系（houou_race・houou_pages・houou_leagues・ask_data）→ 5 live_pages・saikyo_pages → 6 残り（`page.generate()` の9ページ・ouka_leagues・resource_efficiency）
  - 凍結中の books_pages（`FROZEN_FROM_ALL`）を `--check all` の対象から外すか（推奨: 外す。今の `all` と同じで、名前を指定すれば動く形）
  - title の `fetch_tab()`（`generate_title_pages.py` 255〜279行）は sheets.py を通らず、title_pages・jpml_pros・jpml_test が読む「タイトル」「タイトル戦」「連盟プロ以外」「別名」にフィルタの検知（#432）が掛かっていない（再試行も無い）。`--check` の3本目に入れるか、別の issue にするか
  - ask_data は live_pages の Warnings を作るが `report()` を呼ばず、除外した行が出ない（`generate_ask_data.py` 128行）。`--check` の4本目で警告として出すか
- 未確認の項目: なし（スクリプトは実行せず、コードを読んで洗い出した。行番号は cloudflare 915bafa2 のもの）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 102f629d）: https://github.com/retroeater/mj-logs/tree/main/guide/102f629d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/102f629d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
