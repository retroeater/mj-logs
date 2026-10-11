# CHAT-1011-ABM-01

- 着手日時: 2026-10-11
- 対象issue: #425（関連: #501・#502）
- ブランチ: work/1011-abm
- 着手時HEAD: 1245b908301a7974e4a023786d02a255325363c8

## 指示

【Claude作成】Claude Code 向け指示：#425（最強戦の対局・卓の個別ページと ABEMA）の着手前の調査（読むだけ） Chat-Ref: CHAT-1011-ABM-01 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シート・生成物・issue の本文は変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#425 を現行サイトで作る前に、決めることを /grill-me で詰めるための材料を集める。この指示では何も作らず、調べた事実をログに書くだけにする。
決定（平野さん。正は docs/decisions/features.md の 2026-10-05 RUN-05〜07 の #425 の行。この指示で新しく決めたことは無い）

* 新サイト（#296）送りにせず現行サイトで作る。「最強戦」シートに動画の URL の列を足す。対局のページと卓のページの両方を作る（ABEMA の URL が卓ごとにあるため）
* 年度ページは「各対局の勝ち上がり者」だけにする。ページ数の増加と URL の入力の量は問題にしない（API などで取れればなおよい）
* ABEMA はリンクだけで始め、サムネイルは #502。ABEMA のページの取得は平野さんの PC（日本）で動かすスクリプト（#501）
* 今の `?match=` は対局のページへ 301 で畳む（案A）。手段は Cloudflare のリダイレクトルールで、個別ページを作る時に平野さんがダッシュボードで設定する

前提（チャット側。平野さんの決定ではない）

* #425・#501・#502 は 2026-10-09 の時点で Open（要確認）。実装は未着手（要確認）
* 平野さんは「最強戦」シートに動画の URL の列をまだ足していない（2026-10-11、平野さんの申告）
* 「最強戦」シートの列は A 対局日〜K 表示（docs/notes/saikyo-page-design.md「1. データ源」。要確認）。対局日が空の行（2011〜2020年度の全行と2021年度の一部）は、アンカーと `?match=` の対局識別子が `<年度>-n<連番>` 形式で、日付を埋めると変わる（同「アンカー」）
* work/1010-xap（CHAT-1011-XAP-09、作業中）が `generate_saikyo_pages.py` の選手写真の読み先（`load_name_book()` 経由）を SNS ブックへ切り替えている（要確認）。この指示は読むだけなので重なりでは止まらない。読むコードは origin/cloudflare の版にする
* `_redirects` はクエリ文字列で一致させられない、とチャット側は理解している（要確認。案A の手段がリダイレクトルールに決まった背景）

手順

1. issue と関係するものを洗い出す（読むだけ）
   * #425・#501・#502 の状態・本文・コメントの全文を読み、要点（「先に決めること」の各項目の今の記載、決定と食い違う記述の有無）をログに書く。sub-issue の登録が #425 に #501・#502 の2件か確かめる
   * 同じ論点の issue を、クローズ済みとコメントの決定まで含めて洗い出す。検索語に少なくとも「最強戦」「saikyo」「ABEMA」「アベマ」「match」「個別ページ」「動画」を入れる。#530・#458・#533・#442・#428・#343・#514 は必ず含め、#425 との関係を1行ずつ書く
   * `git branch -r --no-merged origin/cloudflare` の一覧を出し、`saikyo/`・`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`（実名は確かめる）・`sitemap-saikyo.xml`・`_redirects` を変えているブランチを書く
2. 今の作りとデータを調べる（読むだけ。生成と同じ経路でシートを読む）
   * 「最強戦」シートの見出し行（全列）と行数、K=Y の行数。年度ごとに「対局の数・卓の数（ステージ×卓）・対局日が空の対局の数」を表にする。対局と卓の数え方は `generate_saikyo_pages.py` の今のまとめ方に合わせ、その関数名を書く。2回読んで行数が違えば止まる
   * 対局・卓の個別ページを作った場合のページ数の見込み（対局ページ・卓ページの別、合計）と、今のリポジトリの静的アセットのファイル数（`.assetsignore` を除いた数の求め方を書く）を並べる。上限 20,000 ファイルとの差を書く
   * 今の作りの要点を、ファイルと関数を指して書く: トップ・年度ページの生成、対局識別子とアンカーの作り方、`?match=` の扱い（JS 側・canonical・プルダウン・対局ごとの共有ボタン）、`sitemap-saikyo.xml` に載せる URL、OGP（年度ごとの画像の有無）、`llms.txt` の最強戦の記載、`_redirects` の saikyo の行、毎朝の再生成（`regenerate-saikyo.yml`）が対象にするファイルの範囲
   * 年度ページの「勝ち上がり者だけ」にした場合に、今の表示（卓ごとの4人・順位・バッジ）のどこが変わるかを、今の HTML の構造（クラス名）で書く。見た目の案は作らない
