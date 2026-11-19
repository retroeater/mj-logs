# CHAT-1120-LVP-29

- 着手日時: 2026-11-20
- 対象issue: #475
- ブランチ: work/1119-lvp
- 着手時HEAD: 9b00c2cd1

## 指示

【Claude作成】Claude Code 向け指示：所属団体「WRPM」の変更（work/1119-lvp）を cloudflare へマージし、再生成して世界選手権 第4回の予選のページを出す Chat-Ref: CHAT-1120-LVP-29 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1119-lvp（CHAT-1119-LVP-28 の WRPM の変更 0e9bfd2f7 がある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1119-LVP-28 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1120-LVP-29` を足す。

目的
CHAT-1119-LVP-28 の判断待ちの3点を、平野さんの答えのとおりに片付け、世界選手権 第4回の予選のページを本番に出す。
決定（2026-11-20、平野さん。番号は LVP-28 の報告の番号）

1. A: 第4回の予選は【3】で載せる。平野さんが予選10行（行4377〜4379・4381・4383・4387・4389・4392〜4394）の掲載に Y を入れた（2026-11-20）。自動の規則は直さない（ほかの大会の予選の扱いは今のまま）
2. WRPM の変更（0e9bfd2f7）をマージしてよい
3. WRPM の人の所属の確かめ方は、#354（所属団体の表示、保留）に着手するときに決める。今は Gemma Sakamoto 1人で、平野さんの確認で足りる

前提（チャット側。平野さんの決定ではない）

* 予選10行の動画ID は LVP-28 の `## 経過` にある（照合は行番号でなく動画ID で行う）
* 予選のページに出る対局者のうち、Gemma Sakamoto は WRPM の変更が入って初めて名前が引ける（入らなければ未登録の名前として飛ぶ）

手順

1. #475 に着手中のコメントを書く。【3】を読み、予選10行の掲載が Y になっていることを動画ID で確かめる。origin/cloudflare を取り込み、`--check live_pages` を回し、`live/wrc/4/q.html` が作られることと Gemma Sakamoto が名前として出ることを確かめる
2. 全ページを再生成し、生成物の差分を書く（第4回の予選のページと、それに伴う一覧・件数・pro/ などのほかに差分が無いこと）。テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages pro_pages title_pages jpml_pros` で手動実行して、Workers Builds の check-run の結果を確かめる。#475 に Gemma Sakamoto が消えた旨をコメントする。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 予選10行のうち、掲載が Y でない行がある（その行を書いて、マージせずに止まる。直すのは平野さん）
* 生成物の差分が、第4回の予選のページとそれに伴う一覧・件数・pro/ のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/names.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #475 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1120-LVP-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1120-LVP-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13ebaa17）: https://github.com/retroeater/mj-logs/tree/main/guide/13ebaa17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
