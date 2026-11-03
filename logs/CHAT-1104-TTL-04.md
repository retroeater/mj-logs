# CHAT-1104-TTL-04

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 57f02a1a6

## 指示

【Claude作成】Claude Code 向け指示：TTL-03 の続き（入口は写真カード・大会ページと期ページは行の案に決め、比較ページと使わないコードを消して仕上げる。プレビューまで） Chat-Ref: CHAT-1104-TTL-04 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-03 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-ttl を続けて使う（TTL-02・03 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（title/ の HTML・sitemap の lastmod など）の衝突は docs/notes/branch-operations.md の解き方で生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-04` を足す。

目的
TTL-03 の「判断が必要なこと」(1)(2) への回答で title/ の形を決め、比較ページと使わないコードを消して仕上げる。マージはプレビューを見てから別の指示で行う。
決定（2026-11-04、平野さん）

* (1) 入口（現タイトルホルダーと、年を選んだときの各年の優勝者）は写真カード案。大会ページと期ページ（決勝メンバー）は行の案
* (2) TTL-03 の仮置きの一覧は、すべて今の値で決定（共通ナビの判定は body の class `mj-title-n-page`、期ページのパンくずの最後は「第41期」で対局日は題名の2行目、大会ページの題名の2行目に期の範囲、節の見出しは 13px 灰色＋上の余白 8px、節の間隔 16px、「成績」は面の中の押せる行、検索欄は題名の下、年のシートの題は「年を選ぶ」、navbar は固定しない、行の案の大会ページ・期ページの形、写真の無い選手は頭の1字の丸、検索結果の期のカードの形、など。行の案の入口〈写真・選手名・大会名・›〉は (1) で採らない）
* 2026-11-04（CHAT-1104-PRP-10 で記録される決定）: TTL（work/1104-ttl）を先に cloudflare へマージし、pro/ の公開のブランチ（work/1101-prp-pub）の側で重なりを解く。決勝メンバーの行き先を決める部分は `member_link()` の1つの関数のまま残し、TTL では pro/ の判定を入れない

前提（チャット側。平野さんの決定ではない）

* 暗い配色でメンバー限定のバッジ（#212529 の地）の縁が見えない件は、暗い配色だけ地の色を新サイトの暗い配色の値に寄せて直す案（直し方は任せる。直した値とコントラスト比〈WCAG 2.x の式で計算〉を報告に書く）
* 比較ページ `/title/compare-layout.html` は消す（noindex のため、転送は不要と見ている。要確認）。`LAYOUT` の切り替えと、使わない側（入口の行の案、大会ページ・期ページの写真カード案）のコード・CSS を消す。写真カードの部品は入口と検索結果で残る
* TOP 系列の伝言（2026-11-04）: 新しい共通ナビ（TOP-11、work/1104-top）は白地の対象のページ全部に出る。title/ はこの TTL で白地の対象に入れる。title/ の「質問」の文脈は横断のまま（`context` なし）。TOP-11 のマージとの前後は問わず、`navbar.js` の対象の判定の重なりは両方を残して解ける見込み（要確認）
* 文書: docs/notes/title-pages.md（入口・大会ページ・期ページ・固定バーと検索・入口の年の切り替えの節）と docs/notes/design.md（「共通ナビ（新しい形）」の適用範囲に title/ を足す、title/ の値の行）を今の形に直す。`scripts/promo_video/title/capture.mjs` は告知動画の作り直しまで直さない（注記だけ足す）

手順

1. 上の決定で、入口を写真カード、大会ページ・期ページを行の形に固定し、比較ページ・`LAYOUT` の切り替え・使わない側のコードと CSS を消す。メンバー限定のバッジの暗い配色を直す
2. 文書（docs/notes/title-pages.md・docs/notes/design.md・docs/decisions/title.md）を直す。追記先の今の内容を読み、同じ趣旨の記述は置き換える
3. 生成・テストを通し、TTL-02 の手順3と同じ確かめ（360px と PC 幅、明・暗。入口〈既定・`?year=2025`・`?q=白鳥`〉・大会ページ〈鳳凰戦・王位戦〉・期ページ〈第41期鳳凰戦・第42期十段戦・第26期王位戦〉）を行い、はみ出し・切れ・重なりの件数を報告に書く。比較ページの URL が 404 になることも確かめる。プレビューのビルドが付くこと（docs だけの push では付かない）を確かめ、確認用 URL を出す

止まる条件

* 生成物の差分に、title/ の HTML・`sitemap-title.xml`・比較ページの削除・共通の CSS／JS 以外の変化（ほかのページの HTML、`search.json`・`years.json` の中身）が出た（想定外の行を全部書いて止まる）
* 未マージの work/ ブランチ（`work/1101-prp-pub` は除く）が `generate_title_pages.py`・`assets/title.js`・`style.css` の title/ の節・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。`work/1104-top` は重なりを報告に書くだけで止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、決めずに置いた値があればその一覧と、マージのときの `work/1101-prp-pub`・`work/1104-top` との重なりの見込みを書く
* マージは冒頭の「マージ:」の行のとおり（プレビューまでで止める）
* ターミナルへの最終報告に「確認用:」の行を書く。Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dbac236b）: https://github.com/retroeater/mj-logs/tree/main/guide/dbac236b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
