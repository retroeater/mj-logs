# CHAT-1015-MTK-09

- 着手日時: 2026-10-15
- 対象issue: #570・#569
- ブランチ: work/MTK
- 着手時HEAD: 4a36152

## 指示

【Claude作成】Claude Code 向け指示：Mつく（/mtsuku/）を公開する（#570、段2）— noindex を外し、navbar・サイトマップ・llms.txt に載せ、旧 video_mtsuku.html を 301 Chat-Ref: CHAT-1015-MTK-09 マージ: 承認済み（チャットで。平野さんが「いますぐ公開する」と決めた。下の「止まる条件」に当たらなければマージしてよい） 貼る時機: いつでも（CHAT-1015-MTK-08 の完了の後。MTK-08 と同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/MTK の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/MTK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/MTK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-MTK-08 のログの `## 報告` が「完了」であることを確かめ、そうでなければ何もせず止まる。

目的
未公開で本番に入れた /mtsuku/（CHAT-1015-MTK-08、cloudflare 1e1cc49）を公開する。docs/new-page-checklist.md の段2と #570 の本文のチェックリストに沿う。
決定（平野さん）

* 2026-10-15: いますぐ公開する（#570）。平野さんはプレビュー（CHAT-1015-MTK-07）と MTK-08 の報告を見て公開を決めた
* 2026-10-14（CHAT-1014-MTK-01）: 公開と旧 URL の 301 は #570 で行う。旧 `video_mtsuku.html` は `/mtsuku/` へ 301 で転送する
* 2026-10-15（CHAT-1015-MTK-05）: 旧ページの生成は #570 の公開のときに消す（`generate_video_mtsuku.py` と `FROZEN_FROM_ALL` の `video_mtsuku`）
* 2026-10-15（CHAT-1015-MTK-04）: 再生時間はシートに手で入れる。YouTube の情報を週次で自動で取る仕組みは作らない（#570 の本文の公開の条件の2つ目〈`fetch_youtube_meta.py --series mtsuku` をワークフローに入れる〉はこれで要らなくなった）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 公開前の Search Console の数字は残さない（チャット側の案。Mつくの旧ページの流入はわずかと見込む。要るなら平野さんが後で言う）
* navbar は「動画」の Mつくの項目のリンク先を `video_mtsuku.html` から `/mtsuku/` に差し替える。項目名・位置は変えない
* 旧 URL の転送: `_redirects` に `/video_mtsuku.html /mtsuku/ 301`（帰り道 `/video_wayhome.html` と同じ形）。`?name=` が転送先に引き継がれるかを本番で確かめる（引き継がれなければ、そのことを報告に書く。この指示では直さない）。`docs/notes/redirects.md` の一覧に行を足す。`video_mtsuku.html` のファイルは消す（転送が先に効くので配信されない。帰り道と同じ扱いか、実物で確かめる）
* 一覧は canonical を出さない（`?name=` の別形を持つ）。各話は canonical あり（今のまま）
* サイトマップは、帰り道（`sitemap-wayhome.xml`）と同じ形を案とする（各話の生成が `sitemap-mtsuku.xml` を書き出し、`sitemap.xml` から参照する）。lastmod は手で書かない。`update_sitemap_lastmod.py` と、`regenerate-page.yml` の well-formedness の一覧に要る変更があれば入れる（ワークフローの変更は、その一覧への1行の追加だけ）
* `llms.txt` に入口の行を足す（帰り道の行に倣う）

手順

1. #570 の「着手前」を済ませる（公開の条件が揃ったことを #570 に書く。YouTube の情報の取得は不要になったことも書く）。noindex を外して一覧・各話を再生成し、「公開していない」と書いたコメント・docstring・生成物の説明を直す。旧ページの生成（`generate_video_mtsuku.py`・`FROZEN_FROM_ALL` の `video_mtsuku`）を消す
2. navbar・サイトマップ・`llms.txt`・`_redirects` を直し、`docs/notes/redirects.md`・`docs/notes/static-generation.md`「ページの一覧」・Mつくの実装記録・`docs/handover.md` の「未公開」の記述・`docs/decisions/`（MTK と同じ分野のファイル）を直す。テストと `python3 scripts/regenerate.py --check all` を回し、サイトマップを push 前に `xml.etree.ElementTree` で parse する
3. cloudflare へマージし、#570 の「公開後の確かめ」のうち Code ができるものを行う（Workers Builds の check-run、本番の HTML〈`/mtsuku/`・各話が 200 で noindex と `x-robots-tag` が無い、`navbar.js`・`sitemap.xml`・`sitemap-mtsuku.xml`・`llms.txt` に URL がある、`/video_mtsuku.html` と `/video_mtsuku.html?name=魚谷` の転送の結果〉）。#569 に公開した日とマージの SHA をコメントしてクローズする。#570 には済んだ項目を書き、平野さんの作業（実機・X と LINE のカード・Search Console）が残るのでクローズしない

止まる条件

* MTK-08 の後に cloudflare で /mtsuku/ の生成物が変わっていて、再生成で説明できない差分が出る
* 帰り道・最強戦・/live などほかのページの生成物に、この変更による差分が出る（navbar.js の変更で全ページが変わるのは見込みどおり。その場合は navbar.js の差分だけであることを確かめる）
* `--check all`・テスト・assets-check が、この変更によって落ちる（無関係な失敗は内容を報告に書いて進めてよい。データ・コンシェルジュの Worker のテスト `query.test.mjs`「記録の上位」の失敗は既知で HOU に伝えてある）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `navbar.js` の「動画」の項目・`_redirects` の同じ行・`sitemap.xml`・`llms.txt` の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の Workers Builds の失敗は、この変更によるものか無関係かを分けて報告する（無関係なら本番の確かめを進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんに残る作業（実機・X と LINE のカード・Search Console の `sitemap.xml` 再送信と入口の URL 検査）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。MTK-08 のログの `## 報告` は「状態: 完了」（cloudflare 上で確かめた）
- work/MTK はローカル・リモートとも cloudflare にマージ済み → ローカルの work/MTK を `git merge --ff-only origin/cloudflare` で進めた（4a36152）
- 雛形の行はすべてあり（貼られた文では改行が詰まっていた）。0.「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/MTK
- ログ: https://github.com/retroeater/mj/blob/work/MTK/docs/logs/CHAT-1015-MTK-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/MTK
- 確認用URL: なし
- マージ: 未
- issue: #570・#569
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a361525）: https://github.com/retroeater/mj-logs/tree/main/guide/4a361525

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a361525/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
