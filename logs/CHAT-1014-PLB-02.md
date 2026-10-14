# CHAT-1014-PLB-02

- 着手日時: 2026-10-14
- 対象issue: #567
- ブランチ: work/1014-plb
- 着手時HEAD: 131150c

## 指示

【Claude作成】Claude Code 向け指示：（PLB-01 の続き）「プロ」の A1 の違いを許して読む先を「ryoei.pro_選手」に切り替え、「桜花」「JWRC」を見出しで読む形に直し、全ページの生成結果を比べる Chat-Ref: CHAT-1014-PLB-02 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1014-PLB-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-plb を続けて使う（PLB-01 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1014-PLB-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。判断待ちなら、その状態の末尾に `/ 続き: CHAT-1014-PLB-02` を足す。

目的
PLB-01 の手順2〜4の続き（issue #567）。PLB-01 は「プロ」の見出し A1 の違い（旧「登録名\n0.74」・新「登録名」）で止まった。平野さんがこの違いを許したので、切り替えを進める。
決定（2026-10-14、平野さん）

* 「プロ」の A1 の違い（旧「登録名\n0.74」・新「登録名」）は許して、読む先の切り替えを進める（チャット側が示した案 A）。新しいブックの A1 は直さない
* PLB-01 の「決定」節はそのまま有効（「ryoei.pro_選手」から読む、元の3タブは「(旧)」で残し1週間後を目途に消す、「桜花」「JWRC」の列を消すためのコードの直しを含めてよい）

前提（チャット側。平野さんの決定ではない）

* PLB-01 のログの「経過」の洗い出し（読み手の一覧、`generate_title_pages.py` が1つの ID で4タブを読むこと、「プロ」は `pro_sheet.SPREADSHEET_ID` を変えれば足りること、JS は3タブを読まないこと）を使う。着手時点で洗い出し直し、増えた読み手があればログに書いて含める
* 新旧の値は PLB-01 で一致を確かめた。着手時に同じ比較をもう一度行い、A1 以外に違いが出たら止まる（PLB-01 の後に平野さんが片方だけ直した可能性があるため）
* `pro_sheet.label_keys()` の改行の前で引く処理（#467 の回避）は、この指示では消さない。新しいブックで A1 が1行になったことを #467 にコメントで書く（消すかは後で決める）
* 「桜花」「JWRC」の直し方は PLB-01 の「前提」のとおり（見出しで列を引き、消す予定の列を読まない・必須にしない。列を消す前でも後でも同じ結果）
* 未マージのブランチとの重なり（PLB-01 の経過）: work/1014-skd が `generate_saikyo_pages.py`、work/1014-jpl が `generate_title_pages.py` を変える予定がある。これらのファイルでは読む先を指す行だけを変え、関数の中身には手を入れない
* 一部の読み手だけを切り替えたままマージすると、平野さんが元のタブを改名したときにそこだけ生成が止まる。外した読み手があれば必ず報告に書く

手順

1. 新旧の3タブをもう一度比べ（PLB-01 と同じ方法）、A1 以外の違いが無いことを確かめる。そのうえで、PLB-01 の手順2の残り（読む先の切り替え、案内文・docs/notes の直し）を行う。テストを足すか直し、`python3 -m unittest discover -s scripts/tests` を通す。#567 に進み具合を、#467 に A1 の件をコメントする。
2. PLB-01 の手順3（「桜花」「JWRC」を見出しで読む形に直す。テストとプレビューでの JS のページの確かめを含む）を行う。
3. PLB-01 の手順4（同じ時点で origin/cloudflare のコードと作業ブランチのコードで `python3 scripts/regenerate.py all` を実行し、生成物の差を比べる。見込みは差なし）を行う。

止まる条件

* 手順1で、新旧の3タブに A1 以外の違いがある（切り替えずに、違いを全部書いて止まる）
* 読むたびに行数が変わる
* 手順3で、2つのコードの生成物に差がある（差の種類・件数・対象を書いて止まる。差を直そうとしない）
* 未マージの work/ ブランチが、変える行と同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。対象が複数なら重なる対象だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
* 前提が実物と食い違う

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、切り替えなかった読み手（あれば）と、マージ後に平野さんが行う手作業の順番（改名・削除はマージ後、外した読み手があればそれが切り替わるまで改名しない）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-PLB-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-PLB-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: ログの「指示」欄の末尾は指示文の最後の行と一致。PLB-01 の `## 報告` は「判断待ち」→ 末尾に `/ 続き: CHAT-1014-PLB-02` を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 全部ある
- `CHAT-1014-PLB-02` のコミット: 無し。`work/1014-plb` はリモートにあり、ローカルと一致（131150c）
- `origin/cloudflare` が祖先でなかった → ログ先行 push の後 `git merge origin/cloudflare`（衝突なし。JPL-05 〈`lib/taikai_names.py` など〉が入った）

### 手順1: 新旧の比較と読む先の切り替え

