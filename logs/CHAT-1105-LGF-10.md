# CHAT-1105-LGF-10

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1105-lgf-ui
- 着手時HEAD: c5721c607

## 指示

【Claude作成】Claude Code 向け指示：log/ の期間限定の出店2件を載せない／「現在地から探す」を検索欄の右のアイコンにする／検索結果の並び順を確かめる Chat-Ref: CHAT-1105-LGF-10 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1105-LGF-09 は cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgf-ui の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-lgf-ui を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-lgf-ui origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-10` を足す。

目的
LGF-09 の判断待ちの残りと、平野さんの新しい決定を進める。(1) 期間限定の出店2件を載せない (2) 「現在地から探す」を検索欄の右のアイコンにする (3) 検索結果の並び順（特に同じ距離の店どうし）を確かめる（並びは変えない）。
決定（2026-11-05、平野さん）

* 期間限定の出店の候補のうち「寒鱈まつり」（鍵 p2022849721023299710、place_id なし）と「東京ラーメンショー極み麺」（place_id ChIJT2WXm-aNGGAR24zlaGQ7fiQ）は載せない。LGF-07 の候補のほかの7件は載せたまま
* 「現在地から探す」は、今の文字のボタンをやめ、検索欄の右のアイコンにする（チャットで並べた案 B）

前提（チャット側。平野さんの決定ではない）

* 載せない店は `scripts/generate_log_pages.py` の `HIDDEN_PLACES`（LGF-07）に理由つきで持つ。place_id の無い店の鍵は「p<投稿ID>」なので、`HIDDEN_PLACES` の鍵を「店の鍵」（place_id か p<投稿ID>）として扱えば、寒鱈まつりも同じ一覧で外せる（シートの【3】は触らない）。外す処理が place_id だけを見ているなら、店の鍵で外すように直す（名前を `HIDDEN_SHOPS` などに変えてよい。変えたら log-page.md・テスト・`convert_x_posts.py` の `closed_targets()` の参照もそろえる）。理由は2件とも「期間限定の催事の出店で、検索した人が訪れられないため」。docs/notes/log-page.md「載せない投稿・店」の表に足す
* アイコン（チャットで並べた案 B）: 検索欄の右端の中に、区切りの細い線と照準のアイコン（現在地を表す一般的な形。自前のインライン SVG。外部のアイコンのフォントは使わない。log/ の地図のピンと同じ作り）。押せる範囲は 44px 以上。`aria-label` と `title` は「現在地から探す」。押した後の動き（位置を聞く・「×現在地」のタグ・取れないときの「駅名で探してください」・駅の点のある店が無いときの準備中）は今のまま。準備中の扱いは、押せないアイコンにするか、押したら文で知らせるかを、今のボタンの作りに合わせて決めてよい（決めた形を報告に書く）。今の文字のボタンは消す
* 並び順: docs/notes/log-page.md では、駅のタグは駅どうしの距離の近い順（その駅の店が先）、現在地は店の駅の点までの距離の近い順。それ以外（検索欄の語・店名・投稿者・投稿年などのタグ）の並びと、距離が同じ店どうしの並び（同じ駅の店どうしなど）は文書に無い（要確認）。平野さんは「より近い駅・より新しい投稿のある店が先」がよいと考えている
* ほかの共通の部品（`assets/league.js` の検索欄など）を変えるときは、使っているほかのページへの影響を報告に書く

手順

1. 2件を載せない一覧に足し（上の前提のとおり）、テストと log-page.md を直す
2. 「現在地から探す」をアイコンにし、テスト（`scripts/tests/js/log.test.js` ほか）と log-page.md（「現在地から探す」「仮置きの一覧」）を直す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる。`regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、店・投稿の数の LGF-09 のマージ（a428af80f）の search.json との差と、外れた店・投稿を書く。プレビュー（Playwright、UA に mj-internal、幅 390）で、検索欄の右のアイコン・押した後の「×現在地」のタグを撮って確かめる（位置は Playwright の geolocation で神保町あたりにしてよい）
3. 並び順を `assets/log.js` で確かめ、探し方ごと（語だけ・駅のタグ・現在地・店名／投稿者／投稿年のタグ・タグの組み合わせ）に、1つ目の並びの鍵と、同じ値の店どうしの並び（2つ目の鍵）を表にしてログに書く。「近い駅 → 新しい投稿のある店」になっていない探し方があれば、直す場合の変え方（関数と行）を「判断が必要なこと」に書く。この指示では並びを変えない

