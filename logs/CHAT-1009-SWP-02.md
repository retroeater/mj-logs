# CHAT-1009-SWP-02

- 着手日時: 2026-10-08
- 対象issue: なし
- ブランチ: work/1009-swp-fix
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：横断レビューの第1弾 — 手書きの3ページ（404.html・rh_links.html・jpml_links.html）と ouka_results.js を小さく直し、プレビューで止まる Chat-Ref: CHAT-1009-SWP-02 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1009-swp-fix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-fix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-fix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1006-SWP-01（サイト全体の横断レビュー）の指摘のうち、手書きのファイルだけで直せる小さなものを直す。生成スクリプト・生成物・`style.css` には触らない。
決定（2026-10-09、平野さん）

* 横断レビューの指摘は3段で直す。第1弾は手書きのファイルだけ（生成物に触らない）
* `rh_links.html` の「GitHub」のリンク（`retroeater/mj`。非公開のため訪問者には 404）は外す

前提（チャット側。平野さんの決定ではない）

* 対象の指摘は、CHAT-1006-SWP-01 のログの「### 手順3 統合した指摘の一覧」の G5-01・G3-10（`404.html`）、G4-07（`rh_links.html`）、G1-12 のうち `jpml_links.html` の分、G5-02（`ouka_results.js`）の5行。中身はそのログの行を正とし、ここに写さない
* チャット側が第1弾に入れようとしていたもののうち、次は外した
   * `resource_dictionary.html`（G4-06・G1-12 の辞書の分）: 別のチャットが work/1008-dic（#515、CHAT-1008-DIC-04 は作業中）で同じページを変えているため。所見は CHAT-1009-SWP-03 が #515 にコメントする
   * sitemap のコメントの件数（G5-07）と `llms.txt` の件数（G5-08）: `llms.txt` の手書きの件数の食い違いは #227 に任せて今は直さない、と平野さんが別のチャットで決めた（2026-10-07。docs/decisions/ にあるかは要確認）。sitemap は鳳凰戦の新ページ（#518、work/1008-hou は未マージ）の公開で変わる見込み。どちらも CHAT-1009-SWP-03 が #227 にコメントする
* `style.css` は変えない（work/1008-hou が末尾に houou/ 用の行を足しており、CHAT-1008-DIC-03 はその重なりで止まった）。`404.html` の右の余白（G3-10）が `style.css` を変えないと直せないなら、余白だけ直さずに報告する
* G4-07 の外部リンク（`target="_blank"` も予告も無い）は、`jpml_links.html` と同じ形（新しいタブで開く・予告あり）にそろえる案。G1-12 は、アイコンだけがリンクになっているのを、隣の文字（「公式サイト」など）までリンクに含める案。形は既存の手書きページの書き方（docs/notes/static-generation.md「navbar.js と検索欄」、ルート相対の href〈#162〉）に合わせる
* 修正の検証は、先に「修正前でも通らないか」を確かめる（CLAUDE.md「判断・作業の原則」、#310）
* 表示の確認は、作業ブランチの push で Workers Builds が作るプレビュー（docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」）を Playwright の Chromium で開く。本番（ryoei.pro）はブラウザで巡回しない
* 使う skill は無い

手順

1. 確かめる
   * CHAT-1006-SWP-01 のログの上の5行を読み、今の origin/cloudflare の4ファイルで事象がまだあるかを確かめる（`404.html` の深い階層の挙動は、SWP-01 と同じく 404 応答を返す手元の配信で再現する）。再現しない指摘は直さず、外した理由を書く
   * `git branch -r --no-merged origin/cloudflare` の各ブランチが、この4ファイル（`404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js`）を変えていないかを確かめる
