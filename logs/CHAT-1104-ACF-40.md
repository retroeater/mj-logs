# CHAT-1104-ACF-40

- 着手日時: 2026-11-04
- 対象issue: #662
- ブランチ: work/1104-acf-cache
- 着手時HEAD: 8c7dd9a3

## 指示

【Claude作成】Claude Code 向け指示：Sheets API の読み取りを1回の再生成の中でページをまたいでキャッシュし、呼び出しを減らす（#662 の対策、自律開発）
Chat-Ref: CHAT-1104-ACF-40
マージ: 承認済み（チャットで、2026-11-04）。条件: (1) origin/cloudflare を取り込んだ後に `python3 -m unittest discover -s scripts/tests` が通る (2) 作業ブランチで regenerate-page.yml を dry_run 真・対象 all・compare_ref＝その時点の origin/cloudflare の SHA で動かし、新旧の全ページの生成物の差分が0、かつ飛ばしたページが0 (3) 同じ run の新しい側（作業ブランチ側）の、ページごとの `Sheets API の呼び出し: 合計 N` の N の総和が 150 以下（見込み 105。ACF-38 の計測で重複を除いた組の数）。1つでも満たさなければマージせず止まる
貼る時機: いつでも（並走の有無は手順2で見る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-acf-cache の作成と push、条件を満たしたときの cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-acf-cache を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-acf-cache origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#662（Sheets API の1分あたりの読み取り上限〈429〉で、並走する再生成がページを飛ばす）の対策。`scripts/regenerate.py` はページごとに生成スクリプトを別のプロセスで動かすため、`lib/sheets.py` の `lru_cache` がページをまたいで効かず、全ページの生成1回で 545 回呼んでいる（重複を除くと 105 組。CHAT-1104-ACF-38 のログ・#662 の計測のコメント）。1回の再生成の中だけ有効なファイルのキャッシュをページの間で共有し、呼び出しを減らす。生成物は変えない。

### 決定（2026-11-04、平野さん）
- #662 の対策として、ページをまたぐ読み取りのキャッシュを自律開発で進める
- 範囲はキャッシュだけ。batchGet（同じブックの複数タブを1回で読む）は入れない（効果を見てから別に判断）
- ACF-37 で足した「呼び出しのたびの1行」（`Sheets API の読み取り: …`）は消し、ページごとの要約の行（`Sheets API の呼び出し: …`）は残す
- 冒頭の「マージ:」の行の条件 (1)〜(3) をすべて満たせば、プレビューを待たずに cloudflare へマージしてよい

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 設計の案:
  - `scripts/regenerate.py` が実行の始めに一時ディレクトリ（リポジトリの外。`tempfile` か `RUNNER_TEMP` の下）を作り、環境変数（例 `SHEETS_CACHE_DIR`）で子のプロセスに渡す。実行の終わりに消す。`run_page()` は子の環境を `os.environ` から作っている（要確認）
  - `lib/sheets.py` の `_book()`・`_values()` は、環境変数があればキャッシュのファイル（鍵: ブック ID・タブ名・render・`BOOK_FIELDS` などの引数の hash。ファイル名にブック ID を出さない）を先に見て、あれば API を呼ばない。無ければ今までどおり読んでから書く。環境変数が無ければ今までと同じ動き（単独で生成スクリプトを動かすとき・ほかのワークフローから呼ぶとき）
  - 書き込みは一時ファイルに書いてから rename（途中で落ちたときに壊れたキャッシュを残さない）。読み込みに失敗したら API で読み直す
  - 要約の行の「合計」は API を実際に呼んだ回数のままにし、キャッシュに当たった回数を別に足す（例 `…、キャッシュ k 回`）。待ちの行（`Sheets API 429: …`）は残す
  - キャッシュは1回の実行の中だけなので、シートの更新が次の実行に反映されない心配は無い。compare_ref の旧い側の生成は旧いコードで動くのでキャッシュを使わない（要確認。新旧が同じキャッシュを共有しないこと）
- 確かめること（要確認。食い違えば止まる条件へ）:
  - 1回の `regenerate.py` の実行の中で、シートへ書き込んでから同じタブを読み直すページが無いこと（`lib/sheets_write.py` などの書き込みを生成スクリプトの中から呼んでいないか）。あればキャッシュで古い値を読むので止まる
  - キャッシュのファイルの中身（シートの値）は一時ディレクトリにだけ置き、リポジトリ・生成物・実行サマリ・artifact に出ないこと（コミットの手順が拾わないこと）
