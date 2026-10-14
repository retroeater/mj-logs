# CHAT-1014-MTK-01

- 着手日時: 2026-10-14
- 対象issue: #569
- ブランチ: work/MTK
- 着手時HEAD: 67ac863

## 指示

【Claude作成】Claude Code 向け指示：「Mつく」を帰り道と同じ仕様・デザインで作り直す（一覧 /mtsuku/・各話、未公開でプレビューまで）
Chat-Ref: CHAT-1014-MTK-01
マージ: 判断待ちで止まる（平野さんが動く画面〈プレビュー〉で仕様を確かめてから決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/MTK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/MTK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/MTK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   **平野さんは約5時間不在。着手前・作業中に質問せず、帰り道との差異や不明な点は「仮置き」で決めて進め、仮置きしたことをすべて報告の「判断が必要なこと」に一覧で書く。止まるのは下の「止まる条件」に当たるときだけ。**

## 目的
今の「Mつく」（`video_mtsuku.html`、型Aの3列の表。YouTube チャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」の動画の一覧）を、帰り道（一覧 `/wayhome/`・各話 `wayhome/<動画ID>.html`）と同じ仕様・デザインの一覧 `/mtsuku/` と各話 `mtsuku/<動画ID>.html` に作り直し、未公開の形でプレビューに出す。平野さんは戻ったらプレビューで仕様を確かめる。

### 決定（2026-10-14、平野さん）
- 「Mつく」を「帰り道」と同様の仕様・デザインにする。差異や不明な点は仮置きして進める
- 各話の主人公（ヒーローの名前と X の写真、「〇〇の他のエピソード」の束ね方）は、チームを作る人だけ。選ばれた4人はヒーローに名前を並べ、一覧の検索で拾う（「〇〇が選ばれた回」の段は作らない）
- 新しい回の検知はしない。シートに行を足せば、週次の再生成が YouTube の情報を取って反映する形にする（Mつくは別チャンネルで /live の取り込みからは拾えないため）
- 一覧の OGP の合成画像（最新4本のサムネイルの2×2）は作らない。一覧の og:image は最新話の YouTube サムネイルをそのまま使う（各話と同じ扱い。帰り道の合成画像は三田さんの許可を取って作ったもので、麻雀遊戯王の許可は取っていないため）
- URL は `/mtsuku/`（一覧は `mtsuku/index.html`）。docs/new-page-checklist.md の段1のとおり未公開（noindex・どこからも未リンク・サイトマップ未参照・`llms.txt` に載せない）で作り、旧 `video_mtsuku.html` は今のまま残す。公開と旧 URL の 301 は別の issue にする

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 帰り道の仕様の正は `docs/decisions/wayhome.md`・`docs/notes/video-wayhome.md`。「同じ仕様」はここに書かれた一覧・各話の形（濃色固定〈`docs/new-site-design.md` の「動画セクションだけ濃色の例外」に入る〉、最新話のヒーロー、横スクロールのカード、固定の絞り込みバー、件数の表示なし・0件の知らせ、再生時間、「▶ 再生」、ヒーロー左上の「一覧に戻る」、前後のエピソード〈左が次・右が前、公開日つき、片方だけでも2分割〉、「〇〇の他のエピソード」、各話の canonical・`VideoObject`・`BreadcrumbList`、共有ボタンなし、各話の `.mj-lead` なし）を指す
- 帰り道に固有で Mつくに当てはまらないもの（チャット側の読み）: 「決勝戦を見る」と title/ の期のページとの相互リンク、/live からの新しい回の検知と常設 issue、一覧の OGP 合成画像。作らない
- 今の Mつくの元データは `video_mtsuku` の生成スクリプトが読むブックの「Mつく」タブ（要確認。列の並び・見出し・作る人とチーム名と所属の書き方は実物で確かめる）。シート（列・見出し・値）は変えない。列を足す必要があれば、足したい列を「判断が必要なこと」に書き、足されていなくても生成が通る作りにする
- 本番の今のページは61本、各行は「公開日・作る人（所属）・チーム名」と「選ばれた4人（所属）」（2026-10-14 にチャット側が本番で見た。要確認）。所属の表記（「（連盟）」など）は今のページのまま出す
- 仮置きの案（チャット側の案。実物を見て変えてよい）:
  - 紹介文（ヒーロー・meta description・`VideoObject.description` で同じ文）は自動で組む。例「YouTube チャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」。<作る人>（<所属>）が作ったチーム「<チーム名>」の回です。」。チーム名が空の回の書き方も決める。帰り道の「紹介文（補正用）」と同じ名前の列がタブにあれば上書きに使う（無ければ自動の文だけ）
  - ヒーローの X の写真は帰り道と同じ読み元（SNS ブック、`lib/sns.fetch_pros()`）・同じ大きさ・同じフチで、引ける作る人にだけ出す。連盟以外など引けない人は名前だけ。読めない写真で生成を止める帰り道の規則（WHS-09）を、写真を出す人に同じく当てる
  - 一覧の検索は、作る人・チーム名・選ばれた4人・所属で拾う。今のページの `?name=` の初期値を引き継ぐ（後の 301 で検索の URL を生かすため）。このため一覧には canonical を付けない（WHS-24 の基準「URL の別形を持たない」に当たらない）。各話は帰り道と同じく canonical を付ける
  - 帰り道と共通にできる部品（`lib/wayhome.py`・`style.css` の `.mj-video-*`・`video_wayhome.js`・`wayhome_episodes.js` など）は、複製せず共通化してよい。ただし帰り道の生成物は1バイトも変えない
