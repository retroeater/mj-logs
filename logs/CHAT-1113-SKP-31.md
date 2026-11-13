# CHAT-1113-SKP-31

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-amateur
- 着手時HEAD: 21109f45b（着手時に origin/cloudflare を merge で取り込んで 339dec38d）

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1113-SKP-30 の続き）河井保国の重複を直した後に SNS ブックを更新し、全日本プロ選手権4名の写真を確かめ、SKP-30 のログ・決定をマージする
Chat-Ref: CHAT-1113-SKP-31
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログと決定だけ。SKP-30 のログと docs/decisions/saikyo.md の節も一緒に入る。ページの再生成のコミットはワークフローが cloudflare へ入れる）
貼る時機: いつでも（CHAT-1113-SKP-30 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1113-skp-amateur を続けて使う（SKP-30 のログ・決定があり、判断待ちの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1113-SKP-30 のログ（docs/logs/CHAT-1113-SKP-30.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1113-SKP-31` を足す。

目的
SKP-30 で、平野さんの update-sns-book.yml の手動実行が「連盟プロ以外」の河井保国の名前の重複で止まっていた。平野さんが重複を直したので、SNS ブックを更新して全日本プロ選手権の4名の X の写真を本番に出し、確かめる。止めていた SKP-30 のログ・決定もマージする。
決定（2026-11-13、平野さん）

* 「連盟プロ以外」の河井保国の重複を直した（SKP-30 の判断待ちへの回答）。SNS ブックの更新をやり直す

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: 河井保国の2行（SKP-30 のログでは 103行目・433行目）を直した（1行を消したか、名前で区別したかは伝えられていない）
* 4名（山本亜衣・宮本一輝・河野貴大・はじめ）の X の ID は、平野さんが SNS ブック【2】に入れてある（SKP-30 の申告）
* 仕組みは docs/notes/sns-book.md。Sheets API の読み取りの上限（#662）があるので、再生成どうしを並走させない

手順

1. 確かめ: `gh run list` で update-sns-book.yml・regenerate-page.yml・regenerate-saikyo.yml・run-daily.yml に実行中のものが無いことを確かめる（あれば終わるまで待つ。15分を超えたら止まる）。update-sns-book.yml を ref cloudflare、mode check で手動実行し、元の一覧の件数を SKP-29 の手順1の表と並べる（河井保国をどう直したかで「連盟プロ以外」の件数が −1 か ±0 になる見込み）。check-saikyo-unregistered.yml を ref cloudflare、dry_run の既定（true）で手動実行し、「登録が無い」「重複などで使われない」が 0 か確かめる。名前の重複・登録漏れが残っていれば止まる（名前と行を書く）
2. 更新: update-sns-book.yml を ref cloudflare、mode update、dry_run を偽で手動実行し、続く全ページの再生成が終わるまで待つ（同じセッションで合計45分まで延長してよい）。4名の【2】の行の X の状態、X API を呼んだ件数、再生成のコミットの変更ファイルの分類（「4名の X」「河井保国の直し」「シート側のほかの変化」「説明できない」）を表にする
3. 本番の確かめ: saikyo/2026/20261115-s1-a.html・-s1-b.html を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み、4名の写真が代替でなくなったか（なっていなければ X の状態と理由）を表にする。河井保国のページ・カードが壊れていないか（最強戦の出場があれば、その卓のページ）も確かめる

止まる条件

* 手順1で名前の重複・登録漏れが残っている
* update-sns-book.yml が入力の保全の確かめ・名前の重複で書かずに終わった、または失敗した（理由をジョブのログから書く）
* 「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 4名のうち X の状態が「解決」でない人がいても止まらない（状態と理由を表に書き、平野さんの直す先〈【2】の ID・【4】〉を添える）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない。check-saikyo-unregistered は dry_run なので常設 issue も書かない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-31.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-31 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c39047e0）: https://github.com/retroeater/mj-logs/tree/main/guide/c39047e0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c39047e0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b3a20d7c.md