- 比較（PLB-01 と同じ。`fetch_table()`・`fetch_csv()` で旧・新を2回ずつ）: 「プロ」1099行・「連盟プロ以外」745行・「別名」28行、2回とも行数同じ、値の違う行は0。
  違いは「プロ」の A1（旧「登録名\n0.74」・新「登録名」）だけ → 進めた
- 読み手の洗い出し直し: PLB-01 の一覧から増減なし。`check_unused_names.py` の `load_registrations()` が `title.SPREADSHEET_ID` で「連盟プロ以外」「別名」のフィルタを確かめていた（PLB-01 の一覧に無かった。含めた）
- 未マージのブランチ: work/1014-jpl はマージ済みになった。work/1014-skd の `generate_saikyo_pages.py` の変更は 116〜947 行あたりで、今回変えた `load_name_book()`（1140・1142 行）とは別の箇所
- 直し:
  - `lib/pro_sheet.py`: `SPREADSHEET_ID` を ryoei.pro_選手 に
  - `lib/live.py`: `PLAYERS_SPREADSHEET_ID`（= `pro_sheet.SPREADSHEET_ID`。ID を1か所に持つ）・`PLAYER_SHEETS`・`spreadsheet_of()` を足した。`SPREADSHEET_ID` は変えない（「タイトル戦」「リーグ」「辞書」「書籍」などが読む）
  - `fetch_records(live.SPREADSHEET_ID, live.SHEET_OTHERS/ALIASES, …)` を `live.PLAYERS_SPREADSHEET_ID` に: generate_books_pages・generate_houou_race・generate_saikyo_pages（読む先の行だけ）・update_sns_book・generate_ask_data
  - `generate_live_pages.read_sheet()`・`generate_title_pages.fetch_tab()` の URL・`check_unused_names.load_registrations()` は `live.spreadsheet_of(tab)` で引く（generate_title_pages は読む先の1行と `SPREADSHEET_ID = live.SPREADSHEET_ID` だけ）
  - docs/notes: live-page-design.md（1-1 の表）・title-pages.md（データ源）・sns-book.md・static-generation.md（「プロ」シートの項）・houou-top.md。コードのコメント: generate_live_pages・generate_resource_dictionary・lib/names・lib/pro_sheet
  - 案内文: ワークフロー・issue 本文に旧ブックの URL・ID は無かった（grep）
- テスト: `scripts/tests/test_players_book.py`（読む先・title の fetch_tab の問い合わせ先・旧ブックとの組み合わせが戻らないこと）。修正前のコード（origin/cloudflare を git archive で展開）では 4 failures・1 error、修正後は通る。全体 1081 件 OK
- コミット 3874cb9

### 手順2: 「桜花」「JWRC」を見出しで読む

実データ（2026-10-14、`fetch_table()`）: 消す予定の列の値の有無

- 桜花（1719行）: 前後・結果・第9〜13節は全行空。プロは全行に値（Yes/No、読み手なし）
- JWRC（1507行）: 前後・リーグ・リーグキー・第6〜13節は全行空。**結果は259行に値（敗退197・通過62）**、プロは全行に値。
  結果を読むのは league_ranking.js の「連続昇級回数」だけで、ouka_ranking・wrc_ranking のメニューに無い（houou_ranking だけ）。wrc_results.js では結果はコメントアウト済み。
  消すと結果の値は失われる（平野さんの決定どおり。報告に書く）

直し（コミット 4de39ec）:

- `scripts/generate_ouka_leagues.py`: `fetch_sheet(… 'SELECT A,B,C,D,F WHERE F > 0')` → `fetch_table()` と見出し（名前・期・リーグ・順位）で読む `select_rows()`。行の並び `[名前, 期, None, リーグ, 順位]` は旧と同じ（lib/leagues.py・check_leagues_dropped.py の列の位置を変えないため、前後の位置は None）
- `scripts/lib/pro_stats.py`: 「桜花」は「前後」を読まず、行の並び（前後は None）は変えない。「鳳凰」は変えない
- `ouka_results.js`・`wrc_results.js`: houou_results.js と同じ形（`SELECT * LIMIT 1` で見出しを読み、列記号を引き直す）。問い合わせから消す列を外した（桜花 `A,B,D,E,F,H,I〜P`、JWRC `A,B,F,H,I〜M`）。`getValue` の位置を問い合わせに合わせた
- `league_ranking.js`: `resolveHououQuery` を `resolveQuery` に広げ、「鳳凰」「桜花」「JWRC」で引き直す。タブごとの列 `SHEET_COLUMNS`（鳳凰 `A,B,C`・節 I〜U、桜花 `A,B`・節 I〜P、JWRC `A,B`・節 I〜M）。
  節・合計を読むループは問い合わせの列数から（鳳凰は従来と同じ範囲）。引き直しは問い合わせに出る列だけ（消した列を探してコンソールに出さない）

確かめ:

