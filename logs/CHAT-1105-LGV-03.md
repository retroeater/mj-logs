# CHAT-1105-LGV-03

- 着手日時: 2026-11-05
- 対象issue: なし（#640 は参照）
- ブランチ: work/1105-lgv-amp
- 着手時HEAD: e96e39d70

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の店名・メニューなどに残る HTML の文字参照（&amp; など）を、生成と取り込みで元の文字に戻す
Chat-Ref: CHAT-1105-LGV-03
マージ: 判断待ちで止まる（log/ の告知の期間中の変更なので、マージには平野さんの承認〈「告知の期間中の変更を承認」〉が要る。プレビューを見てから決める）
貼る時機: いつでも（CHAT-1105-LGV-02 と並行してよい。別のセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgv-amp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1105-lgv-amp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgv-amp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。log/ の告知の期間は CHAT-1105-LGV-01 の着手（2026-11-05 14:20 JST）から続いている。この指示は告知で挙げる log/ の生成を変えるので、マージは平野さんの承認を待つ。

## 目的
本番の log/ で、新宿の店名が「レディース&amp;ジェントルメン」と `&amp;` のまま出ている（CHAT-1105-LGV-01 のログの経過「2.」。place_id `ChIJ0_RXpduMGGAR0YKe38aV2p8`）。原因を確かめ、同じ形の値をまとめて直し、今後の取り込みでも起きないようにする。

### 決定（2026-11-05、平野さん）
- `&amp;` はコードで直す（案A）: 生成のときに HTML の文字参照を元の文字に戻し、取り込み（変換）でも戻すようにする。シートの既存の値は書き換えない

### 前提（チャット側。平野さんの決定ではない）
- 原因の見込み（要確認）: X の投稿の本文が `&amp;` のような文字参照のまま【1】に入り、変換（`convert_x_posts.py`）がそのまま【2】の店名・メニューに書いた。【3】の手書きの値に入っている可能性もある
- 戻すのは文字参照（`&amp;` `&lt;` `&gt;` `&quot;` `&#39;` `&#NNN;` `&#xHH;` など。Python の `html.unescape()` で戻るもの）。戻した後の値は、ページ・`search.json` に出すときに今までどおりエスケープされることを、`<` `&` `"` を含む値のテストで確かめる（戻したことで HTML に生の `<` が入らないように）
- 載せない店・閉業の店の扱い、店名の数え方（`shop_names()`）、駅の決め方は変えない。戻すのは比べる前（店名の表記を数える前）にする。そうすると、`&amp;` の表記と `&` の表記が同じ表記として数えられる
- 変わる範囲の見込み: log/ の `index.html`・`search.json` のうち、文字参照を含んでいた値だけ。ほかに毎朝の取り込みによる差分が混ざりうる

## 手順
1. 【1】【2】【3】の、log/ が読む列（店名・メニュー・駅・本文など）で文字参照を含むセルを全件洗い出し、シート・列・投稿 ID・値の表で書く（略さない）。読むだけで、シートは書き換えない。原因（どの段で入ったか）を書く
2. 生成（`lib/log_rows.py` か `generate_log_pages.py` の、値を読むところ）と変換（`convert_x_posts.py` が【2】に書くところ）で `html.unescape()` を通す。テストを足す（戻す・二重に戻さない〈`&amp;amp;` は `&amp;` に一度だけ戻す〉・出すときのエスケープ）。log/ を作業ブランチで生成し、差分を種類に分けて書く（文字参照の直り・毎朝の取り込みの差・そのほか）
3. 作業ブランチを push してプレビューを作り、最終報告の「確認用:」の行に新宿の店（`?shop=ChIJ0_RXpduMGGAR0YKe38aV2p8`）を開く URL を書く。判断待ちで止まる

