# CHAT-1012-RGN-09

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: 915bafa2

## 指示

【Claude作成】Claude Code 向け指示：#551 の1本目。シートを読む全ページについて、今の検査（止まる条件）と書き出しを洗い出し、`--check` の共通の作りの案を比べて報告する（調べるだけ。コードは変えない） Chat-Ref: CHAT-1012-RGN-09 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。判断の要る点は報告の「判断が必要なこと」に書き、状態は CLAUDE.md「作業ログ」節のとおり「判断待ち」にする 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

目的
#551（シートを読むページに、ファイルを書かず生成と同じ検査だけを回す手段 `--check` を持たせる）を、シートを読む全ページで行う。対象が多いので、1本目は実物の洗い出しと共通の作りの案の比較だけにし、作りを平野さんと決めてから実装の指示を出す。
決定（2026-10-12、平野さん）

* `--check` は、シートを読む全ページに作る（辞書は済み）
* 帰り道の「JSON に無い回」（今は止めずに外して警告）は、`--check` では「止まる理由」と「警告」を分けて両方を全部出す。警告だけなら終了コード 0（今の生成と同じ扱い）
* 実装の指示のマージは承認済み（止まる条件つき）。この1本目は調べるだけ

前提（チャット側。平野さんの決定ではない。手順で確かめる）

* 2026-10-11 の決定（#551 の本文）: 帰り道の `--check` は `lib/wayhome.py` の `load_episodes()` と写真の確かめ（`check_player_photos()`）の両方を回し、途中で止めずに全部の理由を出す
* 辞書の `--check` は `scripts/generate_resource_dictionary.py` の `run_check()`（CHAT-1012-RGN-08 のログでは 269〜286行）。同名の `--check` を持つ別の意味のスクリプト（`build_ogp_image.py`・`update_sns_book.py`・`write_live_channel_candidate.py`）がある（要確認）
* 共通の作りの案（チャット側の案。実物を見て足し引きしてよい）:
   * (A) 各生成スクリプトに `--check` を足し、理由を集める共通の部品（例: `scripts/lib/` に、止まる理由と警告を集めて最後にまとめて出すもの）を使う。全部の理由を出せるが、スクリプトごとに直しが要る
   * (B) `regenerate.py --check <ページ|all>` が、書き出しを止める指定（環境変数など）で各生成スクリプトを動かす。`lib/page.py` の書き出しなどを止める。直しは少ないが、最初の理由で止まるスクリプトはそのまま
   * (C) (B) を入口にし、(A) の部品は止まった実績のあるページから順に入れる（段階的）
* 気をつけること: `--check` が外部の API（YouTube Data API、X API など）を呼ぶと、割り当て・課金に響く。どのページが何を呼ぶかを洗い出す

手順

1. 洗い出す（読むだけ）: `python3 scripts/regenerate.py --list` の全ページについて、生成スクリプトごとに次を表にする: 読むシート（ブック・タブ）、外部の API の呼び出し（YouTube・X・楽天など）、止まる条件（`raise`・`SystemExit`・`FilteredSheetError` など。ファイルと行）と、最初の1つで止まるか全部を集めるか、警告で続けるもの、書き出すファイル（`lib/page.py` を通るか、直接書くか）、`--check` の既存の有無。凍結中のページ（`FROZEN_FROM_ALL`）は表に載せ、対象から外すかを判断が必要なことに書く。
2. 案を比べる: 上の (A)〜(C) と、手順1で見つかったほかの案について、直すファイルの数の見積もり、全部の理由を出せるページ、外部の API の扱い（呼ばない・割り当てを使う）、`regenerate.py` の今の作り（#533 の飛ばす作り）との関係、試験の作り方を表にする。チャット側が平野さんと決めるための推奨を1つ、理由とともに書く。指示の分け方の案（1本3項目程度。何本で、どの順か）も書く。
3. 記録する: 上の「決定」を `docs/decisions/automation.md` に足す。#551 に、決定と、この調べのログへの permalink（SHA を固定）をコメントする（#551 は閉じない）。

止まる条件

* #551 が Closed、または他セッションの着手中コメントがある
* ログ・`docs/decisions/` 以外（コード・ワークフロー・生成物・ほかの文書）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（推奨と指示の分け方は「判断が必要なこと」に書き、状態は「判断待ち」）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未
- issue: #551
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dba9c8ed）: https://github.com/retroeater/mj-logs/tree/main/guide/dba9c8ed

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dba9c8ed/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/915bafa2.md
