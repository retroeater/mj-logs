# CHAT-1013-WHS-21

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-whs-nav
- 着手時HEAD: c26b1949

## 指示

【Claude作成】Claude Code 向け指示：帰り道の各話の見た目の直し3点 — 前後のエピソードの並びを入れ替える（左に「次」）／前後のエピソードに日付を足す／画面最下部の説明文（.mj-lead）を各話から消し、一覧では画面幅で折り返す。作業ブランチでプレビューまで作り、判断待ちで止まる Chat-Ref: CHAT-1013-WHS-21 マージ: 未承認（プレビューを平野さんが見てから、次の指示で決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-whs-nav を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-whs-nav origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが本番の各話（`/wayhome/Yjp_PKHvldw.html`）を見て気づいた3点を直す。
決定（2026-10-13、平野さん）

1. 前後のエピソードの並び: 下の「〇〇の他のエピソード」が左ほど新しいので、それに合わせて**左に「次のエピソード」（新しい回）、右に「前のエピソード」（古い回）**にする。見出しの言葉（「次のエピソード」「前のエピソード」）は変えない
2. 前後のエピソードに日付を足す: 「次のエピソード」「前のエピソード」の文字のすぐ下に、その回の公開日を入れる
3. 画面最下部の説明文（`.mj-lead`）: 各話（`wayhome/*.html`）からは消す（ヒーローの紹介文とまったく同じ文のため）。meta description・og:description・`VideoObject.description` はそのまま。一覧（`video_wayhome.html`）は残し、今は中途半端な幅で折り返しているので、画面幅で折り返すようにする
4. プレビューを見てからマージを決める

前提（チャット側。平野さんの決定ではない）

* 今の作り（cloudflare、2026-10-13）: 各話の前後のエピソードは `<nav class="mj-video-episodes mj-video-prevnext">` に、左に前（古い回）、右に次（新しい回）の順で並ぶ（`scripts/generate_wayhome_episodes.py`）。下の「他のエピソード」のカードは新しい順で、日付は `YYYY-MM-DD`（`lib/wayhome.py` の `published_date_text()`）
* 日付の書き方は、下のカードと同じ `YYYY-MM-DD` を案とする（ヒーローの「2026年10月11日公開」の書き方にする案もある。違う書き方を選ぶなら理由をログに書く）。見た目（文字の大きさ・色）は下のカードの日付に合わせる
* スマホ幅で前後のエピソードが縦に積まれるなら、上が「次」（新しい回）、下が「前」になる。読み上げの順（DOM の順）も見た目の順と同じにする（CSS の `order` だけで入れ替えない）
* 最新話には「次」が無く、最古の回には「前」が無い。片方だけのときの置き方（今の作りのまま左右どちらに寄るか）を確かめ、プレビューに含める
* `.mj-lead` は `style.css` で `max-width: 720px`（#158・#312）。一覧の説明文は `<main>` の外にあり、`docs/notes/mj-lead.md` では帰り道は「対象外、720px のまま」とされている。一覧では、上の部品（カードの並びなど）と同じ幅・左端にそろえる案を出す（`docs/decisions/houou.md` の「ページ下の説明文の幅は、その上の部品の幅に合わせる」と同じ考え方。帰り道は濃色の別系統なので、`body:has(.mj-video-page)` の範囲で書く）。ほかのページの `.mj-lead` は変えない
* 各話から `.mj-lead` を消すと、`docs/notes/video-wayhome.md` の「`VideoObject` の `description` と `.mj-lead` が同一文言」（thin content の記述）や `docs/notes/mj-lead.md` の対象外の行が古くなる。直す
* テスト（`scripts/tests/`）に前後のエピソードの順・`.mj-lead` の有無を見ているものがあれば直し、無ければ足す（前後の DOM の順、日付の有無、片方だけの回、各話に `.mj-lead` が無いこと、一覧には有ること）

手順

