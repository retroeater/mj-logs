# CHAT-1016-HOU-63

- 着手日時: 2026-10-16
- 対象issue: #540（関係: #518・#542・#520・#141・#371・#228・#541）
- ブランチ: work/1016-hou4
- 着手時HEAD: 55e02ba7a

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦 houou/ の公開（#540）の準備 — 旧4ページの301・リンクの張り替え・ナビの「鳳凰戦」の1段化・noindex とサイトマップと llms.txt・質問欄の1日の上限200問・告知動画のスクリプトの取り込みを作業ブランチで作り、プレビューで確かめ、告知の投稿文の案を書く Chat-Ref: CHAT-1016-HOU-63 マージ: 判断待ちで止まる（公開は戻しにくいため、プレビューと投稿文の案を平野さんが見てから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（CHAT-1016-HOU-62 は判断待ちで、平野さんは v4 で確定と回答済み。同じセッションでよい。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-hou4 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-hou4 を origin/cloudflare から作る（リモートにあってマージ済みでなければ止まる。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。作った後、未マージの work/1015-hou5（告知動画の制作のスクリプト、HOU-48・60〜62）を merge で取り込む（rebase しない）。取り込みで生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `docs/decisions/houou.md` の末尾の追記どうし）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は CLAUDE.md「ブランチ運用」のとおり。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1016-HOU-62 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
告知動画が確定し、公開（#540）の前の条件はそろった。公開と告知は同時に行う決定なので、公開に要る変更を1本の作業ブランチにまとめ、プレビューで確かめ、告知の投稿文の案まで用意する。cloudflare へ入れるのは、平野さんが見た後の別の指示。
決定（平野さん）

* 告知動画は houou-promo-v4.mp4 で確定（2026-10-16、HOU-62 への回答）。HOU-61 の決定（ⓘ を開く所を外す）・HOU-62 の決定（ランキングは「期平均」→「期最高」で終わる・通算のグラフを全体が映るまでスクロール）を含む
* ナビの「鳳凰戦」は1段だけにし、押すと `/houou/` へ行く。個人成績・ランキング・順位変動・成績詳細などの下の項目はナビに出さない。旧ページも含め、どのページのナビも同じ（2026-10-16）
* 質問欄の1日の上限は200問にする（2026-10-16、試行のまとめへの回答 a）
* 公開（#540）と告知は同時に行う。動画が確定してから公開の指示を出す（2026-10-16）
* 1週間の試行は待たない。公開の条件から週次の確かめを外す（2026-10-16、HOU-52 の決定。記録済み）
* 2026-10-14 の決定（docs/decisions/houou.md に記録済みのものは再掲しない。未記載なら、この指示で足す）:
   * 旧 `houou_ranking.html` は `houou/ranking/total/` へ 301（title/houou/ へは向けない）
   * 旧 `houou_leagues.html` は `houou/leagues/` へ 301 し、そこから JS で移す。`houou/leagues/` は公開後も noindex・サイトマップに載せない
   * jpml_pros など他ページの旧4ページへのリンクは新しい URL へ直接張り替える。jpml_pros の行き先は個人成績の期別 `houou/players/?name=`（view なし）
   * 旧4ページのファイル・生成は公開では残し、後始末は別の issue
   * og:title は `<title>` と同じ文言のまま。公開前の Search Console の基準値は既存の `docs/gsc/2026-10-09/` を使い、取り直さない
   * X で告知する

前提（チャット側。平野さんの決定ではない）

