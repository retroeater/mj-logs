# CHAT-1002-CLD-03

- 着手日時: 2026-10-03
- 対象issue: #448
- ブランチ: work/1002-cld
- 着手時HEAD: a8fcf150

## 指示

【Claude作成】Claude Code 向け指示：10-03 朝の同期で、放送対局カレンダーの残り27件が消えたかを確かめ、記録を直す（10-03 朝の実行の後に貼る） Chat-Ref: CHAT-1002-CLD-03 マージ: ドキュメントのみの変更（docs/logs/・docs/notes/yotei-sheet.md）なので、CLAUDE.md「ブランチ運用」のとおり完了報告のうえ cloudflare へ入れてよい。それ以外のファイルを変える必要が出たら、変えずに判断待ちで止まる。 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1002-CLD-02 のログの `## 報告` を読み、状態が「判断待ち」で、判断が必要なことが「10-03 朝の同期が、消す予定32件で上限30件を超えて止まる」であることを確かめる（違えば止まる）。#448 に他セッションの着手中コメントが無いか確かめ、着手中コメントを残す。

目的
#448。CHAT-1002-CLD-02 でマージした規則（題名の違う公開版を、同じ日・同じ大会で配信の時間が重なる限定版に付ける）で消える32件のうち、平野さんが5件を手で消した。残り27件を 10-03 朝の毎朝の同期が消したかを確かめ、止めていたログの状態と文書を直す。
決定（2026-10-02、平野さん）

* CLD-02 の案(1)（カレンダーの画面で数件を手で消し、残りを翌朝の同期に消させる）にした。平野さんは 2026-10-02 に5件を手で消した（チャットで「5件手動で削除しました」）

前提（チャット側。平野さんの決定ではない）

* チャット側が頼んだ5件は、Focus M season8 以外の公開版: `dFOIYiIeQy4`（2026-10-01 鳳匠戦）・`J3KYImyN7-s`（2026-08-29 桜蕾戦）・`DNnt08iLGm0`（2026-03-29 女流プロ麻雀日本シリーズ）・`G4w5fnsWVco`（2023-10-13 若獅子戦）・`6LJecimPcYI`（2023-07-16 麻雀日本シリーズ）
* チャット側が 2026-10-02 18:40 JST 頃に Google カレンダーの連携で「mj_放送対局」のこの5日を読み、5件が無く、付く先の限定版の予定（`lRfK1G89L-M`・`lkFA50-qMi0`・`fEx5AHBtqvc`・`v8I76nBJHyc`・`gMDOwdKFSLw`）が残っていることを確かめた
* 残りは Focus M season8 の公開版27件（CLD-02 のログの手順1の表、2023-02-15〜2023-05-03）。10-03 朝の同期で消す予定は27件＋その日のほかの理由の分になる見込み（上限は `sync_live_calendar.MAX_DELETES` の30件。チャット側は上限の比較が「超える」か「以上」かを読んでいない）
* 毎朝の実行は 07:00 JST 頃に動くことが多い（cron は 02:43 JST）。この指示は 10-03 の実行の後に貼られる想定
* この指示でも、カレンダー・シート・コード・ワークフローは変えない。書き込みありの手動実行もしない

手順

1. 10-03 朝の実行を確かめる。
   * `update-live-channel.yml` の 2026-10-02 15:00 UTC 以降の schedule の run を特定し、ジョブ `yotei` のステップ「放送対局の公開カレンダーへ同期する」の成否と、ログの「作る・直す・消す・そのまま」の件数、消した予定の一覧（出ていれば）を書く
   * 公開の iCal を読み、全件数と、CLD-02 の32件（公開版の動画ID）がどれも無いこと、付く先の限定版32件が残っていることを書く。27件以外に消えた予定があれば、理由（仮の予定から枠への切り替えなど）を1件ずつ書く
   * 同じ入力から `build_desired()` を動かし、カレンダーの件数と一致するかを書く
2. 記録を直す。
   * CHAT-1002-CLD-02 のログの `## 報告` の状態を、判断が出て片付いたこと（平野さんが5件を手で消し、残り27件を 10-03 朝の同期が消した。続きは CHAT-1002-CLD-03）に合わせて直す
   * docs/notes/yotei-sheet.md の今の内容を読み、「1回に消す上限（`MAX_DELETES`）を超えると同期が何も書かずに失敗する」ことと、「同期の規則を変える変更では、マージの前に消す件数の見込みを上限と比べ、超えるなら入れる前に手順（手で消す件数・`--allow-many-deletes` の扱い）を決める」ことが書かれているかを確かめ、無ければ該当の項に足す（writing-for-agents の skill を使う。既にあれば足さず、場所を報告に書く）
   * #448 に結果をコメントする（クローズしない。10-09 の朝の実行の後に 10-08 の鳳匠戦 C卓・D卓が1件ずつかを確かめる件が残ることを書く）
3. 「マージ:」の行のとおり cloudflare へ入れ、マージ後に `regenerate-page.yml` が動いたかを書く（docs だけなので動かない見込み。待つのは15分まで）。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）。

止まる条件

* 0章で、CLD-02 の報告の状態・内容が上と違う。#448 に他セッションの着手中コメントがある
* 2026-10-02 15:00 UTC 以降の schedule の run がまだ無い、または実行中（「まだ動いていない」と書いて止まる。記録は直さない）
* 同期のステップが失敗している。原因（上限を超えた・別のエラー）と、ログに出た消す件数を書いて止まる（記録は直さない。状態は判断待ち）
* CLD-02 の32件のうちカレンダーに残っているものがある。付く先の限定版が消えている。27件以外に消えた予定で理由の説明がつかないものがある
* docs 以外のファイルを変える必要が出た
* 作業ブランチへの push や cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、10-03 朝の run（番号・成否・作る/直す/消すの件数）、カレンダーの全件数と `build_desired()` との一致、32件が無いこと、27件以外に消えた予定の有無と理由、yotei-sheet.md に足した（または既にあった）記述の場所、本番で確かめられていないこと（10-08 の C卓・D卓）を入れる
* 同期が失敗していたときは、失敗通知のメールについて「この件（上限の超過）によるもので、対応はこの報告の判断待ちのとおり」か、別の原因ならその内容を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-03` は無し
- 作業ブランチ: `origin/work/1002-cld`（29b3e66b）は cloudflare へマージ済み。ローカルの `work/1002-cld` も cloudflare の祖先なので、`git merge --ff-only origin/cloudflare` で a8fcf150 へ進めた

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: #448
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 89431339）: https://github.com/retroeater/mj-logs/tree/main/guide/89431339

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/89431339/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0621b467.md
