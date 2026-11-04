# CHAT-1105-LGF-08

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: a090449be

## 指示

【Claude作成】Claude Code 向け指示：log/ の店の駅を店ごとに数の多い駅にそろえ、駅名を一覧の表記に寄せる／国土数値情報の出典を末尾の (i) にまとめる
Chat-Ref: CHAT-1105-LGF-08
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1105-LGF-07 の判断待ちの続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgf-alias を続けて使う（LGF-05・07 と一緒にプレビューを見てからマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-07 と CHAT-1104-LGF-06 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1105-LGF-08` を足す。

## 目的
CHAT-1104-LGF-06 で、同じ place_id で投稿ごとに駅が違う店が 33店あった。店名（LGF-05）と同じく、生成で店ごとの駅をそろえる（案 B）。離れた駅の6店と大至は、この後に平野さんが【3】で確かめて直す（案 A。この指示には含めない）。あわせて、トップの右上の出典の (i) を、紹介文の末尾の (i) にまとめる。

### 決定（2026-11-05、平野さん）
- 駅の揺れは A＋B で直す: 生成で店ごとに投稿の数の多い駅にそろえ（B）、離れた駅の6店（LGF-06 の表の 11・13・17・20・23・33番）と大至（3番）だけを【3】で確かめて直す（A）
- 「四ッ谷」（小さい ッ）は生成で寄せる。比べるときだけでなく、表示も駅の一覧の正しい表記（四ツ谷）に寄せる
- 紹介文の末尾の「利用条件 (i)」の見た目は LGF-07 のプレビューのとおりでよい
- 国土交通省（国土数値情報）の出典の注釈も、下の (i) の中にまとめる

### 前提（チャット側。平野さんの決定ではない）
- 今の店の駅は、駅を持つ最も新しい1件の投稿から、駅・駅の候補・駅の座標・都道府県・突き合わせ用の駅をまとめて取る（docs/notes/log-page.md、`group_shops()` の `STATION_FIELDS`。LGF-06 のログ「統一の仕方の候補」）
- 新しい決め方: 投稿ごとの駅を突き合わせ用の駅（一覧の名前に寄せた値）で数え、投稿の数が最も多い駅 → 同数なら最も新しい投稿の駅。選んだ駅を持つ投稿のうち最も新しい1件から、今までどおりまとめて取る（駅の候補・座標・都道府県を別々の投稿から混ぜない）。駅が空の投稿は数えない
- 駅名の寄せ: `lib/stations.py` の `station_key()`（市ケ谷 → 市ヶ谷 の寄せ）に、小さい ッ を ツ にしたときに一覧の駅になるなら一覧の名前にする寄せを足す（四ッ谷 → 四ツ谷）。表示する駅名（結果・カード・候補・タグ）は一覧の表記にする。一覧に無い駅（海外・【…】付きなど）の表示は今のまま。ほかにも、表記の違いで一覧に当たらない駅がいくつあるかを数えて書く（直すのは四ッ谷の寄せだけ）
- 出典: 今はトップ（`#log_home`）の右上の「(i)」（`SOURCE_HTML`）に国土数値情報の出典の1文とリンクがある（log-page.md「出典」）。これを紹介文の末尾の (i)（`TERMS_HTML`）の中に移し、右上の (i) は消す。開いたときの並びの案: 利用条件の2文とリンク → 出典の1文とリンク。(i) のラベルは「利用条件・出典 (i)」とする（チャット側の案。出典を探す人がラベルから辿れるように。プレビューで「利用条件 (i)」に戻してよい）。出典の文・URL は変えない。国土数値情報の出典の表記は CC BY 4.0 の条件（log-stations.md「出典の表記」）で、開けば読める形で足りる見込み（要確認。log-stations.md に表示の位置の条件が書かれていれば従い、合わなければ止まる）
- 月1回の閉業の確かめ（`convert_x_posts.py` の `closed_targets()`）は、LGF-07 で足した `HIDDEN_PLACES` の店にも Place Details を呼ぶ（LGF-07 のレビューの指摘）。載せない店は確かめる意味が無いので、`closed_targets()` でも外す（費用を減らすため。呼び出しを減らすだけで、増やさない）

