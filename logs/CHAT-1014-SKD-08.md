# CHAT-1014-SKD-08

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 1b7e79dc3

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しい年度ページで、順位が空の行の右端の「通過」をピルにし、その対局の見出しの下の「通過」を外す — プレビューで止まる（#566、SKD-07 の続き） Chat-Ref: CHAT-1014-SKD-08 マージ: 判断待ちで止まる（チャット側がプレビューを見てから、マージの指示を別に出す） 貼る時機: いつでも（CHAT-1014-SKD-07 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-07 の変更に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SKD-07 のプレビューを平野さんが見た結果を入れる。順位が空の勝ち上がり者の行（29対局・52行）の右端の「通過」をピルにし、行の右端が「通過」になる対局では、対局名の行の下の「通過」の見出しを外す（同じ字が2回続くのをなくす）。
決定（2026-10-14、平野さん）

1. SKD-07 の変更はマージしてよい（この指示の直しを入れたうえで。マージは次の指示）
2. 順位が空の行の右端の「通過」は、13px 灰色の字ではなくピルにする
3. 行の右端が「通過」になる対局では、対局名の行の下の「通過」（1回だけの見出し）を外す（チャット側の案 (b)）

前提（チャット側。平野さんの決定ではない）

* ピルの形と色は、対局のページの「通過」のピル（今の生成のもの）と同じにする（年度ページの「優勝」のピルとは字だけが違う形になる見込み。違う色・形にする理由があれば止まって報告する）。どのクラス・色にしたかと、コントラスト（WCAG 2.x の相対輝度の式と値）をログに書く
* 見出しの「通過」を外す条件は「その対局の勝ち上がり者の行の右端がすべて『通過』」（＝順位がすべて空）。一部の行だけ順位が空の対局があれば、見出しは残し、その対局を一覧にしてログに書く（SKD-05 の一覧では29対局とも全員が空の見込み）
* 「A卓 1位」の行・優勝の行・対局のページ・卓のページ・今の年度ページ・トップは変えない
* 生成物の差は、SKD-07 の7年度（2011・2016・2017・2018・2019・2020・2021）の新しい年度ページだけ。ほかのページには、X の画像の URL の変化とシートの変化以外の差が出ないこと
* 確かめ: 2019 年度の年度ページで、375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%。右端のピルが「優勝」のピルと同じ位置・高さにそろい、1行に収まること。見出しを外した対局の面の高さが崩れないこと
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 2019 年度の「男子プレミアトーナメント 恐れ知らずの打撃戦」の大和の行の右端が「通過」のピルで、対局名の下に「通過」の見出しが無い。URL は書かない）
* SKD-07 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1014-SKD-08` を足す。決定を `docs/decisions/design-renewal.md` に記録する（SKD-07 の決定も未記録なら足す）

手順

1. #566 に着手中のコメントを書く。右端と見出しを作る関数を直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通す
2. `python3 scripts/regenerate.py saikyo_pages` で生成し直し、差を分類して、上の確かめを行う
3. 決定を記録して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、2019 年度の年度ページのプレビューの URL（`/saikyo/2019/index.html`）を書く

止まる条件

* 生成物の差が上の見込みを外れる
* ピルを対局のページの「通過」のピルと同じにできない理由がある
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 直した関数とテスト、ピルのクラス・色・コントラスト、見出しを外した対局の数（一部だけ空の対局の一覧）、生成物の差の種類と件数、確かめの結果、確認用URL の印、決定の記録先、SKD-07 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/work/1014-skd/docs/logs/CHAT-1014-SKD-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e6dd2e0e）: https://github.com/retroeater/mj-logs/tree/main/guide/e6dd2e0e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e6dd2e0e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
