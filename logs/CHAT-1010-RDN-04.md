# CHAT-1010-RDN-04

- 着手日時: 2026-10-10
- 対象issue: （起票する親 issue）、#327
- ブランチ: work/1010-rdn
- 着手時HEAD: 86e965ca

## 指示

【Claude作成】Claude Code 向け指示：「プロ」シートを読む全箇所を、列の英字ではなく見出しの名前で読む形に切り替える（段1。シートの列はまだ消さない。RDN-02 の続き、houou 系の3つを外す）
Chat-Ref: CHAT-1010-RDN-04
マージ: 承認済み（チャットで）。下の「止まる条件」に1つでも当たればマージせずに止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-rdn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1010-rdn を続けて使う（RDN-02 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-RDN-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-RDN-04` を足す（このログと同じコミットでよい）。RDN-02 のログ `## 経過`「0章ゲート」「進め方の案」を読んでから始める。

目的
「プロ」シートの11列（C・D・E・O〜U・W）を廃止する作業の段1。今はどのスクリプトも「プロ」を列の英字で読み、在籍の判定を `WHERE Y = "Y"` で行っているため、列を1つ消すと全箇所が壊れる（CHAT-1010-RDN-01 のログ「手順3」）。全箇所を見出しの名前で読む形に変え、列を消しても動くようにする。表示・生成物は変えない。
決定（2026-10-10、平野さん）

* 8列（鳳凰出場・鳳凰43後・鳳凰最高・桜花出場・桜花21期・桜花最高・最強出場・放送対局）は、まず今の数式の定義のまま生成時の集計に移す。定義を直すのは列を消した後に別の指示で行う
* 「鳳凰43後」「桜花21期」は、生成時に最新の期へ自動で追随させる（見出しも生成時に作る。「鳳凰Ampai」「桜花Ampai」タブの期と合わなければ Ampai のリンクを付けない）
* 「Last Name」「First Name」は名簿のブックの「【2】値貼付」タブ（登録名英字姓・登録名英字名）を読む。「所属」は名簿の「公開」タブ
* #327（「プロ」N列の廃止）は、この段1（見出しで読む形への切り替え）の後に回す
* 段1のマージは条件付きで承認（条件は「止まる条件」）
* 未マージの work/1008-hou（houou/、#518）との重なりは、RDN-02 のログの案C で進める: 段1から `generate_houou_leagues.py`・`generate_houou_race.py`・`check_leagues_dropped.py`（`generate_houou_leagues.PRO_QUERY` を借りるため）を外し、その3つと work/1008-hou にある `generate_houou_pages.py` は work/1008-hou のマージ後に別の指示で切り替える。11列を消す（段4）のは、それらも済んでから
* （上の1〜3項目めは段2・段3で実装する。この指示では記録するだけ）

前提（チャット側。平野さんの決定ではない）

* 段取りは RDN-01 のログ「手順6」の4段（段1 見出しで読む → 段2 C・D・E を名簿から → 段3 8列を集計 → 段4 平野さんが11列を消す）で進める見込み。段2以降は別の指示で出す
* 読み取りは1つの関数に寄せる（置き場所・名前は任せる。`lib/sheets.py` の `fetch_records()`〈#346〉を使うか、それに相当する「プロ」用の関数）。見出しはセル内改行を除いて比べる。A列の見出し「登録名\n0.74」（#467）は、改行の前（「登録名」）で引けるようにする。必要な見出しが無い・重複するときは生成を止める
* `WHERE Y = "Y"` は読んだ後に見出し「表示」= Y で絞る。`ORDER BY B` は見出し「ソートキー」での並べ替えに替えるが、gviz の並べ替えと Python の並べ替えで順が変わることがある（要確認）。順が変わるなら、生成物が同じになる方法を選ぶ
* `generate_houou_leagues.py`・`generate_ouka_leagues.py`・`check_leagues_dropped.py` の「Q/T が空でない」は、段1では見出し「鳳凰最高」「桜花最高」が空でない、で置き換える（段3で「鳳凰」「桜花」タブに名前がある、に替える見込み）
* work/1009-swp-526 の scripts の変更は cloudflare に入っている（RDN-02 のログ）
* `generate_ouka_leagues.py` は work/1008-hou で変わっていない（RDN-02 のログの洗い出しによる。要確認、変わっていれば同じく外す）
* 別チャット（houou/）には、`generate_houou_pages.py` が「プロ」の C・D・E を英字で読んでいることを平野さんから伝える（この指示では work/1008-hou に触らない）

