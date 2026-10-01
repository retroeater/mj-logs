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

## 報告

- 状態: 作業中
- ブランチ: work/1001-cal-title
- ログ: https://github.com/retroeater/mj/blob/work/1001-cal-title/docs/logs/CHAT-0930-CAL-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1001-cal-title
- 確認用URL: なし
- マージ: 未
- issue: #450
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c97e4a85）: https://github.com/retroeater/mj-logs/tree/main/guide/c97e4a85

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c97e4a85/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14245a4d.md
