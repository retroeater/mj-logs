# CHAT-1012-SNM-02

- 着手日時: 2026-10-12
- 対象issue: #514, #327
- ブランチ: work/1011-snm
- 着手時HEAD: e9542807

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の SNS の6列と「連盟プロ以外」の X ID・X画像URL を列ごと消しても止まらないよう、読む所を直してマージする（#514。CHAT-1011-SNM-01 の続き。シートの列はまだ消さない）
Chat-Ref: CHAT-1012-SNM-02
マージ: 承認済み（チャットで。2026-10-12 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認）。CHAT-1011-SNM-01 のログも一緒に入れる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-snm の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01 で使ったもので、重複ではない。
作業ブランチ: クラウドセッションで実行する。未マージの work/1011-snm を続けて使う（CHAT-1011-SNM-01 のログのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1011-SNM-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に ` / 続き: CHAT-1012-SNM-02` を足す。

## 目的
SNS の値はすでにすべて SNS ブックから読んでいる。シートに残る旧列（「プロ」の X ID・X画像・note ID・note画像・YouTube ID・YouTube画像〈今の D〜I〉と「連盟プロ以外」の X ID・X画像URL〈今の E・F〉、計8列）を平野さんが列ごと消せるように、旧列の見出しを求めて止まる所を直し、マージする。シートの列を消すのは、このマージの後に平野さんが行う（確かめは次の指示）。

### 決定（2026-10-12、平野さん）
- 旧列の扱いは、列ごと消せるものは列ごと消す。「連盟プロ以外」の X ID・X画像URL も含める（2026-10-10〈CHAT-1010-XAP-05〉の「値を空にし、列は残す」と、2026-10-10〈CHAT-1011-XAP-09〉の「旧列を空にするのは切り替えのマージの数日後」を置き換える）
- 消す時機は「一度パスしていれば」すぐ。SNS ブックの毎日の更新・全ページの再生成は 10/12 にページの生成まで通っている（失敗の表示は帰り道の手順によるもの。CHAT-1012-XAP-11・CHAT-1012-WHS-15）ので、このマージの後に消す。#327（N列「YouTube画像」の削除）も同じときに消す
- XAP のチャットが判断待ちで持っていた「旧列を空にする指示」（CHAT-1011-XAP-09 の `## 報告`）は、このチャット（SNM）が引き取る
- CHAT-1011-SNM-01 で #514 にあった CHAT-1007-PHT-09・CHAT-1012-XAP-11 の着手中コメントは、どちらも完了済みで並行作業ではないので、調査を進めた判断でよい。SNM-01 のログはこの指示で一緒にマージする
- このマージは、下の「止まる条件」の確かめが通れば承認する

### 前提（チャット側。平野さんの決定ではない）
- 直す箇所は CHAT-1011-SNM-01 のログ `## 報告`「直す箇所（A）」: `scripts/lib/live.py` の `OTHER_HEADERS`、`scripts/generate_title_pages.py` の `EXPECTED_HEADERS["連盟プロ以外"]` と `main()` の差し込みの位置、`scripts/update_sns_book.py` の `load_sources()`（と init・check の照合）。着手時に grep し直し、SNM-01 の表と食い違えば書く（増えていたら同じ扱いで直してよい）
- 直した後のコードは、旧列が残っている今のシートと、消した後のシートの両方で動くこと（マージが先、列の削除が後のため）。旧列の見出しを読む・求める所を無くす形にする
- `update_sns_book.py` の init は 2026-10-10 に実行済みで使わない（docs/notes/sns-book.md「手動実行」）。init は旧列を読まずに「使えない（旧列は廃止）」と出して終える形にし、check は旧列との照合をやめる（タブ・見出し・行数・編集者の確かめは残す）。**`.github/workflows/` は変えない**（`update-sns-book.yml` の mode の選択肢はそのまま）
- SNM-01 の模擬（`## 経過` の手順2）で、A（8列を落とした表）で止まったもの: ページ7（ask_data・books_pages・houou_pages・houou_race・live_pages・saikyo_pages・title_pages）、`collect_saikyo_images.py`・未登録の検査・`write_live_channel_candidate.py`、`update_sns_book.py` の毎日の更新・check・init。`check_unused_names.py` は「検査できなかった」と知らせる。books_pages は直す前から「書籍」の行数で止まる（凍結中、差し替えなしでも同じ）
- 旧列にだけ値がある2名（篠原冴美・村田昴）はどちらも誤入力とみられ、写さない（SNM-01 の `## 報告`）
- 10/12 に #551（RGN）が `scripts/regenerate.py --check` と `lib/check.py` を入れた（CHAT-1012-RGN-10）。RGN は今後 title 系などの `--check` を直す予定。重なりは手順1で確かめる
- 使う skill は無い

