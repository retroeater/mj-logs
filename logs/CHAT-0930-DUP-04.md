# CHAT-0930-DUP-04

- 着手日時: 2026-09-30
- 対象issue: #232
- ブランチ: work/0930-dup-04
- 着手時HEAD: 2e1b8f0d

## 指示

【Claude作成】Claude Code 向け指示：#232（title/ の OGP 画像の出し分け）を /grill-me で詰め、決定を記録する Chat-Ref: CHAT-0930-DUP-04 マージ: 承認済み（チャットで、2026-09-30）。条件: 変更が docs/ だけのとき（docs/decisions/・docs/logs/・docs/handover.md を含む）。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/0930-dup-04 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-dup-04 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-dup-04 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#232 が open で、ほかのセッションの着手中コメントが無いことを確かめ、着手中のコメントを残す。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、#232・OGP・title/ に触れているものを書く（CHAT-0930-DUP-02・DUP-03 は並行して実行中。触らない）。

目的
#232（title/ の期ページ・大会ページの OGP 画像の出し分け）の実装に入る前に、決めることを /grill-me で平野さんと詰め、決定を記録する。この指示では実装しない。
決定（2026-09-30、平野さん）

* #232 の /grill-me を今始める（handover の「#269・#473 の後」を待たない）。
* grill の結果（docs だけの変更）は、終わったら cloudflare へマージしてよい。

前提（チャット側。平野さんの決定ではない）

* 論点の候補は、CHAT-0930-OLT-09 のログの9つ（範囲・画像の中身・意匠・生成の仕組み・名前のつけ方・og:title・進め方・スコープ・#165 との分け方）と、CHAT-0930-OLT-11 のログの「#232 を /grill-me で詰める論点の候補」の10〜14。両方のログを読み、食い違いや重複はまとめてよい。
* 現行サイトに作り込みすぎない方針（handover 4章）と、既存の知見（docs/notes/ogp.md・docs/notes/title-pages.md、最強戦の `og_image_for()`、帰り道の自動生成 #340、`build_ogp_image.py`）を踏まえて問う。

手順

1. 読む: 上の2つのログ、#232 の本文・コメント、docs/notes/ogp.md・docs/notes/title-pages.md・docs/decisions/title.md を読み、論点の一覧（重複をまとめた順番つき）をログに書く。
2. grill: /grill-me を使い、論点を1つずつ平野さんに問う（答えやすいように選択肢と、チャット側・Code 側の推奨があれば添える）。平野さんが「後で決める」とした論点は未決として残す。
3. 記録: 決定を docs/decisions/title.md に「（grill Qn）」を添えて追記し（先に今の内容を読む）、#232 に決定と未決の一覧をコメントし、docs/handover.md の #232 の行を今の状態に直す。報告に、次の実装の指示の分け方の案（試作 → 見本 → 本実装 など）を書く。条件を満たせば cloudflare へマージする（その時点の origin/cloudflare を取り込み、docs/decisions/title.md が DUP-02 などの追記と重なったら両方を残す）。

止まる条件

* #232 が閉じている、またはほかのセッションの着手中コメントがある。
* 論点のログ（OLT-09・OLT-11）が読めない。
* grill の中で、docs/ 以外の変更（試作・スクリプト）が要ることになった（決定だけ記録し、実装は別の指示にする）。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* マージは冒頭の「マージ:」の行のとおり。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-DUP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-DUP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-09-30 Chat-Ref の重複確認（`git fetch --unshallow` 後に `git log --all --grep`・`docs/logs/` の履歴）: DUP-04 のコミットなし。
  `origin/work/0930-dup-04` は無いため `git checkout -b work/0930-dup-04 origin/cloudflare` で作成。

## 報告

- 状態: 作業中
- ブランチ: work/0930-dup-04
- ログ: https://github.com/retroeater/mj/blob/work/0930-dup-04/docs/logs/CHAT-0930-DUP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-dup-04
- 確認用URL: なし
- マージ: 未
- issue: #232
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 78e67ff7）: https://github.com/retroeater/mj-logs/tree/main/guide/78e67ff7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/78e67ff7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b7f138e5.md
