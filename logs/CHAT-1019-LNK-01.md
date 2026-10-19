# CHAT-1019-LNK-01

- 着手日時: 2026-10-19
- 対象issue: 未定（手順1で起票）
- ブランチ: work/1019-lnk
- 着手時HEAD: 6bb0f617c

## 指示

【Claude作成】Claude Code 向け指示：「連盟 > リンク」（jpml_links.html）を廃止する（ページを消して 404、メニュー・サイトマップ・llms.txt・文書から外す） Chat-Ref: CHAT-1019-LNK-01 マージ: 承認済み（チャットで。2026-10-19 に平野さんが「マージまで任せてよい（今すぐ）」と答えた） 貼る時機: CHAT-1019-EXM-06（/exam/ の公開。メニュー「連盟」からプロテストを外す変更を含む）が cloudflare にマージされた後。0章で、origin/cloudflare の `_redirects` に `/jpml_test.html` の 301 の行があることで確かめ、無ければ何もせず止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて「貼る時機」の前提を確かめる。

目的
メニュー「連盟 > リンク」（`jpml_links.html`。連盟・リーチ麻雀世界選手権・龍龍の外部リンク集）を廃止する。役割は連盟公式サイトが担う予定で、現状もあまりメンテしていないため。
決定（2026-10-19、平野さん）

* 「連盟 > リンク」を廃止する。理由は、公式サイトがその役割を担う予定であること、現状もあまりメンテしていないこと
* 旧 URL `/jpml_links.html` は転送せず、ページを消して 404 にする（公式サイトのトップへの 301・サイトのトップへの 301 は採らない）
* cloudflare へのマージまで任せてよい（今すぐ）

前提（チャット側。平野さんの決定ではない）

* `jpml_links.html` は手書きの静的ページ（docs/notes/static-generation.md の「静的なページ」の表。要確認）。2026-10-19 にチャット側が本番を読んだときの中身は、見出し「日本プロ麻雀連盟 リンク」と【日本プロ麻雀連盟】9件・【リーチ麻雀世界選手権】7件・【龍龍】5件の外部リンク
* 転送を足さないので docs/notes/redirects.md の一覧は変えない
* 次の決定はこの廃止で役目を終える。docs/decisions/ にあれば「済（廃止）」の注記か置き換えにする（消さない）: `jpml_links.html` の【龍龍】リンクは残す（2026-09-28 の龍龍廃止のとき。記録の場所は要確認）、site-review.md のアイコンの `alt`・右の余白、seo-bing.md の h1 の8ページ
* 参照の候補（要確認。名指しに頼らず、手順2の検索で出た所を全部直す）: `navbar.js` のメニュー、`sitemap.xml`、`llms.txt`、`scripts/` の生成・メタ付けの対象一覧（`apply_page_meta.py`・`regenerate.py` など）、テスト、`style.css` の `.mj-margin-text` を使うページの記述、docs/notes/static-generation.md・design.md・video-wayhome.md の一覧
* 他ページから `jpml_links.html` への本文中のリンクがあれば外す（リンク先を公式サイトに差し替えるなどの新しい仕様は足さない。外し方に迷えば止まる）
* 決定の記録先の分野は、既存の docs/decisions/ のファイルから受け手が選ぶ（ページの廃止を書いた先例があればそれに合わせる）

手順

1. 同じ論点の issue（「jpml_links」「リンク」で、クローズ済みも含めて）を検索する。別の方針（残す・転送する等）の決定があれば止まって報告する。無ければ「連盟 > リンク（jpml_links.html）の廃止」を起票し（ラベルは既存のものから。合うものが無ければ付けずに報告に書く）、着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、手順2で変えるファイルの同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめて `## 経過` に書く。
2. `jpml_links` を、リポジトリ全体（`docs/logs/`・`docs/gsc/` などの記録を除く）で検索し、出た所を全件 `## 経過` に表で書いてから直す: ページを削除し、メニュー・サイトマップ・`llms.txt`・スクリプトの対象一覧・テスト・文書から外す。docs/decisions/ に今回の決定を書き、上の前提の役目を終える決定を注記・置き換えする。外した後のメニュー「連盟」に残る項目を全件 `## 経過` に書く（0件になるなら止まる。メニュー自体の扱いは平野さんの判断が要る）。
3. 確かめてマージする: テスト、`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「消した `jpml_links.html`」「navbar を描く全ページの変化があればその種類」「`sitemap.xml`・`llms.txt`」「スクリプト・テスト・文書」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる。work/ ブランチのプレビューで PC とスマホ幅のメニュー「連盟」を開いた状態を撮って `docs/logs/CHAT-1019-LNK-01/` に置く。通ればマージし（docs/notes/branch-operations.md「マージするとき」）、本番で `/jpml_links.html` が 404、メニュー「連盟」に「リンク」が無いこと、`sitemap.xml`・`llms.txt` に無いことを確かめ（クエリ `?v=<未使用の値>` 付きでも確かめる）、issue に結果を書いてクローズする。

止まる条件

* 前提が食い違う（`jpml_links.html` が生成されるページだった、ほかのページの生成に使われている等で、消すと別のページが変わる）
* 同じ論点で別の方針の issue・決定がある、他セッションの着手中コメントがある
* 未マージの work/ ブランチが手順2で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 外した後のメニュー「連盟」の項目が0件になる
* 手順3の差分に、上の種類で説明できないものが出た（全部を書いて止まる）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係な失敗なら原因を報告に書いたうえで本番の確かめ・クローズを進めてよい。今回の変更で落ちると分かっているテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 貼る時機の前提: origin/cloudflare（6bb0f617c）の `_redirects` 111行目に `/jpml_test.html  /exam/  301` がある
- Chat-Ref の重複: `git log --all --grep` で `CHAT-1019-LNK` も `LNK` も無し（unshallow 後）。`docs/logs/` の履歴にも `-LNK-` は無い
- 作業ブランチ: ローカルにもリモートにも `work/1019-lnk` は無かったので `git checkout -b work/1019-lnk origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1

