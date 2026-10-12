# CHAT-1012-RVC-03

- 着手日時: 2026-10-12
- 対象issue: #492・#298
- ブランチ: work/1012-rvc-handoff
- 着手時HEAD: a0fe4f252

## 指示

【Claude作成】Claude Code 向け指示：申送り（2026-10-12 のレビュー）。コミット件名の閉じるキーワードを禁止する規則・handover 6章の表の網羅のテスト・レビューの観点2つを足し、hook で止める issue を起票してマージする Chat-Ref: CHAT-1012-RVC-03 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rvc-handoff の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rvc-handoff を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rvc-handoff origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-12 のレビュー（CHAT-1012-RVC-01・02）で分かったことのうち、機械的に確かめられるものだけを残す。 (1) #298 が 2026-10-10 にコミット件名の「fix #298」で意図せず自動クローズされた。(2) handover.md 6章の表に `docs/notes/ask-worker.md`・`sns-book.md` の行が抜けていた。(3) handover 5章の表に閉じた issue（#298）の行が残り、チャット側のメモリーには docs/decisions に書かれた後の控えが残っていた。
決定（2026-10-12、平野さん）

* 次の3点を足し、完了したらマージしてよい: (1) コミット件名・本文に GitHub の閉じるキーワードを書かない規則を CLAUDE.md に1行と、過去の誤クローズの洗い出し (2) handover.md 6章の表に docs/notes/ の全ファイルが載っているかのテスト (3) docs/notes/chat-routines.md「レビュー」の観点に、handover の表の issue の Open/Closed の確認と、メモリーの控えが docs に書かれたら消すことを足す
* 閉じるキーワードを含む `git commit` を hook（mj-git-guard）で止める仕組みは、issue を起票するだけにする（`.claude/` の変更は平野さんの手作業が要るため、実装は後日）

前提（チャット側。平野さんの決定ではない）

* (1) の文面の案（CLAUDE.md「コミットのルール」に1行）: 「コミットメッセージ（件名・本文）に GitHub の閉じるキーワード（close・fix・resolve とその活用形の直後の `#番号`）を書かない（cloudflare に入ると issue が自動で閉じる、#298。issue を閉じるのは `gh issue close`）」。GitHub の公式文書でキーワードの一覧（close / closes / closed / fix / fixes / fixed / resolve / resolves / resolved、大文字小文字を問わない）を確かめて書く（要確認）
* (1) の洗い出し: `git log origin/cloudflare` の件名・本文から閉じるキーワード＋`#番号` を grep し、その issue が今 closed で、閉じたのがそのコミット（issue の timeline の `closed` の `commit_id`）なら一覧にしてログに書く。意図したクローズかは判断せず、一覧を報告に書くだけにする（開け直さない）
* (2) のテスト: `scripts/tests/` に unittest を足す（標準ライブラリのみ）。`docs/notes/*.md` の各ファイル名が handover.md 6章の表に `docs/notes/<名前>` として出ることを確かめる。今の cloudflare で通ることを確かめる（RVC-01 で2行を足した後なので通る見込み）。修正前の handover（2行を足す前、コミット a23c99804 の親など）で落ちることも確かめる（CLAUDE.md「修正の検証は、先に修正前のコードでも通らないか」）
* (3) の文面の案（chat-routines.md「レビュー」の観点のチェックリスト (a) に足す。容量の上限の無い文書）: 「handover 5章の表の issue は REST か Code で Open/Closed を確かめ、閉じたものの行を直す」「プロジェクトのメモリーの『docs/decisions に書かれるまでの控え』は、docs/decisions・docs/notes に書かれたものを消す」
* hook の issue の本文の案: 題「コミットメッセージの閉じるキーワードを mj-git-guard で止める」。背景（#298 の誤クローズ）、判定（`git commit -m`／`-F` の本文に閉じるキーワード＋`#番号`）、`.claude/` の変更なので平野さんの手作業（docs/notes/skills.md）、ラベルは `分野: 整理・保守`。起票の前に同じ主題の issue をクローズ済みも含めて検索する
* 決定は docs/decisions/operations.md に足す
* 取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。閉じるキーワード・自動クローズ・handover の表の検査）、あれば止まって報告する。CLAUDE.md「コミットのルール」・docs/notes/chat-routines.md「レビュー」の今の内容と、`docs/notes/handover-archive-2026.md` の「〜から外した記述」の節を読み、同じ趣旨の行を前に意図して外していないか確かめる
2. (1)〜(3) を作る。(1) の洗い出しの一覧と、(2) のテストの修正前・修正後の結果をログに書く。`python3 -m unittest discover -s scripts/tests` を通す。CLAUDE.md のサイズ（`wc -c`）を測る
3. CLAUDE.md「ブランチ運用」のとおりマージし、check-run を待つ（上限15分）。hook の issue を起票する。#492 に「申送り（閉じるキーワードの規則・6章の表のテスト）を入れた」とコメントする（末尾に Chat-Ref の行）

止まる条件

* 同じ論点の issue がある、または archive に同じ趣旨の行を意図して外した記録がある
* (2) のテストが今の cloudflare で落ちる（6章の表に抜けがある。抜けたファイル名を報告に書いて止まる）
* CLAUDE.md が警告域（26KB）に入った
* 取り込みで両立しない衝突が出た
* check-run が今回の変更で失敗した（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に (1) の洗い出しの一覧と、起票した issue の番号を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RVC-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RVC-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rvc-handoff
- ログ: https://github.com/retroeater/mj/blob/work/1012-rvc-handoff/docs/logs/CHAT-1012-RVC-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rvc-handoff
- 確認用URL: なし
- マージ: 未
- issue: #492・#298
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a0fe4f25）: https://github.com/retroeater/mj-logs/tree/main/guide/a0fe4f25

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a0fe4f25/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
