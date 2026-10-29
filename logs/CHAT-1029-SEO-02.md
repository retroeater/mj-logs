# CHAT-1029-SEO-02

- 着手日時: 2026-10-29
- 対象issue: #264（S1）・#227（S4）・#560・#568（S5 の参照）
- ブランチ: work/1029-seo2
- 着手時HEAD: b06f4f27c

## 指示

【Claude作成】Claude Code 向け指示：SEO-01 の「現行で直す小さいもの」S1・S3・S4・S5 を直し、プレビューで止める Chat-Ref: CHAT-1029-SEO-02 マージ: 判断待ちで止まる 貼る時機: いつでも（SEO-01 と同じセッションでよい。新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo2〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1029-seo2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1029-SEO-01 のログ（`## 報告` の「判断が必要なこと」）の「現行で直す小さいもの」のうち、S1・S3・S4・S5 を直す。S2（ランキングの description）は #5 の description の生成の型の直しとまとめて別の指示で行うので、この指示では扱わない。プレビューで止め、平野さんの確認の後に別の指示でマージする。
決定（2026-10-29、平野さん）

* SEO-01 の推奨どおりに進める: 現行で直す小さいもののうち S1・S3・S4・S5 を直す。S2 は #5 の生成の型の直しとまとめる

前提（チャット側。平野さんの決定ではない）

