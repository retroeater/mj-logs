# CHAT-1011-SNM-01

- 着手日時: 2026-10-12
- 対象issue: #514, #327
- ブランチ: work/1011-snm
- 着手時HEAD: 28649cd5

## 指示

【Claude作成】Claude Code 向け指示：「プロ」「連盟プロ以外」の SNS の旧列をシートから消せる状態かを、コードとシートの実物で確かめる（調査のみ。コードもシートも変えない） Chat-Ref: CHAT-1011-SNM-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-snm の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-snm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-snm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「プロ」シートに残る SNS の6列（X ID・X画像・note ID・note画像・YouTube ID・YouTube画像）と、「連盟プロ以外」の X ID・X画像URL（以下まとめて「旧列」）を、シートから無くしても生成・毎日の更新・検査が止まらず、値も失われないかを確かめる。結果から、コードを直す指示と、平野さんが旧列を消す手順を作る。 この指示で変わるのはこのログ（と docs/decisions/）だけ。コード・生成物・シート・SNS ブック・issue の本文／状態／ラベルは変えない。
決定（2026-10-11、平野さん）

* 「プロ」に残る SNS の6列を SNS ブックへ寄せきる作業（SNS 移行の完了）を進める

前提（チャット側。平野さんの決定ではない）

* 識別子 SNM は新しいチャット（SNS 移行の完了）のもの。XAP（#514）・RDN（#536）のチャットの続きを受ける
* 旧列を「列ごと消す」か「値を空にして列は残す」かは未決（docs/decisions/saikyo.md の 2026-10-10〈CHAT-1010-XAP-05〉は「値を空にし、列は残す」。チャットで平野さんに確認中）。この調査は、どちらになっても使える形で報告する（手順2の A・B）
* 次はログ・文書にこう書いてあること。このチャットはコードを読んでいない（すべて要確認）
   * 「プロ」の見出しは13列（登録名・ソートキー・出身地・X ID・X画像・note ID・note画像・YouTube ID・YouTube画像・最終更新・表示・鳳凰Ampai・桜花Ampai）、1,099行（CHAT-1011-RDN-12 のログ「経過」）
   * ページの生成と写真の検知は、SNS の値（X・note・YouTube の ID と画像）を `scripts/lib/sns.py` で SNS ブックから読む（CHAT-1011-XAP-09 のログ「経過」、docs/notes/sns-book.md「生成の読み方」、CHAT-1011-RDN-13 のログ `## 報告` の表）
   * `scripts/update_sns_book.py` は `pro_sheet.fetch_pros` に 登録名・ソートキー・XID・X画像・noteID・note画像・YouTubeID を渡している（CHAT-1011-RDN-08 のログ「経過」の表）。毎日の更新が使うのは名前と名前かなだけで、旧列の値は init と check の確かめにだけ使う（CHAT-1011-XAP-09 のログ `## 報告`）
   * `pro_sheet.fetch_pros` は、渡された見出しがシートに無いと ValueError で止まる（CHAT-1010-RDN-04 のログ「経過」）。ここから、旧列を列ごと消すと `update_sns_book.py` の毎日の更新が止まる見込み。`lib/sns.py` の `fetch_pros()`・`others()` が旧列の見出しを「プロ」「連盟プロ以外」の読み取りに渡しているか（渡していれば全ページの生成が止まる）は分からない
   * 「連盟プロ以外」は 765行（CHAT-1011-XAP-09 のログ「経過」手順1）
   * YouTube画像（N列）を読むコードは無い（CHAT-1011-RDN-12 のログ「経過」の #327 へのコメントの記述）
   * 2026-10-11 から、ID の直しは SNS ブックの【2】で行い、旧列は直さない運用（CHAT-1011-XAP-09 のログ `## 報告`）
* 使う skill は無い

手順

