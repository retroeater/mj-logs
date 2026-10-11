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


### 手順1 確かめる

- #525 の本文を確かめた。コメントは無く、他セッションの「着手中」も無い。#525 に着手中のコメントを残した。
- 未マージのブランチの重なり（`git diff --name-only origin/cloudflare...<ブランチ>` と変更の場所）:

| ブランチ | この作業のファイル | 変更の場所 | 重なり |
|---|---|---|---|
| `origin/work/1011-abm`（#425、最強戦の試作） | `style.css`・`assets/saikyo.js` | `style.css` 1704行付近（`body:has(.mj-saikyo) .mj-lead` の後に追加）、`saikyo.js` 99〜103行付近（`bindYearSelect` の後） | なし（この作業は `style.css` の 113・763・950・1917・2270行付近と、`saikyo.js` の `trackBarHeights` の中を変える） |
| `origin/work/1011-hou`（#518） | `style.css` | 3889行付近〜（houou の部品） | なし |
| `origin/work/1009-nen`（古いブランチ、SWP-08 で記載済み） | `style.css`・`assets/title.js` | `.mj-tl-bar`（年表）と `title.js` の 320行以降 | なし（`title.js` の `setHeights` は35〜50行） |

- 5件を今の `origin/cloudflare`（`git archive` を手元の `http.server` で配信）で再現した。Playwright の Chromium、`viewport` だけ（`mobile: true` は使わない）。外部の画像（X・YouTube）は、遮断すると壊れた画像の代わりの文字でヒーローの中身が伸びて値が狂うため、同じ大きさの灰色の画像を返すようにした（最初の計測は遮断したままで、1280px でも見出しが切れるという誤った値が出た。画像を返すと出ない）。
- 文字の拡大の近似は2通り: **root** = ルートの `font-size` を 150%・200%（`rem`・`em` の文字だけ大きくなる）、**all** = 全要素の計算済みの `font-size` を 1.5・2 倍にして書き込む（`px` 指定の文字も大きくなる。iPhone の Safari の「ぁあ」の文字サイズに近い）。
  - `jpml_pros` の表の 13px は root では変わらず、all でだけ 19.5px・26px になった（表の `px` の文字は、ブラウザの文字サイズの拡大で大きくなる側として扱う）
- **再現した（直す前）:**
  - G2-01: `title/`・`title/ourai/12`・`saikyo/`・`saikyo/2022` の全部で、メニューを開くと `--mj-title-nav-h`／`--mj-saikyo-nav-h` が 575px（開いたナビの高さ）になり、メニューの `max-height` が 269px（390×844）・92px（390×667）・65px（360×640）に縮む。8項目のうち見えるのは 4・1・1 項目。固定バーは y=575 に下がり、ナビとの間に 250・427・454px の隙間。本文の上の余白も 171px→691px に変わる。原因は `--mj-*-nav-h` を更新する ResizeObserver が、開く途中（`.collapsing`）の高さを拾うこと（`.show` だけを除いていた）
  - G2-06: ヒーローが固定の高さ（`height: clamp(420px, 72vh, 640px)`・`overflow: hidden`）で、文字 200% では見出しの上が切れる（系列名がヒーローの上端より `video_wayhome` で 197px・`wayhome/atD2e-NgnKw` で 200px 上、390px）。360px では 150% でも `wayhome/` の個別が 24px 切れ、戻るボタンが見出しに重なる。文字の大きさがふだんのときは、どのページも切れない
  - G2-07: `rh_results_detail` の 390px で、日付 321/321 件が折り返し（最大3行。360px は最大4行）、着順 20/321 件（最大3行。360px は 212 件）、得点 130/321 件（最大2行）
  - G2-09: `resource_efficiency` の見出し（h1）の左端が x=0、本文（`.mj-margin-text`）は x=32。見出しの上はナビの直下で余白 0
  - G2-11: `title/ourai/12` の写真カードは、ふだんの大きさでも順位の帯と名前が写真の 54% に重なる（作りどおり）。all 1.5 で 69〜92%、all 2 で 84〜114%（文字が写真からはみ出す）。root では帯・名前の字（`px` と `cqi` の指定）は大きくならない。`jpml_pros` は all 2 で見出し 14 列のうち 2 列（「所属出身地」「YouTube」）の字が枠からはみ出し、表の幅は 934px のまま

### 手順2 直す

変えたファイル: `style.css`・`assets/title.js`・`assets/saikyo.js`、比較ページ `title_card_compare.html`（新規）。生成スクリプト・`navbar.js`・共有ボタンは変えていない。ナビの位置（#417）も変えていない。