3. URL の形と転送の手段を調べる（読むだけ）
   * リポジトリとシート（「最強戦」・/live の3層〈ID は docs/notes/live-channel-write.md〉・連盟の予定表の取り込み〈docs/notes/yotei-sheet.md〉）に ABEMA の URL があるか探し、あれば形（対局・卓・番組の別が URL から分かるか）を例を挙げて書く。/live・予定表に最強戦の放送が載っているか、載っていれば最強戦の対局・卓と突き合わせられそうかを書く（件数と例。値の正しさは判定しない）
   * セッションから `https://abema.tv/` に届くか（HTTP の状態のみ）を1回試して書く。届かないことを根拠に結論を書かない
   * `_redirects` でクエリ付きの URL（`/saikyo/2026.html?match=…`）を別のパスへ転送できるか、Cloudflare の公式文書（Workers の静的アセットの `_redirects`、Single Redirects／Bulk Redirects）で確かめ、引用元の URL と一緒に書く。docs/notes/cloudflare.md にリダイレクトルールの今の使用の記録があるかも書く。ダッシュボードは触らない
   * 最後に、/grill-me で決めることの一覧（URL の形、日付未登録の対局の URL、対局と卓のページの中身、年度ページの勝ち上がり者の見せ方、サイトマップ・canonical・OGP・`llms.txt`、`?match=` の転送の対応表の作り方、#501 のスクリプトの入出力、シートの列の形）を、調べた事実から見て足すもの・不要になるものがあれば直して、ログの `## 報告` の「判断が必要なこと」に書く

止まる条件

* #425・#501・#502 のいずれかがクローズ済み、または他セッションの着手中コメントがある（読んだところまで書いて止まる）
* シートの行数が2回の読みで違う、またはシートを読めない
* ログ・docs/decisions/ 以外を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-10-11）

- クラウドセッション。`git fetch origin`・`git fetch --unshallow origin` の後に Chat-Ref を確かめた: `git log --all -E --grep 'CHAT-[0-9]{4}-ABM-'`・`docs/logs/CHAT-*-ABM-*.md` の履歴とも0件。`work/1011-abm` はローカル・リモートとも無し → `git checkout -b work/1011-abm origin/cloudflare`（起点 1245b908）
- 0.: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4つとも冒頭にある（1行目にまとめて書かれている）
- issue の検索 API（`/search/issues`）はセッションの GitHub の経路で拒否された（「sessions are bound to their configured repositories」）。代わりに `/repos/retroeater/mj/issues?state=all`（542件、PR 1件を除く）と `/issues/comments`（1,957件）を全件取得し、手元で検索した

### 前提の確かめ（チャット側の前提と実物）

| 前提 | 実物 | 判定 |
|---|---|---|
| #425・#501・#502 は Open、実装は未着手 | 3件とも Open。着手中のコメントは無い。未マージのブランチに saikyo の変更は無い（下） | 一致 |
| シートの列は A 対局日〜K 表示 | **A〜L**（L「備考」がある）。gviz の見出し: 対局日・年度・対局・ステージ・卓・順位・結果・名前・X・画像URL・表示・備考。#425 の本文も「A〜L 列」と書いている。docs/notes/saikyo-page-design.md「1. データ源」の表は K までで、L が無い | 食い違い（前提が古い。文書も L が抜けている） |
| 対局日が空の行は 2011〜2020年度の全行と2021年度の一部 | **K=Y の行で対局日が空の対局は 0件**（全年度）。`<年度>-n<連番>` の識別子は今の生成物に1件も無い。#323（対局日を埋める）はクローズ済み。saikyo-page-design.md「1. データ源」の A 列の備考（「2011〜2020年度の全行と…空欄」）は古い | 食い違い（前提・文書とも古い） |
| work/1010-xap（XAP-09）は作業中 | **cloudflare にマージ済み**（`origin/work/1010-xap` = 1245b908 = 今の `origin/cloudflare` の先頭。`5e1ac4f8 feat: ページの生成と写真の検知の SNS の値を SNS ブックから読む` が cloudflare にある）。読んだ `generate_saikyo_pages.py` はその後の版（`load_name_book()` が `sns.fetch_pros()`・`sns.others()` を読む） | 食い違い（読むだけなので止まらない） |
| `_redirects` はクエリ文字列で一致させられない | 公式文書で確かめた（下の手順3） | 一致 |

### 手順1: issue と関係するもの

#### #425・#501・#502

- 3件とも Open（2026-10-11 時点）。sub-issue は #425 に #501・#502 の2件（REST の `/issues/425/sub_issues`）
- **#425**（ラベル: 分野: SEO/AIO・分野: UI/UX・対象: saikyo。最終更新 2026-10-05）: 本文冒頭の「やること」は決定（docs/decisions/features.md の RUN-05〜07）と一致。「先に決めること」1〜4 は見出しにすべて決定が書かれている
  - 1（列を足す）: 決定済み。「足す作業は平野さん。入力の範囲と時期は未決」
  - 2（サムネイル）: リンクのみで始める。サムネイルは #502
  - 3（粒度）: 対局と卓の両方（RUN-06 の「対局単位」を置き換え）。本文の数は「対局219・卓642」（2026-10-05 に生成物の `class` の出現数で数えた）
  - 4（`?match=`）: 案A・Cloudflare のリダイレクトルール（Single Redirects）
  - 決定と食い違う記述: 無し。ただし本文下の「URL の形」（c-1〜c-3）と「生成とサイトマップ」は RUN-06 以前の検討のままで、URL の形は未決（決定には URL の形が無い）。本文の「1. 」の節の「対局と動画を突き合わせる鍵は（年度, 対局, ステージ, 卓）」は、対局日を含まない（下の「対局識別子の重複」と関係）
  - コメント4件（RUN-05・06・07・09）は、本文の決定の経過と sub-issue の登録。着手中のコメントは無い
