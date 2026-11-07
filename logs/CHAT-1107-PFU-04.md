# CHAT-1107-PFU-04

- 着手日時: 2026-11-07
- 対象issue: #674
- ブランチ: work/1107-pfu
- 着手時HEAD: 8dfc39bf7

## 指示

【Claude作成】Claude Code 向け指示：成績・成績詳細の生成を「R_タイトル戦_成績」の「良栄」シートへ切り替え、見出しの名前で読む形にする Chat-Ref: CHAT-1107-PFU-04 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも（2026-11-08 04:00 JST の毎朝の再生成より前に本番に入れたい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-pfu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1107-pfu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-pfu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが「成績詳細」シートを「R_タイトル戦_成績」の「良栄」シートへ移し、要らない列を消した。`rh_results`・`rh_results_detail` の生成を新しいシートから読むように切り替え、列の位置ではなく見出しの名前で読む形にする（列を足し引きしてもずれないように）。ページの中身は変えない。
決定（2026-11-07、平野さん）

* 「成績」シート（ブック「mj_ryoei」）は消した（#674）
* 「成績詳細」シートを、ブック「R_タイトル戦_成績」の「良栄」シートへ移した。今後はこちらを読む。旧シートは消した
* 「良栄」シートから次の列を消した: Organization（旧 D）・Title（旧 F）・Tournament（旧 H）・Player 1〜4（旧 N〜Q）・Results（旧 U）
* 下の「止まる条件」の確かめがすべて通れば、判断待ちにせずそのまま cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 列を消した後の「良栄」シートの見出し（チャット側の見込み。要確認）: 日付・ID・団体・タイトル・対局・対局者・対局者1〜4・着順・得点・結果・twitter_url・表示・1着・2着・3着・4着・合計（20列、A〜T）。旧の位置の A・B・C・E・G・I・R・S・T・V・W は、もう同じ位置にない
* 生成が読む見出し: `rh_results`＝日付・ID・着順・得点・表示、`rh_results_detail`＝日付・団体・タイトル・対局・対局者・着順・得点・結果・twitter_url・表示（PFU-02・PFU-03 の読み方の列に当たる名前）。見出しの名前で読む仕組みが `scripts/lib/` に既にあれば、それを使う（例: 「リーグ」タブの `league_maps()`。要確認）。要る見出しが無い・同じ見出しが2つあるときは止める
* ブック「R_タイトル戦_成績」は、ほかの生成も読んでいる定数がある（定数名は `git grep` で確かめる）。今回はその定数を使い、ID を新しく書かない。#662（Sheets API の1分あたりの読み取り上限）があるので、読み取りの回数は報告に書く
* ブック「mj_ryoei」の扱い（要確認）: 平野さんの「旧シートは消した」は、タブを消したのか、ブックごと消したのかを確かめていない。`check-sheets-reader.yml`（読むだけ）で mj_ryoei が読めるかとタブの一覧を見て、どちらかをログに書く。 読めなくなっていれば、`scripts/check_sheets_reader.py` の BOOKS から mj_ryoei を外す。読めるならブックは残っているので、タブの一覧を報告に書いて、外さずに止める（ほかに読む処理が無いことだけ確かめる）
* 「1着」〜「合計」の列は、もう生成に使わない（PFU-03 で集計をコードに移した）。この指示では読まず、消すかは平野さんが決める

手順

1. 確かめ: 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）が `generate_rh_results.py`・`generate_rh_results_detail.py`・`scripts/check_sheets_reader.py` の同じ行・同じ関数を変えていないか見る。`check-sheets-reader.yml` を作業ブランチで手動実行し、「R_タイトル戦_成績」の「良栄」の行数・列数と見出し（1行目の全セル）、mj_ryoei の状態（上の前提）をログに書く。 `git grep` で mj_ryoei の定数（`SPREADSHEET_ID` を含む）と「成績詳細」のシート名を読む処理を全件洗い、ログに書く（除外: `docs/decisions/` の過去の行・`docs/logs/`・`docs/notes/handover-archive-2026.md`）。
2. 書き換え: 2つの生成スクリプトを「R_タイトル戦_成績」の「良栄」から見出しの名前で読む形にし、mj_ryoei の定数を消す（`check_sheets_reader.py` は手順1の結果に従う）。見出しの名前で読むことと、要る見出しが欠けたり重なったりしたら止まることを、テストに足す。 `docs/notes/static-generation.md` など、2ページの元データ（ブック・タブ・列）を書いた文書と `docs/decisions/` を直す。
3. 確かめとマージ: `regenerate-page.yml` を作業ブランチの ref で、入力 `check` と `compare_ref`（または `dry_run`）で手動実行する。`target_page: rh_results` と `target_page: rh_results_detail` の2つで、作った2ページを cloudflare のものと比べ、違う行があれば全行をログに書く。 止まる条件にすべて当たらなければマージする。マージ後に push で動く `regenerate-page.yml` が2ページを変えるコミットを作らないことを確かめる。済んだら #674 に結果（mj_ryoei の状態を含む）をコメントしてクローズする（「状況:」ラベルを外す）。

止まる条件

* 未マージの work/ ブランチが、上の3つのファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 「良栄」シートが読めない、または生成に要る見出しが1つでも無い・同じ見出しが2つある（見出しの一覧を書いて止まる。シートは直さない）
* mj_ryoei がまだ読める（タブの一覧を書いて止まる。生成の書き換えはマージせずに作業ブランチに残す）
* 手順1の `git grep` で、2つの生成スクリプトと `check_sheets_reader.py` のほかに mj_ryoei・「成績詳細」を読む処理が見つかる
* 作った `rh_results.html`・`rh_results_detail.html` が cloudflare のものとファイル全体で1行でも違う（全行をログに書いて止まる）
* テストが1件でも落ちる（自分の変更で落ちると分かっているテストは直してよい）
* 差分に、生成スクリプト・`scripts/check_sheets_reader.py`・`scripts/lib/`（見出しで読む仕組みを足す場合だけ）・テスト・文書（`docs/`）以外の変更がある
* `scripts/lib/` を変えた場合に、CLAUDE.md「判断・作業の原則」の全ページの再生成で、2ページのほかに変わるページがある（待つ時間は同じセッションで合計45分まで延長してよい）
* マージ後の check-run・push で動く再生成が今回の変更で失敗する（無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（mj_ryoei の状態、「良栄」の見出しの一覧、Sheets API の呼び出しの回数を含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-PFU-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-PFU-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1107-pfu
- ログ: https://github.com/retroeater/mj/blob/work/1107-pfu/docs/logs/CHAT-1107-PFU-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-pfu
- 確認用URL: なし
- マージ: 未
- issue: #674
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8dfc39bf）: https://github.com/retroeater/mj-logs/tree/main/guide/8dfc39bf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
