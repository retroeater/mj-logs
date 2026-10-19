# CHAT-1019-EXM-01

- 着手日時: 2026-10-19
- 対象issue: #612（作る）・#613（公開）
- ブランチ: work/1019-exm
- 着手時HEAD: 10e55a48e612baaa82e26b6597ff251a2e32e085

## 指示

【Claude作成】Claude Code 向け指示：「連盟 > プロテスト」の動画12本を、番組の形（帰り道・女流勉強会と同じ）の新ページ /exam/ に作り直す（自律開発。未公開のプレビューまで） Chat-Ref: CHAT-1019-EXM-01 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-exm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-exm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
`jpml_test.html`（メニュー「連盟 > プロテスト」）のうち YouTube の動画だけを、帰り道（`wayhome/`）・Mつく（`mtsuku/`）・女流勉強会（`study/`）と同じ形（動画が主役の一覧＋各話ページ、出演者の写真カードとリンク）の新ページ `/exam/` に作り直す。 平野さんの「自律開発」の手続き（事前の質問は済み → 最後まで作る → 不明点は仮置きして進め、プレビューと一緒にまとめて確認）で進める。公開（noindex を外す・メニューの移動・301・サイトマップ・`llms.txt`）はこの指示に含めない。
決定（2026-10-19、平野さん）

* 移行の対象は動画だけ。連盟サイト（ma-jan.or.jp）の記事は新ページに載せない
* デザインは「番組」に合わせる（動画が主役、出演者などのリンク）
* 旧 `jpml_test.html` は、新ページの公開のときに廃止して `/exam/` へ 301 する（記事は見られなくなる。それは受け入れる）。301 とメニューの移動は公開の別の指示で行う
* URL は `/exam/`
* 帰り道・女流勉強会と同じく、動画1本ごとの各話ページも作る
* データは動画のブック（帰り道・Mつく・女流勉強会と同じブック、`lib/video_book.py`）に新しいタブを作って持つ。タブは平野さんが作る

前提（チャット側。平野さんの決定ではない）

* 本番の `jpml_test.html`（2026-10-19 にチャット側が読んだ）は34行で、YouTube 12行・ma-jan.or.jp 22行（要確認。生成と同じ経路のデータで数え直す）。動画の12行は 2026-06-30〜2016-08-08 の公開日。出演者の欄は「本田朋広（28期）、部谷幸則（31期）」の形で、2行（2021-07-31「…受験される方へ【ルール・マナーなど】」・2016-08-08「第33期プロテスト募集受付開始！」）は「-」
* 本番の navbar の「連盟」は プロ・タイトル戦・プロテスト・リンク、「番組」は 放送対局・帰り道・Mつく・English（2026-10-19 にチャット側が読んだ）。女流勉強会は未公開（#576）で、公開時は「Mつく」の下に入る決定（docs/decisions/page-release.md・study.md）
* 型は女流勉強会（`docs/notes/video-study.md`・docs/decisions/study.md）にいちばん近いとみる: 一覧（最新話のヒーロー・エピソードのカード・検索）＋各話ページ（サムネイル・再生のリンク・前後のエピソード・人の写真カード）。共通にできる部品は既存の形を使い、新しい依存を足さない（CLAUDE.md「コード規約」）
* タブができるまでの扱いは、女流勉強会で使った「仮の TSV」の前例（`docs/notes/video-study.md`。タブへ移ったら仮のファイルと書き出しのスクリプトを消した）に倣ってよい。仮の TSV を置くなら、外すトリガ（平野さんがタブを作り、生成の経路で読めた時点で消す）を docs に書く
* 以下はチャット側の仮置きの案（実物に合わせて変えてよく、変えたものも含めて確認の一覧に出す）:
   * 一覧の題は「プロテスト | 番組 | ryoei.pro」、大見出しの英語名は「Pro Test」（仮）
   * 各話の見出しは動画のタイトル（女流勉強会の「part73」のような回の番号が無いため）
   * 人の節は「出演者」の1つにまとめる（部谷幸則さんは多くの回に出るが MC かは未確認なので分けない）。期（「28期」）は名前の下などに出すか、出さないかを決めて確認に出す
   * 出演者が「-」の回は人の節を出さない
   * 名前は名前の辞書の「別名」を通して現在名にする（女流勉強会と同じ）。写真で止める規則は帰り道・Mつく・女流勉強会と同じ
   * 新しい回の検知は作らない（タブに足せば週次で反映。Mつくと同じ）
   * タイトル・公開日・再生時間を層1（`data/live_channel_raw.jsonl`）から引けるかは要確認（女流勉強会の part1〜33 は層1に無かった）。引けない回があれば、タブに持つ列を決めて確認に出す