2. 直す
   * `404.html`: 自サイトの参照（favicon・`assets/vendor/…`・`style.css`・`navbar.js` など）をすべてルート相対（`/` 始まり）にする（G5-01）。本文に「トップへ戻る」のリンク（`/`）を足し、右の余白を左と同じにする（G3-10）
   * `rh_links.html`: 「GitHub」のリンクを外す。残りの外部リンクを `jpml_links.html` と同じ形（新しいタブ・予告）にする（G4-07）
   * `jpml_links.html`: アイコンと隣の文字を1つのリンクにする（G1-12）
   * `ouka_results.js`: `console.log` の2行を消す（G5-02）
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * プレビューで、存在しない深い URL（例 `/title/nothing/here.html`）と `/nothing.html` を幅 1280px と 390px で開き、`style.css`・`navbar.js` などが 200 で読まれること、ナビと書式が出ること、「トップへ戻る」で `/` に行けることを確かめる
   * `rh_links`・`jpml_links` のリンクの数・行き先・`target`・予告を、直す前と後で表にする（増えた・減ったリンクはすべて書く）
   * 直す前と後の画面写真を撮って見比べる（写真はコミットしない）。平野さんがプレビューで見る点を、ページと見る点の1行ずつで `## 報告` の「判断が必要なこと」に書く

止まる条件

* 未マージのブランチが4ファイルのどれかを変えている（どのブランチが何を変えているかを書いて止まる）
* `style.css`・生成スクリプト・生成物、またはこの4ファイルと docs/logs/・docs/decisions/ 以外を変える必要が出た（G3-10 の余白だけなら、余白を直さずに進めて報告する）
* プレビューで、直した4ファイル以外のページの表示が変わった
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-02"` に該当なし。識別子 SWP は同じチャットの CHAT-1006-SWP-01 のみ（`git log --all --grep="SWP"`）で、他セッションでは未使用。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-fix origin/cloudflare`。

### 手順1 確かめる

SWP-01 の5行（G5-01・G3-10・G4-07・G1-12・G5-02）を、着手時の origin/cloudflare（cd4e3d2c）の4ファイルで再現した。404 の深い階層は、SWP-01 と同じく 404 応答を返す手元の配信（`python3 -m http.server` ＋ Playwright の `route.fulfill`、status 404）で確かめた。

| 指摘 | 直す前の再現 | 判断 |
|---|---|---|
| G5-01 `404.html` | `/title/nothing/here.html` で `/title/nothing/style.css`・`…/assets/vendor/bootstrap/css/bootstrap.min.css`・`…/navbar.js`・`…/bootstrap.bundle.min.js` が 404、`nav` 無し、body の font が "Times New Roman"、favicon も `/title/nothing/favicon.ico`。`/nothing.html` は正常（1280・390px とも） | 直す |
| G3-10 `404.html` | 本文に `a` が 0 個。`.mj-margin-text` の margin-left 32px・margin-right 0px | リンクは足す。**右の余白は直さない**（下記） |
| G4-07 `rh_links.html` | リンク 16 本、`target` 無し・予告無し。GitHub のリンク先 `retroeater/mj` は未ログインで 404（SWP-01 で確認済み） | 直す（GitHub を外す） |
| G1-12 `jpml_links.html` | リンク 22 本、リンクの範囲は 16×17px のアイコンだけ、文字はリンクの外 | 直す |
| G5-02 `ouka_results.js` | `console.log(season)`・`console.log(formattedClass)` が `getFormattedClass`（281・285 行）にある | 直す |

再現しなかった指摘: なし。

未マージのブランチ: `origin/work/1008-dic`・`origin/work/1008-hou`・`origin/work/1009-nen` について `git diff --stat origin/cloudflare...origin/<ブランチ> -- 404.html rh_links.html jpml_links.html ouka_results.js` を取った。3ブランチとも4ファイルの変更は無し（止まる条件に当たらない）。

### 手順2 直した内容

