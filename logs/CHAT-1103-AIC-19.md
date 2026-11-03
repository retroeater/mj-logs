# CHAT-1103-AIC-19

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: 46a47f07

## 指示

【Claude作成】Claude Code 向け指示：（AIC-18 の続き）質問欄で、決勝だけに記録がある選手を「この大会の記録にいません」と答える誤りを直し、cloudflare へマージする Chat-Ref: CHAT-1103-AIC-19 マージ: 承認済み（チャットで）。AIC 系列の例外（2026-11-02 の平野さんの決定）による。下の「止まる条件」のうち、次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1103-AIC-18 のマージ〈62570dd46〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。62570dd46 が origin/cloudflare の祖先であることを確かめる。AIC-18 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-AIC-19` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜18 で使用。AIC-17 は欠番）。

目的
AIC-18 の 1 を直す。決勝だけに記録がある選手（リーグ・予選の成績の行が無い人）をその大会の質問欄で聞くと、「この大会の記録に当たる選手はいません」と答えている。事実の誤りなので直す。
AIC-18 の止まる条件「jwrc/ の『佐々木寿人』の答えが変わらない」は、チャット側の誤りだった。佐々木寿人にも WRC リーグの決勝（第10期、4位）の記録があり、今の答えも同じ誤り。前提を確かめずに書いた。
決定（2026-11-03、平野さん）

* AIC-18 の「判断が必要なこと」への回答: 決勝だけに記録がある選手の答えを、決勝の記録で答える形に変えてよい（jwrc/ の「佐々木寿人」を含む）
* 直しの時機（事実の誤りは即日）と、AIC 系列のマージの例外は 2026-11-02 の決定のまま

前提（チャット側。平野さんの決定ではない）

* 決勝だけに記録がある選手の数（AIC-18 のログ「判断が必要なこと」から写した。着手時にデータで数え直す）
   * 鳳凰戦9人・WRC リーグ5人・WRC-R 7人
   * 女流桜花・最強戦は0人
* 原因（AIC-18 の前提のまま。着手時に今のコードで開いて確かめる）
   * `guideAnswer()` が、その大会の決勝の記録の有無を見ずに「${here.name}の記録に「…」に当たる選手はいません」と書いている
* 答えが変わってよい問い: 上の21人の名前を、その大会の質問欄で聞いたときの答え
   * 例: houou/ の「森山茂和」、jwrc/ の「佐々木寿人」
   * 変わった後の形の例（文面は実物に合わせて直してよい）: 「WRCリーグの予選の成績の記録はありませんが、WRCリーグの決勝の第10期に4位の記録があります。ほかに鳳凰戦・麻雀最強戦にも記録があります」
   * ほかの大会の案内（AIC-08・16・18）は、決勝の記録の後に添える
   * 決勝の記録は `final` などの今の検索の結果から取り、期・順位はプログラムが付ける
* AIC-18 で直した、連盟の外の選手を最強戦の個人成績へ送る件は壊さない
* 選手の役職などの人の属性は、ログ・テストに書かない（データの名前と期・順位だけ）
* 本番の確かめは1日10問まで（同じ接続元から）。2026-11-03 は AIC-18 で5問を使っている

手順

1. 直す: 上の原因を今のコードとデータで再現してから直す（5大会に同じ形で入れる）。docs/notes/ask-worker.md の該当の行を直し、docs/decisions/houou.md に上の「決定」を足す
2. テスト:
   * 21人全員について、その大会の質問欄で聞いたときに決勝の記録（期・順位）で答えることを確かめる
   * 「答えが変わってよい問い」の外で、案内の答え（「萩原聖人」を houou/ で、「TAKUYA」を houou/ で聞く）が変わらないことを確かめる
   * 直す前のコードで新しいテストが落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
3. マージと本番:
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の4つ。答えがデータと一致するかを書く
      * houou/ の「森山茂和の成績は？」
      * jwrc/ の「佐々木寿人」
      * houou/ の「TAKUYA」
      * ask-worker.md「本番で送る固定の問い」から1〜2問
   * #654 に結果をコメントする

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、「答えが変わってよい問い」の外で今答えられている問いの答えが変わる（どれか1つでもマージしない）
* 原因が見立てと違い、直すと `guideAnswer()` の外の答えも変わる（直さずに原因を書いて止まる）
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs`・`query.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-18 の「未確認の項目」2つは、この指示の「未確認の項目」にも写す
   * 2026-11-09（月）03:00 JST の書き出しと「現女流桜花」の引数
   * 地域で断られた問いの記録
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 46a47f07）: https://github.com/retroeater/mj-logs/tree/main/guide/46a47f07

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/46a47f07/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