止まる条件

* 手順2の数の差が、2件の店（寒鱈まつり 1件・東京ラーメンショー極み麺 1件）と毎朝の取り込みの分だけで説明できない
* 共通の部品を変えた結果、ほかのページの生成物が変わる（変わるなら、変わるページと理由を書いて止まる）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが `assets/log.js`・`scripts/generate_log_pages.py` の同じ関数を変えている、または取り込みで衝突する（origin/work/1104-lgn-policy の `SOURCE_HTML` は、マージされない見込みなので重なっても止まらずログに書く）
* 終わったら、マージせずに判断待ちで止まる（平野さんがプレビューを見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（トップの検索欄、`?tag=` で探した後の検索欄）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミットは0件（LGF の識別子の重複の確かめは LGF-01 で済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある（貼られた指示文は改行が詰まって1行目につながっていたが、行の欠けはない）
- 作業ブランチ: ローカル・リモートとも無かったので `git checkout -b work/1105-lgf-ui origin/cloudflare`（起点 c5721c607）
- LGF-09 のログの状態に「 / 続き: CHAT-1105-LGF-10」を足した（ログ先行のコミット 052dacb2e に含めた）
- 未マージの work/ ブランチで `assets/log.js`・`generate_log_pages.py`・`convert_x_posts.py` を変えているのは `origin/work/1104-lgn-policy`（`SOURCE_HTML`）だけ。今回の関数とは重ならない

### 1. 期間限定の出店2件（b7f24a0b2・df0fd116c）

- 今までの外し方は place_id だけを見ていた（LGF-07 の `HIDDEN_PLACES` を `drop_closed()` で外す）。寒鱈まつりは place_id が無く、店の鍵が「p2022849721023299710」なので外せなかった
- `HIDDEN_PLACES` を `HIDDEN_SHOPS`（`{店の鍵: 理由}`）に改め、`shop_key()`（place_id、空なら「p<投稿ID>」。`group_shops()` の鍵と同じ）と `drop_hidden()` を足した。2件の理由は「期間限定の催事の出店で、検索した人が訪れられないため」。`convert_x_posts.py` の `closed_targets()`・テスト・log-page.md の参照もそろえた（リポジトリ全体で `HIDDEN_PLACES` の残りは0件）
- 「p<投稿ID>」の鍵は、その投稿1件にだけ効く（【3】でその投稿に place_id を付けると鍵が変わって載る。同じ催事の別の投稿も別の店になる）。レビューの指摘で、コメントと log-page.md の表に書いた
- テスト: `HiddenShopsTest`（理由・鍵の形〈place_id か p<数字>〉、place_id の店と p の店の両方が search.json・カテゴリに出ず、ログの行に「2店」、`shop_key()` が `group_shops()` の鍵と同じ）、`test_targets_skip_hidden_places`

### 2. 現在地から探すのアイコン（56f284b6e・df0fd116c）

