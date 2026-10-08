# CHAT-1008-NEN-03

- 着手日時: 2026-10-08
- 対象issue: #277
- ブランチ: work/1008-nen
- 着手時HEAD: ed801987

## 指示

【Claude作成】Claude Code 向け指示：NEN-02 の続き。timeline で落ちる2つのテストの扱いを決めたので、年表の試作と比較ページを最後まで作る（未マージ・判断待ちで止まる） Chat-Ref: CHAT-1008-NEN-03 マージ: 判断待ちで止まる（プレビューを見て平野さんが表示の形と色分けを決める。cloudflare へは入れない） 貼る時機: CHAT-1008-NEN-02 が「判断待ち（テストの扱いを平野さんに質問中）」で止まった後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-nen の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-nen を続けて使う（NEN-02 のログがある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1008-NEN-02.md の `## 報告` を読み、状態が「判断待ち（テストの扱いを平野さんに質問中）」でなければ何もせず止まる。NEN-02 の `## 報告` の状態の末尾に `/ 続き: CHAT-1008-NEN-03` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
NEN-02 は、`title/timeline/` を置くと `test_title_ogp` と `test_title_redirects` が落ちる（title/ 直下のディレクトリをすべて大会とみなす）ため、止まる条件に当たって実装の前で止まった。2つのテストの扱いを決めたので、NEN-02 の目的（年表の試作と比較ページ）を最後まで行う。
決定（2026-10-08、平野さん）

* （この指示で新しい決定はない。テストの扱いは下の「前提」のチャット側の案）

前提（チャット側。平野さんの決定ではない）

* `test_title_ogp.test_every_taikai_has_image`: 大会の集合は title/ 直下のディレクトリの列挙ではなく、生成が持つ大会の一覧（「タイトル戦」タブの表示する大会の slug。`generate_title_pages.py` に既にある関数か定数があればそれ）から取る形に直し、`timeline` のような大会でないディレクトリを対象に入れない。大会の一覧が生成時にしか得られない（シートを読む）なら、`timeline` を「大会でないディレクトリ」の定数（例: `NON_TAIKAI_DIRS`）として `generate_title_pages.py` に置き、テストはそれを除く。どちらにしたかと理由を報告に書く。「置いた画像が実在の大会に当たる」の検査はそのまま
* `test_title_redirects.test_each_taikai_has_trailing_slash_redirect`: `_redirects` に `/title/timeline /title/timeline/ 301` の1行を足す（#455 の規則どおり、大会と同じ扱い。未公開でも URL を直接打って開けることが段1の条件なので要る。docs/notes/title-pages.md「URL の解決（_redirects）」の該当の記述も、大会以外のディレクトリがあることが分かるように直す）。`_redirects` の先頭行は触らない（CLAUDE.md「禁止事項」）
* 上の2つ以外のテストが落ちたら、直さず止まる（NEN-02 と同じ）
* 試作の内容・比較の仕組み・年ジャンプ・リンク・title・年の無い2期の扱い・確かめ方は、docs/logs/CHAT-1008-NEN-02.md の `## 指示` 欄の「前提」と手順2・3のとおり（この指示に写さない。読んでから作る）。決定は docs/decisions/title.md「2026-10-08（CHAT-1008-NEN-01）」

手順

1. 確かめる: NEN-02 の手順1の残り（「タイトル戦」タブに slug `timeline` の大会が無い。生成と同じ経路でシートを読み、表示する大会数と期数、年の無い期の数と、リーチ麻雀世界選手権 第1・2回の年の有無〈平野さんは 2014年・2017年を入れたと言っている〉を報告する）。未マージの `work/` ブランチの重なりは NEN-02 で確かめ済みだが、`git branch -r --no-merged origin/cloudflare` を取り直して増えていないかを見る
2. 作る: 上の前提のとおり2つのテストの扱いを入れ、NEN-02 の手順2（生成・比較の仕組み・年ジャンプ・リンク・共有ボタン・noindex・`sitemap-title.xml` に載せない・生成物の差分の確かめ・`python3 -m unittest discover -s scripts/tests`・#277 へのコメント）を行う
3. 確かめる: NEN-02 の手順3（プレビューで3つの幅、A1〜A3 × B0〜B3、年ジャンプ、固定バーの高さ、遅延読み込み、コントラスト比の表、平野さんに見てもらう手順）