## 止まる条件
- 手順1で、原因が文字参照でない（例: 店名そのものが「&amp;」と書かれた店）と分かった
- 手順2の差分に、文字参照の直りと毎朝の取り込みの差で説明できない変更がある（件数と例を書いて止まる）
- 戻したことで、ページや `search.json` にエスケープされない `<` `>` が出る
- 未マージの work/ ブランチが `lib/log_rows.py`・`generate_log_pages.py`・`convert_x_posts.py` の同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-05 17:58 JST）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。告知の期間中（LGV-01 の着手 2026-11-05 14:20 JST から）なので、マージは平野さんの承認を待つ
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 貼る時機は「別のセッションに貼る」だが、このセッション（LGV-01・02 と同じ）に貼られた。LGV-02 は判断待ちで止まっていて並行の作業は無いため、このセッションで行った。ブランチは分けた
- ブランチ: work/1105-lgv-amp はローカル・リモートとも無し → `git checkout -b work/1105-lgv-amp origin/cloudflare`
- 未マージの work/ ブランチで `scripts/lib/log_rows.py`・`generate_log_pages.py`・`convert_x_posts.py`（とそのテスト）を変えているもの: 0件

### 1. 文字参照を含む値の洗い出し

- **シートの【1】【2】【3】は、このコンテナから読めない**（読み取りの鍵 `SHEETS_READER_SA_KEY` は GitHub Actions の Secret にだけある。docs/notes/cloud-sessions.md「鍵」）。値を出すワークフローも無い（regenerate-page の dry_run・convert-x-posts は件数だけ）。そのため、シートの列・セルごとの表は作れていない（下の「判断が必要なこと」）
- 代わりに、公開中の本番 `log/search.json`（【2】を【3】で直し、載せない投稿・店を外した値。2026-11-05 17:59 JST に取得、店 1,737・投稿 2,425）の全部の値（店名・別名・駅・突き合わせ用の駅・ジャンル・都道府県・駅の候補・メニュー・投稿者・prefs・areas）を、文字参照の形（`&名前;`・`&#数字;`・`&#x16進;`）で `html.unescape()` で変わるものに絞って調べた。当たったのは1件だけ:

| 出どころ | 列 | 投稿 ID | 値 |
|---|---|---|---|
| 【2】か【3】（log/ の値では区別できない） | 店名 | 2014248828128698462（谷岡育夫、2026-01-22、place_id ChIJ0_RXpduMGGAR0YKe38aV2p8、駅 新宿） | `レディース&amp;ジェントルメン` |

- この店の投稿は1件で、別名は無い。メニュー（プリンアラモード、シトラス(ノンアル)）に文字参照は無い。店の URL の query も `%26amp%3B` を含む（生成が店名から組み立てるため）
- ほかの店名の「&」（23店。例: `802 CAFE&DINER`・`ELLA&LOUIS`）とメニューの「&」（33件）は、文字参照でなく元の文字で入っている
- 原因（見込み）: X API の投稿の本文は `&` を `&amp;` で返し（`<` `>` も同じ）、【1】は API の値のまま入る（fetch_x_posts.py）。変換（convert_x_posts.py）は本文をそのまま Claude に渡し、Claude の抜き出した店名をそのまま【2】に書く。Claude はほとんどの投稿で「&」に戻して書いていたが、この投稿では `&amp;` を写した。【1】の本文と【2】【3】のセルを見ていないので、【3】の手書きの値の可能性は消せていない。どちらでも、生成で戻せば直る
- 止まる条件「店名そのものが &amp; と書かれた店」: 店は新宿の「レディース&ジェントルメン」で、`&amp;` は `&` の文字参照と判断した

### 2. 直したこと（713b25b8c）

- 生成（`generate_log_pages.py`）: `build_posts()` の初めで、行の「店名」「メニュー」「駅」を `html.unescape()` で一度だけ戻した写し（`unescaped()`）を使う。店名の表記を数える（`shop_names()`）前・地図の URL を組み立てる（`shop_link()`）前に戻る。出すときは今までどおり `esc()`（HTML）と `json.dumps`（search.json）
- 変換（`convert_x_posts.py`）: `build_row()` で Claude が返した店名・メニューを `html.unescape()` で戻して【2】に書く（店の URL も戻した店名から）。本文は戻さずに渡す（戻す所を1か所にして、二重に戻さないため）。既存の【2】【3】の値は書き換えない
- テスト: `test_log_pages.py` の `CharRefTest`（戻す・`&amp;amp;` は `&amp;` に一度だけ・`&amp;` と `&` の表記を同じ表記として数える・カードの HTML で `<` `"` `&` がエスケープされる・search.json は戻した文字列）と `test_convert_x_posts.py` の `test_char_refs_are_unescaped`。直す前のコードで4件とも失敗することを確かめてから直した。直した後 `python3 -m unittest discover -s scripts/tests` 2,036件 OK、`node --test scripts/tests/js/log.test.js` 23件 pass

