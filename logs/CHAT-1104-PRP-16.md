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

## 報告

- 状態: 作業中
- ブランチ: work/1104-prp-inv
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-inv/docs/logs/CHAT-1104-PRP-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-inv
- 確認用URL: なし（docs/logs のみ）
- マージ: 作業中
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ff3a1179）: https://github.com/retroeater/mj-logs/tree/main/guide/ff3a1179

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ff3a1179/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