1. 確かめる: 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が `generate_wayhome_episodes.py`・`generate_video_wayhome.py`・`lib/wayhome.py`・`style.css` の帰り道の部分を変えていれば止まる。「前提」の今の作りの読みと実物が食い違えば止まる
2. 作る: 決定の 1〜3 を実装する。`python3 -m unittest discover -s scripts/tests` が通ること。帰り道の2ページを生成し直し、差が今回の3点で説明できるものだけであること（帰り道以外の生成物は変わらない。`style.css` の変更が帰り道以外の `.mj-lead` に効いていないこと）
3. プレビューを用意して止まる: 作業ブランチを push し、Workers Builds のプレビューで `/wayhome/Yjp_PKHvldw.html`（前後とも有る回）・最新話（「次」が無い回）・最古の回（「前」が無い回）・`/video_wayhome.html` のスマホ幅（390px）とパソコン幅（1280px）のスクリーンショットを撮り、見え方をログに書く。横スクロールが出ないこと。プレビューの URL はターミナルへの最終報告にだけ書く。決定を `docs/decisions/wayhome.md` に足す。状態は判断待ち（マージの判断）で止まる

止まる条件

* 手順1で、未マージのブランチと重なる、または今の作りの読みが実物と食い違う
* テストが落ちる。帰り道以外の生成物が変わる。帰り道以外のページの `.mj-lead` の見え方が変わる
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち。プレビューで見るページの一覧、日付の書き方と一覧の説明文の幅で選んだ案、平野さんに選んでもらう点を書く）
* マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-21"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- 作業ブランチ: `work/1013-whs-nav` はローカル・リモートとも無し → `git checkout -b work/1013-whs-nav origin/cloudflare`（c26b1949）

### 手順1 確かめ

- 未マージのブランチ: `work/1012-dsn` だけが `style.css` を変えている（1151行の追記）。差分の塊は末尾の1つで、帰り道（`mj-video`・`wayhome`）に触れる行は無い（`.mj-lead` は `body:has(.mj-houou, .mj-houou-race)` の範囲だけ）。ほかのブランチは対象の4ファイルに差分なし → 重なりなし
- 今の作り: `generate_wayhome_episodes.py` は `prev_row`（古い回）→ `next_row`（新しい回）の順に `.mj-video-prevnext-link` を並べ、`.mj-video-prevnext` は `flex-wrap`・各リンク `flex: 1 1 240px`（片方だけなら全幅）。下のカードは新しい順・`YYYY-MM-DD`。`.mj-lead` は `lib/page.py` の `render_content()` が `<main>` の後に必ず出し、一覧・各話とも `max-width: 720px`・左寄せ。前提と一致
- 気づいたこと: シートで「第6期鸞和戦」の #1・#2 が入れ替わっていた（`Yjp_PKHvldw` が #1、`OkKAh8yP8Uk` が #2）。本番の生成物もその並びで、今回の直しとは関係なし。最新話は `OkKAh8yP8Uk`、最古は `mj3hZ_udzKM`（第4期鸞和戦）

### 手順2 作る

