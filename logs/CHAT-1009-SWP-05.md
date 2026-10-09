# CHAT-1009-SWP-05

- 着手日時: 2026-10-09
- 対象issue: なし
- ブランチ: work/1009-swp-fix（CHAT-1009-SWP-02 の続き）
- 着手時HEAD: 7701ba1e

## 指示

【Claude作成】Claude Code 向け指示：第1弾の直しの続き — jpml_links の代替テキストを空にし、rh_links を4本に絞って、プレビューで止まる Chat-Ref: CHAT-1009-SWP-05 マージ: 判断待ちで止まる（プレビューを平野さんが見てから、別の指示でマージする） 貼る時機: CHAT-1009-SWP-02 が判断待ちで止まった後（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-swp-fix を続けて使う（CHAT-1009-SWP-02 の直しの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-swp-fix の使用と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-SWP-02 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

目的
CHAT-1009-SWP-02 の「判断が必要なこと」への平野さんの回答を、同じ作業ブランチに入れる。
決定（2026-10-09、平野さん）

* `404.html` の右の余白（G3-10）は第2弾（`style.css` を触る段）で直す。`style.css` に `.mj-margin-text` の右の余白を足す形で、`404`・`jpml_links`・`rh_links`・`resource_dictionary` をまとめて直す
* `jpml_links.html` のアイコンの `alt` は空にする（どの団体の「公式サイト」かは見出しで分かる）
* `rh_links.html` に残すリンクは次の4本だけにする。ほかの11本は消す
   * Bootstrap（https://getbootstrap.com/）
   * Google Search Console（https://search.google.com/search-console）
   * Google Sheets（https://www.google.com/sheets/about/）
   * PageSpeed Insights（https://pagespeed.web.dev/）

前提（チャット側。平野さんの決定ではない）

* 4本の形（新しいタブ・外部リンクのアイコン・「（新しいタブで開く）」の予告）は SWP-02 で直した形のまま。行き先の URL は今のファイルの値を正とし、上と違えば止まる
* 4本を消した後に空になる見出し・まとまりがあれば、それも消す（残すと空の見出しになるため）。消した見出しは報告に書く
* G3-10 の第2弾の受け皿: CHAT-1009-SWP-03 が「共通ナビとスキップリンク（第2弾）」の issue を起票する予定（並行して実行中のことがある）。`Chat-Ref: CHAT-1009-SWP-03` を含む issue を検索し、第2弾の issue があればそこに G3-10 の決定（上の1点目）をコメントする。まだ無ければコメントせず、その旨を「判断が必要なこと」に書く
* 使う skill は無い

手順

1. CHAT-1009-SWP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-05` を足す（docs/instruction-template.md の注意書き）
2. 直す: `jpml_links.html` のアイコンの `alt` を空にする。`rh_links.html` を上の4本だけにする。ほかのファイルは変えない
3. プレビューで確かめる（ビルドの完了を待つのは15分まで。超えたら待つのをやめ、その時点の状態を書いて「未確認の項目」に回す）
   * `rh_links`・`jpml_links` のリンクの数・行き先・`target`・予告・アクセシブルネームを、この指示で直す前と後で表にする
   * SWP-02 で直した `404.html`（深い URL・浅い URL）がプレビューで変わっていないことを確かめる
   * G3-10 を第2弾の issue にコメントする（上の前提のとおり）
   * 平野さんがプレビューで見る点（SWP-02 の分を含めて、この指示の後の状態で）を、ページと見る点の1行ずつで `## 報告` の「判断が必要なこと」に書く

止まる条件

* CHAT-1009-SWP-02 の状態が判断待ちでない、または work/1009-swp-fix がリモートに無い
* 残す4本の行き先が、今のファイルの値と違う
* `jpml_links.html`・`rh_links.html` と docs/logs/・docs/decisions/ 以外を変える必要が出た
* 取り込みで衝突した（生成物でない文書で、両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。プレビューの URL は最終報告の「確認用:」の行にだけ書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-SWP-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-SWP-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-09 着手。Chat-Ref の重複確認: `git log --all --grep="CHAT-1009-SWP-05"` に該当なし。識別子 SWP は同じチャットの SWP-01〜04 のみ。
- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。
- CHAT-1009-SWP-02 のログの `## 報告` の状態は「判断待ち（プレビューを平野さんが見てから、別の指示でマージ）」（origin/work/1009-swp-fix、`7701ba1e`）。`work/1009-swp-fix` はリモートにある（未マージ）。ローカルにもあり、リモートと一致していたため、そのまま `git checkout work/1009-swp-fix` で使う。

### 手順1・2 直した内容