1. 状態と読む所の洗い出し
   * #514・#327 の Open/Closed、本文に残っている作業・期日、直近のコメントの要旨をログに書く。#514 に着手中のコメントを書く（本文・状態・ラベルは変えない。Closed なら書かずにログにそう書いて進む）
   * `git branch -r --no-merged origin/cloudflare` の一覧を出し、`scripts/lib/pro_sheet.py`・`scripts/lib/sns.py`・`scripts/lib/sns_book.py`・`scripts/update_sns_book.py` か、旧列を読む所を変えているブランチがあれば、ブランチ名と変えている関数を書く（止まらない）
   * リポジトリ全体（`scripts/`・`scripts/tests/`・`.github/workflows/`・ページ側の JS・`docs/`）を、旧列の見出し（改行あり・なしの両方）と `pro_sheet` の定数名、「連盟プロ以外」の X ID・X画像URL の見出しで grep し、表にする: 読む所（ファイル・関数）／渡す・読む見出し／通る場面（ページの生成・毎日の更新・init・check・写真の検知・カレンダーの同期・テスト・文書の記述）／その値を何に使うか（使っていないなら「使わない」）
2. 旧列が無いときの模擬（リポジトリのファイルは変えず、コミットしない。読み取りの差し替えの方法は任せる）
   * 生成と同じ経路で読んだ「プロ」「連盟プロ以外」の表を元に、次の2通りを作る。A: 旧列を列ごと落とした表（見出しも無い）。B: 見出しは残し、旧列の値を全行空にした表
   * 差し替えなし → A → B の順に、`python3 scripts/regenerate.py --list` の全ページを1ページずつ続けて生成し、差し替えなしとの差を比べる（CHAT-1011-RDN-08 と同じ比べ方）。ページごとに「通る・差 0」「通るが差あり（どの値が変わったか）」「止まる（箇所とエラー文）」を表にする
   * `update_sns_book.py` の毎日の更新（dry-run。X API を呼ばず、書き込まない）と check も、A・B で同じように確かめる。手順1の表にあるそのほかのスクリプト（名簿の検査・誕生日・道場部ゲスト・写真の検知・`fetch_youtube_channels.py` など）は、書き込み・X API・YouTube API・カレンダー・issue への書き込みを伴わない形で動かせるものだけ A・B で動かす。鍵が無い・書き込みを伴うなどで動かせないものは、コードを読んで「止まる・止まらない」を判定し、読んで判定したと書く
   * A・B のそれぞれで、直す必要のあるコードの箇所（ファイル・関数・何を変えるか）と、古くなる文書の記述（ファイルと節）を一覧にする。直さない
3. 値の照合とシートの数式
   * 「プロ」の在籍者について、旧列と SNS ブックを名前で突き合わせる。ID（X・note・YouTube）は【2】、画像（X・note・YouTube）は実効の値（【3】に【4】の空でないセルを重ねたもの）と比べる。【2】の「-」は空として扱う。SNS・ID／画像ごとに、(a) 一致 (b) SNS ブックにだけ値がある (c) 両方に値があり違う (d) 旧列にだけ値がある、の件数を表にする。(c) は【3】の状態ごとの内訳を書く。(d) と、ID の (c) は、名前と両方の値の一覧を書く（旧列を消すと失われる値・どちらが正か決める値）
   * 「連盟プロ以外」の X ID・X画像URL も同じ形で突き合わせる
   * 「プロ」のあるブックと「連盟プロ以外」のあるブックを xlsx でエクスポートし（CHAT-1011-RDN-10 と同じ方法。数式は `data_only=False`、配列数式を含む）、旧列の今の列の英字、旧列のセル自体に数式が入っているか、同じタブのほかの列・ほかのタブの数式が旧列を引いているか（タブ・セルの範囲・数式の例・件数）を書く。今ある `#REF!` の件数と場所も書く（消す前の基準）。エクスポートが取れなければ、取れなかったと書く（別の手段は試さない）

止まる条件

* 「プロ」の行数が 1,000〜1,300 を外れる、「連盟プロ以外」の行数が 700〜850 を外れる、どちらかが2回の読みで変わる（件数を書いて止まる）
* SNS ブックの【2】【3】【4】が読めない、【2】で名前が重複している
* #514 に、XAP（CHAT-1010-XAP-02〜CHAT-1011-XAP-10）以外の Chat-Ref の着手中コメントがある
* コード・生成物・シート・SNS ブックを変える、X API を呼ぶ、本番のワークフローを手動実行する必要が出た（行わずに止まる）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の表（読む所）、手順2の表（ページ・スクリプトごとの A・B の結果）と直す箇所の一覧、手順3の表（件数）・名前の一覧・数式の結果が、ログの「経過」にある
* ログの `## 報告` の項目の後ろに、結果の要約を書く（CHAT-1011-RDN-13 のログと同じ置き方）: A で止まるもの・B で止まるか差が出るもの、直す箇所の一覧、旧列にだけ値がある人の数と名前、旧列を引く数式の有無、#514・#327 の状態
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SNM-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SNM-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-12）

