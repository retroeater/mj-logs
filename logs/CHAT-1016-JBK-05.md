# CHAT-1016-JBK-05

- 着手日時: 2026-10-16
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 2180084e05788a5074b0aedb5aa3cc6e68fc1b02

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（新しいブックの「女流勉強会」タブから読む、仮のファイルをやめる）
Chat-Ref: CHAT-1016-JBK-05
マージ: 判断待ちで止まる（平野さんがプレビューで仕様を確かめてから決める）
貼る時機: CHAT-1016-VSH-01（帰り道・Mつくの読む先の切り替え）が cloudflare にマージされた後。JBK-01〜04 と同じセッションに貼る
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1015-JBK-04 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1016-VSH-01 のログの `## 報告` でマージ済みであることを確かめる（マージされていなければ何もせず止まる）。CHAT-1015-JBK-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1016-JBK-05` を足す。

## 目的
平野さんが新しいブックに「女流勉強会」タブを作った。仮のファイルをやめ、そのタブから読む形にする。

### 決定（2026-10-16、平野さん）
- 「女流勉強会」タブを、帰り道・Mつくと同じ新しいブック（CHAT-1016-VSH-01 で定数にしたもの）に作った。サービスアカウントに編集者の権限を付けた
- 列の順番を入れ替えた
- 講師は「講師」の1つのセルに「、」区切りで名前を並べる。生徒も「生徒」の1つのセルに「、」区切りで並べる

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 取り込み: origin/cloudflare（VSH-01 のマージを含む）を merge で取り込む。VSH-01 と同じ箇所（`lib/wayhome.py`・`lib/mtsuku.py` の読み取りなど）で衝突したら、両方の変更を残す形で解いてよい（VSH-01 の読む先の切り替えを正とし、女流勉強会の変更を載せ直す）。解いた後の該当箇所をログに引用し、テストと帰り道・Mつく・title/ の生成物の差分0で確かめる。両立しない衝突は解かずに止まる
- 読み取り: タブを見出しの名前で読む（列の並びに依存しない）。「講師」「生徒」は「、」で分け、前後の空白を除く。値が「-」・空なら0人。ほかの列（動画ID・回・MC など）の見出しは実物で確かめる。読み取りは VSH-01 の定数のブックから
- タブの中身を、仮のファイル（`data/joryu_benkyokai_provisional.tsv`）と比べる（動画IDの集合・講師・MC）。違いは平野さんの直しとみなして進め、違う行をすべてログに書く。動画IDが層1に無い行があれば生成を止める（JBK-03 の作りのまま）
- 仮のファイルと「タブが無ければ仮のファイルを読む」扱い（JBK-03 の仮置き3）は消す。タブが読めなければ生成を止める。抽出のスクリプト（`scripts/extract_benkyokai_provisional.py`）は、新しい回の知らせ（未決）に使える見込みがあれば残し、そうでなければ消す（どちらにしたかを報告に書く）
- 生徒は今も載せない決定のまま（名前が入った回だけ節が出る作りは JBK-03 のまま）
- 写真の規則は帰り道・Mつくと同じ（CHAT-1015-MTK-12 の緩和を含む）。`.github/workflows/` は変えない

## 手順
1. 取り込み（上の前提）。タブを読み、仮のファイルとの比較をログに書く
2. 読み取りを直し、仮のファイルと扱いを消し、テストを直す。`regenerate.py --check` に女流勉強会のページを足して通し、`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる。女流勉強会のページを生成し、375px と PC 幅で一覧・最新話・最古の回を headless Chrome で見て所見を書き、プレビュー（確認用 URL）を出す
3. 決定を `docs/decisions/`（JBK の分野のファイル）に、変わった仕様を JBK の実装記録に書く（今の内容を読み、古い記述は置き換える）。#575 に進み具合をコメントする

## 止まる条件
- 0章の VSH-01 がマージされていない
- 「女流勉強会」タブが読めない、または行数が読み直すたびに変わる（行数を書いて止まる）
- 取り込みで両立しない衝突が出た
- 帰り道・Mつく・title/ の生成物に差分が出る
- 未マージの work/ ブランチ（work/JBK 以外）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、JBK-04 から引き継いだ残りの仮置きとこの指示の仮置きを1つの一覧にし、平野さんの手作業（MC「大和」・part1〜33 の確認）を引き継いで書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1016-JBK-05"` と `docs/logs/` に無し（JBK は JBK-01〜04 と同じセッションの続き）
- 0: CHAT-1016-VSH-01 のログの `## 報告` は「中断（エラー） / 続き: CHAT-1016-VSH-02」でマージ未。続きの CHAT-1016-VSH-02 が読む先の切り替え（e4943b7de「chore: read wayhome and mtsuku tabs from the video book」、`scripts/lib/video_book.py` に新しいブックの定数）を cloudflare にマージ済み（VSH-02 の報告「マージ: 済（8e48fa5f6、fast-forward）」）。
  指示の字面（VSH-01 がマージ済み）とは食い違うため、着手前に平野さんに質問し、「VSH-02 のマージで進める」の回答を得て着手した
- 作業ブランチ: ローカル work/JBK はリモートと一致（2180084e0）。`origin/cloudflare` を取り込む

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82bde681）: https://github.com/retroeater/mj-logs/tree/main/guide/82bde681

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82bde681/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
