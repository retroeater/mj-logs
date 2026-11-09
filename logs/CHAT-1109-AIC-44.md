# CHAT-1109-AIC-44

- 着手日時: 2026-11-09（JST）
- 対象issue: #654
- ブランチ: work/1109-aic
- 着手時HEAD: 6afa6323c

## 指示

【Claude作成】Claude Code 向け指示：（AIC 系列の振り返り、その2）質問欄の運用の学びを docs/notes/ask-worker.md に1節として足す。あわせて、事実（数・期・順位・回数）を今も Claude に書かせている箇所を棚卸しして、その節に一覧で書く。docs のみ
Chat-Ref: CHAT-1109-AIC-44
マージ: ドキュメントのみの変更なので、完了を報告のうえ cloudflare へマージしてよい（CLAUDE.md「ブランチ運用」）
貼る時機: CHAT-1109-AIC-43 のマージの後（同じ作業ブランチを使う。AIC-43 のログのマージが「済」になっていること）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1109-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1109-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1109-AIC-43 のマージのコミットが origin/cloudflare の祖先であることを確かめる（されていなければ止まる）。AIC-43 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1109-AIC-44` を足す（AIC-43 の「判断が必要なこと」が残っていれば「CHAT-1109-AIC-44 の時点でチャット側の回答なし」と書く）。識別子 `AIC` はこのチャットの系列（AIC-01〜43 で使用。AIC-17・24・31 は欠番）。

## 目的

AIC 系列（質問欄、#654）の 2026-11-03〜09 の指示（AIC-21〜42）の振り返りのうち、規則でない学びを、次の AIC のチャットと Code が読む docs/notes/ask-worker.md に残す。規則にする2点は CHAT-1109-AIC-43 でガイド文に入れた。

### 決定（2026-11-09、平野さん）

- 振り返りとそのレビューを、2本の指示に分けて記録する（規則にするもの＝AIC-43、規則でない学び＝この指示）
- 持ち越しの一覧（週次の書き出しで実際に聞かれたら直す候補）は、2週続けて実際に聞かれなければ一覧から外し、#654 にコメントで残すだけにする（Fable のレビューの案。平野さんが2本に分ける案として受けた）

### 前提（チャット側。平野さんの決定ではない）

- 足す節（例「## 運用の学び（2026-11-09、AIC-21〜42 の振り返り）」）に書くこと
  1. 同じ所で Claude の答えが揺れたら（目安は2回）、指示の言葉を足すのでなく、プログラムが文を作る形に移す。言葉を足して止まらなかった例: 「成績の行」（AIC-21〜23）、「前期・後期」（AIC-29・35）、「通算」（AIC-29）、「座る」（AIC-31〜33）、「検索で43件」（AIC-40）。プログラムの形で止まった例: タイトルの文（AIC-34 の `withTitle()`）、予定の答え（AIC-37 の `scheduleAnswer()`）
  2. 事実（数・期・順位・回数）を今も Claude に書かせている箇所の一覧（下の手順1で棚卸しする）。次の直しの候補として、実際に聞かれた問いで揺れた順に移す
  3. 答えを変える案を、指示文の「第一の案」にしない（AIC-21 の期の数え方、AIC-41 の「絞って1人ならその人で答える」が誤りの元になった。AIC-42 の、今の答えを変えない形で止まった）
  4. Claude の判断を作り物で決め打ちする検査は、判断した場合としなかった場合の両方を入れる（AIC-37 の検査が1回目を schedule に決め打ちしていて、王位戦の予定に答えられない誤りを見逃した。AIC-39 で埋めた）
  5. 直しの引き金: 2026-11-03〜09 の直しの大半は、こちらの本番の確かめの問いで見つかり、来訪者がまだ聞いていない問いのためだった。来訪者の問いから見つかったのは、内部の言葉の漏れ（「鮎川」「HIRO柴田」）と名字の候補（「原」）。2026-11-05 から、直す対象は週次の書き出しの実際の質問で決める
  6. 持ち越しの一覧の閉じ方（上の決定）
  7. 系列の受け持ち: 画面（`assets/top_ask.js`・`assets/nav.js`）は TOP 系列、Worker（`workers/ask/`）は AIC 系列。AIC の指示は画面を変えず、要るときは #654 にコメントして TOP 系列に回す（AIC-30・40 の形）。ask-worker.md に同じ趣旨の記述が今あれば、足さずに参照にする（要確認）
- 書き方: ask-worker.md の今の節の調子にそろえ、AIC の指示番号で出典を書く（ask-worker.md には Chat-Ref を書いてよい。CLAUDE.md・handover.md・chat-side-operations.md には書かない規則）。#654 への参照を添える
- 大きさ: ask-worker.md は約 92KB（docs/notes/ には上限なし）

## 手順

1. 棚卸し: `workers/ask/src/ask.mjs` の2回目の指示（`answerSystem()`・`ANSWER_LINES`・横断の `writeMany()`）と、2回目に渡す結果（`answerInput()` など）を読み、答えのうち事実（数・期・順位・回数・日付）を Claude が文にしている箇所を、型（player・league・champions・ranking・entry・live・final・year など）ごとに一覧にする。プログラムが文を作っている箇所（タイトルの文・予定・候補・案内・決勝だけ・期の外・迎えた期など）は「プログラム」として並べる。コードは変えない
2. 書く: 上の「前提」の1〜7 と、手順1の一覧を、ask-worker.md に1節として足す。持ち越しの一覧の閉じ方は、docs/decisions/houou.md にも上の「決定」として足す
3. マージ: ドキュメントのみなので、完了を報告のうえ cloudflare へマージする（CLAUDE.md「ブランチ運用」「マージするとき」）

## 止まる条件

- 足す内容が、ask-worker.md の今の記述（「5大会の応答ルール」など）と食い違う（食い違う箇所を書いて止まる。今の記述は変えない）
- 手順1で、コードを変えないと一覧が作れない
- 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-43 の「未確認の項目」の持ち越しの一覧は、この指示の「未確認の項目」にも写す
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-AIC-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1109-AIC-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `## 指示` 欄の末尾は指示文の最後の行と一致。同じ Chat-Ref のコミット・ログ: なし
- CHAT-1109-AIC-43 のマージ（6afa6323c）は origin/cloudflare の祖先。作業ブランチ: ローカルの work/1109-aic が origin/cloudflare と同じ（6afa6323c）なので `git merge --ff-only origin/cloudflare`（変化なし）で使った
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- この指示は規則でない学びを docs/notes/ask-worker.md に置くもので、ガイド文（CLAUDE.md・handover.md・chat-side-operations.md）に規則を足さない（「知見 → 選んだ層 → 理由」の表は要らない）
- AIC-43 のログの状態に「/ 続き: CHAT-1109-AIC-44」と「CHAT-1109-AIC-44 の時点でチャット側の回答なし」を足した