- 0.: このログの「指示」欄の末尾は、指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 識別子 SNM: `git fetch --unshallow` の後、全ブランチのコミット（`CHAT-[0-9]{4}-SNM-`）と `docs/logs/` の履歴に 0 件。同じ Chat-Ref のコミットも 0 件
- 作業ブランチ: `work/1011-snm` はローカル・リモートとも無かったため `git checkout -b work/1011-snm origin/cloudflare`（28649cd5）
- 実装を含まない指示（調査のみ）のため、0章ゲートは「未マージのブランチとの重なり」だけ手順1で見た

### 手順1: 状態

**#514**（最強戦の選手写真の自動解決）: Open。ラベル 状況: 待ち・分野: 自動化・対象: saikyo。本文の期日なし。
本文の「やること」（案 A・B・確かめの入口）は CHAT-1007-PHT-09 で実装済み、その後 X API への切り替え（XAP-03）・SNS ブック（XAP-05〜06）・
生成と検知の読む先の切り替え（XAP-09）まで進んだ。閉じる条件は「次に写真が切れた検知で表示を確かめてから決める」（PHT-13 のコメント）。
直近のコメントは CHAT-1012-XAP-11（10/12）: 10/12 04:10 の update-sns-book.yml の失敗は帰り道の手順の権限で既に直っている。
あわせて「YouTube画像は誰も読まない。XID・X画像・noteID・note画像・YouTubeID と『連盟プロ以外』の X ID・X画像URL は
`update_sns_book.py` の `load_sources()` が update でも見出しの存在を求めるため、列を消す前に直す必要がある」。

**#327**（「プロ」シートの YouTube画像〈N列〉の廃止）: Open。本文の期日 2026-10-13（10/5・10/12 の週次を見て判断）。
直近のコメントは CHAT-1011-RDN-12（10/11）: #536 が閉じ、N列の削除に進める。G・H 列は平野さんが削除済み。N列を読むコードは無い。

**#514 の着手中コメントと止まる条件**: 止まる条件の範囲（CHAT-1010-XAP-02〜CHAT-1011-XAP-10）の外の着手中コメントが2件ある。
CHAT-1007-PHT-09（10/07）と CHAT-1012-XAP-11（10/12）。どちらも直後に結果のコメントがあり、PHT-09 は完了、XAP-11 はログの `## 報告` が「状態: 完了」で
cloudflare にマージ済み（7c73600b）。並行して作業中のセッションは無いと判断し、調査のみの指示なので止まらずに進めた（`## 報告` の「判断が必要なこと」に書いた）。
着手中のコメントを書いた: https://github.com/retroeater/mj/issues/514#issuecomment-6116705847

**未マージのブランチ**（`git branch -r --no-merged origin/cloudflare`）: work/1009-swp-526・work/1011-snm（このブランチ）・work/1011-swp-hnd・
work/1011-swp-nav・work/1012-rgn。どれも `scripts/lib/pro_sheet.py`・`scripts/lib/sns.py`・`scripts/lib/sns_book.py`・`scripts/update_sns_book.py` を変えていない
（`git diff --name-only origin/cloudflare...<branch>`）。旧列を読む所（手順1の表のファイル）を変えているかは手順1の表の後に追記する。

**行数**（gviz の `fetch_table`、2回読んだ。2回とも同じ）: 「プロ」1,099行（「表示」Y も 1,099）、「連盟プロ以外」739行（名前のある行 739）。
指示文の前提の 765 と違う（範囲 700〜850 の中なので止まらない。減った理由は見ていない）。

実際の見出し:
- 「プロ」: `登録名\n0.74`・`ソートキー`・`出身地`・`X\nID`・`X\n画像`・`note\nID`・`note\n画像`・`YouTube\nID`・`YouTube\n画像`・`最終更新`・`表示`・`鳳凰\nAmpai`・`桜花\nAmpai`（13列。前提どおり）
- 「連盟プロ以外」: `名前`・`名前かな`・`所属団体`・`所属補足`・`X ID`・`X画像URL`（6列）

