# CHAT-1015-SKD-19

- 着手日時: 2026-10-15
- 対象issue: #566（公開は #549）
- ブランチ: work/1015-skd-top
- 着手時HEAD: 5ef0f63bd

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいトップの検索の結果で、卓の行の卓名を2行目に移す（「決勝・12月14日」）— 別ブランチでプレビューまで（#566） Chat-Ref: CHAT-1015-SKD-19 マージ: 判断待ちで止まる（この指示ではマージしない。cloudflare へのマージは #549 の公開の指示で行う） 貼る時機: いつでも（CHAT-1015-SKD-18 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skd-top の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-skd-top を続けて使う（SKD-17・18 の新しいトップに重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 と #549 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

目的
SKD-18 のプレビューで、検索の結果の卓の行の1行目「対局名 卓名」が 375px で語の途中で折り返す（例「ファイナル 2nd Stage ベスト8 B卓」）。卓名を2行目の日付の前に移し、1行目を対局名だけにする。
決定（2026-10-15、平野さん。SKD-18 の判断待ちへのチャット側の案から選んだもの）

1. 卓の行は案1: 1行目に対局名（16px）、2行目に「卓名・月日」（13px 灰色。例「決勝・12月14日」「ベスト8 B卓・8月16日」）。右端の順位「1位」・ピル・› は今のまま

前提（チャット側。平野さんの決定ではない）

* 2行目の日付は年を出さない（面の上の見出し「麻雀最強戦2025」に年度があるため）。月日の書き方は今の新しい年度・対局のページの日付と同じ形にそろえ、どうしたかをログに書く
* 日付の無い対局の卓は、2行目を卓名だけにする。卓名が無い（対局に卓が1つだけで卓名を持たない）行があれば、2行目を日付だけにし、どちらも無ければ2行目を出さない。該当する行が実データにあるかをログに書く
* 長い対局名は1行目で折り返してよく、右端と重ならないこと。2行目も同じ
* SKD-18 のほかの点（年度の見出しを年度ページへのリンクにしたこと、200% で結果が長くなること）は、チャット側がプレビューで見てこのままでよいとした。変えない
* 変えるのはトップの検索の結果の卓の行（`assets/saikyo.js` のトップの検索と、その CSS）だけの見込み。生成物の差はトップだけ（シートの変化・X の画像の URL の変化を除く）
* 確かめ（DSN-07 以降の定型）: 375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、部品の状態ごとの高さ（検索の前・結果あり〈1人・複数〉・結果なし、選手の行を開く前・開いた後）。代表は桑田憲汰と「鈴木」。375px で1行目が語の途中で折り返す行が無くなったか（対局名そのものが長くて折り返す行は、どの対局かを書く）、390px で桑田憲汰の結果の高さを SKD-18 の版と比べて書く
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 検索欄に「桑田」と入れると「麻雀最強戦2025」の面の中に、1行目「ファイナルトーナメント」、2行目「決勝・12月14日」の行がある〈実データの値で書く〉。URL は書かない）
* 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/saikyo-page-design.md` の「#549 で入れる新しい形」の卓の行の記述を直す。SKD-18 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-SKD-19` を足す
* #549 には新しいコメントを書かない（SKD-18 のコメントのとおり、このブランチを公開の指示で一緒にマージする）

手順

1. #566 に着手中のコメントを書く。卓の行の出し方を直し、テストがあれば足して `python3 -m unittest discover -s scripts/tests` を通す
2. 生成し直して差を分類し、上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、トップ `/saikyo/index.html` のプレビューの URL を書く

止まる条件

* #566 か #549 に他のセッションの着手中コメントがある
* 生成物の差がトップ以外に出る（シートの変化と X の画像の URL の変化で説明できるものを除く）
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 直した関数・CSS、日付の書き方、卓名・日付が無い行の扱いと実データの該当、375px の折り返しの結果、桑田憲汰の結果の高さの前後、生成物の差、確かめの結果、確認用URL の印、決定と文書の記録先、SKD-18 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-19.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a177d10d）: https://github.com/retroeater/mj-logs/tree/main/guide/a177d10d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
