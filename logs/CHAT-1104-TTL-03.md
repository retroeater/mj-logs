# CHAT-1104-TTL-03

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 811040fef

## 指示

【Claude作成】Claude Code 向け指示：TTL-02 の続き（Workers Builds のプレビューを出し直し、確認用 URL を出す。中身は変えない） Chat-Ref: CHAT-1104-TTL-03 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1104-TTL-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-ttl を続けて使う（TTL-02 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物（title/ の HTML・sitemap の lastmod など）の衝突は docs/notes/branch-operations.md の解き方で生成し直す

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TTL-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-TTL-03` を足す。

目的
TTL-02 のコミット f4b38e70a の Workers Builds のプレビューが失敗し（同じ時間帯〈2026-11-03 16:27〜16:32Z〉に本番・ほかのブランチのビルドも失敗。その後のビルドは CHAT-1104-AIC-33・PRP-09 のログで success）、確認用 URL が無い。title/ の中身は変えずに、プレビューを出し直す。
決定（2026-11-04、平野さん）

* なし（TTL-02 の「判断が必要なこと」〈写真カード案と行の案・仮置き〉は、プレビューを見てから答える）

前提（チャット側。平野さんの決定ではない）

* 失敗は一時的なものと見ている（要確認）。ログの push（この指示のログ・TTL-02 のログの状態の直し）でビルドが付けば足りる
* 本体のページは写真カード案（`LAYOUT_CARDS`）のまま。比較ページ `/title/compare-layout.html` も残す

手順

1. 上の手順0のログの push でビルドが付くのを待つ（CLAUDE.md の待機の上限のとおり）。origin/cloudflare を取り込んだときは、生成し直した後のテストを通してから push する
2. ビルドが success なら、プレビューで TTL-02 の手順3の確かめのうち、入口（既定・`?year=2025`・`?q=白鳥`）・鳳凰戦・第41期鳳凰戦・比較ページの表示を 360px の明・暗で見て、ローカルの結果と食い違いが無いことを報告に書く（UA に mj-internal を含める）
3. ビルドが failure なら、check-run の内容（`docs/notes/cloudflare.md`「ビルドが Cloning で失敗したとき」）と、同じ時間帯の本番・ほかのブランチのビルドの成否を報告に書き、平野さんのダッシュボードの確認（「Retry build」）が要るかを「判断が必要なこと」に書く

止まる条件

* title/ の HTML・`generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js` に、origin/cloudflare の取り込みと生成し直し以外の差分が出た
* 取り込みで `generate_title_pages.py` が衝突した（解かずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には TTL-02 の (1)(2)(3) をそのまま引き継いで書く（取り込みで変わった点があればそれも）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL、比較ページの URL を含む）を書く。Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1104-TTL-03'` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし。対象 issue の指定なし
- 作業ブランチ: `work/1104-ttl`（ローカルと `origin/work/1104-ttl` は同じ 811040fef、未マージ）を続けて使った
- 手順0: このログの「指示」欄の末尾の行は指示文の最後の行と一致。TTL-02 のログの `## 報告` の状態に ` / 続き: CHAT-1104-TTL-03` を足し、このログと一緒に push した（d790d8ae8）
- 前提との食い違い（ビルドの起動）: 非本番のビルドの watch paths は `docs/**` を除外している（docs/notes/cloudflare.md「Build watch paths: Exclude（非本番のトリガー…）」）。ログだけの push の d790d8ae8 には、Workers Builds の check-run が付かなかった（check-runs が空）
- 手順1: `origin/cloudflare` は作業ブランチの祖先でなかった（34コミット先行）。`git merge-tree --write-tree` で衝突なしを確かめてから `git merge origin/cloudflare` で取り込んだ（b8f4e5f61、衝突なし）
  - 取り込んだ変更（docs 以外）: `.github/workflows/research-candidates.yml`・`pro/` の6ページ・`scripts/requirements-ogp.txt`・`scripts/research_candidates.py`・`scripts/tests/` の2本・`top/index.html`・`workers/ask/` の4本。`generate_title_pages.py`・`assets/title.js`・`style.css`・`navbar.js`・title/ は差分なし（`git diff HEAD~1 HEAD -- …` が空）。title/ の生成に使う `scripts/lib/`・`generate_saikyo_pages.py` も変わっていないので、生成し直しはしていない
  - `python3 -m unittest discover -s scripts/tests` → 1807 tests OK を確かめてから push した
