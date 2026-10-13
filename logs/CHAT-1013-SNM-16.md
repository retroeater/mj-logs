# CHAT-1013-SNM-16

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-snm-league
- 着手時HEAD: 9bba7d550

## 指示

【Claude作成】Claude Code 向け指示：成績の集計が読む「リーグ」タブを正本からその他マスタに切り替え、正本の「リーグ」タブを消しても止まらない形にする。check_leagues_dropped.py の故障を直す（CHAT-1013-SNM-15 の続き） Chat-Ref: CHAT-1013-SNM-16 マージ: 承認済み（チャットで。2026-10-13 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認） 貼る時機: 平野さんがその他マスタの「リーグ」タブで、女流桜花の ID 0 の名前を「女流桜花」から「桜花」に直した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-snm-league の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜15 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する。work/1013-snm-league を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-snm-league origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1013-SNM-15 のログの `## 報告` を読み、状態が「完了」でなければ何もせず止まる。その他マスタの「リーグ」タブを読み、女流桜花の ID 0 の名前が「桜花」になっていることを確かめる（なっていなければ何もせず止まる）。

目的
CHAT-1013-SNM-15 の調べで、正本の「リーグ」タブを読むのは `lib/pro_stats.py`（jpml_pros の成績の集計）だけで、数式は引いていないと分かった。読む先を、平野さんが移したその他マスタの「リーグ」タブに変える。あわせて、正本の「リーグ」タブ（今の先頭のタブ）を消したときに、先頭のタブの名前に頼る判定で全ページの生成が止まらない形にする。手動実行の検査 `check_leagues_dropped.py` が今の cloudflare で止まる件も直す。正本の「リーグ」タブは、このマージの後に平野さんが消す。
決定（2026-10-13、平野さん）

* 成績の集計が読む「リーグ」タブは、正本ではなくその他マスタの「リーグ」タブにする（正本の「リーグ」タブは消す）
* 女流桜花の ID 0 の名前は「桜花」にそろえる（その他マスタの「女流桜花」を平野さんが直す。jpml_pros の表示は今と同じ）
* `check_leagues_dropped.py` の故障は、この指示で直す
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* CHAT-1013-SNM-15 のログ `## 報告`「まとめ (2)」の直す箇所: `lib/pro_stats.py` の `fetch_sources()`・`league_maps()`（その他マスタの「リーグ」は縦の並び〈タイトル戦・リーグ名・リーグID〉）、テスト、文書。その他マスタの先頭のタブが「リーグ」になり、`lib/live.FIRST_SHEET`（「連盟プロ以外」）と実際の先頭が食い違っている。正本の `FIRST_SHEET = "リーグ"` が4か所（`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/houou_tab.py`・`generate_houou_race.py`）にあり、正本の「リーグ」タブを消すと「「プロ」シートがありません」で全ページの生成が止まる
* 先頭のタブとの比べは、gviz が無いタブ名を渡されると先頭のタブを返すことへの備え（無いタブを読んだことに気づくため）。`lib/sheets._sheet_gids()`（htmlview のタブの一覧）がある（CHAT-1012-SNM-04 のログ）
* `check_leagues_dropped.py` は `generate_houou_leagues.QUERY` が c28057dc で無くなって止まる（SNM-15 のログ「気づいたこと」）
* 使う skill は無い

手順

1. 確かめ: `git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか確かめる。その他マスタの「リーグ」タブと正本の「リーグ」タブを読み、ID 0 の名前を除いても値の対応（鳳凰戦・女流桜花の ID とリーグ名）が同じであることを確かめる（違いがあれば行ごとに書く）
2. 直す:
   * `lib/pro_stats.py` がその他マスタの「リーグ」タブを見出しで読む形にする（テスト・文書も）
   * 無いタブを読んだことの判定を、先頭のタブの名前に頼らない形にする（タブの一覧で有無を確かめる、など。方法は任せる）。正本とその他マスタの両方で、先頭のタブを消したり並べ替えたりしても止まらないようにする。`FIRST_SHEET` の定数は要らなくなれば消し、残すなら実際の先頭と合わせる
   * `check_leagues_dropped.py` を今のコードで動く形に直す（直した後に手元で動かし、結果をログに書く）
3. 確かめてマージ:
   * (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる
   * (b) 正本の「リーグ」タブが無い模擬（タブの一覧と読み取りの差し替え。シートは変えない）と、その他マスタの先頭のタブを「連盟プロ以外」に戻した模擬で、直した後のコードの全ページを生成し、(a) の直した後と比べる
   * 止まる条件に当たらなければ、docs/decisions/pros.md に上の「決定」を足し、cloudflare へマージする。ログの `## 報告` の項目の後ろに、平野さんがこの後にすること（正本の「リーグ」タブを消す。消してよい時機）を書く

