# CHAT-1016-JBK-09

- 着手日時: 2026-10-16
- 対象issue: #576（公開）
- ブランチ: work/JBK
- 着手時HEAD: f6fae4d33

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の公開の前に（メニュー「動画／Video」を「番組／Program」に改称、「動画」の文言の洗い出し、生徒の載せ方の確かめの仕組み） Chat-Ref: CHAT-1016-JBK-09 マージ: 判断待ちで止まる（メニューの見え方と、題などの置き換え案を平野さんが見てから決める） 貼る時機: いつでも（JBK のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/JBK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/JBK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-08 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-09` を足す（ログが cloudflare にあれば、この作業ブランチで直す）。

目的
女流勉強会の公開（#576）の前に、(A) メニューの「動画／Video」を「番組／Program」に改称し、題などに残る「動画」を洗い出して置き換え案を出す。(B) 生徒の名前を平野さんがタブに入れたとき、写真入りで載る人・名前だけの人が分かり、生成が止まらない形にする。
決定（2026-10-16、平野さん）

* 本番の `/study/`（未公開）を確かめた（OK）
* メニューの「動画／Video」を「番組／Program」に改称する
* 題・説明文などにある「動画」は、箇所の一覧と置き換え案を出させ、見てから決める（この指示ではメニューだけ変える）
* 生徒の名前は平野さんがタブ「女流勉強会」の「生徒」列に入れる（概要欄には無く、ウェブにも参加者の一覧は見当たらなかった）
* 生徒の載せ方の方針（予定）: 現役のプロとして同じ名前で活動している人だけ写真入りで載せ、ほかは名前だけ（代わりの絵）にする。どれくらい載せられるかを確かめてから決める
* 公開（#576）は、この指示の結果を見てから別の指示で行う

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* (A) メニュー: `navbar.js` の「動画」の項目の見出し（日本語「動画」・英語「Video」の2行の形、要確認）を「番組」「Program」にする。項目の中身（帰り道・Mつくなどのリンク）と並びは変えない。メニューを使う全ページに効く。メニューの見出しを文字で持つほかの場所（`llms.txt`・文書・テスト）があれば合わせる
* (A) 洗い出し: `git grep` で、ページの題（`<title>`・og:title の末尾の「| 日本プロ麻雀連盟の動画 |」など）・meta description・パンくず（`BreadcrumbList`）・見出し・説明文・`llms.txt` にある「動画」を洗い出し、箇所ごとに「今の文」「置き換え案」「変えない方がよい理由（あれば。例: YouTube の動画そのものを指す文）」を表にしてログに書く。置き換えはしない
* (B) 生徒の写真の扱い: 今の作り（JBK-03）は、生徒も講師と同じ写真の規則で、登録が無い人で生成を止める。これを、生徒に限り「『プロ』『連盟プロ以外』に登録があり、写真が読める人だけ写真入り。登録が無い・X ID が無い・写真が読めない人は止めずに名前だけ（代わりの絵）」に変える（講師・MC は今のまま止める規則）。「現役のプロとして同じ名前で活動している」かの判定は、登録の有無で代える（同姓同名の取り違えの恐れは報告に書く）
* (B) 確かめの出力: `regenerate.py --check` で、生徒について「写真入り」「名前だけ（理由: 登録なし／X ID なし／写真が読めない）」を回ごとに一覧で出す。今のタブは生徒が全回空なので、テストの仮データで出し方を確かめる
* `.github/workflows/` は変えない

手順

1. 未マージの work/ ブランチが `navbar.js`・`style.css` のメニューの箇所・女流勉強会の生成を変えていないかを `git branch -r --no-merged origin/cloudflare` の一覧から確かめる。(A) のメニューを直し、生成し直す（メニューを生成物に埋めているページがあれば、その差分がメニューの見出しだけであることを確かめる）
2. (A) の「動画」の洗い出しの表をログに書く。(B) を直し、テスト（生徒の写真入り・名前だけの各理由・講師は今までどおり止まる）を足し、`--check` の出力の見本をログに書く
3. プレビュー（確認用 URL）で、メニューの「番組／Program」を PC 幅と 375px で見て所見を書く。決定を `docs/decisions/`（女流勉強会の分野と、メニューの改称はサイト全体の分野）に、仕様を実装記録に書く。#576 に進み具合をコメントする

止まる条件

* 未マージの work/ ブランチ（work/JBK 以外）が `navbar.js` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* メニューの改称で、メニューの見出し以外の差分が生成物に出る
* 帰り道・Mつく・title/ の生成物に、メニューの見出し以外の差分が出る
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、「動画」の置き換え案（ログのどの表か）、生徒の写真の扱いの仮置き、平野さんの手作業（生徒の名前をタブに入れる。入れた後に `--check` で写真入り・名前だけの一覧を出す）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（メニューが見えるページ1つ）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1016-JBK-09"` と `docs/logs/` に無し
- 0: ログの「指示」欄の末尾が指示文の最後の行と一致。指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 作業ブランチ: origin/work/JBK は cloudflare の祖先（マージ済み）。ローカル work/JBK も cloudflare の祖先なので `git merge --ff-only origin/cloudflare`（f6fae4d33）
- 0: JBK-08 のログ（work/JBK）の状態の末尾に ` / 続き: CHAT-1016-JBK-09` を足した（b22eb103c）

