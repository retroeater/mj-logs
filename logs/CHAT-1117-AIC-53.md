# CHAT-1117-AIC-53

- 着手日時: 2026-11-17
- 対象issue: #654
- ブランチ: work/1117-aic
- 着手時HEAD: 3e21c40

## 指示

【Claude作成】Claude Code 向け指示：（申送り）AIC 系列 2026-11-10〜17 の振り返りを記録し、知見を層に落とす（Worker の指示の文の検査を広げる・self-review.md と chat-routines.md に規則を足す・ask-worker.md に学びを足す）。文書とテストだけ。cloudflare へマージする
Chat-Ref: CHAT-1117-AIC-53
マージ: 承認済み（AIC 系列のマージの例外〈2026-11-02、平野さん〉: テストが通る・本番の答えがデータと一致する・答えが変わらない）。この指示は文書とテストだけを変え、`workers/ask/src/` を変えない。src を変えないと通らないなら、変えずに止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1117-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1117-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。識別子 `AIC` はこのチャットの系列（AIC-01〜52 で使用。AIC-17・24・31 は欠番）。CHAT-1109-AIC-44 のログには手を入れない。

## 目的

平野さんが「振り返り」「振り返りのレビューをお願いします」「申送り」と言い、Fable 5.2 のレビューを入れた（2026-11-17）。AIC 系列のこのチャット（2026-11-10〜17。CHAT-1116-AIC-45〜CHAT-1117-AIC-52 と、11/11・11/16 の週次の確認）の振り返りを記録し、機械的に確かめられる手順に落とせる知見だけを層に落とす。中身と層はチャット側の案で、平野さんが個別に承認したものではない。

### 決定（平野さん）

* 朝一の確認の予定は 6:00 JST にそろえる（2026-11-16。「9:30 まで待つ必要がないので朝イチ確認系は 6:00 で統一します」）。docs/decisions/houou.md の AIC-47 の節に書かれているが、ほかの系列にも当たるので docs/decisions/operations.md にも記録する

### 知見 → 選んだ層 → 理由

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| Claude への指示の文に実データの数の例を入れると、Claude が別の選手の答えにその数を写す（AIC-47、`SUBJECT_LINE`。本番で約41分誤った答えが出た）。テストは Claude を作り物にしているので、マージ前に捕まえる層が無かった | 計算的センサー（Worker のテスト） | AIC-49 が player の指示に足した検査（指示の文に数・期・名前の例が無い）と同じ形で、ほかの型の指示にも当てられる |
| 続きの指示で差分のレビューを受けたとき、実装に影響しない書き方の提案だけで止まり、往復が1回増えた（AIC-50） | 推論的センサー（docs/notes/self-review.md「収束」に1行） | Code がレビューの結果を見て進むか止まるかを決める手順。指示ごとの文面に頼ると揺れる |
| 予定が済んだかを聞かれ、予定の説明の項目をログと突き合わせる前に「完了」と答えた（11/11。約束 (u) を2回続けて守れなかった） | ガイド文（docs/notes/chat-routines.md「期日とカレンダー」に1行） | チャット側の振る舞いで、センサーを置けない。約束のままでは2回続けて守れなかったので、【済】にする手順の中の順序として書く |
| 朝一の確認は 6:00 JST（平野さんの決定） | ガイド文（docs/notes/chat-routines.md「期日とカレンダー」に1行） | チャット側が予定・予約を作るときの決まり。センサーを置けない |
| Worker はプレビューを持たず、本物の Claude の答えはマージ後に初めて見える。戻し方を指示に書いていないと、誤った答えが戻す指示を待つ間残る（AIC-47→48） | 学び（docs/notes/ask-worker.md「運用の学び」） | 規則でない学びの正はここ（AIC-44）。指示の文面の話で、センサーを置けない。約束でも見る |
| 新しい非 GET の入口は WAF のカスタムルールに遮られる（AIC-45→46） | 足さない | AIC-45 で docs/notes/cloudflare.md に書いた。約束で見る |
| 本番の確かめの1日10問を同じ日の複数の指示で使い切った（11/16、AIC-47・48 → AIC-49 が確かめられず翌日へ） | 学び（docs/notes/ask-worker.md「運用の学び」） | チャット側の見込みの話。約束でも見る |

