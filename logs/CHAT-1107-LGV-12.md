# CHAT-1107-LGV-12

- 着手日時: 2026-11-07
- 対象issue: #636（参照）
- ブランチ: work/1107-lgv-check
- 着手時HEAD: 8dfc39bf7

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の再掲の投稿（「n選」など）の候補と、【3】手動補正で消してよい列・行を洗い出す（読むだけ） Chat-Ref: CHAT-1107-LGV-12 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-check の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1107-lgv-check を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-check origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが #636 の【3】の手作業（CHAT-1105-LGF-13 のコメントの一覧）を進めている。その中で出た2つを、平野さんが決める材料として調べる。調べるだけで、シートにもページにも書かない。

1. 過去の訪問の店を選んで並べ直した再掲の投稿（「n選」など）の候補を、【1】の本文から全件出す
2. 【3】手動補正を、補正が要る行と要る列だけにしてよいか（消してよい列・行）を確かめる

決定（2026-11-07、平野さん）

* #636 の (a) の 12・14・19 の投稿（「個人的おすすめ桃スイーツ12選」「ついでに、激ウマまでではないけどシンプルに好きでつい行っちゃう池袋ラーメン2店」「新規開拓したラーメン店89杯の中で、想像以上に美味い！と思ったラーメン12選」「個人的池袋ラーメン12選」など）は、過去メシと同じく、過去の訪問の店から選んで再掲しているだけなので載せない。「おすすめ*選」などの語で探せるかを確かめ、同じ種類の投稿を洗い出す
* 【3】手動補正には、補正が要る行だけを置き、列は「投稿ID」「表示」「駅」「店名」「メニュー」だけでよいと考えている。ほかの列と、今の値が要らない（【2】の値と同じ）行を消せるかを確かめる

前提（チャット側。平野さんの決定ではない）

* シートはセッションから読めない。CHAT-1106-LGV-07 と同じく、`convert_x_posts.py` に読むだけの一度きりの mode を足し、`convert-x-posts.yml` の mode の選択肢にも足して、作業ブランチの ref・dry_run で手動実行し、ジョブのログから結果を読む。終わったら足したコミットを戻すコミットで消す。シート（【1】【2】【3】・閉業）は読むだけで書かない。Places・Claude・X の API は呼ばない
* 1 の対象: 今の log/ に載っている投稿（`shown_pairs()` の後、`HIDDEN_SHOPS` を外した後。LGV-07 と同じ絞り方）
* 1 の探し方（チャット側の案。増やしてよい）: 本文を NFKC にして、(i) 数字＋「選」（例 `\d+選`）(ii) 数字＋「店」「杯」「軒」（例「2店」「89杯」）(iii) 語: おすすめ・オススメ・まとめ・ピックアップ・ランキング・ベスト・振り返り・ついでに・再掲・過去 (iv) 店名の列に複数の店が並ぶ（「、」「 / 」区切り。LGF-04 の 12・14・19 の形）。平野さんが挙げた 12・14・19 の投稿（2091733447329140777・2064559698876248467・2064549698619556105 と、19 の残りの2件 2072882115335836001・2050783334700339710）が、どの探し方で当たるか・当たらないかを書く（当たらないなら、当てるのに要る語を書く）
* 1 の出す値: 投稿 ID・日付・投稿者・店名・当たった探し方と語・当たった所の前後 各15字まで。本文の全文は出さない（ログは public の mj-logs に写るため）。探し方ごとの当たりの件数と、催事・通常の訪問など別の意味で当たったものの件数と例も書く。当たった投稿が多い探し方は、全件でなく件数と例 20件でよい（(i) と (iv) は全件）
* 2 の確かめ: `lib/log_rows.py` の `merged_rows()` が【3】をどう読むか（見出しの名前で読むか列の位置で読むか、空のセルは上書きしないか、【2】に無い投稿 ID の行をどう扱うか）。【3】の見出しの一覧。ほかに【3】を読む・書く処理が無いか（`scripts/` と `.github/` を検索）。【3】の行ごとに【2】と比べ、(A) 【3】のどのセルも空か【2】と同じ値の行（消しても結果が変わらない行）の数 (B) 「投稿ID」「表示」「駅」「店名」「メニュー」以外の列に、空でなく【2】と違う値がある行（その列を消すと結果が変わる）を列ごとに件数と投稿 ID の全件 (C) 5列のどれかに【2】と違う値がある行の数、を出す。セルの値そのもの（本文・URL）は出さず、列の名前と投稿 ID で書く
* 2 で、列を消すときにコードの直しが要る（列の位置で読んでいる、消す列を名前で探して無いと止まる、など）なら、その箇所と直し方の案を書く。直さない

手順

1. 未マージの work/ ブランチで `scripts/convert_x_posts.py`・`.github/workflows/convert-x-posts.yml`・`scripts/lib/log_rows.py` を変えているものを確かめる。`merged_rows()` と【3】を読む・書く処理をコードで確かめてログに書く
2. 一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。run の番号と行数（【1】・【2】・【3】・log/ の投稿）をログに書く
3. 1 の結果（探し方ごとの件数・候補の表・12/14/19 の当たり方）と、2 の結果（(A)(B)(C) と、消せる列・行の結論、コードの直しが要るか）をログに書く
4. 足したコミットを戻すコミットで一度きりの mode を消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

止まる条件

* 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
* 【3】の見出しに「投稿ID」に当たる列が見つからない
* 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
* 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、(1) 再掲の投稿を外す規則（語・形）と外す投稿 (2) 【3】で消してよい列・行と、平野さんが消す前にコードの直しが要るか、を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-check
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-check/docs/logs/CHAT-1107-LGV-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-check
- 確認用URL: なし
- マージ: 未
- issue: #636（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8dfc39bf）: https://github.com/retroeater/mj-logs/tree/main/guide/8dfc39bf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8dfc39bf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