### 手順1: 旧列の見出しで読む所（grep の結果）

grep の対象: `scripts/`・`scripts/tests/`・`.github/workflows/`・ページ側の JS（ルートの `*.js`・`assets/*.js`、vendor を除く）・`docs/`（logs を除く）。
見出しは改行あり（`X\nID` など、シートの実物）・なし（`XID`・`X画像`・`noteID`・`note画像`・`YouTubeID`・`YouTube画像`）、
`pro_sheet` の定数名（`X_ID`・`X_IMAGE`・`NOTE_ID`・`NOTE_IMAGE`・`YOUTUBE_ID`・`YOUTUBE_IMAGE`）、「連盟プロ以外」の `X ID`・`X画像URL`、`live.OTHER_HEADERS`。
ページ側の JS で「プロ」のブック（1h4-…）を読むのは houou_results.js・ouka_results.js・wrc_results.js・league_ranking.js で、タブは「鳳凰」「桜花」「JWRC」とリーグのタブ（「プロ」ではない）。
「連盟プロ以外」のブック（10g_…）を読む JS は無い。未マージのブランチ5本は、下の表のファイルをどれも変えていない。

| 読む所（ファイル・関数） | 渡す・読む見出し | 通る場面 | 値の使い道 |
|---|---|---|---|
| `scripts/lib/pro_sheet.py` 定数 `X_ID`〜`YOUTUBE_IMAGE`・`column_indexes()`・`fetch_pros()` | 呼び手が渡した見出し。無いと ValueError | 下の各行 | 定義だけ |
| `scripts/lib/sns.py` `PRO_FIELDS`・`fetch_pros()` | 旧列の6見出しを呼び手から受けるが、`pro_sheet.fetch_pros()` には**渡さない**（登録名とほかの見出しだけ） | ページの生成・誕生日・道場部・`fetch_youtube_channels.py`・写真の検知 | 使わない（値は SNS ブック） |
| `scripts/lib/sns.py` `others()` | 受けた dict の 名前・所属団体・所属補足だけ | 下の `live.OTHER_HEADERS` の行 | X ID・X画像URL は使わない（SNS ブック） |
| `scripts/lib/live.py` `OTHER_HEADERS`（名前・所属団体・所属補足・**X ID・X画像URL**）→ `lib/sheets.fetch_records()` の必須の見出し | X ID・X画像URL | `generate_books_pages.load_name_book()`・`generate_houou_race.load_name_book()`（houou_pages も呼ぶ）・`generate_saikyo_pages.load_name_book()`（`load_rows()` 経由で `collect_saikyo_images.py`・`check_saikyo_unregistered.py` も）・`generate_live_pages.load_name_book()`（`write_live_channel_candidate.py` も）・`generate_ask_data.load_name_book()` | 使わない（`sns.others()` が捨てる。ask_data は空文字を入れる） |
| `scripts/generate_title_pages.py` `EXPECTED_HEADERS["連盟プロ以外"]`（…・**X ID・X画像URL**）→ `fetch_tab()`・`column_order()` | X ID・X画像URL（無いと GenerationError） | title_pages の生成・`check_unused_names.py` の `load_registrations()`（`title.fetch_tab()` を呼ぶ） | `check_bad_chars()`（置換文字・制御文字の検査）だけ。生成の値は `main()` で SNS ブックの値に置き換える |
| `scripts/update_sns_book.py` `load_sources()` | 「プロ」: 登録名・ソートキー・XID・X画像・noteID・note画像・YouTubeID（`pro_sheet.fetch_pros()` に直接）。「連盟プロ以外」: 名前・名前かな・X ID・X画像URL（`OTHER_HEADERS`、`fetch_records()`） | 毎日の更新・init・check | update は名前・名前かなだけ（`_values` を捨てる）。init は【2】【3】へ写す元。check は `verify_init()` の照合 |
| `generate_jpml_pros.py` `COLUMNS`・`generate_books_pages.py`/`generate_houou_race.py`/`generate_saikyo_pages.py`/`generate_live_pages.py` `PRO_COLUMNS`・`generate_title_pages.py` `PROS_COLUMNS`・`generate_jpml_test.py` `PRO_COLUMNS`・`lib/wayhome.py` `PRO_COLUMNS`（wayhome_episodes・video_wayhome）・`lib/birthdays.py`・`sync_dojo_calendar.py` `PRO_COLUMNS`・`fetch_youtube_channels.py` `collect_channel_ids()` | XID・X画像・noteID・note画像・YouTubeID を `sns.fetch_pros()` に渡す | ページの生成・誕生日・道場部・YouTube のチャンネル取得 | SNS ブックの値（旧列は読まない） |
| `generate_ask_data.py`・`generate_houou_leagues.py`・`generate_houou_pages.py`・`generate_ouka_leagues.py`・`generate_resource_dictionary.py`・`check_meibo.py`（`pro_sheet.fetch_pros()`）・`check_wayhome.py`（`sns.fetch_pros()`） | 登録名（とソートキー）だけ | 生成・名簿の検査・帰り道の検査 | 旧列に関係しない |
| テスト `test_pro_sheet.py`・`test_sns.py`・`test_jpml_pros.py`・`test_wayhome_player_links.py`・`test_sns_book.py`・`test_saikyo_pages.py`・`test_x_images.py` | 定数・固定の見出し（シートは読まない） | テスト | `test_sns.py` は旧列が `pro_sheet` に渡らないこと、`test_jpml_pros.py` は YouTube画像を読まないことを確かめる。シートの列を消しても結果は変わらない（定数を消すと直す） |
| `.github/workflows/check-saikyo-unregistered.yml` 137行 | 文言「名前・所属団体・所属補足・X ID・X画像URL」 | 未登録の知らせ | 文書の記述（入力の案内） |
| `.github/workflows/check-image-links.yml` | 文言の X ID（SNS ブックの【2】を案内） | 写真の検知 | 文書の記述（直さなくてよい） |
| `docs/notes/sns-book.md` 冒頭・「生成の読み方」 | 「旧列は読まない（数日後に値を空にする。列は残す）」 | 文書 | 文書の記述 |
| `docs/notes/title-pages.md` 100〜104行・`docs/notes/live-page-design.md` 24・97〜98・269〜270行・`docs/notes/houou-race.md` 49行・`docs/notes/static-generation.md` 367行・`docs/notes/books-covers.md` 93行・`docs/notes/saikyo-page-design.md` 40〜41・563〜564行 | 「連盟プロ以外」の必要な見出しに X ID・X画像URL、「プロ」の XID・X画像を読む、など | 文書 | 文書の記述（下の「古くなる文書」） |

