# CHAT-1010-WHS-03

- 着手日時: 2026-10-10
- 対象issue: #194
- ブランチ: work/1010-whs
- 着手時HEAD: 57963e3e

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」シートの移動・列の変更（平野さんが実施）に生成の読み先を合わせ、JSON に無い回は外して生成する形にする。作業ブランチでプレビューまで出して止まる（#194 の前段） Chat-Ref: CHAT-1010-WHS-03 マージ: 判断待ちで止まる（シートの変化による差分を平野さんがプレビューで見てから、別の指示でマージ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが「帰り道」シートを別のブックへ移し、列を直した。生成（一覧・各話・OGP 画像・YouTube の情報の取得）が新しいシートを読むようにする。あわせて、決定3（JSON に無い回は外して生成する）の生成側だけを入れる。自動の取り込み（ワークフロー・知らせ・OGP の自動生成）は次の指示で行う。
決定（2026-10-10、平野さん）
シートの変更（平野さんが行った）

* 「帰り道」シートを次のブックへ移した: https://docs.google.com/spreadsheets/d/10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34/
* 「備考」列を消した。「X ID」「公開日」「画像URL」列を消した。列の順番を入れ替えた
* 「URL」は「動画ID」に、「決勝動画URL」は「決勝動画ID」に列名を変え、値を URL ではなく ID にした
* 「決勝動画ID」で、決勝動画が無いことをはっきりさせる回は、空ではなく「-」にした（空は「まだ埋めていない」）
* `6WAPjcxT78A`（第3期JPML WRC-Rリーグ 勝又健志）をシートに足した（帰り道の回として載せる）
* 「帝王戦」2行・「世界麻雀TOKYO2025」1行のタイトルを title/ の名前に直した。三浦智博の十段戦2行の決勝動画を埋めた。ワールド・リーチ・プロ（タマシュ・エルドス）は「-」にした

#194・#340 の grill の続き（CHAT-1010-WHS-02 の「判断が必要なこと」への回答。WHS-02 で docs/decisions に記録した決定に足す・置き換える）

* 新しい回は、/live の層1の題名に「帰り道」か「ついて」を含むもので拾う（決定5の語を広げる）。拾えない特別編（ワールド・リーチ・プロの回など）は手で直す
* 決勝動画の候補は、title/ の期ページの「決勝ライブ」から採る。複数日なら最終日、冒頭版と全編があれば全編（title/ と同じ）。見つからないときは空にして、そう書く（決定8の「複数あるときは空」を置き換える）
* 知らせの1行のタイトルは、title/ の大会名（title/ の別名の対応表）で作る。シートに登録するときに平野さんが帰り道用に直す
* 決勝動画IDが空の回の知らせは、空の回が新しく増えたときだけ常設 issue に書き、失敗の扱いにしない。「-」の回は知らせない（「-」にすれば、その回の知らせは終わる）
* 知らせの1行の表示の列は `Y` で出す
* メンバー限定の帰り道の回も拾う

前提（チャット側。平野さんの決定ではない）

