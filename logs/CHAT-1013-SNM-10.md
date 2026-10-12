# CHAT-1013-SNM-10

- 着手日時: 2026-10-13
- 対象issue: #548
- ブランチ: work/1012-snm-ampai
- 着手時HEAD: a843c441

## 指示

【Claude作成】Claude Code 向け指示：生成のスクリプトが「鳳凰」タブを見出しで読む形にして列の挿入・削除に耐えるようにし、組を F 列「組」から読めるようにする。あわせて jpml_pros の Ampai のリンクを新しい「Ampai」ブックから引く形に切り替え、新しい期の URL が無いときに issue で知らせる（#548。CHAT-1013-SNM-09 の続き。ページ側の JS の2つは次の指示） Chat-Ref: CHAT-1013-SNM-10 マージ: 承認済み（チャットで。2026-10-12 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: いつでも（平野さんは 2026-10-12 に「鳳凰」の F 列「組」を削除して元の並びに戻した。0章で確かめる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-snm-ampai の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-snm-ampai を続けて使う（CHAT-1012-SNM-08・CHAT-1013-SNM-09 のログのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて (1) CHAT-1013-SNM-09 のログの `## 報告` を読み、状態が「中断」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1013-SNM-10` を足す（SNM-06・SNM-08 の状態の `/ 続き:` はそのままでよい）。(2) 「鳳凰」タブを生成と同じ経路で読み、見出しの並びが「組」の列を含まず、X 列の見出しが「備考」である（挿入を戻した後の形である）ことを確かめる。そうでなければ何もせず止まる。

目的
平野さんは「鳳凰」タブの組（①②）を X 列「備考」から F 列「組」へ移し、残った X〜AA 列（「プロ」・「備考」など）を消したい。今のコードには「鳳凰」を列の英字で読む所があり（例: 順位変動が `SELECT A,X` で組を読む）、列を挿入・削除すると値を取り違える。そこで生成のスクリプトが「鳳凰」を読む所をすべて見出しで読む形にし、組は見出し「組」から読み、消したい列を読まない形にする。あわせて、Ampai の URL を平野さんが作った新しいブックから引く形に切り替える。シートの列の挿入・削除は、ページ側の JS の2つ（旧方式の2ページ）を見出しで読む形にする次の指示のマージの後に、平野さんが行う。
決定（2026-10-12、平野さん）

