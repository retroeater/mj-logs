# CHAT-1027-SKP-18

- 着手日時: 2026-10-27
- 対象issue: #633
- ブランチ: work/1027-skp-vid
- 着手時HEAD: cd2e819d3

## 指示

【Claude作成】Claude Code 向け指示：最強戦の告知動画を6部門のランキングで撮り直して送る（場面3・4、#633） Chat-Ref: CHAT-1027-SKP-18 マージ: 判断待ちで止まる（動画の確定までスクリプトの直しはマージしない。docs/notes/page-announcement.md「進め方」） 貼る時機: いつでも（CHAT-1027-SKP-17 のセッションに続けて貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1027-skp-vid を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-skp-vid origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1027-SKP-17 のログの `## 報告` が「完了」で、マージ済みであることを確かめ、そうでなければ何もせずに止まる。

目的
CHAT-1026-SKP-13 で確定した告知動画（第2版、54.97秒）は、ランキングが9部門のときに撮った。SKP-17 でランキングを6部門に変えたので、ランキングが映る場面を本番から撮り直して送る。X への投稿は、平野さんが新しい版を確定した後（告知の期間は投稿まで続く）。
決定（2026-10-27、平野さん）

* ランキングは6部門（最強位・ファイナル・勝率／出場数・連続出場・対局数）。告知動画はこの後に撮り直す
* 動画の構成・見本の選手（桑田憲汰）・対戦成績の2人（桑田憲汰・近藤誠一）・曲は、CHAT-1025-SKP-12 の決定のまま

前提（チャット側。平野さんの決定ではない）

* 撮り直すのは、ランキングが映る場面だけ: 場面3（個人成績。部門別のタブが6行になった）と場面4（ランキング）。場面4は「最強位」→「勝率」に切り替える（前は「通過率」）。場面4のテロップ「ランキング9部門」を「ランキング6部門」に直す。ほかの場面（1・2・5・6・7）は第2版のまま
* 制作のスクリプトは `scripts/promo_video/saikyo_renewal/`（SKP-14 でマージ済み）。直しは作業ブランチに置き、確定までマージしない
* 撮り方・確かめ方は docs/notes/page-announcement.md「告知動画の型」と SKP-12・13 のとおり（上位が映る位置で止める、テロップが名指すものが映る、白いままのコマが無い）

手順

1. CHAT-1026-SKP-13 で確定した版との違いを、撮る前に本番で確かめる（部門別のタブの行・ランキングの格子・勝率の表示「.xxx」）
2. 場面3・4を本番から撮り直し、テロップを直して作り直す。長さと各場面の秒を表にし、撮った後にコマで確かめる
3. mp4 を `SendUserFile` で送り、スクリプトの直しを作業ブランチに push する。判断待ちで止まる（確定は次の指示）

止まる条件

* 本番のランキング・部門別のタブが SKP-17 の決定のとおりでない
* 未マージの work/ ブランチが `scripts/promo_video/saikyo_renewal/` の同じファイルを変えている、または取り込みで衝突する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-SKP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-SKP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1027-skp-vid
- ログ: docs/logs/CHAT-1027-SKP-18.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: #633
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cd2e819d）: https://github.com/retroeater/mj-logs/tree/main/guide/cd2e819d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd2e819d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/954de2fa.md
