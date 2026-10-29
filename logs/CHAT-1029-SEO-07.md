# CHAT-1029-SEO-07

- 着手日時: 2026-10-29
- 対象issue: #5
- ブランチ: work/1029-seo7
- 着手時HEAD: 2be723565

## 指示

【Claude作成】Claude Code 向け指示：Mつくの各話で、チーム名の無い回の題を「作る人（公開日）」にし、条件を満たせば cloudflare へマージする
Chat-Ref: CHAT-1029-SEO-07
マージ: 承認済み（チャットで。2026-10-29、平野さん「マージしてよい」。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる）
貼る時機: CHAT-1029-SEO-06 の完了（work/1029-seo4 の手順3〈動画の各話の JSON-LD の寄せ替え〉が cloudflare にマージ済み、または手順3だけ止まった）の後。SEO-06 が Mつくの生成のスクリプトを変えるため
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo7〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo7 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo7 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo7 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1029-SEO-06 のログの `## 報告` の状態が「完了」か、手順3だけ止まった「判断待ち」であることを確かめ、どちらでもなければ何もせず止まる。

## 目的
Mつくの各話の `<title>`・og:title・前後のリンク・パンくず（`BreadcrumbList`）は「「チーム名」 作る人」と決まっている（docs/notes/video-mtsuku.md「帰り道との違い」）。シートの「チーム名」が空の5回はチーム名の無い回で、今は作る人の名前だけになり、滝沢和典の2回（2020-02-04・2024-08-10）の title が同じになっている（CHAT-1029-SEO-01 の②-4、CHAT-1029-SEO-02 の S3。#5 に移した）。チーム名の無い回の題を「作る人（公開日）」にして重複をなくす。

### 決定（2026-10-29、平野さん）
- シートの「チーム名」が空の回は、チーム名の無い回である（シートに入れる値は無い）
- チーム名の無い回の題（`<title>`・og:title・前後のリンク・パンくずの名前）は「作る人（公開日）」にする。例: 「滝沢和典（2020年2月4日） | Mリーグチームを作ろう！ | ryoei.pro」
- 下の「止まる条件」の差分の範囲に収まれば、プレビューを見ずに cloudflare へマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- 対象の5回（CHAT-1029-SEO-02 の報告から。シートは鍵が無く未照合）: nLeGJz-nt-E（2019-12-03 園田賢）・CtDQ5NjWSN0（2020-01-02 多井隆晴）・2FOsXsIqTAA（2020-02-04 滝沢和典）・f7__Vg7EUNE（2020-04-29 沢崎誠）・vSduV7-kLls（2024-08-10 滝沢和典）。生成で「チーム名が空」と判定される回がこの5回と違えば、全件を書いて止まる
- 公開日の書き方は、カードと前後のエピソードの日付の決まり「2026年8月27日」（月日はゼロ埋めしない）に合わせる。正は Mつくの生成が今カードの日付に使っている関数（docs/notes/video-mtsuku.md と、帰り道の系列 WHS の決定）。新しい書式の関数は作らない
- チーム名のある回の題は変えない。h1（作る人の名前だけ、2026-10-15 の決定）・紹介文（meta description・og:description）・「〇〇の他のエピソード」の束ね方は変えない
- docs/notes/video-mtsuku.md「帰り道との違い」の題の決まりの行に、チーム名の無い回の形を1句足す（追記先の今の内容を読む）
- 生成は `regenerate-page.yml` で行う（コンテナにはシートの鍵が無い）。先に作業ブランチで `dry_run`・`compare_ref`（その時点の origin/cloudflare）を実行し、新旧のコードで同じデータから生成した差を見る

## 手順
1. Mつくの生成で、題を組む箇所（`<title>`・og:title・前後のリンク・パンくずが同じ文字列を使っているか）を読み、チーム名が空のときに「作る人（公開日）」を返すように直す。テストを足す（チーム名が空の回の題に公開日が入ること・同じ作る人の2回の題が違うこと・チーム名のある回の題が変わらないこと）。docs/notes/video-mtsuku.md を上の前提のとおり直す
2. `regenerate-page.yml` を work/1029-seo7 で `dry_run`・`compare_ref` 付きで実行し、差の行を全件分類してログに書く（ファイル名・行の種類〈title・og:title・パンくず・前後のリンク・ほか〉・前・後）。下の「止まる条件」の範囲に収まれば、書く実行で生成してコミットする
3. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/mtsuku.md に足し、ログを仕上げて `git push origin work/1029-seo7:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で `mtsuku/2FOsXsIqTAA.html`・`mtsuku/vSduV7-kLls.html` の `<title>` と、チーム名のある回1件の `<title>` が変わっていないことを確かめてログに書く。#5 に結果をコメントする（S3 の分は済んだと書く。本文・状態・ラベルは変えない）。CHAT-1029-SEO-05 のログには触れない（完了済み）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 0章の前提（SEO-06 の状態）が満たされない（何もせず止まる）
- チーム名が空と判定される回が、上の前提の5回と違う（全件を書いて止まる）
- 手順2の差に、次の範囲の外の変化が1つでもある（全件を書いて、マージせずに止まる）: (a) 対象の5回のページの `<title>`・og:title・パンくずの名前の、作る人の名前の後への「（YYYY年M月D日）」の追加 (b) mtsuku/ の各ページの前後のリンク・パンくずで、対象の5回を指す文字への同じ追加 (c) 生成の後に動く sitemap の `<lastmod>`（変わったページの分だけ）。ほかのシートの変化による差が混ざって切り分けられないときも止まる
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが、Mつくの生成の同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo7
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo7/docs/logs/CHAT-1029-SEO-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo7
- 確認用URL: なし
- マージ: 未
- issue: #5
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e6e91d58）: https://github.com/retroeater/mj-logs/tree/main/guide/e6e91d58

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6e91d58/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
