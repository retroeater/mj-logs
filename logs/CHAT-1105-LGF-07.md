# CHAT-1105-LGF-07

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: 765678989（7cfc3188e に origin/cloudflare を取り込んだマージ）

## 指示

【Claude作成】Claude Code 向け指示：log/ に「載せない店」と理由の置き場を作り新宿地下ラーメンを外す／期間限定の出店の候補を洗い出す／利用条件を紹介文の末尾の (i) にたたむ Chat-Ref: CHAT-1105-LGF-07 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-LGF-05 の判断待ちの続き。LGF-05 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgf-alias を続けて使う（LGF-05 の店名の統一と一緒にプレビューを見てからマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1104-LGF-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGF-07` を足す。

目的
LGF-05 のプレビューを見た平野さんの決定を、同じ作業ブランチに足す。(1) 載せない店を理由つきで1か所にまとめ、新宿地下ラーメンを外す (2) 期間限定の出店（ラーメンフェスなど）の候補を洗い出す（外すのは平野さんが候補を見てから）(3) 利用条件をたたむ。
決定（2026-11-05、平野さん）

* LGF-05 の店名の統一は、プレビュー（中華蕎麦 きみのあーる）で OK
* 「新宿地下ラーメン」（place_id ChIJkdDwfBKNGGAR2oXHz9NjCc8）は、期間限定でいろいろな店舗が出店する場所なので、一律に載せない。載せない理由をどこかに残す。「過去メシ」も同じく理由を残す
* ほかにも期間限定で出店している店（ラーメンフェスなど）は、検索した人が訪れることができないので載せないことにしたい。「期間限定メニュー」と混同しないこと
* 利用条件は「利用条件 (i)」のような表記にして、見たい人だけが開いて見る形にしたい。できるなら、一番下の紹介文の文末に置く

前提（チャット側。平野さんの決定ではない）

* 今、log/ に載せない投稿は、石立岳大の行（更新が終わった）と、谷岡育夫の「過去メシ」（再投稿）の行（docs/notes/log-page.md、`scripts/lib/log_rows.py` の merged_rows または `generate_log_pages.py` の絞り込み。どこで外しているかを確かめる）
* 置き場（チャット側の案）: 店（place_id）単位で載せない一覧を `scripts/generate_log_pages.py` の定数（例 `HIDDEN_PLACES = {place_id: 理由}`）にし、理由をコメントではなく値として持つ。docs/notes/log-page.md に節「載せない投稿・店」を作り、規則ごとに理由と決めた日を書く（石立岳大・過去メシ・新宿地下ラーメン）。シート（【3】）には書かない（コードは【3】に書かない規則）。毎朝の取り込みで新しい投稿が入っても、この一覧の店は載らない
* 期間限定の出店の候補: `log/search.json` の店名・投稿のメニューから、出店・催事を表す語（例: フェス・ラーメンフェス・つけ麺博・博覧会・祭・催事・物産展・ポップアップ・POP UP・出店・特設・期間限定店。語は増やしてよい）で候補を出す。「期間限定」だけ（「期間限定メニュー」「期間限定の味」など）は候補にしない。本文はシートにしか無いので、この指示では読まない（search.json だけで出せる範囲。足りなければ、その旨を書く）
* 利用条件: 今は `generate_log_pages.py` の `TERMS_HTML` がページの末尾に見出し「利用条件」と2文を常に出している（Google Maps Platform 規約 3.2.2(a)(i) の知らせ。docs/notes/log-page.md「利用条件」）。規約が求めるのは、利用条件で知らせることで、常に開いた形で出すことまでは求めていない（CHAT-1104-LGN-27 の原文の引用。開いた形は LGN-30 のチャット側の前提だった）。案: 紹介文（`.mj-lead`）の文末に「利用条件 (i)」を置き、押すと2文とリンクが開く（`<details>`。トップの出典の「(i)」と同じ部品・書き方。キーボードでも開ける）。トップ・検索の結果・`?year=` などどの状態でも辿れること（紹介文がどの状態でも見えるかを確かめ、見えない状態があれば、その状態でも辿れる置き方を書いて止まらず進めてよい）
* 新宿地下ラーメンを外すと、カテゴリ「投稿回数の多い店」のカードが1枚入れ替わる。店の数・投稿の数は外した分だけ減る

手順

1. 載せない投稿の今の外し方（石立岳大・過去メシ）を確かめてログに書き、`HIDDEN_PLACES`（名前は合わせてよい）を足して新宿地下ラーメンを入れる。docs/notes/log-page.md に「載せない投稿・店」の節を足す。テストを足す（一覧の店が search.json・カテゴリに出ない）
2. 利用条件を紹介文の文末の「利用条件 (i)」にたたむ（上の案）。log-page.md の「利用条件」の項を直す。テストを直す・足す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、(a) 店の数・投稿の数の LGF-05 の生成（dd39e780b）との差と、外れた店・投稿の一覧 (b) 期間限定の出店の候補を全件表にする（鍵・店名・投稿の数・当たった語・当たった値〈店名かメニューか〉・投稿 ID）。候補は外さない（平野さんが選ぶ）

止まる条件

* 手順3の (a) で、新宿地下ラーメンの店（LGF-05 の search.json で投稿 9件）以外の店・投稿が減った、または毎朝の取り込みで説明できない差がある
* 利用条件の2文とリンクの中身（文・URL）が変わる（置き方と見た目だけを変える）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 未マージの work/ ブランチが、同じ関数（載せない投稿を外す処理・`TERMS_HTML` の置き場）を変えている、または取り込みで衝突する（並行の work/1104-lgf-stations は `convert_x_posts.py` だけを一時的に変える見込みで、重ならない）
* 終わったら、マージせずに判断待ちで止まる（LGF-05 と合わせて平野さんがプレビューを見る）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（トップ・紹介文の「利用条件 (i)」・候補の多い店の `?shop=` のパス）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致
- 同じ Chat-Ref のコミットは0件（LGF の識別子の重複の確かめは LGF-01 で済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある（貼られた指示文は改行が詰まって1行目につながっていたが、行の欠けはない）
- 作業ブランチ: ローカルの `work/1104-lgf-alias` は `origin/work/1104-lgf-alias`（7cfc3188e）と同じ。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったので `git merge origin/cloudflare` で取り込んだ（765678989）。docs/decisions/operations.md の末尾の追記どうし（LGF-05 の節と LGF-06 の節）が衝突し、両方を残して解いた。解いた後の箇所:

> ## 2026-11-04（CHAT-1104-LGF-05）
>
> - log/ の店名の表記揺れは A＋B で直す: 生成で店ごとに表記をそろえ（B）、要確認の 10店と誤字だけを【3】で直す（A）
> - B は、投稿の行に別名を出さず、別名でも検索に当たるようにする
> - この指示はプレビューを見てから決める（判断待ちで止まる）
> - #665（Claude でジャンルを付ける）は保留にする（優先順位が低いため）
> - 作業中の回答: なし
>
> ## 2026-11-04（CHAT-1104-LGF-06）
>
> - log/ で同じ place_id に異なる駅が付いている店を確かめる
> - 作業中の回答: なし

- LGF-05 のログの状態を「判断待ち / 続き: CHAT-1105-LGF-07」にした（ログ先行のコミットに含めた）
- 未マージの work/ ブランチで `scripts/generate_log_pages.py` を変えているのは `origin/work/1104-lgn-policy` だけで、変えているのは `SOURCE_HTML`（出典の (i) の中）。`drop_posts()`・`TERMS_HTML` の置き場ではないので止まらない（/policy/ は作らない決定でマージされない見込み）。work/1104-lgf-stations は LGF-06 で cloudflare にマージ済み（一度きりの mode は消してある）

### 1. 載せない投稿の今の外し方と、載せない店

- 今の外し方: `scripts/lib/log_rows.py` の `merged_rows()` は判定（飲食店）・表示（Y）だけで絞り、人・過去メシは見ない。外しているのは `scripts/generate_log_pages.py` の `drop_posts()`: 「名前」が `HIDDEN_PEOPLE`（石立岳大）の投稿と、`REPOST_PERSON`（谷岡育夫）の投稿で【1】元データの本文に `REPOST_MARK`（「過去メシ」）がある投稿。閉業の店は `drop_closed()` が「閉業」シートの place_id で外す。順は `shown_pairs()`（merged_rows → drop_posts）→ `fetch_shops()` の `drop_closed()`
- 足したもの（dc6ca29e5）: `HIDDEN_PLACES = {place_id: 理由}` に新宿地下ラーメン（`ChIJkdDwfBKNGGAR2oXHz9NjCc8`、理由「期間限定でいろいろな店が出店する場所で、検索した人が投稿の店を訪れられないため」）。`fetch_shops()` が閉業の後に `drop_closed(kept, HIDDEN_PLACES)` で外し、生成のログに「載せない店（HIDDEN_PLACES）で外した N店」を出す。シートには書かない
- docs/notes/log-page.md に節「載せない投稿・店」（規則・外す単位・理由・決めた日・置き場の表。石立岳大・過去メシ・載せない店・閉業）を足した。石立岳大は、log/ に出さない決定（CHAT-1031-LGN-10）に理由の記述が無いので、そう書き、取り込みを止めた理由（更新が終わった、2026-10-27）を添えた
- テスト: `HiddenPlacesTest`（理由が空でない・place_id の形。`fetch_shops()` を通して、一覧の店が search.json の店と「投稿回数の多い店」に出ず、ログの行に外した数が出る）

### 2. 利用条件のたたみ方

- `TERMS_HTML` を `<section>`＋見出しから `<details class="mj-log-terms"><summary class="mj-log-terms-button">利用条件(i)</summary><p class="mj-houou-note">2文とリンク</p></details>` にした（(i) はトップの出典と同じ `INFO_ICON`）。2文とリンクの文・URL は変えていない（レビューで旧版とバイト単位で同じことを確かめた）
- 置き場: 説明文（`.mj-lead`）は共通の `render_content()` が `<p class="mj-lead">` で出すが、`<p>` の中に `<details>` は置けないので、log/ だけ `build()` が `has_lead=False` にして `<div class="mj-lead">説明文 利用条件(i)</div>` を本文の後に出す。説明文の文は meta description・og:description と同じ `apply_count(meta.description, count)`
- どの状態でも辿れるか: 説明文は `#log_home`・`#log_result` の外（`main` の最後）にあり、log.js が隠すのはその2つだけなので、トップ・探した結果・`?year=` などどの状態でも見える
- CSS（style.css の「log/ の利用条件」）: `.mj-log-terms` を inline、summary を下線の語と 14px の (i)、押せる高さ 44px（上下を負のマージンで打ち消す。トップの (i) と同じ作り）。前の見出しの規則は消した
- 見た目: テストの見本の2店でページを作って幅 390 で開いた。閉じた形は説明文の文末に「利用条件 ⓘ」、Enter で開き、説明文の下に2文とリンクが出る（`details.open` が true）。summary の押せる範囲は 68×44px
- docs/notes/log-page.md の「利用条件」の項と「段2 の後」の行を直した
- テスト: `test_terms_folded_at_the_end_of_lead`（説明文の文末にあること、文とリンク、説明文が1つだけ、#log_result の後）
- 検証: `python3 -m unittest discover -s scripts/tests` は OK、`node --test scripts/tests/js/*.test.js` は pass 22・fail 0。修正前（b4808751a の scripts・assets・data）に新しいテストのファイルだけを差し替えると `test_log_pages` で errors=3（新しい3つ）

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェントに、指示の目的・決定と `git diff b4808751a dc6ca29e5`（scripts・style.css・docs/notes）、CLAUDE.md「コード規約」を渡した（1回目）。サブエージェントは `test_log_pages`・`test_descriptions`・`test_convert_x_posts` の 107件を回して OK、`TERMS_HTML` の2文と URL が旧版とバイト単位で同じことを確かめた。