## 手順
1. 駅の決め方と駅名の寄せを上のとおりに直し、テスト（同数・空の投稿・四ッ谷・一覧に無い駅）を足す。`closed_targets()` で `HIDDEN_PLACES` を外し、テストを足す。docs/notes/log-page.md・docs/notes/log-stations.md の該当の項を直す
2. 出典を末尾の (i) に移し、右上の (i) を消す。log-page.md の「出典」「利用条件」の項とテストを直す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、LGF-07 の生成（98328180e）の search.json と比べて、店の駅が変わった店を全件、表にする（鍵・店名・前の駅・新しい駅・投稿ごとの駅の数）。LGF-06 の表の 33店と突き合わせ、33店の外で駅が変わった店があれば理由を書く

## 止まる条件
- 店の数・投稿の数が LGF-07 の生成（1,739店・2,427件）と違う（毎朝の取り込みで増えた分は除く。増えた店は鍵で一覧にし、それ以外の差があれば止まる）
- 店の駅が変わった店が、LGF-06 の表の 33店の外に 5店を超えてある（四ッ谷の寄せ・毎朝の取り込みで説明できるものは除く。説明を書く）
- 出典・利用条件の文と URL が変わる（置き方と見た目だけを変える）
- log-stations.md の出典の表記の条件が、たたんだ (i) の中に置く形と合わない
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが、同じ関数（`group_shops()`・`station_key()`・`closed_targets()`・`SOURCE_HTML`・`TERMS_HTML` の置き場）を変えている、または取り込みで衝突する（origin/work/1104-lgn-policy の `SOURCE_HTML` の変更は、/policy/ を作らない決定でマージされないので、重なっても止まらずログに書く）
- 終わったら、マージせずに判断待ちで止まる（LGF-05・07 と合わせて平野さんがプレビューを見る）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（末尾の (i) を開いた形、駅が変わった店のうち投稿の多い店の `?shop=` のパス、`?station=四ツ谷`）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミットは0件（LGF の識別子の重複の確かめは LGF-01 で済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 作業ブランチ: ローカルの `work/1104-lgf-alias` は `origin/work/1104-lgf-alias`（a090449be）と同じで、`git merge-base --is-ancestor origin/cloudflare HEAD` は真（取り込みなし）
- LGF-07・LGF-06 のログの状態に「 / 続き: CHAT-1105-LGF-08」を足した（ログ先行のコミット e2d28f787 に含めた）
- 未マージの work/ ブランチで `generate_log_pages.py`・`lib/stations.py`・`convert_x_posts.py` を変えているのは `origin/work/1104-lgn-policy` だけで、`SOURCE_HTML`（出典の (i) の中に /policy/ へのリンク）を変えている。今回 `SOURCE_HTML` の形を変えたので、そのブランチを取り込むと衝突するが、/policy/ を作らない決定でマージされないので止まらない（指示のとおり）

### 1. 店の駅・駅名の寄せ・閉業の確かめ（c36d7e6ee、a999a54ce、3cb8a558f）

