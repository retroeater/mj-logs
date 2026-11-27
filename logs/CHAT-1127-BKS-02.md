# CHAT-1127-BKS-02

- 着手日時: 2026-11-27
- 対象issue: #568、新規（書籍のリニューアル）
- ブランチ: work/1127-bks
- 着手時HEAD: 2b6488bc033e61d49060b5cf23659643edc0628d

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1127-BKS-01 の続き）「書籍のリニューアル」の issue を起票し、メニューの「書籍」（booklog）を外して /resource_books.html を棚卸しの対象へ移す
Chat-Ref: CHAT-1127-BKS-02
マージ: 承認済み（チャットで）。ただし下の「止まる条件」を1つでも満たさなければマージせず止まる
貼る時機: いつでも（CHAT-1127-BKS-01 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告は状態と公開ログの URL の2行）。平野さんは、この指示のための作業ブランチ work/1127-bks の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1127-bks を続けて使う（CHAT-1127-BKS-01 のログと docs/decisions の記録が載っているため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1127-BKS-01 の「判断が必要なこと」3点に答え、BKS-01 の作業（メニューの「書籍」〈booklog への外部リンク〉を外し、`/resource_books.html` の転送を docs/notes/redirects.md の「別名」から「棚卸しの対象」へ移す）を最後まで行う。BKS-01 の「決定」節はそのまま有効。

### 決定（2026-11-27、平野さん）
- 「書籍」のリニューアルは新しい issue を起票して扱う（#97〈2026-11-01 に not planned でクローズ〉は再オープンしない）。今回のメニューの変更はその issue の最初の作業とする
- docs/notes/static-generation.md（「navbar.js と検索欄」の navbar.js の href の本数の記述）を、マージしてよい差分の範囲に加える。下の「止まる条件」を満たせばプレビューの確認なしでマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 新しい issue の題は「書籍のリニューアル」の案。本文には、2026-11-27 に着手したこと・booklog を今後使わないこと・経緯として #97・#429・docs/notes/books-freeze.md へのリンクを書く。リニューアルの中身はまだ決まっていないので書かない。ラベルは似た issue にそろえてよい
- BKS-01 の「判断が必要なこと」2つめ（未マージの origin/work/1101-prp-pub〈PRP-20 の判断待ち〉が「棚卸しの対象」の表の末尾に `/jpml_pros.html` の行を足している）への答え（チャット側）: 1101-prp-pub は待たない。`/resource_books.html` の行は表の末尾ではなく先頭（入れた日 2026-09-07 が今の表で最も古いため）に置き、1101-prp-pub の追加と同じ箇所にしない。このため、BKS-01 の止まる条件のうち「未マージの work/ ブランチが redirects.md の『棚卸しの対象』の表を変えている」は、「同じ行・隣り合う行を変えている」に読み替える
- 「最後の棚卸し」の列は「未（初回 2027-01 上旬）」、issue の列は今の注記を短くして #568 と新しい issue を添える案（表の形とテストに合わせて変えてよい）。「別名」の節の「`/resource_calendar.html`・`/resource_books.html` をここに置くのは…」の文は `/resource_calendar.html` だけに直す
- docs/notes/books-freeze.md の booklog の2か所（12行目・27行目付近）は、2026-11-27 の決定で置き換わった旨を添える（凍結時点の記録なので消さない）。sitemap-pages.xml・style.css・`scripts/generate_books_pages.py` のコメントと docs/new-page-checklist.md の実例の表は変えない
- #429 の状態は確かめていない（要確認）。状態をログに書き、変えない

## 手順
1. CHAT-1127-BKS-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1127-BKS-02` を足す。「書籍」「booklog」「リニューアル」で issue をクローズ済みも含めて検索し、同じ主題のものが #97・#429 のほかにあれば起票せず止まる。無ければ「書籍のリニューアル」を起票して着手中のコメントを書く（番号・#429 の状態をログに書く）。
2. `navbar.js` から「書籍」の項目だけを消す（「リソース」の残りがカレンダー・ログ・辞書・牌効率のまま並びが変わらないことを、消す前と後の一覧でログに書く）。docs/notes/redirects.md・docs/notes/books-freeze.md・docs/notes/static-generation.md を上の「前提」のとおり直し、docs/decisions/cloudflare.md の BKS-01 の記録にこの指示の「決定」を足す。`scripts/tests/` のテストを全部実行して結果をログに書く。
3. 「止まる条件」に当たらなければ cloudflare へマージし、本番の `navbar.js`（`?v=<未使用の値>` 付き）に「書籍」と `resource_books` が無いことを確かめる。新しい issue と #568 に、この変更（メニューから外した・転送を棚卸しの対象へ移した・マージの SHA）をコメントする。

## 止まる条件
- 手順1の検索で同じ主題の issue が見つかった
- `navbar.js` の差分に「書籍」の項目の削除以外の変更がある、または「リソース」の残りの項目が変わる
- 作業ブランチの差分（origin/cloudflare との比較）のファイルが `navbar.js`・docs/notes/redirects.md・docs/notes/books-freeze.md・docs/notes/static-generation.md・docs/decisions/cloudflare.md・docs/logs/CHAT-1127-BKS-01.md・docs/logs/CHAT-1127-BKS-02.md と、origin/cloudflare の取り込みで入るものの範囲を超える（ページの再生成やキャッシュ用のクエリの更新が規則上要るとわかったときも、行わずに要る範囲を書いて止まる）
- `scripts/tests/` のテストが1件でも失敗する
- 未マージの work/ ブランチが `navbar.js` の「リソース」の項目を変えている、または docs/notes/redirects.md の同じ行・隣り合う行を変えている、または取り込みで両立しない衝突になる（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 前提が実物と食い違う
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告は docs/logs/_template.md「ターミナルへ返す最終報告」の形（1行目に状態、2行目に https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-BKS-02.md?v=<SHA> だけ）で書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1127-bks
- ログ: https://github.com/retroeater/mj/blob/work/1127-bks/docs/logs/CHAT-1127-BKS-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1127-bks
- 確認用URL: なし
- マージ: 未
- issue: #568
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 75620aa7）: https://github.com/retroeater/mj-logs/tree/main/guide/75620aa7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/75620aa7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e69c8e6c.md
