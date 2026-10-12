# CHAT-1012-SNM-02

- 着手日時: 2026-10-12
- 対象issue: #514, #327
- ブランチ: work/1011-snm
- 着手時HEAD: e9542807

## 指示

【Claude作成】Claude Code 向け指示：「プロ」の SNS の6列と「連盟プロ以外」の X ID・X画像URL を列ごと消しても止まらないよう、読む所を直してマージする（#514。CHAT-1011-SNM-01 の続き。シートの列はまだ消さない）
Chat-Ref: CHAT-1012-SNM-02
マージ: 承認済み（チャットで。2026-10-12 に平野さんが、下の「止まる条件」の確かめが通ればマージしてよいと承認）。CHAT-1011-SNM-01 のログも一緒に入れる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-snm の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01 で使ったもので、重複ではない。
作業ブランチ: クラウドセッションで実行する。未マージの work/1011-snm を続けて使う（CHAT-1011-SNM-01 のログのコミットがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1011-SNM-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に ` / 続き: CHAT-1012-SNM-02` を足す。

## 目的
SNS の値はすでにすべて SNS ブックから読んでいる。シートに残る旧列（「プロ」の X ID・X画像・note ID・note画像・YouTube ID・YouTube画像〈今の D〜I〉と「連盟プロ以外」の X ID・X画像URL〈今の E・F〉、計8列）を平野さんが列ごと消せるように、旧列の見出しを求めて止まる所を直し、マージする。シートの列を消すのは、このマージの後に平野さんが行う（確かめは次の指示）。

### 決定（2026-10-12、平野さん）
- 旧列の扱いは、列ごと消せるものは列ごと消す。「連盟プロ以外」の X ID・X画像URL も含める（2026-10-10〈CHAT-1010-XAP-05〉の「値を空にし、列は残す」と、2026-10-10〈CHAT-1011-XAP-09〉の「旧列を空にするのは切り替えのマージの数日後」を置き換える）
- 消す時機は「一度パスしていれば」すぐ。SNS ブックの毎日の更新・全ページの再生成は 10/12 にページの生成まで通っている（失敗の表示は帰り道の手順によるもの。CHAT-1012-XAP-11・CHAT-1012-WHS-15）ので、このマージの後に消す。#327（N列「YouTube画像」の削除）も同じときに消す
- XAP のチャットが判断待ちで持っていた「旧列を空にする指示」（CHAT-1011-XAP-09 の `## 報告`）は、このチャット（SNM）が引き取る
- CHAT-1011-SNM-01 で #514 にあった CHAT-1007-PHT-09・CHAT-1012-XAP-11 の着手中コメントは、どちらも完了済みで並行作業ではないので、調査を進めた判断でよい。SNM-01 のログはこの指示で一緒にマージする
- このマージは、下の「止まる条件」の確かめが通れば承認する

### 前提（チャット側。平野さんの決定ではない）
- 直す箇所は CHAT-1011-SNM-01 のログ `## 報告`「直す箇所（A）」: `scripts/lib/live.py` の `OTHER_HEADERS`、`scripts/generate_title_pages.py` の `EXPECTED_HEADERS["連盟プロ以外"]` と `main()` の差し込みの位置、`scripts/update_sns_book.py` の `load_sources()`（と init・check の照合）。着手時に grep し直し、SNM-01 の表と食い違えば書く（増えていたら同じ扱いで直してよい）
- 直した後のコードは、旧列が残っている今のシートと、消した後のシートの両方で動くこと（マージが先、列の削除が後のため）。旧列の見出しを読む・求める所を無くす形にする
- `update_sns_book.py` の init は 2026-10-10 に実行済みで使わない（docs/notes/sns-book.md「手動実行」）。init は旧列を読まずに「使えない（旧列は廃止）」と出して終える形にし、check は旧列との照合をやめる（タブ・見出し・行数・編集者の確かめは残す）。**`.github/workflows/` は変えない**（`update-sns-book.yml` の mode の選択肢はそのまま）
- SNM-01 の模擬（`## 経過` の手順2）で、A（8列を落とした表）で止まったもの: ページ7（ask_data・books_pages・houou_pages・houou_race・live_pages・saikyo_pages・title_pages）、`collect_saikyo_images.py`・未登録の検査・`write_live_channel_candidate.py`、`update_sns_book.py` の毎日の更新・check・init。`check_unused_names.py` は「検査できなかった」と知らせる。books_pages は直す前から「書籍」の行数で止まる（凍結中、差し替えなしでも同じ）
- 旧列にだけ値がある2名（篠原冴美・村田昴）はどちらも誤入力とみられ、写さない（SNM-01 の `## 報告`）
- 10/12 に #551（RGN）が `scripts/regenerate.py --check` と `lib/check.py` を入れた（CHAT-1012-RGN-10）。RGN は今後 title 系などの `--check` を直す予定。重なりは手順1で確かめる
- 使う skill は無い