## 手順

1. 実物で確かめる
   * mj-logs ではなくリポジトリの docs/decisions/operations.md の最後の節まで読み、最新の約束と使われている記号を確かめる。下の約束の記号 (y)(z)(aa) が使われていれば、空いている次の記号に替える
   * #654 の状態（open か）を REST で確かめ、記録に書く
   * 追記先（docs/notes/self-review.md・docs/notes/chat-routines.md・docs/notes/ask-worker.md・docs/decisions/operations.md）の今の大きさと、上限・警告域のある文書はその差を測ってログに書く。警告域を超えるなら、規則だけを残し、事例を docs/notes/handover-archive-2026.md へ移す。それでも超えるなら止まる
   * `workers/ask/test/` の、player の指示に数・期・名前の例が無いことを確かめる検査（AIC-49 が足したもの。名前は（要確認））を開く
2. テスト: その検査を、Claude に渡すほかの指示の文（1回目・2回目・横断の指示。どれが対象かは実物で決め、対象と対象外をログに書く）へ広げる。今の指示の文が検査に当たるなら、**src は変えず**、当たった文と場所をログの表と「判断が必要なこと」に書き、その型は検査の対象から外して理由をコメントに書く（指示の文を変えると答えが変わるため、直すかどうかは週次の確認で決める）
3. 文書
   * docs/notes/self-review.md「収束」に1行: 続きの指示で差分だけのレビューを受けたとき、要修正が無ければマージへ進む。書き方の提案は直さず issue にコメントで移す（指示文が別に定めていればそちら）
   * docs/notes/chat-routines.md「期日とカレンダー」に2行: (1) 予定を【済】にする前・済んだかを答える前に、予定の説明の項目を1行ずつログの「未確認の項目」と突き合わせ、確かめた跡の無い項目は行き先の予定へ移す (2) 朝一の確認の予定と予約は 6:00 JST に置く（2026-11-16、平野さんの決定）
   * docs/notes/ask-worker.md「運用の学び」に「### 2026-11-17（AIC-45〜52 の振り返り、#654）」を足す: 指示の文に実データの例を入れない（検査の名前を添える）／Worker の答えを変える指示は「マージ:」の行に「マージ後の本番の確かめで外れたら、戻す指示を待たずに revert して止まる」を初めから入れる／同じ日に Worker の指示を複数出すときは本番の10問を指示ごとに割り振る／週次の書き出しの再試行は、読み取りは 429・5xx・ネットワークの失敗、追記と行の削除は 429 だけ（今の「週次の cron」の記述と食い違えば、そちらに合わせる）
   * docs/decisions/operations.md に「## 2026-11-17（CHAT-1117-AIC-53）」の節を足し、下の「記録する中身」を書く。docs/decisions/houou.md には足さない（6:00 の決定は operations.md に書く）
4. テストが通れば cloudflare へマージする。本番の確かめは要らない（src を変えない）

### 記録する中身（docs/decisions/operations.md）

