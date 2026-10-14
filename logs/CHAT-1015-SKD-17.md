# CHAT-1015-SKD-17

- 着手日時: 2026-10-15
- 対象issue: #566（公開は #549）
- ブランチ: work/1015-skd-top
- 着手時HEAD: 1854428a4

## 指示

【Claude作成】Claude Code 向け指示：最強戦のトップを作り直す（白地のナビ・題名・検索欄・歴代最強位の面の行・検索の結果を年度ページの形に）— 別ブランチでプレビューまで、#549 の公開までマージしない（#566）
Chat-Ref: CHAT-1015-SKD-17
マージ: 判断待ちで止まる（プレビューを見て形を決めたあとも、cloudflare へのマージは #549 の公開の指示で行う。この指示ではマージしない）
貼る時機: いつでも（CHAT-1014-SKD-16 は完了・マージ済み〈d12b1f2e8〉。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skd-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-skd-top を使う（#549 の公開まで残す別ブランチ。work/1014-skd とは分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-skd-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#566 と #549 の最新のコメントを読み、他のセッションの着手中コメントがあれば止まる。

## 目的
#566 の最後の残り、最強戦のトップ `saikyo/index.html`（公開中）を新しい形に作り直す。今のトップは公開中なので、本番には #549（新しい年度・対局・卓のページの公開）と同じ時に入れる。それまでは別ブランチに置き、公開済みのページには影響を出さない。

### 決定（2026-10-14〜15、平野さん。SKD-15 のログ・docs/decisions に記録済みのものを含む）
1. トップは B: 歴代最強位を1枚の面に年度ごとの行（年度・写真の丸・名前・›）で並べる
2. 最強位が決まっていない年度（今の 2026）の行は、名前の欄を空欄にし、写真の丸も出さず、年度と › だけにする
3. 選手名の検索の結果を押したときの行き先は、新しい卓のページ
4. 選手名の検索の結果の画面も、年度ページと同じデザインにそろえる
5. トップの共通ナビは白地にする（2026-10-14）
6. 新しいトップの本番への反映は #549 の公開と同じ時にする。ギリギリまで公開済みのページ（今のトップ・今の年度ページなど）には影響を出さない

### 前提（チャット側。平野さんの決定ではない）
- 作りの元は SKD-14 の比較ページの B（`git show 17939bf92:scripts/build_saikyo_top_compare.py`。SKD-15 のログ `## 経過` 2 の控え）。比較ページのクラス名 `.mj-cmp-*` は本体用の名前（例 `.mj-saikyo-n-*`）に直し、新しい年度ページと同じ部品を使えるところは使う
- 上の部分: 白地のナビ（SKD-12 の `.mj-nav-new` の出し分けをトップにも当てる。今の年度ページ `saikyo/<年度>.html` には当てない）→ 28px 太字の題名「最強戦」→ 1行の選手名の検索欄（SKD-14 の B と同じ）→ 13px 灰色の「歴代最強位」→ 面の行。今の濃色の固定バー（年度のプルダウンと検索欄）は置かない
- 歴代最強位の行の行き先は新しい年度ページ（今の形では `saikyo/<年度>/index.html`。#549 で `/saikyo/<年度>/` で開けるようにするなら、そのときにそろえる）
- 検索の結果（決定4）: 年度ページと同じ部品で出す。選手の名前の見出し（13px 灰色、または写真の丸と名前の行）の下に、出場した対局ごとに1枚の面（対局名の行 16px 太字、日付は2行目 13px 灰色）を年度の新しい順に並べ、その中に出場した卓の行（卓の名前、右端に順位「1位」と「通過」「優勝」のピル〈対局のページと同じ形・色〉）を置く。卓の行を押すと新しい卓のページへ（決定3）。複数の選手が当たったときの出し方（選手ごとに見出しを分ける等）と、検索の前・結果なしの出し方は Code が年度ページの形に合わせて決め、ログに書く
- 変えるのはトップの生成（`scripts/generate_saikyo_pages.py` のトップの関数）・`assets/saikyo.js` のトップの検索・`navbar.js` のトップの出し分け・その CSS だけ。今の年度ページ・新しい年度・対局・卓のページ・live/ は変えない
- ブランチの扱い: この指示ではマージしない。プレビューで形が決まったら、#549 にコメントで「公開の指示で work/1015-skd-top を一緒にマージする（最強戦のトップの作り直し、CHAT-1015-SKD-17）」と書く（本文・状態・ラベルは変えない）。毎朝の再生成（`regenerate-saikyo.yml`）で cloudflare が進むので、#549 の公開の直前に取り込み・生成し直しが要ることもログに書く
- 確かめ（DSN-07 以降の定型）: 375・390・430px × 明・暗 × 標準・幅の広い字体・150%・200%、タップ領域 44px（行・検索欄・結果の行）、灰色の字の条件、部品の状態ごとの高さ（検索の前・結果あり〈1人・複数〉・結果なし）。代表の選手で検索の結果を確かめる（例: 桑田憲汰〈出場の多い選手〉、名前の一部で複数が当たる語）。今の年度ページ・新しい年度ページ・houou/ のナビと見た目の前後比較で差が無いこと
- ログの「確認用URL」の行に、この版で見えるはずの印を書く（例: トップの 2026 の行が年度と › だけ、検索欄に「桑田」と入れると対局ごとの面が出る。URL は書かない）
- 決定を `docs/decisions/design-renewal.md` に記録し（SKD-15 で記録済みのものは重ねない）、`docs/notes/saikyo-page-design.md` のトップの記述を、今の形を残したまま「#549 で入れる新しい形（未マージ、work/1015-skd-top）」として足す