- 計測の run は並走で 429 が出ても呼び出しの数（条件 (3)）は変わらないが、旧い側の生成（キャッシュなし、約545回）が 429 でページを飛ばすと条件 (2) を満たさない。起動の前に regenerate-page の実行中の run を見て、あれば 15 分まで待つ（待っても残れば並走を記録して起動してよい）

## 手順
1. #662 が open で他セッションの着手中コメントが無いことを確かめ、着手中のコメントを書く。未マージの work/ ブランチが scripts/lib/sheets.py の `_book()`・`_values()`・`_get()`・要約の関数、scripts/regenerate.py の `run_page()`・実行の始めと終わりを変えていないかを `git branch -r --no-merged origin/cloudflare` の各ブランチで確かめる。「前提」の確かめること2点を確かめる。そのうえでキャッシュを実装し、呼び出しのたびの1行を消す。`scripts/tests/` にテストを足す（2つ目のプロセスがキャッシュに当たって API を呼ばない・環境変数が無いときは今までどおり・壊れたキャッシュは読み直す・要約の行の形）。テストが通ることを確かめ、self-review（CLAUDE.md のとおり）を回す
2. 作業ブランチで regenerate-page.yml を dry_run 真・対象 all・compare_ref＝その時点の origin/cloudflare の SHA で動かし、完了を待つ（15分の上限は CLAUDE.md「判断・作業の原則」のとおり。超えたら run の ID を書いて、同じセッションで続けて待ってよい〈この指示の範囲で1回まで延長、合計 45 分〉）。結果: 新旧の差分の有無、飛ばしたページ、ページごとの「呼び出し・キャッシュに当たった回数・429 の待ち回数と秒数・経過秒数」の表（全件）と合計、並走していた run。差分か飛ばしたページが出たときは、理由が 429 か、シートの値の変化（新旧の読む時刻のずれ）で説明できるなら、並走の無いときにもう1回だけ同じ実行をしてよい。それでも出たら止まる
3. 冒頭の条件 (1)〜(3) をすべて満たせば cloudflare へマージする（取り込み直しの後もテストを流す）。マージの push で動く push 検知の regenerate-page.yml を 15 分を上限に待ち、結論と、ページごとの要約の行から呼び出しの合計・429 の待ちの合計を報告に書く（終わらなければ run の ID を未確認の項目へ）。#662 に「【キャッシュ（CHAT-1104-ACF-40、2026-11-04）】」の見出しで、手順2の表・合計・マージの SHA・push 検知の run の結果をコメントする（#662 は閉じない。毎朝の運行の所要で効果を見てから閉じるかを平野さんが決める）。docs/notes/static-generation.md「シートの読み方（Sheets API）」・docs/notes/sheets-reader.md の該当の記述をキャッシュの仕組みに合わせて直し（追記先の今の内容を読んでから）、docs/decisions/security.md の #662 の節に上の「決定」を足す

## 止まる条件
- #662 が open でない、または他セッションの着手中コメントがある
- 生成の途中でシートへ書いて読み直すページがある、またはキャッシュの中身がリポジトリ・生成物・実行サマリ・artifact に出る経路がある（見つけた所を報告に書いて止まる）
- 未マージの work/ ブランチが、手順1に挙げた関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよく、解いた後の該当箇所をログに引用する）
- 冒頭のマージの条件 (1)〜(3) のどれかを満たさない（手順2のやり直し1回の後も）。そのときは作業ブランチを残し、表と差分の内容を報告に書いて「判断待ち」にする
- マージ後の push 検知の再生成が、今回の変更に起因する理由（キャッシュの読み書きの例外など）で失敗した。429 で飛ばしたページがあるだけなら止まらず、飛ばしたページ・並走していた run・429 の待ちの合計を報告に書く
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手順2の run の ID、マージの SHA、push 検知の run の ID と結論、#662 のコメントの URL を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-ACF-40.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-ACF-40 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 同じ Chat-Ref のコミット: なし（`git log --all --grep=CHAT-1104-ACF-40`）。`ACF` は ACF-36〜39 の続きで最初の指示ではない
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 作業ブランチ: ローカル・リモートとも work/1104-acf-cache が無かったので `git checkout -b work/1104-acf-cache origin/cloudflare`（8c7dd9a3）
- #662: open。着手中コメントは ACF-37（別セッション、計測。ACF-39 でマージ済みで作業は終わっている）だけで、進行中の他セッションの着手は無いと判断。着手中のコメント: https://github.com/retroeater/mj/issues/662#issuecomment-6623269249

