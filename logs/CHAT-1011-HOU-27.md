# CHAT-1011-HOU-27

- 着手日時: 2026-10-11
- 対象issue: #540・#542
- ブランチ: work/1011-hou
- 着手時HEAD: 6b7bb050

## 指示

【Claude作成】Claude Code 向け指示：`houou/` の公開（#540）について、平野さんの決定（iPhone の文字の拡大の確かめは済み・navbar は「鳳凰戦」1項目でトップへ・公開はデータ・コンシェルジュ〈#542〉と一緒に）を #540 と文書に記録する（文書のみ。コードは変えない） Chat-Ref: CHAT-1011-HOU-27 マージ: 文書のみの変更なので、CLAUDE.md「ブランチ運用」のとおり完了報告のうえマージしてよい 貼る時機: CHAT-1011-HOU-26 の完了の後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1011-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1011-HOU-26 のログの `## 報告` を読み、状態が「完了」であることを確かめる（でなければ何もせず止まる）。 #540 と #542 を読み、他セッションの着手中コメントが無いことを確かめる（あれば止まって報告する。CLAUDE.md「issueの着手ルール」）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
`houou/` の公開（#540）を進める前に、平野さんが決めたことを #540 と文書に残す。公開の作業そのもの（navbar・転送・sitemap・`llms.txt`・noindex を外す）はこの指示ではしない。コード・生成物は変えない。
決定（2026-10-11、平野さん）

* 公開の条件の「iPhone での文字の拡大（Chrome で最大）で5ページが崩れないこと、演出の見え方」の確かめは OK（済み）
* navbar の「鳳凰戦」は、押すとトップ（`houou/`）へ飛ぶ1項目だけにする（個人成績・ランキング・リーグ推移・順位変動の項目は navbar に並べない）
* 公開は #540 で行い、AI に言葉で質問できる機能（データ・コンシェルジュ、#542）までを公開に含める（#542 ができてから #540 で公開する）

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。

* #540 にコメントする（`--body-file`、末尾に `Chat-Ref: CHAT-1011-HOU-27`）。内容: 上の「決定」3点。本文のチェックリストのうち、iPhone の確かめを済みにし、navbar の項目の仮置き（5項目）を「『鳳凰戦』1項目でトップへ」に直す（本文の編集は `--body-file`。直した行をログに引用する）。#542 を公開の前提として本文に1行足す（例「公開は #542〈データ・コンシェルジュ〉ができてから」）
* #542 に、#540 の公開に含めることになった旨を1行コメントする（末尾に `Chat-Ref: CHAT-1011-HOU-27`）
* 下層ページ（個人成績・ランキング・リーグ推移・順位変動）から `houou/` のトップへ戻るリンクは、今は本文の中に無い（チャット側が `git grep` で確かめた。戻る道は navbar の「鳳凰戦」になる）。実物で確かめ、無ければ #540 の本文の「公開の段」に「下層ページからトップへ戻る導線（見出しのカードをトップへのリンクにする等）を、公開の前に要否を決める」を1行足す（作らない。仮置き）
* `docs/notes/houou-top.md`「非公開の形と公開の方針」の「公開の段」を、上の決定に合わせて直す（navbar の項目・iPhone の確かめ済み・#542 を待つこと）。仮置きの一覧から navbar の項目を外す
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の15個目の節として足す（navbar の仮置き〈5項目〉を置き換える決定。README の書き方で印）
* `docs/handover.md` の「次にやること」に `houou/` の項目があれば、「公開（#540）は #542 の後」と分かるように1行で直す（追記先の今の内容を読んでから。上限は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）

手順

1. 0章の確かめ。#540 の本文の編集とコメント、#542 へのコメント
2. 文書（houou-top.md・decisions・handover）の更新。push する
3. 文書のみの変更なので、CLAUDE.md「ブランチ運用」の手順で cloudflare へマージする（push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでからその手順のとおり

止まる条件

* 0章の確かめが通らない（HOU-26 が完了でない、#540・#542 に他セッションの着手中コメントがある）
* 差分に docs/ 以外のファイルが入る
* cloudflare か作業ブランチへの push、issue の編集が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜26 から続いている（新しく始めたセッションではない）**
- `CHAT-1011-HOU-27` のコミットは無し。`origin/work/1011-hou` はマージ済み。ローカルも cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で 6b7bb050 へ進めた

### 手順1 確かめ・issue

- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-26 のログの報告は 状態「完了」。指示文の冒頭の雛形の行は揃っている
- #540・#542 のコメントを読んだ。#540 は HOU-14 のコメントだけ。#542 の着手中（AIC-01〜04）はすべて解除のコメントがあり、今の着手中は無い
- 下層ページから `houou/` のトップへのリンク: 生成された `houou/players/`・`houou/ranking/`（と部門のページ）・`houou/leagues/`・`houou/race/` の HTML と `assets/houou.js` に、トップ（`houou/`）への href は無い（ランキングの部門のチップの「通算」の `./`・`../` は `houou/ranking/`）。前提のとおり
- #540 の本文を直した（MCP の `issue_write`。`gh` はこのセッションに無い）。直した行:
  - 「- [x] 平野さんの iPhone での確かめ: 文字の拡大（Chrome で最大）で5ページ（トップ・個人成績・ランキング・リーグ推移・順位変動）が崩れないこと、リーグ推移の演出の見え方（2026-10-11 に OK）」（`[ ]` → `[x]` と末尾）
  - 足した行（公開の条件）「- [ ] 公開は #542（データ・コンシェルジュ）ができてから。#542 の質問欄までを公開に含める（2026-10-11 の平野さんの決定）」
  - 置き換えた行（変更）「- [ ] navbar「鳳凰戦」は、押すとトップ（`houou/`）へ飛ぶ1項目だけにする（個人成績・ランキング・リーグ推移・順位変動は並べない。2026-10-11 の平野さんの決定）。旧ページへのリンクを差し替える」
  - 足した行（変更）「- [ ] 下層ページ（個人成績・ランキング・リーグ推移・順位変動）からトップへ戻る導線（見出しのカードをトップへのリンクにする等）を、公開の前に要否を決める（今は本文の中に無く、戻る道は navbar の「鳳凰戦」だけ。仮置き）」
  - 本文に「公開の段」という見出しは無いので、導線の行は「変更（1つの作業ブランチで）」の navbar の行の次に置いた
- コメント: #540 に決定3点と本文の直し（https://github.com/retroeater/mj/issues/540#issuecomment-6110309015 ）、#542 に #540 の公開に含める旨（https://github.com/retroeater/mj/issues/542#issuecomment-6110309234 ）。どちらも末尾に `Chat-Ref: CHAT-1011-HOU-27`

### 手順2 文書

- `docs/notes/houou-top.md`「非公開の形と公開の方針」の公開の段: navbar を「鳳凰戦」1項目でトップへに、iPhone の確かめを済みに直し、#542 を待つこと・下層ページからトップへ戻る導線の要否を足した。「仮置きの一覧」の「公開時の navbar「鳳凰戦」の項目」を外し、「下層ページからトップへ戻る導線の要否」に置き換えた
- `docs/decisions/houou.md` に CHAT-1011-HOU-27 の節を足した（2026-10-11 の節は AIC を含めて18個目。HOU の節としては15個目）。navbar の5項目は #540 の本文の仮置きで、decisions には前の決定の行が無い（139行目は「navbar の項目は公開の issue で決める」で、すでに置き換え済み）ので、新しい節の側に「#540 の本文の仮置き〈5項目〉を置き換える」と書いた
- `docs/handover.md`「次にやること」の #540 の行に「公開は #542 ができてから、その質問欄までを含めて行う」を足し、「公開の条件は平野さんの iPhone での確かめ」を外した（23,389 バイト。警告域 26KB 未満）

### 手順3 マージ・片付け

- 差分（`origin/cloudflare...HEAD`）は `docs/` の4ファイルだけ（decisions・handover・houou-top・このログ）
- マージ: push 直前に再 fetch し（origin/cloudflare は 6b7bb050 のまま）、`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめて `git push origin work/1011-hou:cloudflare`（6b7bb050..976c61cb）。docs のみなので Workers Builds は走らない（#171）
- 片付け: このクラウドセッションはブランチを削除できない（docs/notes/cloud-sessions.md「ブランチの削除」）。`origin/work/1011-hou` は `git branch -r --merged origin/cloudflare` に出る（マージ済み）。削除は `delete-merged-branches.yml` に任せる。このログの push の後、同じ先頭を `cloudflare` へも push する（docs/logs だけの追いの push）。追いの push の前の先頭は 976c61cb

## 報告

- 状態: 完了
- ブランチ: work/1011-hou（マージ済み。削除は `delete-merged-branches.yml` に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1011-HOU-27.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済（6b7bb050..976c61cb。この報告の docs/logs のみの追いの push あり）
- issue: #540（本文の直しとコメント）・#542（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9973fe01）: https://github.com/retroeater/mj-logs/tree/main/guide/9973fe01

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9973fe01/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
