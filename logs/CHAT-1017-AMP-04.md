# CHAT-1017-AMP-04

- 着手日時: 2026-10-17
- 対象issue: #584
- ブランチ: work/1016-amp（CHAT-1016-AMP-01・CHAT-1017-AMP-03 の続き）
- 着手時HEAD: 6e4edb9

## 指示

【Claude作成】Claude Code 向け指示：期別の進行中の期（AMP-03 の続き）— 案内文を消し、前期の B1 で昇級した選手のチップを「B1」にし、質問欄の選手カードのチップを個人成績とそろえて、プレビューで止まる Chat-Ref: CHAT-1017-AMP-04 マージ: 判断待ちで止まる（平野さんが直した後の見え方をプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1017-AMP-03 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-amp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-amp を続けて使う（AMP-01・AMP-03 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1017-AMP-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-AMP-04` を足す。

目的
AMP-03 のプレビューへの平野さんの回答（2026-10-17）を入れる。期別の案内文を消す。前期の B1 で昇級した選手は後期に出ないので、名前の面のチップを「B1」にする。質問欄の選手カードのチップを、個人成績の名前の面と同じ決め方にそろえる。
決定（2026-10-17、平野さん）

* 期別の案内文「進行中の期の成績は Ampai でリアルタイムに見られます」は不要。消す（同日の「短くして1行にする」を置き換える）
* 名前の面のチップを進行中の期のリーグにした見え方（AMP-03 のプレビュー）は OK
* 和田直樹の 43期後期の B2（43期前期は E3 で昇級）はシートの値のままで正しい（特別昇級。9段階の昇級）
* 質問欄の選手カード（`houou/ask-players.json`）のチップも、個人成績の名前の面と同じ決め方にそろえる
* 前期の B1 で昇級した選手は、後期のリーグ戦に出場しない（B リーグ以下は前期・後期、A リーグは通期のため、翌年度の A リーグの開幕まで出ない）。この選手は期別に進行中の期の行は出さなくてよいが、名前の横のチップは「B1」と出す（今回は中村毅・吉野敦志）

前提（チャット側。平野さんの決定ではない）

* チップの決め方の案（進行中の期があるとき）: 進行中の期の行がある選手はそのリーグ（AMP-03 のまま）。行が無く、成績 JSON の最後の行が進行中の期の直前の前期（今は 43前）の B1 で結果が「昇級」の選手は「B1」。どちらでもない在籍者はチップを出さない（AMP-03 のまま）
* AMP-03 のログ「2. 直し」の「消える 7名」のうち、中村毅（B1）・吉野敦志（B1）が「B1」に戻り、残る5名（井上泰秀・伊藤優孝・山宮雅之・島崎涼・後藤真帆）は出さないままの見込み（要確認。43前の結果は確かめていない）
* そろえ方の案: 名前の面（`assets/houou.js` の `currentLeague()`）と質問欄のカード（`ask_roster()`）が同じ値を出すことをテストで確かめる（在籍者 690名で食い違い 0名）。片方の決め方をもう片方から使う形にできるならそうする
* 変えないもの: 期別の進行中の期の行（形・リンク）、検索の候補・最近見た選手の「最後の期とリーグ」、ランキング、トップ、`jpml_pros`、`workers/ask/data.json`

手順

1. `python3 scripts/regenerate.py --check houou_pages` を実行し、止まる理由があれば一覧を書いて止まる。#584 に着手中のコメント（続き、CHAT-1017-AMP-04）を書く
2. 案内文を消す。チップの決め方に上の「前提」の B1 の分を足し、`ask_roster()` も同じ決め方にする。テストを足して生成し直す。`## 経過` に次を書く: 前期の B1 で昇級して後期の行が無い選手の名前の一覧、AMP-03 から変わる選手（名前の面・質問欄のカードのそれぞれ）の名前の一覧、名前の面と質問欄のカードの食い違いの数（0 のはず）、生成物の差分の種類
3. プレビューで 375px 相当の明・暗と PC の幅で、中村毅・吉野敦志・末続ヒロトシ・白鳥翔・東幸一郎の名前の面と期別の先頭を確かめて書く（案内文が無いこと、中村毅・吉野敦志のチップが B1 で進行中の期の行が無いこと）。docs/notes/houou-top.md・docs/notes/ask-worker.md（質問欄の選手カードの節）・docs/decisions/houou.md を直す。#584 に直した点のコメントを書く（本文・状態・ラベルは変えない）。判断が必要なことに「直した後の見え方でマージしてよいか」を挙げる

止まる条件

* 手順1 の止まる条件、他セッションの着手中コメントがある
* 前期の B1 で昇級して後期の行が無い選手が、中村毅・吉野敦志のほかにもいる、またはこの2人が当たらない（一覧を書いて止まる）
* 名前の面と質問欄のカードのチップが1名でも食い違う
* 生成物の差分が、`houou/players/index.html`・`houou/ask-players.json`（と `assets/houou.js`・テスト・docs）と、取り込んだ origin/cloudflare のシートの変化で説明できるもの以外に出る。`jpml_pros.html`・`workers/ask/data.json` が変わる
* 未マージの work/ ブランチが `scripts/generate_houou_pages.py`・`assets/houou.js`・`ask_roster()` のある生成スクリプトの同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* プレビューの「Workers Builds: mj」が付かない・失敗する（原因を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-AMP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-AMP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6e1da5ac）: https://github.com/retroeater/mj-logs/tree/main/guide/6e1da5ac

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0962f9e0.md
