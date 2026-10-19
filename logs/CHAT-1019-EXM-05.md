# CHAT-1019-EXM-05

- 着手日時: 2026-10-19
- 対象issue: #612（#613 は開いたまま）
- ブランチ: work/1019-exm
- 着手時HEAD: 4b55f29c9

## 指示

【Claude作成】Claude Code 向け指示：work/1019-exm（/exam/ を未公開で作成）を cloudflare にマージする Chat-Ref: CHAT-1019-EXM-05 マージ: 承認済み（チャットで。2026-10-19 に平野さんがプレビューを見て「マージしてよい」と答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-exm を続けて使う（CHAT-1019-EXM-04 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書の衝突は、両方の変更が両立するもの（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
EXM-01〜04 で作った `/exam/`（プロテストの動画12本の一覧と各話、未公開）を、プレビューを見た平野さんの承認により cloudflare にマージする。公開（noindex を外す・navbar・サイトマップ・`llms.txt`・旧 `jpml_test.html` からの 301）は #613 で別に行い、この指示では行わない。
決定（2026-10-19、平野さん）

* プレビューを確かめ、未公開のまま cloudflare にマージしてよい
* 見出しの折り返し（ヒーロー最大3行、カードと前後の動画2行）は今のままでよい（EXM-04 の推奨。チャット側も同じ推奨を示し、平野さんはそのうえでマージを承認した）

前提（チャット側。平野さんの決定ではない）

* 2026-10-19 にチャット側が `origin/cloudflare...origin/work/1019-exm` の差分を見た範囲（docs/logs を除く）: 新規 `exam/`（一覧1・各話12）・`scripts/lib/exam.py`・`scripts/generate_exam.py`・`scripts/generate_exam_episodes.py`・`scripts/tests/test_exam.py`・`docs/notes/video-exam.md`・`docs/decisions/exam.md`。変更 `scripts/regenerate.py`（+6）・`scripts/check_unused_names.py`・`_redirects`（+2、`/exam` の 301 と `/exam/` の 200）・`.github/workflows/check-assets.yml`（+2）・`style.css`（`.mj-exam-en` を足す4行）・`docs/notes/static-generation.md`・`docs/handover.md`・`docs/decisions/README.md`。navbar・サイトマップ・`llms.txt`・`jpml_test.html` とその生成は変えていない（要確認）
* マージ後に `regenerate-page.yml` が自動で再生成する範囲は、`scripts/regenerate.py`・`scripts/lib/` の変更しだいで広くなりうる（docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」）。EXM-01 の比較では、両方の木で同じく変わるデータの変化（SNS ブックの X ID が入った人の写真カードのリンク、`jpml_pros.html` の1人の回数など）が出ていた。その種のシートの変化が本番に出るのは見込みのうち

手順

1. EXM-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-EXM-05` を足す。origin/cloudflare を取り込んだ後、`git diff --stat origin/cloudflare...HEAD`（docs/logs を除く）を `## 経過` に全件で書き、前提の範囲と違うファイルがあれば止まる
2. 取り込んだ後の木で再検証する: `python3 -m unittest discover -s scripts/tests`、`python3 scripts/regenerate.py --check exam exam_episodes`、`python3 scripts/regenerate.py exam exam_episodes` で `exam/` の生成物の差分が0（シートが変わっていて差分が出たら、変わった動画ID・項目を全件で書いて止まる）、容量の検査（`check-assets.yml` と同じもの）。マージ後の自動再生成で変わる範囲（対象ページと、前回の再生成以後のシートの変化）を見積もって `## 経過` に書く
3. docs/notes/branch-operations.md「マージするとき」の手順で cloudflare にマージする（push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめる）。マージ後、「Workers Builds: mj」の check-run と `regenerate-page.yml` の実行の結果を待ち（待機は15分まで）、本番で次を確かめる: `https://ryoei.pro/exam/` と各話1枚が 200 で `noindex` が付いている、`/exam` が `/exam/` へ 301、`https://ryoei.pro/jpml_test.html` が今までどおり 200 で中身が変わっていない、navbar に「プロテスト」の行き先の変化が無い。#612 に完了のコメント（何を入れたか、公開は #613）を書いてクローズする（「状況:」ラベルがあれば外す）。#613 は開いたまま残す

