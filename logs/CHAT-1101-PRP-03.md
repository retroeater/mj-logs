# CHAT-1101-PRP-03

- 着手日時: 2026-11-01
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 2caddbbcc

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その1: 生成・メタ・導線）を作業ブランチに作り、プレビューで止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-03
マージ: 判断待ちで止まる（この作業ブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: いつでも（CHAT-1101-PRP-02 と別のセッションで同時に動かしてよい。PRP-02 は別の作業ブランチ）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-prp-pub を使う（同じ系列の work/1101-prp・work/1101-prp-docs と分ける。公開までこのブランチを続ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-prp-pub origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
pro/ の公開（#641）のうち、生成・メタ情報・導線の変更を作業ブランチに作り、プレビューで確かめられる状態にする。転送（`jpml_pros.html` の 301・Worker の `?name=` の 301）・検知の付け替え・質問欄のカード（K8）・`playerUrl()`・title/ の優勝者は、このブランチの続きの指示（CHAT-1101-PRP-04 の予定）で行う。番号は CHAT-1101-PRP-01 のログの `## 経過`「2. 棚卸しの表」（以下「表」）の番号。

### 決定（2026-11-01、平野さん）
- ① 個別ページ 1,098件もサイトマップに載せる。形は saikyo/・title/ と同じ別ファイル（`sitemap-pro.xml`）（表5）
- ② 名前の面だけのページも含め、全員を index させる（表33）
- ⑤ 個別ページの title と og:title は「<名前> | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」にする（表13）
- ⑦ トップの表の並べ替えのボタンを 44px の基準まで広げる。表の行の高さ 40px は変えない（表35）
- ⑧ 画像の alt は空のままにする（表36）
- pro/ のトップに canonical（自分自身）を付ける（表9）。トップページ（`index.html`）のサイドメニュー・`llms.txt` の「プロ」の行・`sitemap-pages.xml` の `jpml_pros.html` を pro/ に置き換える（表3・4・5）
- 前の決定から: noindex を外す（表1）、navbar の「連盟プロ」の行き先を `/pro/` に（表2）、個別ページに canonical（og:url と同じ値、K13、表10）、トップの OGP 画像を `img/ogp/pros/` から `img/ogp/pro/` へ移す（写さない。`jpml_pros.html` の参照も移した先に、K14、表16）、「未公開」の記述を直す（表24）
- 公開の時期は点検が済んだら。live/（#362）を pro/ より先に公開する（マージはこの指示ではしない）

### 前提（チャット側。平野さんの決定ではない）
- 実物の場所（チャット側が origin/cloudflare の 50dcad88 で見た。要確認）: noindex は `scripts/generate_pro_pages.py:52` の `NOINDEX_TAG`、個別ページの title は同じファイルの `PageMeta(title=f"{pro.name} | {SECTION} | ryoei.pro", …)`（726行目付近）。並べ替えのボタンは `.mj-pro-sort`（`assets/pro.js`）。サイトマップの束ねは `sitemap.xml`（コメントに配下のファイルの一覧がある）と `scripts/update_sitemap_lastmod.py`（`SITEMAP_*` の定数）
- `sitemap-pro.xml` は、saikyo/・title/ の生成スクリプトが別ファイルを書く形に倣う。lastmod は手で書かない（`update_sitemap_lastmod.py --from-git`、docs/notes/sitemap-lastmod.md）
- `llms.txt` の「プロ」の行は、今の pro/ の中身（1,098名・個別ページ・絞り込み・質問欄）に合わせ、個別ページの URL の形を書くかはほかの行の書き方に倣う（チャット側の案）
- canonical の文書（`docs/notes/cloudflare.md`「配信設定」の付ける・付けないページの一覧と、K13 の理由の言い方）もこの指示で直す。CHAT-1101-PRP-02 は同じ文書のこの節を直さない
- navbar.js は未マージの work/1029-lgn-page・work/1028-lgn・work/1017-lvp も別の行（現在地の分岐）を変えている（PRP-01 のログの重なりの表）。行き先の行（30行目付近）とは離れている見込み
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下にする（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）。全ページの確かめは生成物・ローカル・プレビューで行う

## 手順
1. 着手時に `regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: pro_pages` で手動実行し、生成が止まる理由が無いことを確かめる（表26。止まる理由があれば一覧を書いて止まる）。#641 に着手中のコメントを書く
2. 生成とメタ情報: noindex を外す・canonical（トップと個別）・個別ページの title と og:title の形・OGP 画像の移動（K14）・`sitemap-pro.xml` の生成と `sitemap.xml`・`update_sitemap_lastmod.py`・docs/notes/sitemap-lastmod.md・並べ替えのボタンの大きさ・「未公開」の記述（表24 の一覧）・`docs/notes/cloudflare.md`「配信設定」と `docs/notes/static-generation.md`「ページの一覧」。テストを足す・直す（`scripts/tests/test_pro_pages.py` ほか）。pro/ を再生成する
3. 導線: `navbar.js` の「連盟プロ」の行き先・`index.html` のサイドメニュー・`llms.txt`・`sitemap-pages.xml`（`jpml_pros.html` の行を `/pro/` に）。`scripts/tests/test_navbar.py` ほかを直す。そのうえで次を確かめて `## 経過` に書く:
   - テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る
   - 生成物の全件で: noindex 0件・canonical が og:url と一致・title の形（最短と最長の名前つき）・`sitemap-pro.xml` の URL の数が pro/ のページの数と一致・ほかのページの生成物に想定外の差分が無い（差分を種類ごとに数えて書く）
   - プレビューで、トップ・`pro/yurika.aoi.24/`・`pro/a-ji.skull-reaper.27/`・404（`pro/taro.yamada.99/`）を 360px と 1280px・明暗で見る（並べ替えのボタンの大きさを測った値も書く）
   - 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

## 止まる条件
- 手順1の手動実行で、生成が止まる理由が出た（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 前提の実物の場所が食い違い、どこを変えればよいか決められない
- #641 にほかのセッションの着手中のコメントが残っている
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- pro/ 以外のページの生成物に、この指示の変更で説明できない差分が出た（一覧を書いて止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f0cfaaa）: https://github.com/retroeater/mj-logs/tree/main/guide/6f0cfaaa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
