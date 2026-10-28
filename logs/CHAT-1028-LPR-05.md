# CHAT-1028-LPR-05

- 着手日時: 2026-10-28
- 対象issue: #529（コメント）・新規2件（起票した番号は `## 報告`）
- ブランチ: work/1028-lpr-iss
- 着手時HEAD: 7e2614751

## 指示

【Claude作成】Claude Code 向け指示：LPR-02 の続き。新サイトの要件は #529 にコメント、共通化と canonical は新しく起票して、ログ・決定を cloudflare に入れる
Chat-Ref: CHAT-1028-LPR-05
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ）
貼る時機: CHAT-1028-LPR-02 の判断待ちの後（LPR-02 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-iss の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1028-lpr-iss を続けて使う（LPR-02 のログと同じブランチで入れるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/site-review.md などの文書が衝突したとき（LPR-03・LPR-04 も同じファイルの末尾に節を足している）、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、docs/logs/CHAT-1028-LPR-02.md の `## 報告` の状態が「判断待ち」で、LPR-02 で起票・コメントがされていない（ログの「issue: なし」）ことを確かめ、食い違えば何もせず止まる。

## 目的
LPR-02 で止まった起票を、平野さんの答え（Q1〜Q4）に沿って行う。新サイトの要件は既存の #529 にコメントで足し、共通化の件と canonical の件はそれぞれ新しく起票する。

### 決定（2026-10-28、平野さん）
- Q1: 新サイトの要件（N1〜N10・D1〜D8）は新しく起票せず、#529 にコメントで足す。#529 と中身が重なる2件（D7 と G2-08、N8 と G3-04）はその旨を書く
- Q2: 共通化の件（S7・S8・S15・S18）は3件目として新しく起票する。#614（内部の名前の付け替え）にはコメントせず、関連としてリンクだけ張る
- Q3: canonical（S19）は新しく起票する
- Q4: #296 の本文の「子 issue」の表には行を足さない（新しい sub-issue を作らないため）
- （LPR-02 の決定のまま）S19 の方針: `saikyo/players/`（`?name=` で中身が変わるページ）には canonical を付けない。年度・対局・卓の個別ページは docs/decisions/saikyo.md の決定のまま（自分自身）

### 前提（チャット側。平野さんの決定ではない）
- 材料は docs/logs/CHAT-1028-LPR-01.md。issue・コメントには、`## 報告` の「判断が必要なこと」の該当の行と、`## 経過`「指摘の一覧」の元の番号の行（証拠の欄を含む）を引用する（要約で置き換えない）。ログの URL（blob/cloudflare/docs/logs/CHAT-1028-LPR-01.md）を添える
- LPR-01 の指摘のうち S1〜S6・S9〜S14・S16・S17 は LPR-03（マージ済み）・LPR-04 で直した、または直している。issue・コメントには、直したものを入れない
- #529 へのコメント: N1〜N10・D1〜D8（W1 は #630、W2 は #633 で扱っている旨を1行で添える）。重なる2件は「#529 の G2-08 と同じ論点」「G3-04 と同じ論点」と書く
- 共通化の issue（新規）: S7（②-5。相互検証の「条件つき同意」の理由〈top の空文字が JS で「鳳凰位」に戻る〉を含める）・S8（②-7）・S15（②-8 の saikyo 側の後半。`-hit-toggle` は scripts/promo_video/saikyo/capture.mjs が参照している旨と、消す前に JS での組み立てと DOM での出現を確かめる要があること。LPR-03 の S9 の確かめ方〈ログ手順1・手順3 (c)〉を参照として添える）・S18（②-12 の前半）。本文に「関連: #614（同じファイルの名前の付け替え。着手の順に注意）」と書く
- canonical の issue（新規）: S19（①-5）の内容と証拠、上の方針。canonical が付いている saikyo/ のほかのページ（トップ・`ranking/`・`vs/`・`years/`）の扱いは「未決（#113 の原則は付けない）」と書く
- ラベルは `gh label list` の正（読めなければ既存の issue から）で、`分野:`・`対象:` を付ける。`状況:` は付けない（未着手）

## 手順
1. LPR-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1028-LPR-05` を足す。#529 の今のコメントを読み、LPR-01 の分がまだ無いことを確かめる（あれば重ねずに止まる）
2. #529 にコメントし、共通化の issue と canonical の issue を起票する。各コメント・issue の末尾に `Chat-Ref: CHAT-1028-LPR-05` を書く
3. 上の「決定」を docs/decisions/README.md の書き方に従って分野のファイル（LPR-01〜04 と同じ）に足す。#529 のコメントの URL、起票した2件の番号・題・ラベルをログに書き、ログを仕上げて `git push origin work/1028-lpr-iss:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 0章の確かめが食い違う
- 手順1で #529 に LPR-01 の分がすでにある
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- 取り込みの衝突のうち、両立しないもの（作業ブランチの行の衝突の扱いのとおり）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7f9e605c）: https://github.com/retroeater/mj-logs/tree/main/guide/7f9e605c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7f9e605c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
