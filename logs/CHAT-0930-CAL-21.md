# CHAT-0930-CAL-21

- 着手日時: 2026-10-01 13:33（JST）
- 対象issue: #450（クローズ済み）
- ブランチ: work/1001-cal-title
- 着手時HEAD: c97e4a85（origin/cloudflare。ローカルの work/1001-cal-title〈e3f62b2f、cloudflare の祖先〉を `git merge --ff-only origin/cloudflare` で進めた）

## 指示

【Claude作成】Claude Code 向け指示：カレンダーの件名から「【Free broadcast】」を外して cloudflare へ入れ、書き込みありで直す。あわせて、件名に残るほかの印の候補を数えて報告する（候補は外さない） Chat-Ref: CHAT-0930-CAL-21 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、cloudflare へのマージ、書き込みありのワークフローの手動実行（下の手順のもの）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1001-cal-title を使う（CAL-19・CAL-20 で使いマージ済み）。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1001-cal-title origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 承認済み（チャットで、2026-10-01。work/1001-cal-title を cloudflare へ。手順3の見込みが止まる条件に当たらない限り、確認を求めずにマージと書き込みありの実行まで進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CAL-20 の報告で残った「【Free broadcast】」（3件）を、「【無料放送】」と同じく件名から外す。あわせて、【】以外の形で件名に残っている印（ほかの括弧・定型の語など）を数え、外すかどうかを平野さんが決められるようにする。
決定（2026-10-01、平野さん）

* カレンダーの件名から「【Free broadcast】」を外す。
* ほかに外すか判断が要りそうな文字列があれば、数えて質問する（この指示では外さない）。
* マージと、件名を直す書き込みありの実行を承認する（見込みが止まる条件に当たらなければ）。

前提（チャット側。平野さんの決定ではない）

* 外す印の一覧は `lib/live_calendar.py` の `TITLE_MARKS`（CAL-20）。足すのは「【Free broadcast】」の1つだけ。
* CAL-20 の数えでは、今の件名に残る【…】は「【Free broadcast】」3件だけ。

手順

1. 実装: `TITLE_MARKS` に「【Free broadcast】」を足し、テストに1件足して全件通す。資料（docs/notes/yotei-sheet.md など、外す印を挙げている所）を直す。
2. 印の候補の棚卸し（読むだけ）: 今の載せる予定（2,649件前後）の件名について、次を種類ごとに件数と例（3件まで）で数える: (a) 【】以外の括弧（〔〕［］[]《》〈〉＜＞<>（）() など）で囲まれた部分のうち、2件以上に現れるもの、(b) 「生放送」「LIVE」「Live」「ライブ」「配信」「無料」「限定」「アーカイブ」「再放送」などの定型の語、(c) 末尾の「｜…」「| …」「/ …」などの区切りの後ろの部分のうち、2件以上に現れるもの、(d) ハッシュタグ（#…）、(e) 絵文字・記号（★☆■◆♪ など）、(f) 件名の前後の空白や連続する空白、全角と半角が混ざった同じ語（「Ａ１」と「A1」など）の目立つもの。件数が多い順に、種類ごとに上位10件までを書く。見つからなかった種類は「なし」と書く。
3. 見込み（書き込まない）: 作業ブランチで `update-live-channel.yml` を apply・yotei_apply・calendar_apply をすべて外して起動し、作る／直す／消すの件数を書く。直すが「【Free broadcast】」の3件（と放送の翌朝の時刻の直しなど数件）で、作る・消すが 0（または新しい枠の分だけ）であることを確かめる。
4. マージと書き込み: 差分が手順1のものとログのほかに無いことを確かめて cloudflare へ入れる。実行中の実行が無いことを確かめ、cloudflare で calendar_apply だけを付けて起動する。作る／直す／消すの件数を書き、手順3の見込みと同じであることを確かめる。公開 iCal で「【Free broadcast】」を含む件名が 0 件になったことと、直した3件の件名を書く。

止まる条件