- CHAT-1009-SWP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-SWP-05` を足した（`docs/logs/CHAT-1009-SWP-02.md`）。
- 残す4本の行き先は、今の `rh_links.html` の値と一致（`https://getbootstrap.com/`・`https://search.google.com/search-console`・`https://www.google.com/sheets/about/`・`https://pagespeed.web.dev/`）。止まる条件に当たらない。
- `jpml_links.html`: アイコンの `alt` を空にした（22本）。リンクの文字が名前になる。
- `rh_links.html`: 4本だけにした（11本を削除: Codespaces・EzOCR・Favicon Generator・Google Analytics・Google Charts・ImportJSON・Namecheap・Twitobu・Twitter Card Validator・vis.js・Visual Studio Code）。ページに見出し・まとまり（`<h2>` など）は無く、消した見出しは無い。ほかのファイルは変えていない。
- 取り込み: `git merge-base --is-ancestor origin/cloudflare HEAD` が偽だったため `origin/cloudflare` を merge した（rebase なし、`c92d7e81`）。衝突は `docs/decisions/site-review.md` の1ファイルだけ（追記どうし: この枝の CHAT-1009-SWP-02 の項と、`cloudflare` に入った CHAT-1009-SWP-03・SWP-04 の項）で、両方の項を残して解いた。解いた後の見出しと「作業中の回答」の並び:

```text
## 2026-10-06（CHAT-1006-SWP-01）
- 作業中の回答: なし
## 2026-10-09（CHAT-1009-SWP-02）
- 横断レビューの指摘は3段で直す。第1弾は手書きのファイルだけ（生成物に触らない）
- `rh_links.html` の「GitHub」のリンク（`retroeater/mj`。非公開のため訪問者には 404）は外す
- 作業中の回答: なし
## 2026-10-09（CHAT-1009-SWP-03）
… （cloudflare の項のまま）
- 作業中の回答: なし
## 2026-10-09（CHAT-1009-SWP-04）
- `houou_race`（鳳凰戦「順位変動」）は公開されたので、横断レビューの対象に含める
- 作業中の回答: なし
## 2026-10-09（CHAT-1009-SWP-05）
- `404.html` の右の余白（G3-10）は第2弾（`style.css` を触る段）で直す。…
- `jpml_links.html` のアイコンの `alt` は空にする（どの団体の「公式サイト」かは見出しで分かる）
- `rh_links.html` に残すリンクは Bootstrap・Google Search Console・Google Sheets・PageSpeed Insights の4本だけ。ほかの11本は消す
- 作業中の回答: なし
```

（生成物の衝突は無い。ほかの取り込みの差分は `cloudflare` の再生成・他セッションのコミットで、この作業のファイルとは重ならない。）

### 手順3 検証

- 「直す前」は、この指示の前（SWP-02 の直しの後）の作業ツリーを手元の配信で開いて取った（`scratchpad/fix/shot.js`、1280px・390px）。「直した後」は、作業ブランチの push（`c92d7e81`）でできたプレビュー（Workers Builds の check-run は `completed / success`、push から約3分）を Playwright で開いて取った。URL は最終報告にだけ書く。
- プレビューの配信が作業ツリーと同じこと: `404.html`・`rh_links.html`・`jpml_links.html`・`style.css`・`navbar.js`・`jpml_pros.html` の本文の sha256 が手元と一致。
- `404.html`（SWP-02 の直し）: プレビューの実際の 404 で `/title/nothing/here.html`・`/nothing.html` を 1280px・390px で開いた。`style.css`・Bootstrap の CSS/JS・`navbar.js` の失敗した応答は 0 件、`nav` が出て、font は "Hiragino Sans"、`a[href="/"]`「トップへ戻る」が1つ。SWP-02 の結果から変わっていない（右の余白 `margin-right: 0px` も同じ。第2弾で直す）。

#### `jpml_links.html`（22本、増減なし。行き先・`target`・予告は前後とも同じ）

変わったのはアクセシブルネームだけ（前は `alt` と文字が続けて読まれ、後は文字だけ）:

