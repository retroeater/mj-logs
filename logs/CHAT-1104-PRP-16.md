# CHAT-1104-PRP-16

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-inv
- 着手時HEAD: 7beb935b0（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：live/ の公開を受けて、pro/ の公開の前に「未公開のまま進められる作業」をすべて洗い出す（読むだけ） Chat-Ref: CHAT-1104-PRP-16 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ） 貼る時機: いつでも（新しいセッションを推奨〈調べる範囲が広いため〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-prp-inv を使う（公開の準備の work/1101-prp-pub とは分ける。work/1101-prp-pub はこの指示では変えない）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-inv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
live/（#362）が公開された。pro/ の公開（#641）を「公開のマージを1本出すだけ」の状態にするため、公開の前に進められる作業を出所を問わずすべて洗い出し、平野さんが1件ずつ進めるかを決められる表にする。この指示では一覧を作るだけで、コード・生成物・issue の本文と状態は変えない（#641 への着手中・完了のコメントは書く）。
決定（2026-11-04、平野さん）

* 告知動画はまだ保留する
* live/ とつなぐ部分など、非公開のまま進められるすべての作業を洗い出して、公開直前の状態にしたい

前提（チャット側。平野さんの決定ではない）

* live/ は公開された（チャット側が 2026-11-04 12:20 JST ごろ cloudflare で確かめた: `live/index.html` に robots の meta が無い・`navbar.js` に `href="/live/"`）。`regenerate-sitemap.yml`・`llms.txt` のサイトマップの行・`docs/notes/sitemap-lastmod.md` の3か所には `sitemap-live.xml` がまだ無い（LVP の伝言。work/1101-prp-pub の側で足してほしいとの依頼）
* 公開の段の変更は work/1101-prp-pub にある（一覧は `docs/notes/pro-pages.md`「公開のマージ（#641）」、CHAT-1104-PRP-14 で cloudflare を取り込み済み）。未公開のまま入れたものは PRP-11・13 で cloudflare に入っている
* 「公開の後」とした項目は、CHAT-1101-PRP-01 のログの「2. 棚卸しの表」と CHAT-1101-PRP-02 の決定（docs/decisions/pros.md）にある。例: 表19（live/ の選手名から個別ページへ。live/ の公開の後とした）、表20（帰り道〈wayhome〉の選手から個別ページへ、#195）、#644（成績ページから個別ページへ戻るリンク。公開の前に入れるかは別に検討）、表42（X の既定のアイコン）、表45〜52（#219 の要件）、#645（旧 `jpml_pros.html` の退役。301 の後）、#646（書籍の面。2026-12-15 までに決める）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. #641 に着手中のコメントを書く（本文・状態・ラベルは変えない）。origin/cloudflare と origin/work/1101-prp-pub の差（`git diff --stat` と、`docs/notes/pro-pages.md`「公開のマージ」）と、未マージの `work/*`（`git branch -r --no-merged origin/cloudflare`）のうち pro/・live/・title/・wayhome/・成績ページ・navbar に触れるものを表にして `## 経過` に書く
2. 洗い出し: 次の出所から、pro/ の公開に関係する「まだ済んでいない作業」を全件拾い、1つの表にする（件数で済ませず全件。「略」にしない）
   * (a) live/ の公開で前提が変わったもの（表19 の live/ の選手名、`sitemap-live.xml` の3か所、live/ から pro/ へ・pro/ から live/ へのリンクの形の違い、live/ の公開で変わった生成の部品〈`generate_live_pages.py`・`load()`〉と pro/ の生成の関係）
   * (b) CHAT-1101-PRP-01 の表と CHAT-1101-PRP-02 の決定で「公開の後」「別に検討」「新サイト」とした項目のうち、今も済んでいないもの（済んだものは除き、除いた番号を書く）
   * (c) #641 の本文のチェックリストの未チェックの項目、#644・#645・#646・#219・#405・#159・#195・#238・#13・#82 の今の状態と本文の残り
   * (d) work/1101-prp-pub の公開の段の変更のうち、公開のマージで初めて確かめられるもの（例: 転送の Worker〈P1 の後〉・Search Console・X と LINE のカード）
   * (e) 公開の後の確かめ（docs/new-page-checklist.md「段2 公開する」の「公開後の確かめ」、#641 の「公開後の確かめ」）
   * 表の列: 番号・出所・項目・今の状態（確かめた場所）・大きさ（S/M/L と触るファイル）・依存・区分（次の4つのどれか: ①未公開のまま本番に入れられる〈pro/ の中だけで効く〉 ②公開のブランチ work/1101-prp-pub に足しておける〈入れると pro/ が外から見える・公開中のページから pro/ へ人を送る〉 ③公開のマージ・その当日にしかできない〈平野さんのダッシュボード作業 P1 など〉 ④公開の後でよい）・Code の見立て（見立てであって決定ではないと書き分ける）
   * 表の後に、区分ごとの件数と番号の一覧を書く
3. 記録: 上の「決定」を docs/decisions/pros.md に足す。#641 に完了のコメント（ログの表へのリンクと区分ごとの件数）を書く

止まる条件

* #641 にほかのセッションの着手中のコメントが残っている
* この指示で変えてよいのは docs/logs・docs/decisions と #641 へのコメントだけ。ほかを変える必要が出たら、変えずに「判断が必要なこと」に書く

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open。#641 にほかのセッションの着手中のコメントは無い（最後は CHAT-1104-PRP-15 の完了）。着手中のコメントを書いた
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし
- work/1104-prp-inv はリモートにもローカルにも無かったので `git checkout -b work/1104-prp-inv origin/cloudflare`（7beb935b0）で作った
- 前提の確かめ（origin/cloudflare 7beb935b0）: `live/index.html` に robots の meta なし（0件）・`navbar.js` に `href="/live/"` あり。`sitemap-live.xml` は `regenerate-sitemap.yml`・`llms.txt`・`docs/notes/sitemap-lastmod.md` の3つに無い（cloudflare・work/1101-prp-pub とも0件）。`update_sitemap_lastmod.py`・`regenerate-page.yml`・`sitemap.xml` には cloudflare 側にある

