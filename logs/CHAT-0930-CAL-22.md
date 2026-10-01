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
- ログの「### 手順1: 麻雀格闘倶楽部プロNo.1決定戦

予定表の【1】（全期間）を gviz で読み、件名を NFKC で正規化したうえで「麻雀格闘倶楽部」と「No1」「No.1」の両方を含む予定を拾った。【3】の件名・掲載も並べた。

| 日付 | 予定表の件名（【1】そのまま） | 予定ID の先頭8文字 | 【3】の掲載 |
|---|---|---|---|
| 2024-12-29 | 第7回麻雀格闘倶楽部プロNo1決定戦（1/2） | _8d9lcgr | 空欄 |
| 2024-12-30 | 第7回麻雀格闘倶楽部プロNo1決定戦（2/2） | _8d9lcgr | 空欄 |
| 2025-12-29 | 第8回麻雀格闘倶楽部プロNo1決定戦（1/2） | 22q3g4vc | 空欄 |
| 2025-12-30 | 第8回麻雀格闘倶楽部プロNo1決定戦（2/2） | 6r4c4nqu | 空欄 |
| 2026-12-29 | 第9回麻雀格闘倶楽部プロNo1決定戦（1/2） | _8d9lcgr | Y |
| 2026-12-30 | 第9回麻雀格闘倶楽部プロNo1決定戦（2/2） | _8d9lcgr | Y |

- 予定ID の先頭8文字が同じ「_8d9lcgr」の行が4つある。予定表の繰り返しの予定（元の予定ID＋日付）の形で、全体の予定IDは別
- **予定表にある3回（第7・8・9回）とも、2日に分けて「（1/2）」「（2/2）」が付いている。** 予定表は 2024-12-01 からなので、第6回以前は無い
- 【3】の件名も【1】と同じ

YouTube の層1の同じ大会の枠（ライブ、すべて公開版。限定版は無い）と、カレンダーの今の件名:

| 日付 | 動画ID | 原題 | カレンダーの件名 |
|---|---|---|---|
| 2020-12-29 | prDtKKcpsCE | 麻雀格闘倶楽部 第３回プロNo.1決定戦~予選~ | 麻雀格闘倶楽部 第3回プロNo.1決定戦 予選 |
| 2020-12-30 | JJahNiFHCCU | 麻雀格闘倶楽部 第３回プロNo.1決定戦~準決勝・決勝~ | 麻雀格闘倶楽部 第3回プロNo.1決定戦 準決勝・決勝 |
| 2021-12-29 | EHhNzRJLSWY | 麻雀格闘倶楽部 第４回プロNo.1決定戦~予選~ | 麻雀格闘倶楽部 第4回プロNo.1決定戦 予選 |
| 2021-12-30 | 6mpompiMh7o | 麻雀格闘倶楽部 第４回プロNo.1決定戦~二次予選・準決勝・決勝~ | 麻雀格闘倶楽部 第4回プロNo.1決定戦 二次予選・準決勝・決勝 |
| 2022-12-29 | 6icQZiUkwTg | 麻雀格闘倶楽部 第５回プロNo.1決定戦~予選~ | 麻雀格闘倶楽部 第5回プロNo.1決定戦 予選 |
| 2022-12-30 | 6QvM4l47tsI | 麻雀格闘倶楽部 第５回プロNo.1決定戦~二次予選・準決勝・決勝~ | 麻雀格闘倶楽部 第5回プロNo.1決定戦 二次予選・準決勝・決勝 |
| 2023-12-29 | 12CoLCHP7UU | 麻雀格闘倶楽部 第６回プロNo.1決定戦~予選~【無料放送】 | 麻雀格闘倶楽部 第6回プロNo.1決定戦 予選 |
| 2023-12-30 | Vdjvi1Zalas | 麻雀格闘倶楽部 第６回プロNo.1決定戦~二次予選・準決勝・決勝~【無料放送】 | 麻雀格闘倶楽部 第6回プロNo.1決定戦 二次予選・準決勝・決勝 |
| 2024-12-29 | QcvBKlQynMQ | 麻雀格闘倶楽部 第７回プロNo.1決定戦~予選~【無料放送】 | 麻雀格闘倶楽部 第7回プロNo.1決定戦 予選 |
| 2024-12-30 | tyNKw8PbizI | 麻雀格闘倶楽部 第７回プロNo.1決定戦~二次予選・準決勝・決勝~【無料放送】 | 麻雀格闘倶楽部 第7回プロNo.1決定戦 二次予選・準決勝・決勝 |
| 2025-12-29 | t1XpQsWJuVc | 麻雀格闘倶楽部プロNo.1決定戦2025~予選~【無料放送】 | 麻雀格闘倶楽部プロNo.1決定戦2025 予選 |
| 2025-12-30 | -0vtHJmBBfI | 麻雀格闘倶楽部プロNo.1決定戦2025~二次予選・準決勝・決勝~【無料放送】 | 麻雀格闘倶楽部プロNo.1決定戦2025 二次予選・準決勝・決勝 |

