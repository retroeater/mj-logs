# CHAT-1017-WFL-26

- 着手日時: 2026-10-17
- 対象issue: #565
- ブランチ: work/1017-wfl-handoff
- 着手時HEAD: d3a5c45c0

## 指示

【Claude作成】Claude Code 向け指示：申送り（WFL の振り返り）— chat-routines.md「指示の種類ごとの確かめ」に 2 項目、#565 にコメント
Chat-Ref: CHAT-1017-WFL-26
マージ: ドキュメントのみ（docs/notes・ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1017-wfl-handoff を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-wfl-handoff origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。ログは docs/notes/glossary.md の語で書く。

## 目的
WFL（Actions の整理、CHAT-1014-WFL-01〜CHAT-1017-WFL-25）の振り返りの知見のうち、手順に落とせるものを残す。

### 決定（2026-10-17、平野さん）
- 振り返り（チャット側の報告と Fable 5.1 のレビュー）の後、「申送り」として知見を残す

### 知見 → 選んだ層 → 理由
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| チャット側が、ガイド文書の規則を読まずに指示を書き、Claude Code が着手前に止まった（WFL-12: 未マージのブランチの削除は平野さんの手作業という CLAUDE.md の規則、WFL-21: ガイド文に規則を足す指示の「知見 → 選んだ層 → 理由」の表〈#492〉）。WFL-12 の後の反省を「削除」に限ったため、別の規則で同じ型を繰り返した | 推論的センサー（docs/notes/chat-routines.md「指示の種類ごとの確かめ」に、チャット側が書く前に確かめる手順として 1 項目） | 受け手側（Claude Code）の検査はすでにあり（止まる条件）、2 回とも働いた。損は往復 1 回で、それを減らすのはチャット側が書く前に読むこと。テスト・hook はチャット側の指示文を検査できない。場面を「削除」に限らず「ガイド文・運用・ブランチに触れる指示」に広げる |
| 外のサイト・API に触れるワークフローの手動実行を止まる条件に入れたため、道場部のページの取得の一時的なタイムアウトで止まり、往復が 1 回増えた（WFL-15） | 推論的センサー（同じ節に 1 項目） | 一時的な失敗かどうかは、受け手が再実行の結果で確かめられる。止まる条件の書き方の問題なので、指示を書く側の手順に置く。テストでは判定できない |

### 前提（チャット側。平野さんの決定ではない）
- 足す項目（文面は実物の節に合わせて整えてよい。規則と理由の一句だけ。事例は書かない）:
  1. 「ガイド文（CLAUDE.md・handover.md・docs/notes/ のチャット側の文書・用語集）・運用ルール・ブランチ（削除・入れ直し）に触れる指示: 書く前に chat-side-operations.md『場面ごとに読む文書』の表と、CLAUDE.md の該当節（『ブランチを削除するとき』『Chat-Ref』のガイド文の規則など）を読む（受け手の止まる条件に当たると往復が増えるため）」
  2. 「外のサイト・API に触れるワークフローの手動実行を確かめに入れる指示: 止まる条件に『一時的な接続の失敗は、失敗したジョブだけを 1 回再実行して success なら止まらない』を添える（一時的な失敗で止まると往復が増えるため）」
- docs/notes/chat-routines.md は上限のある文書ではない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の上限の対象は 4 文書）。要確認。大きさを前と後で測ってログに書く
- 同じ趣旨の項目が既にあれば、足さずに統合・置き換える（追記型にしない）
- #565（文書参照の検査テスト）: 段③-3（CHAT-1016-WFL-20）で改名と参照の洗い出しが済み、着手できる時機になった、とコメントする（本文は変えない。着手はしない）
- CLAUDE.md・chat-side-operations.md・handover.md は変えない

## 手順
1. docs/notes/chat-routines.md「指示の種類ごとの確かめ」に 2 項目を足す（統合できるものは統合）。前と後の大きさを測る
2. #565 にコメントする
3. 上の「決定」を `docs/decisions/` の合う文書（WFL-21 の決定と同じ文書）に `## 2026-10-17（CHAT-1017-WFL-26）` として足す。完了報告のうえ cloudflare へマージする（push 直前に再 fetch と祖先の確認）

## 止まる条件
- chat-routines.md が上限のある文書だと分かった（足さずに判断待ち）
- 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（用語集の語で書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WFL-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WFL-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9857cf32）: https://github.com/retroeater/mj-logs/tree/main/guide/9857cf32

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9857cf32/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2922a722.md
