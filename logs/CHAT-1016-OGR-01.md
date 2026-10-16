# CHAT-1016-OGR-01

- 着手日時: 2026-10-16
- 対象issue: 新規起票（#165 にコメント）
- ブランチ: work/1016-ogr
- 着手時HEAD: 91b5dd304

## 指示

【Claude作成】Claude Code 向け指示：OGP 画像の横断レビューの issue を起票する Chat-Ref: CHAT-1016-OGR-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ryoei.pro の各ページの OGP 画像（og:image）を横断的にレビューし、整合性・統一感のある設定にするための issue を起票する。この指示は起票と現状の一覧まで。レビュー・方針決め・直しは起票した issue で後日行う。
決定（2026-10-16、平野さん）

* ryoei.pro で採用している OGP 画像を横断的にレビューし、整合性・統一感のある設定にする。その issue を起票する
* 方針の例として「YouTube 動画（のページ）は、その動画のサムネイルを使う」等を挙げた（例であり、全ページの方針はこの issue のレビューで決める）

前提（チャット側。平野さんの決定ではない）

* 2026-10-16 にチャット側が REST で issue の題・本文を見た範囲では、同じ目的（現行サイトの og:image の横断レビュー）の issue は見当たらない（要確認）。近いもの: #165（open・保留、新サイトで OGP 画像をページ別に出し分けるか。選手個別ページが主）、#267（open、og:locale）、#517（open、apply_page_meta.py）、#78・#232・#339・#340・#343・#353・#546（closed、各区分の OGP 画像）
* 今わかっている区分ごとの決め方（docs/decisions・docs/notes/ogp.md より。実物は手順2で確かめる）: 共通の `img/ogp.png`（#78、「ryoei.pro」の文字だけ）／帰り道の一覧は合成画像（三田さんの許可を得て作成、#339・#340）／Mつくの一覧は最新話の YouTube サムネイルそのまま（docs/decisions/mtsuku.md。合成は許可が無いため作らない）／title/ と houou/ は黒地・白字の画像（#232、houou/ は #540）／最強戦の年度・対局・卓は ABEMA の作品の og:image（docs/decisions/saikyo.md の 5、#549）
* 平野さんは 2026-10-02 頃、映画「マインドアスリート」の宣材写真で（共通の）OGP 画像を作ることを検討していた（チャット側の控え。issue・docs にあるかは要確認。あれば番号を本文の「関連」に書く）
* 本文の論点の案（チャット側の案。issue には「チャット側の案」と明記して書く）: (a) 動画1本のページは YouTube（ABEMA は ABEMA）のサムネイルをそのまま使うか、帰り道のような合成画像との関係をどうするか (b) 一覧・入口のページの意匠（黒地・白字の型にそろえるか）と、共通の `img/ogp.png` の扱い (c) og:title・og:description・og:type・og:image:width/height・alt の付け方の平仄（#267 の og:locale と一緒に見るか） (d) 対象外（凍結・廃止予定・作り直し予定のページ。2026-10-06 のサイト全体の横断レビューの範囲の決め方にならうか） (e) 確かめ方（docs/notes/ogp.md「試作は比較用の画像だけで判断しない」〈1ページを本番に出し X・LINE で見てから広げる〉）

手順

1. 同じ目的の issue（クローズ済み・コメントの決定を含む。検索語に「OGP」「og:image」「サムネイル」「マインドアスリート」を入れる）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で OGP を変えているものを確かめる。同じ目的の open issue があれば起票せず止まる。関連する issue・ブランチは番号と題をログの `## 経過` に書く
2. origin/cloudflare の生成済み HTML の og:image（と og:title・og:type の有無）を全ページ分集め、区分（共通画像／区分ごとの画像／YouTube・ABEMA のサムネイル／og:image なし 等）ごとのページ数と代表のページを表にしてログの `## 経過` に書く。凍結中（`regenerate.py` の `FROZEN_FROM_ALL`）・未公開（noindex）のページは印を付ける。区分に入らないものは名前の一覧も書く
3. issue を起票する。題の案「OGP 画像を横断的にレビューし、ページの種類ごとにそろえる」。本文は、目的と平野さんの決定（上の「決定」の2点、2026-10-16）、現状（手順2の表を引用。どのログのどの節かを書く）、論点（上の (a)〜(e) を「チャット側の案」として）、関連（手順1の issue）。ラベルは既存のものから「分野: SEO/AIO」「対象: 全ページ」。期日は書かない。#165 には「現行サイトの横断レビューは #<新番号>」の1行のコメントを書く（本文・状態・ラベルは変えない）

止まる条件