* うまくいかなかったこと
  * 11/11 の予定を、説明の項目をログと突き合わせる前に「完了」と答え、後で訂正した（「現女流桜花は？」の from・to が確かめられていなかった。11/16 の CSV で閉じた）
  * 11/16 の週次の書き出しの失敗の調べで、Cloudflare の画面を「Trigger Events」と案内したが、実行の成否は「Observability」だった（往復が1回増えた）。D1 の SQL の列名の出典を「AIC-11 のログ」と書いたが、AIC 系列のログでその列名があるのは AIC-45 だけ（SQL は使われていない）
  * AIC-45 が WAF（Block non-GET/HEAD methods）で止まり、AIC-46 と平野さんの手作業が1回ずつ増えた。新しい POST の入口を作らせたのに、WAF を前提に書かなかった
  * AIC-47 のマージ後、本番の「中田」の答えがデータと合わなくなった（2026-11-16 11:11〜11:52 JST の約41分）。Claude への指示に足した一文の例（HIRO柴田さんの実データの数）を Claude が写した。指示文に戻し方を書いておらず、戻す指示（AIC-48）を待った
  * AIC-47 の3点目（大会別の要求を外す）は、画面と週次の行数だけを見て、Worker のテスト79件が大会別の要求に頼ることを見ていなかった（AIC-52 が1本増えた）
  * AIC-49 は2回目のレビューの後の直しで止まり（規則どおり）、AIC-50 は止まる条件を「直しが出たら止まる」と書いたため、テストの書き方の提案2件で止まった（AIC-51 が1本増えた）
  * 11/16 に本番の確かめの10問を AIC-47・48 で使い切り、AIC-49 の本番の確かめが翌日に回った
* 事実として残る点
  * 週次の書き出し: 再試行（読み取りは 429・5xx・ネットワークの失敗、追記と行の削除は 429 だけ）と、Actions の「AIボットの週次書き出し」（ask-weekly.yml）での再実行。合言葉 `ASK_WEEKLY_TOKEN`、WAF の例外1句。11/16 の再実行で 11/09〜15 の52行が入った（AIC-45・46）
  * player の答えの最初の文はプログラムが書き、データに無い期が出た Claude の文は落とす（AIC-49〜51、261e00317）。大会別の要求は、その大会を context とした横断として答える（AIC-52、a5b852192）
  * 持ち越しの一覧（正は CHAT-1109-AIC-44 のログ）は、1週目（11/09〜15）に来訪者から聞かれていない。2週目は 11/23 の書き出しで見る
  * AIC-52 の本番の確かめで「今期の首位は？」（context 鳳凰戦）が「答えられません」だった。以前に答えられていたかは確かめていない（#654 に移した。11/23 の書き出しで聞かれていれば直す候補）
  * #654 の状態（手順1で確かめた結果を書く）
* 前回の約束（CHAT-1116-ACF-49 の節までの約束。手順1でより新しい約束が見つかれば「この振り返りでは見ていない」と書く）
  * (s) は守れた。(w) は守れた（選択の問いとマージの可否の問いを分けた）
  * (u) は守れなかった（上の 11/11 の予定。2回続けて守れなかったので、約束から外して chat-routines.md の行に移す）
  * CHAT-1114-SEO-30（時機を待つものは予約を入れる）は一部守れなかった（AIC-50 の「11/17 以降に貼る」は、カレンダーの予定だけで予約を入れなかった。平野さんが翌朝貼り、害は出ていない）
  * (p) の後の行（スプレッドシートを読み書きする実行の前に、ほかの実行が無いことを見る）は、AIC-45・46 の指示に入っていた
  * (v)・(x)・(j)・(k)・(r)・(t)・(o)・(g)・(h)・(n)・(q)・(a-7) は当たる場面が無い
* ガイド文に足したもの: 上の「知見 → 選んだ層 → 理由」の表のとおり（この指示の表を参照と書く）
* 約束（次の振り返りで確かめる）。今までの約束は置き換えず、次を足す
  * (y) Worker に新しいパス・GET 以外の入口を作る指示で、前提に WAF のカスタムルールとの関係を書き、手順1で到達を確かめさせたか
  * (z) Worker の答えを変える指示の「マージ:」の行に、本番で外れたときの戻し方を初めから入れたか。同じ日に Worker の指示を複数出すとき、本番の10問を割り振ったか
  * (aa) 平野さんに外部サービスの画面を案内する前に、その画面で知りたいこと（設定か、実行の成否か）が見られるかを文書で引いたか
  * (u) は約束から外す。次の振り返りでは、chat-routines.md の行（【済】にする前の突き合わせ）が守られたかを見る
