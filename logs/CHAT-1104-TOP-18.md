# CHAT-1104-TOP-18

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top3
- 着手時HEAD: b3f9e5121

## 指示

【Claude作成】Claude Code 向け指示：houou_ask.js の片付け（TOP-17）を cloudflare へマージする（#101）
Chat-Ref: CHAT-1104-TOP-18
マージ: 承認済み（チャットで）。条件: (1) origin/cloudflare を取り込んだ後にテスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'`）が通る (2) cloudflare との差分（`git diff --name-only origin/cloudflare...HEAD`）が TOP-17 の変更（TOP-17 のログ `## 経過` の差分の一覧のファイル）とログ・決定だけで、ほかの新しい変更を含まない (3) 取り込んだ後の `regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝その時点の origin/cloudflare）で、中身の違う生成物が無い。1つでも満たさなければマージせずに止まる
貼る時機: いつでも（TOP-17 と同じセッションの続きで可）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top3 への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top3 を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1104-TOP-17 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に ` / 続き: CHAT-1104-TOP-18` を足す。

## 目的

TOP-17（読むページの無い `assets/houou_ask.js` とその JS の検査を消し、注記を直した）を cloudflare へマージする。

### 決定（2026-11-04、平野さん。TOP 系列のチャットで）

- TOP-17 を cloudflare へマージしてよい（マージの可否はほかの問いと分けて聞いた。条件は冒頭の「マージ:」の行）
- TOP-17 の「判断が必要なこと」（答えの文から名前を拾う考え〈Worker の `playersIn()`〉の検査が workers/ask に無い）は、このマージとは分けて決める。この指示では扱わない

### 前提（チャット側。平野さんの決定ではない）

- チャット側が cloudflare との差分を見た: `assets/houou_ask.js` の削除、`assets/top_ask.js`・`assets/league.js`・`workers/ask/src/ask.mjs` の注記、`scripts/tests/test_houou_ask.py`・`test_top_page.py`、docs 3件と `docs/decisions/top.md` だけ
- 取り込み直しの前に、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `assets/houou_ask.js` か `scripts/tests/test_houou_ask.py` を変えていないことを確かめ直す（TOP-17 の後に変えたものがあれば止まる）

## 手順

1. 上の確かめをし、origin/cloudflare を取り込み、マージの条件 (1)〜(3) を確かめる（確かめた一覧をログに書く）
2. 条件を満たせば `git push origin work/1104-top3:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`。偽なら1に戻って取り込み直す。2回目も追いつけなければ止まる）
3. 本番で確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）: `https://ryoei.pro/assets/houou_ask.js?v=<未使用の値>` が 404、`https://ryoei.pro/houou/?v=<…>` で AI 検索のアイコンを押して欄が開き、`assets/top_ask.js` が読み込まれ、ページの JS エラーが無いこと（質問は送らない）。#101 にマージと本番の確かめをコメントし、着手中を解除する

## 止まる条件

- TOP-17 が判断待ちでない、または work/1104-top3 がすでにマージ済み
- 未マージの work/ ブランチが `assets/houou_ask.js` か `scripts/tests/test_houou_ask.py` を変えている
- マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
- 上の扱いで解けない衝突が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8c7dd9a3）: https://github.com/retroeater/mj-logs/tree/main/guide/8c7dd9a3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
