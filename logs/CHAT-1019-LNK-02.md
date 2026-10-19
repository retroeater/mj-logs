# CHAT-1019-LNK-02

- 着手日時: 2026-10-19
- 対象issue: なし（#616 の続き）
- ブランチ: work/1019-lnk
- 着手時HEAD: a33a81859

## 指示

【Claude作成】Claude Code 向け指示：メニュー「連盟」をやめ、「連盟プロ」「タイトル戦」をトップメニューの項目にする（プレビューまで） Chat-Ref: CHAT-1019-LNK-02 マージ: 判断待ちで止まる（navbar の見た目が変わるので、プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1019-LNK-01（#616）で「連盟 > リンク」を廃止し、メニュー「連盟」の項目が「プロ」「タイトル戦」の2つだけになった。サブメニューをやめ、2つをトップメニューの項目に上げる。
決定（2026-10-19、平野さん）

* 「連盟 > プロ」をトップメニューの「連盟プロ」にする
* 「連盟 > タイトル戦」をトップメニューの「タイトル戦」にする
* トップメニューから「連盟」は無くなる。並びは左から「R」「連盟プロ」「タイトル戦」「鳳凰戦」…（「鳳凰戦」以降は今のまま）

前提（チャット側。平野さんの決定ではない）

* 2つの項目はサブメニューを持たない1つのリンクで、行き先は今と同じ（「連盟プロ」は `/jpml_pros.html`、「タイトル戦」は `/title/`。要確認）
* 今いるページの印（今のメニュー「連盟」に付く印）は、`jpml_pros.html` では「連盟プロ」、`title/` 以下では「タイトル戦」に付ける（今の印の付け方に合わせる。実物で付け方が違えば合わせ方をログに書く）
* トップの項目が1つ増えるので、PC 幅で折り返し・はみ出しが出ないかを確かめる。スマホ幅（ハンバーガーを開いた状態）の並びも確かめる
* ページの見出し・パンくず・og:title・`llms.txt` に「連盟 >」の形の表記があれば、メニューに合わせるかは判断が要るので変えずに一覧だけ書く（navbar.js と文書の記述だけを直す）
* 変えるファイルの見込み: `navbar.js` と、メニュー「連盟」を書いている文書（docs/notes/title-pages.md・static-generation.md・video-exam.md など。名指しに頼らず手順1の検索で出た所）と docs/decisions/

手順

1. `git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`navbar.js` の同じ行・同じ関数を変えているもの、または取り込みで衝突するものが無いかを確かめて `## 経過` に書く。「連盟」のメニューを指す記述を、リポジトリ全体（`docs/logs/`・`docs/gsc/` などの記録を除く）で「メニュー「連盟」」「連盟 >」「連盟 >」などで検索し、全件を表で `## 経過` に書く（直す・変えない〈記録・判断が要る〉の区別も）。
2. `navbar.js` を直し、手順1の表で「直す」にした文書を直す。docs/decisions/ に今回の決定を書く（先例どおり site-review.md の LNK-01 の続きでよい）。テストを回す。
3. work/ ブランチのプレビューで、PC（1280px と 1024px）とスマホ幅（375px、メニューを開いた状態）のトップメニューを、`jpml_pros.html`・`title/`・`houou/` の3ページで撮って `docs/logs/CHAT-1019-LNK-02/` に置く（今いるページの印が分かるように）。本番（cloudflare）の同じ幅の画像も1枚ずつ撮って並べる。折り返し・はみ出しの有無をログに書き、判断待ちで止まる。

止まる条件

