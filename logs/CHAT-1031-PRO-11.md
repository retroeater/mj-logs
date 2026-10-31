# CHAT-1031-PRO-11

- 着手日時: 2026-10-31
- 対象issue: なし（PRO-06 の続き。指示に issue の指定なし）
- ブランチ: work/1031-pro-nav
- 着手時HEAD: f797a06

## 指示

【Claude作成】Claude Code 向け指示：共通ナビの配色の2点（暗い配色のドロップダウン・404 のナビの地と境の線）を直す（プレビューで止める）
Chat-Ref: CHAT-1031-PRO-11
マージ: 判断待ちで止まる
貼る時機: CHAT-1031-PRO-10（404 の暗い配色）が cloudflare へマージされた後（同じ 404 と `style.css` に触るため）。CHAT-1031-PRO-09 の完了は待たなくてよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-pro-nav を使う（同じ系列のほかの作業ブランチと分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-pro-nav origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-06 の変更（`404.html` の body の class と暗い配色の規則）が origin/cloudflare に入っていることを確かめ、入っていなければ何もせず止まる

## 目的
PRO-06 で見つかった共通ナビの配色の2点を直す。どちらも直す前からの状態で、ナビは全ページで共通なので、ページの種類ごとに見た目を確かめてからマージする。

### 決定（2026-10-31、平野さん）
- 次の2点を今直す（PRO-06 の「判断が必要なこと」への回答）
  - ① 従来の黒いナビのドロップダウンが、暗い配色でも白地のまま
  - ② 明るい配色で `/pro/`・`/houou/` などの下の URL の 404 を開くと、navbar.js が白地のナビにするのに色の変数が無く、ナビの地が透明で境の線が無い

### 前提（チャット側。平野さんの決定ではない。要確認）
- ページの種類（PRO-06 のログと docs/notes/design.md で確かめる）: (A) 新サイトの形のページ（houou/・ouka/・jwrc/・saikyo/・pro/ など。明暗の両方を持つ）(B) 従来のページ（明るい配色だけ。暗い配色の端末でも明るく出る）(C) 濃色固定のページ（帰り道・live/）(D) 404（PRO-06 で明暗の両方を持つようになった）。navbar.js がどの種類でどの形のナビ（黒いナビ・白地のナビ）を描くかを先に表にする
- ①の直し方の案: 暗い配色で地が暗くなるページ（A・D、と C の扱いは実物に合わせる）では、ドロップダウンも暗い地・明るい字にする。B のページは地が明るいままなので、ドロップダウンも今のまま（白地）にする。B でもドロップダウンだけが周りと合っていない箇所があれば、それも直す対象として報告に書く
- ②の直し方の案: 404 のページにも、navbar.js が白地のナビで使う色の変数（新サイトの基本の形の値、docs/notes/design.md）を当て、地と境の線が A のページのナビと同じになるようにする。これは明るい配色の 404 の見た目を変える（意図した変更）。どの URL の 404 で白地のナビになり、どの URL で黒いナビになるかを表にする
- `navbar.js`・`style.css` は全ページに効くので、変えたら種類ごとに代表のページ（A: `/houou/`・`/pro/`、B: `/jpml_pros.html`・`/title/` など実物で B に当たるもの、C: `/live/`・`/wayhome/`、D: `/no-such-page`・`/pro/taro.yamada.99/`）を 360・390px × 明暗で、ナビを閉じた状態とドロップダウンを開いた状態で撮り、直す前と並べて `docs/logs/CHAT-1031-PRO-11/` に置く。①②の対象でないページは画素まで同じであること
- 字とドロップダウンの地のコントラストは、暗い配色で本文 4.5:1 以上（docs/notes/design.md の濃色の基準）
- 新しい文脈のサブエージェントに差分をレビューさせる（CLAUDE.md「判断・作業の原則」）
- 決定は完了時に docs/decisions/ の該当の分野（design-renewal.md など、ナビの決定がある分野）に足す

## 手順
1. ページの種類とナビの形の表、①②が出るページの一覧を作り、直し方をログに書く
2. 直して、上の代表のページを撮り、①②の対象でないページが変わっていないこと・コントラストを確かめる。レビューを受ける
3. ブランチ名の別名 URL でプレビューできる状態にして止まる（プレビューで確かめる URL を報告に書く）

## 止まる条件
- 0章の確かめが通らない
- 前提が実物と食い違い、決定が守れない（どれが・なぜを書いて止まる）
- ①②の対象でないページの見た目が変わる変更が要る（変えずに書いて止まる）
- 未マージの work/ ブランチが `navbar.js`・`style.css` のナビの規則・`404.html` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/eb14de8a.md