| 行き先 | 前: アクセシブルネーム／`target`／予告 | 後: アクセシブルネーム／`target`／予告 |
|---|---|---|
| https://www.ma-jan.or.jp/ | 日本プロ麻雀連盟（公式サイト） 公式サイト（新しいタブで開く）／_blank／あり | 公式サイト（新しいタブで開く）／_blank／あり |
| https://note.com/jpml | 日本プロ麻雀連盟（note） note（新しいタブで開く）／_blank／あり | note（新しいタブで開く）／_blank／あり |
| https://x.com/JPML0306 | 日本プロ麻雀連盟（X） X（新しいタブで開く）／_blank／あり | X（新しいタブで開く）／_blank／あり |
| https://x.com/JPML_sokuhou | 日本プロ麻雀連盟 速報（X） X（速報）（新しいタブで開く）／_blank／あり | X（速報）（新しいタブで開く）／_blank／あり |
| https://www.youtube.com/channel/UCqHDeUer8bgaqswSuFP7FxQ | 日本プロ麻雀連盟（YouTube） YouTube (ja)（新しいタブで開く）／_blank／あり | YouTube (ja)（新しいタブで開く）／_blank／あり |
| https://www.youtube.com/channel/UCVL_w6mjAhRUmd7S32YXGLg | JPML English (YouTube) YouTube (en)（新しいタブで開く）／_blank／あり | YouTube (en)（新しいタブで開く）／_blank／あり |
| https://www.openrec.tv/user/jpml0306 | 日本プロ麻雀連盟（OPENREC.tv） OPENREC.tv（新しいタブで開く）／_blank／あり | OPENREC.tv（新しいタブで開く）／_blank／あり |
| https://ch.nicovideo.jp/jpml | 日本プロ麻雀連盟（ニコニコチャンネル） ニコニコチャンネル（新しいタブで開く）／_blank／あり | ニコニコチャンネル（新しいタブで開く）／_blank／あり |
| https://shop.ma-jan.or.jp/ | 日本プロ麻雀連盟 公式オンラインショップ 公式オンラインショップ（新しいタブで開く）／_blank／あり | 公式オンラインショップ（新しいタブで開く）／_blank／あり |
| https://www.worldriichi.org/ | World Riichi Championship（公式サイト） 公式サイト（新しいタブで開く）／_blank／あり | 公式サイト（新しいタブで開く）／_blank／あり |
| https://wrc2025tokyo.com/ | World Riichi Championship: Tokyo 2025 (ja) Tokyo 2025 (ja)（新しいタブで開く）／_blank／あり | Tokyo 2025 (ja)（新しいタブで開く）／_blank／あり |
| https://wrc2025tokyo.com/en/ | World Riichi Championship: Tokyo 2025 (en) Tokyo 2025 (en)（新しいタブで開く）／_blank／あり | Tokyo 2025 (en)（新しいタブで開く）／_blank／あり |
| https://wrc2022vienna.com/ | World Riichi Championship: Vienna 2022 Vienna 2022（新しいタブで開く）／_blank／あり | Vienna 2022（新しいタブで開く）／_blank／あり |
| http://wrc2017vegas.com/ | World Riichi Championship: Vegas 2017 Vegas 2017（新しいタブで開く）／_blank／あり | Vegas 2017（新しいタブで開く）／_blank／あり |
| https://x.com/riichisekai | リーチ麻雀世界選手権（X） X (ja)（新しいタブで開く）／_blank／あり | X (ja)（新しいタブで開く）／_blank／あり |
| https://x.com/WorldRiichi | World Riichi (WRC)（X） X (en)（新しいタブで開く）／_blank／あり | X (en)（新しいタブで開く）／_blank／あり |
| https://www.youtube.com/@worldriichi | World Riichi Championship（YouTube） YouTube（新しいタブで開く）／_blank／あり | YouTube（新しいタブで開く）／_blank／あり |
| http://www.ron2.jp/ | 龍龍（公式サイト） 公式サイト（新しいタブで開く）／_blank／あり | 公式サイト（新しいタブで開く）／_blank／あり |
| https://x.com/ron2jp | 龍龍（X） X（公式）（新しいタブで開く）／_blank／あり | X（公式）（新しいタブで開く）／_blank／あり |
| https://x.com/tattuan_ | たっつぁん（X） X（たっつぁん）（新しいタブで開く）／_blank／あり | X（たっつぁん）（新しいタブで開く）／_blank／あり |
| https://www.youtube.com/channel/UCmyVPm8_UQOE-qyuQBqJ9Yg | 龍龍（YouTube） YouTube（新しいタブで開く）／_blank／あり | YouTube（新しいタブで開く）／_blank／あり |
| https://ron2.jp/3/ | 龍龍（ゲームクライアント） ゲームクライアント（新しいタブで開く）／_blank／あり | ゲームクライアント（新しいタブで開く）／_blank／あり |

#### `rh_links.html`（15本→4本）

