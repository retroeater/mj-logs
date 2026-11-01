# CHAT-1101-PRP-05

- 着手日時: 2026-11-01
- 対象issue: #641・#159
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 951a8c213

## 指示

【Claude作成】Claude Code 向け指示：`jpml_pros.html` の転送を別の小さな Worker で作り（決定④）、名前の表記の揺れを洗い出す。同じ作業ブランチに足してプレビューで止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-05
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1101-PRP-04 の完了報告（判断待ち）の後。PRP-04 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03・04 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-04 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に ` / 続き: CHAT-1101-PRP-05` を足して push する。

## 目的
PRP-04 で止めた `jpml_pros.html` の 301 を、サイトの Worker `mj` とは別の、そのパスのルートだけで動く小さな Worker で作る。あわせて、名前の表記の揺れで個別ページへ向かない選手を洗い出し、元のシートの直しの材料にする。番号は CHAT-1101-PRP-01 のログの「2. 棚卸しの表」の番号。

### 決定（2026-11-01、平野さん）
- PRP-04 の判断が必要なことへの回答:
  - 転送は案B: ルート `ryoei.pro/jpml_pros.html*` だけで動く小さな Worker を別に作る（mj-ask と同じ形。サイトの `mj` は静的アセットだけのまま）
  - 順位変動の向け替えが houou/・ouka/・jwrc/・wrcr/ の4大会に当たったのはよい
  - title/ の向け替えを期ページの写真カード（決勝に出た全員）に当てたのはよい（在籍者でない人は今の X、X も無ければリンクなし、のまま）
  - 名前の表記の揺れ（例: `houou/race/38-2.json` の「水口 崚」）は、空白・字形の違いだけで在籍者と一致する名前を洗い出し、元のシートの値を直す（コードでのあいまいな照合はしない）
- 前の決定から（docs/decisions/pros.md に記録済み）: ④ `?name=` の旧 URL は Worker の 301。範囲は `jpml_pros.html?name=` だけ。旧名は「別名」の表から引き、在籍者でない名前・表に無い名前は `/pro/` のトップへ

### 前提（チャット側。平野さんの決定ではない）
- `?name=` の無い `/jpml_pros.html` も、同じ Worker で `/pro/` へ 301 する（チャット側の案。このルートに来る要求は `_redirects` より先に Worker に渡る見込みのため。要確認。違えば実物に合わせ、ログに書く）
- 手本は `workers/ask/`（`wrangler.jsonc` の `routes`・`workers_dev: false`・`preview_urls: false`、Workers Builds の Root directory、`docs/notes/ask-worker.md`「B1: Workers Builds のプロジェクト `mj-ask` を作る」）。Worker の名前・ディレクトリ名は既存に倣って決めてよい（チャット側の案: `mj-pros-redirect`）
- 名前 → slug の表は、pro/ の生成と同じ流れで毎回 `workers/<名前>/` のデータに書く（シートの変化に追いつくため）。毎朝の再生成でその表が変わったときにこの Worker がデプロイし直されるか（Workers Builds の Root directory と、サイトの `mj` の Build watch paths の除外 `workers/**`、`docs/notes/cloudflare.md`）を確かめて書く
- Workers Builds のプロジェクトは平野さんがダッシュボードで作る。本番のブランチ（`cloudflare`）にこの Worker のディレクトリが入る前に作るとビルドが失敗する見込みなので、**作る時機（公開のマージの前か後か）と、その間に `/jpml_pros.html` が何を返すか**を調べて、平野さんの手順と一緒に書く（要確認）
- 転送の月次の集計（RDR、`fetch-redirect-counts`、`docs/notes/redirects.md`）が Worker の 301 を数えられるかを確かめる（数えられなければ、どうすれば数えられるかの案を書く。作らない）
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）