### 1. 棚卸し（コードは変えていない）

- 読んだもの: `workers/ask/src/ask.mjs` の `answerSystem()`（126行）・`ANSWER_LINES`（164行）・`answerInput()`（435行）・`tidyAnswer()`（477行）・`withTitle()`（490行）・`writeMany()`・`settle()`、プログラムの答えの関数（`candidatesAnswer()` 561行・`guideAnswer()` 597行・`finalsOnlyAnswer()` 614行・`entryHalfLead()` 636行・`beyond()` 649行）、`workers/ask/src/query.mjs` の `runQuery()` と型ごとの関数（champions 246行・final 299行・player 329行・league 374行・rankingQuery 415行・live 458行・entry 482行・yearlyPlayer 515行・yearMatches 539行）の items の形、`seatAnswer()`（34行）
- 一覧は ask-worker.md「運用の学び」の「答えの事実を誰が書くか」に書いた（Claude が文にしている事実を型ごとに9行、プログラムが文を作っている箇所を10行）

### 2. 書いたもの（1621d3011）

- docs/notes/ask-worker.md に「## 運用の学び（2026-11-09、AIC-21〜42 の振り返り、#654）」を「予定」の節の後に足した。前提の1〜7 と棚卸しの一覧
  - 7（系列の受け持ち）: ask-worker.md の「横断」の冒頭に「サイト全体で1つの質問欄（呼び出しの部品とトップは TOP 系列）の Worker 側」とあるが、「AIC の指示は画面を変えず、#654 にコメントして TOP 系列に回す」は書かれていなかったので、1行で足し、受け持ちは「横断」の冒頭を参照にした
- 今の記述との食い違い: 無い（「5大会の応答ルール」の表の行は、Claude に書かせる事実の歯止め〈`SEASONS_LINE`・`pointsLine()`・`FILTERED_LINE` など〉とプログラムの文〈`withTitle()` など〉を記述していて、一覧と同じ。「言葉を足すのでなく、プログラムの文に移す」は今ある歯止めを外すものではない）
- docs/decisions/houou.md に「2026-11-09（CHAT-1109-AIC-44）」（2本に分けて記録すること、持ち越しの一覧の閉じ方）を足した
- テスト: `python3 -m unittest discover -s scripts/tests` OK（docs のみ）

## 報告

- 状態: 判断待ち
- ブランチ: work/1109-aic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1109-AIC-44.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1109-aic
- 確認用URL: なし（docs のみ）
- マージ: 済（docs のみ、完了の報告のうえマージ）
- issue: なし（docs のみ。#654 にはコメントしていない）
- 判断が必要なこと:
  - 状態を判断待ちにしたのは、下の「未確認の項目」（持ち越しの一覧）が残るため。この指示について新たな判断は無い
  - 棚卸しで見えた次の直しの候補（実際に聞かれた問いで揺れた順に移す。今週の書き出しで揺れたのは player の要約〈「鮎川」「HIRO柴田」〉）: player の要約（どの期を挙げるか）、champions の人数・回数、ranking の同率、live の回数
- 未確認の項目:
  - 持ち越す言い回しと見送った問い（AIC-43 から写した。次の週次の書き出し〈2026-11-16〉で、実際に聞かれたか見る。2週続けて聞かれなければ一覧から外し、#654 にコメントで残す〈2026-11-09 の決定〉）:
    - AIC-19: 決勝だけの答えの「成績」「記録」の重なり、「WRCリーグ決勝の第10期に4位」の語順
    - AIC-29: 「4位の古川孝次さんから10位の杉浦勘介さんまでは残留です」（11〜13位も残留）
    - AIC-37: 予定の略称（「グランプリ」「日本シリーズ」）、title/ に無い大会と title/ の大会を両方書くと後者だけ、件名の「決勝進出者決定戦」が決勝になる、候補が最初の大会の名簿から
    - AIC-38: 予定の問いで過去・否定でも段を読む、「決勝進出者決定戦はいつ？」で1回目の stage を使う
    - AIC-39: 近道に入らない予定の言い回し（「王位戦、次はいつ？」「王位戦の次の放送は？」「王位戦っていつ？」など）、「直近」の過去の意味
    - AIC-40: 作り直しの JSON が null のとき、空白入り・漢数字の「検索で 43 件」
    - AIC-42: 名字だけの1文字の問い（「勝」）で、大会によって1回目が選手名の問いと読まない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fc46d17c）: https://github.com/retroeater/mj-logs/tree/main/guide/fc46d17c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b912fa9a.md