* 作業中の回答: なし

## 止まる条件

* 追記先が警告域を超え、事例を archive へ移しても収まらない
* 検査を広げるために `workers/ask/src/` を変える必要がある（変えずに、手順2のとおり対象から外して書く。それでも通らなければ止まる）
* 未マージの work/ ブランチが同じ文書の同じ節・同じテストを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-AIC-53.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-AIC-53 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット: `git log --all --grep='CHAT-1117-AIC-53'` は0件（ログ先行 push の前）。AIC は系列の続きなので XXX の重複確認は対象外
- 作業ブランチ: origin/work/1117-aic はあり、`git merge-base --is-ancestor origin/work/1117-aic origin/cloudflare` が真（マージ済み）。ローカルに無いので `git checkout -b work/1117-aic origin/cloudflare`（3e21c40）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- #654: open（GitHub MCP の issue_read、2026-11-17）。最近のコメントに他セッションの着手中なし（最後は AIC-52 の完了）。着手コメント: https://github.com/retroeater/mj/issues/654#issuecomment-6949004122
- 0章ゲート: 未マージの work/ ブランチ7本（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1101-prp-pub・1103-ryo5・1115-rsc-autowrite）は、self-review.md・chat-routines.md・ask-worker.md・decisions/operations.md・workers/ask/test/ のどれも変えていない（`git diff --name-only origin/cloudflare...<branch>`）。CLAUDE.md 23,299・handover.md 23,619 バイト（警告域 25,600 未満）

### 手順1: 実物の確かめ

- docs/decisions/operations.md の最後の節は「2026-11-17（CHAT-1117-LGV-43）」。約束の記号は CHAT-1116-ACF-49 の (v)(w)(x) が最新で、(y)(z)(aa) は未使用 → そのまま使う。ACF-49 より新しい節（ACF-51〜54・LGV-40〜43）に約束は無い
- 大きさ（追記前 → 追記後、バイト）: self-review.md 3,523 → 3,835、chat-routines.md 28,449 → 28,897、ask-worker.md 106,460 → 107,851、decisions/operations.md 299,420 → 305,797。4つとも check-assets.yml の上限・警告域の対象外（対象は CLAUDE.md・handover.md・chat-side-operations.md・instruction-template.md）なので、archive への移しは不要
- AIC-49 の検査: `workers/ask/test/player.test.mjs`「player の2回目の指示の一文に、数・期・名前の例が無い」。`PLAYER_FOLLOW_LINE` に `/[0-9０-９]/` と `/第|さん|「|例/` が無いことを見る
- 週次の cron の記述（ask-worker.md「週次の cron」）と `workers/ask/src/weekly.mjs` の `retryable()`: 追記・行の削除（once）は 429 だけ、ほかの Google の呼び出し（トークン・ブックの情報など）は 429・5xx・ネットワーク。指示文の「読み取りは…」は、記述に合わせて「ほかの Google の呼び出し（トークン・読み取りなど）」と書いた

### 手順2: 検査を広げる

Claude に渡す指示の文（`workers/ask/src/ask.mjs`）と、対象にしたか:

| 指示 | どこで作るか | 対象 | 理由 |
|---|---|---|---|
| 1回目（plan） | `PLAN_SYSTEMS`＋`PLAN_LINES`（`holderLine()` など）＋`SCHEDULE_PLAN_LINE` | 対象外 | 答えの文を書かせず問いを JSON の引数に直すだけ。読み方の例（「23期生」なら23、「松澤」「堀」）と、データの時点の期・年度（`holderLine()`・`saikyoLines()` の最新の決勝の期）を意図して入れている。写しても引数でデータを引くので、例の数が答えの文に出ない |
| 2回目（大会ごと） | `answerSystem()`＋`ANSWER_LINES[id]`（`write()`） | 対象 | |
| 2回目（player） | `answerSystem()`＋`playerLines()`（`PLAYER_FOLLOW_LINE`） | 対象（`PLAYER_FOLLOW_LINE` 単独は player.test.mjs が従来どおり見る） | |
| 横断の2回目（2大会以上） | `crossSystem()`（`writeMany()`） | 対象 | |