* 同じ目的の open issue がある（番号と題を書いて止まる）
* 手順2で og:image を集められないページの種類がある（生成の仕組みが想定と違う）ときは、集められた分で起票してよい。集められなかった分は名前の一覧を本文とログに書く
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（新しい issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 識別子 `OGR`: 全ブランチのコミット・`docs/logs/` の履歴とも該当なし。`CHAT-1016-OGR-01` のコミットも無し
- 作業ブランチ: ローカル・リモートとも `work/1016-ogr` が無いため `git checkout -b work/1016-ogr origin/cloudflare`（着手時 HEAD 91b5dd304）
- 手順0: このログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも有る

### 手順1: 同じ目的の issue と未マージのブランチ

検索 API（`/search/issues`）はセッションの制限で使えない（repository-scoped のみ）ため、`repos/retroeater/mj/issues?state=all`（579件）と
`issues/comments`（2,376件）を全件取得し、題・本文・コメントを「OGP」「og:image」「サムネイル」「マインドアスリート」で手元で検索した。

**同じ目的（現行サイトの og:image の横断レビュー）の open issue は無い。** 起票する。

関連する issue（OGP 画像そのものを扱うもの）:

| 番号 | 状態 | 題 |
|---|---|---|
| #12 | closed | OGPタグを追加する |
| #78 | closed | OGP画像を作成して og:image を設定する |
| #165 | open（保留） | 新サイトでOGP画像をページ別に出し分けるか検討する（選手個別ページが主） |
| #232 | closed | 大会別ページのOGP画像を出し分ける（#165 から分割） |
| #267 | open | og:locale を全ページに追加する |
| #288 | closed | wayhome 個別ページの og:type を video.other にし og:video を併記する |
| #329 | open（待ち） | 帰り道の各話に twitter:player を入れる（#288 第2段） |
| #339 | closed | 「帰り道」一覧・個別ページのOGP画像を作る |
| #340 | closed | 「帰り道」の新しい回に合わせてOGP画像を自動で生成する |
| #343 | closed | 最強戦の年度ページごとにOGP画像を出し分ける |
| #353 | closed | LINE のカードで横長の OGP 画像が正方形に切り抜かれ、中央しか見えない |
| #429 | open（保留） | 書籍ページの公開（booklog からの切替）。本文 8 に「OGP は別途検討」（楽天の書影を使う決定、2026-09-22） |
| #517 | open | scripts/apply_page_meta.py を消すか、生成スクリプトの説明文と揃えるか決める |
| #540 | open | 鳳凰戦の新ページ houou/ を公開する（専用 OGP 画像 `img/ogp/houou/index-black.png`） |
| #546 | closed | 最強戦の個別ページの OGP 画像を検討する |
| #549 | closed | 最強戦の新しい年度・対局・卓のページを公開する（#425 の段2） |
| #576 | open | 女流勉強会の一覧・各話（/study/）を公開する（og:image の確認を含む） |

- 「マインドアスリート」: issue では #388（リソース「映画」の一覧の例）と #101（トップの portfolio の候補）に出るだけで、
  OGP 画像に宣材写真を使う話は issue・コメント・docs のどこにも無い（docs も grep で該当なし）。本文の「関連」には書かず、論点の側に「控え」として書く
- #267 は上の検索語に当たらない（og:locale の issue）。題は issue_read で確かめた

