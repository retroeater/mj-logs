# CHAT-1009-NEN-06

- 着手日時: 2026-10-09
- 対象issue: #277（#521）
- ブランチ: work/1009-nen
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：#277 年表の見直し。平野さんの実機確認の指摘（共有ボタン右上・件数なし・年のまとまり・カードの文字の置き方）を反映し、並べ方3案（A 年ごとの一覧の改良／B 年×タイトル戦の格子／C タイトル戦×年の格子）をラジオボタンで見比べる比較ページを試作する。未マージ・判断待ちで止まる Chat-Ref: CHAT-1009-NEN-06 マージ: 判断待ちで止まる（プレビューを見て平野さんが並べ方を決める。cloudflare へは入れない） 貼る時機: CHAT-1008-NEN-05 の完了の後（済んでいる。`/title/timeline/` は未公開のまま本番にある） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1009-nen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-nen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1008-NEN-05 のログの `## 報告` を読み、完了していなければ何もせず止まる。

目的
平野さんが本番（未公開）の `/title/timeline/` を実機で見て、直したい点と並べ方の案を出した。小さな直し（共有ボタンの位置・件数・年のまとまり・カードの文字の置き方）は3案に共通で入れ、並べ方は3案を実データで見比べられる比較ページにする。この指示ではマージしない。採用後の本実装は別の指示。
決定（2026-10-09、平野さん）

* 共有ボタンは右上に置く（今は左上）
* 年の見出しの件数「（18）」は出さない（2026-10-08 の grill Q9「2025年（25）」の形を置き換える）
* 年ごとのまとまりが分かる形にする（年をカードにする等）
* 選手名は写真の下部に「いつもの形」（入口の写真カードと同じ、グラデーションに白文字）で重ねる。今の、写真の下に文字を並べる形は上下が不揃いでガタガタする
* 期は写真の上部に、タイトル戦名は写真の外側の下に、など、タイトル戦名の長さでごちゃごちゃしない工夫をする
* 2026年と2025年の同じタイトル戦が縦方向に並ぶ形がよい。タイトル戦を縦に並べて、左から右に 2026 → 2025 → … と並べると、歴代のタイトルホルダーの移り変わりが分かりやすいのではないか（この2つは案として試作して見比べる）

前提（チャット側。平野さんの決定ではない）

* 今の年表の作り（`timeline_card_html()`・`timeline_bar_html()`・`year_heading()`・`.mj-tl-*`、docs/notes/title-pages.md「年表」）を土台にする。公開関連（noindex・sitemap・navbar・`llms.txt`）は触らない（未公開のまま。公開は #521）
* 共通の直し（3案すべてに入れる）:
   * カード: 写真の上部に期の帯「第42期」（入口の大会名の帯と同じ部品・変数 `--mj-title-band-bg`）、写真の下部に選手名（入口と同じグラデーション＋白文字＋影）。タイトル戦名は写真の外側の下に小さく1行（案 B・C では列・行の見出しに大会名があるので、カードには出さない）。押す先は期ページ（変えない）
   * 固定バー: 共有ボタンを右端に（年ジャンプは左から）。年ジャンプの中身は今のまま（横に並べる・年代の区切り・今見ている年の強調・`#y2025`）。案 B・C では年ジャンプの押し先が列（案 C）か行（案 B）になる
   * 年の見出しは「2026年」だけ（件数なし）
* 並べ方（ラジオボタンで切り替え。`:has(#…:checked)` で CSS を差し替え、JS なし・再読み込みなし。HTML は3案で共有できる形を目指し、できなければ3つ焼き込む）:
   * A: 年ごとの一覧の改良。年ごとを1枚のカード（枠・薄い背景・見出し）にまとめ、中は今の並び（大会の表示順）
   * B: 年 × タイトル戦の格子。行＝年（上から 2026 → 1973）、列＝タイトル戦（「タイトル戦」タブの表示順、20列）。列の見出し（大会名）は上に固定（sticky）、年は左の列に固定。同じタイトル戦が縦に並ぶ。空のマスは空欄。同じ年に同じ大会が2期あるマスは2枚を縦に重ねる（見込み: 年2回ある大会があるため。実データで数を報告）
   * C: タイトル戦 × 年の格子（B の転置）。行＝タイトル戦（表示順、20行）、列＝年（左から 2026 → 1973、54列）。大会名は左の列に固定、年は上の行に固定。横スクロールで歴代の移り変わりを追う
   * 格子の幅: 1マスは今の小さい写真（最小 96px）を基準にし、スマホでは横スクロール（B は20列、C は54列）。固定の見出し（sticky）はスクロール中も見えるようにする。1マスの大きさはスマホ幅・PC 幅で見て Code が決めてよい
* 確かめ方は NEN-03 と同じ（headless Chromium で 375px・1280px・1920×1080、JS のエラー、固定バーの高さ、年ジャンプ、ラジオの切り替え、写真の遅延読み込み、横スクロールの有無を案ごとに表に）。比較用のラジオボタンは本実装で消す
* `title/timeline/` 以外の生成物（他の title/ のページ・`sitemap-title.xml`・`search.json`）は変えない。シートの変化による差分が混ざれば種類と件数を報告

手順

1. 確かめる: #277・#521 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*`／`.mj-tl-*` 節を変えていない（`work/1008-hou` の `style.css` 末尾の houou/ の節は当たらない）。生成と同じ経路でシートを読み、表示する大会数・期数と、同じ年に同じ大会が2期以上あるマスの数を報告する。#277 に着手中のコメントを残す
2. 作る: 上の共通の直しと A・B・C の比較の仕組みを `title/timeline/index.html` に入れ、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分が `title/timeline/index.html` だけであることを確かめる。`python3 -m unittest discover -s scripts/tests` を通す（`test_title_timeline.py` の見出しの件数の検査は「件数なし」に合わせて直してよい）
3. 確かめる: Workers Builds のプレビュー（上限15分）で上の確かめを行い、結果を案ごとの表でログに書く。平野さんに見てもらう手順（確認用 URL、A → B → C の順、見る年の例〈2026・2014・1973〉、スマホでの横スクロールと固定の見出し）を報告に書く

止まる条件

* #277 か #521 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分に `title/timeline/index.html` 以外が出た（シートの変化で説明できるものは止まらず報告）
* 既存のテストが落ちる（`test_title_timeline.py` の件数の検査以外）
* 固定の見出し（sticky）が固定バー（`--mj-title-filter-h`）と両立しない（案を書いて止まる）
* cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが決める点（並べ方 A／B／C、共通の直しの可否）、見てもらう手順、本実装で消す・足すものを書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-05 の状態は「完了」。このセッションは NEN-01〜05 から続けて受けたもの（同じセッション。識別子 NEN はこのセッションが使ってきたもの）
- 識別子: `git log --all --grep="CHAT-1009-NEN-06"` は0件
- ブランチ: `work/1009-nen` はローカル・リモートとも無かったため `git checkout -b work/1009-nen origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1009-nen
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen/docs/logs/CHAT-1009-NEN-06.md
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
