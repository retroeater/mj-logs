# CHAT-1127-SNU-01

- 着手日時: 2026-11-27
- 対象issue: なし
- ブランチ: work/1127-snu
- 着手時HEAD: 888add4

## 指示

【Claude作成】Claude Code 向け指示：SNS ブック【2】の更新（最強戦出場者の SNS ID）を反映し、全ページを再生成する
Chat-Ref: CHAT-1127-SNU-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（ページの再生成は cloudflare で走るワークフローのコミットで入る）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/SNU を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/SNU origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが SNS ブックの【2】ID で最強戦出場者の SNS ID を更新した。その値（X の写真を含む）を本番のページへ反映する。

決定（2026-11-27、平野さん）

* 最強戦出場者の SNS ID を SNS ブックで更新したので、再生成する

前提（チャット側。平野さんの決定ではない）

* 平野さんが直したのは【2】ID と見ている（どのタブ・どの選手・どの SNS かは聞いていない。要確認）。【4】手動補正を直しただけなら update では再生成が起動しない（docs/notes/sns-book.md「検知と更新の役割」）
* X の ID を足した・変えた行の写真は X API で取るので、ページの再生成だけでは入らない。`update-sns-book.yml` の `update`（dry_run オフ）を手動実行すれば、【3】のページが使う値が変わったときジョブ `regenerate` が `regenerate-page.yml` を `all` で呼ぶ（docs/notes/sns-book.md「手動実行」）
* YouTube の ID を変えた行の画像は `data/youtube_channels.json`（毎週月曜の fetch-youtube.yml）から取る。YouTube の ID の変更があれば、今日は画像が出ないことがある（要確認。出ない行は報告に書くだけでよく、fetch-youtube.yml は動かさない）
* 毎日の更新（04:10 JST 起動の run-daily）は平野さんの更新より前に終わっている見込み（要確認）
* 11月の Actions の使用量は含まれる分の 181%（mj-logs の actions/morning.md、11/26 まで）。全ページの再生成は1回だけにする

手順

1. `regenerate-page.yml` を作業ブランチ work/SNU の ref・入力 `check`・`target_page: all` で手動実行し、止まるページがあれば一覧（ページ名・理由）を書いて止まる（docs/notes/chat-routines.md「データ更新後の生成」）
2. `update-sns-book.yml` を cloudflare で入力 mode `update`・dry_run を偽で手動実行する。X API を呼んだ行（名前・前の URL・今の URL・状態）を全件表に書き、ジョブ `regenerate` が `regenerate-page.yml` の `all` を起動したか（run の ID）を書く
3. ジョブ `regenerate` が動かなかった（【3】のページが使う値が変わらなかった）ときは、【2】の今回の変更が【3】に写っているかを確かめて理由を書いたうえで、`regenerate-page.yml` を cloudflare・`target_page: all`・dry_run 偽で1回だけ手動実行する。再生成の完了と Workers Builds の成否を確かめ、最強戦（saikyo/）の個別ページのうち手順2で値が変わった選手のページ（数件）で、本番（`?v=<未使用の値>` 付き）に新しい SNS のリンク・写真が出ていること（画像 URL は HTTP 200）を確かめて表に書く

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる。 全ページの再生成を待つのは、同じセッションで合計45分まで延長してよい。

止まる条件

* 手順1で止まるページがある
* 手順2の update が失敗した、または X API を呼べていない（認証の失敗・クレジット切れ・レート制限・トークンなし）
* 手順2の X API の呼び出しが上限（`API_CALL_LIMIT`）に届き、「上限で未取得」の行が残った（残った行の一覧を書いて止まる。再生成は起動されていればそのままでよい）
* 再生成の差分に、SNS の値（ID・リンク・画像 URL）とシートのほかの変化で説明できない種類の行がある（想定外の行を全部書いて止まる）
* 再生成または Workers Builds が失敗した
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-SNU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-SNU-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cde56134）: https://github.com/retroeater/mj-logs/tree/main/guide/cde56134

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cde56134/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/888add4c.md
