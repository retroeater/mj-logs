# CHAT-1031-PRO-06

- 着手日時: 2026-10-31
- 対象issue: なし（#641 の関連。指示に issue の指定なし）
- ブランチ: work/1031-pro-404
- 着手時HEAD: 6e783acce

## 指示

【Claude作成】Claude Code 向け指示：404 のページを暗い配色（ダークモード）に対応させる（プレビューで止める）
Chat-Ref: CHAT-1031-PRO-06
マージ: 判断待ちで止まる
貼る時機: いつでも。CHAT-1031-PRO-05 とは別のセッションで並行してよい（作業ブランチを分ける）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-404 を使う（同じ系列の CHAT-1031-PRO-05 の work/1031-pro と並行するため分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-404 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する

## 目的
存在しない URL で出る 404 のページ（`404.html`、`not_found_handling: "404-page"`）が、端末が暗い配色のときも明るい配色のまま出る。新しいページ（houou/・pro/ など）は暗い配色に対応しているので、そこから来た人の画面が急に白くなる。404 のページを暗い配色に対応させる。

### 決定（2026-10-31、平野さん）
- 存在しない選手の URL（例 `/pro/taro.yamada.99/`）で 404 になるのはよいが、ダークモードでもライトモードの色で表示されるので直す

### 前提（チャット側。平野さんの決定ではない）
- `404.html` は手書きの静的なページで、`/style.css` と Bootstrap を読む（要確認）。暗い配色の値は新サイトの基本の形の値（docs/notes/design.md「2. 色」の新サイトの色〈暗い配色〉、houou/ の値）を使い、`@media (prefers-color-scheme: dark)` で切り替える（docs/notes/design.md の「`data-bs-theme` は採用しない」の方針のまま）
- 共通ナビ（navbar.js が描く）も暗い配色で崩れないか確かめる。ナビが houou/ などで暗い配色に対応しているなら、同じ形にする。ナビ自体を変える必要があるなら、変えずに「判断が必要なこと」に書く（全ページに効くため）
- 明るい配色の見た目は変えない（明るい配色の画面の撮影が直す前と同じであること）
- `style.css` に足すなら 404 のページだけに効く形（body の class など）にし、ほかのページに効かないことを確かめる（共有の CSS を変えるときは全ページの再生成は要らないが、規則がほかのページに当たらないことを `git grep` で確かめる）
- 文字のコントラストは、暗い配色で本文 4.5:1 以上（docs/notes/design.md の濃色の基準）
- 画面の確かめ: 360px・390px の明暗で `/pro/taro.yamada.99/` と `/no-such-page` を撮り、直す前と後を並べて `docs/logs/CHAT-1031-PRO-06/` に置く

## 手順
1. `404.html` と共通ナビの暗い配色の扱いを確かめ、直し方をログに書く
2. 直して、明暗・2つの幅で撮影し、明るい配色が直す前と同じこと・暗い配色のコントラストを確かめる。新しい文脈のサブエージェントに差分をレビューさせる
3. ブランチ名の別名 URL でプレビューできる状態にして止まる（プレビューで 404 になる URL を報告に書く）

## 止まる条件
- 前提が実物と食い違い、決定が守れない（どれが・なぜを書いて止まる）
- 明るい配色の見た目が変わる、またはほかのページに効く変更が要る（変えずに書いて止まる）
- 未マージの work/ ブランチが `404.html`・`style.css` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: work/1031-pro-404 はローカル・リモートとも無し → `git checkout -b work/1031-pro-404 origin/cloudflare`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 063f4b64）: https://github.com/retroeater/mj-logs/tree/main/guide/063f4b64

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/063f4b64/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