止まる条件

* NEN-02 の止まる条件のとおり（#277 の状態、未マージのブランチの重なり、slug `timeline` の重なり、大会 20〜21・期 363〜365 の範囲外、生成物の差分に `title/timeline/` の追加と `_redirects` の1行以外が出た、上の2つ以外のテストが落ちる、固定バーの高さの仕組みを大きく変える必要がある）
* `_redirects` の変更が1行の追加で済まない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが決める2点（形 A1〜A3・色 B0〜B3）と、見てもらう手順、本実装で消す・足すものの一覧（ラジオボタン・採らない案の CSS・og:image `img/ogp/title/timeline-black.png`・テスト・docs/notes/title-pages.md・static-generation.md「ページの一覧」・公開の issue の起票）を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-NEN-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-NEN-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-02 の状態は「判断待ち（テストの扱いを平野さんに質問中）」だった。末尾に「/ 続き: CHAT-1008-NEN-03」を足した。このセッションは NEN-01・NEN-02 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1008-NEN-03"` は0件
- ブランチ: `origin/work/1008-nen` はあり、ローカルと一致（03fdba35）。`origin/cloudflare` が祖先でなかった（work/1008-hou のマージで進んでいた）ため `git merge origin/cloudflare`（docs のみ、衝突なし）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1 確かめ

- #277: Open。着手中のコメントは NEN-01 のもの（同じセッション）だけ
- 未マージの `work/` ブランチ: `origin/work/1008-nen` だけ（NEN-02 の時の `work/1008-hou` はマージ済み）
- 「タイトル戦」タブ（30行）の slug に `timeline` は無く、名前に timeline を含む大会も無い
- 生成と同じ経路（`fetch_tab`・`load_taikai`・`load_periods`）で読んだ: 表示する大会 20、期 363（範囲内）。年の無い期は0
- リーチ麻雀世界選手権 第1回は `2014-XX-XX`、第2回は `2017-XX-XX` が入っていた（年表では 2014年・2017年に並ぶ）

### 手順2 作る

