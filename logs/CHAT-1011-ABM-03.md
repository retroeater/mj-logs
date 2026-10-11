# CHAT-1011-ABM-03

- 着手日時: 2026-10-11
- 対象issue: #425
- ブランチ: work/1011-abm
- 着手時HEAD: d43b0f9135a478e635f86f8c6849d64e806331b7

## 指示

【Claude作成】Claude Code 向け指示：#425 最強戦の年度・対局・卓の新しいページを試作する（未公開・プレビューで確かめる）
Chat-Ref: CHAT-1011-ABM-03
マージ: 判断待ちで止まる（平野さんがプレビューを見てから決める）
貼る時機: いつでも（平野さんが「最強戦」シートに M「動画URL」を足し、URL を1〜2卓入れた後なら、ABEMA のリンクも確かめられる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#425 に他セッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。

## 目的
#425 の決定（docs/decisions/saikyo.md の 2026-10-11〈CHAT-1011-ABM-02〉の節）のとおりに、新しい年度ページ・対局のページ・卓のページを試作し、プレビューで平野さんに見てもらう。今の公開中のページ（トップ・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）はこの指示では変えない。

### 決定（平野さん）
- この指示で新しく決めたことは無い。決定の正は docs/decisions/saikyo.md の CHAT-1011-ABM-02 の節（grill 1〜11）。そこと食い違う作りにしない

### 前提（チャット側。平野さんの決定ではない）
- 試作は新しい URL の場所に作る: `saikyo/<年度>/index.html`（新しい年度ページ）・`saikyo/<年度>/<対局識別子>.html`・`saikyo/<年度>/<卓のアンカー>.html`。どれも noindex（`NOINDEX_TAG` の定数にし、外すのを公開の指示の1か所の変更にする）、サイトマップ・`llms.txt`・navbar に載せず、今のトップ・年度ページからリンクしない（docs/new-page-checklist.md「段1」）。新しいページ同士のリンク（年度 → 対局 → 卓、パンくず）は張る
- 年度プルダウンとパンくずの年度の行き先は、試作の間は新しい年度ページ（`/saikyo/<年度>/index.html` か `/saikyo/<年度>/`。`_redirects` の行はこの指示では足さないので、プレビューで開ける方にする。どちらにしたかをログに書く）
- 未対局の対局・卓のページも作る（中身は「未対局」）
- 同じ日の2つ目の対局の枝番 `-2` は、新しいページにだけ使う。今の年度ページの `?match=` と共有ボタンは変えない（公開の指示で年度ページごと置き換えるため）
- ページ数の見込み: CHAT-1011-ABM-01 のログ「手順2」の表の計（対局 219・卓 643）と年度 16 から、新しいファイルは 878（シートの更新で 2026年度が増えることがあるので、対局・卓の増加は合わせて +10 まで許す。減るのは許さない）
- M「動画URL」列は、あれば使い、無ければ ABEMA のリンクは出さずに作る（どちらだったかをログに書く）

## 手順
1. 確かめる: `git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`saikyo/`・`style.css` の同じ関数・同じ行を変えていないか。「最強戦」シートを生成と同じ経路で読み、見出し（M 列の有無）・行数・M 列に値のある卓の数と、4行で値が食い違う卓を書く
2. 作る: `generate_saikyo_pages.py`（と必要なら `assets/saikyo.js`・`style.css`）に、上の3種のページの生成を足す。中身は決定 grill 1・3〜6・10 のとおり（年度ページは勝ち上がり者と「未対局」、選手名の絞り込みなし、対局ごとの共有ボタンなし。対局のページは今の `?match=` の表示と同じで卓の見出しから卓のページへ。卓のページは4人・「ABEMA で見る」・同じ対局のほかの卓と対局のページへの導線。個別ページに共有ボタンなし。新しい年度ページの JS は `#<卓のアンカー>`・`#match-…` を卓・対局のページへ `location.replace`）。canonical は自分自身、OGP 画像は年度の今の画像、題名と説明はページごと。固定バー・`mj-saikyo-page` のクラスは今の年度ページと同じ。テストを `scripts/tests/` に足し、`python3 -m unittest discover -s scripts/tests` を通す。`regenerate.py` の `saikyo_pages` の対象パスで新しいファイルが拾われ、毎朝の再生成で消されないことを確かめる
3. 確かめてプレビューに出す: 生成して、(a) 新しいファイルの数（見込みとの差）、(b) 今の公開中のファイル（トップ・`saikyo/<年度>.html`・`sitemap-saikyo.xml`）に差分が無いこと、(c) 新しいページの内部リンクがすべて実在のファイルを指すこと、(d) 各ページの id の重複が無いこと、(e) 同じ日の2対局（例: 2025 の `20251214` と `20251214-2`）がそれぞれのページになり中身が違うこと、(f) 配信ファイル数（`check_asset_limits.py`）、(g) headless Chrome でスマホ幅と PC 幅の見え方（崩れの有無を文で）を確かめてログに書く。push してプレビューを作り、ターミナルの最終報告の「確認用:」に、2025年度の新しい年度ページ・`20251214` と `20251214-2` の対局のページ・卓のページ1つ・2026年度の新しい年度ページ（未対局を含む）・`#<卓のアンカー>` 付きの新しい年度ページの URL を並べる

## 止まる条件
- 決定（docs/decisions/saikyo.md の CHAT-1011-ABM-02 の節）と食い違う作りが要る（作らずに報告する）
- 新しいファイルの数が見込みの 878 より少ない、または +10 を超えて多い
- 今の公開中のファイルに差分が出る（シートの更新による差分は、その種類を書いたうえで止まらない）
- 未マージの work/ ブランチが `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- シートを2回読んで行数が違う
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d43b0f91）: https://github.com/retroeater/mj-logs/tree/main/guide/d43b0f91

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d43b0f91/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
