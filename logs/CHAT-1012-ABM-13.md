# CHAT-1012-ABM-13

- 着手日時: 2026-10-12
- 対象issue: #425
- ブランチ: work/1012-abm-map
- 着手時HEAD: 1a0da912fe6ae7a333b8e3f827357a431a7e6aa6

## 指示

【Claude作成】Claude Code 向け指示：#425 ABEMA ブックの【2】（卓ごとの動画の割り当て）と【3】を作り、毎朝作り直し、生成が【2】＋【3】から動画の URL を読むように切り替える Chat-Ref: CHAT-1012-ABM-13 マージ: 判断待ちで止まる（2011〜2025年度の卓・対局のページにサムネイルが増えるので、平野さんがプレビューを見てから決める） 貼る時機: いつでも（平野さんは ABEMA ブックの「【1】元データ」を14列の表に置き換え済み、サービスアカウント `live-channel-writer` へ編集者として共有済み〈いずれも 2026-10-12 の申告〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1012-abm-map を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-abm-map origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1012-ABM-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1012-ABM-13` を足す。

目的
CHAT-1012-ABM-10 の割り当ての規則で、ABEMA ブック（ID `1umiz7Sb1ZV76qpFtFEptWsH5dPCQMFPpTNsF5vLJrwc`）の【2】に「最強戦」の卓ごとの ABEMA の回を自動で割り当て、【3】で平野さんが上書きできるようにする。生成（`generate_saikyo_pages.py`）は「最強戦」シートの M 列ではなく【2】＋【3】から動画の URL を読む。卓のページの見た目（実況・解説・司会の表示）はこの指示では変えない。
決定（平野さん）

* （2026-10-12）ABEMA の元データは【1】、【2】に卓ごとの動画の URL を自動で割り当て、補正が要る値だけ平野さんが【3】で上書きする。【1】は平野さんが CSV を取り込み、【2】は Actions が【1】と「最強戦」シートから毎朝作り直す。書き込みはサービスアカウント `live-channel-writer`（2026-10-10 の「書ける先を SNS ブックに限る」を、このブックにも広げる）
* （2026-10-12）割り当ての規則は CHAT-1012-ABM-10 のログ「手順3: 規則の案」のとおり
* （2026-10-12）当たらない回（67回）は【2】に「割り当てなし」の行として残す
* （2026-10-12）2016 の ABEMA「著名人代表決定戦 麻雀代理戦争」（`68-38_s16_p16`〜`p18`）は、シートの「著名人代表決定戦」（2016-05-21）と、ABEMA の【1】の「対局者など」に出る名前が重なれば当て、重ならなければ別の対局（割り当てなし）として載せる（ABEMA のページに日付が無かったため、日付の代わりに出場者で確かめる）
* （2026-10-12）片方にしか無い対局・卓の扱い（シートに対局を足すか、別の動画を当てるか）は後回し
* （2026-10-12）「最強戦」シートの M「動画URL」の18卓は【3】へ移さない（自動の割り当てが18 / 18で一致したため）。生成を【2】＋【3】に切り替えた後に M 列を空にする（列は残す。空にするのは平野さん）
* （2026-10-12）実況・解説・司会は ABEMA の出演者の一覧から取り、ABEMA ブックに入れて必要に応じて平野さんが補正する（表示は後の指示）

前提（チャット側。平野さんの決定ではない）

