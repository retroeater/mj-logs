# CHAT-1008-NEN-04

- 着手日時: 2026-10-08
- 対象issue: #277
- ブランチ: work/1008-nen
- 着手時HEAD: bf2f152e

## 指示

【Claude作成】Claude Code 向け指示：#277 年表 `/title/timeline/` の本実装（A2・B0 に確定、比較の仕組みを消す、固定バーは年の段だけ、og:image・テスト・文書）→ 未公開（noindex）のまま cloudflare へマージ → 公開の issue を起票 Chat-Ref: CHAT-1008-NEN-04 マージ: 承認済み（チャットで、2026-10-08。プレビューを見ずにマージまで進めてよい）。条件: 生成物の差分が決定とシートの変化で説明できるものだけ（見込み: `title/timeline/` の追加、`title/wrc/1.html`・`2.html` の対局日の年、`title/search.json` の2期の年、`_redirects` の1行、`img/ogp/title/timeline-black.png` の追加。これ以外の title/ の生成物が変われば止まる） 貼る時機: CHAT-1008-NEN-03 が「判断待ち」で止まった後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-nen の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-nen を続けて使う（NEN-03 の試作がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1008-NEN-03.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。NEN-03 の `## 報告` の状態の末尾に `/ 続き: CHAT-1008-NEN-04` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
NEN-03 の試作（比較ページ）を平野さんがプレビューで見て、表示の形・色分け・固定バーを決めた。決定どおりに本実装し、未公開（noindex・導線なし）のまま cloudflare に入れ、公開の issue を起票する（docs/new-page-checklist.md「段1」）。公開（noindex を外す・navbar・sitemap・`llms.txt`）はこの指示に含めない。
決定（2026-10-08、平野さん）

* 1期の表示の形は A2（小さい写真のカード）。色分けは B0（なし）（NEN-01 の grill Q4・Q5 の未決を決めた）
* 年ジャンプは NEN-03 の試作のとおりでよい（横に並べる・強調・`#y2025`）
* 年表のページの固定バーは、タイトル戦のプルダウン（「すべてのタイトル戦」）と検索ボックスを置かず、年の選択と共有ボタンだけにする（NEN-01 の grill Q8「固定バーは他の title/ のページと同じ」を置き換える）
* 本実装はプレビューを見ずにマージまで進めてよい（未公開のまま。公開の issue は起票する）

前提（チャット側。平野さんの決定ではない）

* 本実装で消す・足すものは NEN-03 の報告の一覧のとおり: 比較のラジオボタン（`timeline_compare_html()`・`TIMELINE_FORMS`・`TIMELINE_COLORS`・`.mj-tl-compare`）、採らない形 A1・A3 の HTML・CSS、色分け B1〜B3 の CSS と li のクラス（`is-houou` 等。B0 なので不要なら消す）、og:image `img/ogp/title/timeline-black.png`（文字「年表」。docs/notes/title-pages.md「OGP」のコマンド、黒地・白字。クラウドのセッションのフォントの取得も同節）と `og_image_for()` の対応、テスト（年の並び〈新しい年が上・年の中は大会の表示順・同じ大会は新しい期から〉・件数の見出し・優勝者のリンクの3通り・noindex・`sitemap-title.xml` に載せない・`NON_TAIKAI_DIRS`）、docs/notes/title-pages.md（年表の節を足す。「年を持たないのは第1・2回」の記述を今の実物〈0期〉に直す）、docs/notes/static-generation.md「ページの一覧」（「noindex・メニュー未掲載、公開は #NNN」）
* 固定バー: 年表のページだけプルダウンと検索欄を出さない（`filterbar_html()` の引数など、他の title/ のページの出力が変わらない形）。共有ボタンは今の位置（バーの中、年の段の前）のまま。`assets/title.js` が検索欄・プルダウン・「該当する選手はいません」の要素を前提にしていれば、無くても動くようにする（JS のエラーが出ない）。高さの仕組み（`--mj-title-filter-h`）は変えない
* 写真カード A2 の押す先は期ページ（grill Q7）。優勝者名のリンク（プロ一覧／X／なし）は A2 では置かない（カード全体が期ページへのリンクのため。NEN-03 の試作の A2 と同じ）。試作の A2 と違う点があれば報告に書く
* `test_title_ogp` の「置いた画像が実在の大会に当たる」は、`timeline-black.png` を `NON_TAIKAI_DIRS` の分として許す形に直す（timeline 以外の大会でない画像は引き続き落ちる）
* 公開の issue（docs/new-page-checklist.md「段1」の最後）: 題は「タイトル戦の年表 `/title/timeline/` を公開する」の形。本文は段2のチェックリストを写し、「#277（作る issue）の段1を待つ → この指示で済む」、公開の条件は「平野さんの実機での最終確認（iPhone Safari を含む）」、メニューの位置は「未決（navbar の『タイトル戦』の近くか、title/ の入口からのリンクか。公開の時に平野さんが決める）」、置き換える旧ページは「なし」、告知は docs/notes/page-announcement.md の型、と書く。ラベルは #277 と同じ「分野: UI/UX」。#277 に公開の issue の番号と「段1 済み」をコメントする（#277 は閉じない。閉じるかは公開の後に平野さんが決める）
* 決定の記録: docs/decisions/title.md に上の「決定」を「2026-10-08（CHAT-1008-NEN-04）」として足し、grill Q8 の固定バーの行に「→ 置き換え（固定バー）: 2026-10-08（CHAT-1008-NEN-04）」を付ける（README「書き方」）。docs/handover.md 5章の #277 の記述を「段1 済み（未公開）。公開は #NNN」に直す（警告域 26,624 の外であることを確かめる）
* 取り込みの衝突の扱い: docs/decisions/ の追記どうし、docs/handover.md・docs/notes/ の隣り合う行で両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけの衝突は CLAUDE.md「ブランチ運用」のとおり生成し直す。それ以外の衝突は解かずに止まる
* マージ後、docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから work/1008-nen を削除してよい（削除直前の先頭 SHA をログに残す）。削除はほかの検証の成否に条件づけない

