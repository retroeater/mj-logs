# CHAT-1009-STL-02

- 着手日時: 2026-10-09
- 対象issue: #475
- ブランチ: work/1009-stl
- 着手時HEAD: 1ddf8321

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」「別名」の使われていない登録を #475 の知らせに並べる（実装・試運転・文書。マージ前に止まる） Chat-Ref: CHAT-1009-STL-02 マージ: 判断待ちで止まる 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1009-stl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-stl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-STL-01 のログの `## 経過`（手順1後半の表・手順2・手順3）と `## 報告` を読む。STL-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-STL-02` を足す。

目的
CHAT-1009-STL-01 の調査をもとに、「連盟プロ以外」「別名」（区分 `訂正`）の登録のうち、もうどこでも使われていないものを、#475 の毎日の知らせに並べて出す。手動実行でも出せるようにする。行を消すのは平野さんの手作業で、仕組みはシートを変えない。
決定（2026-10-09、平野さん）

* 「連盟プロ以外」「別名」の登録のうち、どこでも使われていないものを、#475 の毎日のコメントに並べて書く。手動実行でも出せるようにする。「別名」は区分 `訂正` の行だけを対象にする（`登録名変更` は対象外）（STL-01 で記録済み）
* 載せ方: 未使用の一覧が前の回から変わった日だけ、未登録の名前の知らせと同じコメントに「使われていない登録」として全件を並べる
* 手動実行: 入力 `unused_report` を足す。手動で回したときは、変わっていなくても今の一覧をコメントする
* 検査が失敗したとき: 取り込み・未登録の名前の知らせは止めない。コメントには「検査できなかった」の1行だけを書く
* 「タイトル」「鳳凰」の表示しない行（「タイトル」の表示≠Y、「鳳凰」の表示 N・LEAGUES 外）も「使われている」に数える。凍結中の「書籍」は数えない
* #475 の本文に、行を消すのは平野さんの手作業であることと、概要欄の直しが届くのは毎週水曜の取り込み（すぐ届かせるなら手動実行で `apply` と `verify` を付ける）であることを書き足す
* 実装のマージは判断待ちで止める（試運転の結果を見てから決める）

前提（チャット側。平野さんの決定ではない）

* 判定の入力と定義は STL-01 の「判定」と「判断が必要なこと」の案に、上の決定（表示しない行を足す）を加えたもの: 層1から別名を当てずに抜いた名前（`build_rows(videos)` を book なしで。【2】のタブは訂正の後なので使わない）、【3】の対局者・実況・解説（掲載を問わない）、カレンダーの抜き出し（全動画で近似）、「タイトル」（全行）、「テスト」（J=Y）、「最強戦」（K=Y）、「鳳凰」（全行）。`訂正` の行は変換前がこの集合にあれば使われている。「連盟プロ以外」の行は名前がこの集合にあるか、使われている `訂正` または `登録名変更` の行の変換後なら使われている（STL-01 の案。旧名で出ている人の写真・X に要るため）
* 前回の一覧は、bot のコメントの末尾の隠した注記（HTML のコメント）に書き、次の実行で最後の bot のコメントから読む案（STL-01）。実物に合わせて別の持ち方にしてよい（理由を報告に書く）
* 検査は層2・#475 の未登録の知らせ・層3の後の別のステップにし、`continue-on-error` で実行を失敗にしない案（STL-01）。失敗が続く間、「検査できなかった」の1行は初回の日だけ書く
* 一覧の各行は、行番号・名前（別名は変換前→変換後）・所属団体と所属補足（別名は区分と備考）。一覧は「消してよい候補」で、他団体の現役プロなど今後また出る人も入る（コメントの案内の文にそう書く）
* STL-01 の試算は「別名」2行・「連盟プロ以外」26行（読む範囲を変えた差は 0〜3行）。今日までに平野さんがシートを直していれば変わる

手順

1. 実装とテスト: 着手時に `git branch -r --no-merged origin/cloudflare` で、層2・`names.py`・`live_candidate`・`update-live-channel.yml`・#475 の知らせに触れるブランチが無いことを確かめる。判定の部品（読み込み・判定・前回との比較・コメントの文面）を作り、既存の関数を流用するときは import・参照している所をすべて挙げ、既存の関数の振る舞いを変えない（変えるなら、マージ前に全ページを再生成して差分が無いことを確かめる）。テストを足す（判定の定義、`訂正` だけを数える、`登録名変更` の変換後、一覧が変わらない日は書かない、`unused_report` のときは書く、検査が失敗したときの1行）。`update-live-channel.yml` に入力 `unused_report` とステップを足す。docs/notes/branch-operations.md「ワークフローを変更したとき」を読んで従う
2. 試運転（書かない）: 作業ブランチで `update-live-channel.yml` を `apply` を付けずに `unused_report` を付けて手動実行し（15分を上限に待つ。超えたらその時点の状態を書き「未確認の項目」に回す）、実行サマリに出た一覧（件数・全行）をログに引用する。シート・#475・カレンダー・cloudflare には何も書かれていないことも確かめる。セッションのトークンで起動が403なら、平野さんに GitHub の画面の「Run workflow」を頼んで止まる
3. 文書: docs/notes/live-channel-write.md（#475 の知らせの節に「使われていない登録」と入力 `unused_report`、概要欄の直しが届く日）、docs/notes/live-page-design.md「1-5」（「使われている」の定義と読む利用先）、`update-live-channel.yml` の冒頭のコメント、docs/decisions/live.md を、それぞれ今の内容を読んでから直す。#475 の本文に書き足す文面はログに案として書き、本文はまだ変えない（マージの後に書く）

止まる条件

* 手順1で、重なる未マージのブランチがある
* 既存の関数の振る舞いを変える必要があり、全ページの再生成で差分が出る
* 試運転の一覧の件数が STL-01 の試算（「別名」2行・「連盟プロ以外」26行）から、それぞれ ±3行を超えて違う（表示しない行を足したことと、シートの直しで説明できる差は止めずに理由を書く）
* 試運転でシート・#475・カレンダー・cloudflare のどれかに書き込みがあった
* 追記先の文書が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい。どう処理したかを報告に書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れずに判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-STL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-STL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1009-stl
- ログ: https://github.com/retroeater/mj/blob/work/1009-stl/docs/logs/CHAT-1009-STL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-stl
- 確認用URL: 作業中
- マージ: 未
- issue: #475
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6f2125c）: https://github.com/retroeater/mj-logs/tree/main/guide/f6f2125c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6f2125c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eabe7134.md
