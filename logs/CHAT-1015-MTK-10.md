# CHAT-1015-MTK-10

- 着手日時: 2026-10-15
- 対象issue: #570・#569
- ブランチ: work/MTK
- 着手時HEAD: c1f4409

## 指示

【Claude作成】Claude Code 向け指示：Mつく（/mtsuku/）の公開の続き — 写真の URL が入った後に生成し直してマージする（#570） Chat-Ref: CHAT-1015-MTK-10 マージ: 承認済み（チャットで。平野さんが 2026-10-15 に公開を決めた。下の「止まる条件」に当たらなければマージしてよい） 貼る時機: SNS ブックの更新（`update-sns-book.yml`、平野さんの手動実行か毎朝 04:10 JST の実行）が成功で終わった後（CHAT-1015-MTK-09 の続き。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/MTK の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/MTK を続けて使う（CHAT-1015-MTK-09 の公開の変更〈db12674〉の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物（`*.html`・サイトマップなど）の衝突は、取り込んだ後に生成し直して解いてよい。それ以外の衝突は解かずに止まる。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-MTK-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MTK-10` を足す。 SNS ブックの更新（`update-sns-book.yml`）の、MTK-09 の報告より後の実行が成功で終わっていることを確かめ（run の番号・開始時刻・結論をログに書く）、無ければ何もせず止まる。

目的
CHAT-1015-MTK-09 は、七瀬美希・設楽遙斗（平野さんが SNS ブックの【2】に X の ID を入れた）の写真の URL がまだ【3】に無く、決めた範囲（X の ID があって写真の URL が無い人は止める）で Mつくの生成が止まった。SNS ブックの更新で写真の URL が入った後に、生成し直して公開する。
決定（2026-10-15、平野さん）

* いますぐ公開する（#570）。CHAT-1015-MTK-09 の決定はそのまま
* 生成を止める範囲（X の ID があって写真が読めない・写真の URL が無い人は止める）はそのまま。止まった人を外して公開しない

前提（チャット側。平野さんの決定ではない）

* 佐藤聖誠は【2】の X が「-」（2026-10-15、平野さん）。止まる理由にならず、写真は代替アバター
* SNS ブックの更新で【3】が変わると、cloudflare の全ページの再生成（all）が呼ばれる。MTK-08 で入った未公開の /mtsuku/ もそこで生成される（要確認。成功していれば、その結果を取り込む）

手順

1. `python3 scripts/regenerate.py --check mtsuku mtsuku_episodes` で止まる理由が無いことを確かめる（七瀬美希・設楽遙斗の写真の URL をログに書く）
2. MTK-09 の手順の続き: noindex を外した一覧・各話を生成し直し、MTK-09 の変更（navbar・サイトマップ・`llms.txt`・`_redirects`・旧ページの削除・文書）と合わせて、テスト・`--check all`・サイトマップの parse を確かめ、cloudflare へマージする
3. MTK-09 の手順3（公開後の確かめ）を行う: Workers Builds の check-run、本番の HTML（`/mtsuku/`・各話が 200 で noindex と `x-robots-tag` が無い、`navbar.js`・`sitemap.xml`・`sitemap-mtsuku.xml`・`llms.txt` に URL がある、`/video_mtsuku.html` と `/video_mtsuku.html?name=魚谷` の転送の結果）。#569 に公開した日とマージの SHA をコメントしてクローズする。#570 には済んだ項目を書き、平野さんの作業が残るのでクローズしない

止まる条件

* 0章の SNS ブックの更新が無い・失敗している
* 手順1でまだ止まる理由がある（その一覧を書いて止まる）
* MTK-09 の「止まる条件」に当たる（ほかのページへの差分、`--check all`・テスト・assets-check の失敗〈無関係なものは内容を書いて進めてよい。データ・コンシェルジュの Worker のテストの1件は既知〉、未マージのブランチとの重なり、cloudflare への push の拒否）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんに残る作業（実機・X と LINE のカード・Search Console の `sitemap.xml` 再送信と入口の URL 検査）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/MTK はローカル・リモートとも c1f4409（未マージ）
- 雛形の行はすべてあり（貼られた文では改行が詰まっていた）。0.「指示」欄の末尾は指示文の最後の行と一致。MTK-09 のログの状態に「 / 続き: CHAT-1015-MTK-10」を足した
- SNS ブックの更新: MTK-09 の報告（c1f4409、06:55 UTC）より後の実行は #29（run 38696400299、workflow_dispatch、cloudflare、2026-10-15 07:05 UTC = 16:05 JST 開始、結論 success）

## 報告

- 状態: 作業中
- ブランチ: work/MTK
- ログ: https://github.com/retroeater/mj/blob/work/MTK/docs/logs/CHAT-1015-MTK-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/MTK
- 確認用URL: なし
- マージ: 未
- issue: #570・#569
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 23eb8ffb）: https://github.com/retroeater/mj-logs/tree/main/guide/23eb8ffb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/23eb8ffb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
