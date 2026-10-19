# CHAT-1019-ACF-27

- 着手日時: 2026-10-19
- 対象issue: #513・#357
- ブランチ: work/1019-acf-old
- 着手時HEAD: 5968094617296524f5bc6998cdd4f37c3c75db42

## 指示

【Claude作成】Claude Code 向け指示：旧い作業ログ（2026-10-08 より前に始まったもの）を仕分け、未済事項を issue へ移して、すべて削除する
Chat-Ref: CHAT-1019-ACF-27
マージ: 承認済み（チャットで。変わるのは docs/logs の削除と、決定を書くなら docs/decisions だけ。止まる条件つき）
貼る時機: いつでも（CHAT-1019-ACF-26 は完了・マージ済み）。ただし JST 03:45〜05:15（run-daily の起動の間）と、月曜 06:00〜09:00（週次の作業ログの片付けの前後）は cloudflare へ push しない
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-acf-old を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-acf-old origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#513 に「着手中」のコメントを書く（本文・状態・ラベルは変えない）。

目的
自動削除の規則（#513）より前の形式で書かれた作業ログは、週次の片付けで消えず、#357 の通知を毎週ふくらませている（10/19 の通知 212件のうち旧いログが 139件）。旧いログを1本ずつ仕分け、まだ片付いていない事項だけを issue に書き写してから、旧いログをすべて削除する。
決定（2026-10-19、平野さん）

* 旧い形式のログ（2026-10-08 より前に始まったもの）は、未済事項を書き写したうえで、すべて削除する
* 書き写し先: 関係する open の issue があればその issue へコメントし、無いものだけ新しい issue 1本に表でまとめる。片付いた論点は書き写さない
* 仕分け・書き写し・削除を1本の指示で行う（仕分けを見てから削除する2段にはしない）

前提（チャット側。平野さんの決定ではない）

* 対象の見込み: 139件（CHAT-1019-ACF-26 のログ `## 経過`「#513 1回目の計測」の (3) の内訳。旧いログで7日以上のもの 165件のうち、10/19 に削除されたのが 26件、残りが 139件〈②127・①12〉）。「始まった」の読み方は ACF-26 と同じ（ログの最初のコミットの日を JST で見る。`cleanup_logs.py` の `is_new_rule_log` と同じ）。対象は「その時点の origin/cloudflare の `docs/logs/CHAT-*.md` のうち、新しいログでないものすべて」で、通知に載ったかは問わない
* 削除はコミットなので、git の履歴から戻せる。書き写しと、ログを名指しする所には、削除の直前の cloudflare のコミットの SHA を固定した permalink（`https://github.com/retroeater/mj/blob/<SHA>/docs/logs/<ファイル名>`）を使う（docs/notes/branch-operations.md「作業ログの寿命」の書き方）
* 仕分けの3つの区分（チャット側の案。実物に合わせて区分の名前は変えてよい）:
   * (a) 片付いている: 論点が、後の指示・閉じた issue・docs/decisions の決定・今のコードで済んでいる。書き写さない。片付いたと判断した根拠（後の Chat-Ref、issue の番号と状態、コミット）を1つ以上書く
   * (b) 未済で、関係する open の issue がある: その issue にコメントする（1つの issue に複数のログが当たるときは1コメントにまとめる）
   * (c) 未済で、関係する issue が無い: 新しい issue 1本に表でまとめる（題は「旧い作業ログから移した未済事項（2026-10-19）」の案。起票の前に同じ主題の issue をクローズ済みも含めて検索する）
   * 片付いたかどうか判断がつかないものは (c) に入れる（黙って落とさない）
* カレンダー「ryoei.pro」の予定が、旧いログを名指ししている（チャット側が 2026-10-19 に読んだ。Code からは読めない）: CHAT-1005-RVW-22（10/21 の【R#298】。mj-logs の main の URL で名指し）・CHAT-1002-INV-01・CHAT-1005-RUN-06・CHAT-1005-RUN-08・CHAT-1005-RUN-10・CHAT-1006-WKR-07・CHAT-1005-RVW-04・CHAT-0930-DUP-03・CHAT-0924-TQ-28・CHAT-0928-SC-04・CHAT-0928-SC-08・CHAT-1007-PHT-14。残っているものは削除してよいが、報告に permalink を書く（チャット側が予定の説明を permalink に書き換える）
* 10/26 の #513 の2回目の計測の (3)（通知の合計）は、この削除で減る。#513 のコメントに削除した件数を書いておけば、2回目で差し引ける
* 案A（判定のくり返し）は 10/26 の後に入れる決定のまま。この指示では `scripts/cleanup_logs.py`・ワークフロー・docs/notes を変えない

手順

1. 対象の旧いログの一覧を作り、件数を見込み 139件と比べる。リポジトリの中で `docs/logs/` 以外のファイル（scripts・docs/notes・CLAUDE.md・docs/handover.md など。docs/decisions の出典としての Chat-Ref は除く）が対象のログを名指ししていないかを `git grep` で確かめる。名指しされているログは対象から外し、名指ししている所と一緒に報告する。1本ずつ読み、(a)〜(c) に仕分けて `## 経過` に全件の表を書く（列: ログ・状態・未済事項の要約〈(a) は「なし」〉・区分・根拠または書き写し先）。略さない
2. 書き写す。(b) は issue ごとに1コメント（ログの permalink・未済事項・元の状態を書く。本文・状態・ラベルは変えない）。(c) は新しい issue 1本に表でまとめる（ラベルは付けない。ログの permalink を各行に）。permalink の SHA は、削除のコミットの親になる cloudflare のコミットにする
3. 対象のログを1つのコミットで削除し、cloudflare へマージする（push 直前に再 fetch と祖先の確認。コミットメッセージに閉じるキーワードと `#番号` を続けて書かない）。マージ後に mj-logs の `logs/` から写しが消えたかを数件確かめる。#513 に「旧いログ N件を削除した（2026-10-19、10/26 の (3) で差し引く数）」とコメントする。報告の `## 経過` に、前提のカレンダーの名指しのログそれぞれの permalink（削除済みなら削除前の版、もとから無ければ「既に無い」）を表で書く

止まる条件