| 指摘 | 直し方 |
|---|---|
| G2-01 | `title/`・`saikyo/` の開いたメニューの `max-height` を、ナビの実測を使わない `calc(100dvh - 60px)`（`100vh` を先に書く）にした。第2弾の固定ナビの型A（`body:has(.mj-table)`）と同じ値。`assets/title.js`・`assets/saikyo.js` の高さの実測で、`.navbar-collapse.show` に加えて `.navbar-collapse.collapsing`（開閉の途中）のときも測らないようにした |
| G2-06 | `.mj-video-hero` の `height` を `min-height`（値は同じ `clamp(420px, 72vh, 640px)`）にし、中身が収まらないときは伸びるようにした。戻るボタンのあるヒーロー（`wayhome/` の個別）は、中身の上にボタンの分の余白（`clamp(12px, 3vw, 24px) + 44px + 8px`）をとる。ふだんの文字の大きさでは中身が下端に寄るため見た目は変わらない |
| G2-07 | `#rh_results_detail_table` の日付の列は「-」でだけ折り返す（`overflow-wrap: normal`。「2026-」「01-24」の2行）。得点は折り返さない（`white-space: nowrap`）。着順は `min-width: calc(5ch + 8px)` で5桁まで1行にし、6桁以上の4件だけ折り返す。最初は着順も折り返さない形にしたが、9桁の1件のために列が 73px に広がり、対局の列が1行2字ほどに縮んで表の長さが 27,448→40,448px（390px）になったため改めた |
| G2-09 | `.mj-page-heading` に `margin: 24px 32px 0.5rem`（本文の `.mj-margin-text` の左右 32px・上 24px にそろえる）。使っているのは `resource_efficiency` だけ。生成スクリプトは変えていない |
| G2-11 写真カード | 直し方で見た目が変わるため、比較ページ `/title_card_compare.html` を作った（noindex・どこからもリンクしない・sitemap に載せない）。案: 今の形・A いつも写真の外（帯を写真の上、名前を写真の下。ふだんの大きさでもカードが縦に長くなる）・C 文字が大きいときだけ写真の外（帯と名前の高さの合計が写真の高さの7割を超えたら A の形。JS で測る。ふだんの大きさでは今の形のまま）。カードは `title/ourai/12` の4枚と `title/` の入口の8枚の写し |
| G2-11 `jpml_pros` | **直さない。** 表は 934px 固定（列の幅を px で決めている）で、文字だけが大きくなると列からはみ出す。現行で小さく直す案はどれも見た目の新しい決定が要る。案は「判断が必要なこと」 |

`python3 -m unittest discover -s scripts/tests` は通った（変更のたびに実行）。

#### 直す前と後（手元の現行と作業ブランチ。プレビューで測り直した値は手元の作業ブランチと全項目で一致、不一致 0）

**G2-01**（メニューを開いた状態。4ページとも同じ値）

| 画面 | メニューの max-height | 見える項目 | ナビの下端 | 固定バーの位置 | ナビとバーの隙間 | 本文の上の余白 |
|---|---|---|---|---|---|---|
| 390×844 | 269px → 784px | 4/8 → 8/8 | 325 → 576px | y=575 → 56（開いたメニューの下に隠れる） | 250px → なし | 開くと 691px → 171px のまま |
| 390×667 | 92px → 607px | 1/8 → 8/8 | 148 → 576px | y=575 → 56 | 427px → なし | 同上 |
| 360×640 | 65px → 580px | 1/8 → 8/8 | 121 → 576px | y=575 → 56 | 454px → なし | 同上 |

閉じた後のナビ 56px・バー y=56・余白 171px は前後とも同じ。

**G2-06**（ヒーローの高さ／最初の文字〈系列名〉の上端がヒーローの上端から何 px 下か〈負は上が切れている〉／戻るボタンと見出しの重なり）

