# CHAT-1024-VOD-01

- 着手日時: 2026-10-23
- 対象issue: #620, #268
- ブランチ: work/1024-vod
- 着手時HEAD: c8aed73

## 指示

【Claude作成】Claude Code 向け指示：動画の一覧4ページ（mtsuku・exam・wayhome・study）から VideoObject を外す（Search Console「動画再生ページに動画がありません」、#620 の範囲を広げる） Chat-Ref: CHAT-1024-VOD-01 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-vod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1024-vod を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-vod origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
Search Console の「動画再生ページに動画がありません」を直す。動画の一覧ページはプレーヤーを埋め込んでいないのに VideoObject（最新話）を持っているのが原因なので、一覧ページから VideoObject を外し、動画の情報は各話のページだけに持たせる。#620（/mtsuku/ のみ）の範囲を、同じ作りの一覧4ページに広げる。
決定（2026-10-24、平野さん）

* 動画の一覧4ページ（/mtsuku/・/exam/・/wayhome/・/study/）から VideoObject を外す。ItemList は残す（案 A。#620 の 2026-10-20 の決定「一覧ページから動画の構造化データを外す。一覧ページにプレーヤーは埋め込まない」を4ページに広げる）

前提（チャット側。平野さんの決定ではない）

* 平野さんのスクリーンショット（2026-10-24）: 動画のインデックス登録 > 動画再生ページに動画がありません、影響を受ける動画 2。例は `https://ryoei.pro/exam/`（動画 `https://www.youtube.com/watch?v=-PoeE0Bm2sE`、前回のクロール 2026/10/19）と `https://ryoei.pro/mtsuku/`（動画 `https://www.youtube.com/watch?v=ZeFtFG2ov_Q`、2026/10/16）。wayhome/・study/ は例に出ていない
* CHAT-1020-MRA-03 のログの経過: 4ページとも一覧は VideoObject 1・ItemList 1。mtsuku は `scripts/generate_mtsuku.py` の `build_json_ld()`、wayhome・study は `generate_video_wayhome.py`・`generate_study.py` の `build_json_ld()` が同じ形。exam の生成箇所は未確認（要確認）
* 各話のページ（例 `mtsuku/ZeFtFG2ov_Q.html`）は VideoObject と og:video を持つが `<iframe>` は無い。各話の扱い（プレーヤーを埋め込むか、各話からも外すか）はこの指示では変えず、別の issue に起票するだけ
* #268（open「動画一覧3ページに VideoObject 構造化データを入れる」）は今回の決定と逆向きなので閉じる
* 決定を足す先は docs/decisions/mtsuku.md（#620 の行の続き）と、exam.md・wayhome.md・study.md に同じ決定の参照1行（書き場所は実物に合わせて変えてよい）

手順

1. #620・#268 と、同じ論点の open issue を検索する（クローズ済みも含めて確認）。#620 に他セッションの着手中コメントがあれば止まる。4ページの一覧の生成箇所（exam を含む）を確かめ、各一覧の JSON-LD を本番の HTML とリポジトリの版で数え（@type ごとの件数をページ別の表で）ログに書く
2. 4ページの一覧から VideoObject を外し、ItemList は残す（`build_video_object()` は各話と共用なので、各話の出力は変えない）。生成し直し、4つの一覧は VideoObject 0・ItemList 1、各話のページは変更前と同じ（差分なし）であることを確かめてログに表で書く。ページを決めたテスト（あれば）を直す。決定を docs/decisions に足す
3. issue: #620 に、範囲を4ページに広げた決定と変更の要約・比較 URL をコメントする（閉じるのはマージ後の本番確認のとき）。#268 に「CHAT-1024-VOD-01 の決定（一覧ページには VideoObject を持たせない）により不要」とコメントし、not planned で閉じる。各話のページ（プレーヤーなし・VideoObject あり）の扱いを決める issue を新規起票する（ラベルなし。題の例「動画の各話ページにプレーヤーを埋め込むか VideoObject を外すか（Search Console: 動画再生ページに動画がありません の予防）」。本文に案〈埋め込む／外す／今のまま様子を見る〉と、各話の数を一覧ごとに書く）

止まる条件

* #620 に他セッションの着手中コメントがある
* 一覧ページの JSON-LD が前提（VideoObject 1・ItemList 1）と違うページがある（そのページだけ外して進め、全部が違うときだけ止まる）
* 各話のページの出力が変わる
* 未マージの work/ ブランチが4つの生成スクリプトの `build_json_ld()` または JSON-LD を組む関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なるページだけ外して進め、全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（判断が必要なこと: マージしてよいか。起票した issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり（マージ後に平野さんが Search Console で「修正を検証」を押す。報告にその旨を書く）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-VOD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-VOD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c8aed737）: https://github.com/retroeater/mj-logs/tree/main/guide/c8aed737

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c8aed737/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
