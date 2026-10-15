# CHAT-1015-SKD-18

- 着手日時: 2026-10-15
- 対象issue: #566（公開は #549）
- ブランチ: work/1015-skd-top
- 着手時HEAD: 58adc9d8e

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいトップの検索の結果を「年度ごとに1枚の面」にまとめ、#549 にマージ予定を書く — 別ブランチでプレビューまで（#566） Chat-Ref: CHAT-1015-SKD-18 マージ: 判断待ちで止まる（この指示ではマージしない。cloudflare へのマージは #549 の公開の指示で行う） 貼る時機: いつでも（CHAT-1015-SKD-17 は判断待ち。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skd-top の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-skd-top を続けて使う（SKD-17 の新しいトップに重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 と #549 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

目的
SKD-17 の新しいトップのプレビューを平野さんが見た結果を入れる。形は SKD-17 のままでよく、検索の結果だけを、出場の多い選手でも短くなるよう年度ごとに1枚の面にまとめる。あわせて、#549 に「公開の指示でこのブランチを一緒にマージする」と書く。
決定（2026-10-15、平野さん。SKD-17 の判断待ちへの回答）

1. SKD-17 の新しいトップの形（白地のナビ・題名・検索欄・歴代最強位の面の行・2026 の行・複数の選手が当たったときに選手の行を押して開く形）でよい
2. 検索の結果は、対局ごとに1枚の面を並べる形をやめ、年度ごとに1枚の面にまとめ、行を「対局名と卓」の1行にする（チャット側の案 (b)）
3. 日付の無い対局の2行目を「麻雀最強戦<年度>」とすること（SKD-17 の判断待ちの (3)）は、(b) にしたことで2行目の出し方が変わるので、下の前提のとおりにする

前提（チャット側。平野さんの決定ではない）

* 年度ごとの面: 面の上に 13px 灰色の見出し「麻雀最強戦2025」（年度ページへのリンクにするなら › を付け、押せる高さ 44px 以上。しないなら見出しだけ。どちらにしたかをログに書く）。面の中は、その年度に出場した卓の行を、対局のページと同じ順（対局の新しい順、対局の中は卓の順）に並べる
* 卓の行: 1行目に「対局名 卓名」（例「ファイナルトーナメント 決勝」「ファイナル 2nd Stage ベスト8 B卓」、16px）、2行目に日付（13px 灰色。日付の無い対局は2行目を出さない）。右端に順位「1位」（13px 灰色）と「通過」「優勝」のピル（今と同じ）と ›。押すと新しい卓のページへ（SKD-17 と同じ行き先）。長い対局名は折り返してよく、右端と重ならないこと
* 選手の見出しの行（写真の丸・名前・「11卓」・開閉）は SKD-17 のまま
* #549 に「公開の指示で work/1015-skd-top（最強戦のトップの作り直し、CHAT-1015-SKD-17・SKD-18）を一緒にマージする。公開の直前に origin/cloudflare を取り込み、生成し直してから入れる」とコメントする（本文・状態・ラベルは変えない。末尾に `Chat-Ref: CHAT-1015-SKD-18`）
* 変えるのはトップの検索の結果の出し方（`assets/saikyo.js` のトップの検索と、その CSS）だけの見込み。生成物の差はトップだけ（シートの変化・X の画像の URL の変化を除く）
* 確かめ（DSN-07 以降の定型）: 375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px、部品の状態ごとの高さ（検索の前・結果あり〈1人・複数〉・結果なし、選手の行を開く前・開いた後）。代表は桑田憲汰（出場の多い選手）と「鈴木」（複数）。390px で桑田憲汰の結果の高さを SKD-17 の版と比べて書く
* ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: 検索欄に「桑田」と入れると「麻雀最強戦2025」の面の中に「ファイナルトーナメント 決勝」の行がある。URL は書かない）
* 決定を `docs/decisions/design-renewal.md` に記録し、`docs/notes/saikyo-page-design.md` の「#549 で入れる新しい形」の記述を直す。SKD-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-SKD-18` を足す

手順

1. #566 に着手中のコメントを書く。検索の結果の出し方を直し、テストがあれば足して `python3 -m unittest discover -s scripts/tests` を通す
2. 生成し直して差を分類し、上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめる。#549 にコメントして、判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、トップ `/saikyo/index.html` のプレビューの URL を書く

止まる条件

* 生成物の差がトップ以外に出る（シートの変化と X の画像の URL の変化で説明できるものを除く）
* 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

完了条件

* 直した関数・CSS、年度の見出しをリンクにしたか、桑田憲汰の結果の高さの前後、生成物の差、確かめの結果、確認用URL の印、#549 のコメントの URL、決定と文書の記録先、SKD-17 のログの状態の直しがログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66464371）: https://github.com/retroeater/mj-logs/tree/main/guide/66464371

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
