# CHAT-1019-OUK-28

- 着手日時: 2026-10-19
- 対象issue: #614（決定の出典の追記）
- ブランチ: work/1019-ouk27（CHAT-1019-OUK-27 の続き）
- 着手時HEAD: cef56c5ef

## 指示

【Claude作成】Claude Code 向け指示：OUK 後半の申送り（work/1019-ouk27）を cloudflare にマージし、内部名をそろえる決定を docs/decisions に書き足す Chat-Ref: CHAT-1019-OUK-28 マージ: 下の確かめが通れば cloudflare にマージする（2026-10-19、平野さんが OUK-27 の足した行を確かめ、マージを許可した） 貼る時機: いつでも（CHAT-1019-OUK-27 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk27 の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-ouk27 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない。`docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしの衝突は、OUK-27 で足した規則のとおり両方を残して解く。ほかが衝突したら解かずに止まる）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1019-OUK-27 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-OUK-28` を足す。

目的・決定（2026-10-19、平野さん）

* OUK-27 の足した行（テスト `test_race_stopped.py`・`test_league_pages_compare.py` の強化、比較の文書の項目の表、`branch-operations.md`・CLAUDE.md・`chat-routines.md`・`page-announcement.md` の各行、7ファイルの手直し、#614 の起票）を、OUK-27 のログのとおりマージする。置き場所を指示の表から変えたこと（C・D を chat-routines.md へ、A の許す一覧を大会・リーグごとに）もそのままでよい
* OUK-27 の報告のとおり、2026-10-17 の決定「残りの内部名（`houou_ask.*`・`.mj-houou-*` のクラス・`houou_search` などの id・`generate_houou_pages.py`）は公開後に別 issue でそろえる」が docs/decisions に無かった（チャット側のメモにだけあった）。この決定を docs/decisions/houou.md に書き足す

手順

1. docs/decisions/houou.md の末尾に「## 2026-10-19（CHAT-1019-OUK-28）」の節を足し、上の決定を1行で書く（決めた日は 2026-10-17〈CHAT-1017-OUK-01 の後の仮置きの確かめ、平野さん〉、起票は #614〈CHAT-1019-OUK-27〉と書く）
2. 取り込み（必要なら）の後、`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。容量の検査（`check-assets.yml` と同じもの）を手元で回し、CLAUDE.md ほか上限のある文書が警告域の手前であることを確かめる
3. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。Workers Builds・check-assets の成否を書く

止まる条件

* テストが落ちて直せない。容量の検査が警告域・上限に入る
* 取り込みで、末尾への追記どうし以外の衝突が出る（解かずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。CHAT-1019-OUK-27 のログの状態を「完了（CHAT-1019-OUK-28 でマージ）」の趣旨にする。取り込みで末尾追記の衝突を既定の規則で解いたなら、そのことと並べた順を書く（規則の初めての適用の記録）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-28 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk27
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk27/docs/logs/CHAT-1019-OUK-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk27
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce06729b）: https://github.com/retroeater/mj-logs/tree/main/guide/ce06729b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce06729b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