### 手順1: 未マージの work/ ブランチ
`git branch -r --no-merged origin/cloudflare` の各ブランチで `git diff origin/cloudflare...<b> -- scripts/lib/sheets.py scripts/regenerate.py`:
- origin/work/1101-prp-pub: scripts/regenerate.py の `OUTPUT_OVERRIDES` の `pro_pages` の行だけ
- origin/work/1104-lgn-policy: scripts/regenerate.py の `OUTPUT_OVERRIDES` に `policy_page` を足すだけ
- どちらも手順1に挙げた関数（`_book()`・`_values()`・`_get()`・要約・`run_page()`・`main()` の始めと終わり）ではないので止まらない。lib/sheets.py を変えるブランチは無し

### 手順1: 前提の確かめ
- 生成の途中でシートへ書くページ: 無し。`scripts/generate_*.py`・`scripts/lib/*.py` で `sheets_write` を import するものは無い（`lib/sheets.py` はコメントで名前を出すだけ）。`batchUpdate|:append|"PUT"|"POST"|values.update` は lib/gcal.py（カレンダー）・lib/sheets_write.py だけ。生成スクリプトから書き込みのスクリプトを subprocess で動かすものも無い（`generate_log_pages.py`・`lib/sns.py` などはコメントで名前を出すだけ）
- キャッシュの置き場所: `tempfile.TemporaryDirectory(prefix="sheets-cache-")`（ランナーでは /tmp の下、リポジトリの外）。regenerate.py の `with` を抜けると消える。作業ツリーの外なので `TreeSnapshot`（git add -A）・コミットの手順・`git status` を見る dry_run のサマリには入らない。値は stderr にも出さない（例外の型名だけ）
- compare_ref の旧い側: regenerate-page.yml「生成物の差を実行サマリに出す」は別のステップで `/tmp/base` の旧いコードの regenerate.py を動かす。新しい側の環境変数は前のステップの中だけで、旧いコードは環境変数を読まないので共有しない
- `run_page()` は子の環境を `{**os.environ, ...}` から作っている（確かめた）。なので `sheets_cache()` が `os.environ` に置き、終わりに元へ戻す

### 手順1: 実装（1947e5dbc）
- `lib/sheets.py`: `_read(method, url, params)` を足し、`_book()`・`_values()` の `_get()` をこれに替えた。`SHEETS_CACHE_DIR` があれば、鍵＝`sha256(json.dumps([url, params], sort_keys=True))`（URL にブック ID・タブ、params に render・fields が入る）のファイル `<鍵>.json` に API の応答そのもの（JSON）を置き・読む。書きは同じディレクトリの一時ファイル→`os.replace`。読めない・壊れたファイルは1行出して API で読み直し、書けないときも1行出して生成は続ける。失敗（SheetsReadError）は置かない
- 呼び出しのたびの1行（`CALL_PREFIX`・`book_token`・`_count_call`）を消した。要約の行に `キャッシュ k 回` を足した（合計は API を実際に呼んだ回数）。キャッシュだけのプロセスも要約を出す
- `regenerate.py`: `sheets_cache()`（contextmanager）を足し、通常の生成と `--check` の両方を囲んだ
- docs: docs/notes/static-generation.md「シートの読み方（Sheets API）」の「読む回数」、docs/notes/sheets-reader.md「段取りと今の状態」5.（別のコミット）

### 手順1: テスト
- `python3 -m unittest discover -s scripts/tests`: Ran 1976 tests、OK（足したテストを含む）。test_sheets_count は 21 件 OK
- 足したテスト（scripts/tests/test_sheets_count.py）: 2つ目のプロセスがキャッシュに当たって API を呼ばない（本物の subprocess 2本。値 `[[6010.0, 'a', True]]` の型が変わらない・要約が `合計 0…、キャッシュ 1 回、`）・環境変数が無いと各プロセスが呼ぶ・render やタブの違いは別の鍵・ファイル名にブック ID が出ない・壊れたキャッシュは読み直して書き直す・失敗は置かない・書けなくても値を返す・regenerate の子が同じディレクトリを受け取り、終わりに消え、リポジトリの外・要約の行の形
- 修正前のコードで通らないか（#310）: origin/cloudflare の worktree に新しいテストのファイルだけを写して実行 → `Ran 21 tests … FAILED (errors=20)`（通るのは理由の行のテスト1件だけ）

