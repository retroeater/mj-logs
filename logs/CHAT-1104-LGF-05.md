# CHAT-1104-LGF-05

- 着手日時: 2026-11-04
- 対象issue: #665
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: 23efd467c

## 指示

【Claude作成】Claude Code 向け指示：log/ の店名を店ごとに一つの表記にそろえる（生成で吸収）／#665 を保留にする Chat-Ref: CHAT-1104-LGF-05 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-LGF-04 は cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgf-alias を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgf-alias origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGF-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-LGF-05` を足す。

目的
CHAT-1104-LGF-04 で、同じ place_id で店名の表記が2つ以上ある店が 95店あった。生成のときに店ごとの表記を一つにそろえ、今後の揺れも吸収する（案 B）。要確認の 10店と誤字の店は、この後に平野さんが【3】で直す（案 A。この指示には含めない）。あわせて #665 を保留にする。
決定（2026-11-04、平野さん）

* 店名の表記揺れは A＋B で直す: 生成で店ごとに表記をそろえ（B）、要確認の 10店と誤字だけを【3】で直す（A）
* B は、投稿の行に別名を出さず、別名でも検索に当たるようにする
* この指示はプレビューを見てから決める（判断待ちで止まる）
* #665（Claude でジャンルを付ける）は保留にする（優先順位が低いため）

前提（チャット側。平野さんの決定ではない）

* 今の仕組み（LGF-04 のログ `## 経過`「3.」）: 店の店名は `scripts/generate_log_pages.py` の `group_shops()` が「店名を持つ最も新しい投稿」から取る。投稿の店名が店の店名と違えば `search_data()` が投稿の6要素目に入れ、`assets/log.js` の結果の投稿の行（`mj-log-post-meta`）に出す。`prepare()` は店の店名と投稿の店名をすべて `shop.names` に入れ、店名の候補と検索欄の語の突き合わせに使う
* 店の店名の決め方: 投稿の数が最も多い表記 → 同数なら最も新しい投稿の日付の表記 → 同じ日なら最も新しい投稿の表記（LGF-04 の案の決め方と同じ）。表記の比べ方は文字列の完全一致（NFKC などで寄せない。機械的な揺れも表記が違えば別に数える）
* 別名: search.json の店に、店の店名と違う表記の一覧を足す（例: 12要素目）。投稿の6要素目は空にする（または消す。形を変えるなら log-page.md「データの持ち方」を直す）
* 別名の使い道（チャット側の案。プレビューで変えてよい）: 検索欄の語の突き合わせには使う（「HARBS」で別名の店が当たる）。店名の候補（「店名 ○○ 3店」の行）には出さず、代表の表記だけを出す
* カテゴリのカードの店名も、店の店名（代表の表記）になる。店の鍵（place_id）は変えないので、店の数・`?shop=` の URL は変わらない
* place_id の無い店（「p<投稿ID>」）は1投稿1店なので変わらない
* #665: 本文・ラベルを読み、「状況: 保留」を付け、保留の理由（2026-11-04、平野さんの決定。優先順位が低いため）をコメントする。閉じない

手順

1. #665 に上のとおりコメントし、「状況: 保留」を付ける（ほかのラベルは変えない）
2. `group_shops()`・`search_data()`・`assets/log.js`（`prepare()`・結果の投稿の行・店名の候補）を上の前提のとおりに直し、テスト（`scripts/tests/test_log_pages.py`・`scripts/tests/js/log.test.js` ほか）を足す・直す。docs/notes/log-page.md の「店の店名」「データの持ち方」「結果」「検索の候補」の項を直す。docs/notes/self-review.md のとおり差分をレビューさせる
3. log/ の生成物（`log/index.html`・`log/search.json`）を、今までの log/ の生成と同じ経路（`regenerate-page.yml` を作業ブランチの ref で。ページ名は `python3 scripts/regenerate.py --list` で確かめる）で作り直す。作り直した search.json で、(a) 店の数・投稿の数（origin/cloudflare の search.json との差を書く）(b) 店の店名が変わった店の数と一覧（鍵・前の店名・新しい店名） (c) 別名を持つ店の数 を数えてログに書く。LGF-04 の表の 95店と (b)・(c) を突き合わせ、合わない店を書く

止まる条件

