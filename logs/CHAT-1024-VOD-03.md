# CHAT-1024-VOD-03

- 着手日時: 2026-10-24
- 対象issue: #629, #173
- ブランチ: work/1024-vod
- 着手時HEAD: 070c0aa98

## 指示

【Claude作成】Claude Code 向け指示：動画の各話ページ（mtsuku・exam・wayhome・study）から VideoObject を外す（#629 の案 b）
Chat-Ref: CHAT-1024-VOD-03
マージ: 判断待ちで止まる
貼る時機: CHAT-1024-VOD-02 の完了（work/1024-vod が cloudflare にマージ済み）の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-vod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-vod を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-vod origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1024-VOD-02 の変更が origin/cloudflare に入っていなければ何もせず止まる。

## 目的
動画の各話のページはプレーヤーを埋め込んでいない（サムネイルと YouTube へのリンク）のに VideoObject を持っていて、Search Console の「動画再生ページに動画がありません」の元になりうる。各話からも VideoObject を外し、動画の構造化データを4つの番組のページから無くす（#629）。

### 決定（2026-10-24、平野さん）
- #629 は案 b: 各話のページ（mtsuku・exam・wayhome・study）からも VideoObject を外す（プレーヤーは埋め込まない）

### 前提（チャット側。平野さんの決定ではない）
- 各話の数は mtsuku 61・exam 12・wayhome 42・study 40 で、すべて VideoObject 1・og:video あり・`<iframe>` 0（CHAT-1024-VOD-01 のログの経過）。VideoObject を組むのは `scripts/lib/{mtsuku,exam,wayhome,study}.py` の `build_video_object()`、呼ぶのは各話の生成スクリプト（`generate_*_episodes.py` など。要確認）
- 外すのは VideoObject だけ。BreadcrumbList・og:type（video.other）・og:video・twitter 系は残す（og の規則は docs/decisions/seo-bing.md「埋め込みできる動画のページ（YouTube）は video.other ＋ og:video」のまま）
- 外した後に呼ばれなくなる関数（`build_video_object()`・`json_ld_html()` の VideoObject 用の部分など）は消してよい（残さない）。ただし4つの番組以外から呼ばれているものは残す
- docs/notes/video-{mtsuku,exam,wayhome,study}.md の VideoObject の記述（`VideoObject.name`・`VideoObject.description`・`isAccessibleForFree` など）を今の形に直す（紹介文・題名の規則そのものは meta・og に残るので変えない）
- #173（open、帰り道シートの列を個別ページと VideoObject に反映）は VideoObject の部分が無くなる。#173 にその旨をコメントする（状態は変えない）
- 4つの番組以外で VideoObject を出しているページがあるかは未確認（要確認）。あれば一覧にしてログと #629 に書くだけで、この指示では変えない

## 手順
1. #629・#173 と同じ論点の open issue を確かめ（クローズ済みも含めて）、#629 に他セッションの着手中コメントがあれば止まる。`git grep -n 'VideoObject'`（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）の結果を、4つの番組とそれ以外に分けてログに表で書く
2. 4つの番組の各話から VideoObject を外し、使われなくなった関数を消す。テストを直す（各話に VideoObject が無いこと・BreadcrumbList と og:video が残ることを確かめるテストを番組ごとに足す）。regenerate-page.yml で生成し直し、各話が VideoObject 0・BreadcrumbList 1・og:video 1 になり、差分が VideoObject の行だけであることを確かめる（変更前後の件数を番組ごとの表で書き、変わったファイルの一覧は `git diff --stat` で全件添える）。一覧4ページは変わらないことも確かめる。docs/notes と docs/decisions（mtsuku.md に決定、exam.md・wayhome.md・study.md に参照1行）を直す
3. issue: #629 に変更の要約・比較 URL・手順1の「それ以外」の一覧をコメントする（閉じるのはマージ後の本番確認のとき）。#173 に上の前提のとおりコメントする

## 止まる条件
- CHAT-1024-VOD-02 の変更が origin/cloudflare に入っていない
- #629 に他セッションの着手中コメントがある
- 各話の数が前提（mtsuku 61・exam 12・wayhome 42・study 40）から ±3 を超えて違う（新しい回の追加はこの範囲で許す）
- 各話の差分に VideoObject の行以外の変更がある
- 未マージの work/ ブランチが各話の生成スクリプトの JSON-LD を組む関数・`build_video_object()` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なる番組だけ外して進め、全部が重なるときだけ止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（判断が必要なこと: マージしてよいか）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-VOD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-VOD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 96fdc103）: https://github.com/retroeater/mj-logs/tree/main/guide/96fdc103

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/96fdc103/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/070c0aa9.md
