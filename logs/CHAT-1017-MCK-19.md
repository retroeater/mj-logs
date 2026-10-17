# CHAT-1017-MCK-19

- 着手日時: 2026-10-17
- 対象issue: なし（申送り）
- ブランチ: work/1017-mck-handoff
- 着手時HEAD: b3ad7b4c3

## 指示

【Claude作成】Claude Code 向け指示：（申送り）MCK の振り返りの知見3つを、チャット側のガイド文に規則として足す（判断待ちで止まる）
Chat-Ref: CHAT-1017-MCK-19
マージ: 判断待ちで止まる（追記の全文と容量をログに貼って止まる。平野さんがチャットで読んでから、マージの指示を別に出す）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-mck-handoff の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-mck-handoff を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-mck-handoff origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

MCK 系列のチャット（2026-10-10〜17、CHAT-1010-MCK-01〜CHAT-1017-MCK-18）の振り返りと、Fable 5.1 のレビューで出た知見のうち3つを、チャット側のガイド文に規則として足す（申送り。docs/notes/chat-routines.md「申送り」の手順）。

### 決定（2026-10-17、平野さん）

- 申送りは、振り返りのレビュー（Fable 5.1）が勧めた3つで行う

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」のガイド文に規則を足す指示の表）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| (1) マージの承認を、別の問いの答え（「進む」など）から推して「承認済み」と書いた。MCK-16 と MCK-18 の2回 | ガイド文（docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」） | チャット側だけの判断で、Code の検査・ワークフローでは確かめられない（承認はチャットでしか見えない） |
| (2) 平野さんに日付つきの手作業（翌日の請求の確認）を頼んだのに、カレンダーの予定を作らず、言われてから作った | ガイド文（docs/notes/chat-routines.md「期日とカレンダー」） | カレンダーはチャット側のコネクタでしか書けず、Code からは確かめられない。今の規則は「issue に期日を書かせたら」だけで、手作業の依頼を含んでいない |
| (3) 系列の目的（月次運用チェック）から外れた指示（WRC・「ログ」）が十数本続いてから、平野さんに言われて分割した | ガイド文（docs/notes/chat-routines.md「ほかのセッションへの共有」） | どの指示が系列の目的から外れるかはチャット側の判断で、機械的に数えられない |

### 前提（チャット側。平野さんの決定ではない）

- 足す文（チャット側の案。規則と理由の一句だけにする。事例は書かない）:
  - (1) chat-side-operations.md「平野さんの判断とマージの許可」の「指示文を作る前にマージの可否を平野さんに確かめ…」の項に続けて: **マージの可否は、ほかの問いと分けて単独で聞く。別の問いの答え（「進む」など）から承認を推さない**（推すと、承認されていない変更までマージされるため）
  - (2) chat-routines.md「期日とカレンダー」に: **平野さんに日付つきの手作業を頼むときは、その返信の中でカレンダーに予定を作る**（頼んだ後に作ると抜けるため）
  - (3) chat-routines.md「ほかのセッションへの共有」に: **系列の目的から外れた指示が3本続いたら、チャットの分割（引継ぎ）を平野さんに提案する**（1つのチャットに別の話題が積もると、引継ぎが重くなるため。本数の目安は平野さんが変えてよい）
- 同じ趣旨の規則がすでにあれば、足さずに既存の項を直す案にする（重複させない）。どちらにしたかと理由をログに書く
- chat-side-operations.md は容量の上限がある（上限と警告域は CLAUDE.md・文書の冒頭を見る）。chat-routines.md は上限なし

## 手順

1. docs/notes/chat-side-operations.md・docs/notes/chat-routines.md の追記先の節の今の内容を読み、同じ趣旨の規則が無いかを確かめる。chat-side-operations.md の今の大きさと上限・警告域との差を測ってログに書く
2. 前提の3つの文を足す（または既存の項を直す）。追記後の大きさを測り、上限を超えるなら、足さずにその旨を書いて止まる
3. 変えた節の追記後の全文（変えた項の前後を含む）と、`git diff` をログに貼り、`## 報告` の状態を「判断待ち」にして止まる（cloudflare へはマージしない）

## 止まる条件

- 追記で chat-side-operations.md が上限を超える
- 追記先の節が見つからない、または同じ趣旨の規則と食い違う規則がある（どの項かを書いて止まる）
- push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）
- マージはしない（冒頭の「マージ:」の行のとおり）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-MCK-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-MCK-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b3ad7b4c）: https://github.com/retroeater/mj-logs/tree/main/guide/b3ad7b4c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b3ad7b4c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
