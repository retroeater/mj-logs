# CHAT-1014-PLB-01

- 着手日時: 2026-10-14
- 対象issue: 未定（手順1で確かめる）
- ブランチ: work/1014-plb
- 着手時HEAD: a31620f

## 指示

【Claude作成】Claude Code 向け指示：「プロ」「連盟プロ以外」「別名」を新しいブック「ryoei.pro_選手」から読むように切り替え、「桜花」「JWRC」タブを列記号でなく見出しで読む形に直す（不要な列を消せるようにする） Chat-Ref: CHAT-1014-PLB-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-plb を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-plb origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
スプレッドシートの整理（不要なデータの削除・冗長な構造の正規化）の一部。選手のマスタ3タブを1つのブックに集め、「桜花」「JWRC」の使っていない列を平野さんが消せるようにする。シートの列・タブの削除は、この作業のマージ後に平野さんが行う。
決定（2026-10-14、平野さん）

* 「プロ」「連盟プロ以外」「別名」は、新しいブック「ryoei.pro_選手」（ID `1ZYCEMu57jAngNHNxTh0xaFxRILqLerqSlIdzBLhR7wQ`）から読むようにする
* 「ryoei.pro_選手」の3タブは、元のタブの値を写したもの（元のタブとはつながっていない）
* 元のタブ（`pro_sheet.SPREADSHEET_ID` のブックの「プロ」、`live.SPREADSHEET_ID`〈ryoei.pro_その他マスタ〉の「連盟プロ以外」「別名」）は、名前に「(旧)」を付けて残し、1週間後を目途に消す（平野さんの手作業。名前の変更はこの作業のマージ後）
* 「ryoei.pro_選手」の共有は「リンクを知っている全員が閲覧可」にした（平野さん）
* 「桜花」タブの C・G・Q〜U・W 列（前後・結果・第9節〜第13節・プロ）と、「JWRC」タブの C〜E・G・N〜U・W 列（前後・リーグ・リーグキー・結果・第6節〜第13節・プロ）を消したい。そのためのコードの直しを、この指示に含めてよい
* `generate_jpml_test.py` 等のブック（`generate_jpml_test.SPREADSHEET_ID`）の「Ampai」タブは、平野さんが削除した（コードは読んでいない）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-14 に origin/cloudflare（a31620f）で確かめた読み手（作業の時点で洗い出し直すこと）:
   * 「プロ」: `scripts/lib/pro_sheet.py` の `SPREADSHEET_ID`・`SHEET_NAME`（`fetch_pros()` を通して多数のスクリプトが読む）
   * 「連盟プロ以外」「別名」: `scripts/lib/live.py` の `SPREADSHEET_ID` と `SHEET_OTHERS`・`SHEET_ALIASES`。同じ `live.SPREADSHEET_ID` を「タイトル戦」（`SHEET_TITLES`）や「リーグ」（`pro_stats` の `LEAGUE_SPREADSHEET_ID` 等）も使うので、定数を分ける必要がある（要確認）
   * `scripts/generate_title_pages.py` も `live.SPREADSHEET_ID` のブックの ID を直接持っている（要確認）
   * 案内文（issue の本文・ワークフローの通知・docs）に「プロ」「連盟プロ以外」「別名」のブックの URL・場所を書いている箇所があれば、新しいブックに直す
* 「桜花」「JWRC」の見出し（チャット側が gviz の html で読んだ、A〜X）: 名前・期・前後・リーグ・リーグキー・順位・結果・合計・第1節〜第13節・表示・プロ・備考（両タブ同じ並び）
* 「桜花」「JWRC」を列記号で読んでいる箇所（チャット側の確認。要確認）:
   * `ouka_results.js`・`wrc_results.js`（`SELECT A…U WHERE V = "Y"` と `getValue(i, 番号)`）
   * `league_ranking.js`（`getQueryString()` の列記号。「鳳凰」だけ `resolveHououQuery()` で見出しから列記号を引いている）
   * `scripts/generate_ouka_leagues.py`（`QUERY = 'SELECT A,B,C,D,F WHERE F > 0'` と行の位置）
   * `scripts/lib/pro_stats.py` は「桜花」を見出しで読むが、見出し「前後」を必須にしている（C 列を消すと止まる）
* 直し方の案: 列を消す前でも後でも同じ結果になる形（見出しの名前で列を引く）にする。JS は `resolveHououQuery()` と同じ考え方を「桜花」「JWRC」にも広げる。消す予定の列（前後・結果・第9節以降・プロ、JWRC はさらにリーグ・リーグキー・第6〜8節）を読まない・必須にしない。「鳳凰」タブの読み方は変えない
* 「ryoei.pro_選手」のタブの有無はチャット側が htmlview で確かめた（「プロ」「連盟プロ以外」「別名」の3つ）
* 関係する issue: #557（ブックの公開範囲の整理）、#547（成績8列の定義の見直し）、#7（Google Charts の旧方式のページ）。この作業の issue は無い（チャット側の検索、要確認）

