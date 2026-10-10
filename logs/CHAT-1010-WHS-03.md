# CHAT-1010-WHS-03

- 着手日時: 2026-10-10
- 対象issue: #194
- ブランチ: work/1010-whs
- 着手時HEAD: 57963e3e

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」シートの移動・列の変更（平野さんが実施）に生成の読み先を合わせ、JSON に無い回は外して生成する形にする。作業ブランチでプレビューまで出して止まる（#194 の前段） Chat-Ref: CHAT-1010-WHS-03 マージ: 判断待ちで止まる（シートの変化による差分を平野さんがプレビューで見てから、別の指示でマージ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが「帰り道」シートを別のブックへ移し、列を直した。生成（一覧・各話・OGP 画像・YouTube の情報の取得）が新しいシートを読むようにする。あわせて、決定3（JSON に無い回は外して生成する）の生成側だけを入れる。自動の取り込み（ワークフロー・知らせ・OGP の自動生成）は次の指示で行う。
決定（2026-10-10、平野さん）
シートの変更（平野さんが行った）

* 「帰り道」シートを次のブックへ移した: https://docs.google.com/spreadsheets/d/10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34/
* 「備考」列を消した。「X ID」「公開日」「画像URL」列を消した。列の順番を入れ替えた
* 「URL」は「動画ID」に、「決勝動画URL」は「決勝動画ID」に列名を変え、値を URL ではなく ID にした
* 「決勝動画ID」で、決勝動画が無いことをはっきりさせる回は、空ではなく「-」にした（空は「まだ埋めていない」）
* `6WAPjcxT78A`（第3期JPML WRC-Rリーグ 勝又健志）をシートに足した（帰り道の回として載せる）
* 「帝王戦」2行・「世界麻雀TOKYO2025」1行のタイトルを title/ の名前に直した。三浦智博の十段戦2行の決勝動画を埋めた。ワールド・リーチ・プロ（タマシュ・エルドス）は「-」にした

#194・#340 の grill の続き（CHAT-1010-WHS-02 の「判断が必要なこと」への回答。WHS-02 で docs/decisions に記録した決定に足す・置き換える）

* 新しい回は、/live の層1の題名に「帰り道」か「ついて」を含むもので拾う（決定5の語を広げる）。拾えない特別編（ワールド・リーチ・プロの回など）は手で直す
* 決勝動画の候補は、title/ の期ページの「決勝ライブ」から採る。複数日なら最終日、冒頭版と全編があれば全編（title/ と同じ）。見つからないときは空にして、そう書く（決定8の「複数あるときは空」を置き換える）
* 知らせの1行のタイトルは、title/ の大会名（title/ の別名の対応表）で作る。シートに登録するときに平野さんが帰り道用に直す
* 決勝動画IDが空の回の知らせは、空の回が新しく増えたときだけ常設 issue に書き、失敗の扱いにしない。「-」の回は知らせない（「-」にすれば、その回の知らせは終わる）
* 知らせの1行の表示の列は `Y` で出す
* メンバー限定の帰り道の回も拾う

前提（チャット側。平野さんの決定ではない）

