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

## 報告

- 状態: 作業中
- ブランチ: work/1009-nen-year
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen-year/docs/logs/CHAT-1009-NEN-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen-year
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと: なし
- 未確認の項目: なし
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
