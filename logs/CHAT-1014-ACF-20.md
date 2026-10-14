# CHAT-1014-ACF-20

- 着手日時: 2026-10-14
- 対象issue: #514・#562
- ブランチ: work/1014-acf-refetch
- 着手時HEAD: ab6bf27df36bbe63005cd2dc00023db150800650

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの毎日の更新で、ページが使う X の画像の大きさ（`_80x80`・`_200x200`・`_400x400`）のどれかが取れなければ、X API で取り直すようにする Chat-Ref: CHAT-1014-ACF-20 マージ: 承認済み（チャットで、2026-10-14。平野さんが「マージ＋自動で取り直す」を選んだ）。条件は「止まる条件」に当たらず、作業ブランチでの dry_run の手動実行の結果が見込みどおりであること 貼る時機: いつでも。ただし毎日の更新（Worker から 04:10）と重ならないよう、03:30〜05:00 には cloudflare へ入れない。CHAT-1014-ACF-18・ACF-19 とはブランチが別なので、並行で動かすなら別のセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-acf-refetch の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜15・CHAT-1014-ACF-16〜19 で使ったもの。 作業ブランチ: クラウドセッションで実行する。work/1014-acf-refetch を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-acf-refetch origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
X の写真は、大きさによって片方だけ 404 になることがある（例: 上田稜は `_80x80` だけ 404、`_200x200`・`_400x400` は 200。2026-10-14、CHAT-1014-ACF-17・#562）。毎日の SNS ブックの更新（`update_sns_book.py`）は前の画像を残すかどうかを `_400x400`・`_200x200` の確かめだけで決めるので、`_80x80` だけ切れた写真は取り直されない。jpml_pros（48px）・鳳凰戦の表のアイコン（40px）・順位変動の丸（28px）・候補（32px）は `_80x80` を使う（#558）。ページが使うどの大きさが切れても、毎日の更新で取り直すようにする。
決定（2026-10-14、平野さん）

* ページが使う大きさのどれかが取れなければ、毎日の更新で X API で取り直す（`_80x80` を確かめに足す）
* 鳳凰戦の `_80x80` 化（ACF-19）は、この仕組みを待たずにマージする

前提（チャット側。平野さんの決定ではない。2026-10-14 11:25 JST、cloudflare と REST で確かめた）

* `update_sns_book.py` の `Reachability.all_ok(url)` は `x_api.page_urls(url)` のすべてを HEAD で確かめる（94行付近）。`x_cell()` は前の値が `all_ok` なら残し、そうでなければ `fetcher.get()`（X API）で取り直す（142行付近）。192行付近でも `x_api.page_urls()` で確かめる URL を集めている
* `collect_saikyo_images.py` にも `page_urls()`（101行付近、`X_PAGE_SIZES` の2つの大きさ）があり、最強戦の写真の検知（ジョブ `saikyo`）が使う。`x_api.page_urls()` と同じものか、別のものかは確かめる。最強戦の検知が確かめる URL の集まりは変えない（変えるなら止まる）
* 直し方の案: 毎日の更新の確かめに使う大きさだけに `_80x80` を足す（例: `update_sns_book.py` の中で確かめる大きさの一覧を持つ、または `x_api.page_urls()` に大きさの引数を足して毎日の更新からだけ `_80x80` を含めて呼ぶ）。HEAD の数は1行あたり2→3、X画像URL のある約1,050行で1日 約1,050 回増える見込み。所要時間の増分をログに書く
* 取り直しで X API の呼び出しが増えるのは、どれかの大きさが切れた行だけ（今は上田稜の1件の見込み）。上限（1日 30）の内
* 取り直しても X 側で `_80x80` が 404 のままの写真がありうる（新しい URL でも同じ）。その場合は毎日取り直し続けないよう、取り直した URL が前と同じなら残す・同じ日に1回だけなど、無駄に呼ばない形にする（案を書いて実装。迷えば「判断が必要なこと」に書く）
* #562（画像リンク切れの検知結果、2026-10-14 00:28Z に github-actions が作成、ラベル無し）は、#352（常設、Closed）が閉じていたため新しく作られた見込み（ACF-09 の作り）。この指示では直さず、#352 を開き直すのと新しく作るのとどちらが前の動きだったかを確かめて「判断が必要なこと」に書く
* 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize・1014-acf-cron・1014-gds）は、どれも `update_sns_book.py`・`collect_saikyo_images.py`・`check_image_links.py`・`lib/x_images.py`・`lib/sns.py` に触れていない。着手時に改めて確かめる
* 使う skill は無い

手順

