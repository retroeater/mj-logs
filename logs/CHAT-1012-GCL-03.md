# CHAT-1012-GCL-03

- 着手日時: 2026-10-12
- 対象issue: #492・#297（確認後に決める）
- ブランチ: work/1012-gcl
- 着手時HEAD: c5daf1e8c

## 指示

【Claude作成】Claude Code 向け指示：chat-side-operations.md が警告域の直前（残り1バイト）であることを issue に書き残す
Chat-Ref: CHAT-1012-GCL-03
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1012-gcl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-gcl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1012-GCL-01 で docs/notes/chat-side-operations.md を 26,623 バイトに収めた（チャット側が mj-logs の写しで実測。警告域は 26,624 バイト、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。次に規則を1行でも足すと assets-check.yml の警告で止まる。カレンダー移行（GCL）の振り返りの申送りとして、この事実と「足す前に整理（docs/notes/chat-routines.md「レビュー」）を行う」を issue に残す。文書には足さない（容量が無い）。

### 決定（2026-10-12、平野さん）
- 振り返りの申送りは、必要なものだけ issue に書き残す（文書は変えない）

### 前提（チャット側。平野さんの決定ではない）
- 書き先は、ガイド文書のサイズを扱う既存の open issue が第一候補。docs/handover.md の表には #492「ガイド文書の上限の見直し」（11月中旬）があり、カレンダーには【R#297】「ガイド文書のサイズ上限の見直し」（11/16）がある。どちらが open で、どちらがこの件に合うかは要確認（チャット側は mj の issue を読めない）
- コメントの案（規則と理由の一句だけ）: 「docs/notes/chat-side-operations.md は 2026-10-12（CHAT-1012-GCL-01）時点で 26,623 バイト（警告域 26,624 まで残り1バイト）。規則を足す指示を作る前に、docs/notes/chat-routines.md「レビュー」の手順で整理する（整理の指示は判断待ちで止め、整理後の全文をログに貼る）。整理の目安は警告域から 2KB 以上下」

## 手順
1. open の issue を検索し（語: ガイド文書、サイズ、上限、chat-side-operations。クローズ済みも含めて確認）、#492・#297 の状態と本文を読む。合う open issue が1つあればそこへコメント（末尾に Chat-Ref: の行）。2つとも open で重なるなら止まって報告する。合う open issue が無ければ、題「chat-side-operations.md の整理（警告域の直前）」で新しく起票し、上の案を本文にする（期日は付けない。平野さんが決める）。
2. 作業する worktree の上で `wc -c docs/notes/chat-side-operations.md` を実測し、26,623 と違えば実測値でコメント・本文を書く（違っても止まらない）。
3. 完了時に docs/decisions/operations.md に決定を足す（CLAUDE.md「作業ログ」節）。

## 止まる条件
- #492 と #297 が両方 open で、どちらもこの件を含む（どちらに書くか平野さんの判断）
- 対象 issue に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。書いた issue の番号（新規なら番号と URL）を報告に書く
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs/ と docs/decisions/ だけ）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-GCL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-GCL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- CHAT-1012-GCL-03 のコミット: 無し。同じセッションの3件目なので識別子の重複確認は対象外
- 作業ブランチ: ローカルの work/1012-gcl は origin/cloudflare の祖先（GCL-02 でマージ済み）→ `git merge --ff-only origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 指示欄の末尾が指示文の最後の行と一致することを確認

## 報告

- 状態: 着手中
- ブランチ: work/1012-gcl
- ログ: https://github.com/retroeater/mj/blob/work/1012-gcl/docs/logs/CHAT-1012-GCL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-gcl
- 確認用URL: なし
- マージ: 未
- issue: 確認中
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c5daf1e8）: https://github.com/retroeater/mj-logs/tree/main/guide/c5daf1e8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c5daf1e8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c5daf1e8.md
