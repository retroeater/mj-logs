# CHAT-1103-AIC-27

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: 5c53eb050

## 指示

【Claude作成】Claude Code 向け指示：（AIC-26 の続き）質問欄の答えの事実の誤り2つを直す（佐々木寿人の鳳凰戦を「鳳凰位にはなっていません」と書く、歴代最強位の「ほか10人」の年度を作って書く）。cloudflare へマージする
Chat-Ref: CHAT-1103-AIC-27
マージ: 承認済み（チャットで）。AIC 系列の例外（2026-11-02 の平野さんの決定）による。下の「止まる条件」のうち、次の3つを1つでも満たさなければマージしない
  - テストが通る
  - 本番の問いの答えがデータと一致する
  - 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない
貼る時機: いつでも（CHAT-1103-AIC-26 のマージ〈c129a98b9〉は済んでいる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。c129a98b9 が origin/cloudflare の祖先であることを確かめる。AIC-26 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1103-AIC-27` を足し、その「判断が必要なこと」3つへの扱い（下の「AIC-26 の判断の扱い」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜26 で使用。AIC-17・24 は欠番）。

## 目的

AIC-26 の本番の確かめで、2回目の Claude の書き方がデータと食い違った。事実の誤りなので即日直す。

1. 「佐々木寿人の成績は？」（横断、`context: "houou"`）の鳳凰戦の答えに「鳳凰位にはなっていません」。鳳凰位決定戦の優勝は第37・38・40期
2. 「歴代最強位は？」（横断、`context: "saikyo"`）の答えに「ほか10人…2013年度以降に1回ずつ」。10人のうち板川和俊は2011年度、水沼利晃は2012年度。結果に無い年度を作って書いた

### 決定（2026-11-02、平野さん。この指示での当てはめはチャット側）

- 事実の誤りは即日直す。AIC 系列のマージの例外（冒頭の「マージ:」の行）

### AIC-26 の判断の扱い（チャット側が上の決定に当てはめた。平野さんの個別の決定ではない）

- 1つ目（本番の2問の食い違い）: 事実の誤りなので、この指示で直す
- 2つ目（`top_ask.js` の要求の形）: 確かめた結果として受けた。判断は要らない
- 3つ目（画面の統合の前提）: PRP（`work/1101-prp-pub`）と TOP-07（`work/1103-top`）の両方のマージの後に、画面の統合の指示を書く（2026-11-03 の決定どおり）

### 前提（チャット側。平野さんの決定ではない）

チャット側が 2026-11-03 18:50 JST ごろ、origin/cloudflare（5c53eb050）で開いて確かめた。着手時に今のコードで開き直す。

- 1（鳳凰位の期）
  - `workers/ask/data.json` で、佐々木寿人が `finals` の順位1にいる期は第37・38・40期。`players` の league が「鳳凰位」の行は第38・39・41期（前の期に鳳凰位になって座っている期）
  - `workers/ask/src/query.mjs` の `player()` は、items に league「鳳凰位」の行があると注記 `seatNote()`（「成績の「鳳凰位」は、前の期に鳳凰位を獲って座っている期です」）を付ける。鳳凰位になった期（決勝の優勝）は結果に入っていない
  - 原因の見立て: 鳳凰位になった期が結果に無く、Claude が座っている期の行と注記から、鳳凰位になったかを読めなかった
  - 直し方の案（実物に合わせて選んでよい。選んだ方と理由を報告に書く）
    - (a) `player()` の結果に、決勝で優勝した期（例 `titleTerms: [37, 38, 40]`）をプログラムが付ける。2回目の指示（`ANSWER_LINES`）に「タイトル（鳳凰位など）になった期は titleTerms を書く。titleTerms が空でなければ『なっていない』と書かない」を足す
    - (b) 指示の1行だけ（「league が鳳凰位の期の前の期に鳳凰位になった。なっていないと書かない」）
    - (a) を第一の案とする（数と期はプログラムが出す、という今の答えの規則と同じ考え）
  - 女流桜花など、座っている期の行（`seatNote()`）を持つほかの大会にも同じ直しを入れる。WRC リーグ・WRC-R・最強戦の player にタイトルの期を足すかは、データで確かめて決める
  - 大会別の欄と横断の両方の2回目に効くこと（`ANSWER_LINES` は両方で使われる。AIC-21 で確かめた）
- 2（歴代最強位の「ほか10人」）
  - `workers/ask/src/ask.mjs`
    - 393〜415行あたり: 2回目に渡す結果は、選手が `NAMES_MAX` 人を超えると上位だけを渡し、残りの人数を `others` で添える
    - 162行（最強戦の `ANSWER_LINES`）: 「others は…一覧や人数を聞かれたとき(「ほか N人」)のほかは書かない」
    - 127行: 「『ほか N人』…は、一覧や人数を聞かれたときだけ添える」
  - Claude は「ほか10人」の年度・回数を、渡されていないのに書いた
  - 直し方の案: 「others の人については、人数(ほか N人)のほかに、年度・期・回数・名前を書かない(結果に無い)」を、5大会に共通の答えの規則（127行あたり）に足す。最強戦の行だけでなく共通にするか、最強戦だけにするかは、ほかの大会の `champions`・`entry` の others で同じことが起こりうるかを見て決める
- 答えが変わってよい問い
  - 選手の成績のうち、タイトル（鳳凰位・女流桜花など）になったかに触れる答え（大会別と横断の両方）
  - `others` のある一覧の答えのうち、others の人の年度・回数・名前に触れる答え
- 選手の役職などの人の属性は、ログ・テストに書かない（データの名前と期・年度だけ）
- 本番の確かめは1日10問まで（同じ接続元から）。上限に当たったら、送れなかった問いを「未確認の項目」に書く

## 手順

1. 直す: 上の2点を今のコードで確かめてから直す。docs/notes/ask-worker.md「5大会の応答ルール」の該当の行を直し、docs/decisions/houou.md に上の「AIC-26 の判断の扱い」を足す
2. テスト
   - 1: 佐々木寿人（鳳凰戦）の player の結果に、タイトルの期（37・38・40）が入る（(a) のとき）。2回目の指示（大会別と横断）に規則が入る。座っている期の行を持つほかの大会でも同じ
   - 2: 2回目の指示に others の規則が入る（足した大会すべて）
   - 「答えが変わってよい問い」の外で、今のテスト（AIC-08〜26）が変わらないことを確かめる
   - 変わる範囲の比較（AIC-21〜26 の scratchpad の比べ方）で、2回目に渡す結果と指示の違いが、足したものだけであることを確かめる
   - 直す前のコードで新しいテストが落ちることを確かめる
   - `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
   - 新しい文脈のサブエージェントにレビューさせる（CLAUDE.md「判断・作業の原則」）
