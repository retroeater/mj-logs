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

- 着手前の確認:
  - 同じ Chat-Ref のコミット・ログは無い。`RSC` は続き（36）
  - 作業ブランチ: origin に work/1123-rsc-key が無かったので、origin/cloudflare から作った（a3916043c）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順・作業ブランチ・作業ブランチの片付け・取り込みの衝突）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1123-RSC-35 の `## 報告` の状態を「判断待ち / 続き: CHAT-1123-RSC-36」にした
- 未マージの `work/` ブランチ: `research-candidates.yml` を変えるのは work/1115-rsc-autowrite（CHAT-1115-RSC-31、マージしないと決めたもの）だけ。変えているのは入力 `saikyo` と issue へのコメントのステップで、Secret の箇所ではない。止まる条件に当たらない

### 1・2. 差し替えと試し

- `research-candidates.yml` で Secret を参照するのは2か所だった: `on.workflow_call.secrets` の宣言と、ステップ「実行」の env
  - 宣言を `ANTHROPIC_API_KEY_ONEOFF`（`required: false`）にした
  - env は `ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY_ONEOFF }}` にした。スクリプトが読む環境変数の名前は今のまま
  - コミット 85c57e0b0
- 呼び出し元: `run-daily.yml` のジョブ `sokuho` だけで、渡すのは `LIVE_SHEETS_SA_KEY`・`SHEETS_READER_SA_KEY` の2つ（`ANTHROPIC_API_KEY` は渡していなかった）。`run-daily.yml`・`sync-dojo-calendar.yml`・`convert-x-posts.yml` は変えていない（毎朝の運行は変わらない）
- 試し（作業ブランチ、run 45112383777、success）:
  - 入力: kind A・`limit` 1・Haiku 5.5・`max_cost` 0.05・`max_uses` 1・`dry_run` false
  - `max_uses` 1 にしたのは、1要求の見込みを $0.021 にして `max_cost` の内に収めるため（既定の 2 では見込み $0.12 で、呼ぶ前に止まる）
  - 結果: 要求 1回、実額 $0.0148（入力 43,619・出力 817、検索 1回・取得 1回）。認証の失敗・鍵の空は無く、新しい Secret で API を呼べた（手順1の Secret の有無の確かめも兼ねる）
  - 書き込まない入力は無い（kind A〜C は `dry_run` を外すと1件ごとに候補タブに書く）ので、1行を書いた: 種別 A・対象「世界のウエニー」（一般、2026年度）・確度「見つからない」・モデル claude-haiku-5-5・費用 $0.0148
- 自己レビュー（新しい文脈のサブエージェント、1回）: 指摘なし
  - 呼び出し元が宣言していない Secret を渡す経路は無い
  - 旧 Secret の参照は残っていない
  - docs とワークフローは一致している

### 3. 記録

- `docs/decisions/automation.md` の末尾に「## 2026-11-23（CHAT-1123-RSC-36）」を足した
- `docs/notes/research-candidates.md` の139行（キーの記述）を、`ANTHROPIC_API_KEY_ONEOFF`（ワークスペース `ryoei-pro-oneoff`・キー `mj-oneoff`・月の上限 $30）に直した
- `docs/notes/github-actions.md`:
  - 一覧の research-candidates の行の Secret を直した
  - 「権限と Secret の渡し方」に「Claude API のキーの Secret」の項を足した（今後の一度きりのワークフローは `ANTHROPIC_API_KEY_ONEOFF`、git の履歴から戻すときも替える）
  - sokuho の行を直した
- `docs/notes/dojo-guest-calendar.md`: キーを共有している記述は無い（Secret の表と登録の手順だけ）。直していない

### コミット

- 差し替え: `chore: use the one-off workspace key for research candidates`
- docs: `docs: note the one-off workspace key for research and one-off jobs`

### マージ

- `git push origin work/1123-rsc-key:cloudflare` で入れた
  - push 直前に再 fetch して `merge-base --is-ancestor` を確かめた
  - 範囲のコミットの件名・本文に、閉じるキーワードの直後の `#番号` が無いことも確かめた
  - 差分は `research-candidates.yml`・このログ・RSC-35 のログの状態の行・docs だけ
  - この行を含むコミットが範囲の終わり

## 報告

- 状態: 完了
- ブランチ: work/1123-rsc-key（cloudflare にマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1123-RSC-36.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済み（範囲は「マージ」の行）
- issue: なし
- 判断が必要なこと: なし（Default のワークスペースに上限を付けるかは、指示のとおり扱っていない）
- 未確認の項目: なし（試しの1回で、新しい Secret で API を呼べたことを確かめた。試しで候補タブに1行〈種別 A・世界のウエニー・見つからない〉を書いた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4c166413）: https://github.com/retroeater/mj-logs/tree/main/guide/4c166413

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c166413/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
