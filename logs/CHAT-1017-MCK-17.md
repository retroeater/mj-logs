# CHAT-1017-MCK-17

- 着手日時: 2026-10-17
- 対象issue: #367
- ブランチ: work/1017-mck-367-cat
- 着手時HEAD: 2c104a70f

## 指示

【Claude作成】Claude Code 向け指示：（MCK-16 の続き）#367 の「カテゴリ案」シートを作るスクリプトを足してマージし、既存のカテゴリと新しい案を件数とまとめ先の案つきで並べる Chat-Ref: CHAT-1017-MCK-17 マージ: 承認済み（チャットで）。条件: テストが通る・入力の既定が dry-run のまま・書く先は【1】〜【3】と同じスプレッドシートの「カテゴリ案」シートだけ（【1】【2】【3】・「比較」と「ログ」シートには書かない）・`scripts/generate_resource_logs.py` は変えない・Claude API の費用の上限は $0.5（スクリプトの定数）・下の「止まる条件」に当たらない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-mck-367-cat の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-mck-367-cat を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-mck-367-cat origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-MCK-16 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
MCK-16 で【2】自動変換後に 809行を入れた。カテゴリは既存の値だけの行が 353行で、新しい案が 64種類・226件出た。平野さんがカテゴリを採るか・まとめるかを決められるように、【1】〜【3】と同じスプレッドシートに「カテゴリ案」シートを作る。既存の値と新しい案を件数つきで並べ、新しい案ごとにまとめ先の案を Claude に付けさせる。決まったカテゴリを【2】・生成に当てる作業は、平野さんが決めた後の別の指示で行う。
決定（2026-10-17、平野さん）

* 次の指示は「カテゴリ案」シート → 生成（`generate_resource_logs.py`）が3層のスプレッドシートを読む変更、の順で進める
* 「カテゴリ案」シートには、既存のカテゴリと新しい案を件数つきで並べ、新しい案ごとにまとめ先の案（既存の値にまとめるか、新しく採るか）を Claude が付ける。平野さんは「決定」の列を直す
* この指示のマージは承認済み（冒頭の「マージ:」の条件のとおり）

前提（チャット側。平野さんの決定ではない）

* 既存のカテゴリ: MCK-14 のログでは、「ログ」シートのカテゴリの単独の値（「、」で分けた後）は 20種類（綴りの揺れ「バンバーグ」「ハンバーガー」が1件ずつ）。今回も「ログ」シートを生成と同じ経路で読み、単独の値ごとの件数を出す（要確認）
* 新しい案: 【2】の「カテゴリの新しい案」の列（と「カテゴリ」の列）を「、」で分けて単独の値にし、既存に無い値を新しい案とする。MCK-16 の集計では 64種類・226件（数え方が違えば、その違いをログに書く）
* シートの列（チャット側の案）: カテゴリ・種類（既存／新しい案）・「ログ」での件数・【2】での件数・まとめ先の案（既存の値の名前、または「新しく採る」）・案の理由（一言）・決定（平野さんが書く。空なら案のとおりとみなすかは、次の指示で決める）。並びは 既存（件数の多い順）→ 新しい案（件数の多い順）
* 綴りの揺れ（「バンバーグ」など、既存どうしの近い値）も、まとめ先の案を付けてよい（既存の行の値は変えない。案だけ）
* まとめ先の案の作り方（チャット側の案）: `claude-sonnet-5-5` を1回（多くて数回）呼び、既存の値の一覧と新しい案の一覧（値と件数だけ。投稿の本文は渡さない）を渡して、JSON で返させる。費用の上限は $0.5（スクリプトの定数）
* 「カテゴリ案」シートがすでにあり、「決定」の列に値があれば、上書きせずに止まる（平野さんの書いた値を消さないため）。シートが無いか、「決定」が全部空なら作り直してよい
* ワークフローは、既存の `convert-x-posts.yml` に `mode=categories` を足すか、別のワークフローにするかを Code が選び、理由をログに書く（既定は dry-run）

手順

1. #367 に他セッションの着手中コメントが無いこと、未マージの work/ ブランチが `scripts/convert_x_posts.py`・`convert-x-posts.yml` を変えていないことを確かめ、着手中のコメントを残す（本文・状態・ラベルは変えない）。「カテゴリ案」シートを作る処理を足し、テスト（dry-run は何も書かず Claude も呼ばない・単独の値への分け方と件数・既存と新しい案の分け方・「決定」に値があれば止まる・書く先が「カテゴリ案」だけ・Claude に投稿の本文を渡さない・費用の上限）を足して `python3 -m unittest discover -s scripts/tests` を通し、冒頭の「マージ:」の条件を確かめて cloudflare へマージする
2. cloudflare で dry-run を1回動かして予定（既存の種類と件数・新しい案の種類と件数・Claude の呼び出し回数と見込みの費用・「カテゴリ案」シートの有無）を確かめ、続けて `dry_run=false` で動かして「カテゴリ案」シートを作る。ログには種類の数・件数・まとめ先の案の内訳（既存にまとめる案の数・新しく採る案の数）・費用を書く（カテゴリの値そのものは書いてよい。投稿の本文・店名・メニューは書かない）
3. #367 に結果をコメントする（本文は変えない）。決定を docs/decisions/operations.md に記録する。「判断が必要なこと」に、平野さんが「カテゴリ案」シートで決めること（「決定」の列の書き方）と、次の指示（決まったカテゴリを【2】・生成に当てる作業と、生成が3層を読む変更）の候補を書く。CHAT-1017-MCK-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-MCK-17` を足す

止まる条件

* #367 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが `scripts/convert_x_posts.py`・`convert-x-posts.yml` を変えている
* マージの条件のどれかが満たされない
* 「カテゴリ案」シートがすでにあり、「決定」の列に値がある
* 費用の上限（Claude API $0.5）に届いた、または Claude API が認証の失敗・クレジット切れ・レート制限・権限の拒否を返した（それ以上呼ばずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-MCK-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-MCK-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c104a70）: https://github.com/retroeater/mj-logs/tree/main/guide/2c104a70

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c104a70/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