- issue の検索（「jpml_links リンク ページ 廃止」「連盟 リンク集 メニュー 残す 転送」、クローズ済みを含む）: 別の方針の決定は無い。
  関係するのは #313（クローズ。リンクの入れ替え）と #531（クローズ。`rh_links.html` の廃止＝先例）だけ
- 起票: #616「連盟 > リンク（jpml_links.html）の廃止」。ラベルは #531 と同じ「分野: 整理・保守」「対象: 全ページ」。着手中のコメントを書いた
- 未マージのブランチ（7本）と cloudflare の差で、手順2のファイル（jpml_links.html・navbar.js・sitemap-pages.xml・llms.txt・static-generation.md・design.md・video-wayhome.md・site-review.md・seo-bing.md ほか）を変えているもの:
  `origin/work/1017-lvp` が `navbar.js` の91行付近（`newNavCurrent` の分岐に `mj-live-n-page` を足す）を変えている。今回は35行のメニュー項目で、同じ行・同じ関数ではなく、取り込みでも衝突しない（止まらない）

### 手順2: `jpml_links` の検索結果（`git grep`、docs/logs・docs/gsc を除く）

| 場所 | 中身 | 扱い |
|---|---|---|
| `jpml_links.html` | ページ本体（13行目 og:url） | 削除 |
| `navbar.js:35` | メニュー「連盟」の項目「リンク」 | 削除 |
| `sitemap-pages.xml:40` | `<url>` | 削除（lastmod は手で書き換えていない） |
| `llms.txt:18` | 「リンク」の行 | 削除 |
| `docs/notes/static-generation.md:280` | 「静的なページ」2件 | 1件・`404.html` だけに。256行の「HTMLは23ページ」も先例（#531）にならい 22 に |
| `docs/notes/static-generation.md:454・463` | `data-search="off"` のトップ階層7ページ・手書き2ページ | 6ページ・手書き1ページ（`404`）に |
| `docs/notes/design.md:76` | `.mj-margin-text` を使うページ | `404`・`resource_efficiency` に（`style.css` の規則は2ページで使うので残す） |
| `docs/notes/video-wayhome.md:335` | `data-search="off"` の手書き6ページ | 5ページに |
| `docs/decisions/site-review.md:37・38` | 右の余白・アイコンの `alt` | 「済（廃止）」の注記 |
| `docs/decisions/seo-bing.md:60` | h1 の8ページ | `jpml_links` の分に「済（廃止）」の注記 |
| `docs/lighthouse-baseline.md:116・498` | 計測時点の記録 | 変えない（過去の計測の記録） |
| `docs/notes/handover-archive-2026.md:550・553` | 事例の退避先 | 変えない（記録） |
| `docs/review-followup-instructions.md:177` | 過去の指示文の写し | 変えない（記録） |

- スクリプト・テスト・`_redirects`・`_headers`・`.assetsignore`・`workers/` には出ない（`apply_page_meta.py` は既に削除済み）。`jpml_links.html` は手書きで、生成にも他ページの生成にも使われていない（前提どおり）
- 他ページ（HTML）の本文から `jpml_links.html` へのリンクは無い
- 前提の「`jpml_links.html` の【龍龍】リンクは残す」の決定は `docs/decisions/` に見つからなかった（`grep 龍龍` は pros.md・title.md の列の削除だけ）。注記する対象なし
- 決定の記録先: 先例（`rh_links.html` の廃止）と同じ `docs/decisions/site-review.md` に「2026-10-19（CHAT-1019-LNK-01）」を足した
- `docs/notes/static-generation.md`「navbar.js と検索欄」の「27本のページhref…実ページ25本」は今回の前から実数（22本）と合っていない。今回の項目ではないので触らない
- 外した後のメニュー「連盟」の項目: 「プロ」（`/jpml_pros.html`）・「タイトル戦」（`/title/`）の2件（0件ではない）
- テスト: `python3 -m unittest discover -s scripts/tests` 1442件 OK
- 成果物のコミット fc2798f31。その後 origin/cloudflare（b8020e176、docs のみ2コミット）を取り込んだ（衝突なし）

### 手順3: 確かめ

- 再生成: スクラッチのクローン2つ（a = 作業ブランチ fc2798f31、b = origin/cloudflare の 6bb0f617c）で `python3 scripts/regenerate.py all`。どちらも rc=0
- `git status --short` は a・b とも0件（データの変化なし。navbar はページに焼き込まれず実行時に描くので、生成ページの変化も無い）
- a と b の木の差（`diff -rq -x .git`）: `jpml_links.html`（b のみ）・`navbar.js`・`sitemap-pages.xml`・`llms.txt`・`docs/notes/` の3つ・`docs/decisions/` の2つ・このログ、と `scripts/**/__pycache__/*.pyc`（実行で作られたバイトコード。追跡外）だけ。説明できない差は無い
- プレビュー（d2964cf9c の「Workers Builds: mj」success。URL は最終報告だけ）: `/jpml_links.html` が 404、`navbar.js` に `jpml_links` 0件。
  ヘッドレス Chromium で `jpml_pros.html` のメニュー「連盟」を開いて撮影: PC（1280px）・スマホ幅（375px）とも項目は「プロ」「タイトル戦」の2件。
  `docs/logs/CHAT-1019-LNK-01/menu-pc.png`・`menu-sp.png`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8020e17）: https://github.com/retroeater/mj-logs/tree/main/guide/b8020e17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8020e17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