1. 同じ目的の issue と、未マージの work/ ブランチで上のファイルに触れるものが無いことを確かめる。#514・#558・#562 の最新のコメント、docs/notes/sns-book.md「検知と更新の役割」を読む。`x_api.page_urls()` と `collect_saikyo_images.page_urls()` の関係と、それぞれの呼び出し元をすべて洗い出してログに書く
2. テストを先に足し、直す前のコードで通らないことを確かめる（前の値の `_80x80` だけ 404 なら取り直す／3つとも 200 なら X API を呼ばない／最強戦の検知が確かめる URL は変わらない／取り直しても同じ URL なら無駄に呼び続けない）。そのうえで直し、`python3 -m unittest discover -s scripts/tests` を通す
3. 作業ブランチで update-sns-book.yml を `mode: update`・`dry_run: true` で手動実行し、X API を呼ぶ予定の数（見込み: 上田稜の1件前後。外の揺れを見込み、見込みより多ければ同じ実行をもう1回行い、それでも多ければ止まる）と所要時間をログに書く。可能なら同じ日に cloudflare でも dry_run を1回行い比べる。docs/notes/sns-book.md「検知と更新の役割」に確かめる大きさを書く。上の「決定」を docs/decisions/saikyo.md に足す。#514 に結果をコメントし、マージする（03:30〜05:00 を避ける）

止まる条件

* 同じ目的の issue がある、または未マージの work/ ブランチが上のファイルを同じ行・同じ関数で変えている
* 最強戦の検知（ジョブ `saikyo`）が確かめる URL の集まりが変わる
* 足したテストが直す前のコードで通ってしまう、またはテストが通らない
* dry_run で X API を呼ぶ予定が、やり直しても 5件を超える、または X画像URL 以外の列の変化が出る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（dry_run の run の URL・件数・所要時間を書く。マージ後の翌朝 04:10 の更新で上田稜の X画像URL が取り直され、`_80x80` が 200 になるかは「未確認の項目」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-ACF-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-ACF-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- Chat-Ref の重複: `git log --all --grep="CHAT-1014-ACF-20"` は 0 件。`work/1014-acf-refetch` はローカル・リモートとも無く、`git checkout -b work/1014-acf-refetch origin/cloudflare`（ab6bf27d）
- 手順1:
  - 同じ目的の issue: ACF-07 以降の検索の範囲で無い（#558 は 10/14 にクローズ、#514 は SNS ブック全般、#562 は検知の結果）
  - 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-gds・1014-wfl）で `update_sns_book.py`・`collect_saikyo_images.py`・`check_image_links.py`・`lib/x_images.py`・`lib/sns.py` に触れるものは無い
  - **`x_api.page_urls()` と `collect_saikyo_images.page_urls()` は同じ関数**（`update_sns_book.py` は `import collect_saikyo_images as x_api`）。呼び出し元は5か所:
    - `update_sns_book.py` 95行 `Reachability.all_ok()`（前の値・取り直した値の到達）
    - `update_sns_book.py` 192行 `update_images()` の先読み（`reach.prefetch()`）
    - `collect_saikyo_images.py` 112行 `broken_urls()`（最強戦の写真の検知、ジョブ `saikyo`）
    - `collect_saikyo_images.py` 185行 `image_from()`（X API の `profile_image_url` を `_400x400` に直す。`[0]` だけ使う）
    - `collect_saikyo_images.py` 211行 `resolve()`（`--resolve-test` の診断）
  - #514・#558・#562 の最新のコメント、docs/notes/sns-book.md「検知と更新の役割」「毎日の更新」を読んだ
- 手順2:
  - テストを4件足した（`scripts/tests/test_sns_book.py` の `XCellTest`）: `_80x80` だけ 404 なら取り直す・3つとも取れれば呼ばない（先読みに `_80x80` が入る）・最強戦の検知の大きさは2つのまま・取り直しても同じ URL で `_80x80` だけ取れなければ「解決(一部の大きさが取得できない)」で残し、翌日は呼ばず、7日後に取り直す。直す前のコードでは3件が FAIL／ERROR（最強戦の大きさのテストは変わらないことの確かめなので前後とも通る）
  - 直したところ: `collect_saikyo_images.page_urls(url, sizes=X_PAGE_SIZES)`（既定は今の2つ。最強戦の検知・`image_from()`・`resolve()` は既定のまま）。`update_sns_book.py` に `CHECK_SIZES = X_PAGE_SIZES + ("_80x80",)`・`PARTIAL = "解決(一部の大きさが取得できない)"`・`PARTIAL_RETRY_DAYS = 7`・`retry_due()` を足し、`Reachability.all_ok(url, sizes=CHECK_SIZES)` と先読みを3つの大きさにした。`x_cell()` は、前の状態が PARTIAL で7日たっておらず `_400x400`・`_200x200` が取れれば X API を呼ばずに残す。取り直した URL が3つとも取れなければ、`_400x400`・`_200x200` が取れるときは取り直した URL を PARTIAL で書き、取れなければ今までどおり「取得したURLが取得できない」で前の値を残す
  - 無駄に呼ばない形の案は上のとおり（7日ごと）。日数は `PARTIAL_RETRY_DAYS` の定数で変えられる
  - `python3 -m unittest discover -s scripts/tests` OK（783113af）
