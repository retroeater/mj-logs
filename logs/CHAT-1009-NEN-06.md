# CHAT-1009-NEN-06

- 着手日時: 2026-10-09
- 対象issue: #277（#521）
- ブランチ: work/1009-nen
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：#277 年表の見直し。平野さんの実機確認の指摘（共有ボタン右上・件数なし・年のまとまり・カードの文字の置き方）を反映し、並べ方3案（A 年ごとの一覧の改良／B 年×タイトル戦の格子／C タイトル戦×年の格子）をラジオボタンで見比べる比較ページを試作する。未マージ・判断待ちで止まる Chat-Ref: CHAT-1009-NEN-06 マージ: 判断待ちで止まる（プレビューを見て平野さんが並べ方を決める。cloudflare へは入れない） 貼る時機: CHAT-1008-NEN-05 の完了の後（済んでいる。`/title/timeline/` は未公開のまま本番にある） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1009-nen を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-nen origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1008-NEN-05 のログの `## 報告` を読み、完了していなければ何もせず止まる。

目的
平野さんが本番（未公開）の `/title/timeline/` を実機で見て、直したい点と並べ方の案を出した。小さな直し（共有ボタンの位置・件数・年のまとまり・カードの文字の置き方）は3案に共通で入れ、並べ方は3案を実データで見比べられる比較ページにする。この指示ではマージしない。採用後の本実装は別の指示。
決定（2026-10-09、平野さん）

* 共有ボタンは右上に置く（今は左上）
* 年の見出しの件数「（18）」は出さない（2026-10-08 の grill Q9「2025年（25）」の形を置き換える）
* 年ごとのまとまりが分かる形にする（年をカードにする等）
* 選手名は写真の下部に「いつもの形」（入口の写真カードと同じ、グラデーションに白文字）で重ねる。今の、写真の下に文字を並べる形は上下が不揃いでガタガタする
* 期は写真の上部に、タイトル戦名は写真の外側の下に、など、タイトル戦名の長さでごちゃごちゃしない工夫をする
* 2026年と2025年の同じタイトル戦が縦方向に並ぶ形がよい。タイトル戦を縦に並べて、左から右に 2026 → 2025 → … と並べると、歴代のタイトルホルダーの移り変わりが分かりやすいのではないか（この2つは案として試作して見比べる）

前提（チャット側。平野さんの決定ではない）

* 今の年表の作り（`timeline_card_html()`・`timeline_bar_html()`・`year_heading()`・`.mj-tl-*`、docs/notes/title-pages.md「年表」）を土台にする。公開関連（noindex・sitemap・navbar・`llms.txt`）は触らない（未公開のまま。公開は #521）
* 共通の直し（3案すべてに入れる）:
   * カード: 写真の上部に期の帯「第42期」（入口の大会名の帯と同じ部品・変数 `--mj-title-band-bg`）、写真の下部に選手名（入口と同じグラデーション＋白文字＋影）。タイトル戦名は写真の外側の下に小さく1行（案 B・C では列・行の見出しに大会名があるので、カードには出さない）。押す先は期ページ（変えない）
   * 固定バー: 共有ボタンを右端に（年ジャンプは左から）。年ジャンプの中身は今のまま（横に並べる・年代の区切り・今見ている年の強調・`#y2025`）。案 B・C では年ジャンプの押し先が列（案 C）か行（案 B）になる
   * 年の見出しは「2026年」だけ（件数なし）
