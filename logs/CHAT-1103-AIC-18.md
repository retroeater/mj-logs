# CHAT-1103-AIC-18

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: c1bc06de

## 指示

【Claude作成】Claude Code 向け指示：（AIC-16 の続き）質問欄の案内を2つ直す（決勝だけに記録がある選手を「この大会の記録にいません」と答える誤り、連盟の外の選手を最強戦の個人成績ではなくトップへ送る点）。cloudflare へマージする Chat-Ref: CHAT-1103-AIC-18 マージ: 承認済み（チャットで）。AIC 系列の例外（2026-11-02 の平野さんの決定）による。下の「止まる条件」のうち、次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1102-AIC-16 のマージ〈f520341f5〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。f520341f5 が origin/cloudflare の祖先であることを確かめる。AIC-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-AIC-18` を足し、その「判断が必要なこと」2つへの回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜16 で使用。AIC-17 は送る前に差し替えたため欠番）。

目的
質問欄の「ほかの大会への案内」（AIC-08・16）を2つ直す。

1. 鳳凰戦の質問欄で「森山茂和」の成績を聞くと、「鳳凰戦の記録に「森山茂和」に当たる選手はいません」と答える。実際には、鳳凰戦の決勝（第2・4・7期）に記録がある（事実の誤り）
2. 連盟の外の選手（TAKUYA など）は最強戦の個人成績に載っているのに、案内の行き先が「麻雀最強戦のトップ」止まりになっている

AIC-17 は送る前に差し替えたため欠番。
決定（2026-11-02〜03、平野さん）

* 直しの時機: 事実の誤りは即日、言い回しは週次（2026-11-02）
* AIC 系列のマージの例外（冒頭の「マージ:」の行、2026-11-02）
* AIC-16 の「判断が必要なこと」への回答（2026-11-03）: 2つともすぐ直す（この指示）

前提（チャット側。平野さんの決定ではない）
チャット側が 2026-11-03 10:45〜10:50 JST に、origin/cloudflare（11e7c6551）で開いて確かめた。

* 1（決勝だけの人）
   * `workers/ask/data.json`
      * `finals` のうち、第2・4・7期に「森山茂和」がいる
      * `players` には「森山茂和」が無い（データの中の出現は4回で、決勝の3期と、ほかに1か所）
   * `workers/ask/src/query.mjs` の130〜138行 `recordNames()`
      * 記録の有無を `data.players[name]` か `data.finals` の順位で判定している（決勝だけの人も「記録あり」になる）
   * `workers/ask/src/ask.mjs` の597〜603行
      * `player` などの型で `not_found` のとき、`guideAnswer(deps, data, name)` を呼ぶ
   * `workers/ask/src/ask.mjs` の446〜469行 `guideAnswer()`
      * 案内する先は、その大会を除いた大会だけ（`d !== data`）
      * 答えの文は、ほかの大会に記録があれば「${here.name}の記録に「${written}」に当たる選手はいません。記録があるのは…」の形
      * その大会の決勝の記録の有無を見ずに「いません」と書いている。これが誤りの原因（見立て）
   * 直し方の候補（実物に合わせて選んでよい。選んだ方と理由を報告に書く）
      * (a) `not_found` のとき、その大会の `recordNames()` に当たれば、「リーグの成績の記録はありませんが、決勝に第N期の記録があります」と、その大会の決勝の記録で答える（`final`・`champions` の検索の結果を使う）
      * (b) (a) に加え、ほかの大会の記録があれば、その案内も添える
   * 同じ形が、女流桜花・WRC リーグ・WRC-R・最強戦にもあるか（決勝だけに出た人）を data で確かめ、同じ直しを5大会に入れる
* 2（連盟の外の選手）
   * `guideAnswer()` の464〜465行
      * 出典のリンクは、`names.length === 1 && d.people[names[0]]` のときだけ個人成績（`${d.urls.players}?name=…`）
      * それ以外は大会のトップ
   * `workers/ask/data-saikyo.json`
      * `players` にあって `people`（連盟の在籍者）に無い人が283人いる（JOKER・TAKUYA・ZERO など）
      * `urls.players` は `/saikyo/players/`
   * `assets/saikyo.js` の262行は `const NAME_PARAM = 'name'`
      * 最強戦の個人成績のページは `?name=` で選手を開く（在籍者に限るかは要確認）
   * 直し方の案: リンクの条件を「`d.people` にある」から「その大会の個人成績のページで開ける（`d.players` にある）」に広げる
      * ほかの4大会でも `players` と `people` の関係が同じか確かめる
      * 開けない大会は今のままにする
* 選手の役職などの人の属性は、ログ・テストに書かない（データの名前と期だけ）
* 本番の確かめは1日10問まで（同じ接続元から）

手順

1. 直す: 1・2を今のコードとデータで再現してから直す。docs/notes/ask-worker.md の該当の行を直し、docs/decisions/houou.md に上の「決定」を足す
2. テスト:
   * 1: 各大会で決勝だけに出た人を data から1人ずつ選び（いれば）、その大会の質問欄で聞いたときに、決勝の記録で答えることを確かめる
   * 2: 連盟の外の最強戦の選手（TAKUYA など）を鳳凰戦の質問欄で聞いたときに、出典が最強戦の個人成績（`/saikyo/players/?name=…`）になることを確かめる
   * AIC-08・16 の案内の答え（「佐々木寿人」を jwrc/ で聞く、「萩原聖人」を houou/ で聞く）が変わらないことを確かめる
   * 直す前のコードで新しいテストが落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
3. マージと本番:
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の4つ。答えと出典のリンクがデータと一致するかを書く
      * houou/ の「森山茂和の成績は？」
      * houou/ の「TAKUYA」
      * jwrc/ の「佐々木寿人」
      * ask-worker.md「本番で送る固定の問い」から数問
   * 2 のリンクは、本番の `/saikyo/players/?name=TAKUYA` を開いてその選手が出るかを、HTML/JS の取得で確かめられる範囲で書く
   * #654 に結果をコメントする

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、今答えられている問いの答えが変わる（どれか1つでもマージしない）
* 1 の原因が見立てと違い、直すと `guideAnswer()` の外の答えも変わる（1 だけ外して原因を書き、2 は進めてよい）
* 2 で、最強戦の個人成績のページが連盟の外の選手を `?name=` で開けないと分かった（2 だけ外して報告する。1 は進めてよい）
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs`・`query.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-16 の「未確認の項目」2つは、この指示の「未確認の項目」にも写す
   * 2026-11-09（月）03:00 JST の書き出しと「現女流桜花」の引数
   * 地域で断られた問いの記録
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c1bc06de）: https://github.com/retroeater/mj-logs/tree/main/guide/c1bc06de

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c1bc06de/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c1bc06de.md
