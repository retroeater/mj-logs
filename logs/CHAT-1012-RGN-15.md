# CHAT-1012-RGN-15

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: 2d8992f5

## 指示

【Claude作成】Claude Code 向け指示：#551 の7本目（title 系。RGN-12 のやり直し）。title_pages・jpml_pros・jpml_test に、ファイルを書かず止まる理由と警告を全部出す --check を lib/check.py で作る Chat-Ref: CHAT-1012-RGN-15 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-14 は完了。WHS-17〈#553〉は cloudflare に入った〈RGN-14 の報告〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
RGN-12 は、未マージの work/1012-whs-links（CHAT-1012-WHS-17、#553）と `generate_title_pages.py` の `main()`・`period_body()` が重なって止まった。WHS-17 が cloudflare に入ったので、取り込んだ後の作りに合わせて title 系を作る。#551 の (E) の2段目の続き。title 系の3ページ（title の `fetch_tab()` と GenerationError を共有する）で、`regenerate.py --check` が止まる理由を最初の1つでなく全部、ファイルを書かずに出せるようにする。
決定（2026-10-12、平野さん）

* title 系は WHS-17 のマージの後に回す（RGN-12 で止まった後の判断）。saikyo_pages は、未マージの ABM の2本（work/1012-abm-ui・work/1012-abm-map）の後に回す（RGN-14 の報告のとおり）
* 作りは (E)。2段目以降は、各スクリプトの `--check`（書かない）を `scripts/lib/check.py` で作る。帰り道・houou 系・live_pages は済み。止まる理由と警告を分け、警告だけなら終了コード 0。実装のマージは承認済み（止まる条件つき）
* title の `fetch_tab()` がフィルタの検知（#432）と再試行を通らない件は #552 で扱う（この指示では直さない）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作りは CHAT-1012-RGN-09 の洗い出しの表（title_pages・jpml_pros・jpml_test の行）と、RGN-10（`lib/check.py`・`regenerate.py --check`）、RGN-11・13・14（生成の止まり方を変えずに `Check` に集める形）のとおり（要確認。行番号は WHS-17 の取り込みで変わっている。RGN-12 のログの調べも参考にする）
* title_pages の要: 書き出しの後に止まる 1070行（決勝メンバーの行数の照合）を、`--check` では書き出しの前に回す。GenerationError のうち【初】のもの（266・269・287・329・361行など）を、`--check` では集めて全部出す。warnings（363〜385・678・759・1087・1089行）は `Check.warn()` に出す。`fetch_tab()` の通信の例外・sns・pro_sheet・live_layer3 の ValueError は、`--check` では止まる理由として出す（traceback にしない）
* jpml_pros・jpml_test は title の検査（GenerationError）を借りている。3本で共有する検査は1か所で `Check` に集める形にし、二重に書かない
* 生成のとき（`--check` でないとき）の止まり方・生成物・終了コードは変えない

手順

1. 確かめる: 上の前提の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/generate_title_pages.py`・`scripts/generate_jpml_pros.py`・`scripts/generate_jpml_test.py`・関係する `scripts/lib/`・試験の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめる（別の行の変更は止まる理由にしない。重なりの内容はログに書く）。#552 の作業ブランチがあれば、重なりを特に確かめる。
2. 作る: 3本に `--check` を作り、試験を足す（作った行で: 複数の不整合が全部止まる理由に出る／1070行の照合が書き出しの前に回る／warnings は警告で終了コード 0／`--check` ではファイルを書かない）。先に、直す前のコードでは足した試験が通らないことを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。今のシートで `python3 scripts/regenerate.py --check title_pages jpml_pros jpml_test` と、続けて同じ3ページの生成を流し、結果と、生成物が変える前のコードで流したときと同じであること、`git status` が空であることを確かめる。docs/notes/static-generation.md の `--check` の節に、書かない `--check` を持つページとして足す（追記先の今の内容を読んでから）。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と push で動く再生成の結果を待つ（上限15分）。上の「決定」を `docs/decisions/automation.md` に足す（同じ決定が既にあれば足さない）。#551 に経過と、残り（saikyo_pages〈ABM の2本の後〉・残り〈`page.generate()` の9ページ・ouka_leagues・resource_efficiency〉）をコメントする（閉じない）。

止まる条件

* #551 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の title 系のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する
* 試験が通らない。または、直す前のコードでも足した試験が通る（振る舞いを変えないことの確かめは除く）
* 3ページの生成物、または生成の終了コードが、変える前のコードで流したときと違う（シートの変化で説明できる差を除く）
* `--check` の後の `git status` が空でない、または `--check` がファイルを書いた
* ワークフロー（`.github/workflows/`）を変える必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（#551 の残りは #551 で追うので、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未
- issue: #551
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2d8992f5）: https://github.com/retroeater/mj-logs/tree/main/guide/2d8992f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2d8992f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7b3e1334.md