- `404.html`: favicon・Bootstrap の CSS・JS・`style.css`・`navbar.js` の5つの参照を `/` 始まりにした。本文に `<p><a href="/">トップへ戻る</a></p>` を足した。
- `rh_links.html`: GitHub の行（`https://github.com/retroeater/mj`）を削除。残り15本を `jpml_links.html` と同じ形（`target="_blank"`・リンク内に外部リンクのアイコン・「（新しいタブで開く）」の visually-hidden）にした。リンクの文字が名前になるため、アイコンの `alt` は空にした。
- `jpml_links.html`: アイコンと隣の文字（と予告）を1つのリンクにした。アイコンの `alt` は元のまま残した（同名の「公式サイト」が3つあり、アクセシブルネームの区別を保つため。読み上げでは `alt` と文字が続けて読まれるので冗長になる点は報告に書く）。
- **アイコンを `<span>` で包んだ理由**: `style.css` の `a:has(> img) { text-decoration: none }` は `img` が `<a>` の直下にあるリンクの下線を消す。文字をリンクに入れると、直下に `img` があるままでは文字の下線まで消えて色だけの区別になる。`style.css` は変えられないため、`img` を `<span>` で包んで規則に当たらないようにした（HTML のコメントに理由を書いた）。計測: 直す前は全リンクの `text-decoration-line` が `none`（`jpml_links`）、直した後は `underline`。
- `ouka_results.js`: `console.log` の2行を、前後の空行ごと消した。`node --check` は通る。
- 4ファイル以外・`style.css`・生成スクリプト・生成物は変えていない。

#### G3-10 の右の余白を直さなかった理由

`.mj-margin-text` の余白は `style.css:80-83`（`margin-left: 32px; margin-top: 24px`）で、右の指定が無い。`style.css` を変えずに直すには、インラインの `style` 属性（CSP #9 の予定に反する）か、32px にならない Bootstrap の余白クラス（`me-4`＝24px、`me-5`＝48px）しかない。指示どおり余白だけ直さずに進めた。同じ `.mj-margin-text` を使う `jpml_links`・`rh_links`・`resource_dictionary` も右の余白が 0（390px で文字が右端に付く）。`style.css` に `margin-right: 32px` を足せば4ページがまとめて直る。

### 手順3 検証

- 先に「修正前でも通らないか」: 同じスクリプト（`scratchpad/fix/shot.js`）を、直す前の作業ツリーに対して実行し、上の表の事象が出ることを確かめてから直した（#310）。直した後に同じスクリプトで、事象が消えることを確かめた。
- `python3 -m unittest discover -s scripts/tests`: 654件 OK。
- プレビュー: 作業ブランチの push（`eaf7f00b`）で Workers Builds が走り、check-run「Workers Builds: mj」は `completed / success`（push から約5分）。URL は最終報告にだけ書く。
- プレビューの配信が作業ツリーと同じこと: `404.html`・`rh_links.html`・`jpml_links.html`・`ouka_results.js` と、無関係な `jpml_pros.html`・`ouka_results.html`・`style.css`・`navbar.js`・`index.html` の本文の sha256 が、手元のファイルと一致（変更した4ファイル以外は cloudflare と同じ内容を配信している）。
- プレビューの実際の 404（`not_found_handling: 404-page`）: `/title/nothing/here.html`・`/nothing.html` とも HTTP 404 で `404.html` の本文を返す。1280px・390px とも、`style.css`・Bootstrap の CSS/JS・`navbar.js` は 200（失敗した応答 0 件）、`nav` が出て、font は "Hiragino Sans"、`a[href="/"]`「トップへ戻る」が1つ。直す前は手元のモックで再現したもので、プレビューでの直す前は取っていない（直す前のコミットのプレビューが無いため）。「トップへ戻る」のリンク先は `href="/"` を確認した（クリックして `/` へ遷移することはプレビューで未実施）。
- 画面写真は scratchpad にだけ置いた（コミットしない）。

#### `jpml_links.html`（22本、増減なし。行き先はすべて同じ）

全22本で、`target="_blank"`・予告ありは前後とも同じ。変わったのはリンクの範囲と下線:

| | 直す前 | 直した後 |
|---|---|---|
| リンクの範囲 | アイコンだけ（16×17px） | アイコン＋文字（52〜200px 幅×17px。例: 公式サイト 102×17、ニコニコチャンネル 168×17、公式オンラインショップ 200×17） |
| 下線（`text-decoration-line`） | none（アイコンのみ） | underline（文字に付く） |
| 文字 | 予告「（新しいタブで開く）」だけがリンク内 | 「公式サイト（新しいタブで開く）」のように文字と予告がリンク内 |

