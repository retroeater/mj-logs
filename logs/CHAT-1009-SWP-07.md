# CHAT-1009-SWP-07

- 着手日時: 2026-10-09
- 対象issue: #531
- ブランチ: work/1009-swp-rhl
- 着手時HEAD: f3e6f8b4

## 指示

【Claude作成】Claude Code 向け指示：rh_links.html を廃止する（#531。転送せず 404）— プレビューで止まる Chat-Ref: CHAT-1009-SWP-07 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1009-swp-rhl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-swp-rhl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-rhl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`rh_links.html` をサイトから外す（#531）。ページを消し、サイト内の導線と一覧から外す。
決定（2026-10-09、平野さん）

* `rh_links.html` は転送（301）せず、404 にする
* #531 には今すぐ着手する（鳳凰戦の新ページ〈#518〉の公開を待たない）

前提（チャット側。平野さんの決定ではない）

* 外す先の見込み（要確認。`git grep -n "rh_links"` で全件を洗い出し、実物を正とする）: `rh_links.html` 本体、`navbar.js` の項目（「良栄」のメニューの「リンク」と見られる）、`sitemap-pages.xml` の `<loc>`、`llms.txt` の行、ほかのページからのリンク、docs/notes/static-generation.md「ページの一覧」（静的なページの数）・「navbar.js と検索欄」（href の本数・`data-search="off"` の対象の一覧）などの文書、`scripts/tests/` の一覧があればそれも
* `_redirects` には足さない（決定のとおり 404）。sitemap の lastmod は手で書き換えない（CLAUDE.md「禁止事項」）。sitemap・`llms.txt` の冒頭コメントや本文の件数は今は直さない（G5-07 は #518 の公開の issue、`llms.txt` の件数は #227 に任せる、という平野さんの決定）
* 未マージの work/1008-hou（#518）も `navbar.js`・sitemap・`llms.txt` を変えている見込み（要確認）。重なりがあっても、平野さんの決定（今すぐ着手）により止まらずに進め、変える行は `rh_links` に関わる行だけにする（後で work/1008-hou を取り込むときの衝突を小さくするため）。重なったファイルと行を報告に書き、#518 にその旨をコメントする
* 公開の手順の逆になるので、`docs/new-page-checklist.md` の項目を参考に、外し漏れが無いかを確かめる
* 使う skill は無い

手順

1. 確かめる: #531 の本文と、他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。`git grep -n "rh_links"` の全件と、`git branch -r --no-merged origin/cloudflare` の各ブランチが同じファイルを変えているかを表にする
2. 外す: `rh_links.html` を消し、上の見込み（手順1で直したもの）から `rh_links` に関わる行を消す。`python3 -m unittest discover -s scripts/tests` を通す
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）: `/rh_links.html` が 404（`404.html` の本文）になること、ナビの「良栄」のメニューの項目が意図どおり減っていること（1280px・390px）、ほかのページの表示が変わっていないこと。平野さんがプレビューで見る点を、ページと見る点の1行ずつで「判断が必要なこと」に書く。#531 に結果をコメントする（末尾に `Chat-Ref: CHAT-1009-SWP-07`）

止まる条件

* #531 に他セッションの着手中コメントがある、または #531 が閉じている
* `rh_links` を参照するものが、上の見込みのほかに生成スクリプト・データ（シート）にある（消し方の判断が要る）
* 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-07"` に該当なし。識別子 SWP は同じチャットの SWP-01〜06 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1009-swp-rhl origin/cloudflare`。

### 手順1 確かめる

- #531: open、コメントは0件（他セッションの着手中コメントなし）。「着手中」のコメントを残した（Chat-Ref 付き）。
- `git grep -n "rh_links"`（`docs/logs/` を除く）の全件と、未マージのブランチが同じファイルを変えているか:

