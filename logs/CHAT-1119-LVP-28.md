# CHAT-1119-LVP-28

- 着手日時: 2026-11-19
- 対象issue: #475
- ブランチ: work/1119-lvp
- 着手時HEAD: 13ebaa174

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」の所属団体に「WRPM」を足し、条件つきで cloudflare へマージする
Chat-Ref: CHAT-1119-LVP-28
マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1119-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
世界選手権 第4回の予選に出た Gemma Sakamoto は「連盟プロ以外」に所属団体「WRPM」で入っているが、生成が所属団体を6つ（連盟・最高位戦・協会・RMU・連合・-）しか受け付けないため、その行を使わずに飛ばし、未登録の名前として残っている（#475）。そのため `live/wrc/4/q.html` が作られない。

### 決定（2026-11-19、平野さん）
- 所属団体に「WRPM」（World Riichi Professional Mahjong）を足す
- 足したら、すぐ再生成して本番に出す

### 前提（チャット側。平野さんの決定ではない）
- 受け付ける所属団体の一覧は `scripts/lib/names.py` の `AFFILIATIONS`（2026-11-19 の cloudflare）。ほかに所属団体の値で分けている所（`generate_saikyo_mens.py` の団体名の対応、#354 の略称の表示〈保留〉、pro/・mtsuku の表示など）があるかは要確認。表示に団体名を出す所では「WRPM」がそのまま出る見込み
- 「連盟プロ以外」で所属団体が「WRPM」の行は、2026-11-19 の時点で Gemma Sakamoto の1行（平野さんの CSV）

## 手順
1. #475 に着手中のコメントを書く。`AFFILIATIONS` に「WRPM」を足し、所属団体の値を使うほかの箇所を洗い出す（団体名の対応表が要る所があれば足す）。テストを足す（「WRPM」の行が使われる、6つ＋WRPM のほかは今までどおり飛ばす）
2. 全ページを再生成し、生成物の差分を書く（`live/wrc/4/q.html` が作られること、Gemma Sakamoto が出るページ）。テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、`regenerate-page.yml` を `target_page: live_pages pro_pages title_pages jpml_pros` で手動実行して、Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md（または所属団体の決定の置き場所）に足す

## 止まる条件
- 生成物の差分が、Gemma Sakamoto の出るページ（世界選手権 第4回の予選など）と、それに伴う一覧・件数のほかに出る（マージせずに止まる）
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが、`scripts/lib/names.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- #475 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-LVP-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-LVP-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13ebaa17）: https://github.com/retroeater/mj-logs/tree/main/guide/13ebaa17

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13ebaa17/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13ebaa17.md
