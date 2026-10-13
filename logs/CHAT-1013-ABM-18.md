# CHAT-1013-ABM-18

- 着手日時: 2026-10-13
- 対象issue: #425
- ブランチ: work/1013-abm-cast
- 着手時HEAD: 351c7c7e635bb234ad3c0cf3e6256c0b1fe7ebc6

## 指示

【Claude作成】Claude Code 向け指示：#425 卓のページの司会・実況・解説（CHAT-1013-ABM-17）を cloudflare へマージする Chat-Ref: CHAT-1013-ABM-18 マージ: 承認済み（チャットで。2026-10-13 平野さんがプレビュー〈2026 超頭脳合戦 ベスト8 A卓・2023 最高勝率決戦 ベスト8 A卓・2011 鉄人プロ代表決定戦 ベスト8 A卓〉を見て「マージしてよい」とした）。ただし「止まる条件」に当たればマージせずに報告する 貼る時機: いつでも（平野さんは 2026-10-13 に、CHAT-1013-ABM-17 の未登録の名前8つを登録済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1013-abm-cast を続けて使う（CHAT-1013-ABM-17 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-ABM-17 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。ABM-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1013-ABM-18` を足す。

目的
CHAT-1013-ABM-17 で作った卓のページの節「司会・実況・解説」を本番に入れる。新しい変更は加えない。
決定（2026-10-13、平野さん）

* 卓のページの司会・実況・解説は CHAT-1013-ABM-17 の作りでよい（同じ人が司会と実況を兼ねるときはカード1枚・帯「司会・実況」を含む）
* CHAT-1013-ABM-17 の未登録の名前8つ（小山剛志・梶本琢程・篠原みなみ・高見奈央・竹内幸輔・梶本琢・独歩・桜井章一）は、平野さんがシートに登録した（どのタブにどう登録したかは平野さんの申告に無い。生成と同じ経路で読んで確かめる）

前提（チャット側。平野さんの決定ではない）

* 登録により、卓のページの司会・実況・解説のカードの一部が代替アバターから写真に変わる見込み。変わるのは節のある卓のページ 596 の中だけ
* 「連盟プロ以外」「別名」への登録は、最強戦のほかのページや /live・title/ の生成に影響しうる。この指示で生成し直すのは `saikyo_pages` だけで、ほかは毎日の再生成に任せる

手順

1. origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直す。司会・実況・解説の名前のうち未登録のまま残る名前の数と一覧（出る卓の数つき）を書く（見込み 0。残れば一覧を書いて止まらずに進める）。origin/cloudflare との差分を種類に分けて数える（節を足した卓のページ・シートの変化による差分・文書とコード）。対局のページ・年度ページ・今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・live/ に差分が無いことを確かめる
2. CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後の check-run を確かめる（Workers Builds が GitHub に出ない・待ちのままなら、そう書いて進めてよい）。本番の `/saikyo/2011/20110625-s1-a.html`・`/saikyo/2023/20230326-s1-a.html` を `curl` で読み、節「司会・実況・解説」と帯があり noindex が付いていることを確かめる（本番がまだ前の版なら、そう書いて進めてよい）
3. 決定を docs/decisions/saikyo.md に足す（今の内容を読んでから）。#425 にこのログを指すコメントを書く（閉じない）

止まる条件（当たればマージしない）

* テストが通らない
* 卓のページが 596 より少ない、または +10 を超えて多く変わる。対局のページ・年度ページ・今の公開中のファイル・live/ に差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり生成し直して解く。コード（`scripts/`）が衝突したら解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ABM-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ABM-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

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

ガイド文書（この版を写した時点の最新、mj 5bd6c739）: https://github.com/retroeater/mj-logs/tree/main/guide/5bd6c739

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bd6c739/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2c5fedab.md
