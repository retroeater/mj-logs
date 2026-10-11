# CHAT-1011-SWP-15

- 着手日時: 2026-10-11
- 対象issue: #525
- ブランチ: work/1011-swp-mob
- 着手時HEAD: 3afefa883

## 指示

【Claude作成】Claude Code 向け指示：横断レビュー第3弾（#525）— 実機で再現したスマホの崩れを直し、プレビューで止まる
Chat-Ref: CHAT-1011-SWP-15
マージ: 判断待ちで止まる（プレビューを平野さんが iPhone で見てから、別の指示でマージする）
貼る時機: いつでも（新しいセッションに、この指示だけを貼る）
作業ブランチ: クラウドセッションで実行する。work/1011-swp-mob を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-swp-mob origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-swp-mob の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

横断レビューの第3弾（#525）として、iPhone の実機で再現したスマホの崩れを直す。

### 決定（2026-10-11、平野さん）

- 第3弾（#525）に着手する（2026-10-09 の「横断レビューの直しは3段」の3段目）
- iPhone で本番（第2弾〈CHAT-1011-SWP-14〉のマージ後）を見て、次の4件が実機で出ることを確かめた（スクショを平野さんがチャットに貼った。文字の拡大はブラウザの文字サイズの拡大で見た）
  - G2-01: `title/` と `saikyo/` でメニュー（≡）を開くと、ナビが「女流桜花」の次の「JPML」の途中で切れ、その下に大きな白い隙間ができて、その下に絞り込みの固定バーが出る
  - G2-06: `wayhome/` の個別ページ（例 `wayhome/atD2e-NgnKw.html`）で文字を大きくすると、ヒーローの見出しの上の部分がナビの下で切れ、戻るボタンが見出しに重なる
  - G2-07: `rh_results_detail.html` で、着順（例「2321」「4」の2行）・得点（例「13.」「6」）が桁の途中で折り返され、日付が「2026」「-01-」「24」の3行になる
  - G2-11: 文字を大きくすると、`title/` の期のページ（例 `title/ourai/12.html`）の写真カードで順位と名前の文字が顔に大きく重なる。`jpml_pros.html` では表の見出しと本文の文字が隣の列にはみ出して重なる（見出し「所属／出身地」「YouTube」「鳳凰出場」などが重なり、セルの文字が写真に重なる）

### 前提（チャット側。平野さんの決定ではない）

- 対象の指摘は #525 の本文と、CHAT-1006-SWP-01 のログ「### 手順3 統合した指摘の一覧」の G2-01・G2-06・G2-07・G2-09・G2-11 の行（SWP-01 のログが片付けられていれば #525 の本文を正とする）。中身はそちらを正とし、ここには写さない。G2-09（`resource_efficiency` の見出しが本文の左端とそろわない）は実機確認の対象ではないが、#525 に含まれるので一緒に直す
- 直し方の見込み（SWP-01 の「直す先」の欄による。実物に合わせて変えてよい）:
  - G2-01: `style.css` の `title/`・`saikyo/` の開いたメニューの `max-height` が、開いたナビの高さを含む変数で計算される循環。帰り道向けの同じ問題の直し（WH-17）と同じ形で直す。第2弾（SWP-13・14）で入れた固定ナビの開いたメニューの規則と食い違わないようにする
  - G2-06: `.mj-video-hero` の `height` と `overflow: hidden` を、中身に合わせて伸びる形（`min-height` など）にする
  - G2-07: `rh_results_detail` の着順・得点・日付の列だけ折り返さない（`white-space: nowrap` など）。#274 は型A の2列だけが対象で、この表（型A'）は範囲外（CHAT-1009-SWP-03 が #274 にコメント済み）
  - G2-09: `resource_efficiency` の見出しの左の余白を本文とそろえる
  - G2-11: 写真カードの文字の重なりと、`jpml_pros` の表のはみ出しは、直し方で見た目が変わる。直し方が1つに決まり見た目の新しい決定が要らないものは直し、案が2つ以上あるものは、ページ内のラジオボタンで切り替える比較ページにする（docs/notes/chat-side-operations.md「見た目の決め方」。作り方は docs/notes/title-pages.md、比較ページは noindex・どこからもリンクしない・sitemap に載せない）。`jpml_pros` の表は幅が固定（934px）で、表示件数を絞る直しは新サイトの要件（#529、#24 の五十音タブ）なので、現行で小さく直せないなら直さず、理由と案を「判断が必要なこと」に書く
- 文字の拡大の確かめは、ブラウザの文字サイズの拡大に近い手段（ルートの文字サイズを 150%・200% にする近似と、表の `px` 指定の文字も拡大されるか）で行い、手段を書く。実機との差は「未確認の項目」に書く。スマホ幅の計測は docs/notes/cloudflare.md の headless Chromium の注意に従う（`mobile: true` は使わない）
- 修正の検証は、先に「修正前でも通らないか」を確かめる（#310）
- 使う skill は無い

## 手順

1. 確かめる
   - #525 の本文と、他セッションの着手中コメントを確かめ、着手中のコメントを残す
   - `git branch -r --no-merged origin/cloudflare` の各ブランチが、この指示で変える行・関数と同じところを変えていないか、取り込みで衝突しないかを確かめる
   - 5件を今の origin/cloudflare で再現する（幅 390px・360px、文字の拡大は 150%・200%）
2. 直す
   - G2-01・G2-06・G2-07・G2-09 を直す。G2-11 は上の前提のとおり直すか、比較ページにするか、直さずに案を書く
   - 直す前と後で、メニューを開いたときのナビと固定バーの位置・白い隙間の有無（G2-01）、文字の拡大での見出しの切れ（G2-06）、折り返したセルの数（G2-07）、見出しと本文の左端（G2-09）を表にする。直した部品を使うほかのページ（`title/` の入口・大会・期、`saikyo/` の入口・年度、`video_wayhome`、`wayhome/` の個別、`live/` のヒーロー、型A' の `rh_results`）の表示が崩れていないことも確かめる
   - `python3 -m unittest discover -s scripts/tests` を通す
3. プレビューと記録
   - 作業ブランチを push し、プレビュー（check-run「Workers Builds: mj」）で上の表を確かめ直す（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   - 平野さんが iPhone で見る点を、ページ（プレビューのドメインに付けるパス）と見る点の1行ずつで「判断が必要なこと」に書く。文字を大きくして見るものは、そう書く
   - #525 に結果をコメントする（末尾に `Chat-Ref: CHAT-1011-SWP-15`）

## 止まる条件

- #525 に他セッションの着手中コメントがある
- 未マージのブランチが、この指示で変える行・関数と同じところを変えている、または取り込みで衝突する
- 直すために、ナビの位置（#417）・生成スクリプトの表の列の構成・共有ボタンを変える必要が出た
- プレビューで、直した部品を使うページのほかに表示が変わった
- 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-SWP-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-SWP-15 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1011-SWP-15"` に該当なし。識別子 SWP は同じチャットの SWP-01〜14 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- 作業ブランチ: リモート・ローカルとも無かったため `git checkout -b work/1011-swp-mob origin/cloudflare`（上流の設定は外した）。

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1011-swp-mob
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-mob/docs/logs/CHAT-1011-SWP-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-mob
- 確認用URL: 作業中
- マージ: 未
- issue: #525
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3afefa88）: https://github.com/retroeater/mj-logs/tree/main/guide/3afefa88

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3afefa88/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