| 指摘 | 扱い |
|---|---|
| 生成物（log/）を作り直していない。CSS だけ先に本番に入ると、旧 HTML の見出しの「利用条件」が崩れる | 直した（レビューに渡したコミットの後に、同じブランチで生成のコミット 98328180e を積んだ。マージは一緒に入る） |
| 月1回の閉業の確かめ（`convert_x_posts.py` の `closed_targets()`）は外す前の `shown_pairs()` を使うので、HIDDEN_PLACES の店にも Place Details を呼ぶ（今は1件/月） | 見送り（指示の範囲の外で、害は1回/月の要求だけ。直すかを「判断が必要なこと」に書いた） |
| `<details>` を inline にしたときの描画（開いたときの位置・負のマージンの重なり）をブラウザで見ていない | 確かめた（上の「見た目」。幅 390 で閉じた形と開いた形を撮って見た。プレビューでも平野さんが見る） |
| 「新着」のカテゴリを検査で見ていない | 見送り（`recent_shops()` と `most_posted()` はどちらも同じ `shops` から選ぶので、`shops` に出ないことを確かめれば足りる） |
| log-page.md の「CHAT-1104-LGN-27 の原文」は定期削除で消えるログを指す | 意図どおり（log-page.md の既存の書き方に合わせた。CLAUDE.md の禁止の3文書ではない） |