### 手順2: 模擬の方法

- scratchpad に `/home/user/mj` のクローンを3つ（base・A・B）作り、28649cd5 を detach で出した（102f629d まで scripts の差は無い）。リポジトリの作業ツリーは触っていない
- `PYTHONPATH` の `sitecustomize.py` で `urllib.request.urlopen` を包み、docs.google.com の GET を記録（base）・再生（A・B）する。A・B では gviz の `SELECT *` のうち
  「プロ」（1h4-…）の旧列6列と「連盟プロ以外」（10g_…）の X ID・X画像URL を、A は列ごと落とし（`cols` と各行のセル）、B はセルを全行 null にした。
  `SELECT COUNT(A)`（フィルタの検知）は差し替えない。title の `fetch_tab()` も `urlopen` を通るので同じく差し替わる。再生でキャッシュに無かった URL は 0 件
- ページごとに base → A → B の順で `python3 scripts/regenerate.py <ページ>` を続けて実行し、生成物を `diff -rq`（`.git`・`__pycache__` を除く）で比べた。
  base の生成物は 28649cd5 のファイルと同じだった（3つのクローンとも `git status` が空。止まったページは `regenerate.py` が戻すため）

### 手順2: ページごとの結果（23ページ）

| ページ | A（列ごと落とす） | B（値を空にする） |
|---|---|---|
| ask_data | 止まる: `generate_ask_data.load_name_book()` → `fetch_records()`「連盟プロ以外」シートの見出しが不正です(欠けている: ['X ID', 'X画像URL']…) | 通る・差 0 |
| books_pages | 止まる（base も同じ: 「書籍」シートの行数が想定外です(95行)）。旧列の判定は下の「読んで・関数で判定」 | 止まる（base と同じ理由） |
| houou_leagues | 通る・差 0 | 通る・差 0 |
| houou_pages | 止まる: `main()` → `generate_houou_race.load_name_book()` → `fetch_records()`（同じ文言） | 通る・差 0 |
| houou_race | 止まる: `load_name_book()` → `fetch_records()`（同じ文言） | 通る・差 0 |
| jpml_pros | 通る・差 0 | 通る・差 0 |
| jpml_test | 通る・差 0 | 通る・差 0 |
| live_pages | 止まる: `load_name_book()` → `read_sheet()` → `fetch_records()`（同じ文言） | 通る・差 0 |
| ouka_leagues | 通る・差 0 | 通る・差 0 |
| resource_dictionary | 通る・差 0 | 通る・差 0 |
| resource_efficiency | 通る・差 0 | 通る・差 0 |
| resource_logs | 通る・差 0 | 通る・差 0 |
| rh_paifu | 通る・差 0 | 通る・差 0 |
| rh_results | 通る・差 0 | 通る・差 0 |
| rh_results_detail | 通る・差 0 | 通る・差 0 |
| saikyo_mens | 通る・差 0 | 通る・差 0 |
| saikyo_pages | 止まる: `main()` → `load_rows()` → `load_name_book()` → `fetch_records()`（同じ文言） | 通る・差 0 |
| title_pages | 止まる: `column_order()`「連盟プロ以外」の見出しが不正です(欠けている: ['X ID', 'X画像URL'] / 重複: なし) | 通る・差 0 |
| video_en | 通る・差 0 | 通る・差 0 |
| video_live | 通る・差 0 | 通る・差 0 |
| video_mtsuku | 通る・差 0 | 通る・差 0 |
| video_wayhome | 通る・差 0 | 通る・差 0 |
| wayhome_episodes | 通る・差 0 | 通る・差 0 |