- `body_html()`: 検索欄（`.mj-houou-search`）の中、入力を消す × の後に、細い区切りの線（`.mj-log-geo-sep`）と照準のアイコンのボタン（`id="log_geo"`、`GEO_ICON` の自前のインライン SVG、`aria-label`・`title` は「現在地から探す」）を置き、前の検索欄の下の文字のボタンの行（`.mj-log-tools`）を消した
- 準備中: 今のボタンと同じく `disabled` にした（押せないアイコン。`aria-label`・`title` は「現在地から探す（準備中）」、色は灰色）。押した後の動き（`assets/log.js` の `geoButton` の処理）は変えていない（`#log_geo` と `disabled` で見る作りのまま）
- CSS: `.mj-log-geo` を 44×44px の丸い押す所（色は `--mj-n-accent`）にし、区切りの線は `--mj-n-muted` の 35% の濃さ（`--mj-n-line` は検索欄の地と近くて見えなかった。手元で撮って確かめて直した）。共通の `.mj-houou-search*` の規則は変えていない（`.mj-log-more` と共有していたセレクタを分けただけで、`.mj-log-more` の宣言は前と同じ）。ほかのページの生成物は変わらない（log_pages だけを生成し、差分は log/ だけ）
- テスト: `test_geo_button_waits_for_coords`（検索欄の中にアイコン、前の行が無い、準備中は押せない）
- docs/notes/log-page.md の「構成」「現在地から探す」「仮置きの一覧」を直した
- 検証: `python3 -m unittest discover -s scripts/tests` は OK（2009件）、`node --test scripts/tests/js/*.test.js` は pass 22・fail 0

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに、指示の目的と決定、`git diff 052dacb2e 56f284b6e`（scripts・style.css・docs/notes・assets）、CLAUDE.md「コード規約」を渡した（1回目）。`test_log_pages`・`test_convert_x_posts` の 98件は OK。バグの指摘は無し。

| 指摘 | 扱い |
|---|---|
| 生成物（log/index.html）を作り直していない（CSS だけ入ると前の文字のボタンが 44px の円に押し込まれる） | 直した（同じブランチで生成のコミット aef3f41ce。マージは一緒に入る） |
| 「p<投稿ID>」の鍵は、【3】で place_id が付くと鍵が変わってまた載る。同じ催事の別の投稿も別の店になる | 直した（df0fd116c。コメントと log-page.md に書いた。仕組みは変えていない） |
| 鍵の形の検査が `startswith(("ChIJ", "p"))` だけで、打ち間違いを拾えない | 直した（df0fd116c。`^(ChIJ[\w-]+|p\d+)$` にした） |
| style.css の log/ の節の冒頭のコメントに「現在地のボタン」が残っている | 直した（df0fd116c） |
| disabled のボタンでは Firefox などで title のツールチップが出ない（準備中のとき） | 見送り（名前は aria-label で伝わる。今のデータでは押せる状態） |
| 区切りの線があること・アイコンが × の後にあることを検査していない／`shop_key()` の投稿 ID も空の場合 | 見送り（軽微。位置はプレビューで撮って確かめた。投稿 ID の空の行は merged_rows の行に無い） |

1回目の後の差分（コメント・文書・検査の形）は生成物を変えないので、2回目のレビューはしていない。

### 生成と数

- `regenerate-page.yml` を `work/1105-lgf-ui`・`target_page: log_pages` で手動実行（56f284b6e の後）、生成のコミット aef3f41ce（`log/index.html`・`log/search.json`）
- LGF-09 のマージ（a428af80f）の search.json と比べて: 店 1,739 → 1,737（−2）・投稿 2,427 → 2,425（−2）。外れた店は 東京ラーメンショー極み麺（`ChIJT2WXm-aNGGAR24zlaGQ7fiQ`、投稿 1513813599906824195）と寒鱈まつり（`p2022849721023299710`、投稿 2022849721023299710）だけ。足された店・投稿は0件（毎朝の取り込みの差は無い）
- 生成した log/index.html に `mj-log-tools` は0件、`id="log_geo"` は検索欄の中のアイコン

### プレビュー

- 0ac5d53c1 の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、幅 390、geolocation を神保町〈35.6959, 139.7577〉にして許可）で開いた
  - トップの検索欄: 右端に区切りの線と照準のアイコン。前の文字のボタンの行は無い
  - アイコンを押すと「×現在地」のタグが付き、1,670店が近い順に出る（先頭の3店の駅は神保町）
  - `?tag=ラーメン` で探した後の検索欄: 語・入力を消す ×・区切りの線・アイコンの順に並ぶ

### 3. 検索結果の並び順（assets/log.js の `search()`、:124-149。変えていない）

前提: 検索用のデータの店の並びは、店の最も新しい投稿の新しい順（`generate_log_pages.py` の `group_shops()` が新しい順の投稿から店を作る順。同じ日は投稿 ID の大きい順）。`search()` はこの並びのまま条件に合う店を集め、駅のタグか現在地のときだけ距離で並べ替える（`Array.prototype.sort` は安定なので、同じ距離の店は元の並びのまま）。店の中の投稿は新しい順。

