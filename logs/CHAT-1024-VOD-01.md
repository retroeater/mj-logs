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

## 経過

### 着手前の確認

- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 識別子: `git log --all -E --grep 'CHAT-[0-9]{4}-VOD-'` と `docs/logs/CHAT-*-VOD-*.md` の履歴とも0件。同じ Chat-Ref のコミットも0件
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つともあり（欠けなし）
- issue の state: #620 open・コメント0件（他セッションの着手中なし）、#268 open。#620 に着手中のコメントを残した
- 作業ブランチ: ローカル・リモートとも work/1024-vod は無かったので `git checkout -b work/1024-vod origin/cloudflare`（c8aed73）
- 同じ論点の issue（クローズ済みを含めて検索）: #620・#268 のほか、#173（open、帰り道シートの列を個別ページと VideoObject に反映。各話の側で今回は触らない）。各話のプレーヤー埋め込みを扱う issue は無い（#288・#329 は closed、og:video・twitter:player）
- 0章ゲート: 未マージの work/ ブランチのうち、触るファイル（4つの生成スクリプト・`scripts/lib/{mtsuku,exam,study,wayhome}.py`・4つの index.html・`scripts/tests/`）を変えているのは2本
  - work/1017-lvp: `scripts/lib/study.py`（397〜408行、カードの画像の alt。`build_video_object()` は415行で、JSON-LD を組む関数は変えていない）と `scripts/tests/test_study.py`（4行）。同じファイルの別の箇所なので止まらない
  - work/1018-log-367-migrate: `scripts/tests/test_convert_x_posts.py`・`test_fetch_x_posts.py` だけ（重ならない）
  - 4つの `build_json_ld()` を変えているブランチは無い

### 生成箇所

| 一覧 | 生成スクリプト | VideoObject を組む関数 |
|---|---|---|
| /mtsuku/ | `scripts/generate_mtsuku.py` の `build_json_ld()` | `lib/mtsuku.build_video_object()` |
| /exam/ | `scripts/generate_exam.py` の `build_json_ld()` | `lib/exam.build_video_object()` |
| /wayhome/ | `scripts/generate_video_wayhome.py` の `build_json_ld()` | `lib/wayhome.build_video_object()` |
| /study/ | `scripts/generate_study.py` の `build_json_ld()` | `lib/study.build_video_object()` |

各 `build_video_object()` は各話の生成スクリプト（`generate_*_episodes.py`）と共用なので触らない。

### 変更前の JSON-LD（@type ごとの件数）

本番は `https://ryoei.pro/<一覧>/` を curl で取得（2026-10-23）、リポジトリは cloudflare c8aed73。

| 一覧 | 本番 | リポジトリ | `<iframe>` |
|---|---|---|---|
| /mtsuku/ | VideoObject 1・ItemList 1 | VideoObject 1・ItemList 1 | なし |
| /exam/ | VideoObject 1・ItemList 1 | VideoObject 1・ItemList 1 | なし |
| /wayhome/ | VideoObject 1・ItemList 1 | VideoObject 1・ItemList 1 | なし |
| /study/ | VideoObject 1・ItemList 1 | VideoObject 1・ItemList 1 | なし |

4ページとも前提どおり。

### 変更

- 4つの `build_json_ld()` から VideoObject を外し、ItemList だけを返す（引数 `hero_thumb_url` は VideoObject にだけ使っていたので外した。wayhome の `build_page()` の `hero_thumb_url` の行も同じ理由で消した。ヒーローは `build_hero_html()` が自分で `resolve_hero_thumb()` を呼ぶ）
- テスト: 4つの一覧のテストに「VideoObject を含まない・ItemList が1つ」を1本ずつ足した（`test_mtsuku.py`・`test_exam.py`・`test_study.py`・`test_wayhome_prevnext.py`）。修正前のコード（origin/cloudflare の worktree に新しいテストを写した）では4本とも FAIL、修正後は全件 OK
- 手元ではシートの鍵（`SHEETS_READER_SA_KEY`）が無く生成できない（regenerate.py が全8ページを飛ばした）ので、生成は `regenerate-page.yml` の手動実行で行う

### 生成と確かめ

