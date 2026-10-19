# CHAT-1019-OUK-29

- 着手日時: 2026-10-19
- 対象issue: なし（CHAT-1019-OUK-27・OUK-28 のマージ）
- ブランチ: work/1019-ouk27（CHAT-1019-OUK-28 の続き）
- 着手時HEAD: 6c75515b8

## 指示

【Claude作成】Claude Code 向け指示：OUK 後半の申送り（work/1019-ouk27）を cloudflare にマージする（OUK-28 が止まった page-announcement.md の衝突の解き方を足した） Chat-Ref: CHAT-1019-OUK-29 マージ: 下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんが OUK-27 の足した行を確かめ、マージを許可した） 貼る時機: いつでも（CHAT-1019-OUK-28 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk27 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk27 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない。`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしの衝突は、OUK-27 で足した規則のとおり両方を残して解く。ほかが衝突したら解かずに止まる）。リモートに無ければ止まる。 衝突の解き方（2026-10-19、OUK-28 の報告の案。チャット側が採った。どちらも同じ箇所への追記で両立するため）: `docs/notes/page-announcement.md` の「告知動画の型」の衝突は、cloudflare 側（CHAT-1019-WRC-18 が延ばした「順位の表を止める画面は…」の行）を採り、そのすぐ下にこちら（OUK-27 の E、「止める位置・送る量を変えるときは…測ってから決める」）の1行を置いて解く（どちらの中身も変えない）。これ以外のファイルが衝突したら解かずに止まる。解いた後に衝突の印が0であること（`git diff --check`・`<<<<<<<` の検索）を書く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-27・CHAT-1019-OUK-28 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-OUK-29` を足す。

目的・決定（2026-10-19、平野さん）

* OUK-27 の足した行（テスト `test_race_stopped.py`・`test_league_pages_compare.py` の強化、比較の文書の項目の表、`branch-operations.md`・CLAUDE.md・`chat-routines.md`・`page-announcement.md` の各行、7ファイルの手直し、#614 の起票）を、OUK-27 のログのとおりマージする。置き場所を指示の表から変えたこと（C・D を chat-routines.md へ、A の許す一覧を大会・リーグごとに）もそのままでよい
* OUK-27 の報告のとおり、2026-10-17 の決定「残りの内部名（`houou_ask.*`・`.mj-houou-*` のクラス・`houou_search` などの id・`generate_houou_pages.py`）は公開後に別 issue でそろえる」が docs/decisions に無かった（チャット側のメモにだけあった）。この決定を docs/decisions/houou.md に書き足す

手順

1. docs/decisions/houou.md の内部名の決定は、OUK-28 で「## 2026-10-19（CHAT-1019-OUK-28）」の節として作業ブランチに足してある。それがあることを確かめる（無ければ同じ内容で足す）
2. 取り込み（必要なら）の後、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。容量の検査（`check-assets.yml` と同じもの）を手元で回し、CLAUDE.md ほか上限のある文書が警告域の手前であることを確かめる
3. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く

止まる条件

* テストが落ちて直せない。容量の検査が警告域・上限に入る
* 取り込みで、末尾への追記どうしと page-announcement.md の上の1か所以外の衝突が出る（解かずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。CHAT-1019-OUK-27・CHAT-1019-OUK-28 のログの状態を「完了（CHAT-1019-OUK-29 でマージ）」の趣旨にする。取り込みで末尾追記の衝突を既定の規則で解いたなら、そのことと並べた順を書く（規則の初めての適用の記録）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-29 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk27
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk27/docs/logs/CHAT-1019-OUK-29.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk27
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f73f403）: https://github.com/retroeater/mj-logs/tree/main/guide/4f73f403

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
