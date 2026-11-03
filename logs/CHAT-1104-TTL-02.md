# CHAT-1104-TTL-02

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 0c106d379

## 指示

【Claude作成】Claude Code 向け指示：TTL-01 の続き（work/1101-prp-pub との衝突を承知で、title/ 全ページを新しい形で作り、比較ページつきのプレビューまで） Chat-Ref: CHAT-1104-TTL-02 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなく、TTL-01 のログだけの未マージのブランチなら、続けて使ってよい（`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい。rebase しない）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-02` を足す。

目的
CHAT-1104-TTL-01 の指示（目的・決定・前提・手順・止まる条件・完了条件）を、下の変更だけを加えてそのまま実行する。TTL-01 は未マージの `work/1101-prp-pub`（#641、pro/ の公開）との重なりで着手前に止まった。
決定（2026-11-04、平野さん）

* TTL-01 の判断が必要なことへの回答: 案 B。`work/1101-prp-pub` との衝突を承知で、今の `origin/cloudflare` の上で期ページを含む全ページを作る。後でマージする側が `generate_title_pages.py` の `member_card_html()`・`period_body()` と title/ の期ページの HTML の取り込みを解く
* （TTL-01 の決定はそのまま。中身はそのまま・写真カード案と面の行の案を比較ページで・対象は全ページ）

前提（チャット側。平野さんの決定ではない）

* TTL-01 の前提はそのまま
* 期ページの決勝メンバーの行き先は、今の `origin/cloudflare` の形（X）で作る。`work/1101-prp-pub` のマージ後に「個別ページのある在籍者は pro/、無ければ X」へ変わる前提で、行の案・写真カード案のどちらでも行き先を1か所（関数）で決める形にしておくと、後の取り込みが楽になる（要確認。作りは任せる）
* `work/1101-prp-pub` は消さない・取り込まない・変えない

手順

1. TTL-01 の手順1を行う。重なりの洗い出しでは、`work/1101-prp-pub` の `member_card_html()`・`period_body()` の変更の中身（行き先の決め方・HTML の形）を `## 経過` に引用し、後で取り込むときに解く箇所の見込みを書く
2. TTL-01 の手順2を行う（期ページを含む）
3. TTL-01 の手順3を行う

止まる条件

* TTL-01 の止まる条件のうち、未マージの work/ ブランチとの重なりは次に置き換える: `work/1101-prp-pub` との重なりでは止まらない（報告に書く）。ほかの未マージの work/ ブランチが `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突するときは止まる（同じファイルの別の箇所の変更では止まらず、ログに書く）
* ほかの止まる条件は TTL-01 のとおり

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、(1) 写真カード案と行の案のどちらにするか (2) 仮置きの一覧（1件ずつ、今の値と理由）(3) `work/1101-prp-pub` との衝突の見込み（ファイル・関数・解き方の案）を書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8071c9be）: https://github.com/retroeater/mj-logs/tree/main/guide/8071c9be

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8071c9be/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59c4b82d.md
