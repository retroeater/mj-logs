# CHAT-1104-TTL-03

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 811040fef

## 指示

【Claude作成】Claude Code 向け指示：TTL-02 の続き（Workers Builds のプレビューを出し直し、確認用 URL を出す。中身は変えない） Chat-Ref: CHAT-1104-TTL-03 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-ttl を続けて使う（TTL-02 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（title/ の HTML・sitemap の lastmod など）の衝突は docs/notes/branch-operations.md の解き方で生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-03` を足す。

目的
TTL-02 のコミット f4b38e70a の Workers Builds のプレビューが失敗し（同じ時間帯〈2026-11-03 16:27〜16:32Z〉に本番・ほかのブランチのビルドも失敗。その後のビルドは CHAT-1104-AIC-33・PRP-09 のログで success）、確認用 URL が無い。title/ の中身は変えずに、プレビューを出し直す。
決定（2026-11-04、平野さん）

* なし（TTL-02 の「判断が必要なこと」〈写真カード案と行の案・仮置き〉は、プレビューを見てから答える）

前提（チャット側。平野さんの決定ではない）

* 失敗は一時的なものと見ている（要確認）。ログの push（この指示のログ・TTL-02 のログの状態の直し）でビルドが付けば足りる
* 本体のページは写真カード案（`LAYOUT_CARDS`）のまま。比較ページ `/title/compare-layout.html` も残す

手順

1. 上の手順0のログの push でビルドが付くのを待つ（CLAUDE.md の待機の上限のとおり）。origin/cloudflare を取り込んだときは、生成し直した後のテストを通してから push する
2. ビルドが success なら、プレビューで TTL-02 の手順3の確かめのうち、入口（既定・`?year=2025`・`?q=白鳥`）・鳳凰戦・第41期鳳凰戦・比較ページの表示を 360px の明・暗で見て、ローカルの結果と食い違いが無いことを報告に書く（UA に mj-internal を含める）
3. ビルドが failure なら、check-run の内容（`docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき」）と、同じ時間帯の本番・ほかのブランチのビルドの成否を報告に書き、平野さんのダッシュボードの確認（「Retry build」）が要るかを「判断が必要なこと」に書く

止まる条件

* title/ の HTML・`generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js` に、origin/cloudflare の取り込みと生成し直し以外の差分が出た
* 取り込みで `generate_title_pages.py` が衝突した（解かずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には TTL-02 の (1)(2)(3) をそのまま引き継いで書く（取り込みで変わった点があればそれも）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL、比較ページの URL を含む）を書く。Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6fe4b43f）: https://github.com/retroeater/mj-logs/tree/main/guide/6fe4b43f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6fe4b43f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
