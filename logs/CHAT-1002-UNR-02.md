# CHAT-1002-UNR-02

- 着手日時: 2026-10-03
- 対象issue: #475
- ブランチ: work/1002-unr
- 着手時HEAD: bb0fca60

## 指示

【Claude作成】Claude Code 向け指示：#475 の未登録の名前の今の全件を、「連盟プロ以外」「別名」に貼り付けられる形でログに書く（調査のみ。シート・コード・issue は変えない） Chat-Ref: CHAT-1002-UNR-02 マージ: 判断待ちで止まる（成果物はログの一覧のみ。シート・コード・生成物・issue は変えない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
常設 issue #475「/live の未登録の名前」の今の全件を、平野さんがスプレッドシートの「連盟プロ以外」（実在の人）と「別名」（誤記）にそのまま貼り付けられる形でログに書く。登録するのは平野さんで、この指示ではシートに書かない。CHAT-1002-UNR-01 は送る前に差し替えたため欠番。
決定（2026-10-02、平野さん）

* #475 の /live の未登録の名前の直近の一覧を、「連盟プロ以外」に貼り付けられる形で受け取る

前提（チャット側。平野さんの決定ではない）

* #475 を Chrome で読んだ（2026-10-02）: Open。最新の bot のコメントは「55名 → 56名、新しく出た名前: 村越一郎(2行)」。本文は初回の 173名（上位30名）のままで、今の全件は issue に無い。全件は「【2】自動変換後」の「理由」列から読む（`scripts/write_live_channel_candidate.py` の `unknown_names()`。#475 の 2026-09-30 のコメント）
* 「連盟プロ以外」の見出しは docs/notes/live-page-design.md「1-4」では 名前 / 名前かな / 所属団体 / 所属補足 / X ID / X画像URL、「別名」は 変換前 / 変換後 / 区分 / 備考。シートの今の列の並びはチャット側では確かめていない
* 未登録の名前には読み違い（CHAT-0929-SH-15 のログの例:「解説：」「藤居冴加 ※放送卓」「◎A卓 越後良太」「A卓予選:高宮まり」「1卓:滝沢和典」「1:西野拓也」）が混ざる。これらは「連盟プロ以外」に入れるものではない
* どの名前を実在の人として登録するかを決めるのは平野さん。下の分類は受け手の案として書く（解釈を事実として書かない。所属団体・かな・X ID は推測で埋めない）
* 層2の規則の改善の受け皿は #446 と読んでいる（CHAT-0930-OLT-06 のログ）が、同じ日に issue の再編成が別のチャットで進んでおり、番号が変わることがある。issue の番号は手順のとおり実物で確かめる
* 名前だけの行を「連盟プロ以外」に足すと、かな・所属団体などが空欄の行が増える（#396「公開行の連盟プロ以外のかな等を埋める」の対象。CHAT-1002-INV-01 のログの分類表）。この指示では埋めない
* 貼り付け用の形は、コードブロックの中のタブ区切り（1行=1名、見出しの行は付けない）。Google スプレッドシートにそのまま貼れる形にする

手順

1. 読む: #475 の Open/Closed と最新のコメントの人数を確かめる。毎日の取り込みと同じ読み方で「【2】自動変換後」を読み、「理由」列から未登録の名前の全件（名前・行数）を取る（【2】の行数と、取れた人数を書く。#475 の最新の人数と違っても【2】の実物を正として進め、差の名前を報告に書く）。「連盟プロ以外」「別名」を生成と同じ経路で読み、今の見出しの並び（左からの列順）と行数を書く。あわせて、生成・検査（docs/notes/live-page-design.md「1-5」、`lib/live.py` の `OTHER_HEADERS` を読む所）が「連盟プロ以外」の新しい行に求める値（名前だけでよいか、所属団体などが空欄だと止まる・警告になるか）をコードで確かめて書く。
2. 分ける: 全件を次の3つに分け、1名ずつ「名前・行数・例の動画（動画ID・タイトル・配信日。【1】元データか層1から1本）・分類・根拠1行」の表にする。(a) 実在の人らしい名前（そのまま「連盟プロ以外」に足す候補。世界選手権の海外選手などの短い名前も、ほかに根拠が無ければここ。迷うものは (a) に入れて根拠に「要確認」と書く）(b) 誤記らしい名前（「プロ」「連盟プロ以外」に1〜2字違い・異体字・空白や記号の違いだけの名前がある。直す先の名前を書く）(c) 読み違い（前置き・卓名・記号・見出しが付いたもの、名前でないもの。中に含まれる名前が登録済みかどうかと、層2（【2】）の規則で直すものか【3】の補正で直すものかの案を書く。層2の規則の受け皿の issue は、その時点で Open のものを実物で確かめて番号を書く。#446 が Closed なら、そのクローズのコメントが指す後継の issue）。
3. 貼り付け用の形を書く: 「## 報告」の直前に見出し「貼り付け用」を作り、コードブロックを2つ書く。1つ目は「連盟プロ以外」用で、(a) の全件を、手順1で読んだ今の列順のタブ区切りで（名前の列だけを埋め、ほかの列は空欄。手順1で空欄にできない列が分かったら、その列に入れる値と理由をコードブロックの外に書く。行の並びは行数の多い順）。2つ目は「別名」用で、(b) の全件を今の列順のタブ区切りで（変換前=今の表記、変換後=直す先、区分=`訂正`）。(c) は貼り付け用にせず、手順2の表だけにする。コードブロックの後に、(a)(b)(c) の人数と合計（手順1の人数と一致すること）を書く。#475 にはコメントしない。

止まる条件

* 「【2】自動変換後」「連盟プロ以外」「別名」のどれかが読めない、必要な見出し（理由／名前／変換前・変換後・区分）が無い、行数が読み直すたびに変わる（件数を書いて止まる）
* #475 が Closed になっている、または同じ目的（未登録の名前の一覧づくり・登録）の他セッションの着手中コメント・未マージの work/ ブランチがある（`git branch -r --no-merged origin/cloudflare` で確かめる）
* (a)(b)(c) の合計が手順1の人数と合わない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なことに「(a) を平野さんが『連盟プロ以外』に、(b) を『別名』に貼る。(c) の扱い」を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-UNR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-UNR-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow origin` の後、`git log --all --grep="UNR"` は0件、`docs/logs/` に UNR の履歴なし、`origin/work/*unr*` なし
- `git checkout -b work/1002-unr origin/cloudflare` が auto モードの分類器に拒否された（理由「Interfere With Workloads」）。止まってターミナルで報告。
  平野さんの許可の返答（同じコマンドを1回だけ実行し直す）を受けて再実行し、通った

## 報告

- 状態: 作業中
- ブランチ: work/1002-unr
- ログ: https://github.com/retroeater/mj/blob/work/1002-unr/docs/logs/CHAT-1002-UNR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-unr
- 確認用URL: なし
- マージ: 未
- issue: #475
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー:
  - `git checkout -b work/1002-unr origin/cloudflare` が auto モードの分類器に拒否された（理由「Interfere With Workloads」）。平野さんの許可の後の再実行で通った

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5c0f5ffa）: https://github.com/retroeater/mj-logs/tree/main/guide/5c0f5ffa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5c0f5ffa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/85555f77.md