## 手順
1. Worker: `workers/<名前>/`（コード・`wrangler.jsonc`・テスト）と、名前 → slug の表の生成（旧名は「別名」の表から）を作る。テストには、在籍者・旧名・在籍者でない名前・表に無い名前・`?name=` の無いもの・記号や空白や % の符号を含む値・大文字小文字の違うパス（`/JPML_PROS.html` など、ルートに当たるかも書く）を入れ、`node --test 'workers/<名前>/test/*.test.mjs'` を回す。ローカルの `wrangler dev` で同じ場合の状態コードと `Location` を確かめる（CLAUDE.md「禁止事項」の `wrangler dev` の形に倣い、ポートは衝突しない値でよい）
2. 文書: `docs/notes/redirects.md`（一覧の行。`scripts/tests/test_redirects_inventory.py` の照合に合わせる）・`docs/notes/cloudflare.md`・新しい Worker の説明（`docs/notes/ask-worker.md` に倣う。平野さんのダッシュボードの手順〈Workers Builds のプロジェクトを作る・Root directory・ビルドの確かめ方〉と、作る時機を書く）・#159 の設計の記述。集計の確かめの結果も書く
3. 表記の揺れの洗い出しと確かめ: 順位変動の JSON（4大会）・title/ の写真カード・pro/ のトップの質問欄の索引で、slug が付かなかった名前のうち、空白・全角半角・異体字などの違いを除くと在籍者の名前と一致するものを全件、出どころ（ファイルと、元のシートの名前）とともに表にする（例の「水口 崚」を含む。件数だけでなく全件）。直すのは平野さんのシートの作業なので、この指示では直さない。そのうえで次を `## 経過` に書く:
   - テスト（`python3 -m unittest discover -s scripts/tests` と workers のテスト）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   - サイトの `mj`（`wrangler.jsonc`）に変更が無いこと
   - 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

## 止まる条件
- 0章の確かめで、PRP-04 のログの状態が「判断待ち」でない
- ルート `ryoei.pro/jpml_pros.html*` だけで動く形にできない（サイトの `mj` に `main` を足す必要が出た）
- 新しい依存（npm・pip のパッケージ、外部 API）が要る
- pro/ 以外のページの生成物に、この指示の変更で説明できない差分が出た
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）。平野さんのダッシュボードの作業も頼むだけで、この指示の中では待たない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんのダッシュボードの作業の時機と、表記の揺れの表（シートで直すもの）があることを書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- CHAT-1101-PRP-04 のログの状態は「判断待ち」だった。末尾に「 / 続き: CHAT-1101-PRP-05」を足して push した（18728eec6）
- 同じ Chat-Ref のコミット: なし。作業ブランチはローカル・リモートとも 951a8c213 で一致、`origin/cloudflare` は祖先だった（取り込み不要）
- #641 にほかのセッションの着手中は無い。着手中のコメントを書いた

### 1. Worker（`mj-pros-redirect`）

調べたこと（Cloudflare の公式の文書、2026-11-01 に読んだ。Workers の Routes のページ）:

- 「The path component of route patterns is case sensitive」→ `ryoei.pro/jpml_pros.html*` は `/JPML_PROS.html` に当たらない（今までどおりサイトの 404）
- 「Route pattern matching considers the entire request URL, including the query parameter string. ... the only way to have a route pattern match URLs with query parameters is to terminate it with a wildcard, `*`」→ 末尾の `*` が要る。`*` は `/jpml_pros.htmlx` なども拾うので、コードでパスを確かめて、ほかは `fetch(request)` でサイトへ渡す
- 「Routes can `fetch()` Custom Domains and take precedence if configured on the same hostname」→ ルートの Worker がサイト（Custom Domain の `mj`）より先に動く。前提の「`_redirects` より先に Worker に渡る」はこのとおりで、`?name=` の無い `/jpml_pros.html` も同じ Worker で `/pro/` へ送った（`_redirects` には行を足していない）

作ったもの:

- `workers/pros-redirect/`: `wrangler.jsonc`（`mj-pros-redirect`・ルート `ryoei.pro/jpml_pros.html*`・`workers_dev: false`・`preview_urls: false`。mj-ask と同じ形）、`src/redirect.mjs`（`targetPath()`）、`src/index.mjs`（301、転送しないパスは `fetch(request)`）、`test/redirect.test.mjs`
- 表: `scripts/generate_pro_pages.py` の `redirect_table()` が `workers/pros-redirect/data.json` を書く（在籍者の今の名前と、「別名」の変換前のうち変換後が在籍者のもの）。「別名」は今後の予定のために読んでいる `live_pages.load()` の名前の辞書から取る（`Site.aliases`）。`scripts/regenerate.py` の `pro_pages` の出力に足した（Actions の再生成がコミットする）
- Actions の生成（run 41286842756、`pro_pages`、success）で `data.json` ができた: 1,125件（在籍者 1,098名・旧名 27件）。pro/ の生成物は変わらなかった
- サイトの `mj` の `wrangler.jsonc` は変えていない（origin/cloudflare・この指示の前と差分0）

テスト:

- `node --test 'workers/pros-redirect/test/*.test.mjs'`: 6件 OK（在籍者・旧名・在籍者でない名前・表に無い名前・空・`null`・名前の一部・`?name=` 無し・ほかのクエリだけ・前後の空白・`+`・`&` と空白を含む名前・中の空白・壊れた `%` の符号・`__proto__`・`/jpml_pros.htmlx`・`/jpml_pros.html/`・`/JPML_PROS.html`）。`workers/*/test` の全部（162件）も OK
- `python3 -m unittest discover -s scripts/tests`: 1,752件 OK（足した `test_redirect_table` は、この指示の前のコードで ERROR）