検査の形: `answerSystem()`・`ANSWER_LINES`・`crossSystem()` は export されていないので src を変えず、Anthropic を作り物にした `ask()` で Worker が送る2回目の system をそのまま集める（5大会×champions・player の context 付きの問いと、「鳳凰戦と最強戦の歴代の優勝者は？」の横断）。集めた範囲（5大会の champions の行・player の一文・横断の一文）があることも確かめる。行ごとに、算用数字と「さん」（人の名前）が無いことを見る。player.test.mjs の `第|「|例` は、2回目の指示が「第N期」「「現〜」」の形で置き場所を書くため使わない（当てると規則の行がすべて当たる）。

今の指示の文で当たった箇所（2026-11-17）:

| 当たった文 | 場所 | 扱い |
|---|---|---|
| 同率の書き方の例「4回が最多で、3人が並んでいます（大和さん・覚野陽生さん・角谷陽介さん）」「2位は10回で、3人が並んでいます（客野直さん・山脇千文美さん・末続ヒロトシさん）」 | `answerSystem()` の tie の行（全大会・横断の2回目に共通） | 実データの数・名前の例。src は変えず、2つの引用だけを対象外（`TIE_EXAMPLES`。レビュー1回目の後に行ごと除外から改めた）。同じ行の「1人だけ」「1位なら」は規則の数として `RULE_NUMBERS`。直すかは週次の確認で決める（「判断が必要なこと」） |
| 「上位2段」 | `answerSystem()` の byTimes の行 | 規則の数。対象外（`RULE_NUMBERS`） |
| 「リーグ名(A1・Aリーグなど)」 | `answerSystem()` の日本語の行 | リーグ名。対象外 |
| 「3文程度まで」 | `answerSystem()` の最後の行 | 規則の数。対象外 |
| 「鳳凰位決定戦は期ごとに1回」 | `ANSWER_LINES.houou` の champions の行 | 規則の数。対象外 |
| 「1st・2nd Stage」「対局数10回以上」 | `SAIKYO_TOTALS_LINE` | 段の名前・規定の数。対象外 |

検証:

- `node --test workers/ask/test/prompt-examples.test.mjs` → pass 1・fail 0
- 修正前でも通らないか（#310）: `ANSWER_LINES.ouka` の先頭に AIC-47 の例の行（「主語を書かずに述語から書き始める(例:「第18期から第42期まで28期出場しています。」)」）を一時的に入れると fail（found にその行が出る）。src は退避した写しに戻し、`git diff --stat workers/ask/src` は空
- `node --test 'workers/ask/test/*.test.mjs'` → tests 226・pass 226・fail 0（AIC-52 の 225 件＋1）
- `python3 -m unittest discover -s scripts/tests` → OK
- `python3 scripts/check_asset_limits.py` → すべて OK

### 手順3: 文書

- docs/notes/self-review.md「収束」に1行
- docs/notes/chat-routines.md「期日とカレンダー」に2行（【済】の前の突き合わせ、朝一の確認は 6:00 JST）
- docs/notes/ask-worker.md「運用の学び」の末尾（「答えの事実を誰が書くか」の表の後）に「### 2026-11-17（AIC-45〜52 の振り返り、#654）」
- docs/decisions/operations.md に「## 2026-11-17（CHAT-1117-
### 自己レビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose）に `git diff origin/cloudflare...HEAD -- workers docs/notes docs/decisions`、指示の目的、CLAUDE.md「コード規約」を渡した。

