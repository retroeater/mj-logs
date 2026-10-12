# CHAT-1012-RGN-12

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: 1a0da912

## 指示

【Claude作成】Claude Code 向け指示：#551 の4本目（title 系）。title_pages・jpml_pros・jpml_test に、ファイルを書かず止まる理由と警告を全部出す --check を lib/check.py で作る Chat-Ref: CHAT-1012-RGN-12 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-11 は完了） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
#551 の (E) の2段目の続き。title 系の3ページ（title の `fetch_tab()` と GenerationError を共有する）で、`regenerate.py --check` が止まる理由を最初の1つでなく全部、ファイルを書かずに出せるようにする。
決定（2026-10-12、平野さん）

* 作りは (E)。2段目以降は、各スクリプトの `--check`（書かない）を `scripts/lib/check.py` で作る。順番は帰り道（済）→ title 系 → houou 系 → live・最強戦 → 残り。止まる理由と警告を分け、警告だけなら終了コード 0。実装のマージは承認済み（止まる条件つき）
* title の `fetch_tab()` がフィルタの検知（#432）と再試行を通らない件は #552 で扱う（この指示では直さない）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作りは CHAT-1012-RGN-09 の洗い出しの表（title_pages・jpml_pros・jpml_test の行）と、RGN-10（`lib/check.py`・`regenerate.py --check`）、RGN-11（帰り道の `--check` の作り。生成の止まり方を変えずに `Check` に集める形）のとおり（要確認。RGN-11 で決めた形にそろえる）
* title_pages の要: 書き出しの後に止まる 1070行（決勝メンバーの行数の照合）を、`--check` では書き出しの前に回す。GenerationError のうち【初】のもの（266・269・287・329・361行など）を、`--check` では集めて全部出す。warnings（363〜385・678・759・1087・1089行）は `Check.warn()` に出す。`fetch_tab()` の通信の例外・sns・pro_sheet・live_layer3 の ValueError は、`--check` では止まる理由として出す（traceback にしない）
* jpml_pros・jpml_test は title の検査（GenerationError）を借りている。3本で共有する検査は1か所で `Check` に集める形にし、二重に書かない
* 生成のとき（`--check` でないとき）の止まり方・生成物・終了コードは変えない

手順

1. 確かめる: 上の前提の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/generate_title_pages.py`・`scripts/generate_jpml_pros.py`・`scripts/generate_jpml_test.py`・関係する `scripts/lib/`・試験の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめる（別の行の変更は止まる理由にしない。重なりの内容はログに書く）。#552 の作業ブランチがあれば、重なりを特に確かめる。
2. 作る: 3本に `--check` を作り、試験を足す（作った行で: 複数の不整合が全部止まる理由に出る／1070行の照合が書き出しの前に回る／warnings は警告で終了コード 0／`--check` ではファイルを書かない）。先に、直す前のコードでは足した試験が通らないことを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。今のシートで `python3 scripts/regenerate.py --check title_pages jpml_pros jpml_test` と、続けて同じ3ページの生成を流し、結果と、生成物が変える前のコードで流したときと同じであること、`git status` が空であることを確かめる。docs/notes/static-generation.md の `--check` の節に、書かない `--check` を持つページとして足す（追記先の今の内容を読んでから）。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と push で動く再生成の結果を待つ（上限15分）。#551 に経過と、残り（houou 系 → live・最強戦 → 残り）をコメントする（閉じない）。

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
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-RGN-12"` は0件
- 作業ブランチ: ローカル・リモートの work/1012-rgn とも `origin/cloudflare` の祖先（RGN-11 でマージ済み）→ `git merge --ff-only origin/cloudflare`（950bd402 → 1a0da912）
- 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- #551 は Open。コメントはこのセッションの RGN-09〜11 のものだけ（どれも完了のコメントあり）。コードに手を付ける前に止まったため、着手中コメントは残していない

### 1. 確かめる → 止まる条件に当たった

未マージの work/ ブランチで、`scripts/generate_title_pages.py`・`scripts/generate_jpml_pros.py`・`scripts/generate_jpml_test.py`・`scripts/lib/`・title 系の試験・docs/notes/static-generation.md を変えているもの（`git diff --name-only origin/cloudflare...<ブランチ>`）:

- **`origin/work/1012-whs-links`（CHAT-1012-WHS-17、#553）**: `scripts/generate_title_pages.py`・`scripts/lib/wayhome.py`・`scripts/lib/wayhome_intake.py`
  - `generate_title_pages.py` で変えているところ: `from lib import wayhome` の import、新しい関数（`load_wayhome()`・`wayhome_card_a_html()`・`wayhome_card_b_html()`・`wayhome_html()`、定数 `PREVIEW_CARD_B` など）、**`period_body()` の引数（`wayhome_episodes` を足す）と本体**、**`main()`**（`load_wayhome()` の呼び出し、`period_body()` の呼び出し、件数の print）
  - そのブランチのログの状態は「判断待ち（マージの判断。プレビューを見て選んでもらう点がある）」。期ページのカードの案A・案B を決めて `PREVIEW_CARD_B` と使わない側を消してからマージする、とある
  - そのブランチは今の cloudflare と衝突せずに取り込める（`git merge-tree --write-tree`）。RGN-11 で変えた `lib/wayhome.py` とも衝突しない
- #552 の作業ブランチは無い

title_pages の `--check` は、RGN-11 と同じ形（生成の止まり方を変えずに `Check` に集める）にするには、`main()` の読み込み・検査・組み立てを切り出す必要があり（1070行の照合を書き出しの前に回す、`fetch_tab()`・3層・sns の例外を止まる理由にする、期ページの組み立て `period_body()` を回ごとに `attempt()` する）、WHS-17 が変えている `main()`・`period_body()` と同じ関数を変える。止まる条件「未マージの work/ ブランチが上の title 系のファイルの同じ行・同じ関数を変えている」に当たるため、コードは変えずに止まった。マージもしない（このログは作業ブランチにだけ push する）。

jpml_pros・jpml_test は `generate_title_pages.py` の `fetch_tab()`・`build_aliases()`・`final_counts()` などを借りるだけで、WHS-17 はこれらを変えていない。ただし3本で共有する検査を1か所にまとめる作り（前提のとおり）にすると `generate_title_pages.py` に手が入るため、jpml の2本だけを先に進めることもしていない。

## 報告

- 状態: 判断待ち / 続き: CHAT-1012-RGN-13
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未（止まる条件に当たったため。コードは変えていない）
- issue: #551（変えていない）
- 判断が必要なこと:
  - 未マージの `work/1012-whs-links`（CHAT-1012-WHS-17、#553、判断待ち）が `scripts/generate_title_pages.py` の `main()` と `period_body()` を変えていて、title 系の `--check` が変える関数と重なる（止まる条件）。進め方を決めてほしい。案: (1) WHS-17 のマージ（カードの案の決定）を待ってから title 系を作る（推奨。取り込んだ後の `main()` に合わせて切り出すので、二度手間にならない）／(2) 先に houou 系（5本目の予定）を進め、title 系はその後に回す（houou 系のファイルは未マージのブランチと重なっていない。ただし ask_data は houou と live を借りる）／(3) title_pages の `main()`・`period_body()` に触れない形（`run_check()` を別に書き、読み込みの手順を二重に持つ）で今作る（WHS-17 のマージ後に `run_check()` が `load_wayhome()` を回らず、ずれが出る。推奨しない）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 387505ef）: https://github.com/retroeater/mj-logs/tree/main/guide/387505ef

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