* チャット側は新しいブックを読めていない（サンドボックスから docs.google.com に届かない）。列の並び・見出し・表示の列（旧 G列 `Y`）の有無・タブ名・行数は（要確認）
* 今の読み先: `scripts/lib/wayhome.py` の `SPREADSHEET_ID`・`SHEET_NAME`・`COLUMNS`（列記号 A・D・E・H）・`QUERY`（`WHERE G = "Y"`）。使う所は `generate_video_wayhome.py`・`generate_wayhome_episodes.py`・`fetch_youtube_meta.py`・`build_wayhome_ogp.py`（2026-10-10 に cloudflare 647a8db で読んだ。ほかにもあるかは洗い出す）。`COLUMNS` の注記のとおり列記号で読んでいるため、列の削除・並べ替えで別の値が入る。今回は見出しの名前で引く形にするのがよいと考えている（`scripts/lib/` にほかのシートの見出し照合の仕組みがあれば借りる）
* `6WAPjcxT78A` は `data/youtube_meta.json` に無いので、今の作りでは生成が止まる。この指示で決定3の生成側（JSON に無い回はその回だけ外して生成し、外した回を標準出力と GitHub Actions の警告〈`::warning::`〉に出す）を入れる。ワークフローを失敗の扱いにする部分と、YouTube の情報の取得は次の指示（ワークフローの変更）で入れる。それまでは、この回は外れたまま載らない
* 決勝動画IDの「-」は「決勝戦を見る」を出さない（空と同じ表示）。区別が要るのは次の指示の知らせだけ
* 見込み: 一覧・各話で変わるのは、シートの変化で説明できるものだけ（タイトルを直した3回の題・説明・og:title・パンくず、決勝動画を埋めた2回の「決勝戦を見る」、決勝動画を全編に直した回があればそのリンク）。各話のページ数は今と同じ39（`6WAPjcxT78A` は外れるため）。一覧の OGP 画像の名前は変わらない（最新話は変わらない）
* `regenerate-page.yml` は「辞書」シートの件で止まっている（辞書のチャットで対応中）。`regenerate.py` は帰り道の2ページ（`video_wayhome`・`wayhome_episodes`）だけを流す
* CHAT-1010-WHS-01 のログの状態の行が「判断待ち / 辞書の件の続き: CHAT-1008-DIC-17」になっていて、`docs/notes/branch-operations.md`「作業ログの寿命」の決まった形（` / 続き: CHAT-…`）と違う（要確認）

手順

1. 新しいシートを読む: 生成と同じ経路（公開シートの読み取り）で新しいブックの帰り道のタブを読み、見出し・列の並び・行数・表示の列の値の内訳をログに書く。旧ブックの帰り道のタブがまだ読めるかも書く。今の公開物（39話）と行ごとに突き合わせ、差（足された回・タイトル・決勝動画ID の変化・消えた回）を表にする。上の「決定」と食い違う差（説明できない差）があれば、そこで止まる
2. 読み先を直す: 4本のスクリプト（ほかにあれば全部）が新しいブックを見出しの名前で読むようにし、動画ID・決勝動画ID（「-」を含む）から今と同じ URL を組み立てる。決定3の生成側を入れる。テストを足す・直す（見出しの名前で引くこと、「-」、JSON に無い回を外すこと）。`docs/notes/video-wayhome.md`（「新しい回を追加する手順」を含む）と `docs/notes/static-generation.md` の帰り道の記述を直す（今の内容を読んでから）。上の「決定」を `docs/decisions/` の合う分野（WHS-02 で書いた所）に足す。WHS-01 のログの状態の行を決まった形（`判断待ち / 続き: CHAT-1008-DIC-17`）に直す
3. 生成して止まる: `regenerate.py` で帰り道の2ページだけを生成し、差分を種類ごとに数えてログに書く（見込みと比べる）。push して Cloudflare のプレビューを出す。平野さんが見るページは、一覧・タイトルを直した回1つ・決勝動画を埋めた回1つ（確認用 URL は最終報告にだけ書く）。#194 に進みをコメントする

止まる条件

* 新しいシートが読めない（共有の設定など）。読めない理由を書いて止まる
* 手順1で、決定とシートの変化で説明できない差がある（例: 回が消えた、表示の列が無い、行数が 40 と違う）
* 一覧・各話の生成物に、見込み（上の「前提」）で説明できない差がある。各話のページ数が 39 でない
* ほかのページ（title/・jpml_pros など）が帰り道のシートを読んでいて、この指示の変更でそのページの生成物が変わる（変えずに止まる）
* 未マージのブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）が、`scripts/lib/wayhome.py` か上の4本のスクリプトの同じ関数を変えている、または取り込みで衝突する
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-03"` は0件
- 作業ブランチ: ローカルの `work/1010-whs`（963efe0d）は `origin/cloudflare` の祖先、リモートもマージ済み → `git merge --ff-only origin/cloudflare`（57963e3e）
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 未
- issue: #194
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 57963e3e）: https://github.com/retroeater/mj-logs/tree/main/guide/57963e3e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/57963e3e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