3. マージと本番
   - cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   - 本番に送る問いは次の3つ（`Origin: https://ryoei.pro`）。答えがデータと一致するかを書く
     - 横断（`contest: "all"`, `context: "houou"`）の「佐々木寿人の成績は？」（鳳凰位になった期が正しいか）
     - 横断（`contest: "all"`, `context: "saikyo"`）の「歴代最強位は？」（others の年度を書いていないか）
     - ask-worker.md「本番で送る固定の問い」から1問（大会別の要求のまま）
   - #654 に結果をコメントする

## 止まる条件

- テストが通らない、本番の問いの答えがデータと一致しない、「答えが変わってよい問い」の外で今答えられている問いの答えが変わる（どれか1つでもマージしない）
- 原因が見立てと違い、`player()` の結果・2回目に渡す結果・`ANSWER_LINES`・答えの規則の外を変えないと直らない（直さずに原因を書いて止まる）
- 未マージの work/ ブランチが `workers/ask/src/` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- #654 に他セッションの着手中コメントがある
- 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-26 の「未確認の項目」4つは、この指示の「未確認の項目」にも写す
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 指示欄の末尾は指示文の最後の行と一致。c129a98b9 は origin/cloudflare の祖先。origin/work/1103-aic はマージ済みで、ローカルの work/1103-aic は origin/cloudflare（5c53eb050）と同じ。#654 に他セッションの着手中コメントなし（着手中をコメント、issuecomment-6600375911）。未マージの work/ ブランチで `workers/ask/src/` を変えているものなし。雛形の行は4つともある。AIC-26 のログの状態に「/ 続き: CHAT-1103-AIC-27」と扱い3つを書き足した
- 前提の確かめ: 佐々木寿人の鳳凰戦の決勝の順位1は第37・38・40期、league「鳳凰位」の行は第38・39・41期、`player()` の結果に優勝の期は無く注記 `seatNote()` だけ（前提どおり）。2回目に渡す結果の others と、127行・最強戦の行の others の規則も前提どおり
- 座っている期の行と優勝の期（データ）: 座っている期の行（league が大会の top）を持つのは鳳凰戦（14人）と女流桜花（top「桜花」、10人）。WRC リーグ・WRC-R は座っている期の行は無いが、player の結果に優勝の期が無いのは同じ（優勝者16人のうち15人、7人のうち6人に成績の行がある）。最強戦は player の行の result に「最強位」、totals に最強位の回数があるので足さない
- 直し（bb36a8a88）:
  - 1: 候補 (a)。`player()` が決勝の順位1から優勝した期 `titleTerms` を付ける（期で絞ればその期、優勝が無ければ付けない。無い選手に空を渡すと「なっていない」と書かせうるため）。`answerInput()` が渡す。`ANSWER_LINES` の鳳凰戦・女流桜花・WRC リーグ・WRC-R に `titleLine()` の一文「- player の titleTerms は<鳳凰位になった期など>(決勝で優勝した期)。<…>期と回数は titleTerms から書き、titleTerms があるのに<…なっていない>と書かない」、鳳凰戦・女流桜花には「items の league が<鳳凰位・桜花>の期は、前の期に<…>になって座っている期」を足した（champions の一文は最後のまま）
  - 2: 5大会共通の `answerSystem()` に「- others の人については人数だけを書き、結果(items・byTimes・tie)に無い年度・期・回数・名前を書かない」を足した。others は champions・entry・ranking など5大会のどの一覧にも付くので共通にした（「ほか N人」の語を2行目に書くと、その語の行が1つであることを見るテストに当たるので、語を変えた）
- テスト（count.test.mjs に2本）: titleTerms（佐々木寿人 [37, 38, 40]、第39期で絞ると無し、第40期で [40]、女流桜花・WRC・WRC-R の優勝者も決勝から、優勝の無い選手には付かない）と、大会別・横断の2回目に titleTerms と一文が入る（最強戦には入らない）／others の一文が5大会の欄と横断に1回入る。直す前のコードで2本とも落ちる。直した後は node 154本・python 1747本 通過
- 変わる範囲の比較（in21.mjs、直す前と後）: 応答・記録は全部同じ。2回目に渡す結果が変わるのは、優勝のある選手の player だけ（鳳凰戦4・女流桜花2・WRC 2・WRC-R 2・横断10 の通り。titleTerms が付く）。2回目の指示（system）は2回目を呼ぶ問いすべてで変わる（共通の一文。鳳凰戦・女流桜花・WRC・WRC-R は titleTerms の一文も）
- docs: ask-worker.md「5大会の応答ルール」の表に2行を足した（65d04c110）。docs/decisions/houou.md に 2026-11-03（CHAT-1103-AIC-27）の節

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 28ad5e2a）: https://github.com/retroeater/mj-logs/tree/main/guide/28ad5e2a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/28ad5e2a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