手順

1. 下を確かめ、ログの `## 経過` に書く。食い違えば止まる。
   * `jpml_test`・プロテスト・`/exam/` の issue（クローズ済み・コメントを含む）。同じ作業の open issue があれば止まる。無ければ、作成の issue と公開（301・メニューの移動・サイトマップ・`llms.txt`）の issue を対で起票する（Mつくの #569・#570 の形。親・ラベルは既存の対に合わせ、案のラベルが無ければ付けずに報告に書く）。作成の issue に着手中コメントを書く
   * `git branch -r --no-merged origin/cloudflare` の各ブランチが、この作業で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突するか（同じファイルの別の箇所なら止まらずログに書く）。特に `lib/video_book.py`・帰り道・Mつく・女流勉強会の共通部品・navbar
   * 今の `generate_jpml_test.py` が読むデータ（生成と同じ経路で読む）の行数と、YouTube の行数・名前の一覧（動画ID・公開日・タイトル・出演者を全件で）。前提の「34行・YouTube 12行」と違えば、件数を書いて止まる
2. `/exam/` の一覧と各話ページを作る（未公開: noindex、navbar・サイトマップ・`llms.txt` に載せない。`docs/new-page-checklist.md` の公開前までの項目）。タブの見出しの案（列名と並び）を決め、平野さんがタブに貼る TSV（12行）を `docs/logs/CHAT-1019-EXM-01/` に置く。テストを足し、`docs/notes/` にこのページの文書を（女流勉強会の `video-study.md` に倣って）作る。決定は docs/decisions/ の分野のファイル（新しく作るなら名前を報告に書く）に足す。旧 `jpml_test.html` には触れない（生成もそのまま）
3. 確かめ: `python3 -m unittest discover -s scripts/tests`、`python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、差分が `/exam/` の新しいファイルと、この作業で説明できるもの（共通部品を直したならその影響）だけであることを確かめる（ほかのページの差分は種類ごとに全部書く）。容量の検査（`check-assets.yml` と同じもの）を手元で回し、増えた量を報告する。work/ ブランチのプレビュー（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」）で、一覧・各話（出演者の多い回・出演者「-」の回・いちばん古い回・最新の回）を headless Chrome で PC とスマホ幅で見て、画像を `docs/logs/CHAT-1019-EXM-01/` に置く

止まる条件

* 手順1の前提が食い違う、同じ作業の open issue がある、他のセッションの着手中コメントがある
* 生成と同じ経路でデータが読めない、YouTube の行数が12でない（件数と一覧を書いて止まる）
* 未マージの work/ ブランチが変えるファイルの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* `regenerate.py all` の差分に、`/exam/` と共通部品の直しで説明できないページの変化がある
* 開発中の不明点では止まらない。仮置きして進め、「判断が必要なこと」に、仮置きした内容・選んだ理由・ほかの案（メリット・デメリットと推奨）をまとめて書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、平野さんに頼む手作業（タブの作成と TSV の貼り付け: どのセルから貼るか・貼った後の行数）を1列1行で書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow origin` の後、`git log --all --grep=EXM` と `docs/logs/` の履歴に EXM は無い
- 作業ブランチ: ローカル・リモートとも `work/1019-exm` は無く、`git checkout -b work/1019-exm origin/cloudflare` で作成
- 0章: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1の確かめ