- テストの扱い: `test_title_ogp.test_every_taikai_has_image` は、大会の一覧がシートを読む生成時にしか得られないため、前提の2つ目の案（定数）にした。`generate_title_pages.py` に `NON_TAIKAI_DIRS = ("timeline",)` を置き、テストは title/ 直下のディレクトリからそれを除く。生成は大会の slug が `NON_TAIKAI_DIRS` と重なれば止める。「置いた画像が実在の大会に当たる」の検査は変えていない
- `_redirects`: `/title/timeline  /title/timeline/  301` の1行だけ足した（大会の行の並び、teiou と wakajishi の間）。先頭行は触っていない。直上のコメント「大会ページの…」は1行の追加に収めるため変えず、`docs/notes/title-pages.md`「URL の解決」に大会でないディレクトリがあることと `NON_TAIKAI_DIRS` を書いた
- 生成（`scripts/generate_title_pages.py`）:
  - `title/timeline/index.html` を書き出す。`NOINDEX_TAG`（`<meta name="robots" content="noindex">`）を付け、`sitemap-title.xml` の URL には入れない。`<title>`・og:title「年表 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」、h1「年表」（visually-hidden）、パンくず「タイトル戦 > 年表」、og:image は共通の `img/ogp.png`。canonical は他の title/ のページと同じく自分の URL
  - 年は `period_year()`（検索の年と同じ）。新しい年が上、年の中は大会の表示順、同じ大会は新しい期から。年の無い期は末尾の「年不明」（今は0件で出ない）
  - 見出し「2025年（25）」の件数は期の数（第26期王位戦の2名は1期）
  - 3つの形を全部焼き込み、ラジオボタンで1つだけ見せる（`:has(#tl-a1:checked)` など、JS なし）。A1 は入口と同じ `photo_card_html()`（帯は期の表記「第42期鳳凰戦」、押すと期ページ）。A2 は写真（最小幅96px のグリッド）の下に期の表記と優勝者、押すと期ページ。A3 は大会名・期（期ページへのリンク）・優勝者（プロ一覧／X／リンクなし）
  - 色分けは li に `is-houou`・`is-ouka`・`is-other`（slug の houou・ouka、ほかはその他）を付け、B1〜B3 で CSS 変数を差し替える。B3 の写真カードは既存の帯の変数 `--mj-title-band-bg` を上書きする
  - 固定バーの2段目に年ジャンプ（`.mj-tl-years`）。高さの仕組み（`assets/title.js` がバー全体の `offsetHeight` を `--mj-title-filter-h` に入れる）は変えずに済んだ。今見ている年の強調は `assets/title.js` に足した（スクロールのたびに、見出しがバーの下端を越えた最後の年に `aria-current`。年の並びだけを横に動かし、ページは動かさない）。最後の年（1973）も見出しをバーの下まで上げられるよう、最後の節に最小の高さを付けた
  - `photo_card_html()` に li のクラスを足す引数、`filterbar_html()`・`page_html()` にバーの2段目の引数を足した（既定は空で、既存のページの出力は変わらない）
- 生成物の差分: `title/timeline/index.html`（425,763 バイト）の追加のほかに、シートの変化（世界選手権の年の入力）による3ファイル: `title/wrc/1.html`・`title/wrc/2.html`（パンくずの対局日「（2014年）」「（2017年）」）、`title/search.json`（2期の年が空 → 2014・2017。ほかは同じ）。`sitemap-title.xml` は変わらない。シートの変化分は別のコミットにした
- `python3 -m unittest discover -s scripts/tests`: 644件 OK
- #277 に試作の着手をコメントした（issuecomment-6051052004）

### 手順3 確かめ

Workers Builds（39bc5de3）は success。プレビュー（URL は最終報告）の `/title/timeline/` は 200 で、手元の生成物と同じ HTML。`/title/timeline` は `/title/timeline/` へ 301。robots は noindex。
headless Chromium（Playwright）で、まず手元（`python3 -m http.server`）で、次にプレビューで同じ確かめをした（スクリーンショットはセッションの作業フォルダ。ログには置かない）。

| 項目 | スマホ 375×740 | PC 1280×800 | PC 1920×1080 |
|---|---|---|---|
| 固定バーの高さ（実測 / `--mj-title-filter-h`） | 162px / 162px | 108px / 108px | 108px / 108px |
| navbar + バー（本文の上の余白） | 218px | 188px | 188px |
| 横スクロール（ページ全体） | なし | なし | なし |
| A1〜A3 × B0〜B3 の12通り | 選んだ形だけ表示 | 同 | 同 |
| 年ジャンプ（1990・2014・2025・1973 を押す） | 見出しがバーの下端に揃い、押した年が強調され、年の並びの中で見える | 同 | 同 |
| `#y2000` を付けて直接開く | 2000 の見出しがバーの下端、2000 が強調 | 同 | 同 |
| 写真の遅延読み込み（開いた直後に読まれた写真 / 364枚） | 134 | 44 | 126 |

- 写真は 364枚（363期、第26期王位戦が2枚）。A1 の写真カードは `loading="lazy"`、A2 の写真も同じ。A2・A3 を選んでいる間、A1 の写真は `display: none` のため読まれない
- 読み込みのエラーは各回1件（`ERR_TUNNEL_CONNECTION_FAILED`。セッションのプロキシで外部の画像1件が拒否されたもの。代替アバターに差し替わる）。JS のエラーは無い
- スマホでは固定バーが3段（プルダウン・検索・年）で 218px（画面の約3割）。他の title/ のページより年の段（約54px）だけ高い

