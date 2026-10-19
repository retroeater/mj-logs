# CHAT-1019-EXM-06

- 着手日時: 2026-10-19
- 対象issue: #613
- ブランチ: work/1019-exm
- 着手時HEAD: 77431408e

## 指示

【Claude作成】Claude Code 向け指示：/exam/ を公開する（#613。メニュー「番組」へ移す・jpml_test.html を /exam/ へ 301・旧ページの生成をやめる） Chat-Ref: CHAT-1019-EXM-06 マージ: 承認済み（チャットで。2026-10-19 に平野さんが「確かめが全部通ったらマージして公開まで済ませてよい」と答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-exm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-exm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-05 で未公開のまま本番に入れた `/exam/`（プロテストの動画12本）を、`docs/new-page-checklist.md` の段2のとおり公開し、旧「連盟 > プロテスト」（`jpml_test.html`）を置き換える。
決定（2026-10-19、平野さん）

* 公開は今すぐ行う
* メニューは「連盟」から「プロテスト」を外し、「番組」の「Mつく」の下（女流勉強会の公開後は、その下）・「English」の上に置く。つまり今は 放送対局・帰り道・Mつく・プロテスト・English の順。女流勉強会（#576）は公開のとき「Mつく」と「プロテスト」の間に入る
* 旧 `jpml_test.html` は廃止して `/exam/` へ 301（2026-10-19 の決定のとおり。連盟サイトの記事22件は載せない）。`?name=` は引き継がない（`/exam/` は `?name=` を読まない。転送は一覧へ）
* 確かめが全部通れば、マージして公開まで済ませてよい

前提（チャット側。平野さんの決定ではない）

* 2026-10-19 にチャット側が読んだ本番: `/exam/` は 200・noindex・題「プロテスト | 番組 | ryoei.pro」・12本。`navbar.js` の「連盟」に `/jpml_test.html` の「プロテスト」、「番組」に 放送対局・帰り道・Mつく（`/mtsuku/`）・English（`/video_en.html`）（origin/cloudflare の `navbar.js` で確かめた。要確認）
* 公開の作業の形は Mつくの公開（#570。`docs/notes/video-mtsuku.md` の冒頭: navbar・`sitemap-mtsuku.xml` を `sitemap.xml` から参照・`llms.txt`、旧生成スクリプトを消して旧 URL を 301）に倣う。#613 の本文の段2のチェックリストも見る
* 旧ページの後片付け: `generate_jpml_test.py`・`jpml_test.html`・それだけが使う JS・テスト・`regenerate.py` の登録・ほかの検査（title 系の検査・`check_unused_names.py`・SNS ブックの利用先など、`jpml_test` を名指しする所）を、参照を洗い出して外す。共有の `table.js` など、ほかのページも使うものは残す（要確認）。旧ページが読んでいたスプレッドシートのシート（`generate_jpml_test.SPREADSHEET_ID` のブックの、記事と動画の行があるシート）を、ほかに読む所が無くなるかを報告に書く（シートは消さない。消すかは平野さんが決める）
* 転送は `docs/notes/redirects.md`（転送の一覧、RDR）にも載せる（要確認。一覧の形に合わせる）
* 公開後に平野さんが行う手作業は、Mつくの公開と同じ形（Search Console の `sitemap.xml` の再送信、`/exam/` の URL 検査とインデックス登録のリクエスト、スマホの実機でメニューから開く）を見込む

手順

1. #613 に着手中コメントを書く（他セッションの着手中コメントがあれば止まる）。`git branch -r --no-merged origin/cloudflare` の各ブランチが、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・`scripts/regenerate.py` の同じ行を変えている、または取り込みで衝突するかを確かめる（同じファイルの別の箇所なら止まらずログに書く）。旧ページの参照を全部洗い出して `## 経過` に表で書く
2. 公開と後片付けを行う: `exam/` の noindex を外す（生成スクリプトの定数の1か所）、`navbar.js` を上の決定の並びに直す、`/exam/` のサイトマップを足して `sitemap.xml` から参照、`llms.txt`、`_redirects` に `/jpml_test.html` → `/exam/` の 301（クエリは引き継がない形。Cloudflare の `_redirects` の既定の動きを確かめて書く）、旧ページの生成スクリプト・ページ・専用の JS・テスト・登録を消す、`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/video-exam.md`・`docs/notes/redirects.md`・`docs/decisions/exam.md`（この指示の決定）・#576 の本文の段2（女流勉強会の公開時の位置が「Mつく」と「プロテスト」の間になること）を直す
3. 確かめてマージする: テスト、`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「`exam/` の noindex の行」「navbar を描く全ページの変化があればその種類」「サイトマップ・`llms.txt`・`_redirects`」「消した旧ページ」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる、容量の検査、work/ ブランチのプレビューで PC とスマホ幅のメニュー（連盟・番組を開いた状態）と `/jpml_test.html` と `/jpml_test.html?name=部谷幸則` の転送先を確かめて画像を `docs/logs/CHAT-1019-EXM-06/` に置く。通ればマージし（docs/notes/branch-operations.md「マージするとき」）、本番で `/exam/` が 200・noindex なし、`/jpml_test.html` が `/exam/` へ 301、メニューの並び、`sitemap.xml` からの参照を確かめる。#613 に結果と平野さんの手作業（上の前提の3つ）を書く。#613 はクローズせず「状況: 待ち」にして、平野さんの手作業の報告を待つ

止まる条件

* 他セッションの着手中コメントがある、未マージの work/ ブランチと同じ行を変えている・取り込みで衝突する
* 旧ページの参照の中に、消すと別のページ・検査の動きが変わるもの（共有の JS・関数など）があり、残すか消すかを決められない（表を書いて止まる）
* `regenerate.py all` の差分に、上の種類で説明できない変化がある
* テスト・容量の検査が通らない、プレビューでメニューや転送が決定のとおりでない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら原因を書いて止まる。無関係な失敗なら原因を報告に書いたうえで、本番の確かめと #613 への記録を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-06` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカル・リモートの `work/1019-exm` は cloudflare にマージ済み（祖先）。docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」のとおり `git merge --ff-only origin/cloudflare` で進めた（77431408e）
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- #613 に他セッションのコメントは無かった。着手中コメントを書いた

