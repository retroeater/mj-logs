# CHAT-1020-RVD-03

- 着手日時: 2026-10-21
- 対象issue: #492
- ブランチ: work/1020-rvd
- 着手時HEAD: 607a1fecf

## 指示

【Claude作成】Claude Code 向け指示：RVD-01・RVD-02 のガイド4文書の整理を origin/cloudflare を取り込んでマージし、#492 にコメントして片付ける Chat-Ref: CHAT-1020-RVD-03 マージ: 承認済み（チャットで。この指示文を貼ることが承認） 貼る時機: いつでも（RVD-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-rvd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1020-rvd を続けて使う（RVD-01・RVD-02 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1020-RVD-01・RVD-02 のログの `## 報告` が「判断待ち」で、成果物が 2d9ca2383・6de55b38a のまま（work/1020-rvd にその後の成果物のコミットが無い）ことを確かめ、違えば止まる。

目的
RVD-01（古い事実・重複・場面限定の手順の移動）と RVD-02（Codespace 前提の記述を外す）で整理したガイド4文書と移した先（chat-routines.md・branch-operations.md・cloud-sessions.md・`docs/logs/_template.md`・archive）を cloudflare に入れる（#492）。
決定（2026-10-21、平野さん）

* RVD-01（2d9ca2383）・RVD-02（6de55b38a）の整理をマージしてよい（チャット側が両ログの `## 経過` の全文と差分を整理前と読み比べ、規則が失われていないこと・前に外した行を足し戻していないことを確かめた。変わるのは docs と `docs/logs/_template.md` だけ）

前提（チャット側。平野さんの決定ではない）

* チャット側の REST（2026-10-21 0時台）では、origin/cloudflare は 6de55b38a から 7 コミット進み（PLG-08・09 と PR #621 のマージ）、変わったのは `.claude/hookify.warn-sitemap-lastmod.local.md`・docs/decisions/operations.md・docs/logs/CHAT-1020-PLG-08.md・PLG-09.md・docs/notes/skills.md。work/1020-rvd と重なるのは docs/decisions/operations.md の末尾への追記どうしだけの見込み（要確認）
* 取り込みの衝突: docs/decisions/operations.md・docs/logs/ の末尾への追記どうしは両方を残して解く（CLAUDE.md「ブランチ運用」）。そのほかの両立する衝突（隣り合う行）も両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 再検証の見込み: 取り込み後も4文書のサイズは RVD-02 の報告の値（CLAUDE.md 23,129・handover.md 22,108・chat-side-operations.md 21,980・instruction-template.md 11,978 バイト）と同じ（cloudflare 側は4文書を変えていないため）。違えば差の理由をログに書く（止まらない）

手順

1. origin/cloudflare を取り込み（前提の衝突の扱いのとおり）、`wc -c` で4文書のサイズを報告に書く。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通す。4文書を `grep -i "codespace\|worktree\|/workspaces/mj"` し、残るのが handover 6章の2行（旧 Codespace の記録と示す語）だけであることを確かめる
2. マージ（`git push origin work/1020-rvd:cloudflare`、push 直前に再 fetch して祖先確認）。check-run の成否を確かめる（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進む）
3. #492 に、RVD-01・RVD-02 の結果をコメントする（引用元: RVD-02 のログ `## 経過`「手順3: サイズと検査」の「着手時の cloudflare（03eef856d）からの通算」の行と、RVD-01 のログ `## 経過`「手順2: 対応表」の b1・b2・c1〜c4 の行〈移した先〉。要約を書かず引用する）。#492 は閉じない（本文・状態・ラベルは変えない）。RVD-01・RVD-02 のログの `## 報告` の状態を「完了 / 続き: CHAT-1020-RVD-03」に直す（docs/logs のみの追いの push は可）。work/1020-rvd の片付けは docs/notes/cloud-sessions.md のとおり（マージ済みの削除はワークフローに任せる）

止まる条件

* 0章の確かめが食い違う
* 取り込みで両立しない衝突が出た
* 手順1の検査（check_asset_limits・unittest）が失敗した（今回の変更と無関係な失敗なら原因を書いて進んでよい）
* 4文書の grep に handover 6章の2行以外が残った
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-RVD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-RVD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce302389）: https://github.com/retroeater/mj-logs/tree/main/guide/ce302389

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce302389/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ce302389.md
