# CHAT-1020-PLG-09

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: be1570fae

## 指示

【Claude作成】Claude Code 向け指示：hookify の規則を「sitemap*.xml に lastmod を書くときだけ注意」で確定し、hook の層の分け方を文書にして、平野さんのコミット手順を用意する
Chat-Ref: CHAT-1020-PLG-09
マージ: 判断待ちで止まる（`.claude/` の規則ファイルは平野さんが GitHub の画面でコミットし、PR〈Create a merge commit〉でマージする。docs/notes/skills.md「git の hook」）
貼る時機: いつでも（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1020-plg を続けて使う（CHAT-1020-PLG-08 の決定の記録とログが載っており、平野さんの PR でまとめてマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1020-PLG-08 のログの `## 報告` を読み、状態が判断待ちで、work/1020-plg が cloudflare に未マージであることを確かめる（食い違えば止まる）。

## 目的
CHAT-1020-PLG-08 の判断待ちへの回答の続き。hookify の最初の規則を、PLG-08 の `## 経過` 2. の代わりの案（`sitemap*.xml` に `<lastmod>` を書くときだけ注意）で確定する。PLG-08 で行わなかった手順 3（docs/notes/skills.md「hook の層の分け方」の節、平野さんの手作業の手順）を行う。セッションは `.claude/` を変えない。

### 決定（2026-10-20、平野さん）
- hook の層の分け方: **止める**規則はリポジトリの hook（mj-git-guard と同じ Python・テスト付き）かテスト・CI に置く。**注意を出す**規則は hookify に置く（PLG-08 の決定のまま）
- hookify の最初の規則は PLG-08 の `## 経過` 2. の案とする: `sitemap*.xml` のどれかを Edit・Write するとき、書く内容に `<lastmod>` を含むときだけ注意（warn）。ファイル名 `.claude/hookify.warn-sitemap-lastmod.local.md`。PLG-08 の決定の「sitemap.xml を Edit・Write したら注意」を置き換える（公開の段の `sitemap.xml`・`sitemap-pages.xml` の手編集は正しい作業のため）
- 1〜2 週間使って振り返る（カレンダー「ryoei.pro」の 2026-11-04、作成済み）

### 前提（チャット側。平野さんの決定ではない）
- 規則ファイルの全文と試験の結果は PLG-08 のログ `## 経過` 2. にある。全文は変えずに使う（変える必要があれば理由を書く）
- `.gitignore` は `*.local.md` を無視していない（PLG-08 で確認済み）。mj では規則を共有するので `.gitignore` に足さない
- 「知見 → 選んだ層 → 理由」の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| hook の層の分け方（止める規則と注意の規則） | ガイド文（docs/notes/skills.md の 1 節） | 規則を置く場所の方針で、機械では確かめられない。CLAUDE.md には足さない（容量） |
| sitemap の lastmod を手で書かない | hookify の warn（既存の禁止事項の補助） | 試行。振り返りで、止める規則（リポジトリの hook かテスト）へ格上げするかを決める。CLAUDE.md「禁止事項」の行はそのまま残す（warn は強制ではないため） |

## 手順
1. docs/notes/skills.md の今の内容（「plugin の扱い」「git の hook」「置き場所と永続性」）を読み、「hook の層の分け方」の節を足す。内容: 決定の 1 つ目の箇条、hookify の規則の置き場所（`.claude/hookify.<名前>.local.md`、カレントディレクトリからの相対パスで探す、`.gitignore` に足さない）、規則の一覧（この 1 本。対象・条件・warn・振り返りの日）、規則を足す・止める規則へ格上げするときの目安。同じ趣旨の記述があれば置き換え・拡張する。docs/decisions/operations.md の PLG-08 の決定のうち規則の対象の記述を、上の決定で置き換える（置き換えた旨を書く）。
2. ログの `## 経過` に、平野さんがコミットする規則ファイルの全文（ファイル名つき）をコードブロックで置く（PLG-08 の `## 経過` 2. と同じもの）。
3. ログの `## 報告` の「判断が必要なこと」に、**平野さんの手作業の手順**を番号つきで書く（各リンクは直接開ける URL）: (a) work/1020-plg の `.claude/` を開くリンク (b)「Add file → Create new file」、ファイル名 `hookify.warn-sitemap-lastmod.local.md`（`.claude/` の中）、手順 2 の全文を貼る、「Commit directly to the work/1020-plg branch」を選ぶ（「Create a new branch」や cloudflare を選ばない） (c) cloudflare への PR を作るリンク（比較 URL）と、「Create a merge commit」でマージすること (d) PR の差分に出るはずのファイルの一覧（規則ファイル 1 つ、docs/notes/skills.md、docs/decisions/operations.md、PLG-08・PLG-09 のログ。ほかが出たらマージせず知らせる）。CHAT-1020-PLG-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1020-PLG-09` を足す。

## 止まる条件
- 手順 0 の確かめが食い違うとき
- 手順 1 で docs/notes/skills.md の記述と決定が矛盾し、判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
- `.claude/` を変える必要が出たとき（変えずに止まる）
- （未マージの work/ ブランチとの重なり）ほかの未マージの work/ ブランチが docs/notes/skills.md の同じ節・docs/decisions/operations.md の同じ行を変えている、または取り込みで衝突する（両立する衝突は上の「作業ブランチ」の行のとおり解いてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない。平野さんの PR でマージする）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/work/1020-plg/docs/logs/CHAT-1020-PLG-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 03eef856）: https://github.com/retroeater/mj-logs/tree/main/guide/03eef856

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
