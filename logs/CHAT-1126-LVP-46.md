# CHAT-1126-LVP-46

- 着手日時: 2026-11-26
- 対象issue: #362
- ブランチ: work/1126-lvp-cmt
- 着手時HEAD: c250d09b1

## 指示

【Claude作成】Claude Code 向け指示：generate_pro_pages.py の古くなったコメント（live/ の「放送予定」）を直す
Chat-Ref: CHAT-1126-LVP-46
マージ: 承認済み（チャットで）。コメントのほかに差分が出たらマージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1126-lvp-cmt の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1126-lvp-cmt を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1126-lvp-cmt origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1126-LVP-45 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1126-LVP-46` を足す。

## 目的
CHAT-1126-LVP-45 の判断待ちのとおり、live/ の「放送予定」を外したので古くなったコメントを直す。

### 決定（2026-11-26、平野さん）
- `scripts/generate_pro_pages.py` の `load_live_plans` のコメント「冒頭版は載せない(live/ の「放送予定」と同じ、2026-11-25 の決定)」を、今の状態（AIコンシェルジュの「大会の予定」と同じ）に直す。コメントだけで、動きは変えない

### 前提（チャット側。平野さんの決定ではない）
- ほかのファイル（コード・docs/notes）にも live/ の「放送予定」を今あるものとして書いた記述が残っているかは未確認

## 手順
1. #362 に着手中のコメントを書く。上のコメントを直す。リポジトリの中で live/ の「放送予定」を今あるものとして書いた記述をほかにも洗い（docs/decisions・docs/logs の過去の記録は除く）、同じ指示で直せる記述（コメント・docs/notes）は直す
2. テストを回す（コメント・docs のみの変更なので、生成物の差分が出ないこと）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする

## 止まる条件
- コメント・docs のほかに差分が出る（マージせずに止まる）
- テストが落ちる（無関係な失敗は原因をログに書いて進めてよい）
- #362 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1126-LVP-46.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1126-LVP-46 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し・0章（指示欄の末尾が指示文の最後の行と一致）・#362 は open で、着手中コメントはこのセッションのものだけ。雛形の行の欠け無し。LVP-45 の状態の末尾に「 / 続き: CHAT-1126-LVP-46」を足した
- 着手中コメント: #362

### 洗い出し（`git grep 放送予定`、docs/decisions・docs/logs・生成物・data を除く）
- live/ の「放送予定」を今あるものとして書いていた記述は5か所。ほかに `opening_keys` の使い手を「live/・pro/」と書いた docstring が1か所
- 直したもの（b1276d8af、コメント・docstring だけ）:
  - scripts/generate_pro_pages.py（load_live_plans のコメント）: 「live/ の「放送予定」と同じ」→「AIコンシェルジュの「大会の予定」と同じ」
  - scripts/lib/live_calendar.py（opening_keys の docstring）: 使い手を「pro/ の「今後の予定」と AIコンシェルジュの「大会の予定」」に
  - scripts/generate_ask_data_schedule.py（load_desired の docstring）: 「live/・pro/ と同じ」→「pro/ と同じ」
  - scripts/tests/test_live_calendar.py・test_pro_pages.py の docstring
  - assets/live.js のコメント: 段の例「放送予定・ライブなど」→「ライブ・動画・決勝」（配信するファイルだがコメントだけ）
- 直さなかったもの: scripts/generate_live_pages.py・test_live_pages.py・docs/notes/live-page-design.md の記述は「放送予定」を置かないことを書いたもので、今の状態と合う
- docs/notes には古い記述は無かった（live-page-design.md は LVP-45 で置き換え済み）

### 確かめたこと
- 差分はコメント・docstring の6行だけ（git diff で確かめた）。生成物を作る処理は変わらないので、生成物の差分は出ない
- テスト: Python 2,323件 OK、`node --check assets/live.js` OK
- セルフレビューのサブエージェントは使っていない（変更がコメント・docstring だけで、動きに関わる行が無いことを差分で確かめたため）

### マージ
- aa1dbdc58 を cloudflare へ push（直前に fetch して祖先を確かめた）。Workers Builds: mj の check-run は aa1dbdc58 で success

## 報告

- 状態: 完了
- ブランチ: work/1126-lvp-cmt
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1126-LVP-46.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1126-lvp-cmt
- 確認用URL: なし（コメント・docs のみ）
- マージ: 済（aa1dbdc58）
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cde56134）: https://github.com/retroeater/mj-logs/tree/main/guide/cde56134

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c19c2147.md