- Python: 旧読み方と新読み方を実データで比較。ouka_leagues 1561行・pro_stats の桜花 1719行とも一致
- テスト `scripts/tests/test_ouka_columns.py`（列を消す前の表と消した後の表で同じ行）。修正前のコードでは 3 errors、修正後は通る。全体 OK
- JS（node）: `getQueryString()` を旧と比べ、鳳凰の9区分は文字列が完全に一致。桜花・JWRC は消す列だけが落ちる
- JS（node、見出しの表を差し替え）: 列を消す前の見出しと消した後の見出しで、引き直した問い合わせが同じ見出しの列を指す（results 2本・ranking 16区分すべて）。消した後もコンソールのエラーは0件

### 手順3: 全ページの生成と JS のページの確かめ

- 着手中に origin/cloudflare が 0d933bb に進んだ（work/1014-skd のマージ。`generate_saikyo_pages.py` の別の箇所）→ `git merge origin/cloudflare`（衝突なし）。今回変えた `load_name_book()` の2行はそのまま
- 生成物の比較: scratchpad に `git clone --shared` で2つ作り、origin/cloudflare（0d933bb）と作業ブランチ（fd4dd90）で `python3 scripts/regenerate.py all` を並べて実行
  （旧は約1分先に始まった）。新の live_pages だけ SNS ブックの読み込み中の通信断（`http.client.IncompleteRead`、変更と無関係）で飛ばされたため、live_pages を両方で同時にもう一度実行（どちらも exit 0）。
  **結果: 生成物の差なし。** 両方とも作業ツリーの変更は0件（コミット済みの生成物と同じ）、`diff -rq` で違うのはソース（scripts・docs・JS 3本）だけ
- JS のページ（ローカルのヘッドレス Chrome）: origin/cloudflare のファイルと作業ツリーを別々の `http.server` で配り、同じページの表の全行の文字と折れ線のSVGの文字を比べた。
  対象は ouka_results・wrc_results（名前なし・名前あり）、ouka_ranking・wrc_ranking の8区分、houou_ranking の9区分（計33ページ）。
  - 1回目で ouka_ranking・wrc_ranking が表示されないことを見つけた（`ReferenceError: Cannot access 'SHEET_COLUMNS' before initialization`。ファイル先頭の `getQueryString()` の呼び出しが後ろの `const` を参照していた。node での確かめは関数だけを取り出していたため拾えなかった）。
    定義を先頭へ移した（コミット 5f6f6a4）。houou_ranking は鳳凰で `getQueryString` が同じ箇所を通るため同じく壊れていたはず（1回目は ranking の比較の途中で止めたため未計測）
  - 直した後: 33ページすべて旧と一致。新の側のエラーは0件（旧の側の1件は手元の旧ファイルに favicon が無い 404）
- プレビュー（Workers Builds、5f6f6a4、check-run 成功）: プレビューの league_ranking.js が手元と一致することを確かめ、旧（手元の origin/cloudflare）とプレビューで同じ33ページを比べた。すべて一致、プレビュー側のエラーは0件
- docs: `docs/notes/static-generation.md` の Google Charts 依存のページの項を直した（コミット 9eb0c1b）

### 外した読み手

なし（「プロ」「連盟プロ以外」「別名」の読み手はすべて ryoei.pro_選手 に切り替えた）

## 報告
- 状態: 判断待ち / 続き: CHAT-1014-PLB-03
- ブランチ: work/1014-plb
- ログ: https://github.com/retroeater/mj/blob/work/1014-plb/docs/logs/CHAT-1014-PLB-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-plb
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: ouka_results・wrc_results・ouka_ranking・wrc_ranking・houou_ranking（見た目は変わらない。旧と同じ表になる）
- マージ: 未（平野さんの判断待ち）
- issue: #567（進み具合をコメント）、#467（A1 の件をコメント）
- 判断が必要なこと:
  - マージの判断（生成物の差なし、JS の33ページは旧と一致。切り替えなかった読み手は無し）
  - マージ後に平野さんが行う手作業（どれもマージの後。先に行うと今の本番のコードが元のタブ・列を読めず、生成やページが止まる）:
    1. 元の3タブ（正本の「プロ」、ryoei.pro_その他マスタの「連盟プロ以外」「別名」）の名前に「(旧)」を付ける
    2. 1週間後を目途に元の3タブを消す
    3. 「桜花」の C・G・Q〜U・W 列と「JWRC」の C〜E・G・N〜U・W 列を消す。**JWRC の G 列「結果」には259行の値（敗退・通過）がある**（どのページも読んでいない。消すと値は失われる）
  - 新しいブックを読み始めた後は、選手の追加・改名は「ryoei.pro_選手」の3タブで行う（元のタブを直しても反映されない）
- 未確認の項目:
  - 列を消した後のシートでのページの表示（コードは見出しで引き直すため、消す前と同じ表になる見込み。node で、消した後の見出しでも引き直した問い合わせが同じ列を指すことは確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 913ca2c1）: https://github.com/retroeater/mj-logs/tree/main/guide/913ca2c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
