# CHAT-1012-RGN-10

- 着手日時: 2026-10-12
- 対象issue: #551
- ブランチ: work/1012-rgn
- 着手時HEAD: 102f629d

## 指示

【Claude作成】Claude Code 向け指示：#551 の2本目（作り (E) の1段目）。regenerate.py --check（生成して作業ツリーを戻す）と共通の部品 lib/check.py を作り、辞書の --check を載せ替える。title の fetch_tab() の件を起票する Chat-Ref: CHAT-1012-RGN-10 マージ: 承認済み（チャットで）。下の「止まる条件」のどれかに当たったらマージしない 貼る時機: いつでも（CHAT-1012-RGN-09 は判断待ち。この指示がその続き） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-rgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1012-RGN-09.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。RGN-09 の `## 報告` の状態の末尾に `/ 続き: CHAT-1012-RGN-10` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#551 が Open で、他セッションの着手中コメントが無いことを確かめる。

目的
RGN-09 の推奨 (E) の1段目。`regenerate.py --check` で、シートを直した直後に全ページ（または指定のページ）が生成で止まるかを、本番に何も出さずに確かめられるようにする。あわせて、2段目以降で各ページに入れる「書かずに理由を全部出す」形の共通の部品を作り、辞書の `--check` をそれに載せ替える。
決定（2026-10-12、平野さん）

* 作りは (E): 1段目は `regenerate.py --check` が今の生成スクリプトをそのまま動かし、終わったら #533 の `TreeSnapshot` で作業ツリーを戻す（書いて戻す形を許す）。2段目以降で、止まる理由を全部出したいページから各スクリプトの `--check`（書かない）と共通の部品を入れる
* 凍結中の books_pages は `--check all` の対象から外す（今の `all` と同じ。名前を指定すれば動く）
* title の `fetch_tab()`（sheets.py を通らず、「タイトル」「タイトル戦」「連盟プロ以外」「別名」にフィルタの検知〈#432〉が掛かっていない）は、#551 とは別の issue に起票する
* ask_data が live_pages の警告（除外した行）を作るのに出していない件は、4本目（houou 系）で警告として出す
* （2026-10-12 RGN-09 の前に決めたもの）`--check` はシートを読む全ページに作る。止まる理由と警告を分けて両方を全部出し、警告だけなら終了コード 0。実装の指示のマージは承認済み（止まる条件つき）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 行番号・作りは CHAT-1012-RGN-09 のログ（cloudflare 915bafa2）のとおり（要確認。その後の cloudflare の変更に合わせる）
* `regenerate.py --check <ページ…|all>` の形の案:
   * 対象の選び方は今の `regenerate.py` と同じ（`all` は `FROZEN_FROM_ALL` を除く）。`--changed` との組み合わせは要らない
   * 各ページについて、そのページの生成スクリプトが `--check` を持っていれば（今は辞書だけ）それを呼ぶ（書かない）。持っていなければ普通に生成し、終わったら（成功・失敗とも）`TreeSnapshot` でそのページの前の状態に戻す。最後に作業ツリーが `--check` を始める前と同じであることを確かめる
   * `check_asset_limits` とコミット対象の一覧（最後の行）は出さない。ワークフローからは呼ばない（`regenerate-page.yml` は変えない）
   * 出力: ページごとに「止まる理由」（失敗したページは生成の出力の最後の行など）と「警告」（生成の出力の `警告:`・`::warning::` などの行。見分け方は実物で決める）を分け、最後にまとめて出す。止まる理由が1つでもあれば終了コード 1、警告だけなら 0
   * 外部への HEAD（live_pages の img.youtube.com・帰り道の pbs.twimg.com）は生成と同じに回る（割り当て・課金の要る API は無い。RGN-09 で確かめ済み）。live_pages のキャッシュの書き込みも戻る
