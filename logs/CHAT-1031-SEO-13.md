# CHAT-1031-SEO-13

- 着手日時: 2026-11-01（JST。UTC 2026-10-31）
- 対象issue: #560・#638
- ブランチ: work/1031-seo13
- 着手時HEAD: 7aaff3045

## 指示

【Claude作成】Claude Code 向け指示：canonical の基準の見直し（#560・#638）のため、ページの種類ごとに受けるクエリと今の canonical を調べ、新しい基準で付ける・付けないの案を出して止まる
Chat-Ref: CHAT-1031-SEO-13
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: 2026-11-01 の月次の取得（run-monthly の fetch-gsc、03:00 JST の起動）の後（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1031-seo13〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-seo13 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1031-seo13 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1031-seo13 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、`docs/gsc/` に 2026-11-01 以降の取得のフォルダがあるかを確かめてログに書く。無くても止まらない（下の前提のとおり、Search Console の数は補助のため。その場合は最新の取得を使い、日付を書く）。

## 目的
canonical の付け方（#560）を、ページの種類ごとに新しい基準で決め直す材料を作る。この指示では何も直さず、ページの種類ごとの表と、付ける・付けないの案をログに残して止まる。直すのは平野さんが案を見て決めた後の別の指示で、#638（saikyo/players/ から外す）もその指示で一緒に行う。

### 決定（2026-10-31、平野さん）
- canonical の基準を次に置き換える（#113 の 2026-10-13 のコメントの基準「URL の別形を持たず、Search Console にも `?` 付きの URL が出ていないページには付けてよい」を置き換える）: **クエリがページの主題（選手など）を選ぶページには付けない。クエリが同じ主題の表示の絞り込み・切り替え（`?q=` の絞り込み、`?year=`・`?all=1`・`?view=` などの表示の切り替え）だけなら、自分自身の canonical を付ける。** Search Console の `?` 付きの着地は判定の補助（裏づけ）で、主な判定はコードの受けるクエリで行う
- 選手で中身が変わる個人成績（`*/players/`）は付けない（#638 の決定のまま）。これは選手個別ページ（pro/、#219）と `?name=` からの 301 ができるまでの暫定で、それらが本番に入ったら players/ の canonical の扱いを見直す
- `saikyo/vs/`（対戦成績）など、選手の組をクエリで選ぶページの扱いは、案の表を見て平野さんが決める（この指示で「決めること」に挙げる）
- 進め方: この読むだけの指示の後に、決めたことと #638 を1本の指示で直す
- （#5 の続き）title/ の期ページ・大会ページで説明文が50字未満のもの（期237件・大会11件、CHAT-1030-SEO-12）は今のままにする（文頭の「日本プロ麻雀連盟の」を外したのは、連盟の主催でない大会に付けないためで、事実の正しさを優先する。2026-10-31 の決定）

### 前提（チャット側。平野さんの決定ではない）
- Search Console の取得は遅行の指標で、新しいページ（houou/ 公開 2026-10-14・最強戦の新しいページ 2026-10-16 など）の `?` 付きの着地はまだ出ていない見込み。**「出ていない」ことを「付けてよい」根拠にしない。** Google が選んだ正規 URL を知る道具は #458（URL 検査 API）で、この指示では使わない
- 今の状態は #560 の本文の表（2026-10-29、cloudflare b06f4f27c）。SEO-01 の①-3・①-8・①-9 と、#560 の 2026-10-29 のコメント（title/ の異議の両方の言い分と `assets/title.js` の行）を読む
- 基準の置き換えは、記録先が3か所ある: docs/decisions（seo-bing.md）・#113 へのコメント・docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」。この指示では docs/decisions だけを書き、ほかの2か所は直す指示で書く。そのために、3か所の今の文面（#113 の 2026-10-13 のコメントの基準・cloudflare.md の canonical の段落・docs/handover.md 2章「配信」の canonical の行）をログに引用しておく
- PRO 系列（pro/ への移行、作業ブランチ work/1030-pro）が `jpml_pros.html` と選手の個別ページを扱っている。トップ階層のページ（`jpml_pros.html` など）は今 canonical が無く、`?name=` の着地があるので、新しい基準でも「付けない」の見込み。この指示の案でも、トップ階層は「今のまま（PRO 系列の作業に任せる）」とする
- #560 の S5(e)（`scripts/regenerate.py` の `OUTPUT_OVERRIDES` のコメントと docs/notes/static-generation.md の「未公開」）は work/1028-lgn のマージ待ち（2026-10-29 の決定）。この指示では扱わない
- noindex のページ（`live/`・`study/`・`books/`・各大会の `leagues/`）は、案の表には載せるが、決めるのは公開のとき（#362・#576 など）でよい