手順

1. 起票: 同じ目的の issue をクローズ済みも含めて検索し（RDN-01 のときは無し）、無ければ親 issue「「プロ」シートの冗長な列（氏名の英字・所属・成績の集計8列）を廃止する」を起票する。本文に上の決定・4段の段取り・段1から外した4つ（上の3つと `generate_houou_pages.py`）を work/1008-hou のマージ後に切り替える残作業・RDN-01 のログの URL（`## 経過` の「手順2」〜「手順6」を参照先として指す）・関係する issue（#327・#467・#127・#168）を書く。#327 にコメントで「N列の削除はこの親 issue の段1の後に回す（2026-10-10、平野さんの決定）」と書き、#327 の期日の記述は変えない（10/13 の取得の安定の判断はそのまま行う）。この親 issue に着手中コメントを残す。
2. 実装: RDN-01 のログ「手順3」の表の全箇所（`generate_jpml_pros.py` の `QUERY` と位置のタプル展開 `build_row_html()` を含む）を見出しの名前で読む形に変える。洗い出しは表を写さず、着手時に grep し直して（ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`）表と食い違えばログに書く。`scripts/tests/` の英字前提のテスト（`test_jpml_pros.py`・`test_wayhome_player_links.py`・`test_birthdays.py` ほか）を見出し前提に直し、見出しの欠け・重複で止まることのテストを足す。`docs/notes/static-generation.md` の「プロ」の読み方の記述と、`docs/decisions/pros.md` を直す。
3. 確かめとマージ: 同じ時点で、変える前のコード（その時点の origin/cloudflare）と変えた後のコードの両方で `python3 scripts/regenerate.py all` を実行し、生成物を比べる（シートの変化が混ざらないように、間を空けずに続けて生成する）。チェック系のスクリプト（`check_meibo.py`〈dry_run〉・`check_leagues_dropped.py`・`check_saikyo_unregistered.py`）も変える前後で出力を比べる。`python3 -m unittest discover -s scripts/tests` を通す。差が0なら、生成物は含めずに（再生成のコミットは作らない）コードと文書だけを cloudflare へマージする。マージ後、親 issue に結果をコメントする（親 issue は閉じない）。

止まる条件

* 手順1で同じ目的の issue が見つかった、他セッションの着手中コメントがある
* 変える前後の生成物に差が1つでもある（どのページのどこかをログに書く）。チェック系スクリプトの出力に差がある
* 「プロ」の行数が 1,000〜1,300 を外れる、または必要な見出しが見つからない・重複する
* 手順2の grep で RDN-01 の表に無い「プロ」の読み取り箇所が見つかり、扱いが決められない（見つかっただけなら直して進めてよい）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、段1で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（衝突の箇所をログに書いて止まる）。work/1008-hou が外した3つを変えていることでは止まらない。work/1010-rdn-yama（RDN-03、調査のみ）は対象外
* `.github/workflows/` を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-RDN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-RDN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。RDN は同じセッションの続き
- ブランチ: ローカル work/1010-rdn は origin/work/1010-rdn と一致（86e965ca）。`git checkout work/1010-rdn`。origin/cloudflare は祖先でないため、このログの push の後に merge で取り込む
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾の行は指示文の最後の行と一致。RDN-02 のログの状態に `/ 続き: CHAT-1010-RDN-04` を足した。RDN-02 のログ「0章ゲート」「進め方の案」を読んだ（案C で進める）
- 指示文は改行が潰れた形で貼られたため、文面は変えずに項目の区切りで改行した
- ログの push（01ee9c68）の後に `git merge origin/cloudflare`（衝突なし）

### 0章ゲート

- 未マージの work/ ブランチ（取り込み後）: work/1008-dic、work/1008-hou、work/1009-swp-526、work/1010-rev-124、work/1010-whs
  - 段1で変えるファイルとの `git diff --stat origin/cloudflare...<branch>`: work/1008-dic が `generate_resource_dictionary.py`（38行目の `DICT_HEADERS` と `rows_from_dict_tab()`。
    段1で変えるのは26行目の import・32〜34行目の `PRO_*`・`main()` の「プロ」の読み取りで、別の行・別の関数）と `tests/test_resource_dictionary.py`（段1では変えない）。
    work/1008-hou が `docs/notes/static-generation.md`（「ページの一覧」の2行。段1で足すのは「生成を止める条件の設計」の節）。ほかは重なりなし
  - その後 work/1008-dic は cloudflare にマージされ（DIC-18）、2回目の取り込みで `generate_resource_dictionary.py` は自動マージで衝突なし
  - work/1008-hou は外した3つ（`generate_houou_leagues.py`・`generate_houou_race.py`）を変えているが、決定どおり止まらない。
    前提の「`generate_ouka_leagues.py`・`check_leagues_dropped.py` は work/1008-hou で変わっていない」は正しい（`git diff --stat` で0）
- 文書のサイズ: CLAUDE.md 22,098・handover.md 22,704・chat-side-operations.md 26,306 バイト（警告域は 30,720・26,624・26,624。chat-side は警告域の手前。今回は触らない）

### 手順1: 起票

- 同じ目的の issue の検索（MCP `search_issues`、クローズ済みを含む）:「プロ シート 冗長な列 廃止 氏名の英字 所属 成績 集計」「プロ シート 見出しで読む 列の英字 WHERE Y」→ 同じ目的は無し（#475・#484・#445 が出たが別の目的）
- **親 issue #536「「プロ」シートの冗長な列（氏名の英字・所属・成績の集計8列）を廃止する」を起票**（ラベル: 分野: 整理・保守、対象: jpml_pros）。本文に決定・4段の段取り・段1の残作業（4つ）・RDN-01 のログの URL・#327・#467・#127・#168
- #536 に着手中コメント（セッションの URL）。#327 に「N列の削除は #536 の段1の後に回す（2026-10-10、平野さんの決定）」とコメント（期日の記述は変えていない）

### 手順2: 実装

grep し直した結果（ブックの ID・`"プロ"`・`WHERE Y`・`PRO_QUERY`・`PROS_QUERY`）は RDN-01 の表と一致（表に無い読み取り箇所は無し）。

- 新しい `scripts/lib/pro_sheet.py`: `fetch_pros(headers, required=(), sort=False)` が「プロ」を `SELECT *` で読み、見出しで列を選ぶ。在籍は「表示」= Y、`required` は空欄（None）の行を除く（旧 `IS NOT NULL`）、
  `sort=True` は「ソートキー」の順（安定ソート。旧 `ORDER BY B`）。見出しはセル内改行を除いた形（`X\nID`→`XID`）と改行の前（`登録名\n0.74`→`登録名`）の両方で引け、
  無い・2つ以上の列に当たる見出しは ValueError。見出しの定数（`NAME`・`X_ID` など）もここに置く
  - 当初 `lib/pros.py` にしたが、pyflakes で `generate_books_pages.py`・`generate_live_pages.py`・`generate_saikyo_pages.py` の `load_name_book()` のローカル変数 `pros` がモジュール名を隠すことが分かり
    （`pros = [Pro(...) for ... in pros.fetch_pros(...)]` が UnboundLocalError になる）、`pro_sheet` に改名した。単体テストは `load_name_book()` を通らないため、この誤りはテストでは出ていなかった
- `scripts/lib/sheets.py`: `fetch_table()`（全体を読み (見出し, 行) を返す。存在しないタブ名の検知は `fetch_records()` と同じ）を足した。`fetch_records()` は変えていない
- 切り替えた箇所: `generate_jpml_pros.py`（`QUERY` → `COLUMNS`。使っていない YouTube画像〈#327 で消す N列〉・最終更新は読まないようにし、`build_row_html()` の展開から外した）、
  `generate_ouka_leagues.py`（候補は `required=(桜花最高,)`・`sort=True`）、`generate_saikyo_pages.py`・`generate_live_pages.py`・`generate_books_pages.py`（`PRO_COLUMNS`）、
  `generate_title_pages.py`（`PROS_COLUMNS`）、`lib/wayhome.py`（`PRO_COLUMNS`。添字の定数はそのまま）と `generate_wayhome_episodes.py`・`generate_video_wayhome.py`、
  `lib/birthdays.py`・`sync_dojo_calendar.py`・`generate_jpml_test.py`・`generate_resource_dictionary.py`・`check_meibo.py`・`fetch_youtube_channels.py`
- `generate_ouka_leagues.py` の `PRO_QUERY` は、外した `check_leagues_dropped.py` が `mod.PRO_QUERY` で借りるため残した（コメントで段1の残作業と書いた）。#536 の残作業に含まれる
- 外した `generate_houou_leagues.py`・`generate_houou_race.py`・`check_leagues_dropped.py` は変えていない
- テスト: `test_jpml_pros.py`（`QUERY` の列記号 → `COLUMNS` の見出し）、`test_wayhome_player_links.py`（`PRO_QUERY` → `PRO_COLUMNS`）、`test_birthdays.py`（注記）を直し、
  `test_pro_sheet.py`（改行を除いた見出し・A列の「登録名」・在籍の絞り込み・required・並べ替え・見出しの欠け〈「表示」を含む〉・重複・列の並べ替えと削除）を足した
- 文書: `docs/notes/static-generation.md`「生成を止める条件の設計」に「プロ」の読み方と段1の残作業を足した。`docs/decisions/pros.md` に RDN-04 の決定を足した
- 実装前の確かめ: gviz の `ORDER BY B` と Python の安定ソートの順は 1,099名で一致（ソートキーの重複は3組）。「鳳凰最高」「桜花最高」「YouTube ID」の空欄は全部 None で空文字は0件、
  `IS NOT NULL` の件数（177・82）と None でない件数が一致

### 手順3: 確かめ

- `python3 -m unittest discover -s scripts/tests`: 693件 OK（変更の後と、cloudflare の取り込みの後の両方）
- pyflakes: 変えたファイルの指摘は既存の2件（f-string に置き換えが無い。`generate_title_pages.py:598`・`generate_video_wayhome.py:100`）だけ
- **生成物の比較**: origin/cloudflare（74c6dc64）を scratchpad に worktree で出し（比較の後に削除）、作業ブランチ（74c6dc64 を取り込んだ後）と、
  `regenerate.py --list` の21ページを**1ページずつ「変える前 → 変えた後」の順に続けて**生成した（`regenerate.py all` は途中の失敗で止まるため1ページずつ）。
  - **両方のツリーとも、生成後の `git status` が変更0件**（どちらも origin/cloudflare の生成物と同じ）。`diff -rq`（.git・scripts・docs を除く、gitignore されたファイルを含む）でも差0。**差0**
  - 各ページの生成ログも、ツリーのパスを置き換えれば21ページとも一致
  - `books_pages` は両側とも同じ理由で失敗（「書籍」タブの行数が想定外〈95行〉で止める。books は凍結中で、今回の件と無関係）。ほかの20ページは両側とも rc=0
- **ページ以外の読み取り**（変える前後で同じ値か、JSON に書き出して `cmp`）: `fetch_youtube_channels.collect_channel_ids()`（77件）・`birthdays.fetch()`（1,072件・警告0・件数）・
  `sync_dojo_calendar._fetch_roster()`（1,099名）・`generate_resource_dictionary.rows_from_pros()`（1,099行）→ **一致**（API・カレンダーには書き込んでいない）
- **チェック系**: `check_meibo.py --json`（名簿 1,099名と在籍者 1,099名が一致）・`check_saikyo_unregistered.py`（440名、未登録0）・`check_leagues_dropped.py` → 変える前後で出力が**一致**
  （`check_meibo.py` にはdry_runの引数が無く、issue を書くのはワークフロー側なので、スクリプトの実行だけで書き込みは無い）
- 「プロ」の行数: 1,099（範囲内）。必要な見出しは全部見つかった（重複なし）
- マージ直前に origin/cloudflare が d0fd8918（`chore: regenerate resource_dictionary.html dic/`、sitemap-pages.xml の1行だけ）まで進んでいたため取り込んだ（scripts の変更は無いので比較はそのまま有効）

## 報告

- 状態: 完了
- ブランチ: work/1010-rdn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-RDN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-rdn
- 確認用URL: なし（生成物は変わらないため、コードと文書だけをマージ）
- マージ: 済（このログを含むコミットを cloudflare へ fast-forward で push）
- issue: #536（起票。段1の結果をコメント、閉じない）、#327（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf3cb320）: https://github.com/retroeater/mj-logs/tree/main/guide/cf3cb320

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf3cb320/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