### 1. 確かめ・メニューの改称

- 未マージの work/ ブランチ: `navbar.js`・女流勉強会の生成を変えるものは無い。`1016-hou2` が `style.css` を変えているが鳳凰戦の箇所（4709行付近、`.mj-houou-floor`）で、メニューの箇所ではない
- メニュー: `navbar.js` の61行目の見出し `動画<br>Video` を `番組<br>Program` にした（日本語・英語の2行の形は前提どおり）。中身（放送対局・帰り道・Mつく・English）と並びは変えていない
- メニューは `navbar.js` が実行時に描くので、生成物（HTML）には埋め込まれていない。`regenerate.py study study_episodes video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` は7ページとも成功し、**生成物の差分は0**
- メニューの見出しを文字で持つほかの場所: `llms.txt` の節見出し「## 動画」を「## 番組」に、`docs/notes/video-mtsuku.md` の「navbar の「動画」の「Mつく」」を「番組」にした。テストにメニューの見出しは無い
- #576 の本文の「メニューの位置」と段2の「navbar の「動画」に載せる」を「番組」に直し、公開の条件に改称と「動画」の置き換えの判断を足した

### 2-1. 「動画」の洗い出し（表A。置き換えはしていない）

ページの題・説明文・見出しなどの文を作る元（生成スクリプトの文字列と `llms.txt`）で洗い出した。手書きの HTML（`index.html`・`404.html` など）の題・説明文・見出しに「動画」は無かった。
「動画ID」と、コードの中だけの名前（データの列名「動画の単位」・止まる理由の文など）は除いた。