| 場所 | 内容 | 扱い | 未マージのブランチの変更 |
|---|---|---|---|
| `rh_links.html` | 本体 | 削除 | なし |
| `navbar.js:83` | メニュー「良栄」の「リンク」 | 1行削除 | なし（`work/1008-hou` は変えていない） |
| `sitemap-pages.xml:140` | `<url>`（`<loc>` と `<lastmod>`） | 4行＋空行を削除（lastmod は触っていない） | なし |
| `llms.txt:61` | 「リンク」の行 | 1行削除 | なし |
| `docs/notes/static-generation.md` 252・271・438・446 行 | 全ページ数 26→25、静的なページ 3→2、手書きの列挙 | 直した | **`work/1008-hou` が変更（252 行は同じ行、271 行は隣り合う）。衝突する** |
| `docs/notes/video-wayhome.md:254` | `has_search_boxes` の手書きの列挙（7→6ページ） | 直した | なし |
| `scripts/apply_page_meta.py:130` | `PAGES` の `rh_links.html` の項目 | **触らない**（下の判断） | なし |
| `docs/lighthouse-baseline.md:499`・`docs/review-followup-instructions.md:177` | 過去の計測・指示の記録 | 触らない | なし |
| `docs/decisions/`（seo-bing・site-review）・`docs/gsc/2026-10-03/pages-unindexed.md` | 過去の決定・データ | 触らない | なし |
| `rh_links.html:12`（`og:url`） | 本体の中 | 本体と一緒に削除 | — |

- ほかのページの HTML・`_redirects`・シート・生成スクリプト（`scripts/generate_*.py`）・`scripts/tests/` に `rh_links` を参照するものは無い（`git grep` に出ない）。
- **止まる条件の判断（`scripts/apply_page_meta.py`）**: 指示の見込みに無い参照が1件ある。このスクリプトは全ページの `<title>`・description・OGP を一度書き換えた手動の保守用スクリプトで、`regenerate.py`・Actions のどちらからも呼ばれず（`grep` で確認）、`PAGES` の各項目は `path.exists()` が偽なら飛ばす（`apply_page_meta.py:195` 付近）。生成スクリプトでも、シートの読み込みでもなく、消し方に判断は要らない（残しても動作に影響しない）。#531 の本文も「#517 でスクリプトの扱いを決める」としているため、**触らずに進めた**。止まる条件の「生成スクリプト・データにある」には当たらないと判断した（当たるなら止まるべきなので、判断が違えば教えてほしい）。
- 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）: `work/1008-dic`・`work/1008-hou`・`work/1009-nen`（と自分）。`navbar.js`・`sitemap-pages.xml`・`llms.txt`・`rh_links.html`・`apply_page_meta.py` を変えているものは無く、`docs/notes/static-generation.md` だけが `work/1008-hou` と重なる。指示の見込み（`work/1008-hou` が `navbar.js`・sitemap・`llms.txt` も変えている）とは違い、実物では変えていなかった。

### 手順2 外した内容

- `rh_links.html` を削除（`git rm`）。
- `navbar.js`: 「良栄」のメニューの `<a class="dropdown-item" href="/rh_links.html">リンク</a>` の1行を削除（成績・成績詳細・牌譜の3項目になる）。`node --check` OK。
- `sitemap-pages.xml`: `rh_links.html` の `<url>` ブロックを削除（`lastmod` は書き換えていない）。XML として読める。冒頭コメントの件数・`llms.txt` の本文の件数は直していない（G5-07 は #518 の公開の issue、`llms.txt` の件数は #227。平野さんの決定）。`python3 scripts/update_sitemap_lastmod.py --from-git` を手元で実行して、ほかの URL の lastmod が変わらず、削除した状態のままエラーなく終わることを確かめた（出力はコミットしていない）。
- `llms.txt`: 「リンク」の行を削除。
- `docs/notes/static-generation.md`: 「HTMLは26ページ」→「25ページ」、「静的なページ」の行（3→2、`404.html` / `jpml_links.html`）、has_search_boxes の列挙（トップ階層の9ページ→8ページ、手書きの残り3ページ→2ページ）。「現在の対象は1521ページ（2026-09-29）」は日付つきの実測なので直していない。
- `docs/notes/video-wayhome.md`: `has_search_boxes` の手書きの対象「7ページ」→「6ページ」。
- `_redirects` には足していない（決定どおり 404）。`python3 -m unittest discover -s scripts/tests`: 659件 OK。
- 取り込み: `origin/cloudflare` を merge した（衝突なし、`1f666535`）。

#### `work/1008-hou` との重なり（取り込み時の衝突の見込み）

