# CHAT-1103-AIC-26

- 着手日時: 2026-11-03（JST）
- 対象issue: #654
- ブランチ: work/1103-aic
- 着手時HEAD: d9cf93a82

## 指示

【Claude作成】Claude Code 向け指示：（AIC-25 の続き）質問欄の統合のうち Worker の側だけを入れる。横断（`contest: "all"`）＋`context` で、大会名の無い問いは `context` の大会だけで答え、答える大会が1つのときは今の大会別と同じ応答にする。画面の JS は変えない。cloudflare へマージする Chat-Ref: CHAT-1103-AIC-26 マージ: 承認済み（チャットで。2026-11-03、平野さんが CHAT-1103-AIC-25 について単独で承認した範囲のうち Worker の部分。AIC-25 の止まりの後、平野さんが「Worker だけ先に、画面は後」を選んだ）。次の3つを1つでも満たさなければマージしない

* テストが通る
* 本番の問いの答えがデータと一致する
* 下の「答えが変わってよい問い」の外で、今答えられている問いの答えが変わらない 貼る時機: いつでも（CHAT-1103-AIC-23 のマージ〈22d902d01〉は済んでいる。AIC-25 はコードを変えずに止まった） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-aic を使う。AIC-25 のログのコミットが乗っていて、まだマージされていない。そのまま使い、origin/cloudflare を取り込んでから作る（docs/notes/cloud-sessions.md「作業ブランチの用意」。取り込みで衝突したら CLAUDE.md「ブランチ運用」のとおり）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。AIC-25 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-AIC-26` を足し、その「判断が必要なこと」3つへの回答（下の「決定」）を書き足す。識別子 `AIC` はこのチャットの系列（AIC-01〜25 で使用。AIC-17・24 は欠番）。

目的
AIC-25（質問欄を横断の1つにまとめる）は、作る前に止まった。画面の JS `assets/houou_ask.js` を、未マージの PRP 系列のブランチ（`work/1101-prp-pub`）も変えていたため。
平野さんの判断で、Worker の側だけを先に入れる。画面の JS（どのページが何を送るか、2つ以上の大会の見せ方）は、PRP と TOP-07 のマージの後に別の指示で行う。
この指示の後も、画面は今の大会別の要求を送り続ける。今の見た目と答えは変わらない。変わるのは、`contest: "all"`＋`context` を送ったときの Worker の答え方だけ（今はトップの未公開の欄だけが使う）。
決定（2026-11-03、平野さん）

* AIC-25 の「判断が必要なこと」への回答
   * 1つ目（PRP との重なり）: Worker の側だけを先に入れる（この指示）。画面の JS は、PRP（`work/1101-prp-pub`）と TOP-07（`work/1103-top`）がマージされた後、トップの `assets/top_ask.js` に寄せて1つの部品にする方向で、別の指示で行う
   * 2つ目（`top_ask.js` と `houou_ask.js`）: 上のとおり、後で `top_ask.js` に寄せて1つにする方向。細部は、その指示を書くときに両方の実物を読んで決める
   * 3つ目（大会別の要求を消す時機の案）: 案のとおり、画面の統合の後に、週次の記録で大会別の型の行が0になった週の後に消す。この指示では消さない（チャット側が案を受けた。平野さんの個別の決定ではない）
* AIC-25 の「決定」（大会名の無い問いは `context` の大会だけ、選手名は記録のある大会を `context` を先に並べる、pro/ は `context` 無し）は、そのまま有効

前提（チャット側。平野さんの決定ではない）
AIC-25 の前提（`crossContests()` の今の選び方など）と、AIC-25 のログ `## 経過` を読む。着手時に今のコードで開き直す。

* 直すこと（Worker）
   * `workers/ask/src/ask.mjs` の `crossContests(deps, question, context)`
      * `context` があり、問いに大会名も、記録のある名前も無いときは、`context` の大会だけを返す
      * `context` が無いとき（トップ）は、今のまま5大会
   * 答える大会が1つのとき、横断の応答を今の大会別の応答とそろえる。比べる項目は `answer`・`lead`・`notes`・`asof`・`sources`、案内・期の外・決勝だけの人の文、記録の `type` の扱い
      * 横断で足した項目（`contests`・`players`）は付けたままでよい（足すだけで、今の項目を変えない）
      * 大会が1つのときは今の大会別の流れで答える形が簡単なら、それでよい
      * 記録の `type` を `all:…` にするか、今の大会別の形にするかは実物で決め、報告に書く