## 手順
1. 確かめ: #514 に他セッションの着手中コメント（完了していない作業のもの）が無いこと。`git branch -r --no-merged origin/cloudflare` の各ブランチが、直すファイルの同じ行・同じ関数を変えていないか。#514 に着手中のコメントを書く
2. 直す: 上の箇所を直し、旧列の見出しを求める所が残っていないことを grep で確かめる（テストに「旧列の見出しを読み取りに渡していない」検査を足す。`pro_sheet` の X_ID などの定数は、使う所が無くなれば消すか `RETIRED` 相当に移す。置き方は任せる）。既存のテストを直し、全部通す。文書を直す: SNM-01 が挙げた `docs/notes/sns-book.md`・`title-pages.md`・`live-page-design.md`・`houou-race.md`・`books-covers.md`・`static-generation.md`、`check-saikyo-unregistered.yml` の案内の文（ワークフローの中の文なら変えずに、ログに「変えていない」と書く）。「旧列は数日後に空にする・列は残す」の記述は「旧列は列ごと消す（2026-10-12 の決定。平野さんが削除し、CHAT-1012-SNM-03 で確かめる予定）」に置き換える。追記先の今の文を読んでから直す
3. 確かめてマージ: (a) 今のシートで、直す前（その時点の origin/cloudflare）と直した後のコードで全ページを1ページずつ続けて生成し、差を比べる（SNM-01 と同じ比べ方）。(b) SNM-01 の模擬 A（8列を落とした表）と B（値を空にした表）で、直した後の全ページの生成と、`update_sns_book.py` の毎日の更新（dry-run）・check、SNM-01 で A で止まったスクリプト（書き込みを伴わない形で動かせるもの）を動かす。止まる条件に当たらなければ、docs/decisions/saikyo.md に上の「決定」を足し、#514 と #327 に結果をコメントし（閉じない。#327 は「N列は次の列の削除で一緒に消す」）、cloudflare へマージする

## 止まる条件
- (a) で、直す前と後の全ページの生成に差がある（シートの変化で説明できる差は、直す前と後を続けて生成して出ないことを確かめる。説明できない差が1件でもあれば止まる）、または直す前は通ったページが直した後に止まる
- (b) で、A・B のどちらかで止まるページ・スクリプトがある、または差し替えなしとの差がある（books_pages が直す前と同じく「書籍」の行数で止まるのは許す）、`update_sns_book.py` の毎日の更新（dry-run）の【1】【2】【3】の行数・状態の数が差し替えなしと違う
- 旧列の見出しを求める所が SNM-01 の表に無い形で見つかり、直し方が上の前提で決まらない
- `.github/workflows/` を変える必要が出た（変えずに止まる）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 「プロ」の行数が 1,000〜1,300 を外れる、「連盟プロ以外」の行数が 700〜850 を外れる、どちらかが2回の読みで変わる
- 未マージの work/ ブランチが、直すファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- 直した箇所（ファイル・関数・何を変えたか）、grep の結果、テストの結果、(a)(b) の表（ページ・スクリプトごと）、直した文書の一覧がログの「経過」にある
- ログの `## 報告` の項目の後ろに、平野さんが消す列の一覧を書く: ブックとタブ・今の列の英字・見出し（「プロ」の6列〈N列「YouTube画像」を含む〉と「連盟プロ以外」の2列）。マージの時点で読み直した英字を書く
- マージ後の自動再生成（regenerate-page.yml、push）で生成物が変わらない見込み（シートの変化による差は除く）。結果の run と結論をログに書く（失敗が今回の変更によるものでなければ、原因を書いて完了してよい）
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-SNM-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-SNM-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf8b4b97）: https://github.com/retroeater/mj-logs/tree/main/guide/cf8b4b97

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf8b4b97/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/28649cd5.md
