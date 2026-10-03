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

### 手順1: 確かめ

- 「【4】カレンダー非掲載」を見出しの名前で読んだ: **6行**
  - oc5eK9LEXu0（テスト放送）
  - p4G1enKcSTw（テスト放送）
  - 2UQGDePTDl0（1分未満の断片〈15秒〉）
  - 0PuFUIz_dk0（1分未満の断片〈51秒〉）
  - **wyPTXmvKlOU**（日本語の配信と重複）
  - **jXnwXtnX6sY**（日本語の配信と重複）
- schedule の実行:
  - run 36932229813（10-02 07:02 JST）: 「【4】カレンダー非掲載」は **4本**。平野さんの追記の前で、作る 2・直す 5・消す 0
  - **run 37066828131（10-03 06:30 JST）が追記の後の最初の実行**。update・yotei・regenerate すべて success
    - 「【4】カレンダー非掲載」: 6本
    - 今の予定（全期間）2,646件・載せる予定 2,617件（枠 2,493・予定表 124）
    - 「書き込みました: **作る 0・直す 2・消す 29**」。作成は止まっていない。エラーの行なし
    - 直す2件は、放送の翌朝の実際の時刻（10-02 第24期プロクイーン ベスト8 A卓）と、今日以降の枠の時刻（10-13 第6期若獅子戦 ベスト16 A、B卓 最終戦）
    - **消す29件**:
      - `video:wyPTXmvKlOU`（WORLD RIICHI Online Team League semi-final・final、理由「【4】カレンダー非掲載」）
      - `video:jXnwXtnX6sY`（WORLD RIICHI Online Team League [SANMA] semi-final・final、同じ理由）
      - Focus M season8 の公開版27件（2023-02-15〜05-03、理由「完全版でなくなった枠」）
- 消すの27件（Focus M season8）の原因を確かめた: **別の指示 CHAT-1002-CLD-02 のコミット 92de13c1（10-02 マージ）による、意図した削除**
  - 変更: `yotei.attached_publics()`。限定版の無い組の公開版が、同じ日・同じ大会の限定版と実際の配信の時間で重なれば、その限定版の無料版にして予定にしない
  - 対象: 「Focus M season8」（公開版）と「【メンバー限定】FocusM season8」（限定版）は表記が違うため別の組になり、二重の予定になっていた
  - 例: 2023-05-03 t2YUQ3jQYJ0（02:55〜04:05 UTC）→ 限定版 wKNA37ULJpY（02:55〜05:06 UTC）に付く
  - 今の層1で `attached_publics()` に当たる公開版は32件で、消えた27件はすべてその中にある
  - CLD-02 のログは「消す32件が上限30件を超え、10-03 朝の同期が止まる」と見込んでいた
    - 実際には、10-02 の実行の後（2,649＋作る2＝2,651件）から10-03 の実行の始め（2,646件）までに **5件が減っていた**
    - そのため、消すは27件＋英語の配信2件＝29件で上限の内に収まり、止まらなかった
    - 5件を誰が消したか（CLD-02 の案(1)「平野さんがカレンダーで手で消す」か）は、ワークフローの実行（その間の update-live-channel の手動実行は無い）からは分からない
- 公開 iCal（10-03 09:1x JST）:
  - 予定 **2,617件**（載せる予定と同じ）
  - `video:wyPTXmvKlOU`・`video:jXnwXtnX6sY` の予定は**無い**
  - 日本語の配信は残っている: fhiDwro18M0（2024-11-10「WORLD RIICHI Online Team League 準決勝・決勝」）・VPiFsF7HTNM（2025-04-19「WORLD RIICHI Online Team League三人麻雀 準決勝・決勝」）

### 手順2: 決定の記録

- `docs/decisions/broadcast-calendar.md` にこの指示の決定を足した。CAL-22 の「（1/2）（2/2）」「[SANMA]」の「未決」の行に、結論の印（→ 残す: 2026-10-02〈CHAT-0930-CAL-23〉）を付けた

## 報告

- 状態: 完了
- ブランチ: work/1001-cal-title（ログと決定の記録を cloudflare へ入れる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 済（docs/logs・docs/decisions のみ）
- issue: なし
- 判断が必要なこと:
  - 英語の配信2件は、10-03 朝の実行（run 37066828131）で消えた。日本語の配信2件は残っている。カレンダーは 2,617件
  - 同じ実行で Focus M season8 の公開版27件も消えた。これは CHAT-1002-CLD-02（92de13c1）の二重の予定の解消による、意図した削除
    - CLD-02 が見込んだ32件のうち5件は、10-02 と 10-03 の実行の間に別の手段で減っていた。手で消したなら記録と合う。そうでなければ確かめてほしい
    - CLD-02 のログは「10-03 朝に同期が止まる」としていたが、止まらなかった。CLD-02 のログの「判断が必要なこと」はこの結果で解消している
- 未確認の項目: 10-02〜10-03 の間に減った5件が何だったか（ワークフローの実行のログからは分からない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77c35579）: https://github.com/retroeater/mj-logs/tree/main/guide/77c35579

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77c35579/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d7dac40b.md
