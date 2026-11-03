# CHAT-1103-AIC-21

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: e4049b271

## 指示

【Claude作成】Claude Code 向け指示：（AIC-20 の続き）質問欄が選手の成績の行の数を「N期分」と答える誤り（佐々木寿人の鳳凰戦は20期分を「28期分」）を直し、cloudflare へマージする Chat-Ref: CHAT-1103-AIC-21 マージ: 承認済み（チャットで）。AIC 系列の例外（2026-11-02 の平野さんの決定）による。下の「止まる条件」のうち、次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1103-AIC-20 のマージ〈93f8976cc〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。93f8976cc が origin/cloudflare の祖先であることを確かめる。AIC-20 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-AIC-21` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜20 で使用。AIC-17 は欠番）。

目的
AIC-20 の本番の確かめで、横断の問い「佐々木寿人の成績は？」の答えが、鳳凰戦の成績を「28期分」と書いた。データでは28行・20期（前期・後期を別の行に持つため）。事実の誤りなので即日直す。大会別の欄（houou/）でも同じ誤りが出る見込み。
決定（2026-11-03、平野さん）

* AIC-20 の「判断が必要なこと」への回答
   * 横断の1問あたりの Claude の呼び出しの費用（今の約4倍）は受け入れる
   * 並べる大会の順は今の形（`context` の大会を先に）のまま
* 直しの時機（事実の誤りは即日）と、AIC 系列のマージの例外は 2026-11-02 の決定のまま

前提（チャット側。平野さんの決定ではない）
チャット側が 2026-11-03 に、origin/cloudflare（e4049b271）で開いて確かめた。着手時に今のコードで開き直す。

* データ: `workers/ask/data.json` の `players['佐々木寿人']` は28行（各行 `[期, 前後期, リーグ, 順位, 結果, 得点, …]`）。期の種類は20
* `workers/ask/src/query.mjs` の304〜331行 `player()`
   * `items` は行ごと（`period: termLabel(ki, half)`、例「第23期前期」）
   * 326行 `count: items.length` は行の数で、期の数ではない
* `workers/ask/src/ask.mjs`
   * 123行の答えの規則「人数や件数は count を使う」
   * 139行 `ANSWER_LINES`。最強戦には「player の count は卓の行の数で、出場の回数ではない」の行がある（154行、CHAT-1024-SKP-10）。鳳凰戦・女流桜花・WRC リーグ・WRC-R には player の count の行が無い
   * `ANSWER_LINES` は428行（大会別の欄）と751行（横断）の両方で使われる。ここを直せば両方に効く
* 直し方の候補（実物に合わせて選んでよい。選んだ方と理由を報告に書く）
   * (a) `player()` の結果に、プログラムが数えた期の数（例 `terms: 20`）を足し、`ANSWER_LINES` に「player の count は成績の行の数（前期・後期は別の行）で、期の数ではない。期の数は terms を書く」を足す
   * (b) (a) の規則の行だけ（期の数を書かせない）
   * 数はプログラムが数える（Claude に数え直させない、123行の規則と同じ考え）
* 5大会のうち、1期に複数の行を持つ大会（前期・後期など）がほかにあるかをデータで確かめ、同じ直しを入れる。最強戦の今の規則（154行）は変えない
* 答えが変わってよい問い: 選手の成績を聞く問いのうち、期の数・行の数を書いた答え（大会別の欄と横断の両方）。例: houou/ の「佐々木寿人」、横断の「佐々木寿人の成績は？」
* 選手の役職などの人の属性は、ログ・テストに書かない（データの名前と期の数だけ）
* 本番の確かめは1日10問まで（同じ接続元から）。2026-11-03 は AIC-18・19・20 で使っている。上限に当たったら、送れなかった問いを「未確認の項目」に書く

手順

1. 直す: 上の誤りを今のコードとデータで再現してから直す。docs/notes/ask-worker.md「5大会の応答ルール」の該当の行を直し、docs/decisions/houou.md に上の「決定」を足す
2. テスト:
   * 1期に複数の行を持つ選手（佐々木寿人の鳳凰戦）で、Claude に渡す結果に期の数（候補 (a) のとき）が入り、行の数と取り違えない規則が大会別・横断の両方の指示に入ることを確かめる
   * 「答えが変わってよい問い」の外で、今のテスト（AIC-08〜20 の案内・決勝だけの人・横断）が変わらないことを確かめる
   * 直す前のコードで新しいテストが落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
   * 新しい文脈のサブエージェントにレビューさせる（CLAUDE.md「判断・作業の原則」）
3. マージと本番:
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の3つ。答えがデータと一致するか（期の数を書いたなら20か）を書く
      * 横断（`contest: "all"`）の「佐々木寿人の成績は？」
      * houou/ の「佐々木寿人の成績は？」
      * ask-worker.md「本番で送る固定の問い」から1問
   * #654 に結果をコメントする

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、「答えが変わってよい問い」の外で今答えられている問いの答えが変わる（どれか1つでもマージしない）
* 原因が見立てと違い、`player()` の結果か `ANSWER_LINES` の外を変えないと直らない（直さずに原因を書いて止まる）
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs`・`query.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-20 の「未確認の項目」は、この指示の「未確認の項目」にも写す（少なくとも次の2つ）
   * 2026-11-09（月）03:00 JST の書き出しと「現女流桜花」の引数
   * 地域で断られた問いの記録
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 指示欄の末尾は指示文の最後の行と一致。93f8976cc は origin/cloudflare の祖先。origin/work/1103-aic はマージ済みで、ローカルの work/1103-aic は origin/cloudflare（e4049b271）と同じ。#654 に他セッションの着手中コメントなし（着手中をコメント、issuecomment-6597051499）。未マージの work/ ブランチで `workers/ask/src/` を変えているものなし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4つともある。AIC-20 のログの状態に「/ 続き: CHAT-1103-AIC-21」と回答を書き足した
- 前提の確かめ: `players['佐々木寿人']` は28行・期の種類20（前提どおり）。`player()` の `count: items.length` は行の数（前提どおり。行番号は 304〜331 で同じ）。`ANSWER_LINES` の鳳凰戦・女流桜花・WRC・WRC-R に player の count の行は無く、最強戦にはある（前提どおり）
- 1期に複数の行を持つ大会: 鳳凰戦だけ（1292人のうち1088人。例 HIRO柴田 29行・25期）。女流桜花245人・WRC リーグ357人・WRC-R 192人はすべて1期1行。最強戦は年度ごとの卓の行で、今の規則（154行あたり）のまま
- 再現: 今のコードで `answerInput(runQuery(data, player 佐々木寿人))` の鍵は type・count・items・notes で、count 28（items 28、期は20）。期の数は渡っていない
- 直し（62fb61417）: 候補 (a) を選んだ。`player()` が前期・後期のある大会（`contest.halves`、鳳凰戦だけ）のとき、期の数 `termCount`（絞った後の行の期の種類）を足し、`answerInput()` がそれを渡す。鳳凰戦の `ANSWER_LINES` に「- player の count は成績の行の数(前期・後期は別の行)で、期の数ではない。何期出たか(期の数)は termCount を書き、count を期の数として書かない」を、champions の一文の前に足した（champions の一文が最後であることを `jwrc.test.mjs` が見ている）
  - (a) の理由: (b) では「何期出た？」に答えられなくなる。数はプログラムが数える
  - 名前は指示の例の `terms` ではなく `termCount` にした。鳳凰戦の指示には「champions の terms は鳳凰位になった期」の一文があり、同じ名前だと紛れるため
  - `answerInput()` に受け渡しの1行を足した（結果の鍵を2回目に渡すのはここだけなので、(a) には要る。原因は見立てどおりで、止まる条件の「player() の結果か ANSWER_LINES の外を変えないと直らない」には当たらないと判断した）
- テスト（test/count.test.mjs、3本）: `player()` の termCount（28行・20期、期で絞ると1、1期1行の大会には付かない）、大会別の欄で2回目に termCount と一文が入る（ほかの4大会の指示には入らない）、横断のまとめた2回目で鳳凰戦の結果に termCount と一文が入る
  - 直す前のコード（origin/cloudflare）で3本とも落ちる。直した後は node 145本・python 1747本 通過
- 変わる範囲の比較（scratchpad の in21.mjs、5大会と横断 × 8つの型 × 名前）: 応答と記録は全部同じ。2回目に渡す結果が変わるのは鳳凰戦の player だけ（termCount が付く。横断でも同じ）。2回目の指示（system）は鳳凰戦の全部の型で1行増える（ほかの4大会は同じ）
- docs: ask-worker.md「5大会の応答ルール」の表に player の count の行を足した（0e476b537）。docs/decisions/houou.md に 2026-11-03（CHAT-1103-AIC-21）の節
- 自己レビュー（新しい文脈のサブエージェント、1回）:

  | 回 | 対象 | 観点 | 結果 |
  |---|---|---|---|
  | 1 | 62fb61417・0e476b537 | termCount の数え方（期で絞る・通期の行・名前の解決・期の外・横断）、ほかの大会と型への影響、指示の紛れ、テスト、docs | 要修正なし。任意3件: ほかの大会のループが佐々木寿人で not_found になり確かめになっていない → 直した（各大会の在籍の選手で2回目まで通す）／期で絞ったときの count も見る → 足した／docs の最強戦の列が ○ → －（別の規則）に直した。証跡: 145本通過、直す前のコードで3本とも落ちる、runQuery で 28行・20期、期で絞ると1、読みの「ささきひさと」でも20、HIRO柴田 29行・25期 |

  直しはテストと docs の表だけなので2回目のレビューは行わなかった。直した後も node 145本通過、直す前のコードで新しい3本が落ちる

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 93f8976c）: https://github.com/retroeater/mj-logs/tree/main/guide/93f8976c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
