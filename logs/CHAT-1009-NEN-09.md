# CHAT-1009-NEN-09

- 着手日時: 2026-10-09
- 対象issue: #277
- ブランチ: work/1009-nen-year
- 着手時HEAD: 2285a445

## 指示

【Claude作成】Claude Code 向け指示：NEN-08 の続き。年の切り替えの文言を直し、部品の見た目を複数案（矢印なしのプルダウンだけ、を含む）で見比べる比較を入れる。あわせて「JPML WRC(-R)リーグ」から「JPML」を外す件を別 issue に起票する。未マージ・判断待ちで止まる Chat-Ref: CHAT-1009-NEN-09 マージ: 判断待ちで止まる（プレビューを見て平野さんが部品の見た目を決める。cloudflare へは入れない） 貼る時機: CHAT-1009-NEN-08 が「判断待ち」で止まった後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen-year の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-nen-year を続けて使う（NEN-08 の試作がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1009-NEN-08.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。NEN-08 の `## 報告` の状態の末尾に `/ 続き: CHAT-1009-NEN-09` を足す（`## 指示` 欄より後ろの見出しを相手にする）。

目的
平野さんが NEN-08 のプレビューを見て、動き（年の切り替え・帯の期・並び・大会ページと期ページの固定バー）は OK とした。文言を直し、スマホで水色の ◀▶ が野暮ったいので、部品の見た目を複数案で見比べられるようにする。この指示ではマージしない。
決定（2026-10-09、平野さん）

* NEN-08 の試作の確かめ: 年の切り替えの動き、帯の期の出し方、大会ページ・期ページの固定バー（検索欄だけ）は OK。年の中の並び（決勝日の降順、月日の無い期は後ろ）も OK
* 文言: 「現在のタイトルホルダー」を「タイトルホルダー」に、「2026年」を「2026年優勝者」にする（年の選択肢と、年を選んだときの見出しの両方）
* 年の切り替えの部品は、スマホで ◀▶ が水色で野暮ったい。スタイリッシュな見た目を複数パターン出して見比べる（矢印なしでプルダウンだけ、の案も含める）
* 「JPML WRC-Rリーグ」「JPML WRCリーグ」は、タイトル戦の名称が変わったので、やがて「JPML」を外す。過去の YouTube 概要欄との紐づけ等に影響が出そうなので、別課題（別 issue）にする
* `work/1009-nen`（年表の試作）は平野さんが GitHub の画面で削除した（2026-10-09、スクリーンショットで「Deleted now」を確かめた）

前提（チャット側。平野さんの決定ではない）

* 文言: 選択肢は「タイトルホルダー」「2026年優勝者」…「1973年優勝者」。年を選んだときの h1（今は「タイトル戦 2025年のタイトル獲得者」）は「タイトル戦 2025年優勝者」の形に。`aria-label` も合わせる。テスト・docs/notes/title-pages.md の記述も直す
* 部品の見た目の案（ページ内のラジオボタンで切り替える。`:has(#…:checked)` で CSS を差し替え、JS なし・再読み込みなし。docs/notes/chat-side-operations.md「見た目の決め方」。HTML は共通にし、CSS で見せ方を替えられる形を目指す。案ごとに動き〈選ぶ・送る〉が同じに保てないなら、そのことを書く）:
   * S1: プルダウンだけ。矢印なし。ピル型（角丸いっぱい）、枠は薄いグレー、文字は本文と同じ色、右端に細い ▾ 。幅は中身に合わせる
   * S2: プルダウン＋左右の矢印を「‹」「›」の細い山括弧のアイコンボタンに。背景・枠なし、色はグレー（本文より薄く）、押せる大きさは 44px 四方
   * S3: ステッパー型。1つのピルの中に「‹ 2025年優勝者 ▾ ›」を収める（左右の端が送り、中央がプルダウン）。区切りの細い縦線
   * S4: テキスト型。枠なしで「2025年優勝者 ▾」を見出しのような太字の文字だけで置き、送りの矢印は無し（S1 よりさらに軽い）
   * 色は Bootstrap の既定の水色（`.btn-outline-*` 等）を使わない。本文の文字色・`#f2f2f2`／`#dee2e6` 系のグレー・リンク色 `#14459b` の範囲で選ぶ。フォーカスの枠は 1.4.11（3:1）を満たす。押せる大きさは 44px 四方以上（スマホ）。無効の矢印は薄くする
   * 4案すべて、スマホ（375px）で固定バーが今より高くならないこと（検索欄との並び方は案ごとに Code が決めてよい。1段に収まるならそのほうがよい）
