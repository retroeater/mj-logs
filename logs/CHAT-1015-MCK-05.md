# CHAT-1015-MCK-05

- 着手日時: 2026-10-15
- 対象issue: #365, #571
- ブランチ: work/1015-mck-365
- 着手時HEAD: 8d40f40cb

## 指示

【Claude作成】Claude Code 向け指示：（MCK-03 の続き）今の JWRC タブ（12列）の形で、WRC 第18期・第19期の貼り付け用 TSV を作る Chat-Ref: CHAT-1015-MCK-05 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-365 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-365 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-365 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-MCK-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
#365（WRC 第18期以降の成績）の続き。MCK-03 で作った第18期の TSV は、列を消す前の24列の形だった。そのあと CHAT-1014-PLB-01〜03・CHAT-1015-PLB-04 で JWRC タブの列が消されて12列になったので、今の形で第18期と第19期の TSV を作り直す。平野さんはそれをブックに貼る。第20期は進行中なので含めない。
決定（2026-10-10・2026-10-15、平野さん）

* #365: Claude Code が公式サイトから成績を集めて貼り付け用の TSV と照合の表を作り、平野さんがブックに貼る（2026-10-10、MCK-02 で記録済み）
* 「結果」の列: 公式の表で合計が「敗退」の人には「敗退」と入れる（2026-10-10）。合計の列には出た節の和が入る（下の数式による）
* 第12〜17期の「結果」（今は全行が空）を埋めるかは、この指示では扱わない。#571（JWRC・女流桜花の予選の通過・敗退をサイトに載せる）の中で扱う（2026-10-15）
* JWRC タブの数式の列は「合計」だけ（2026-10-15、平野さんが画面で確認）。ほかの列は値
* 第19期の TSV を作る（2026-10-10）

前提（チャット側。平野さんの決定ではない）

* 今の JWRC タブ（CHAT-1015-PLB-04 のログ。要確認）: 名前・期・順位・結果・合計・第1節〜第5節・表示・備考の12列（A〜L）、1,507行。最新は第17期で、新しい期が上に並ぶ。「プロ」の列は消えたので、MCK-03 の「プロ」の規則は要らない
* ページ（`wrc_results.js`・`league_ranking.js`）は PLB-02 から見出しで列を引くので、列の位置が変わってもそのまま読める（PLB-03 で確かめ済み。要確認）
* 公式の成績表（MCK-03 のログ「手順2」）: 第18期 https://www.ma-jan.or.jp/result-champions-league/116379.html （91人、第5節は全員空）、第19期 https://www.ma-jan.or.jp/result-champions-league/117354.html （93人）。変換の方法は MCK-03 で第17期の81行と全行一致を確かめたもの（`▲` は負の数、合計は各節の和）
* 「合計」は数式なので、TSV では空にする。平野さんが既存の行の「合計」の式を貼った行へコピーする（TSV に式を入れると行番号がずれるため。チャット側の案）
* 貼る順: 新しい期が上なので、第19期（93行）→ 第18期（91行）の順で1つの TSV（184行）にし、2行目の上に184行を挿入して貼る
* 成績は公開の競技の結果で、サイトにも同じ形で載せるもの。TSV をログに載せてよい（MCK-03 と同じ）

手順

1. #365 を読み、Open で他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す（本文・状態・ラベルは変えない）。JWRC タブを生成と同じ経路で読み、見出しの並び・行数・最新の期が前提と同じかを確かめる。今の第17期の行で「合計」と各節の和が全行で一致することも確かめる（「合計」が数式で和を出している、の裏づけ）
2. 第19期・第18期の公式の表を読み、今の12列の並びの TSV（第19期 → 第18期の順、「合計」は空、「表示」は `Y`、「備考」は空、「結果」は公式で「敗退」の人だけ `敗退`）をログにコードブロックで置く。照合の表（期ごとの人数・1位と最下位・合計の和・節ごとの人数と和・「敗退」の人の名前の一覧）と、既存の JWRC の行や「ryoei.pro_選手」の「プロ」タブと名前の表記が違う人の一覧（無ければ「無し」）も置く
3. 平野さん向けの貼り方を3〜5行で書く（JWRC タブの2行目の上に184行を挿入 → A列に「値のみ貼り付け」〈Ctrl+Shift+V〉→ 挿入前の2行目〈今の第17期の1位〉の「合計」のセルをコピーし、貼った184行の「合計」の列に貼る → 照合の値を見る）。#571 に「第12〜17期の『結果』は #571 で扱う（2026-10-15 の決定）」をコメントする。CHAT-1010-MCK-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MCK-05` を足す

止まる条件

* #365 が Closed、または他セッションの着手中コメントがある
* JWRC タブの見出し・行数・最新の期が前提と違う（すでに第18期以降が入っている、列の並びが違う など）
* 第17期で「合計」と各節の和が一致しない行がある（数式の前提が崩れる。行と値を書いて止まる）
* 公式の表が読めない、または人数が MCK-03 の記録（第18期 91人・第19期 93人）と違う
* TSV が 300 行を超える
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-05 を書く

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

ガイド文書（この版を写した時点の最新、mj 3508c80c）: https://github.com/retroeater/mj-logs/tree/main/guide/3508c80c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3508c80c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