- issue: 全 issue（603件、#611 まで）と全コメント（2598件）を REST で取り、`jpml_test`・`プロテスト`・`/exam` で照合（検索 API はセッションで使えない）。
  当たったのは #579・#567・#552・#533・#524・#517・#486・#451・#328・#147・#145・#144・#108・#103・#9 とコメントのみで、どれも別の主題（写真の基準・フィルタの検知・列見出しなど）。同じ作業の open issue・着手中コメントは無い
- 起票: 作る #612・公開 #613（Mつくの #569・#570 の形。ラベルは #569・#570 に合わせ「分野: UI/UX」と、`video_mtsuku` に当たる「対象: jpml_test」。親は #569・#570 に無いので付けない）。#612 に着手中コメント
- 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）でこの作業が触るファイルを変えているもの:
  - `work/1017-lvp`: `scripts/lib/study.py`（メンバー限定の札を `lib/members.py` に移す）・`navbar.js`・`style.css`。study.py は読むだけで変えない（関数を import する）。navbar は触らない
  - `work/1019-ouk25`: `scripts/regenerate.py`（SHARED_GENERATORS の上のコメント）・`scripts/check_unused_names.py`（NAME_RESOLVERS の houou の行・page_names の import）・`docs/notes/static-generation.md`・`style.css`
  - `work/WRR`: `.github/workflows/check-assets.yml`（許可リストの行に `wrcr` を足す）・`_redirects`（jwrc の後と末尾）・`docs/notes/static-generation.md`（「ページの一覧」の冒頭の段落の行・表に行を足す）・`scripts/regenerate.py`（SHARED_GENERATORS・OUTPUT_OVERRIDES の jwrc の後）
  - 同じ行になるのは、WRR の `check-assets.yml` の許可リストの行と static-generation.md「ページの一覧」の冒頭の段落の行。**この2つは同じ行を変えず、別の行に足す**（許可リストは `exam` を別の case の行に、ページの一覧は段落の後に1行）。実装の後に3ブランチとの `git merge-tree` で衝突しないことを確かめる
- 旧ページのデータ（`generate_jpml_test.py` と同じ `lib/page.fetch_sheet()`・`SELECT A,F,G,H,I WHERE J = "Y"`）: 34行。YouTube 12行・ma-jan.or.jp 22行（前提と一致）。YouTube の12行:

  | 動画ID | 公開日（シート） | タイトル（シート） | 出演者（シート） |
  |---|---|---|---|
  | -PoeE0Bm2sE | 2026-06-30 | Mリーガー本田朋広プロにプロテストのことを聞きました！ | 本田朋広（28期）、部谷幸則（31期） |
  | eKMCpGQKTYk | 2026-01-05 | 日本プロ麻雀連盟プロテストを受けてみて | 内川幸太郎（22期）、部谷幸則（31期）、安本きらり（41期） |
  | Lp8m7sAWT_k | 2025-06-25 | 日本プロ麻雀連盟プロテストを受けてみて | 朝比奈ゆり（40期）、部谷幸則（31期）、三浦智博（28期） |
  | jal-jnnwJb4 | 2025-01-01 | 日本プロ麻雀連盟プロテストを受けてみて | 鴨舞（38期）、田中祐（37期）、部谷幸則（31期） |
  | RfZxTnSGnBw | 2024-07-02 | 日本プロ麻雀連盟プロテストを受けてみて | 曽篠春成（36期）、部谷幸則（31期）、御子柴佑梨（38期） |
  | lGPjKHDAlzw | 2023-12-05 | プロテストについて新人連盟員に聞いてみた | 内村翠（39期）、貝原香（35期）、部谷幸則（31期）、林源（38期） |
  | 98GKK3KKYNk | 2021-07-31 | 日本プロ麻雀連盟のプロテストを受験される方へ【ルール・マナーなど】 | - |
  | ix9xGkF5pJs | 2018-02-05 | 第34期後期プロテスト募集受付中！ | 東城りお（29期） |
  | gyYpx9xfrLA | 2017-08-12 | 第34期プロテスト募集受付中！－職業 麻雀プロ編－ | 山脇千文美（29期） |
  | Esz7Mloebaw | 2017-08-12 | 第34期プロテスト募集受付中！－強くなりたい編－ | 山脇千文美（29期） |
  | JTOTffVFqTg | 2017-01-30 | 第33期後期プロテスト募集受付開始！ | 小笠原奈央（28期） |
  | kzKMbbNvAcQ | 2016-08-08 | 第33期プロテスト募集受付開始！ | - |

