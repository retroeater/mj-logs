# CHAT-1023-SRG-04

- 着手日時: 2026-10-23
- 対象issue: 未定（手順1で起票）
- ブランチ: work/1023-srg
- 着手時HEAD: 8255bdad8

## 指示

【Claude作成】Claude Code 向け指示：「R_Ampai」の「成績表」を「R_タイトル戦_マスタ」の「Ampai」シートから読むように切り替える Chat-Ref: CHAT-1023-SRG-04 マージ: 承認済み（チャットで）。新旧のシートの中身が一致し、新旧のコードで同じ時点に全ページを生成した差分が0で、下の「止まる条件」のどれにも当たらないときに限る 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-srg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-srg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
スプレッドシートの再編の続き（CHAT-1018-SRG-01〜03 の「辞書」「書籍」と同じ流れ）。「R_Ampai」（`ampai.SPREADSHEET_ID`）の「成績表」シートを、「R_タイトル戦_マスタ」（`live.SPREADSHEET_ID`）の「Ampai」シートから読むように切り替える。切り替えが済んだら、平野さんが「R_Ampai」をブックごと廃止する。
決定（2026-10-23、平野さん）

* 「R_Ampai」の「成績表」シートを、「R_タイトル戦_マスタ」の「Ampai」シートに移す。読む先を「R_タイトル戦_マスタ」の「Ampai」に切り替える
* 参照の切り替えが済んだら、「R_Ampai」はブックごと廃止する（平野さんの手作業）
* 新旧のシートの中身が一致し、新旧のコードで全ページを生成した差分が0なら、切り替えを cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 「R_タイトル戦_マスタ」には「Ampai」シートがすでにある（CHAT-1021-PRV-07 のログの表: 「Ampai」44行・7列。「R_Ampai」の「成績表」も44行・7列）。平野さんが写したもので、元とつながっていない見込み（要確認）
* 読み手は `scripts/lib/ampai.py`（`SPREADSHEET_ID`）を通す jpml_pros・houou_pages・`scripts/check_ampai.py`（check-meibo の週次）（CHAT-1021-PRV-01 の表、要確認。作業の時点で洗い出し直す）。`check_sheets_reader.py` も「R_Ampai」を対象に持つ（PRV-05）。docs/notes/static-generation.md にも「ryoau.pro_Ampai」ブックとして書かれている（要確認）
* 2026-10-21 から生成は Sheets API（sheets-reader）で読み、Code の環境には鍵が無い（CHAT-1021-PRV-06）。新旧のシートの照合と新旧の生成物の比べは Actions 上で行う（`check-sheets-reader.yml`・`regenerate-page.yml` の dry_run を作業ブランチの ref で手動実行する形。値はジョブのログに出さない）
* 「R_タイトル戦_マスタ」は sheets-reader で読める（専用フォルダ「【本番環境】」）

手順

1. 同じ主題の issue（クローズ済みを含む。「Ampai シート 移す」「R_Ampai 廃止」等）と未マージの work/ ブランチを確かめる。無ければ issue「R_Ampai の成績表を R_タイトル戦_マスタの Ampai シートへ移す」を起票し（本文に上の決定と、マージ後の平野さんの手作業〈R_Ampai のブックごとの廃止〉を書く）、着手中のコメントを残す。読み手を洗い出してログに一覧で書く。
2. 照合: 作業ブランチで、Sheets API で2つのシートを読み、見出しの並び・行数・セルごとの一致を比べ、違うセルの数と位置（行・列）だけを出す形を用意し（`check-sheets-reader` の入力として足すか、別の手動実行の入口にする。値は出さない）、作業ブランチを ref にして手動実行する。食い違いがあれば切り替えずに止まる。一致すれば、`ampai.SPREADSHEET_ID` を「R_タイトル戦_マスタ」（`live.SPREADSHEET_ID` と同じ値。定数の持ち方は重複しない形に）に、シート名を「Ampai」に切り替える。`check_sheets_reader.py` から「R_Ampai」を外す。docs（static-generation.md など、R_Ampai を置き場所として書いている所）を直す。
3. 比べとマージ: 作業ブランチを ref にして、同じ時点の新旧の全ページの生成を Actions 上で比べる（PRV-06 と同じ形）。差分が0なら cloudflare へマージする。マージ後に `check-meibo.yml`（check_ampai を含む）を dry_run で手動実行し成否を書く。issue に結果を書き、`## 報告` の「判断が必要なこと」に平野さんの手作業「R_Ampai をブックごと廃止する」を書く（issue は廃止の確かめの後に閉じる）。

止まる条件

* 手順2で、2つのシートの見出し・行数・セルが一致しない（違うセルの数と位置を書いて止まる。値は書かない）
* 読むたびに行数が変わる（平野さんがシートを編集中の可能性）
* 手順3で新旧の生成物に差分が出た（差のあったファイルの名前・行数・種類を書いて止まる）
* 照合のために本番の書き込み・共有の変更が要る
* 同じ主題の open issue、または同じ目的の未マージの work/ ブランチがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する。表・追記の続く文書で、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・手動実行の失敗が今回の変更によるもの（無関係なら原因を書いて進めてよい。一時的な接続の失敗は、失敗したジョブだけを1回再実行して成功なら止まらない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SRG-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SRG-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref `CHAT-1023-SRG-04` のコミットは 0 件。`SRG` は CHAT-1018-SRG-01〜03 の続き（重複ではない）
- 作業ブランチ: `work/1023-srg` はローカル・リモートとも無く、origin/cloudflare（8255bdad8）から作った

## 報告

- 状態: 作業中
- ブランチ: work/1023-srg
- ログ: https://github.com/retroeater/mj/blob/work/1023-srg/docs/logs/CHAT-1023-SRG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-srg
- 確認用URL: なし
- マージ: 未
- issue: 未定
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 49b9ce27）: https://github.com/retroeater/mj-logs/tree/main/guide/49b9ce27

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/49b9ce27/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8255bdad.md
