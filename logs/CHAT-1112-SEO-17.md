# CHAT-1112-SEO-17

- 着手日時: 2026-11-12
- 対象issue: #641
- ブランチ: work/1112-seo17
- 着手時HEAD: 6eb2da9

## 指示

【Claude作成】Claude Code 向け指示：女流プロ麻雀日本シリーズの期ページが Bing で上位なのにクリックされない件を、ページの中身・データ・Google の数から調べて止まる（読むだけ）。あわせて #641 に1行コメントする
Chat-Ref: CHAT-1112-SEO-17
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1112-seo17〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1112-seo17 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1112-seo17 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1112-seo17 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Bing Webmaster Tools（2026-11-12 に平野さんの画面で読んだ、データは 2026-11-08 まで・期間3か月）で、`title/joryu-nihon-series/2022.html` は平均順位 4.53・表示 224 なのにクリックが 0 だった。直す前に、期ページに検索の意図（「結果」「プレーオフ」「決勝」）に合う中身があるか、手元のデータで足せるかを確かめ、直し方の案を出して止まる。直すのは平野さんが案を見て決めた後の別の指示で行う。

### 決定（2026-11-12、平野さん）
- Bing の確認で出た2件を SEO 系列で扱う（2026-11-12 の Bing レポート確認のチャットからの伝言を、平野さんが承認した）: (1) 女流プロ麻雀日本シリーズの期ページがクリックされない件 (2) #641（pro/ の公開）の公開前の点検に「pro/ のトップの title・description・h1 に『一覧』の語が入っているか」を足す

### 前提（チャット側。平野さんの決定ではない）
- Bing の数（伝言から。Code に確かめさせる値ではない）:

  | ページ | 表示 | クリック | 平均順位 |
  |---|---|---|---|
  | title/joryu-nihon-series/2022.html | 224 | 0 | 4.53 |
  | title/joryu-nihon-series/2024.html | 34 | 0 | 4.88 |
  | title/joryu-nihon-series/2025.html | 70 | 1 | 4.89 |
  | title/joryu-nihon-series/2026.html | 563 | 19 | 3.01 |

  着地した検索語（すべてクリック0）: 「女流 プロ 麻雀 日本 シリーズ 2022 結果」103・「…2022 プレーオフ」72・「…2022 決勝結果」37・「…2024 決勝」27。2022 のページは Copilot の引用も 34
- チャット側が origin/cloudflare の生成物で見たところ、2022 の期ページの title はすでに「女流プロ麻雀日本シリーズ2022 決勝結果 | 日本プロ麻雀連盟 | ryoei.pro」、description は「女流プロ麻雀日本シリーズ2022の決勝結果。優勝は岡田紗佳。以下、魚谷侑未、逢川恵夢、日向藍子。」（要確認）。**「決勝結果」はもう入っているので、文言の直しだけでは足りない見込み。** 「プレーオフ」「（リーグ戦の）結果」を探す人に対して、ページに決勝の4名しか無いことが原因の候補（チャット側の推測）
- 同じ型（文言は合っていて上位なのにクリックが無い）の直しの例は #671（「鳳凰位 歴代」、CHAT-1106-MFQ-06）
- title/ のレイアウトの作り直し（TTL）は CHAT-1105-TTL-13 でマージ済み。生成は `scripts/generate_title_pages.py`
- Search Console の数は `docs/gsc/2026-11-01/`（期間 10/2〜10/29）。Google での同じページ・同じ型の検索語の表示・クリック・順位を補助として見る
- 本番を取りに行くときは逐次で、1分30回未満にする（Rate limiting rule #124、CHAT-1101-SEO-16 の記録）。生成物で足りるなら本番は取らない
- #641 は PRP 系列の issue。この指示ではコメントを1つ書くだけで、本文・状態・ラベルは変えない
- 新しい issue は、この指示の結果を見て平野さんが「直す」と決めてから起票する（この指示では起票しない）

## 手順
1. 女流プロ麻雀日本シリーズの期ページ（全期）について、生成物から title・description・h1・本文に載っている情報（決勝の対局者・順位・点数・対局日・プレーオフやリーグ戦の情報・動画・関連リンク）を表にしてログに書く。ほかの大会の期ページ（鳳凰戦・十段戦・最強戦など数大会）と、載っている情報の種類を並べて比べる
2. その期ページを作るデータ（シートの列。`generate_title_pages.py` が読む列）に、プレーオフ・リーグ戦・予選の結果が入っているか、入っていないなら手元のリポジトリのほかのデータ（シート・JSON）にあるかを確かめてログに書く（シートの鍵が要る確かめは、生成物とコードから分かる範囲でよい。分からない点は「未確認」と書く）
3. `docs/gsc/2026-11-01/` の page.csv と検索語の表から、女流プロ麻雀日本シリーズの期ページ・入口（`title/joryu-nihon-series/`）の Google での表示・クリック・順位と、着地した検索語を全件ログに書く（無ければ「無し」）
4. 1〜3 から、直し方の案を2〜3個、メリット・デメリット付きで書く（例: 期ページにプレーオフ・リーグ戦の結果を足す〈データがあれば〉／title・description に「プレーオフ」の語を入れない〈中身が無いので〉で今のまま／入口や公式の結果ページへのリンクを足す、など）。どの案も、ほかの大会の期ページに同じ型で広がるかを書く
5. #641 に次の1行をコメントする（本文・状態・ラベルは変えない）: 「公開前の点検に追加: pro/ のトップの title・description・h1 に『一覧』の語が入っているかを見る。Bing（2026-11-08 までの3か月）で `jpml_pros.html` は Copilot の引用が 99（31ページ中2位）、そのうち検索語『麻雀 プロ 一覧』の引用が 72。公開と同時の 301（CHAT-1101-PRP-02）で引用の受け皿が pro/ に移るため（CHAT-1112-SEO-17）」。あわせて、今の work/1101-prp-pub か origin/cloudflare の `pro/index.html` の title・description・h1 に「一覧」が入っているかを読んでログに書く（直さない）
6. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、ログを仕上げて `git push origin work/1112-seo17:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告` の「判断が必要なこと」に、手順4の案を平野さんが選べる形で書く

## 止まる条件
- #641 に、他のセッションの完了していない着手中のコメントがあり、コメントを足すと作業と重なる（コメントを書かずに進め、報告に書く）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1112-SEO-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1112-SEO-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1112-seo17
- ログ: https://github.com/retroeater/mj/blob/work/1112-seo17/docs/logs/CHAT-1112-SEO-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1112-seo17
- 確認用URL: なし
- マージ: 未
- issue: #641
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6eb2da98）: https://github.com/retroeater/mj-logs/tree/main/guide/6eb2da98

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6eb2da98/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6eb2da98.md
