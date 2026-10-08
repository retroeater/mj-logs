# CHAT-1009-NEN-07

- 着手日時: 2026-10-09
- 対象issue: #277（#521）
- ブランチ: work/1009-nen
- 着手時HEAD: c66a965a

## 指示

【Claude作成】Claude Code 向け指示：#277 年表は案 C（タイトル戦×年の格子）を採用。A・B と比較の仕組みを消し、横スクロールの送り（画面幅ごとに年を送る）・見えている年に無い大会の行を隠す・期の帯を外す・年の表示を1か所にまとめる、を入れて試作する。未マージ・判断待ちで止まる Chat-Ref: CHAT-1009-NEN-07 マージ: 判断待ちで止まる（プレビューを見て平野さんが確かめる。cloudflare へは入れない） 貼る時機: CHAT-1009-NEN-06 が「判断待ち」で止まった後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-nen を続けて使う（NEN-06 の試作がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1009-NEN-06.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。NEN-06 の `## 報告` の状態の末尾に `/ 続き: CHAT-1009-NEN-07` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
平野さんが NEN-06 の比較ページを見て案 C（行＝タイトル戦、列＝年、左から 2026 → 1973）を採り、操作と表示の直しを4点出した。採らない案 A・B と比較の仕組みを消し、4点を入れた試作をプレビューで確かめてもらう。この指示ではマージしない。採用後の本実装（テスト・文書・マージ）は別の指示。
決定（2026-10-09、平野さん）

* 並べ方は C（タイトル戦×年の格子）を採用する（NEN-06 の未決を決めた。2026-10-08 の grill Q3「新しい年を上」の縦の並びは、横〈左から新しい年〉に置き換える）
* 共通の直し（NEN-06 の決定: 共有ボタン右端、見出しの件数なし、選手名は写真下部にいつもの形）はそのまま
* 横スクロールのナビゲーションは、画面幅ごとに（たとえば2年ずつ送るなど）ストレスがないように工夫する
* スクロールで送ったとき、見えている年に当該タイトル戦が無い行は飛ばして（表示しなくて）よい
* 年ごとに並ぶので、写真上部の期の表示（帯）は不要。クリックすればその期のページに飛ぶのでそれでよい
* 画面の上の年のナビゲーションと、表の年の見出しが冗長なので、兼ねるなどしてシンプルにする

前提（チャット側。平野さんの決定ではない）