* 前提が食い違う（行き先が違う、メニュー「連盟」に「プロ」「タイトル戦」以外の項目がある等）
* 未マージの work/ ブランチが `navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 他セッションが navbar の着手中コメントを書いている
* PC 幅で今は出ていない折り返し・はみ出しが出た（直さずに画像を置いて止まる。直し方は平野さんの判断）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- `git log --all --grep=CHAT-1019-LNK-02` は0件。`LNK` はこのセッションの LNK-01 で使ったもの（同じセッションの2つ目の指示なので重複には当たらない）
- `work/1019-lnk` はローカルにあり `origin/cloudflare` の祖先（LNK-01 でマージ済み）。cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で a33a81859 へ進めた

### 手順1

- 未マージのブランチで `navbar.js` を変えているのは `origin/work/1017-lvp` だけ。変えているのは `newNavCurrent` の分岐（`mj-live-n-page`）で、今回の `document.write` のメニューの行とは別の箇所。
  `git merge-tree` で navbar.js は自動で合わさる（衝突は `live/` の3つと `docs/decisions/live.md` の4件で、cloudflare と 1017-lvp の間にもともとある4件と同じ。今回の変更によるものではない）
- 前提の確かめ: メニュー「連盟」の項目は「プロ」（`/jpml_pros.html`）・「タイトル戦」（`/title/`）の2つだけ。行き先は前提どおり
- 今いるページの印: 実物では印（`aria-current` と白地のナビ）は houou/・ouka/・jwrc/・wrcr/・最強戦の新ページにだけ付き、`jpml_pros.html`・`title/` には今も「連盟」にも印が無い（黒地のナビのまま）。
  そのため「今の付け方に合わせる」＝印を足さない、とした（足すなら白地のナビにするかを含め判断が要る）

「連盟」のメニューを指す記述（`git grep`、docs/logs・docs/gsc を除く。検索語: 「連盟」・連盟 >・連盟<br>・連盟のメニュー など）:

| 場所 | 中身 | 扱い |
|---|---|---|
| `navbar.js:31-36` | メニュー「連盟<br>JPML」と下の2項目 | 直す |
| `scripts/tests/test_navbar.py` | 「タイトル戦」の項目名のテスト（`</a>` の直前を見ていた） | 直す（`<br>` の前を見る）。並びのテストを足す |
| `docs/notes/static-generation.md:273` | 「navbar の「連盟 > タイトル」から入口へ」 | 直す（「タイトル戦」から） |
| `docs/notes/title-pages.md:21` | 公開（#413）のときに「連盟 > タイトル」を差し替えた経緯 | 変えない（経緯の記録） |
| `docs/notes/video-exam.md:4・7` | 旧 `jpml_test.html` が「連盟」のメニューにあった説明 | 変えない（旧ページの記録） |
| `docs/decisions/exam.md:36`・`houou.md:1215`・`page-release.md:55`・`site-review.md:199` | 過去の決定 | 変えない（記録） |
| `docs/decisions/seo-bing.md:130` | 文字の OGP 画像は「メニューの最上位でそれ自体がページになっているもの」だけ作り、タイトル戦は「メニューでは「連盟」の下だが」例外 | 変えない（判断が要る。下の「判断が必要なこと」） |

- ページの見出し・パンくず・og:title・`llms.txt` に「連盟 >」の形の表記は無い（`jpml_pros.html` は title・og:title「プロ | 日本プロ麻雀連盟 | ryoei.pro」、`apple-mobile-web-app-title` は既に「連盟プロ」。`title/` は「タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」）。変えていない

### 手順2

- `navbar.js`: 「連盟」のドロップダウンを消し、`連盟プロ<br>Pros`（`/jpml_pros.html`）・`タイトル戦<br>Title`（`/title/`）の1段の項目を「鳳凰戦」の前に置いた。
  黒地のナビはほかの項目と同じ「日本語<br>英語」の2行にした（英語の行は指示に無いので仮。下の「判断が必要なこと」）。白地のナビ（houou/ など）では `<br>` の前の「連盟プロ」「タイトル戦」になる
- テスト: 1443件 OK。足した・直したテストは cloudflare の `navbar.js` では FAIL・ERROR になることを確かめた（#310）
- 決定を `docs/decisions/site-review.md` に「2026-10-19（CHAT-1019-LNK-02）」として足した
- 成果物のコミット 5c4063e4b

### 手順3: 撮影（`docs/logs/CHAT-1019-LNK-02/`）

- プレビュー（5c4063e4b の「Workers Builds: mj」success。URL は最終報告だけ）: `preview-<ページ>-<幅>.png`（ページ: jpml_pros・title・houou、幅: 1280・1024・375〈メニューを開いた状態〉）。PC は上の160px、スマホは800pxを撮った
- 本番（cloudflare、今の版）: `prod-<ページ>-<幅>.png`（同じ9枚）
- 計測（ヘッドレス Chromium。ナビの項目の行数・ナビの高さ・横のはみ出し）:

| ページ | 幅 | 本番: 行数・ナビ高さ・最後の項目の右端 | プレビュー: 同 | 印 |
|---|---|---|---|---|
| jpml_pros | 1280 | 1行・80px・702px | 1行・80px・812px | なし（両方） |
| jpml_pros | 1024 | 1行・80px・702px | 1行・80px・812px | なし |
| jpml_pros | 375 | 9項目・640px | 10項目・704px | なし |
| title/ | 1280・1024 | 1行・80px・702px | 1行・80px・812px | なし |
| title/ | 375 | 9項目・640px | 10項目・704px | なし |
| houou/ | 1280・1024 | 1行・61px・649px | 1行・61px・767px | 鳳凰戦（両方） |
| houou/ | 375 | 9項目・461px | 10項目・505px | 鳳凰戦 |

- PC 幅（1280・1024）で折り返し・はみ出しは無い（ナビの項目は1行、`nav` の `scrollWidth` は `clientWidth` を超えない、ナビの高さは本番と同じ）。スマホ幅は並びが「連盟プロ」「タイトル戦」「鳳凰戦」…の順で、項目が1つ増えた分だけ縦に伸びる
- `jpml_pros` の375px で本文の横はみ出し（`documentElement.scrollWidth > innerWidth`）があるが、本番でも同じ（表の幅。ナビの変更とは関係しない）

## 報告

- 状態: 判断待ち / 続き: CHAT-1019-LNK-03
- ブランチ: work/1019-lnk
- ログ: https://github.com/retroeater/mj/blob/work/1019-lnk/docs/logs/CHAT-1019-LNK-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-lnk
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは `jpml_pros.html`・`title/`・`houou/`。この版で見える印: トップメニューの左端が「連盟プロ / Pros」「タイトル戦 / Title」で、「連盟」が無い
- マージ: 未（平野さんの判断待ち）
- issue: なし（#616 の続き）
- 判断が必要なこと:
  - プレビューを見て cloudflare へマージしてよいか
  - 黒地のナビの英語の行を「Pros」「Title」にした（指示に英語の行の指定が無いため仮。ほかの項目は「日本語<br>英語」の2行）。この語でよいか
  - 今いるページの印は、`jpml_pros.html`・`title/` には今も付いていない（印は白地のナビのページだけ）ので足していない。2つのページにも印を付けるか（付けるなら白地のナビにするかも）
  - `docs/decisions/seo-bing.md`（2026-10 の OGP の決定）の「文字の OGP 画像はメニューの最上位でそれ自体がページになっているものだけ作る」に、「連盟プロ」が当てはまるようになった。`jpml_pros.html` に専用の画像を作るか（タイトル戦は既に専用の画像がある）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6327c0e3）: https://github.com/retroeater/mj-logs/tree/main/guide/6327c0e3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6327c0e3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0a1e266.md