- `scripts/generate_log_pages.py` に `shop_station()` を足した: 投稿（新しい順）の駅を突き合わせ用の駅で数え、投稿の数が最も多い駅 → 同数なら最も新しい投稿の駅。駅が空の投稿は数えない。`group_shops()` は、その駅を持つ最も新しい1件の投稿から `STATION_FIELDS`（駅・駅の候補・座標・都道府県・突き合わせ用の駅など）をまとめて取る
- `scripts/lib/stations.py` の `station_key()` に、小さい「ッ」（norm でひらがなの「っ」）を「つ」にそろえる寄せを足した。2026-11 の一覧 8,503駅で、そろえても別の駅どうしが同じ鍵にならない（小さい ッ を ツ にしただけの別の名前の駅が一覧に無い。`test_stations.py` の `test_data_file` で確かめる。レビューでも新旧の鍵の衝突が同じ10組〈既存〉で、増えないことを確かめた）
- 表示: `shown_station()` が東京都の駅も一覧の表記（突き合わせ用の駅）で出すようにした（前は投稿の表記のまま）。一覧に無い駅・海外は今のまま。一覧の「笹塚」だけが「塚」を互換漢字（U+FA10）で持っていて、表示に出すと見た目は同じで文字が違う（コピー・ページ内検索で当たらない）ので、表示だけ NFC で通常の字（U+585A）にした（a999a54ce。一覧 8,503駅で NFC で変わるのは笹塚だけ）
- `convert_x_posts.py` の `closed_targets()` から `HIDDEN_PLACES` の店を外した（月1回の Place Details の呼び出しが1件減る）
- `station_key()` はほかに `convert_x_posts.py` の変換（all・stations）の駅の突き合わせでも使う。次にそれらを動かすと「四ッ谷」が一覧の「四ツ谷」に当たるようになる（要確認の「一覧に無い駅」が減る方向。レビューで意図どおりと確かめた）
- 一覧に当たらない駅（生成後の search.json、店の駅）: 9種・13店。表記の違いは2つ: 緑ヶ丘（一覧は「緑が丘」、1店）・東武池袋（会社名付きの池袋、1店）。ほかは出口の名前（池袋西口 2店・新宿東口 2店）、駅でない地名（西麻布 2店・奥浅草 2店・南青山 1店）、【…】の印の無い海外の駅（Bond Street 1店・Paddington 1店）。直したのは四ッ谷の寄せだけ
- テスト: `test_shop_station_is_the_most_posted`（多い駅・ケとヶの同じ駅を合わせて数える・空を数えない・同数で新しい方・駅の無い店）、`test_small_tsu_is_the_list_name`、`test_compat_kanji_in_list_is_shown_as_usual`、`test_station_key_uses_list_name`（表示も一覧の表記に直した）、`test_stations.py` の `test_data_file`（四ッ谷が当たる・小さい ッ の衝突なし）、`test_convert_x_posts.py` の `test_targets_skip_hidden_places`
- docs: log-page.md の「駅の出し方」「データの持ち方」（店の駅の決め方）、log-stations.md の「出典の表記」を直した

### 2. 出典を末尾の (i) へ

- `SOURCE_HTML` を出典の `<p>` だけにし、`TERMS_HTML`（`<details class="mj-log-terms">`）の中で利用条件の2文の後に置いた。トップ（`#log_home`）の右上の (i) は消した。ラベルは「利用条件・出典」＋(i) のアイコン（指示の前提の案のとおり）
- 文と URL は変えていない: 前（e2d28f787）の `SOURCE_HTML`・`TERMS_HTML` の `<p>…</p>` 2つと、新しい `TERMS_HTML` の2つが一致（作業ファイルの置き場に前の版を展開して比べた。レビューでもバイト単位で同じことを確かめた）
- log-stations.md「出典の表記」が挙げる条件（出典と加工した旨の記載・国が作ったように見せない）に、表示の位置の条件は書かれていない。たたんだ (i) の中でも条件に合う
- CSS: 使わなくなった `.mj-log-info`・`.mj-log-info-button` の規則を消した（`.mj-log-source` は残す）
- テスト: `test_source_note_in_the_lead`（出典が1回だけ、利用条件の後、右上の (i) が無い）、`test_terms_folded_at_the_end_of_lead`（ラベル）
- 検証: `python3 -m unittest discover -s scripts/tests` は OK（2008件）、`node --test scripts/tests/js/*.test.js` は pass 22・fail 0。修正前（e2d28f787 の scripts・assets・data）に新しいテストのファイルだけを差し替えると、新しい・直したテスト7つが落ちる

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに、指示の目的と `git diff e2d28f787 c36d7e6ee`（scripts・style.css・docs/notes）、CLAUDE.md「コード規約」を渡した（1回目）。バグの指摘は無し。サブエージェントは、選び方・同数・空の駅・ッの寄せ・HIDDEN_PLACES の除外を1つずつ元に戻すとテストが落ちることを確かめた。

