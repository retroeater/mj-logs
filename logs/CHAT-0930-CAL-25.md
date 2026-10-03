# CHAT-0930-CAL-25

- 着手日時: 2026-10-03 12:33（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-mos
- 着手時HEAD: 16b2dff5（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：CAL のチャットの振り返りから、仕組みに落とせる規則を指示文の雛形・チャット側の手順書・handover に書き足す（マージはしない） Chat-Ref: CHAT-0930-CAL-25 貼る時機: いつでも（CAL-24 と並行で可。触るのは文書だけ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1003-cal-mos を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 未承認（平野さんが差分を読み比べて決める。判断待ちで止まる）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-0930-CAL のチャット（9/30〜10/3、#448 系）の振り返りのうち、受け手かチャット側が機械的に確かめられる形にできるものだけを、規則として書き残す。抽象的な心得は書かない。事例は1行の参照（Chat-Ref）にとどめる。
決定（2026-10-03、平野さん）

* 振り返りの申送りとして、下の規則を書き残す。マージは差分を読み比べてから決める。

前提（チャット側の案。平野さんの決定ではない。規則の文面は追記先の書きぶりに合わせてよい）
書き残す規則（4つ）と、直す古い記述（1つ）:

1. 指示文の「貼る時機」行（docs/instruction-template.md）: 共通手順の行の手前に「貼る時機: …」の行を置く。ほかの指示の完了・毎朝の実行の後など、前提がある指示はそれを書き、手順0で前提が満たされているかを確かめさせる（満たされなければ何もせず止まる）。前提が無ければ「いつでも」。事例: CAL-19 が CAL-09 より先に貼られ、番号を1つ使った。
2. 見込みとの許すずれを数で書く（docs/instruction-template.md）: 見込みと比べて止まる条件は「大きく違えば」と書かず、許すずれを数で書く（例: 「直すは見込み ±3件まで、作る・消すは見込みと同じ」）。事例: CAL-15 で直すが見込み1件に対して2件となり、受け手の判断で進んだ。
3. 同じチャットから並行で出す指示はブランチを分ける（docs/notes/chat-side-operations.md）: 同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを `work/<MMDD>-<識別子>-<短い名前>` のように分ける。1つのセッションには1つの指示だけを貼るよう平野さんに伝える。事例: CAL-04 と CAL-05 が同じ work/0930-cal を使いかけた。CAL-11 と CAL-12 が同じセッションで動き、ブランチの切り替えで止まった。
4. 後の実行の結果に頼る指示は、結果が出てから作る（docs/notes/chat-side-operations.md）: 毎朝の実行や別の指示の結果を見込みとして使う指示（確認の指示など）は、その結果が出てから作る。前もって作るときは「貼る時機」に前提を書き、見込みは「<Chat-Ref> のログの `## 報告` に従う」の形にして数を写さない。事例: CAL-09 を前もって作り、前提が変わるたびに3回書き直した。
5. 直す古い記述（docs/handover.md）: 「未対応の注意: 予定表のジョブ yotei は、「【1】元データ」の1000行の上限で失敗したことがある。…対応済みかは未確認」の行は、CHAT-0930-CAL-15（シートの行数を書く前に自動で足す修正、10-01 マージ）で対応済み。行を消すか、対応済みの1行に直す。

手順

1. 確かめ: 追記先の3文書（docs/instruction-template.md・docs/notes/chat-side-operations.md・docs/handover.md）の今の内容を読み、上の1〜4に当たる規則がすでにあるか（同じ意味の記述、食い違う記述）を書く。あれば、その記述を直すか足さないかを、指示の意図に照らして選び、理由を書く。3文書の今の大きさと、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」などにある容量の上限と残りを書く。同じ文書を触る未マージのブランチが無いことを確かめる。
2. 追記: 上の1〜5を、規則だけ（各2〜3行まで）で書く。事例は Chat-Ref 1つの参照にとどめ、経過は書かない。容量の上限を超える、または残りが1割を切るときは、ほかを削らずに止まる。文書の大きさの検査（あれば）を通す。
3. 読み比べの用意: ログに、3文書それぞれの差分（`git diff origin/cloudflare -- <ファイル>` の全文）を貼る。

止まる条件

* 同じ文書を触る未マージのブランチがある。
* 追記で容量の上限を超える、または残りが1割を切る。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、足した規則の一覧と、手順1で既存の記述と重なったものの扱いを書く。決定は CLAUDE.md のとおり `docs/decisions/` に足す（分野が無ければ、README の決まりに従って「運用」の分野を作る）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-25.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-25 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-25` は0件。`work/1003-cal-mos` はローカル・リモートとも無いので `git checkout -b work/1003-cal-mos origin/cloudflare`（16b2dff5）
- 手順0: 指示欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1003-cal-mos
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-mos/docs/logs/CHAT-0930-CAL-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-mos
- 確認用URL: なし
- マージ: 未（未承認）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fee9a96e）: https://github.com/retroeater/mj-logs/tree/main/guide/fee9a96e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ffc4839a.md