## 手順
1. 確かめ: #514 に他セッションの着手中コメント（完了していない作業のもの）が無いこと。`git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか。#514 に着手中のコメントを書く
2. 直す: 上の箇所を直し、旧列の見出しを求める所が残っていないことを grep で確かめる（テストに「旧列の見出しを読み取りに渡していない」検査を足す。`pro_sheet` の X_ID などの定数は、使う所が無くなれば消すか `RETIRED` 相当に移す。置き方は任せる）。既存のテストを直し、全部通す。文書を直す: SNM-01 が挙げた `docs/notes/sns-book.md`・`title-pages.md`・`live-page-design.md`・`houou-race.md`・`books-covers.md`・`static-generation.md`、`check-saikyo-unregistered.yml` の案内の文（ワークフローの中の文なら変えずに、ログに「変えていない」と書く）。「旧列は数日後に空にする・列は残す」の記述は「旧列は列ごと消す（2026-10-12 の決定。平野さんが削除し、CHAT-1012-SNM-03 で確かめる予定）」に置き換える。追記先の今の文を読んでから直す
3. 確かめてマージ: (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる（SNM-01 と同じ比べ方）。(b) SNM-01 の模擬 A（8列を落とした表）と B（値を空にした表）で、直した後の全ページの生成と、`update_sns_book.py` の毎日の更新（dry-run）・check、SNM-01 で A で止まったスクリプト（書き込みを伴わない形で動かせるもの）を動かす。止まる条件に当たらなければ、docs/decisions/saikyo.md に上の「決定」を足し、#514 と #327 に結果をコメントし（閉じない。#327 は「N列は次の列の削除で一緒に消す」）、cloudflare へマージする

## 止まる条件
- (a) で、直す前と後の全ページの生成に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる
- (b) で、A・B のどちらかで止まるページ・スクリプトがある、または差し替えなしとの差がある（books_pages が直す前と同じく「書籍」の行数で止まるのは許す）、`update_sns_book.py` の毎日の更新（dry-run）の【1】【2】【3】の行数・状態の数が差し替えなしと違う
- 旧列の見出しを求める所が SNM-01 の表に無い形で見つかり、直し方が上の前提で決まらない
- `.github/workflows/` を変える必要が出た（変えずに止まる）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 「プロ」の行数が 1,000〜1,300 を外れる、「連盟プロ以外」の行数が 700〜850 を外れる、どちらかが2回の読みで変わる
- 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 直した箇所（ファイル・関数・何を変えたか）、grep の結果、テストの結果、(a)(b) の表（ページ・スクリプトごと）、直した文書の一覧がログの「経過」にある
- ログの `## 報告` の項目の後ろに、平野さんが消す列の一覧を書く: ブックとタブ・今の列の英字・見出し（「プロ」の6列〈N列「YouTube画像」を含む〉と「連盟プロ以外」の2列）。マージの時点で読み直した英字を書く
- マージ後の自動再生成（regenerate-page.yml、push）で生成物が変わらない見込み（シートの変化による差は除く）。結果の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-SNM-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-SNM-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-12）

- 0.: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 同じ Chat-Ref のコミットは 0 件。識別子 SNM は同じチャットの SNM-01 のもの（指示文のとおり重複ではない）
- CHAT-1011-SNM-01 の `## 報告` の状態は「判断待ち」だったので、` / 続き: CHAT-1012-SNM-02` を足した（26aae7cc）
- 作業ブランチ: ローカル・リモートとも `work/1011-snm` は e9542807 で一致。`origin/cloudflare`（a4ed3bc2）が祖先でなかったので `git merge origin/cloudflare` で取り込んだ（10296b01、衝突なし。入ったのは RGN-09・RGN-10 の `regenerate.py --check`・`lib/check.py`・`generate_resource_dictionary.py`・`static-generation.md` など）

### 手順1: 確かめ

- #514 の着手中コメント: SNM-01 の後に新しいものは無い（最後は SNM-01 の着手中）。完了していない作業の着手中コメントは無い。着手中のコメントを書いた: https://github.com/retroeater/mj/issues/514#issuecomment-6117256866
- 未マージのブランチ（work/1009-swp-526・work/1011-swp-hnd・work/1011-swp-nav・work/1012-whs-links）は、直すファイル（`scripts/lib/live.py`・`generate_title_pages.py`・`update_sns_book.py`・`lib/pro_sheet.py`・`lib/sns.py`・`scripts/tests/`・直す文書6本）をどれも変えていない（`git diff --name-only origin/cloudflare...<branch>`）
- RGN-10 の `regenerate.py --check`・`lib/check.py` とは触るファイルが重ならない（static-generation.md は別の節）
- grep し直した結果は SNM-01 の表と同じ（旧列の見出しを求めるのは `live.OTHER_HEADERS`・title の `EXPECTED_HEADERS["連盟プロ以外"]`・`update_sns_book.py` の `OTHER_HEADERS` と `load_sources()` の `pro_sheet.fetch_pros()` だけ。ほかは `sns.fetch_pros()` 経由）

### 手順2: 直した箇所（b556c68b）

| ファイル・関数 | 変えたこと |
|---|---|
| `scripts/lib/live.py` `OTHER_HEADERS` | `("名前", "所属団体", "所属補足")` に（X ID・X画像URL を外した）。外した2つは `RETIRED_OTHER_HEADERS` に置き、テストが使う |
| `scripts/generate_title_pages.py` `EXPECTED_HEADERS["連盟プロ以外"]`・`main()` | 必要な見出しを 名前・所属団体・所属補足・名前かな に。`main()` の SNS ブックの値の差し込みを `values[:4] + [x_id, x_image] + values[4:]` に（X の2列が無くなり「その他の列」が5列目から始まるため。旧列が残る間は「その他の列」として後ろに付き、使われない） |
| `scripts/update_sns_book.py` `load_sources()` | 「プロ」は登録名・ソートキー、「連盟プロ以外」は名前・名前かなだけを読み、`(【1】の行, プロの人数, 連盟プロ以外の人数)` を返す（写す値を返さない） |
| 同 `run_init()` | 旧列を読まずに「止めました: init は使えません…」と出して終了コード 1 |
| 同 `run_check()` | 旧列との照合（`verify_init()`）と旧列の件数をやめた。タブ・見出し・行数・編集者か・【2】の入力のある名前・元の一覧の件数・名前かなのある行は残した |
| 同 | 使わなくなった `init_tables()`・`verify_init()`・`ensure_tabs()`・`print_counts()`・状態の文言 `COPIED`・`COPIED_YOUTUBE`・`NOT_FETCHED` を消した。docstring の init の行を直した |
| `scripts/lib/pro_sheet.py` | `SNS_HEADERS`（X_ID〜YOUTUBE_IMAGE）を足し、`fetch_pros()` は SNS の見出しを渡されるとシートを読む前に ValueError（定数は `sns.fetch_pros()` のキーとして残る。コメントで「シートの列ではない」と書いた） |
| `.github/workflows/` | 変えていない。`check-saikyo-unregistered.yml` 137行の案内「（名前・所属団体・所属補足・X ID・X画像URL）」はワークフローの中の文なので変えていない（列を消した後は、X は SNS ブックの【2】に入れる案内に直すのがよい。別の指示で） |

grep（直した後）: `OTHER_HEADERS` は `live.py`（3見出し）と `update_sns_book.py`（名前・名前かな）。`"X ID"`・`"X画像URL"` を見出しとして求める所は `live.RETIRED_OTHER_HEADERS`（テスト用）だけ。`pro_sheet.X_ID` などは `sns.fetch_pros()` に渡す所（生成・誕生日・道場部・YouTube）と `sns.PRO_FIELDS`・`pro_sheet.SNS_HEADERS` だけで、`pro_sheet.fetch_pros()` に渡す所は 0。

**テスト**: `python3 -m unittest discover -s scripts/tests` 850件 OK（直す前の cloudflare〈a4ed3bc2〉は 845件 OK。InitTest 1件を消し、6件足した）。
足したテスト: `test_sns_book.RetiredColumnsTest`（`load_sources()` が旧列を求めない／`live.OTHER_HEADERS` に X の列が無い／init が読まずに 1 を返す）、`test_pro_sheet.test_sns_headers_are_not_read_from_the_sheet`、`test_title_tab_headers.NonProsTabHeadersTest`（X の列があっても無くても同じ値／必要な見出しに無い）。
直す前のコード（a4ed3bc2）に新しいテストファイルを置いて走らせると、足したうちの5件が落ちる（FAIL 1・ERROR 4。`live.OTHER_HEADERS` の1件はその後に足した）。

**直した文書**: `docs/notes/sns-book.md`（冒頭の「数日後に値を空にする。列は残す」→「旧列は列ごと消す（2026-10-12 の決定…CHAT-1012-SNM-03 で確かめる予定）」、「手動実行」の check・init）、
`docs/notes/title-pages.md`（「連盟プロ以外」の行と必要な見出し）、`docs/notes/live-page-design.md`（1-1 の「プロ」の行、1-4 の X ID・X画像URL の2行を削除、出場者の X画像の出どころ）、
`docs/notes/houou-race.md`（画像と X の ID の引き方）、`docs/notes/books-covers.md`（著者の写真）、`docs/notes/static-generation.md`（見出しの例を「鳳凰\nAmpai」に、定数の例を `SORT_KEY` に、SNS の見出しを渡すと止まることを追記）

### 手順3: 確かめ

方法は SNM-01 と同じ（`## 経過`「手順2: 模擬の方法」）。scratchpad に4つのクローン: 直す前 pre（origin/cloudflare a4ed3bc2）・直した後 post（b556c68b）・post に A（8列を列ごと落とす）・post に B（8列の値を全行空にする）。
ページごとに pre（docs.google.com の応答を記録）→ post → postA → postB（記録を再生）の順に `python3 scripts/regenerate.py <ページ>` を続けて実行し、生成物を `diff -rq` で比べた。
A・B の差し替えは「プロ」16回・「連盟プロ以外」7回の `SELECT *` に効いた（`SELECT COUNT(A)` は差し替えない）。再生でキャッシュに無かった URL は 0。4つのクローンとも生成の後の `git status` は空（生成物は a4ed3bc2 のファイルと同じ）。

**(a)・(b) ページ（23ページ）**

| ページ | 直す前 | 直した後 | 直した後＋A | 直した後＋B |
|---|---|---|---|---|
| books_pages | 止まる（「書籍」シートの行数が想定外です(95行)） | 同じ理由で止まる | 同じ理由で止まる | 同じ理由で止まる |
| ほかの22ページ（ask_data・houou_leagues・houou_pages・houou_race・jpml_pros・jpml_test・live_pages・ouka_leagues・resource_dictionary・resource_efficiency・resource_logs・rh_paifu・rh_results・rh_results_detail・saikyo_mens・saikyo_pages・title_pages・video_en・video_live・video_mtsuku・video_wayhome・wayhome_episodes） | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |

SNM-01 で A で止まった6ページ（ask_data・houou_pages・houou_race・live_pages・saikyo_pages・title_pages）は、直した後は A でも通り、差 0。

**(b) スクリプト**（書き込み・X API・YouTube API・カレンダー・issue に触れない形。SNM-01 と同じ包み。差は直す前〈pre〉との比較）

| スクリプト | 直す前 | 直した後 | ＋A | ＋B |
|---|---|---|---|---|
| `update_sns_book.py --dry-run`（毎日の更新） | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |
| `update_sns_book.py --check` | 通る | 通る。差は旧列との照合の7行が無くなっただけ（意図した変更） | post と同じ | post と同じ |
| `update_sns_book.py --init --dry-run` | 終了コード 1（【2】に入力があるため止める） | 終了コード 1（「init は使えません」、シートを読まない） | 同じ | 同じ |
| `collect_saikyo_images.py`（最強戦の写真の検知） | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |
| `check_saikyo_unregistered.py` | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |
| `check_unused_names.py --no-issue` | 通る | 通る。出力の JSON は差 0、標準出力の見出しの行に「(使わない列: ['X ID', 'X画像URL'])」が付く | 通る。JSON 差 0、見出しの行は4列 | post と同じ |
| `check_meibo.py`・`sync_birthday_calendar.py`（`--compare` なし）・`check_wayhome.py` | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |
| 関数: `generate_books_pages`・`generate_live_pages`（`write_live_channel_candidate.py` が使う）・`generate_saikyo_pages`・`generate_houou_race` の `load_name_book()`、`fetch_youtube_channels.collect_channel_ids()`、`sync_dojo_calendar._fetch_roster()` | 通る | 通る・差 0 | 通る・差 0 | 通る・差 0 |

`update_sns_book.py --dry-run` の結果（直す前・後・A・B で同じ）: 【2】1,864行（一覧に無い 26）・変わらない、X API を呼ぶ予定 0、X状態 解決 1,052・未入力 799・リンク切れ・未取得(トークンなし) 12・アカウントなし(確認済み) 1、
note状態 未入力 1,660・リンク切れ(取得しない) 204、YouTube状態 未入力 1,782・リンク切れ 82、ページが使う値が変わった人 0。【1】は書き直しの行が出ない（一覧と同じ）。
（note・YouTube の「リンク切れ」は、この環境から assets.st-note.com・yt3.ggpht.com に HEAD が通らないため。4つとも同じ）
`--check`（直した後）: 【1】1,838行・【2】1,864行（入力のある名前 1,069）、元の一覧 プロ 1,099・連盟プロ以外 739・計 1,838。

止まる条件に当たるものは無い。

**行数と列の英字**（マージの直前に gviz で2回読み、2回とも同じ）: 「プロ」1,099行、「連盟プロ以外」739行。
「プロ」A 登録名・B ソートキー・C 出身地・**D XID・E X画像・F noteID・G note画像・H YouTubeID・I YouTube画像**・J 最終更新・K 表示・L 鳳凰Ampai・M 桜花Ampai。
「連盟プロ以外」A 名前・B 名前かな・C 所属団体・D 所属補足・**E X ID・F X画像URL**。
#327 の本文の「N列」は #536 の列の削除の前の英字で、今は I 列（見出し「YouTube画像」）。

### マージ

- push の直前に `git fetch` し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真（cloudflare は a4ed3bc2 のまま）、テスト 850件 OK、作業ツリーは空を確かめて `git push origin work/1011-snm:cloudflare`（a4ed3bc2..61a60278、fast-forward）。CHAT-1011-SNM-01 のログも一緒に入った
- コメント: #514 https://github.com/retroeater/mj/issues/514#issuecomment-6117443064 ／ #327 https://github.com/retroeater/mj/issues/327#issuecomment-6117443499 （どちらも閉じていない）
- マージ後の自動再生成: regenerate-page.yml run 38201636779（push、61a60278）は success。cloudflare に再生成のコミットは足されていない（生成物は変わらなかった）。見込みどおり

## 報告

- 状態: 完了
- ブランチ: work/1011-snm
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-SNM-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-snm
- 確認用URL: なし
- マージ: 済（61a60278）
- issue: #514, #327
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

### 平野さんが消す列（2026-10-12、マージの直前に gviz で読んだ英字）

| ブック・タブ | 列 | 見出し |
|---|---|---|
| 正本「プロ」 | D | X ID（セルの中は「X⏎ID」） |
| 正本「プロ」 | E | X 画像（「X⏎画像」） |
| 正本「プロ」 | F | note ID（「note⏎ID」） |
| 正本「プロ」 | G | note 画像（「note⏎画像」） |
| 正本「プロ」 | H | YouTube ID（「YouTube⏎ID」） |
| 正本「プロ」 | I | YouTube 画像（「YouTube⏎画像」。#327 の旧N列） |
| /live 用「連盟プロ以外」 | E | X ID |
| /live 用「連盟プロ以外」 | F | X画像URL |

列を1本消すと右の列の英字が詰まるので、右から（「プロ」は I→D、「連盟プロ以外」は F→E）消すか、見出しで確かめながら消す。消した後の確かめは CHAT-1012-SNM-03 の予定。
`check-saikyo-unregistered.yml` の案内の文（「名前・所属団体・所属補足・X ID・X画像URL」）はワークフローなので変えていない。消した後は X を SNS ブックの【2】に入れる案内に直すのがよい（別の指示）。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 61a60278）: https://github.com/retroeater/mj-logs/tree/main/guide/61a60278

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/61a60278/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
