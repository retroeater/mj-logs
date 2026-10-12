# CHAT-1012-ABM-15

- 着手日時: 2026-10-12
- 対象issue: #425
- ブランチ: work/1012-abm-map
- 着手時HEAD: e1d99ce408c35c65660892fe98595b6b5ecd8ae7

## 指示

【Claude作成】Claude Code 向け指示：#425 ABEMA ブックの【2】＋【3】と生成の切り替え（CHAT-1012-ABM-13）を cloudflare へマージする Chat-Ref: CHAT-1012-ABM-15 マージ: 承認済み（チャットで。2026-10-12 平野さんがプレビュー〈2016 ファイナルトーナメント 決勝・2020 超攻撃型プロ決戦 ベスト8 A卓・2023 最高勝率決戦 ベスト8 A卓・2016 著名人代表決定戦 ベスト8 A卓〉を見て「マージしてよい」とした）。ただし「止まる条件」に当たればマージせずに報告する 貼る時機: CHAT-1012-ABM-14（卓のページの直しのマージ）の完了後。0章で CHAT-1012-ABM-14 のログの `## 報告` の状態が「完了」で、`origin/work/1012-abm-ui` が origin/cloudflare の祖先であることを確かめ、そうでなければ何もせず止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1012-abm-map を続けて使う（CHAT-1012-ABM-13 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。「貼る時機」の前提を確かめる。CHAT-1012-ABM-13 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。ABM-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1012-ABM-15` を足す。

目的
CHAT-1012-ABM-13 で作った ABEMA ブックの【2】（毎朝の作り直し）・【3】と、生成の動画の URL の読み先の切り替え（「最強戦」シートの M 列から【2】＋【3】へ）を本番に入れる。2011〜2025年度の卓のページ・対局のページに ABEMA のサムネイルと「▶ 再生」が出る。新しい変更は加えない。
決定（2026-10-12、平野さん）

* CHAT-1012-ABM-13 の割り当て（卓 603 にサムネイルと「▶ 再生」）でマージする
* 「最強戦」シートの M「動画URL」は、このマージと本番の確かめが済んだ後に平野さんが空にする（列は残す。CHAT-1012-ABM-13 の決定のとおり）

前提（チャット側。平野さんの決定ではない）

* CHAT-1012-ABM-14 で卓のページの直し（各選手の他の対局など）が先に cloudflare に入っている。取り込むと、卓のページ（生成物）と docs/decisions/saikyo.md の末尾が衝突しうる。コードは同じ関数・同じ行を変えていない（CHAT-1012-ABM-13 のログ「手順1」の `git merge-tree` の結果）
* 取り込んで生成し直した後の origin/cloudflare との差分の見込み: 対局のページ 199（CHAT-1012-ABM-13 のログ「手順3」）。卓のページは 590 より増えうる（各選手の他の対局のカードの画像も ABEMA のサムネイルに変わるため）が 643 を超えない。年度ページ・今の公開中のファイル（`saikyo/index.html`・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）・live/ は変わらない
* 毎朝の `regenerate-saikyo.yml` のジョブ `abema` は workflow_run で cloudflare でだけ働く。作業ブランチでは手動実行でしか確かめていない

手順

1. origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直す（ABEMA ブックの【2】は CHAT-1012-ABM-13 で書いたものを読む）。origin/cloudflare との差分を種類に分けて数える（ABEMA のサムネイルの入った卓のページ・対局のページ・シートの変化による差分・文書とコード・ワークフロー）。年度ページ・今の公開中のファイル・live/ に差分が無いこと、2026年度の18卓の動画が今の本番と同じであることを確かめる
2. CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後の check-run（Workers Builds）を確かめる。cloudflare で `regenerate-saikyo.yml` を手動実行（入力 abema=check・regenerate=false）し、cloudflare からもサービスアカウントで ABEMA ブックに書けることを確かめる。本番の `/saikyo/2016/20160521-s1-a.html`・`/saikyo/2023/20230326-s1-a.html`・`/saikyo/2026/20260920-s1-a.html` を `curl` で読み、ABEMA の回へのリンクとサムネイルの URL（`https://image.p-c2-x.abema-tv.com/image/programs/<回のID>/thumb001.png?…`）があり、noindex が付いていることを確かめる
3. 決定（上の2つ）を docs/decisions/saikyo.md に足す（今の内容を読んでから）。#425 にこのログを指すコメントを書く（閉じない）。報告の「判断が必要なこと」に、平野さんへの知らせ「M 列を空にしてよい（本番で 2026年度の18卓が【2】から出ていることを確かめた）」を書く

止まる条件（当たればマージしない）

* テストが通らない
* 卓のページが 643 を超えて変わる、対局のページが 199 から ±10 を超えてずれる、年度ページ・今の公開中のファイル・live/ に差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 2026年度の18卓の動画が今の本番と違う、または【2】が読めず動画のある卓が減る
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり生成し直して解く。コード（`scripts/`）が衝突したら解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・手動実行の失敗は、今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-15 を書く

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

ガイド文書（この版を写した時点の最新、mj d627615c）: https://github.com/retroeater/mj-logs/tree/main/guide/d627615c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d627615c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