止まる条件

* 手順1の差分に前提の範囲に無いファイルがある、テスト・`--check`・容量の検査のどれかが通らない、`exam/` の生成物に差分が出る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら原因を書いて止まる（#612 はクローズしない）。無関係な失敗なら原因を報告に書いたうえで、本番の確かめと #612 のクローズを進めてよい
* 本番の確かめで `/exam/` が 200 でない・`noindex` が無い・`jpml_test.html` が変わった

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-05` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカルの `work/1019-exm` は `origin/work/1019-exm`（4b55f29c9）と一致、作業ツリーはきれい。cloudflare（7b7fe6f57）は祖先でないため、この後 merge で取り込む
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- EXM-04 のログの状態の末尾に ` / 続き: CHAT-1019-EXM-05` を足した

### 手順1: 取り込みと差分

cloudflare（7b7fe6f57）を `git merge origin/cloudflare` で取り込んだ（4d26179c6）。衝突なし。`git diff --stat origin/cloudflare...HEAD -- . ':!docs/logs'`（全件）:

```
 .github/workflows/check-assets.yml |   2 +
 _redirects                         |   2 +
 docs/decisions/README.md           |   1 +
 docs/decisions/exam.md             |  26 ++++
 docs/handover.md                   |   1 +
 docs/notes/static-generation.md    |   3 +
 docs/notes/video-exam.md           |  35 ++++++
 exam/-PoeE0Bm2sE.html              |  71 +++++++++++
 exam/98GKK3KKYNk.html              |  65 ++++++++++
 exam/Esz7Mloebaw.html              |  72 +++++++++++
 exam/JTOTffVFqTg.html              |  72 +++++++++++
 exam/Lp8m7sAWT_k.html              |  72 +++++++++++
 exam/RfZxTnSGnBw.html              |  72 +++++++++++
 exam/eKMCpGQKTYk.html              |  72 +++++++++++
 exam/gyYpx9xfrLA.html              |  72 +++++++++++
 exam/index.html                    | 164 +++++++++++++++++++++++++
 exam/ix9xGkF5pJs.html              |  72 +++++++++++
 exam/jal-jnnwJb4.html              |  72 +++++++++++
 exam/kzKMbbNvAcQ.html              |  64 ++++++++++
 exam/lGPjKHDAlzw.html              |  72 +++++++++++
 scripts/check_unused_names.py      |   7 +-
 scripts/generate_exam.py           | 147 ++++++++++++++++++++++
 scripts/generate_exam_episodes.py  | 171 ++++++++++++++++++++++++++
 scripts/lib/exam.py                | 242 +++++++++++++++++++++++++++++++++++++
 scripts/regenerate.py              |   6 +
 scripts/tests/test_exam.py         | 149 +++++++++++++++++++++++
 style.css                          |   4 +-
 27 files changed, 1804 insertions(+), 4 deletions(-)