### 手順1: 未マージのブランチと旧ページの参照

- 未マージのブランチ（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-log-367-migrate）のうち、この作業のファイルを変えているのは work/1017-lvp の `navbar.js` だけ（93行目付近の `newNavCurrent` の分岐に live/ を足す）。こちらは35行目（連盟のプロテスト）と49行目（番組）で、別の箇所
- 前提の確かめ: origin/cloudflare の `navbar.js` は「連盟」にプロ・タイトル戦・プロテスト（`/jpml_test.html`）・リンク、「番組」に放送対局・帰り道・Mつく・English。本番の `/exam/` は 200・noindex・題「プロテスト | 番組 | ryoei.pro」・12本（EXM-05 で確かめた）
- `jpml_test` の参照（`git grep`、docs/logs を除く）と扱い:

  | 参照 | 種類 | 扱い |
  |---|---|---|
  | `jpml_test.html` | 旧ページ | 消した |
  | `scripts/generate_jpml_test.py` | 旧ページの生成（`regenerate.py` はファイルの有無で対象を決めるので、登録は消すだけで外れる） | 消した |
  | `scripts/tests/test_jpml_test.py` | 旧生成のテスト | 消した |
  | `scripts/tests/test_title_check.py` の `JpmlTestCheckTest` | 旧生成の `--check` のテストと、共有の `lib/page.generate(write=False)` のテスト | 前者を消し、後者は旧ページの設定を使わずに同じ内容で残した（`PageGenerateTest`） |
  | `scripts/check_unused_names.py`（import・「テスト」シートの読み取り・`NAME_RESOLVERS` の `generate_jpml_test.load_photos`） | 「使われていない登録」の利用先 | 外した（プロテストの出演者は EXM-01 から `lib.exam.resolve_people` で数えている） |
  | `scripts/tests/test_name_resolvers.py` のコメント | 説明 | 「title/・jpml_test」→「title/」 |
  | `navbar.js` の「連盟」の「プロテスト」 | メニュー | 外し、「番組」の Mつく の下に `/exam/` を足した |
  | `llms.txt` の「プロテスト」 | 索引 | `/exam/` の行に差し替え（「番組」の節へ） |
  | `sitemap-pages.xml` の `jpml_test.html` | サイトマップ | 消した |
  | `table.js`・`style.css` のコメント、`lib/page.py`・`generate_video_live.py` などのコメントの「jpml_test の前例」 | 共有の JS・CSS・説明 | 残した（ほかのページも使う。コメントは当時の記録） |
  | docs（`static-generation.md` の表とページ名の列挙・`site-findings.md`・`lighthouse-baseline.md`・`handover-archive-2026.md`・`sns-book.md`・docs/gsc の CSV） | 記録 | `static-generation.md` の「ページの一覧」と名前の照合の列挙だけ直した。ほかは当時の記録として残した |
  | `.github/workflows/` | — | 名指しは無い |

- 旧ページが読んでいたシート: ブック `1y8xBxGp…`（`generate_resource_logs.py`・`generate_video_en.py` も同じブックの別のシートを読む）の「テスト」シート。**「テスト」シートを読む所は無くなった**（シートは消していない）

### 手順2: 公開と後片付け

