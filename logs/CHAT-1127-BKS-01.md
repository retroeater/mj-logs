# CHAT-1127-BKS-01

- 着手日時: 2026-11-27
- 対象issue: #97, #568
- ブランチ: work/1127-bks
- 着手時HEAD: e69c8e6c4af96f204b7ea2ca35bb1e02fc850093

## 指示

【Claude作成】Claude Code 向け指示：メニュー「リソース」の「書籍」（booklog への外部リンク）を外し、/resource_books.html の転送を「別名」から棚卸しの対象へ移す
Chat-Ref: CHAT-1127-BKS-01
マージ: 承認済み（チャットで）。ただし下の「止まる条件」を1つでも満たさなければマージせず止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1127-bks の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1127-bks を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-bks origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「書籍」のリニューアルに着手するにあたり、booklog は今後使わない。メニュー（`navbar.js`）の「リソース」にある「書籍」（`/resource_books.html` → booklog への 301）を外し、メニューが使わなくなった転送 `/resource_books.html` を docs/notes/redirects.md の「別名」から「棚卸しの対象」へ移す。

### 決定（2026-11-27、平野さん）
- 「書籍」のリニューアルに着手する。booklog は今後使用しない
- メニューから、リソース「書籍」の外部リンク（booklog）を廃止する
- `/resource_books.html` → booklog の 301 は今は消さず、「別名」から「棚卸しの対象」へ移し、四半期の棚卸しの削除の基準（直近90日で流入0）で消す（2026-10-14〈CHAT-1014-RDR-03〉の「`/resource_books.html` は『別名』として棚卸しの対象外のままにする」を、`/resource_books.html` についてだけ置き換える。`/resource_calendar.html` は変えない）
- マージは、下の「止まる条件」に当たらなければプレビューの確認なしで行ってよい

### 前提（チャット側。平野さんの決定ではない）
- 「書籍」の項目は `navbar.js` の `document.write` の1か所（`mjNavBand()` を含め、旧い濃色のナビと新しい形の帯・シートの両方がそこから読む）にあり、href は `/resource_books.html`（docs/notes/design.md「共通ナビ（新しい形）」・docs/notes/redirects.md「別名」から。（要確認））
- 棚卸しの対象の表に移す行の「入れた日」は 2026-09-07 のまま、「issue」は今の注記を短くして #568 を添え、「最後の棚卸し」は他の行にそろえて「未（初回 2027-01 上旬）」とする案。表の列の形・テスト（`scripts/tests/test_redirects_inventory.py`・`test_fetch_redirect_counts.py`）に合わせて変えてよい。「別名」の節の「`/resource_calendar.html`・`/resource_books.html` をここに置くのはチャット側の案…」の文は `/resource_calendar.html` だけに直す
- docs/notes/books-freeze.md の「他団体を含むプロの書籍はこれまでどおり booklog（`/resource_books.html`）とする」「`/resource_books.html` は引き続き booklog へ301」は、2026-11-27 の決定で置き換わった旨を同じ箇所に添える（凍結時点の記録なので消さない）。docs/new-page-checklist.md の実例の表は時点の記録なので変えない
- 決定の書き先は docs/decisions/cloudflare.md（2026-10-14 の RDR-03 の節がある）の案。docs/decisions/README.md に従って別のファイルがよければそちらでよい
- 関係する issue: #97（書籍ページの本体、保留）と #568（転送の棚卸しの常設）。どちらも本文・状態・ラベルは変えない

## 手順
1. #97・#568 に他セッションの着手中コメントが無いことと、`git branch -r --no-merged origin/cloudflare` の各ブランチが `navbar.js`・docs/notes/redirects.md の同じ箇所を変えていないことを確かめ、#97 に着手中のコメントを書く。リポジトリ全体で `resource_books` と `booklog` を検索し、ヒットした箇所をすべてログの `## 経過` に表で書く（ファイル・行・中身）。
2. `navbar.js` から「書籍」の項目だけを消す（ほかの項目・まとまりの並びは変えない）。「リソース」のまとまりに残る項目の一覧（消す前と後）をログに書く。docs/notes/redirects.md・docs/notes/books-freeze.md を上の「前提」のとおり直し、`scripts/tests/` のテストを全部実行して結果をログに書く。
3. 「止まる条件」に当たらなければ cloudflare へマージし、本番の `navbar.js`（`?v=<未使用の値>` 付き）に「書籍」と `resource_books` が無いことを確かめる。#97 と #568 に、この変更（メニューから外した・転送を棚卸しの対象へ移した・マージの SHA）をコメントする。

## 止まる条件
- 手順1の検索で、`navbar.js`・docs・テスト以外（生成ページ・テンプレート・`llms.txt`・サイトマップ・ほかの JS など）に `/resource_books.html` か booklog へのサイトのリンクがある（表を書いて止まる。「別名」の理由が残るため）
- `navbar.js` の差分に「書籍」の項目の削除以外の変更がある、または「リソース」のまとまりの残りの項目が消す前と比べて「書籍」以外で変わる
- 作業ブランチの差分（origin/cloudflare との比較）のファイルが `navbar.js`・docs/notes/redirects.md・docs/notes/books-freeze.md・docs/decisions/ の1ファイル・docs/logs/CHAT-1127-BKS-01.md の範囲を超える（`navbar.js` の変更でページの再生成やキャッシュ用のクエリの更新が規則上要るとわかったときも、行わずに要る範囲を書いて止まる）
- `scripts/tests/` のテストが1件でも失敗する
- 未マージの work/ ブランチが `navbar.js` の「リソース」の項目、または docs/notes/redirects.md の「別名」「棚卸しの対象」の表を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 前提が実物と食い違う（項目の場所・href・表の形など）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-BKS-01.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1127-bks
- ログ: https://github.com/retroeater/mj/blob/work/1127-bks/docs/logs/CHAT-1127-BKS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1127-bks
- 確認用URL: なし
- マージ: 未
- issue: #97, #568
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f268dbf5）: https://github.com/retroeater/mj-logs/tree/main/guide/f268dbf5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e69c8e6c.md