* 対象の件数が 139件と 10件より多くずれる（一覧と、ずれの理由の見当を書いて止まる）
* (b) で書き込む issue が 30本を超える、または (c) が 60件を超える（書き写しの前に止まり、仕分けの表を報告して「判断待ち」にする。通知が多すぎないか平野さんが決める）
* 対象のログに、他のセッションが今日（2026-10-19）コミットしている、または未マージの work/ ブランチが対象のログを変えている（そのログだけ外して進め、外したものを報告に書く。全部が当たるときだけ止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「issue」の項目に、コメントした issue の番号と、新しく作った issue の番号を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-ACF-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-ACF-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `CHAT-1019-ACF-27` のコミット無し（fetch の後）。雛形の行は揃っている。着手は 09:58 JST（月曜 06:00〜09:00 の外）
- 作業ブランチ: ローカル・リモートとも `work/1019-acf-old` は無く、`git checkout -b work/1019-acf-old origin/cloudflare`（596809461）で作った
- 0. 指示欄の末尾は指示文の最後の行と一致。#513 の着手中コメント: https://github.com/retroeater/mj/issues/513#issuecomment-6257405802

### 手順1: 対象の一覧

- origin/cloudflare（596809461）の `docs/logs/CHAT-*.md` のうち、`cleanup_logs.is_new_rule_log(path, "2026-10-08")` が偽のもの: **144件**。見込み 139件との差 +5 は、ACF-05 で 10/12 に完了にした5件（CHAT-0930-BNG-01・DUP-03・DUP-10・OLT-09・CHAT-1002-INV-01。最終コミットが 10/12 のため 10/19 は7日の条件に届かず、139件に入っていなかった）。139件の残りとは名前の集合が一致した（ACF-26 の再現の結果と照合）。差は10件以内なので進めた
- 今日（2026-10-19 JST）のコミットで対象のログを変えたものは無い（`git log --all --since=2026-10-18T15:00Z -- docs/logs/`）。未マージの `work/*` で対象のログを変えているものも無い（`git branch -r --no-merged origin/cloudflare` の各ブランチの merge-base からの差分）
- `docs/logs/`・`docs/decisions/` 以外で名指ししているログ（`git grep`）: **23件を対象から外した**。多くは出典としての Chat-Ref（コードのコメント・テストの文字列・docs/notes の事例）だが、指示のとおり外した。対象は **121件**
  - docs/decisions の中で、出典でなくログを読む先として名指ししているのは「CHAT-1006-PHT-06 のログ」（`docs/decisions/saikyo.md:16`）だけで、PHT-06 は下の表で外している。`docs/decisions/operations.md:85`「CHAT-1003-CLF-01 のログと docs/decisions は cloudflare へマージしてよい」は決定の出典なので外していない

外したログと名指ししている所:

| ログ | 名指ししている所 |
| --- | --- |
| CHAT-0930-BNG-05 | `docs/notes/handover-archive-2026.md:853` |
| CHAT-0930-CAL-15 | `docs/notes/handover-archive-2026.md:858`・`docs/notes/live-channel-write.md:76`・`scripts/lib/sheets_write.py:20`・`scripts/tests/test_sheets_write.py:244` |
| CHAT-0930-OLT-01 | `docs/gsc/2026-10-01/jpml-titles-landing.md:16` |
| CHAT-1002-CLD-02 | `docs/notes/yotei-sheet.md:90`・`docs/notes/yotei-sheet.md:131`・`scripts/lib/yotei.py:181`・`scripts/lib/yotei.py:214`・`scripts/tests/test_cleanup_logs.py:240`・`scripts/tests/test_yotei.py:136` |
| CHAT-1002-CLD-03 | `docs/notes/yotei-sheet.md:131` |
| CHAT-1002-CLD-04 | `docs/notes/handover-archive-2026.md:654` |
| CHAT-1002-CLD-05 | `docs/notes/handover-archive-2026.md:656` |
| CHAT-1002-CLD-06 | `docs/notes/yotei-sheet.md:112`・`scripts/lib/live_calendar.py:126`・`scripts/lib/live_layer3.py:34`・`scripts/tests/test_live_calendar.py:114`・`scripts/tests/test_live_layer3.py:74` |
| CHAT-1002-CLD-07 | `docs/notes/handover-archive-2026.md:687`・`docs/notes/handover-archive-2026.md:704` |
| CHAT-1002-DOJ-01 | `scripts/tests/test_dojo_guest.py:27`・`scripts/tests/test_dojo_guest.py:384` |
| CHAT-1002-DOJ-03 | `scripts/tests/test_dojo_guest.py:424` |
| CHAT-1002-INV-01 | `docs/notes/handover-archive-2026.md:966` |
| CHAT-1002-INV-02 | `docs/notes/handover-archive-2026.md:966` |
| CHAT-1003-INV-04 | `docs/notes/handover-archive-2026.md:854`・`docs/notes/handover-archive-2026.md:966` |
| CHAT-1003-RUN-01 | `docs/gsc/2026-10-03/README.md:8` |
| CHAT-1004-VID-03 | `scripts/promo_video/title/build.sh:2`・`scripts/promo_video/title/capture.mjs:1`・`scripts/promo_video/title/composition/index.html:2`・`scripts/promo_video/title/composition/index.html:44`・`scripts/promo_video/title/setup.sh:2` |
| CHAT-1004-VID-05 | `scripts/promo_video/title/music.py:1` |
| CHAT-1004-VID-06 | `scripts/promo_video/title/capture.mjs:3`・`scripts/promo_video/title/compose.py:1` |
| CHAT-1005-RVW-07 | `docs/notes/handover-archive-2026.md:657` |
| CHAT-1005-RVW-10 | `docs/notes/handover-archive-2026.md:708` |
| CHAT-1005-UNR-10 | `docs/notes/live-page-design.md:100` |
| CHAT-1006-LGR-05 | `scripts/tests/test_cleanup_logs.py:235`・`scripts/tests/test_cleanup_logs.py:236` |
| CHAT-1006-PHT-06 | `docs/notes/saikyo-page-design.md:649` |

### 手順1: 仕分け

- 121件を4つに分け、読むだけのサブエージェント4つで1本ずつ読んで仕分けた（全文・続き先のログ・issue の状態・docs/decisions・今のコードを確かめ、根拠を書かせた）。結果を受け取り、a の根拠の数件（docs/decisions/operations.md の CHAT-1005-RVW-22・CHAT-1018-BIL-01 の節、houou.md の CHAT-1006-LGR-02 の行、seo-bing.md の #5 の行）と b の RVW-22 の本文を手元で照合した
- 区分の名前は指示の案のまま。a には「論点を open の issue へ移し済み」のもの（ACF-05 の5件・SH-18 など）を含めた（書き写しが要らないため）
- 件数: **a 115件・b 5件（issue 5本: #308・#390・#492・#298・#504）・c 1件**。止まる条件（b の issue 30本超・c 60件超）に当たらない
- (c) の起票の前の検索: 「旧い作業ログ」「番号が食い違」「止まらず記録」（REST の search、0件）と、MCP の search_issues（同じ主題の issue は無し。近いのは #513・#357・#492 だが、指示文の issue 番号の例外を扱うものではない）