* 比較のラジオボタンは本実装（マージの指示）で消す。この指示の生成物の差分は `title/index.html`（と `years.json` を使う JS・CSS）だけの見込み。大会ページ・期ページは NEN-08 から変わらない見込み
* 別 issue（「JPML」を外す件）: 題は「タイトル戦名『JPML WRC-Rリーグ』『JPML WRCリーグ』から『JPML』を外す」の形。本文に、平野さんの決定（名称の変更、影響があるので別課題）と、影響が出そうな所の洗い出しの候補を書く（Code が実物で確かめて足し引きしてよい。要確認の印を付ける）: 「タイトル」「タイトル戦」タブの大会名、title/ の slug・URL（変えるかどうか）と `_redirects`、OGP 画像 `img/ogp/title/<slug>-black.png`（大会名の文字）、/live の層2の規則と YouTube 概要欄の大会名の照合（`broadcast_key()` など）、「別名」タブ・`yotei.EVENTS`、sitemap、検索のデータ、`title/years.json`。ラベルは「分野: データ」（無ければ近いもの）。起票の前に同じ主題の issue をクローズ済みも含めて検索する（CLAUDE.md「issueの着手ルール」）。この指示では直さない
* 決定の記録: docs/decisions/title.md に上の「決定」を「2026-10-09（CHAT-1009-NEN-09）」として足す（作業の完了時。README「書き方」）

手順

1. 確かめる: #277 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*` 節を変えていない（NEN-08 の時の確かめを取り直す。`work/1008-hou` の houou/ の行・節と `docs/new-page-checklist.md` の隣り合う行は当たらない）。「JPML」を外す件と同じ主題の issue を検索する
2. 作る: 文言を直し、部品の4案と比較のラジオボタンを入れ、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分を種類に分けて報告する。テストを直し、`python3 -m unittest discover -s scripts/tests` を通す。別 issue を起票する（番号を報告に書き、#277 にもコメントする）
3. 確かめる: Workers Builds のプレビュー（上限15分）を headless Chromium で開き、375px・1280px で、4案それぞれの固定バーの高さ・押せる大きさ・フォーカスの枠のコントラスト比（WCAG 2.x の相対輝度の式）・動き（選ぶ・送る・端で無効）・JS のエラーを表でログに書く。平野さんに見てもらう手順（確認用 URL、ラジオで S1 → S4、スマホで年を選ぶ・送る）を報告に書く

止まる条件

* #277 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている（上の例外を除く）
* 「JPML」を外す件と同じ主題の open issue が既にある（番号を書き、起票せずに進んでよい。その issue にこの指示の決定をコメントする）
* 生成物の差分に、`title/index.html` 以外の title/ のページ・`sitemap-title.xml`・`search.json` の変化が出た（シートの変化で説明できるものは止まらず報告）
* 「タイトルホルダー」（既定）の表示のカードの並び・中身が本番と変わる
* 既存のテストが落ちる（文言の検査は直してよい）
* cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが決める点（S1〜S4）、見てもらう手順、起票した issue の番号、本実装（マージの指示）で消すもの（比較のラジオボタンと採らない案の CSS）を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-08 の状態は「判断待ち」だった。末尾に「/ 続き: CHAT-1009-NEN-09」を足した。このセッションは NEN-01〜08 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1009-NEN-09"` は0件
- ブランチ: ローカルの `work/1009-nen-year` は `origin/work/1009-nen-year` と一致（2285a445）。`origin/cloudflare` は祖先（取り込み不要）。`origin/work/1009-nen` は fetch --prune でリモートから消えていた（平野さんの削除のとおり）
- 未マージの `work/` ブランチ: `origin/work/1008-dic`（ee99f082）・`origin/work/1009-swp-fix`（7701ba1e）は対象のファイルを変えていない。`origin/work/1008-hou`（7275a697）は `_redirects`・`style.css` を変えるが、title の行・`.mj-title*` 節を含む差分の行は0（houou/ の行・節だけ）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている


### 手順1 確かめ

- #277: Open。最新のコメントはこのセッションの NEN-08。他セッションの着手中コメントは無い
- 「JPML」を外す件と同じ主題の issue: 検索（「JPML WRCリーグ 名称 外す 改名」と、全 issue の題・本文の「WRC」「JPML…外す・改名・名称」）で無かった。近いもの: #497（「JPMLリーグ」を足す）・#365・#366（WRC・WRC-R の成績）・#488