## 手順
1. ページの種類ごと（#560 の表の行。`houou/`・`ouka/`・`jwrc/`・`wrcr/`・`saikyo/`・`title/` はトップ・個人成績・ランキング・順位変動・年度・対局・卓・vs・years・入口・大会・期など、canonical の扱いが変わりうる単位まで分ける）に、次を表にしてログに書く: (a) ページを生成するスクリプトと、ページで動く JS が読むクエリ（`URLSearchParams`・`location.search`・`history.replaceState` を grep し、パラメータ名・それが選ぶもの〈主題か表示か〉・ファイル:行） (b) 今の canonical の有無と向き先 (c) `/x/index.html` などの別名が 200 になるか（SEO-01 の①-9 の値でよい） (d) Search Console の `?` 付きの着地の数（0章で決めた取得の page.csv。補助） (e) 新しい基準での案（付ける・付けない・決めること）と理由。「主題を選ぶ」と判定したクエリは、何を選ぶのかをコードの引用で示す（文字列の一致だけで判定しない）
2. 上の前提の3か所の今の文面をログに引用する。#638 と saikyo/ の canonical の決定（docs/decisions/saikyo.md の「個別ページも年度ページも自分自身」の行）も引用し、案と食い違う点を書く
3. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/seo-bing.md に足し、ログを仕上げて `git push origin work/1031-seo13:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。`## 報告` の「判断が必要なこと」に、案の表のうち「決めること」の行（saikyo/vs/ ほか）と、案が今の canonical から変わる行（付ける・外す）の一覧を、平野さんが番号で選べる形で書く。#560 と #638 に着手中のコメントを書く（本文・状態・ラベルは変えない）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- #560・#638 に、他のセッションの完了していない着手中のコメントがある（読むだけで進めず止まる）
- 手順1で、受けるクエリが「主題を選ぶ」か「表示の絞り込み」かをコードから決められないページの種類がある（止まらず、その行を「決めること」に入れて進める。全種類の半分以上で決められないときだけ止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-SEO-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-SEO-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行と一致。`docs/gsc/2026-11-01` がある（origin/cloudflare 7aaff3045）。この取得を使う。
- 同じ Chat-Ref のコミット・ログ: なし。work/1031-seo13 はローカル・リモートとも無く、origin/cloudflare から作成。
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。

- #560・#638: 他のセッションの完了していない着手中のコメントは無い。着手コメントを付けた（#560 https://github.com/retroeater/mj/issues/560#issuecomment-6550469021 ・#638 https://github.com/retroeater/mj/issues/638#issuecomment-6550469518 ）。本文・状態・ラベルは変えていない。
- 調べた版: origin/cloudflare 7aaff3045 の HTML とコード。Search Console は `docs/gsc/2026-11-01/20261002-20261029/page.csv`（600行）。

### 手順1: ページの種類ごとの表

調べ方:
- (a) `grep -rn 'URLSearchParams\|location\.search\|replaceState\|searchParams'`（vendor・workers・scripts を除く JS）と、各 JS を読み込む HTML。
- (b) 各 HTML の `<link rel="canonical">` を数えた。「自分」は向き先が自分の URL（`index.html` を除いた形）。
- (c) 本番に curl。`index.html` 付きの別名は、調べた15個（`/index.html`・`houou/`・`houou/players/`・`houou/ranking/total/`・`houou/race/`・`saikyo/`・`saikyo/players/`・`saikyo/vs/`・`saikyo/years/`・`saikyo/2025/`・`title/`・`title/oui/`・`mtsuku/`・`exam/`・`wayhome/` の各 `index.html`）がすべて 200（SEO-01 の①-9 と同じ。html_handling は none）。ほかのディレクトリも同じとみる。
- (d) page.csv の `?` 付きの URL を、`?` の前の部分で数えた。