- **#501**（分野: 自動化・対象: saikyo、コメント0件）: 平野さんの PC で、シートの動画 URL の列を順に開き、HTTP の状態・転送先・og:title・og:image を CSV に出す。作る前に、利用規約（自動取得の可否）・公式 API の有無・番組一覧から最強戦の URL を列挙できるか、を確かめる。使い方を docs/notes/ に書く
- **#502**（状況: 待ち・分野: UI/UX・対象: saikyo、コメント0件）: 権利・CSP（#9 の `img-src`）・自前で置く手間・取得の手段（#501 の og:image）を見て、載せるかを決める。#501 と #425 の個別ページを待つ

#### 同じ論点の issue（指定の7件）

| issue | 状態 | #425 との関係 |
|---|---|---|
| #530 サイト全体の共有ボタンを見直す | Open（期日 2026-10-24） | 最強戦の年度ページの「対局ごとの共有」（`?match=` 付きの URL）は「変わる → 残す」と決まった（2026-10-10、SKS）。個別ページができると、このボタンの URL（`?match=`）を個別ページへ変えるか・外すかが #425 側の論点になる |
| #458 URL 検査 API で sitemap の URL の登録状態を定期取得 | Open（期日 2026-11-02） | 最強戦の年度ページ15件の登録を 11/2 に確かめる予定。個別ページで `sitemap-saikyo.xml` が 17 → 約880件に増えると、URL 検査の対象（2,000件/日の上限）と月次の量に入る |
| #533 regenerate.py: 1ページの生成の失敗で残りが止まる | Open（実装はマージ済み a930b4a9、10/12 の週次を見て閉じる） | 個別ページは `generate_saikyo_pages.py` の中で書けば `saikyo_pages` の1ページとして扱われ、失敗したときは saikyo/ 全体が飛ばされる（ほかのページは止まらない）。シートの動画 URL の列の検査で止める形にすると、#533 の「シートの検査の失敗」の例になる |
| #442 saikyo_mens.html を廃止し代わりのページを検討 | Open（状況: 保留） | 直接は重ならない。代わりのページを `saikyo/` の配下に置くかが論点にあり、saikyo/ の URL の形（c-2/c-3 のディレクトリ）を決めるときに関係しうる |
| #428 固定バーの上余白を body のクラスに | Open（saikyo/ の分はマージ済み 3a857ea7） | 個別ページを作るとき `add_body_class()`（`mj-saikyo-page`）を付けるか（固定バーを持つか）で関係する。#382 の入れ直しも残る |
| #343 年度ページごとに OGP 画像を出し分ける | Closed（completed） | 範囲外に「対局ごと（`?match=`）の OGP は静的サイトではクエリ別に出せないため見送り（SK-19）」。個別ページなら出せるが、画像を作るかは未決（下の「判断が必要なこと」） |
| #514 最強戦の選手写真の自動解決 | Open（状況: 待ち） | 写真の読み先は SNS ブック（XAP-09 でマージ済み）。個別ページの写真も同じ `load_rows()` を通る。直接の重なりは無い |

#### 検索で見つかったもの（キーワード: 最強戦・saikyo・ABEMA・アベマ・match・個別ページ・動画）

本文・コメントを含めた件数: 最強戦 75・saikyo 131・ABEMA 6・アベマ 0・match 27・個別ページ 75・動画 97（「個別ページ」「動画」は /live・帰り道・書籍などを多く含む）。#425 に関係するものだけ挙げる:

- ABEMA を含むのは #319・#323・#412・#425・#501・#502 の6件。#319（年度別構成、Closed）と #412（UI の平仄、Closed）は「#425 に分けた」という記載だけ。#323（対局日を埋める、Closed）は資料の話で ABEMA の URL の記載は無い
- `?match=` を含む: #319・#343・#350・#355・#383・#406・#409・#425・#457・#511・#530
  - #383（content-visibility、Open・保留）: `?match=` の絞り込みとアンカーの着地を確かめる条件がある。年度ページの中身が変われば前提が変わる
  - #457（Closed）・#511（Closed）: GSC で `saikyo/2026.html?match=` が1件だけ現れた（表示1・クリック0、2026-10-09）。`?match=` の URL の外からの流入は今のところほぼ無い
  - #355（Closed）: トップの検索結果は卓の一覧を `<年度>.html#<卓のアンカー>` へリンクする（641種類が実在、と当時確認）
- そのほか関係しうるもの: #372（最強戦の出場者同士の対戦成績、Open。個別ページと同じデータを使う）、#434（`/saikyo_results.html` の301を消すか、Open）、#469（最強戦の公式サイトを資料に使うときの扱い、Open。ABEMA の URL を手で集めるときの資料の扱いに関係しうる）、#537（毎朝の再生成、Closed）、#9（CSP、Open。#502 のサムネイル）
- 同じ目的（最強戦の個別ページ・ABEMA）の別の issue は無い

#### 未マージのブランチ

