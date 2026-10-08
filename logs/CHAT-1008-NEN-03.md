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

## 報告

- 状態: 作業中
- ブランチ: work/1008-nen
- ログ: https://github.com/retroeater/mj/blob/work/1008-nen/docs/logs/CHAT-1008-NEN-03.md
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