| # | ページの種類（件数） | (a) 受けるクエリ（ファイル:行）と選ぶもの | (b) 今の canonical | (d) GSC の `?` 付き | (e) 案 |
|---|---|---|---|---|---|
| 1 | トップ階層（11。`jpml_pros.html` ほか、noindex 1） | `jpml_pros.js:5`・`table.js:22`（`getSearchParam`。表の絞り込み欄の初期値、`?name=` など） | 無し 0/11 | 104（`houou_results.html` 23・`jpml_titles.html` 22・`jpml_pros.html` 17・`video_live.html` 14・`ouka_results.html` 6・`houou_leagues.html` 5・`houou_ranking.html` 3・`ouka_leagues.html` 2・`ouka_ranking.html` 1・`resource_logs.html` 1。多くは今は 301 の旧 URL） | 今のまま（PRO 系列の作業に任せる。前提のとおり） |
| 2 | houou/・ouka/・jwrc/・wrcr/ のトップ（4） | 読まない（`assets/houou_ask.js` はクエリを読まない。`league.js` はトップでは何もしない、`league.js:2226` の分岐に無い） | 無し 0/4 | 0 | **付ける（自分）**。クエリが無い |
| 3 | 同 `players/`（4） | `league.js:553` `const name = params.get('name') / if (name) { show(currentName(name), view) }` → **選手を選ぶ（主題）**。`league.js:471` `?view=` はタブ（表示） | 無し 0/4 | 310（ouka 142・houou 64・wrcr 58・jwrc 46） | 付けない（今のまま。#638 の決定、pro/ と 301 ができるまでの暫定） |
| 4 | 同 `ranking/<部門>/`（35） | `league.js:1160` `?division=` → `location.replace(chip.href)`（その部門のページへ移すだけ）。`league.js:1171` `?name=` → その選手の行に `is-me` を付けて `scrollIntoView`（同じ表の中の位置と色。表示） | 無し 0/35 | 13（houou 7・ouka 4・jwrc 2。すべて `?name=`） | **付ける（自分）**。`?name=` は表の行を示すだけで、表の中身（部門のランキング）は変わらない |
| 5 | 同 `race/`（4） | `league.js:2169`・`2172` `?term=`・`?league=` → 表示する期・リーグの順位変動を選ぶ（同じ大会の順位変動の切り替え。title/ 入口の `?year=` と同じ型） | 無し 0/4 | 0 | **付ける（自分）**。表示の切り替え |
| 6 | houou/・ouka/ の `leagues/`（2、noindex） | `league.js:2235` → `players/?…&view=league` へ移すだけ | 無し | 0 | 今のまま（noindex。移すだけのページ） |
| 7 | saikyo/ トップ（1） | `saikyo.js:219` `?q=` → `location.replace('players/?name=' + …)`（個人成績へ移すだけ。トップの中身は変わらない） | 自分 1/1 | 0 | 付ける（今のまま） |
| 8 | saikyo/ `players/`（1） | `saikyo.js:1022` `const name = params.get('name')` → **選手を選ぶ（主題）**。`saikyo.js:674` `?view=division`（表示） | 自分 1/1 | 0 | **外す**（#638。表3と同じ扱い） |
| 9 | saikyo/ `ranking/<部門>/`（6） | `league.js` の `initRanking`（表4と同じ。`data-houou="ranking"`）: `?name=` は行の印 | 自分 6/6 | 0 | 付ける（今のまま） |
| 10 | saikyo/ `vs/`（1） | `saikyo.js:453` `get(NAME_PARAM)` を `,` で分け、`saikyo.js:459` `names.length <= 2 && found.every(Boolean)` で**2名の組を選び、その組の対戦成績を出す（主題＝選手の組）** | 自分 1/1 | 0 | **決めること（Q1）** |
| 11 | saikyo/ `years/`（1） | 読まない | 自分 1/1 | 0 | 付ける（今のまま） |
| 12 | saikyo/ 年度（16）・対局（219）・卓（646） | 読まない（`saikyo.js` がクエリを読むのはトップ・vs・players の節だけ） | 自分 881/881 | 1（`saikyo/2026.html?match=…`。旧 URL で、今は 301） | 付ける（今のまま） |
| 13 | title/ 入口（1） | `title.js:469` `?year=` → 表示する年のカード（表示の切り替え。`title.js:434` で `replaceState`）。`title.js:331-332` `?q=`・`?name=`（旧 `jpml_titles.html?name=` の 301 で付く）→ 検索欄の初期値で、`title.js:282`（`applyFilter`）が `content.hidden = words.length > 0` でページの中身を隠し、title/ 全体の検索結果（選手ごとの期の一覧、`title.js:310-322`）に置き換える | 自分 1/1 | 0（`jpml_titles.html?name=` 22 は 301 で入口の `?name=` に着く） | **決めること（Q2）** |
| 14 | title/ 大会（20）・期（363） | `title.js:331-332` の `?q=`・`?name=`（表13と同じ。`#title_filter` は全 title/ ページにある）。`?year=` は無い | 自分 383/383 | 0 | 付ける（今のまま）。Q2 の答えに合わせる |
| 15 | wayhome/ 一覧・各話（44） | `video_wayhome.js:62` は検索欄に `data-param` があるときだけ読む。帰り道の一覧には無い | 自分 44/44 | 0 | 付ける（今のまま） |
| 16 | mtsuku/ 一覧（1） | `video_wayhome.js:62` `get(infoInput.dataset.param)`（`mtsuku/index.html` は `data-param="name"`）→ 検索欄の初期値で一覧を作る人の名前に絞り込む（同じ一覧の絞り込み。表示） | 無し 0/1（`generate_mtsuku.py:10`「?name= で中身が変わる別形の URL を持つため」） | 0 | **付ける（自分）**。新しい基準では絞り込みだけ |
| 17 | mtsuku/ 各話（61）・exam/ 各話（12） | 読まない | 自分 73/73 | 0 | 付ける（今のまま） |
| 18 | exam/ 一覧（1） | 読まない（`data-param` 無し） | 無し 0/1（`generate_exam.py:9`「女流勉強会と同じ」） | 0 | **付ける（自分）**。クエリが無い |
| 19 | study/ 一覧・各話（41、noindex） | 読まない | 各話 自分 40・一覧 無し | 0 | 公開のときに決める（前提のとおり） |
| 20 | books/（191、noindex） | 読まない | 個別 自分 190・一覧 無し | 0 | 公開のときに決める |
| 21 | live/（942、noindex） | `live.js:187` `?q=`（検索欄の初期値。一覧の絞り込み） | 無し | 0 | 公開のときに決める（#362） |
| 22 | pro/（1099、noindex） | `pro.js:53`・`103` `?all=1`・`?by=`・`?<切り口>=<値>`（一覧の絞り込み） | 無し | 0 | 今のまま（PRO 系列の作業に任せる） |

