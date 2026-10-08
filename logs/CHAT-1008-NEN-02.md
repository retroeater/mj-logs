# CHAT-1008-NEN-02

- 着手日時: 2026-10-08
- 対象issue: #277
- ブランチ: work/1008-nen
- 着手時HEAD: 0930d8d5

## 指示

【Claude作成】Claude Code 向け指示：#277 タイトル戦の年表 `/title/timeline/` の試作と比較ページ（表示の形3案 × 色分け数パターンをラジオボタンで切り替え）。未マージのままプレビューで見る Chat-Ref: CHAT-1008-NEN-02 マージ: 判断待ちで止まる（プレビューを見て平野さんが表示の形と色分けを決める。cloudflare へは入れない） 貼る時機: CHAT-1008-NEN-01 の完了の後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-nen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1008-nen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-nen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1008-NEN-01 のログの `## 報告` を読み、マージ済みでなければ何もせず止まる。docs/logs/CHAT-1008-NEN-01.md の `## 報告` の状態の末尾に `/ 続き: CHAT-1008-NEN-02` を足す（ログの節の書き換えは CLAUDE.md「作業ログ」節のとおり `## 指示` 欄より後ろの見出しを相手にする）。

目的
#277 の年表ページ `/title/timeline/` を、NEN-01 の決定（docs/decisions/title.md「2026-10-08（CHAT-1008-NEN-01）」）に沿って試作し、未決の2点（1期の表示の形・大会の色分け）を平野さんが実物で見比べて決められる比較ページにする。この指示ではマージしない。本実装（比較の仕組みを消す・og:image・テスト・文書）と、未公開でのマージ・公開の issue の起票は次の指示で行う。
決定（2026-10-08、平野さん）

* NEN-01 の grill の決定（docs/decisions/title.md の Q1〜Q10）のとおり作る。この指示で新しい決定はない

前提（チャット側。平野さんの決定ではない）

* NEN-01 の報告の見込み: `title/timeline/index.html` を `scripts/generate_title_pages.py` が書き出す。`regenerate.py` の `OUTPUT_OVERRIDES` は `"title/"` のままで足りる。`_redirects` の `/title/:slug/` の行でそのまま開ける。`title/search.json` は変わらない。実物と食い違えば実物に合わせ、報告に書く
* 未公開の段（docs/new-page-checklist.md「段1」）: `NOINDEX_TAG` の定数で noindex を付ける。`sitemap-title.xml`（`sitemap.xml` から参照済み）に timeline を載せない。navbar・`llms.txt`・既存のページからリンクしない。これらは次の指示（本実装）でも同じで、公開は別の issue
* 比較の仕組み: ページ内のラジオボタンで切り替える（URL パラメータではない。docs/notes/chat-side-operations.md「見た目の決め方」）。作り方は docs/notes/title-pages.md「帯と回戦ラベルの色」の TP-14〜TP-16 と同じ（`:has(#…:checked)` で CSS 変数・クラスを差し替え、JS なし・再読み込みなし）。形と色は別々のラジオボタンの組にし、組み合わせを全部作らない。試作のページ自体にラジオボタンを一時的に付ける形でよい（別ファイルにするなら noindex・どこからもリンクしない・sitemap に載せない）
* 表示の形（ラジオ A）: (A1) 写真カード（入口と同じ `.mj-title-holder*` の大きさ）(A2) 小さい写真のカード（例: 幅 80〜100px 程度。値は Code が決めてよい）(A3) 文字だけの行（期の表記・大会名・優勝者）。写真は `loading="lazy"`（363期分、要確認）。A3 では期の表記を期ページへのリンクにし、A1・A2 ではカード全体を期ページへのリンクにする（grill Q7）
* 色分け（ラジオ B）: 鳳凰戦・女流桜花・その他の3色を基本に、(B0) 色分けなし (B1) 行・カードの左端の線 (B2) 行・カードの背景 (B3) 帯（写真カードの大会名の帯の色。A3 では大会名の文字の前の印など、形に合う置き方）の3〜4パターン。色の値は `style.css` の既存の値（リンク色 `#14459b`、縞 `#f2f2f2` など）と合う範囲で Code が選び、文字のコントラスト比は WCAG 2.x の相対輝度の式で計算して報告に書く（.mj-table の文字は AAA〈7:1〉が基準。docs/handover.md「表の色とアクセシビリティ」）
* 年ジャンプ（grill Q9）: 固定バー（タイトル戦のプルダウン・検索欄の下か同じ段。`assets/title.js` が高さを `--mj-title-filter-h` に入れているので、年の帯の分も含めて狂わないように）に年を横に並べて横スクロール、年代（10年）の切れ目に区切り、今見ている年を強調（スクロールスパイ。`IntersectionObserver` などでよい。外部依存は増やさない）。アンカーは `#y2025`。各年の見出しは「2025年（25）」
* 優勝者のリンク（grill Q7）: 「プロ」タブにいる人は `jpml_pros.html?name=<名前>`、いなければ「連盟プロ以外」の X ID から X（新しいタブ）、それも無ければリンクなし。名前は「別名」を適用した登録名（`generate_title_pages.py` の既存の処理を使う）
* title・h1・パンくず（grill Q10）: `<title>`・og:title「年表 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」、h1「年表」（画面には出さず visually-hidden、他の title/ のページと同じ）、パンくず「タイトル戦 > 年表」。og:image はこの試作では共通の `img/ogp.png` のままでよい（`timeline-black.png` は本実装で作る）。`scripts/tests/test_title_ogp.py` が timeline を大会と取り違えないことを確かめる
* 年の無い2期（リーチ麻雀世界選手権 第1・2回）: 平野さんが「タイトル」タブに 2014年・2017年を入れる予定（NEN-01 の決定）。この指示では、入っていれば年に並べ、入っていなければ最下部に「年不明」の見出しでまとめて出して止まらず、報告に「入っていた／いなかった」を書く
* 共有ボタン（`scripts/lib/share.py`）は他の title/ のページと同じく置く