`git branch -r --no-merged origin/cloudflare`（2026-10-11、自分のブランチを除く3件）。`saikyo/`・`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`（実名。`assets/` の最強戦の JS はこの1つ）・`sitemap-saikyo.xml`・`_redirects` を変えているブランチは**無い**（merge-base からの差分で確かめた）。

| ブランチ | 先頭 | 該当の変更 |
|---|---|---|
| origin/work/1009-swp-526 | 2026-10-10 docs: report CHAT-1010-SWP-12 (merged) | 無し |
| origin/work/1011-pht-473 | 2026-10-11 docs: CHAT-1011-PHT-23 の確かめの結果と報告を書く | 無し |
| origin/work/1011-swp-nav | 2026-10-10 docs: report CHAT-1011-SWP-13 (awaiting decision) | 無し |

### 手順2: 今の作りとデータ

#### シート（生成と同じ経路で2回読んだ）

`lib.sheets.fetch_sheet()`（gviz。フィルタの検知 `check_not_filtered()` を含む）で、`SELECT *` と生成の `QUERY`（`SELECT A,B,C,D,E,F,G,H WHERE K = "Y"`）を2回ずつ読んだ。見出しは `_query(..., "SELECT * LIMIT 1")` の列の label。

- 見出し（全列）: A 対局日・B 年度・C 対局・D ステージ・E 卓・F 順位・G 結果・H 名前・I X・J 画像URL・K 表示・L 備考
- 行数: 全行 2,744 / 2,744（2回とも同じ）。K=Y 2,572 / 2,572（同じ）。K の値は Y 2,572・N 172
- まとめ方は生成と同じ `build_matches()`（(年度, 対局日, 対局名) で対局にまとめる）→ `build_match()`（(ステージ, 卓) で卓にまとめる）。下の表はこの2つの関数をそのまま呼んで数えた（写真の解決 `load_name_book()` は数に関係しないので呼んでいない）

| 年度 | 行 | 対局 | 卓（ステージ×卓） | 対局日が空の対局 | 未対局の卓 |
|---|---:|---:|---:|---:|---:|
| 2011 | 92 | 8 | 23 | 0 | 0 |
| 2012 | 92 | 7 | 23 | 0 | 0 |
| 2013 | 108 | 9 | 27 | 0 | 0 |
| 2014 | 116 | 9 | 29 | 0 | 0 |
| 2015 | 104 | 8 | 26 | 0 | 0 |
| 2016 | 156 | 13 | 39 | 0 | 0 |
| 2017 | 168 | 14 | 42 | 0 | 0 |
| 2018 | 220 | 19 | 55 | 0 | 0 |
| 2019 | 208 | 18 | 52 | 0 | 0 |
| 2020 | 204 | 17 | 51 | 0 | 0 |
| 2021 | 196 | 17 | 49 | 0 | 0 |
| 2022 | 196 | 17 | 49 | 0 | 0 |
| 2023 | 196 | 17 | 49 | 0 | 0 |
| 2024 | 196 | 17 | 49 | 0 | 0 |
| 2025 | 196 | 17 | 49 | 0 | 0 |
| 2026 | 124 | 12 | 31 | 0 | 10 |
| 計 | 2,572 | 219 | 643 | 0 | 10 |

- 卓は全643卓が4人。卓の英字が無い卓（E が「-」・空）は201卓。ステージ: ベスト8 318・決勝 177・ベスト16 100・ベスト32 48
- 1対局の卓数: 1卓 17・2卓 12・3卓 171・4卓 6・5卓 13
- 年度ページの生成物の `class="mj-saikyo-match"`・`class="mj-saikyo-table"` の数も年度ごとに上の表と同じ（計 219・643）。#425 の本文の「卓642」（10-05）から1卓増えている（2026年度の入力が進んだためとみられる。10-05 の内訳は確かめていない）
- **対局識別子の重複（新しく見つけた事実）**: 同じ年度に、同じ対局日の対局が2つある組が **14組**（2011・2014・2016・2017・2018×2・2019×2・2020〜2025 の各ファイナル最終日など。例: 2025 の `20251214` は「ファイナルトーナメント」と「ファイナル 2nd Stage」）。対局識別子は対局日だけ（`YYYYMMDD`）なので、2つの対局が同じ `?match=20251214` を持つ
  - 生成物で確かめた: `saikyo/2025.html` の2つの `<section class="mj-saikyo-match">` がどちらも `data-match="20251214"`、共有ボタンの `data-share-url` もどちらも `…/2025.html?match=20251214`
  - `assets/saikyo.js` は `matchSections.find(...)` で最初の1つだけを選ぶ。そのため2つ目の対局（例: 2nd Stage）の共有ボタンの URL を開くと、1つ目の対局（ファイナルトーナメント）が出る（コードと生成物からの判断。ブラウザでは確かめていない）
  - アンカー（`match-<先頭の卓のアンカー>`・卓の `id`）は、ステージ連番と卓の組み合わせが違うため今は衝突していない（16ページとも `id` の重複は0）。saikyo-page-design.md「アンカー」は、同日の2対局で衝突しうることを書いているが、`?match=` が重複することは書いていない
  - 対局日が全部埋まった（#323）ことで、以前は `<年度>-n<連番>` で分かれていた古い年度の同日の対局も、同じ識別子になった
