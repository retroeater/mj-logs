# CHAT-1019-EXM-06

- 着手日時: 2026-10-19
- 対象issue: #613
- ブランチ: work/1019-exm
- 着手時HEAD: 77431408e

## 指示

【Claude作成】Claude Code 向け指示：/exam/ を公開する（#613。メニュー「番組」へ移す・jpml_test.html を /exam/ へ 301・旧ページの生成をやめる） Chat-Ref: CHAT-1019-EXM-06 マージ: 承認済み（チャットで。2026-10-19 に平野さんが「確かめが全部通ったらマージして公開まで済ませてよい」と答えた） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-exm の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-exm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-exm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-EXM-05 で未公開のまま本番に入れた `/exam/`（プロテストの動画12本）を、`docs/new-page-checklist.md` の段2のとおり公開し、旧「連盟 > プロテスト」（`jpml_test.html`）を置き換える。
決定（2026-10-19、平野さん）

* 公開は今すぐ行う
* メニューは「連盟」から「プロテスト」を外し、「番組」の「Mつく」の下（女流勉強会の公開後は、その下）・「English」の上に置く。つまり今は 放送対局・帰り道・Mつく・プロテスト・English の順。女流勉強会（#576）は公開のとき「Mつく」と「プロテスト」の間に入る
* 旧 `jpml_test.html` は廃止して `/exam/` へ 301（2026-10-19 の決定のとおり。連盟サイトの記事22件は載せない）。`?name=` は引き継がない（`/exam/` は `?name=` を読まない。転送は一覧へ）
* 確かめが全部通れば、マージして公開まで済ませてよい

前提（チャット側。平野さんの決定ではない）

* 2026-10-19 にチャット側が読んだ本番: `/exam/` は 200・noindex・題「プロテスト | 番組 | ryoei.pro」・12本。`navbar.js` の「連盟」に `/jpml_test.html` の「プロテスト」、「番組」に 放送対局・帰り道・Mつく（`/mtsuku/`）・English（`/video_en.html`）（origin/cloudflare の `navbar.js` で確かめた。要確認）
* 公開の作業の形は Mつくの公開（#570。`docs/notes/video-mtsuku.md` の冒頭: navbar・`sitemap-mtsuku.xml` を `sitemap.xml` から参照・`llms.txt`、旧生成スクリプトを消して旧 URL を 301）に倣う。#613 の本文の段2のチェックリストも見る
* 旧ページの後片付け: `generate_jpml_test.py`・`jpml_test.html`・それだけが使う JS・テスト・`regenerate.py` の登録・ほかの検査（title 系の検査・`check_unused_names.py`・SNS ブックの利用先など、`jpml_test` を名指しする所）を、参照を洗い出して外す。共有の `table.js` など、ほかのページも使うものは残す（要確認）。旧ページが読んでいたスプレッドシートのシート（`generate_jpml_test.SPREADSHEET_ID` のブックの、記事と動画の行があるシート）を、ほかに読む所が無くなるかを報告に書く（シートは消さない。消すかは平野さんが決める）
* 転送は `docs/notes/redirects.md`（転送の一覧、RDR）にも載せる（要確認。一覧の形に合わせる）
* 公開後に平野さんが行う手作業は、Mつくの公開と同じ形（Search Console の `sitemap.xml` の再送信、`/exam/` の URL 検査とインデックス登録のリクエスト、スマホの実機でメニューから開く）を見込む

手順

1. #613 に着手中コメントを書く（他セッションの着手中コメントがあれば止まる）。`git branch -r --no-merged origin/cloudflare` の各ブランチが、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・`scripts/regenerate.py` の同じ行を変えている、または取り込みで衝突するかを確かめる（同じファイルの別の箇所なら止まらずログに書く）。旧ページの参照を全部洗い出して `## 経過` に表で書く
2. 公開と後片付けを行う: `exam/` の noindex を外す（生成スクリプトの定数の1か所）、`navbar.js` を上の決定の並びに直す、`/exam/` のサイトマップを足して `sitemap.xml` から参照、`llms.txt`、`_redirects` に `/jpml_test.html` → `/exam/` の 301（クエリは引き継がない形。Cloudflare の `_redirects` の既定の動きを確かめて書く）、旧ページの生成スクリプト・ページ・専用の JS・テスト・登録を消す、`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/video-exam.md`・`docs/notes/redirects.md`・`docs/decisions/exam.md`（この指示の決定）・#576 の本文の段2（女流勉強会の公開時の位置が「Mつく」と「プロテスト」の間になること）を直す
3. 確かめてマージする: テスト、`python3 scripts/regenerate.py all` を作業ブランチと origin/cloudflare のクローンで回し、差分が「`exam/` の noindex の行」「navbar を描く全ページの変化があればその種類」「サイトマップ・`llms.txt`・`_redirects`」「消した旧ページ」とデータの変化（両方の木で同じく変わるもの。コミットしない）だけであることを確かめる、容量の検査、work/ ブランチのプレビューで PC とスマホ幅のメニュー（連盟・番組を開いた状態）と `/jpml_test.html` と `/jpml_test.html?name=部谷幸則` の転送先を確かめて画像を `docs/logs/CHAT-1019-EXM-06/` に置く。通ればマージし（docs/notes/branch-operations.md「マージするとき」）、本番で `/exam/` が 200・noindex なし、`/jpml_test.html` が `/exam/` へ 301、メニューの並び、`sitemap.xml` からの参照を確かめる。#613 に結果と平野さんの手作業（上の前提の3つ）を書く。#613 はクローズせず「状況: 待ち」にして、平野さんの手作業の報告を待つ

止まる条件

* 他セッションの着手中コメントがある、未マージの work/ ブランチと同じ行を変えている・取り込みで衝突する
* 旧ページの参照の中に、消すと別のページ・検査の動きが変わるもの（共有の JS・関数など）があり、残すか消すかを決められない（表を書いて止まる）
* `regenerate.py all` の差分に、上の種類で説明できない変化がある
* テスト・容量の検査が通らない、プレビューでメニューや転送が決定のとおりでない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run・`regenerate-page.yml` の失敗: 今回の変更による失敗なら原因を書いて止まる。無関係な失敗なら原因を報告に書いたうえで、本番の確かめと #613 への記録を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-EXM-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-EXM-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git log --all --grep=CHAT-1019-EXM-06` と `docs/logs/` の履歴に無い
- 作業ブランチ: ローカル・リモートの `work/1019-exm` は cloudflare にマージ済み（祖先）。docs/notes/cloud-sessions.md「作業ブランチの用意」の「ローカルにあり origin/cloudflare の祖先」のとおり `git merge --ff-only origin/cloudflare` で進めた（77431408e）

## 報告

- 状態: 作業中
- ブランチ: work/1019-exm
- ログ: https://github.com/retroeater/mj/blob/work/1019-exm/docs/logs/CHAT-1019-EXM-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-exm
- 確認用URL: なし
- マージ: 未
- issue: #613
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a91c3d04）: https://github.com/retroeater/mj-logs/tree/main/guide/a91c3d04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a91c3d04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