```

前提の範囲と一致（`navbar.js`・サイトマップ・`llms.txt`・`jpml_test.html`・`generate_jpml_test.py` は変えていない）

### 手順2: 取り込んだ後の木での再検証

- `python3 -m unittest discover -s scripts/tests`: 1445件 OK
- `python3 scripts/regenerate.py --check exam exam_episodes`: 2ページとも通る
- `python3 scripts/regenerate.py exam exam_episodes`: `exam/` の差分0（シートは EXM-04 から変わっていない）
- 容量（`scripts/check_asset_limits.py`）: 配信ファイル数 4,195・最大のファイル 2,363,334 バイト・`_redirects` 静的 115・動的 10・`_headers` 7、どれも OK。最上位の許可リストの検査（`check-assets.yml` の手順を手元で）も通った。ガイド文書は handover.md 22,962・CLAUDE.md 24,786・chat-side-operations.md 22,155・instruction-template.md 14,325 バイト（どれも警告域未満）
- マージ後の自動再生成の見積もり: `regenerate-page.yml` は `scripts/lib/**`・`scripts/generate_*.py` の変更で動き、`regenerate.py --changed` は `scripts/lib/exam.py` が変わるので全ページ（30、`exam`・`exam_episodes`・`jpml_test` を含む）を選ぶ。
  取り込んだ後の木で `python3 scripts/regenerate.py all` を回した結果、**変わったファイルは0**（前回の自動再生成以後のシートの変化は、この時点では無い）。マージから自動再生成までにシートが変われば、その分は本番に出る

### 手順3: マージと本番の確かめ

- 1回目の push 直前の確かめで cloudflare が進んでいた（work/1019-wrc14 のマージ、908e3c08d）。もう一度 `git merge origin/cloudflare` で取り込んだ（b5e6dbe14、衝突なし）。
  新しく入ったのは `scripts/promo_video/jwrc/` と docs だけで、ページの生成には関係しない。`origin/cloudflare...HEAD` の差分は1回目と同じ27ファイル。テスト（1445件 OK）と `regenerate.py --check exam exam_episodes`（通る）をやり直した
- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめて `git push origin work/1019-exm:cloudflare`（908e3c08d..b5e6dbe14、fast-forward）
- 「ページの再生成」（run 39232196280）: **失敗。今回の変更とは関係しない。** `saikyo_pages` だけが飛ばされた。原因は「最強戦」シートを読んだときの Google Sheets の HTTP 502（`urllib.error.HTTPError: HTTP Error 502: Bad Gateway`、`lib/sheets.py` の `_query()`）。
  `exam`・`exam_episodes` を含むほかの29ページは正常に生成されたが、どのページも変わらず、コミットはされなかった（「変更なし」）。手元の `--check saikyo_pages` は通る。最強戦のページは次の再生成で作り直される（手元の全ページの再生成では差分0だったので、本番の中身に影響は無い）
- 「Workers Builds: mj」: b5e6dbe14 で最初に出た success（09:45:55）は work ブランチのプレビューのビルド（プレビューの URL 付き）。本番のビルドは 09:54:39 に success。それまでの約10分は本番の `/exam/` が 404 だった
- 本番の確かめ（09:55 以降）:
  - `https://ryoei.pro/exam/` と `https://ryoei.pro/exam/lGPjKHDAlzw.html` が 200 で `<meta name="robots" content="noindex">` 付き。中身はリポジトリの生成物と同じ
  - `/exam` は `https://ryoei.pro/exam/` へ 301
  - `https://ryoei.pro/jpml_test.html` は 200 で、マージ前（908e3c08d）のファイルと同じ
  - 本番の `navbar.js` はマージ前と同じ（「プロテスト」の行き先は `/jpml_test.html` のまま）。`sitemap.xml`・`llms.txt` に `exam` は無い。`style.css` に `.mj-exam-en` がある
- #612 に完了のコメントを書いてクローズした（「状況:」のラベルは付いていなかった）。#613 は開いたまま
- 決定（プレビューの確認・未公開のままマージ、見出しの折り返しは今のまま）を `docs/decisions/exam.md` に足した

## 報告

- 状態: 完了
- ブランチ: work/1019-exm（マージ済み。削除は cleanup-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-EXM-05.md
- 比較URL: https://github.com/retroeater/mj/compare/908e3c08d...b5e6dbe14
- 確認用URL: なし（本番で確かめた。`/exam/` は未公開・noindex）
- マージ: 済（b5e6dbe14）
- issue: #612（クローズ）・#613（公開、開いたまま）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4f73f403）: https://github.com/retroeater/mj-logs/tree/main/guide/4f73f403

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4f73f403/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
