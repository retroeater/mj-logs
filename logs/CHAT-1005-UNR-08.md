# CHAT-1005-UNR-08

- 着手日時: 2026-10-05
- 対象issue: #362・#354・#396・#491（起票1件の予定）
- ブランチ: work/1005-unr
- 着手時HEAD: 517e062b（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」の所属団体を確かめる issue を起票し、UNR のチャットの振り返りの申送りを文書と issue に書く（docs と issue のみ）
Chat-Ref: CHAT-1005-UNR-08
マージ: 承認済み（チャットで、2026-10-05。変更は docs のみ〈docs/notes・docs/logs〉。コード・シートを変える必要が出たらマージせず判断待ちで止まる）
作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-03 に「連盟プロ以外」へ足した39行のうち38行は、所属を調べずに所属団体へ `-`（どの団体でもない）を入れた。ほかの行も含めて所属団体が実際と合っているかを確かめる作業を、issue として残す。あわせて、このチャット（UNR）の振り返りで出た、次の会話でも同じつまずきを防げる事実を文書と issue に書く。

### 決定（2026-10-05、平野さん）
- 「連盟プロ以外」の所属団体を確かめる issue を起票する。対象は 10/3 に足した38名に限らず、「連盟プロ以外」の全行
- 進め方: Claude が調査して提案し、その団体と判断した根拠（公式サイトの URL など）を一覧にする
- 振り返りの内容を申送りする（「申送り」）

### 前提（チャット側。平野さんの決定ではない）
- 10/3 に足した39行: 所属団体は `-` が38、`最高位戦` が1（海老沢稔）。`-` はチャット側が貼り付け用の形に入れた値で、所属は調べていない（CHAT-1002-UNR-02・CHAT-1003-UNR-03 のログ）
- 近い issue が既にある: docs/notes/live-page-design.md「1-4」に、所属団体が `-` の人を4団体の公式サイトの選手一覧と突き合わせて埋めた作業（LV-20）、申し送り #362、所属団体をページに出す #354（「団体の一覧との突き合わせだけで判断しない」というコメントがある）、かな等を埋める #396。どれも実物で Open/Closed と本文を確かめる
- 調査する Claude がチャット側か Claude Code か、調べた結果をシートへ入れるのが誰か、期限は、決まっていない。issue には未決として書く（決めない）
- 申送りに書くのは、機械的に確かめられる事実と手順だけ（docs/notes/chat-side-operations.md「Claude Code とのやり取り」の「申送り」）。事例の経緯は書かず、規則だけを書く

## 手順
1. 起票: #362・#354・#396 と、「連盟プロ以外」「所属団体」で検索した issue（Open・Closed）を読み、同じ目的（「連盟プロ以外」の所属団体を確かめる）の Open の issue があるかを書く。無ければ起票し、あればその issue の本文に今回の範囲と進め方を足す（新しく起票しない。どちらにしたかと理由を報告に書く）。本文に書くこと: 目的、対象（「連盟プロ以外」の全行。今の行数と、所属団体の値ごとの件数を生成と同じ経路で読んで書く）、きっかけ（上の前提の1つ目。該当の38名の名前の一覧）、進め方（決定の2つ目。成果物は「名前・今の所属団体・提案する所属団体・根拠の URL・確かさ」の一覧。#354 のコメントの注意を引用）、値の決まり（docs/notes/live-page-design.md「1-4」）、未決のこと（前提の3つ目）、関連（#362・#354・#396・#475）。ラベル・題の付け方は CLAUDE.md と今の issue の例に合わせる。#396 に、新しい issue との分担を1件のコメントで書く。
2. 申送り（文書）: 追記先の今の内容とサイズを読み、同じ趣旨の記述があれば足さずに置き換える。(1) docs/notes/chat-side-operations.md「Claude Code とのやり取り」と docs/notes/cloud-sessions.md「作業ブランチの用意」の、分類器の拒否の理由の例に「Interfere With Workloads」を足す（CHAT-1002-UNR-02 の `git checkout -b work/1002-unr origin/cloudflare`。対処は同じで、許可の返答で通った）。(2) docs/notes/chat-side-operations.md「指示文を書くときの注意」の合う節に: マージの後にワークフローを手動実行して確かめさせる指示では、変更が届く先（【2】・予定表・公開カレンダー・生成物）ごとに要る入力を docs/notes/yotei-sheet.md「手動実行」で確かめて指定する。カレンダーの件名・説明文が変わる変更では `calendar_apply` を含める（含めないと反映は次の定時の実行になる）。(3) docs/notes/live-channel-write.md「【3】の扱い」に: 【3】の今の見出しの並び（列の記号つき。シートを読んで書く）と、手で行を貼るときは「参考:」の数式の列を避けて、その左と右に分けて貼ること。(4) docs/notes/live-page-design.md「1-4」に: 「連盟プロ以外」に行を足すとき、所属団体が空欄の行は使われず未登録のまま残ること（コードの該当箇所を確かめてから書く）と、所属が分からないときの扱い（`-` を入れ、手順1の issue の対象にする）。
3. 記録してマージ: #491 に1件コメントする（CHAT-1004-UNR-06 のログ `## 報告`「未確認の項目」から引用: 概要欄だけから作る予定〈/live の【2】【3】にレコードが無い動画〉の名前には「別名」の訂正をかけていない。今の計画でその形の重複は出ていない。直すかは決めていない）。CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md のサイズを CLAUDE.md「CLAUDE.md / handover.md の更新ルール」のとおり確かめ、冒頭の「マージ:」の行のとおり cloudflare へ入れる。ログの `## 経過` に、足した記述の前後（ファイル・節・足した文）と、足さなかったもの・置き換えたものを書く。

## 止まる条件
- 手順1 で、同じ目的の Open の issue が複数あり、どれに足すか決められない（一覧を書いて判断待ち。手順2・3 は進める）
- 追記で docs/notes/chat-side-operations.md などが容量の上限を超える（超える分を書かずに、どの記述を archive へ移せば入るかの案を書いて判断待ち。入る分は書く）
- 追記先の今の記述が、足す内容と矛盾している（同じ趣旨の記述があるだけなら止めず、置き換えてよい。どう処理したかを報告に書く）
- 同じ文書の同じ節を変えている未マージの work/ ブランチがある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
- cloudflare への push が権限の判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した、または足した issue の番号と URL を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-UNR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-UNR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1005-UNR-08"` は0件。ローカルの `work/1005-unr` は `origin/work/1005-unr`（4f3ae3b5）と同じで origin/cloudflare の祖先（マージ済み）。
  docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」のとおり `git merge --ff-only origin/cloudflare`（517e062b）
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1005-unr
- ログ: https://github.com/retroeater/mj/blob/work/1005-unr/docs/logs/CHAT-1005-UNR-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-unr
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 517e062b）: https://github.com/retroeater/mj-logs/tree/main/guide/517e062b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/517e062b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
