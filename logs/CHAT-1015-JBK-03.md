# CHAT-1015-JBK-03

- 着手日時: 2026-10-15
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: 6b3a300ddbca5c26b3c67f7cb69936a261bdf434

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（生徒は載せない、データをシートのタブで持つ形に）
Chat-Ref: CHAT-1015-JBK-03
マージ: 判断待ちで止まる（平野さんがプレビューで仕様を確かめてから決める）
貼る時機: CHAT-1015-JBK-02 を貼ったのと同じセッションに、JBK-02 の実行中でも貼ってよい（JBK-02 の最終報告の後に取りかかる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1015-JBK-02 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1015-JBK-02 のログの `## 報告` があり、プレビューまで出せていることを確かめる（無い・止まっていれば何もせず止まる）。JBK-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JBK-03` を足す。
   **平野さんは不在のことがある。質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に一覧で書く。**

## 目的
JBK-01 の報告の「判断が必要なこと」のうち、生徒・データの持ち方・MC・part1〜33 に平野さんの答えが出たので反映する。

### 決定（2026-10-15、平野さん）
- 生徒の名前は載せない。ただし、あとで「プロになった人だけ載せる」に広げられる作りにする
- データは Mつくと同じく、シートのタブで持つ（平野さんが持つ）
- MC「大和」の26本（JBK-01 の報告の一覧）は「大和」でよいはず。要確認（平野さんが後日確かめる）
- part1〜33 が層1に無い件は、平野さんが後日担当者に確かめる

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 生徒: タブに生徒の列（生徒1〜N）を置き、値が「-」・空の回は生徒の節を出さない（今は全回空なので、どの回にも出ない）。名前が入れば JBK-02 の写真カードの形で出る（同じ写真の規則）。一覧のヒーローの文・検索欄・紹介文から生徒への言及を外す（例「…講師の名前で振り返れます」）
- タブ: Mつくと同じブック（`lib/mtsuku.py` が読むブック。指示文・ログにブックの ID・URL を書かない）に、タブ「女流勉強会」を平野さんが作る想定。列の案: 動画ID・回・講師1〜3・MC・生徒1〜N（ほかに要る列があれば足す）。値の無いセルは「-」（Mつくと同じ）。見出しの名前で読む
- 題名・公開日（配信開始の JST の日付）・再生時間・サムネイル・メンバー限定かどうかは、タブに持たせず、動画IDで層1から引く案（Mつくは層1に無いチャンネルなので手で入れたが、女流勉強会は層1にある）。タブに書いた動画IDが層1に無ければ生成を止める。手で持つ方が良いと判断したら、理由を報告に書いてその形にしてよい
- 平野さんがタブをまだ作っていなければ: 読み取りはタブの形で作り、テストは仮のファイルで通し、プレビューは JBK-01 の仮のファイル（またはそれをタブの列に合わせたもの）から生成する。タブに貼る内容（見出し＋全行、タブ区切り）を `## 経過` に書く（ログは public。選手の名前は出してよいが、ブックの ID・URL は書かない）
- 新しい回の知らせ（帰り道のように常設 issue で知らせる、など）は作らない。案を報告に書く
- MC「大和」と part1〜33 は、作る issue（#575）に「平野さんが確かめる」項目としてコメントする（この指示で解決しない）
- 写真の規則・`.github/workflows/` を変えないこと・帰り道・Mつく・title/ の生成物の差分0は JBK-01・02 のまま

## 手順
1. 上の決定・前提で、タブの読み取り（`lib/mtsuku.py` のような読み方。共通化してよい）・生徒の節の出し分け・文言を直し、テストを直して足す。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 375px・PC 幅で一覧・最新話・最古の回・メンバー限定の回（あれば）を headless Chrome で見て所見をログに書き、プレビュー（確認用 URL）を出す
3. 決定を `docs/decisions/`（JBK の分野のファイル）に、変わった仕様を JBK の実装記録に書く（今の内容を読み、古い記述は置き換える）。#575 に上の確かめる項目と進み具合をコメントする

## 止まる条件
- 0章の JBK-02 の報告が無い・プレビューまで出せていない
- タブ「女流勉強会」があるのに、行数が層1の本数と食い違い、読み直すたびに変わる（行数を書いて止まる）
- 帰り道・Mつく・title/ の生成物に差分が出て、共通化の直しで消せない
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 取り込みで生成物でない文書が衝突した: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、JBK-02 から引き継いだ残りの仮置きとこの指示の仮置きを1つの一覧にし、平野さんの手作業（タブを作って貼る〈貼る内容は `## 経過` のどこか〉、MC「大和」・part1〜33 の確認）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JBK-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JBK-03 を書く

平野さんは不在のことがあるため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1015-JBK-03"` と `docs/logs/` に無し
- 作業ブランチ: ローカル work/JBK はリモートと一致（6b3a300dd）。`origin/cloudflare` は祖先（取り込み不要）

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1015-JBK-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f6b567c4）: https://github.com/retroeater/mj-logs/tree/main/guide/f6b567c4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f6b567c4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
