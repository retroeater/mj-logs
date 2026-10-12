# CHAT-1012-RGN-13

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: bb329766

## 指示

【Claude作成】Claude Code 向け指示：#551 の5本目（houou 系。title 系は後回し）。houou_race・houou_pages・houou_leagues・ask_data に、ファイルを書かず止まる理由と警告を全部出す --check を lib/check.py で作る Chat-Ref: CHAT-1012-RGN-13 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-12 は判断待ち。この指示がその続き） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1012-RGN-12.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる（RGN-12 のログがまだ cloudflare に無ければ origin/work/1012-rgn の版を読む）。RGN-12 の `## 報告` の状態の末尾に `/ 続き: CHAT-1012-RGN-13` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
RGN-12（title 系）は、未マージの work/1012-whs-links（CHAT-1012-WHS-17、#553）が `generate_title_pages.py` の `main()`・`period_body()` を変えていて止まった。順番を入れ替え、重ならない houou 系を先に作る。
決定（2026-10-12、平野さん）

* 先に houou 系を進め、title 系は WHS-17 のマージの後に回す
* ask_data が live_pages の警告（除外した行）を作るのに出していない件は、この houou 系の指示で警告として出す
* 作りは (E)。各スクリプトの `--check`（書かない）を `scripts/lib/check.py` で作る。止まる理由と警告を分け、警告だけなら終了コード 0。実装のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 作りは CHAT-1012-RGN-09 の洗い出しの表（houou_leagues・houou_pages・houou_race・ask_data の行）と、RGN-10（`lib/check.py`・`regenerate.py --check`）、RGN-11（帰り道。生成の止まり方を変えずに `Check` に集める形）のとおり（要確認）
* 要点（RGN-09 から）:
   * houou_race: 書き出しの後に止まる 315行（`pick_default()`）を `--check` では書き出しの前に回す。【初】の検査（184・197・242・368行）を集めて全部出す。band_mismatch などの print は警告に
   * houou_pages: `render_race()` 514・522行（書き出しの途中）を `--check` では書き出しの前に。`load_rows()` 112・116・118行、597〜598行の【初】を集める。meibo の ValueError を止まる理由として出す（traceback にしない）
   * houou_leagues: 期0件で `periods[0]` が IndexError（157行）になるのを、`--check` では止まる理由として出す
   * ask_data: GenerationError（103・110・120・122行）の【初】を集める。live_pages の Warnings を `report()` で警告として出す（128行）。houou_pages の `load_rows()` と live を借りているので、共有する検査は1か所で `Check` に集め、二重に書かない
   * 共通の pro_sheet・pro_stats・meibo の ValueError も、`--check` では止まる理由として出す
* 生成のとき（`--check` でないとき）の止まり方・生成物・終了コードは変えない。ただし ask_data の警告を出す直しは、生成のときにも同じ警告が出てよい（生成物は変えない）
* houou/ は別のチャット（系列 HOU）が作業していることがある。未マージの work/ ブランチ（例: work/1008-hou）と重ならないかを先に確かめる

手順

1. 確かめる: 上の前提の（要確認）を実物で確かめる。未マージの work/ ブランチを `git branch -r --no-merged origin/cloudflare` で一覧し、`scripts/generate_houou_race.py`・`scripts/generate_houou_pages.py`・`scripts/generate_houou_leagues.py`・`scripts/generate_ask_data.py`・関係する `scripts/lib/`（pro_sheet・pro_stats・meibo・live）・試験の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめる（別の行の変更は止まる理由にしない。重なりの内容はログに書く）。重なるページがあれば、そのページだけ外して残りを進めてよい（外したページと理由を報告に書く）。
2. 作る: 4本に `--check` を作り、試験を足す（作った行で: 複数の不整合が全部止まる理由に出る／書き出しの後にあった検査が書き出しの前に回る／警告だけなら終了コード 0／ask_data で live の除外した行が警告に出る／`--check` ではファイルを書かない）。先に、直す前のコードでは足した試験が通らないことを確かめる（振る舞いを変えないことの確かめは除く）。`python3 -m unittest discover -s scripts/tests` を通す。今のシートで `python3 scripts/regenerate.py --check houou_race houou_pages houou_leagues ask_data` と、続けて同じページの生成を流し、結果と、生成物が変える前のコードで流したときと同じであること、`git status` が空であることを確かめる。docs/notes/static-generation.md の `--check` の節に足す（追記先の今の内容を読んでから）。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と push で動く再生成の結果を待つ（上限15分）。上の「決定」を `docs/decisions/automation.md` に足す。#551 に経過と、残り（title 系〈WHS-17 のマージ待ち〉・live・最強戦・残り）をコメントする（閉じない）。

止まる条件

* CHAT-1012-RGN-12 の状態が「判断待ち」でない、#551 が Closed、または他セッションの着手中コメントがある
* 4本すべてが未マージの work/ ブランチと同じ行・同じ関数で重なる、または取り込みで衝突する（一部なら、そのページを外して進めてよい）
* 試験が通らない。または、直す前のコードでも足した試験が通る（振る舞いを変えないことの確かめは除く）
* 対象のページの生成物、または生成の終了コードが、変える前のコードで流したときと違う（シートの変化で説明できる差を除く）
* `--check` の後の `git status` が空でない、または `--check` がファイルを書いた
* ワークフロー（`.github/workflows/`）を変える必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（#551 の残りは #551 で追うので、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/work/1012-rgn/docs/logs/CHAT-1012-RGN-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし
- マージ: 未
- issue: #551
- 判断が必要なこと: なし
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
