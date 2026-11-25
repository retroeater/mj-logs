# CHAT-1125-LVP-44

- 着手日時: 2026-11-25
- 対象issue: #362
- ブランチ: work/1125-lvp-ask
- 着手時HEAD: fa575d8a1

## 指示

【Claude作成】Claude Code 向け指示：AIコンシェルジュの「大会の予定」からも冒頭版の予定を外す Chat-Ref: CHAT-1125-LVP-44 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1125-lvp-ask の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1125-lvp-ask を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1125-lvp-ask origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1125-LVP-43 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1125-LVP-44` を足す。

目的
CHAT-1125-LVP-43 の判断待ちへの平野さんの答えのとおり、AIコンシェルジュの予定のデータの「大会の予定」（events）からも冒頭版の枠を外し、live/・pro/ とそろえる。
決定（2026-11-25、平野さん）

1. AIコンシェルジュの「大会の予定」（`workers/ask/data-schedule.json` の events）も、同じ対局にメンバー限定の全量版があるとき冒頭版を載せない（LVP-43 の案A）
2. 公開カレンダー「mj_放送対局」は今のまま（冒頭版も表示する。CHAT-1125-LVP-43 の決定3）

前提（チャット側。平野さんの決定ではない）

* LVP-43 の報告: `generate_ask_data_schedule.py` の `load_desired()` の後に、LVP-43 で作った `opening_keys()` を通せば足りる（1行ほど）。今当たるのは 2026-12-01 の A2 第10節B卓の1件
* `workers/ask/` の生成物を変えるので、CLAUDE.md「判断・作業の原則」のとおり `node --test 'workers/ask/test/*.test.mjs'` も回す。AIコンシェルジュの本番への反映の仕組みは docs/notes/ask-worker.md に従う

手順

1. #362 に着手中のコメントを書く。`generate_ask_data_schedule.py` で events に冒頭版の判定を通す（判定は LVP-43 のものを共有する）。テストを足す
2. `data-schedule.json` を作り直して差分を書き（冒頭版の1件が消えるほかに変わらないこと）、Python のテストと `node --test 'workers/ask/test/*.test.mjs'` を回す。公開カレンダーの同期に渡る予定が変わっていないことを確かめる
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、docs/notes/ask-worker.md のとおり AIコンシェルジュの本番への反映を確かめる。上の「決定」を docs/decisions/live.md に足す

止まる条件

* `data-schedule.json` の差分が、冒頭版の枠のほかに出る（マージせずに止まる）
* 公開カレンダーの同期に渡る予定が変わる（マージせずに止まる）
* テスト（Python・node）が落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* AIコンシェルジュの本番への反映に、平野さんの手作業（ダッシュボードなど）が要る（要ることを書いて、マージの後に止まる）
* 未マージの work/ ブランチが `scripts/generate_ask_data_schedule.py`・`workers/ask/` を変えている、または取り込みで衝突する
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1125-LVP-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1125-LVP-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7d4fd35e）: https://github.com/retroeater/mj-logs/tree/main/guide/7d4fd35e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7d4fd35e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2a1c8749.md