* 変えないもの
   * 画面（`assets/`・`navbar.js`・ページ）
   * 大会別の要求（`contest` を省略・大会の id）の流れと答え
* 答えが変わってよい問い: `contest: "all"`＋`context` で送った、大会名も記録のある名前も無い問い（今は5大会 → `context` の大会だけ）。今この形で送るのは、未公開のトップの欄（TOP-07、未マージ）だけ。トップの欄は `context` を送らない見込みなので、トップの答えは変わらない。TOP-07 の `assets/top_ask.js` が何を送っているかは、`work/1103-top` で読んで報告に書く（読むだけで、変えない）
* 本番の確かめは1日10問まで（同じ接続元から）。上限に当たったら、送れなかった問いを「未確認の項目」に書く
* 選手の役職などの人の属性は、ログ・テストに書かない

手順

1. 作る: 上の直しを入れる。docs/notes/ask-worker.md の横断の節（`context` の効き方、大会が1つのときの応答、大会別の要求は残していること）を直す。docs/decisions/houou.md に AIC-25 と上の「決定」を足す
2. テスト
   * 5大会それぞれの `context` で、大会名の無い問い（「現タイトルホルダーは？」「歴代優勝者は？」など、今の固定の問いから選ぶ）の応答が、同じ大会の大会別の要求の応答と同じになる（横断で足した項目を除く）
   * 大会名のある問い（`context: "houou"` で「女流桜花の歴代優勝者は？」）が、名前の大会で答える
   * 選手名（`context: "houou"` で「佐々木寿人の成績は？」）が、記録のある大会を鳳凰戦を先にして並べる
   * `context` が無いときは、AIC-20 の今の横断の答えのまま
   * 大会別の要求の答えが今と同じ
   * 変わる範囲の比較: 今までの比べ方（AIC-21〜23 の scratchpad の比べ方）で、大会別の要求と、`contest: "all"`＋`context` の要求の応答・記録・Claude に渡す結果を、5大会×型×名前で比べる。違いが、横断で足した項目と、選手名で記録のある大会が並ぶところだけであることを確かめ、表にしてログに書く
   * 新しいテストは、直す前のコードで落ちることを確かめる
   * `python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` が通ることを確かめる
   * 新しい文脈のサブエージェントにレビューさせる（CLAUDE.md「判断・作業の原則」）
3. マージと本番
   * cloudflare へマージし、`mj-ask` のビルドの結果を確かめられる範囲でログに書く
   * 本番に送る問いは次の4つ（`Origin: https://ryoei.pro`）。答えがデータと一致するかを書く。1〜3つ目は、大会別の要求の答えと同じか（3つ目は、どう並んだか）も書く
      * `contest: "all"`, `context: "houou"` で「現鳳凰位は？」（鳳凰戦だけ）
      * `contest: "all"`, `context: "saikyo"` で「歴代最強位は？」（最強戦だけ）
      * `contest: "all"`, `context: "houou"` で「佐々木寿人の成績は？」（鳳凰戦を先に）
      * ask-worker.md「本番で送る固定の問い」から1問（大会別の要求のまま）
   * #654 に結果をコメントする（TOP 系列が読む。`context` の効き方を書く）

止まる条件

* テストが通らない、本番の問いの答えがデータと一致しない、「答えが変わってよい問い」の外で今答えられている問いの答えが変わる（どれか1つでもマージしない）
* 大会が1つのときの応答を大会別とそろえるのに、`ask.mjs` の大会別の流れ（1回目・検索・2回目）の中身を書き換える必要がある（作らずに、どこが違うかを書いて止まる）
* 未マージの `work/` ブランチが `workers/ask/src/` の同じ関数・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。`assets/` は触らないので、`assets/houou_ask.js` を変えているブランチ（`work/1101-prp-pub`）では止まらない
* 1問あたりの Claude の呼び出しが、大会が1つの問いで今より増える見込みになる（費用の見込みを書いて止まる）
* #654 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* マージ後の check-run・ビルドの失敗: 今回の変更による失敗なら止まって報告する。無関係な失敗なら原因を報告に書いて、残りの手順を進めてよい。自分の変更で落ちると分かったテストは直してよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。AIC-25 の「未確認の項目」4つは、この指示の「未確認の項目」にも写す
* 「判断が必要なこと」に、`top_ask.js` が送っている要求の形（読んだ結果）と、画面の統合の指示に要る前提（PRP・TOP-07 のマージの状況）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-AIC-26.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-AIC-26 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 45487824）: https://github.com/retroeater/mj-logs/tree/main/guide/45487824

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/45487824/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