- YouTube の原題は毎年「~予選~」（1日目）と「~二次予選・準決勝・決勝~」（2日目。第3回だけ「~準決勝・決勝~」）で、「（1/2）」のような日数の印は無い
- 置き換わり方: カレンダーでは、YouTube の枠があれば枠の件名（上の表の右列）だけが載る。予定表由来の仮の予定は、過去の分は出ない
- 今カレンダーにある予定表由来の仮の予定は、第9回の2件（2026-12-29「第9回麻雀格闘倶楽部プロNo1決定戦（1/2）」・12-30「（2/2）」）だけ
  - 今は大会名「麻雀格闘倶楽部プロNo.1決定戦」（CAL-16 で足した）で同じ日・同じ大会の枠と結び付く
  - YouTube の枠ができた日（例年は直前）から、枠の件名（「〜予選」「〜二次予選・準決勝・決勝」）に置き換わり、「（1/2）」「（2/2）」はカレンダーから消える
  - つまり「（1/2）」「（2/2）」がカレンダーに出るのは、枠ができるまでの仮の予定の間だけ

### 手順2: [SANMA]（WORLD RIICHI Online Team League）

層1のライブ（同じシリーズ。英語の配信と日本語の配信が同じ日に別の枠であり、カレンダーでも別の予定になっている）:

| 日付（JST） | 動画ID | 原題 | カレンダーの件名 | 部 |
|---|---|---|---|---|
| 2024-11-10 | wyPTXmvKlOU | WORLD RIICHI Online Team League~semi-final・final~【Free broadcast】 | WORLD RIICHI Online Team League semi-final・final | 四人（英語） |
| 2024-11-10 | fhiDwro18M0 | WORLD RIICHI Online Team League~準決勝・決勝~【無料放送】 | WORLD RIICHI Online Team League 準決勝・決勝 | 四人（日本語） |
| 2025-04-19 | **jXnwXtnX6sY** | **WORLD RIICHI Online Team League [SANMA]~semi-final・final~【Free broadcast】** | **WORLD RIICHI Online Team League [SANMA] semi-final・final** | 三人（英語） |
| 2025-04-19 | VPiFsF7HTNM | WORLD RIICHI Online Team League三人麻雀~準決勝・決勝~【無料放送】 | WORLD RIICHI Online Team League三人麻雀 準決勝・決勝 | 三人（日本語） |
| 2025-11-08 | WYRKJ3TwCNw | WORLD RIICHI Online Team League2025~準決勝・決勝~【無料放送】 | WORLD RIICHI Online Team League2025 準決勝・決勝 | 四人（日本語） |
| 2026-04-19 | ocs94e6yzaY | WORLD RIICHI Online Team League2026 三人麻雀~準決勝・決勝~【無料放送】 | WORLD RIICHI Online Team League2026 三人麻雀 準決勝・決勝 | 三人（日本語） |

- 「[SANMA]」は YouTube の原題そのもの（英語の配信の題名）。三人麻雀の部の英語の配信で、日本語の配信の「三人麻雀」に当たる
- 三人と四人の区別:
  - 日本語の配信は「三人麻雀」の有無で区別できている
  - 英語の配信は、**「[SANMA]」を外すと 2024-11-10（四人）と 2025-04-19（三人）の件名が同じ「WORLD RIICHI Online Team League semi-final・final」になり、件名では区別できなくなる**（年も入っていない）
- 参考: 2022-08-13 の World Riichi e-Championship 2022 は 51秒の断片（0PuFUIz_dk0）を除外して、本放送（24DDt1Hh2kA）だけが載っている

### 手順3: 決定の記録

- `docs/decisions/broadcast-calendar.md` に、この指示の決定（「(仮)」は残す、「（1/2）（2/2）」と「[SANMA]」は調べてから決める）を足した

## 報告

- 状態: 判断待ち（読むだけの調べと決定の記録まで。コード・シート・カレンダーは変えていない）
- ブランチ: work/1001-cal-title（ログと決定の記録を cloudflare へ入れる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 済（docs/logs・docs/decisions のみ）
- issue: なし
- 判断が必要なこと:
  - **「（1/2）」「（2/2）」: 残す案**
    - 予定表では第7・8・9回の3回とも、2日に分けて付いている（毎年の書き方）
    - カレンダーに出るのは予定表由来の仮の予定の間だけ（今は第9回の2件）。YouTube の枠ができると、枠の件名（「〜予選」「〜二次予選・準決勝・決勝」）に置き換わって消える
    - 2日のどちらかを示す情報でもあるので、外すと12-29と12-30の仮の予定が同じ件名になる
    - 言い換える（「1日目」「2日目」）なら、外す印ではなく置き換えの規則を足すことになる
  - **「[SANMA]」: 残す案**
    - YouTube の原題（英語の配信）そのもの
    - 外すと、英語の配信の四人の部（2024-11-10）と三人の部（2025-04-19）の件名が同じになり、区別できなくなる
    - 「三人麻雀」に言い換えると日本語の配信とそろうが、原題から離れる。英語の配信の件名は英語のまま（semi-final・final）なので、言い換えるなら件名全体の言語がそろわなくなる
  - 参考: WORLD RIICHI Online Team League は、英語の配信と日本語の配信が同じ日に別の予定として並ぶ（2024-11-10・2025-04-19）。CAL-12 の決定（完全版の枠は全部載せる）どおりだが、片方だけにするなら「【4】カレンダー非掲載」に英語の配信の動画ID（wyPTXmvKlOU・jXnwXtnX6sY）を足せばよい
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8d31613b）: https://github.com/retroeater/mj-logs/tree/main/guide/8d31613b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8d31613b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