* 消すもの: 案 A（`timeline_list_html()`・`.mj-tl-year`）、案 B の CSS の割り当て、比較のラジオボタン（`timeline_compare_html()`・`TIMELINE_LAYOUTS`・`.mj-tl-compare`）、`assets/title.js` の年ジャンプの A・B の分岐。格子の HTML（`timeline_grid_html()`、`--yi`・`--ti`）は C の割り当てだけ残す
* カード: 期の帯を外す（写真の上部に何も重ねない）。下部の選手名はそのまま。押す先は期ページ。1位が2名の期（第26期王位戦）は2枚のまま。同じ年に2〜3期あるマス（24マス、NEN-06 の経過）は縦に重ねたまま
* 年の表示を1か所に（案。実物に合わせて Code が決めてよい）: 固定バーの年ジャンプ（54年の横並び）をやめ、格子の上端の固定の年の見出し（sticky）を唯一の年の表示にする。固定バーには「◀ 前へ」「次へ ▶」の送りのボタンと、今見えている年の範囲（例「2026〜2024」）だけを置き、右端に共有ボタン。年代の区切りは格子の年の見出しの側に残す（10年ごとに線）。`#y2025` のアンカーは残す（開いたときにその年の列が左端に来る）
* 横スクロールの送り: 1回の送りは「見えている年の列の数」（スマホで約3列なら3年、PC なら見えている分）を単位にし、送った後は年の列の左端が揃うようにする（`scroll-snap-type: x mandatory` と列の `scroll-snap-align: start`、ボタンは `scrollBy` で列幅の整数倍）。指での横スクロールもそのまま使え、止まったときに列の左端で止まる。送りのボタンは左右の端で無効にする。キーボードの左右矢印でも送れるとよい
* 見えている年に無い大会の行を隠す: 横スクロールが止まった後（`scrollend`。無いブラウザでは `scroll` の後 150ms 程度の待ち）に、見えている年の列に1期も無い大会の行を `hidden` にする。スクロール中は隠さない（行が増減して縦が揺れるのを避ける）。隠す・出すときの縦の揺れが気になる作りなら、その旨を報告に書く。行の順は「タイトル戦」タブの表示順のまま
* 大会名の列（左端の固定の列）は、長い大会名が2〜3行になる（NEN-06 の経過）。幅を少し広げるか、字を小さくするかは Code が決めてよく、何にしたかを報告に書く
* 高さ: 格子の枠の高さは今のまま（画面から navbar と固定バーを除いた分）。行を隠すと枠の中の縦スクロールが減る
* 確かめ方は NEN-06 と同じ（headless Chromium で 375px・1280px・1920×1080。JS のエラー、固定バーの高さ、送りのボタンで年が列の単位で動くこと、行が隠れる・出ること〈例: 1973 の近くでは鳳凰戦だけになる見込み〉、`#y2014` で開いたときの列、写真の遅延読み込み、ページ全体の横スクロールが無いこと）
* `title/timeline/` 以外の生成物は変えない。既存のテスト（`test_title_timeline.py`）は C に合わせて直してよい

手順

1. 確かめる: #277・#521 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*`／`.mj-tl-*` 節を変えていない（`work/1008-hou` の houou/ の節は当たらない）。生成と同じ経路でシートを読み、表示する大会数・期数を報告する
2. 作る: 上の前提のとおり消す・直す・足し、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分が `title/timeline/index.html` だけであることを確かめる。`python3 -m unittest discover -s scripts/tests` を通す。#277 に試作の続きをコメントする
3. 確かめる: Workers Builds のプレビュー（上限15分）で上の確かめを行い、表でログに書く。平野さんに見てもらう手順（確認用 URL、送りのボタン・指での横スクロール・行の増減・2025年の JPML WRC-Rリーグ〈3期〉・1973 の端）を報告に書く

止まる条件

* #277 か #521 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分に `title/timeline/index.html` 以外が出た（シートの変化で説明できるものは止まらず報告）
* 既存のテストが落ちる（`test_title_timeline.py` 以外）
* 行を隠す仕組みが、固定の見出し（sticky）や `scroll-snap` と両立しない（案を書いて止まる）
* cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが確かめる点（送りの単位・行の増減の見え方・年の表示のまとめ方・大会名の列の幅）、見てもらう手順、本実装で足すもの（テスト・docs/notes/title-pages.md「年表」・docs/decisions/title.md・公開は #521 のまま）を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-06 の状態は「判断待ち」だった。末尾に「/ 続き: CHAT-1009-NEN-07」を足した。このセッションは NEN-01〜06 から続けて受けたもの（ただしクローンは作り直されていて、ローカルに `work/1009-nen` が無かった）
- 識別子: `git log --all --grep="CHAT-1009-NEN-07"` は0件
- ブランチ: ローカルに無く、リモートに未マージの `origin/work/1009-nen`（c66a965a）があるため `git checkout -b work/1009-nen origin/work/1009-nen`。`origin/cloudflare` は祖先（取り込み不要）。浅いクローンではない
- 未マージの `work/` ブランチ: `origin/work/1008-dic`（734f7ed0）は対象のファイルを変えていない。`origin/work/1008-hou`（e25f04f4）は `_redirects` と `style.css` 末尾の houou/ の節だけ（`mj-title`・`mj-tl` を含む差分の行は0）で、当たらない
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1009-nen
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen/docs/logs/CHAT-1009-NEN-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
