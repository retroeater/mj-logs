# CHAT-1005-UNR-10

- 着手日時: 2026-10-05
- 対象issue: #500
- ブランチ: work/1005-unr
- 着手時HEAD: 3030674f（origin/cloudflare と同じ）

## 指示

【Claude作成】Claude Code 向け指示：#500「連盟プロ以外」の所属団体の調査の続き。4団体の公式サイトの一覧と突き合わせ、根拠の URL つきの提案の一覧をログに書く（シート・コードは変えない）
Chat-Ref: CHAT-1005-UNR-10
マージ: 承認済み（チャットで、2026-10-05。変更は docs のみ〈docs/logs・docs/notes〉。コード・シートを変える必要が出たらマージせず判断待ちで止まる）
貼る時機: 平野さんが、クラウドセッションの環境の許可ドメインに `saikouisen.com`・`npm2001.com`・`rmu.jp`・`mu-mahjong.jp` を足した後
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。作業ブランチは、リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-UNR-09 のログの `## 経過`「1.」と `## 報告` を読む。4団体の選手一覧の URL（https://saikouisen.com/members/ 、https://npm2001.com/player/ 、https://rmu.jp/cms/player_index 、https://mu-mahjong.jp/player/ ）を1回ずつ読み、4つとも読めなければ何もせず止まる（返ってきたエラーを書く）。#500 の本文、docs/notes/live-page-design.md「1-4」、#354 の CHAT-0919-LP-03 のコメントを読む。

## 目的
「連盟プロ以外」の所属団体のうち、調べずに `-`（どの団体でもない）としている行を、公式サイトなどの根拠で確かめる。他団体のプロを「どの団体でもない」と登録したままにしないため。CHAT-1005-UNR-09 は公式サイトが読めずに対象の一覧までで止まったので、その続き（突き合わせと一覧づくり）を行う。シートへ入れるのは、一覧を見た平野さん。

### 決定（2026-10-05、平野さん）
- #500 の調査は Claude Code が行う
- 所属補足に「元連盟」「一般」などの記載がある行は調査済みなので、対象から除いてよい
- 成果物は、その団体と判断した根拠（公式サイトの URL など）つきの一覧

### 前提（チャット側。平野さんの決定ではない）
- CHAT-1005-UNR-09 のログ（2026-10-05）: 「連盟プロ以外」は 764行。対象は所属団体 `-` で所属補足が空の 323行（名前と行番号はそのログと #500 の本文）。所属団体が4団体の行は 276行。4団体のサイトは許可外で読めなかった
- 所属補足が「元最高位戦？」の行が1行ある。「？」が付いているので、この1行は対象に足して調べる（チャット側の提案）
- 突き合わせの方法は LV-20 と同じ（NFKC のうえで空白と「・」を除き、異体字〈髙→高、﨑・嵜・碕→崎 など〉を同じ字とみなす。UNR-09 のログ）。RMU は一覧のページが複数ある（UNR-09 のログの URL）
- 4ドメインの外のページ（大会の公式ページなど）は、許可外で読めないことがある。読めないページは根拠にしない
- 調べるのは麻雀の団体への所属だけ。それ以外の個人の情報は集めず、ログにも書かない（ログは公開される）
- シートは UNR-09 の後に変わっているかもしれない。行番号は読み直した実物を正とする

## 手順
1. 読み直して突き合わせる: 生成と同じ経路で「連盟プロ以外」を読み（2回読んで行数が同じこと）、対象（所属団体 `-`・所属補足が空、と「元最高位戦？」の1行）の件数が UNR-09 の 323行＋1行と合うかを書く（違えば増減した名前を書いて、実物で進める）。4団体の一覧を読み、団体ごとの人数と、対象の名前との一致（名前・行番号・団体）を書く。所属団体が4団体の 276行は、その団体の一覧に今も同じ名前があるかだけを突き合わせ、無い人を一覧にする（1人ずつは調べない）。
2. 1人ずつ確かめる: 一覧に同じ名前があった人と、10/3 に足した38名（#500 の本文）は、1人ずつ根拠を確かめる（その団体のサイトの一覧・プロフィールのページを優先。その人が出ている /live の動画の題名・概要欄・時期と合うかを見て、同名の別人でないかを確かめる）。この指示で1人ずつ調べるのは50名まで（一覧に一致した人を先に、次に38名。残りは件数と名前だけ書く）。検索のツールが使えるなら根拠探しに使ってよいが、根拠として書くのは自分で開いて内容を確かめられたページの URL だけにする。
3. 一覧を書いて止まる: ログに、調べた人ごとの表（行番号・名前・今の所属団体・今の所属補足・提案する所属団体・提案する所属補足・根拠の URL・確かさ・出ている動画ID の例1つ）を書く。確かさは「高: 公式の一覧に名前があり、動画の題名・概要欄・出場枠でも同じ団体と分かる」「中: 公式の一覧に同じ名前があるだけ」「低: 公式でない情報源だけ」「不明: 根拠が見つからない」「調べられない: 海外選手・名前の一部だけなど」の5つ。退会・移籍などで今は所属していない人の書き方は、今のシートの所属補足の例（「元連盟」など）に合わせて提案する。表の後に「貼り付け用」として、今と違う値を提案する行だけを行番号順に（行番号・名前・所属団体・所属補足）書く。#500 に、件数のまとめとログの該当の節への案内を1件のコメントで書く。docs/notes/cloud-sessions.md「ネットワーク」の許可ドメインの一覧に、読めた4ドメイン（足した日・用途・この指示で読めたこと）を足す。

## 止まる条件
- 「連盟プロ以外」が読めない、必要な見出し（名前・所属団体・所属補足）が無い、行数が読み直すたびに変わる（件数を書いて止まる）
- #500 が Closed になっている、#500 に他セッションの着手中コメントがある、同じ目的の未マージの work/ ブランチがある（`git branch -r --no-merged origin/cloudflare` で一覧を出して確かめる）
- 4団体のうち一部だけ読めない（読めた団体の分だけ進め、読めなかった団体と URL・エラーを「判断が必要なこと」に書く。別の経路で回り込まない）
- 根拠が見つからない人を、推測で団体に割り当てない（「不明」と書く。止まらない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。「判断が必要なこと」に、平野さんがシートへ入れる行の件数、残りの人数、次の回で調べる範囲の案を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-UNR-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-UNR-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep="CHAT-1005-UNR-10"` は0件。ローカルの `work/1005-unr` は origin/cloudflare・`origin/work/1005-unr` と同じ 3030674f（マージ済み）で、そのまま使う
- 手順0: 「指示」欄の末尾の行は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている

## 報告

- 状態: 作業中
- ブランチ: work/1005-unr
- ログ: https://github.com/retroeater/mj/blob/work/1005-unr/docs/logs/CHAT-1005-UNR-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-unr
- 確認用URL: なし
- マージ: 未
- issue: #500
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3030674f）: https://github.com/retroeater/mj-logs/tree/main/guide/3030674f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3030674f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
