# CHAT-1019-EXM-04

- 着手日時: 2026-10-19
- 対象issue: #612
- ブランチ: work/1019-exm
- 着手時HEAD: 3e5d690f5

## 指示

【Claude作成】Claude Code 向け指示：シート「プロテスト」の「タイトル（補正分）」12行をそのまま見出しにして、EXM-02 の手順2・3を済ませる Chat-Ref: CHAT-1019-EXM-04 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1019-exm を続けて使う（CHAT-1019-EXM-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-03 は「タイトル（補正分）」がチャット側の見込み（6行）と違って止まった。値は平野さんが12行すべてに入れたもので、意図どおり。見込みをこの値に改め、EXM-02 の手順2のうち残り・手順3を済ませてプレビューまで進める。
決定（2026-10-19、平野さん）

* 平野さんがシート「プロテスト」の「タイトル（補正分）」を12行すべて補正した（「タイトルをすべて補正しました」）。EXM-02 の決定 (3)「値があるときだけ、その値で見出しを上書きする」のとおり、値をそのまま見出しにする（EXM-03 の案 (a)。補正の値には【】の除去をかけない）

前提（チャット側。平野さんの決定ではない）

* EXM-03 の `## 経過`「手順1」の表の12行（動画ID・出演者・タイトル（補正分））が今のシートの値（要確認。読み直して全件で照合する）
* EXM-02 の実装（仮の TSV を外す、「タイトル（補正分）」の読み取り、`test_exam.py` 12本）と EXM-03 の cloudflare の取り込み（c754260dc）は作業ブランチにある（要確認）
* 補正の値が無い行が無くなるので、自動の見出し（先頭の【】と【日本プロ麻雀連盟】の除去）は今は使われない。コードとテストは消さずに残す（新しい回をシートに足して補正を空にしたときに使う）

手順

1. EXM-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1019-EXM-04` を足す。生成と同じ経路でシートを読み直し、EXM-03 の表の12行と全件で照合して `## 経過` に書く（違う行は両方の値を書いて止まる）
2. EXM-02 の手順2のうち済んでいないもの（`docs/notes/video-exam.md`・`docs/decisions/exam.md` に、決定 (1)〜(8) とこの指示の決定を書く、など）を済ませる。済んだものはやり直さず、確かめた結果だけ書く
3. EXM-02 の手順3を行う: テスト、`regenerate.py all` の作業ブランチと origin/cloudflare のクローンでの比較（データの変化で両方の木が同じく変わったファイルはコミットしない）、`exam/` の EXM-01 の版からの見出しの差分の表（12行全件）、未マージの全 work/ ブランチとの `git merge-tree`、プレビューの画像（一覧・出演者の多い回・出演者「-」の回・いちばん古い回・最新の回、PC とスマホ幅）を `docs/logs/CHAT-1019-EXM-04/` に。見出しが長くなった回（【年度】付き）の一覧のカード・ヒーロー・前後の動画のカードで、折り返しや切れが無いかを画像で見て報告に書く

止まる条件

* シートの行数が12でない、「出演者」が EXM-01 の `exam_cast_column.tsv` と違う、「タイトル（補正分）」が EXM-03 の表と違う（全件の表を書いて止まる）
* `regenerate.py all` の差分に、この作業とデータの変化で説明できないページの変化がある
* 未マージの work/ ブランチとの `git merge-tree` で、cloudflare との組より衝突が増える（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 見た目の崩れでは止まらない。画像と報告の「判断が必要なこと」に書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-04` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカルの `work/1019-exm` は `origin/work/1019-exm`（3e5d690f5）と一致、作業ツリーはきれい。EXM-02 の 2507314e7・EXM-03 の取り込み c754260dc を含む。cloudflare（58fa24fff）は祖先でないため、この後 merge で取り込む
- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- cloudflare（58fa24fff）を `git merge origin/cloudflare` で取り込んだ（31b34e54b）。衝突なし。work/WRR は cloudflare に入っていた

### 手順1: シートの読み直し

`lib/sheets.fetch_table()`（生成と同じ経路）で読んだ。見出し `動画ID`・`出演者`・`タイトル（補正分）`、12行。
12行とも EXM-03 の `## 経過`「手順1」の表（動画ID・出演者・タイトル（補正分））と一致し、出演者は EXM-01 の `exam_cast_column.tsv` とも一致（スクリプトで全件照合、違う行0）。EXM-03 のログの状態の末尾に ` / 続き: CHAT-1019-EXM-04` を足した（b349ae5ab）

### 手順2: EXM-02 の手順2の残り

- 済んでいたもの（確かめた結果）: 2507314e7 に、仮の TSV を読む・照合するコードとテストの削除、「出演者」の列が無ければ止める、「タイトル（補正分）」の読み取り（前後の空白を除き、空・空白だけ・「-」なら自動の見出し）とそのテストがある。`data/exam_provisional.tsv` は 5f5da2d93 で消えている。自動の見出しのコードとテストは残してある
- 今回済ませたもの（542bdf18c）: `docs/notes/video-exam.md`（仮のファイルの記述を外し、読む列・補正の扱い・98GKK3KKYNk の公開日・期を出さないことを書いた。「4本が同じ見出し」の行は年度で見分けられるようになったので消した）、`docs/decisions/exam.md`（EXM-02 の決定 (1)〜(8) と、この指示の決定）、`exam/` の生成し直し

### 手順3: 確かめ