* 【1】元データは14列・670行（要確認）: 年度・回のID・URL・題名・og:title・og:image・サムネイルのHTTP・対局者など・司会・実況・解説・その他の役・出演の一覧（ABEMA のまま）・ページの日付。「対局者など」「司会」「実況」「解説」はチャット側のスクリプトが ABEMA のページの一覧から読み分けた値で、書き方の違い（例「<司会>小山剛志」が1つの項目）で読み分けられていない回がある。読み分けは【2】を作るときに「出演の一覧（ABEMA のまま）」から読み直してよい（読み分けの規則をログに書く）。「ページの日付」は342回にだけあり、全ページに出る無関係な日付は除いてある
* タブ: 【1】元データ・【2】最強戦・【3】手動補正は既にある（CHAT-1012-ABM-10）
* 【2】の形の案（実物に合わせて変えてよく、変えたらログに書く）: 1行1卓（643行）＋割り当てなしの回（67行）。列は 年度・対局日・対局・ステージ・卓・回のID・URL・ABEMA の題名・司会・実況・解説・割り当て（自動／なし）・備考。【3】は同じキーの列（年度・対局日・対局・ステージ・卓）と、上書きする URL・司会・実況・解説・備考。【3】の空のセルは上書きしない（SNS ブックの【4】と同じ考え方、docs/notes/sns-book.md）
* 毎朝の作り直しは、毎朝の最強戦の再生成（`regenerate-saikyo.yml`）の前に【2】を作り直す形が簡単そうに見える（新しい予約実行を足さない）。ほかの形がよければ理由を書いて選んでよい。予約実行の起動の仕組みは docs/notes/scheduler-worker.md
* 書き込みの仕組みは SNS ブックの毎日の更新（`update-sns-book.yml`・docs/notes/sns-book.md・docs/notes/live-channel-write.md）にそろえる
* 生成を【2】＋【3】に切り替えると、2011〜2025年度の卓のページ・対局のページにも ABEMA のサムネイルと「▶ 再生」が出る（見込み: 割り当てられた卓 603。CHAT-1012-ABM-10 のログ「年度ごとの結果」の表の計）
* CHAT-1012-ABM-12（卓のページの直し）が work/1012-abm-ui で並行していることがある。`generate_saikyo_pages.py` の同じ関数を変えるときは止まる条件のとおり

手順

1. 確かめる: ABEMA ブックの【1】の見出し・行数（670）・年度ごとの行数を生成と同じ経路で読む。【2】【3】のタブの今の中身を書く。サービスアカウントで ABEMA ブックに書けるかを、作業ブランチでのワークフローの手動実行（書くのは【2】だけ）で確かめる。`git branch -r --no-merged origin/cloudflare` の各ブランチとの重なりを確かめる
2. 作る: 割り当ての規則と2016の確かめ（出場者の重なり）を実装し、【2】を書くスクリプトとワークフロー（毎朝の作り直し）を作る。【3】に見出しを書く（中身は空）。生成の動画の URL の読み先を M 列から【2】＋【3】に切り替える（M 列は読まなくなる）。テストを足し（規則・2016の確かめ・2026年度の18 / 18・【3】の上書き）、`python3 -m unittest discover -s scripts/tests` を通す。作業ブランチで手動実行し、【2】の行数（卓 643・割り当てなし 67、見込みと違えばその理由）と、割り当てられた卓の数（見込み 603 ±5）を書く。docs/notes/saikyo-page-design.md と、ABEMA ブックの説明の文書（新しく作るなら docs/notes/ の新しいファイル、static-generation.md「ワークフローの一覧」）を書く。決定を docs/decisions/saikyo.md に足す
3. 確かめてプレビューに出す: 生成して、変わったファイルを種類ごとに数える（サムネイルの入った卓のページ・対局のページ。年度ページ・今の公開中のファイル・live/ に差分が無いこと）。組み立てたサムネイルの URL の形が 2026年度の今のものと同じであること。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2016・2020・2023 の卓のページを1つずつと、2016 著名人代表決定戦の卓のページ（当たったか、当たらなかったか）を並べる。報告に、平野さんへの知らせ（M 列を空にするのはマージの後）を書く

止まる条件

* 【1】の見出し・行数が前提と違う（読めた事実を書いて止まる）
* サービスアカウントで ABEMA ブックに書けない（書けない理由を書いて止まる）
* 割り当てられた卓が 603 から ±5 を超えてずれる、2026年度が M 列の18卓と一致しない、1つの卓に2つ以上の回が当たる
* 年度ページ・今の公開中のファイル・live/ に差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
* 未マージの work/ ブランチが `generate_saikyo_pages.py` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-ABM-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-ABM-13 を書く

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

ガイド文書（この版を写した時点の最新、mj 387505ef）: https://github.com/retroeater/mj-logs/tree/main/guide/387505ef

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
