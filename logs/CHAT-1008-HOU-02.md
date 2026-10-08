# CHAT-1008-HOU-02

- 着手日時: 2026-10-08
- 対象issue: #518・#141・#371・#228・#111
- ブランチ: work/1008-hou
- 着手時HEAD: c5b12292

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の新ページ `houou/` の動作サンプルを4画面すべて未公開の形で作り、作業ブランチのプレビューまで（マージせず判断待ちで止まる）
Chat-Ref: CHAT-1008-HOU-02
マージ: 判断待ちで止まる（平野さんがプレビューを iPhone と PC で見て決める。cloudflare へはマージしない）
貼る時機: CHAT-1008-HOU-01 の完了（docs のマージ済み）の後。いつでも（新しいセッションに貼る）
作業ブランチ: クラウドセッションで実行する。work/1008-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   CHAT-1008-HOU-01 のログの `## 報告` を読み、状態が「判断待ち」でマージが「済」でなければ何もせず止まる。`docs/notes/houou-top.md` と `docs/decisions/houou.md`（2026-10-08 の節）が origin/cloudflare にあることを確かめる（無ければ止まる）。
   この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

## 目的
`docs/notes/houou-top.md` の仕様どおりに、`houou/`（トップ＋検索結果）・`houou/ranking/`・`houou/leagues/`・`houou/race/` の4画面を**未公開の形**で作り、作業ブランチのプレビューで平野さんが動作を確かめられる状態にする（#518。ランキングは #141 の移植、成績詳細は #111 の鳳凰戦の分の置き換え、#371・#228 を含む）。平野さんはしばらく応答できないため、**途中で質問せず、仕様に無いことは仮置きで進め、確かめてほしい点を報告にまとめる**（平野さんの決定、grill Q18）。

### 決定（2026-10-08、平野さん）
- 仕様と決定は `docs/decisions/houou.md`（2026-10-08 の節、grill Q1〜Q18）と `docs/notes/houou-top.md` のとおり。この指示文はそれを繰り返さない。食い違えばそちらが正
- 動作サンプルは4画面すべて（トップ＋検索結果・ランキング・リーグ推移・順位変動）を作り、プレビューまで。可能な限り止めずに進め、確認は後でまとめて行う（grill Q18）
- ランキングは現行ページと突き合わせ、違いがあっても止めずに一覧にし、まとめて確認を依頼する（grill Q5）

