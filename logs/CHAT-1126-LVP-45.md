# CHAT-1126-LVP-45

- 着手日時: 2026-11-26
- 対象issue: #362
- ブランチ: work/1126-lvp
- 着手時HEAD: c19c2147d

## 指示

【Claude作成】Claude Code 向け指示：live/ のトップから「放送予定」の段を外し、「ライブ」「動画」「決勝」の3段にする Chat-Ref: CHAT-1126-LVP-45 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1126-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1126-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1126-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
live/ はアーカイブを探すページで、「放送予定」は YouTube の予定の枠に付加価値を付けていない。トップを「ライブ」「動画」「決勝」の3段にする。
決定（2026-11-26、平野さん）

1. live/ のトップから「放送予定」の段を外す。段は上から「ライブ」「動画」「決勝」の3つ（CHAT-1125-LVP-40 の決定1 を改める）
2. 公開カレンダーへのリンクなどの代わりの入口も置かない
3. 冒頭版を外す判定（CHAT-1125-LVP-43・LVP-44）は、pro/ の「今後の予定」と AIコンシェルジュの「大会の予定」で使い続ける（消さない）

前提（チャット側。平野さんの決定ではない）

* トップの「放送予定」は `generate_live_pages.py` の `load_schedule()`・`schedule_rows()`・`schedule_tile()` と `build_top_rows()` の段で作っている（2026-11-25 の cloudflare）。pro/・AIコンシェルジュ・公開カレンダーの同期は別の生成で読む
* live/ の生成が予定のデータ（`sync_live_calendar.load_desired()`）を読む理由はトップの「放送予定」だけの見込み（未確認）

手順

1. #362 に着手中のコメントを書く。トップから「放送予定」の段を外し、live/ の生成でだけ使っていた部品（予定の読み込み・タイル・スタイル・テスト）を消す（残さない）。冒頭版の判定と、pro/・AIコンシェルジュ・公開カレンダーが使う部分は変えない。テストを直す
2. 全ページを再生成して生成物の差分を書き（live/index.html のほかに変わらないこと）、テストを回す。Playwright で PC と 390px のトップを確かめ、ログに書く（スクリーンショットは作業ブランチに置かない）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages` で手動実行して Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足し、docs/notes/live-page-design.md のトップの記述を置き換える

止まる条件

* 生成物の差分が、live/index.html のほかに出る（pro/・AIコンシェルジュのデータ・公開カレンダーの同期を含む。マージせずに止まる）
* 予定のデータの読み込みを live/ の生成から外すと、ほかの生成・ワークフローが動かなくなる（どこが使うかを書いて、マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが `scripts/generate_live_pages.py` のトップの生成の同じ関数を変えている、または取り込みで衝突する
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1126-LVP-45.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1126-LVP-45 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 723e8646）: https://github.com/retroeater/mj-logs/tree/main/guide/723e8646

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/723e8646/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c19c2147.md
