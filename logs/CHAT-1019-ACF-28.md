# CHAT-1019-ACF-28

- 着手日時: 2026-10-19
- 対象issue: #513・#610
- ブランチ: work/1019-acf-old2
- 着手時HEAD: 0122413eefeacea52e5f7fe959601d3df1e0162b

## 指示

【Claude作成】Claude Code 向け指示：残した旧い作業ログ23件の参照を直し、仕分け・書き写しのうえ削除する（CHAT-1019-ACF-27 の続き）
Chat-Ref: CHAT-1019-ACF-28
マージ: 承認済み（チャットで。止まる条件つき。変わるのは docs/logs の削除、参照の書き換え〈docs/notes・docs/gsc・scripts のコメントと文字列〉、docs/decisions だけ）
貼る時機: いつでも（CHAT-1019-ACF-27 は判断待ちで、削除の 121件は cloudflare 8593ce348 に入っている）。ただし JST 03:45〜05:15 と、月曜 06:00〜09:00 は cloudflare へ push しない
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-acf-old2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-acf-old2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1019-ACF-27 のログの `## 報告` を読み、状態が「判断待ち」で、マージが「済（8593ce348…）」でなければ何もせず止まる。CHAT-1019-ACF-27 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-ACF-28` を足す（`## 指示` 欄は変えない）。

## 目的
CHAT-1019-ACF-27 で、`docs/logs/`・`docs/decisions/` 以外のファイルが名指ししていたため残した旧い作業ログ23件（ACF-27 のログ `## 経過`「手順1: 対象の一覧」の「外したログと名指ししている所」の表）を、参照を直したうえで ACF-27 と同じやり方で仕分け・書き写し・削除する。

### 決定（2026-10-19、平野さん）
- 残した23件は、参照を直してから削除する。ACF-27 の判断待ちの案のとおり、出典としてだけ Chat-Ref を書いている所はそのままにして削除し、ログを読む先として指している所は削除前の版（permalink）に書き換えてから削除する
- 仕分け・書き写しは ACF-27 の決定（2026-10-19）と同じ: 片付いた論点は写さない。未済は関係する open の issue へコメントし、無いものは #610 に行を足す

### 前提（チャット側。平野さんの決定ではない）
- 「出典」と「読む先」の分け方（チャット側の案）: 文中の Chat-Ref が「この値・規則は CHAT-… で決めた」の意味で、読まなくても文が通るなら出典。「CHAT-… のログを見よ」「詳細は CHAT-… のログ」のように中身を読ませるなら読む先。ACF-27 の見立てでは、読む先は `docs/notes/handover-archive-2026.md:966`（INV-01・INV-02・INV-04）・`docs/notes/saikyo-page-design.md:649`（PHT-06）など一部（要確認。23件すべてを1か所ずつ判定する）。`docs/gsc/` の README などの名指しも同じ基準で判定する
- permalink の SHA は、削除のコミットの親になる cloudflare のコミットにする（ACF-27 の 4db85e095 でもログの中身は同じはずだが、その後に変わっていないかを確かめ、変わっていれば新しい親を使う）
- テスト（`scripts/tests/test_cleanup_logs.py`・`test_yotei.py`・`test_dojo_guest.py`・`test_sheets_write.py`・`test_live_calendar.py`・`test_live_layer3.py`）の名指しは、文字列やコメントとして書かれているだけで実ファイルを読まない見込み（要確認）。実ファイルを読むテストがあれば、テストの作りを変えずに止まる
- `scripts/promo_video/title/` のコメントの名指し（VID-03・05・06）は出典の見込み
- 書き換えるのは名指しの行だけ。コードの動き・文書の中身は変えない。CLAUDE.md・handover.md・chat-side-operations.md には触れない見込み（触れるならその3文書には出典としての Chat-Ref を書かない規則を守る）

## 手順
1. 23件について、名指ししている所ごとに「出典」か「読む先」かを判定し、`## 経過` に表で書く（列: ログ・名指ししている所・判定・直し方）。あわせて ACF-27 と同じ (a)〜(c) の仕分けを全件の表で書く（略さない）
2. 「読む先」の所を permalink に書き換え、テスト（`python3 -m unittest discover -s scripts/tests`）が通ることを確かめる。(b) は issue ごとに1コメント（本文・状態・ラベルは変えない）、(c) は #610 にコメントで行を足す（#610 の本文は変えない）
3. 23件を1つのコミットで削除し（書き換えとは別のコミット）、cloudflare へマージする（push 直前に再 fetch と祖先の確認。コミットメッセージに閉じるキーワードと `#番号` を続けて書かない）。マージ後にテストをもう一度回し、mj-logs から写しが消えたかを数件確かめる。#513 に「旧いログ N件を追加で削除した（2026-10-19、10/26 の (3) で差し引く数。ACF-27 の 121件と合わせて M件）」とコメントする。ACF-27 の決定は docs/decisions/operations.md にあるので、今回の決定を同じ所に足す