* チャット側は新しいブックを読めていない（サンドボックスから docs.google.com に届かない）。列の並び・見出し・表示の列（旧 G列 `Y`）の有無・タブ名・行数は（要確認）
* 今の読み先: `scripts/lib/wayhome.py` の `SPREADSHEET_ID`・`SHEET_NAME`・`COLUMNS`（列記号 A・D・E・H）・`QUERY`（`WHERE G = "Y"`）。使う所は `generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`fetch_youtube_meta.py`・`build_wayhome_ogp.py`（2026-10-10 に cloudflare 647a8db で読んだ。ほかにもあるかは洗い出す）。`COLUMNS` の注記のとおり列記号で読んでいるため、列の削除・並べ替えで別の値が入る。今回は見出しの名前で引く形にするのがよいと考えている（`scripts/lib/` にほかのシートの見出し照合の仕組みがあれば借りる）
* `6WAPjcxT78A` は `data/youtube_meta.json` に無いので、今の作りでは生成が止まる。この指示で決定3の生成側（JSON に無い回はその回だけ外して生成し、外した回を標準出力と GitHub Actions の警告〈`::warning::`〉に出す）を入れる。ワークフローを失敗の扱いにする部分と、YouTube の情報の取得は次の指示（ワークフローの変更）で入れる。それまでは、この回は外れたまま載らない
* 決勝動画IDの「-」は「決勝戦を見る」を出さない（空と同じ表示）。区別が要るのは次の指示の知らせだけ
* 見込み: 一覧・各話で変わるのは、シートの変化で説明できるものだけ（タイトルを直した3回の題・説明・og:title・パンくず、決勝動画を埋めた2回の「決勝戦を見る」、決勝動画を全編に直した回があればそのリンク）。各話のページ数は今と同じ39（`6WAPjcxT78A` は外れるため）。一覧の OGP 画像の名前は変わらない（最新話は変わらない）
* `regenerate-page.yml` は「辞書」シートの件で止まっている（辞書のチャットで対応中）。`regenerate.py` は帰り道の2ページ（`video_wayhome`・`wayhome_episodes`）だけを流す
* CHAT-1010-WHS-01 のログの状態の行が「判断待ち / 辞書の件の続き: CHAT-1008-DIC-17」になっていて、`docs/notes/branch-operations.md`「作業ログの寿命」の決まった形（` / 続き: CHAT-…`）と違う（要確認）

手順

1. 新しいシートを読む: 生成と同じ経路（公開シートの読み取り）で新しいブックの帰り道のタブを読み、見出し・列の並び・行数・表示の列の値の内訳をログに書く。旧ブックの帰り道のタブがまだ読めるかも書く。今の公開物（39話）と行ごとに突き合わせ、差（足された回・タイトル・決勝動画ID の変化・消えた回）を表にする。上の「決定」と食い違う差（説明できない差）があれば、そこで止まる
2. 読み先を直す: 4本のスクリプト（ほかにあれば全部）が新しいブックを見出しの名前で読むようにし、動画ID・決勝動画ID（「-」を含む）から今と同じ URL を組み立てる。決定3の生成側を入れる。テストを足す・直す（見出しの名前で引くこと、「-」、JSON に無い回を外すこと）。`docs/notes/video-wayhome.md`（「新しい回を追加する手順」を含む）と `docs/notes/static-generation.md` の帰り道の記述を直す（今の内容を読んでから）。上の「決定」を `docs/decisions/` の合う分野（WHS-02 で書いた所）に足す。WHS-01 のログの状態の行を決まった形（`判断待ち / 続き: CHAT-1008-DIC-17`）に直す
3. 生成して止まる: `regenerate.py` で帰り道の2ページだけを生成し、差分を種類ごとに数えてログに書く（見込みと比べる）。push して Cloudflare のプレビューを出す。平野さんが見るページは、一覧・タイトルを直した回1つ・決勝動画を埋めた回1つ（確認用 URL は最終報告にだけ書く）。#194 に進みをコメントする

止まる条件

* 新しいシートが読めない（共有の設定など）。読めない理由を書いて止まる
* 手順1で、決定とシートの変化で説明できない差がある（例: 回が消えた、表示の列が無い、行数が 40 と違う）
* 一覧・各話の生成物に、見込み（上の「前提」）で説明できない差がある。各話のページ数が 39 でない
* ほかのページ（title/・jpml_pros など）が帰り道のシートを読んでいて、この指示の変更でそのページの生成物が変わる（変えずに止まる）
* 未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、`scripts/lib/wayhome.py` か上の4本のスクリプトの同じ関数を変えている、または取り込みで衝突する
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-03"` は0件
- 作業ブランチ: ローカルの `work/1010-whs`（963efe0d）は `origin/cloudflare` の祖先、リモートもマージ済み → `git merge --ff-only origin/cloudflare`（57963e3e）
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている

### 手順1: 新しいシート