### 手順2 作る

- 文言（`generate_title_pages.py`）: 既定の選択肢を「タイトルホルダー」（`YEAR_CURRENT_LABEL`）、年の選択肢を「2026年優勝者」（`YEAR_LABEL`）にした。年を選んだときの読み上げ用の h1 は選択肢の文字から「タイトル戦 2025年優勝者」の形（`assets/title.js`）。既定の表示の h1「タイトル戦 現在のタイトルホルダー」（と `<title>`）は検索に出る文言なので変えていない（指示の「年の選択肢と、年を選んだときの見出し」の範囲外と読んだ）。`aria-label`（「表示する年」「新しい年へ」「古い年へ」）は文言に「現在」を含まないので変えていない
- 送りの矢印を ◀ ▶ から文字の ‹ › にした。◀ ▶（U+25C0・U+25B6）は iPhone などで絵文字（水色の四角）として描かれ、平野さんの言う「水色」はこれだったとみられる（CSS の色は灰色系を指定していた）
- 部品の4案（`style.css`。HTML は共通で、ラジオボタン `#title_ys_s1`〜`s4` と `:has()` で CSS だけを差し替える。動きは4案とも同じ JS）:
  - S1 プルダウンだけ: 矢印を隠す。ピル型（角丸いっぱい）、枠は薄いグレー（白の35%）、文字 #f2f2f2、右端に細い ▾（2本の斜めのグラデーションで描く。画像なし）
  - S2 ＋細い矢印: S1 のプルダウンの左右に ‹ ›（背景・枠なし、#adb5bd、1.75rem、44×44px）
  - S3 ステッパー: 1つのピルの中に「‹ ｜ プルダウン ▾ ｜ ›」。区切りは細い縦線。外枠は高さを取らない影（`box-shadow: inset`）で描いた（枠線にすると固定バーが2px 高くなったため）
  - S4 文字だけ: 枠なしの太字（1.125rem）と ▾、矢印なし
  - 固定バーは暗い地（#131316）なので、色は本文の白系（#f2f2f2）とグレー（#adb5bd・白の35%）で選んだ（指示の「本文の文字色・グレー・リンク色」を暗い地に読み替えた）。Bootstrap の既定の色は使っていない
  - フォーカスの枠は #f2f2f2 の2px（`outline-offset: 2px`）。無効の矢印は不透明度35%
- 比較のラジオボタン `year_switcher_compare_html()`（`YEAR_SWITCHER_STYLES`、`.mj-title-year-compare`）を入口の本来の内容の先頭に置いた（既定は S1）
- テスト: `test_title_years.py` に選択肢の文言（「タイトルホルダー」「NNNN年優勝者」）の検査を足した。`python3 -m unittest discover -s scripts/tests`: 654件 OK
- 生成物の差分: `title/index.html` だけ（選択肢の文言・矢印の文字・比較のラジオボタン）。`years.json`・大会ページ・期ページ・`sitemap-title.xml`・`search.json` は変わらない。「タイトルホルダー」の表示のカードは変えていない
- 別 issue を起票した: #523（タイトル戦名「JPML WRC-Rリーグ」「JPML WRCリーグ」から「JPML」を外す。ラベル「分野: データ」）。影響の候補はコードを grep して足した（`scripts/lib/live_extract.py` の大会名の対応と「WRC」の正規表現、`scripts/lib/yotei.py`、`scripts/apply_page_meta.py`、`llms.txt`、`index.html`、`rh_results_detail.html`、`live/` の生成物、OGP 画像 `jwrc-black.png`・`jwrc-r-black.png`、`broadcast_key()`。「WRC」がリーチ麻雀世界選手権〈slug `wrc`〉と重なる点も書いた）。#277 にコメントした（issuecomment-6065641986）
- `docs/notes/title-pages.md`「入口の年の切り替え」の文言を直した。`docs/decisions/title.md` に NEN-09 の決定を足した

### 手順3 確かめ

Workers Builds（76cfd5f2）は success。プレビュー（URL は最終報告）の `/title/`・`assets/title.js`・`style.css`・`/title/houou/42.html` は手元と同じ（`cmp` で一致）。
headless Chromium でプレビューを開き、4案それぞれで確かめた（固定バーは入口の実測、NEN-08 の時は 375px で 115px・1280px で 61px）:

| 案 | 幅 | 固定バー | 矢印（押せる大きさ） | プルダウン | 送る（› → ‹ ‹） | 1973 | 「タイトルホルダー」に戻す | JS のエラー |
|---|---|---|---|---|---|---|---|---|
| S1 | 375 | 115px（2段） | なし | 181×44 | — | 1枚、`?year=1973` | 20枚、`?year` なし | なし |
| S2 | 375 | 115px（2段） | 44×44、既定で ‹ 無効 | 181×44 | 2024 → 2026（`?year=` も追う） | 1枚、› 無効 | 20枚、‹ 無効 | なし |
| S3 | 375 | 115px（2段） | 44×44、既定で ‹ 無効 | 177×44 | 同上 | 同上 | 同上 | なし |
| S4 | 375 | 115px（2段） | なし | 185×44 | — | 1枚 | 20枚 | なし |
| S1 | 1280 | 61px（1段） | なし | 181×44 | — | 1枚 | 20枚 | なし |
| S2 | 1280 | 61px（1段） | 44×44 | 181×44 | 2024 → 2026 | 1枚、› 無効 | 20枚 | なし |
| S3 | 1280 | 61px（1段） | 44×44 | 177×44 | 2024 → 2026 | 1枚、› 無効 | 20枚 | なし |
| S4 | 1280 | 61px（1段） | なし | 185×44 | — | 1枚 | 20枚 | なし |

- 年を選んだときの見出し（読み上げ用 h1）: 「タイトル戦 2025年優勝者」「タイトル戦 1973年優勝者」。選択肢の表示は「2025年優勝者」
- スマホ（375px）は4案とも、今（NEN-08）と同じ2段（年の切り替えの段と検索欄の段）で 115px。1段に収めると検索欄の幅が約100px になるため、2段のままにした
- 初めて年を選んだときは `years.json` を読むまで「タイトルホルダー」の並びのまま（プレビューで約0.2秒。最初の確かめで、待ちの0.6秒の間に1回だけ読み終わらなかった回があった）

コントラスト比（WCAG 2.x、固定バーの地 #131316 に対して）:

| 前景 | 比 | 基準 |
|---|---|---|
| フォーカスの枠 #f2f2f2（2px） | 16.56 | 非テキスト 3:1 |
| プルダウンの文字・▾ #f2f2f2 | 16.56 | テキスト（太字）4.5:1（AAA 7:1 も満たす） |
| 矢印 ‹ › #adb5bd（S2） | 8.94 | 同上 |
| ピルの枠・区切り（白の35%、地に重ねて #666668） | 3.24 | 非テキスト 3:1 |
| 無効の矢印（不透明度35%） | — | 無効な部品はコントラストの対象外 |

## 報告

- 状態: 判断待ち
- ブランチ: work/1009-nen-year
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen-year/docs/logs/CHAT-1009-NEN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen-year
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ `/title/`
- マージ: 未（平野さんの判断待ち。この指示ではマージしない）
- issue: #277（コメント）、#523（起票）
- 判断が必要なこと:
  - 平野さんが決める点: 年の切り替えの見た目 S1（プルダウンだけ）／S2（＋細い矢印 ‹ ›）／S3（ステッパー）／S4（文字だけ）
  - 見てもらう手順: 確認用 URL の `/title/` を開く → 本文の先頭の枠「年の切り替えの見た目」で S1 → S2 → S3 → S4 を押す → それぞれでスマホから年のプルダウンで 2025・1973 を選ぶ、S2・S3 は ‹ › で送る（端で薄くなる）→「タイトルホルダー」に戻す
  - 既定の表示の h1・`<title>`（「現在のタイトルホルダー」）は変えていない。変えるかどうか
  - 本実装（マージの指示）で消すもの: 比較のラジオボタン（`year_switcher_compare_html()`・`YEAR_SWITCHER_STYLES`・`.mj-title-year-compare` の CSS）と、採らない案の CSS（`.mj-title:has(#title_ys_s…)` の規則。S1 か S4 なら矢印のボタンの HTML・JS も消せる）。マージで #277 を閉じる
  - 起票した issue: #523（「JPML」を外す件。影響の候補に要確認の印。この指示では直していない）
- 未確認の項目:
  - 実機（iPhone の Safari など）で ‹ › が絵文字にならず灰色の文字で出ること・ピルの ▾ の見え方（headless Chromium だけで確かめた）
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
