# CHAT-1025-VOD-04

- 着手日時: 2026-10-25
- 対象issue: #629, #362, #620
- ブランチ: work/1024-vod
- 着手時HEAD: eee4b86f6

## 指示

【Claude作成】Claude Code 向け指示：work/1024-vod（動画の各話ページから VideoObject を外す、#629）を cloudflare にマージし、本番で確かめる
Chat-Ref: CHAT-1025-VOD-04
マージ: 承認済み（チャットで。この指示文を貼ることが承認）
貼る時機: いつでも（CHAT-1024-VOD-03 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-vod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1024-vod を続けて使う（CHAT-1024-VOD-03 の判断待ちの変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1024-VOD-03 で止めた変更（4つの番組の各話 155 ページから VideoObject を外し、build_video_object() を消す）を本番に入れる。新しい変更は加えない。

### 決定（2026-10-25、平野さん）
- work/1024-vod を cloudflare にマージしてよい（CHAT-1024-VOD-03 の報告を読んだうえでの承認）

### 前提（チャット側。平野さんの決定ではない）
- 変わるのは各話 155 件の各1行（VideoObject の削除）・生成スクリプトとライブラリ・テスト・docs/notes と docs/decisions・sitemap 3つの lastmod（CHAT-1024-VOD-03 のログの経過）。一覧4ページは変わらない
- マージ後の自動再生成（regenerate-page.yml）では、この4番組に加え、シート側の変更の反映で無関係なページが変わることがある。無関係なページの変更は止めずにログに一覧で書く
- work/1017-lvp（#362、判断待ち）は、この変更の後に `scripts/lib/study.py`・`scripts/tests/test_study.py` でも衝突する。解き方は「study.py は札の行を残して `build_video_object()` を消す側、test_study.py は札の assert を残して `isAccessibleForFree` の行を消す側」（CHAT-1024-VOD-03 のログの経過）。work/1017-lvp には触らず、#362 にこの解き方をコメントするだけにする
- #620 は閉じない（Search Console の検証の結果を待つ。チャット側がカレンダーで追う）

## 手順
1. CHAT-1024-VOD-03 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1025-VOD-04` を足す。origin/cloudflare を取り込み（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外は解かずに止まる）、`python3 -m unittest discover -s scripts/tests` を通してから cloudflare にマージする
2. 本番（キャッシュよけの値付き）で、各話4件（mtsuku/ZeFtFG2ov_Q・exam/-PoeE0Bm2sE・wayhome/Yjp_PKHvldw・study/1QVc_wj9T-c）と一覧4ページの JSON-LD を @type ごとに数えて表に書く（見込み: 各話は BreadcrumbList 1・VideoObject 0、一覧は ItemList 1・VideoObject 0）。マージ後の自動再生成の結果（変わったファイル）もログに書く
3. issue: #629 に本番に入ったこと（マージのコミット・表）をコメントして閉じる。#362 に上の前提の work/1017-lvp の衝突と解き方を1つのコメントで書く（状態は変えない）。#620 に「各話も本番に入った（#629）。検証の結果を待って閉じる」を1行コメントする（閉じない）。作業ブランチの片付けは docs/notes/branch-operations.md のとおり

## 止まる条件
- 取り込み後のテストが失敗する（今回の変更による失敗なら止まる。無関係な失敗なら原因をログに書いて進めてよい）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- 本番の各話4件のどれかに VideoObject が残っている、または一覧4ページのどれかで ItemList が無い（デプロイの遅れは待機の上限まで待ってから判定する）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1025-VOD-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1025-VOD-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1814645e）: https://github.com/retroeater/mj-logs/tree/main/guide/1814645e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1814645e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/070c0aa9.md
