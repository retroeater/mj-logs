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

### 手順1: 仕分け

- ACF-27 と同じく、読むだけのサブエージェント2つ（12件・11件）で1本ずつ読んで仕分け、結果を受け取った
- 件数: **a 21件・b 2件（#390〈CHAT-1002-DOJ-03〉・#496〈CHAT-1002-INV-02〉）・c 0件**（#610 への追記なし）
- 判断が分かれうる1本: CHAT-1002-CLD-03 の未確認「v8I76nBJHyc が『直す』になった理由」は理由そのものは分かっていないが、続く CLD-04 で説明欄を調べ、CLD-06 で予定を直してマージ済み・#448 も closed のため a にした

| ログ | 状態 | 未済事項の要約 | 区分 | 根拠または書き写し先 |
| --- | --- | --- | --- | --- |
| CHAT-0930-BNG-05 | 完了（docs のみ、マージ済み） | なし | a | 任意とされた BNG-01〜04 の決定の遡り追記は docs/decisions/seo-bing.md に BNG-02〜04 の節として入っている。#126 は closed。未確認の項目・エラーは「なし」 |
| CHAT-0930-CAL-15 | 完了（97d0b4f5 でマージ済み） | なし | a | #479 は closed。未確認だったマージ後の毎日の実行は、その後も run-daily.yml（毎朝の運行）が 10-18 まで success で続いている（グリッドは超えると自動で足す実装）。判断が必要なことは事後報告のみ |
| CHAT-0930-OLT-01 | 完了（調査のみ、ログをマージ済み） | なし | a | 廃止の論点は docs/decisions/title.md「旧表 jpml_titles.html は廃止する。/title/ へ301で転送し…?name= も受け取る（案 c2）」で決定。今のコードは _redirects に `/jpml_titles.html /title/ 301` があり旧ファイルは削除済み。#441・#269 は closed |
| CHAT-1002-CLD-02 | 完了（案(1)で32件削除済み。続き CHAT-1002-CLD-03） | なし | a | 32件の削除は状態欄のとおり 10-03 の同期で済み。10-08 鳳匠戦ベスト16 C卓・D卓の確認は docs/decisions/broadcast-calendar.md 2026-10-09（CHAT-1002-CLD-19）で済み、#448 は closed |
| CHAT-1002-CLD-03 | 完了（docs のみ、f35729a7 でマージ済み） | なし | a | 10-08 C卓・D卓は CHAT-1002-CLD-19 で確認済み（#448 closed）。v8I76nBJHyc は続く CLD-04 で説明欄の差（9人目）を調べ、CLD-06 で予定を【3】の値に直してマージ済み（broadcast-calendar.md 2026-10-03 CLD-06） |
| CHAT-1002-CLD-04 | 完了（判断: 【3】に値がある見出しは【3】だけ。続き CLD-06） | なし | a | 直し方は CLD-05 で決定・CLD-06 で実装（broadcast-calendar.md 2026-10-03）。別件の「◎B卓 関本幸樹」は 31b59136b（#490 見出しの接頭辞除去）の後、CHAT-1002-CLD-20 で FXtYzZBEtXA の対局者が8名の正しい名前だけと確認済み。#448 closed |
| CHAT-1002-CLD-05 | 完了（判断: 【3】の値だけを使う。続き CLD-06） | なし | a | jt4E_u--mxg の扱いは broadcast-calendar.md 2026-10-03（CLD-06）で決定し実装・マージ（c42c8735）。さらに CLD-09 で【4】カレンダー非掲載に。#448 closed |
| CHAT-1002-CLD-06 | 完了（c42c8735 でマージ済み） | なし | a | 残った未確認の 10-08 C卓・D卓は CHAT-1002-CLD-19 で確認して #448 を closed（broadcast-calendar.md 2026-10-09）。v8I76nBJHyc・jt4E_u--mxg はその後の CLD-09・CLD-20 の確認の対象で、後者は【4】で非掲載 |
| CHAT-1002-CLD-07 | 完了（判断: 【3】3147行を案Bに書き換え。続き CLD-08） | なし | a | jt4E_u--mxg は broadcast-calendar.md 2026-10-04（CHAT-1002-CLD-09）で「【4】カレンダー非掲載」に入れ予定は全編だけになったため、件名に南場を出す論点は消えた。/live の見出しは live.md（CLD-13・14 の続き）で【3】の枝番「4回戦 南場」で決定 |
| CHAT-1002-DOJ-01 | 完了（マージ済み） | なし | a | 22件の書き込みと手段は docs/decisions/dojo-guest.md 2026-10-02（DOJ-02: 読み取り結果を保存して書き込む）で決定・実施、10月分は平野さんが確認済み（DOJ-03 の節）。B1 の見出しは DOJ-03 の報告で直っていたと確認 |
| CHAT-1002-DOJ-03 | 完了（dcc5cf21 でマージ済み） | 道場部ゲストの書き込み済みの月の画像差し替え時の自動更新（予定の書き換え・削除、サービスアカウントの権限を含む）が本番で未確認。翌月分の掲載後に当月の差し替えを検知できるかも本番で未確認 | b | #390（open）。この実装の issue で、#390 のコメントに同じ未済事項（本番でまだ一度も動いていない）が既に書かれている |
| CHAT-1002-INV-01 | 完了（論点は #221 に移した。CHAT-1012-ACF-05） | なし | a | 論点（「親: #n」と sub-issue の不一致）は CHAT-1012-ACF-05 で #221 に移し済み（docs/decisions/operations.md の CHAT-1012-ACF-04 の移し先の行）。報告の判断・未確認は「なし（#221 に移した）」 |
| CHAT-1002-INV-02 | 完了 | #496（issue のクローズでカレンダー「ryoei.pro」の予定を自動で済みにする）を進めるには、既存のサービスアカウント（GCP_SA_KEY）をカレンダー「ryoei.pro」に「予定の変更」権限で共有する平野さんの作業が要る。 | b | 書き写し先 #496（open。本文の「決めること」「前提作業」に同じ論点がある）。ほかの項目は済み: INV-03 の範囲は CHAT-1003-INV-03（ee771096 ほか）で処理し、.github・birthday-calendar.md に #370 を通知先とする記述は今は無い（git grep）。#491・#482 は closed。Projects ボードは今は使わない（CLAUDE.md） |
| CHAT-1003-INV-04 | 完了 | なし | a | 10-05 の check-meibo の予約実行は success（#472 の 2026-10-05 のクローズのコメント、CHAT-1005-RUN-07）、#472 closed。blocked by の向きは #382・#428 とも closed で論点が消えた。#124 closed |
| CHAT-1003-RUN-01 | 完了 | なし | a | #457 は 2026-10-04 にクローズ（CHAT-1004-RUN-04。CSV はリポジトリに置かない、残る作業は無いと決定）。404・5xx の今の状態は #11（closed）につないだ |
| CHAT-1004-VID-03 | 完了（CHAT-1004-VID-04 で cloudflare へマージ） | なし | a | 動画は CHAT-1004-VID-07 で標準版に確定（d82931aa、docs/decisions/title.md 2026-10-04 VID-07）。マージ済み。docs/notes/title-pages.md「告知動画」に確定版・作り直しの手順がある。「全行で一致」の記述は「2026-09-28 時点で」と日付つきで書かれている |
| CHAT-1004-VID-05 | 完了（CHAT-1004-VID-07 で cloudflare へマージ。確定は標準版） | なし | a | どれを投稿するかは CHAT-1004-VID-07 で標準版・音楽 B に確定（docs/decisions/title.md 2026-10-04 VID-07、d82931aa）。title-pages.md「告知動画」の節も直っている。X への投稿は平野さんが行う |
| CHAT-1004-VID-06 | 完了（CHAT-1004-VID-07 で cloudflare へマージ。確定は標準版） | なし | a | 標準版で確定し比較版は採らない（docs/decisions/title.md 2026-10-04 VID-07、d82931aa）。title-pages.md「告知動画」に VARIANT・曲調・字幕の自動の時刻が書かれている |
| CHAT-1005-RVW-07 | 中断（続きは CHAT-1005-RVW-09） | なし | a | 「辞書」タブを正とし625語、「備考」は出さないと決定（docs/decisions/features.md 2026-10-06 CHAT-1005-RVW-09）。#377 closed |
| CHAT-1005-RVW-10 | 中断（続きは CHAT-1005-RVW-11） | なし | a | 続きの CHAT-1005-RVW-11 で実装、RVW-13 でマージ（docs/decisions/features.md 2026-10-07 RVW-10・RVW-12）。static-generation.md「ページの一覧」に辞書と houou_race の行が両方ある。#377 closed |
| CHAT-1005-UNR-10 | 判断待ち | なし | a | 続きの CHAT-1005-UNR-11〜13 で13名・277名・RMU を処理し、残り295行は今のままと決めて #500 を閉じた（#500 の締めのコメント、docs/decisions/live.md 2026-10-05 UNR-12・UNR-13）。#500 closed |
| CHAT-1006-LGR-05 | 完了 | なし | a | 出場なしの選手の扱いは CHAT-1006-LGR-07 で置き換え（docs/decisions/houou.md 2026-10-06）。ページは #540 で公開し houou/race/ へ 301（static-generation.md「ページの一覧」）。#507 closed |
| CHAT-1006-PHT-06 | 判断待ち（続き: CHAT-1007-PHT-09） | なし | a | 診断と表示（案 A・B）は CHAT-1007-PHT-09 で入れ、その後 X 公式 API への切り替えとヘッドレス Chromium の解決の削除を決定（docs/decisions/saikyo.md 2026-10-09〜10 CHAT-1010-XAP-02）。今の scripts/・.github に解決用の headless Chrome は無い（git grep）。残りの経過は open の #514 が追う |