## 止まる条件
- 0章の確かめが通らない
- 23件の名前・名指しの所が ACF-27 の表と食い違う（食い違いを書いて止まる）
- テストが実際のログのファイルを読んでいて、削除で落ちる（テストを直さずに止まる）
- 名指しの所が「出典」か「読む先」か決められないものがある（そのログだけ外して進め、外したものを「判断が必要なこと」に書く。全部が当たるときだけ止まる）
- 未マージの work/ ブランチが、書き換える行と同じ行を変えている、または取り込みで衝突する（そのログだけ外して進め、全部が当たるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ACF-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ACF-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1019-ACF-28` のコミット無し。雛形の行は揃っている。着手は 11:06 JST
- 作業ブランチ: ローカル・リモートとも `work/1019-acf-old2` は無く、`git checkout -b work/1019-acf-old2 origin/cloudflare`（0122413ee）で作った
- 0. 指示欄の末尾は指示文の最後の行と一致。origin/cloudflare の CHAT-1019-ACF-27 の `## 報告` は状態「判断待ち」・マージ「済（8593ce348。…）」で、確かめは通った。状態の末尾に ` / 続き: CHAT-1019-ACF-28` を足した（`## 指示` 欄が変わっていないことを確かめ、`cleanup_logs.continuation_of` が CHAT-1019-ACF-28 を返すことを確かめた）
- 23件の名前と名指しの所: 今の `git grep`（`docs/logs`・`docs/decisions` を除く）は23件・44か所で、ACF-27 の表と名前・所（ファイルと行番号）とも一致した
- テスト: 名指しのあるテストは文字列・コメントだけで、実際のログのファイルを読むテストは無い（`docs/logs` を扱う test_chat_ids・test_sync_*・test_cleanup_logs は一時のリポジトリに作ったファイルを使う）
- 未マージの `work/*` で書き換えるファイルを変えているのは `work/1017-lvp`（docs/notes/live-page-design.md の 400行あたりに節を足す）だけで、書き換える100行とは別の所。`git merge-tree` の衝突は書き換えの前後で同じ（1017-lvp と cloudflare の間の既存の衝突だけ）で、書き換えで増えていない

### 手順1: 名指しの判定

判定の基準は指示の前提のとおり（読まなくても文が通るなら出典、中身を読ませるなら読む先）。決められないものは無かった。