### 1. cloudflare と work/1101-prp-pub の差、未マージの work/*

work/1101-prp-pub（04c5d480c）は origin/cloudflare（7beb935b0）より 131 コミット遅れている（LVP の live/ 公開・TTL の続き・log/ など）。`git merge-tree` で取り込みを試すと衝突するのは: `.github/workflows/regenerate-page.yml`・`scripts/update_sitemap_lastmod.py`・`sitemap-pages.xml`・`docs/handover.md`（LVP の `sitemap-live.xml` と同じ行。決定どおり両方を残せる）、`scripts/generate_title_pages.py`・`docs/notes/title-pages.md`（TTL の続き「決勝メンバーの順位をそろえ、検索は入口だけに」〈8e8761046〉）、`docs/notes/ogp.md`（log/ の OGP 画像〈7e0984151〉と pro/ の OGP の移動）、`docs/decisions/pros.md`（末尾の追記どうし）、title/ 192・houou/ と ouka/ の race 8（生成物）。

work/1101-prp-pub の cloudflare からの差（生成物・ログを除く、`git diff --stat origin/cloudflare...origin/work/1101-prp-pub`）は 51ファイル: ワークフロー4（`check-assets.yml`・`check-image-links.yml`・`regenerate-page.yml`・`regenerate-sitemap.yml`）、`assets/league_race.js`・`assets/pro.js`（注記1行）、docs 15（`pros-redirect-worker.md` の新規ほか）、`img/ogp/pros → pro`、`index.html`・`jpml_pros.html`・`llms.txt`・`navbar.js`、スクリプト 8（`check_image_links.py`・`check_sns_book_images.py`・`generate_houou_pages.py`・`generate_jpml_pros.py`・`generate_pro_pages.py`・`generate_title_pages.py`・`lib/pro_search.py`・`regenerate.py`）・`update_sitemap_lastmod.py`、テスト 9、`sitemap-pages.xml`・`sitemap-pro.xml`（新規）・`sitemap.xml`、`style.css`（注記1行）、`workers/pros-redirect/` 5（新規）。中身の一覧は work/1101-prp-pub の `docs/notes/pro-pages.md`「公開のマージ（#641）」のとおり（pro/ の noindex・canonical・OGP・サイトマップ、入口、`jpml_pros.html` の 301、公開中のページからのリンク、入れないもの〈質問欄と K8〉）。