* 集計元「鳳凰」の組（①②）は、F 列に挿入する見出し「組」の列で持つ（X 列「備考」の値を写したもの）。挿入した後の X 列「プロ」から AA 列「備考」までは消す。シートの作業は、ページ側の JS の2つを直す次の指示のマージの後に平野さんが行う（一度挿入したが、今のコードが列の英字で読んでいるため戻した）
* ページ側の JS の `houou_results.js`・`league_ranking.js`（旧方式の Google Charts の2ページ）が「鳳凰」を A〜V 列の英字で読んでいる件は、2つの JS を見出しで読む形に直す（案 ii。2026-10-13、平野さん）。この指示では直さず、次の指示で直す
* 新しいブックの ID は `1zk3FQV0DDZhqG9-m2i2LXQcNx8ZzpLjCS4dEFKFrZ98`（ファイル名「ryoei.pro_Ampai」。CHAT-1012-SNM-08 の指示文の ID はチャット側の写し間違いで、それが 404 の原因）
* Ampai の URL は、「プロ」のブックのタブではなく新しい別ブックで持つ: https://docs.google.com/spreadsheets/d/1zk3FQV0DDZhqG9-m2i2LXQcNx8ZzpLjCS4dEFKFrZ98/edit?gid=0#gid=0 （「リンクを知っている全員が閲覧可」にしてある。ID はコードに書いてよい）
* 1行は タイトル戦（「鳳凰戦」「女流桜花」）× 期 × 前後 × リーグ × 組。ブックの先頭の見出しは「タイトル戦」（「棋戦」ではない）で、生成はこの見出しで読む（2026-10-13、平野さん）。空の欄（組の無いリーグの「組」、女流桜花の「前後」）には「-」が入っており、生成は「-」を空として扱って突き合わせる（ブックは直さない。2026-10-13、平野さん）。初期データは CHAT-1012-SNM-06 の TSV（今ある 43期前期・43期後期・桜花21期）から、鳳凰位・桜花の2行を除いたもの（Ampai の成績表に無いため。同じ日の「入れる」を置き換える）。白鳥翔・清水香織の Ampai のリンクは付かなくなる
* 過去の期は埋めない。選手に合う行が無いときはリンクを付けない（生成は止めない）。新しい期が始まったのにその期の行がブックに無いときは issue で知らせる（生成は止めない）
* 「プロ」の鳳凰Ampai・桜花Ampai の2列と「鳳凰Ampai」「桜花Ampai」の2タブは、この切り替えと確かめ（次の指示）の後に平野さんが消す（2タブは一度「(旧)〜」に改名したが、生成が名前で読んでいるため元の名前に戻した）
* このマージは、下の「止まる条件」の確かめが通れば承認する
* （既存の決定: 2026-10-11〈CHAT-1011-RDN-14〉「Ampai の URL は複数の期をすべて管理し、選手の期・リーグ・組で URL を選ぶ。新しい期で URL が無いときは知らせる」〈#548〉）

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-09 のログ: 正しい ID で新しいブックは読めた。見出しの先頭が「タイトル戦」、空の欄が「-」で止まった（上の決定で解決）。それ以外の結果はそのログの「経過」を使ってよい
* CHAT-1012-SNM-08 のログ: 新しいブックは（写し間違いの ID で）404。ページ側の JS の2つが「鳳凰」を A〜V 列の英字で読んでいる。それ以外の手順1の洗い出しの結果は、そのログの「経過」を使ってよい（着手時に読み直して食い違えば書く）
* docs/notes/houou-top.md: 「鳳凰」は A名前・B期・C前後・Dリーグ・Eリーグキー・F順位・G結果・H合計・I〜U 第1〜13節・V表示・Wプロ・X備考。docs/notes/houou-race.md: 順位変動は「備考」が X・Z の2列あるため `SELECT A,X` で組を読み、ほかは `fetch_records()` で見出しの名前で読む。Y 列・Z 列の中身、ほかに「鳳凰」を列の英字で読む所（houou/ など）は未確認（要確認）
* 平野さんの申告では、F 列に「組」を挿入した後の X〜AA 列は「プロ」から「備考」まで（元の W〜Z 列）。それぞれを読む所（コード・ほかのタブの数式）が無いかは未確認（要確認）
* 新しい Ampai のブックのタブ・見出し・行数はこのチャットでは読んでいない（要確認）。見込みは見出し タイトル戦・期・前後・リーグ・組・Ampai URL・備考 の7列、データ 43行（SNM-06 の TSV の 45行から2行を除いた数）
* Ampai の直す箇所は SNM-06 のログ `## 報告` の表のとおり（`lib/pro_stats.py`・`generate_jpml_pros.py`・`lib/pro_sheet.py`・テスト・文書）
* 知らせの issue は、すでに issue を書いているワークフロー（`check-meibo.yml` など）に相乗りし、ワークフローの変更をなるべく小さくする案（置き方は任せる。ワークフローを変えるなら CLAUDE.md「ブランチ運用」のとおりマージ前に作業ブランチで手動実行する）。「新しい期が始まった」の判定は、集計元の最新の期（`pro_stats.periods()`）の行が新しいブックに1行も無いとき（SNM-06 の案）
* 使う skill は無い

手順

