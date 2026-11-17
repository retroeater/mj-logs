# CHAT-1117-AIC-52

- 着手日時: 2026-11-17（JST）
- 対象issue: #654
- ブランチ: work/1117-aic
- 着手時HEAD: aeaf0fb52

## 指示

【Claude作成】Claude Code 向け指示：（質問欄の整理）Worker のテストのうち大会別の要求を使うものを横断＋context に書き直し、大会別の要求を Worker から外す。AIC-50 で見送ったテストの提案2件も直す。cloudflare へマージする
Chat-Ref: CHAT-1117-AIC-52
マージ: 承認済み（チャットで。2026-11-17、平野さん）。次の3つを1つでも満たさなければマージしない。マージ後の本番の確かめで3つ目を満たさなければ、戻す指示を待たずにこの指示のマージを revert して cloudflare へ入れ、止まる
* テストが通る
* 外す前のテストが確かめていた中身（流れ・指示・記録の型・答えの文）を、書き直した後のテストも確かめている（対応表をログに書く）
* マージ後の本番の問いの答えがデータと一致する
貼る時機: いつでも（CHAT-1117-AIC-51 のマージ〈261e00317〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1117-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1117-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。261e00317 が origin/cloudflare の祖先であることを確かめる。識別子 `AIC` はこのチャットの系列（AIC-01〜51 で使用。AIC-17・24・31 は欠番）。

## 目的

質問欄は横断（`contest: "all"`）にまとめた。大会別の要求は古い JS のために Worker に残していたが、週次の記録で `all:` で始まらない型の行は 2026-11-02〜08 と 11-09〜15 の2週続けて0行。画面は `assets/nav.js` の `'all'` だけを送っている（AIC-47 の報告）。AIC-47 は、Worker のテスト79件（ask 24・cross 14・count 10・week3 7 ほか）が大会別の要求で1つの大会の流れ・指示・記録の型を確かめているため、外すのを見送った。

### 決定（2026-11-16〜17、平野さん）

* Worker のテストを横断＋context に書き直して、大会別の要求を外す（2026-11-16、AIC-47 の「判断が必要なこと」への回答）
* AIC-50 で見送ったテストの書き方の提案2件（`player.test.mjs` のコメントの括弧書きの食い違い、seasons 0 の検査を全員に広げる。#654 に移したもの）は、この指示で直す（2026-11-17）
* この指示は、上の「マージ:」の行の条件でマージしてよい。マージ後に本番で外れたら Code がその場で戻す

### 前提（チャット側。平野さんの決定ではない）

* 外した後に `contest` が `all` でない要求が届いたときの扱いの案: その大会を context とした横断として答える（古い JS が残っていても答えは返る）。400 で断る形にするなら理由をログに書く。どちらにしても、記録の型は `all:` で始まるようにする
* 外す範囲: 大会別の要求だけが使う入口・分岐・指示・関数。横断の中で各大会の答えを作るのに使っている関数は残す（どれが大会別だけかは（要確認）。開いた関数名と行番号をログに書く）
* テストは件数を合わせるのではなく、確かめていた中身（各大会の流れ・指示・記録の型・答えの文）を横断＋context で確かめる形に書き直す。大会別の要求でしか確かめられない中身があれば、書き直せない理由と一緒に止まる
* 画面のファイル（assets/、TOP 系列の受け持ち）は変えない
* docs/notes/ask-worker.md「横断」（「大会別の要求は古い JS のため残す」の行）と、記録の型の説明を直す。docs/decisions/houou.md に上の「決定」を足す
* 答えが変わってよい問い: `contest` が `all` でない要求（上の扱いの案のとおりに変わる）。横断の要求の答えは変わらない
* 本番の確かめは1日10問まで（同じ接続元から）

## 手順