* #665 が既に閉じている、またはほかのセッションの着手中コメントがある（1 だけ外して 2・3 を進めてよい）
* 未マージの work/ ブランチが `scripts/generate_log_pages.py` の `group_shops()`・`search_data()`、`assets/log.js` の `prepare()`・結果の投稿の行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で、店の数が origin/cloudflare の search.json と違う（毎朝の取り込みで増えた分は除く。増えた店は鍵で一覧にし、それ以外の差があれば止まる）
* 手順3で、別名を持つ店の数が LGF-04 の 95店から ±10店を超えて違う（毎朝の取り込みで増えた投稿で説明できる差は除く。説明を書く）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 終わったら、マージせずに判断待ちで止まる（プレビューを平野さんが見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見るとよい店（店名が変わった店のうち、投稿の多いもの3店ほど）の `?shop=` のパスを「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGF-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGF-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミットは0件（LGF の識別子の重複の確かめは LGF-01 で済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある（貼られた指示文は改行が詰まって1行目につながっていたが、行の欠けはない）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1104-lgf-alias origin/cloudflare`（起点 23efd467c）
- LGF-04 のログの状態を「判断待ち / 続き: CHAT-1104-LGF-05」にした（ログ先行のコミットに含めた）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）のうち、cloudflare との差に `scripts/generate_log_pages.py`・`assets/log.js` を含むのは `origin/work/1104-lgn-policy` だけ。変えているのは `SOURCE_HTML`（出典の「(i)」の中に /policy/ へのリンク）で、`group_shops()`・`search_data()`・`prepare()`・結果の投稿の行ではないので止まらない（/policy/ は作らない決定でマージされない見込み）

### 1. #665

- open、コメント0件（着手中のコメントなし）。ラベルは「分野: データ」「対象: resource_logs」
- 保留の理由をコメント（https://github.com/retroeater/mj/issues/665#issuecomment-6633847148 ）し、「状況: 保留」を足した（ほかの2つは残した。`get_labels` で3つを確かめた）。閉じていない

### 2. コードとテスト（e0dad02b8）

- `scripts/generate_log_pages.py`: `shop_names()` を足し、`group_shops()` が店の店名と別名（`aliases`）をそこから取る。決め方は投稿の数 → 同数なら最も新しい投稿（投稿は新しい順なので、その表記の最初の位置が小さい方。同じ日は merged_rows の投稿 ID の大きい順）。表記は文字列の完全一致。`SHOP_FIELDS` から `shop` を外した。`search_data()` は店の12要素目に別名の一覧を足し、投稿の6要素目（店名）を消した（投稿は5要素）
- `assets/log.js`: `SHOP.aliases`（11）を足し、`POST.name` を消した。`prepare()` は `shop.names` を店名と別名から作り（検索欄の語・店名のタグ〈`?store=` を含む〉に当たる）、店名の候補（`values.store`）には店の店名だけを入れる。結果の投稿の行の別名の表示を消した
- テスト: `test_log_pages.py` に `test_shop_name_is_the_most_posted`（多い表記・同数で新しい日付・同じ日で投稿 ID の大きい方・空の店名）を足し、`test_group_by_place_id_and_single_posts`・`test_shape` を直した。`log.test.js` は見本の形を直し、別名が候補に出ないことを足した
- docs/notes/log-page.md: 「検索欄」「店名」（タグ）「結果」「データの持ち方」（店の店名・search.json の形）「仮置きの一覧」（店名の候補・検索欄の語）を直した
- 検証:
  - `python3 -m unittest discover -s scripts/tests`: Ran 2002 tests、OK
  - `node --test scripts/tests/js/*.test.js`: pass 22・fail 0
  - 修正前のコードで新しいテストが落ちること（#310）: origin/cloudflare の `scripts`・`assets`・`data` を作業ファイルの置き場に展開し、新しい2つのテストのファイルだけを差し替えて実行。`test_log_pages`: failures=2・errors=1（`test_shape`・`test_shop_name_is_the_most_posted`・`test_group_by_place_id_and_single_posts`）。`log.test.js`: fail 3（店名のタグ・候補・別名）

### 3. 生成と数（dd39e780b）

- ページ名は `python3 scripts/regenerate.py --list` で `log_pages`。`regenerate-page.yml` を `work/1104-lgf-alias` で手動実行（target_page=log_pages）: run 41852711010。生成のコミット dd39e780b（`log/index.html`・`log/search.json` の2ファイル）
- (a) 店 1,740・投稿 2,436。origin/cloudflare（23efd467c）の search.json と同じ数で、足された店・消えた店は0件（毎朝の取り込みの差も無い）。店の要素は全件12・投稿の要素は全件5。店名・別名・投稿の6要素目のほかに変わった値は無い（鍵・駅・URL・座標・駅の候補・投稿の5要素・突き合わせ用の駅・ジャンル・都道府県を店ごとに比べて差0件）
- (c) 別名を持つ店: 95店。LGF-04 の表の 95店と鍵の集合が一致（どちらにだけある店は0件）。各店の「店の店名＋別名」は、前の search.json で集めた表記の集合と全店で一致
- (b) 店の店名が変わった店: 15店（下の表、投稿の多い順）。LGF-04 の表と突き合わせると、案のある 90店は新しい店名が全店で LGF-04 の案と一致。案を出していない要確認の5店（LGF-04 の 1・9・17・91・93番）は決め方どおりの表記になり、このうち 1番（一品香 → 新宿地下ラーメン）と 17番（やまぐち → 巌哲）は店名が変わった。合わない店は無い
- log/index.html の差はカテゴリ「投稿回数の多い店」のカードの店名4つ（中華そば きみのあーる → 中華蕎麦 きみのあーる、焼肉屋 Kazu → 焼肉家 Kazu、鮨 酒 肴 杉玉 → 杉玉、一品香 → 新宿地下ラーメン）だけ

| # | 鍵（place_id） | 前の店名 | 新しい店名 | 投稿の数 |
|---|---|---|---|---|
| 1 | ChIJzyJ4NBiNGGARlmBtxAiDwsk | 中華そば きみのあーる | 中華蕎麦 きみのあーる | 34 |
| 2 | ChIJc9ZjLluMGGARVVMatB5p8lc | 焼肉屋 Kazu | 焼肉家 Kazu | 18 |
| 3 | ChIJA5fGqXCNGGARRqON_S3nADU | 鮨 酒 肴 杉玉 | 杉玉 | 17 |
| 4 | ChIJkdDwfBKNGGAR2oXHz9NjCc8 | 一品香 | 新宿地下ラーメン | 9 |
| 5 | ChIJQbWaOxiNGGAR2Wr2BfXxEU8 | やまぐち | 巌哲 | 5 |
| 6 | ChIJe1Y0JxmLGGARZ6sC3dtQKWI | 目黒のさんま菜の花 | 目黒のさんま 菜の花 | 4 |
| 7 | ChIJAxKeZNmMGGARQ4EOGHqSdIg | 凪 | すごい煮干しラーメン凪 | 4 |
| 8 | ChIJA-YrZAGMGGARXXj2g_kPcOk | ルードメール | ルー・ド・メール | 3 |
| 9 | ChIJcWT7lMLtGGAR456BhfnIycU | SHUN RAKU、cafe イチフジ、オクシタニアル、ミッドナイトブルー | SHUN RAKU | 3 |
| 10 | ChIJTbD0tqGLGGAR_F9iW6BGesQ | コメダ | コメダ珈琲店 | 3 |
| 11 | ChIJ86P7b16NGGARQnPB24N3lIY | 蔵 | 炭火焼珈琲 蔵 | 3 |
| 12 | ChIJ4bpfn1WJGGARO1OEAby5VeA | 肉寿司 肉和食 KINTAN | KINTAN | 3 |
| 13 | ChIJFUmflX2LGGAR72gjQHIiWKM | 富喜製麺所 | 富喜製麺研究所 | 3 |
| 14 | ChIJHYXJaAftGGARh4scOx6eXho | hobomizu | ホボミズ | 3 |
| 15 | ChIJ49tXz1PtGGARIlvW5mJPmx8 | よしかわ | 寿製麺よしかわ | 3 |

### 4. 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに、指示の目的・決定と `git diff origin/cloudflare...e0dad02b8`（scripts・assets・docs/notes）、CLAUDE.md「コード規約」を渡した（1回目）。サブエージェントは変更後と、生成スクリプト・log.js だけを戻した版の両方でテストを回し（変更後: Python 39件・JS 22件が成功。戻した版: Python 3件・JS 3件が落ちる）、目的・既存の形・ほかへの影響・検査・文書を見た。動作の不具合の指摘は無し。

| 指摘 | 扱い |
|---|---|
| 店の URL（`shop_link()`、`SHOP_FIELDS` の `shop_url`）の `query=` は「店の URL を持つ最も新しい投稿」の店名で、代表の店名と違うことがある。query は place ID が見つからないときだけ使われ実害はほぼ無いが、log-page.md の「query=<店名>」と厳密には合わない | 文書を直した（93f3db7a2。log-page.md の店の URL の項に、その URL を取った投稿の店名で店の店名と違うことがあると書いた）。URL の組み立ては変えていない |
| 「（店名非公開）」の扱いが投稿の数で決まるようになった（非公開が多数なら実名は別名として検索に残る） | 意図どおり（前も投稿の店名として実名を search.json に持ち、結果に出していたので悪化ではない。今のデータに該当する店は0） |
| 差分に search.json の再生成が入っていない（古い JSON を新しい JS が読むと別名が検索に当たらない） | 意図どおり（レビューに渡した e0dad02b8 の後に、同じブランチで生成のコミット dd39e780b を積んだ。マージは両方を一緒に入れる） |
| `?store=<別名>` を `readParams()` から通す検査が無い（任意） | 見送り（`readParams()` は値を確かめずにタグにし、店名のタグが別名に当たることは `log.test.js` で確かめている。プレビューで `?store=中華そば きみのあーる` が 中華蕎麦 きみのあーる の 34件を出すことを確かめた） |
| コード・テストのコメントに `CHAT-1104-LGF-05` を出典として書いている（ログは定期削除される） | 直した（93f3db7a2。assets/log.js・generate_log_pages.py・log.test.js のコメントを日付だけにした。log-page.md は既存の書き方に合わせて残した） |

2回目のレビューは、1回目の後の差分がコメントと文書の各1行だけなのでしていない。93f3db7a2 の後も `python3 -m unittest discover -s scripts/tests -p 'test_log*'` は OK、`node --test scripts/tests/js/*.test.js` は pass 22・fail 0。コメントの変更は生成物に影響しない（生成物は dd39e780b のまま）。

### 5. プレビュー

- dd39e780b（生成の後）の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、幅 390）で開いた
  - `?shop=ChIJzyJ4NBiNGGARlmBtxAiDwsk`: 店名「中華蕎麦 きみのあーる」・投稿 34件。投稿の行に「中華そば きみのあーる」「らぁ麺や 弾」は出ない
  - `?tag=HARBS`: 「ハーブス」の店・投稿 2件（別名で検索欄の語が当たる）
  - `?store=中華そば きみのあーる`（別名の店名のタグ）: 「中華蕎麦 きみのあーる」・34件
  - 検索欄に「きみのあーる」: 店名の候補は「中華蕎麦 きみのあーる 1店」だけ（別名は出ない）
- 93f3db7a2（コメントと文書だけ）の Workers Builds は、最終報告を書く時点で in_progress のまま（20分ほど）。生成物は dd39e780b と同じなので、プレビューの確かめは dd39e780b の版で行った

### 決定の記録

- 指示文の「決定」節を docs/decisions/operations.md の末尾に「2026-11-04（CHAT-1104-LGF-05）」として足した。作業中の平野さんの回答は無い

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-lgf-alias
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgf-alias/docs/logs/CHAT-1104-LGF-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgf-alias
- 確認用URL: プレビューあり（URL は最終報告）。この版で見える印: `?shop=ChIJzyJ4NBiNGGARlmBtxAiDwsk` の店名が「中華蕎麦 きみのあーる」で、投稿の行に別の店名が出ない
- マージ: 未（平野さんの判断待ち）
- issue: #665（保留にした）
- 判断が必要なこと:
  - プレビューを見て、マージしてよいか。見るとよい店（店名が変わった店のうち投稿の多い3店）: `/log/?shop=ChIJzyJ4NBiNGGARlmBtxAiDwsk`（中華そば きみのあーる → 中華蕎麦 きみのあーる、34件）・`/log/?shop=ChIJc9ZjLluMGGARVVMatB5p8lc`（焼肉屋 Kazu → 焼肉家 Kazu、18件）・`/log/?shop=ChIJA5fGqXCNGGARRqON_S3nADU`（鮨 酒 肴 杉玉 → 杉玉、17件）
  - 要確認の店のうち2店は、決め方どおりで店名が別の店の名前に変わった: `/log/?shop=ChIJkdDwfBKNGGAR2oXHz9NjCc8`（一品香 → 新宿地下ラーメン。カテゴリ「投稿回数の多い店」のカードにも出る）・`/log/?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`（やまぐち → 巌哲）。【3】で直す（案 A）までこのままでよいか
  - 店の URL の `query=` は店の URL を持つ最も新しい投稿の店名のまま（店の店名と違うことがある。place ID が見つからないときだけ使われる）。代表の店名で組み直すか（経過の「4.」）
- 未確認の項目: 93f3db7a2（コメントと文書だけ）の Workers Builds のプレビューが in_progress のまま（生成物は dd39e780b の版と同じで、確かめはその版で行った）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1a06d89d）: https://github.com/retroeater/mj-logs/tree/main/guide/1a06d89d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
