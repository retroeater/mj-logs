# CHAT-0930-CAL-23

- 着手日時: 2026-10-03 09:09（JST）
- 対象issue: なし
- ブランチ: work/1001-cal-title
- 着手時HEAD: a8fcf150（origin/cloudflare。ローカルの work/1001-cal-title〈cloudflare の祖先〉を `git merge --ff-only origin/cloudflare` で進めた）

## 指示

【Claude作成】Claude Code 向け指示：WORLD RIICHI の英語の配信2件が毎朝の実行でカレンダーから消えたことを確かめ、件名の印についての決定を記録する（読むだけ＋記録） Chat-Ref: CHAT-0930-CAL-23 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1001-cal-title を使う（CAL-22 まで使いマージ済み）。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1001-cal-title origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 承認済み（チャットで、2026-10-02。この作業のログと決定の記録〈docs/logs・docs/decisions のみ〉を cloudflare へ）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CAL-22 の後に平野さんが決めたことを記録し、「【4】カレンダー非掲載」に足した英語の配信2件が、毎朝の実行でカレンダーから消えたかを確かめる。コード・シート・カレンダーは変えない。
決定（2026-10-01〜02、平野さん）

* 「（1/2）」「（2/2）」（麻雀格闘倶楽部プロNo1決定戦の予定表由来の仮の予定）は件名に残す。
* 「[SANMA]」（WORLD RIICHI Online Team League の英語の配信の三人麻雀の部）は件名に残す。
* WORLD RIICHI Online Team League の英語の配信（wyPTXmvKlOU・jXnwXtnX6sY）は、同じ日の日本語の配信と重なるので非掲載にする。平野さんが「【4】カレンダー非掲載」に2行足した。
* Google カレンダーの画面での見え方は確認済みで問題なし。

手順

1. 確かめ: 「【4】カレンダー非掲載」を見出しの名前で読み、行数と動画IDの一覧を書く（6件の見込み）。平野さんの追記の後の最初の schedule の `update-live-channel.yml` の実行を探し、成否と、カレンダーの作る／直す／消すの件数と中身を書く。消すに wyPTXmvKlOU・jXnwXtnX6sY の2件が入っていること、公開 iCal で `video:wyPTXmvKlOU`・`video:jXnwXtnX6sY` の予定が無く、日本語の配信（fhiDwro18M0・VPiFsF7HTNM）の予定は残っていることを書く。追記の後の実行がまだ無ければ、そう書いて止まる（待たない）。
2. 決定の記録: 上の「決定」を CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す（CAL-22 の「調べてから決める」に結論の印を付ける）。

止まる条件

* 手順1で、追記の後の実行がまだ無い（決定の記録だけ済ませて止まる）。
* 消すに2件が入っていない、または予定が残っている（原因を書いて止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、ログと決定の記録を cloudflare へ入れる。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-23.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-23` は0件
  - origin/work/1001-cal-title は origin/cloudflare の祖先（マージ済み）
  - ローカルも祖先なので、`git merge --ff-only origin/cloudflare` で進めた（a8fcf150）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1001-cal-title
- ログ: https://github.com/retroeater/mj/blob/work/1001-cal-title/docs/logs/CHAT-0930-CAL-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 89431339）: https://github.com/retroeater/mj-logs/tree/main/guide/89431339

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/48398cff.md