| 探し方 | 1つ目の鍵 | 同じ値の店どうし（2つ目の鍵） | 「近い駅 → 新しい投稿のある店」か |
|---|---|---|---|
| 検索欄の語だけ | 店の最も新しい投稿（店のすべての投稿のうち。語に当たった投稿に限らない） | — | 距離は無い。新しい順だが、語に当たった投稿でなく店全体の最も新しい投稿で並ぶ |
| 駅のタグ | 店の駅の候補の中の、その駅までの距離（その駅の店は 0m。候補に無い店は後ろ） | 店の最も新しい投稿の新しい順 | なっている |
| 現在地 | 現在地から店の駅の点までの距離（同じ駅の店は同じ距離） | 店の最も新しい投稿の新しい順 | なっている |
| 店名・ジャンル・都道府県のタグ | 店の最も新しい投稿 | — | 新しい順（店のすべての投稿が出るので、出る投稿の最も新しいものと同じ） |
| 投稿者・投稿年のタグ | 店の最も新しい投稿（店のすべての投稿のうち） | — | 新しい順だが、出る投稿（その投稿者・その年の投稿）でなく店全体の最も新しい投稿で並ぶ |
| 駅のタグ＋ほかのタグ・語 | 駅のタグと同じ距離 | 店の最も新しい投稿（店全体） | 近い駅 → 新しいはなっているが、2つ目の鍵は出る投稿でなく店全体の投稿 |
| 現在地＋ほかのタグ・語 | 現在地と同じ距離 | 店の最も新しい投稿（店全体） | 同上 |

- 例: 「×2024年」で探すと、2026年の投稿もある店は、2024年12月の投稿だけの店より上に出ることがある（2026年の投稿は出ないのに、その日付で並ぶ）
- 直すなら: `search()`（assets/log.js:124-149）で、`matchShop()` が返した投稿（条件に合う投稿、新しい順）の先頭の日付を2つ目の鍵にして並べ替える。距離の無い探し方は「合う投稿の最も新しい日付の新しい順」で並べ替え、距離のある探し方は距離が同じとき同じ日付で比べる（今は元の並びに任せている。146行目の比べる関数に、距離が同じなら `posts[0]` の日付〈同じ日なら X の URL の投稿 ID〉で比べる形を足す）

### 決定の記録

- 指示文の「決定」節を docs/decisions/operations.md の末尾に「2026-11-05（CHAT-1105-LGF-10）」として足した。作業中の平野さんの回答は無い

## 報告

- 状態: 判断待ち / 続き: CHAT-1105-LGF-11
- ブランチ: work/1105-lgf-ui
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgf-ui/docs/logs/CHAT-1105-LGF-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgf-ui
- 確認用URL: プレビューあり（URL は最終報告）。この版で見える印: 検索欄の右端に照準のアイコンがあり、検索欄の下の「現在地から探す」の文字のボタンが無い
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - プレビューを見て、マージしてよいか。見る所: トップ（`/log/`）の検索欄の右端のアイコン（押すと位置の許可を聞き、「×現在地」のタグで近い順に出る）、`/log/?tag=ラーメン` で探した後の検索欄（語・×・区切りの線・アイコンの並び）、`/log/?shop=ChIJT2WXm-aNGGAR24zlaGQ7fiQ` が「見つかりませんでした」になること
  - 準備中のアイコンは、押せないアイコン（灰色、名前に「準備中」）にした。この形でよいか
  - 並び順: 投稿者・投稿年のタグと検索欄の語、駅・現在地で同じ距離の店どうしは、出る投稿でなく店全体の最も新しい投稿で並ぶ（経過の「3.」の表）。「出る投稿の最も新しい日付」で並べるように直すか（変え方は経過の「3.」の最後）
  - LGF-09 から移した残り: 一覧に当たらない駅の表記2つ（緑ヶ丘・東武池袋）と、【3】で平野さんが直す店（LGF-04 の要確認の10店と誤字の2店、LGF-06 の離れた駅の6店と大至）。issue に移すか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7864a244）: https://github.com/retroeater/mj-logs/tree/main/guide/7864a244

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7864a244/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2aae12da.md