`git merge-tree --write-tree HEAD origin/work/1008-hou` の結果、`docs/notes/static-generation.md` で2か所が衝突する: **252 行**（この枝は「25ページ」、`work/1008-hou` は同じ行の別の部分を変更）と、271 行付近（`cloudflare` 側の `title/` の行の変更と、`work/1008-hou` の鳳凰戦の行の追加）。後者は `rh_links` とは関係なく、`origin/cloudflare` 単体との衝突でもある（`docs/new-page-checklist.md` も同じ）。#518 に重なりをコメントした。

### 手順3 プレビューの確認

- 作業ブランチの push（`1f666535`）で Workers Builds が走り、check-run は `completed / success`（push から約2分）。プレビューの URL は最終報告にだけ書く。
- プレビューの配信が手元と同じこと: `navbar.js`・`llms.txt`・`sitemap-pages.xml`・`jpml_links.html`・`jpml_pros.html`・`404.html`・`style.css` の本文の sha256 が手元と一致。`/sitemap-pages.xml`・`/llms.txt` に `rh_links` は無い。
- `/rh_links.html`・`/rh_links`: HTTP 404。`/rh_results.html`・`/jpml_links.html`・`/sitemap-pages.xml`・`/llms.txt`: HTTP 200。
- 1280px・390px で `/rh_links.html` を開くと、`404.html` の本文（h1「ページが見つかりません」）・ナビが出る・書式はゴシック体・失敗した応答 0 件。
- 1280px・390px で `/rh_results.html` を開いてメニュー「良栄」を開くと、項目は 成績（`/rh_results.html`）・成績詳細（`/rh_results_detail.html`）・牌譜（`/rh_paifu.html`）の3つ。390px のメニューの見え方の写真を確認した。
- ほかのページの表示: ナビの項目は全ページ共通で1つ減るが、これは意図。ほかの HTML・CSS・JS は変えていない（上の sha256 が一致）。
- #531 に結果をコメントした。#518 に重なりをコメントした。

## 報告

- 状態: 判断待ち（プレビューを平野さんが見てから、別の指示でマージ）
- ブランチ: work/1009-swp-rhl
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-rhl/docs/logs/CHAT-1009-SWP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-rhl
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `/rh_links.html`（404）・`/rh_results.html` のメニュー「良栄」・`/jpml_links.html`。Workers Builds の check-run は `completed / success`
- マージ: 未（平野さんの判断待ち）
- issue: #531（着手中・結果をコメント）・#518（`work/1008-hou` との重なりをコメント）。起票なし
- 判断が必要なこと:
  - 平野さんがプレビューで見る点:
    - `/rh_links.html`（プレビューのドメインに付ける）: 404 になり、`404.html` の本文（「ページが見つかりません」・ナビ・「トップへ戻る」）が出る。スマホでも崩れない
    - ナビのメニュー「良栄」（PC とスマホ）: 項目が 成績・成績詳細・牌譜 の3つになっている（「リンク」が無い）
    - ほかのページ（例: `/jpml_pros.html`）: ナビ以外は見た目が変わっていない
  - **`scripts/apply_page_meta.py` の `rh_links.html` の項目は触っていない**（手動の保守用で、存在しないファイルは飛ばすため無害。#517 でスクリプトの扱いを決める予定）。止まる条件の「生成スクリプト・データにある」に当たるという判断なら、止まるべきだった。違うなら教えてほしい
  - **`work/1008-hou`（#518）の取り込みで `docs/notes/static-generation.md` が衝突する**（252 行は、この枝の「25ページ」と work/1008-hou の変更が同じ行。271 行付近は `cloudflare` 側の変更と work/1008-hou の変更。後者は `rh_links` とは無関係）。指示の見込みと違い、`navbar.js`・`sitemap-pages.xml`・`llms.txt` は work/1008-hou が変えておらず、重ならない。#518 にコメントした
  - 「1521ページ」（`docs/notes/static-generation.md` の `data-search="off"` の実測）は日付つきの実測のため直していない
- 未確認の項目:
  - 本番はまだ反映していない（マージは別の指示）。本番での `/rh_links.html` の 404、ナビの項目は、マージ後に確かめる
  - 実機（iPhone Safari）での見え方。写真は Chromium（1280px・390px）のみ
  - 外部から `rh_links.html` へのリンクがあるか（Search Console では「クロール済み未登録」で、直近28日の検索の行は無い。外部リンクは未確認）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5bba42c1）: https://github.com/retroeater/mj-logs/tree/main/guide/5bba42c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5bba42c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e7c1a8d.md