コントラスト比（WCAG 2.x の相対輝度の式）:

| 前景 | 背景 | 比 | 基準 |
|---|---|---|---|
| 文字 #212529 | 白 / B2 鳳凰 #fbf1d9 / 桜花 #fdeaf1 / その他 #f2f2f2 | 15.43 / 13.73 / 13.38 / 13.78 | AAA 7:1 |
| リンク #14459b | 白 / #fbf1d9 / #fdeaf1 / #f2f2f2 | 8.92 / 7.94 / 7.74 / 7.97 | AAA 7:1 |
| 白字（B3 の帯・印） | 鳳凰 #6b4800 / 桜花 #8e1244 / その他 黒75%（白の上で #404040） | 8.23 / 9.05 / 10.37 | AAA 7:1（写真の上の帯は写真で変わるが、黒75%の既存の帯と同じ考え方） |
| B1 の線（文字でない） | 白に対し 鳳凰 #a87400 / 桜花 #c2185b / その他 #6c757d | 4.07 / 5.87 / 4.69 | 非テキスト 3:1 |
| 年ジャンプ #a8adb3 / 強調 #131316 | #131316 / #f5f5f5 | 8.21 / 17.01 | AAA 7:1 |

## 報告

- 状態: 判断待ち
- ブランチ: work/1008-nen
- ログ: https://github.com/retroeater/mj/blob/work/1008-nen/docs/logs/CHAT-1008-NEN-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-nen
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ `/title/timeline/`
- マージ: 未（平野さんの判断待ち。この指示ではマージしない）
- issue: #277
- 判断が必要なこと:
  - 平野さんが決める2点: 表示の形（A1 写真カード / A2 小さい写真 / A3 文字だけ）と色分け（B0 なし / B1 左端の線 / B2 背景 / B3 帯・印）
  - 見てもらう手順: 確認用 URL の `/title/timeline/` を開く → 上の枠の「表示の形」で A1 → A2 → A3、それぞれで「色分け」B0 → B1 → B2 → B3 を押す（2026年の先頭が鳳凰戦。女流桜花は 2026年に無く、2025年の第20期で見る）→ 固定バーの年で 2025・1973（最下部、鳳凰戦だけの年）・2014（世界選手権 第1回）を押し、強調と移動を見る。スマホと PC の両方で
  - 気になる点（決めてほしい）: スマホでは固定バーが3段で画面の約3割（218px）になる。年の段を残すか、スマホだけ年の段を別の形（例: プルダウン）にするか
  - 本実装で消す・足すもの: 比較のラジオボタン（`timeline_compare_html()`・`TIMELINE_FORMS`・`TIMELINE_COLORS`・`.mj-tl-compare`）、採らない形の焼き込み（A1〜A3 のうち2つの HTML と CSS）と採らない色の CSS（B1〜B3 のうち採らないもの）、og:image `img/ogp/title/timeline-black.png`（文字「年表」、`og_image_for(TIMELINE_SLUG, …)`。入れると `test_title_ogp` の「置いた画像が実在」に当たるので確かめる）、テスト（年の並び・優勝者のリンク・noindex と sitemap に載せないことの単体テスト）、`docs/notes/title-pages.md`（年表の節）、`docs/notes/static-generation.md`「ページの一覧」、公開の issue の起票（docs/new-page-checklist.md 段1）
- 未確認の項目:
  - 実機（iPhone の Safari など）での見え方・横スクロールの操作感（headless Chromium だけで確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5b12292）: https://github.com/retroeater/mj-logs/tree/main/guide/c5b12292

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5b12292/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cb2ba7f5.md
