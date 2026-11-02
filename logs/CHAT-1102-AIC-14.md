# CHAT-1102-AIC-14

- 着手日時: 2026-11-02（JST）
- 対象issue: #586
- ブランチ: work/1102-aic
- 着手時HEAD: 3203fecd

## 指示

【Claude作成】Claude Code 向け指示：（AIC-13 の続き）質問欄の言い回しを2つ直す（同率でない一覧の「N人が並んでいます」、入会期の一覧の「主な」「上位」）。cloudflare へマージする
Chat-Ref: CHAT-1102-AIC-14
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1102-AIC-13 のマージ〈4212ea255〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1102-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。4212ea255 が origin/cloudflare の祖先であることを確かめる。AIC-13 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1102-AIC-14` を足し、その「判断が必要なこと」2つへの回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜13 で使用）。

## 目的

AIC-13 の本番で出た言い回しの誤り2つを直す。どちらも事実の誤りではない。

### 決定（2026-11-02、平野さん）

AIC-13 の「判断が必要なこと」への回答。

- 同率でない一覧の「N人が並んでいます」（WRC-R の「40期生」で「10人が並んでいます」）: `tidyAnswer()` の置き換えを「N人が並んでいます」まで広げる（プログラムで直す）。同率（tie）のある答えの「並んでいます」は残す
- 入会期の一覧の「主な名前は」（「37期生で一覧」）: 「上位」「主な」と書かない一文を、`entry` の一覧にも広げる
- どちらも、11/09 の書き出しを待たずにすぐ直す

### 前提（チャット側。平野さんの決定ではない）

- AIC-11 で、「ほか N人が並んでいます」を同率が無いときに「ほか N人がいます」に置き換えた（`tidyAnswer()`）。AIC-12 で「上位」「主な」と書かない一文を champions の一覧に入れた。今回はその範囲を広げる
- 置き換えの形（「10人が並んでいます」を「10人です」「10人がいます」など）は、前後の文が自然になるように任せる。同率のときの答え（AIC-13 の「同率の名前は10人と others」）を崩さない
- 「主な」「上位」の一文が効かない場合に、プログラムで置き換えるかは任せる（AIC-11 で言語の混入が指示の文だけでは止まらなかった前例がある）
- 5大会（鳳凰戦・女流桜花・WRC リーグ・WRC-R・最強戦）で同じ形にする
- 本番の確かめは1日10問まで（同じ接続元から）。2026-11-02 は AIC-11〜13 で問いを使っている。上限に当たったら送るのをやめ、送れなかった問いを「未確認の項目」に書く

## 手順

1. 直す: 上の2つを、今の `workers/ask/` で再現してから直す
2. テスト:
   - 次をテストの例にする
     - WRC-R の「40期生」
     - 「37期生で一覧」
     - 同率のある問い（「並んでいます」が残ること）
   - 直す前のコードで新しいテストが落ちることを確かめる
   - `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
   - docs/notes/ask-worker.md の該当の行を直し、docs/decisions/houou.md に上の「決定」を足す
3. issue・マージ・本番:
   - #586 に結果をコメントする
   - cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   - 本番に送る問いは次の順（上限に当たったらそこでやめる）
     1. wrcr/ の「40期生」
     2. 「37期生で一覧」
     3. 同率のある問い1つ
     4. ask-worker.md「本番で送る固定の問い」から数問

## 止まる条件

- #586 に他セッションの着手中コメントがある
- 未マージの work/ ブランチが `workers/ask/` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 直しで、同率のある答えの「並んでいます」が消える、またはほかの型の答えが変わる（テストで確かめる）
- テストが通らない（マージせずに止まる）
- 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-13 の「未確認の項目」2つは、この指示の「未確認の項目」にも写す
  - 2026-11-09（月）03:00 JST の書き出しと、「現女流桜花」の引数
  - 地域で断られた問いの記録
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-AIC-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-AIC-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認（2026-11-02）

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- `git log --all --grep="CHAT-1102-AIC-14"` は0件。`AIC` はこのチャットの系列（AIC-01〜13）
- 4212ea255 は origin/cloudflare の祖先。`origin/work/1102-aic` はマージ済みで、ローカルも cloudflare の祖先なので `git merge --ff-only origin/cloudflare`（3203fecd）
- AIC-13 のログの `## 報告` の状態に ` / 続き: CHAT-1102-AIC-14` を足し、「判断が必要なこと」2つの後ろに回答を書き足した
- #586 に他セッションの着手中は無い（最後は AIC-13 の結果）。着手中のコメントを書いた。未マージの work/ ブランチで `workers/ask/` を変えているものは無い

