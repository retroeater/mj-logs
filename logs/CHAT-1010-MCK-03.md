# CHAT-1010-MCK-03

- 着手日時: 2026-10-10
- 対象issue: #365
- ブランチ: work/1010-mck-365
- 着手時HEAD: a929cafc

## 指示

【Claude作成】Claude Code 向け指示：#365（WRC 第18期以降の成績）の下調べと、第18期分の貼り付け用 TSV の見本を作る Chat-Ref: CHAT-1010-MCK-03 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1010-MCK-04 と並行してよい。ブランチは別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-mck-365 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-mck-365 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-mck-365 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#365 の WRC リーグ第18期以降の成績を、Claude Code が公式サイトから集めて貼り付け用の TSV にし、平野さんがブックに貼る（2026-10-10 の決定）。この指示では、今のシートの形と公式の資料の在りかを確かめ、第18期の分だけ TSV の見本を作る。全期の TSV は、見本を平野さんが確かめてから別の指示で作る。
決定（2026-10-10、平野さん。#365 のコメント〈CHAT-1010-MCK-02〉と docs/decisions/operations.md にあるもの）

* #365: Claude Code が公式サイトから成績を集めて貼り付け用の TSV と照合の表を作り、平野さんがブックに貼る。期日は未定

前提（チャット側。平野さんの決定ではない）

* `wrc_results.html`・`wrc_ranking.html` は Google Charts でブックを直接読むページ（#7 の旧方式）で、シートに貼れば再生成なしで出る（CHAT-1010-MCK-01 のログの #365 の節。要確認）
* 公式の成績は日本プロ麻雀連盟のサイト（`www.ma-jan.or.jp`、セッションから読める〈MCK-01 の記述〉）にある想定（要確認。どのページに第18期以降の何が載っているかは確かめていない）
* セッションからはブックに書き込めない（書き込みの鍵は Actions のシークレットだけ）。読むのは生成・ページと同じ経路で行う
* 成績は公開の競技の結果で、サイトにも同じ形で載せるもの。TSV をログに載せてよい。ただし大きくなるので、この指示では第18期の分だけにする

手順

1. #365 の本文とコメントを読み、Open で他セッションの着手中コメントが無いことを確かめる。2つのページが読むブック・タブ・範囲をページのコードから特定し、同じ経路で今のシートを読む。見出し（列の並び）・行数・載っている最新の期と節・1行が何を表すか（1人1期か、1人1節か など）をログに書く
2. 公式サイトで WRC リーグの第18期以降の成績が載るページを探し、期ごとに URL・載っている内容（最終成績だけか、節ごとか）・シートの列との対応（足りない列・余る列）を表にする。第18期より後に何期あるか、終わっていない期があるかも書く
3. 第18期の分を、シートの列の並びのとおりの TSV にしてログにコードブロックで置く。あわせて照合の表（公式の合計・人数とシートに貼った後に見るべき値、名前の表記がシートの既存の行と違う人）を置く。貼る場所（タブ・行）と貼り方（値のみ貼り付け など）を平野さん向けに1〜3行で書く

止まる条件

* #365 が Closed、または他セッションの着手中コメントがある
* ページが読むブック・タブを特定できない、または今のシートを読めない（推測で列を決めない）
* 公式サイトに第18期の成績が見つからない、またはシートの列を埋めるのに要る値が公式に無い（何が無いかを書いて止まる）
* 第18期の TSV が 300 行を超える（行数を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-MCK-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-MCK-03 を書く

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

ガイド文書（この版を写した時点の最新、mj a929cafc）: https://github.com/retroeater/mj-logs/tree/main/guide/a929cafc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a929cafc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