* `scripts/lib/check.py` の案: 止まる理由と警告を集めて、最後にまとめて出し、終了コードを決める小さな部品。2段目以降で各生成スクリプトの `--check` が使う。辞書の `run_check()`（`generate_resource_dictionary.py`）をこれに載せ替え、出力と終了コードが今と同じか、同じでなければ差を報告に書く
* docs/notes/static-generation.md に `regenerate.py --check` の使い方を足す（シートを直した直後、再生成の前に回す。書いて戻すこと、HEAD が回ること、`--check` を持つページは書かないこと）。追記先の今の内容を読んでから足す
* fetch_tab の issue: 題の案「title の fetch_tab() がフィルタの検知（#432）と再試行を通らずにシートを読む」。本文は RGN-09 のログの該当（1-1 の「例外」の段落）を permalink で引用し、読むページ（title_pages・jpml_pros・jpml_test）とタブを書く。論点（未決）: `lib/sheets.py` の読み方に寄せるか、`fetch_tab()` に検知だけ足すか。関係: #432・#551。期日なし

手順

1. 作る: `regenerate.py --check` と `scripts/lib/check.py` を作り、辞書の `--check` を載せ替える。`scripts/tests/` に試験を足す（`test_regenerate.py` の #533 の試験と同じ一時のリポジトリの形で: 成功するページ・失敗するページ・警告を出すページ・`--check` を持つページを置き、作業ツリーが元に戻ること、止まる理由と警告が分かれて全部出ること、終了コードが 1／0 になること、`--check` を持つページは書き出しが起きないこと）。先に、直す前のコードでは足した試験が通らないことを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。
2. 確かめる: 今の cloudflare で `python3 scripts/regenerate.py --check all` を流し、終わった後の `git status` が空であること、各ページの結果（止まる理由・警告の数と中身）、かかった時間をログに書く。続けて `python3 scripts/regenerate.py all` を流し、生成物が変える前のコードで流したときと同じであること（シートの変化で説明できる差を除く）を確かめる。辞書の `python3 scripts/generate_resource_dictionary.py --check` の出力と終了コードを、載せ替えの前後で比べる。docs/notes/static-generation.md に使い方を足す。
3. 起票とマージ: 同じ主題の issue をクローズ済み・コメントまで含めて検索し（語: 「fetch_tab」「フィルタ」×「title」）、無ければ fetch_tab の issue を起票する（ラベルは `分野: 自動化` と `gh label list` で合う対象。「状況:」・期日なし）。上の「決定」を `docs/decisions/automation.md` に足す。「マージ:」の行のとおり cloudflare に入れ、マージ後の Workers Builds と push で動くワークフローの結果を待つ（上限15分）。#551 に経過と、2段目以降の残り（帰り道 → title 系 → houou 系 → live・最強戦 → 残り）をコメントする（閉じない）。

止まる条件