* 並べ方（ラジオボタンで切り替え。`:has(#…:checked)` で CSS を差し替え、JS なし・再読み込みなし。HTML は3案で共有できる形を目指し、できなければ3つ焼き込む）:
   * A: 年ごとの一覧の改良。年ごとを1枚のカード（枠・薄い背景・見出し）にまとめ、中は今の並び（大会の表示順）
   * B: 年 × タイトル戦の格子。行＝年（上から 2026 → 1973）、列＝タイトル戦（「タイトル戦」タブの表示順、20列）。列の見出し（大会名）は上に固定（sticky）、年は左の列に固定。同じタイトル戦が縦に並ぶ。空のマスは空欄。同じ年に同じ大会が2期あるマスは2枚を縦に重ねる（見込み: 年2回ある大会があるため。実データで数を報告）
   * C: タイトル戦 × 年の格子（B の転置）。行＝タイトル戦（表示順、20行）、列＝年（左から 2026 → 1973、54列）。大会名は左の列に固定、年は上の行に固定。横スクロールで歴代の移り変わりを追う
   * 格子の幅: 1マスは今の小さい写真（最小 96px）を基準にし、スマホでは横スクロール（B は20列、C は54列）。固定の見出し（sticky）はスクロール中も見えるようにする。1マスの大きさはスマホ幅・PC 幅で見て Code が決めてよい
* 確かめ方は NEN-03 と同じ（headless Chromium で 375px・1280px・1920×1080、JS のエラー、固定バーの高さ、年ジャンプ、ラジオの切り替え、写真の遅延読み込み、横スクロールの有無を案ごとに表に）。比較用のラジオボタンは本実装で消す
* `title/timeline/` 以外の生成物（他の title/ のページ・`sitemap-title.xml`・`search.json`）は変えない。シートの変化による差分が混ざれば種類と件数を報告

手順

1. 確かめる: #277・#521 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*`／`.mj-tl-*` 節を変えていない（`work/1008-hou` の `style.css` 末尾の houou/ の節は当たらない）。生成と同じ経路でシートを読み、表示する大会数・期数と、同じ年に同じ大会が2期以上あるマスの数を報告する。#277 に着手中のコメントを残す
2. 作る: 上の共通の直しと A・B・C の比較の仕組みを `title/timeline/index.html` に入れ、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分が `title/timeline/index.html` だけであることを確かめる。`python3 -m unittest discover -s scripts/tests` を通す（`test_title_timeline.py` の見出しの件数の検査は「件数なし」に合わせて直してよい）
3. 確かめる: Workers Builds のプレビュー（上限15分）で上の確かめを行い、結果を案ごとの表でログに書く。平野さんに見てもらう手順（確認用 URL、A → B → C の順、見る年の例〈2026・2014・1973〉、スマホでの横スクロールと固定の見出し）を報告に書く

止まる条件