- 層1（`data/live_channel_raw.jsonl`）: 12本とも最新の行がある。タイトル・公開日時・長さがあり、配信開始日時は全部空（ライブではない）、メンバー限定 0本。
  層1のタイトルは「【2026年度前期プロテスト応募受付中！】Mリーガー本田朋広プロに…」のように【】の札が付く。
  **公開日の食い違い1件: `98GKK3KKYNk` はシート 2021-07-31、層1の公開日時 2021-07-21T11:26:24Z（JST 7月21日）**。ほかの11本はシートと層1（JST）が一致

### 実装（手順2）

- **タブ「プロテスト」は平野さんが既に作っていた**（動画のブックのタブ: Mつく・帰り道・女流勉強会・プロテスト。列は 動画ID・公開日・再生時間、12行。動画IDの並びは旧ページと同じ。公開日は旧「テスト」シートの値、再生時間は空。出演者の列は無い）。
  指示文の前提「タブができるまで（仮の TSV）」とは形が違うが、決定（タブは平野さんが作る）とは食い違わないため止まらず、次の形にした
  - 生成はタブを見出しの名前で読み、使う列は 動画ID・出演者だけ。公開日・再生時間は層1から引く（女流勉強会と同じ）。タブの「公開日」「再生時間」は読まない
  - タブに「出演者」の列が無い間は、出演者を `data/exam_provisional.tsv`（動画ID・出演者、12行）から読み警告を出す。タブと仮のファイルの動画IDが食い違えば止める。外すトリガは `docs/notes/video-exam.md`「構成」
  - 平野さんがタブに貼る出演者の列: `docs/logs/CHAT-1019-EXM-01/exam_cast_column.tsv`（見出し「出演者」＋12行の1列。タブの今の行の並びと同じ）
- 作ったもの: `scripts/lib/exam.py`・`scripts/generate_exam.py`（ページ名 `exam`）・`scripts/generate_exam_episodes.py`（`exam_episodes`）・`scripts/tests/test_exam.py`（11本）・`exam/index.html`＋各話12枚・`docs/notes/video-exam.md`・`docs/decisions/exam.md`（新しい分野のファイル）
- 直したもの: `scripts/regenerate.py`（OUTPUT_OVERRIDES に2行、各話が `generate_study_episodes.py` の `build_cast_html()` を使うので SHARED_GENERATORS に1つ）・`_redirects`（`/exam` の 301 と `/exam/` の 200）・`check-assets.yml`（許可リストに `exam`）・`style.css`（`.mj-study-en` の規則に `.mj-exam-en` を足す）・`scripts/check_unused_names.py`（利用先にプロテストの出演者）・`docs/notes/static-generation.md`「ページの一覧」・`docs/handover.md` の関連文書の表・`docs/decisions/README.md`
- 旧 `jpml_test.html` とその生成には触れていない。navbar・サイトマップ・`llms.txt` にも載せていない（noindex）
- `--check`: 出演者16人とも名前の辞書で引け、X の写真が全員読めた（代わりの絵になる人はいない）。止まる理由なし（警告は仮のファイルの1件）
- 未マージのブランチとの衝突を避けるための置き方（同じ行・隣の行を変えない）:
  - `check-assets.yml`: work/WRR が許可リストの行に `wrcr` を足すため、`exam` は別の case の行にした
  - `docs/notes/static-generation.md`: work/WRR が「ページの一覧」の冒頭の段落の行を変えるため、段落の後に「ほかに、プロテストの…がある」の1行を置いた（後で段落にまとめる）
  - `scripts/regenerate.py`: 最初は SHARED_GENERATORS の辞書の先頭に足したが、work/1019-ouk25 が隣の `ouka_pages` の行を変えていて `git merge-tree` で衝突した。辞書の後で `SHARED_GENERATORS["exam_episodes"] = ...` と足す形に直した（fec91bcbb）
  - 直した後、未マージの全ブランチ（8本）と `git merge-tree` を回し、cloudflare との組で出る衝突（各ブランチの docs/decisions など、元からのもの）より増えた衝突は0