1回目（複製の src に例を足す変異で確かめた: AIC-47 の形・SEASONS_LINE・横断の行の例は fail、tie の行に足した例と漢数字は pass）:

| 指摘 | 扱い |
|---|---|
| 要修正: tie の行を行ごと除外していて、その行に後から足した例が素通りする（`prompt-examples.test.mjs` の `TIE_LINE`） | 直した（88e0fbb04。2つの引用だけを除き、「1人だけ」「1位なら」を `RULE_NUMBERS` へ。tie の行に「(例: 中田さんは第18期から28期出場)」を一時的に足すと fail、戻して 226 件通過） |
| 提案: `EXAMPLE` は漢数字・「さん」の無い名前を拾わない。`簡略化:` の1行が無い | 直した（88e0fbb04、`簡略化:` のコメント） |
| 提案: 要求の組み立てに `cross-body.mjs` の `rawRequest()` を使える | 見送り（player.test.mjs も手で組み立てており、既存の形と揃っている。書き方の提案なので収束の規則どおり直さない。#654 にコメントで残す） |
| 文書とコード（`weekly.mjs` の `retryable()`）の突き合わせ・網羅（plan・`write()`・`writeMany()` の3か所）・コミット 261e00317・a5b852192 の実在 | 問題なし |

2回目（`git diff 4744a1766 HEAD -- workers`）: 要修正・提案なし。変異3つ（tie の行の末尾・途中に例、tie の引用の数を変える）はすべて fail。2回目の後に直しは無い。

### cloudflare の取り込み

マージ前の再 fetch で origin/cloudflare が進んでいた（LGV-44 のログ・decisions、log/ の再生成）。`git merge origin/cloudflare` で docs/decisions/operations.md が衝突。末尾への追記どうし（cloudflare 側「## 2026-11-17（CHAT-1117-LGV-44）」、こちら「## 2026-11-17（CHAT-1117-AIC-53）」）なので両方を残し、LGV-44 の節の後に AIC-53 の節を置いた（dc65b8aae）。解いた後の該当箇所:

```
- LGV-43 の試しで止まった3件の値: 2122104025969815647（さわやか）の駅は 浜松、…（【3】には書かなかった）

## 2026-11-17（CHAT-1117-AIC-53）

- 朝一の確認の予定は 6:00 JST にそろえる（2026-11-16、平野さん。…）
```

同じマージコミットで、AIC-53 の節の tie の記述を「その2つの引用だけを…対象から外した」に直した（レビュー1回目の直しに合わせる）。取り込み後も `node --test 'workers/ask/test/*.test.mjs'` 226 件通過、`python3 -m unittest discover -s scripts/tests` OK。

## 報告

- 状態: 判断待ち / 続き: CHAT-1123-AIC-54
- ブランチ: work/1117-aic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1117-AIC-53.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1117-aic
- 確認用URL: なし（文書とテストだけ。`workers/ask/src/` は変えていない）
- マージ: 済（122f9e4a8。`git push origin work/1117-aic:cloudflare`、直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` が真）
- issue: #654（open。着手と結果のコメント: https://github.com/retroeater/mj/issues/654#issuecomment-6949212553 ）
- 判断が必要なこと:
  - Claude に渡す2回目の指示の共通の一文（`workers/ask/src/ask.mjs` の `answerSystem()` の tie の行）に、実データの数・名前の例「4回が最多で、3人が並んでいます（大和さん・覚野陽生さん・角谷陽介さん）」「2位は10回で、3人が並んでいます（客野直さん・山脇千文美さん・末続ヒロトシさん）」が残る。指示どおり src は変えず、この2つの引用だけを新しい検査（`workers/ask/test/prompt-examples.test.mjs`）の対象から外した。直すか（例を「N回」「〇〇さん」の形にする・プログラムが文を作る形に移す）は週次の確認で決める
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ab69c2f7）: https://github.com/retroeater/mj-logs/tree/main/guide/ab69c2f7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab69c2f7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