- `regenerate-page.yml` を work/1024-vod で手動実行（dry_run・compare_ref `origin/cloudflare`、対象は一覧4つと各話4つ）: run 39980236794。新旧で中身の違うファイルは `exam/index.html`・`mtsuku/index.html`・`study/index.html`・`wayhome/index.html` の各1行だけ（違いがあるとジョブを失敗にする作りなので結論は failure。想定どおり）。各話のページは新旧のコードで同じ
- 同じワークフローを書く道筋で手動実行（対象は一覧4つ）: run 39981042542 が success、687018dea「chore: regenerate mtsuku/index.html exam/index.html study/index.html wayhome/index.html via GitHub Actions」を push した（4つの index.html の各1行の削除と、sitemap-exam・mtsuku・wayhome の lastmod〈ワークフローが導出〉）
- 変更後（687018dea）の JSON-LD（@type ごとの件数）:

| 一覧 | 変更前 | 変更後 | 差分 |
|---|---|---|---|
| /mtsuku/ | VideoObject 1・ItemList 1 | ItemList 1 | 1行削除 |
| /exam/ | VideoObject 1・ItemList 1 | ItemList 1 | 1行削除 |
| /wayhome/ | VideoObject 1・ItemList 1 | ItemList 1 | 1行削除 |
| /study/ | VideoObject 1・ItemList 1 | ItemList 1 | 1行削除 |

- 各話のページ（`git diff origin/cloudflare HEAD -- mtsuku exam wayhome study` は4つの index.html だけ）:

| 一覧 | 各話のページ | VideoObject を持つ | og:video | `<iframe>` | 変更前との差分 |
|---|---|---|---|---|---|
| mtsuku/ | 61 | 61 | 61 | 0 | なし |
| exam/ | 12 | 12 | 12 | 0 | なし |
| wayhome/ | 42 | 42 | 42 | 0 | なし |
| study/ | 40 | 40 | 40 | 0 | なし |

- プレビュー（Workers Builds、687018dea、success）: 4つの一覧は ItemList 1 だけ。各話の `mtsuku/ZeFtFG2ov_Q.html`・`exam/-PoeE0Bm2sE.html` はリポジトリの版とバイト一致（VideoObject 1・BreadcrumbList 1）
- テスト: `python3 -m unittest discover -s scripts/tests` は OK

### issue

- #620: 範囲を4ページに広げた決定と変更の要約・比較 URL をコメントした（閉じるのはマージ後の本番確認のとき）
- #268: 「CHAT-1024-VOD-01 の決定（一覧ページには VideoObject を持たせない）により不要」とコメントし、not planned で閉じた（「状況:」ラベルは付いていなかった）
- #629 を起票（ラベルなし）: 「動画の各話ページにプレーヤーを埋め込むか VideoObject を外すか（Search Console: 動画再生ページに動画がありません の予防）」。案（埋め込む／外す／今のまま様子を見る）と各話の数（mtsuku 61・exam 12・wayhome 42・study 40）。(a) では CSP の `frame-src 'none'` を緩めることになる点も書いた

### 決定

- `docs/decisions/mtsuku.md` に 2026-10-24（CHAT-1024-VOD-01）の見出しで決定を足し、exam.md・wayhome.md・study.md に参照1行を足した

## 報告

- 状態: 判断待ち / 続き: CHAT-1024-VOD-02
- ブランチ: work/1024-vod
- ログ: https://github.com/retroeater/mj/blob/work/1024-vod/docs/logs/CHAT-1024-VOD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1024-vod
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは /mtsuku/・/exam/・/wayhome/・/study/ と各話2件。印: 4つの一覧の `<head>` に `"@type": "VideoObject"` が無い（ItemList の1行だけ）
- マージ: 未（平野さんの判断待ち）
- issue: #620（コメント。マージ後の本番確認で閉じる）、#268（not planned で閉じた）、#629（起票、各話のページの扱い）
- 判断が必要なこと:
  - work/1024-vod を cloudflare にマージしてよいか。マージ後に平野さんが Search Console（動画のインデックス登録 > 動画再生ページに動画がありません）で「修正を検証」を押す
  - #629（各話のページにプレーヤーを埋め込む／VideoObject を外す／様子を見る）の案の選択
- 未確認の項目: 本番への反映（マージ前のため）と Search Console の検証の結果
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0a91c125）: https://github.com/retroeater/mj-logs/tree/main/guide/0a91c125

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
