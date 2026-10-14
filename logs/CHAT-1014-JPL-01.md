# CHAT-1014-JPL-01

- 着手日時: 2026-10-14
- 対象issue: #488
- ブランチ: work/1014-jpl
- 着手時HEAD: 59e33bdf0

## 指示

【Claude作成】Claude Code 向け指示：第1期「JPMLリーグ」を yotei.EVENTS に足す（#488） Chat-Ref: CHAT-1014-JPL-01 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらないこと（テストがすべて通り、変わるのが scripts/lib/yotei.py・scripts/tests/test_yotei.py・docs/（decisions・notes・logs）だけ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#488 が Open で、他セッションの着手中コメントが無いことを確かめる（Closed なら何もせず止まる）。着手中コメントを残す。

目的
連盟の公式予定表で大会名の「(仮)」が外れたので、2026-10-06 の決定（docs/decisions/features.md「第1期 JPMLリーグの追加〈#497・#488〉は…『(仮)』が外れてから行う」、broadcast-calendar.md「正式な大会名が決まってから yotei.EVENTS に足す〈#488〉」）に従い、「JPMLリーグ」を `scripts/lib/yotei.py` の `EVENTS` に足して #488 をクローズする。
決定（2026-10-14、平野さん）

* #488 のマージは承認する（テストが通り、変わるのが yotei.py・テスト・docs だけなら）
* 「タイトル戦」タブへの行の追加（#497）は平野さんが手で行う。#497 はこの指示では扱わない（別の指示 CHAT-1014-JPL-02）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-14 に、連盟サイト「年間行事予定」が埋め込む予定表（`yotei.CALENDAR_ID` のものと同じはず。要確認）を Google カレンダーの連携で読んだ。件名は「第1期JPMLリーグ第1節」〜「第1期JPMLリーグ第20節」（2026-10-06〜2027-01-21）、「第1期JPMLリーグトーナメント」（2027-02-06）、「第1期JPMLリーグベスト16AB卓」「…ベスト16CD卓」「…ベスト8AB卓」「…決勝」（2027-03）、「第2期JPMLリーグ第1節」（2027-03-01）。いずれも「(仮)」は無く、件名の更新は 2026-10-13 14:09〜14:15 JST。「JPMLリーグ 仮」の検索は0件
* 予定表には「第20期JPML WRCリーグ…」「第8期JPML WRC-Rリーグ…」もある。足し方によってはこれらが JPMLリーグ に誤って当たる・JPMLリーグ がこれらに当たるおそれがある（#488 の本文の「並び順に注意」の件、要確認）
* `EVENTS` は予定表の【2】の「大会」列と、YouTube の枠との結び付け（`event_of()`）に使われている（docs/notes/yotei-sheet.md。要確認）。YouTube の JPMLリーグ の放送の題名の書き方も実物で確かめる

手順

1. 確かめる: #488 の本文・コメント、`scripts/lib/yotei.py` の `EVENTS` と `event_of()` の当て方（順・部分一致か）、`scripts/tests/test_yotei.py`。層1・予定表【1】の今のデータで「JPMLリーグ」を含む件名・題名を洗い出し（予定表とYouTubeそれぞれ、件数と例）、上の前提と食い違えばログに書く（件名の書き方が前提と違うだけなら止まらず、実物に合わせる）
2. 足す: `EVENTS` に JPMLリーグ を足す（名前・位置は #488 と既存の書き方に合わせる）。テストに、第1期JPMLリーグの節・トーナメント・ベスト16・決勝・第2期が JPMLリーグ に当たること、JPML WRCリーグ・JPML WRC-Rリーグ の件名が今までどおりの大会に当たることを足す。テストをすべて走らせる。手元のデータで `event_of()` を足す前と後で比べ、大会が変わる件名・題名を全件（前→後）ログに書く。JPMLリーグ 以外が変わるなら止まる
3. マージする（CLAUDE.md「ブランチ運用」）。マージで動く自動処理（ワークフロー）と、次の毎朝の取り込み・カレンダー同期で変わる見込み（【2】の「大会」列、mj_放送対局 の予定）を報告に書く。#488 に結果（コメントの末尾に Chat-Ref の行）を書いてクローズする。docs/decisions/broadcast-calendar.md に1節足す（上の「決定」と、2026-10-13 に「(仮)」が外れたことをチャット側が確かめた旨）。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で #488 が Closed、または他セッションの着手中コメントがある
* `EVENTS` を足すと JPMLリーグ 以外の件名・題名の大会が変わる、またはテストが通らない
* scripts/lib/yotei.py・scripts/tests/test_yotei.py・docs/ 以外を変える必要が出た
* 未マージの work/ ブランチが yotei.py の `EVENTS`・`event_of()` かテストの同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` で確かめる。同じファイルの別の箇所なら止まらずログに書く）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL:
- マージ:
- issue: #488
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 76487b6d）: https://github.com/retroeater/mj-logs/tree/main/guide/76487b6d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