手順

1. 同じ主題の issue（クローズ済みを含む。「ryoei.pro_選手」「ブックの整理」「桜花 列」等）と未マージの work/ ブランチを確かめる。無ければ issue「選手のマスタを ryoei.pro_選手 に移し、桜花・JWRC を見出しで読む」を起票し（本文に上の決定と、マージ後の平野さんの手作業〈元の3タブの改名・1週間後の削除、「桜花」「JWRC」の列の削除〉を書く）、着手中のコメントを残す。
2. 新しいブックの確かめ: 「ryoei.pro_選手」の3タブを、生成と同じ経路（`lib/sheets.py`）で読み、元の3タブと比べる。見出しの並び・行数・値が一致するかをタブごとにログに書く（違う行は名前と列を全部書く）。違いがあれば切り替えずに止まる。そのうえで、`pro_sheet`・`live`（と ID を直接持つ箇所）の読む先を新しいブックに切り替える。「タイトル戦」「リーグ」など他のタブの読む先は変えない。案内文・docs/notes（static-generation.md、live-page-design.md、sns-book.md など、元のブックを「プロ」「連盟プロ以外」「別名」の置き場所として書いている箇所）を直す。
3. 「桜花」「JWRC」を見出しで読む形に直す（上の4か所）。テスト（`scripts/tests/`）に、消す予定の列を除いた見出しの表でも同じ結果になることを足す。JS のページ（ouka_results・wrc_results・ouka_ranking・wrc_ranking）は、プレビューで列を消す前の今のシートで動くことを確かめる（届かなければその旨を書き「未確認の項目」へ）。
4. 全ページの確かめ: 同じ時点で、origin/cloudflare のコードと作業ブランチのコードの両方で `python3 scripts/regenerate.py all` を実行し、生成物の差を比べる。見込みは「差なし」（新しいブックは元の値の写しで、「桜花」の読み方の直しは結果を変えない）。差があれば種類ごとにログに書く。

止まる条件

* 手順2で、新しいブックの3タブと元の3タブの見出し・行数・値が一致しない（切り替えずに、違いを全部書いて止まる）
* 読むたびに行数が変わる（平野さんがシートを編集中の可能性）
* 手順4で、2つのコードの生成物に差がある（差の種類と件数・対象を書いて止まる。差を直そうとしない）
* 同じ主題の open issue、または同じ目的の未マージの work/ ブランチがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 決定・前提が実物と食い違う

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、マージ後に平野さんが行う手作業（元の3タブの「(旧)」への改名、「桜花」「JWRC」の列の削除）と、その順番の注意（改名・削除はマージ後）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-PLB-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-PLB-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 全部ある
- `git fetch --unshallow origin` の後、`CHAT-1014-PLB-01` のコミット・`PLB` の識別子（全ブランチのコミット・`docs/logs/` の履歴）: どちらも無し
- `work/1014-plb`: ローカル・リモートとも無し → `git checkout -b work/1014-plb origin/cloudflare`（a31620f）
- サイズ: 今回はガイド文（CLAUDE.md・handover.md）を変えていない

### 手順1: 同じ主題の issue・未マージのブランチ

- issue の検索（「ryoei.pro_選手 ブック 選手マスタ 移行」「桜花 JWRC 列 見出しで読む 列記号」「スプレッドシート ブックの整理 不要な列 タブ削除」「連盟プロ以外 別名 タブ 移す 新しいブック」、クローズ済みを含む）: 同じ主題は無し
  （近いもの: #544・#473・#536〈いずれもクローズ〉、#396・#397〈連盟プロ以外の値を埋める。主題は別〉）
- 未マージの work/ ブランチ（work/1009-swp-526・1011-swp-hnd・1011-swp-nav〈報告のみ〉、1014-jpl、1014-skd、1014-wfl-s3）: 同じ目的のものは無し。
  変える予定のファイルとの重なり:
  - work/1014-wfl-s3: `docs/notes/static-generation.md` の `check_leagues_dropped.py` の行（今回直す予定の箇所とは別の行）
  - work/1014-skd: `scripts/generate_saikyo_pages.py`（`load_name_book()` 付近の `live.SPREADSHEET_ID` の読む先を変える予定だった。skd 側の変更箇所は未確認のまま止まった）
  - work/1014-jpl（JPL-05）: いまは docs のみだが、WRC の旧名の読み替えを `generate_title_pages.py` 等に入れる予定の指示が進行中。今回 `generate_title_pages.py` の読む先を変える予定だった