- `python3 -m unittest discover -s scripts/tests`: 1445件 OK
- `python3 scripts/regenerate.py all` を作業ブランチ（b349ae5ab）と cloudflare（58fa24fff）のクローンで同時に回した。どちらも終了コード 0。**どちらの木も HEAD から変わったファイルは0**（今回はデータの変化も無かった）。2つの木の違い（`diff -rq`、docs・scripts・data を除く）は `exam/` と手で直した `_redirects`・`style.css`・`check-assets.yml` だけ
- `exam/` の EXM-01 の版（a97973c28）からの差分: 13ファイル・170行。足した行はすべて新しい見出し（「【20xx年度前期/後期】」）を含む行（`<title>`・description・og:title・og:description・og:image:alt・JSON-LD・大見出し・紹介文・前後の動画・カード）。見出しの変化（12本全件）:

  | 動画ID | EXM-01 の見出し（自動） | 今の見出し（補正分） |
  |---|---|---|
  | -PoeE0Bm2sE | Mリーガー本田朋広プロにプロテストのことを聞きました！ | 【2026年度前期】Mリーガー本田朋広プロにプロテストのことを聞きました！ |
  | eKMCpGQKTYk | 日本プロ麻雀連盟プロテストを受けてみて【プロ雀士にインタビュー】 | 【2025年度後期】日本プロ麻雀連盟プロテストを受けてみて |
  | Lp8m7sAWT_k | 日本プロ麻雀連盟プロテストを受けてみて【プロ雀士にインタビュー】 | 【2025年度前期】日本プロ麻雀連盟プロテストを受けてみて |
  | jal-jnnwJb4 | 日本プロ麻雀連盟プロテストを受けてみて【プロ雀士にインタビュー】 | 【2024年度後期】日本プロ麻雀連盟プロテストを受けてみて |
  | RfZxTnSGnBw | 日本プロ麻雀連盟プロテストを受けてみて【プロ雀士にインタビュー】 | 【2024年度前期】日本プロ麻雀連盟プロテストを受けてみて |
  | lGPjKHDAlzw | プロテストについて新人連盟員に聞いてみた | 【2023年度後期】プロテストについて新人連盟員に聞いてみた |
  | 98GKK3KKYNk | 日本プロ麻雀連盟のプロテストを受験される方へ【ルール・マナーなど】 | 【2021年度前期】日本プロ麻雀連盟のプロテストを受験される方へ |
  | ix9xGkF5pJs | 第34期後期プロテスト募集受付中！ | 【2017年度後期】第34期後期プロテスト募集受付中！ |
  | gyYpx9xfrLA | 第34期プロテスト募集受付中！－職業 麻雀プロ編－ | 【2017年度前期】第34期プロテスト募集受付中！－職業 麻雀プロ編－ |
  | Esz7Mloebaw | 第34期プロテスト募集受付中！－強くなりたい編－ | 【2017年度前期】第34期プロテスト募集受付中！－強くなりたい編－ |
  | JTOTffVFqTg | 第33期後期プロテスト募集受付開始！（2017年2月28日まで） | 【2016年度後期】第33期後期プロテスト募集受付開始！ |
  | kzKMbbNvAcQ | 第33期プロテスト募集受付開始！（2016年8月31日まで） | 【2016年度前期】第33期プロテスト募集受付開始！ |

- `git merge-tree`: 未マージの全ブランチ8本（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-lvp・1018-log-367-migrate・1019-wrc14・1019-wrc17）と回し、cloudflare との組より増えた衝突は0
- 容量（`scripts/check_asset_limits.py`）: 配信ファイル数 4,182 → 4,195（+13）、`_redirects` 静的 113 → 115（+2）、最大のファイルは変わらず。`exam/` は 108,218 バイト。ガイド文書は handover.md 22,962・CLAUDE.md 24,786 バイト（どれも警告域未満。この指示では変えていない）
- プレビュー: b349ae5ab の「Workers Builds: mj」は報告の時点で `in_progress` のまま（docs/notes/cloud-sessions.md の既知の止まり方）。ブランチの別名 URL に `?v=b349ae5ab` を付けて取得し、`/exam/` に新しい見出し（「2026年度前期」）があること、各話が 200 であることで新しい版を確かめた
- headless Chromium で PC（1280×800）とスマホ（390×844）の全体の画像を `docs/logs/CHAT-1019-EXM-04/` に置いた（JPEG、計 2.2MB。一覧 `list-*`・出演者の多い回 lGPjKHDAlzw `most-cast-*`・出演者「-」の回 98GKK3KKYNk `no-cast-*`・いちばん古い回 kzKMbbNvAcQ `oldest-*`・最新の回 -PoeE0Bm2sE `latest-*`）。JS のエラー0、横のはみ出し無し
- 見出しの長さの見え方（画像で確かめた）: 切れ（省略・はみ出し）は無い。折り返しは、PC のヒーローで3行（例「【2026年度前期】Mリーガー本田朋広プロにプロテストのことを聞きました！」）、スマホのヒーローで3行、一覧のカードの題名は PC・スマホとも2行、前後の動画のカードは2行。EXM-01 の版と比べて1行増える所がある

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `/exam/` と各話5枚（PC・スマホ）。この版の印: 一覧の最新の動画の見出しが「【2026年度前期】Mリーガー本田朋広プロにプロテストのことを聞きました！」
- マージ: 未（平野さんの判断待ち。プレビューを見て決める）
- issue: #612
- 判断が必要なこと:
  - プレビューを見て、cloudflare へのマージ（未公開のまま入れる）を決めてほしい
  - 見出しの折り返し: 【年度】が付いた分、ヒーローは PC・スマホとも最大3行、カードと前後の動画は2行になる（切れは無い）。今のままでよいか。ほかの案: カードだけ【年度】を小さく別の行に出す（実装が増える）。推奨は今のまま
- 未確認の項目:
  - 平野さんの実機での見え方
  - b349ae5ab の「Workers Builds: mj」の check-run の完了（報告の時点で `in_progress`。別名 URL で新しい版が返ることは確かめた）
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
