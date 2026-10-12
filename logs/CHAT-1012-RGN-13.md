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

### 0. 着手前の確認

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-RGN-13"` は0件
- 作業ブランチ: ローカルの work/1012-rgn は `origin/work/1012-rgn`（bb329766）と一致し、RGN-12 のログと決定（文書のみ）が未マージで残っていた。指示文の「作業ブランチ」の行は「マージ済みでなければ止まる」だが、同じ指示文の 0. は RGN-12 のログを `origin/work/1012-rgn` の版で読んで書き換えることを求めている。CLAUDE.md「Chat-Ref」節（記述同士が食い違ったら前提・ルールの側を優先）により、docs/notes/cloud-sessions.md「作業ブランチの用意」の「未マージの作業を続ける」に従って進めた（cloudflare は作業ブランチの祖先で、取り込みは要らなかった）
- 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- RGN-12 の `## 報告` の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1012-RGN-13` を足した
- #551 は Open。コメントはこのセッションのものだけ。着手中コメント: https://github.com/retroeater/mj/issues/551#issuecomment-6118510867

### 1. 確かめる

- 前提の作りと行番号は実物のとおり（RGN-09 の表の houou_race 184・197・242・247・315・368行、houou_pages 112・116・118・580・597行・`render_race()` 514・522行、houou_leagues 157行、ask_data 103・110・120・122・128行）
- 実物で分かったこと: houou_pages の `GenerationError` は `ValueError` の子、houou_race・ask_data は別々の `GenerationError`。houou_pages は「名前の辞書」を2回読む（自分と `race_page.load_periods()`。生成でも同じ警告が2回出る）。meibo の `check_pros()` は jpml_pros も使う
- 未マージの work/ ブランチ（この作業ブランチを除く）で、`generate_houou_race.py`・`generate_houou_pages.py`・`generate_houou_leagues.py`・`generate_ask_data.py`・`lib/pro_sheet.py`・`lib/pro_stats.py`・`lib/meibo.py`・`lib/live.py`・`lib/check.py`・`generate_live_pages.py`・houou 系と ask_data・meibo の試験・docs/notes/static-generation.md を変えているものは無い（work/1008-hou を含む）。マージ直前に見直したときも無かった。外したページは無い

### 2. 作る

生成の振る舞いは変えない形にそろえた（RGN-11 と同じ）。

- `scripts/lib/check.py`: `fail(stop, message, error)` を足した（stop が None〈生成〉なら error で止め、あれば〈--check〉理由を渡して続ける）。`Check.warn()` は同じ警告を1回だけにした（houou_pages が名前の辞書を2回読むため）
- `scripts/lib/meibo.py`: `pros_problems()`（名簿の人数・名簿にいない在籍者を全部）を足し、`check_pros()` はその最初の理由で今と同じ文言の `ValueError` にした（jpml_pros も同じ）
- `generate_houou_race.py`: `check_bad_chars()`・`load_notes()`・`build_periods()`・`check_down_rows()`・`load_periods()` に `stop` を、`load_name_book()`・`load_periods()` に `warn` を足した（既定は今と同じ）。`--check` では「前後」の不正・組の混ざりを行・リーグを飛ばして全部出し、G列と合わない表を警告にする。`write_data()` を `data_files()`（中身）と `write_files()`（書く・古いものを消す）に分けた。`run_check()`: 読み込み → ページ（既定の表示の検査 `pick_default()` を含む。生成では JSON を書いた後）→ 期ごとのデータ
- `generate_houou_pages.py`: `load_rows()`・`load_profiles()` に `stop`（と `warn`）を足した。`main()` を `prepare()`（読み込みと検査。ファイル名にできない名前・ランキングの件数・順位変動の検査も `stop` で）と `outputs()`（書くものを書く順に1つの表にしたもの）に分け、`main()` はその表のとおり書く（書く順・古いファイルの消し方は今と同じ）。`run_check()` は同じ表の中身を書かずに1つずつ `attempt()` する（順位変動の枠・説明文の検査 514・522行と既定の表示も、書く前に回る）
- `generate_houou_leagues.py`: `run_check()`（期が0件なら、`periods[0]` の IndexError ではなく止まる理由にする）
- `generate_ask_data.py`: `finals_data()` に `stop`（期ごとの検査を全部）、`load_name_book()`・`load_people()`・`live_data()` に `warn` を足し、読み込みを `load()` にまとめた。**/live の Warnings（公開しなかった行・行に紐づかない警告）を `live_warnings()` で警告にした**。生成でも「警告: /live で公開しなかった「【3】手動補正」の行 N件」と行の一覧が出る（決定のとおり。data.json は変わらない）。ジョブのサマリには書かない（live_pages の生成が書く）
- 試験 `scripts/tests/test_houou_check.py`（11件。ファイルの書き出し・削除・ディレクトリ作成が起きないことを確かめる）: houou_race の「前後」の不正2件と組の混ざりが全部出る／生成は最初の行で止まる（振る舞いを変えないことの確かめ）／既定の表示の検査が書かずに回る／G列と合わない表は警告／houou_pages の件数・前後・リーグが全部出る／`run_check()` が表の中身を全部作り、旧データの削除は回さない／meibo の理由が全部出て、`check_pros()` は最初の理由／houou_leagues の期0件は止まる理由／ask_data の決勝の問題が全部出る／/live の除外した行が警告になる／警告だけなら 0
- 直す前のコード（HEAD bb329766 を一時のディレクトリに `git archive` で出し、足した試験だけ置いた）で流すと、11件のうち10件が ERROR。残る1件（生成は最初の行で止まる）は振る舞いを変えないことの確かめで、直す前でも通る
- `python3 -m unittest discover -s scripts/tests` は全件 OK

### 3. 今のシートで確かめる

`python3 scripts/regenerate.py --check houou_race houou_pages houou_leagues ask_data`: 終了コード 0、59秒。4本とも `--check` で呼ばれ、止まる理由は0件。警告は ask_data の1件（/live で公開しなかった「【3】手動補正」の行 830件。詳細 830行。live_pages の警告と同じ中身）。G列と合わない表は0（生成の出力でも 0）。コミットした後のきれいな作業ツリーで流し直し、終わった後の `git status` は空。

| ページ | 結果 | 止まる理由 | 警告 |
|---|---|---|---|
| houou_race | 通る（`--check`） | 0 | 0 |
| houou_pages | 通る（`--check`） | 0 | 0 |
| houou_leagues | 通る（`--check`） | 0 | 0 |
| ask_data | 通る（`--check`） | 0 | 1 |

`python3 scripts/regenerate.py houou_race houou_pages houou_leagues ask_data`（変えた後）: 終了コード 0、`git status` は空。変える前のコード（HEAD bb329766 の全体を一時のディレクトリに出したもの）で4本を流した結果（終了コードはすべて 0）と、`houou_race.html`・`houou_race/`・`houou/`・`houou_leagues.html`・`houou_leagues_data.json`・`workers/ask/data.json` は差0件。

docs/notes/static-generation.md の `regenerate.py --check` の項に、houou 系の4本が書かない `--check` を持つことを足した。

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

ガイド文書（この版を写した時点の最新、mj 84eba31d）: https://github.com/retroeater/mj-logs/tree/main/guide/84eba31d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/84eba31d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