手順

1. 確かめる: #277 が Open で、他セッションの着手中コメントが NEN-01 のもの以外に無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*` 節を変えていない。「タイトル戦」タブに slug `timeline` の大会が無い。生成と同じ経路でシートを読み、表示する大会数と期数を報告する（止まる条件の範囲内であること）。年の無い期の数とその2期の年の有無
2. 作る: `generate_title_pages.py` に `title/timeline/index.html` の生成を足し（noindex、`sitemap-title.xml` に載せない）、上の形 A1〜A3 と色 B0〜B3 をラジオボタンで切り替える比較の仕組み、年ジャンプの固定バー、優勝者のリンク、共有ボタンを入れる。`python3 scripts/regenerate.py title_pages`（名前は `--list` で確かめる）で生成し、生成物の差分が `title/timeline/` の追加だけであること（他の title/ のページ・`sitemap-title.xml`・`title/search.json` が変わらないこと。シートの変化による差分が混ざれば種類と件数を報告）を確かめる。`python3 -m unittest discover -s scripts/tests` を通す。#277 に試作の着手をコメントする
3. 確かめる: Workers Builds のプレビュー（上限15分。走らなければ Codespace の確認用 URL か、headless Chrome のスクリーンショットをログに書かずターミナルに場所を書く）で、スマホ幅（360〜390px）・PC 幅・高さのある PC（1920×1080）の3つで、A1〜A3 × B0〜B3 の切り替え、年ジャンプ（横スクロール・強調・アンカー）、固定バーの高さ、写真の遅延読み込みを確かめ、結果を表でログに書く。コントラスト比の表も書く。平野さんに見てもらう手順（確認用 URL、どのラジオを順に押すか、見る年の例〈1973・2025・年不明〉）を報告に書く

止まる条件

* #277 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている
* 「タイトル戦」タブに slug `timeline`（または名前が `timeline` と重なるもの）がある
* 表示する大会が 20〜21、期が 363〜365 の範囲外（シートの変化。件数を書いて止まる）
* 生成物の差分に `title/timeline/` の追加以外が出た（シートの変化で説明できるものは止まらず報告。それ以外は止まる）
* テストが落ちる（timeline を足したことで落ちるなら、直さず報告して止まる）
* 固定バーの高さの仕組み（`assets/title.js`）を大きく変えないと年の帯が置けない（案を書いて止まる）
* （マージしない指示だが）cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが決める2点（形 A1〜A3・色 B0〜B3）と、見てもらう手順、本実装で消す・足すものの一覧（ラジオボタン・採らない案の CSS・og:image・テスト・docs/notes/title-pages.md・static-generation.md「ページの一覧」・公開の issue の起票）を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-NEN-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-NEN-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-01 の `## 報告` はマージ済み（0930d8d5）。NEN-01 の状態に「/ 続き: CHAT-1008-NEN-02」を足した。このセッションは NEN-01 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1008-NEN-02"` は0件
- ブランチ: ローカルの `work/1008-nen` は `origin/cloudflare` の祖先（マージ済み）のため、そのまま `git merge --ff-only origin/cloudflare`（Already up to date）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1 確かめ（途中）

- #277: Open。着手中のコメントは NEN-01 のもの（同じセッション）だけ
- 未マージの `work/` ブランチ: `origin/work/1008-hou`（`docs/logs/CHAT-1008-HOU-01.md` だけ）と `origin/work/1008-nen`。対象のファイルを変えていない
- `regenerate.py --list` の名前は `title_pages`

### テストの事前確認（止まる条件に当たった）

- 生成を書く前に、`title/timeline/` を仮に置いて（空の `index.html`）`python3 -m unittest discover -s scripts/tests` を流した。置かない状態では 644件 OK
- 置くと2件落ちる（仮のファイルは消した）:
  - `test_title_ogp.OgImageTest.test_every_taikai_has_image`: `title/` 直下のディレクトリをすべて大会とみなし、`img/ogp/title/timeline-black.png` が無いので落ちる（指示の前提の「timeline を大会と取り違えないか」の確かめで、取り違える）
  - `test_title_redirects.TitleRedirectsTest.test_each_taikai_has_trailing_slash_redirect`: `_redirects` に `/title/timeline  /title/timeline/  301` が無いので落ちる（#455 の規則。大会と同じく末尾スラッシュなしの 301 が要る）
- 止まる条件「テストが落ちる（timeline を足したことで落ちるなら、直さず報告して止まる）」に当たるため、生成の実装に入らず平野さんに聞いて止まる

## 報告

- 状態: 判断待ち（テストの扱いを平野さんに質問中） / 続き: CHAT-1008-NEN-03
- ブランチ: work/1008-nen
- ログ: https://github.com/retroeater/mj/blob/work/1008-nen/docs/logs/CHAT-1008-NEN-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-nen
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと: なし
- 未確認の項目: なし
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