- 「各対局の勝ち上がり者」の数（その対局の最後のステージで結果が「通過」の人。参考に数えた）: 1人の対局 201・4人 7・8人 6（ファイナルの 1st/2nd Stage）・0人 5（2026年度の未対局の対局）。最後のステージは 決勝1卓 177・ベスト16 1卓 24・ベスト8 2卓 12・ベスト16 4卓 6

#### ページ数とファイル数

| | 件数 |
|---|---:|
| 対局のページ（1対局1ページ） | 219 |
| 卓のページ（1卓1ページ） | 643 |
| 個別ページの合計 | 862 |
| 今の配信ファイル数 | 2,456 |
| 個別ページを足した後 | 3,318 |
| 上限 | 20,000 |
| 上限との差（足した後） | 16,682 |

- 今の配信ファイル数の求め方: `python3 scripts/check_asset_limits.py`（`git ls-files --cached --others --exclude-standard` から `.assetsignore` の項目〈その項目自身とその配下〉を除き、実在するファイルを数える。`published_files()`）。2,456（12.3%）。内訳の上位は live 908・houou 746・title 386・books 191・ルート直下 56・img 51・houou_race 41・wayhome 39・assets 17・saikyo 17
- 上限 20,000 は `check_asset_limits.py` の `MAX_FILES`（Workers Free の静的アセット、2026-09-28 確認と書かれている）
- 個別ページごとに OGP 画像を作ると、さらに最大 862 枚（`img/ogp/saikyo/`）。足しても 4,180 で上限の 21%
- 年度は毎年 約17対局・約49卓 増える

#### 今の作りの要点（origin/cloudflare 1245b908）

- **生成**: `scripts/generate_saikyo_pages.py`（735行）が1本で、トップ `saikyo/index.html` と年度ページ `saikyo/<年度>.html` 16枚と `sitemap-saikyo.xml` を書く。`main()` → `load_rows()`（シート＋SNS ブックの写真）→ `upgrade_x_images()` → `build_matches()` → 年度ごとに `display_order()`（日付の降順、未登録はシート順で後ろ）・`build_year_body()`・`render_content()`（`lib/page.py`）・`add_body_class()` → `write_top()` → シートに無い年度のページを削除 → `write_sitemap()`
- **対局識別子とアンカー**: `build_matches()` が対局日があれば `YYYYMMDD`、無ければ `<年度>-n<連番>`（年度内の未登録の対局にシート順で1から）。卓のアンカーは `build_match()` で `<識別子>-s<ステージ連番>[-<卓の小文字>]`（ステージ連番はその対局の表示順）。対局の `id` は `match_anchor()` で `match-<先頭の卓のアンカー>`、`data-match` は識別子
- **`?match=`**:
  - JS: `assets/saikyo.js` の年度ページの部分（`MATCH_PARAM = 'match'`）。`data-match` が一致する最初の対局だけを残し（選手名の絞り込みと併用）、`document.title` の先頭に対局名、「麻雀最強戦<年度>のすべての対局」のリンク（`#saikyo_all_matches`、生成時は `hidden`）を出す。一致しない値は絞り込まない
  - canonical: 年度ページは `canonical=<年度>.html`（クエリなし）。#113（canonical を付けない）の例外として SK-19 で付けた（`main()` のコメント）。トップも `canonical=/saikyo/`
  - プルダウン: `build_year_select()`。絞り込み中は JS が選べない同じ文字の項目を足し、同じ年度を選び直すと `?match=` を外した年度ページへ移る（SK-22）
  - 対局ごとの共有ボタン: `build_match_html()` が `share.build_share_button()` で `https://ryoei.pro/saikyo/<年度>.html?match=<識別子>` と「麻雀最強戦<年度> <対局名（日付）>」を焼き込む（219個。ページ全体の共有は #530 で外した）
- **サイトマップ**: `write_sitemap()` がトップ `https://ryoei.pro/saikyo/` と年度ページ16件の計17件。`?match=` は載せない。lastmod は既存を残し新規だけ当日、後で `update_sitemap_lastmod.py --from-git`
- **OGP**: `og_image_for()` が `img/ogp/saikyo/<年度>-black.png`（2011〜2026 の16枚）と `index-black.png` を使う。無ければ共通の `img/ogp.png`。対局・卓ごとの画像は無い
- **llms.txt**: 「## 麻雀最強戦」の節に「[年度別](https://ryoei.pro/saikyo/): 歴代最強位の一覧と…選手名で探せるトップ。年度ごと・卓ごとの対局結果は `https://ryoei.pro/saikyo/<年度>.html`。」。末尾のサイトマップの行に「麻雀最強戦のトップと年度別ページ（sitemap-saikyo.xml）」
- **`_redirects` の saikyo の行**: `/saikyo  /saikyo/  301`・`/saikyo/  /saikyo/index.html  200`・`/saikyo_results.html  /saikyo/  301`（2〜4行目、いずれも静的）。今の `_redirects` は静的38・動的4（`check_asset_limits.py`）
- **毎朝の再生成**: `.github/workflows/regenerate-saikyo.yml` が `check-image-links.yml`（「画像リンク切れの検知」）の完了（`workflow_run`、cloudflare）で `regenerate-page.yml` を `target_page: saikyo_pages` で呼ぶ。`regenerate.py` の `saikyo_pages` の対象パスは `saikyo/`、サイトマップは `regenerate-page.yml` の `sitemap*.xml` の glob で拾う。**`img/ogp/` は対象に入らない**（個別ページの OGP 画像を生成で作るなら、対象パスを足す必要がある）。個別ページを `saikyo/` の配下に置けば（c-1〜c-3 のどれでも）今の対象パスのままコミットされる
- **トップの検索結果のリンク**: `build_top_data()` が卓ごとに `[対局の添字, 卓のアンカー, 卓の見出し, 結果]` を持ち、`assets/saikyo.js` が `<年度>.html#<卓のアンカー>` を組み立てる（643種類）