2回目のレビューはしていない（1回目の後にコードの差分を足していない）。

### 3. 生成と数（98328180e）

- `regenerate-page.yml` を `work/1104-lgf-alias`・`target_page: log_pages` で手動実行。生成のコミット 98328180e（`log/index.html`・`log/search.json`・`sitemap-pages.xml` の lastmod）
- (a) LGF-05 の生成（dd39e780b）との差: 店 1,740 → 1,739（−1）・投稿 2,436 → 2,427（−9）。外れたのは新宿地下ラーメン（`ChIJkdDwfBKNGGAR2oXHz9NjCc8`）の1店・9件だけ。足された店は0、ほかの店の投稿の数の変化も0（毎朝の取り込みの差も無い）。外れた投稿 ID: 2113831435018903887・2090367532138123444・2054832720027763024・2044604086314820028・2028790727619350700・1854433921636958491・1841081910467932498・1793548399968690369・1629318463905996800（LGF-04 の表で「一品香」1件・「YOKOKURA STOREHOUSE」1件の表記だった投稿も、同じ place_id なので一緒に外れた）
- log/index.html の差: 説明文が `<div class="mj-lead">` になり文末に「利用条件 (i)」、店の数が 1,739店（2,427件）、カテゴリ「投稿回数の多い店」から新宿地下ラーメンのカードが消えて1枚入れ替わった

