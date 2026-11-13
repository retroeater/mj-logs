# CHAT-1113-SKP-36

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-retro
- 着手時HEAD: 8a4983285（着手時に origin/cloudflare を merge で取り込んだ）

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1113-SKP-35 の続き）佐藤凌の X の ID の変更を SNS ブックとページに反映する（待つ対象をシートを読む実行に絞る）
Chat-Ref: CHAT-1113-SKP-36
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（SKP-35 の止まった後のログも一緒に入る。ページの再生成のコミットはワークフローが cloudflare へ入れる）
貼る時機: いつでも（CHAT-1113-SKP-35 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-skp-retro を続けて使う（SKP-35 の止まった後のログが未マージで残っているため。SKP-35 の手順1の分は 29294f560 でマージ済み）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ origin/cloudflare から作る（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1113-SKP-35 のログ（docs/logs/CHAT-1113-SKP-35.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1113-SKP-36` を足す。

目的
SKP-35 の手順2〜4（佐藤凌の X の ID の変更を SNS ブックとページに反映して確かめる）が、ほかのセッションの実行（Search Console のエクスポート）を待って止まった。待つ対象を絞って続ける。
決定（2026-11-13、平野さん）

* 明朝の自動を待たず、今夜続きの指示で反映する（SKP-35 の判断待ちへの回答）
* 佐藤凌（麻雀最強戦2026 全日本プロ選手権に出場）の X のアカウント名が変わったので、SNS ブックの ID を変えた。X画像URL の差し替え・ページの再生成を行う（SKP-35 と同じ）

前提（チャット側。平野さんの決定ではない）

* SKP-35 の「全ワークフローの実行が無くなるまで待つ」は広すぎた（チャット側の書き方の誤り）。待つ理由は Sheets API の1分あたりの読み取り上限（#662）なので、待つ対象はスプレッドシートを読む・書く実行だけにする。Search Console のエクスポートがシートを読まないことは、チャット側は確かめていない（要確認。ワークフローとスクリプトを読んで決める）
* 仕組みは docs/notes/sns-book.md「毎日の更新」（X の ID が変わった行は X API で取り直す。【3】のページが使う値が変わると全ページの再生成が続く）。SNS ブックのどのタブ・どの値に変えたかは伝えられていない（【2】ID の X の列と見ている。要確認）

手順

1. 待つ対象の確かめ: `gh run list`（絞らず）で実行中・待機中のものを一覧にし、それぞれがスプレッドシートを読む・書くかを、ワークフローと呼ばれるスクリプトから確かめて表にする（読まない実行は待たない。理由を1行ずつ書く）。シートを読む・書く実行があれば終わるまで待つ（15分を超えたら止まる）
2. SNS の確かめ: update-sns-book.yml を ref cloudflare、mode update、dry_run を真で手動実行し、X API を呼ぶ予定の行（名前・前の URL・理由）を表にする。佐藤凌の行が取り直しの対象に入っていなければ止まる
3. 更新: 起動の直前に手順1の確かめをもう一度行い、update-sns-book.yml を ref cloudflare、mode update、dry_run を偽で手動実行する。続く全ページの再生成が終わるまで待つ（同じセッションで合計45分まで延長してよい）。佐藤凌の行の X の状態・前後の画像 URL、X API を呼んだ件数、再生成のコミットの変更ファイルの分類（「佐藤凌の X」「シート側のほかの変化」「説明できない」）を表にする。再生成の前後で saikyo/ の `abema.tv/video/episode`・`abema.tv/channels` のリンクの数を比べる
4. 本番の確かめ: 佐藤凌の写真・X のリンクが出るページ（saikyo/2026/20261115-s1-a.html と、`git grep` で前の画像 URL・前の ID を引いて見つかる全ページ）を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み（Workers Builds の success の後）、新しい画像（HTTP 200）と新しい ID のリンクになったこと、前の ID・前の画像 URL が生成物に残っていないことを表にする

止まる条件

* 手順1で、シートを読む・書く実行が15分を超えて終わらない（実行の名前・起動時刻・今のステップを書く）
* 手順2で佐藤凌の行が取り直しの対象に入らない、または名前の重複・入力の保全の確かめで止まる
* 手動実行のどれかが失敗した（原因をジョブのログから書く）
* 佐藤凌の X の状態が「解決」でない（状態と、平野さんの直す先〈【2】の ID・【4】〉を書いて判断待ちにする）
* 「説明できない」変更ファイルがある、または最強戦の卓のページから ABEMA のリンクが減る後退がある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-36.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-36 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c3ef367b）: https://github.com/retroeater/mj-logs/tree/main/guide/c3ef367b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c3ef367b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/de3fa135.md