- YouTube の情報（公開日・再生時間・サムネイルの有無）は帰り道と同じ取り方にする。鍵（`YOUTUBE_API_KEY`）はコンテナに無いので、鍵の要る取得はワークフローを作業ブランチで手動実行して行う（docs/notes/cloud-sessions.md「鍵」、docs/notes/github-actions.md「ワークフローを手動実行するとき」）
- `.github/workflows/` のファイルは、この指示では変えない（Actions の整理〈WFL 系列、#564〉が並行して変えているため）。週次に Mつくの取得を入れるのに YAML の変更が要るなら、要る変更を「判断が必要なこと」に書き、この指示ではスクリプトの側だけを作る

## 手順
1. 調べる: 今の `video_mtsuku` の生成スクリプト・読むタブの見出しと行数・SNS ブックで写真を引ける作る人（名前の一覧）、帰り道の生成スクリプト・`lib/wayhome.py`・`fetch_youtube_meta.py`・`regenerate-page.yml` の帰り道の手順（どの条件で何を書くか。作業ブランチで手動実行したときに常設 issue「帰り道の新しい回」へコメントが出るか）を読み、ログの `## 経過` に書く。issue を起票し（作る issue。題は「Mつくを帰り道と同じ形に作り直す（/mtsuku/）」程度）、着手中のコメントを書く
2. 作る: `/mtsuku/` の一覧と各話を、上の決定・前提で生成する（`regenerate.py` のページ名・`OUTPUT_OVERRIDES`・`_redirects` の `/mtsuku`・`/mtsuku/` の行・`.assetsignore`・`assets-check.yml` の許可リスト〈#331〉の要否は new-page-checklist.md の「作る段の確かめ」で決める。`assets-check.yml` の許可リストの変更が要るときも上の「workflows を変えない」により、要る変更を報告に書いて止まらず進める）。YouTube の情報は、作業ブランチでの手動実行が帰り道の常設 issue や本番のデータを書き換えない見込みのときだけ実行して取る。書き換える見込み、または取れないときは、シートの公開日・`img.youtube.com` のサムネイルで仮置きして生成し（再生時間は出さない）、そう書く。テスト（帰り道の `scripts/tests/` に倣う）を足し、帰り道の生成物の差分が0であることを `regenerate.py video_wayhome wayhome_episodes` で確かめる
3. 確かめと文書: 375px・PC 幅・1920×1080 で、一覧・最新話・最古の回・チーム名が空の回・写真のある作る人の回・写真の無い作る人の回を headless Chrome で見て、スクリーンショットの所見をログに書く。プレビュー（確認用 URL）を出す。段1のとおり公開の issue を起票し（本文に段2のチェックリスト、旧 `video_mtsuku.html` の 301〈`?name=` を引き継ぐか確かめる〉と navbar の差し替え、「作る issue を待つ」）、作る issue に番号をコメントする。`docs/notes/` に Mつくの実装記録（新しい文書なら handover.md の6章の表にも1行）、`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」、平野さんの決定を `docs/decisions/`（分野は README に従う）に書く

## 止まる条件
- 帰り道の生成物（`wayhome/` 以下・title/ の期のページの帰り道の節）に差分が出て、共通化の直しで消せない
- 手動実行が本番（cloudflare）や常設 issue に書き込みそうで、書き込まない形が見つからない（実行せずに仮置きで進めるのは止まる条件ではない。書き込んでしまったら止まる）
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数（`lib/wayhome.py`・帰り道の生成スクリプト・`style.css` の `.mj-video-*`・`regenerate.py` の `OUTPUT_OVERRIDES`・`_redirects`）を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- 「Mつく」タブの行数が本番の今のページの本数（61、要確認）と食い違い、読み直すたびに変わる
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、仮置きしたことの一覧（何を・どう仮置きしたか・ほかの案）と、週次の取得に要るワークフローの変更（あれば）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-MTK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-MTK-01 を書く

平野さんは不在のため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子 MTK: 全ブランチのコミット・`docs/logs/` の履歴に使用なし。同じ Chat-Ref のコミットなし
- work/MTK はローカル・リモートとも無し → `git checkout -b work/MTK origin/cloudflare`（67ac863）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてあり
- 0.「指示」欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致

### 手順1 調べたこと

- 作る issue を起票: #569（同じ主題の issue は「Mつく」「mtsuku」「video_mtsuku」で検索して無し。未マージの work/ にも同じ目的のブランチ無し）。着手中のコメント済み
- 今の生成 `scripts/generate_video_mtsuku.py`: ブック `1y8xBxGpIt-C23cwG7MDjDkebMlpnBufa4_IzYAo2QyQ`（live/title/ のブック `lib/live.SPREADSHEET_ID` とは別）の「Mつく」タブを列記号で読む（`SELECT A..N WHERE O = "Y"`）。型Aの表、`?name=` は3列目（選手）の絞り込みの初期値
- 「Mつく」タブの見出し（2026-10-14 に読んだ）: 名前・団体・公開日・URL・画像URL・チーム名・名前1・団体1・名前2・団体2・名前3・団体3・名前4・団体4・表示・備考
  - 61行、すべて 表示=Y。本番相当の `video_mtsuku.html` の行も61（`<tr data-info` の数）。食い違い無し
  - 公開日は 2019-12-03〜2025-07-22、同じ日付は無し。URL はすべて `watch?v=` の形で動画IDが取れ、重複無し
  - チーム名が空: 5行（滝沢和典 2024-08-10・沢崎誠 2020-04-29・滝沢和典 2020-02-04・多井隆晴 2020-01-02・園田賢 2019-12-03）
  - 名前4が空（3人の回）: 4行。作る人の団体が空: 2行（片山まさゆき・馬場裕一）。団体の値は 連盟・最高位戦・協会・連合・RMU（選手側に「RMU?」が1つ）
  - 「紹介文（補正用）」列は無い。備考はすべて空
  - 作る人は50名。2回の人が11名（和泉由希子・白鳥翔・滝沢和典・小林剛・瀬戸熊直樹・魚谷侑未・松ヶ瀨隆弥・東城りお・佐々木寿人・仲林圭・松本吉弘）
- SNS ブック（`lib/sns.fetch_pros()`）で写真を引ける作る人（団体=連盟の作る人を引いた。X ID あり・写真 URL あり）:
  二階堂亜樹・二階堂瑠美・伊達朱里紗・佐々木寿人・内川幸太郎・勝又健志・古橋崇志・和久津晶・和泉由希子・堀慎吾・宮内こずえ・手塚紗掬・日吉辰哉・本田朋広・東城りお・沢崎誠・滝沢和典・瀬戸熊直樹・猿川真寿・白鳥翔・菅原千瑛・藤島健二郎・藤崎智・高宮まり・魚谷侑未・黒木真生・黒沢咲（27名）。
  荒正義は X ID が空で名前だけ。連盟以外の作る人は「プロ」に居ないため名前だけ
- 帰り道: `lib/wayhome.py`（シートの読み・`data/youtube_meta.json` との突き合わせ・サムネイル・再生時間・X の写真・VideoObject）、
  `generate_video_wayhome.py`（一覧）・`generate_wayhome_episodes.py`（各話・`sitemap-wayhome.xml`）、JS は `video_wayhome.js`（一覧。`?name=` は扱わない）・`wayhome_episodes.js`（各話）
- `fetch_youtube_meta.py`: 帰り道タブの動画IDを videos.list で引き、`data/youtube_meta.json`（publishedAt・thumbnails・duration）に書く。返らなかった回は前回の値を残す
- `regenerate-page.yml` の帰り道の手順: 「帰り道のYouTube情報を取得」「一覧のOGP画像を作る」は `target_page` が all（週次・手動の all）で、`workflow_ref` がこのファイルのときだけ動く。
  そのとき「前回の知らせを読む」→ `check_wayhome.py` → 状態が前回の知らせと変われば常設 issue「帰り道の新しい回」へコメントする（`notify`）。**作業ブランチで all を手動実行しても `workflow_ref` は `/regenerate-page.yml@refs/heads/work/MTK` で条件に当たり、知らせが変わっていれば常設 issue に書く。**
  さらに all の再生成で全ページの生成物が作業ブランチにコミットされる。Mつくの取得の手順は YAML に無く、workflows を変えない指示のため足せない
  → **手動実行はしない**。YouTube の情報は仮置き（下の手順2）
- 未マージの work/（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-hou）と、変えるファイルの重なり: work/1014-hou が `_redirects` の `/houou/` の行の後に1行足している（`/houou/ranking/`）。
  Mつくの行は `/video_wayhome.html` の行の後に足すため、同じファイルの別の箇所（間に変えない行が3行ある）。止まる条件に当たらない。`lib/wayhome.py`・帰り道の生成・`style.css`・`regenerate.py` を変える未マージのブランチは無し
- テストの基準: 着手時に `python3 -m unittest discover -s scripts/tests` は 1092件 OK

### 手順2 作ったもの（490b7ce）

- `scripts/lib/mtsuku.py`（新規）: 「Mつく」タブを見出しの名前で読む（`sheets.fetch_table()`。必須は 名前・団体・URL・チーム名・名前1〜4・団体1〜4・表示。「紹介文（補正用）」は任意の列で、あれば上書き）。
  紹介文の自動の文・ページの名前・VideoObject（再生時間が無い回は `duration` を付けない）・NOINDEX_TAG。サムネイル・再生時間・Xの写真は `lib/wayhome.py` の関数をそのまま使う
- `scripts/generate_mtsuku.py`（一覧、ページ名 `mtsuku` → `mtsuku/index.html`）・`scripts/generate_mtsuku_episodes.py`（各話、`mtsuku_episodes` → `mtsuku/`）。`regenerate.py` の `OUTPUT_OVERRIDES` に2行
- 共通化（帰り道の生成物は変えない）: 固定の検索バーを `lib/wayhome.build_filterbar_html(placeholder, aria_label, param=None)` に、戻る矢印を `lib/wayhome.BACK_ICON_SVG` に移し、帰り道の2本の生成スクリプトはそれを呼ぶ形にした。
  `build_card_thumb_html()` は再生時間が無い回だけ重ねない。`video_wayhome.js` は検索欄に `data-param` があるときだけその URL パラメータを初期値にする（帰り道の検索欄には付けない）
- `scripts/fetch_youtube_meta.py --series mtsuku`: 「Mつく」タブの動画を `data/mtsuku_youtube_meta.json` に書く（引数なしは今までどおり帰り道 → `data/youtube_meta.json`）
- YouTube の情報は**仮置き**: 手順1のとおり手動実行はしていない。`data/mtsuku_youtube_meta.json` を、公開日=シートの「公開日」の 00:00 JST、サムネイル=`img.youtube.com` の maxresdefault・sddefault・hqdefault・mqdefault への HEAD（61本すべて4つとも 200）、再生時間=なし、で作った（JSON の先頭の `provisional` に書いた。作ったスクリプトはセッションの scratchpad で、リポジトリには入れていない）。
  再生時間が無いので、カード・ヒーローの再生時間、一覧の総再生時間、`VideoObject.duration` は出ない。API の取得が入れば上書きされ、出るようになる
- `_redirects`: `/mtsuku  /mtsuku/  301`・`/mtsuku/  /mtsuku/index.html  200`（`/video_wayhome.html` の行の後）。`docs/notes/redirects.md` の末尾スラッシュの行に `/mtsuku` を足した
- `.assetsignore`: 変更なし（`data`・`scripts` は既に除外）。`assets-check.yml` の許可リストに `mtsuku` が要る（workflows は変えていない。作業ブランチの check は「許可リストに無い最上位の項目: mtsuku」だけで失敗した〈job 115766233407〉）
- テスト `scripts/tests/test_mtsuku.py`（18件）。全体 1110件 OK
- **帰り道の生成物の差分0**: 変更前に `regenerate.py video_wayhome wayhome_episodes` で差分0を確かめ（基準）、変更後に `regenerate.py video_wayhome wayhome_episodes title_pages` で `wayhome/`・`title/`・`sitemap*.xml` の差分0
- 生成のログ: 各話の生成で「jpml_prosに該当する選手がいないため、Xの写真を出しません: 前原雄大」の警告（連盟の作る人で「プロ」に居ない。名前だけで出る）

### 手順3 確かめ

headless Chromium（ローカルの `python3 -m http.server`、`mtsuku/index.html` を直接開いた）で 375×812・1366×900・1920×1080。
見たページ: 一覧・一覧 `?name=魚谷`・最新話 ZeFtFG2ov_Q（和泉由希子、写真あり）・最古の回 nLeGJz-nt-E（園田賢、チーム名が空・写真なし〈最高位戦〉）・チーム名が空で写真のある回 vSduV7-kLls（滝沢和典）・写真の無い作る人の回 6vXHmiBj--c（竹内元太）。

- 全ページ・全幅で JS のエラーなし、横スクロールなし、読めない画像なし
- 一覧: ヒーローは作る人の名前＋Xの写真、「最新話」・公開日・「チーム名」、その下に「選んだ選手：…」、▶ 再生。固定の検索バー、件数は出ない。`?name=魚谷` で検索欄に「魚谷」が入り4件（作る人2回＋選ばれた回）。存在しない名前で「該当する動画がありません」。帰り道の一覧に `?name=堀` を付けても検索欄は空のまま（今までどおり）
- 各話: 左上の「一覧に戻る」、シリーズ名のリンク、見出し「「チーム名」 作る人」＋写真、選んだ選手、紹介文、▶ 再生。前後のエピソードは最新話が右の「前」だけ、最古が左の「次」だけ、中間は2つ（公開日つき）。「〇〇の他のエピソード」は作る人で出る（和泉由希子・滝沢和典）
- 気づいたこと: PC 幅で各話の見出しが「「どこでも行けます。」 和 / 泉由希子」のように名前の途中で折り返す（帰り道の「期の題 選手名」も同じ組み方で、同じことが起きうる）。直していない（判断が必要なことに書いた）
- チーム名が空の回は、カードのチーム名の行を出さない（日付と名前だけ）。同じ行のカードと高さはそろう

### プレビュー

- 490b7ce の「Workers Builds: mj」は success（08:56 UTC）。プレビューで `/mtsuku` → 301 `/mtsuku/`、`/mtsuku/`・`/mtsuku/index.html`・最新話・最古の回が 200 で noindex あり。`/mtsuku/` は手元の `mtsuku/index.html` と同じバイト、`video_wayhome.js` も同じ。`/wayhome/`・`/video_mtsuku.html` は 200（noindex なし、今までどおり）
- headless Chromium でプレビューの `/mtsuku/?name=魚谷`: 検索欄に「魚谷」、4件、背景は濃色、JS のエラーなし。各話の「一覧に戻る」で `/mtsuku/` へ
- 決定を `docs/decisions/mtsuku.md`（新しい分野、README の一覧に1行）に書いた

## 報告

- 状態: 判断待ち / 続き: CHAT-1015-MTK-04
- ブランチ: work/MTK（指示文の指定どおり。セッションに割り当てられた claude/… は使っていない）
- ログ: https://github.com/retroeater/mj/blob/work/MTK/docs/logs/CHAT-1014-MTK-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/MTK
- 確認用URL: プレビューあり（URL は最終報告。Workers Builds: mj が 490b7ce で success）。この版で見える印: /mtsuku/ のヒーローの見出しが「和泉由希子」で、その下に「選んだ選手：魚谷侑未（連盟）・…」
- マージ: 未（平野さんの判断待ち。マージの行のとおり）
- issue: #569（作る issue、起票・着手中）、#570（公開の issue、起票。段2のチェックリスト・旧 video_mtsuku.html の 301〈?name= の引き継ぎ〉・navbar の差し替え・#569 を待つ）
- 判断が必要なこと:
  - プレビューで仕様を確かめ、マージするかを決める（マージ: 判断待ち）
  - 仮置き1 シリーズ名・ページ名: 一覧の見出しは「Mリーグチームを作ろう！」、`<title>` は「Mリーグチームを作ろう！ | Mつく | ryoei.pro」、og:title の短い名前は「Mつく」（帰り道の「帰り道ついていってイイっすか | 帰り道」と同じ形）。ほかの案: 旧ページの「日本プロ麻雀連盟 Mつく動画」を残す
  - 仮置き2 各話の見出し: 「「チーム名」 作る人」（例「「どこでも行けます。」 和泉由希子」）。チーム名が空の5回は作る人だけ。`<title>` は「<見出し> | Mリーグチームを作ろう！ | ryoei.pro」。ほかの案: 「作る人のチーム「チーム名」」、作る人だけを見出しにしてチーム名は日付の行に置く。PC 幅で名前の途中で折り返す（帰り道も同じ組み方）。名前を折り返さない CSS を足す案もある（帰り道にも効く）
  - 仮置き3 選ばれた選手の出し方: ヒーローの日付の行の下に「選んだ選手：名前（団体）・…」（一覧のヒーローと各話。帰り道の `.mj-video-hero-meta` の書式を流用し CSS は足していない）。カードには出さない。ほかの案: 「メンバー」などの見出し、1人1行
  - 仮置き4 紹介文（各話のヒーロー・meta description・VideoObject.description）: 「YouTubeチャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」。<作る人>（<団体>）が作ったチーム「<チーム名>」の回です。」。チーム名が空なら「…がチームを作った回です。」、団体が空（2回）なら括弧なし。「紹介文（補正用）」列を足せばその回を上書きできる（今のタブには無い。足すかどうか）
  - 仮置き5 一覧のヒーローの説明文: 「YouTubeチャンネル「麻雀遊戯王」の企画。最新話から選手名・チーム名で振り返れます。」。meta description: 「YouTubeチャンネル「麻雀遊戯王」の企画「Mリーグチームを作ろう！」の動画61本を、最新話から選手名・チーム名で検索できます。」
  - 仮置き6 Xの写真: 団体が「連盟」の作る人だけを SNS ブックで引く（帰り道と同じ大きさ・フチ、読めなければ生成を止める）。引けるのは27名。荒正義（X ID が空）・前原雄大（「プロ」に居ない。生成のログに警告）と連盟以外の人は名前だけ。ほかの案: 連盟以外も名前で引いてみる（別団体の同姓同名を誤って引く恐れ）
  - 仮置き7 一覧の検索・カード: 公開日・チーム名・作る人・選ばれた選手・団体で拾う。カードは日付・「チーム名」・作る人（チーム名が空ならその行なし）。検索欄の文言は「選手名・チーム名で検索」。`?name=` を初期値にし、そのため一覧は canonical なし（各話はあり）
  - 仮置き8 YouTube の情報: `data/mtsuku_youtube_meta.json` を仮置きで作った（公開日はシートの日付の 00:00 JST、サムネイルは img.youtube.com の HEAD、**再生時間なし** → 再生時間・総再生時間は出ていない）。作業ブランチでの手動実行は帰り道の常設 issue に書きうるため実行していない
  - 仮置き9 JS: 一覧は帰り道の `video_wayhome.js`、各話は `wayhome_episodes.js` をそのまま読む（ファイル名は帰り道のまま。名前を共通のものへ変えるかは別に決める）
  - 週次の取得に要るワークフローの変更（この指示では変えていない）: `regenerate-page.yml` に、「帰り道のYouTube情報を取得」と同じ条件で `python3 scripts/fetch_youtube_meta.py --series mtsuku` の手順（continue-on-error、失敗の報告）を足し、「変更をコミット・push」の `git add` に `data/mtsuku_youtube_meta.json` を足す。これが入るまで、シートに足した回は JSON に無いため外して生成される（警告だけ）
  - `assets-check.yml` の許可リスト（`allowed()` の最上位のディレクトリの並び）に `mtsuku` を足す（この指示では変えていない。作業ブランチの check はこれだけで失敗している。cloudflare に入れる前に要る）
  - 公開の段（#570）で決めること: サイトマップ（`sitemap-mtsuku.xml` を作るか）、旧 `video_mtsuku.html` の 301 と旧生成スクリプトの扱い
- 未確認の項目:
  - プレビューの見え方は、headless Chromium で一覧（375px、`?name=魚谷` で4件・濃色）と各話の「一覧に戻る」で /mtsuku/ に戻ることまで。ほかの幅・ページはローカルで確かめた（手順3）。平野さんの実機は未
  - YouTube Data API の値（実際の公開時刻・再生時間・サムネイルの有無）での生成（ワークフローの変更の後）
  - `_redirects` の `/mtsuku` → `/mtsuku/` の 301・`/mtsuku/` の 200 はプレビューで確かめた。本番は未（未マージ）
- エラー: なし（作業ブランチの「公開対象を検査する」の失敗は、上の許可リストの未変更によるもので想定どおり）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cca707c1）: https://github.com/retroeater/mj-logs/tree/main/guide/cca707c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cca707c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8c23572b.md