* CHAT-1012-RGN-09 の状態が「判断待ち」でない、#551 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが `scripts/regenerate.py`・`scripts/generate_resource_dictionary.py`・`scripts/tests/test_regenerate.py` の同じ行・同じ関数を変えている、または取り込みで衝突する
* 試験が通らない。または、直す前のコードでも足した試験が通る
* `--check all` の後の `git status` が空でない（戻せなかったファイルを書いて止まる）
* `regenerate.py all` の生成物が、変える前のコードで流したときと違う（シートの変化で説明できる差を除く）
* 辞書の `--check` の出力・終了コードが載せ替えで変わり、理由を説明できない
* 同じ主題（fetch_tab）の issue がある（起票せず、ほかは進めてよい。報告に書く）
* ワークフロー（`.github/workflows/`）を変える必要が出た
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（`--check all` の結果の表と時間、起票した issue の番号を書く。#551 の2段目以降が残るので、状態は同節と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RGN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RGN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- Chat-Ref の重複: `git log --all --grep="CHAT-1012-RGN-10"` は0件
- 作業ブランチ: ローカル・リモートの work/1012-rgn とも `origin/cloudflare` の祖先（RGN-09 でマージ済み）→ `git merge --ff-only origin/cloudflare`（変化なし、102f629d）
- 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- RGN-09 の `## 報告` の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1012-RGN-10` を足した（41920e80）
- #551 は Open。コメントは RGN-09 の着手中と結果（このセッション）だけ。着手中コメント: https://github.com/retroeater/mj/issues/551#issuecomment-6116963191
- 未マージの work/ ブランチで `scripts/regenerate.py`・`scripts/generate_resource_dictionary.py`・`scripts/tests/test_regenerate.py`・`scripts/tests/test_resource_dictionary.py`・`docs/notes/static-generation.md`・`scripts/lib/check.py` を変えているものは無い（`git diff --name-only origin/cloudflare...<ブランチ>` で全件を見た）

### 1. 作る（027286cf）

変える前の基準（102f629d）:

- `python3 scripts/generate_resource_dictionary.py --check` → 終了コード 0、「検査: 通りました(保存の語数 1870語。ファイルは書いていません)」（5秒）
- `python3 scripts/regenerate.py all` → 終了コード 0、199秒、`git status` は空（生成物はコミット済みのものと同じ）

作ったもの:

- `scripts/lib/check.py`（新）: `Check`（`stop()`・`warn()`・`report()`）と `requested()`。`report()` は警告を `警告: …` の行で出し、止まる理由があれば「検査: 止まります(N件)」と「- 理由」の行を出して 1、無ければ「検査: 通りました(…ファイルは書いていません)」で 0
- `scripts/regenerate.py`: `--check` を足した（`--changed` とは一緒に使えない）。対象の選び方は今と同じ（`all` は `FROZEN_FROM_ALL` を除く）。`run_checks()` は、ページごとに `TreeSnapshot` を取り、生成スクリプトを動かして（`lib/check.py` を import するスクリプトは `--check` で）、成功・失敗とも前の状態に戻す。最後に `--check` を始める前の状態に戻し、戻したファイルがあれば止まる理由にする。`--check` を持つページがファイルを書いたら、それも止まる理由にする（戻す）。`check_asset_limits` とコミット対象の一覧は出さない。`run_page()` に引数と出力の行を受け取る口を足した（今の生成の動きは同じ）
  - 警告の見分け方（実物の出力から決めた）: 行頭が `警告: ` か `::warning::` の行。続く字下げの行はその警告の詳細として数える（live_pages の「公開しなかった行 830件」の行一覧など）。title_pages の「警告: 0件」は警告にしない
  - 止まる理由: 生成は失敗したときの出力の最後の行（#533 の飛ばしたページの理由と同じ）。`--check` は「検査: 止まります」の後の「- 」の行（無ければ最後の行）
- `scripts/generate_resource_dictionary.py`: `run_check()` を `check.Check` に載せ替え、`__main__` の判定を `check.requested()` にした
- 試験: `scripts/tests/test_regenerate.py` に `CheckModeTest` 7件（一時のリポジトリに、成功・警告・失敗・`--check` を持つ・`--check` で書いてしまう の5つの生成スクリプトを置く。作業ツリーが未コミットの変更ごと元に戻る／止まる理由と警告が分かれて全部出る／止まる理由があれば 1・警告だけなら 0／`--check` を持つページは `--check` で呼ばれる／`--check` で書いたら止まる理由／コミット対象と `check_asset_limits` を出さない）。#533 の試験の一時のリポジトリにも `lib/check.py` を置いた（`regenerate.py` が import するため）
- 直す前のコードで足した試験を流すと7件とも落ちた（5件 FAIL・2件 ERROR。`--check` が無いので argparse が終了コード 2）。直した後は `test_regenerate.py` 17件・`python3 -m unittest discover -s scripts/tests` 845件とも OK

### 2. 確かめる

`python3 scripts/regenerate.py --check all`（コミット 027286cf の上、作業ツリーはきれいな状態で）: 終了コード 0、177秒。終わった後の `git status` は空。
（コミット前、未コミットの変更がある状態でも1回流し、186秒・終了コード 0・前後の `git status` が同じことを確かめた）

| ページ | 結果 | 止まる理由 | 警告 |
|---|---|---|---|
| ask_data | 通る | 0 | 0 |
| houou_leagues | 通る | 0 | 0 |
| houou_pages | 通る | 0 | 0 |
| houou_race | 通る | 0 | 0 |
| jpml_pros | 通る | 0 | 0 |
| jpml_test | 通る | 0 | 0 |
| live_pages | 通る | 0 | 1（公開しなかった「【3】手動補正」の行 830件。詳細 830行） |
| ouka_leagues | 通る | 0 | 0 |
| resource_dictionary | 通る（`--check`） | 0 | 0 |
| resource_efficiency | 通る | 0 | 0 |
| resource_logs | 通る | 0 | 0 |
| rh_paifu | 通る | 0 | 0 |
| rh_results | 通る | 0 | 0 |
| rh_results_detail | 通る | 0 | 0 |
| saikyo_mens | 通る | 0 | 0 |
| saikyo_pages | 通る | 0 | 0 |
| title_pages | 通る | 0 | 0 |
| video_en | 通る | 0 | 0 |
| video_live | 通る | 0 | 0 |
| video_mtsuku | 通る | 0 | 0 |
| video_wayhome | 通る | 0 | 0 |
| wayhome_episodes | 通る | 0 | 1（jpml_prosに該当する選手がいないため、Xの写真を出しません: タマシュ・エルドス） |

（books_pages は凍結で対象外。22ページ）

`python3 scripts/regenerate.py all`（変えた後）: 終了コード 0、172秒、`git status` は空、最後の行（コミット対象の一覧）は変える前と同じ。生成物は変える前のコードで流したときと同じ（どちらもコミット済みのものと差0件）。

辞書の `--check` の載せ替えの前後:

- 実物のシート: 出力・終了コードとも同じ（終了コード 0、「辞書」タブ・「プロ」の語数の行と「検査: 通りました(保存の語数 1870語。ファイルは書いていません)」）
- `fetch` を差し替えて3通りを比べた（変える前のファイルを一時に置いて両方の `run_check()` を呼んだ）: 行の検査で止まる場合・通る場合は同じ。**シートの読みの例外（見出し・タブ・フィルタ）で止まる場合だけ、「検査: 止まります」が「検査: 止まります(1件)」になる**（共通の部品は件数を必ず添えるため。終了コード 1 と「- 理由」の行は同じ）。既存の試験（`test_resource_dictionary.py` の `CheckModeTest`）はそのまま通る

docs/notes/static-generation.md「メンテナンス用スクリプトの詳細」の `regenerate.py` の項の次に `regenerate.py --check` の使い方を足し、辞書の `--check` の項に出力の形の参照を足した（1650cfa8）。

### 3. 起票

同じ主題の検索: issue 550件・コメント 2015件を REST の `repos/retroeater/mj/issues?state=all`・`issues/comments` で全部取って手元で検索（`search/issues` はセッションの制限で使えない、RGN-08 と同じ）。`fetch_tab` を含むのは #551 の RGN-09 のコメントだけ。題に「フィルタ」を含むのは #48・#153・#189（表の絞り込み）・#432（フィルタの検知。本文・コメントに title の `fetch_tab()` が検知を通らない件は無い）。→ #552 を起票した（ラベル: 分野: 自動化・対象: title・対象: jpml_pros・対象: jpml_test。「状況:」・期日なし）。

`docs/decisions/automation.md` に「決定」節の4項目を足した（RGN-09 の前の決定は RGN-09 で足し済み）。

### マージ・マージ後

- `git fetch` のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真 → `git push origin work/1012-rgn:cloudflare`（102f629d..cf8b4b97）
- cf8b4b97 の check-run: Workers Builds: mj・regenerate・check とも success（数分で揃った）。push で動いた再生成（`scripts/lib/` の変更で全ページが対象）は生成物の差が無く、コミットは無い
- #551 に経過と2段目以降の残りをコメントした: https://github.com/retroeater/mj/issues/551#issuecomment-6117169270

## 報告

- 状態: 完了
- ブランチ: work/1012-rgn
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-RGN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rgn
- 確認用URL: なし（生成物は変わらない。表示を変える変更は無い）
- マージ: 済（cf8b4b97。このログの仕上げは追いの push）
- issue: #551（2本目を実装。2段目以降の残り〈帰り道 → title 系 → houou 系 → live・最強戦 → 残り〉は #551 にコメントした）、#552（起票: title の fetch_tab() がフィルタの検知と再試行を通らない）
- 判断が必要なこと: なし（#551 に移した）
- 未確認の項目: なし
- エラー: なし

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
