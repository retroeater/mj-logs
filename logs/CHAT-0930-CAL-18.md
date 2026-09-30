# CHAT-0930-CAL-18

- 着手日時: 2026-09-30 20:49（JST）
- 対象issue: #450・#453
- ブランチ: work/0930-cal-450
- 着手時HEAD: 77033776（origin/work/0930-cal-450 と同じ）

## 指示

【Claude作成】Claude Code 向け指示：#450（過去の放送もカレンダーに載せる）を cloudflare へ入れ、今夜のうちに層1の取り直しと初回の作成（約2,500件）を手動実行で行って見届ける Chat-Ref: CHAT-0930-CAL-18 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push、cloudflare へのマージ、書き込みありのワークフローの手動実行（下の手順のもの）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-cal-450 を続けて使う（CHAT-0930-CAL-16 の実装のコミットがあるため）。`git checkout -b work/0930-cal-450 origin/work/0930-cal-450` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる マージ: 承認済み（チャットで、2026-09-30。work/0930-cal-450 を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-16 のログの `### 手順4: 初回の作成

- 始めた時刻は 21:08 JST（05:30 より前）。実行中の実行は無かった
- cloudflare で calendar_apply だけを付けて起動した: run 36712938984。**update・yotei success**、regenerate skipped
  - yotei ジョブは 12:10〜12:40 UTC（約30分）
- 出力:
  - 「作る: 2503件…/ 直す: 14件 / 消す: 0件 / そのまま: 132件」
  - 進み具合の行は 25行（100/2503 〜 2500/2503）
  - **「書き込みました: 作る 2503・直す 14・消す 0」**
- 途中で止まらなかった（「作成は止まっていません」）。Calendar の上限にも当たらず、#450 への止まった所のコメントも無い
  - 約2,500件を約30分で作れた。1件あたり約0.7秒で、見込みの 0.5秒より遅い
- 見込み（作る 2,503・直す 14・消す 0）と同じ

### 手順5: 確かめ

- 公開 iCal（calendar.google.com の basic.ics）で「mj_放送対局」を読んだ:
  - **予定 2,649件**。載せる予定（枠 2,525＋予定表 124）と同じ
  - 年ごと: 2015年 2・2020年 134・2021年 374・2022年 409・2023年 438・2024年 419・2025年 417・2026年 390・2027年 66
- 除外の4本（oc5eK9LEXu0・p4G1enKcSTw・2UQGDePTDl0・0PuFUIz_dk0）は説明欄のどこにも無い（載っていない）
- 過去の予定を見た:
  - 2015-08-04 第32期鳳凰戦 A2リーグ第5節
  - 2015-08-08 【麻雀】インターネット麻雀日本選手権2015 決勝戦
  - 2020-11-03 Focus M season4
  - 2020-08-31 第37期鳳凰戦 A1リーグ第7節A卓
  - 2024-10-08 Focus M season10
  - 2024-11-18 第19期女流桜花 AリーグプレーオフB卓
- どれも、開始・終了は実際の時刻（取り直した配信終了日時）で、説明欄は1行目が URL、次に【対局者】【実況】【解説】の見出しごとに1人1行。**今日以降の枠の予定と同じ形**
  - 気づいた点: /live の【3】に行が無い古い枠は、件名に YouTube の題名の「【麻雀】」がそのまま残る（例 2015-08-08）。`clean_title()` は【メンバー限定】だけを外す作りで、今日以降の枠と同じ扱い。直すかは下の「判断が必要なこと」に書いた
- #450 に結果をコメントした（5911511598）。#450 は開けたまま
- 決定の記録: `docs/decisions/broadcast-calendar.md` に CAL-18 の決定を足した。CAL-12 Q11 の行に置き換えの印を付けた

## 報告

- 状態: 完了（#450 は明朝の毎朝の実行を確かめるまで open）
- ブランチ: work/0930-cal-450（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-18.md
- 比較URL: https://github.com/retroeater/mj/compare/675fa13e...4bd68b4b
- 確認用URL: なし（カレンダーは公開 iCal で確かめた）
- マージ: 済（4bd68b4b、fast-forward）
- issue:
  - #450: 結果をコメントした。open
  - #453: コメントしてクローズした
- 判断が必要なこと:
  - **明朝の毎朝の実行の見込み**: 作成は途中で止まらなかったので、続きは無い
    - 作る 0（新しい枠や、枠ができた予定があればその分）
    - 直す 数件（放送翌朝の実際の時刻など）
    - 消す 0（今日 09-30 の仮の予定は無いので、日付が過ぎて消えるものも無い）
    - 予定表の【3】の追記・削除 0、#481 への知らせなし（今夜の実行で【1】【2】【3】がそろったため）
    - 層1は毎日の取り直しだけ（backfill は入力が無いと動かない）
  - /live の【3】に行が無い古い枠の件名に「【麻雀】」などの接頭辞が残る（2015年ごろの題名）。直すなら `live_calendar.clean_title()` で外す規則を足す（今日以降の枠にも効く）。今はそのまま
- 未確認の項目:
  - 明朝の毎朝の実行の結果（#450 を閉じる前に確かめる）
  - Google カレンダーの画面での見え方（確かめたのは公開 iCal の中身まで）
- エラー: なし。取り直しの実行で /live の【1】の読み返しに ReadTimeout が1回あったが、再試行で通った

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2e1b8f0d）: https://github.com/retroeater/mj-logs/tree/main/guide/2e1b8f0d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2e1b8f0d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b7f138e5.md
