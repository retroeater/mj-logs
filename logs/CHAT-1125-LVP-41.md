# CHAT-1125-LVP-41

- 着手日時: 2026-11-25
- 対象issue: #362・#448
- ブランチ: work/1125-lvp-sched
- 着手時HEAD: 020129f77

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップの「放送予定」から、メンバー限定の全量版と重なる冒頭版の予定を外す
Chat-Ref: CHAT-1125-LVP-41
マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp-sched の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1125-lvp-sched を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1125-lvp-sched origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
live/ のトップの「放送予定」に、同じ対局の予定が2つ並ぶ（例: 2026-11-25 の本番で「第43期鳳凰戦 A2リーグ第10節B卓 2026年12月1日 16:00」が2つ）。一方はメンバー限定の全量版、一方は冒頭版の配信枠。冒頭版を外す。

### 決定（2026-11-25、平野さん）
1. 「放送予定」では、同じ対局にメンバー限定の全量版と冒頭版の両方の予定があるとき、冒頭版は載せない
2. 放送が終わった予定がその日のうちは「放送予定」に残るのは問題ない（ページは毎朝作り直す。直さない）

### 前提（チャット側。平野さんの決定ではない）
- 「放送予定」は `generate_live_pages.py` の `load_schedule()` が `sync_live_calendar.load_desired()` を読んで作る。同じ読み方を、公開カレンダー「mj_放送対局」（#448）と pro/ の「今後の予定」も使う。そのため同じ重なりがカレンダーと pro/ にもある見込み（未確認）
- 冒頭版とメンバー限定版の見分け方（題名の【メンバー限定】・配信枠の限定・【無料放送】など）は未確認。2026-11-20 の本則（全量の公開版があれば公開版を載せる。CHAT-1120-LVP-32）と食い違わない見分け方にする（【無料放送】の全量の公開版は外さない）

## 手順
1. #362・#448 に着手中のコメントを書く。今の予定のデータで、同じ対局に予定が2つ以上ある組を全件洗い、全量版と冒頭版の見分け方を決める（見分けられない組は書く）
2. 「放送予定」の段で冒頭版を外す。テストを足す。全ページを再生成して生成物の差分を書き、テストを回す。公開カレンダー「mj_放送対局」と pro/ の「今後の予定」に同じ重なりがあるかと件数を書く（こちらは直さない。直すかは平野さんが決める）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- 全量版と冒頭版を見分けられない組が半分以上ある（組を書いて、マージせずに止まる）
- 生成物の差分が、live/index.html のほかに出る（マージせずに止まる）
- 直すと公開カレンダー・pro/ の表示も変わる（共通の関数を変えることになる）とき（変えずに、live/ のトップだけで外す形にする。できなければ止まる）
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが `scripts/generate_live_pages.py` の同じ関数・`scripts/sync_live_calendar.py` を変えている、または取り込みで衝突する
- #362・#448 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、公開カレンダーと pro/ の重なりの件数と、直す案を書く
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-41.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-41 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 手順0: ログの「指示」欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け無し
- 着手前の確認（指示文の issue の state、docs/notes/branch-operations.md「指示文の issue の state」）: #362 は open、**#448 は closed**（2026-10-09 にクローズ。クローズの条件〈2026-09-30 の決定〉を確かめた、のコメント）。指示文は #448 に着手中のコメントを書く対象にしているので、着手せずに止まる
- #362・#448 への着手中コメント、予定のデータの洗い出し、コードの変更はしていない
- 参考（止まる判断には使っていない）: 未マージの work/1101-prp-pub が scripts/generate_live_pages.py を変えている（load_schedule とは別の関数）。scripts/sync_live_calendar.py を変える未マージのブランチは無い

## 報告

- 状態: 中断
- ブランチ: work/1125-lvp-sched
- ログ: https://github.com/retroeater/mj/blob/work/1125-lvp-sched/docs/logs/CHAT-1125-LVP-41.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1125-lvp-sched
- 確認用URL: なし
- マージ: 未（着手前に止まった。ログのみ）
- issue: #448（closed のため着手しない）・#362（着手していない）
- 判断が必要なこと:
  - #448（公開カレンダー「mj_放送対局」）は 2026-10-09 にクローズ済み。着手中のコメントを #448 に書かず #362 だけにするか、#448 を開き直すか、カレンダーの重なりの記録先として別の issue にするかを決めて、新しい Chat-Ref で貼り直す
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eccfcf04）: https://github.com/retroeater/mj-logs/tree/main/guide/eccfcf04

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eccfcf04/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2a1c8749.md