### 前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。HOU-01 の報告「チャット側が決めておくこと」への答え:
- (1) 作業ブランチは work/1008-hou を使う（マージ済みなので origin/cloudflare から作り直す。上の「作業ブランチ」の行）
- (2) マージはしない。作業ブランチの push で Workers Builds のプレビューが出る（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」）。出なければ Codespace ではないので、`npx wrangler dev`（CLAUDE.md「禁止事項」の起動方法）か headless Chromium の `file://` ではなく、ビルドが走らなかった事実と原因を報告に書く（確かめは headless Chromium で作業ツリーの HTML を直接開いて行い、その旨を書く）
- (3) `houou/` を配信の対象にする（`.github/workflows/assets-check.yml` の `allowed()` に `houou` を足す）。平野さんの「非公開のまま開発」の決定と、`houou_race/` で同じ判断をした実例（docs/decisions/houou.md 2026-10-06 の「`houou_race/` を配信の対象にしてよい（未公開の形で）」。publishing.md に houou_race の記述は無い）に合わせたチャット側の判断。`docs/decisions/publishing.md` には「houou/ を未公開の形で配信の対象にした（チャット側の判断、平野さんの確認は HOU-02 のプレビューの後）」と書く。`.github/workflows/` を変えるので、着手時に docs/notes/branch-operations.md「ワークフローを変更したとき」を読む。assets-check は push で走る見込みなので、作業ブランチの push の結果で確かめる（手動実行は要らない。走らなければ報告に書く）
- (4) URL パラメータの規約（docs/new-page-checklist.md「URL パラメータの規約」）に `term`（期と前後。`43-1` の形。1は前期、2は後期）と `division`（ランキングの部門名）を足す。`all`・`name`・`league` は既にある
- (5) 止まらない条件: 突合の違い・仕様に無い見た目や文言・データの揺れ（同点・空欄・改名など）は、仮置きの判断を1つ選んで進め、「仮置きの一覧」に書く。止まる条件は下の「止まる条件」だけ
- (6) 1回の指示で4画面すべて（grill Q18）。ただし手順は3つに分け、途中で止まったときに何が済んだか分かるようにする。セッションが途中で失われたときのために、手順ごとに push する
- title の形（HOU-01 の報告の論点）: 仮置きとして、トップは決定どおり「鳳凰戦（リーグ戦） | ryoei.pro」、ランキング・リーグ推移・順位変動は今の旧ページと同じ形（「ランキング | 鳳凰戦 | ryoei.pro」など）、検索結果の表示中は決定どおり「<選手名> | 成績詳細 | 鳳凰戦 | 日本プロ麻雀連盟 | ryoei.pro」。そろえるかは平野さんがサンプルを見て決める（仮置きの一覧に書く）
- 旧ページ（`houou_ranking.html`・`houou_leagues.html`・`houou_race.html`・`houou_results.html`）と、ほかのページの生成物は**この指示では一切変えない**。既存の部品（`lib/leagues.py`・`generate_houou_race.py` の集計・`lib/share.py`・`assets/share.js`・`style.css` の既存の節）を lib に寄せたり拡張したりするときは、変える前に `git grep` で参照を洗い出し、変えた後に `python3 scripts/regenerate.py all` で全ページを生成して、`houou/` の新規ファイルと `docs/`・`.github/` 以外に差分が無いことを確かめる（シートの変化で動く生成物の差分は、変わったファイル名と行数を報告に書く。houou_race は 2026-10-08 時点のデータで生成されているので差分は出ない見込み）。再生成に YouTube API の鍵が要るページ（`jpml_pros`）は、鍵が無ければ対象から外してその旨を書く
- `houou_race.js`・`houou_race/` の JSON は、`houou/race/` では写し（または共通化）で使い、旧 `houou_race.html` の動きは変えない。`?term=&league=` の受け取りは `houou/race/` 側だけに付ける
- 既定で在籍者のみ（#371）の「在籍」は「プロ」タブの `Y = "Y"` の行にいる名前（`lib/names.py` の `NameBook` と、houou_race の画像の引き方と同じ。「別名」で現在名に直してから引く）。ランキングの表に出す名前は「鳳凰」タブの名前のまま
- 突合（#141 本文の進め方）: 9部門×上位100件を、現行の `league_ranking.js` の計算と同じ条件で Python で出し、現行ページの表示と突き合わせる。現行ページは Google Charts がブラウザで計算するので、headless Chromium で本番の `https://ryoei.pro/houou_ranking.html?sheet=鳳凰&division=<部門>` を開いて表を読み取るか、`league_ranking.js` を Node で動かす（どちらが確実かは実物で決める。本番を読むときは `?v=<未使用の値>` を足す）。シートの読み取り時刻の差で違いが出ることがあるので、同じ日に取る。違いは「部門・順位・名前・現行の値・新の値・考えられる理由」の表で報告に書く（止まらない）
- 「鳳凰」タブの行数の止まる条件: 14,000 未満か 20,000 超なら件数を書いて止まる（HOU-01 の時点で 15,416 行・1,296 名）
- 選手ごとの JSON は 1,296 ファイル前後になる見込み。Workers の静的アセットの上限 20,000 ファイルに対して、`git ls-files` で配信対象の総数を数えて報告に書く（`.assetsignore` の除外後。目安として 15,000 を超えていたら「判断が必要なこと」に書く）
- noindex は生成スクリプトの定数（`NOINDEX_TAG`。`generate_books_pages.py` に同名の定数があるので同じ形で、`generate_houou_pages.py` に置く）で全ページに付け、navbar・`sitemap*.xml`・`llms.txt`・既存ページからのリンクには載せない（docs/new-page-checklist.md 段1）。公開の issue はこの指示では起票しない（本実装の指示で起票する）。`docs/notes/static-generation.md`「ページの一覧」には「noindex・メニュー未掲載、公開は別 issue（未起票）」と書く
- 共有ボタンの URL と文言を表示中の状態に合わせて JS で差し替える拡張は、`assets/share.js` を変えずに済むなら `assets/houou.js` 側で行う（`data-share-url`・`data-share-text` を書き換えるなど）。`share.js` を変えるときは上の全ページ再生成の確かめを行う
- アクセシビリティ: #178〜#185 の指摘を避ける（select に label、検索欄に `aria-label`・`aria-controls`、候補は `role="listbox"`、行を開く操作はボタンで `aria-expanded`、インラインの `onchange`・`onerror` は使わない）。色は `.mj-table` の値（CLAUDE.md・style.css のコメント）を使う
- 見た目の基準は `docs/notes/houou-top.md`「共通」。順位変動の CSS 変数（`style.css`「鳳凰戦 順位変動」）を元にする。見比べの比較ページは作らない（仮置きで1案に決めて作り、平野さんがサンプルを見て直す）
- 確かめ（手順3）は headless Chromium で、iPhone の幅（390×844、3倍）と PC の幅（1280 と 1920×1080）の両方。スクリーンショットはログに貼れないので、見た結果（はみ出し・重なり・崩れ）を文で書く
- 使う skill: `/grilling`・`/grill-me` は使わない（平野さんが不在）。`/domain-modeling` も使わない。`/cloudflare` は要らない見込み