1. 実物で確かめる: 大会別の要求の入口・分岐と、それを使うテスト（ファイルごとの件数）を開いて確かめ、上の前提と食い違えば止まる。`git branch -r --no-merged origin/cloudflare` で、未マージの work/ ブランチが `workers/ask/` の同じ関数・同じテストを変えていないか確かめる
2. 書き直す: テストを横断＋context に書き直す（外す前と後の対応表をログに書く）。提案2件を直す。大会別の要求を外す。テストが通り、対応表で漏れが無ければ cloudflare へマージする
3. 本番の確かめ（Workers Builds の成功を確かめてから。`Origin: https://ryoei.pro`、UA に `mj-internal`）: 横断・context 無しで「hiro」「白鳥翔の第40期の成績は？」、横断・context 鳳凰戦で「今期の首位は？」、`contest: "houou"`（大会別）で「藤崎」の4問。答えがデータと一致するか、大会別の要求が上の扱いのとおりになるかを書く。外れたら、この指示のマージを revert して cloudflare へ入れ、Workers Builds の成功を確かめてから止まる。結果を #654 にコメントする（判定だけ）

## 止まる条件

* 前提（入口・分岐・テストの件数）が実物と食い違う
* 大会別の要求でしか確かめられない中身があり、書き直せない
* 未マージの work/ ブランチが `workers/ask/` の同じ関数・同じテストを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 画面のファイル（assets/）を変えないと外せない
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の確かめで外れた（revert して止まる。上の手順3）

## 完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、2026-11-23 の書き出し（確認は 6:00 JST）で `all:` で始まらない型の行が0であることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1117-AIC-52.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1117-AIC-52 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（0章）

- ログの「指示」欄の末尾は指示文の最後の行と一致。261e00317 は origin/cloudflare の祖先
- 同じ Chat-Ref のコミット・ログ: なし。雛形の行: 欠けなし
- 作業ブランチ: origin/work/1117-aic もローカルも無かったので `git checkout -b work/1117-aic origin/cloudflare`（着手時 HEAD aeaf0fb52）
- #654: open。着手コメントを残した

### 実物の確認（手順1）

- 大会別の要求の入口・分岐（着手時の `workers/ask/src/ask.mjs`）: `ask()`（886行）の `body.contest` の分岐（902〜909行。省略は鳳凰戦、`deps.contests` の id は大会別、`all` は横断）と、大会別だけの流れ（938〜950行。`settle()` → `finish()` → `record(…, recordType(c, …))`）。`recordType()`（849行）と、`record()` の既定の引数 `argsText()` は大会別だけが使う。`settle()`・`finish()`・`write()`・`writeMany()` などは横断の各大会でも使うので残す
- 大会別の要求を使うテスト: AIC-47 で測ったとおり、外すと 79件が落ちる。「その大会を context とした横断」として扱う形にすると 36件が落ちる（ファイルごと: ask 8・count 9・cross 5・context 3・ouka 2・week3 2・wrcr 2・all 1・jwrc 1・saikyo 1・schedule 1、ほか）。前提と食い違いは無い
- 画面: `assets/` で `/api/ask` を送るのは `assets/top_ask.js` だけで、`contest` は `assets/nav.js` の `'all'` だけ（AIC-47 で確かめたまま）。画面は変えない
- 未マージの work/ ブランチで `workers/ask/` を変えているもの: なし

### 外し方（手順2）

- `contest` が `all` でない要求は **その大会を context とした横断として答える**（指示文の案のとおり）。400 で断る形は採らなかった（古い JS・キャッシュの残ったページから届いても答えを返すため）。`contest` の省略は鳳凰戦、大会の id は5大会のどれか（`contestData()`。`"houou"` も受けるようになった。前は 400）。知らない大会・`__proto__` などは今までどおり 400 で数えない
- 外したもの: `ask()` の大会別の流れ（`settle()` → `finish()` → 記録・応答）、`recordType()`、`argsText()` と `record()` の `request` の引数（すべての呼び手が引数の JSON を渡すようになり使われない）。記録の型はすべて `all:` で始まる
- 横断の要求の答えは変わらない（`crossAsk()`・`contextAsk()` は変えていない）

### テストの書き直し

