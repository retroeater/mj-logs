# CHAT-1101-MCK-32

- 着手日時: 2026-11-01
- 対象issue: #304（ほか棚卸しの対象の issue）
- ブランチ: work/1101-mck-inv2
- 着手時HEAD: 2caddbbcc

## 指示

【Claude作成】Claude Code 向け指示：（MCK-28 の続き）(5) の棚卸しの候補のうち、平野さんが決めたものを閉じる・統合する・本文を直す
Chat-Ref: CHAT-1101-MCK-32
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-inv2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-mck-inv2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-inv2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1101-MCK-28 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

## 目的

CHAT-1101-MCK-28 の (5) の深い棚卸しの候補について、平野さんがチャットで決めた。pro/ に関わらないものを MCK が片付ける。pro/ に関わるもの（#159・#13・#24・#82・#238・#405・#23・#22・#139・#416 と docs/handover.md の食い違い）は PRO のチャットへ伝言で渡すので、この指示では触らない。

### 決定（2026-11-01、平野さん）

- 閉じる: #264（index.html の lang は直し済み）・#96・#165・#272・#325・#285（MCK-28 の案のとおり）
- 閉じる: #242 と #239（シートの「最終更新日」の列はもう無い。平野さん）
- 閉じる: #249（ページ送りの「最初」「最後」は、ほかの表のページにも要らない。平野さん）
- 統合する: #95 → #21、#164 → #21、#290 → #456、#160 → #283、#97 → #429（楽天データの保存期限 2026-12-22 の期日を #429 へ移す）
- #21（Astro への移行）は**まだ決めない**。残す
- #365 は残し、本文の対象ページ名を今のページ（jwrc/・wrcr/）に直す
- pro/ に関わる候補は PRO のチャットに渡す（この指示では触らない）

### 前提（チャット側。平野さんの決定ではない）

- 閉じる・統合するときは、各 issue に理由（MCK-28 の根拠の列）と「CHAT-1101-MCK-32、#304 の 2026-11 の (5)」をコメントしてから閉じる。統合は、統合先に「#NNN を統合した（残る論点: …）」とコメントし、元を閉じる（closed as not planned か completed かは、目的を果たしたなら completed、やめたなら not planned）
- #296 の子（#95・#160・#164・#285 など）は、#296 の本文の「新サイト送りにするとき・やめるとき」の手順に従い、sub-issue から外す・本文の表を直す
- #239 の中身は MCK-28 で見ていない。開いて「最終更新日」の列に関する issue であることを確かめ、違えば閉じずに書く
- #97 の期日の移し方: #97 の本文・コメントの期日（2026-12-22）を #429 の本文かコメントに書き、カレンダーに期日の予定があれば、その説明の issue 番号を #429 に直すのは平野さんの手作業（ログの「判断が必要なこと」に、予定の名前を書く）

## 手順

1. 対象の issue（#264・#96・#165・#272・#325・#285・#242・#239・#249・#95・#164・#290・#160・#97・#365・統合先の #21・#456・#283・#429）の状態を確かめる（すでに閉じている・題が違うものは触らずに書く）。#304 に着手中のコメントを残す
2. 決定のとおり閉じる・統合する・#365 の本文を直す。#296 の sub-issue・本文の表を直す。それぞれの URL をログに表で書く
3. 決定を docs/decisions/operations.md に記録し、cloudflare へ入れる。#304 の「2026-11 実施」のコメントに返信で「(5) の片付け: 閉じた N件・統合 N件・本文の直し 1件（CHAT-1101-MCK-32）。pro/ に関わる 10件は PRO のチャットへ」と書く。MCK-28 のログの状態の末尾に ` / 続き: CHAT-1101-MCK-32` を足す

## 止まる条件

- #304 が Closed、または他セッションの着手中コメントがある
- 対象の issue のうち、閉じる理由が今の状態と合わない（MCK-28 の後に動きがあった）ものが 3件以上（書いて、手順1だけで止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-32.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-32 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f0cfaaa）: https://github.com/retroeater/mj-logs/tree/main/guide/6f0cfaaa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