#### 年度ページを「勝ち上がり者だけ」にしたときに変わるところ（今の HTML の構造）

今の年度ページの1対局:

```
section.mj-saikyo-match#match-<先頭の卓のアンカー>[data-match=<識別子>]
  div.mj-saikyo-match-head
    h2.mj-saikyo-match-title（対局名（日付））
    共有ボタン（.mj-share-btn.mj-share-btn-round、?match= の URL）
  section.mj-saikyo-table#<卓のアンカー>   ← ステージ×卓の数だけ（ベスト8 → 決勝の順）
    h3.mj-saikyo-table-title（「ベスト8 A卓」「決勝」）
    ol.mj-saikyo-players
      li.mj-saikyo-player[data-name]  ← 4人
        a.mj-saikyo-card（X へのリンク。X ID が無ければ div）
          img.mj-saikyo-photo（alt に名前と結果）
          span.mj-saikyo-rank（順位の数字。F が空なら無し）
          span.mj-saikyo-name（.is-pass 金 / .is-out 黒の帯）
```

- 卓ごとのまとまり `section.mj-saikyo-table` と見出し `h3.mj-saikyo-table-title` は、勝ち上がり者だけにすると「どの卓か」を出す場所でなくなる（卓は個別ページへ）。卓の `id`（643種類のアンカー）が年度ページから消えると、**トップの検索結果のリンク `<年度>.html#<卓のアンカー>` と外部からの `#` 付きリンクの着地先が無くなる**（行き先を卓のページへ変える必要がある）
- 1卓4人の `ol.mj-saikyo-players`（敗退者の `is-out` の帯・順位 `mj-saikyo-rank`）は、勝ち上がり者だけなら1対局1人（ファイナルの段は4人・8人、未対局は0人）になる。`is-out` の帯は年度ページから無くなり、バッジの文字（「1位通過」「優勝」）の扱いも変わる
- 対局の `section.mj-saikyo-match`・`h2`・共有ボタンは残しうる（個別ページへのリンクをどこに置くかは未決）
- 年度ページの選手名の絞り込み（`li[data-name]`）は勝ち上がり者しか探せなくなる。トップの全期間の検索（`saikyo_top_data`）は全員を持つので影響を受けない
- 未対局の卓だけの対局（2026年度の5対局）は、勝ち上がり者が0人になる

### 手順3: URL の形と転送の手段

#### ABEMA の URL があるか

| 場所 | ABEMA の URL | 最強戦 |
|---|---|---|
| リポジトリ（`docs/logs/` を除く `git grep`） | `https://abemafresh.tv/jpml` 1,959回・`https://abemafresh.tv/asa-jan` 3回。すべて `data/live_channel_raw.jsonl`（YouTube の概要欄の写し）の、旧 AbemaFRESH! のチャンネルの URL。ほかに `dic/mleague.json`・`scripts/promo_video/dictionary/` の辞書の語 | 動画・番組の URL は無い |
| /live の3層（`live_layer3.SPREADSHEET_ID`）【1】元データ 14,178行 | 概要欄に上と同じチャンネルの URL（1,956行）。番組・回の URL は無い | 「最強戦」を含む行は2件（YouTube。`GI5aXgtRU1I`「【インタビュー】麻雀最強戦2023優勝 桑田憲汰 特別インタビュー」・`uNuYUcP_mgM`「KONAMI麻雀格闘倶楽部最強戦出場選抜マッチ」）。対局の放送ではない |
| /live【2】自動変換後 14,178行・【3】手動補正 4,326行 | 0 | 【2】は上と同じ2件、【3】は0件 |
| 予定表（`yotei.SPREADSHEET_ID`）【1】1,132行・【2】1,132行・【3】1,132行 | 0（「abema」「アベマ」の語も0） | 0件 |
| 「最強戦」シート 2,744行（全列） | 0 | — |

- ABEMA の番組・回の URL の形は、リポジトリとシートからは分からない。手元にある例は #425・#501 の本文の `https://abema.go.link/8KZPv` → `https://abema.tv/video/episode/68-38_s26_p1`（チャット側の 2026-10-05 の取得）だけ。この1件から、URL に対局・卓・番組のどれが入るかは判断できない（`68-38` が番組、`_s26`・`_p1` が季・回にあたるように見えるが、規則性は #501 の「作る前に確かめること」）
- /live・予定表に最強戦の放送は載っていない（最強戦は連盟の YouTube・予定表の対象外とみられる）ので、最強戦の対局・卓と突き合わせられるものは無い

