# CHAT-1016-HOU-64

- 着手日時: 2026-10-16
- 対象issue: #540（閉じる: #518・#520・#141・#371・#228・#540）
- ブランチ: work/1016-hou4
- 着手時HEAD: 42aacbf2b

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 houou/ を公開する（#540）— work/1016-hou4 を cloudflare へマージし、シートの直しの後の再生成・本番の確かめ・旧4ページの後始末の issue の起票・issue のクローズまで Chat-Ref: CHAT-1016-HOU-64 マージ: 承認済み（チャットで。2026-10-16 に平野さんが、HOU-63 のプレビューと PNG を見たうえで「公開してよい」と回答） 貼る時機: いつでも（CHAT-1016-HOU-63 は判断待ちで、平野さんは回答済み。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou4 の push と、cloudflare へのマージの push と、issue の起票・クローズを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-hou4 を続けて使う（HOU-63 の続き）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり（取り込んだ後のスクリプトで生成し直して解く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-63 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
HOU-63 で作った公開の変更（旧4ページの 301・jpml_pros のリンクの張り替え・ナビの「鳳凰戦」の1段化・noindex の解除・サイトマップと `llms.txt`・質問欄の1日の上限200・告知動画のスクリプト）を cloudflare へ入れ、houou/ を公開する。名簿の食い違いはシートの側で直したので、生成をやり直してから入れる。
決定（2026-10-16、平野さん）

* 公開のマージをしてよい（HOU-63 の判断 1）
* X の投稿文は案B（HOU-63 の判断 2）。投稿は平野さんが動画 houou-promo-v4.mp4 を付けて行う
* 名簿の食い違いは、公開の前に直す（HOU-63 の判断 3 → b）。食い違っていた1名「朝井大元」は退会したため、平野さんが「プロ」シートから削除した。他のシートは変えていない

前提（チャット側。平野さんの決定ではない）

* 再生成: シートの直しの後なので、`python3 scripts/regenerate.py houou_pages jpml_pros` は通る見込み。通ったら、HOU-63 で手で当てた生成物と比べる。見込みの差は、退会した1名の分（`jpml_pros.html` の行、`houou/search.json`・名簿の JSON の行、トップ・個人成績の説明文の在籍の人数〈691名 → 690名〉など）とシートの他の変化だけ。ほかの差が出たら止まる。差の種類と、在籍の人数が変わったかどうかをログに書く
* 他のシートの確かめ（変えない）: 退会した1名が、在籍者として読まれる他のシート・ブック（SNS ブック・選手マスタのブックなど、生成スクリプトが在籍を判定する元）に残っていて、生成や表示に影響するかを確かめ、影響があれば報告の「判断が必要なこと」に書く。過去の成績（鳳凰戦の成績のシートなど）に名前があるのは正しいので、指摘しない
* 投稿文の案Bの全文は HOU-63 のログ「手順4」のとおり。在籍の人数が変わっても案Bには人数が無いので、文は直さない。動画の範囲のテロップ（691名）との食い違いは、人数が変わったときだけ報告に書く（撮り直さない）
* 公開後の確かめは #540 の本文「公開後の確かめ」と docs/new-page-checklist.md の公開の段のとおり。質問欄の上限200の確かめは、本番に送る質問を1問までにし、上限の値が分かる応答・設定で確かめる（確かめられなければ「未確認の項目」に書く）
* 後始末の issue は、HOU-63 のログの本文の案で起票する（題も案のまま。#540 を本文に書く）
* 閉じる issue は #540 の本文のとおり（#518・#520・#141・#371・#228）。#540 は公開後の確かめが済んでから閉じる。#541 は公開の後に回す決定のまま開けておく

手順

1. 着手の確かめと取り込み: CHAT-1016-HOU-63 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-64` を足す。#518・#540・#542 に他のセッションの着手中のコメントが無いか確かめる。origin/cloudflare を取り込む。
2. 再生成と再検証: 前提のとおりに生成し直して差を確かめ、生成物をコミットする。他のシートの確かめをログに書く。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。決定を `docs/decisions/houou.md` に足す（投稿文は案B・名簿は公開の前に直した）。
3. マージ: push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1016-hou4:cloudflare` で入れる。HOU-48・60〜63 のログの `## 報告` の状態を、このマージで入ったことが分かる形に直す。
4. 公開後の確かめ: Workers Builds の本番の check-run（`mj`・`mj-ask`）が success か（`mj-ask` のビルドが15分待っても走らなければ、平野さんに頼む画面〈Cloudflare → Workers & Pages → `mj-ask` → Deployments〉と操作を報告に書く）。本番（`?v=<未使用の値>`）で、旧4ページの 301 と行き先（クエリ付きを含む）、houou/ の12ページに noindex が無いこと・`houou/leagues/` に残ること、ナビの「鳳凰戦」、`jpml_pros.html` のリンク、`sitemap-pages.xml`・`llms.txt`、質問欄の上限200を確かめる。マージの後の `regenerate-page.yml` の結果をログに書く（待つのは15分まで）。
5. issue: 後始末の issue を起票する。#540 に、マージの SHA・公開後の確かめの結果・後始末の issue の番号のコメントを書く（末尾に `Chat-Ref:` の行）。確かめが済んだら、#518・#520・#141・#371・#228 と #540 を閉じる（閉じる前に各 issue に、公開で閉じる旨の1行と `Chat-Ref:` の行のコメントを書く）。

止まる条件

* CHAT-1016-HOU-63 のログの状態が判断待ちでない、または work/1016-hou4 がリモートに無い
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 取り込みで、上に書いた解き方で解けない衝突が出た
* 再生成がまだ名簿の食い違いなどで止まる（直さずに、止まった理由を報告する。マージしない）
* 再生成の差に、前提の見込みとシートの変化で説明できない種類がある（ランキングの値・順位が変わったら止まる）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージの後の check-run の失敗・配られている版の食い違い・本番で旧4ページの転送が 301 にならない・行き先が 404。今回の変更による失敗と無関係な失敗を分け、無関係なら原因を報告に書いて残りの手順を進めてよい。今回の変更によるものなら、直さずに報告する（issue は閉じない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージの結果を書く docs/logs のみの追いの push は可）。「判断が必要なこと」に、他のシートの確かめで見つかった点（あれば）と、投稿の準備ができたこと（投稿文は案B、動画は houou-promo-v4.mp4）を書く
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-64.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-64 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。CHAT-1016-HOU-63 のログの状態は「判断待ち」。origin/work/1016-hou4 はあり、ローカルと一致（42aacbf2b）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1016-hou4
- ログ: https://github.com/retroeater/mj/blob/work/1016-hou4/docs/logs/CHAT-1016-HOU-64.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-hou4
- 確認用URL: なし
- マージ: 未
- issue: #540
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 64ee77ff）: https://github.com/retroeater/mj-logs/tree/main/guide/64ee77ff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/64ee77ff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