| ログ | 名指ししている所 | 判定 | 直し方 |
| --- | --- | --- | --- |
| CHAT-0930-BNG-05 | `docs/notes/handover-archive-2026.md:853` | 出典（「規則の出典」） | そのまま |
| CHAT-0930-CAL-15 | `docs/notes/handover-archive-2026.md:858` | 出典（規則の実例。事例の中身は文中にある） | そのまま |
| CHAT-0930-CAL-15 | `docs/notes/live-channel-write.md:76` | 出典（値の決まりの出典） | そのまま |
| CHAT-0930-CAL-15 | `scripts/lib/sheets_write.py:20` | 出典（コードのコメントの出典） | そのまま |
| CHAT-0930-CAL-15 | `scripts/tests/test_sheets_write.py:244` | 出典（テストの説明の出典） | そのまま |
| CHAT-0930-OLT-01 | `docs/gsc/2026-10-01/jpml-titles-landing.md:16` | 出典（基準値の出どころ） | そのまま |
| CHAT-1002-CLD-02 | `docs/notes/yotei-sheet.md:90` | 出典（規則の出典） | そのまま |
| CHAT-1002-CLD-02 | `docs/notes/yotei-sheet.md:131` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-CLD-02 | `scripts/lib/yotei.py:181` | 出典（コードのコメントの出典） | そのまま |
| CHAT-1002-CLD-02 | `scripts/lib/yotei.py:214` | 出典（コードのコメントの出典） | そのまま |
| CHAT-1002-CLD-02 | `scripts/tests/test_cleanup_logs.py:240` | 出典（テストの入力の文字列（ファイルは読まない）） | そのまま |
| CHAT-1002-CLD-02 | `scripts/tests/test_yotei.py:136` | 出典（テストの説明の出典） | そのまま |
| CHAT-1002-CLD-03 | `docs/notes/yotei-sheet.md:131` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-CLD-04 | `docs/notes/handover-archive-2026.md:654` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-CLD-05 | `docs/notes/handover-archive-2026.md:656` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-CLD-06 | `docs/notes/yotei-sheet.md:112` | 出典（規則の出典） | そのまま |
| CHAT-1002-CLD-06 | `scripts/lib/live_calendar.py:126` | 出典（コードのコメントの出典） | そのまま |
| CHAT-1002-CLD-06 | `scripts/lib/live_layer3.py:34` | 出典（コードのコメントの出典） | そのまま |
| CHAT-1002-CLD-06 | `scripts/tests/test_live_calendar.py:114` | 出典（テストの説明の出典） | そのまま |
| CHAT-1002-CLD-06 | `scripts/tests/test_live_layer3.py:74` | 出典（テストのコメントの出典） | そのまま |
| CHAT-1002-CLD-07 | `docs/notes/handover-archive-2026.md:687` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-CLD-07 | `docs/notes/handover-archive-2026.md:704` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1002-DOJ-01 | `scripts/tests/test_dojo_guest.py:27` | 出典（テストのデータの出どころ（データはテストの中）） | そのまま |
| CHAT-1002-DOJ-01 | `scripts/tests/test_dojo_guest.py:384` | 出典（テストのデータの出どころ（データはテストの中）） | そのまま |
| CHAT-1002-DOJ-03 | `scripts/tests/test_dojo_guest.py:424` | 出典（テストの説明の出典） | そのまま |
| CHAT-1002-INV-01 | `docs/notes/handover-archive-2026.md:966` | 読む先（「経過は作業ログ …」で中身を読ませる） | permalink に書き換え（c33418c98） |
| CHAT-1002-INV-02 | `docs/notes/handover-archive-2026.md:966` | 読む先（同上） | permalink に書き換え（c33418c98） |
| CHAT-1003-INV-04 | `docs/notes/handover-archive-2026.md:854` | 出典（規則の実例。事例の中身は文中にある） | そのまま |
| CHAT-1003-INV-04 | `docs/notes/handover-archive-2026.md:966` | 読む先（同上（966行）） | permalink に書き換え（c33418c98） |
| CHAT-1003-RUN-01 | `docs/gsc/2026-10-03/README.md:8` | 出典（作業したセッションの名前） | そのまま |
| CHAT-1004-VID-03 | `scripts/promo_video/title/build.sh:2` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-03 | `scripts/promo_video/title/capture.mjs:1` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-03 | `scripts/promo_video/title/composition/index.html:2` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-03 | `scripts/promo_video/title/composition/index.html:44` | 出典（値の出どころ） | そのまま |
| CHAT-1004-VID-03 | `scripts/promo_video/title/setup.sh:2` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-05 | `scripts/promo_video/title/music.py:1` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-06 | `scripts/promo_video/title/capture.mjs:3` | 出典（コメントの出典） | そのまま |
| CHAT-1004-VID-06 | `scripts/promo_video/title/compose.py:1` | 出典（コメントの出典） | そのまま |
| CHAT-1005-RVW-07 | `docs/notes/handover-archive-2026.md:657` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1005-RVW-10 | `docs/notes/handover-archive-2026.md:708` | 出典（事例の中身は文中にある） | そのまま |
| CHAT-1005-UNR-10 | `docs/notes/live-page-design.md:100` | 読む先（「手順の実例は … のログ」で中身を読ませる） | permalink に書き換え（c33418c98） |
| CHAT-1006-LGR-05 | `scripts/tests/test_cleanup_logs.py:235` | 出典（テストの入力の文字列（ファイルは読まない）） | そのまま |
| CHAT-1006-LGR-05 | `scripts/tests/test_cleanup_logs.py:236` | 出典（同上） | そのまま |
| CHAT-1006-PHT-06 | `docs/notes/saikyo-page-design.md:649` | 読む先（「調査は …」で調べた中身を読ませる） | permalink に書き換え（c33418c98） |

- 書き換え（c33418c98、名指しの行だけ）: `docs/notes/handover-archive-2026.md:966`・`docs/notes/live-page-design.md:100`・`docs/notes/saikyo-page-design.md:649`。同じ行にある削除済みのログ（CHAT-1003-INV-03〈2134dfa97 で削除〉・CHAT-1005-UNR-11・UNR-12〈8593ce348〉・CHAT-1007-PHT-09〈f0204bea8〉）も、それぞれの削除の直前の版の permalink にした（「UNR-10〜12」は3つのリンクに分けた）
- permalink の SHA: 指示の「削除のコミットの親」は、書き換えのコミットが削除のコミットより前にあるため、書き換えの中には書けない（親は書き換えの後のコミットになる）。代わりに、書き換えの前の cloudflare の 0122413ee を使った。23件の中身は 4db85e095（ACF-27 の親）から 0122413ee まで変わっていない（`git diff --stat` が空）。削除の直前にも変わっていないことを確かめる（下）
- 全リンクの先が、その SHA にファイルとしてあることを `git cat-file -e` で確かめた（9件）。テスト `python3 -m unittest discover -s scripts/tests` は 1427件 OK

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

ガイド文書（この版を写した時点の最新、mj 0122413e）: https://github.com/retroeater/mj-logs/tree/main/guide/0122413e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0122413e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8593ce34.md
