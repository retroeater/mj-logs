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

## 報告

- 状態: 作業中
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし
- マージ: 未
- issue: #612
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 58fa24ff）: https://github.com/retroeater/mj-logs/tree/main/guide/58fa24ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/58fa24ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/10e55a48.md