未マージの work/*（`git branch -r --no-merged origin/cloudflare`）のうち pro/・live/・title/・wayhome/・成績ページ・navbar・関係する部品に触れるもの:

| ブランチ | 最後のコミット | 触るもの | pro/ の公開との関係 |
|---|---|---|---|
| work/1101-prp-pub | 2026-11-04（PRP-14 の報告） | 公開の段の全部 | 公開のマージそのもの。cloudflare から 131 コミット遅れ、取り込みで上の衝突 |
| work/1104-lvp | 2026-11-04（CHAT-1104-LVP-15 着手） | `scripts/generate_live_pages.py`（`build_*` の引数を減らす。`load()`・`search_text()`・`page_date_order()`・`date_label()`・`thumb_url()`・`CARD_THUMB`・`Warnings` は残る）・`assets/live.js`（`?q=` は読み続ける）・live/ の生成物・`style.css` | pro/ の生成は `generate_live_pages.load()` などを使う（名前は残っている）。pro/ の「すべて（N件）」は `/live/?q=<名前>` で、live/ のトップの検索に残る。work/1101-prp-pub とは `regenerate-page.yml`・`update_sitemap_lastmod.py`・`docs/handover.md` などで衝突（cloudflare 起点の衝突と同じもの） |
| work/1104-top3 | 2026-11-04（CHAT-1104-TOP-17 着手） | `assets/houou_ask.js` を消す・`assets/league.js`・`assets/top_ask.js`・`docs/notes/pro-pages.md` ほか | K8 を戻さない決定と合う。`ask-players.json` の生成は残す（pro/ の入力と書いている）。work/1101-prp-pub とは `docs/notes/pro-pages.md` も衝突 |
| work/1104-lgn-policy | 2026-11-04（CHAT-1104-LGN-27 判断待ち） | `navbar.js`（「利用規約・プライバシー」の項目と分岐を足す）・`style.css` | navbar の別の行。work/1101-prp-pub の「連盟プロ」の行とは重ならない |
| work/1028-lgn | 2026-10-29（CHAT-1029-LGN-03 判断待ち） | `navbar.js`・`style.css`・`assets/log.js` | 古い navbar（放送対局が `video_live.html`）。pro/ の行とは重ならない |

### 2. 洗い出しの表

調べ方: (a)・(d)・(e) は origin/cloudflare（7beb935b0）と origin/work/1101-prp-pub（04c5d480c）・未マージの work/* を `git show`・`git grep`・`git merge-tree` で読んだ。(b) は CHAT-1101-PRP-01 の「2. 棚卸しの表」（1〜72・H1〜H24）と docs/decisions/pros.md（PRP-02 の ①〜⑭、PRP-03〜15）を、新しい文脈のサブエージェントに両方の ref のコードと生成物で照合させ、結果の主な点（`llms.txt` の文・既定のアイコン 12件・`apple-mobile-web-app-title`・`docs/decisions/title.md:154`）を手で確かめた。(c) は #641・#644・#645・#646・#219・#405・#159・#195・#238・#13・#82 の本文とコメントの全件をサブエージェントに読ませ、#22・#23・#24・#245・#416・#560・#637 と表に出る issue の状態を `gh api` で確かめた。本番（ryoei.pro）へのリクエストは 0件。

区分: ① 未公開のまま本番に入れられる（pro/ の中だけで効く。issue の本文・docs の直しのように公開に依存しないものもここに入れた） ② 公開のブランチ work/1101-prp-pub に足しておける（入れると pro/ が外から見える・公開中のページから pro/ へ人を送る） ③ 公開のマージ・その当日にしかできない ④ 公開の後でよい。「Code の見立て」の列は見立てであって決定ではない。

| 番号 | 出所 | 項目 | 今の状態（確かめた場所） | 大きさ（S/M/L と触るファイル） | 依存 | 区分 | Code の見立て（見立てであって決定ではない） |
|---|---|---|---|---|---|---|---|
| A1 | (a) LVP の伝言 | `sitemap-live.xml` を `regenerate-sitemap.yml` の parse の一覧に足す | 無い（origin/cloudflare の `regenerate-sitemap.yml` 84行目の一覧は sitemap.xml・pages・wayhome・saikyo・title だけ。work/1101-prp-pub は sitemap-pro だけ足した） | S（`.github/workflows/regenerate-sitemap.yml`） | なし | ② | live/ は公開済みなので、本来は cloudflare に今入れてよい変更（ワークフローなので docs のみではない）。work/1101-prp-pub で sitemap-pro と同じ行に足すと公開の日にまとめて入る。LVP は work/1101-prp-pub 側を依頼（見立て） |
| A2 | (a) LVP の伝言 | `llms.txt` のサイトマップの行に `sitemap-live.xml` を書く | 無い（origin/cloudflare `llms.txt` 76行目「サイトマップ」の説明に live の記述なし。work/1101-prp-pub も無し） | S（`llms.txt`） | なし | ② | A1 と同じ（見立て） |
| A3 | (a) LVP の伝言 | `docs/notes/sitemap-lastmod.md` の対象のサイトマップの一覧に `sitemap-live.xml` | 無い（両方の ref で0件） | S（docs） | なし | ② | docs だけなので ① 扱いで cloudflare に先に入れることもできる。work/1101-prp-pub が同じ文書の同じ節に sitemap-pro を足しているので、まとめて ② が衝突しない（見立て） |
| A4 | (a) 取り込み | work/1101-prp-pub に cloudflare を取り込み直す（131コミット遅れ。衝突: `regenerate-page.yml`・`update_sitemap_lastmod.py`・`sitemap-pages.xml`・`docs/handover.md`〈sitemap-live と sitemap-pro〉、`generate_title_pages.py`・`docs/notes/title-pages.md`〈TTL の続き 8e8761046〉、`docs/notes/ogp.md`〈log/ の OGP〉、`docs/decisions/pros.md`、title/ と race の生成物） | 未（`git merge-tree` で確かめた） | M（上の各ファイル。生成物は再生成） | なし | ② | 公開の日を軽くするため、A1〜A3 と同じ指示で取り込むのがよい。TTL の続きは期ページの行の部品を変えているので、`member_link()` の入れ直しの確かめが要る（見立て） |
| A5 | (a) live/ の公開 | live/ の対局ページの選手のカード（`mj-saikyo-card`）の行き先を、在籍者は pro/ の個別ページにする（PRP-01 表19） | 未（origin/cloudflare `live/houou/39/f.html` の選手のカードは `https://x.com/…` で `target="_blank"`。`generate_live_pages.py` に `/pro/`・`pro_search` の参照なし） | M（`scripts/generate_live_pages.py`・テスト・live/ の生成物） | work/1104-lvp（`generate_live_pages.py` を変えている）、`scripts/lib/pro_search.py`（work/1101-prp-pub にだけある） | ② | 公開中の live/ から pro/ へ人を送るので ②。title/ の `member_link()` と同じ判定を `pro_search` で使える。work/1104-lvp のマージの後に作るほうが衝突しない（見立て） |
| A6 | (a) live/ の公開 | pro/ → live/ のリンクの形（個別ページの「すべて（N件）」は `/live/?q=<名前>`。放送対局の行は `/live/<大会>/<期>/<回>.html`） | 効いている（origin/cloudflare `assets/live.js` 224行目が `?q=` を読む。work/1104-lvp でも 204行目で読む。行の行き先は live/ の生成物の path） | — | work/1104-lvp が `?q=` を変えないこと | ④ | 作業は無い。work/1104-lvp のマージの後に、pro/ の「すべて（N件）」が live/ の検索で同じ件数になるかを公開の前に1回見る（A11）（見立て） |
| A7 | (a) live/ の公開 | pro/ の生成が使う `generate_live_pages` の部品（`load()`・`Warnings`・`search_text()`・`page_date_order()`・`date_label()`・`thumb_url()`・`CARD_THUMB`・`Page.first_video`・`full_label`） | 今は合っている。work/1104-lvp の版にも全部の名前がある（`git show origin/work/1104-lvp:scripts/generate_live_pages.py` で数えた） | — | work/1104-lvp | ④ | work/1104-lvp のマージの後に `regenerate-page.yml` の `check`（pro_pages）で止まらないことを見る（A11 と同じ時機）（見立て） |
| A8 | (a) live/ の公開 | live/ の公開の記述: `docs/notes/static-generation.md` の pro/ の行（「シートの絞り込み〈五十音・所属・入会期・リーグ〉」と古い。work/1101-prp-pub の版はさらに「質問欄」がある）、`plans.json` が載っていない | 古い（origin/cloudflare 280行目・work/1101-prp-pub 280行目） | S（`docs/notes/static-generation.md`） | なし | ① | docs だけなので今 cloudflare に入れられる。work/1101-prp-pub の同じ行も同じ文にしておくと公開の日に衝突しない（見立て） |
| A9 | (a) 決定の後の記述 | `docs/new-page-checklist.md`「URL パラメータの規約」の pro/ の行: `by`（`all=1` のときの切り口のタブ）と `all` が今は使われない（PRP-09・10 で既定が全員・`?all=1` は消す）。複数の値は「,」区切り・`ouka` はシートのタブにもある | 古い（origin/cloudflare 34行目） | S（docs） | なし | ① | docs だけ（見立て） |
| A10 | (a) 部品の片付け | `assets/houou_ask.js` の1行目の注記（pro/ のトップの欄だけが使う…`top_ask()`） | work/1104-top3（CHAT-1104-TOP-17 着手）が `houou_ask.js` を消す | — | work/1104-top3 | ④ | TOP の側で片付く。pro/ 側の作業は無い。work/1104-top3 と work/1101-prp-pub は `docs/notes/pro-pages.md` で衝突するので、後に入る側で解く（見立て） |
| A11 | (a) 確かめ | work/1104-lvp・work/1104-top3 のマージの後に、work/1101-prp-pub で `regenerate-page.yml` の `check`（`pro_pages`・`title_pages`）と、pro/ の「すべて（N件）」の件数が live/ の検索と合うかを見る | 未 | S（確かめだけ） | work/1104-lvp・work/1104-top3 | ② | 公開の前の最後の取り込み（A4）の中で行うとよい（見立て） |
| A12 | (b) PRP-01 表4 / 指示の公開の段 | work/1101-prp-pub の `llms.txt` の連盟プロの行の文が古い（「五十音・所属・入会期・鳳凰戦のリーグで絞り込め、言葉で質問できる欄もある」。質問欄は外した、桜花と★ を足した） | 古い（origin/work/1101-prp-pub `llms.txt` 16行目） | S（`llms.txt`） | なし | ② | A2 と同じ指示で直せる。説明文（PRP-13 の平野さんの文）に合わせるとよい（見立て） |
| A13 | (b) PRP-05 の表記の揺れ | 「鳳凰」シートの第38期後期の3名の名前の空白（平野さんがシートで直す、決定 2026-11-01） | 未確認（シートはセッションから読めない。生成物で確かめるなら houou/ の該当期） | —（平野さんのシート） | なし | ① | 直したかは平野さんの申告で（見立て） |
| B1 | (b) PRP-01 表25 | `apple-mobile-web-app-title="連盟プロ"`（`jpml_pros.html` にだけある。301 にするとホーム画面に置いた人の名前を pro/ で保てない） | 未・未決（pro/ に0件、`jpml_pros.html` に1件。#202 open） | S（`scripts/generate_pro_pages.py`） | #202 の判断 | ① | pro/ の中だけで効くので未公開のまま入れられる。全ページに広げるかは #202（見立て） |
| B2 | (b) PRP-01 表42 | X の既定のアイコン（`default_profile_images`）の12ページを、頭の1字の丸にする（#135） | 未（origin/cloudflare の pro/ の個別ページで12件。#135 open「状況: 待ち」） | S（`scripts/generate_pro_pages.py`。`lib/x_images.py` の `DEFAULT_AVATAR_PREFIX` を使える） | なし | ① | pro/ の中だけ。#135 の「jpml_pros の13件」とは別に pro/ だけ先に直せる（見立て） |
| B3 | (b) PRP-01 表45 / #219 要件3 | 個別ページの最上部に「直近の対局結果」（#223 の速報） | 未（出さない決定、2026-10-31「いま」の面は置かない。#223 open） | L | #223 | ④ | 公開に要らない（見立て） |
| B4 | (b) PRP-01 表46 / #219 要件4 | リーグ推移の折れ線の埋め込み | 未（個別ページの h2 は 今後の予定・成績・獲得タイトル・放送対局×2） | M（`generate_pro_pages.py`・race の JSON） | なし | ④ | 入れるなら pro/ の中だけなので ① でもできるが、公開の前の作業ではない（見立て） |
| B5 | (b) PRP-01 表47 / #219 要件5 | ランキング各部門の順位 | 未（`generate_pro_pages.py` に ranking の読み込みなし） | M（`generate_pro_pages.py`） | なし | ④ | 同上（見立て） |
| B6 | (b) PRP-01 表48 / #219 要件6 | タイトル戦ごとの成績 | 一部（「獲得タイトル」と「すべての決勝進出」〈`/title/?q=`〉はある。大会ごとの成績は無い。済とみなすかの記録は無い） | 判断は S、足すなら M | 平野さんの判断 | ④ | 済とみなすかを決めるだけでもよい（見立て） |
| B7 | (b) PRP-01 表49 / #219 要件7 | 偏差値のレーダーチャート | 未（新サイト） | L | — | ④ | |
| B8 | (b) PRP-01 表50 / #219 要件8 | インタビュー記事（キンマweb「雀士名鑑」など） | 未（データ源が無い、新サイト） | M〜L | — | ④ | |
| B9 | (b) PRP-01 表51 | 書籍の面 | C12 と同じ（#646） | — | — | ④ | C12 を見る |
| B10 | (b) PRP-01 表52 / #219 要件2 | 関連する選手・Head to Head（#233）への導線 | 未（出さない決定。#233 open「状況: 保留」） | — | #233 | ④ | |
| B11 | (b) PRP-01 表55 / 決定⑪ | jpml_pros だけを対象にした open の issue の棚卸し（#24・#22・#23・#237・#245・#416・#596・#420・#247・#241・#287・#276・#226・#235・#547・#242・#257・#330・#229・#251）。#24・#22・#416 は公開で閉じ、#23 は #245 に統合（C21）、残りはまとめて棚卸し | 未（公開がまだ） | S（issue） | 公開のマージ | ④ | C21 の分は ③ |
| B12 | (b) PRP-01 表58 | 写真の自前配信（#25。#70・#349 は closed） | 未（#25 open「状況: 保留」） | L | — | ④ | |
| B13 | (b) PRP-01 表61 | 同じ年の獲得タイトルを決勝の日付順に並べる | 未（#408 open） | S（`scripts/generate_pro_pages.py` の `title_wins()`） | #408（対局日の確定） | ① | pro/ の中だけ。#408 が先（見立て） |
| B14 | (b) PRP-01 表62 | `簡略化:` 2件（`sync_live_calendar` の変更・読む先だけの再生成では pro/ を作り直さない。`generate_pro_pages.py` 34行目・`regenerate.py` 112行目） | 意図どおり残す（公開の後） | S（`scripts/regenerate.py`） | — | ④ | 毎朝の再生成で追いつくので公開に要らない（見立て） |
| B15 | (b) PRP-01 表63 | `assets/pro.js` の JS のテスト | 一部（`test_pro_filter.py`・`test_pro_favorites.py` が node で絞り込み・お気に入りの部品を動かす。並べ替え・シートの開閉・過ぎた予定を隠す処理は無い） | M（`scripts/tests/`・`assets/pro.js` の部品の切り出し） | なし | ① | pro/ の中だけ。並べ替えと予定を隠す処理は分岐があるので、公開の前に足す価値はある（見立て） |
| B16 | (b) PRP-01 表64 | 今後の予定の「ほか N件」の行を実データで確かめる・live のシートを2回読む点 | 未確認（`generate_pro_pages.py` に「ほか N件」の処理はある。実データで出た記録は見つけていない） | S（確かめ。直すなら `generate_pro_pages.py`） | なし | ① | 6件以上の予定がある選手の生成物を1つ見れば済む（見立て） |
| B17 | (b) PRP-01 表65 | 道場部ゲストの未定の語・自動更新の上限 | 未（起きていない。起きたら） | S（`scripts/sync_dojo_calendar.py`） | — | ④ | |
| B18 | (b) PRP-01 表66 | `regenerate-page.yml` の push の job が、書き込みの資格情報を持ったまま `pip install --quiet google-auth requests` を行う | 未（origin/cloudflare `regenerate-page.yml` 109行目） | S（`.github/workflows/regenerate-page.yml`） | なし | ④ | pro/ の公開とは独立。セキュリティの直しとして別の issue にするか判断（見立て） |
| B19 | (b) PRP-01 表67 / 決定⑫ | `*/players/` の canonical の見直し（#560） | 受け皿は済（#560 のコメント 2026-11-01）、見直しは未 | S | 公開のマージ・P1 | ④ | C7・C22 と同じ |
| B20 | (b) PRP-01 表68 | 濃色固定のページの明暗対応（#642、新サイト） | 未（open） | — | — | ④ | |
| B21 | (b) PRP-01 表70 | 新サイト送り・保留の issue 群（#160・#220・#224・#278・#279・#233・#4・#494・#529・#392・#225・#304・#186・#411・#415）に「pro/ ができた」とコメントする | 未 | S（コメント） | 公開のマージ | ④ | |
| B22 | (b) PRP-01 表71 | 最終更新日の表示（#239）・theme-color（#271） | 未（pro/ に0件。#239・#271 open） | S（pro/ だけなら `generate_pro_pages.py`）。全ページなら M | なし | ① | pro/ だけ先にするか、全ページの issue に任せるか判断（見立て） |
| B23 | (b) PRP-01 H5 | live/ の残り: #398（紅龍戦の予選のステージ）・#399（第13期麻雀グランプリMAX の予備予選）・#400（第3回・第4回リーチ麻雀世界選手権） | 未（すべて open） | — | — | ④ | live/ の公開の残りで、pro/ の公開には関係しない（見立て） |
| B24 | (b) PRP-01 H6 | live/ の残り: #354（他団体の選手の所属団体を表示）・#359 | #354 open、#359 closed | — | — | ④ | 同上 |
| B25 | (b) PRP-01 H7 | live/ の残り: #401（抜け番のある決定戦の対局者）・#403（対局日の残り） | 未（open） | — | — | ④ | 同上 |
| B26 | (b) PRP-01 H8 | 所属団体が `-` の332名（#396・#397） | 未（open） | — | — | ④ | 同上 |
| B27 | (b) PRP-01 H9 | RMU・麻将連合の41名のかなと X が空 | 未確認（データ側） | — | — | ④ | 同上 |
| B28 | (b) PRP-01 H10 | 麻将連合の一覧と1字違いの表記が1組 | 未確認（データ側） | — | — | ④ | 同上 |
| B29 | (b) PRP-01 H11 | 第3回世界選手権の予選を英語表記で載せる案（#400） | 未 | — | — | ④ | 同上 |
| B30 | (b) PRP-01 H17 | 帝王戦の10行の掲載範囲（#590） | 未（open） | — | — | ④ | 同上 |
| C1 | (c) #641 本文 | 本文のチェックリストが1件も付いていない（未チェック 26件: 公開でやること 17・公開後の確かめ 9）。済んだもの（PRP-03〜14 で work/1101-prp-pub に入れた・PRP-11/13 で cloudflare に入れた）と、前提が変わったもの（K8・Worker `mj`・トップの質問欄の確かめ）がある | 未更新（#641 の本文、2026-11-04 に読んだ） | S（issue の本文） | なし | ① | この指示では本文を変えない（止まる条件）。本文の直しは別の指示で（チャット側か平野さん）。項目ごとの今の状態は C2〜C9 と上の表（見立て） |
| C2 | (c) #641 本文「公開でやること」 | 「トップの質問欄の答えに出た選手のカードの行き先を pro/ の個別ページにする（K8）」 | 前提が変わった（PRP-11 で pro/ のトップの質問欄を外し、PRP-14 で K8 は戻さないと決めた） | S（issue の本文だけ） | なし | ① | 本文から外すか「行わない」と書く（見立て） |
| C3 | (c) #641 本文「公開でやること」 | 「`jpml_pros.html?name=` から個別ページへの 301 を Worker（`mj`）で行う」 | 形が変わった（別の Worker `mj-pros-redirect`、PRP-05。work/1101-prp-pub） | S（issue の本文だけ） | なし | ① | 本文の「Worker（`mj`）」を直す（見立て） |
| C4 | (c) #641 本文「公開でやること」 | 「`jpml_pros.html` → `/pro/` の 301（`_redirects`）」 | 形が変わった（`_redirects` でなく転送の Worker が `?name=` なしも `/pro/` へ送る、`docs/notes/redirects.md` 68行目〈work/1101-prp-pub〉） | S（issue の本文だけ） | なし | ① | C3 と一緒に直す（見立て） |
| C5 | (c) #641 本文「公開後の確かめ」 | 「トップの質問欄に問いを送り、答えが返ることを確かめる」 | 前提が無い（質問欄を外した） | S（issue の本文だけ） | なし | ① | 外す（見立て） |
| C6 | (c) #641 本文「公開後の確かめ」 | 「X の告知と告知動画（これまでのテンプレート、同時に公開）」 | 保留（2026-11-04 の決定: 告知動画はまだ保留） | — | 平野さん | ④ | E7 と同じ |
| C7 | (c) #641 本文「公開後の確かめ」 | 「#560 のコメント（`*/players/` の canonical の見直し）のきっかけが来たことを #560 に書く」 | 未（#560 open） | S（コメント） | 公開のマージ・P1 | ③ | 公開と 301 の後に書く |
| C8 | (c) #641 本文「公開でやること」 | 閉じる issue: #13・#238・#24・#82・#22・#416・#159（#405 は段位・性別だけ残す、#23 は #245 に統合） | すべて open（2026-11-04 に gh で確かめた） | S（issue） | 公開のマージ | ③ | 閉じるのは公開の後。#13 は pro/ の個別ページに `Person` が入っている（`pro/sho.shiratori.23/index.html` の JSON-LD に `"@type": "Person"` 1件）ので「対応済み」で閉じられる見込み（見立て） |
| C9 | (c) #641 本文 / #82 | #82（SNS シェア・URL コピー）を公開で閉じる理由: 共有ボタンは pro/ に置かない（2026-10-31）。本文に PRO-05 で無くなった項目（選手別 og:image・共有ボタン・#82 へのコメント）が残る（PRP-01 表22） | 未（本文のまま） | S（issue の本文） | なし | ① | 閉じる理由を「pro/ には置かない決定・現行サイトの分は #409」と本文かコメントに書く（見立て） |
| C10 | (c) #644 | 成績ページ（houou/・ouka/・jwrc/・saikyo/ の `players/`、wrcr/ は着手時に決める）の名前の面から `/pro/<slug>/` へ戻るリンク | 未（open、コメント0。公開の前に入れるかは別に検討、2026-11-01） | M（`scripts/generate_houou_pages.py`・`scripts/generate_saikyo_pages.py`・`scripts/lib/pro_search.py`〈work/1101-prp-pub にだけある〉・テスト・成績ページの生成物） | `pro_search.py`（公開のブランチ）、#637（成績ページの二重定義） | ② | 公開中の成績ページから pro/ へ人を送るので ②。順位変動の `add_pro_slugs()` と同じ `pro_search.load_slugs()` で作れる（見立て） |
| C11 | (c) #645 | 旧 `jpml_pros.html` の退役（生成・CSS・JS・テスト・ワークフロー・文書の片付け。`check_image_links.py`・`check-image-links.yml`・`style.css`・`table.js`・`generate_jpml_pros.py`・`regenerate.py` の `jpml_pros` ほか） | 未（open、着手のきっかけは 301 が本番に入ること） | L（多数） | 公開のマージ・P1 | ④ | `pros-redirect-worker.md` のとおり P1 の後 |
| C12 | (c) #646 | 個別ページに書籍の面を出すか（2026-12-15 までに決める。`data/rakuten_books.json` の保存期限 2026-12-22） | 未（open、公開の後の作業、決定⑬） | M（決めた後） | 平野さんの判断 | ④ | 期限があるので、公開の時期によらずカレンダーで追う（見立て） |
| C14 | (c) #219 | 本文の冒頭「新サイト（#296）で対応」が、新サイト送りの取り消し（2026-10-31）の後も残る | 未（本文のまま） | S（issue の本文） | なし | ① | #296 の「新サイト送りにするとき・やめるとき」を読んで直す（見立て） |
| C15 | (c) #405 | 名簿の属性のうち段位・性別を pro/ に載せるか | 保留（出さないと決めた、決定⑪。open・親 #296・「状況: 保留」） | M | 平野さんの判断 | ④ | 公開では閉じない（#641 本文どおり）。本文は pro/ 向けなのに親が #296（新サイト送り）のまま。親を外すかは #296 の手順で（見立て） |
| C16 | (c) #159 | `?name=` の 301（`jpml_pros.html?name=` だけ、Worker `mj-pros-redirect`） | 作った（work/1101-prp-pub、未マージ）。#159 は open・「状況: 保留」 | — | 公開のマージ・P1 | ③ | 公開で閉じる（#641 本文）。本文の「301後の Search Console での監視」は D6 に含まれる（見立て） |
| C17 | (c) #195 | 帰り道（wayhome）の各話・一覧のヒーローの選手のリンク（今は X）を個別ページへ。差し替えは `scripts/lib/wayhome.py` の `build_player_links_html()` の1か所 | 未（open、「状況: 待ち」。origin/cloudflare の wayhome/ の各話の選手は `https://x.com/…`） | M（`scripts/lib/wayhome.py`・テスト・wayhome/ の生成物 約数百ページ） | `pro_search.py`（公開のブランチ） | ② | #641 本文は「公開の後」。公開中の wayhome/ から pro/ へ人を送るので、公開の前に作るなら ②（見立て） |
| C18 | (c) #238 | 表の行全体を個別ページへのリンクにする（選手が主語の表） | open・「状況: 保留」・親 #296。#641 本文で公開のときに閉じる | — | 公開のマージ | ③ | pro/ のトップの表は行全体がリンク（`mj-pro-row-link`）。ほかの表（ランキングなど）はそのまま閉じる扱い（見立て） |
| C19 | (c) #13 | 構造化データ。残りは選手の `Person`（と video_wayhome の `duration`） | `Person` は pro/ の個別ページに入っている（`person_json_ld()`、生成物で1件）。open・「状況: 保留」・親 #296 | — | 公開のマージ | ③ | 公開の後に「対応済み」とコメントして閉じる（#641 本文）。`duration` は指摘されていないので閉じてよい見込み（見立て） |
| C20 | (c) #82 | SNS シェア・URL コピーボタン | open・「状況: 保留」・親 #296。#641 本文で公開のときに閉じる | — | 公開のマージ | ③ | C9 の理由づけと一緒に |
| C21 | (c) #641 本文 | 閉じる issue の jpml_pros 専用: #24（五十音の行タブ）・#22（X・note・YouTube 列のかな順）・#416（列見出しの略語の凡例）。#23 は #245 に統合 | すべて open | S（issue） | 公開のマージ | ③ | pro/ に置き換わるので閉じる（決定⑪） |
| C22 | (c) #644 本文 / #560 | `*/players/` の canonical の見直し（#560） | open | S | 公開のマージ・P1 | ④ | C7 のきっかけのコメントの後 |
| C23 | (c) 親の付け外し | #405・#238・#82・#13 が #296（新サイト送り）の子のまま。#238・#82・#13 は #641 で閉じる、#405 は残す | 未 | S（issue の親・コメント） | なし | ① | 閉じるものは閉じるときに #296 の手順で外せばよい。残る #405 だけ先に見直す（見立て） |
| D1 | (d) 公開の段 | 転送の Worker `mj-pros-redirect` の Workers Builds のプロジェクトを作る（平野さんの P1、`docs/notes/pros-redirect-worker.md`「P1」） | 未（公開のマージの後に作る決定、2026-11-01） | —（ダッシュボード） | 公開のマージ | ③ | P1 の前は `/jpml_pros.html` が今の静的なページのまま（navbar から外れるので流入は少ない） |
| D2 | (d) 公開の段 | P1 の後の確かめ（本番へ数件: `/jpml_pros.html`・`?name=在籍者`・旧名・在籍者でない名前の 301 の行き先） | 未 | S | D1 | ③ | `docs/notes/pros-redirect-worker.md`「P1 の後の確かめ」 |
| D3 | (d) 公開の段 | `docs/notes/redirects.md`「棚卸しの対象」の `/jpml_pros.html` の「入れた日」を、仮の 2026-11-01 から P1 の日に直す | 未（work/1101-prp-pub 61行目が仮の日付） | S（docs） | D1 | ③ | P1 の日にしか決まらない |
| D4 | (d) 公開の段 | Worker の 301 が月次の集計（GraphQL の `edgeResponseStatus`）に出るかを確かめる | 未（公式の文書に書かれていない。P1 の翌月の月次で見る、`pros-redirect-worker.md` 62行目） | S | D1 | ④ | 2026-12-01 の月次（P1 が11月中なら） |
| D5 | (d) 公開の段 | X と LINE のカード（og:image が `img/ogp/pro/index-white.png` に移る、og:title「<名前> \| 連盟プロ \| …」） | 未（公開の後でないと貼れない） | S | 公開のマージ | ③ | `docs/notes/ogp.md` の手順（`?x=<数字>`、デプロイ直後の1〜2分は避ける） |
| D6 | (d) 公開の段 | Search Console: 公開前の数字を残す（段2「着手前」）、公開後に `sitemap.xml` を再送信し、入口を URL 検査でインデックス登録をリクエスト | 未 | —（平野さんのダッシュボード） | 公開の前（数字）・公開のマージ（再送信） | ③ | 公開前の数字は公開の当日の前に残せる（平野さん） |
| D7 | (d) 公開の段 | canonical（トップと個別ページ）と noindex の無いことを本番で確かめる（応答ヘッダ `x-robots-tag` も。`_headers` は workers.dev にだけ noindex を付けるホストのルール） | 未 | S（curl 数件） | 公開のマージ | ③ | プレビュー（workers.dev）は `_headers` で noindex が付くので、本番でしか確かめられない |
| D8 | (d) 公開の段 | `jpml_pros.html` の OGP 画像の参照が `img/ogp/pro/` に変わる（画像を移すので、公開のマージの前のキャッシュは古いまま） | 未（work/1101-prp-pub で `img/ogp/pros → pro`） | — | 公開のマージ | ③ | 旧 URL の画像は 404 になる。X のカードのキャッシュ（D5）と一緒に見る |
| E1 | (e) 段2 公開後 | Workers Builds の check-run が success | 未 | S | 公開のマージ | ③ | これだけで本番反映としない |
| E2 | (e) 段2 公開後 | 本番の HTML: 200・noindex なし・`x-robots-tag` なし・`navbar.js`・サイトマップ・`llms.txt` に URL・`_redirects`（`/pro` → `/pro/`）の転送 | 未 | S | 公開のマージ | ③ | D7 と同じ時機 |
| E3 | (e) 段2 公開後 | 質問欄のある大会のページの答えの確かめ | 対象外の見込み（pro/ は質問欄を外した。入口は共通ナビの AI 検索） | — | — | ④ | 共通ナビの AI 検索で、選手の答えのカードが pro/ を指すかは TOP の側の話（見立て） |
| E4 | (e) 段2 公開後 | 平野さんの実機: navbar から開けて、見え方・操作が崩れていない | 未 | — | 公開のマージ | ③ | セッションからは本番の HTML まで |
| E5 | (e) 段2 公開後 | チャット側: 登録の結果を確かめる予定（1週間後、件名【R#641】）をカレンダー「ryoei.pro」に作る | 未 | — | D6 | ③ | チャット側の作業 |
| E6 | (e) 段2 公開後 | 親の issue・作る issue（#219）に公開した日とマージの SHA をコメントする | 未 | S | 公開のマージ | ③ | |
| E7 | (e) 段2 公開後 | X で告知（投稿文と告知動画、`docs/notes/page-announcement.md`） | 保留（2026-11-04 の決定: 告知動画はまだ保留） | — | 平野さんの判断 | ④ | |
| E8 | (e) 段2 着手前 | 公開の条件（平野さんの実機での最終確認、先に済ませる issue）が揃ったことを #641 に書く | 未 | S | 平野さん | ③ | 公開の指示の着手前 |
| E9 | (e) 段2 着手前 | 準備の指示の着手時に `regenerate-page.yml` の `check` を作業ブランチで回す | PRP-14 の生成で通っている。取り込み直し（A4）の後にもう一度 | S | A4 | ② | A11 と同じ |
| E10 | (e) 段2 変更 | 「公開していない」と書いたコメント・docstring・生成物の説明、`docs/notes/static-generation.md`「ページの一覧」・`docs/handover.md` の「未公開」の記述を直す | work/1101-prp-pub でおおむね済み（`generate_pro_pages.py`・`pro-pages.md`・handover 131行目は「公開は #641」）。`static-generation.md` 280行目の pro/ の行は古いまま（A8） | S | A8 | ② | A8 で一緒に直す |

