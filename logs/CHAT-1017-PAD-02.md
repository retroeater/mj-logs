# CHAT-1017-PAD-02

- 着手日時: 2026-10-17
- 対象issue: #428
- ブランチ: work/1017-pad
- 着手時HEAD: f528ff5b8

## 指示

【Claude作成】Claude Code 向け指示：#428 表のページの上余白を `<body>` のクラスに切り替え、フォールバックを幅ごとの値にする（プレビューで止まる） Chat-Ref: CHAT-1017-PAD-02 マージ: 判断待ちで止まる（プレビューを見てから決める） 貼る時機: CHAT-1017-PAD-03 の完了の後（同じセッションに続けて貼る。並行させない） 作業ブランチ: クラウドセッションで実行する。work/1017-pad を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1017-PAD-03 のログの `## 報告` を読み、完了していなければ何もせず止まる。

目的
CHAT-1017-PAD-01 の計測で `:has()` の成立待ちのずれが出た表のページ（`body:has(.mj-table)`）を、saikyo/ と同じく `<body>` のクラスで当てる形に直す。同じ規則の `padding-top` のフォールバック（90px）が実測と合わずに毎回出るずれも一緒に直す。
決定（2026-10-17、平野さん。CHAT-1017-PAD-01 の「判断が必要なこと」への回答）

1. 表のページの `:has()` のずれは案T1 で直す: `scripts/lib/page.py` の表のページ専用の `PAGE_TEMPLATE`（`render()` だけが使う）と `scripts/generate_jpml_pros.py` の `PAGE_TEMPLATE` の `<body>` に `class="mj-table-page"` を足し、`style.css` の `body:has(.mj-table)` 系の4規則（PAD-01 の一覧の 383・409・455・865 行）を `body.mj-table-page` に置き換える。`html:has(.mj-table)`（スクロール位置だけ）は残す。`render_content()`（`CONTENT_TEMPLATE`）は変えない（2026-10-10 の決定のまま）
2. 表のページの `padding-top` のフォールバック 90px と実測（992px 未満 56px・以上 80px）の差で毎回出るずれも、#428 で一緒に直す（幅ごとのフォールバックにする）
3. `live/` の入口の 1100px の `:has()` 由来のずれ（1回 0.0002）は直さない

前提（チャット側。平野さんの決定ではない）

* 変わる見込みのページは表の9枚（`rh_results`・`rh_results_detail`・`rh_paifu`・`jpml_pros`・`jpml_test`・`resource_logs`・`saikyo_mens`・`video_live`・`video_en`。PAD-01 の経過の一覧）。うち `render()` を通るのが8枚と `jpml_pros`（PAD-01 の経過。要確認: `render()` を呼ぶ生成スクリプトと、`.mj-table` を持つ HTML の一覧が一致するかを確かめ、手書きの HTML や `render()` を通らない表のページがあれば、そのページの扱いを書いて止まる）
* フォールバックの値は PAD-01 の計測の実測（390px で 56px、1100px で 80px）。境目の幅は navbar が折りたたまれる幅（Bootstrap の `lg`、992px）を想定（要確認: `table.js`・`jpml_pros.js` が入れる `--content-offset` の値を 991px と 992px で測り、境目と値を実物に合わせる）。`html:has(.mj-table)` の `scroll-padding-top` のフォールバックも同じ値にそろえてよい
* `body:has(#pros_table)`・`body:has(#pros_table) .mj-lead`（jpml_pros の幅）は PAD-01 でずれが出ていないので変えない
* `scripts/lib/page.py` は全ページ共通なので、CLAUDE.md「判断・作業の原則」のとおり全ページを再生成して差分を確かめる。見込みの差分は、表の9枚の `<body` の行（クラスが付く）・`style.css`・テスト（足すなら）・docs/logs/・docs/decisions/ だけ。シートの変化で動く差分は種類を分けて書く
* 決定の書き先は PAD-01 と同じ `docs/decisions/site-review.md`
* 使う skill は無い

手順

1. 直す: CHAT-1017-PAD-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-PAD-02` を足す（ログが cloudflare にあるので、この作業ブランチで直す）。#428 に着手中のコメントを書く。上の決定 1・2 のとおり `scripts/lib/page.py`・`scripts/generate_jpml_pros.py`・`style.css` を直す。`<body>` に既に `data-search="off"` が付くページでも属性が正しく並ぶことを確かめる（`render()` の出力をテストで確かめられるなら `scripts/tests/` に足す）。`python3 -m unittest discover -s scripts/tests` を通す
2. 確かめる: `python3 scripts/regenerate.py all` で全ページを作り直し、origin/cloudflare の生成物との差分を種類ごとに表にする（見込みの差分だけか）。計測は PAD-01 と同じ方法で、`file://` で origin/cloudflare の生成物と作業ブランチの生成物を交互に各10回（`rh_results_detail`・`jpml_pros`・`video_live`・`jpml_test`・`rh_results`、390px・1100px）。見るのは、遅れたフレームの数、CLS の「0 でない回数」と中央値を `:has()` 由来と他（フォールバックの差し替え）に分けたもの。JS 無効（390・460・1100px）で本文が隠れないことと上の空きの大きさも書く。作業ブランチを push し、プレビューのビルドの成否を確かめる（待つのは15分まで）
3. まとめる: 結果を #428 にコメントする（決定 1〜3 を冒頭に、表はログの `## 経過` から引用。末尾に `Chat-Ref: CHAT-1017-PAD-02`）。平野さんが iPhone とブラウザで見る点（表のページのどれを開き、何を見るか）を「判断が必要なこと」に書いて止まる

止まる条件

* CHAT-1017-PAD-03 が完了していない
* #428 に他セッションの未解除の着手中コメントがある
* `.mj-table` を持つページに、`render()` も `generate_jpml_pros.py` も通らないものがある（一覧を書いて止まる）
* 全ページの再生成の差分が見込み（表の9枚の `<body` の行・`style.css`・テスト・docs）を超える（シートの変化で動く差分は種類を分けて書き、それ以外が出たら止まる）
* 作業ブランチの計測で、`:has()` 由来のずれが 0 でない回が残る、または「他」の 0 でない回数が origin/cloudflare より増える
* 未マージの work/ ブランチが `scripts/lib/page.py`・`scripts/generate_jpml_pros.py`・`style.css` の同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（マージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（`CHAT-1017-PAD-02`）のコミット: 0件。識別子 PAD はこのセッションの PAD-01 で確認済み
- CHAT-1017-PAD-03 の `## 報告` の状態: 完了
- 作業ブランチ: ローカルの `work/1017-pad` は origin/cloudflare の祖先（PAD-01 でマージ済み）→ `git checkout work/1017-pad` のうえ `git merge --ff-only origin/cloudflare`（f528ff5b8）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4つとも有る（貼られた指示文は改行の一部と見出しの `#` が落ちた形。そのまま写した）
- 0. 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-pad
- ログ: https://github.com/retroeater/mj/blob/work/1017-pad/docs/logs/CHAT-1017-PAD-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad
- 確認用URL: なし
- マージ: 未
- issue: #428
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f528ff5b）: https://github.com/retroeater/mj-logs/tree/main/guide/f528ff5b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f528ff5b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
