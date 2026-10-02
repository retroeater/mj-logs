# CHAT-1002-DOJ-05

- 着手日時: 2026-10-02
- 対象issue: #426（関連 #390）
- ブランチ: work/1002-doj
- 着手時HEAD: 29b3e66b

## 指示

【Claude作成】Claude Code 向け指示：#426「道場部ゲストの取り込み」の本文を、今の同期の動きに合わせて直す Chat-Ref: CHAT-1002-DOJ-05 マージ: 承認済み（チャットで、2026-10-02）。条件: 変更が docs/（docs/decisions/ を含む）だけのとき。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-doj の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-doj を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-doj origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-DOJ-04 のログの `## 報告` を読み、完了していなければ止まる。

目的
#426（道場部ゲストの同期の通知先。平野さんが毎月見る）の本文が、今の動きと食い違っている。CHAT-1002-DOJ-04 の報告によると、本文に「カレンダーへの書き込みは自動では行わない」とあるが、CHAT-1002-DOJ-03 の後は、書き込み済みの月の画像が差し替わると定期実行が当日以降を自動で直す。本文を今の動きに合わせる。コードは変えない。
決定（2026-10-02、平野さん）

* #426 の本文を、今の動きに合わせて直す。

前提（チャット側。平野さんの決定ではない）

* チャット側は #426 の本文を見ていない。食い違いは CHAT-1002-DOJ-04 の報告の「判断が必要なこと」で知った。
* 今の動きの正は docs/notes/dojo-guest-calendar.md（「仕組み」「毎月の運用」）と docs/decisions/dojo-guest.md。本文はこれと食い違う箇所だけを直し、仕組みの説明を本文に写して長くしない（詳しくは文書を指す）。
* 平野さんが通知を見てすることが本文から分かるようにする: 新しい月の通知は確かめてから「カレンダーに書き込む」を付けて手動実行する／「自動で更新しました」は確かめるだけ／「自動では更新していません」は確かめてから手動実行する／「見出しが見つかりません」は画像の URL と月を指定して手動実行する。
* docs/notes/dojo-guest-calendar.md に、すでに過ぎた見込みの記述が残っている（例: 「#426 への通知の見え方は、10月分の画像が出たときが初めてになる」）。ほかにもあるかは確かめていない。

手順

1. #426 の今の本文を読み、全文をログに写す。docs/notes/dojo-guest-calendar.md・docs/decisions/dojo-guest.md と突き合わせ、食い違う箇所を一覧にする。
2. 本文を直す。直した後の全文をログに写す。コメントは足さない（本文の修正だけ。通知のコメントに紛れないように）。
3. docs/notes/dojo-guest-calendar.md に、今の動きと食い違う記述や、すでに過ぎた見込みの記述が残っていれば、今の内容を読んでから直す（文書の直しは writing-for-agents の skill を使う）。無ければ「無い」と書く。変更があれば CLAUDE.md の検証を通し、マージの行のとおり cloudflare へ入れる。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。

止まる条件

* CHAT-1002-DOJ-04 のログの `## 報告` が完了でない。
* #426 に他セッションの着手中コメントがある。
* 本文の食い違いが、文書（docs/notes/dojo-guest-calendar.md）の側の誤りに見える。直さずに、どちらが実物と合っているかを書いて止まる。
* コードやワークフローなど docs/ 以外の変更が要ることになった。
* issue の本文の編集や、ブランチの作成・cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、本文のどこをどう直したか（要点）、文書の直しの有無を入れる。
* マージは冒頭の「マージ:」の行のとおり。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-DOJ-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-DOJ-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `CHAT-1002-DOJ-05` のコミット・ログは無し
- ブランチ: ローカルの `work/1002-doj`（2a4f4fc1）は `origin/cloudflare` の祖先、`origin/work/1002-doj` もマージ済み。`git merge --ff-only origin/cloudflare` で 29b3e66b へ（別セッションの CLD-02 のログの更新だけ）
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1002-DOJ-04 の `## 報告` は「状態: 完了」

## 報告

- 状態: 作業中
- ブランチ: work/1002-doj
- ログ: https://github.com/retroeater/mj/blob/work/1002-doj/docs/logs/CHAT-1002-DOJ-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-doj
- 確認用URL: なし
- マージ: 未
- issue: #426
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 29b3e66b）: https://github.com/retroeater/mj-logs/tree/main/guide/29b3e66b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/29b3e66b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b308711e.md
