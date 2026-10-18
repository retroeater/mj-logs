# CHAT-1018-SRG-01

- 着手日時: 2026-10-18
- 対象issue: 未定（手順1で起票）
- ブランチ: work/1018-srg
- 着手時HEAD: 6a2b5b8

## 指示

【Claude作成】Claude Code 向け指示：「辞書」「書籍」シートを新しいスプレッドシートから読むように切り替える Chat-Ref: CHAT-1018-SRG-01 マージ: 承認済み（チャットで）。ただし下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-srg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-srg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
スプレッドシートの再編の一部。「辞書」「書籍」シートを ryoei.pro_その他マスタから新しいスプレッドシートへ移し、生成の読む先を切り替える。新しいスプレッドシートで動作を確かめられたら、平野さんが元のシートを消す（CHAT-1014-PLB-01〜06 の選手マスタの移行と同じ流れ）。
決定（2026-10-18、平野さん）

* 「辞書」「書籍」シートは、新しいスプレッドシート（ID `1WpSXlEKHw5-GUIxH0G9eSW2TUHRh6oc56FX57jRNqOM`）から読むようにする
* 新しいスプレッドシートの共有は「リンクを知っている全員」が閲覧可。「live…」のアカウントに編集者の権限を付けた（平野さん）
* 新しいスプレッドシートで動作を確かめられたら、元のスプレッドシートの「辞書」「書籍」シートは平野さんが消す
* このマージは、下の「止まる条件」の確かめが通れば承認する

前提（チャット側。平野さんの決定ではない）

* 元のスプレッドシートは ryoei.pro_その他マスタ（`live.SPREADSHEET_ID`、要確認）。docs/notes/static-generation.md には「辞書」シートは「連盟プロ以外と同じブック」とあるが、「連盟プロ以外」は 2026-10-14 に ryoei.pro_選手へ移っているため、この記述は古い（要確認。直す対象）
* 読み手の候補（チャット側がガイドの文書で見たもの。コードで洗い出し直すこと）:
   * 「辞書」: `scripts/generate_resource_dictionary.py`（`--check` を含む）
   * 「書籍」: `scripts/generate_books_pages.py`（2026-09-22 から凍結中。`regenerate.py` の `FROZEN_FROM_ALL`）、`scripts/sync_books_calendar.py`（ワークフローは 2026-10-14 に削除）、`scripts/fetch_rakuten_books.py`（要確認）。凍結中のものも、再開したときに古い読む先が残らないよう、読む先は切り替える（凍結は解かない）
   * `live.SPREADSHEET_ID` は「タイトル戦」「リーグ」なども読むので、定数を分ける必要がありそう（要確認）。ほかのシートの読む先は変えない
* チャット側は新しいスプレッドシートのシート名・中身を読めていない（未確認）。名前（スプレッドシートの題）も手順2で確かめる
* 「live…」のアカウントが何に使うものかはチャット側で確かめていない。編集者の権限はこの指示では使わない
* docs/notes/glossary.md に従い、ログ・文書では「スプレッドシート／シート」と書く

手順

1. 同じ主題の issue（クローズ済みを含む。「辞書 シート 移行」「書籍 シート」「スプレッドシート 再編」等）と未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめる。同じ主題の open issue が無ければ issue「「辞書」「書籍」シートを新しいスプレッドシートへ移す」を起票し（本文に上の決定と、マージ後の平野さんの手作業〈動作の確認の後に元の2シートを消す〉を書く）、着手中のコメントを残す。
2. 新しいスプレッドシートの確かめ: スプレッドシートの題・シート名の一覧をログに書く。「辞書」「書籍」を生成と同じ経路（`lib/sheets.py`）で読み、元の2シートと見出しの並び・行数・値を比べてシートごとにログに書く（違う行はすべて、行と列を書く）。違いがあれば切り替えずに止まる。一致すれば、読み手をコードで洗い出して（一覧をログに書く）読む先を新しいスプレッドシートに切り替え、元のスプレッドシートを「辞書」「書籍」の置き場所として書いている文書（docs/notes/static-generation.md・books-freeze.md・books-calendar.md・books-covers.md など）・案内文を直す。
3. 確かめ: `python3 scripts/generate_resource_dictionary.py --check` を作業ブランチで回し、止まる理由が無いことと、カテゴリごとの語数をログに書く。同じ時点で、origin/cloudflare のコードと作業ブランチのコードの両方で `python3 scripts/regenerate.py all` を実行し、生成物の差を比べる（見込みは「差なし」。新しいシートは元の値の写しで、凍結中の書籍のページは all に含まれない）。書籍の読み手は、ファイルを書かずにシートを読める形（`--check`・dry-run など、あるもの）で新しいスプレッドシートを読めることを確かめる（無ければ、読み込みの関数をテストから呼ぶ形でよい。どう確かめたかを書く）。そのうえで cloudflare へマージし、マージ後の check-run と本番の resource_dictionary.html の語数を確かめる。

止まる条件

* 手順2で、新しいスプレッドシートに「辞書」「書籍」のどちらかが無い、または元のシートと見出し・行数・値が一致しない（切り替えずに、違いを全部書いて止まる）
* 読むたびに行数が変わる（平野さんがシートを編集中の可能性。件数を書いて止まる）
* 手順3で `--check` に止まる理由が出る、または2つのコードの生成物に差がある（差の種類と件数・対象を書いて止まる。差を直そうとしない）
* 同じ主題の open issue、または同じ目的の未マージの work/ ブランチがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。文書の表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 決定・前提が実物と食い違い、どちらが正か平野さんの判断が要る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんの手作業（元のスプレッドシートの「辞書」「書籍」シートの削除）と、その時機（このマージと本番の確かめの後。消した後に `--check` を回して確かめる指示をチャット側が出す）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-SRG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-SRG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子 SRG の重複: 全ブランチのコミット・docs/logs の履歴ともに無し（unshallow 済みで確認）
- 作業ブランチ: ローカル・リモートとも work/1018-srg が無かったため `git checkout -b work/1018-srg origin/cloudflare`（6a2b5b8）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも揃っている
- 0. 指示欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1018-srg
- ログ: https://github.com/retroeater/mj/blob/work/1018-srg/docs/logs/CHAT-1018-SRG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-srg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6a2b5b89）: https://github.com/retroeater/mj-logs/tree/main/guide/6a2b5b89

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6a2b5b89/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8c038f0d.md
