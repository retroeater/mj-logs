# CHAT-1031-PRO-13

- 着手日時: 2026-10-31
- 対象issue: #390・#426
- ブランチ: work/1031-pro-dojo
- 着手時HEAD: f797a06

## 指示

【Claude作成】Claude Code 向け指示：道場部ゲストの同期で「Coming soon」など未定の日だけを飛ばして残りを書けるようにする（プレビューで止める）
Chat-Ref: CHAT-1031-PRO-13
マージ: 判断待ちで止まる
貼る時機: いつでも（ほかの PRO の指示と別のセッションで並行してよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-dojo を使う（同じ系列のほかの作業ブランチと分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-dojo origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する

## 目的
道場部ゲストの同期（#390、通知は #426）は、読み取った名前が1件でも登録名と一致しないと、その月を書き込まない。2026年11月分は画像の3日が「Coming soon」（ゲスト未定）だったため、残りの18件もカレンダーに入っていない（#426 の 2026-10-28 の通知、CHAT-1031-PRO-08 の調べ）。未定の日だけを飛ばして残りを書けるようにする。読み違い（「福島佑一」を「福島祐一」と読んだ実例）を防ぐ今の守りは残す。

### 決定（2026-10-31、平野さん）
- 同期の規則を変える: 「Coming soon」など未定の日だけを飛ばして、残りの日を書く。読み違いで登録名と一致しない名前があるときは、今どおり書かずに止める

### 前提（チャット側。平野さんの決定ではない。要確認）
- 仕組みと今の規則は docs/notes/dojo-guest-calendar.md と docs/decisions/dojo-guest.md。読み取りは `scripts/lib/dojo_guest.py`（Claude API の画像入力）、突き合わせと書き込みは `scripts/sync_dojo_calendar.py`
- 「未定の日」の見分け方の案: 読み取りの結果の名前が、決まった語の一覧（「Coming soon」とその表記の揺れ〈大文字小文字・空白・全角〉、「未定」「TBA」など、実際の画像に出た語だけ）に当たる日。一覧は定数にし、当たらない名前は今どおり「照合できない名前」として止める（推測で未定とみなさない）。読み取りの指示（プロンプト）で未定の日を区別させる方法もあるが、Claude API の呼び出しの中身が変わるので、この指示では突き合わせ側だけで見分ける案
- 飛ばした日の扱い: カレンダーには何も書かない。#426 の通知に「未定で飛ばした日」として日付と読み取った文字を書く。後の月で画像が差し替わって名前が入ったら、今の「書き込み済みの月の画像が差し替わったとき」の自動の更新（当日以降を画像に合わせる。変更が3件以上なら保留）で入る見込み（要確認。未定の日を飛ばした月が「書き込み済みの月」として扱われるかを確かめる）
- 費用: この指示では Claude API を呼ばない（読み取りは前回の状態〈Actions のキャッシュの `dojo-state.json`〉に保存した読み取り結果を使う。保存した結果が無ければ呼ばずに止まる）
- 確かめ: テスト（未定の語の一覧・揺れ・未定と照合できない名前が混じる月・全部が未定の月）と、作業ブランチで `sync-dojo-calendar.yml` を書き込み無しで手動実行し、11月分で「追加 18件・未定で飛ばした日 3件」と出ることを確かめる（数は #426 の通知と PRO-08 の一覧で照合する。違えば止まる）。**カレンダーへの書き込み（「カレンダーに書き込む」を選んだ実行）はしない**（書き込みは、マージの後に平野さんの判断で行う）
- 通知の変化が #426 に出るのは書き込み無しの実行でも同じか（今の通知の条件）を確かめ、試しの実行で #426 に余計なコメントが付くなら、付いたコメントのリンクを報告に書く
- docs/notes/dojo-guest-calendar.md の規則の行を書き換え（追記型にしない）、決定は完了時に docs/decisions/dojo-guest.md に足す
- コード・ワークフローの扱いは CLAUDE.md「判断・作業の原則」のとおり（新しい文脈のサブエージェントのレビュー）

## 手順
1. 今の規則と11月分の保存した読み取り結果（未定の3日の文字）を確かめ、見分け方をログに書く
2. 突き合わせの規則・通知・テスト・docs を直し、作業ブランチで書き込み無しの手動実行をして結果を確かめる
3. レビューを受け、判断待ちで止まる（マージの後に11月分を書き込む手動実行の手順を、報告の「判断が必要なこと」に書く）

## 止まる条件
- 前提が実物と食い違い、決定が守れない（どれが・なぜを書いて止まる）
- 保存した読み取り結果が無く、確かめに Claude API の呼び出しが要る（呼ばずに止まる）
- 書き込み無しの実行の結果が「追加 18件・未定 3件」と違う（違いを書いて止まる）
- 未マージの work/ ブランチが `scripts/sync_dojo_calendar.py`・`scripts/lib/dojo_guest.py`・`.github/workflows/sync-dojo-calendar.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する
- 他セッションの着手中コメントが #390・#426 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9fa674b4.md