* Rate limiting のルール（平野さんが 2026-10-29 にダッシュボードの画面を見せた）: Rate limiting rules は2本。「Throttle rapid HTML fetches (#124)」は条件に「URI Path ends with .html or URI Path ends with /, Known Bots does not equal true and…」（以下は画面で切れている）、アクション Managed Challenge。「Throttle /api/ask (#598)」は Block。検証済みのボット（Googlebot・Bingbot など）は1本目の対象外
* S3 について（チャット側が docs/notes/video-mtsuku.md「帰り道との違い」で確かめた）: 各話の `<title>`・og:title・前後のリンク・パンくずは「「チーム名」 作る人」と決まっている（同じ人の回が2つある人がいるため）。一方、SEO-01 の②-4 では `mtsuku/2FOsXsIqTAA.html` と `mtsuku/vSduV7-kLls.html` の title が同じ「滝沢和典 | Mリーグチームを作ろう！ | ryoei.pro」だった。直すのは title（と og:title）を文書の決まりに合わせることで、新しい形は作らない。 各話の見出し（h1）が作る人の名前だけなのは 2026-10-15 の決定（同文書）なので、h1 の重複（11組）は直さない（SEO-01 の S3 の「h1 に回の区別」は採らない）。title が決まりと違う原因（生成の不具合か、シートのチーム名が空か）は確かめてから直す。シートが原因なら直さずに止まり、どの回のどの列かを報告する
* S1: `index.html` の `<html lang="en">` を `ja` にする（#264。1行）
* S4: `llms.txt` に、Mつく・プロテストの各話の URL の形（`mtsuku/<YouTube動画ID>.html`・`exam/<YouTube動画ID>.html`）を、帰り道の行（SEO-01 の③-5 の llms.txt:77）と同じ形で1行ずつ足す
* S5: 文書の食い違いを直す。(a) docs/notes/redirects.md の `?match=` のルールを「予定」から作成済みへ（SEO-01 の①-5。あわせて、他のクエリが付くと存在しない URL へ転送される件〈①-5 の補足〉を「既知の問題」として1行書き、#568 を指す）(b) docs/notes/cloudflare.md の canonical の「例外は帰り道だけ」を実態（①-3 の集計）に直す。title/・saikyo/ に付いている canonical が #113 の例外の基準に当たるかは #560 で決めるので、「#560 で見直し中」と書く (c) 同 cloudflare.md の「HTML に ETag が付かない」を直す（①-6）(d) docs/notes/ogp.md の規則の表に jwrc/・wrcr/・公開済みの ouka/・saikyo/ の下位ページを足す（②-7、B6。値は docs/decisions/houou.md・seo-bing.md と実物の og:image から）(e) `scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと docs/notes/static-generation.md「ページの一覧」の前文の ouka/・jwrc/・wrcr/ の「未公開」を直す（L-1）(f) docs/notes/cloudflare.md の Rate Limiting の記述を、上の前提の画面（2本・条件に Known Bots の除外・アクション Managed Challenge）と照らし、食い違えば直す。SEO-01 の①-7（同時4本の全件 HEAD で約半数が 429）は「Managed Challenge の画面とは別の応答。原因は未確認」と記録だけする
* 直す前に、追記・修正の先の節の今の内容を読む。同じ趣旨の記述があれば置き換え・拡張してよい（処理を報告に書く）
* S1・S4・S5 は生成物の差分が出ない（S5 の (e) はコードのコメントだけ）。S3 は Mつくの再生成で、title・og:title が変わる回だけが差分になる見込み

手順

1. S3: `scripts/` の Mつくの生成（docs/notes/video-mtsuku.md「構成」）と `scripts/tests/test_mtsuku.py` を読み、title・og:title が「「チーム名」 作る人」にならない原因を確かめる。生成の不具合なら直してテストを足し（同じ作る人の2回が別の title になること）、Mつくを再生成する。差分の title・og:title の変わった回を全件（ファイル名・前・後）ログに書く
2. S1・S4 を直し、S5 の (a)〜(f) を直す。変えた節ごとに、前後の要点をログに書く
3. 本番と同じ形でプレビューを用意し（docs/notes/cloud-sessions.md「プレビュー」）、`mtsuku/2FOsXsIqTAA.html`・`mtsuku/vSduV7-kLls.html`・`/`・`/llms.txt` をプレビューで開いて title・lang・llms.txt の行を確かめ、ログに書く。最終報告の「確認用:」にプレビューの URL を書き、判断待ちで止まる

止まる条件

* S3 の原因がシートの値（チーム名が空など）だった（直さずに、回・列を報告して止まる。S1・S4・S5 は進めてよい）
* Mつくの再生成の差分に、title・og:title 以外の変化（本文・カード・リンクなど）が出た（全件を書いて止まる）
* 直す先の文書の記述と、この指示の前提が矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めない）
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし（行の区切りが無く1行につながって貼られていたが、4つとも含まれる）
- 同じ Chat-Ref のコミット: なし（識別子 SEO は SEO-01 でこのセッションが使ったもの）。CLAUDE.md は起動時から変わっていない（`git log HEAD..origin/cloudflare -- CLAUDE.md` が空）
- 作業ブランチ: work/1029-seo2 はローカル・リモートとも無かったので `git checkout -b work/1029-seo2 origin/cloudflare`（着手時 HEAD b06f4f27c）
- issue の state: #264・#227・#568・#560 は open（SEO-01 の確認から変わりなし）
- 0章ゲート: CLAUDE.md 23,786・handover.md 22,477 バイトで警告域（26KB）の外。未マージの work/ ブランチのうち、触るファイルと重なるのは次の3本（`git diff --stat origin/cloudflare...<branch> -- <ファイル>`）
  - work/1028-lgn: `docs/notes/static-generation.md`「ページの一覧」の前文の同じ1行（log/ を足す）と、`scripts/regenerate.py` の `OUTPUT_OVERRIDES` の wrcr/ の行の直後（log/ の2行を足す）→ **S5(e) が同じ行・隣の行に当たり取り込みで衝突するので、S5(e) だけ外した**（止まる条件「重なる対象だけ外して進め」）
  - work/1029-lgn-eki: `docs/notes/static-generation.md` の別の節（メンテナンス用スクリプト）に1行。S5(e) を外したので重ならない
  - work/1017-lvp: `scripts/lib/members.py`・`scripts/lib/study.py`。S3 で mtsuku の生成を直す場合に備えて `scripts/lib` を見たもので、今回は触らない

### S3（Mつくの title）: 原因はシートの「チーム名」が空 → 直さずに止めた

- 生成: `scripts/lib/mtsuku.py` の `episode_name()` は `f"{episode.title} {episode.interviewee}".strip()`、`episode.title` は `team_title(row.team)`（チーム名が空なら空文字）。`<title>`（`episode_page_title()`）・og:title（`episode_og_title()`）・前後のリンク・パンくずは同じ `episode_name()` を使う。チーム名が空の回は「作る人」だけになる作りで、生成の不具合ではない
- `<title>` が「「」で始まらない各話は61件中5件（`grep -L '<title>「' mtsuku/[!i]*.html`）。5件とも一覧のカードにチーム名の行が無く（`mtsuku/index.html` のカード題 56/61）、各話の日付の行にもチーム名が無い。シートの「Mつく」のタブ（動画のタブをまとめたブック、列「チーム名」）が空とみられる。コンテナには `SHEETS_READER_SA_KEY` が無くシートを直接は読めない（docs/notes/cloud-sessions.md「鍵」）ので、シートの値そのものは未確認
- チーム名が空の回（ファイル／公開日／作る人）: `mtsuku/nLeGJz-nt-E.html` 2019-12-03 園田賢 ／ `mtsuku/CtDQ5NjWSN0.html` 2020-01-02 多井隆晴 ／ `mtsuku/2FOsXsIqTAA.html` 2020-02-04 滝沢和典 ／ `mtsuku/f7__Vg7EUNE.html` 2020-04-29 沢崎誠 ／ `mtsuku/vSduV7-kLls.html` 2024-08-10 滝沢和典。title が重なるのは滝沢和典の2回だけ
- 平野さんがシートの「チーム名」に入れれば、次の再生成（週次の all）で title・og:title・カード・日付の行に出る（コードの変更は要らない）

### S1・S4

- S1（5a7632ed4）: `index.html:2` の `<html lang="en">` → `<html lang="ja">`。`index.css`・`index.js` に `:lang` の指定は無い
- S4（b6dda6fb7）: `llms.txt` の Optional 節、帰り道の各話の URL の行（77行）の後に、Mつく（`mtsuku/<YouTube動画ID>.html`、sitemap-mtsuku.xml）・プロテスト（`exam/<YouTube動画ID>.html`、sitemap-exam.xml）の行を同じ形で足した

### S5（1c0056791・8ac0e059c）

- (a) `docs/notes/redirects.md`: 「予定（公開時に増える）」の `?match=` の行を、新しい節「ダッシュボードの転送（テストで拾えない）」の表へ移した（作成済み・2026-10-29 に本番の応答で確認）。同じ節に既知の問題（他のクエリが付くと存在しない URL へ 301、#568）を1行。「予定」の節は「今は無い。」（節は公開時に行が増える場所として残した）。「常設」の節の「Redirect Rules は www to apex 1本（2026-10-14 の申告）」に、その後 `?match=` のルールが足された旨を注記。`?match=` の行は `_redirects` に無いので「棚卸しの対象」の表（`test_redirects_inventory.py` の照合の対象）には入れなかった
- (b) `docs/notes/cloudflare.md`「配信設定」の canonical: 「例外は帰り道の各話と一覧だけ」を、2026-10-29 の集計（title/ 384・saikyo/ 891・wayhome/ 44・mtsuku/ の各話61・exam/ の各話12。ほかは無し）に置き換え、title/・saikyo/ の分が基準に当たるかは #560 で見直し中、`saikyo/players/` は外すと決めた（#638）と書いた。基準の文はそのまま
- (c) 同: 「HTML には ETag も Last-Modified も付かない」を、Last-Modified は付かない・ETag は 2026-09-28 の実験（#449）では付かなかったが 2026-10-29 には本番の HTML に付いていた（いつからか・304 になるかは未確認）に置き換えた
- (d) `docs/notes/ogp.md`「ページの種類ごとの og:image・og:type の規則」: 区分の画像の行に jwrc/・wrcr/ の全ページと saikyo/ の vs/・years/・players/・ranking/ を足し、画像の場所を書いた。「4種類」の文を「2026-10-16 の決定の時点では4種類」にし、ouka/ の「仮置き」を「#591 で公開済み（`ouka/leagues/` だけ noindex）」に、今の区分の画像は8種類（ほかに帰り道の合成画像）と足した。値は実物（jwrc/・wrcr/・ouka/ の12件ずつ、saikyo/ の下位10件が各自の画像を指す）と docs/decisions/houou.md の WRC-07・WRR-03
- (e) 外した（上の 0章ゲート）。`scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと `docs/notes/static-generation.md` の前文の ouka/・jwrc/・wrcr/ の「未公開」は今も残る
- (f) 同 cloudflare.md「Rate limiting rules（2026-09-29 に導入、#124）」: 既存の表（条件式 `... and not cf.client.bot and ...`、Managed Challenge、2本目の `/api/ask` は Block）と前提の画面は食い違わないので、表は変えず、箇条に2026-10-29 の画面の申告（2本・「Known Bots does not equal true」は `cf.client.bot` の画面の表示・Managed Challenge）と、SEO-01 の①-7（同時4本で約半数が 429、同時2本で172件、逐次0件。Managed Challenge の画面とは別の応答で原因は未確認）を足した

### 検証

- `python3 -m unittest discover -s scripts/tests`: 1600 件 OK（S5 の後）。`test_redirects_inventory`・`test_og_image` も個別に OK
- 修正前でも通るか（#310）: 今回はテストを足していない（S3 を直さなかったため）。S1・S4・S5 は検査の対象の外
- 生成物: 再生成はしていない（S3 を直さなかったため）。差分は index.html・llms.txt・docs だけ
- プレビュー: 1c0056791 の check-run「Workers Builds: mj」は completed / success（バージョンの URL は最終報告）。プレビューで `?v=seo02a` を付けて取得: `/mtsuku/2FOsXsIqTAA.html`・`/mtsuku/vSduV7-kLls.html` は 200・`<html lang="ja">`・`<title>` は2つとも「滝沢和典 | Mリーグチームを作ろう！ | ryoei.pro」（S3 は直していないので本番と同じ）、`/` は 200・`<html lang="ja">`・title「ryoei.pro」、`/llms.txt` は 77〜79行に帰り道・Mつく・プロテストの各話の URL の形。8ac0e059c は docs だけで、ビルドは走らない（check-run 0件）

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose、sonnet、84,766 トークン）に、指示の目的・決定、`git diff origin/cloudflare...HEAD`（ログを除く）、CLAUDE.md「コード規約」を渡した。1回目で要修正は無し、任意3件。

| 指摘 | 扱い |
|---|---|
| redirects.md の棚卸しの手順4が「予定」の行を見るが、行が無くなった | 意図どおり（節は公開時に行が増える場所として残す。手順の文も今後の行に当たる） |
| cloudflare.md の「同時2本で172件、逐次は0件」の根拠 | 意図どおり（SEO-01 の①-7 の計測。SEO-01 のログの指摘の一覧にある） |
| ogp.md の「ouka/ は #591 で公開済み」は ouka/leagues/ が noindex なので食い違って読める | 直した（8ac0e059c） |

2回目は、直したのが1語の追記だけなので行っていない。

## 報告

- 状態: 判断待ち / 続き: CHAT-1029-SEO-05
- ブランチ: work/1029-seo2
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo2/docs/logs/CHAT-1029-SEO-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo2
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `/`・`/llms.txt`・`/mtsuku/2FOsXsIqTAA.html`・`/mtsuku/vSduV7-kLls.html`。この版で見える印は、`/llms.txt` の Optional 節の「「Mつく」の各話ページは `https://ryoei.pro/mtsuku/<YouTube動画ID>.html` の形式。」の行
- マージ: 未（平野さんの判断待ち。マージは別の指示）
- issue: #264（S1）・#227（S4）・#560・#568（S5 で参照）
- 判断が必要なこと:
  - マージしてよいか（S1・S4・S5 の (a)〜(d)・(f)。差分は index.html・llms.txt・docs/notes の3文書）
  - S3 は直していない: title が「「チーム名」 作る人」にならないのは、シートの「Mつく」のタブの列「チーム名」が空の5回（nLeGJz-nt-E 2019-12-03 園田賢・CtDQ5NjWSN0 2020-01-02 多井隆晴・2FOsXsIqTAA 2020-02-04 滝沢和典・f7__Vg7EUNE 2020-04-29 沢崎誠・vSduV7-kLls 2024-08-10 滝沢和典）とみられる（生成はチーム名が空なら作る人だけにする作り。シートは鍵が無く直接は未確認）。チーム名を入れるか、空のままにするか → #5 に移した（CHAT-1029-SEO-05）
  - S5(e) は外した: work/1028-lgn（未マージ）が `docs/notes/static-generation.md` の同じ前文の行と `scripts/regenerate.py` の `OUTPUT_OVERRIDES` の隣の行を変えていて、取り込みで衝突するため。work/1028-lgn のマージ後に別の指示で直すか → #560 に移した（CHAT-1029-SEO-05）
  - 指示の範囲外で見つけた食い違い（直していない）: docs/handover.md 2章「配信」の「canonical は付けない（#113）。例外は `wayhome/` の個別ページ」も、S5(b) と同じく実態（title/・saikyo/・mtsuku/・exam/ にも付く）と合わない → #560 に移した（CHAT-1029-SEO-05）
- 未確認の項目:
  - シートの「チーム名」の値そのもの（セッションに鍵が無い）
  - ETag の付いた HTML の再検証で 304 になるか（S5(c) の記述は「未確認」と書いた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a8bca67b）: https://github.com/retroeater/mj-logs/tree/main/guide/a8bca67b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a8bca67b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
