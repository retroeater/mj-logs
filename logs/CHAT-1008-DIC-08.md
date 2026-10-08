# CHAT-1008-DIC-08

- 着手日時: 2026-10-08
- 対象issue: #522
- ブランチ: work/1008-dic
- 着手時HEAD: cd00396f

## 指示

【Claude作成】Claude Code 向け指示：DIC-07 の続き。辞書ページの小さな直し（「複数選べます」を消す・説明文を新しい文言にしてステップカードと同じ幅にする・不要な CSS を消す）をしてマージする
Chat-Ref: CHAT-1008-DIC-08
マージ: 承認済み（チャットで）
貼る時機: いつでも（CHAT-1008-DIC-07 は中断で止まっている）
作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-07 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   あわせて、CHAT-1008-DIC-07 のログの `## 報告` を読み、状態が「中断（エラー）」でなければ何もせず止まる。

## 目的
平野さんが DIC-06 のプレビューを見た。小さな直しを入れて、新しい見た目の辞書ページ（Gboard は画面に出さない）を本番に入れる（#522）。CHAT-1008-DIC-07 は、決定の説明文の文言と位置が実物と食い違ったため止まった（平野さんの文は、今の文言の差し替えのつもりだった）。DIC-07 の決定をこの指示の決定で置き換える。

### 決定（2026-10-09、平野さん）
- カテゴリの段の「複数選べます」の文言を消す
- 辞書ページの説明文（ページ末尾の `.mj-lead`）と META の description を「一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム等）の辞書ファイルを公開しています。」に変える（`scripts/generate_resource_dictionary.py` の `META` と `scripts/apply_page_meta.py` の `PAGES["resource_dictionary.html"]` を同じ文言にする）
- 説明文の位置は今のまま（ページ末尾）
- 説明文の横幅は、ステップカードと同じ幅・位置にそろえる（DIC-07 の測定で PC 296〜984px、スマホ 16〜374px）
- @IT の2記事へのリンクは消したままでよい
- 使われなくなった `style.css` の `img.dictionary` は今回消す（2026-10-09 の「`style.css` は変えない」の、この1つの規則だけの例外）
- 直したら、プレビューを見ずにマージしてよい（Gboard は画面に出さないまま。Gboard を出すのは平野さんの Android 実機での確認の後）

### 前提（チャット側。平野さんの決定ではない）
- 横幅は辞書ページだけで変える（`resource_dictionary.css` で。全ページ共通の `.mj-lead` の規則〈`style.css`〉は変えない）。og:description など description を写している所があれば同じ文言にそろえる
- パンくずリストは付けない（リソースのほかのページ〈例: resource_efficiency.html〉と、title/ の入口のページにも無いため。チャット側の確かめ）
- `img.dictionary` を消すとき、`style.css` の中でほかに `.dictionary` を使う規則や、ほかのページの HTML・生成スクリプトに `class="dictionary"` の画像が残っていないことを確かめる。残っていれば消さずに止まる
- 未マージの work/1008-hou などが `style.css` の末尾を変えている。こちらは `img.dictionary` の規則だけを消すので、cloudflare に入れても重ならない見込み。重なれば止まる

## 手順
1. 確かめる: CHAT-1008-DIC-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1008-DIC-08` を足す（DIC-06 のログの状態の末尾は ` / 続き: CHAT-1008-DIC-07` のままでよい）。上の決定を `docs/decisions/` に足す（2026-10-09 の「`style.css` は変えない」に `img.dictionary` の例外を付ける。DIC-07 が作業ブランチで `docs/decisions/features.md` に足した決定のうち、説明文の横幅の行は「→ 置き換え」を付けてこの指示の決定を足す。DIC-07 の決定がまだ push されていなければ、この指示の決定だけを書く）。未マージの work/ ブランチが `style.css` の `img.dictionary` の行の付近（前後3行）、または辞書のファイル（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/`）を変えていないか確かめる。
2. 直す: 決定のとおり直す。全ページを再生成し、差分を種類に分けて報告する。PC 幅（1280px）とスマホ幅（390px）のスクリーンショットで、説明文とステップカードの左右の端の位置（px）を測って報告する（そろっていなければ止まる）。2形式の保存（4つ全部、行数 1,822 の見込み〈「辞書」タブが変わっていれば `dic/*.json` から数え直す〉）と、Gboard が画面に出ていないことを確かめる。
3. マージ: 「マージ:」の行のとおり cloudflare に入れる。マージ後の Workers Builds と regenerate-page.yml の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回す）。本番の `resource_dictionary.html`・`.css`・`.js` が 200 で新しい見た目であること、`resource_dictionary_compare.html` が 404 であることを確かめる。#522 に経過をコメントする（Gboard は #515 で後日足すことを書く。#522 は閉じてよいかを報告に書く）。

## 止まる条件
- CHAT-1008-DIC-07 の状態が「中断（エラー）」でない
- 未マージの work/ ブランチが上の手順1の場所を変えている
- `class="dictionary"` がほかで使われている
- 全ページの再生成の差分に、決定とシートの変化で説明できない変更がある（見込み: 辞書ページ・`resource_dictionary.css`・`style.css` の `img.dictionary` の行・description を持つ所〈`scripts/apply_page_meta.py` など〉と、マージ後の自動再生成と同じ種類の変化だけ）
- 保存の行数が見込みと合わない
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり。止まる条件に当たったときはマージせずに報告する
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a6988a56）: https://github.com/retroeater/mj-logs/tree/main/guide/a6988a56

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6988a56/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