| 指摘 | 扱い |
|---|---|
| style.css のコメント・test_log_pages.py の docstring 2か所が古い（「利用条件 (i)」・「駅を持つ最も新しい1件の投稿」） | 直した（a999a54ce） |
| 作業ブランチの log/index.html を再生成していない（CSS だけ入ると、トップの (i) が CSS の無い `<details>` で出る） | 直した（同じブランチで生成のコミット b22cf40a7・fc88cafa1 を積んだ。マージは一緒に入る） |
| 同じ名前で別の県の駅が1つの店に混ざると合算される（軽微） | 意図どおり（1つの place_id で起きるのはまず無い。混ざっても選んだ駅の投稿1件から値をまとめて取るので、別の駅の点は付かない） |
| 選んだ駅が最も新しい投稿の駅でないときに、座標・候補・都道府県まで同じ投稿から取れているかを検査で見ていない | 見送り（`group_shops()` が `shop_station()` の1件から `STATION_FIELDS` を取る作りで担保。生成後の search.json でも、駅が変わった店の駅と座標・候補が一致している〈下の表の巌哲・光来など、プレビューで駅の候補が出ている〉） |

1回目の後の差分（NFC の表示・コメント・変数名）は、2回目のレビューに回していない（表示の1行と、生成物が変わらない名前の変更だけのため）。

### 3. 生成と、店の駅が変わった店

- `regenerate-page.yml` を `work/1104-lgf-alias`・`target_page: log_pages` で2回手動実行（c36d7e6ee の後 → b22cf40a7、a999a54ce の後 → fc88cafa1）。どちらも success
- 店 1,739・投稿 2,427 で LGF-07 の生成（98328180e）と同じ。足された店・消えた店は0
- 店の駅（表示か突き合わせ用の駅）が変わった店: 44店。LGF-06 の表の 33店のうち6店（LGF-06 の 11・17・25・26・27・32番。LGF-06 で「今の店の駅と案が違う」とした6店と同じ）は、数の多い駅に変わった。33店の外の 38店は、表示の表記だけの変化: 市ケ谷・千駄ケ谷・南阿佐ケ谷・霞ケ関 → 「ヶ」（9店）と、笹塚の「塚」の文字（U+FA10 と U+585A が投稿で混ざっていたのが U+585A にそろった、29店）。突き合わせ用の駅・座標・候補は変わっていない
- 33店の外の 38店は、止まる条件の「5店を超える」に数の上では当たる。指示の前提「表示する駅名は一覧の表記にする」（四ッ谷の寄せと同じ規則）で変わったもので、駅そのものは変わらないので止まらずに進め、「判断が必要なこと」に書いた