未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`、本ブランチを除く7本）で、`docs/logs` 以外の差分に `og:`・`ogp` を含むのは
`work/1015-hou5` だけ。houou/ の告知動画の制作スクリプト（`scripts/promo_video/houou/`）が締めの画面に `img/ogp.png` を素材として借りるもので、
ページの OGP は変えていない。OGP を変えているブランチは無し。

### 手順2: 生成済み HTML の og:image（origin/cloudflare 91b5dd304）

方法: 作業ツリー（= origin/cloudflare＋このログ）の `*.html` のうち公開対象（`.assetsignore` の `scripts`・`docs` 等を除く）2,544件の `<head>` の meta を読む
（スクリプトは scratchpad、使い捨て）。**集められなかったページは無い**（og:image が無いのは 404.html だけ）。リダイレクト用の HTML は無かった。

区分ごと（「noindex」は `robots` が noindex の件数。凍結は `regenerate.py` の `FROZEN_FROM_ALL = {"books_pages"}` で、books/ がこれ）:

| 区分 | og:image | 場所 | ページ数 | noindex | og:type | 代表のページ |
|---|---|---|---|---|---|---|
| 共通画像 | `img/ogp.png`（1200×630、alt「ryoei.pro」） | ルート直下の旧ページ | 22 | 0 | website | index.html・jpml_pros.html・houou_results.html |
| 共通画像 | 同上 | live/ の一覧・大会・期 | 165 | 165 | website | live/index.html・live/gpmax/10.html |
| 共通画像【凍結】 | 同上 | books/ | 191 | 191 | website | books/9784046013460.html |
| 区分ごとの画像（黒地・白字） | `img/ogp/title/<大会>-black.png` など21種＋`index-black.png` | title/ | 384 | 0 | website | title/index.html・title/gpmax/10.html |
| 区分ごとの画像（黒地・白字） | `img/ogp/houou/index-black.png`（全ページ同じ1枚） | houou/ | 13 | 13 | website | houou/index.html |
| 区分ごとの画像（黒地・白字） | `img/ogp/saikyo/index-black.png` | saikyo/ のトップ | 1 | 0 | website | saikyo/index.html |
| 区分ごとの画像（合成） | `img/ogp/wayhome/index-20261011-2.jpg` | wayhome/ の一覧 | 1 | 0 | website | wayhome/index.html |
| YouTube のサムネイル | `maxresdefault.jpg`（1280×720） | wayhome/ の各話 | 42 | 0 | video.other | wayhome/0OPca1Bl5xk.html |
| YouTube のサムネイル | 同上（一覧は最新話） | mtsuku/ の一覧＋各話 | 62 | 0 | 各話 video.other 61・一覧 website 1 | mtsuku/index.html |
| YouTube のサムネイル | 同上 | study/ の一覧＋各話 | 39 | 39 | 各話 video.other 38・一覧 website 1 | study/1QVc_wj9T-c.html |
| YouTube のサムネイル | `hqdefault.jpg`（480×360） | study/ の各話 | 2 | 2 | video.other | study/631Orl1nAcY.html |
| YouTube のサムネイル | `maxresdefault.jpg`（1280×720） | live/ の卓・対局 | 672 | 672 | website | live/gpmax/10/b16-a.html |
| YouTube のサムネイル | `sddefault.jpg`（640×480） | live/ の卓・対局 | 71 | 71 | website | live/champions-league/29/f.html |
| ABEMA の画像 | 作品「麻雀最強戦」の画像（全ページ同じ1枚、alt「麻雀最強戦（ABEMA）」） | saikyo/ の年度・対局・卓 | 878 | 0 | website | saikyo/2011/20110430.html |
| og:image なし | — | 404.html | 1 | 1 | なし | 404.html |
| 計 | | | 2,544 | 1,154 | | |

- og:image のある 2,543ページはすべて og:title・og:description・og:image:width/height・og:image:alt・`twitter:card`（summary_large_image）を持つ
- 区分に入らないもの: 404.html（og 系の meta が無い。noindex）
- 気づいた点（論点の材料。判断はしない）:
  - 動画1本のページの og:type が、wayhome・mtsuku・study は video.other、live/ の卓・対局は website と分かれている
  - YouTube のサムネイルの大きさが maxresdefault・sddefault・hqdefault の3通り（maxres が無い動画の代わりと思われる。未確認）
  - saikyo/ の年度・対局・卓（878）は ABEMA の作品の画像1枚で、ページごとの違いは出ない（#549 の決定 5 のとおり）。#343 の年度別の画像は今は使っていない
  - ルート直下の旧ページ（houou_*.html など）と live/ の一覧は共通の `img/ogp.png`。title/・houou/・saikyo/ のトップは黒地・白字の区分画像

### 手順3: 起票

- #581「OGP 画像を横断的にレビューし、ページの種類ごとにそろえる」を起票。ラベル「分野: SEO/AIO」「対象: 全ページ」（既存、#267 と同じ）。期日は書いていない。
  本文は目的・決定（2026-10-16 の2点）・現状（手順2の表、このログの `## 経過`「手順2」を出典に）・論点 (a)〜(e)（チャット側の案と明記）・関連
- 「マインドアスリート」の宣材写真の件は記録が見当たらないため、関連ではなく論点の「控え（チャット側）」として書いた
- #165 に「現行サイトの横断レビューは #581」のコメント（Chat-Ref 付き）。本文・状態・ラベルは変えていない
- 決定の記録: docs/decisions/seo-bing.md に 2026-10-16 の節を足し、README の一覧の説明に「OGP 画像」「#581」を足した

## 報告

- 状態: 完了
- ブランチ: work/1016-ogr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-OGR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-ogr
- 確認用URL: なし
- マージ: 済（このログを含む先頭を cloudflare へ fast-forward。SHA は `git log origin/cloudflare --grep CHAT-1016-OGR-01`）
- issue: #581（起票）、#165（コメント）
- 判断が必要なこと: なし（#581 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 371e0036）: https://github.com/retroeater/mj-logs/tree/main/guide/371e0036

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