## 手順
1. #566 に着手中のコメントを書く。今のトップの生成・検索・ナビの出し分けを読み、未マージの work/ ブランチが同じ行・同じ関数を変えていないかを確かめる
2. トップを作り直し、テストを足して `python3 -m unittest discover -s scripts/tests` を通し、`python3 scripts/regenerate.py saikyo_pages` で生成し直して差を分類する（差はトップと、トップの CSS・JS・navbar.js だけの見込み。新しい年度・対局・卓のページ、今の年度ページに差が出たら止まる）。上の確かめを行う
3. 決定と文書を直して作業ブランチに push し、Workers Builds のプレビューの成功を check-run で確かめて判断待ちで止まる。ターミナルの最終報告の「確認用:」の行に、トップ `/saikyo/index.html` のプレビューの URL を書く

## 止まる条件
- #566 か #549 に他のセッションの着手中コメントがある
- 生成物の差がトップ以外に出る（シートの変化と X の画像の URL の変化で説明できるものを除く）
- navbar.js の変更で今の年度ページ・houou/ などのナビが変わる
- 未マージの work/ ブランチが、この指示で変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- 作り直した関数・CSS・JS、検索の結果の出し方（複数・結果なし）、生成物の差の種類と件数、確かめの結果、確認用URL の印、決定と文書の記録先がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（この指示ではマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前