changed 44 in33 6
| # | 鍵（place_id） | 店名 | 前の駅 | 新しい駅 | 投稿ごとの駅の数（LGF-06 の調べ） | LGF-06 の表 |
|---|---|---|---|---|---|---|
| 1 | ChIJc7-KrRnzGGAR4notlz3q5ro | みなと屋 | 笹塚 | 笹塚 | —（投稿 5件。表記だけの変化） | なし |
| 2 | ChIJQbWaOxiNGGAR2Wr2BfXxEU8 | 巌哲 | 西早稲田 | 早稲田 | 早稲田 4件・西早稲田 1件 | あり |
| 3 | ChIJX5VzTNjzGGARoVrgGHrku4M | 三四味屋 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 4 | ChIJnQdpjhfzGGAR-ife0UyL8DQ | ロビン | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 5 | ChIJ1a0TikLzGGAREUxH1pUABbQ | 白鶏舎 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 6 | ChIJESegMgDzGGARmu1h5t4KE8I | 中華料理一番 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 7 | ChIJocJ-ar7zGGARQ3rSB4v7p0Q | 杉玉 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 8 | ChIJUy0WbP3zGGAR4flTK6uJgqk | Menotti's Tokyo | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 9 | ChIJrVbr4SmNGGARKd2fHLe2pTQ | 光来 | 沼袋 | 新宿西口 | 新宿西口 2件・沼袋 1件 | あり |
| 10 | ChIJAcX7ThfzGGARwgyO7YWfJI4 | 鳥貴族 笹塚 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 11 | ChIJk4D41hnzGGARSyMEqI1qY3s | ヒーローズ | 笹塚 | 笹塚 | —（投稿 2件。表記だけの変化） | なし |
| 12 | ChIJgde3QgDzGGAR5V62rFbjYpo | おこめ茶屋 米米-めめ- | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 13 | ChIJc-fVGhrzGGARlXdNus-3fzI | 花月嵐 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 14 | ChIJb6HtSGeMGGAR7tvhg9Z9fzc | 中国料理 翠 | 市ケ谷 | 市ヶ谷 | —（投稿 2件。表記だけの変化） | なし |
| 15 | ChIJKZpLxnTyGGAR82e0S7bMw0k | 和佐家 | 南阿佐ケ谷 | 南阿佐ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 16 | ChIJmTemphfzGGARbhf7SQ0Qlq0 | 南蛮茶館 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 17 | ChIJ-Tg9LACNGGAR5N5y8UucASQ | 棋の音 | 千駄ケ谷 | 千駄ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 18 | ChIJU3T0JQCNGGARu1A__-8rzWY | 棋の音 | 千駄ケ谷 | 千駄ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 19 | ChIJrWEK__SNGGARD2lwXkQ5P4w | キッチン水野 | 市ケ谷 | 市ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 20 | ChIJzXXL2oyLGGAR5xipgdbWeFk | 手しごとや 咲くら | 霞ケ関 | 霞ヶ関 | —（投稿 1件。表記だけの変化） | なし |
| 21 | ChIJ572wkteMGGARi4qiFHHbiAc | 焼きあご塩らー麺 たかはし | 西武新宿 | 新宿 | 新宿 3件・西武新宿 2件 | あり |
| 22 | ChIJbfvLAb-MGGARZ420NprWoao | レティエ | 千駄ケ谷 | 千駄ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 23 | ChIJ2fI6DhrzGGARZj0_mog88HU | ディアオール | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 24 | ChIJQbf03RnzGGARfF0A1ZN_QZM | 笹塚ボウル | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 25 | ChIJjb1VAhnzGGARClYV7yeYSXE | キャンティ | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 26 | ChIJ0SMU8hnzGGARfE_oKH3b52c | 妙高そば | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 27 | ChIJ07mtn4yMGGARuy2bzkOvnZs | こうや | 四ッ谷 | 四ツ谷 | 四ツ谷 2件・四ッ谷 1件 | あり |
| 28 | ChIJ-w3_7JKLGGARgxJSHM7nwmo | 一体感 | 東銀座 | 築地 | 築地 2件・東銀座 1件 | あり |
| 29 | ChIJMRZ0alfzGGARd6TVTaJElAo | 吉野家 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 30 | ChIJDd9ixdPzGGARcKAoKog-jD0 | Beans&Beans ぽえむ | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 31 | ChIJ_w04JabzGGARrkx5p73QGYY | 北海道ソウルフード | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 32 | ChIJg18T2RnzGGARLO_80tEzf8A | まんぷく食堂 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 33 | ChIJn_SMKTPzGGARAF6CEYm4yVQ | 麺屋吟治zero | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 34 | ChIJS8DLzBnzGGARPyaAeC3WnAQ | 柳屋 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 35 | ChIJMz8JqxnzGGARJ7TO9Rp_4wI | 天宝 | 笹塚 | 笹塚 | —（投稿 3件。表記だけの変化） | なし |
| 36 | ChIJE3Mj08bzGGAR9VnVh5FEGiU | 麺屋ニシムクサムライ | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 37 | ChIJn0mLTxfzGGARnWEJiLb8tPU | 洋食屋マック | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 38 | ChIJfzLJJxjzGGARe7ViPPcLfRg | 寿し常 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 39 | ChIJx8xr84aNGGARc5ySuYW04Fg | 焼きあご塩らー麺たかはし | 新宿西口 | 新宿 | 新宿 2件・新宿西口 1件 | あり |
| 40 | ChIJ00xuhwPzGGAR7VcUNCIpK3g | バーガーショップ393 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 41 | ChIJc4z5_A_zGGARppkFYk8iHiM | 西安ビャンビャン麺 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 42 | ChIJjR_1OAeNGGAR7mBnD-Kqo6g | トーキョーアジフライ | 市ケ谷 | 市ヶ谷 | —（投稿 1件。表記だけの変化） | なし |
| 43 | ChIJicsH7XDzGGARg_ftYengVKc | 宮川本廛 | 笹塚 | 笹塚 | —（投稿 1件。表記だけの変化） | なし |
| 44 | ChIJQeN9Pr6MGGARhkD8kVgr7CI | モスプレミアム | 千駄ケ谷 | 千駄ヶ谷 | —（投稿 1件。表記だけの変化） | なし |