1. 確かめと洗い出し: #548 の本文・コメントを読み、ほかの Chat-Ref の未完了の着手中コメントが無いことを確かめて着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。「鳳凰」を2回読み、見出しの一覧（列の英字つき）・行数を書く。リポジトリ全体（`scripts/`・`scripts/tests/`・`.github/workflows/`・ページ側の JS）で「鳳凰」を読む所を洗い出し（SNM-08 のログの表と比べる）、読み方（見出しか列の英字か）・読む列を表にする。「プロ」のブックを xlsx でエクスポートし（CHAT-1011-RDN-10 と同じ方法）、ほかのタブ・同じタブの数式が「鳳凰」の W〜Z 列（今の英字）を引いているか、W〜Z 列のセル自体が数式かを書く。新しい Ampai のブックを2回読み、タブの名前・見出し・行数を書き、SNM-06 の TSV から2行を除いたものと値を比べる（「棋戦」と「タイトル戦」の見出しの違い、空と「-」の違いは同じとみなす）
2. 実装:
   * 生成のスクリプト（`scripts/`）が「鳳凰」を読む所をすべて見出しの名前で読む形にする（列の英字の読み取りを残さない。テストで再登場を検査する）。ページ側の JS（`houou_results.js`・`league_ranking.js`）は変えない（次の指示）。組は見出し「組」の列から読み、無ければ今の X 列「備考」から読む（挿入の前後の両方で動くように。今の読み方を外すのは次の指示）。「プロ」「備考」（W・X・Z 列）と Y 列は、読む必要が無ければ読まない
   * Ampai: 新しいブックを読む部品を作り（ID はコードに書く。見出しで読む。タイトル戦・期・前後・リーグ・組の組み合わせ〈「-」は空として〉が重複していたら生成を止める）、`generate_jpml_pros.py` が選手の最新の期のタイトル戦・期・前後・リーグ・組で URL を引く形にする。「プロ」の鳳凰Ampai・桜花Ampai の2列と「鳳凰Ampai」「桜花Ampai」の2タブは読まない（`pro_sheet` の定数は使う所が無くなれば消すか `RETIRED` に移す）
   * 知らせ: 最新の期の行が新しいブックに無いときに、生成は止めずに issue で知らせる（題名で探して、無ければ作る・あれば更新する。解消したら閉じる。既存の検査の issue の作りに合わせる）。生成のログにも1行出す
   * テスト（直す前のコードで通らないことを確かめる）と文書（`docs/notes/houou-race.md`・`houou-top.md`・`static-generation.md` など、「鳳凰」の列や Ampai の読み方を書いている所）を直す。追記先の今の文を読んでから直す
