# CHAT-1123-RSC-36

- 着手日時: 2026-11-23
- 対象issue: なし
- ブランチ: work/1123-rsc-key
- 着手時HEAD: a3916043c

## 指示

【Claude作成】Claude Code 向け指示：候補の調べもの・一度きりの作業の Claude API のキーを、専用のワークスペースのキーに分ける
Chat-Ref: CHAT-1123-RSC-36
マージ: 承認済み（チャットで。research-candidates.yml の Secret の名前の差し替えと docs。手順2の試しの実行が成功したときに限る）
貼る時機: いつでも（平野さんが新しいワークスペース・キー・Secret を作り終えている）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1123-rsc-key の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1123-rsc-key を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-rsc-key origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1123-RSC-35 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1123-RSC-36` を足す。

目的
CHAT-1123-RSC-35（判断待ち）への回答。Claude API のキー `mj-dojo-guest`（Secret `ANTHROPIC_API_KEY`、Default のワークスペース）を、道場部ゲスト・X 投稿の変換・候補の調べもの・一度きりの作業で共有しているのをやめ、候補の調べものと一度きりの作業を専用のワークスペースのキーに分ける。
決定（2026-11-23、平野さん）

* RSC-35 の判断待ち1は案A: 候補の調べもの（research-candidates）と、今後の一度きりの作業（連盟ニュースの読み取り・文字の点検のようなもの）は、新しいワークスペースのキーを使う。道場部ゲストと X 投稿の変換は、今のキー（`ANTHROPIC_API_KEY`）のまま
* 新しいワークスペースの月の支出上限は $30（$20 でメール通知）。大きい一度きりの作業の前には、そのつど上げる
* （報告）平野さんが Console・GitHub で作った（2026-11-23、スクリーンショット）: ワークスペース `ryoei-pro-oneoff`（月間支出上限 $30・$20 でメール通知）、そのワークスペースの API キー `mj-oneoff`（有効期限 2027-11-23）、GitHub の Repository secret `ANTHROPIC_API_KEY_ONEOFF`
* （報告）RSC-35 の判断待ち2: Default のワークスペース（キー `mj-dojo-guest`）の月間支出上限は未設定で、組織の上限 $200 が効いている（今月の使用 $130.10）

前提（チャット側。平野さんの決定ではない）

* 作ったのは平野さん。Default のワークスペースに上限を付けるかは別に決める（この指示では扱わない）
* research-candidates の kind D（最強戦の速報）は Claude API を使わない（run-daily の `sokuho` は `ANTHROPIC_API_KEY` を渡さない。docs/notes/github-actions.md 161行付近）。毎朝の運行は変わらない見込み（要確認）
* 一度きりのワークフロー（oneoff-news-read.yml・oneoff-text-audit.yml）はもう外してある。git の履歴から戻すときは新しい Secret を使う、と docs に書く

手順

1. Secret の有無を確かめる: `research-candidates.yml` を作業ブランチで `ANTHROPIC_API_KEY_ONEOFF` に差し替え、手順2の試しの実行で、鍵が空で止まるかどうかで確かめる（Secret の一覧の権限が無ければ。値はログに出さない）
2. 差し替えと試し: `research-candidates.yml` の Secret を `ANTHROPIC_API_KEY_ONEOFF` にする（Secret を参照するほかの場所が無いことも確かめる）。作業ブランチで `workflow_dispatch` を1回、API を実際に1回呼ぶ最小の入力（例 kind A・`limit` 1・Haiku 5.5・`max_cost` 0.05・`dry_run` false。候補タブへの書き込みが起きる入力なら、書き込まない入力を探し、無ければ書いた1行を報告する）で動かし、成功と実額をログに書く
3. 記録: 上の決定を docs/decisions/automation.md の RSC の節に「## 2026-11-23（CHAT-1123-RSC-36）」として書く。docs/notes/research-candidates.md（139行付近のキーの記述）・docs/notes/github-actions.md（Secret の表）・docs/notes/dojo-guest-calendar.md（共有の記述があれば）を今の事実に直し、「今後の一度きりの Claude API のワークフローは `ANTHROPIC_API_KEY_ONEOFF` を使う」を docs/notes/github-actions.md の Secret の節に書く

止まる条件

* 手順2の試しで、Secret が空・認証の失敗になる（平野さんの登録の前に貼った見込み。エラーを書いて止まる。別の Secret に戻して動かさない）
* 差分が `research-candidates.yml`・このログ・docs 以外に出る、または `ANTHROPIC_API_KEY` を使うほかのワークフロー（sync-dojo-calendar・convert-x-posts・run-daily）を変える必要が出る
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が `research-candidates.yml` の同じ箇所を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* コミットの件名・本文に、閉じるキーワード（close・fix・resolve とその活用形）の直後の `#番号` を書かない（push の前に確かめる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-RSC-36.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-RSC-36 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1123-rsc-key
- ログ: https://github.com/retroeater/mj/blob/work/1123-rsc-key/docs/logs/CHAT-1123-RSC-36.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1123-rsc-key
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 820ada9d）: https://github.com/retroeater/mj-logs/tree/main/guide/820ada9d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
