# CHAT-1008-DIC-07

- 着手日時: 2026-10-08
- 対象issue: #522
- ブランチ: work/1008-dic
- 着手時HEAD: ee99f082

## 指示

【Claude作成】Claude Code 向け指示：DIC-06 の続き。辞書ページの小さな直し（「複数選べます」を消す・説明文の横幅をメニューに合わせる・不要な CSS を消す）をしてマージする Chat-Ref: CHAT-1008-DIC-07 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1008-DIC-06 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-06 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-06 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
平野さんが DIC-06 のプレビューを見た。小さな直しを入れて、新しい見た目の辞書ページ（Gboard は画面に出さない）を本番に入れる（#522）。
決定（2026-10-09、平野さん）

* カテゴリの段の「複数選べます」の文言を消す
* ページ上部の説明文「一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム等）の辞書ファイルを公開しています。」の横幅を、メニュー（navbar）の横幅に合わせる
* @IT の2記事へのリンクは消したままでよい
* 使われなくなった `style.css` の `img.dictionary` は今回消す（2026-10-09 の「`style.css` は変えない」の、この1つの規則だけの例外）
* 直したら、プレビューを見ずにマージしてよい（Gboard は画面に出さないまま。Gboard を出すのは平野さんの Android 実機での確認の後）

前提（チャット側。平野さんの決定ではない）

* 「メニューの横幅に合わせる」は、説明文の左右の端を navbar の中身（ロゴ・メニューの並び）の左右の端にそろえる、と読む。PC 幅とスマホ幅の両方で。ステップカードの横幅も同じにそろったほうがよければそろえ、どう解釈して何を変えたかを報告に書く。解釈が2つ以上に分かれて決められないときは、マージせずに両方のスクリーンショットを撮って止まる
* パンくずリストは付けない（リソースのほかのページ〈例: resource_efficiency.html〉と、title/ の入口のページにも無いため。チャット側の確かめ）
* `img.dictionary` を消すとき、`style.css` の中でほかに `.dictionary` を使う規則や、ほかのページの HTML・生成スクリプトに `class="dictionary"` の画像が残っていないことを確かめる。残っていれば消さずに止まる
* 未マージの work/1008-hou などが `style.css` の末尾を変えている。こちらは `img.dictionary` の規則だけを消すので、cloudflare に入れても重ならない見込み。重なれば止まる

手順

1. 確かめる: CHAT-1008-DIC-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-07` を足す。上の決定を `docs/decisions/` に足す（2026-10-09 の「`style.css` は変えない」に、この例外を付ける）。未マージの work/ ブランチが `style.css` の `img.dictionary` の行の付近（前後3行）、または辞書のファイル（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`）を変えていないか確かめる。
2. 直す: 決定の3つを直す。全ページを再生成し、差分を種類に分けて報告する。PC 幅（1280px）とスマホ幅（390px）のスクリーンショットで、説明文と navbar の左右の端の位置（px）を測って報告する。2形式の保存（4つ全部、行数 1,822 の見込み〈「辞書」タブが変わっていれば `dic/*.json` から数え直す〉）と、Gboard が画面に出ていないことを確かめる。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。本番の `resource_dictionary.html`・`.css`・`.js` が 200 で新しい見た目であること、`resource_dictionary_compare.html` が 404 であることを確かめる。#522 に経過をコメントする（Gboard は #515 で後日足すことを書く。#522 は閉じてよいかを報告に書く）。

止まる条件

* CHAT-1008-DIC-06 の状態が「判断待ち」でない
* 未マージの work/ ブランチが上の手順1の場所を変えている
* 前提の「横幅」の解釈が決められない、または `class="dictionary"` がほかで使われている
* 全ページの再生成の差分に、決定とシートの変化で説明できない変更がある（見込み: 辞書ページ・`resource_dictionary.css`・`style.css` の `img.dictionary` の行と、マージ後の自動再生成と同じ種類の変化だけ）
* 保存の行数が見込みと合わない
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522
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
