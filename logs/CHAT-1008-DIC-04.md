# CHAT-1008-DIC-04

- 着手日時: 2026-10-08
- 対象issue: #515
- ブランチ: work/1008-dic
- 着手時HEAD: 734f7ed0

## 指示

【Claude作成】Claude Code 向け指示：DIC-03 の続き。辞書ページの CSS を別ファイルにして、Gboard 形式の追加と見た目の比較ページを作る（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-04 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-03 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1008-DIC-03 は、未マージの work/1008-hou（#518）が `style.css` を変えているため手順1で止まった。その答えを受けて、DIC-03 の手順2（作る）・手順3（報告する）を行う。仕様（目的・決定・前提・止まる条件）は DIC-03 のログの「指示」欄のとおりで、下の決定だけを足す。
決定（2026-10-09、平野さん）

* 辞書ページの新しい見た目の CSS は `style.css` に入れず、辞書用の別ファイル（例 `resource_dictionary.css`）に置く。比較ページの案ごとの CSS はページ内の `<style>` でよい（DIC-03 の報告の案のとおり）

前提（チャット側。平野さんの決定ではない）

* 別ファイルの CSS は自ドメインの静的ファイルとして読み込む（外部ドメインは使わない）。`.assetsignore` に載せない（公開する）。色の値は `style.css` の変数・値をそのまま使い、別ファイルで新しい色を作るときはコントラストを WCAG 2.x の式で計算して報告する
* work/1008-hou がこの作業中にマージされて `style.css` が変わっても、辞書の CSS は別ファイルのまま進めてよい

手順

1. 確かめる: CHAT-1008-DIC-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-04` を足す。上の決定を `docs/decisions/` に足す。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）を一覧にし、`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/`・新しく作る CSS のファイル名を変えているものが無いことを確かめる（`style.css` だけを変えているものは止まる理由にしない）。
2. 作る: DIC-03 の手順2のとおり（本番の辞書ページに Gboard 形式を足す〈見た目は今のまま〉・4つの軸を切り替えられる比較ページ・PC 幅とスマホ幅のスクリーンショット・Gboard の zip の中身の確かめ・キーボード操作とフォーカスの確かめ）。
3. 報告する: DIC-03 の手順3のとおり（比較ページと本番ページのプレビュー URL、相性の良い組み合わせ2〜3個の提案）。判断待ちで止まる。

止まる条件

* CHAT-1008-DIC-03 の状態が「判断待ち」でない
* 未マージの work/ ブランチが上の手順1のファイル（`style.css` を除く）を変えている
* DIC-03 の「止まる条件」に当たった（未マージのブランチの条件は上の手順1に置き換える）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #515
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
