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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ab6bf27d）: https://github.com/retroeater/mj-logs/tree/main/guide/ab6bf27d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