#### abema.tv への到達（1回）

`curl https://abema.tv/` → `CONNECT tunnel failed, response 403`（セッションのプロキシの拒否。`__agentproxy/status` の `recentRelayFailures` に `abema.tv:443`・`connect_rejected`）。ABEMA のサーバの応答ではない。環境の Network access の許可ドメインに `abema.tv` が無いため（docs/notes/cloud-sessions.md「ネットワーク」）。ここから ABEMA の可否は結論づけない

#### `_redirects` と Cloudflare のリダイレクト（公式文書、2026-10-11 に取得）

- Workers の静的アセットの `_redirects`（https://developers.cloudflare.com/workers/static-assets/redirects/ ）: 「Advanced redirects」の表で「Query Parameters ❌ `/shop id=:id /blog/:id 301`」。宛先には「Can include fragments, query strings, splats, and placeholders」。上限は「2,000 static redirects and 100 dynamic redirects」。→ **クエリ付きの URL を条件にした転送はできない**（宛先にクエリを付けることはできる）
- Bulk Redirects（https://developers.cloudflare.com/rules/url-forwarding/bulk-redirects/reference/url-components/ ）: Query string の行が「Supported in source URL: No」。→ **できない**
- Single Redirects（Redirect Rules）: 上限は Free 10・Pro 25・Business 50・Enterprise 300 ルール、Wildcard は全プラン Yes、正規表現は Business 以上（https://developers.cloudflare.com/rules/url-forwarding/ の「Availability」）。Wildcard の動的な転送は `wildcard_replace(http.request.full_uri, r"<REQUEST_URL_PATTERN>", r"<TARGET_URL_PATTERN>")` で、宛先に `${1}`・`${2}` を使う。Preserve query string は既定で無効（https://developers.cloudflare.com/rules/url-forwarding/single-redirects/settings/ ）。`http.request.full_uri` は「The full URI as received by the web server」で、例の値はクエリを含む（`https://www.example.org/articles/index?section=539061&expand=comments`、https://developers.cloudflare.com/ruleset-engine/rules-language/fields/reference/http.request.full_uri/ ）。wildcard は大文字小文字を区別しない（strict wildcard は区別する）、`*` は0文字以上、`/` に特別な意味は無く、値の全体が一致する必要がある（https://developers.cloudflare.com/ruleset-engine/rules-language/operators/ ）。→ **Wildcard の1ルールでクエリ付きの完全な URL に一致させられる見込み**（RUN-06 の調べと同じ。実地では試していない）
- 実行の順: Single Redirects は Bulk Redirects より先（同じ「url-forwarding」の Execution order）。`_redirects` との順は公式の `_redirects` の頁に「Bulk Redirects will run in front of your Worker」とある。docs/notes/cloudflare.md の www→apex の節に「Redirect Rules は Workers より前に評価される」
- docs/notes/cloudflare.md のリダイレクトルールの記録: 「www→apexのRedirect Rule（#115、2026-09-11対応完了）」の1件だけ（Wildcard pattern・`https://www.ryoei.pro/*` → `https://ryoei.pro/${1}`・308・Preserve query string 有効・Place at First）。ほかのルールの記録は無い。プランは Pro（同「Cloudflare Pro でできること・できないこと」）。ダッシュボードは触っていない

#### 転送の対応で気づいたこと（事実から）

- 個別ページの URL に年度と対局識別子がそのまま入る形（例: #425 本文の c-1 `/saikyo/<年度>-<対局識別子>.html`）なら、`https://ryoei.pro/saikyo/*.html?match=*` → `https://ryoei.pro/saikyo/${1}-${2}.html`（Preserve query string 無効・301）の1ルールで、対応表なしで畳める見込み。URL に識別子と違うもの（連番・対局名など）を使うなら、対応表（ルールでは書けないので `_redirects` か JS）が要る
- ただし、上の「対局識別子の重複」14組では、今の `?match=<日付>` が2つの対局のどちらを指していたかが URL から分からない（今の JS は1つ目を出す）。個別ページで2つを分ける識別子にするなら、`?match=` の転送先は1つ目の対局のページにするか、年度ページにするかを決める必要がある
- `?match=` の値が個別ページに無いもの（古い `<年度>-n<連番>` の共有 URL、打ち間違い）は、ワイルドカードの1ルールだと存在しない URL への 301 → 404 になる。今は「該当する対局が無い値は絞り込まない」（年度ページの全件表示）
- `?match=…&utm_source=…` のように後ろに別のパラメータが付くと、`${2}` に混ざる（RUN-06 の指摘のまま）

### マージ

- 再 fetch で origin/cloudflare が先へ進んでいた（XAP-09・PHT-23 のログ、docs/decisions/title.md）ため `git merge origin/cloudflare` で取り込んだ（衝突なし）。`git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1011-abm:cloudflare`（109c57a3..850921a6）。cloudflare との差はこのログ1ファイルだけ

### docs/decisions

この指示の「決定」節は docs/decisions/features.md の 2026-10-05（RUN-05〜07）の #425 の行の再掲で、新しい決定は無い。作業中の平野さんの回答も無い。docs/decisions/ には足していない

## 報告