- 新しいブック（title/ ・ /live と同じ `lib/live.py` の `SPREADSHEET_ID`）を、生成と同じ gviz で読めた。タブ一覧（htmlview）に「帰り道」がある
- 見出し・列の並び: A 名前・B タイトル・C 動画ID・D 決勝動画ID・E 表示。行数 40。表示は 40行とも `Y`。値はすべて文字列
- 旧ブックの「帰り道」タブもまだ読める（40行）。ただし旧タブも列が変わっていて（A 名前・B X ID・C 公開日・D タイトル・E 動画ID・F 画像URL・G 表示・H 決勝動画ID。B・C・F は空、値は ID）、
  タイトル3行と ワールド・リーチ・プロ の決勝（空）は直す前の値のまま。**今の本番のコード（列記号 `SELECT A,D,E,H WHERE G = "Y"`）は E列の ID を視聴URLとして読めないため、マージ前に帰り道の再生成が走ると `load_episodes()` で止まる**
- 今の公開物（WHS-02 で読んだ 2026-10-10 朝の旧シート、39話）との差（動画IDで突き合わせ）:

| 動画ID | 名前 | 差 | 決定での説明 |
|---|---|---|---|
| `6WAPjcxT78A` | 勝又健志 | 足された（タイトル 第3期JPML WRC-Rリーグ、決勝動画ID `q8z6HgpCXug`） | 足した回 |
| `tXbbwFA-bjg` | 紺野真太郎 | タイトル 第3期帝王戦 → 第3期小島武夫杯帝王戦 | 帝王戦2行 |
| `OoK3O2BCm8M` | 三浦智博 | タイトル 第4期帝王戦 → 第4期小島武夫杯帝王戦 | 帝王戦2行 |
| `76OsWTSSnso` | 内川幸太郎 | タイトル 世界麻雀TOKYO2025 → 第4回リーチ麻雀世界選手権 | 世界麻雀TOKYO2025 |
| `GjzVKdJ5jSM` | 三浦智博（第41期十段戦） | 決勝 空 → `amTs0IqIutE` | 十段戦2行（WHS-02 の照合の最終日と一致） |
| `WDRBtJJME7I` | 三浦智博（第40期十段戦） | 決勝 空 → `x5eNiaZK9r0` | 十段戦2行（同上） |
| `o28svvuVI0M` | タマシュ・エルドス | 決勝 空 → `-` | ワールド・リーチ・プロ |

- 消えた回は無い。名前の差は無い。動画IDの重複は無い。決勝動画を全編に直した回は無かった（小車祥・横田幸太朗の冒頭版はそのまま）
- 以上で説明できない差は無い → 進める

### 手順2: 読み先

- 帰り道のシートを読むのは `lib/wayhome.py` を通す4本（`generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`fetch_youtube_meta.py`・`build_wayhome_ogp.py`）だけ。title/ ・ jpml_pros など、ほかのページは読んでいない
  （`generate_jpml_test.py` などが旧ブックの ID を持つが、別のタブ）
- 未マージのブランチで `lib/wayhome.py`・4本・`lib/sheets.py`・文書に触れるのは `work/1008-hou` の `docs/notes/static-generation.md`（252行と表の行の追加〈268行付近〉）だけ。こちらは 264行の1行で重ならない
- `lib/wayhome.py`:
  - `SPREADSHEET_ID = live.SPREADSHEET_ID`。`HEADERS = ("名前", "タイトル", "動画ID", "決勝動画ID", "表示")` を `lib/sheets.fetch_records()`（`resource_dictionary` と同じ。先頭のタブの判定は `live.FIRST_SHEET`）で読む `fetch_rows()` を足し、列記号の `COLUMNS`・`QUERY` と位置で読む `to_rows()` をやめた
  - `to_rows(records)` は表示=Y の行だけを `WayhomeRow(interviewee, title, url, final_video_url)` にする（今までと同じ形。呼び出し側の `row.url`・`video_id_from_watch_url()` はそのまま）。
    URL は `https://www.youtube.com/watch?v=<ID>`。決勝動画IDの空と `-`（`NO_FINAL_VIDEO`）は空文字にして「決勝戦を見る」を出さない
  - `load_episodes()`: JSON に無い回は外して続け、`::warning::` の1行を標準出力に出す（決定3の生成側）
- 4本は `wayhome.to_rows(fetch_sheet(..., QUERY))` を `wayhome.fetch_rows()` に替え、使わなくなった import・定数とコメント（E列・H列・B列）を直した
- テスト `scripts/tests/test_wayhome_rows.py`（5件）: 動画ID・決勝動画IDから URL、空と `-`、表示=Y だけ、見出しの名前で読む（`fetch_records` に渡す見出し）、JSON に無い回を外して警告。
  修正前のコード（`git archive HEAD` を一時の場所に展開）では5件とも失敗、修正後は全体 726件 OK