### 手順1: self-review（docs/notes/self-review.md）
1回目（新しい文脈の general-purpose、`git diff origin/cloudflare...HEAD -- scripts/ docs/notes/`）: JSON の往復・鍵の衝突・compare_ref・--check・参照元・権限は問題なし。指摘3件:

| 指摘 | 扱い |
|---|---|
| `main()` が `sheets_cache()` で囲むことを確かめるテストが無い（regenerate.py の main、test_sheets_count.py） | 直した（14472414e、`RegenerateMainCacheTest` で通常の生成と --check） |
| 書き込みの途中の OSError で `.tmp` が残る（lib/sheets.py `_write_cache`） | 直した（14472414e、unlink とテスト） |
| 「1回の実行の中でシートが変わらない」前提の `簡略化:` のコメントが無い | 直した（14472414e、`_read` の冒頭） |

2回目（新しい文脈、`git show 14472414e` だけ）: 3点とも直っている。変異試験（コピーした一時ディレクトリで）: --check の `sheets_cache()` を外す・通常の生成の `sheets_cache()` を外す・ページごとに作る・unlink を消す、の4つともテストが落ちた。対応の要る指摘なし。`python3 -m unittest discover -s scripts/tests`: Ran 1979 tests、OK

### 手順2: 計測の run
- 起動前の並走: 05:23Z に regenerate-page の実行中が2本（41778530568 work/1104-lgn-gnotice 05:19Z〜、41778794993 work/1104-top3 05:21Z〜）。05:38Z まで15分待ち、41778530568 は終わったが 41778794993 が残り、途中で 41779916769（work/1104-prp-polish、05:32Z〜）も始まって終わった。41778794993 の並走を記録して起動した
- 起動の前に origin/cloudflare が 8b36d3c34 へ進んでいたので `git merge origin/cloudflare`（衝突なし、234ff84b2）。取り込み後のテスト: OK
- run 41780431563（work/1104-acf-cache、head 234ff84b2、target_page all・dry_run 真・compare_ref 8b36d3c3457d65afe924fb7d12e2227f054867ad）、05:37:55Z 開始
- 15分を超えたので、指示の延長（合計45分まで）で同じセッションで待った。05:58:54Z に終了、success
- 結果（1回目）:
  - 「対象ページを再生成」（新しい側）: 05:38:25Z〜05:44:29Z（約6分。ACF-38 の計測は約22分）。「飛ばしたページを報告」は skipped（新しい側で飛ばしたページは無し）
  - 「生成物の差を実行サマリに出す」: 05:44:29Z〜05:58:52Z。注釈 `compare: 差分なし、compare_ref の生成の終了コード 3`。**旧い側（compare_ref のコード、キャッシュなし）がページを飛ばした**（終了コード 3）。どのページかと理由は `/tmp/base_regen.txt` にだけ出てジョブログに出ないので確かめられない（#662 本文の注記と同じ）。旧いコードは約545回呼ぶので、並走の中での 429 が最もありそうな理由と判断した（推定。実測ではない）
  - 条件 (2) の「飛ばしたページが0」を満たさないので、指示のとおり並走の無いときにもう1回だけ同じ実行をする
  - 並走していた regenerate-page: 41778794993（work/1104-top3、05:21:55Z〜05:54:55Z）・41780537284（cloudflare、push、05:39:00Z〜05:40:14Z）・41782258945（work/1104-top3、05:56:04Z〜）
- ページごとの表（1回目、新しい側。job 126410482438 のログの `== <ページ> ==` の区切りの中の要約の行から。31ページすべてに要約が1行。`Sheets API 429:` の行は28行で、待ちの回数の和と一致。`キャッシュを読めない`・`キャッシュに書けない`・`Traceback`・`生成に失敗しました` の行は無し）:

| ページ | 合計（API） | spreadsheets.get | values.get | キャッシュ | 429 の待ち 回 | 429 の待ち 秒 | ほかの待ち 回 | ほかの待ち 秒 | 経過 秒 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| ask_data | 21 | 5 | 16 | 0 | 0 | 0.0 | 0 | 0.0 | 9.8 |
| ask_data_jwrc | 2 | 0 | 2 | 19 | 0 | 0.0 | 0 | 0.0 | 1.8 |
| ask_data_ouka | 2 | 0 | 2 | 19 | 0 | 0.0 | 0 | 0.0 | 1.8 |
| ask_data_saikyo | 9 | 1 | 8 | 11 | 6 | 65.8 | 0 | 0.0 | 69.4 |
| ask_data_wrcr | 2 | 0 | 2 | 19 | 5 | 32.5 | 0 | 0.0 | 35.0 |
| exam | 3 | 1 | 2 | 14 | 0 | 0.0 | 0 | 0.0 | 2.8 |
| exam_episodes | 0 | 0 | 0 | 17 | 0 | 0.0 | 0 | 0.0 | 1.4 |
| houou_pages | 4 | 0 | 4 | 21 | 0 | 0.0 | 0 | 0.0 | 6.1 |
| jpml_pros | 6 | 0 | 6 | 31 | 0 | 0.0 | 0 | 0.0 | 5.8 |
| jwrc_pages | 0 | 0 | 0 | 25 | 0 | 0.0 | 0 | 0.0 | 0.5 |
| live_pages | 0 | 0 | 0 | 22 | 0 | 0.0 | 0 | 0.0 | 2.3 |
| log_pages | 7 | 1 | 6 | 0 | 6 | 65.4 | 0 | 0.0 | 72.0 |
| mtsuku | 2 | 0 | 2 | 15 | 0 | 0.0 | 0 | 0.0 | 10.5 |
| mtsuku_episodes | 0 | 0 | 0 | 17 | 0 | 0.0 | 0 | 0.0 | 7.9 |
| ouka_pages | 0 | 0 | 0 | 25 | 0 | 0.0 | 0 | 0.0 | 0.5 |
| resource_dictionary | 3 | 1 | 2 | 3 | 0 | 0.0 | 0 | 0.0 | 1.2 |
| resource_efficiency | 3 | 1 | 2 | 0 | 0 | 0.0 | 0 | 0.0 | 1.1 |
| rh_paifu | 3 | 1 | 2 | 0 | 0 | 0.0 | 0 | 0.0 | 1.3 |
| rh_results | 2 | 0 | 2 | 1 | 0 | 0.0 | 0 | 0.0 | 0.7 |
| rh_results_detail | 2 | 0 | 2 | 1 | 0 | 0.0 | 0 | 0.0 | 0.7 |
| ryoei_pages | 15 | 1 | 14 | 0 | 6 | 65.9 | 0 | 0.0 | 70.7 |
| saikyo_mens | 3 | 1 | 2 | 0 | 0 | 0.0 | 0 | 0.0 | 1.1 |
| saikyo_pages | 5 | 1 | 4 | 22 | 0 | 0.0 | 0 | 0.0 | 2.8 |
| study | 2 | 0 | 2 | 15 | 0 | 0.0 | 0 | 0.0 | 4.7 |
| study_episodes | 0 | 0 | 0 | 17 | 0 | 0.0 | 0 | 0.0 | 2.8 |
| title_pages | 2 | 0 | 2 | 25 | 0 | 0.0 | 0 | 0.0 | 1.9 |
| video_wayhome | 0 | 0 | 0 | 13 | 0 | 0.0 | 0 | 0.0 | 0.1 |
| wayhome_episodes | 0 | 0 | 0 | 13 | 0 | 0.0 | 0 | 0.0 | 0.7 |
| wrcr_pages | 0 | 0 | 0 | 25 | 0 | 0.0 | 0 | 0.0 | 0.3 |
| pro_pages | 7 | 1 | 6 | 27 | 5 | 34.2 | 0 | 0.0 | 41.4 |
| top_page | 0 | 0 | 0 | 20 | 0 | 0.0 | 0 | 0.0 | 0.4 |
| **合計** | 105 | 15 | 90 | 437 | 28 | 263.8 | 0 | 0.0 | |

  - **API の呼び出しの合計 105**（ACF-38 の 545 から。重複を除いた組の数 105 と同じ）。キャッシュに当たった 437 と足すと 542（ACF-38 の 545 との差 3 は、その後のコードの変更で読む組が変わったためと見るが、確かめていない）

### 手順2: やり直し（2回目）
- 41782258945（work/1104-top3、05:56:04Z〜）の終了を 06:06Z から待った。06:21Z に実行中の regenerate-page は0本
- その間に cloudflare が e0b3a1409・b877e1e87 へ進んだので2回取り込んだ（衝突なし。a4846398c・37bc64a83。どちらの後もテスト OK）
- run 41785057721（head 37bc64a83、target_page all・dry_run 真・compare_ref b877e1e87550e724cc2210541965780d71bf1b10）、06:22:13Z 開始。起動時の並走: なし

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b877e1e8）: https://github.com/retroeater/mj-logs/tree/main/guide/b877e1e8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b877e1e8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
