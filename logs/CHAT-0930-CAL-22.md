# CHAT-0930-CAL-22

- 着手日時: 2026-10-01 14:34（JST）
- 対象issue: なし
- ブランチ: work/1001-cal-title
- 着手時HEAD: 8efeb156（origin/cloudflare。ローカルの work/1001-cal-title〈97d8926a、cloudflare の祖先〉を `git merge --ff-only origin/cloudflare` で進めた）

## 指示

【Claude作成】Claude Code 向け指示：麻雀格闘倶楽部プロNo1決定戦の予定表の件名に毎年「（1/2）」などが付いているかと、「[SANMA]」の予定の YouTube の原題を調べて報告する（読むだけ）
Chat-Ref: CHAT-0930-CAL-22
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1001-cal-title を使う（CAL-21 まで使いマージ済み）。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1001-cal-title origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
マージ: 承認済み（チャットで、2026-10-01。この作業のログと決定の記録〈docs/logs・docs/decisions のみ〉を cloudflare へ）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CAL-21 の報告で残った件名の印のうち2つについて、平野さんが外すかどうかを決めるための材料をそろえる。コード・シート・カレンダーは変えない。

### 決定（2026-10-01、平野さん）
- 「(仮)」は件名に残す（正式な大会名が決まれば予定表の側で更新されるため）。
- 「（1/2）」「（2/2）」は、麻雀格闘倶楽部プロNo1決定戦の予定表の件名に毎年付いているかを見てから決める。
- 「[SANMA]」は、YouTube の原題を見てから決める。

## 手順
1. No.1決定戦: 予定表の【1】（全期間、2024-12-01〜）と【3】から、件名に「麻雀格闘倶楽部」と「No1」（「No.1」「Ｎｏ１」なども）を含む予定をすべて挙げる（日付・件名そのまま・予定ID の先頭8文字）。回ごと（第8回・第9回など）に、「（1/2）」「（2/2）」などの日数の印が付いているかを書く。あわせて、YouTube の層1から同じ大会のライブ配信の枠を探し、回ごとの日付と原題と、カレンダーに載っている件名を挙げる（予定表由来の仮の予定が、枠の予定にどう置き換わったか・置き換わるかが分かるように）。
2. [SANMA]: カレンダーの `video:jXnwXtnX6sY` の予定について、層1の原題（YouTube の題名そのまま）と、今のカレンダーの件名を並べて書く。同じシリーズ（WORLD RIICHI Online Team League）のほかの枠（`video:wyPTXmvKlOU` など）の原題と件名も並べ、三人麻雀の部と四人麻雀の部が件名で区別できているかを書く。
3. 決定の記録: 上の「決定」を CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す。

## 止まる条件
- 手順3まで終えたら、判断待ちで止まる。

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、ログと決定の記録を cloudflare へ入れる。「判断が必要なこと」に、手順1・2から見た「（1/2）（2/2）」と「[SANMA]」の扱いの案（残す・外す・言い換える）を書く。
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-22.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-22` は0件
  - origin/work/1001-cal-title は origin/cloudflare の祖先（マージ済み）
  - ローカルの work/1001-cal-title も祖先なので、`git merge --ff-only origin/cloudflare` で進めた（8efeb156）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1001-cal-title
- ログ: https://github.com/retroeater/mj/blob/work/1001-cal-title/docs/logs/CHAT-0930-CAL-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a9af0a1d）: https://github.com/retroeater/mj-logs/tree/main/guide/a9af0a1d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a9af0a1d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
