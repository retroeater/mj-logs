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

### 0. 着手前

- 「指示」欄の末尾と指示文の最後の行が一致。`git log --all --grep="CHAT-1015-SKD-18"` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- ブランチ: origin/work/1015-skd-top（58adc9d8e、未マージ）を続けて使用。ログ先行 push f79890910（[sync-logs]）の後、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったため `git merge origin/cloudflare` で取り込んだ（725d22144、衝突なし。取り込んだのは docs のみで、`saikyo/index.html` の差は無かった）
- #566・#549 の最新のコメントに他セッションの着手中コメントなし。#566 に着手中のコメント（6182741129）
- 未マージの work/ ブランチ: `assets/saikyo.js`・`scripts/generate_saikyo_pages.py` を変えているものはなし。`style.css` は work/1015-hou（5084〜6356 行付近の houou/ の節）と work/MTK（1504 行付近）が変えているが、この指示の変更（2097 行付近から後の最強戦のトップの節）とは別の箇所

### 1. 直したもの（2087cfe66）

- `assets/saikyo.js` のトップの節: `buildMatches()`（対局ごとの面）を `buildYears()`（年度ごとの面）に置き換えた。年度が変わるたびに見出し `h3.mj-saikyo-n-year-title`（「麻雀最強戦<年度>」と ›）と面 `ol.mj-saikyo-n-match.mj-saikyo-n-rows.mj-saikyo-n-hit-rows` を作り、卓の行（`a.mj-saikyo-n-row.mj-saikyo-n-row-link`）を入れる。行は `.mj-saikyo-n-name` に「対局名 卓名」、その中の2行目 `.mj-saikyo-n-date` に日付（日付の無い対局は出さない）、右端に順位 `.mj-saikyo-n-place`「1位」と `.mj-saikyo-n-pill`、›。行き先は SKD-17 と同じ新しい卓のページ
- **年度の見出しは年度ページ（`<年度>/index.html`）へのリンクにした**（13px 灰色、› 付き、高さ 44px。› は面の中の行の › と縦にそろえた）
- `style.css`: `.mj-saikyo-n-hit-matches` を消し、`.mj-saikyo-n-year-title`（と中のリンク・フォーカス枠）、`.mj-saikyo-n-hit-rows .mj-saikyo-n-row` の上下 8px の余白（「対局名 卓名」が折り返したときに詰まらないよう）を足した。先頭の行の区切り線を消す規則を `.mj-saikyo-n-hit-rows` にも当てた
- 並び: データ（`build_top_data()`）がもともと年度の降順・対局日の降順、対局の中は卓の順なので、生成スクリプトは変えていない。JS だけの変更なので Python のテストは足していない（`python3 -m unittest discover -s scripts/tests`: OK）
- 選手の見出しの行（写真・名前・「N卓」・▾）は SKD-17 のまま

### 2. 生成物の差

`python3 scripts/regenerate.py saikyo_pages` で生成し直した結果、作業ツリーの差は0（生成スクリプトを変えていないため）。origin/cloudflare との差はトップ `saikyo/index.html` だけ（SKD-17 の分）。ほかの saikyo/ のページ・今の年度ページの差は0。新しい CSS の名前（`.mj-saikyo-n-hit-rows`・`.mj-saikyo-n-year-title`）を使う HTML は無い（JS がトップで作るだけ）

### 3. 確かめ（ローカル、headless Chromium）

- トップ × 375/390/430 × 明・暗 × 標準・幅の広い字体2種・150%・200% × 状態5つ（検索の前・1人〈桑田 → 桑田憲汰〉・複数〈鈴木 → 7人、開く前〉・複数で鈴木たろうを開いた後・該当なし）の150通り: 横スクロール・はみ出し・名前と右端／› の重なり・右端の折り返し・44px 未満・transition は0件。年度の見出しのリンクは全通り 44px。ナビは `mj-nav-new`・relative・「最強戦」
- **桑田憲汰の結果の高さ（390px・明）**:

| 字 | SKD-17（対局ごとの面） | SKD-18（年度ごとの面） |
|---|---|---|
| 標準 | 1,423px（10面） | 1,019px（3面） |
| 150% | 1,712px | 1,702px |
| 200% | 2,396px | 3,198px |

  標準では約 28% 短くなった。200% では「対局名 卓名」が2〜3行に折り返すため長くなる
- 状態ごとの高さ（390px・明・標準）: 検索欄は全状態で上端 120px（動かない）。検索の前は歴代最強位 935px、複数（鈴木 7人）の開く前は 392px、鈴木たろうを開いた後は 3,341px、該当なしは状態の1行だけ
- キーボード: 検索の後 Tab で選手の見出し → Enter で開く → Tab で年度の見出し（`2026/index.html`）→ 卓の行、Enter で卓のページ（`2026/20260906-s1-a.html`）へ移動した。Esc で検索を消すと歴代最強位に戻る
- 灰色の字（年度の見出し・日付・順位）は `--mj-n-muted`（明 #555555: 面 7.46:1・地 6.68:1、暗 #aeaeb2: 面 7.69:1・地 9.50:1）
- プレビュー: 2087cfe66 の check-run「Workers Builds: mj」completed/success。別名 URL の `?v=2087cfe` で `saikyo/index.html`・`style.css`・`assets/saikyo.js` がローカルとバイト単位で一致。ブラウザでの見え方はここからは確かめていない

### 4. 記録

- #549 にマージ予定のコメント（https://github.com/retroeater/mj/issues/549#issuecomment-6182877574 ）。本文・状態・ラベルは変えていない
- 決定1〜3は `docs/decisions/design-renewal.md` の「2026-10-15（CHAT-1015-SKD-18）」。文書は `docs/notes/saikyo-page-design.md` の「#549 で入れる新しい形」の検索の結果の項を直した
- SKD-17 のログの状態を「判断待ち / 続き: CHAT-1015-SKD-18」にした
- #549 の公開の直前に origin/cloudflare を取り込み、`python3 scripts/regenerate.py saikyo_pages` で生成し直してから入れる（毎朝の再生成で `saikyo/index.html` が進むため）

## 報告

- 状態: 判断待ち
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: トップの検索欄に「桑田」と入れると、桑田憲汰の見出しの下に灰色の「麻雀最強戦2025 ›」があり、その面の中に「ファイナルトーナメント 決勝」（2行目 2025年12月14日、右端に「1位」と「優勝」）の行がある。面は年度ごとに1枚（URL はターミナルの最終報告）
- マージ: 未（この指示ではマージしない。#549 の公開の指示で一緒にマージする）
- issue: #566（公開は #549）
- 判断が必要なこと: (1) この形でよいか。(2) 年度の見出しを年度ページへのリンク（› 付き）にした。(3) 200% の字では行が折り返して SKD-17 より長くなる（標準の字では約 28% 短い）
- 未確認の項目: 実機（iPhone・PC）のブラウザでの見え方（セッションからは確かめられない）。#549 の公開の直前の取り込みと生成し直し
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