- `lib/exam.py` の `NOINDEX_TAG` と、一覧・各話の生成でそれを読む2か所を消した（Mつくの公開と同じく定数ごと外した）。`exam/` の13ファイルは noindex の行が消えただけ
- `navbar.js`: 「連盟」はプロ・タイトル戦・リンク、「番組」は 放送対局・帰り道・Mつく・プロテスト（`/exam/`）・English
- サイトマップ: `generate_exam_episodes.py` が `sitemap-exam.xml`（一覧が先頭、続いて各話の13 URL）を書く（Mつくの `build_sitemap()` と同じ形、`--check` でも組み立てる）。`sitemap.xml` から参照。`update_sitemap_lastmod.py` に `exam/` → `sitemap-exam.xml` の振り分けと同期の対象を足した。`regenerate-page.yml` の XML の確かめの一覧に `sitemap-exam.xml` を足した
- `llms.txt`: 「日本プロ麻雀連盟」の節の `jpml_test.html` の行を消し、「番組」の節の Mつく の下に `/exam/` の行を足した。末尾のサイトマップの説明にも `sitemap-exam.xml` を足した
- `_redirects`: `/jpml_test.html  /exam/  301`。**Cloudflare の `_redirects` はクエリを転送先にそのまま付ける**（本番の `/video_mtsuku.html?name=abc` → `/mtsuku/?name=abc`、`/jpml_titles.html?name=abc` → `/title/?name=abc` で確かめた。公式の文書には書かれていない）。
  そのため `/jpml_test.html?name=部谷幸則` は `/exam/?name=部谷幸則` へ転送されるが、`/exam/` は `?name=` を読まないので、検索欄は空のまま12本すべてが出る（プレビューの headless Chromium で確かめた）。決定の「`?name=` は引き継がない（転送は一覧へ）」はこの形で満たしたものとした
- 文書: `docs/notes/static-generation.md`「ページの一覧」（HTML 24→23ページ、型A・2列/3列 6→5、`exam/` の行を公開に。冒頭の段落の列挙に `exam/` を入れた〈EXM-01 で別の行に置いていたもの。work/WRR はマージ済み〉）・`docs/notes/video-exam.md`・`docs/notes/redirects.md`（棚卸しの対象に `/jpml_test.html` の行、末尾スラッシュの行に `/exam`）・`docs/handover.md` の関連文書の行・`docs/decisions/exam.md`
- #576 の本文の「メニューの位置」と段2のチェックリストの navbar の行を「Mつく」と「プロテスト」の間に直した（REST で部分置換。直前の `updated_at` が取得時と同じことを確かめた）
- テスト: `test_exam.py` の noindex の確かめを「無い」に変え、`sitemap-exam.xml` のテストを足した
- コミット: 3fb048f0b は `git add` が消したパスで止まり、`git rm` で済ませていた旧ページ・生成・テストの削除だけが入った（件名は全体の内容のまま）。残りは e40840212

### 手順3: 確かめ

- `python3 -m unittest discover -s scripts/tests`: 1441件 OK
- `python3 scripts/regenerate.py all` を作業ブランチ（e40840212 の後）と cloudflare（77431408e）のクローンで回した。どちらも終了コード 0、どちらの木も HEAD から変わったファイルは0（データの変化なし）。
  2つの木の違い（`diff -rq`、docs・scripts・data を除く）: `exam/` の13ファイル（noindex の行だけ）・`navbar.js`・`llms.txt`・`_redirects`・`sitemap.xml`・`sitemap-pages.xml`・`sitemap-exam.xml`（作業ブランチだけ）・`jpml_test.html`（cloudflare だけ）・`.github/workflows/regenerate-page.yml`。navbar はページに焼き込まれない（各ページが `navbar.js` を読む）ので、ほかのページの HTML は変わらない
- 容量（`scripts/check_asset_limits.py`）: 配信ファイル数 4,195（cloudflare と同じ。`sitemap-exam.xml` が増え `jpml_test.html` が減った）・`_redirects` 静的 115 → 116・最大のファイルは変わらず、どれも OK。最上位の許可リストの検査も通った。ガイド文書は handover.md 22,956・CLAUDE.md 24,883 バイト（警告域未満）
- ワークフローの手動実行: `regenerate-page.yml` を作業ブランチで `target_page=exam_episodes` で起動（run 39242849915）、success。`sitemap-exam.xml` を含む XML の確かめが通り、生成物は「変更なし」
- `git merge-tree`: 未マージの全ブランチ6本と、cloudflare との組より増えた衝突は0
- プレビュー（e40840212 の「Workers Builds: mj」success）: `/exam/` 200・noindex なし、`sitemap.xml` が `sitemap-exam.xml` を参照し、`sitemap-exam.xml` は 200、`/jpml_test.html` → `/exam/` の 301、`/jpml_test.html?name=部谷幸則` → `/exam/?name=部谷幸則` で12本すべて表示。
  メニュー（PC・スマホ、headless Chromium）: 連盟 = プロ・タイトル戦・リンク、番組 = 放送対局・帰り道・Mつく・プロテスト・English。画像は `docs/logs/CHAT-1019-EXM-06/`（`menu-jpml-*`・`menu-program-*`・`redirect-name-*`、PC とスマホ）

## 報告

- 状態: 作業中
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし
- マージ: 未
- issue: #613
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a32f2ba9）: https://github.com/retroeater/mj-logs/tree/main/guide/a32f2ba9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a32f2ba9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
