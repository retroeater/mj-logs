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

## 報告

- 状態: 作業中
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし
- マージ: 未
- issue: #612
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7b7fe6f5）: https://github.com/retroeater/mj-logs/tree/main/guide/7b7fe6f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b7fe6f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