- 文書: `docs/notes/video-wayhome.md`（「新しい回を追加する手順」の書く列と `-`・JSON に無いときの動き、新しい節「シートを移し、見出しの名前で読む」、決勝動画のデータ元、#192 第2段の「生成を止める」、WH-57 の【注意】）、
  `docs/notes/static-generation.md`（ページの一覧の `video_wayhome.html` の行）。決定は `docs/decisions/wayhome.md` に WHS-03 の節を足し、置き換えた WHS-02 の2行に「→ 置き換え」を付けた
- WHS-01 のログの状態: 着手時は「判断待ち / 辞書の件の続き: CHAT-1008-DIC-17 / 辞書の件: CHAT-1008-DIC-18 で解消」だった（DIC-17・DIC-18 の作業で書き足されていた）。指示のとおり「判断待ち / 続き: CHAT-1008-DIC-17」に直した（別のコミット 6dfbe99b）

### 手順3: 生成とプレビュー

- `python3 scripts/regenerate.py video_wayhome wayhome_episodes`: シート 40件、`6WAPjcxT78A` を外した警告が出て、各話 39ページ。sitemap・OGP（`index-20260921.jpg`）は変わらず
- 差分: `video_wayhome.html` と各話12ページ（58行の入れ替え）。旧版に「タイトル3つの置き換え」と「`https://www.youtube.com/live/3N2s-WC3nrQ` → `https://www.youtube.com/watch?v=3N2s-WC3nrQ`」を当てると、
  11ファイルは新版と完全に一致し、残る2ファイル（`GjzVKdJ5jSM`・`WDRBtJJME7I`）の差はヒーローの「決勝戦を見る」1つの追加だけ（スクリプトで比較）
  - タイトルの置き換えは、その3回のページ（title・description・og・JSON-LD・h1・パンくず）と、一覧のカード、前後・同じ選手のカードに出る（各話9ページ＋一覧）
  - **見込みに無かった差**: 最新話（`2Bn3SktouP4`）の決勝動画の URL が `/live/<ID>` から `watch?v=<ID>` になった（一覧のヒーローと最新話のページ。同じ動画）。旧シートで `/live/` の形はこの1本だけで、値を ID にしたことによる（決定で説明できる）
- push（11f57780・6dfbe99b）→「Workers Builds: mj」success（6dfbe99b）。プレビューの3ページ（一覧・`OoK3O2BCm8M`・`GjzVKdJ5jSM`）は 200 で、手元の生成物とバイト単位で一致
- #194 に進みをコメントした

## 報告

- 状態: 判断待ち / 続き: CHAT-1010-WHS-04
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: プレビューあり（URL は最終報告）。見るページは一覧・タイトルを直した回 `OoK3O2BCm8M`（第4期小島武夫杯帝王戦 三浦智博）・決勝動画を埋めた回 `GjzVKdJ5jSM`（第41期十段戦 三浦智博）
- マージ: 未（平野さんの判断待ち。指示のとおり）
- issue: #194（進みをコメント）
- 判断が必要なこと:
  - プレビューを見てマージするか（差分はシートの変化で説明できるものだけ。見込みに無かったのは最新話の決勝動画の URL が `/live/<ID>` から `watch?v=<ID>` になった1本）
  - マージを急ぐか: 旧ブックの「帰り道」タブも列が変わっているため、マージ前に本番で帰り道の再生成（月曜 05:37 JST の週次 `all` など）が走ると止まる
  - `6WAPjcxT78A` は `data/youtube_meta.json` に無いため外れたまま。載せるには `fetch_youtube_meta.py` の実行（手元の鍵か、次の指示のワークフロー）が要る
- 未確認の項目:
  - プレビューのブラウザでの見え方（HTML が手元の生成物と一致することまでは確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a79dfa83）: https://github.com/retroeater/mj-logs/tree/main/guide/a79dfa83

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a79dfa83/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
