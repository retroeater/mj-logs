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

## 報告

- 状態: 作業中
- ブランチ: work/1014-plb
- ログ: https://github.com/retroeater/mj/blob/work/1014-plb/docs/logs/CHAT-1014-PLB-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-plb
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f8f8ab2）: https://github.com/retroeater/mj-logs/tree/main/guide/6f8f8ab2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a31620fe.md