A で止まる7ページ（books_pages を含む）はすべて「連盟プロ以外」の X ID・X画像URL が原因。「プロ」の旧列6列を落としたことで止まったページ・差が出たページは無い（jpml_pros・jpml_test・帰り道の2ページなど、`sns.fetch_pros()` を通るページは A でも差 0）。

### 手順3: 値の照合（2026-10-12、gviz で読んだ）

- SNS ブック: 【2】1,864行（見出し シート・名前・名前かな・X・note・YouTube・備考・状態。状態「一覧に無い」26行）、【3】1,864行、【4】は見出しだけで 0行。
  【2】の名前の重複は 0。【4】に値のある行は 0 なので、画像の実効の値は【3】の値そのもの
- 「プロ」は在籍者（「表示」が Y）1,099名、「連盟プロ以外」は名前のある 739行。どちらも全員が【2】に名前で見つかった（【2】に無い 0）
- 数え方: (a) 両方に値があり同じ (b) SNS ブックにだけ値 (c) 両方に値があり違う (d) 旧列にだけ値。両方空は「空」。【2】の「-」は空。画像は【3】の値をそのまま比べた（ID が空でも画像を消す処理〈`sns.build()`〉はしない）

| シート | 列 | (a) 一致 | (b) SNS ブックだけ | (c) 違う | (d) 旧列だけ | 両方空 | (c) の【3】の状態 |
|---|---|---|---|---|---|---|---|
| プロ | XID ↔【2】X | 855 | 0 | 0 | 0 | 244 | - |
| プロ | noteID ↔【2】note | 204 | 0 | 0 | 0 | 895 | - |
| プロ | YouTubeID ↔【2】YouTube | 82 | 0 | 0 | 0 | 1,017 | - |
| プロ | X画像 ↔【3】X画像URL | 841 | 0 | 14 | 0 | 244 | 解決 14 |
| プロ | note画像 ↔【3】note画像URL | 204 | 0 | 0 | 0 | 895 | - |
| プロ | YouTube画像 ↔【3】YouTube画像URL | 0 | 0 | 82 | 1 | 1,016 | 解決 82 |
| 連盟プロ以外 | X ID ↔【2】X | 203 | 0 | 0 | 1 | 535 | - |
| 連盟プロ以外 | X画像URL ↔【3】X画像URL | 159 | 43 | 1 | 0 | 536 | 解決 1 |