- 決められなかった種類: 無し（Q1・Q2 は決定が平野さんに残したもの、または同じ仕組みが主題を選ぶとも絞り込みとも読めるもの）。止まる条件（半分以上）に当たらない。
- Q2 の補足: `?q=` は決定の「`?q=` の絞り込み」の例に当たる。ただ title/ の `?q=`・`?name=` は、そのページの中を絞るのではなく、ページの中身を隠して title/ 全体の選手の検索結果を出す。旧 `jpml_titles.html?name=<選手>` の着地22件は 301 で入口の `?name=<選手>` に来て、その選手の期の一覧を出す（中身が選手で変わる）。
- 「出ていない」を根拠にしていない: 表の (d) が0の行も、案は (a) のコードで決めた。

### 手順2: 今の文面（直す指示で書き換える3か所）と saikyo/ の決定

- #113 の 2026-10-13 のコメント（https://github.com/retroeater/mj/issues/113#issuecomment-6138219785 ）:
  > 例外の基準: **URL の別形（`?name=` などのクエリで中身が変わる URL）を持たず、Search Console にも `?` 付きの URL が出ていないページ**。一覧は絞り込みで URL が変わらず（`video_wayhome.js` はクエリを読まない）、`docs/gsc/` の書き出しにも `?` 付きの URL は無い
  > 基準に当てはまるほかのページは、ページごとに確かめてから付ける（今回は帰り道だけ）
- docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」の canonical の段落（483〜492行）:
  > **canonicalは原則付けない。** **#113で「現行サイトにはcanonicalを付けない（Googleの正規化に任せる）」と決定した（2026-09-11）。** 単純に付けるとSearch Console実測の`?name=`付き14URL（`docs/notes/site-findings.md` のSEO節）が正規化で検索結果から消えるため。選手個別ページとtitle/descriptionの出し分けは新サイト（#296）で解く（#79も同時にクローズ）。
  > **自分自身を指す canonical を持つのは、2026-10-29 の集計で `title/`（全384件）・`saikyo/`（全891件）・`wayhome/`（各話と一覧の44件）・`mtsuku/` の各話（61件）・`exam/` の各話（12件）。** ほか（トップ階層・houou/・ouka/・jwrc/・wrcr/・mtsuku/ と exam/ の一覧）には無い。帰り道の各話（#162）と一覧（2026-10-13）は下の基準で付けたもの。title/・saikyo/ の分（クエリで中身が変わるページを含む。`saikyo/players/` は外すと決めた、#638）が基準に当たるかは #560 で見直し中。
  > 例外の基準は「URL の別形（`?name=` などのクエリで中身が変わる URL）を持たず、Search Console にも `?` 付きの URL が出ていないページ」。値は`lib/page.py`の`PageMeta.canonical`（既定`None`）で渡し、html_handling の影響を受けない正規の URL（一覧は末尾スラッシュ付きの`https://ryoei.pro/wayhome/`）にする。基準に当てはまるほかのページは、ページごとに確かめてから付ける。
