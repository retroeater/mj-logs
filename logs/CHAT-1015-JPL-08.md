# CHAT-1015-JPL-08

- 着手日時: 2026-10-15
- 対象issue: #523
- ブランチ: work/1015-jpl
- 着手時HEAD: 1854428a4

## 指示

【Claude作成】Claude Code 向け指示：JPL-07 の続き。WRC 2大会の新名称を ryoei.pro の表示すべて（過去の大会・予定・成績・動画の題名を含む）に効かせ、入力（YouTube 概要欄・連盟の予定表など）の旧名は読み替える（#523）。判断待ちで止まる
Chat-Ref: CHAT-1015-JPL-08
マージ: 判断待ちで止まる（表示の変化の範囲が広いため、差分の内訳とプレビューを平野さんが見てから決める。cloudflare へは入れない）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-jpl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1015-JPL-07.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-07 の `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JPL-08` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

## 目的
WRC 2大会の改称を、ryoei.pro の表示すべてに効かせる。JPL-05 で入れた読み替え（`taikai_names` など）を、まだ旧名が出ている所（JPL-07 の「mj_放送対局」の仮の予定、#523 本文の「残り」）まで広げる。元のデータ（YouTube の概要欄・題名、連盟の予定表、シートの旧名の行）は書き換えず、ryoei.pro 側で読み替える。

### 決定（2026-10-15、平野さん）
- 「JPML WRCリーグ」「JPML WRC-Rリーグ」は新名称「WRCリーグ」「WRC-Rリーグ」が適用された。今後は、過去の大会・予定・成績なども含め、ryoei.pro での表示はすべて新名称にする
- YouTube の概要欄など（過去の分）は変わらないので、ryoei.pro 側で読み替える
- （JPL-07 の「判断が必要なこと」2点目〈仮の予定の件名〉は、上の決定で表示を新名にそろえる。案 (a)〜(c) の選び方は下の前提）
- video_mtsuku の再生成の停止（JPL-07 の1点目）は MTK のチャットで対応中。この指示では扱わない

### 前提（チャット側。平野さんの決定ではない）
- 方針の案: 入力（連盟の予定表の件名、YouTube の題名・概要欄、シートの旧名の行）は旧名・新名のどちらでも同じ大会に当たるようにし、出力（ページの文字・meta・OGP・`search.json`・`llms.txt`・カレンダー「mj_放送対局」の件名と説明・予定表シート【2】の「大会」列など ryoei.pro が書くもの）は新名にそろえる。読み替えは JPL-05 の仕組み（`taikai_names.current_title()` など。名前は実物で確かめる）に寄せ、別の対応表を増やさない
- 動画の題名（YouTube の題名を /live・title/ の期ページなどにそのまま出している所）も、表示では旧名を新名に置き換える（決定の「すべて新名称」に含まれるとみている。置き換える所と件数を報告に書く。要確認）
- 予定表シート【2】の「大会」列は ryoei.pro が書く出力なので新名にする（JPL-07 の案 (b) に近い。`yotei.EVENTS` の照合は旧名・新名の両方に当たるように）。カレンダーの件名は仮の予定・枠の予定とも新名（案 (a) を含む）
- #523 本文の「残り」の手書きのページ・メタ（`apply_page_meta.py`・`llms.txt`・`index.html`・wrc_*.html など）の「JPML WRC」も新名に直す（JPL-05 の報告の列挙。実物で足し引きする）
- 新しい名前に「JPML」が付かないことで WRC（リーチ麻雀世界選手権、slug wrc）と紛れる箇所が出たら、置き換えずに一覧にして止まらずに報告する（表示の文言の判断は平野さん）
- slug・URL（jwrc・jwrc-r、wrc_*.html）は変えない

## 手順
1. 洗い出す: リポジトリ全体（生成スクリプト・手書きのページ・テンプレート・テスト・docs 以外の静的ファイル）と生成物で「JPML WRC」「JPML WRC-R」を検索し、入力の照合／出力の表示／手書きの文言／テストに分けて、ファイルと件数を表で `## 経過` に書く。カレンダー「mj_放送対局」と予定表【2】で旧名が出ている件数も書く
2. 直す: 上の前提の方針で、入力は旧名・新名の両方に当たり、出力は新名になるようにする。テストを足す（旧名の入力が新名で表示される、新名の入力も同じ大会に当たる、WRC〈世界選手権〉と混ざらない、slug・URL が変わらない）。`python3 -m unittest discover -s scripts/tests` を通す。影響のある生成を走らせ、origin/cloudflare との差分を種類に分けて書く（ページ数、「JPML WRC(-R)リーグ」→「WRC(-R)リーグ」の置き換えの件数、それ以外の差分は全件）。カレンダー同期と予定表【2】の書き込みは dry-run（書き込みなし）で、変わる予定・行の件数と例を書く
3. 確かめる: Workers Builds のプレビュー（上限15分）を headless Chromium で開き、title/jwrc/・title/jwrc-r/ の入口と期ページ1つずつ、/live の WRC の回1つ、wrc_*.html、index.html の該当箇所の表示を確かめて書く。平野さんに見てもらう URL の一覧（確認用 URL からの相対パス）を報告に書く。docs/notes/title-pages.md（と /live・予定表の文書）に方針を短く書き、docs/decisions/title.md に上の「決定」を足す。#523 に結果をコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）

## 止まる条件
- 0章で JPL-07 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
- テストが通らない、または生成が止まる
- 差分に「旧名→新名の置き換え」と生成日時など毎回変わるもの以外が出て、シートの変化で説明できない（全件書いて止まる。直さない）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、変えるファイルの同じ関数・行を変えている、または取り込みで衝突する（重なるファイルだけ外して残りを進め、外したものと理由を報告に書く。全部が重なるときだけ止まる。MTK のブランチの mtsuku 関係のファイルは触らない）
- cloudflare への push が求められる状況になった（しない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、マージの可否と、見てもらう URL、WRC（世界選手権）と紛れる箇所の一覧（あれば）を書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JPL-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JPL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1015-jpl/docs/logs/CHAT-1015-JPL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-jpl
- 確認用URL:
- マージ:
- issue: #523
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1854428a）: https://github.com/retroeater/mj-logs/tree/main/guide/1854428a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d12b1f2e.md