ローカルの `wrangler dev`（`npx wrangler@4.156.0 dev --port 8791 --ip 127.0.0.1 --persist-to /tmp/wrangler-state-pr`、`workers/pros-redirect/` で起動。本番には出ていない）:

| 要求 | 状態 | Location |
|---|---|---|
| `/jpml_pros.html` | 301 | `/pro/` |
| `?name=白鳥翔`（在籍者） | 301 | `/pro/sho.shiratori.23/` |
| `?name=ガースネルソン`（「別名」の旧名） | 301 | `/pro/garthe.22/` |
| `?name=藤川まゆ`（在籍者でない） | 301 | `/pro/` |
| `?name=存在しない名前` | 301 | `/pro/` |
| `?name=%20白鳥翔%20`（前後の空白） | 301 | `/pro/sho.shiratori.23/` |
| `?name=A%26B%2B%25`（記号） | 301 | `/pro/` |
| `?name=%E0%A4%A`（壊れた符号） | 301 | `/pro/` |
| `?place=東京`（name 無し） | 301 | `/pro/` |
| `/JPML_PROS.html` | （ローカルでは `fetch(request)` が `wrangler dev` 自身に戻り 301 の繰り返しになった。本番ではルートが大文字小文字を区別するので、この Worker は動かない） | — |

### 2. 文書

- 新しく `docs/notes/pros-redirect-worker.md`（動き・表・デプロイと表の更新・平野さんの手作業 P1・P1 の後の確かめ・月次の集計）。`docs/handover.md` の設計メモの表に1行（`test_handover_notes_table.py` のため）
- `docs/notes/redirects.md` に「Worker の転送（テストで拾えない）」の節と行（「棚卸しの対象」には入れていない。`test_redirects_inventory.py` が `_redirects` と照合するため）
- `docs/notes/cloudflare.md`（Worker の一覧の段と、canonical の節の `jpml_pros.html` の注記）・`docs/notes/pro-pages.md`
- #159 に設計をコメントした

作る時機（平野さんの P1）: **pro/ の公開のマージの後。** Workers Builds は本番のブランチ `cloudflare` の Root directory を読むので、`workers/pros-redirect/` が `cloudflare` に入る前に作るとビルドが失敗する見込み。マージから P1 までの間、`/jpml_pros.html` は今の静的なページが 200 で返る（ページは消していない。消すのは #645 で、P1 の後にする）。

毎朝のデプロイ: 毎朝の `update-live-channel.yml` の再生成（`pro_pages`）で表が変わると `data.json` がコミットされ、P1 の4で Build watch paths の Include を `workers/pros-redirect/**` にすれば、この Worker がデプロイし直される。サイトの `mj` は Exclude に `workers/**` がある（2026-10-06 の申告値。ここからは確かめられない）。

月次の集計: `fetch_redirect_counts.py` は「棚卸しの対象」の転送元だけを数えるので、今は数えない。数える案（「棚卸しの対象」へ移し、`test_redirects_inventory.py` で「仕組み」が Worker の行だけ `_redirects` との照合を外す）は書いたが作っていない。Worker の 301 が GraphQL の `httpRequestsAdaptiveGroups` に入るかは文書に書かれておらず、未確認

### 3. 表記の揺れ

順位変動の JSON（4大会）・title/ の写真カード・質問欄の名簿（各大会の `ask-players.json`）で、在籍者の名前と完全には一致しないが、空白の除去・NFKC（全角半角）・異体字（髙→高・﨑→崎・邊/邉→辺・齋→斎・濵/濱→浜・澤→沢 など）を当てると在籍者の名前と一致するものを全件探した（調べるだけの一時のスクリプト。コードの照合は変えていない）。

| 書かれている名前 | 在籍者の名前 | 出どころ（生成物） | 元のシート |
|---|---|---|---|
| 「こばり　しん」（全角空白） | こばりしん | `houou/race/38-2.json`（ほかに `workers/ask/data.json`） | 「鳳凰」シートの第38期後期の行 |
| 「水口 崚」（半角空白） | 水口崚 | `houou/race/38-2.json`（ほかに `workers/ask/data.json`） | 同上 |
| 「泉　裕斗」（全角空白） | 泉裕斗 | `houou/race/38-2.json`（ほかに `workers/ask/data.json`） | 同上 |

3件とも空白の違いだけ。title/ の写真カードと質問欄の名簿には0件。直すのは平野さんの「鳳凰」シートの作業（この指示では直していない）

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f1d4a0a0）: https://github.com/retroeater/mj-logs/tree/main/guide/f1d4a0a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
