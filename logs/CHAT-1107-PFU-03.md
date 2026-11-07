# CHAT-1107-PFU-03

- 着手日時: 2026-11-07
- 対象issue: （起票予定）
- ブランチ: work/1107-pfu
- 着手時HEAD: bd37332c9

## 指示

【Claude作成】Claude Code 向け指示：「成績」ページ（rh_results.html）を「成績詳細」シートから集計して作る（「成績」シートを読まなくする） Chat-Ref: CHAT-1107-PFU-03 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-pfu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1107-pfu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-pfu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続けて、CHAT-1107-PFU-02 のログの `## 報告` の状態「判断待ち」の末尾に `/ 続き: CHAT-1107-PFU-03` を足す。

目的
「成績」シートは「成績詳細」シートを年ごとに集計した数式だけでできている（CHAT-1107-PFU-02 の手順3、チャット側が平野さんの Excel の書き出しで数式を確かめた）。入力を「成績詳細」の1か所にするため、`generate_rh_results.py` が「成績詳細」から集計して `rh_results.html` を作る形に変え、「成績」シートを読まなくする。ページの見た目は変えない。
決定（2026-11-07、平野さん）

* 「成績詳細」シートを元データとして残し、「成績」シートを消す。年ごとの集計は生成スクリプトが「成績詳細」から計算する（案 A。「成績詳細を成績から導いて消す」という当初の意向を置き換える）
* 「成績」シートは、この変更が本番に入った後に平野さんが消す（この指示ではシートに触れない）
* 下の「止まる条件」の確かめがすべて通れば、判断待ちにせずそのまま cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 「成績」シートの数式（平野さんの Excel の書き出し、2026-11-07 時点。チャット側が読んだ）: 行は「通算」と「2026年」〜「2014年」（2020・2021年は行が無い）。各年は「成績詳細」の B列（ID、例 `2026-01-24-01`）の先頭が年に一致する行を SUMIF で集める（「通算」は全行）
   * 得点＝S列の合計
   * 半荘数＝AB列の合計（AB＝X〜AA の和。X〜AA は R列〈着順〉の文字のうち「1」「2」「3」「4」の個数）
   * 平均順位＝（1着数×1＋2着数×2＋3着数×3＋4着数×4）÷半荘数
   * トップ率＝1着数÷半荘数
   * 4位回避率＝1−4着数÷半荘数
   * 数式は W列（表示）で絞っていない。今は 321行すべてが Y
* 集計の範囲（チャット側の案）: ページの成績詳細と同じく W＝"Y" の行だけを集める（今は全行 Y なので結果は同じ）。年は A列（日付）の年で決める（PFU-02 で A の年で集めて一致を確かめた。B の先頭と食い違う行があれば止める）
* R列（着順）の文字に「1」〜「4」以外が混ざる行・空の行があれば、生成を止めて行（ID）を出す
* 行は「通算」を先頭に、対局のある年を新しい順に並べる（対局の無い年の行は出さない。今の 2020・2021年と同じ）。新しい年は「成績詳細」に行を足せば自動で出る
* 表示の書式（桁区切り・小数の桁・％か小数か）は、今の `rh_results.html`（cloudflare の生成物）の表の文字に合わせてコードで持つ（PFU-02 の時点では書式はシートの表示形式に任せている）
* 「成績詳細」を読むときは `fetch_sheet()` の今の読み方（列記号で読む・`check_not_filtered`）のまま。読む列は A・B・R・S・W（`rh_results_detail` と同じブック・タブ。Sheets API の呼び出しの回数は報告に書く）
* `scripts/check_sheets_reader.py` の BOOKS の「mj_ryoei」は ID を `generate_rh_results.py` の定数から読んでいる（PFU-02）。定数の名前・置き場所を変えるなら、こちらも合わせる
* 平野さんがマージ後に「成績」シートを消したら、別の指示で読む処理が無いことと、毎朝の再生成が通ることを確かめ、起票した issue を閉じる

手順

1. 同じ主題の issue（クローズ済みを含む。検索語「成績詳細」「rh_results」）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、同じ目的のものがあれば止まる。 無ければ issue「良栄 > 成績を『成績詳細』から集計し、『成績』シートを消す」を起票して着手中のコメントを残し、#673 にリンクのコメントを残す。
2. `generate_rh_results.py` を「成績詳細」から集計する形に書き換え、「成績」シートを読む処理をなくす。集計（年の判定・着順の数え方・各指標・行の並び・書式）の単体テストを `scripts/tests/` に足す（着順に 1〜4 以外がある・空・A と B の年が食い違う、の3つで止まることを含む）。 `docs/notes/static-generation.md` など `rh_results` の読み方を書いた文書と `docs/decisions/` を直す。`git grep -n '"成績"'` などで「成績」シートを読む処理が残っていないことを確かめ、全件をログに書く（シート名の「成績詳細」・ページの見出しの「成績」は除く）。
3. 確かめとマージ: `regenerate-page.yml` を作業ブランチの ref・入力 `target_page: rh_results` で `dry_run`（または `compare_ref`）で手動実行し、作った `rh_results.html` の表を cloudflare の `rh_results.html` の表と比べる（12行×6列の全セルの文字。差があれば全セルを表にして書く）。 止まる条件にすべて当たらなければマージする。マージ後に push で動く `regenerate-page.yml` が `rh_results.html` を変えるコミットを作らないことを確かめる（作ったら差分をログに書く）。issue はクローズしない（「状況: 待ち」のラベルがあれば付け、平野さんのシートの削除を待つとコメントする）。

止まる条件

* 同じ目的の issue か未マージの work/ ブランチがある
* 未マージの work/ ブランチが `generate_rh_results.py`・`scripts/check_sheets_reader.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 作った `rh_results.html` の表が、cloudflare の `rh_results.html` の表と1セルでも違う（行の数・並び・桁区切り・小数の桁を含む）
* 「成績詳細」の R列に 1〜4 以外の文字がある・空の行がある・A の年と B の先頭の年が食い違う行がある（行の ID を書いて止まる。シートは直さない）
* 手順2の `git grep` で「成績」シートを読む処理が1件でも残る
* テストが1件でも落ちる（自分の変更で落ちると分かっているテストは直してよい）
* 差分に、生成スクリプト・`scripts/check_sheets_reader.py`・テスト・文書（`docs/`）以外の変更がある（生成物の `rh_results.html` が変わるのは止まる条件の3つ目で止まる）
* マージ後の check-run・push で動く再生成が今回の変更で失敗する（無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（集計の範囲を W＝"Y" にしたこと、Sheets API の呼び出しの回数、起票した issue の番号を含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-PFU-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-PFU-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1107-pfu
- ログ: https://github.com/retroeater/mj/blob/work/1107-pfu/docs/logs/CHAT-1107-PFU-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-pfu
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1e1ee61d）: https://github.com/retroeater/mj-logs/tree/main/guide/1e1ee61d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