止まる条件

* その他マスタと正本の「リーグ」タブで、ID 0 の名前のほかに値の対応が違う（行ごとに書いて止まる）
* (a) で生成物に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる（books_pages が「書籍」の行数で止まるのは、これまでと同じなら許す）
* (b) で (a) の直した後と差がある、またはページが止まる
* 無いタブを読んだことに気づけなくなる（タブ名の誤りで別のタブの中身を読んでしまう）形になる
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の比較、直した箇所（ファイル・関数・何を変えたか）、`check_leagues_dropped.py` を動かした結果、テストの結果、(a)(b) の結果、直した文書の一覧がログにある
* マージ後の自動再生成（regenerate-page.yml、push）の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-SNM-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-SNM-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし。work/1013-snm-league はローカル・リモートとも無かったので origin/cloudflare（9bba7d55）から作った
- CHAT-1013-SNM-15 の `## 報告` は 状態「完了」
- その他マスタの「リーグ」タブ（2回読んで 見出し タイトル戦・リーグ名・リーグID、20行）で、女流桜花の ID 0 は「桜花」

### 手順1 確かめ

- 正本の「リーグ」（今の `league_maps()` で読んだもの）とその他マスタの「リーグ」を ID ごとに比べた: 鳳凰戦 0〜13（鳳凰位・A1〜E3）、女流桜花 0〜5（桜花・A・B・C1・C2・C3）の20組がすべて同じ。違い 0
- 未マージのブランチとの重なり: 直すファイルを変えているのは `origin/work/1012-dsn` の `scripts/generate_houou_pages.py` だけ。dsn の変更は72行目以降（表示の部品）で、こちらは52行目（`FIRST_SHEET` の定数を消す）と110行目（`fetch_records()` の引数）。別の箇所

### 手順2 直した箇所（25c2299d・c7700d74）

| ファイル・関数 | 何を変えたか |
|---|---|
| `scripts/lib/sheets.py` `check_sheet_exists()`（新） | タブの有無を htmlview のタブの一覧（`_sheet_gids()`、フィルタの確認で前から読んでいるもの）で確かめ、無いタブ名は「「…」シートがありません(タブ: …)」で止める。先頭のタブの名前には頼らない。一覧が読めないときだけ、読んだ中身が先頭のタブと同じなら止める（無いタブを読んだかもしれないため） |
| `scripts/lib/sheets.py` `fetch_records()`・`fetch_table()` | 引数 `first_sheet` を消し、`check_sheet_exists()` を使う（フィルタの確認より先に） |
| `FIRST_SHEET` の定数（`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/houou_tab.py`・`lib/live.py`・`lib/live_layer3.py`・`generate_houou_race.py`・`generate_houou_pages.py`） | 消した |
| 呼び出し35か所（23ファイル） | 最後の引数（先頭のタブ名）を消した。機械的に置き換え |
| `scripts/lib/pro_stats.py` `league_maps()`・`fetch_sources()` | その他マスタ（`LEAGUE_SPREADSHEET_ID = live.SPREADSHEET_ID`）の「リーグ」を、見出し タイトル戦・リーグ名・リーグID で読む。「鳳凰戦」「女流桜花」で分ける。見出しの欠け・重複、同じタイトル戦の ID の重複、どちらかのタイトル戦の行が無いときは止める |
| `scripts/check_leagues_dropped.py` `analyze()` | 生成と同じ `mod.load()`（鳳凰は見出しで読む）で行と候補を読む。`generate_houou_leagues.QUERY` を読まない |
| テスト | `test_sheets_filter.py` に `SheetExistsTest`（先頭のタブを名前に頼らず通す・中身が違っても無いタブは止める・一覧が読めないときの扱い）、`test_pro_stats.py` の `LeagueMapsTest` を縦の並びに、`test_sns_book.py` の差し替えの引数 |