| # | 箇所（元） | 出る所 | 今の文 | 置き換え案 | 変えない方がよい理由 |
|---|---|---|---|---|---|
| A1 | `generate_video_live.py` | `video_live.html` の `<title>`・og:title | 放送対局 \| 動画 \| ryoei.pro | 放送対局 \| 番組 \| ryoei.pro | （メニューの名前を題に入れている形。メニューと合わせるなら変える） |
| A2 | `generate_video_en.py` | `video_en.html` の `<title>`・og:title | English \| 動画 \| ryoei.pro | English \| 番組 \| ryoei.pro | （A1 と同じ） |
| A3 | `lib/study.py` `list_page_title()` | `study/` の `<title>`・og:title | 女流勉強会 \| 日本プロ麻雀連盟の動画 \| ryoei.pro | 女流勉強会 \| 日本プロ麻雀連盟の番組 \| ryoei.pro、または 女流勉強会 \| Lady’s Mahjong Workshop \| ryoei.pro | （公開前なので変えやすい） |
| A4 | `generate_video_live.py` | `video_live.html` の h1 | 日本プロ麻雀連盟 放送対局動画 | 日本プロ麻雀連盟 放送対局 | YouTube の動画の一覧なので「動画」のままでも誤りではない |
| A5 | `generate_video_live.py` | `video_live.html` の表の上の説明・meta description | 日本プロ麻雀連盟の放送対局動画の一覧。／YouTubeチャンネル「日本プロ麻雀連盟」の放送対局動画N本をまとめています。… | 変えない | YouTube の動画そのものを数えている |
| A6 | `generate_video_live.py` | `video_live.html` の表の列見出し | 動画 | 変えない | 列の中身が動画（サムネイル）そのもの |
| A7 | `generate_video_en.py` | `video_en.html` の h1・説明・meta description | 日本プロ麻雀連盟 English動画／…英語動画N本… | h1 だけ「日本プロ麻雀連盟 English」も可 | 説明文は動画そのものを数えている |
| A8 | `generate_jpml_test.py` | `jpml_test.html` の説明・meta description・列見出し | 関連記事・動画N件／記事・動画一覧／動画・記事 | 変えない | 記事と動画（YouTube）を並べた一覧で、メニューの「番組」とは別の意味 |
| A9 | `generate_rh_paifu.py` | `rh_paifu.html` の列見出し | 動画 | 変えない | 列の中身が動画そのもの |
| A10 | `generate_video_wayhome.py` | `wayhome/` の meta description | …密着インタビュー動画N本を、… | 変えない（または「…密着インタビューN本」） | 動画そのものを数えている |
| A11 | `lib/wayhome.py` | `wayhome/<ID>.html` の紹介文・meta description | …への密着インタビュー動画です。 | 変えない | 各話は1本の動画 |
| A12 | `generate_mtsuku.py` | `mtsuku/` の meta description | …の動画N本を、最新話から… | 変えない | 動画そのものを数えている |
| A13 | `generate_study.py` | `study/` の meta description | …「女流勉強会」の動画N本を、最新話から… | 変えない | 動画そのものを数えている |
| A14 | `generate_live_pages.py` | `live/` の各ページの meta description（トップ・大会・期・卓） | …の放送対局動画N本… | 変えない | 動画そのものを数えている |
| A15 | `generate_live_pages.py` | `live/` の卓のページの節見出し | 動画（と「ライブ」） | 変えない | YouTube の種別「動画」「ライブ」の分け方（設計メモ 2-9） |
| A16 | `generate_saikyo_pages.py` | `saikyo/` の卓のページの節見出し | 関連動画 | 変えない | ABEMA・YouTube の動画そのもの |
| A17 | `generate_title_pages.py` | `title/` の期ページの節見出し | 決勝動画（と「決勝ライブ」） | 変えない | 動画そのもの（ライブと分けた見出し） |
| A18 | `llms.txt` 3行目 | サイトの概要 | …放送対局動画の索引を公開している。 | 変えない | 動画そのもの |
| A19 | `llms.txt` 18・45〜48行 | 各ページの説明 | 記事・動画34件／放送対局動画2,332本／密着インタビュー動画の一覧／の動画61本の一覧／英語動画39本 | 変えない | 動画そのものを数えている |
| A20 | `llms.txt` 43行 | 節見出し | ## 動画 | ## 番組（**今回変えた**） | メニューと同じ名前の見出し |

まとめ: メニューの名前として「動画」を使っているのは A1・A2・A3（題の真ん中）と A20（今回変えた）。ほかは YouTube の動画そのものを指す文で、変えない案。

### 2-2. 生徒の写真の扱い

- `lib/study.py` の `resolve_people()` を、講師・MC と生徒で分けた: 講師・MC は今までどおり、登録が無い・写真が読めない人で止める。
  生徒（講師・MC でもある人を除く）は止めずに、登録が無い → 「名前だけ（登録なし）」、X ID が無い → 「名前だけ（X ID なし）」、X ID があって写真が読めない（`wayhome.photo_problems()` が理由を返す。写真の URL が無い・既定のアイコンも含む）→ 「名前だけ（写真が読めない）」で代わりの絵、読めた人だけ「写真入り」
- 生成と `--check` のログに、回ごとの一覧を出す（`student_report_lines()`）
- テスト `scripts/tests/test_study.py` に3件足した（生徒の理由の4通りと写真を外すこと／講師は写真が読めないと止まる／生徒の登録が無い・写真が読めなくても止まらず、各話の生徒の節が写真入り1・代わりの絵2になる）。`python3 -m unittest discover -s scripts/tests` は全件 OK
- 実データの `python3 scripts/regenerate.py --check study study_episodes`（2ページとも通る）の出力:

```
== study(検査、--check) ==
生徒の写真: 生徒の名前を入れた回はありません
検査: 通りました(40回。ファイルは書いていません)
```