### 3. 作業ブランチでの生成とプレビュー

- `regenerate-page.yml` を作業ブランチで手動実行（`target_page: log_pages`、`dry_run: false`。run 42000798195）→ `chore: regenerate log/ via GitHub Actions`（b40b3a415）
- 差分（`git diff --stat origin/cloudflare -- log/`）: `log/search.json` だけ（1行。1行の JSON）。`log/index.html` は変わらない（この店はカテゴリの行に出ない）。cloudflare の log/ は 2026-11-05 10:32 JST の生成
- 種類ごと（新旧の search.json を店ごとに比べた）:
  - 文字参照の直り: 1店（ChIJ0_RXpduMGGAR0YKe38aV2p8）の店名 `レディース&amp;ジェントルメン` → `レディース&ジェントルメン` と、店の URL の query の `%26amp%3B` → `%26`
  - 毎朝の取り込みの差: 0（店 1,737・投稿 2,425 のまま。増えた・減った店・投稿は無し）
  - そのほか: 0（people・prefs・areas も同じ）
- `<` `>`: 新旧の search.json とも0個。index.html の HTML は変わらない
- プレビュー: check-run「Workers Builds: mj」success。版ごとの URL で `log/search.json` の店名が `レディース&ジェントルメン`、`?shop=ChIJ0_RXpduMGGAR0YKe38aV2p8` を iPhone 13 の幅で開き、タグ・店名が「レディース&ジェントルメン」、地図のリンクの query が `%26` であることを確かめた（URL は最終報告）

### 4. 自己レビュー（サブエージェント、1回）

| 指摘 | 扱い |
|---|---|
| 変換と生成の両方で戻すので、本文に字で `&amp;` と書いた投稿（X API では `&amp;amp;`）は、変換で `&amp;`、生成でもう一度 `&` になる。前提「一度だけ」が端から端では守られない | 意図どおり（決定「生成で戻し、変換でも戻す」に従った。前提より決定を優先）。「判断が必要なこと」に回した |
| `html.unescape()` はセミコロンの無い古い名前（`&copy`・`&reg`・`&lt3`）も戻す | 直した（bc8922cd2。`簡略化:` のコメントで上限と次の一手を残した。今のデータでは差分は上の1店だけ） |
| 「駅」は変換が一覧の名前で書くので戻す理由が無い | 意図どおり（手順1の対象の列に駅があり、【3】の手書きの駅にも効く。今のデータで変わる値は無い） |
| HIDDEN_NAME との比べ・出力のエスケープ・共有の関数・テスト | 問題なし（修正前のコードで新しいテスト4本が失敗することもレビュー側で確かめた） |

## 報告

- 状態: 判断待ち / 続き: CHAT-1106-LGV-06
- ブランチ: work/1105-lgv-amp
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgv-amp/docs/logs/CHAT-1105-LGV-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgv-amp
- 確認用URL: プレビューあり（URL は最終報告）。log/ の `?shop=ChIJ0_RXpduMGGAR0YKe38aV2p8` で、店名とタグが「レディース&ジェントルメン」（`&amp;` が出ない）
- マージ: 未（log/ の告知の期間中の変更のため、平野さんの承認〈「告知の期間中の変更を承認」〉を待つ）
- issue: なし（#640 は参照）
- 判断が必要なこと:
  - マージの承認（告知の期間中の変更を承認するか）。マージすると本番の log/ のこの店の名前が直る。告知動画（LGV-02）にはこの店は映らない
  - 手順1の【1】【2】【3】のセルごとの表は作れていない（コンテナにシートの読み取りの鍵が無く、値を出すワークフローも無い）。代わりに本番の search.json の全値を調べ、文字参照は上の1件だけだった。シートのセル（`&amp;` が【2】か【3】のどちらにあるか、【1】の本文が `&amp;` か）を確かめるなら、平野さんがシートで投稿 ID 2014248828128698462 の行を見る
  - 二度戻る経路: 変換と生成の両方で戻す（決定）ので、本文に字で `&amp;` と書いた投稿は `&` まで戻る（前提の「一度だけ」と食い違う。決定を優先した）。このままでよいか、変換では戻さず生成だけにするか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82fe7190）: https://github.com/retroeater/mj-logs/tree/main/guide/82fe7190

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82fe7190.md