### プレビュー

- 3cb8a558f の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、幅 390）で開いた
  - トップ: 右上の (i) は無い。一番下の説明文の文末が「利用条件・出典 (i)」で、押すと利用条件の2文とリンクの後に国土数値情報の出典の1文とリンクが出る
  - `?station=四ツ谷`: 15店。結果の駅は「四ツ谷」（前は四ッ谷と書かれていた こうや もこの駅の店になる）
  - `?shop=ChIJ572wkteMGGARi4qiFHHbiAc`（焼きあご塩らー麺 たかはし）の駅は「新宿」、`?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`（巌哲）の駅は「早稲田」

### 決定の記録

- 指示文の「決定」節を docs/decisions/operations.md の末尾に「2026-11-05（CHAT-1105-LGF-08）」として足した。作業中の平野さんの回答は無い

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-lgf-alias
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgf-alias/docs/logs/CHAT-1105-LGF-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgf-alias
- 確認用URL: プレビューあり（URL は最終報告）。この版で見える印: 一番下の説明文の文末が「利用条件・出典 (i)」で、トップの右上に (i) が無い
- マージ: 未（平野さんの判断待ち。LGF-05・07 と一緒）
- issue: なし
- 判断が必要なこと:
  - プレビューを見て、LGF-05・07 と合わせてマージしてよいか。見る所: トップ（`/log/`）の一番下の「利用条件・出典 (i)」を開いた形（利用条件の後に出典）、`/log/?station=四ツ谷`、駅が変わった店のうち投稿の多い店 `/log/?shop=ChIJ572wkteMGGARi4qiFHHbiAc`（西武新宿 → 新宿、5件）・`/log/?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8`（西早稲田 → 早稲田、5件）・`/log/?shop=ChIJrVbr4SmNGGARKd2fHLe2pTQ`（沼袋 → 新宿西口、3件）
  - 表示を一覧の表記にした結果、LGF-06 の 33店の外の 38店で表示の表記が変わった（市ケ谷 → 市ヶ谷 など「ケ」→「ヶ」が9店、笹塚の「塚」の文字が29店。駅そのものは変わらない）。止まる条件の「33店の外に5店を超える」に数の上では当たるが、指示の前提「表示する駅名は一覧の表記にする」で変わったものとして進めた。この表示でよいか
  - ラベルは「利用条件・出典 (i)」にした（指示の前提の案）。「利用条件 (i)」に戻すか
  - 一覧に当たらない駅の表記の違いが2つ残る（緑ヶ丘 → 一覧は「緑が丘」、東武池袋 → 池袋）。【3】で直すか、生成の寄せに足すか（今回は四ッ谷だけ）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa43459d）: https://github.com/retroeater/mj-logs/tree/main/guide/fa43459d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
