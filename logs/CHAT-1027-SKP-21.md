# CHAT-1027-SKP-21

- 着手日時: 2026-10-27
- 対象issue: #633
- ブランチ: work/1027-skp-vid
- 着手時HEAD: f22bddc20

## 指示

【Claude作成】Claude Code 向け指示：最強戦の告知動画を第3版 v3b で確定し、制作のスクリプトの直しをマージする（CHAT-1027-SKP-18 の続き、#633） Chat-Ref: CHAT-1027-SKP-21 マージ: 承認済み（チャットで。work/1027-skp-vid の、スクリプトと docs だけの差分） 貼る時機: いつでも（CHAT-1027-SKP-18 のセッションに続けて貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1027-skp-vid を続けて使う（CHAT-1027-SKP-18 の判断待ちをマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1027-SKP-18 で作った告知動画の第3版のうち、v3b（場面2〜4を6部門の本番で撮り直した版）で確定した。制作のスクリプトの直しをマージする。X への投稿は平野さんが行う（告知の期間は投稿まで続く）。
決定（2026-10-27、平野さん）

* 告知動画は第3版 v3b（54.97秒、`saikyo-renewal-promo-v3b.mp4`）で確定。v3a は使わない
* スクリプトの直し（work/1027-skp-vid）を cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 差分は `scripts/promo_video/saikyo_renewal/` と docs（docs/notes/page-announcement.md の実例の行・ページの資料の「告知動画」の節など）だけの見込み（要確認）。スクリプトの既定が v3b を作る形になっていること（v3a を作る形が既定なら、v3b を既定に直す）
* 動画はリポジトリに入れない（平野さんの端末に置く）

手順

1. CHAT-1027-SKP-18 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1027-SKP-21` を足す。上の「決定」を docs/decisions/saikyo.md に足し、docs/notes/page-announcement.md の実例の最強戦の行を第3版 v3b に直す
2. origin/cloudflare を取り込み（要れば）、origin/cloudflare との差分がスクリプトと docs だけであることを確かめて `git push origin work/1027-skp-vid:cloudflare` でマージする。#633 に、動画を確定したことをコメントする。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う

止まる条件

* 差分に、スクリプトと docs 以外がある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1027-skp-vid
- ログ: docs/logs/CHAT-1027-SKP-21.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: #633
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ccadb236）: https://github.com/retroeater/mj-logs/tree/main/guide/ccadb236

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccadb236/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8030de19.md
