# CHAT-1016-SKR-11

- 着手日時: 2026-10-16
- 対象issue: なし
- ブランチ: work/1016-skr-rank
- 着手時HEAD: c74340cee

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページの「出場者」の通過・敗退を、三角から「名前の下のピル・順位つき」（④ × ⓒ）に変え、比較ページを消す（プレビューで止める） Chat-Ref: CHAT-1016-SKR-11 マージ: 判断待ちで止まる（プレビューをチャット側と平野さんが確かめた後、別の指示でマージする） 貼る時機: いつでも（CHAT-1016-SKR-10 と同じセッションの続きでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-rank の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-skr-rank を続けて使う（CHAT-1016-SKR-10 の比較ページの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成されたページ（`saikyo/` など）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-SKR-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-11` を足す。

目的
CHAT-1016-SKR-10 の比較ページで平野さんが選んだ形を、最強戦の卓のページに本実装する。使わない案のコード・比較ページ・比較ページを作るスクリプトを消す（docs/notes/design.md「7. チャット側の決め方」）。
決定（2026-10-16、平野さん）

* 卓のページの「出場者」の写真カードの通過・敗退は、④ 名前の下のピル × ⓒ 順位つき（例「1位 通過」「4位 敗退」。順位が空欄の行は「通過」「敗退」だけ）にする。左上の三角はやめる

前提（チャット側。平野さんの決定ではない）

* 形・色・置き場所は SKR-10 の比較ページの ④ のまま（カードの外、名前の下・中央寄せ。通過・優勝は金 `#f2c230`／`#2b2100`、敗退は明 `#555555`／`#e5e5e5`・暗 `#aeaeb2`／`#38383a`。対局のページのピルと同じ部品を使えるなら使う）。SKR-10 のログの経過の値が正
* 「優勝」の行は「1位 優勝」（順位が空欄なら「優勝」）、未対局は今と同じくピルを出さない（要確認: 対局のページのピルの文言と同じ規則か確かめ、違えば対局のページに合わせてログに書く）
* 三角は最強戦の卓のページだけ（SKR-10 の洗い出し）。/live と共有の写真カード（`.mj-saikyo-card`）の見た目は変えない（#566 の決定）。三角の CSS・`--mj-saikyo-rank-size`・擬似要素は、ほかで使っていなければ消す（参照を洗い出してから）。写真の `alt` の「（1位通過）」などの読み上げの文字は残す
* 消すもの: `saikyo_rank_compare.html`・`scripts/build_saikyo_rank_compare.py`・比較ページ用の CSS（docs/notes/title-pages.md の比較ページの後片付けに従う）
* 文書: docs/notes/saikyo-page-design.md の三角の節（通過・敗退は順位ラベルの色で表す、一辺 40% など）を今の形に置き換え、置き換えた旨を書く。docs/notes/design.md に最強戦の三角の行があれば直す。決定を docs/decisions/saikyo.md に足す（SKR-10 の決定の後に）

手順

1. 確かめる: 未マージの work/ ブランチ（work/1016-skr-rank を除く）が `style.css` の最強戦の写真カードの節・`scripts/generate_saikyo_pages.py` の卓のページの出場者の部分・`assets/saikyo.js` を変えていないか。三角の CSS の参照の洗い出し
2. 作る: 上の決定と前提のとおり。テストを足し・直し（ピルの文言: 順位あり・順位空欄・優勝・敗退・未対局）、全件通す。`saikyo_pages` を生成し直し、生成物の差を種類に分けてログに書く（卓のページで三角がピルに変わったもの・それ以外。それ以外があれば中身）
3. 確かめて止まる: プレビューで、2025 決勝（`saikyo/2025/20251214-s1.html`）・SKR-10 で選んだ 2019年度の卓・未対局の卓1つを、headless Chromium の 390px・明・暗で撮り、崩れ・名前とのかぶり・文字の切れが無いかをログに表で書く。比較ページが 404 になったこと。ログの「確認用URL」の行に、この版で見える印を書く（URL はターミナルの最終報告にだけ）

止まる条件

* 未マージの work/ ブランチが同じ節・関数を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く）
* 三角の CSS を最強戦の卓のページ以外が使っている（そこは消さずに残し、ログに書いて進めてよい）
* 生成物の差に、卓のページの出場者の変化と、毎朝の再生成のシートの変化で説明できない差がある（saikyo/ 以外のページの変化を含む）
* cloudflare への push が求められる場面になった（この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: プレビューのこの形でマージしてよいか）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bd4bff47）: https://github.com/retroeater/mj-logs/tree/main/guide/bd4bff47

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bd4bff47/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