* 旧4ページの転送（`_redirects`、301）: `houou_ranking.html` → `/houou/ranking/total/`・`houou_leagues.html` → `/houou/leagues/`・`houou_race.html` → `/houou/race/`・`houou_results.html` → `/houou/players/`。クエリ（`?name=`・`?division=`・`?sheet=` など）は行き先へ引き継がれる見込み（HOU-37 で `houou/ranking/` の 301 は引き継がれた）。行き先で `?name=` が効くこと、`houou_ranking.html?sheet=鳳凰` が `total/` で害なく開くこと、`?division=<部門名>` が部門のページへ移ることを確かめる。食い違えば止まらず、どうしたかと理由をログに書く
* リンクの張り替え: HOU-37 の表のとおり（`jpml_pros.html` の `houou_results.html?name=`〈690件〉と `houou_leagues.html?name=`〈715件〉を、生成元 `scripts/generate_jpml_pros.py` の `get_internal_link()` で両方とも `houou/players/?name=` に。`scripts/tests/test_jpml_pros.py` の期待値も直す）。件数は今の数を数え直してログに書く。HOU-37 の後に旧4ページへのリンクが増えていないかを `git grep` で洗い直す（docs/ と旧4ページの中を除く）。`scripts/promo_video/houou_race/capture.mjs` の撮影の URL は 301 で着くので変えない
* ナビ: `navbar.js` の「鳳凰戦」のドロップダウンを、`/houou/` への1段の項目にする。見た目は、ナビで下の項目を持たない他の項目と同じ形にそろえる。houou/ の下のどのページを開いていても「鳳凰戦」が選ばれた状態に見えるようにする（houou/ のページのナビが `navbar.js` と別の作りなら、そちらも同じにし、どこを直したかをログに書く）
* noindex・サイトマップ・`llms.txt`: houou/ の noindex を外す（`houou/leagues/` だけ残す）。`sitemap*.xml` に houou/ のページ（トップ・個人成績・ランキング9部門・順位変動。`houou/leagues/` は載せない）を足し、旧4ページの `<loc>` を外し、冒頭のコメントの件数を直す。`llms.txt` の旧4ページの行を新しい URL の行に置き換える。手順は docs/new-page-checklist.md の公開の段のとおり
* 質問欄の上限: 1日の上限（全体）を200にする。置き場（Worker の設定・`wrangler.toml`・D1 など）は docs/notes/ask-worker.md で確かめる。リポジトリの外（Cloudflare の画面）にしか無い設定なら、変えずに、平野さんに頼む画面と操作を報告に書く。IP ごとの上限は変えない
* 転送の文書: `docs/notes/redirects.md` に4行を足す（形は今の行に合わせる）。`_redirects` の行の順は、`/houou/:slug/` などの既存の行に先に当たらない位置にする（HOU-37 の `houou/ranking/` と同じ注意）
* 告知動画: work/1015-hou5 の取り込みで入る制作のスクリプトと README のほか、`docs/notes/page-announcement.md` の実例の表に houou/ の行を足し、`docs/decisions/houou.md` に動画の確定（v4）を足す。動画・連番・素材はコミットしない
* 告知の投稿文の案: `docs/notes/page-announcement.md` の型で、X の投稿文の案を1〜2通り書く（URL は `https://ryoei.pro/houou/`、動画を付ける前提）。ログと報告に全文を書く。投稿するのは平野さん
* #540 の本文: HOU-37 の突き合わせ（#540 のコメント）と 2026-10-14〜16 の決定に合わせて直す（済んだ項目のチェック、決まった行き先、閉じる issue〈#518・#520・#141・#371・#228。本文が正〉）。#541 は公開の後に回す決定のまま
* 旧4ページの後始末の issue は、マージの指示で起票する（この指示では起票しない。本文の案を報告に書く）

手順