| ページ | 幅 | 文字 | 前 | 後 |
|---|---|---|---|---|
| `video_wayhome` | 390 | ふだん | 608px／340 | 608px／340（同じ） |
| `video_wayhome` | 390 | all 2 | 608px／**−197** | 824px／20 |
| `video_wayhome` | 360 | all 1.5 | 461px／13 | 466px／18（5px 伸びた） |
| `video_wayhome` | 360 | all 2 | 461px／**−342** | 821px／18 |
| `wayhome/atD2e-NgnKw` | 390 | ふだん | 608px／325／重ならない | 同じ |
| `wayhome/atD2e-NgnKw` | 390 | all 2 | 608px／**−200**／**重なる** | 872px／64／重ならない |
| `wayhome/atD2e-NgnKw` | 360 | all 1.5 | 461px／**−24**／**重なる** | 549px／64／重ならない |
| `wayhome/atD2e-NgnKw` | 360 | all 2 | 461px／**−444** | 969px／64 |
| `live/` | 390 | ふだん〜all 2 | 608px／切れない | 同じ |
| `live/` | 360 | all 2 | 461px／15 | 464px／18（3px 伸びた。切れてはいなかった） |

root 150%・200% も同じ値（root と all で同じ結果）。3ページ×2幅×5通りの30組で、「切れる」は前 10 組 → 後 0 組。ふだんの文字の大きさでは、どのページも前後で同じ。

**G2-07**（折り返したセル／件数。`rh_results_detail`）

| 幅 | 日付 | 着順 | 得点 | 表の幅 | ページの長さ |
|---|---|---|---|---|---|
| 390 | 321（最大3行）→ 321（最大2行、「-」でだけ） | 20（最大3行）→ 4（6桁以上） | 130 → 0 | 390 → 390 | 27,448 → 31,658px |
| 360 | 321（最大4行）→ 321（最大2行） | 212 → 4 | 130 → 0 | 360 → 360 | 33,450 → 42,612px |
| 1280 | 0 → 0 | 0 → 0 | 0 → 0 | 1280 | 同じ |

数字が桁の途中で切れなくなった分、文字の列（団体・対局・対局者）が細くなり、ページが 390px で 15%、360px で 27% 長くなる。

**G2-09**（`resource_efficiency`）: 見出しの左端 x=0 → 32（本文 32）、上端 ナビの直下（余白 0）→ 24px 下（390・1280px）。

**G2-11 比較ページ**（写真に重なる文字の割合の最大／カードの高さ。390px、all の倍率）

| 案 | ふだん | 1.5 倍 | 2 倍 |
|---|---|---|---|
| 今の形 | 54%／175px | 92%／175px | 114%／175px |
| A いつも写真の外 | 0%／251px | 0%／278px | 0%／357px |
| C 大きいときだけ外 | 54%／175px（今の形のまま） | 0%／278px | 0%／357px |

360px も同じ傾向（C はふだん 58%・今の形のまま、1.5 倍から外）。

#### 直した部品を使うほかのページ

`git archive origin/cloudflare` と作業ブランチを、15ページ（`title/`・`title/houou/`・`title/ourai/12`・`saikyo/`・`saikyo/2022`・`video_wayhome`・`wayhome/` の個別2つ・`live/`・`rh_results`・`rh_results_detail`・`resource_efficiency`・`jpml_pros`・`404`・`jpml_test`）× 390・360・1280px の全体画像で比べた（外部の画像は灰色の画像を返す）。

- 差があったのは `rh_results_detail`（G2-07）と `resource_efficiency`（G2-09）だけ。`title/` の入口・大会・期、`saikyo/` の入口、`video_wayhome`・`wayhome/` の個別・`live/`（ふだんの文字の大きさ）、`rh_results`（型A'）は差 0
- `saikyo/2022` に差が出たが、中身は写真カードの写真（遅延読み込みの画像）が出る・出ないの違いで、切り取って見た。この作業の CSS が効く部分ではない

### 手順3 プレビューと記録

- 作業ブランチを push した（`origin/cloudflare` へは push していない）。check-run「Workers Builds: mj」は `completed / success`（2回。最後は `2fb3c528b`）。プレビューの `style.css`・`assets/title.js`・`assets/saikyo.js`・`title_card_compare.html` は作業ブランチと一致（`cmp`）。
- プレビューで G2-01・G2-06・G2-07・G2-09・G2-11 を測り直し、手元の作業ブランチの値と全項目で一致した。G2-01 はプレビューで開閉の前後も確かめた（閉→開→閉で、ナビ 56→576→56px、バー y=56 のまま、本文の上の余白 171px のまま。現行は開くとバー y=576・余白 691px）。
- #525 に結果をコメントした（末尾に `Chat-Ref: CHAT-1011-SWP-15`）。

## 報告