- 仮データ（生徒A: 登録あり・写真が読める、生徒B: 登録なし、生徒C: X ID なし、生徒D: 写真が読めない）での出力の見本:

```
生徒の写真(4件):
  part73(rrBlY_X4lFA): 生徒 生徒A 写真入り
  part73(rrBlY_X4lFA): 生徒 生徒B 名前だけ(登録なし)
  part72(Kl1pVrnC2og): 生徒 生徒C 名前だけ(X ID なし)
  part72(Kl1pVrnC2og): 生徒 生徒D 名前だけ(写真が読めない)
検査: 通りました(2回。ファイルは書いていません)
```

- 同姓同名の恐れ: 「現役のプロとして同じ名前で活動している」かを「プロ」「連盟プロ以外」の登録の有無で代えているため、生徒と同じ名前の別人（ほかの団体のプロなど）が登録されていると、その人の写真が出る。名前を入れた後の一覧で、写真入りになった人を平野さんが目で確かめる必要がある
- コミット: 70b6eb666（メニュー・`llms.txt`・生徒の扱い・テスト）、2aa7ea0ae（文書）

### 3. プレビュー・文書

- プレビュー（70b6eb666 の Workers Builds が success）で `navbar.js` が `番組<br>Program` を返すことを確かめた。headless Chromium で `mtsuku/` のメニューを開いて見た:
  - PC 幅（1280）: 横並びのメニューに「番組 / Program」。開くと 放送対局・帰り道・Mつく・English（並びは前と同じ）。幅は「動画 / Video」とほぼ同じで、ほかの項目の位置はずれない
  - 375px: ハンバーガーを開いた縦のメニューに「番組 / Program」。開いた中身は同じ4項目。横スクロールは出ない
- 決定: `docs/decisions/study.md`（JBK-09: 本番の確かめ・生徒の名前をタブに入れる・載せ方の方針（予定）・公開は結果を見てから）、`docs/decisions/page-release.md`（メニューの改称と、「動画」の置き換えは一覧を見てから）。`docs/decisions/README.md` の表の `study.md` の行をアルファベット順に直した
- 実装記録: `docs/notes/video-study.md` の生徒の記述を置き換えた
- #576 に進み具合をコメントした

## 報告

- 状態: 判断待ち
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: プレビューあり（URL は最終報告）。メニューの「番組 / Program」を `mtsuku/` で見た。この版の印: 上のメニューの「動画 / Video」が「番組 / Program」
- マージ: 未（平野さんの判断待ち）
- issue: #576（本文を直し、進み具合をコメント）
- 判断が必要なこと:
  - 「動画」の置き換え案: このログの `## 経過`「2-1. 「動画」の洗い出し（表A）」。メニューの名前として使っているのは A1（放送対局 \| 動画）・A2（English \| 動画）・A3（女流勉強会 \| 日本プロ麻雀連盟の動画）の3つ。ほかは YouTube の動画そのものを指す文で、変えない案
  - 生徒の写真の扱いの仮置き: 生徒だけ止めずに、「プロ」「連盟プロ以外」に登録があり X ID があって写真が読める人は写真入り、登録が無い・X ID が無い・写真が読めない人は名前だけ（代わりの絵）。講師・MC は今までどおり止める。同姓同名の別人が登録されていると写真が出る恐れがある
  - 平野さんの手作業: タブ「女流勉強会」の「生徒」列に生徒の名前を入れる（「、」区切り）。入れた後に `python3 scripts/regenerate.py --check study` で「写真入り」「名前だけ（理由）」の一覧を出し、写真入りになった人が本人かを確かめる（続きの指示で行う）
  - 平野さんの手作業（JBK-08 から）: part1〜33 が層1に無い件を担当者に確かめる
  - 本番の反映（JBK-08 から）: 4fdda151b の本番向けのビルドが走っていなかった件は、この指示では確かめていない（#575 はまだ open）
- 未確認の項目:
  - 平野さんの実機でのメニューの見え方（プレビュー）
  - 生徒の名前を入れた後の写真入り・名前だけの一覧（今は生徒が全回空）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 374ad1fb）: https://github.com/retroeater/mj-logs/tree/main/guide/374ad1fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