**(d) 旧列にだけ値がある（旧列を消すと失われる値）: 2名**

| シート・列 | 名前 | 旧列の値 | SNS ブック | 【3】の状態 | 見立て |
|---|---|---|---|---|---|
| プロ・YouTube画像 | 篠原冴美 | `https://assets.st-note.com/production/uploads/images/27792589/profile_c5c29492….jpeg?…`（note の画像の URL） | 【2】YouTube は空 | 未入力 | YouTube の ID が無い行に note の画像が入っている。誤入力とみられ、YouTube画像はどのコードも読まない |
| 連盟プロ以外・X ID | 村田昴 | `https`（URL の切れ端） | 【2】X は「-」（アカウントなし・確認済み） | アカウントなし(確認済み) | ID として使えない値。【2】の「-」のほうが確かめた値 |

**ID の (c): 0 件**（どの SNS も、旧列と【2】の ID が食い違う人はいない）。

画像の (c) の内訳（参考。どちらが正かは【3】の状態が「解決」なので SNS ブック〈X API で取り直した値〉とみられる）:
- プロ・X画像 14名: 青木惇・天野ヨシアキ・石原和輝・さくら美緒・武田雛歩・田中光太・塚本将之・萬代進一郎・久山浩司・平野よしつね・深谷勇太・外間完樹・森東賢一・山田蓮。
  旧列はすべて `_80x80` の URL（武田雛歩だけは同じ画像の大きさ違い、ほかの13名は画像の ID も違う）、【3】は `_400x400`
- プロ・YouTube画像 82名（全員）: 旧列は `yt3.googleusercontent.com`（53）・`yt3.ggpht.com`（29）の `s160`・`s48` など、【3】は `data/youtube_channels.json` の `yt3.ggpht.com` の `s88`。値の出どころが違うため全員が違う（YouTube画像は誰も読まない）
- 連盟プロ以外・X画像URL 1名: ヒデオ銀次（旧列 `…/2096757365509173248/Lf7UktBr_400x400.jpg`、【3】`…/2108120648459640832/s-Mt8_gD_400x400.jpg`。XAP-03 で X API が解決した人）

### 手順3: シートの数式（xlsx エクスポート）

- 正本（1h4-…、200・3,133,735 バイト）と /live 用（10g_…、200・269,028 バイト）を `export?format=xlsx` で scratchpad の別々の新しいフォルダに保存し、openpyxl で読んだ（数式は `data_only=False`、`ArrayFormula` を含む。値の `#REF!` は `data_only=True`）
- 旧列の今の列の英字: 「プロ」は **D（XID）・E（X画像）・F（noteID）・G（note画像）・H（YouTubeID）・I（YouTube画像）**（見出しは A 登録名・B ソートキー・C 出身地・D〜I・J 最終更新・K 表示・L 鳳凰Ampai・M 桜花Ampai。1,100行×13列）。
  「連盟プロ以外」は **E（X ID）・F（X画像URL）**（A 名前・B 名前かな・C 所属団体・D 所属補足。740行×6列）
- 旧列のセル自体の数式: どちらも 0
- 旧列を引く数式: **0 件**。数式の字句（openpyxl の Tokenizer）の範囲の参照を全タブで見て、対象のタブの旧列にかかる参照（`D2`・`$E:$E`・`A:Z` のような旧列を含む範囲・行全体の参照を含む）を数えた。文字列の中にタブ名を書く数式（`INDIRECT`・`QUERY` など）も 0
  - 正本は数式 49,788（うち配列数式 18,204）。「プロ」を引く参照は「鳳凰」32,012・「桜花」1,719・「JWRC」1,507 で、すべて `'プロ'!$A:$A`（登録名）。「プロ」の中の数式は L・M 列の `XLOOKUP($A2,'鳳凰Ampai'!…)` など（同じタブの参照は $A だけ）
  - /live 用は数式 52。「連盟プロ以外」の中は B 列の `=A102` など4件（名前かなの仮置き）で、旧列を引かない。ほかのタブから「連盟プロ以外」を引く数式は無い
- 今ある `#REF!`（消す前の基準）: 正本・/live 用とも、数式の中 0・値 0

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