### (b) 期間限定の出店の候補

search.json の店名・別名・投稿のメニューを、出店・催事の語（ラーメンフェス・つけ麺博・フェス・博覧会・万博・祭・まつり・催事・物産展・ポップアップ・POP UP・出店・特設・期間限定店・期間限定ショップ・限定店舗・百貨店・ラーメン博・ラーメン女子博・グルメ展・イベント・屋台・キッチンカー・マルシェ・フードフェス・肉フェス・横丁・フェア・展、追加で ショー・大集合・会場・限定出店・初出店・コラボ・出張・祭り・フード・ドーム・スタジアム・競馬場・球場・ホール・メッセ・ビッグサイト・幕張・物産・うまいもの）で NFKC・小文字にして部分一致で探した。「期間限定」だけの語は候補にしていない（「期間限定のピーチパフェ」は当たったが外した）。メニューの「ショートケーキ」「シーフード」など、語が別の意味で当たったもの（「ショー」「フード」）は表に入れず、店名に当たったものと、メニューに当たったもののうち催事の意味がありうるものだけを全件書いた。

| # | 鍵 | 店名 | 投稿の数 | 当たった語 | 当たった値（どこ） | 投稿 ID | 見立て |
|---|---|---|---|---|---|---|---|
| 1 | p2022849721023299710 | 寒鱈まつり | 1 | まつり | 寒鱈まつり（店名） | 2022849721023299710 | 催事の名前の見込み。place_id が無い（1投稿1店）ので、`HIDDEN_PLACES` では外せない（外すなら【3】の「表示」を N にするか、投稿 ID で外す規則が要る） |
| 2 | ChIJT2WXm-aNGGAR24zlaGQ7fiQ | 東京ラーメンショー極み麺 | 1 | ショー | 東京ラーメンショー極み麺（店名） | 1513813599906824195 | 催事（東京ラーメンショー）の名前を含む。常設の店かは search.json からは分からない |
| 3 | ChIJB5QcTjuNGGARCgLCA2yJYXo | ソフトクリームマルシェ | 1 | マルシェ | ソフトクリームマルシェ（店名） | 2035241883665125773 | 店名に「マルシェ」。常設の店の名前の見込み |
| 4 | ChIJQWq_ZFWJGGARFAj32EqhM3E | カフェ・ディ・フェスタ | 1 | フェス | カフェ・ディ・フェスタ（店名） | 1854444085958041978 | 「フェスタ」の当たり。常設の店の名前の見込み |
| 5 | ChIJUTb7PoGLGGAR-XZ2I3djAv4 | ガトーフェスタハラダ | 1 | フェス | ガトーフェスタハラダ（店名） | 1844291119892631596 | 「フェスタ」の当たり。常設の店（菓子店）の名前 |
| 6 | ChIJH26w8SHuGGARpttWeNcumqw | 田祭木 | 1 | 祭 | 田祭木（店名） | 1761596511480344648 | 「祭」の字の当たり。常設の店の名前の見込み |
| 7 | ChIJ0_mnXD2NGGARql05JhBYN04 | 麺処あきら | 1 | 屋台 | 屋台ラーメン、餃子、ライス（メニュー） | 1912735264927281190 | メニュー名の「屋台ラーメン」。常設の店の見込み |
| 8 | ChIJtUxmFteMGGAR_0Z9ZE-8a0g | 但馬屋珈琲店 | 1 | 横丁 | プリンマダム、横丁サバラン（メニュー） | 1865687527660896365 | メニュー名の「横丁サバラン」。常設の店 |
| 9 | ChIJp6flawOMGGARUjOe-cACTSY | 栄屋ミルクホール | 2 | ホール | 栄屋ミルクホール（店名） | 1882345700203331660、1527188014044426240 | 「ホール」の当たり。常設の店（食堂）の名前 |

