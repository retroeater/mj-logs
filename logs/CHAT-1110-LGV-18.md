# CHAT-1110-LGV-18

- 着手日時: 2026-11-10
- 対象issue: #675（参照）
- ブランチ: work/1110-lgv-tidy
- 着手時HEAD: 449e771df

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の3層のシートから石立岳大の投稿を消す、店名が空の投稿を載せず毎月の issue で知らせる、椿屋珈琲 日比谷店の place_id を調べる Chat-Ref: CHAT-1110-LGV-18 マージ: 判断待ちで止まる（シートの行の削除は承認済み。コードの変更はプレビューを見てから決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-lgv-tidy の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-lgv-tidy を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-lgv-tidy origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1110-LGV-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1110-LGV-18` を足す。

目的
3層のシートの管理する行を減らし、店名の分からない投稿を載せないようにする。

1. 石立岳大の投稿を、3層のシートのすべてのタブから消し、コードの「石立岳大を外す」規則を外す
2. 店名が空の投稿を log/ に載せず、平野さんが【3】で確かめられるよう毎月の常設 issue で知らせる
3. CHAT-1110-LGV-17 で分かった椿屋珈琲の place_id の取り違えを直す材料を出す

決定（2026-11-10、平野さん）

* 石立岳大の投稿を3層のシートのすべて（【1】【2】【3】と「【3】手動補正（旧）」）から消す（管理する行数を減らすため）。Code が消してよい。【3】（と旧）は「Code は書かない」決まりの今回だけの例外。消した後、コードの石立岳大を外す規則を外す
* 店名が空の投稿（今の本番の「（店名なし）」13件）は載せない。平野さんの確かめる対象にする: 店名を補えるものは平野さんが【3】の「店名」に入れて載せる。判別できないものは【3】の「表示」を N にする（確かめ済みの印）
* 確かめる対象の一覧は、毎月の常設 issue（今の題「ログ: 本文の語で外した投稿」）に「店名が空の投稿」の節を足して知らせ、題を「ログ: 確かめる投稿」に変える（案a）
* 椿屋珈琲: 投稿 1450394631586279428（新宿）に付いている place_id `ChIJn1k_ZdqMGGARatQHM4XEIjk` は「椿屋珈琲 新宿茶寮」で正しい（チャット側が 2026-11-10 に Google マップで確かめた、新宿区新宿3丁目）。直しが要るのは日比谷の投稿 1855425650372542512（同じ place_id が付いている）。日比谷店は、平野さんが送った共有リンク https://maps.app.goo.gl/nFrh6WtATb1vVC5u6 の店

前提（チャット側。平野さんの決定ではない）

* 石立岳大の行の見分け方: 【1】【2】【3】と旧の行の投稿者（「名前」の列など、今の `HIDDEN_PEOPLE` か石立岳大を外すコードが見ている値）で決める。【3】と旧は「投稿ID」で【2】の石立岳大の行に当たるものを消す。LGV-17 の生成のログでは「石立岳大で外した 601件」
* 消し方: 行ごと消す（Sheets API の `deleteDimension`、下の行から）。【1】→【2】→【3】→旧 の順。毎朝・毎週の取り込み（`convert-x-posts.yml`・`fetch-x-posts.yml` など、3層に書くジョブ）が走っていないことを Actions で確かめてから始める。消した行の投稿 ID の全件をログに残す（戻すときはシートの版の履歴から戻す）
* 消す順は「シートの行を消す → コードの規則を外す」。規則を先に外すと、本番の再生成で石立岳大の投稿が載ってしまうため。シートの行を消しても、cloudflare のコードには規則が残っているので本番の log/ は変わらない
* 店名が空の判定: `merged_rows()` の後（【3】の上書きの後）の「店名」が空（空白だけを含む）の投稿を、過去メシ・催事・再掲と同じ所で外す。外した数を生成のログに出す。【3】の「表示」が N の投稿はもともと載らないので、一覧にも出ない
* 毎月の一覧: 今の「本文の語（催事・再掲）で外した投稿」の一覧（`run_closed()`・job `closed-issue`）に「店名が空の投稿」の節を足す（投稿 ID〈X の投稿へのリンク〉・日付・投稿者・駅。本文は出さない）。戻し方の説明に「店名を補えるなら【3】の『店名』に、判別できないなら【3】の『表示』に N を入れる」を足す。常設 issue の題は「ログ: 確かめる投稿」（前の題の issue はまだ作られていない見込み。作られていたら、その issue をどうするかを書いて止まる）。どちらの節も0件なら閉じる
* 椿屋珈琲 日比谷店: 共有リンクの転送先から店名を読み（ワークフローの中で1回。このコンテナからは共有リンクが拒否される〈LGV-17〉）、Places の Text Search（IDs Only、保存するのは place_id だけ）を1回呼ぶ。結果が1件で `ChIJn1k_ZdqMGGARatQHM4XEIjk` と違えば、その値をログと #675 のコメントに書く（平野さんが【3】の投稿 1855425650372542512 の place_id に入れる）。シートには書かない。複数・同じ値・0件なら選ばずに書く
* 一度きりの処理（行の削除・place_id の調べ）は、`convert_x_posts.py` の一度きりの mode にして、終わったら戻すコミットで消す（LGV-15〜17 と同じ進め方）
* docs/notes/log-page.md: 石立岳大の投稿を3層から消したこと（2026-11-10、件数）と規則を外したこと、店名が空の投稿の規則、常設 issue の題と節を書く

手順

1. 未マージの work/ ブランチで `scripts/convert_x_posts.py`・`scripts/generate_log_pages.py`・`scripts/lib/log_rows.py`・`.github/workflows/convert-x-posts.yml`・`run-monthly.yml` を変えているものを確かめる。石立岳大を外すコード（定数・関数・テスト・docs）をすべて洗い出してログに書く
2. 一度きりの mode を足し、dry_run 真で手動実行する: タブごとの石立岳大の行の数と、【3】・旧で【2】の石立岳大の行に当たる行の数、日比谷店の place_id の候補（転送先の店名と Text Search の結果。Text Search は dry_run でも1回だけ呼んでよい）。止まる条件に当たらなければ、3層に書くジョブが走っていないことを確かめて `--apply` で行を消す。消した後にもう一度 dry_run 真で回し、どのタブにも石立岳大の行が0件であることを確かめる
3. 一度きりの mode を戻すコミットで消す。石立岳大を外す規則・テストを外し、店名が空の投稿を外す規則と毎月の一覧の節・常設 issue の題を足す。テストを足す（店名が空・空白だけの投稿が外れる、【3】で店名を補うと載る、【3】の表示 N は一覧に出ない、一覧の節が0件なら閉じる）。docs を直し、docs/notes/self-review.md のとおり差分をレビューさせる
4. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行し、cloudflare の生成と比べる: (a) 店・投稿の数の差 (b) 外れた投稿の全件（店名が空の13件の見込み）と、増えた投稿（0件のはず。石立岳大の投稿が出ていないこと）。`run-monthly.yml` を作業ブランチの ref で手動実行（dry-run）し、issue に書く内容を書く（Place Details は呼ばない）
5. 日比谷店の place_id を #675 にコメントする。作業ブランチを push してプレビューを作り、判断待ちで止まる

止まる条件

* 手順2で、【2】の石立岳大の行が 560〜640件の外になる、または投稿者の値で石立岳大か判断できない行がある（数と例を書いて止まる。シートは消さない）
* 3層に書くジョブが走っている（終わるのを待ってから進めてよい。30分待っても終わらなければ止まる）
* 行の削除の途中で失敗した（消したところまでをログに書いて止まる。やり直さない）
* 手順4の (b) に、店名が空の投稿以外の減り、または石立岳大の投稿の増えがある（毎朝の取り込みの差は分けて書き、止まらない）
* 常設 issue「ログ: 本文の語で外した投稿」が既に作られている
* テストが通らない
* 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、プレビューで見る所と、平野さんが【3】に入れる日比谷店の place_id を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-LGV-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-LGV-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1110-lgv-tidy
- ログ: https://github.com/retroeater/mj/blob/work/1110-lgv-tidy/docs/logs/CHAT-1110-LGV-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-lgv-tidy
- 確認用URL: なし
- マージ: 未
- issue: #675（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bee60139）: https://github.com/retroeater/mj-logs/tree/main/guide/bee60139

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/449e771d.md