- ビルド: b8f4e5f61 の「Workers Builds: mj」が success（Build ID 0d5e0f46-91a8-45ba-b313-e7d2ee4e4ab0、Version ID 125654d9…、completed 2026-11-03T16:58:30Z）。`.output.summary` に Preview URL と Preview Alias URL が出た（URL はターミナルへの最終報告にだけ書く）
- 手順2（プレビューの確かめ、版ごとの URL、UA に mj-internal）:
  - TTL-02 の撮影のスクリプト（`docs/logs/CHAT-1104-TTL-02/shoot.js`）を 360px の明・暗に絞って回した（9ページ × 2 = 18枚）。はみ出し・切れ・重なりは 18ページとも 0件（ローカルと同じ）
  - 入口（既定・`?year=2025`・`?q=白鳥`）・鳳凰戦・第41期鳳凰戦・比較ページ（写真カード案・行の案）の 360px の明・暗 14枚を、TTL-02 のローカルの撮影と画素で比べた: 13枚は差なし、入口の暗い配色だけ 5×6px の範囲（写真の中）に差があり、差が 40 を超える画素は0（読み込みの揺れ）。食い違いなし
  - 応答: `/title/compare-layout.html`・`/title/`・`/title/houou/41.html` とも HTTP 200、`x-robots-tag: noindex`（workers.dev の既定）。比較ページは `<meta name="robots" content="noindex">` を持つ

## 報告