## 手順
1. 集計とデータの移植（コードとテスト。ページはまだ作らない）
   - `scripts/lib/ranking.py`（9部門の集計。大会をパラメータに。`league_ranking.js` の閾値〈期連続浮き回数の最小回数 鳳凰6・桜花3・JWRC3・特昇3、節連続浮き回数 鳳凰8・桜花6・JWRC5…、`DEFAULT_RANK_LIMIT`・小数桁〉を定数で持つ）と `scripts/lib/results.py`（選手ごとの成績 JSON と索引 `search.json`）を作る。「鳳凰」タブは `lib/sheets.py` の `fetch_records()` で見出しの名前で読む
   - `scripts/tests/` に、小さな固定データでの9部門の集計・成績 JSON の形・索引の正規化（NFKC）のテストを足し、`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
   - 突合を行い、結果（一致した部門・違いの表）をログの経過に書く。ここで一度 push する（`[sync-logs]` は付けない）
2. 4画面の生成と未公開の形
   - `scripts/generate_houou_pages.py`（`regenerate.py` の `OUTPUT_OVERRIDES` に登録）で `houou/index.html`・`houou/search.json`・`houou/results/*.json`・`houou/ranking/index.html`・`houou/leagues/index.html`（＋`data.json`）・`houou/race/index.html`（＋`<期>-<1|2>.json`）を書き出す。`assets/houou.js` と `style.css` の `houou/` の節を書く。`?name=`・`?division=`・`?all=1`・`?term=&league=` を受ける
   - noindex・`assets-check.yml` の `allowed()`・`docs/notes/static-generation.md`「ページの一覧」（件数も）・`docs/new-page-checklist.md` の URL パラメータの規約・`docs/decisions/publishing.md`・`docs/notes/houou-top.md`（実物に合わせて直し、仮置きを足す）を更新する。`llms.txt`・navbar・sitemap は触らない
   - 全ページを再生成し、`houou/`・`docs/`・`.github/`・`assets/houou.js`・`style.css`・`scripts/` 以外に差分が無いことを確かめる（上の「前提」）。push する
3. プレビューでの確かめと報告
   - Workers Builds のプレビュー URL を取り（待つ上限は15分。超えたらその時点の状態を「未確認の項目」に書いて先へ進む）、headless Chromium で4画面を iPhone の幅と PC の幅で開き、仕様（`docs/notes/houou-top.md`「画面ごとの仕様」）の各項目が動くことを1つずつ確かめる（検索の候補→結果→`?name=`、行を押して節を開く、ローソク足、ランキングの部門の切り替えと `?all=1`、リーグ推移の `?name=`、順位変動の `?term=&league=` と既定、共有ボタンの URL と文言、noindex が全ページにあること、本番の navbar・sitemap・`llms.txt` に `houou/` が無いこと）
   - 報告の「判断が必要なこと」に、平野さんが確かめる手順（画面ごとに、プレビューの URL は最終報告のターミナルにだけ書き、ログには「確認用の URL は最終報告」と書く）と、仮置きの一覧（HOU-01 の分と今回足した分）、突合の違いの表、題（title）の形の論点を書く

## 止まる条件
- 0章の確かめが通らない（HOU-01 が判断待ち・マージ済みでない、仕様メモ・決定が無い）
- 「鳳凰」タブの見出しが `docs/notes/houou-top.md`「データの持ち方」と違う、行数が 14,000 未満か 20,000 超
- #518・#141 に他セッションの着手中コメントがある。`git branch -r --no-merged origin/cloudflare` のブランチで `houou/`・ランキング・成績詳細・`league_ranking.js`・`lib/leagues.py`・`generate_houou_race.py` に触れているものがある（work/1008-hou 自身を除く）
- 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た（既存の部品を変えた影響。直せなければ止まる）
- 変更が `.github/workflows/assets-check.yml` の `allowed()` の1行を超えてワークフローに及ぶ
- cloudflare への push は行わない（判断待ち）。作業ブランチへの push が権限判定で拒否されたら別の手段を試さずに止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。「判断が必要なこと」に、確かめる手順・仮置きの一覧・突合の違い・題の形の論点を書く
- `docs/decisions/houou.md` には、この指示の「決定」節の新しい決定は無いので足さない（仮置きは決定に書かず `docs/notes/houou-top.md`「仮置きの一覧」に書く）
- マージは冒頭の「マージ:」の行のとおり（マージしない）
- ターミナルへの最終報告に「確認用:」の行（プレビューの URL。4画面分）を書き、Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-HOU-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-HOU-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-08 着手。**このセッションは新しく始めたものではない**（CHAT-1008-HOU-01 を行ったセッションに続けて貼られた。指示文は新しいセッションの前提）。HOU-01 の文脈を持っているが、作業は指示文と cloudflare 上の文書に従う
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-01 のログ（origin/cloudflare）の報告は 状態「判断待ち」・マージ「済（8ab65260 …）」。`docs/notes/houou-top.md` と `docs/decisions/houou.md` の 2026-10-08 の節は origin/cloudflare にある
- Chat-Ref の確認: `CHAT-1008-HOU-02` のコミットは無し（識別子 HOU は HOU-01 で使用済みの同じチャットのもの）
- 作業ブランチ: ローカルの `work/1008-hou`（c5b12292）は origin/cloudflare（c5b12292）と一致し、その祖先（cloud-sessions.md「ローカルにあり origin/cloudflare の祖先」）。`git checkout work/1008-hou` のまま、`--ff-only` は進める分が無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

- 着手中のコメント: #518・#141（他セッションの着手中コメントは無し）。未マージのブランチは `origin/work/1008-nen`（#277 の docs）と `origin/work/1008-wkr-11`（#504 のワークフロー）で、`houou`・ranking・results・`league_ranking.js`・`lib/leagues.py`・`generate_houou_race.py` に触れない

### 手順1 集計とデータの移植

- `scripts/lib/ranking.py`（9部門。閾値は `Contest` に大会ごと: 鳳凰 10期・50節・期連続6・節連続8、`節単位浮き率` は50件。桜花・JWRC・特昇も旧JSの値を写した）、`scripts/lib/results.py`（選手ごとの JSON・索引・NFKC の正規化）、`scripts/tests/test_houou_pages.py`（18件）を作った。`python3 -m unittest discover -s scripts/tests` は 662件 OK
- 突合の方法: 本番の `houou_ranking.html` はセッションから Google Charts（`www.gstatic.com`）を読めず（プロキシで `ERR_TUNNEL_CONNECTION_FAILED`）、headless Chromium では表が出ない。代わりに、旧 `league_ranking.js` をそのまま Node で実行した（`google.visualization.DataTable` だけを最小の代用にし、`getChartData('鳳凰', 部門, データ)` を呼ぶ。データは旧JSと同じ gviz のクエリを同じ時刻に読んだもの）。代用の並べ替えは安定ソート・文字列は `<` の比較で、本物の Google Charts の同値の並びと違う可能性はある（同値の並びの違いは下の表の「理由」のとおり順位には影響しない）
- 結果（2026-10-08、「鳳凰」タブ V="Y" 15,416行）: 7部門は件数・順位・名前・値が一致（通算得点 100・期単位浮き率 100・期連続浮き回数 88・節最高得点 100・節単位浮き率 50・節連続浮き回数 100・連続昇級回数 95）。違いは2部門で次の表

| 部門 | 順位 | 名前 | 現行の値 | 新の値 | 考えられる理由 |
|---|---|---|---|---|---|
| 通算得点/期 | 4 | 川村直寛 | 72.0 | 72.1 | 864.6/12 = 72.05 ちょうど。旧は gviz の SUM の浮動小数の誤差（…49999）で切り下げ。新は四捨五入 |
| 通算得点/期 | 41 | 里木祐介 | 34.5 | 34.6 | 414.6/12 = 34.55（同上） |
| 通算得点/期 | 50 | 沖野健行 | 32.7 | 32.8 | 458.5/14 = 32.75（同上。gviz の SUM は 458.49999999999994） |
| 通算得点/期 | 53→51 | 中島寿太郎 | 32.0 | 32.1 | 320.5/10 = 32.05（同上）。値が 32.1 になり、伊藤鉄也・上田直樹（32.1）と同じ51位 |
| 通算得点/期 | 93 | 藤井崇勝 | 23.8 | 23.9 | 429.3/18 = 23.85（同上） |
| 通算得点/期 | 100 | 小野塚永遠 | 22.3 | 22.4 | 268.2/12 = 22.35（同上） |
| 期最高得点 | 78 | 岡田啓佑・小松武蔵 | 岡田→小松 | 小松→岡田 | 同じ 233.3（同じ78位）の並び。旧は gviz の並び、新は名前の文字コード順 |

- 旧JSの不具合で、今回のデータでは表に出なかったもの（移植で直した。`lib/ranking.py` の冒頭）: 期単位浮き率の最後の選手の率、節連続浮き回数の各選手の最初の行の 0点の節。通算得点の同値の並び（旧は gviz の並び）は今回の上位100件では一致
- 突合に使ったスクリプト（`fetch_old.py`・`run_old.js`・`compare.py`）は scratchpad に置いた（コミットしない）

### 手順2 4画面の生成と未公開の形

- 再生成の基準: 既存の部品を変える前に全ページを生成した（`regenerate.py all` は `resource_dictionary` が「辞書」タブの未知のカテゴリ「連盟」で止まるため、ページごとに実行。`resource_dictionary` は変更の前後とも同じ理由で失敗し、生成物は変わらない。シート側の問題でこの作業とは無関係。`books_pages` は凍結で対象外）。シートの変化による差分は4ファイル（`houou_leagues_data.json`〈石川豪士の順位、37後 D3 の直しの反映〉・`title/search.json`・`title/wrc/1.html`・`title/wrc/2.html`〈第1回・第2回に年が付いた〉）で、どれも1行
- 既存の部品の変更: `generate_houou_leagues.py`（`load()`・`build()` に分けた）・`generate_houou_race.py`（`load_periods()` に分け、`render_page()`・`write_data()` にパスの引数）・`assets/share.js`（押したときに data 属性を読む）。参照は `git grep` で洗い出した（`check_leagues_dropped.py`・`tests/test_regenerate.py`・`tests/test_houou_race.py` は使う名前が変わらない）
- 変更後に全ページを生成し直し、`houou/` 以外の生成物の差分が基準と同じ4ファイル・同じ内容（sha1 一致）であることを確かめた。この4ファイルはコミットしていない（シートの変化で、この指示の対象外）。`jpml_pros` は鍵なしで生成でき、差分なし
- 指示の「差分を許す範囲」の外の変更が1つある: `_redirects` に3行（`/houou` → `/houou/` の301、`/houou/` と `/houou/:slug/` → `index.html` の200）。`wrangler.jsonc` の `html_handling: "none"` のため、無いと `/houou/` などのディレクトリの URL が 404 になる（`title/`・`live/` と同じ書き方）。メニュー・sitemap 等からは辿れないままで、未公開の形は保つ
- `assets-check.yml` の `allowed()` は1行（`houou` を足した）。docs/notes/branch-operations.md「ワークフローを変更したとき」を読んだ。push で走る（`work/**`、`workflow_dispatch` もある）ので push の結果で確かめる
- 生成: `houou/` に 1,343ファイル・3,811,827バイト（選手ごとの成績 1,296、`race/` の JSON 41、`index.html` 4、`search.json`・`leagues/data.json`）。配信対象の総数（`.assetsignore` の除外後、`check_asset_limits.py`）は 3,049 / 20,000（15.2%）、`_redirects` は静的 38・動的 3
- 手元の確かめ（`python3 -m http.server` で作業ツリーを配信し、headless Chromium〈Playwright〉で 390×844〈3倍〉・1280×800・1920×1080）: 検索の候補 → 選択 → `?name=` と title と共有の URL・文言、`?name=` の直接、無い名前、行のボタンで各節（`aria-expanded`）、ローソク足、ランキングの部門・`?all=1`・`?name=`、リーグ推移の `?name=` と共有、順位変動の `?term=30-2&league=C1`・既定・選び直しで URL、noindex、横のはみ出し 0、JS のエラー 0。見た目を見て2点直した（成績の表の縞が開く行のせいで出ていなかった、PC の成績の表が 720px に収まらず 960px まで広げた）
- テスト: `python3 -m unittest discover -s scripts/tests` 662件 OK
- 文書: `docs/notes/houou-top.md`（実物に合わせて直し、仮置きを足した）・`docs/notes/static-generation.md`「ページの一覧」・`docs/new-page-checklist.md`「URL パラメータの規約」（`term`・`division`）・`docs/decisions/publishing.md`

## 報告

- 状態: 作業中
- ブランチ: work/1008-hou
- ログ: https://github.com/retroeater/mj/blob/work/1008-hou/docs/logs/CHAT-1008-HOU-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a6f17ef3）: https://github.com/retroeater/mj-logs/tree/main/guide/a6f17ef3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a6f17ef3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cb2ba7f5.md
