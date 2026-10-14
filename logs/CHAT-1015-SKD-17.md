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

## 報告

- 状態: 作業中
- ブランチ: work/1015-skd-top
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-top/docs/logs/CHAT-1015-SKD-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-skd-top
- 確認用URL: なし
- マージ: 未
- issue: #566
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1854428a）: https://github.com/retroeater/mj-logs/tree/main/guide/1854428a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1854428a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d12b1f2e.md
