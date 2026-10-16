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
- 作業ブランチ: origin/work/JBK は cloudflare の祖先（マージ済み）。ローカル work/JBK も cloudflare の祖先なので、`git merge --ff-only origin/cloudflare` で進めた（f6fae4d33）

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2536efeb）: https://github.com/retroeater/mj-logs/tree/main/guide/2536efeb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
