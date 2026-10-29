# CHAT-1029-SEO-04

- 着手日時: 2026-10-29
- 対象issue: #13・#101・#219・#159・#362・#640・#296
- ブランチ: work/1029-seo4
- 着手時HEAD: 4aa1e3f28

## 指示

【Claude作成】Claude Code 向け指示：新サイトの要件のうち N1（トップの title・h1・description）と N5（構造化データ）を現行サイトに前倒しで入れ、プレビューで止める
Chat-Ref: CHAT-1029-SEO-04
マージ: 判断待ちで止まる
貼る時機: いつでも（SEO-02・SEO-03 とは別のセッションに貼る。SEO-02 が先にマージされていれば、取り込んでから始める）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo4〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo4 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo4 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo4 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1029-SEO-01 のレビューで「新サイトの要件」とした N1〜N5 のうち、今の作りで実装できる N1・N5 を現行サイトに前倒しで入れる。あわせて、新サイト送りの記録（docs/handover.md 4章・#13・#296）と、前倒ししない N2〜N4 の受け皿の issue を直す。プレビューで止め、平野さんの確認の後に別の指示でマージする。

### 決定（2026-10-29、平野さん）
- 新サイト（#296）の要件のうち、現行サイトでも実装できるものは前倒しで実装する
- 前倒しするのは N1（トップの title・h1・description）と N5（構造化データ）。N2（`jpml_pros.html?name=` の title）は前倒ししない
- N3（players/・race/ の本文が JS で描かれる）は #219 のまま、N4（video_live・resource_logs の大きな HTML）は live/（#362）・log/（#640）の置き換えに任せる
- 新サイトの要件 N1〜N5 は、新しい issue を作らず、受け皿の issue（#101・#219・#159・#13・#362・#640）にコメントで足す

### 前提（チャット側。平野さんの決定ではない）
- N5 の範囲（チャット側の案）: (a) 公開中の全ページに、見えているナビ・ディレクトリの階層に合う `BreadcrumbList` を入れる（今は動画の各話116件だけにある。docs/notes/video-mtsuku.md など） (b) トップ（`/`）に `WebSite`（name・url）を入れる（検索窓の `SearchAction` は入れない） (c) **ItemList を大会ランキングに入れる案は取り下げる**（Google のリッチリザルトの対象にならず、効果が見込めないため。チャット側の見直し） (d) `Person`（選手）は選手個別ページが無いので入れない（#13・#219 に残る） (e) `VideoObject` は入れない（#620・#629 の決定）
- 階層の決め方: navbar のメニュー（`navbar.js` の項目）とディレクトリの階層から、ページの種類ごとに「トップ → 区分 → …」の並びを決め、表にしてログに書く。title/ の見えているパンくずがある種類はそれと同じ並びにする。並びの名前は各ページの h1 か title の先頭の語に合わせる。どちらにも決まらない種類は仮置きして「判断が必要なこと」に挙げる
- 入れ方: 生成のページは生成スクリプト（`scripts/lib/page.py` など共通の部分を優先）で入れ、手書きのページは HTML を直接直す。JSON-LD は `<script type="application/ld+json">` で、今の動画の各話の書き方に合わせる
- N1 の文言（チャット側の案。プレビューで平野さんが見て決める）: 今の `<title>` は「ryoei.pro」、h1 は「Ryoei Hirano」（SEO-01 の②-5）。案は title「平野良栄 | 麻雀プロ（日本プロ麻雀連盟）| ryoei.pro」、h1「平野良栄」。meta description・og:title・og:description は今の文を読み、title と食い違う所だけを直す。見た目が変わるのは h1 の文字だけにし、テンプレート（iPortfolio）の英語のナビ・レイアウトは変えない（#101 の範囲）
- 新サイト送りの記録の直し: docs/handover.md 4章「現行サイトに作り込みすぎない」の「構造化データ（#13）は現行サイトでは見送り、新サイトで対応」を、前倒しした範囲（BreadcrumbList・WebSite）と残り（Person など。#13）に分けて書き直す（追記先の今の内容を読み、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」に従う）。#13 は #296 の sub-issue のまま残し（残りがあるため）、前倒しした範囲をコメントで書く。#296 の本文「新サイト送りにするとき・やめるとき」の手順に当たるかを読んで確かめ、当たるならその手順に従う
- 告知の期間: 最強戦は告知済みとみなしてよい（2026-10-28 の平野さんの決定）。ほかに告知の期間中のページ（docs/notes/page-announcement.md）がある場合は、そのページの生成のファイルの変更を外して進め、外したものを報告に書く
- 生成物の差分は、JSON-LD の追加（と N1 のトップの数行）だけになる見込み。公開中のページは 1,450件（SEO-01 の手順1(a)）。全ページの再生成の扱いは CLAUDE.md「判断・作業の原則」のとおり

## 手順
1. N5: 上の前提の「階層の決め方」で、ページの種類ごとの並びの表を作ってログに書く。生成スクリプト・手書きのページに BreadcrumbList を、トップに WebSite を入れ、テストを足し（JSON として読めること・並びの最後が自分自身の URL であること）、再生成する。公開中のページのうち JSON-LD の入ったページの数を種類ごとに数え、1,450件と突き合わせて、入らなかったページとその理由を全件書く
2. N1: `index.html` の title・h1・meta description・og:title・og:description を、上の前提の案で直す。前後をログに書く
3. 記録: docs/handover.md 4章を上の前提のとおり直す。#13 に前倒しした範囲をコメントし（#296 の手順に当たればそれに従う）、#101 に N1（トップの title・h1 の文言だけを前倒しし、英語のナビは残る）、#219・#159 に N2・N3（SEO-01 の②-10・③-10 を引用）、#362・#640 に N4（③-11 を引用）をコメントする（本文・状態・ラベルは変えない。引用は SEO-01 のログの `## 経過`「指摘の一覧（全件）」の行から）。本番と同じ形でプレビューを用意し（docs/notes/cloud-sessions.md「プレビュー」）、トップ・種類ごとの代表のページ（SEO-01 の手順1(a) の表）で JSON-LD を取り出して JSON として読めることを確かめ、ログに書く。最終報告の「確認用:」にプレビューの URL を書き、判断待ちで止まる

## 止まる条件
- 手順1で、ページの種類の半分以上で並びが決まらない（表を書いて止まる）
- 再生成の差分に、JSON-LD と N1 のトップの数行以外の変化が出た（種類ごとの件数と例を書いて止まる）
- 再生成で、シートの変化による差分が混ざって切り分けられない（書いて止まる）
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる。重なる対象〈生成スクリプト・ページの種類〉だけ外して進め、外したものと理由を報告に書く。全部が重なるときだけ止まる）
- docs/handover.md の追記先が、この指示の前提と矛盾していて、どちらが正か判断が要る

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo4
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo4/docs/logs/CHAT-1029-SEO-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo4
- 確認用URL: 作業中
- マージ: 未
- issue: 作業中
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4aa1e3f2）: https://github.com/retroeater/mj-logs/tree/main/guide/4aa1e3f2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4aa1e3f2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74d7dc08.md