手順

1. 確かめる: #277 が Open で、他セッションの着手中コメントが NEN-01 のもの以外に無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*` 節・`_redirects` を変えていない。同じ目的の公開の issue が無い（`gh issue list --state all --search "年表 公開"` など）。生成と同じ経路でシートを読み、表示する大会数と期数を報告する（止まる条件の範囲内であること）
2. 作る: 上の前提のとおり本実装・og:image・テスト・文書・決定を入れ、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分を種類に分けて報告する（「マージ:」の行の見込みと突き合わせる）。`python3 -m unittest discover -s scripts/tests` を通す。headless Chromium で `/title/timeline/` をスマホ幅（375px）と PC 幅（1280px）で開き、JS のエラーが無いこと・固定バーが年の段と共有ボタンだけであること・年ジャンプが動くこと・ラジオボタンが無いことを確かめ、ログに書く
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおりマージし、check-run（Workers Builds）を待つ（上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番の HTML（`curl`、URL に `?v=<未使用の値>`）で、`/title/timeline/` が 200 で noindex があること、`/title/timeline` が `/title/timeline/` へ 301、本番の `navbar.js`・`sitemap.xml`・`sitemap-title.xml`・`llms.txt` に timeline が無いことを確かめる。公開の issue を起票し、#277 にコメントする（末尾に Chat-Ref の行）。結果は docs/logs のみの追いの push で入れる。ブランチの片付けは上の前提のとおり

止まる条件

* #277 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている、同じ目的の公開の issue が既にある
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分が「マージ:」の行の見込みの外（他の title/ のページ・`sitemap-title.xml` が変わった。シートの変化で説明できるもの〈写真 URL の差し替えなど〉は種類と件数を書いて進んでよい）
* テストが落ちる（直せるのは timeline のために足した・直したテストだけ。既存のテストが落ちたら止まる）
* headless Chromium で JS のエラーが出る
* check-run が失敗した（原因を調べず報告に書いて止まる）
* docs/handover.md が警告域に入る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、生成物の差分の種類と件数の表、本番の確かめの表、公開の issue の番号を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-NEN-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-NEN-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-03 の状態は「判断待ち」だった。末尾に「/ 続き: CHAT-1008-NEN-04」を足した。このセッションは NEN-01〜03 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1008-NEN-04"` は0件
- ブランチ: `origin/work/1008-nen` はローカルと一致（c34ceb37）。`origin/cloudflare` が祖先でなかった（WKR-11・WKR-12 のマージで進んでいた。docs と `.github/workflows/update-live-channel.yml`・`workers/scheduler/`）ため `git merge origin/cloudflare`（衝突なし）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1 確かめ（止まる条件に当たった）

- #277: Open。着手中のコメントは NEN-01 のもの（同じセッション）だけ
- 同じ目的の公開の issue: 検索（年表 公開 timeline）で0件
- 未マージの `work/` ブランチ: `origin/work/1008-hou`（CHAT-1008-HOU-02、先頭 e25f04f4、2026-10-08 03:03 UTC、ログの状態「判断待ち」）が `_redirects` と `style.css` を変えている
  - `_redirects`: houou/ の3行を足す（`/houou  /houou/  301`・`/houou/  /houou/index.html  200` を books の行の後ろ、`/houou/:slug/  /houou/:slug/index.html  200` を末尾）。このブランチの `/title/timeline` の1行（teiou と wakajishi の間）とは場所が離れ、内容も重ならない
  - `style.css`: 末尾（3427行目以降）に houou/ の節を足すだけで、`.mj-title*` 節は変えていない
  - `title/`・`scripts/generate_title_pages.py`・`assets/title.js` は変えていない
- 止まる条件「未マージの `work/` ブランチが上のファイル（`_redirects` を含む）を変えている」に当たるため、実装に入らず止まる。どちらが先にマージされても、git の取り込みでは衝突しない見込み（行が離れている）

## 報告

- 状態: 判断待ち（work/1008-hou との `_redirects` の重なりを平野さんに質問中）
- ブランチ: work/1008-nen
- ログ: https://github.com/retroeater/mj/blob/work/1008-nen/docs/logs/CHAT-1008-NEN-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-nen
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと:
  - `work/1008-hou`（HOU-02、未マージ）が `_redirects`（houou/ の3行）と `style.css`（末尾の houou/ の節）を変えている。このまま進めてよいか（行は離れていて衝突しない見込み）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 88d5ee4b）: https://github.com/retroeater/mj-logs/tree/main/guide/88d5ee4b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cb2ba7f5.md