| ログ | 状態 | 未済事項の要約 | 区分 | 根拠または書き写し先 |
| --- | --- | --- | --- | --- |
| CHAT-0929-SH-18 | 完了 | なし | a | #446 の未決 U1〜U4 は CHAT-1002-INV-02 で #490（open）に分けて #446 をクローズ済み（#446 の 2026-10-02 のコメント）。本文の書き写しは不要 |
| CHAT-0929-SH-19 | 完了 | なし | a | /title の残件 #470・#471・#232・#222 は closed、#408 は open のまま handover.md の表で追跡中。「ログ（公開）」の形は CLAUDE.md「作業ログ」節（mj-logs）に定着 |
| CHAT-0929-ZK-10 | 完了 | なし | a | 毎日の取り込み後の確認とステージの直しは CHAT-0929-ZK-14（35ed4da84、#476 クローズ）で済み。404 のままにする決定は SH-19 で記録。#476 closed |
| CHAT-0929-ZK-18 | 完了 | なし | a | #482 closed。未確認の数式の文字は全行の値で確かめ済み。後に 6a3e3dfc7（参考列を毎日の追記で飛ばす）も入っている |
| CHAT-0929-ZK-19 | 完了 | なし | a | #478 の3つ目（ほかの大会に同じ形があるか）は #489（closed、洗い出し）で扱い済み |
| CHAT-0929-ZK-22 | 完了 | なし | a | docs/notes/title-pages.md「期ページの放送」が #476 の案A（is_listed）・#487（drop_member_duplicates）を反映済み |
| CHAT-0930-ACT-01 | 完了 | google-github-actions/auth の Node 24 対応版と Node.js 20 打ち切りの期限、actions/cache@v4（sync-dojo-calendar）が警告の対象かが未確認。あわせて #475 の「57名が 9/29 に出ていた」実行は見つかっていない（78a7fab による減少と推定） | b | #308（open、Node 24 対応版へ上げる issue。2026-10-10 にも警告の注記があり未解決） |
| CHAT-0930-BNG-01 | 完了（論点は #304 に移した。CHAT-1012-ACF-05） | なし | a | 論点は CHAT-1012-ACF-05 で #304 に移し済み（#304 の 2026-10-12 のコメント、docs/decisions/operations.md） |
| CHAT-0930-BNG-02 | 完了 | なし | a | #126 は CHAT-0930-BNG-03 で結論を出してクローズ（closed）。結論は docs/decisions/seo-bing.md |
| CHAT-0930-BNG-03 | 完了 | なし | a | #486 の「決めること」は CHAT-0930-BNG-04 で決定し docs/decisions/seo-bing.md に記録。Bing の指摘の対象は BNG-04 で照合済み。実装は #486（open）本体で追跡 |
| CHAT-0930-BNG-04 | 完了（#5 の扱いは判断待ち） | なし | a | docs/decisions/seo-bing.md（BNG-04）: #5 を再オープンして受け皿にする決定、#5 は open。Bing の古い指摘の見直しは BNG-06 で #486 に申送りコメント済み |
| CHAT-0930-BNG-06 | 完了（手順2 (b) は判断待ちで足していない） | 指示文の issue 番号が食い違ったとき「止まらず記録する」例外を CLAUDE.md「Chat-Ref」節（前提と実物の食い違いは中断して報告）に足すか、どこまでを例外にするか（参照の番号違いだけか）を平野さんが決める | c | docs/decisions/ に決定が無く、CLAUDE.md にも例外は入っていない。この論点を扱う open の issue が見当たらない |
| CHAT-0930-CAL-09 | 完了 | なし | a | CAL-19 の件名の直しは 045544143（【麻雀】【無料放送】を外す・除外タブが無いと止める）で済み。#479・#450 closed |
| CHAT-0930-CAL-10 | 完了 | なし | a | #480 closed。未確認は本番のブラウザの見え方のみ（プレビューで平野さん確認済み） |
| CHAT-0930-CAL-14 | 完了 | なし | a | 決定の書き足しは CLAUDE.md「作業ログ」節、決定の読み方は docs/notes/chat-side-operations.md（docs/decisions/README.md を読む項目）に入っている |
| CHAT-0930-CAL-17 | 完了 | なし | a | 判断が必要なこと なし。未確認は mj-logs の blob の見え方のみ（raw で確認済み） |
| CHAT-0930-CAL-18 | 完了（#450 は明朝の毎朝の実行を確かめるまで open） | なし | a | #450 は CAL-20 時点で closed。「【麻雀】」の接頭辞は scripts/lib/live_calendar.py の TITLE_MARKS で外すようにした（045544143） |
| CHAT-0930-CAL-20 | 完了 | なし | a | 「【Free broadcast】」は scripts/lib/live_calendar.py の TITLE_MARKS に入った（2026-10-01 の決定）。除外タブの注意は docs/notes/yotei-sheet.md 側 |
| CHAT-0930-CAL-23 | 完了 | なし | a | 減った5件は 10-02 に平野さんが手で消したもの（#491 本文「MAX_DELETES の件」、closed）。件名の決定は docs/decisions/broadcast-calendar.md（CAL-23） |
| CHAT-0930-CAL-26 | 完了 | なし | a | #491 の「MAX_DELETES の件」は済（CHAT-1003-INV-04、#491 closed）。手順は docs/notes/yotei-sheet.md の calendar_max_delete |
| CHAT-0930-CAL-28 | 完了 | なし | a | ログの `## 報告` は最後の一致を使う規則を CHAT-0930-CAL-29 で CLAUDE.md「作業ログ」節に移した（今の CLAUDE.md にある） |
| CHAT-0930-CAL-29 | 完了 | なし | a | 判断が必要なこと・未確認の項目 ともになし |
| CHAT-0930-DUP-02 | 完了（CHAT-0930-DUP-05 でマージ、#487 は閉じた） | なし | a | CHAT-0930-DUP-05（78e67ff7）でマージと本番確認、#487 closed |
| CHAT-0930-DUP-03 | 完了（論点は #485 に移した。CHAT-1012-ACF-05） | なし | a | 論点は CHAT-1012-ACF-05 で #485 にコメント済み（2026-10-12） |
| CHAT-0930-DUP-04 | 完了 | なし | a | #232 は CHAT-0930-DUP-07〜11 で試作・見本・本実装を経て closed（DUP-11） |
| CHAT-0930-DUP-05 | 完了 | なし | a | #487 closed、判断が必要なこと なし |
| CHAT-0930-DUP-06 | 完了（ラベル「対象: jpml_titles」の削除だけ判断待ち） | なし | a | ラベルの論点は #485（open）を閉じるときに決めると決定（docs/decisions/operations.md、CHAT-1012-ACF-05 の #485 コメント）。書き写し済み |
| CHAT-0930-DUP-07 | 完了（試作は採用せず、DUP-08 で入口を文字だけに） | なし | a | 平野さんの判断で入口を文字だけの画像に変更（CHAT-0930-DUP-08）、Q11 等の論点は消滅。#232 closed |
| CHAT-0930-DUP-10 | 完了（論点は #304 に移した。CHAT-1012-ACF-05） | なし | a | X・LINE での本番確認は #304 の 2026-11 の回で見る（docs/decisions/operations.md、#304 の 2026-10-12 コメント） |
| CHAT-0930-DUP-11 | 完了 | なし | a | #232 closed、判断が必要なこと なし |
| CHAT-0930-DUP-12 | 完了（CHAT-0930-DUP-13 でマージ） | なし | a | CHAT-0930-DUP-13 で平野さんのプレビュー確認のうえマージ済み |
| CHAT-0930-DUP-13 | 完了 | なし | a | 判断が必要なことは「なし」。未確認はブラウザでの本番の見え方だけで、navbar.js の中身とヘッダは curl で確認済み（確認済みの記録のみ） |
| CHAT-0930-HKG-06 | 完了 | なし | a | 未確認の「push 時の確認ダイアログ」は、その後 hook が 7448e108 で差し替えられ CHAT-0930-HKG-08 で39件の試験を通して確かめ直した。Actions の項は docs/logs のみの push で、マージ 780337ea の Actions・check-run は全件 success 記録済み |
| CHAT-0930-HKG-08 | 完了 | なし | a | CHAT-0930-CAL-13 の再開の可否: CAL-13 は 12f3deac3「docs: finish report for CHAT-0930-CAL-13」で仕上がり、55e218fc3 で完了ログとして削除済み。#176 へのコメントも済み |
| CHAT-0930-OLT-04 | 完了 | なし | a | 案A/案Bは CHAT-0930-OLT-06 で案B（#446 項目8、#446 closed）を実施。SGlbTPLSs7Q の誤記は OLT-06 の決定どおり平野さんが修正（CHAT-0930-OLT-08 で確認）。ff8_G1P7dm0 の掲載空欄は docs/decisions/live.md「【3】の掲載が空欄の行は N で埋めず空欄のまま」で決定済み |
| CHAT-0930-OLT-05 | 完了 | なし | a | #484 は CHAT-0930-OLT-08 でクローズ。#485「十分に減った」の基準は 2026-10-14 に決定（直近90日でボット以外の転送0件かつ Search Console の着地0件、#485 本文・#568） |
| CHAT-0930-OLT-06 | 完了（判断待ちの後 CHAT-0930-OLT-07 で決定を受けて片付けた） | なし | a | 決定はログ内に記録済み。yotei の失敗（グリッド1000行の上限）は CHAT-0930-CAL-15 で修正（69c62f6f / 97d0b4f5、appendDimension で行を足す）し #479 は closed。【3】の2セルは OLT-07・OLT-08 で処理 |
| CHAT-0930-OLT-07 | 完了 | なし | a | 平野さんのシート手順3つ・title-pages.md の3か所・QUERY から V を外す件は CHAT-0930-OLT-08 で実施（49eba2ef・39df8e4c、d5970d8c でマージ）。#484 closed、旧タブ削除は #473（closed） |
| CHAT-0930-OLT-08 | 完了 | なし | a | #473 は closed（2026-10-11 に6タブ削除、CHAT-1011-PHT-25 で確認・文書修正 3f294739）。「プロ」V 列は 2026-10-11 の15列削除で列ごと無くなった（#473 のコメント） |
| CHAT-0930-OLT-09 | 完了（論点は #408 に移した。CHAT-1012-ACF-05） | なし | a | 論点は CHAT-1012-ACF-05 で #408（open）へ移し済み（docs/decisions/operations.md「OLT-09（#408 の大半を外の資料待ちにするか・食い違いの警告）は #408 で決める」）。報告の項目も「なし（#408 に移した）」 |
| CHAT-0930-OLT-11 | 完了 | なし | a | #232 は closed。マージ条件の解釈と「タイトル」タブの163行減は docs/decisions/title.md「2026-09-30（CHAT-0930-OLT-11）」で決定済み（条件内・平野さんの意図した整理） |
| CHAT-0930-OLT-12 | 完了 | なし | a | yotei の失敗は CHAT-0930-CAL-15 で修正（69c62f6f）、#479 のコメント（2026-10-01、CHAT-0930-CAL-09）で毎朝の実行の成功を確認して #479 closed |
| CHAT-1001-ASG-01 | 完了 | なし | a | #331 closed。Workers Builds の in_progress はワークフローと docs だけの変更で配信に影響なし。分類器の拒否は #493 のコメント（2026-10-03、CHAT-1003-CLF-01）に書き写し済み |
| CHAT-1001-GSC-01 | 完了 | なし | a | #269 closed。#390 の10月分の読み取りは CHAT-1002-DOJ-02 で22件を読み取り・書き込み済み（#390 のコメント 2026-10-02） |
| CHAT-1002-CLD-01 | 完了（判断が出た: 案1。続きは CHAT-1002-CLD-02） | なし | a | 案1は CHAT-1002-CLD-02 で実装（yotei.attached_publics()）。#448 は CHAT-1002-CLD-19 で 10-08 の鳳匠戦 C・D卓を iCal で確かめ 2026-10-09 に closed |
| CHAT-1002-CLD-08 | 完了 | なし | a | 3147行（jt4E_u--mxg）は後の CLD-11〜15 で扱いを決め直し、origin/cloudflare の live/ranwa/6/b16-d.html に「ベスト16 D卓 4回戦 南場」が反映済み。#448 closed |
| CHAT-1002-CLD-09 | 完了（判断が出た: 2点を直してマージ。続きは CHAT-1002-CLD-10） | なし | a | CHAT-1002-CLD-10 でマージ（bc730f65）。jt4E_u--mxg が【4】にあることは CHAT-1002-CLD-15 で確認（build_desired() に載らない） |
| CHAT-1002-CLD-10 | 完了 | なし | a | jt4E_u--mxg は CHAT-1002-CLD-15 で【4】にあり build_desired() に載らないことを確認。#448 は CLD-19 で iCal を確かめて closed |
| CHAT-1002-CLD-11 | 完了（判断が出た。続きは CHAT-1002-CLD-12） | なし | a | docs/decisions/live.md「2026-10-05（CHAT-1002-CLD-12）」で jt4E_u--mxg を残す・R3IZ244ANxQ の対局者は8名のまま・コードは直さないと決定 |
| CHAT-1002-CLD-12 | 完了 | なし | a | 3147行は CHAT-1002-CLD-13・15 で読み直し済み。決定は docs/decisions/live.md に記録 |
| CHAT-1002-CLD-13 | 完了（判断が出た。続きは CHAT-1002-CLD-15） | なし | a | CHAT-1002-CLD-15 で 3147行の値を確認し live.md に決定を記録。origin/cloudflare の live/ranwa/6/b16-d.html に「ベスト16 D卓 4回戦 南場」が反映済み |
| CHAT-1002-CLD-14 | 完了（判断が出た。続きは CHAT-1002-CLD-15） | なし | a | 枝番の空白は平野さんが直し、CHAT-1002-CLD-15 で U+0020 入りの「4回戦 南場」を確認。生成物にも反映済み（origin/cloudflare の live/ranwa/6/b16-d.html） |
| CHAT-1002-CLD-15 | 完了 | なし | a | 未確認だった次の /live の生成での反映は、origin/cloudflare の live/ranwa/6/b16-d.html に「ベスト16 D卓 4回戦 南場」があり、b16-c.html に jt4E_u--mxg が無いことで確認 |
| CHAT-1002-CLD-16 | 完了（判断が出た: そのままマージ。続きは CHAT-1002-CLD-17） | なし | a | CHAT-1002-CLD-17 でマージ（87134fea）。決定は docs/decisions/operations.md「2026-10-05（CHAT-1002-CLD-16）」 |
| CHAT-1002-CLD-17 | 完了 | なし | a | 判断が必要なこと・未確認ともに「なし」。マージ 87134fea |
| CHAT-1002-CLD-18 | 完了 | なし | a | /live の反映は origin/cloudflare の live/ourai/9/b16-d.html「ベスト16 D卓 4回戦 南場」・live/ourai/7/b16-d.html「ベスト16 D卓 4回戦」で確認。2本は【4】にあり予定に載らない。#448 は CLD-19 で iCal を確かめて closed（カレンダーの2件の削除そのものは個別に見ていない） |
| CHAT-1002-DOJ-02 | 完了 | なし | a | 「樫野凪」は docs/decisions/dojo-guest.md「改名のため。対応は不要」で決定。見出しは 2026-10-02 に「～道場部～」に直り cloudflare で通知が出なかったことを #390 のコメント（CHAT-1002-DOJ-03）で確認 |
| CHAT-1002-DOJ-04 | 完了 | 道場部ゲストの書き込み済みの月の自動更新・保留からの反映が本番でまだ一度も動いていない（実際の画像の差し替えが起きていない。予定の書き換え・削除の権限も未確認）。差し替えで #426 に「自動で更新しました」等が出たら、カレンダーの当日以降が画像と合うかを確かめる。 | b | #390（open。道場部ゲストの取り込みの issue で、CHAT-1002-DOJ-03 のコメントに「本番ではまだ動いていない」とあり、docs/handover.md も #390 の行で追っている。#426 の本文の食い違いは CHAT-1002-DOJ-05 で直し済み） |
| CHAT-1002-UNR-02 | 完了（CHAT-1003-UNR-04 で #475 が0名になった） | なし | a | CHAT-1003-UNR-04 で平野さんの登録と規則の直しをマージ（6d694cef..bc7308a8）し #475 の未登録の名前が0名に。(c) の規則の残りは #490 が受け皿 |
| CHAT-1003-CLF-01 | 完了（新規起票はせず #493 に集約。CHAT-1003-CLF-02 で反映） | なし | a | CHAT-1003-CLF-02 で平野さんが (A) を採用。拒否の再現の未確認を含む事例は #493（open）のコメント 2026-10-03 に書き写し済み |
| CHAT-1003-INV-05 | 完了 | なし | a | 検査を作る件は #292（open）の本文のチェック項目に追記済み（決定のとおり #292 の着手時に作る）。未確認・エラーは「なし」 |
| CHAT-1003-RUN-02 | 完了 | なし | a | #459・#461 は closed（#461 は CHAT-1004-RUN-04 でクローズし、jpml_pros.html の ?name= が1つにまとまる件は #296 にコメント済み）。#142 の本文に title/ 公開前後の比較の節が足されている（2026-10-04 追加） |
| CHAT-1003-RUN-03 | 完了 | なし | a | #498 は closed（CHAT-1005-RUN-09 でクローズ）。予約実行の初回は以後の運用で動いている（#504 本文が sync-logs の予約実行を前提に書かれ、2026-10-07 に #298 で mj-logs 側へ移った） |
| CHAT-1003-UNR-03 | 完了（CHAT-1003-UNR-04 で #475 が0名に） | なし | a | 規則の直しは CHAT-1003-UNR-04 でマージ（6d694cef..bc7308a8）。「畑谷翔太/翔大」の重複は CHAT-1004-UNR-06 で直してカレンダーに反映（#491 のコメント、#491 は closed） |
| CHAT-1003-UNR-04 | 完了 | なし | a | カレンダーの直す件は CHAT-1004-UNR-06 の calendar_apply の手動実行で書き込み済み（#491 のコメント）。handover.md の #446 の行は今の origin/cloudflare の docs/handover.md に無い |
| CHAT-1003-VID-01 | 完了（CHAT-1004-VID-04 で cloudflare へマージ） | なし | a | 判断事項は後の CHAT-1004-VID-03〜07 で決まり、docs/decisions/title.md（2026-10-04 の決定）と docs/notes/title-pages.md「告知動画（2026-10、X 向け）」に記録済み |
| CHAT-1004-UNR-06 | 完了 | なし | a | 件名が空の tMwcjumwz-o は CHAT-1005-UNR-07 で【3】に行を足して直した（#491 コメント、docs/decisions/broadcast-calendar.md）。概要欄だけの予定の別名訂正は CHAT-1002-CLD-21〜23 で済（#491 は closed） |
| CHAT-1004-VID-04 | 完了 | なし | a | 判断が必要なこと: なし。未確認はブランチ削除と本番の見え方のみ（配信ファイル不変） |
| CHAT-1004-VID-07 | 完了 | なし | a | 判断が必要なこと: なし。決定は docs/decisions/title.md に記録済み。未確認はブランチ削除と本番の見え方のみ |
| CHAT-1004-WBD-01 | 完了（起票せず） | なし | a | マージコミットを含む push のビルドを issue にするかは CHAT-1004-WBD-02 で平野さんが (a) 起票しないと決定（#363・#171 は closed） |
| CHAT-1004-WBD-02 | 完了（起票せず。平野さんの判断 (a)） | なし | a | 判断は解消済み、未確認なし。#363・#171 は closed |
| CHAT-1005-LGR-01 | 判断待ち（続き: CHAT-1006-LGR-02） | なし | a | 判断事項は CHAT-1006-LGR-02 以降で決定（docs/decisions/houou.md の 2026-10-05・10-06 の節: 節の数え方は今のまま、24後 A1 はシート修正済み、決定戦の帯など）。#507 は closed |
| CHAT-1005-RUN-05 | 完了 | なし | a | #141/#111 の順序・#425・#220/#405 は CHAT-1005-RUN-06・RUN-07 で処理（#111・#141・#425 は closed、#220・#405 は新サイト送り、#388 に状況: 待ち） |
| CHAT-1005-RUN-06 | 完了 | なし | a | CHAT-1005-RUN-07 で #141 の題を変更・#111 クローズ・#7 に完了条件・#235 を新サイト送り・#366 本文更新（データは平野さんが作る予定と明記）。#425 の論点は #501・#502（closed）へ |
| CHAT-1005-RUN-07 | 完了（#472 は判断待ち） | なし | a | CHAT-1005-RUN-09 で #472 クローズ、#501・#502 を #425 の sub-issue に登録、道場部の失敗は #503 に起票し手動実行で success を確認 |
| CHAT-1005-RUN-08 | 完了 | なし | a | 判断事項はすべて CHAT-1005-RUN-10 で平野さんが決定し、#504（open）の本文「決定」1〜8 と設計に移された。#491 は closed |
| CHAT-1005-RUN-09 | 完了 | なし | a | 手動実行の落とし穴は CHAT-1005-RUN-11 で #503（open）の本文「やること3・4」に追記済み |
| CHAT-1005-RUN-10 | 完了 | なし | a | 639 分の集計期間は docs/notes/cloudflare.md「ビルド時間の見積もり」に課金の期間（September 9 - October 9、641/3,000分）と記録済み。対処は「ビルドが大きく増えたら見直す」と同節に記載。#505 は closed |
| CHAT-1005-RUN-11 | 完了 | なし | a | chat-side-operations.md の余り66バイトの件は CHAT-1005-RVW-02 で縮小（今の origin/cloudflare で 22,155 バイト） |
| CHAT-1005-RVW-02 | 完了（マージしてよい。続きは CHAT-1005-RVW-03） | `.github/workflows/cleanup-logs.yml`・`scripts/cleanup_logs.py` のコメントと #357 の通知文が、今の CLAUDE.md に無い「『作業ログ』節『ログの寿命』」を指したまま（正は docs/notes/branch-operations.md「作業ログの寿命」）。あわせて CLAUDE.md・handover.md は最終目標（20KB・24KB 前後）に未達。 | b | #492（open。ガイド文書の整理とセンサー化した規則の扱いの issue で、同ログが「4-x」の古い参照を #492 の残りとしていたため） |
| CHAT-1005-RVW-04 | 完了 | なし | a | #377 の未決点は CHAT-1005-RVW-05〜13 で決まり #377 は closed（RVW-14）。h1 は CHAT-1005-RVW-06・08 で先に入れ、title の統一は #283（open）の本文そのものとして残る |
| CHAT-1005-RVW-05 | 完了（調査だけ） | なし | a | 文字コード・タブの列・出どころ・旧ファイルの扱いは CHAT-1005-RVW-09〜13 で決まり実装・マージ（#377 は closed） |
| CHAT-1005-RVW-06 | 完了（マージしてよい。続きは CHAT-1005-RVW-08） | なし | a | CHAT-1005-RVW-08 でマージ（a643706c）。ランキング3ページの h1 は #141（closed）の移植後の houou/ranking/*/index.html などに h1 がある |
| CHAT-1005-RVW-08 | 完了 | なし | a | 判断が必要なこと: なし。未確認は実機 Safari の見え方のみ（平野さんが気づけば報告する扱い） |
| CHAT-1005-RVW-09 | 判断待ち（続きは CHAT-1005-RVW-11） | なし | a | 保存名・文言などは CHAT-1005-RVW-11 で決まり、CHAT-1005-RVW-13 でマージ（ccdefd6b）。#377 は closed |
| CHAT-1005-RVW-11 | 完了（マージしてよい。マージは CHAT-1005-RVW-13） | なし | a | CHAT-1005-RVW-13 でマージ、平野さんがプレビューで取り込みを確認（RVW-13 の報告）。#377 は closed |
| CHAT-1005-RVW-12 | 中断（続きは CHAT-1005-RVW-13） | なし | a | 衝突は CHAT-1005-RVW-13 で解いてマージ（ccdefd6b） |
| CHAT-1005-RVW-13 | 完了 | なし | a | #377 は CHAT-1005-RVW-14 でクローズし、Mリーグのカテゴリは #515 に起票（#515 も closed） |
| CHAT-1005-RVW-14 | 完了 | なし | a | #515 は closed。#388 のラベル「対象: resource_dictionary」は今は付いていない（状況: 待ち・分野: UI/UX のみ） |
| CHAT-1005-RVW-15 | 完了 | なし | a | 判断が必要なことは実施済みの報告（new-site-design.md の「冊」を直した）のみ。未確認なし |
| CHAT-1005-RVW-16 | 完了 | なし | a | 判断が必要なこと: なし。#515 は closed |
| CHAT-1005-RVW-17 | 完了 | なし | a | 減らす候補 A〜E の採否と予算は docs/decisions/operations.md「2026-10-18（CHAT-1018-BIL-01）」で決定（予算 $20、案 A だけ採る）。#298 の 2026-10-18 のコメントに10月の実測と案 A の実装（c80a0c4b4） |
| CHAT-1005-RVW-21 | 判断待ち | なし | a | (a)〜(c) の判断は docs/decisions/operations.md「2026-10-07（CHAT-1005-RVW-22）」で決定（mj 側の sync-logs を止め、sync-from-mj の再試行を直す）。RVW-22 で実施（e34005916、mj-logs 2ac1101） |
| CHAT-1005-RVW-22 | 判断待ち（止まる条件「(ii) で写らない」に当たって止めた） | #298 実装4の後半で消すものの一覧: mj の sync-logs.yml・Secret MJ_LOGS_TOKEN と PAT mj-logs-sync・[sync-logs] の規則（CLAUDE.md「作業ログ」・_template.md・cloud-sessions.md・branch-operations.md・static-generation.md・scheduler-worker.md）・scripts/sync_logs.py・sync_guides.py・chat_ids.py・actions_status.py の docstring「sync-logs.yml から呼ぶ」（今も残っている）。Worker が起動しない件は解消済み | b | #298（open。2026-10-12 のコメントで実装4の後半〈2026-10-21〉を受け持つ。docstring まで含む一覧はこのログにしか無い）。Worker の件は docs/notes/scheduler-worker.md の 10/8 の記録（毎分の起動）と docs/decisions/automation.md「2026-10-16（CHAT-1016-ACF-24）」の sync-from-mj の定常の実行で解消 |
| CHAT-1005-UNR-07 | 完了 | なし | a | #491 closed。#491 の 2026-10-10 のクローズのコメント（CHAT-1002-CLD-20）で tMwcjumwz-o がカレンダー上で直っていたことを確かめた |
| CHAT-1005-UNR-08 | 完了 | なし | a | #500 の未決は UNR-09〜13 で進め、#500 closed（2026-10-05 UNR-13）。「貼る時機」の欠けは記録だけで対応不要 |
| CHAT-1005-UNR-09 | 判断待ち（中断。4団体の公式サイトが読めないため手順1で止めた） | なし | a | 続きの UNR-10〜13 で4団体の一覧を読んで調査を進め、#500 closed（締めのコメント 2026-10-05） |
| CHAT-1005-UNR-11 | 判断待ち | なし | a | 9行・迷った3件は docs/decisions/live.md「2026-10-05」の節で反映済み（岡澤和洋 RMU・荒木一甫 元協会・千本松紘子 元RMU）。残り183名は UNR-12 で調査、#500 closed |
| CHAT-1005-UNR-12 | 判断待ち | なし | a | docs/decisions/live.md「2026-10-05（CHAT-1005-UNR-13）」: 7行は反映、不明262名・調べられない33名は今のまま、RMU の3名は RMU のまま。#500 closed |
| CHAT-1005-UNR-13 | 完了 | なし | a | 齋藤けーすけ（87行）は「使われていない登録」に出て 2026-10-11 に削除（docs/decisions/live.md「2026-10-11（CHAT-1011-STL-05）」、STL-01 の一覧）。登録名変更の手順は docs/notes/live-page-design.md「1-5」に記載。chat-side-operations.md は今 22,155 バイトで警告域外 |
| CHAT-1005-VRF-01 | 完了 | なし | a | 判断が必要なことは雛形の「貼る時機」の欠けの記録だけ。未確認・エラーともなし |
| CHAT-1005-WKR-01 | 完了 | なし | a | 平野さんの作業（Worker 作成・Secret 等）は済み、Worker は 10/6 から起動（CHAT-1006-WKR-03 の run #15、予定から36秒）。watch paths・Observability は WKR-05 で確認。docs/notes/scheduler-worker.md の記録 |
| CHAT-1005-WKR-02 | 完了 | なし | a | 文字列 "true" の起動は CHAT-1006-WKR-03 で確認（run #15、INPUT_SCHEDULED true・success） |
| CHAT-1006-LGR-06 | 完了 | なし | a | checklist の写しは CHAT-1006-LGR-08 で sync_guides.py の ALLOWED_PATTERNS に追加。#507・#508 closed |
| CHAT-1006-LGR-07 | 完了 | なし | a | docs/decisions/houou.md「2026-10-06（CHAT-1006-LGR-10）」: 24後 A1 のシートは修正済み、本番の見え方は OK。iPhone は「2026-10-07（CHAT-1007-LGR-14）」で OK。#507 closed |
| CHAT-1006-LGR-08 | 完了 | なし | a | sync-logs.yml の paths の論点は、写しが mj-logs 側へ移り mj の sync-logs.yml の push 契機が外れたため対象が無い（e34005916、docs/decisions/operations.md「2026-10-07（CHAT-1005-RVW-22）」） |
| CHAT-1006-LGR-09 | 完了 | なし | a | docs/decisions/houou.md「2026-10-06（CHAT-1006-LGR-10）」で LGR-09 の直しは本番で確かめて OK。iPhone は LGR-14 で OK |
| CHAT-1006-LGR-11 | 完了 | なし | a | iPhone・X のカードは docs/decisions/houou.md「2026-10-07（CHAT-1007-LGR-14）」で OK。houou_race.html は今 _redirects で /houou/race/ へ 301 のため、旧 URL の Search Console 作業は対象外。#507・#508 closed |
| CHAT-1006-PHT-02 | 完了（#499 は検知で自動クローズ。残る選手なし） | なし | a | #499 closed。未確認はシートの値の目視だけで、再生成後の URL は一致 |
| CHAT-1006-PHT-04 | 完了 | なし | a | #362・#428 のコメントの扱いは docs/decisions/operations.md「2026-10-07（CHAT-1007-PHT-16）」でこのままでよいと決定。#510・#511 closed |
| CHAT-1006-WKR-03 | 完了 | なし | a | observability は CHAT-1006-WKR-05 で追加。watch paths の Include は 10/6 11:20 に直り（docs/notes/scheduler-worker.md の表）、WKR-05 で効きを確認。06:00 の回は scheduler-worker.md の 10/8 の記録 |
| CHAT-1006-WKR-05 | 完了 | なし | a | プレビューで Exclude workers/** が効かない理由は docs/notes/cloudflare.md の表・scheduler-worker.md で解明（非本番のトリガーに無い、2026-10-12 平野さんの決定で受け入れ）。Observability の行は scheduler-worker.md の 10/8 の記録 |
| CHAT-1006-WKR-06 | 完了 | なし | a | 判断1〜5は CHAT-1006-WKR-07 で決定（#504 本文の決定 9〜12、#505 closed、#509 起票後 2026-10-08 に不要で closed）。段階2は WKR-09・WKR-11/12 で実施。未確認の残りは WKR-07 に引き継ぎ |
| CHAT-1006-WKR-07 | 完了 | #504 の起動時刻の表について、平野さんが「連盟ch」のシートを編集する時間帯と update-live-channel（今は run-daily.yml の 04:00）の関係、連盟サイトの道場部の不調が早朝に起きやすいか、の2点が確かめられていない（sync-logs の遅れの点は対象外になった） | b | #504（open。起動時刻の表と段階を受け持つ。段階2の指示で確かめる予定だったが、確かめた記録が見つからない。道場部の不調は #503 にも関係） |
| CHAT-1006-WKR-08 | 完了 | なし | a | #509 は 2026-10-08 に作り替えで不要として closed（docs/decisions/automation.md「2026-10-08（CHAT-1008-WKR-10）」）。使用量は CHAT-1018-BIL-01 の決定で扱った |
| CHAT-1007-LGR-12 | 完了（cloudflare へマージ済み、本番で確認済み） | なし | a | X のカードは docs/decisions/houou.md「2026-10-07（CHAT-1007-LGR-14）」で OK。houou_race.html は今 /houou/race/ へ 301（_redirects）で、旧 URL の URL 検査は対象外 |
| CHAT-1007-LGR-13 | 完了（cloudflare へマージ済み、本番で確認済み） | なし | a | 判断が必要なことは選び方の説明だけ。check-run が付かない件は配信ファイルが変わらないため（本番の表示を確認済み） |
| CHAT-1007-PHT-10 | 完了 | なし | a | 論点は #298・#304・#142 にコメントで移し済み。同じ SHA で2回走る件は #298 の 2026-10-18 のコメント（CHAT-1018-BIL-01、案 A）で閉じた |
| CHAT-1007-PHT-11 | 完了 | なし | a | NEW_RULE_DATE は scripts/cleanup_logs.py で "2026-10-08" に設定済み。旧い続きの形のログは今回の仕分け（旧いログの削除）で片付ける |
| CHAT-1007-PHT-12 | 完了 | なし | a | scripts/ の2か所は docs/decisions/operations.md「2026-10-07（CHAT-1007-PHT-15）」で承認・実施（scripts/lib/yotei.py は permalink、HC-02・ZK-01 は削除済み）。リンクでない文字列・クローズ済み issue は同「CHAT-1007-PHT-12」の決定で書き換えない |
| CHAT-1007-WKR-09 | 完了 | なし | a | 10/8 の朝の起動とキャッシュの範囲は docs/notes/scheduler-worker.md「動いた記録（2026-10-08 の時点）」で確認（予定 2・success 2、cloudflare から作業ブランチのキャッシュは見えない） |

### 手順2・3: 書き写し・削除・マージ

- permalink の SHA は 4db85e095（削除のコミットの親。cloudflare へ fast-forward で入った）
- (b) のコメント（本文・状態・ラベルは変えていない）:
  - #308（CHAT-0930-ACT-01）: https://github.com/retroeater/mj/issues/308#issuecomment-6257502868
  - #390（CHAT-1002-DOJ-04）: https://github.com/retroeater/mj/issues/390#issuecomment-6257503440
  - #492（CHAT-1005-RVW-02）: https://github.com/retroeater/mj/issues/492#issuecomment-6257504207
  - #298（CHAT-1005-RVW-22）: https://github.com/retroeater/mj/issues/298#issuecomment-6257505257
  - #504（CHAT-1006-WKR-07）: https://github.com/retroeater/mj/issues/504#issuecomment-6257505916
- (c) の起票: #610「旧い作業ログから移した未済事項（2026-10-19）」（ラベルなし。CHAT-0930-BNG-06 の1行）: https://github.com/retroeater/mj/issues/610
- 削除: 8593ce348「chore: remove 121 old-format work logs」（121ファイル、親 4db85e095）。メッセージに閉じるキーワードと `#番号` の並びが無いことを grep で確かめた
- マージ: 10:10 JST（月曜 06:00〜09:00・03:45〜05:15 の外）。push 直前に `git fetch origin cloudflare` と `git merge-base --is-ancestor origin/cloudflare HEAD`（真）を確かめ、`git push origin work/1019-acf-old:cloudflare`（596809461..8593ce348、fast-forward）
- mj-logs の写し: CHAT-0929-SH-18・CHAT-1005-RVW-22・CHAT-1007-WKR-09・CHAT-1002-CLD-15 の raw が 404 になった（消えた）。対象から外した CHAT-1002-INV-01 は 200 のまま
- #513 に削除の件数のコメント: https://github.com/retroeater/mj/issues/513#issuecomment-6257510638
- docs/decisions: 指示の「決定」節（2026-10-19）を docs/decisions/operations.md に足した

カレンダーが名指ししているログ:

| ログ | 今の状態 | permalink |
| --- | --- | --- |
| CHAT-1005-RVW-22 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1005-RVW-22.md |
| CHAT-1005-RUN-06 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1005-RUN-06.md |
| CHAT-1005-RUN-08 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1005-RUN-08.md |
| CHAT-1005-RUN-10 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1005-RUN-10.md |
| CHAT-1006-WKR-07 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1006-WKR-07.md |
| CHAT-1005-RVW-04 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1005-RVW-04.md |
| CHAT-0930-DUP-03 | 今回削除 | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-0930-DUP-03.md |
| CHAT-1002-INV-01 | 残っている（`docs/notes/handover-archive-2026.md:966` が名指ししているため対象から外した） | https://github.com/retroeater/mj/blob/4db85e095f2bef0a559b0d24246f7318c3b0e3f9/docs/logs/CHAT-1002-INV-01.md |
| CHAT-0924-TQ-28 | 既に無い（dddb1568d で削除） | https://github.com/retroeater/mj/blob/d1f0d6efbe21ef302802242665bc6449e7fa9e38/docs/logs/CHAT-0924-TQ-28.md （参考: 削除の直前の版） |
| CHAT-0928-SC-04 | 既に無い（b2581dfdf で削除） | https://github.com/retroeater/mj/blob/347eeeca19a7d76661845c6685dbf3f7ae51e1e5/docs/logs/CHAT-0928-SC-04.md （参考: 削除の直前の版） |
| CHAT-0928-SC-08 | 既に無い（55e218fc3 で削除） | https://github.com/retroeater/mj/blob/53c718beba0889de9f73b43560bb4d94cb71ffcb/docs/logs/CHAT-0928-SC-08.md （参考: 削除の直前の版） |
| CHAT-1007-PHT-14 | 既に無い（f0204bea8 で削除） | https://github.com/retroeater/mj/blob/921dbe0ad2a077f0d2fb88b8c1f0aeb604028aba/docs/logs/CHAT-1007-PHT-14.md （参考: 削除の直前の版） |

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-acf-old
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-ACF-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-acf-old
- 確認用URL: なし
- マージ: 済（8593ce348。cloudflare へ fast-forward、docs/logs の削除のみ。このログと決定は追いの push）
- issue: コメント #308・#390・#492・#298・#504・#513（着手中・削除の件数）、起票 #610
- 判断が必要なこと:
  - `docs/logs/`・`docs/decisions/` 以外で名指しされていたため残した旧いログ23件（`## 経過`「手順1: 対象の一覧」の表）の扱い。名指しの多くは出典としての Chat-Ref（コードのコメント・テストの文字列・docs/notes の事例）で、ログを読む先として指しているのは `docs/notes/handover-archive-2026.md:966`（INV-01〜04）・`docs/notes/saikyo-page-design.md:649`（PHT-06）など一部。案: 出典だけのものは名指しとみなさず次の指示で仕分けて削除し、読む先として指すものは permalink に書き換えてから削除する（docs/notes・scripts を変えるので別の指示）。残すと 10/26 以後も #357 の②・①に出続ける
- 未確認の項目: なし
- エラー: なし

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