1. 着手の確かめ: CHAT-1016-HOU-62 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-HOU-63` を足す。#518・#540・#542 に他のセッションの着手中のコメントが無いか確かめ、#540 に着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチ（work/1015-hou5 を除く）に、`_redirects`・`navbar.js`・`sitemap*.xml`・`llms.txt`・`scripts/generate_jpml_pros.py`・`scripts/generate_houou_pages.py`・`workers/ask/` の変更があるかを確かめ、ログに書く。
2. 実装: work/1015-hou5 を取り込み、前提のとおりに直す。テストを必要なだけ足し・直して `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。`python3 scripts/regenerate.py houou_pages` と jpml_pros の生成をやり直し、差分（origin/cloudflare との比較）を種類に分けてログに書く。決定を `docs/decisions/houou.md` に足し、`docs/notes/houou-top.md`「非公開の形と公開の方針」・`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/ask-worker.md` を公開後の形に直す（同じ趣旨の記述があれば置き換える）。
3. プレビューでの確かめ: Workers Builds のプレビュー（待つのは15分まで）で、旧4ページの 301 と行き先（クエリ付きを含む）、`jpml_pros.html` の鳳凰戦の2欄のリンク（数件）、ナビの「鳳凰戦」（他のページと houou/ の下のページ、375px と PC 幅、明・暗）、houou/ の各ページの noindex が無いこと・`houou/leagues/` には残ること、サイトマップと `llms.txt` の中身を確かめる。ナビの PNG（375px・明、他のページと houou/ランキングの2枚）を `SendUserFile` で送る。
4. 告知と issue: 投稿文の案と、後始末の issue の本文の案をログに書く。#540 の本文を直し、プレビューの結果の要約のコメントを書く（末尾に `Chat-Ref:` の行）。

止まる条件

* CHAT-1016-HOU-62 のログの状態が判断待ちでない、または work/1015-hou5 がリモートに無い
* #518・#540・#542 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチ（work/1015-hou5 を除く）が手順1のファイルを変えていて、取り込みで衝突する
* 取り込みで、上に書いた解き方で解けない衝突が出た
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* 生成の差分に、決定と前提で説明できない種類がある（ランキングの値・順位が変わったら止まる。houou/・jpml_pros・ナビ・サイトマップ・`llms.txt`・`_redirects`・質問欄の上限の外の生成物が変わったら止まる）
* プレビューで、旧4ページの転送が 301 にならない、または行き先が 404 になる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、(1) プレビューと送った PNG を見て公開のマージをしてよいか、(2) 投稿文の案のどれにするか（直すならどこか）、(3) 質問欄の上限が平野さんの手作業になる場合はその画面と操作、を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-HOU-63.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-HOU-63 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし（`git log --all --grep`、unshallow 後）。CHAT-1016-HOU-62 のログ（work/1015-hou5）の状態は「判断待ち」。origin/work/1015-hou5 はある。origin/work/1016-hou4 は無いので origin/cloudflare（55e02ba7a）から作った。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。「指示」欄の末尾は指示文の最後の行と一致

### 手順1 着手の確かめ

- CHAT-1016-HOU-62 のログの状態の末尾に ` / 続き: CHAT-1016-HOU-63` を足した（取り込んだ後の work/1016-hou4 の上で）
- 着手中のコメント: #518・#542 は全件「解除」済みか、後続のマージ報告がある。#540 の最後の着手中は HOU-48・60〜62（セッション 01Mx6…、work/1015-hou5）で、この指示の前段（判断待ち。続きがこの指示）なので他セッションの作業には当たらないと判断した。#540 に着手中のコメントを書いた
- `git branch -r --no-merged origin/cloudflare`（work/1015-hou5・1016-hou4 を除く5本: work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1016-skr-rank）は、`_redirects`・`navbar.js`・`sitemap*.xml`・`llms.txt`・`scripts/generate_jpml_pros.py`・`scripts/generate_houou_pages.py`・`workers/ask/` のどれも変えていない（`git diff --name-only origin/cloudflare...<枝>` が空）
- work/1015-hou5 を `git merge` で取り込んだ。衝突なし（`docs/decisions/houou.md` は自動で合わさった）

## 報告

- 状態: 作業中
- ブランチ: work/1016-hou4
- ログ: https://github.com/retroeater/mj/blob/work/1016-hou4/docs/logs/CHAT-1016-HOU-63.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-hou4
- 確認用URL: なし
- マージ: 未
- issue: #540
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9901aa99）: https://github.com/retroeater/mj-logs/tree/main/guide/9901aa99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9901aa99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