- 起票: #567「選手のマスタを ryoei.pro_選手 に移し、桜花・JWRC を見出しで読む」（決定とマージ後の手作業を本文に書いた）。着手中のコメントを残した

### 読み手の洗い出し（origin/cloudflare a31620f）

前提のとおり。追加で分かったこと:

- 「連盟プロ以外」「別名」を `live.SPREADSHEET_ID` で読む箇所: generate_books_pages.py・generate_houou_race.py・generate_saikyo_pages.py（`load_name_book()`、generate_houou_pages.py・check_saikyo_unregistered.py が使う）・generate_ask_data.py・generate_live_pages.py・update_sns_book.py（write_live_channel_candidate.py は generate_live_pages の辞書を使う）
- `generate_title_pages.py` は `SPREADSHEET_ID`（=その他マスタ）1つで「タイトル」「タイトル戦」「連盟プロ以外」「別名」の4タブを読む（`fetch_tab()`）。タブごとに読む先を分ける必要がある
- 「プロ」は `pro_sheet.SPREADSHEET_ID` だけが読む（同じ ID を持つ他の定数は「鳳凰」「桜花」「最強戦」「対局」を読む）。`pro_sheet.SPREADSHEET_ID` を新しいブックに変えれば足りる
- JS（houou_results・ouka_results・wrc_results・league_ranking）は「プロ」「連盟プロ以外」「別名」を読まない

### 手順2: 新旧のブックの比較（止まる条件に当たった）

`lib/sheets.py` の `fetch_table()`（生成と同じ gviz の経路）と `fetch_csv()`（CSV エクスポート）で、旧・新を2回ずつ読んで比べた。
2回の読み込みで行数は変わらなかった。

| タブ | 見出しの並び | 行数（旧/新、見出しを除く） | 値の違う行 |
|---|---|---|---|
| プロ | **不一致**（下記） | 1099 / 1099 | 0 |
| 連盟プロ以外 | 一致（4列） | 745 / 745 | 0 |
| 別名 | 一致（4列） | 28 / 28 | 0 |

「プロ」の見出しの違い（gviz・CSV とも同じ。A1 のセルの文字が違う）:

- 旧: `['登録名\n0.74', 'ソートキー', '出身地', '表示']`
- 新: `['登録名', 'ソートキー', '出身地', '表示']`

旧の A1 は「登録名」の後にセル内改行で「0.74」が入っている（#467 で知られている形。`pro_sheet.label_keys()` は改行の前の「登録名」で引くため、生成はどちらでも同じ列を読む）。
新は「0.74」が無い（値の写しで A1 の数式・付記が落ちた可能性。平野さんの意図は確かめていない）。

指示の止まる条件「新しいブックの3タブと元の3タブの見出し・行数・値が一致しない」に当たるため、読む先の切り替え・手順3・手順4は行わずに止まった。コードは変えていない。

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-PLB-02
- ブランチ: work/1014-plb
- ログ: https://github.com/retroeater/mj/blob/work/1014-plb/docs/logs/CHAT-1014-PLB-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-plb
- 確認用URL: なし（コードを変えていない）
- マージ: 未（平野さんの判断待ち）
- issue: #567（起票・着手中）
- 判断が必要なこと:
  - 「プロ」タブの見出しの A1 が、旧「登録名\n0.74」・新「登録名」で違う（行数 1099・値は全行一致。「連盟プロ以外」「別名」は見出し・行数・値とも一致）。止まる条件に当たったため切り替えていない。
    この違いを許して切り替えを進めてよいか（生成は改行の前の「登録名」で引くので結果は変わらない見込み）、それとも新しいブックの A1 を旧と同じにするか
  - 続きの指示では、手順2の切り替え・手順3（桜花・JWRC を見出しで読む）・手順4（全ページの比較）が残っている
  - マージ後に平野さんが行う手作業（どれも切り替えのマージの後に行う。先に改名・削除すると今のコードが読めず生成が止まる）:
    1. 元の3タブ（正本の「プロ」、ryoei.pro_その他マスタの「連盟プロ以外」「別名」）の名前に「(旧)」を付ける
    2. 1週間後を目途に元の3タブを消す
    3. 「桜花」の C・G・Q〜U・W 列と「JWRC」の C〜E・G・N〜U・W 列を消す（見出しで読む直しのマージの後）
  - 未マージのブランチとの重なり: work/1014-jpl（JPL-05、`generate_title_pages.py` に手を入れる予定の指示が進行中）・work/1014-skd（`generate_saikyo_pages.py`）。続きの指示の着手時に同じ関数を触っていないか確かめる
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d933bbf）: https://github.com/retroeater/mj-logs/tree/main/guide/0d933bbf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d933bbf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