#### 区分ごとの件数と番号（全83件）

- ① 17件: A8・A9・A13・B1・B2・B13・B15・B16・B22・C1・C2・C3・C4・C5・C9・C14・C23
- ② 11件: A1・A2・A3・A4・A5・A11・A12・C10・C17・E9・E10
- ③ 20件: C7・C8・C16・C18・C19・C20・C21・D1・D2・D3・D5・D6・D7・D8・E1・E2・E4・E5・E6・E8
- ④ 35件: A6・A7・A10・B3・B4・B5・B6・B7・B8・B9・B10・B11・B12・B14・B17・B18・B19・B20・B21・B23・B24・B25・B26・B27・B28・B29・B30・C6・C11・C12・C15・C22・D4・E3・E7

#### (b) で除いた番号（済み・対応不要・公開の段で入れたもの）

- 済（work/1101-prp-pub に入れた）: 1（noindex を外す）・2（navbar）・3（`index.html` のサイドメニュー）・4（`llms.txt` の行の差し替え。文は A12）・5（サイトマップ）・6・7（301。別の Worker）・8 の検知の付け替え（`check_image_links.py` の `TARGET_HTML` が `pro/index.html`。片付けは C11）・9・10（canonical）・11（canonical の文書）・16（OGP の画像の移動）・17（順位変動のアイコン）・18（title/ の期ページの決勝メンバー）・24（「未公開」の記述。`static-generation.md` だけ A8）・33（全員を index させる）
- 済（cloudflare に入った）: 12（live/ を先に公開、dd0746383）・13（題の形）・35（並べ替えのボタン 44px）
- 決定どおりで作業なし: 14（トップに `ItemList` を足さない）・15（K8。PRP-11・14 で行わないと決めた。#641 本文の直しは C2）・36（alt は空のまま）・57（選手別の OGP 画像。#165 closed）・69（支部・入会期ごとの静的なページ。#285 closed）
- 済（issue・文書）: 22（#641 本文から無くなった項目を外した。本文の別の古さは C1〜C5）・53（issue と文書の古い記述。`docs/decisions/title.md:154` は PRP-02 で「年表のページの節で、直さない」と確かめた〈CHAT-1101-PRP-02 のログ 108行目〉ので除いた）・54（#405 の題と本文）・⑩（#219・#405・#296 の整理、#159 を #296 から外した）
- 済（そのほか）: 59（iPhone の点検、平野さんの申告）・72（配信の上限との比。`check_asset_limits.py` を `check-assets.yml` が毎回実行）
- live/ の公開で済: H1〜H4・H12・H15・H16・H18・H20・H21・H23・H24
- 対応不要・情報のみ: 34・37〜41・43・44
- 公開に含める手順（後回しではない）: 26・27・60。公開後の確かめ 28〜32 は E1〜E7 に、23（#238・#13 へのコメント）は C18・C19 に入れた
- #219 の要件（C13 として書きかけたもの）は B3〜B10 に分けた

