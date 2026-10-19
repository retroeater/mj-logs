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

## 報告

- 状態: 作業中
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 10e55a48）: https://github.com/retroeater/mj-logs/tree/main/guide/10e55a48

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/10e55a48/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