- 状態: 判断待ち
- ブランチ: work/1104-ttl
- ログ: https://github.com/retroeater/mj/blob/work/1104-ttl/docs/logs/CHAT-1104-TTL-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: 入口（既定・?year=2025・?q=白鳥）・鳳凰戦・第41期鳳凰戦・比較ページ（/title/compare-layout.html）。この版の印: 入口の題名が「現タイトルホルダー ▾」（ナビが白地で「タイトル戦」が紺）
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - （TTL-02 の (1)(2)(3) を引き継ぐ。origin/cloudflare の取り込み〈b8f4e5f61〉で変わった点は無い）
  - (1) 写真カード案と行の案のどちらにするか。本体のページは今は写真カード案（`LAYOUT = LAYOUT_CARDS`）。比較ページ /title/compare-layout.html のラジオボタンで切り替えて見られる（入口〈年のシートも動く〉・鳳凰戦・王位戦・第41期鳳凰戦・第42期十段戦）。決まったら使わない案のコード・ラジオボタン・比較ページを消す
  - (2) 仮置きの一覧（今の値と理由）:
    - 共通ナビの判定: `<body>` の class `mj-title-n-page`（前提の案は「`/title/` のパス」。最強戦・live/ と同じ見分け方にそろえた、レビューの指摘）
    - 期ページのパンくずの最後: 「第41期」（対局日を外した。13px の1行で「タイトル戦 / 鳳凰戦 / 第41期（2025年2月1日）」は 360px で折り返し、題名の2行目と同じ日付が2度出るため）。JSON-LD の BreadcrumbList も同じ並びに変わる
    - 期ページの題名: 期の題（「第41期鳳凰戦」28px 太字）と2行目に対局日（13px 灰色、最強戦の対局のページと同じ）。日付が空なら2行目なし。h1 の読み上げは「第41期鳳凰戦 決勝結果2025年2月1日」
    - 大会ページの題名: 大会名（28px）と2行目に期の範囲「第1期〜第42期」（13px 灰色。今まで出していなかった期の範囲を題名の2行目に使った）
    - 節の見出し（決勝ライブ・決勝動画・帰り道・成績）: 最強戦の「歴代最強位」と同じ 13px 灰色（`.mj-saikyo-n-label`）。上の余白 8px を足した（決まった値の表に無い）
    - 節の間隔: `.mj-title-section` の下 16px（今まで 32px。面の間隔 16px にそろえた）
    - 「成績」のリンク: 面の中の押せる行（名前の字＋›）。今までは本文のリンク
    - 検索欄: 最強戦・live/ の白地のページの検索欄（`.mj-saikyo-n-search`）を題名の下に。form にはしない（Enter で再読み込みしない、今の動きのまま）。0件の知らせは欄の下 13px 灰色（出ている間は欄のまとまりが 44px → 72px）
    - 年のシート: 題「年を選ぶ」、項目は「現タイトルホルダー」と「2026年優勝者」…（`?year=` のリンク）
    - navbar の固定をやめた（スクロールでナビも流れる。最強戦の新しいページと同じ）
    - 行の案の入口: 写真・選手名（16px）・その下に大会名（13px 灰色）・›。押すと大会ページ（年を選んだときは期ページ、大会名に期を足す帯の文字のまま）
    - 行の案の大会ページ: 左に期（13px 灰色、最小幅 3.5rem〈決まった値の表に無い。「第42期」の4字が入る幅〉）・写真・名前・›
    - 行の案の期ページ: 右端に順位（優勝は金のピル、ほかは「2位」の 13px 灰色。前提の「灰色の数字」に「位」を付けた）と ›（X へ、新しいタブ）。X の無い選手は › なしで押せない
    - 写真の無い選手の行: 名前の頭の1字の灰色の丸（最強戦と同じ）。名前の記録が無い行は「—」
    - 検索結果の期のカード: 面の色・角丸 12px・影なし。順位のバッジは灰色の地に灰色の字（最強戦の敗退のピルと同じ）、優勝の淡い金（#fff3cd／#8a5a00）はそのまま
    - 写真カード（影・帯・名前・ホバーで浮く動き）と動画のカードは変えていない。メンバー限定のバッジ（#212529 の地）は暗い配色では地の縁が見えない
  - (3) `work/1101-prp-pub` との衝突の見込み:
    - `scripts/generate_title_pages.py`: `member_card_html()`（この作業ブランチで `member_link()`・`member_holder()`・`members_html()` に分けた）、`period_body()`（引数と本体を書き換えた）、`pages()`（prp-pub が `slugs` を読む行）。解き方の案: prp-pub の行き先の決め方（個別ページのある在籍者は `pro_search.page_path(slug)`・新しいタブにしない、無ければ X）を `member_link()` に移し、`slugs` を `period_body()` → `members_html()` → `member_holder()` → `member_link()` へ渡す（比較ページの `compare_body()` の `members_html()` の呼び出しにも渡す）。import の `pro_search` を足す
    - title/ の期ページの HTML（365ページ）と `sitemap-title.xml` の lastmod: 解いた後のスクリプトで生成し直す（docs/notes/branch-operations.md の生成物の衝突の解き方）
    - `navbar.js`・`style.css`: prp-pub が変えた行（「連盟プロ」の href・`/pro/` のコメント、houou/ の節）とは離れており、`git merge-tree` では衝突なし（`generate_title_pages.py` だけが衝突）
  - そのほか: `scripts/promo_video/title/capture.mjs` が `.mj-filterbar`・`#title_select` を見ている（`#title_select` はこの変更の前から無い。title/ の固定バーも今回なくなった）。docs/notes/design.md の「共通ナビ（新しい形）」の適用範囲には title/ を足していない（案が決まってから）
  - 前提の「ログの push でビルドが付く」は、非本番の watch paths が `docs/**` を除外しているため当たらなかった。今回は origin/cloudflare の取り込みの push でビルドが付いた（TTL-02 の f4b38e70a の失敗は、同じ時間帯に本番も失敗しており一時的なものと見られる。ダッシュボードの「Retry build」は不要）
- 未確認の項目:
  - 実機（iPhone）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6865148b）: https://github.com/retroeater/mj-logs/tree/main/guide/6865148b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6865148b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