- 足りない点: 催事で食べた投稿でも、店名の列に催事の名前でなく出店した店の名前が入っていれば、search.json からは見分けられない（新宿地下ラーメンは場所の名前が店名に入っていた例）。見分けるには【1】の本文（「フェス」「出店」などの語）を読む必要があり、この指示では読んでいない

### プレビュー

- 98328180e の Workers Builds: success。版ごとのプレビューを Playwright（UA に mj-internal、幅 390）で開いた
  - トップ・`?year=2026`（358店）・`?tag=新宿地下ラーメン`・`?shop=ChIJkdDwfBKNGGAR2oXHz9NjCc8` のどれでも、説明文の文末の「利用条件 (i)」が見える（押すと開き、2文が出る）
  - `?tag=新宿地下ラーメン`・`?shop=ChIJkdDwfBKNGGAR2oXHz9NjCc8` は「見つかりませんでした。…」で、新宿地下ラーメンの店は出ない

### 決定の記録

- 指示文の「決定」節を docs/decisions/operations.md の末尾に「2026-11-05（CHAT-1105-LGF-07）」として足した。作業中の平野さんの回答は無い

## 報告

- 状態: 判断待ち / 続き: CHAT-1105-LGF-08
- ブランチ: work/1104-lgf-alias
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgf-alias/docs/logs/CHAT-1105-LGF-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgf-alias
- 確認用URL: プレビューあり（URL は最終報告）。この版で見える印: 一番下の説明文が「…1,739店（2,427件）…探せます。 利用条件 (i)」で終わる
- マージ: 未（平野さんの判断待ち。LGF-05 の店名の統一と一緒）
- issue: なし
- 判断が必要なこと:
  - プレビューを見て、LGF-05 と合わせてマージしてよいか。見る所: トップ（`/log/`）の一番下の説明文の文末の「利用条件 (i)」（押して開く）・探した後（`/log/?year=2026`）も一番下に同じものがあること・カテゴリ「投稿回数の多い店」から新宿地下ラーメンが消えたこと・`/log/?shop=ChIJkdDwfBKNGGAR2oXHz9NjCc8` が「見つかりませんでした」になること
  - 期間限定の出店の候補（経過の「(b)」の9件）から、載せない店を選ぶか。私の見立てで候補らしいのは2件: 1番の寒鱈まつり（place_id が無いので `HIDDEN_PLACES` では外せず、【3】の「表示」を N にするか投稿 ID で外す規則が要る）・2番の東京ラーメンショー極み麺（`/log/?shop=ChIJT2WXm-aNGGAR24zlaGQ7fiQ`）。ほかの7件は語が店名・メニュー名に偶然当たったもので、常設の店の見込み。search.json だけでは催事の投稿を見分けきれない（本文を読む調べは別の指示）
  - 月1回の閉業の確かめ（`convert_x_posts.py` の `closed_targets()`）が `HIDDEN_PLACES` の店にも Place Details を呼ぶ（今は1件/月）。除くように直すか
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