- `python3 -m unittest discover -s scripts/tests`: 984件 OK
- `check_leagues_dropped.py` を手元で動かした: rc=0。鳳凰: 行 16,011・期 52・候補 715・option 690・データのある選手 1,296・候補に無い 606・データの無い候補 25・「#127以前は選べたが、いま選べない」67名。桜花: 行 1,561・期 20・候補 177・option 163・データのある選手 248・候補に無い 85・データの無い候補 14・同 13名。直近（43前・42後）まで出ていた人（林潤一郎・髙田翔ほか。桜花は土田小緒里ほか）も並ぶが、どれも「プロ」にも名簿にもいない（退会・改名。樫野凪は「別名」で「かしのなぎ」）。参考情報で、直すことは無い

文書: `docs/notes/static-generation.md`（タブの有無の確かめ方、jpml_pros の成績の「リーグ」の出どころと読み方）、`docs/notes/live-page-design.md`（シートの読み方、「連盟プロ以外」が先頭のタブという記述を外した）、`docs/notes/title-pages.md`（同じ）

### 手順3 確かめ

(a) 今のシートで、直す前（origin/cloudflare の scripts/ を作業ツリーに戻して）と直した後を続けて、全ページを1ページずつ生成:

- 終了コードは同じ（22ページ rc=0、books_pages は前からの「書籍」95行で rc=3）
- 生成物の木（scripts/ を除いた作業ツリーを一時の index で書いた tree）が直す前と直した後で同じ（21ca6e97）。コミット済みの HEAD とも差 0。jpml_pros の「桜花最高」なども今と同じ

(b) 模擬（`lib.sheets._sheet_gids`・`_query` を差し替える `sitecustomize.py`。シートは変えない）:

| 模擬 | 内容 | 結果 |
|---|---|---|
| 正本の「リーグ」が無い | タブの一覧から「リーグ」を外し「プロ」を先頭に。無いタブ名には「プロ」の中身を返す | 全ページの終了コードが (a) と同じ、生成物の木も同じ（21ca6e97）。無いタブを読んだページは無い。「リーグ」を読むと「「リーグ」シートがありません」で止まる。**直す前のコードでは同じ模擬で jpml_pros・houou_race が「「プロ」シートがありません(gviz が先頭のタブを返しました)」で止まる** |
| その他マスタの先頭を「連盟プロ以外」に戻す | 一覧の先頭を「連盟プロ以外」に。無いタブ名には「連盟プロ以外」の中身を返す | 全ページの終了コード・生成物の木とも (a) と同じ。無いタブ名を読むと止まる |

ほかに、呼び出しを直したスクリプトを動かした: check_meibo・check_saikyo_unregistered・check_unused_names（`--no-issue`）・check_ampai・sync_birthday_calendar（既定）・`regenerate.py --check all`・check_wayhome は rc=0。sync_books_calendar（「書籍」95行）と sync_live_calendar（`google.auth` が無い）は、直す前のコードでも同じ理由で止まる

### マージ

- マージ前に cloudflare が進んでいた（9ded08d7。ほかの指示のログだけ）ので merge で取り込んだ（衝突なし）。テスト 984件 OK を確かめ、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1013-snm-league:cloudflare`。マージ: acb5aa04
- マージ後の自動再生成: regenerate-page.yml（push）の run 38348958077 は success。生成物に変わりが無く、再生成のコミットは無い

## 報告

- 状態: 完了
- ブランチ: work/1013-snm-league
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1013-SNM-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-snm-league
- 確認用URL: なし（生成物に差が無いため）
- マージ: 済（acb5aa04）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

### 平野さんがこの後にすること

- 正本の「リーグ」タブを消す。消してよい時機: いつでも（acb5aa04 のマージで、生成は正本の「リーグ」を読まず、先頭のタブの名前にも頼らなくなった。正本の「リーグ」を引く数式は0件〈CHAT-1013-SNM-15〉）。消した後の最初の再生成（push・毎日）が通ることを確かめるとよい
- その他マスタの「リーグ」タブは先頭のままでも、並べ替えてもよい

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj acb5aa04）: https://github.com/retroeater/mj-logs/tree/main/guide/acb5aa04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/acb5aa04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
