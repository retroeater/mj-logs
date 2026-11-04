# CHAT-1104-LGF-01

- 着手日時: 2026-11-04
- 対象issue: #378
- ブランチ: work/1104-lgf-survey
- 着手時HEAD: a4a9ed1b

## 指示

【Claude作成】Claude Code 向け指示：log/ の残りの前提を確かめる（#378 の閉じ忘れ・ほかのページとの平仄・旧「ログ」のスプレッドシートの廃止の進み）
Chat-Ref: CHAT-1104-LGF-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-survey の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-lgf-survey を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgf-survey origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、識別子 LGF が使われていないこと（CLAUDE.md「Chat-Ref」節の確かめ）をログに書く。

## 目的
log/ は 2026-11-04 に公開した（#640）。LGN 系列から引き継いだ残りのうち、チャット側が実物で確かめていない3点を確かめ、次の指示（#378 のクローズ、平仄の直し、#665 ジャンル付け）を正しい前提で書けるようにする。調べるだけで、変わるのはログだけ。

### 決定（2026-11-04、平野さん）
- log/ の残りは、まず確認だけの指示から始める

### 前提（チャット側。平野さんの決定ではない）
- LGN 系列は使い終わり、このチャットは LGF 系列で続ける
- #378（現在地から近い店を探す）は、CHAT-1028-LGN-01 の決定で現行サイトで再開し、log/ に「現在地から探す」として入れた（docs/decisions/operations.md の 2026-10-28 の節、docs/notes/log-page.md の冒頭「現在地から探す（#378）を入れる」）。log/ の公開で閉じ忘れている見込み（要確認）
- 新しいナビ: docs/decisions/top.md（2026-11-04）は「白地の新しい形のページ全部」に新しいナビ（帯とメニューのシート）を出すと決めた。一方 docs/notes/design.md の「共通ナビ（新しい形）」の行が挙げる、`navbar.js` が新しい形にするパスの一覧に `/log/` が無い。log-page.md は「白地のナビ: `navbar.js` が `/log/` の下で『リソース』に印」と書く。log/ が新しいナビの対象から漏れている見込み（要確認）
- 共有ボタン: docs/handover.md 4章は、title/・wayhome/・saikyo/・live/ の共有を外し、共通の部品は #530 で消すと書く。log/ に共有ボタンが無いのが今の方針どおりの見込み（要確認）
- 出典: log/ の出典はトップの右上の「(i)」だけ（log-page.md「出典」、`generate_log_pages.py` の `SOURCE_HTML`）。ほかのページの出典・データの出どころの出し方とそろっているかは見ていない（要確認）
- 旧「ログ」があったスプレッドシート: 「ログ」シートは3層のスプレッドシートへ移し、同じスプレッドシートの「対局」「英語」シートは平野さんが削除済み（docs/decisions/operations.md の 2026-10-19 の節）。スプレッドシートごと廃止すると決めたが、コードがまだ読んでいるかは確かめていない（要確認。名前の対応を SNS ブックから引く変更は同じ節に「実装は次の指示」とある）
- スプレッドシートの ID・URL、サービスアカウントの名前は、ログに書かない（`scripts/` の定数名で呼ぶ）

## 手順
1. #378 の本文・コメント・ラベル・状態を読み、`assets/log.js`・`scripts/generate_log_pages.py` で「現在地から探す」が入っていることを確かめる。#378 の本文の要件ごとに、log/ で満たしているか・残っているかを表にする（要件・満たしているか・根拠のファイルと行）。#378 には着手中のコメントだけを書き、本文・状態・ラベルは変えない
2. log/ とほかのページの平仄を確かめ、表にする（項目・log/ の今の形・ほかのページの形〈どのページか〉・ずれの有無・根拠のファイルと行）。項目は (a) 新しいナビ（`navbar.js` の `newNavCurrent` が入る条件に /log/ が入っているか。入っていなければ、白地の新しい形のページ全部に出す決定と食い違うか）(b) 共有ボタンの有無 (c) 出典・データの出どころの出し方（`(i)` を使っているほかのページの有無と形）。本番の log/ を開いて確かめるときは UA に `mj-internal` を含める。あわせて、`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、cloudflare との差に `navbar.js`・`assets/nav.js`・`assets/log.js`・`scripts/generate_log_pages.py` を含むものを、ブランチ名と変えている箇所（関数・節）で一覧にする
3. 旧「ログ」があったスプレッドシートを、リポジトリ全体（`scripts/`・`.github/workflows/`・`workers/`・docs。docs/logs と docs/decisions の過去の行は除く）で今も読んでいるかを確かめる。読んでいれば、定数名・読むファイルと行・読むシート名を一覧にし、廃止の前に何を変えればよいかを書く。読んでいなければ、そう書く。スプレッドシートの中身は読まない（シートの API を呼ばない。調べるのはコードと文書だけで足りるため）

## 止まる条件
- #378 が既に閉じている、またはほかのセッションの着手中コメントがある（そこまでの結果を書いて止まる）
- 手順の途中で、ファイル・ブランチ・issue を変える必要が出た（ログ以外は変えずに止まり、要ることを「判断が必要なこと」に書く）
- 本番の log/ が開けない（一時的な接続の失敗は1回だけやり直してよい。それでも開けなければ、本番の確かめを「未確認の項目」に回し、コードの確かめで続ける）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順1〜3の表は `## 経過` に全件で書く（「略」にしない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGF-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGF-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a9ed1b.md
