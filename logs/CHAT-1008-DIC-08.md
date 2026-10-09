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

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- CHAT-1008-DIC-07 の `## 報告` の状態は「中断（エラー）」
- `git log --all --grep="CHAT-1008-DIC-08"` は0件
- ローカルの work/1008-dic は origin/work/1008-dic と同じ（cd00396f）。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽（cloudflare に CHAT-1009-SWP-03 の docs の3コミット）だったため、ログを push した後に `git merge origin/cloudflare` で取り込んだ。衝突なし（入ったのは docs/ だけ。`docs/notes/design.md` が新しく入り、「説明文 `.mj-lead`…表・グラフのページでは表／SVG の幅に揃える」とあり、この指示の横幅の決定と同じ向き）

### 手順1（確かめ）

- DIC-07 のログの状態に ` / 続き: CHAT-1008-DIC-08` を足した
- `docs/decisions/features.md`: DIC-07 の決定は push 済みだったので、説明文の横幅の行に「→ 置き換え: 2026-10-09（CHAT-1008-DIC-08）」を付け、DIC-08 の決定を足した。「`style.css` に入れない」（DIC-04）の行の `img.dictionary` の例外は DIC-07 で付けてあり、そのまま
- 未マージの work/ ブランチ: 辞書のファイルを変えているものは無い。`style.css` の変更は work/1008-hou が 3540 行の後、work/1009-nen が 2379 行以降、work/1009-nen-year が 2224 行以降で、`img.dictionary`（91〜94 行、前後3行を含め 88〜97 行）とは離れている
- `.dictionary`: `style.css` の `img.dictionary` 以外に規則は無く、HTML・JS・生成スクリプトに `class="dictionary"` は無い（`git grep`）

### 手順2（直す）

- 「複数選べます」（`.mj-dic-hint`）を消し、使わなくなった `.mj-dic-hint` の CSS も消した
- 説明文: `generate_resource_dictionary.py` の `META` と `apply_page_meta.py` の `PAGES["resource_dictionary.html"]` を「一般的な麻雀用語、および日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム等）の辞書ファイルを公開しています。」にした。生成されたページの `meta description`・`og:description`・`.mj-lead` の3か所が新しい文言になる。`python3 scripts/apply_page_meta.py --dry` で辞書ページの書き換えが出ないこと（生成スクリプトと同じ文言）を確かめた
  - `llms.txt` の辞書の行（「麻雀プロの名前および麻雀用語の辞書ファイル（Microsoft IME・Google日本語入力）。」）は description の写しでなく要約のため変えていない（判断が必要なことに書いた）
- 横幅: `resource_dictionary.css` に `.mj-dic ~ .mj-lead { width: min(688px, 100% - 32px); max-width: none; margin: 0 auto; }` を足した（全ページ共通の `style.css` の `.mj-lead` は変えていない）
- 色: 説明文は灰色の地（#f1f3f5、DIC-06 からのページの地）の上にあり、共通の文字色 #555555 では 6.70:1 で、このサイトの AAA（7:1）に届かなかった（DIC-06 の版から）。辞書ページだけ #495057（7.35:1）にした。決定には無い変更
- `style.css` の `img.dictionary`（4行と空行）を消した

全ページの再生成（`python3 scripts/regenerate.py all`、1分32秒、エラーなし）の差分は `resource_dictionary.html` だけ（description・og:description・`.mj-lead` の文言、「複数選べます」の行）。`dic/` に変化なし。

測定（ローカルの Chromium、要素の左右の端）:

| 幅 | ステップカード | 説明文 `.mj-lead` |
|---|---|---|
| PC 1280px | 296〜984 | 296〜984 |
| スマホ 390px | 16〜374 | 16〜374 |

保存（スマホ幅、4つ全部）: Microsoft IME 1,822 行（BOM 付き UTF-16LE・CR+LF・3列）、Google 日本語入力 1,822 行（UTF-8・LF・4列）。見込み（`dic/*.json` から重複をまとめて数えた数）1,822 と一致、重複0。形式は `msime,google` の2つで、本文に「Gboard」は無い。「複数選べます」も無い。テストは OK

### マージ

- push 直前に再 fetch し、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1008-dic:cloudflare`（a6988a56..46a292e7）
- 46a292e7 の check-run: Workers Builds: mj success、Workers Builds: mj-scheduler success、regenerate success、sync success、check success（2件）
- regenerate-page.yml が 0e33fde4（`chore: regenerate resource_dictionary.html dic/ via GitHub Actions`）を push。中身は `sitemap-pages.xml`・`sitemap-wayhome.xml` の lastmod だけ
- 本番（`https://ryoei.pro/`）: `resource_dictionary.html`・`resource_dictionary.css`・`resource_dictionary.js` は 200、`resource_dictionary_compare.html` は 404。HTML は新しい見た目（`mj-dic-form`）と新しい説明文で、「Gboard」「複数選べます」は無い。`style.css` に `img.dictionary` は無い
- 本番を Chromium で開いて測定: 説明文とステップカードは PC 296〜984・スマホ 16〜374 で一致。4つ全部の保存は Microsoft IME・Google 日本語入力とも 1,822 行。ブラウザ（実機）での見え方は確かめていない
- #522 に経過をコメントした

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-09
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1008-DIC-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューは見ていない（決定のとおり）。本番で確かめた（経過の「マージ」）
- マージ: 済（46a292e7）
- issue: #522・#515
- 判断が必要なこと:
  - `llms.txt` の辞書の行は「麻雀プロの名前および麻雀用語の辞書ファイル（Microsoft IME・Google日本語入力）。」のまま。新しい説明文に合わせるなら案は「一般的な麻雀用語と、日本プロ麻雀連盟・Mリーグ関連の用語（タイトル戦・選手・チーム等）の辞書ファイル（Microsoft IME・Google日本語入力）。」
  - 説明文の文字色を辞書ページだけ #495057 にした（灰色の地で共通の #555555 は 6.70:1 のため）。決定に無い変更
  - #522 は閉じてよい（見た目の刷新は本番に入った。Gboard は #515 で後日足す）。閉じるかは平野さんの判断
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f13efe1c）: https://github.com/retroeater/mj-logs/tree/main/guide/f13efe1c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13efe1c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