- 状態: 判断待ち（G2-01・G2-06・G2-07・G2-09 を直し、G2-11 の写真カードは比較ページ、`jpml_pros` は直さずに案を書いてプレビューで止まった。マージは別の指示） / 続き: CHAT-1011-SWP-16
- ブランチ: work/1011-swp-mob
- ログ: https://github.com/retroeater/mj/blob/work/1011-swp-mob/docs/logs/CHAT-1011-SWP-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-swp-mob
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは「判断が必要なこと」の表
- マージ: 未（平野さんの判断待ち）
- issue: #525（結果をコメント。閉じていない）。起票なし
- 判断が必要なこと:
  - **G2-11 写真カードの文字の置き場所（今の形・A・C）を決める。** 比較ページ `/title_card_compare.html` を iPhone で開き、「ぁあ」で文字を大きくしてラジオボタンで切り替える。A はいつも写真の外（ふだんもカードが縦に長くなる）、C は文字が大きいときだけ写真の外（ふだんは今の形）。C は JS で測るため、決まったら `assets/title.js` にも入れる（帯と名前の形は `live/` の名前のラベルと共通なので、`live/` にも広げるかも合わせて決める）
  - **`jpml_pros` の表の文字のはみ出し（G2-11）は直していない。** 表は 934px 固定（列の幅を px で決めている）で、文字だけが大きくなると列からはみ出す。案: (a) 列の幅を文字の大きさに比例する単位（em）にする（文字が大きいと表が横に長くなり、横スクロールが増える）、(b) 見出しとセルの文字を列の中で折り返す（行が高くなり、字の途中で切れることがある）、(c) 現行は直さず、新サイト（#529）の選手ページ・五十音タブ（#24）で一覧の作りを変える。表示件数を絞る直しは #529 の要件なので、(c) を勧める
  - G2-07 の直しで、数字が桁の途中で切れなくなった分、文字の列が細くなり `rh_results_detail` のページが長くなる（390px で 27,448→31,658px〈+15%〉、360px で 33,450→42,612px〈+27%〉）。日付は「2026-」「01-24」の2行。着順は6桁以上の4件だけ折り返す。これでよいか
  - 平野さんが iPhone で見る点（URL は最終報告の「確認用」。以下はそのドメインに付けるパス）:
    - `/title/`・`/saikyo/`（ほかに `/title/ourai/12.html`・`/saikyo/2022.html`）: ≡ を押して、メニューの全項目が見え、下の項目までスクロールでき、ナビと固定バーの間に白い隙間が出ないか。閉じた後に本文の位置が戻るか（G2-01）
    - `/wayhome/atD2e-NgnKw.html`・`/video_wayhome.html`: **「ぁあ」で文字を大きくして**、ヒーローの見出しの上が切れず、戻るボタンが見出しに重ならないか。ふだんの大きさで見た目が変わっていないか（G2-06）
    - `/rh_results_detail.html`: 着順・得点が桁の途中で折り返されず、日付が「2026-」「01-24」の2行で読めるか（G2-07）
    - `/resource_efficiency.html`: 見出しが本文の左端（32px）にそろっているか（G2-09）
    - `/title_card_compare.html`: **「ぁあ」で文字を大きくして**、今の形・A・C を比べる（G2-11）
    - `/jpml_pros.html`: 見た目は変えていない（G2-11 は直していない）
  - 新しく決めた値（既決の値にないもの）:
    - `title/`・`saikyo/` の開いたメニューの最大の高さ `calc(100dvh - 60px)`（第2弾で決めた固定ナビの型Aと同じ値）
    - 戻るボタンのあるヒーローの中身の上の余白 `calc(clamp(12px, 3vw, 24px) + 44px + 8px)`（戻るボタンの上の位置＋ボタン44px＋間8px）
    - `rh_results_detail` の着順の列の最小幅 `calc(5ch + 8px)`（5桁＋左右の余白）
    - `.mj-page-heading` の余白 `24px 32px 0.5rem`（本文の `.mj-margin-text` と同じ左右 32px・上 24px）
- 未確認の項目:
  - iPhone の Safari での実機の見え方。文字の拡大は Chromium の近似（ルートの文字サイズ、または全要素の文字サイズを倍にする）で測った。Safari の「ぁあ」が `cqi`・`clamp()` の文字をどう大きくするか、`dvh` の高さは確かめていない
  - 外部の画像（X・YouTube）は灰色の画像に置き換えて測った（実際の写真の読み込みの時刻での見え方は確かめていない）
  - 読み上げソフトでの読まれ方（この作業では見た目だけを変えた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4790f6d2）: https://github.com/retroeater/mj-logs/tree/main/guide/4790f6d2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4790f6d2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