- 1・2（`generate_wayhome_episodes.py`）: 次のエピソード → 前のエピソードの順に DOM へ並べた（CSS の `order` は使わない）。方向の文字の直後に `<span class="mj-video-prevnext-date">YYYY-MM-DD</span>`（`published_date_text()`）。`style.css` に `.mj-video-prevnext-date`（0.75rem・`--mj-v-fg-muted`、下のカードの日付と同じ）
- 3（各話）: `lib/page.py` の `render_content()` に `has_lead`（既定 True、ほかのページの出力は変わらない）を足し、各話は `has_lead=False`。meta description・og:description・`VideoObject.description` はそのまま
- 3（一覧）: `body:has(.mj-video-list) .mj-lead` を `max-width: none`・`margin-inline: calc(50% - 50vw)`・`padding: 12px clamp(12px, 3vw, 48px) 18px`。上のカードの並び（`.mj-fullbleed`）と同じ指定で、左右の端がそろう（最初は margin を付けず、スクロールバーの幅の半分〈約7px〉だけ右にずれたので足した）。`.mj-video-list` は `video_wayhome.html` にしか無い
- テスト: `scripts/tests/test_wayhome_prevnext.py`（6件: 次が先・日付が方向の直後・片方だけの回・各話に `.mj-lead` が無く meta は残る・一覧には有る・`render_content()` の既定は出す）。HEAD のスクリプトを一時ディレクトリに展開して流すと、直す前では4件が落ちる（片方だけの回・一覧に有るは直す前でも通る回帰の防ぎ）。全体 965件 OK
- 生成し直し: 帰り道の各話42枚（前後の並び・日付・`.mj-lead` の削除）。`video_wayhome.html` の HTML は変わらない（CSS だけ）
- 共有の関数（`render_content()`）を変えたので、`python3 scripts/regenerate.py all` で全ページを生成し直した: 作業ツリーに差なし（帰り道以外の生成物は変わらない）
- 文書: `docs/notes/video-wayhome.md` に節「前後のエピソードの並び・日付と、各話の説明文（2026-10-13）」、thin content の記述に「→ 各話の .mj-lead は消した」。`docs/notes/mj-lead.md` の対象外の行を、一覧は `none`・各話は出さない、に直した。決定を `docs/decisions/wayhome.md` に足した
- コミット: d2d1948a（実装・文書）・生成・c553dab0（一覧の説明文の端）・45775771（決定）

### 手順3 プレビュー（Workers Builds: mj success、c553dab0）

Playwright（Chromium）でスマホ幅 390px・パソコン幅 1280px の全体のスクリーンショット。どのページも横スクロールなし（`scrollWidth` ≦ 幅）。

- `/wayhome/Yjp_PKHvldw.html`（前後とも有る回）: パソコン幅は左に「次のエピソード / 2026-10-11 / 第6期鸞和戦 #2 白鳥翔」、右に「前のエピソード / 2026-09-21 / 第16期麻雀グランプリMAX 紺野真太郎」（各 496px）。スマホ幅は上が次・下が前（各 351px）。日付は方向の文字のすぐ下で、下の「白鳥翔の他のエピソード」のカードの日付と同じ形。ページ末尾の説明文は無い
- `/wayhome/OkKAh8yP8Uk.html`（最新話、「次」が無い回）: 「前のエピソード / 2026-10-11 / 第6期鸞和戦 #1 白鳥翔」が全幅（1004px）
- `/wayhome/mj3hZ_udzKM.html`（最古の回、「前」が無い回）: 「次のエピソード / 2024-05-21 / 第32期麻雀マスターズ 本田朋広」が全幅
- `/video_wayhome.html`: 説明文の文字の左右の端が、上のカードの並びと同じ（390px: 5〜371px、1280px: 31〜1234px）。パソコン幅は1行、スマホ幅は4行で折り返す。境界線は画面の端から端まで


## 報告

- 状態: 判断待ち（マージの判断）
- ブランチ: work/1013-whs-nav
- ログ: https://github.com/retroeater/mj/blob/work/1013-whs-nav/docs/logs/CHAT-1013-WHS-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-nav
- 確認用URL: プレビューあり（URL は最終報告）。見るページ: `/wayhome/Yjp_PKHvldw.html`（前後とも有る）・`/wayhome/OkKAh8yP8Uk.html`（最新話）・`/wayhome/mj3hZ_udzKM.html`（最古の回）・`/video_wayhome.html`
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - マージするか（プレビューを見て）
  - 日付の書き方: 下のカードと同じ `YYYY-MM-DD` にした。ヒーローと同じ「2026年10月11日公開」の書き方にするか
  - 一覧の説明文の幅: 上のカードの並びと同じ左右の端で、画面幅いっぱいに折り返す形にした（境界線も端から端まで）。このままでよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cb0173b5）: https://github.com/retroeater/mj-logs/tree/main/guide/cb0173b5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cb0173b5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