* #277 か #521 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分に `title/timeline/index.html` 以外が出た（シートの変化で説明できるものは止まらず報告）
* 既存のテストが落ちる（`test_title_timeline.py` の件数の検査以外）
* 固定の見出し（sticky）が固定バー（`--mj-title-filter-h`）と両立しない（案を書いて止まる）
* cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが決める点（並べ方 A／B／C、共通の直しの可否）、見てもらう手順、本実装で消す・足すものを書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-05 の状態は「完了」。このセッションは NEN-01〜05 から続けて受けたもの（同じセッション。識別子 NEN はこのセッションが使ってきたもの）
- 識別子: `git log --all --grep="CHAT-1009-NEN-06"` は0件
- ブランチ: `work/1009-nen` はローカル・リモートとも無かったため `git checkout -b work/1009-nen origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1 確かめ

- #277・#521: Open。他セッションの着手中コメントは無い（#277 の着手中のコメントはこのセッションの NEN-01・NEN-03 のもの）。#277 に着手中のコメントを残した（issuecomment-6063047960）
- 未マージの `work/` ブランチ: `origin/work/1008-dic`（734f7ed0）は対象のファイルを変えていない。`origin/work/1008-hou`（e25f04f4）は `_redirects` と `style.css` 末尾の houou/ の節だけ（`mj-title`・`mj-tl` を含む差分の行は0）で、指示のとおり当たらない
- 生成と同じ経路で読んだ: 表示する大会 20、期 363、年 54（1973〜2026）
- 同じ年に同じ大会が2期以上あるマス: 24。3期は 2025年の JPML WRC-Rリーグの1マス、ほかは2期（JPML WRCリーグ 2017〜2019・2021〜2026、若獅子戦 2021〜2026、桜蕾戦 2021〜2026、JPML WRC-Rリーグ 2024、女流桜花 2023）。第26期王位戦（1位2名）のマスは1期2枚

### 手順2 作る

- `scripts/generate_title_pages.py`:
  - カード `timeline_card_html()` を入口と同じ写真カード（`.mj-title-holder-card`・`-photo`・`-taikai`・`-name`）にした。上部の帯に期（`period.label`、「第42期」「2026」など）、下部に選手名（グラデーション＋白文字＋影）。押す先は期ページのまま。A だけ写真の外側の下に大会名を1行（`.mj-tl-card-taikai`、長い名前は「…」で切る）
  - 帯の字の大きさは入口と同じ式（`--mj-title-taikai-em`）。期の表記は短く字が大きくなりすぎたため、下限を6字分にした（`TIMELINE_BAND_MIN_EM`。幅104px のカードで約16px）
  - 年の見出しは「2026年」だけ（`year_heading()` から件数を外した）
  - 固定バー `timeline_bar_html()`: 年ジャンプを左、共有ボタンを右端に。年のリンクに `data-year` を付けた
  - 並べ方: A は `timeline_list_html()`（年ごとの `section`、`id="y2025"`）。B・C は `timeline_grid_html()` の1つの格子を共有する（各マス・見出しに年の位置 `--yi` と大会の位置 `--ti` を持たせ、CSS が B では行=年・列=大会、C では行=大会・列=年に割り当てる）。HTML は A と格子の2つを焼き込み（3つではない）
  - 比較のラジオボタン `timeline_compare_html()`（`TIMELINE_LAYOUTS`、既定は A）
- `style.css`（年表の節だけ）: A は年ごとに枠・薄い背景（#f8f9fa）・角丸のまとまり。B・C は格子の枠（`.mj-tl-grid-wrap`）の中で縦横にスクロールし、見出し（大会名・年）を枠の上端・左端に固定（sticky）。枠の高さは画面から navbar と固定バーを除いた分なので、固定の見出しは固定バーと重ならない。1マスは PC 104px・スマホ 88px
- `assets/title.js`（年ジャンプ）: 押した年へ、A はページを、B・C は枠が画面に入るようにページを動かしてから枠の中を B は縦・C は横に動かす（固定の見出しの分を空ける）。今見ている年の強調も並べ方に合わせる（B・C は枠の中のスクロールも拾う。枠の端まで動かしたら最後の年）。ラジオを切り替えたら強調を取り直す
- `scripts/tests/test_title_timeline.py`: 見出しの検査を「件数なし」に直し、カードの関数の引数に合わせた
- 生成物の差分: `title/timeline/index.html` だけ（396,974 バイト。NEN-05 の 133,063 バイトから、A と格子の2つを焼き込んだ分増えた）。シートの変化による差分は無い
- `python3 -m unittest discover -s scripts/tests`: 652件 OK

### 手順3 確かめ

Workers Builds（57889590）は success。プレビュー（URL は最終報告）の `/title/timeline/` と `assets/title.js` は手元と同じ（`cmp` で一致）。
headless Chromium でプレビューを開き、並べ方ごとに確かめた（固定バーの高さは実測 / `--mj-title-filter-h`、共有ボタンの右端は画面の右端からの距離〈本文の右端と同じ〉）:

| 幅 | 並べ方 | JS のエラー | 固定バー | 共有ボタンの右端 | ページの横スクロール | 格子の枠の中のスクロール（横・縦） | 年ジャンプ 2014・1973・2026 の強調 | 開いた直後に読まれた写真（見えている並べ方の364枚中） |
|---|---|---|---|---|---|---|---|---|
| 375×740 | A | なし | 61 / 61px | 16px | なし | — | 2014・1973・2026 | 187 |
| 375×740 | B | なし | 61 / 61px | 16px | なし | あり・あり | 同 | 244 |
| 375×740 | C | なし | 61 / 61px | 16px | なし | あり・あり | 同 | 247 |
| 1280×800 | A | なし | 61 / 61px | 176px | なし | — | 同 | 198 |
| 1280×800 | B | なし | 61 / 61px | 176px | なし | あり・あり | 同 | 272 |
| 1280×800 | C | なし | 61 / 61px | 176px | なし | あり・あり | 同 | 279 |
| 1920×1080 | A | なし | 61 / 61px | 496px | なし | — | 同 | 214 |
| 1920×1080 | B | なし | 61 / 61px | 496px | なし | あり・あり | 同 | 281 |
| 1920×1080 | C | なし | 61 / 61px | 496px | なし | あり・あり | 同 | 296 |

- 年ジャンプの移動先: A は見出しが固定バーの下端に揃う。B は年の行が固定の大会名の行の直下、C は年の列が固定の大会名の列の直右に来る（2014・2026）。1973（最後）は格子の端までしか動かないが、強調は 1973 になる
- 固定の見出し: B・C とも、年へ移動した後も格子の角・大会名・年の見出しが枠の中に見えている
- 写真の遅延読み込み: すべて `loading="lazy"`。見えていない並べ方の写真は読まれない。見えている並べ方でも開いた直後に全部は読まれない（上の表）。格子（B・C）は Chromium が枠の中のスクロールの先も先読みするため、A より多い
- 読み込みのエラー（JS ではない）は、セッションのプロキシで外部の画像が拒否されたもの。代替アバターに差し替わる
- 試作の時の気付き（判断の材料）: B はスマホで1画面に大会が約3列しか入らない。C はスマホで約3年分。C は大会名の列（5.5em）が狭く、長い大会名は2〜3行になる。同じマスに2〜3期ある年は、その行（B）・列（C）の高さ・幅が増える

## 報告

- 状態: 判断待ち / 続き: CHAT-1009-NEN-07
- ブランチ: work/1009-nen
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen/docs/logs/CHAT-1009-NEN-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ `/title/timeline/`
- マージ: 未（平野さんの判断待ち。この指示ではマージしない）
- issue: #277、#521
- 判断が必要なこと:
  - 平野さんが決める点: 並べ方（A 年ごと / B 年×タイトル戦 / C タイトル戦×年）と、共通の直し（カードの帯に期・下部に選手名、A の大会名を写真の下に1行、共有ボタン右端、見出しの件数なし、帯の字の大きさ）の可否
  - 見てもらう手順: 確認用 URL の `/title/timeline/` を開く → 上の枠の「並べ方」で A → B → C の順に押す → それぞれで固定バーの年 2026・2014・1973 を押し、移動と強調を見る → B・C はスマホで格子の中を横・縦にスクロールし、大会名・年の見出しが固定されて見え続けるかを見る（2025年の JPML WRC-Rリーグは1マスに3期、2023年の女流桜花は2期）
  - 本実装で消す・足すもの: 比較のラジオボタン（`timeline_compare_html()`・`TIMELINE_LAYOUTS`・`.mj-tl-compare`）、採らない並べ方の HTML（A なら格子 `timeline_grid_html()` と `.mj-tl-grid*`・`.mj-tl-th*`・`.mj-tl-cell`、B・C なら A の `timeline_list_html()` と `.mj-tl-year`）と、B・C の片方を採るときの他方の CSS、`assets/title.js` の年ジャンプの採らない並べ方の分岐、テスト（並べ方に合わせた生成物の検査）、`docs/notes/title-pages.md`「年表」、`docs/decisions/title.md`（並べ方の決定）。公開は #521 のまま
- 未確認の項目:
  - 実機（iPhone の Safari など）での格子の中のスクロール（ページのスクロールとの切り替わり）と固定の見出しの見え方（headless Chromium だけで確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