- docs/handover.md 2章「配信」（58行）:
  > canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
- 上の3か所と今の実態の食い違い（直す指示で直すもの）: cloudflare.md の件数は今の HTML と合う（saikyo/ 891 は 1+1+6+1+1+16+219+646=891）。handover.md の行は title/・saikyo/・mtsuku/・exam/ の分が無い（#560 の 2026-10-29 のコメントのとおり）。3か所とも新しい基準と違う。ほかに `scripts/lib/page.py:68-70` のコメント（「既定はNoneで出力しない(#113。現行サイトはcanonicalなし=Googleの正規化任せの方針)」）と `scripts/generate_mtsuku.py:10`・`scripts/generate_exam.py:9` の docstring も、表16・18を直すときに一緒に直る。
- #638 の方針（本文）:
  > - `saikyo/players/`（`?name=` で中身が変わるページ）には canonical を付けない（docs/notes/cloudflare.md、#113 に合わせる）
  > - 年度・対局・卓の個別ページは docs/decisions/saikyo.md の決定のまま（自分自身）
  > 未決: canonical が付いている saikyo/ のほかのページ（トップ・`ranking/`・`vs/`・`years/`）の扱いは未決
- docs/decisions/saikyo.md:123:
  > - サイトマップはトップ・年度・対局・卓の全部（見込み 879件）。canonical は個別ページも年度ページも自分自身。…（grill 7） → 置き換え: 2026-10-15（CHAT-1015-SKR-01）
- 案との食い違い: 年度・対局・卓（表12）は saikyo.md:123 のまま「自分自身」で食い違わない（行末の「置き換え」は同じ行のほかの部分〈年度ページの移動・公開の進め方〉のものとみられ、canonical の部分を置き換えた決定は見当たらない）。#638 の `players/` を外すのは案8と同じ。#638 の未決のうち、トップ・`ranking/`・`years/` は案で「付ける（今のまま）」、`vs/` は Q1。

### 手順3

- 決定を docs/decisions/seo-bing.md に足した（2026-10-31 の節）。変えたのは docs/logs と docs/decisions/seo-bing.md だけ。

## 報告

- 状態: 判断待ち
- ブランチ: work/1031-seo13
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1031-SEO-13.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1031-seo13
- 確認用URL: なし
- マージ: 済（下の push の SHA は `## 経過` の最後の行）
- issue: #560・#638（着手コメントのみ）
- 判断が必要なこと:
  - 決めること（表の番号）:
    - Q1（表10、saikyo/vs/、`?name=A,B` で2名の組を選ぶ。今は自分の canonical あり）: (1) 付けない（players/ と同じく主題を選ぶ。外す） (2) 付ける（今のまま）
    - Q2（表13・14、title/ の `?q=`・`?name=`。ページの中身を隠して title/ 全体の選手の検索結果に置き換える。旧 `jpml_titles.html?name=` の着地22件が入口に来る。今は全384件に自分の canonical あり）: (1) 付ける（今のまま。決定の「`?q=` の絞り込み」として扱う） (2) 入口だけ付けない（`?name=` の着地が入口に来るため）、大会・期は付ける (3) title/ 全部で付けない
  - 案が今の canonical から変わる行（直す指示で行うもの。外したいものがあれば番号で）:
    - 表2: houou/・ouka/・jwrc/・wrcr/ のトップ4件に付ける
    - 表4: 同 `ranking/<部門>/` 35件に付ける（`?name=` は行の印だけ。GSC の `?name=` の着地13件は canonical で部門のページにまとまる）
    - 表5: 同 `race/` 4件に付ける（`?term=`・`?league=` は表示の切り替え）
    - 表8: saikyo/players/ から外す（#638）
    - 表16: mtsuku/ の一覧に付ける（`?name=` は一覧の絞り込み。`generate_mtsuku.py` の「付けない」の理由が新しい基準で無くなる）
    - 表18: exam/ の一覧に付ける（クエリを読まない）
  - 直す指示で一緒に直す文面: #113 へのコメント・docs/notes/cloudflare.md の canonical の段落・docs/handover.md 2章「配信」の canonical の行（今の文面は `## 経過`「手順2」）、`scripts/lib/page.py:68-70` のコメントと `generate_mtsuku.py`・`generate_exam.py` の docstring
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8083118c）: https://github.com/retroeater/mj-logs/tree/main/guide/8083118c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8083118c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