* 手順3で、直すが見込みと大きく違う、または消すが出て理由が説明できない（マージしない）。
* 手順4の書き込みありの実行が失敗する、または件数が見込みと違う（マージ済みのまま原因を書いて止まる）。
* 実行が 05:30〜08:30 JST（毎朝の実行の時間帯）にかかる（そのときは手順4の書き込みを始めず、止まって報告する）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順2の候補のうち、件名から外すかどうか平野さんの判断が要りそうなもの（件数と例）を挙げる。決定は CLAUDE.md のとおり `docs/decisions/broadcast-calendar.md` に足す。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-21.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-21` は0件
  - origin/work/1001-cal-title は origin/cloudflare の祖先（マージ済み）
  - ローカルの work/1001-cal-title も祖先なので、docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（c97e4a85）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

### 手順1: 実装

- `scripts/lib/live_calendar.py` の `TITLE_MARKS` に「【Free broadcast】」を足した
  - `TITLE_MARKS` を使うのは `clean_title()` だけ。`clean_title()` を使うのは `summary_of()` だけ
- テスト: `test_clean_title` の【Free broadcast】の期待値を「外す」に変えた（CAL-20 では残すことを確かめていた）。全 480件 OK
- 資料: `docs/notes/yotei-sheet.md` の件名の規則に【Free broadcast】を足した
- `yotei.MARKS`（【メンバー限定】【無料放送】）は、予定表と枠の同じ日・同じ題名のまとめに使う別の一覧なので変えていない

### 手順2: 印の候補の棚卸し（読むだけ）

今のコード（手順1の後）で、載せる予定を手元で組み立てた。2,649件（枠 2,525・予定表 124、除外4本の後）の件名を数えた。

- (a) 【】以外の括弧（〔〕［］[]《》〈〉＜＞<>（）()「」『』）で、2件以上に現れるもの:
  - 「(仮)」**4件**。予定表由来で、例: 2027-03-12 第1期JPMLリーグ(仮)ベスト16AB卓 / 03-13 同CD卓 / 03-19 同ベスト8AB卓
  - ほかに、1件だけのものとして「（1/2）」「（2/2）」がある（予定表由来の 2026-12-29・12-30 第9回麻雀格闘倶楽部プロNo1決定戦）
  - 【】は残っていない（【Free broadcast】は手順1で外れる）
- (b) 定型の語:
  - 「特別」8件（大会名の一部）。例: インターネット麻雀日本選手権2023 Vtuber特別予選 / 2024開幕式特別記念大会 / 世界麻雀TOKYO2025プロ代表決定戦&中国籍特別予選
  - 「スペシャル」6件（番組名の一部）。例: こずえの部屋で迎春8時間スペシャル2021〜2026
  - 「特番」1件。予定表由来で、2027-01-01 お正月特番
  - 生放送・LIVE・Live・ライブ・配信・無料・限定・アーカイブ・再放送・速報・見逃し・SP: **なし**
- (c) 末尾の区切り（｜ | ／ /）の後ろで、2件以上に現れるもの: **なし**
  - 「/」で引っかかったのは「（1/2）」「（2/2）」の中の「/」だけで、区切りではない
- (d) ハッシュタグ: **なし**
- (e) 記号:
  - 「・」32件（「準決勝・決勝」などの並べ）
  - 「!」5件（例: パチスロ麻雀格闘倶楽部 真を打とう!・目指せ第二の日吉辰哉!第1回日本プロ麻雀連盟実況オーディション）
  - 「×」2件（天鳳×Vtuber杯チーム対抗戦2022・WRPM × 日本プロ麻雀4団体 パートナーシップ締結調印式）
  - ★☆■◆♪・絵文字: なし
  - どれも題名の一部で、印ではない
- (f) 前後の空白・連続する空白・全角空白・全角英数: **なし**（`clean_title()` の NFKC と `strip()` で整っている）

### 手順3: 見込み（書き込みなし）

- run 36815806058（work/1001-cal-title、8112fbb8、入力はすべて外した）。update・yotei success
- 「【4】カレンダー非掲載」: 4本。今の予定 2,649件・載せる予定 2,649件
- **作る 0・直す 3・消す 0**・そのまま 2,646
- 直す3件は、すべて件名から【Free broadcast】が外れるもの:
  - `video:lIxITe_DabQ` WRC Online Festival 2020
  - `video:wyPTXmvKlOU` WORLD RIICHI Online Team League semi-final・final
  - `video:jXnwXtnX6sY` WORLD RIICHI Online Team League [SANMA] semi-final・final
- 放送翌朝の時刻の直しは、この時点では無かった

### 手順4: マージと書き込み

- 差分は手順1の3ファイル（`live_calendar.py`・`test_live_calendar.py`・`yotei-sheet.md`）とこのログだけ。テストは OK
- push した: **c97e4a85..8112fbb8**（13:38 JST）
- 実行中の実行が無いことを確かめ、cloudflare で calendar_apply だけを付けて起動した: run 36816068768。update・yotei success
  - **「書き込みました: 作る 0・直す 3・消す 0」**
  - 直す3件は見込みと同じ。「作成は止まっていません」。エラーの行なし
- 公開 iCal: 予定 2,649件
  - 【Free broadcast】を含む件名 **0**
  - 【 を含む件名も 0
  - 直した3件の今の件名:
    - 2020-08-30「WRC Online Festival 2020」
    - 2024-11-10「WORLD RIICHI Online Team League semi-final・final」
    - 2025-04-19「WORLD RIICHI Online Team League [SANMA] semi-final・final」
- 決定の記録: `docs/decisions/broadcast-calendar.md` にこの指示の決定を足した

## 報告

- 状態: 完了
- ブランチ: work/1001-cal-title（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-21.md
- 比較URL: https://github.com/retroeater/mj/compare/c97e4a85...8112fbb8
- 確認用URL: なし（カレンダーは公開 iCal で確かめた）
- マージ: 済（8112fbb8、fast-forward）
- issue: なし（#450 はクローズ済み。今回はコメントしていない）
- 判断が必要なこと（手順2の候補。この指示では外していない）:
  - 「(仮)」4件: 予定表由来の仮の予定。2027-03-12・03-13・03-19・03-29 の第1期JPMLリーグ(仮)…
    - 予定表の件名そのもので、正式な大会名が決まれば予定表の側で変わる見込み（#488）
    - カレンダーの件名から外すか
  - 「（1/2）」「（2/2）」各1件: 予定表由来の 2026-12-29・12-30 第9回麻雀格闘倶楽部プロNo1決定戦
    - 2日に分かれた大会の何日目かを表すので、残す案
    - YouTube の枠ができれば枠の件名（「〜予選」「〜準決勝・決勝」）に置き換わる
  - 「[SANMA]」1件: WORLD RIICHI Online Team League [SANMA]。三人麻雀の部の区別なので、残す案
  - 定型の語（特別 8・スペシャル 6・特番 1）と記号（・ 32・! 5・× 2）は、どれも大会名・番組名の一部で、外す対象ではないと考える
  - ハッシュタグ・区切りの後ろの定型・前後の空白・全角英数: なし
- 未確認の項目: Google カレンダーの画面での見え方（確かめたのは公開 iCal の中身まで）
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
