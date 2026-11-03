# CHAT-1102-AIC-16

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1102-aic
- 着手時HEAD: cf7053c8

## 指示

【Claude作成】Claude Code 向け指示：（AIC-15 の続き）質問欄の「ほかの大会への案内」の行き先に最強戦（saikyo/）を足し、cloudflare へマージする Chat-Ref: CHAT-1102-AIC-16 マージ: 承認済み（チャットで）。AIC 系列の例外（2026-11-02 の平野さんの決定）による。下の「止まる条件」のうち、次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1102-AIC-15 のマージ〈3f6de264b〉は済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1102-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1102-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。3f6de264b が origin/cloudflare の祖先であることを確かめる。AIC-15 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-AIC-16` を足し、その「判断が必要なこと」への回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜15 で使用）。

目的
最強戦のページは公開済み（#627 は 2026-10-26 に閉じた）。しかし、質問欄が「記録があるのはこの大会です」と案内する行き先に、最強戦がまだ入っていない。AIC-08 で「公開のときに足す」と残したまま足していなかったので、足す。
決定（2026-11-02、平野さん）

* AIC-15 の「判断が必要なこと」への回答: 最強戦を案内の行き先に足す。週次にまとめず、すぐ直す。案内の欠けは、事実の誤りの側として扱う
* マージは AIC 系列の例外による（冒頭の「マージ:」の行）

前提（チャット側。平野さんの決定ではない）
チャット側が 2026-11-02 23:40 JST に、origin/cloudflare（7b866bf8a）で開いて確かめた。

* `workers/ask/src/ask.mjs` の27〜29行
   * 27〜28行は次のコメント。「// 案内する大会のページ(質問欄のあるトップ)。公開している大会だけ。」「// 簡略化: 最強戦(#626)は未公開なので載せない。公開(#627)のときに足す。質問欄を1つにまとめるときは大会の設定に移す」
   * 29行は `export const GUIDE_PAGES = { houou: '/houou/', ouka: '/ouka/', jwrc: '/jwrc/', wrcr: '/wrcr/' };`
* `workers/ask/src/index.mjs` の13行: 大会の id は `saikyo`（`contests: { ouka: …, jwrc: …, wrcr: …, saikyo: dataSaikyo }`）
* リポジトリに `saikyo/index.html` がある
* 足すと、最強戦にだけ記録がある選手を、ほかの大会の質問欄で聞かれたときに、最強戦へ案内できる。最強戦の質問欄から、ほかの4大会への案内は、AIC-08 から入っている
* 案内に書く「〜のトップ」の名前・個人成績のリンク（1人で在籍者のとき）の作り方が、最強戦でも成り立つか（最強戦の個人成績のページの URL の形）を確かめる。成り立たなければ、トップへのリンクだけにする
* 27〜28行のコメントは、事実に合わせて直す（「簡略化:」のうち、統合のときに大会の設定に移す点は残す）
* docs/notes/ask-worker.md「5大会の応答ルール」の該当の行を直す。#654（統合）の「ほかの残り」からこの項目を外す
* 本番の確かめは1日10問まで（同じ接続元から）

手順

1. 直す: `GUIDE_PAGES` に最強戦を足し、案内の文とリンクが最強戦でも正しく作られることを確かめる
2. テスト:
   * 最強戦にだけ記録がある選手を data から選び、鳳凰戦の質問欄で聞いたときに最強戦へ案内されることを確かめる
   * ほかの大会の案内の答えが変わらないことを確かめる
   * 直す前のコードで新しいテストが落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
3. マージと本番:
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の3つ。答えがデータと一致するかを書く
      * 上のテストの選手を houou/ で聞く
      * jwrc/ の「佐々木寿人」（今までどおり鳳凰戦へ案内される。最強戦にも記録があれば両方が並ぶ）
      * ask-worker.md「本番で送る固定の問い」から数問
   * #654 に結果をコメントする

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、今答えられている問いの答えが変わる（どれか1つでもマージしない。直す前にマージしていたら、報告に書いて止まる）
* 最強戦の個人成績のリンクの形が、ほかの大会と違って作れない。トップへのリンクだけにして進め、報告に書く。トップへのリンクも作れないなら止まる
* 未マージの work/ ブランチが `workers/ask/src/ask.mjs` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-15 の「未確認の項目」2つは、この指示の「未確認の項目」にも写す
   * 2026-11-09（月）03:00 JST の書き出しと、「現女流桜花」の引数
   * 地域で断られた問いの記録
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-AIC-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-AIC-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82c934c3）: https://github.com/retroeater/mj-logs/tree/main/guide/82c934c3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82c934c3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cf7053c8.md