| 行き先 | 前: アクセシブルネーム／`target`／予告 | 後: アクセシブルネーム／`target`／予告 |
|---|---|---|
| https://getbootstrap.com/ | Bootstrap（新しいタブで開く）／_blank／あり | Bootstrap（新しいタブで開く）／_blank／あり |
| https://github.co.jp/features/codespaces | Codespaces（新しいタブで開く）／_blank／あり | （削除） |
| https://ezocr.net/ | EzOCR（新しいタブで開く）／_blank／あり | （削除） |
| https://favicon.io/favicon-generator/ | Favicon Generator（新しいタブで開く）／_blank／あり | （削除） |
| https://analytics.google.com/ | Google Analytics（新しいタブで開く）／_blank／あり | （削除） |
| https://developers.google.com/chart | Google Charts（新しいタブで開く）／_blank／あり | （削除） |
| https://search.google.com/search-console | Google Search Console（新しいタブで開く）／_blank／あり | Google Search Console（新しいタブで開く）／_blank／あり |
| https://www.google.com/sheets/about/ | Google Sheets（新しいタブで開く）／_blank／あり | Google Sheets（新しいタブで開く）／_blank／あり |
| https://github.com/bradjasper/ImportJSON | ImportJSON（新しいタブで開く）／_blank／あり | （削除） |
| https://www.namecheap.com/ | Namecheap（新しいタブで開く）／_blank／あり | （削除） |
| https://pagespeed.web.dev/ | PageSpeed Insights（新しいタブで開く）／_blank／あり | PageSpeed Insights（新しいタブで開く）／_blank／あり |
| https://twitobu.com/ | Twitobu（新しいタブで開く）／_blank／あり | （削除） |
| https://cards-dev.twitter.com/validator | Twitter Card Validator（新しいタブで開く）／_blank／あり | （削除） |
| https://visjs.org/ | vis.js（新しいタブで開く）／_blank／あり | （削除） |
| https://code.visualstudio.com/ | Visual Studio Code（新しいタブで開く）／_blank／あり | （削除） |

増えたリンクは無い。減った11本は上の表の「（削除）」。

### G3-10 の issue へのコメント

`Chat-Ref: CHAT-1009-SWP-03` を含む issue を検索し、第2弾の #524（共通ナビとスキップリンクの直し）を見つけた。#524 に G3-10 の決定（`style.css` に `.mj-margin-text` の右の余白を足して4ページをまとめて直す）をコメントした。#526（小さな直し）の本文にも「404 の余白」があったため、「#524 へ移す」旨を #526 にもコメントした（指示にない追加のコメント。本文の重複を避けるため）。

## 報告

- 状態: 判断待ち（プレビューを平野さんが見てから、別の指示でマージ） / 続き: CHAT-1009-SWP-06
- ブランチ: work/1009-swp-fix（CHAT-1009-SWP-02 の続き）
- ログ: https://github.com/retroeater/mj/blob/work/1009-swp-fix/docs/logs/CHAT-1009-SWP-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-swp-fix
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `jpml_links.html`・`rh_links.html`・`404.html`（`/title/nothing/here.html`・`/nothing.html`）。Workers Builds の check-run は `completed / success`
- マージ: 未（平野さんの判断待ち）
- issue: 起票なし。コメント #524（G3-10 の決定）・#526（余白を #524 へ移す旨）
- 判断が必要なこと:
  - 平野さんがプレビューで見る点（SWP-02 の分を含め、この指示の後の状態で。スマホは iPhone の Safari）:
    - `jpml_links.html`: アイコンと隣の文字がひとつながりの下線付きリンクになっているか。押す範囲が文字まで広がったか。アイコンと文字の間の空きが気にならないか（読み上げではアイコンの `alt` が無くなり、文字だけが読まれる）
    - `rh_links.html`: リンクが Bootstrap・Google Search Console・Google Sheets・PageSpeed Insights の4本だけで、前にアイコンが付き、押すと新しいタブで開くか。ページが4行だけになって寂しくないか（GitHub と、消した11本が無いこと）
    - 存在しない深い URL（プレビューのドメインに `/title/nothing/here.html`）: ナビが出て、書式が Times 体でなくゴシック体で、「トップへ戻る」を押すと `/` に行くか。右の余白が 0 で、スマホで文字が右端に付くのは第2弾で直す
    - 存在しない浅い URL（`/nothing.html`）: 同じく「トップへ戻る」があり、崩れていないか
  - 取り込みで `docs/decisions/site-review.md` が追記どうしで衝突し、両方の項を残して解いた（ログの「手順1・2」に引用）
  - #526 にも「404 の余白」の項目があったため、#524 へ移す旨をコメントした（指示は #524 のみ）。#526 の本文は直していない
  - `404.html` の右の余白（G3-10）は、この指示では直していない（決定どおり第2弾）
- 未確認の項目:
  - 実機（iPhone Safari）での見え方。写真は Chromium（1280px・390px）のみ。読み上げ（`alt` が空になった後の読まれ方）は未確認
  - 「トップへ戻る」を押して `/` に遷移する操作（`href="/"` の確認のみ）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 55b6a3cb）: https://github.com/retroeater/mj-logs/tree/main/guide/55b6a3cb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55b6a3cb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/55b6a3cb.md