### 手順2・3: 書き写し・削除・マージ

- permalink の SHA（issue のコメント）: 0c15a02f6（仕分けの表を書いたコミット。削除のコミット 3d7a412e6 の親）。23件の中身は 0122413ee から 0c15a02f6 まで変わっていない（`git diff --stat` が空）ので、docs/notes の書き換えに使った 0122413ee の版とも同じ
- (b) のコメント（本文・状態・ラベルは変えていない）:
  - #390（CHAT-1002-DOJ-03）: https://github.com/retroeater/mj/issues/390#issuecomment-6258187171 （書いた直後に、前置きの1文の誤り「上の CHAT-0930… ではなく」を直した）
  - #496（CHAT-1002-INV-02）: https://github.com/retroeater/mj/issues/496#issuecomment-6258187729
- (c) は0件で、#610 には書いていない
- 削除: 3d7a412e6「chore: remove remaining 23 old-format work logs」（23ファイル。書き換えの c33418c98 とは別のコミット）。メッセージに閉じるキーワードと `#番号` の並びが無いことを grep で確かめた。削除の後のテストは 1427件 OK。`is_new_rule_log` が偽のログは0件になった
- マージ: 11:13 JST ごろ（03:45〜05:15・月曜 06:00〜09:00 の外）。push 直前に `git fetch origin cloudflare` と `git merge-base --is-ancestor origin/cloudflare HEAD`（真）を確かめ、`git push origin work/1019-acf-old2:cloudflare`（0122413ee..3d7a412e6、fast-forward）
- マージ後: テストは OK。mj-logs の raw で CHAT-1002-INV-01・CHAT-1006-PHT-06・CHAT-1004-VID-03・CHAT-0930-CAL-15 が 404 になった（写しが消えた）
- #513 に削除の件数のコメント（23件、ACF-27 と合わせて144件）: https://github.com/retroeater/mj/issues/513#issuecomment-6258272732
- docs/decisions: 指示の「決定」節（2026-10-19）を docs/decisions/operations.md の ACF-27 の節の後に足した

## 報告

- 状態: 完了
- ブランチ: work/1019-acf-old2
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-ACF-28.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-acf-old2
- 確認用URL: なし
- マージ: 済（3d7a412e6。cloudflare へ fast-forward。docs/notes 3か所の書き換え〈c33418c98〉と docs/logs の削除。このログと決定は追いの push）
- issue: コメント #390・#496・#513（削除の件数）。#610 への追記なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7c82be63）: https://github.com/retroeater/mj-logs/tree/main/guide/7c82be63

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c82be63/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8593ce34.md