- 状態: 判断待ち / 続き: CHAT-1011-ABM-02
- ブランチ: work/1011-abm
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1011-ABM-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-abm
- 確認用URL: なし
- マージ: 済（850921a6。origin/cloudflare を取り込んだ後、`git push origin work/1011-abm:cloudflare`）
- issue: #425・#501・#502（読むだけ。書き込みなし）
- 判断が必要なこと:
  - /grill-me で決めることの一覧（調べた事実で直した。【足】は足したもの）
    1. URL の形（対局のページと卓のページ）: #425 本文の c-1（`/saikyo/<年度>-<識別子>.html`、`_redirects` 不要）・c-2・c-3 のどれか。卓のページの名前（例: `<年度>-<識別子>-s<n>-<卓>.html`）。どれも `saikyo/` の配下なら毎朝の再生成の対象パスは変えずに済む
    2. 【足】対局識別子の重複: 同じ年度の同じ日の対局が14組あり、今は2つが同じ `?match=<日付>` を持つ（2つ目の対局の共有 URL は1つ目を出す）。個別ページの識別子を `YYYYMMDD` のままにするか、2つを分ける規則（例: 対局名の順の枝番）にするか。#425 本文の「鍵は（年度, 対局, ステージ, 卓）」も対局日を含めるかを合わせて決める。今の共有ボタンの不具合として先に直すかも
    3. 日付未登録の対局の URL: 今は0件（全年度の対局日が埋まった）。決めることは「今後、対局日が空の行が入ったときに生成を止めるか・`<年度>-n<連番>` のまま出すか」だけに縮んだ
    4. 対局のページと卓のページの中身（卓のページ: 4人の写真・順位・結果・ABEMA のリンク、対局のページ: 卓の一覧と勝ち上がり、前後の対局への導線など）と、固定バー・`mj-saikyo-page` の有無（#428）
    5. 年度ページの勝ち上がり者の見せ方: 「勝ち上がり者」の定義（最後のステージの通過者なら 1人 201対局・4人 7・8人 6・0人 5〈未対局〉）、未対局の対局の出し方、卓の見出し・`is-out` の帯・順位が無くなった後の形、年度ページの選手名の絞り込みを残すか
    6. 【足】トップの検索結果のリンク: 今は `<年度>.html#<卓のアンカー>`（643種類）。年度ページから卓が消えるので、卓のページへ変えるか。外部からの `#` 付きリンクの扱いも
    7. 【足】対局ごとの共有ボタン（#530 で「残す」と決めたもの）: 個別ページができた後、URL を個別ページにするか・外すか（#530 の期日 10-24 と合わせる）
    8. サイトマップ・canonical・OGP・`llms.txt`: `sitemap-saikyo.xml` 17 → 879件（#458 の URL 検査の対象にも入る）、個別ページの canonical（自分自身にするか）、OGP 画像（年度の画像を使い回すか、作るなら `img/ogp/` は毎朝の再生成の対象パスに無い）、`llms.txt` の「## 麻雀最強戦」の書き方（新しいページは未公開で入れて公開の issue で載せる、docs/new-page-checklist.md）
    9. `?match=` の転送: URL の形が識別子をそのまま含めば Single Redirects の Wildcard 1ルールで対応表は不要の見込み。【足】重複14組の行き先、個別ページに無い値（古い `n` 形式・打ち間違い）が 404 になってよいか、後ろに別のパラメータが付いたとき、Pro の25ルールの今の使用数（記録は www→apex の1件だけ。平野さんにダッシュボードで確かめてもらう）
    10. #501 のスクリプトの入出力: 入力はシートの動画 URL の列（下の11の形に依存）、出力は CSV（HTTP の状態・転送先・og:title・og:image）。【足】シートのどの行（卓の4行のどれか）を1件と数えるか、出力をどこに置くか（リポジトリに入れるなら `.assetsignore` の確認、#133）
    11. シートの列の形: 今は A〜L（L 備考）。URL は卓ごとだがシートは1人1行（1卓4行）なので、4行すべてに同じ URL を書くか・先頭の行だけか・別のタブ（キーは 年度・対局日・対局・ステージ・卓）にするか。生成は `SELECT A..H` と `row[:COL_X_ID]` の位置で読むので、L の後ろに足せば今の生成は壊れない（途中に挿入すると壊れる）
  - 【足】文書の古い記述（この指示では変えていない）: docs/notes/saikyo-page-design.md「1. データ源」の列の表に L 備考が無く、A 列の備考「2011〜2020年度の全行と2021年度の一部は空欄」は今は当たらない（空欄の対局は0）。同「アンカー」に `?match=` の識別子が同日の2対局で重複することが書かれていない。直すかは平野さんの判断
- 未確認の項目:
  - Single Redirects の Wildcard でクエリ付きの URL に一致させられるかは、公式文書からの見込み。実地では試していない（ダッシュボードはセッションから見られない）
  - ABEMA の URL の規則性・取得の可否（セッションから abema.tv はプロキシで拒否。#501 で平野さんの PC から確かめる）
  - 対局識別子の重複で2つ目の対局の共有 URL が1つ目を出すことは、コードと生成物からの判断で、ブラウザでは確かめていない
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 44da572e）: https://github.com/retroeater/mj-logs/tree/main/guide/44da572e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