- 「指示」欄の末尾と指示文の最後の行が一致。`git log --all --grep="CHAT-1015-SKD-17"` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- ブランチ: work/1015-skd-top はローカルにもリモートにも無かったので `git checkout -b work/1015-skd-top origin/cloudflare`（1854428a4）。ログ先行 push 8db149029（[sync-logs]）
- #566・#549 の最新のコメントに他セッションの着手中コメントなし。#566 に着手中のコメント（6180998275）
- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-wfl-s3・1015-acf-skip・1015-hou・MTK）: `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`navbar.js`・テストを変えているものはなし。`style.css` は work/MTK が 1504 行付近（`.mj-video-episodes-summary` の後）を変えているが、この指示の追加（2093 行付近の最強戦の新しいページの節）とは別の箇所

### 1. 作り直したもの（10e3fa4f3）

- `scripts/generate_saikyo_pages.py`:
  - `build_champions_html()`（写真カードの格子）を `build_champion_rows()` に置き換えた（面の行。行き先 `<年度>/index.html`、最強位が未定の年度は年度と › だけ）
  - `build_top_data()`: 検索のデータを新しいページ向けにした（`m` = [年度, 対局名, 対局日の表示, 新しいページの識別子]、卓 = [m の添字, 卓のページの名前, 卓の見出し, 順位, ピル〈通過・優勝〉]）。対局の中の卓は対局のページと同じ順にし、使わなくなった `stage_desc()` を消した。最強位の名前（`c`）は JS が使っていなかったので外した。データは 170,107 → 165,968 バイト
  - `build_top_body()`: 濃色の固定バーをやめ、題名「最強戦」（`.mj-saikyo-n-page-title`）・検索欄・「歴代最強位」・面の行。本文のクラスは `.mj-saikyo-n-top`（今の `.mj-saikyo-top` の固定バー用の CSS・JS が当たらないよう名前を分けた）
  - `write_top()`: `<body class="mj-saikyo-n-page">`（`add_body_class()` の `mj-saikyo-page` は付けない）。ナビの出し分けは `navbar.js` の `mj-saikyo-n-page` の条件（SKD-12）がそのまま当たるので、**`navbar.js` は変えていない**
  - `build_avatar_html()` に代替画像の引数を足した（トップは `../img/avatar.svg`、新しいページは今のまま `../../img/avatar.svg`）
- `assets/saikyo.js` のトップの節を書き直した（年度プルダウン・固定バーの高さの処理はトップから外した。今の年度ページの処理は変えていない）
- `style.css`: 最強戦の新しいページの節の後に「トップ」の節を足した（検索欄・状態の行・「歴代最強位」・押せる行・年度・検索の結果の見出しの行・▾）。行・面・対局名の行・ピル・順位は年度ページの部品（`.mj-saikyo-n-row`・`.mj-saikyo-n-match`・`.mj-saikyo-n-title*`・`.mj-saikyo-n-pill`・`.mj-saikyo-n-place`）をそのまま使う。今のトップだけの CSS は残した（title/ のコメントが値の写し元として参照しているため、#549 の後に整理）
- テスト `TopTest` 3件（題名・検索欄・固定バーなし・面の行、未定の年度の行、検索のデータの行き先と順位・ピル）。3件とも修正前のコードで失敗し、修正後は全件 OK（`python3 -m unittest discover -s scripts/tests`: 1114件 OK）

### 2. 検索の結果の出し方（Code が決めたこと）

- 選手ごとに見出しの行（写真 40px・名前 16px 太字・右に「N卓」13px 灰色・▾）を置き、押すとその下に対局の面が開く。**1人だけ当たったときは開いた状態**、複数のときは見出しの行だけを並べ、一度に開けるのは1人（今のトップと同じ、#410）
- 対局の面は年度ページと同じ: 対局名の行（16px 太字、2行目に日付 13px 灰色、対局のページへ、›）＋出場した卓の行（56px、卓の名前・右端に順位「1位」13px 灰色と通過・優勝の金のピル・›、新しい卓のページへ）。並びは年度の新しい順・対局日の新しい順、対局の中の卓は対局のページと同じ順（今のトップは決勝→ベスト8 の順だったが、面の中は対局のページにそろえた）
- 検索の前: 歴代最強位を出し、結果は隠す。結果なし: 歴代最強位を隠し、「該当する選手はいません」（13px 灰色）だけ。件数は出さない（今と同じ）
- 行き先: 卓の行は `<年度>/<卓のページ>.html`、対局名の行は `<年度>/<対局>.html`。生成した 219 対局・2,572 件の卓の行・16 年度の行の行き先のファイルがすべてあることを確かめた

### 3. 生成物の差（`python3 scripts/regenerate.py saikyo_pages`）

| 種類 | 件数 |
|---|---|
| トップ `saikyo/index.html` | 1 |
| 今の年度ページ・新しい年度・対局・卓のページ・ほか | 0 |

ほかは `assets/saikyo.js`・`style.css`・`scripts/generate_saikyo_pages.py`・テスト・docs

### 4. 確かめ（ローカル、headless Chromium）

- トップ × 375/390/430 × 明・暗 × 標準・幅の広い字体2種・150%・200% × 状態5つ（検索の前・1人〈桑田 → 桑田憲汰、11卓・10対局〉・複数〈佐々木 → 佐々木寿人・佐々木信也〉・複数で1人を開いた・該当なし）の150通り: 横スクロール・はみ出し・名前と右端の重なり・右端の折り返し・44px 未満・transition は0件。ナビは全通り `mj-nav-new`・relative・今いる項目「最強戦」。濃色の固定バーなし
- 状態ごとの高さ（390px・明・標準）: 検索欄は全状態で上端 120px・高さ 44px（動かない）。検索の前は歴代最強位 935px（1画面に 11 年度）、1人は結果 1,423px、複数（2人）は 112px、複数で佐々木寿人を開くと 4,324px（28 対局）、該当なしは結果なしで状態の1行だけ
- 灰色の字は `--mj-n-muted`（明 #555555: 面 7.46:1・地 6.68:1、暗 #aeaeb2: 面 7.69:1・地 9.50:1）
- キーボード: Tab で スキップ → ナビ → 検索欄 → 歴代最強位の行。`/` で検索欄へ、Esc で消して歴代最強位に戻る。検索の後 Tab で選手の見出し、Enter で開き、Tab で対局名の行 → 卓の行、Enter で卓のページ（`2026/20260906-s1-a.html`）へ移動した
- 前後比較（HEAD の `git archive` と作業ツリー、375・1280px × 明・暗、全ページのスクリーンショット）: 今の年度ページ `saikyo/2025.html`・新しい年度ページ・対局のページ・卓のページ・houou/ トップ・houou/race・live/ の個別ページ。1回目で houou/race（3通り）と新しい年度ページ（1通り）に差が出たが、同じ版どうしを2回撮っても値が変わる（画像の読み込みの揺れ）ことを確かめ、読み込みの完了を待って撮り直すと前後で一致した
- プレビュー: 10e3fa4f3 の check-run「Workers Builds: mj」completed/success。別名 URL の `?v=10e3fa4` で `saikyo/index.html`・`style.css`・`assets/saikyo.js` がローカルとバイト単位で一致。ブラウザでの見え方はここからは確かめていない

### 5. ブランチの扱い

- この指示ではマージしない。#549 へのコメント（「公開の指示で work/1015-skd-top を一緒にマージする」）は、プレビューで形が決まってから書く（まだ決まっていないので書いていない）
- 毎朝の再生成（`regenerate-saikyo.yml`）で cloudflare の `saikyo/index.html` が進むので、#549 の公開の直前に origin/cloudflare を取り込み、`python3 scripts/regenerate.py saikyo_pages` で生成し直す（トップの衝突は生成し直しで解ける）
- 決定: `docs/decisions/design-renewal.md` の「2026-10-15（CHAT-1015-SKD-17）」（新しい決定なし、SKD-14・SKD-15 の節を指す）。文書: `docs/notes/saikyo-page-design.md` の「トップ」の節に「#549 で入れる新しい形（未マージ、work/1015-skd-top）」を足した（今の形の記述は残した）

## 報告

- 状態: 判断待ち
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: トップの上に白地のナビと 28px の「最強戦」、虫眼鏡の検索欄があり、濃色の固定バーが無い。歴代最強位は面の行で、2026 の行は年度と › だけ。検索欄に「桑田」と入れると桑田憲汰の見出しの下に対局ごとの面（例「ファイナルトーナメント／2025年12月14日」の中に「決勝 1位 優勝」）が出る（URL はターミナルの最終報告）
- マージ: 未（この指示ではマージしない。#549 の公開の指示で一緒にマージする）
- issue: #566（公開は #549）
- 判断が必要なこと: (1) この形でよいか（よければ #549 にマージ予定のコメントを書く）。(2) 検索の結果の出し方: 選手ごとの見出しの行を押して開く形（1人なら開いた状態）、対局の中の卓の順を対局のページと同じにした。(3) 日付の無い対局の面の2行目は「麻雀最強戦<年度>」にした
- 未確認の項目: 実機（iPhone・PC）のブラウザでの見え方（セッションからは確かめられない）。#549 の公開の直前の取り込みと生成し直し
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f0aad707）: https://github.com/retroeater/mj-logs/tree/main/guide/f0aad707

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f0aad707/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d12b1f2e.md