- 手順3:
  - dry_run（`mode: update`・`dry_run: true`）:
    - 作業ブランチ（783113af）https://github.com/retroeater/mj/actions/runs/38503614780 : X API を呼ぶ予定 **1件**、X状態 解決 1,050・dry-run(呼ぶ予定) 1・既定のアイコン 12・未入力 780・アカウントなし(確認済み) 1、ページが使う値が変わった人 0、所要（スクリプト）12 秒
    - 同じ時刻の cloudflare https://github.com/retroeater/mj/actions/runs/38503612971 : 呼ぶ予定 0件、解決 1,051、変わった人 0、所要 19 秒
    - 作業ブランチだけ「【3】画像取得 を書き直します」が出たのは、dry-run で1件の X状態が「dry-run(呼ぶ予定)」になるため（X画像URL とほかの列は変わらない。変わった人 0）
    - HEAD は X の画像のある行で 2→3（約 1,050 回増える）だが、所要は 12 秒と 19 秒で通信の揺れの内
  - 1件の中身: dry-run は名前を出さない。直後に手元で【3】の X の写真の3つの大きさを全部 HEAD したところ、上田稜の `_80x80` は 200 に戻っていた（`_200x200`・`_400x400` も 200）。dry_run の時点（02:56Z）では上田稜の `_80x80` がまだ 404 だったと見られる。ほかに取れないものは既定アイコン（abs.twimg.com、セッションのプロキシで拒否、確かめの対象外）だけ
  - docs/notes/sns-book.md「毎日の更新」の X の行に確かめる大きさと PARTIAL の扱い、状態の文言に「解決(一部の大きさが取得できない)」を足した（「検知と更新の役割」は最強戦の検知の大きさを書いていないので、毎日の更新の行に書いた）。決定を docs/decisions/saikyo.md に足した
  - #514 に結果をコメントした https://github.com/retroeater/mj/issues/514#issuecomment-6159881641
  - #562 と #352 の作り: check-image-links の issue の反映は、最初の版（54e0daf2、2026-09-07）から「開いている同じ題の issue が無ければ新しく作る」作りで、閉じた issue を開き直したことは無い（ACF-09 も同じ）。#352 は 2026-09-16 に github-actions が作り、`種類: 常設` は 10/02 に平野さんが付けた。10/13 02:39Z に初めて自動で閉じられ（ACF-09 の作業ブランチの手動実行）、10/14 00:28Z の検知で #562 が新しく作られた（ラベル無し）
- cloudflare へマージ: 11:59 JST に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1014-acf-refetch:cloudflare`（ab6bf27d..ca78f405）。03:30〜05:00 の外

## 報告

- 状態: 判断待ち
- ブランチ: work/1014-acf-refetch
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-ACF-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-acf-refetch
- 確認用URL: なし（ページは変えていない）
- マージ: 済（ca78f405）
- issue: #514（結果をコメント）・#562（読んだだけ）
- 判断が必要なこと:
  - 画像リンク切れの検知の issue: check-image-links は最初の版（2026-09-07）から「開いている issue が無ければ新しく作る」作りで、#352（常設のラベルは平野さんが手で付けた）が 10/13 に自動で閉じたため、10/14 に #562（ラベル無し）が新しく作られた。(a) 閉じた同じ題の issue があれば開き直す作りにする（常設の番号を保つ）／(b) 今のまま（新しく作られたら、その都度ラベルを手で付ける）。どちらかを選んでほしい（#562 と #352 の扱いも合わせて）
- 未確認の項目:
  - マージ後の翌朝（2026-10-15）04:10 の更新で、`_80x80` が取れない写真があれば取り直されること。上田稜の `_80x80` は 10/14 12 時ごろに 200 に戻っていたので、翌朝は取り直しが起きない見込み（起きなければ、X API の呼び出し 0 で「解決」のまま）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a372bb9f）: https://github.com/retroeater/mj-logs/tree/main/guide/a372bb9f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
