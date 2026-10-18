# CHAT-1018-LOG-05

- 着手日時: 2026-10-18
- 対象issue: #367
- ブランチ: work/1018-log-367-migrate
- 着手時HEAD: ed495d4cb

## 指示

【Claude作成】Claude Code 向け指示：#367 の旧「ログ」シートの「表示」Y の行を、X API で投稿 ID から【1】へ取り直し、手で書いた値を【3】へ移す（変換と旧「ログ」の廃止は次の指示） Chat-Ref: CHAT-1018-LOG-05 マージ: 判断待ちで止まる（X から取れなかった行の扱いを平野さんが決めてから） 貼る時機: CHAT-1018-LOG-04 のマージの後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-log-367-migrate の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-log-367-migrate を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-log-367-migrate origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1018-LOG-04 のマージが origin/cloudflare の祖先であることを確かめ、違えば止まる。

目的
旧「ログ」シートを廃止し、過去の行も3層のスプレッドシートの同じ流れに乗せる（2026-10-18 の決定）。その第1段として、旧「ログ」の「表示」Y の行を X API で投稿 ID から【1】元データへ取り直し、旧「ログ」で手で書いた値を【3】手動補正へ移す。【1】→【2】の変換（Claude・Places）と、生成が旧「ログ」を読む処理を外すのは、次の指示（LOG-06）で行う。
決定（2026-10-18、平野さん。docs/decisions/operations.md の「2026-10-18（CHAT-1018-LOG-02）」の節にあるものを含む）

* 旧「ログ」の「表示」Y の行を、X API で投稿 ID から【1】へ取り直す。X の費用の上限は $15（以前の $6 を置き換え済み）
* 旧「ログ」の「表示」N の行は移さない
* 旧「ログ」で手で書いた値（店名・メニュー・店URL・表示）は【3】へ移して優先する。駅・カテゴリ・タグは移さない（駅は変換で決める）
* 【3】へ移す行の「判定」は「飲食店」と書く（Claude の判定で旧「ログ」の行がページから消えないように）
* 旧「ログ」の「日付」は、X の投稿日と違う行だけ【3】へ移す（同じなら空のまま）。違う行の数と例を報告する
* 移す投稿がすでに【3】にある行（2026-10-18 に平野さんが写した行と重なるもの）は、旧「ログ」の値を優先する（旧「ログ」の空でない値で【3】のセルを上書きする）
* X から取れない行（X URL が空・投稿 ID が取り出せない・削除や非公開で取れない）は、数と一覧を報告し、扱いは後で決める。決めるまで旧「ログ」は消さない

前提（チャット側。平野さんの決定ではない）

* 旧「ログ」の数（LOG-01 のログ）: 2708行、表示 Y 2630行。X URL から投稿 ID を取り出せないのは全行で12行（空 11・画像の URL 1）、同じ投稿 ID が2行ある ID が3つ。表示 Y の行に限った数は要確認
* 【1】の今の行（809行）と投稿 ID が重なる旧「ログ」の行（例: 【2】2・12・13・14行の投稿）は、X API を呼ばずに【1】の行をそのまま使う（取り直さない）。重なる数を報告する
* 取り直しは `scripts/fetch_x_posts.py` の `mode=ids`（既定は dry-run）を使う見込み（要確認）。1回の実行の件数の上限がスクリプトの定数にあるなら、上限 $15 の中で複数回に分けてよい。旧「ログ」の名前のうち、【1】の「名前」の対応（投稿者のアカウントと名前）に無い人（例: 石立岳大）がいれば、対応を足す（要確認）
* 費用の見込み: 取り直す件数 × $0.005（2,630件以下なら $13.15 以下）。dry-run で件数と費用を出し、$15 を超える見込みなら呼ばずに止まる
* 【3】へ書く処理は、旧「ログ」の移行のためだけのもの（一度きり）。次の指示（LOG-06）で旧「ログ」を読む処理と一緒に外す前提で作り、その旨をコードのコメントと docs に書く（技術的負債を残さないため）。書く前に【3】を読み直し、読んだときと違えば書かずに止まる
* 【3】の見出しは平野さんが直しているかもしれない（カテゴリの3列を消したなど）。【3】へは見出しの名前で書く。「投稿ID」の列は文字列で書く（19桁が丸められないように）
* 【3】に行があって【2】に行が無い投稿（変換の前の旧「ログ」の行）を、今の生成がどう扱うか（無視する・止まる）を確かめる。旧「ログ」はまだ読んでいるので、ページの行は変わらない見込み（再生成して 3113行のままかを確かめる）

手順

1. #367 に他セッションの着手中コメントが無いこと、未マージの work/ ブランチが `scripts/fetch_x_posts.py`・`scripts/generate_resource_logs.py`・`scripts/convert_x_posts.py`・それらのワークフローの同じ関数を変えていない（または取り込みで衝突しない）ことを確かめ、着手中のコメントを残す（本文・状態・ラベルは変えない）。旧「ログ」・【1】【2】【3】の行数を読み、旧「ログ」の表示 Y の行を区分ごとに数える（【1】と重なる・取り直す・投稿 ID が取り出せない・投稿 ID が重なる）。取り直しを dry-run で行い、件数と費用の見込みを書く
2. 取り直しを本番で行い（上限 $15）、【1】に足す。取れなかった行（削除・非公開・その他）の数と一覧（旧「ログ」の行番号・名前・日付・理由。本文・店名は書かない）を書く。費用は使用量から計算して書く
3. 【3】へ書く処理を作り（テストを足して `python3 -m unittest discover -s scripts/tests` を通す）、作業ブランチから dry-run → 本番で旧「ログ」の値を【3】へ書く。書いた行の数（新しく足した・すでにあって上書きした）と、日付が X の投稿日と違って移した行の数と例を10行書く。resource_logs を再生成して、ページの行が 3113行のまま変わらないことを確かめ、判断待ちで止まる

止まる条件

* #367 に他セッションの着手中コメントがある
* 未マージの work/ ブランチが上のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 旧「ログ」の表示 Y の行数が 2630 から ±10 を超えて違う、または読み直すたびに変わる（数を書いて止まる）
* X の費用の見込みが $15 を超える、または実行中に $15 に届いた（それ以上呼ばずに止まる）
* 【3】を読み直したら、読んだときと違っていた
* 再生成したページの行数が 3113 から変わった（平野さんのシートの直しで説明できる増減は除く。説明できなければ止まる）
* 旧「ログ」シートに書く処理が要ると分かった（書かずに止まる）
* Claude API・Places API を呼ぶ処理が要ると分かった（呼ばずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-LOG-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-LOG-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1018-log-367-migrate
- ログ: https://github.com/retroeater/mj/blob/work/1018-log-367-migrate/docs/logs/CHAT-1018-LOG-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-log-367-migrate
- 確認用URL: なし
- マージ: 未
- issue: #367
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ed495d4c）: https://github.com/retroeater/mj-logs/tree/main/guide/ed495d4c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed495d4c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f5956855.md
