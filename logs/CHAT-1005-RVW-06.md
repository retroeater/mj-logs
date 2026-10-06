# CHAT-1005-RVW-06

- 着手日時: 2026-10-06
- 対象issue: #283（h1 の部分）・#486
- ブランチ: work/1006-rvw-h1
- 着手時HEAD: d5e7ec91

## 指示

【Claude作成】Claude Code 向け指示：h1 の無い11ページに h1 を足し（#283 の h1 の部分・#486）、プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-06 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも（CHAT-1005-RVW-05 と並行してよい。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-h1 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1006-rvw-h1 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-rvw-h1 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Bing の Recommendations（#486）に残る「h1 の無いページ」を解消する。#283 のうち h1 を足す部分だけを今日行い、title・og:title の文言の統一は後に分ける。
決定（2026-10-06、平野さん）

* #283 は今日は h1 の11ページだけを行う。title の文言の統一は分ける（今日は行わない）
* 今日の順番は #377 → #283 と #486 の h1 → #277（RVW-04 で記録済み。#377 の実装はデータの準備を待つため、この指示を先に進める）

前提（チャット側。平野さんの決定ではない）

* 対象の11ページ（RVW-04 の調べ）: `jpml_links`・`houou_*` 3・`ouka_*` 3・`wrc_*` 2・`resource_dictionary`・`rh_links`（要確認: 今の本番の HTML で h1 が無いことを確かめ、件数が違えば実物に合わせて報告に書く）
* h1 の文言は #283 の本文の案（h1「大分類 ページ名」）に合わせ、見た目は既存の h1 のあるページにそろえる。ページの見出しにあたる要素（表題の文字など）が既にあれば、それを h1 にして見た目を変えない方法を先に考える（要確認: #283 の本文の案と、既存のページの h1 の作り）
* ランキング3ページ（#141 で作り直す）は対象に入っていないはず（要確認。入っていれば、#141 の移植と重なるため手を入れずに報告に書く）
* 生成されるページは生成スクリプト側（`scripts/lib/page.py` や各 `generate_*.py`）で直し、生成物は再生成する。手書きのページは HTML を直す。共有の関数を変えるときは CLAUDE.md のとおり参照を洗い出し、全ページを再生成して差分を確かめる
* `resource_dictionary.html` は、別の指示（CHAT-1005-RVW-05、#377 の調査）が調べるだけで変えない。この指示が先に h1 を足してよい
* 今日は title・og:title・description は変えない

手順

1. 確かめる: #283・#486 の本文と最近のコメント、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が対象のページか `scripts/lib/page.py` を変えていないか。対象ページごとに「今の h1 の有無・見出しにあたる要素・生成か手書きか・直す場所」を表にしてログに書く
2. 直す: 11ページに h1 を1つずつ足す（文言はページごとに表にする）。生成ページは再生成する。`python3 -m unittest discover -s scripts/tests` を通す。h1 が1ページに1つだけであることを機械的に確かめる（全ページ）
3. プレビューで確かめ、判断待ちで止まる: PC 幅とスマホ幅（iPhone の Safari の幅）で、11ページの見た目が変わっていないか（h1 を足したことで文字の大きさ・余白が変わっていないか）を確かめる。報告には、11ページの h1 の文言の表、見た目が変わったページ、cloudflare との差分のファイル数（種類ごと）、平野さんに決めてほしい点を書く

止まる条件

* 未マージの `work/` ブランチが対象のページか `scripts/lib/page.py` を変えている
* 対象ページの件数が11と違い、違いの理由が説明できない
* 共有の関数を変えた結果、対象外のページの差分が h1 以外にも出た
* 外部ドメインかライブラリを足す必要が出た
* cloudflare へは push しない（この指示は判断待ちで止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。CHAT-1005-RVW-06 のコミットなし。work/1006-rvw-h1 はローカル・リモートとも無く、origin/cloudflare（d5e7ec91）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 「貼る時機」は「別のセッションに貼る」だが、RVW-05 と同じセッションに貼られた（RVW-05 は完了済みで、作業に影響なし）

## 報告

- 状態: 作業中
- ブランチ: work/1006-rvw-h1
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-h1/docs/logs/CHAT-1005-RVW-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-h1
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fad9eb53）: https://github.com/retroeater/mj-logs/tree/main/guide/fad9eb53

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96fa2201.md