- 要求の本文: 新しい `workers/ask/test/cross-body.mjs` の `crossBody()` で、`contest` が `all` でない本文を `{ contest: "all", context: <大会の id、省略は houou> }` にして送る（all・ask・context・count・cross・jwrc・ouka・saikyo・schedule・values・week3・wrcr の `post()`）
- 1つの大会の流れ（1回目・2回目の指示と入力、決勝だけの答え、ほかの大会への案内）を確かめるテストは、問いの文に記録のある選手名（3文字以上）を書かない形にした（名前を書くと横断が記録のある大会を並べ、context の大会の流れにならないため。名前は1回目の引数で渡す）。count.test.mjs は `post()` で問いの文頭の名前を「この選手」に置き換え、cross・week3 は該当の問いの文を直した
- 大会別の要求そのもの（古い JS の形）は、`crossBody()` を通さない要求で確かめる（下の表の互換の行）

外す前と後の対応表（外す前に落ちた36件と、互換の検査）:

| ファイル・テスト（外す前の名前） | 外す前に確かめていた中身 | 書き直した後の確かめ |
|---|---|---|
| ask: 記録には日時・質問文・結果・型・答えの本文・型の引数が残り、IP は残らない | 記録の項目・型・引数、IP を残さない | 同じ（型 `all:houou:champions`・引数 `{"houou":{"entry":23}}`。項目・IP の検査は同じ） |
| ask: 答えられなかったときの記録 | none・データに無い期・Claude の失敗の記録（答え・引数・「失敗: 」） | 同じ（型・引数を `all:houou:…`・`{"houou":…}` の形に） |
| ask: 型に当たらない質問は「答えられません」 | 2回目を呼ばない・記録・数える | 同じ（型 `all:houou:`） |
| ask: どの大会にも記録の無い名前 | 見つからないと分かる文・記録 | 同じ（型 `all:houou:player`） |
| ask: 名字だけ「本田」「瀬戸」「堀」「松澤」で候補（4件） | 候補の文・2回目を呼ばない・記録 | 同じ（型 `all:houou:player`） |
| context: 5大会の context で大会名も選手名も無い問いは大会別の欄と同じ応答 | 大会別と横断＋context の応答・Claude への入力・記録が同じ | 大会別の要求（古い形）と横断＋context の応答・Claude への入力・記録が全項目で同じ、context の大会だけ・大会名の見出しなし・Claude は2回まで・1回目は context の大会だけ・型 `all:<大会>:` |
| context: context の大会の名前だけがある問いも大会別の欄と同じ | 同上（女流桜花） | 同上 |
| context: context が無いときは5大会。大会別の要求は contests を付けない | 5大会の横断、大会別の応答の形 | 5大会の横断は同じ。大会別の要求（contest の省略）は鳳凰戦を context とした横断で contests `['houou']`・型 `all:houou:champions` |
| count: 大会別の欄の「佐々木寿人の成績は？」の seasons と指示（5大会） | 2回目に seasons が渡る・player の指示の一文 | 同じ（問いの文の名前を外し、context の大会の2回目を見る） |
| count: 「分からない」と書かない一文（5大会の欄と横断） | 5大会の2回目の指示と横断の指示 | 同じ（同上） |
| count: 最強戦の totals の一文、鳳凰戦は「成績の行」を渡さない | 最強戦・鳳凰戦・ほか3大会の2回目の指示 | 同じ（同上） |
| count: titleTerms をプログラムが付け2回目に渡さない（4大会） | 2回目の入力・指示 | 同じ（同上） |
| count: others の一文（5大会の欄と横断に1回） | 指示の一文の回数 | 同じ（同上） |
| count: points として渡る（4大会）・最強戦は変えない | 2回目の入力・指示 | 同じ（同上） |
| count: titleSentence を渡さず、座っている期の行と注記も渡さない | 2回目の入力 | 同じ（同上） |
| count: 迎えた期だけの問いはプログラムの文 | 答え・2回目を呼ばない・記録 | 同じ（型 `all:houou:player`） |
| count: タイトルの文を最後に付ける | 答えの文（大会別・横断） | 同じ（大会別の要求の分は問いの文の名前を外す） |
| count: 内部の言葉の確かめ（大会別の欄も横断も作り直す） | 語の一覧・作り直しの回数・答え | 同じ |
| cross: 女流桜花の「菅原」の候補 | 候補の文・記録 | 同じ（型 `all:ouka:player`） |
| cross: WRC リーグの「佐々木寿人」は決勝の記録で答え、記録のある大会を添える | 決勝だけの答えの文・出典・1回だけ・記録 | 同じ（問いの文を「寿人」に、型 `all:jwrc:player`） |
| cross: 決勝にだけ記録がある選手（5大会の全員と森山茂和・「寿人」） | 決勝の期・順位の文・出典・記録・人数 | 同じ（問いの文に名前を書かない） |
| cross: 最強戦にだけ記録のある萩原聖人を鳳凰戦の欄で聞くと案内 | 案内の文・出典・記録（guided） | 同じ（問いの文を「萩原の成績は？」に） |
| cross: 連盟の外の TAKUYA を鳳凰戦の欄で聞くと出典は最強戦の個人成績 | 案内の文・出典 | 同じ（問いの文に名前を書かない） |
| jwrc・ouka・wrcr・saikyo: 問いの contest がその大会なら、その大会の指示・データで答え、記録の型に大会を付ける（4件） | 1回目・2回目の指示・データ・記録の型 | 同じ（context をその大会に、型 `all:<大会>:<型>`） |
| ouka: 知らない大会は受けない。contest が無ければ鳳凰戦 | 400・数えない・鳳凰戦の指示・記録 | 同じ（型 `all:houou:`） |
| wrcr: 「現タイトルホルダーは？」の1回目の指示と champions | 指示・答えの材料・記録 | 同じ（型 `all:wrcr:champions`） |
| week3: データに無い期の選手の成績（女流桜花の「22期の和久津晶成績」） | 冒頭の文・渡す items・記録 | 同じ（問いの文を「22期の和久津成績」に、型・引数を `all:ouka:player`・`{"ouka":…}`） |
| week3: ハングル・英語の league で1回だけ作り直す | 作り直しの回数・答え | 同じ（問いの文を「和久津の成績」「HIROの成績」に） |
| schedule: 大会別の要求には予定の型を足さない | 大会別の要求の1回目の指示 | **中身を置き換えた**: 大会別の要求も横断＋context と同じ1回目（予定の型を足す）になる。大会別の要求で予定を足さない扱いは、外したことで無くなった（答えが変わってよい問い） |
| all: 横断の要求の検査（context は5大会の id だけ。大会別の要求は今のまま） | context の検査（400）、大会別の要求の応答 | context の検査は同じ。大会別の要求（`contest: "ouka"`・`"houou"`）は context とした横断で contests・型 `all:` になり、知らない大会（`__proto__`）は 400 で Claude を呼ばない |

- 書き直したテストを外す前のコード（origin/cloudflare の src）で回すと、互換の検査の5件（all の横断の要求の検査、context の3件、schedule の1件）が落ち、ほかは通る（横断＋context の要求の中身は外す前と同じことの確かめ。count の2件と画面の1件は写しをリポジトリの外に置いたための失敗）
- AIC-50 の提案2件: `player.test.mjs` のコメントを「得点も順位も無い行は直近に挙げない」に、seasons 0 の検査を全員（今は鳳凰戦の4人）を回す形にした
- `node --test 'workers/ask/test/*.test.mjs'` 225件すべて通過、`python3 -m unittest discover -s scripts/tests` OK
- 文書: docs/notes/ask-worker.md の冒頭の注・「横断」・記録・予定の行を直し、docs/decisions/houou.md に決定を足した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c63f68d8）: https://github.com/retroeater/mj-logs/tree/main/guide/c63f68d8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c63f68d8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2e4dd08c.md