### 確かめ（手順3）

- `python3 -m unittest discover -s scripts/tests`: 1441件 OK（fec91bcbb の後に再実行）
- `python3 scripts/regenerate.py all` を作業ブランチ（a97973c28）と cloudflare（10e55a48e）のクローンで同時に回した。どちらも終了コード 0
  - 2つの木の生成物の比較（`diff -rq`、docs・scripts・data を除く）: 違いは `exam/`（作業ブランチだけ）と手で直した `_redirects`・`style.css`・`check-assets.yml` だけ
  - 両方の木で同じ12ファイルが HEAD から変わった（データの変化。この作業と関係しない）。種類: (1) SNS ブックに X ID が入った人（平賀聡彦・坂本大志・三原孝博など）の写真カードが X へのリンクになる（`live/masters/24/b8-a.html`・`live/masters/24/f.html`・`mtsuku/` の6枚・`title/masters/24.html`・`title/nihon-series/2018.html`・`2020.html`）、(2) `jpml_pros.html` の1人の回数 14回→15回。作業ブランチではこの12ファイルを個別に戻した（コミットしない）
  - `exam/` は生成し直しても差分なし
- 容量（`scripts/check_asset_limits.py`、`check-assets.yml` と同じもの）: 配信ファイル数 4,023 → 4,036（+13）、`_redirects` 静的 110 → 112（+2）、最大のファイルは変わらず。`exam/` は 107,842 バイト。ガイド文書: handover.md 24,006 → 24,157 バイト（警告域 26KB 未満）、CLAUDE.md・chat-side-operations.md・instruction-template.md は変えていない。最上位の許可リストの検査をワークフローの手順のとおり手元で回して通った。work/ への push で走った「check」も success
- プレビュー: e0df62e03 の「Workers Builds: mj」が success。`/exam/`・各話が 200、`/exam` は `/exam/` へ 301。一覧のカード12枚、`style.css` に `.mj-exam-en` がある
- headless Chromium（Playwright）で PC（1280×800）とスマホ（390×844）の全体の画像を撮り、`docs/logs/CHAT-1019-EXM-01/` に置いた（JPEG、計 2.2MB）: 一覧 `list-*`・出演者の多い回 lGPjKHDAlzw `most-cast-*`・出演者「-」の回 98GKK3KKYNk `no-cast-*`・いちばん古い回 kzKMbbNvAcQ `oldest-*`（これも出演者「-」）・最新の回 -PoeE0Bm2sE `latest-*`。
  JS のエラー0、横のはみ出し無し（scrollWidth が画面の幅以下）

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `/exam/` と各話5枚（PC・スマホ）。この版の印: 一覧の大見出しが「プロテスト Pro Test」、カードの上の見出しが「動画（全12本）」
- マージ: 未（平野さんの判断待ち。プレビューを見て決める）
- issue: #612（作る。着手中コメント済み）・#613（公開・301・メニュー・サイトマップ・`llms.txt`）を起票。ラベルは「分野: UI/UX」「対象: jpml_test」、親なし（#569・#570 に合わせた）
- 判断が必要なこと:
  - **平野さんに頼む手作業（1つ）**:

    | 作業 | どこへ | 貼った後 |
    |---|---|---|
    | タブ「プロテスト」の D1 セルに `docs/logs/CHAT-1019-EXM-01/exam_cast_column.tsv` を貼る（見出し「出演者」＋12行の1列。行の並びはタブの今の並びと同じ新しい順） | 動画のブックのタブ「プロテスト」の D1 | 見出し1行＋12行（A〜D列）。D8 と D13 は「-」 |

    貼った後に生成がタブから読めたら、仮のファイル `data/exam_provisional.tsv` と読み替えのコードを消す（次の指示で。`docs/notes/video-exam.md`「構成」）
  - (1) タブの「公開日」「再生時間」の列（仮置き: 読まない。公開日・再生時間は層1から）。ほかの案: タブの公開日を正にする（手で直せるが、層1と二重になる）。推奨は今のまま（列は消しても残してもよい）
  - (2) 98GKK3KKYNk「…受験される方へ【ルール・マナーなど】」の公開日（仮置き: 層1〈YouTube〉の 2021年7月21日。旧ページとタブは 2021-07-31）。推奨は層1
  - (3) 見出し（仮置き: YouTube の題名から先頭の【】の札〈「【2025年度プロテスト応募受付中！】」など〉と【日本プロ麻雀連盟】を外す）。4本が「日本プロ麻雀連盟プロテストを受けてみて【プロ雀士にインタビュー】」で同じになる。ほかの案: (a) 先頭の札を残す（年度で見分けられるが長い）、(b) タブに「見出し」の列を足して手で書く（旧ページの題に近くできるが手間）。推奨は今のまま
  - (4) 英語名（仮置き: 「Pro Test」。指示文の案のまま）。ほかの案: 「Pro Exam」（URL の exam と合う）
  - (5) 期（「28期」）を出さない（仮置き。名前の辞書の現在名だけ）。ほかの案: カードの名前の下に出す（旧ページに近いが、タブに期を持つか「プロ」から引く手間）
  - (6) 人の節は「出演者」の1つ（部谷幸則さんも分けない。指示文の案のまま）。出演者「-」の回は節を出さず、カードの名前の行も出さない
  - (7) 言い方（仮置き）: 一覧「動画（全12本）」、ヒーローの札「最新」、前後「次の動画」「前の動画」（女流勉強会は「エピソード（全N回）」「最新話」「次のエピソード」）。回の番号が無い単発の動画のため。ほかの案: 女流勉強会にそろえる
  - (8) 紹介文（仮置き）: 「日本プロ麻雀連盟のプロテストの動画「<見出し>」。<名前>プロ・…が出演しています。」。一覧のヒーローの文「連盟のプロテストの案内と、受験したプロへのインタビューの動画。出演者の名前で振り返れます。」
  - (9) 一覧の題「プロテスト | 番組 | ryoei.pro」（指示文の案のまま）。メニューを「連盟」から「番組」へ移すかは公開（#613）で決める
  - (10) メンバー限定の回をタブに足すと止まる（仮置き。今の12本は0本で、札の表示を作っていない）
  - (11) 一覧は `?name=` を読まない（旧 `jpml_test.html?name=` からの 301 で検索欄に入れるかは #613 で決める）
- 未確認の項目:
  - 平野さんの実機での見え方
  - 出演者の列を足した後に、生成がタブから読めること（貼った後に `python3 scripts/generate_exam.py --check` で警告が消えるか）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d5b59e67）: https://github.com/retroeater/mj-logs/tree/main/guide/d5b59e67

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d5b59e67/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