### 1. 再現

- `tidyAnswer()` の置き換えは「ほか N人が並んでいます」→「ほか N人がいます」（同率が無いとき）だけで、WRC-R の「40期生」の「…の10人が並んでいます。ほか13人です」の「10人が並んでいます」は当たらない
- 2回目の「上位」「主な」と書かない一文は「champions の items は…」だけで、入会期の一覧（entry）は対象外。「主な名前は」を直す処理も無い

### 2. 直したこと（3f11c6ae7・28f043c6a・7ac34d046、文書 4ec34db24・70c651cee）

| 項目 | 直し方 | 5大会 |
|---|---|---|
| 「N人が並んでいます」 | 入会期の一覧（`entry`）の答えで、同率（tie）が無ければ「N人です」に置き換える（`COUNT_IN_LINE`、全角の数字も） | 共通（`tidyAnswer()`） |
| 「主な」「上位」 | 2回目の一文を「champions の items は最近に獲った人から並ぶ一部、entry の items は大会ごとの並び(リーグや最後に出た期)の一部で、どちらも順位ではない。「上位」「主な」と書かない」に広げ、入会期の一覧の答えの「主な名前」「上位の選手」を「名前」「選手」に置き換える（`ENTRY_RANKED`） | 共通 |
| 同率のある答え | 変えない（tie があれば置き換えをしない、前から） | 共通 |

- 決定の「N人が並んでいます まで広げる」は、入会期の一覧に限った。全部の型に当てると、回数の段（byTimes、「5回が最多で、2人が並んでいます」）や、tie の付かない下位の同率のランキング（「3位には2人が並んでいます」）の本当の並びまで「2人です」に変わり、止まる条件「ほかの型の答えが変わる」に当たるため（自己レビューの指摘）。発端の本番の誤りは2つとも入会期の一覧
- 「ほか N人が並んでいます」の置き換え（AIC-11）も全角の数字に当たるようにした
- テスト: `week3.test.mjs` に、WRC-R の「40期生」と「37期生で一覧」の答え（ask を通して）・champions の「主な」は残すこと／同率のある答え（WRC-R の通過回数の1位の9人）の「並んでいます」が残ること／tie の付かない本当の並び（回数の段・下位の同率）が残ること・入会期の一覧の全角・ranking の「ほか１３人」を足した。`cross.test.mjs` の一文の期待値を直した
- 直す前のコード（origin/cloudflare）に当てると、新しい「入会期の一覧を直す」テストと一文のテストが落ちた（同率で残すテストは前後とも通る）。1回目の自己レビューの後の直しの前（3f11c6ae7）には、「本当の並びを残す」2件が落ちる（レビューの報告で確認）
- `node --test 'workers/ask/test/*.test.mjs'` 131件通過、`python3 -m unittest discover -s scripts/tests` 通過

### 3. 自己レビュー（docs/notes/self-review.md、新しい文脈のサブエージェント、2往復）

1回目（`git diff origin/cloudflare...HEAD -- workers/ask`。目的・決定・観点を渡した。`node --test` 130件通過を相手も確かめた）:

| 指摘 | 扱い |
|---|---|
| 「N人が並んでいます」を全部の型に当てると、回数の段（champions に tie は付かない）や、tie の付かない下位の同率のランキングの正しい文まで変わる（要判断） | 直した（28f043c6a）: 入会期の一覧だけに当てる。止まる条件に合わせ、ほかの型の答えを変えない側を選んだ |
| 全角の数字に当たらない | 直した（28f043c6a・7ac34d046）: `[0-9０-９]` |
| 正規表現の文法・順序・ENTRY_RANKED は問題なし | 指摘なし |
| tie のテストが既存とほぼ重なる／本当の並びの扱いを決める検査が無い | 直した（28f043c6a・7ac34d046）: 本当の並びを残すテストを分けて足した |

2回目（`git diff 4ec34db24 HEAD -- workers/ask` だけ）:

| 指摘 | 扱い |
|---|---|
| 限り方・正規表現・ほかの型が変わらないこと | 指摘なし |
| 「ほか N人」の正規表現だけ全角に当たらず、2つの扱いがそろっていない | 直した（7ac34d046） |
| 足した3行がテスト名と合わない | 直した（7ac34d046）: テストを分けた |

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6d624329）: https://github.com/retroeater/mj-logs/tree/main/guide/6d624329

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6d624329/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/01d3ecae.md