3. 確かめてマージ:
   * (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる
   * (b) 「鳳凰」の読み取りを差し替えた模擬（シートは変えない）で、直した後のコードの全ページを生成し、(a) の直した後と比べる（ページ側の JS が読む2ページの表示は対象外。次の指示で確かめる）: (b1) F 列に「組」（X 列の値の写し）を挿入した表、(b2) (b1) からさらに挿入後の X〜AA 列（元の W〜Z 列）を消した表
   * (c) 新しいブックから最新の期の行を除いた模擬で、jpml_pros が止まらずに Ampai のリンクが付かないこと、知らせの判定が「知らせる」になることを確かめる（本番の issue には書かない）
   * 止まる条件に当たらなければ、docs/decisions/pros.md と docs/decisions/houou.md に上の「決定」を足し、#548 に結果をコメントし（閉じない）、cloudflare へマージする。ログの `## 報告` の項目の後ろに、平野さんがこの後にすること（ページ側の JS の2つを直す次の指示のマージまでは「鳳凰」の列を触らない。その後に F 列「組」を挿入し X 列の値を写す、挿入後の X〜AA 列を消す〈それぞれの見出しを書く〉）と、次の指示で消すもの（「プロ」の鳳凰Ampai・桜花Ampai の2列〈マージの時点の列の英字〉、「鳳凰Ampai」「桜花Ampai」の2タブ）を書く

止まる条件

* 新しい Ampai のブックが読めない、2回の読みで変わる、見出しが前提の7列（タイトル戦・期・前後・リーグ・組・Ampai URL・備考）と違う、データの行数が 43 と違う、または SNM-06 の TSV（2行を除く）と値が違う（空と「-」の違いは同じとみなす）（違いを行ごとに書いて止まる）
* 「鳳凰」の W〜Z 列をほかのタブ・同じタブの数式が引いている、またはコードが読んで使っていて、読まない形に直せない（消すと値が変わる）
* (a) で、jpml_pros の差が「白鳥翔・清水香織の Ampai のリンク（鳳凰・桜花の欄）が外れる」以外にある、または jpml_pros 以外のページに差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が直す前と同じく「書籍」の行数で止まるのは許す）
* (b1)・(b2) のどちらかで、(a) の直した後と差がある、またはページが止まる
* (c) で、jpml_pros が止まる、または知らせの判定が「知らせる」にならない
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* ワークフローを変える必要があり、マージ前に作業ブランチで確かめられない
* #548 にほかの Chat-Ref の未完了の着手中コメントがある
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の「鳳凰」の見出しの一覧・読む所の表・数式の結果・新しいブックの比較、直した箇所（ファイル・関数・何を変えたか）、テストの結果、(a)(b1)(b2)(c) の結果の表、知らせの issue の題名と置き場所（どのワークフローで動くか）、直した文書の一覧がログにある
* マージ後の自動再生成（regenerate-page.yml、push）の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-13）

- 0.: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 同じ Chat-Ref のコミットは 0。作業ブランチ: ローカル・リモートの `work/1012-snm-ampai` は a843c441 で一致。`origin/cloudflare` を `git merge` で取り込んだ（9a26ca08、衝突なし）
- (1) CHAT-1013-SNM-09 の `## 報告` の状態は「中断（エラー）」。末尾に ` / 続き: CHAT-1013-SNM-10` を足した
- (2) 「鳳凰」（`lib/sheets.fetch_table`）を2回読み、2回とも 16,011行・A 名前・B 期・C 前後・D リーグ・E リーグキー・F 順位・G 結果・H 合計・I〜U 第1〜13節・V 表示・W プロ・X 備考・Y 存在チェック・Z 備考。「組」の列は無く、X は「備考」

### 手順1: 確かめと洗い出し

- #548: コメントは SNM-06（判断待ちで終わった）・SNM-08（中断）の2つで、未完了の着手中コメントは無い。着手中のコメント: https://github.com/retroeater/mj/issues/548#issuecomment-6128386195
- 未マージのブランチ: work/1009-swp-526・work/1011-swp-hnd・work/1011-swp-nav・work/1012-dsn は直すファイルを変えていない。**work/1013-hou が `scripts/generate_houou_pages.py` を変えている**が、`render_players()`・`current_leagues()`（新しい関数）・`outputs()` の別の箇所で、この指示が直す `load_groups()`・import・定数の行とは重ならない（同じファイルの別の箇所なので止まらない）
- 「鳳凰」を読む所: SNM-08 のログの表と同じ（Python で列の英字が3つ〈`generate_houou_race.py` の `SELECT A,X`・`generate_houou_leagues.py` の `SELECT A,B,C,D,F`・`generate_houou_pages.py` の `SELECT A,B,C,D,X,V`〉、見出しが3つ〈`generate_houou_race.py`・`generate_houou_pages.py` の `fetch_records()`、`check_unused_names.py`、`lib/pro_stats.py` の `_read()`〉、ページ側の JS が2つ〈`houou_results.js`・`league_ranking.js`。この指示では変えない〉）。取り込んだ cloudflare にも新しい読む所は無かった
- 「鳳凰」の W〜Z 列の数式: SNM-08 のログ（2026-10-12 の xlsx エクスポート）のとおり。W・Y は数式、X・Z は値、ほかのタブ・同じタブから W〜Z を引く数式は 0。コードで W〜Z を読んで使うのは X（組）だけで、`lib/houou_tab.py` が「組」の列を先に読むので、「組」を挿入した後は読まない
- 新しい Ampai のブック（`1zk3FQV0DDZhqG9-…`）: 2回読んで同じ。タブは「成績表」1つ、見出しは タイトル戦・期・前後・リーグ・組・Ampai URL・備考（前提の7列）、データ 43行。SNM-06 の TSV から鳳凰位・桜花の2行を除いた43行と、「-」を空とみなして比べると差 0

### 手順2: 直した箇所（c28057dc・6632e78c）

| ファイル・関数 | 変えたこと |
|---|---|
| `scripts/lib/houou_tab.py`（新） | 「鳳凰」を見出しで読む `fetch_rows(headers)`。見出し `GROUP`（「組」）は「組」の列 → 無ければ左の「備考」（X 列）。見出しが無い・重複すれば ValueError |
| `scripts/generate_houou_race.py` `NOTE_QUERY`→`NOTE_HEADERS`・`load_notes()` | `SELECT A,X` をやめ、`houou_tab.fetch_rows(("名前", 組))` で読む（名前の列で行の対応を確かめるのは同じ）。`fetch_sheet` の import を外した |
| `scripts/generate_houou_leagues.py` `QUERY`→`HEADERS`・`load()` | `SELECT A,B,C,D,F` をやめ、`houou_tab.fetch_rows(("名前", "期", "前後", "リーグ", "順位"))` |
| `scripts/generate_houou_pages.py` `GROUP_HEADERS`・`load_groups()` | `SELECT A,B,C,D,X,V` をやめ、`houou_tab.fetch_rows(("名前", "期", "前後", "リーグ", 組, "表示"))` |
| `scripts/lib/ampai.py`（新） | Ampai のブックを見出しで読む `fetch()`・`build()`（キー: タイトル戦・期・前後・リーグ・組、「-」は空、「①組」は「①」。キーが重複すれば ValueError）・`has_period()` |
| `scripts/lib/pro_stats.py` | 「鳳凰Ampai」「桜花Ampai」タブの先頭の見出しを読むのをやめた（`HOUOU_AMPAI`・`OUKA_AMPAI`・`_first_label()`・`Sources.houou_ampai_label`・`ouka_ampai_label`・`Period.ouka_ampai_label()` を消した）。`Sources.houou_groups`（「鳳凰」の組、`houou_tab`）を足し、`ampai_urls()`（最新の期のタイトル戦・期・前後・リーグ・組で URL を引く）・`missing_ampai_periods()`（最新の期の行が1行も無い期）を足した |
| `scripts/generate_jpml_pros.py` `COLUMNS`・`ROW_FIELDS`・`add_stats()`・`build_page()` | 「プロ」の鳳凰Ampai・桜花Ampai 列を読まない。URL は `pro_stats.ampai_urls()` から。最新の期の行がブックに無ければ警告を1行出す（生成は止めない）。Ampai のブックの読み取りの失敗（キーの重複など）は生成を止める（`--check` では理由にする） |
| `scripts/lib/pro_sheet.py` | 定数 `HOUOU_AMPAI`・`OUKA_AMPAI` を消し、`RETIRED_AMPAI`（読まないことをテストで確かめる）に移した |
| `scripts/check_ampai.py`（新） | 最新の期の行が Ampai のブックに無いかを調べ、標準出力と JSON に書く |
| `.github/workflows/check-meibo.yml` | 名簿の検知の後に「Ampai の URL が無い期を調べる」と「Ampai の結果をissueに反映」の2ステップを足した（dry_run のときは issue に書かない。題名で探して、無ければ作る・あれば本文を更新・解消したらコメントして閉じる。名簿の検知と同じ作り） |

**知らせの issue**: 題名「Ampai の URL が無い期」。`check-meibo.yml` の週次（毎週月曜 05:07 JST）と手動実行（dry_run=false）で動く。生成（`generate_jpml_pros.py`）のログにも「Ampai のブックに最新の期「…」の行がありません」の1行を出す。

**テスト**: `python3 -m unittest discover -s scripts/tests` 939件 OK。足した・直したテスト: `test_houou_tab.py`（新。組の読み方〈挿入前・挿入後・削除後で同じ値〉、見出しの欠け・重複、「鳳凰」を列記号で読む所の再登場の検査、Ampai のブックの「-」・重複・見出し）、`test_pro_stats.py`（`AmpaiTest`: リーグ＋組で URL、鳳凰位は付かない、最新の期の行が無いときの判定）、`test_jpml_pros.py`（Ampai の URL の受け渡し、「プロ」の Ampai 2列を読まない）、`test_pro_sheet.py`（`RETIRED_AMPAI` を定数にしない）。
直す前のコード（cloudflare 62f48a8b）に新しいテストを置くと 15件が落ちる（FAIL 2・ERROR 13）。

**直した文書**: `docs/notes/houou-race.md`（組の読み方）、`docs/notes/houou-top.md`（「鳳凰」の列と見出しで読むこと、JS はまだ列記号）、`docs/notes/static-generation.md`（jpml_pros の Ampai の引き方、`check-meibo.yml` の行、`check_ampai.py` の説明）。

**ワークフローの確かめ**（CLAUDE.md「ブランチ運用」）: `check-meibo.yml` を ref `work/1012-snm-ampai`（6632e78c）・`dry_run: false` で手動実行した。起動の前に、題名「名簿と「プロ」シートの不一致」「Ampai の URL が無い期」の open の issue が無いこと（REST の open の一覧に 0）と、手元で名簿の不一致 0・Ampai の欠け 0 を確かめ、どちらの issue の手順も作らず閉じもしない状態で起動した。
- run 38278633405: success。全ステップ success（テスト・名簿の検知「不一致なし」・Ampai の検知「最新の期の行は Ampai のブックにあります」・Ampai の issue の手順「Ampai の URL が無い期なし」）。issue の作成・更新・クローズ 0

### 手順3: 確かめ

方法: scratchpad に5つのクローン（pre = cloudflare 62f48a8b、post = c28057dc、b1・b2・c = 同じ c28057dc）。ページごとに pre（docs.google.com の応答を記録）→ post → b1 → b2（再生）の順で `regenerate.py <ページ>` を続けて実行し、生成物を比べた。
b1 は「鳳凰」の `SELECT *` の応答で F 列に「組」（X 列の値の写し）を挿入（14回効いた）、b2 はさらに挿入後の X〜AA（元の W「プロ」・X「備考」・Y「存在チェック」・Z「備考」）を消した（見出しは A〜W の23列、最後が「表示」）。c は Ampai のブックの応答から鳳凰戦43期後期の19行を除いた（43→24行）。再生でキャッシュに無かった URL は post が初めて読んだ Ampai のブックの分だけ。

**(a) 直す前と直した後（今のシート）**

| ページ | 直す前 | 直した後 | 差 |
|---|---|---|---|
| books_pages | 止まる（「書籍」の行数 95行） | 同じ理由で止まる | - |
| jpml_pros | 通る | 通る | **2行だけ**: 清水香織の「桜花21期」の欄（「桜花」）と白鳥翔の「鳳凰43後」の欄（「鳳凰位」）の Ampai のリンクが外れ、文字だけになった（決定どおり） |
| ほかの21ページ | 通る | 通る | 0 |

**(b1)・(b2)**（直した後のコード、(a) の直した後と比べる）: 23ページすべて (a) の直した後と差 0（books_pages は同じ理由で止まる。ほかは通る）。`check_unused_names.py --no-issue` も pre・post・b1・b2 とも通る。

**(c) 最新の期の行を除いた Ampai のブック**: jpml_pros は止まらずに通る（終了コード 0）。生成のログに「警告: Ampai のブックに最新の期「鳳凰43後」の行がありません(…)」。post と比べた差は591行で、すべて「鳳凰43後」の欄の Ampai のリンクが外れただけ（女流桜花のリンク 136 はそのまま）。`check_ampai.py` は `missing: ["鳳凰43後"]`（知らせる）、post では `missing: []`。

止まる条件に当たるものは無い。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 19e2d39e）: https://github.com/retroeater/mj-logs/tree/main/guide/19e2d39e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/19e2d39e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/62f48a8b.md
