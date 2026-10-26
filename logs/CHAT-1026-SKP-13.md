# CHAT-1026-SKP-13

- 着手日時: 2026-10-26
- 対象issue: #627・#623
- ブランチ: work/1026-skp-pill（ピルの直し）・work/1024-skp-vid（動画）
- 着手時HEAD: bdf27d95b（work/1026-skp-pill）

## 指示

【Claude作成】Claude Code 向け指示：最強戦の全ページでファイナルの決勝の通過のピルを「最強位」にそろえてマージし、告知動画の2場面を撮り直して送る（#627・#623） Chat-Ref: CHAT-1026-SKP-13 マージ: 承認済み（チャットで。ピルの直しの分だけ。条件は「止まる条件」）。告知の期間中の変更を承認（2026-10-26、平野さん）。動画の制作のスクリプト（work/1024-skp-vid）は判断待ちのまま、マージしない 貼る時機: いつでも（CHAT-1025-SKP-12 のセッションに続けて貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-skp-pill と work/1024-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。ピルの直しは work/1026-skp-pill を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-skp-pill origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。動画は未マージの work/1024-skp-vid を続けて使う（CHAT-1025-SKP-12 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1025-SKP-12 の告知動画の初版では、対戦成績の対局の記録と 2025年度のページに、ファイナルの決勝の通過が「優勝」のピルで映った（CHAT-1023-SKP-02 の決定24で、公開のときに「最強位」へそろえると決めていた分）。そのそろえを先に入れてマージし、動画のその2場面を撮り直して送る。
決定（2026-10-26、平野さん）

* CHAT-1023-SKP-02 の決定24のうち、公開のときに入れる予定だった分（公開済みの年度・対局・卓のページと対戦成績のピル）を、今入れる。ファイナルの決勝の通過（最強位の判定と同じ: 「ファイナルトーナメント」× 決勝 × 通過）のピルを「優勝」から「最強位」にする。予選の決勝の通過は「優勝」のまま
* 告知の期間中に、この直しを本番へマージしてよい（差分がピルの文字だけであることが条件）
* 直した後、告知動画のその2場面（対戦成績・年度別）を撮り直して送る

前提（チャット側。平野さんの決定ではない）

* 変わる見込み: 最強位の15卓（2011〜2025年度）が出るページのピルの文字（年度のページ・対局のページ・卓のページ・対戦成績の対局の記録・今のトップの検索の結果）。卓のページの写真の帯の「優勝」（CHAT-1016-SKR-14 の決勝は「優勝」）も同じ判定なら「最強位」にする（要確認。帯が別の判定なら変えずに書く）
* 対戦成績は HTH の系列から SKP が引き継いだ（2026-10-23）。#623 は公開のときに閉じる
* 平野さんは初版の動画をまだ見ていない。ピル以外の直しは、撮り直した版を見てから決める

手順

1. CHAT-1025-SKP-12 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1026-SKP-13` を足す。上の「決定」を docs/decisions/saikyo.md に足す（SKP-02 の決定24の「公開のときに直す」を置き換えることを書く）。#627 のコメントの「公開の指示に入れる項目」から、この分を済みにする
2. work/1026-skp-pill でピルを直し、テストを足して `python3 -m unittest discover -s scripts/tests` を流す。生成し直し、origin/cloudflare との差分を種類ごとに数え、変わった「優勝」→「最強位」の箇所をページごとの数で表にする。`git push origin work/1026-skp-pill:cloudflare` でマージし、本番で 2025年度のページと桑田憲汰の卓のページ（2025 決勝）・対戦成績 `?name=桑田憲汰,近藤誠一` に「最強位」が出ることを確かめる（15分で本番が古いままなら「未確認の項目」に回す）
3. work/1024-skp-vid に戻り、動画の場面5（対戦成績）と場面6（年度別）を、マージ後の本番から撮り直して作り直す（ほかの場面は初版のまま）。長さ・各場面の秒の実際を書き、撮った後にコマで「最強位」のピルが映ることを確かめる。mp4 を `SendUserFile` で送り、スクリプトを push する。判断待ちで止まる

止まる条件

* 差分に、ピル（と、同じ判定の帯）の文字の変更とシートの変化で説明できるもの以外がある（種類と件数を書いて止まる）
* 「最強位」に変わった卓の数が15でない（どの卓かを書いて止まる）
* テストが通らない
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の最強戦の節の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-SKP-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-SKP-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1026-skp-pill
- ログ: docs/logs/CHAT-1026-SKP-13.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d1ef6e5）: https://github.com/retroeater/mj-logs/tree/main/guide/0d1ef6e5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdf27d95.md
