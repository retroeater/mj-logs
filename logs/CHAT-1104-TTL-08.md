# CHAT-1104-TTL-08

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 6a2ccd78a

## 指示

【Claude作成】Claude Code 向け指示：「松本ちづる」の X の写真を SNS ブックの更新（update-sns-book.yml の手動実行）で取得し、title/ に反映する Chat-Ref: CHAT-1104-TTL-08 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1104-TTL-07 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
TTL-07 で「松本ちづる」の X の ID は入ったが、写真の URL が空のまま（頭の1字の丸）だった。平野さんが今日中にプロクイーンの結果を X に投稿するため、今日のうちに写真を取得して本番の title/（第24期プロクイーンほか）に出す。
決定（2026-11-04、平野さん）

* 写真の取得から実施する。今日中に本番に出す

前提（チャット側。平野さんの決定ではない）

* 写真は SNS ブックの毎日の更新（`update-sns-book.yml`、04:10 JST）が X API で取る。平野さんが X の ID を足したのが今朝の更新の後なので、まだ取られていない見込み（要確認）。手順は docs/notes/sns-book.md「毎日の更新」「手動実行」
* `update-sns-book.yml` を `update` で手動実行すれば取れる見込み。1回 30件まで X API を呼ぶ（費用はこの範囲。毎日の更新と同じ）。【3】のページが使う値が変わると、ジョブ `regenerate` が全ページの再生成を起動する
* #662（Sheets API の読み取り上限〈429〉で並走する再生成がページを飛ばす件）があるので、全ページの再生成で title/ が飛ばされたら、title/ だけをもう一度再生成する

手順

1. `update-sns-book.yml` を `mode: update`・`dry_run: true` で手動実行し、X API を呼ぶ予定の行に「松本ちづる」が入っていること・予定の件数を報告に書く（入っていなければ、【2】の X の列と名前の一致を `check` などで確かめて止まる）。ほかの再生成・SNS ブックの更新が走っていれば終わるのを待つ（上限15分）
2. `dry_run: false` で手動実行する。終わったら、生成と同じ経路で SNS ブックを読み、「松本ちづる」の X の画像 URL が入ったこと・状態（解決 など）を報告に書く（URL・ID の値はログに書かない）。取れなかった（既定のアイコン・アカウントなし・API のエラー など）ら、状態を書いて止まる
3. 起動された再生成（または、起動されなければ `regenerate-page.yml` を title/ だけで手動実行）が終わったら、本番の `/title/pro-queen/24.html?v=<未使用の値>` で松本ちづるの行の写真が頭の1字の丸でなく画像になっていること（`<img>` の src が X の画像で、HEAD が 200）を確かめる。title/ が飛ばされていたら title/ だけ再生成して確かめ直す。Workers Builds の本番の check-run の成否を書く

止まる条件

* 手順1の dry-run で「松本ちづる」が X API を呼ぶ予定に入っていない
* X API の認証の失敗・クレジット切れ・レート制限で更新が終了コード1になった（やり直さずに止まる）
* 【2】の入力の確かめ（docs/notes/sns-book.md「毎日の更新」の2）で更新が書かずに止まった
* 再生成の失敗が title/ のページで起き、title/ だけの再生成をもう1回しても直らない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（X API を呼んだ件数と状態の内訳、再生成の run の結果を含める）
* マージは冒頭の「マージ:」の行のとおり（ログだけ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: 未
- マージ: 未
- issue: なし
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5a8971e1）: https://github.com/retroeater/mj-logs/tree/main/guide/5a8971e1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5a8971e1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