#### (c) の issue の今の状態（2026-11-04）

| issue | 状態・ラベル | 残り |
|---|---|---|
| #641 | open・「状況: 待ち」「分野: SEO/AIO」「分野: UI/UX」 | 本文のチェック 0件・未チェック 26件（公開でやること 17・公開後の確かめ 9）。古い所は C1〜C5・C9 |
| #644 | open・「分野: UI/UX」・コメント0 | C10 |
| #645 | open・「状況: 待ち」 | C11（着手は 301 が本番に入った後） |
| #646 | open・コメント0 | C12（2026-12-15 までに決める） |
| #219 | open | B3〜B10・C14（本文の冒頭「新サイト（#296）で対応」が古い） |
| #405 | open・「状況: 保留」・親 #296 | C15・C23 |
| #159 | open・「状況: 保留」 | C16（公開で閉じる） |
| #195 | open・「状況: 待ち」 | C17 |
| #238 | open・「状況: 保留」・親 #296 | C18（公開で閉じる） |
| #13 | open・「状況: 保留」・親 #296 | C19（pro/ に `Person` あり。公開で「対応済み」で閉じる） |
| #82 | open・「状況: 保留」・親 #296 | C9・C20（公開で閉じる。閉じる理由を書く） |

## 報告

- 状態: 完了
- ブランチ: work/1104-prp-inv
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-PRP-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-inv
- 確認用URL: なし（docs/logs と docs/decisions のみ）
- マージ: 済み（ドキュメントのみ。SHA は #641 の完了のコメント）
- issue: #641
- 判断が必要なこと:
  - 表の83件のうち、どれを進めるか（`## 経過`「2. 洗い出しの表」。① 17件・② 11件・③ 20件・④ 35件）。Code の見立てでは、公開直前の状態にするのに要るのは ② の A1〜A4・A11・A12（`sitemap-live.xml` の3か所・cloudflare の取り込み直し〈131コミット遅れ〉・`llms.txt` の文）と ① の A8・A9（docs の古い記述）。A5（live/ の選手のカード）・C10（#644）・C17（#195）は公開中のページから pro/ へ人を送る ② で、公開の前に入れるか「公開の後」のままにするかは平野さんの判断
  - #641 の本文が古い（C1〜C5・C9: チェック 0件、K8・Worker `mj`・`_redirects` の 301・質問欄の確かめが前提と違う）。この指示では本文を変えていない。直すのはチャット側か別の指示か
  - B18（`regenerate-page.yml` の push の job が書き込みの資格情報を持ったまま `pip install` する）は pro/ と独立したセキュリティの論点。issue にするか
  - B1（`apple-mobile-web-app-title`）・B2（X の既定のアイコン 12件）・B13・B15・B16・B22 は pro/ の中だけで効き、未公開のまま入れられる（①）。公開の前に入れるか
- 未確認の項目: A13（「鳳凰」シートの第38期後期の3名の空白を直したか。シートはセッションから読めない）、B27・B28（live/ のデータ側の残り）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 34170645）: https://github.com/retroeater/mj-logs/tree/main/guide/34170645

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/34170645/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