行き先（22本）: `ma-jan.or.jp`・`note.com/jpml`・`x.com/JPML0306`・`x.com/JPML_sokuhou`・YouTube 2本・`openrec.tv`・`ch.nicovideo.jp`・`shop.ma-jan.or.jp`・`worldriichi.org`・`wrc2025tokyo.com`（ja/en）・`wrc2022vienna.com`・`wrc2017vegas.com`（http）・`x.com/riichisekai`・`x.com/WorldRiichi`・YouTube `@worldriichi`・`ron2.jp`（http）・`x.com/ron2jp`・`x.com/tattuan_`・YouTube（龍龍）・`ron2.jp/3/`。

#### `rh_links.html`（16本→15本）

| 行き先 | 直す前 | 直した後 |
|---|---|---|
| `https://github.com/retroeater/mj`（GitHub） | `target` なし・予告なし・下線あり | **削除**（減った1本） |
| 残り15本（Bootstrap・Codespaces・EzOCR・Favicon Generator・Google Analytics・Google Charts・Google Search Console・Google Sheets・ImportJSON・Namecheap・PageSpeed Insights・Twitobu・Twitter Card Validator・vis.js・Visual Studio Code） | `target` なし・予告なし・下線あり・幅 36〜170px×17px | `target="_blank"`・予告あり・下線あり・アイコン付きで幅が約20px 増える（56〜190px×17px） |

増えたリンクは無い。Google Analytics・ImportJSON・vis.js・Twitter Card Validator・Twitobu の掲載が現状と合っているかは確かめていない（SWP-01 の未確認のまま）。

## 報告

- 状態: 判断待ち（プレビューを平野さんが見てから、別の指示でマージ）
- ブランチ: work/1009-swp-fix
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-fix/docs/logs/CHAT-1009-SWP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-fix
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `404.html`（`/title/nothing/here.html`・`/nothing.html`）・`rh_links.html`・`jpml_links.html`。Workers Builds の check-run は `completed / success`
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - 平野さんがプレビューで見る点（スマホ幅は iPhone の Safari で）:
    - 存在しない深い URL（プレビューのドメインに `/title/nothing/here.html`）: ナビが出る、書式が Times 体でなくゴシック体、「トップへ戻る」を押すと `/` に行く
    - 存在しない浅い URL（`/nothing.html`）: 同じく「トップへ戻る」があり、見た目が崩れていない
    - `rh_links.html`: GitHub が消え、15本のリンクの前に外部リンクのアイコンが付き、押すと新しいタブで開く
    - `jpml_links.html`: アイコンと隣の文字がひとつながりの下線付きリンクになり、押す範囲が文字まで広がった。前の見た目と比べて、アイコンと文字の間の空きが気にならないか
  - **G3-10 の右の余白は直していない。** `style.css` に `.mj-margin-text { margin-right: 32px }` を足せば `404`・`jpml_links`・`rh_links`・`resource_dictionary` の4ページがまとめて直る（`style.css` は work/1008-hou が末尾を変えているため、この指示では触らなかった）。第2弾（`style.css`）に入れるか決めてほしい。
  - `jpml_links.html` のアイコンの `alt` は元のまま残した（同名の「公式サイト」3つを区別するため）。読み上げでは「日本プロ麻雀連盟（公式サイト） 公式サイト（新しいタブで開く）」のように冗長になる。`alt` を空にして文字だけで読ませる（区別は見出し頼み）か、今のままにするか。
  - `rh_links.html` に残るツールの一覧（Google Analytics・ImportJSON・vis.js・Twitter Card Validator・Twitobu）が今も使っているものかは未確認。載せ続けるか決めてほしい。
- 未確認の項目:
  - 直す前の状態をプレビュー（実際の 404 の配信）では取っていない。直す前は手元のモック（404 応答を返す）で再現した。
  - 「トップへ戻る」を押して `/` に遷移することはクリックしていない（`href="/"` の確認のみ）。
  - 実機（iPhone Safari）での見え方。画面写真は Chromium（1280px・390px）のみ。
  - `ouka_results.html` の表示（Charts が描けないため）。`console.log` の削除は `node --check` と本文の一致のみ。
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
