# CHAT-1125-LVP-43

- 着手日時: 2026-11-25
- 対象issue: #362
- ブランチ: work/1125-lvp-sched
- 着手時HEAD: a23b763e0

## 指示

【Claude作成】Claude Code 向け指示：live/ の「放送予定」と pro/ の「今後の予定」から、メンバー限定の全量版と重なる冒頭版の予定を外す（公開カレンダーは今のまま）
Chat-Ref: CHAT-1125-LVP-43
マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp-sched の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1125-lvp-sched（CHAT-1125-LVP-41 のログのコミットがある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1125-LVP-41 は手順0で止まった（中断）ので、この指示はその貼り直し（CHAT-1125-LVP-42 は貼らずに欠番）。CHAT-1125-LVP-41 のログの `## 報告` の状態を「中断 / 続き: CHAT-1125-LVP-43」にする。

## 目的
live/ のトップの「放送予定」に、同じ対局の予定が2つ並ぶ（例: 2026-11-25 の本番で「第43期鳳凰戦 A2リーグ第10節B卓 2026年12月1日 16:00」が2つ。一方はメンバー限定の全量版、一方は冒頭版の配信枠）。live/ と pro/ では冒頭版を外し、公開カレンダーは今のままにする。

### 決定（2026-11-25、平野さん）
1. live/ のトップの「放送予定」では、同じ対局にメンバー限定の全量版と冒頭版の両方の予定があるとき、冒頭版は載せない
2. pro/ の選手ページの「今後の予定」も 1 と合わせる
3. 公開カレンダー「mj_放送対局」は冒頭版も表示する（YouTube のライブの枠と一致させる。直さない）
4. 放送が終わった予定がその日のうちは「放送予定」に残るのは問題ない（ページは毎朝作り直す。直さない）
5. 着手中のコメントは #362 だけに書く（#448 はクローズ済みのまま開き直さない）

### 前提（チャット側。平野さんの決定ではない）
- 3つとも同じ `sync_live_calendar.load_desired()` を読む（live/ は `generate_live_pages.py` の `load_schedule()`）。カレンダーの同期を変えずに、live/ と pro/ の表示の側だけで外す
- 冒頭版とメンバー限定版の見分け方（題名の【メンバー限定】・配信枠の限定・【無料放送】など）は未確認。2026-11-20 の本則（全量の公開版があれば公開版を載せる。CHAT-1120-LVP-32）と食い違わない見分け方にする（【無料放送】の全量の公開版は外さない）

## 手順
1. #362 に着手中のコメントを書く。今の予定のデータで、同じ対局に予定が2つ以上ある組を全件洗い、全量版と冒頭版の見分け方を決める（見分けられない組は書く）
2. 判定を1か所にまとめ、live/ の「放送予定」と pro/ の「今後の予定」から呼んで冒頭版を外す。テストを足す。全ページを再生成して生成物の差分を書き（変わる pro/ のページの件数と例）、テストを回す。公開カレンダーの同期に渡る予定が変わっていないことを確かめる
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages pro_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足す

## 止まる条件
- 全量版と冒頭版を見分けられない組が半分以上ある（組を書いて、マージせずに止まる）
- 生成物の差分が、live/index.html と pro/ の「今後の予定」のほかに出る（マージせずに止まる）
- 公開カレンダーの同期に渡る予定が変わる（マージせずに止まる）
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが `scripts/generate_live_pages.py`・pro/ の生成・`scripts/sync_live_calendar.py` の同じ関数を変えている、または取り込みで衝突する
- #362 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-43.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-43 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

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
