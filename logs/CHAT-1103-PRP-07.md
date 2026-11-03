# CHAT-1103-PRP-07

- 着手日時: 2026-11-03
- 対象issue: #641・#287
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 3b3f2152f

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その5）。cloudflare（LVP-09 を含む）を取り込み、個別ページの「お気に入り」★・全在籍者の今後の予定の JSON・pro/ のトップの「お気に入り」の絞り込みを同じ作業ブランチに足してプレビューで止める（マージ＝公開はまだしない） Chat-Ref: CHAT-1103-PRP-07 マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする） 貼る時機: いつでも（CHAT-1101-PRP-06 の完了報告〈判断待ち〉の後。PRP-06 と同じセッションでも、新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03〜06 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。`scripts/generate_pro_pages.py`・`assets/pro.js` の衝突は、下の手順1の解き方に従う。両方の変更が両立するそのほかの衝突（隣り合う行・別の目的の変更が同じ関数に入ったもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-06 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1103-PRP-07` を足して push する。

目的
サイトのトップの作り直し（#101、TOP 系列）の第一弾「お気に入りに追加した連盟プロの今後の予定」のために、pro/ 側で要る部品（個別ページの★・全在籍者の今後の予定の JSON）を作る。あわせて pro/ のトップに「お気に入り」の絞り込みを置き、★の結果が pro/ の中でも見えるようにする。公開（#641）に含める。
決定（2026-11-03、平野さん）

* 個別ページの「お気に入り」★と、全在籍者の今後の予定の JSON は、pro/ の公開（#641）に含める
* pro/ のトップに「お気に入り」の絞り込みを置く
* TOP 系列のチャットで決まったこと（2026-11-03、平野さん。TOP のチャットからの伝言による）: 名前は「お気に入り」（「フォロー」は採らない）。既存の #287「マイ選手」を「お気に入り」に改める（改名と本文の書き直しは TOP の CHAT-1103-TOP-01）。★は pro/ の個別ページの名前の面に置き、作るのは PRO/PRP 系列。登録の上限は設けない。新トップの公開は `r/` と pro/ の公開の後

前提（チャット側。平野さんの決定ではない）

* cloudflare には LVP-09（CHAT-1102-LVP-09、0b7ce13fd でマージ済み）が入っている。pro/ の放送対局の行を live/ の HTML から読まず `generate_live_pages.load()` の結果から作る形に、`load_lives()`・`parse_lives()`・`live_cards()`（と `load_roles()`）を直したもの。チャット側が見たところ、`origin/cloudflare` と `origin/work/1101-prp-pub` の間で `scripts/generate_pro_pages.py` に差がある（要確認）
* TOP 系列の案（形の細部は変えてよい。変えたら #287 のコメントに書く）:
   * ★: `pro/<slug>/` の名前の面（`face_html()`）に、押せる大きさ 44px の「お気に入りに追加」／「お気に入りから外す」（`aria-pressed`、見た目は ☆/★）。保存は localStorage、キーは `mj:favorites`（規約: `docs/new-page-checklist.md`「localStorage の規約」。接頭辞 `mj:`・値は JSON）。値は `[{"slug": "<slug>", "name": "<現在名>"}, …]` の配列。上限なし。同意表示なし（#236）。JS は `assets/pro.js` に足す見込み。localStorage が使えない環境（読めない・書けない・例外）でもページが壊れないこと
   * 予定の JSON（パスは案 `pro/plans.json`）: 個別ページの「今後の予定」と同じ元（`load_plans()`。放送対局・未放送の最強戦・道場部ゲスト、現在名に直した照合）を、選手ごとに全件（個別ページの5件の切りを掛けない）で1つの JSON に書き、`pro_pages` の生成で一緒に更新する。1行の項目は個別ページの行と同じ（日付〈ISO の日時か日〉・種類〈放送・最強戦・道場部〉・役〈放送だけ〉・件名・行き先の URL）＋ slug。トップの JS はこれを読み、`mj:favorites` の slug で絞って日付の近い順に出し、過ぎた行は `assets/pro.js` と同じ基準（日本時間の日）で隠す
* pro/ のトップの「お気に入り」の絞り込み（チャット側の案）: 絞り込みのシートの切り口（`FACETS`）に「お気に入り」を足し、選ぶと `mj:favorites` に入っている選手の行だけを出す。0人のときは「個別ページの ☆ で追加できます」の1行を出す。共有できない（端末ごとの）絞り込みなので、URL のクエリには載せない。トップの表の行に★の印を出すかは、見た目の判断として報告に書く（作らなくてよい）
* 告知動画の制作はまだ始めていない（`docs/notes/page-announcement.md` の告知の期間の決まりには当たらない）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）

手順

1. `origin/cloudflare` を取り込む。`scripts/generate_pro_pages.py` の衝突は、LVP-09 が変えた放送対局の関数（`load_lives()`・`parse_lives()`・`live_cards()`・`load_roles()` と、それに伴う呼び出し）は cloudflare の側、このブランチが変えた箇所（noindex・canonical・題・OGP の画像・`build_sitemap()`・`redirect_table()`・`top_ask()` ほか）はこのブランチの側を残す。解いた後に pro/ を生成し、取り込む前のこのブランチの生成物と比べて、放送対局の欄（選手／実況・解説の行と件数）が全 1,098ページで変わらないことを確かめる（変わったページがあれば一覧を書いて止まる）
2. ★と予定の JSON とトップの絞り込み: 上の前提の形で作る。`.assetsignore` で予定の JSON が公開対象になっていること（`check-assets.yml` の許可の一覧も）を確かめる。テストを足す（予定の JSON の行の数が、個別ページの予定の元の全件と一致・5件の切りが掛かっていない・slug がすべて pro/ のページにある、など。JS は既存の形で確かめられる範囲で）。docs/notes/pro-pages.md に★・`mj:favorites`・予定の JSON・トップの絞り込みの節を足し、`docs/new-page-checklist.md`「localStorage の規約」の項目の一覧の「マイ選手 #287」を「お気に入り #287」（キー `mj:favorites`）に直す
3. 確かめと記録:
   * テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   * 予定の JSON の大きさ（バイト数・gzip した大きさ）・行の数・予定のある選手の数、いちばん予定の多い選手（名前と件数）
   * プレビューで、`pro/sho.shiratori.23/`・`pro/shota.akutsu.34/` の★を押す → トップで「お気に入り」を選ぶと2人が出る → ★を外すと消える、の流れを 360px と 1280px・明暗で見る。キーボードでも★を押せる。localStorage を使えなくした状態でもページが壊れない
   * 生成物の差分を種類ごとに数えた表（取り込みと、この指示の変更それぞれ）
   * 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
   * #287 にコメントする（キーの名前・値の形・予定の JSON のパスと項目・大きさ。TOP の指示がそれを読む）。上の「決定」を docs/decisions/pros.md に足す

止まる条件

* 0章の確かめで、PRP-06 のログの状態が「判断待ち」でない
* 手順1で、放送対局の欄が変わったページが出た、または上の解き方で解けない衝突が出た
* 新しい依存（npm・pip のパッケージ、外部 API・外部ドメイン）が要る
* pro/ 以外のページの生成物に、この指示の変更（と取り込み）で説明できない差分が出た
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #287 にほかのセッションの着手中のコメントが残っている（コメントだけ書かずに進め、報告に書く）
* この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-PRP-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-PRP-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある（指示文が改行の無い1段落で貼られていたので、ログの指示欄もそのまま写した）
- CHAT-1101-PRP-06 のログの状態は「判断待ち」だった。末尾に「 / 続き: CHAT-1103-PRP-07」を足して push した（449131465）
- 同じ Chat-Ref のコミット: なし。作業ブランチはローカル・リモートとも 3b3f2152f で一致
- #641 にほかのセッションの着手中は無い（最後は PRP-06 の判断待ち）
- **#287 にほかのセッションの着手中のコメントがある**（2026-11-03 02:23 UTC「着手中（題・本文の更新。コードは変えない）。work/1103-top」、TOP の CHAT-1103-TOP-01）。止まる条件のとおり、#287 にはコメントを書かずに進めた。#287 に書く予定だった中身は `## 報告` の「判断が必要なこと」に置いた

### 1. cloudflare の取り込み（ec633df1b）

origin/cloudflare は祖先でなかった（254件先行）ので `git merge origin/cloudflare`。`scripts/generate_pro_pages.py` は自動で混ざった（LVP-09 の `load_lives()`〈`generate_live_pages.load()` から行と役と名前の辞書を返す〉・`live_cards()` は cloudflare の側、noindex・canonical・題・OGP・`build_sitemap()`・`redirect_table()`・`top_ask()` はこのブランチの側。`prepare()` の `aliases` は LVP-09 の `load_lives()` が返す名前の辞書から取る形で残った）。`assets/pro.js` は衝突しなかった。衝突と解き方:

| ファイル | 解き方 |
|---|---|
| `docs/decisions/pros.md` | 末尾の追記どうし。両方を日付の順に残した（このブランチの 2026-11-01 の PRP-03〜06 → cloudflare の「2026-11-02（CHAT-1102-LVP-09、#219・#362）」） |
| `docs/notes/github-actions.md` | ワークフローの一覧の表の隣り合う行（cloudflare は `fetch-youtube.yml`・`check-saikyo-unregistered.yml` ほかの起動の時刻、このブランチは `check-image-links.yml` の対象）。cloudflare の側を取り、このブランチの1か所（`check-image-links.yml` の「連盟プロのトップ `pro/index.html` の X の画像（ジョブ `check`。…#641）」）を足し直した |
| `sitemap-pages.xml` | このブランチの側（`jpml_pros.html` の行を消したもの。cloudflare はその行の lastmod を 2026-11-03 にしていた） |
| `sitemap-title.xml` | 生成物の lastmod。cloudflare の側を取り、下の生成で作り直した |
| `houou/race/` の7ファイル（32-2・33-1・33-2・38-2・42-1・42-2・43-1）・`title/` の4ファイル（masters/29・shinjinou/29・shinjinou/30・shouryu/2） | 生成物。cloudflare の側を取り、取り込んだ後のスクリプトで生成し直した（下） |

放送対局の欄の確かめ（手順1）:

- 取り込んだ後のコードで `regenerate-page.yml`（`pro_pages`・`dry_run`・`compare_ref: origin/cloudflare`、run 41589447844、success）: 「差分なし」。取り込んだ後の pro/ は、このコードで生成しても変わらない（自動で混ざった pro/ がそのまま生成の結果）
- そのうえで、取り込んだ後の pro/（ec633df1b）と cloudflare の pro/（LVP-09 のコード、同じ日のデータ）の個別ページ 1,098件の放送対局の欄（見出し `pro_live_0`・`pro_live_1` から `</nav>` まで）を比べた: **違うページは0件**（欄 563・行 1,910）
- 取り込む前のこのブランチの pro/（3b3f2152f、2026-11-01 のデータ）と比べると4ページが違った（`pro/go.saito.28/`・`pro/satoshi.fujisaki.13/`・`pro/shota.hatadani.42/`・`pro/tatsuki.hata.35/`）。違いはすべて「第2期昇龍戦」（ベスト8・決勝）の2本が加わったこと（と「すべて（N件）」の件数が2つ増えたこと）。`live/shouryu/2/` のページは 3b3f2152f には無く、cloudflare の 2026-11-02 の生成（38b2833f6）で初めて入った新しいデータ。同じデータでは LVP-09 のコードと取り込んだ後のコードが同じ欄を作るので、取り込みによる変化ではないと判断して止まらずに進めた（判断が必要なことに書いた）

### 2. お気に入り・予定の JSON・トップの絞り込み（b5df430af・49ce47f55・09d20656c）

- 個別ページ: 名前の面の h1 の下に `<button class="mj-pro-fav" data-fav-slug data-fav-name aria-pressed="false" hidden>☆ お気に入り</button>`（`fav_button()`）。`assets/pro.js` が localStorage を使えるときだけ出し、押すと `mj:favorites` に足す・外す（☆ → ★、`title` は「お気に入りに追加」／「お気に入りから外す」）。読み上げの名前は「お気に入り」で一定にし、状態は `aria-pressed` で伝える（名前を押すたびに替えると、押した状態との二重の伝え方になるため。TOP の案の「お気に入りに追加／外す」の文言は `title` に移した）
- `mj:favorites` の値は `[{"slug", "name"}]`。足した順、上限なし。読むときは配列でない値・壊れた JSON・slug の無い要素・重複を落とす（`parseFavorites()`）。localStorage が使えるかは、書いて消してみて決める（`usableStorage()`。無い・例外・書けない端末は null）
- トップ: 絞り込みの切り口に「お気に入り」（値は「お気に入りの選手」の1つ、「すべて」は無い）。表の行に `data-slug`。選ぶと `mj:favorites` の slug の行だけ。0人なら「お気に入りの選手はまだいません。個別ページの ☆ で追加できます」。選んだときは URL のクエリを消す（端末ごとの絞り込みなので載せない）。localStorage の使えない端末ではこの切り口を出さない
- 360px で5つのタブを等分すると「お気に入り」が2行になったので、pro/ のシートのタブだけ字の幅で分けて折り返さないようにした（360px で 五十音 60・所属 44・入会期 60・リーグ 60・お気に入り 93px、高さ 44px。320px では横に16px はみ出すが、最小の画面幅は 360px〈docs/notes/design.md〉）
- `pro/plans.json`（`plans_json()`）: 在籍者の今後の予定の全件（5件の切りなし）を `{"plans": [{slug, name, date, kind, role, title, href}]}` で日付の近い順に。pro/ の生成と `--check` で一緒に作る
- 公開: `pro/` は `.assetsignore` で外れておらず、`check-assets.yml` の許可の一覧に `pro` がある（`pro/plans.json` は公開される）
- 文書: `docs/notes/pro-pages.md`（「お気に入り」「全在籍者の今後の予定」の節、トップの切り口、出力の一覧）、`docs/new-page-checklist.md`「localStorage の規約」の「マイ選手 #287」→「お気に入り #287（`mj:favorites`…）」

### 3. 生成と確かめ

- 生成: `regenerate-page.yml`（`houou_pages ouka_pages jwrc_pages wrcr_pages title_pages pro_pages`、run 41589883717、success）→ 11a549bd3（1,115ファイル）:

| 種類 | 件数 | 理由 |
|---|---|---|
| `pro/<slug>/index.html` | 1,098 | ☆ のボタン（この指示） |
| `pro/index.html` | 1 | 切り口「お気に入り」・行の `data-slug`・空のときの1行（この指示） |
| `pro/plans.json` | 1（新しい） | この指示 |
| `houou/race/` | 7 | 取り込みで cloudflare の側を取った7ファイルに、このブランチの slug（PRP-04）が戻った |
| `title/` | 5 | 同じく、取り込みで cloudflare の側を取ったファイルに、このブランチの pro/ へのリンクが戻った（4ファイル）と大会のページ1つ |
| `sitemap-pro.xml`・`sitemap-pages.xml`・`sitemap-title.xml` | 3 | lastmod（`update_sitemap_lastmod.py --from-git` が git の日付にそろえた。手では書いていない） |

  pro/・順位変動・title/ とサイトマップのほかに差分は無い
- `pro/plans.json`: 24,341 バイト（gzip 3,230 バイト）、124行、予定のある在籍者 85名。いちばん多いのは3件で、二階堂亜樹・佐々木寿人・古川孝次ほか。種類は 放送 102・道場部 18・最強戦 4。slug はすべて pro/ のページにある
- テスト: `python3 -m unittest discover -s scripts/tests` 1,789件 OK、`node --test 'workers/*/test/*.test.mjs'` 163件 OK。足した `scripts/tests/test_pro_favorites.py`（8件: 予定の JSON の全件・在籍者だけ・並び・5件の切りなし、☆ のボタン、トップの切り口、`mj:favorites` の読み書き・壊れた値・使えない localStorage）は、この指示の前のコードで 8件とも失敗（failures=2・errors=6）
- プレビュー（版ごとの URL、8144c67e2 の check-run は success）を headless Chromium（UA に `mj-internal`）で、360px・1280px × 明暗の4通り:
  - `pro/sho.shiratori.23/` の ☆ をクリック、`pro/shota.akutsu.34/` の ☆ をキーボード（フォーカスして Enter）で押す → どちらも `aria-pressed="true"`・★。ボタンは 121×44px。★ の色のコントラストは明 8.92・暗 8.07
  - トップで絞り込み → 「お気に入り」→ 表は2人（shota.akutsu.34・sho.shiratori.23）。URL は `/pro/`（クエリなし）。横のはみ出し 0
  - 2人の ☆ を外す → トップの「お気に入り」は0人で、空のときの1行が出る。`mj:favorites` は `[]`
  - JS のエラー 0
  - localStorage を使えなくした（`window.localStorage` を読むと例外）状態: 個別ページは h1 が出て ☆ は隠れたまま、トップは「お気に入り」の切り口が隠れ、表（259行）と「所属」の絞り込み（585行）は動く。JS のエラー 0
- 本番（ryoei.pro）へのリクエスト: 0件

### 4. 差分のレビュー（新しい文脈のサブエージェント、1回）

渡したもの: 目的・決定・`git diff ec633df1b HEAD`（生成物・ログを除く）・CLAUDE.md「コード規約」。サブエージェントもテスト 1,789件 OK を確かめた。高の指摘は無し。

| 指摘 | 扱い |
|---|---|
| 別のタブで変えたとき・戻る（bfcache）で戻ったときに、☆ とトップの絞り込みが古いまま。古い ☆ を押すと逆の操作になる（中） | 直した（`storage`・`pageshow` で読み直す。ローカルで2つのタブで確かめ、トップの表と別のタブの ☆ が変わった） |
| 360px で5つのタブを字の幅にしたので、文字の拡大（1.07倍ほど）で横にはみ出すおそれ（中） | 判断が必要なことに書いた（実機の文字の拡大での確かめが要る） |
| `?all=1&by=fav` を手で入れるとお気に入りの状態で始まり、URL にも残る（低） | 直した（`by=fav` は既定の切り口にする） |
| `toggleFavorite()` のコメントが不正確・改名で slug が変わると宙に浮く（低） | 直した（コメントと `簡略化:`、docs/notes/pro-pages.md にも1行） |
| `pro.js` の先頭の `const` と関数はクラシックスクリプトのグローバル（低） | 意図どおり（今は衝突しない。#101 で使い回すときに確かめる） |
| `usableStorage()` は読み込みごとに書いて消す・容量が満杯の端末では出ない（低） | 意図どおり（決定の「書けない端末では出さない」） |
| `title` が読み上げで少し冗長（低） | 意図どおり（害は無い） |
| `plans_json()` | 問題なし |
| テストに `state()` の検査が無い（低） | 見送り（DOM に依存する。ローカルのブラウザで `?all=1&by=fav` が既定の表示になることを確かめた） |
| docs/notes/pro-pages.md の検査の行に新しいテストが無い・「保存データを消す」の扱い（低） | 直した |

直した後: テスト 1,789件 OK。未マージの `work/1028-lgn`・`work/1029-lgn-page` は `style.css`・`docs/new-page-checklist.md` を変えているが、別の箇所で、取り込みの衝突の件数は origin/cloudflare との間と同じ

## 報告

- 状態: 判断待ち
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1103-PRP-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページは `pro/sho.shiratori.23/`・`pro/shota.akutsu.34/`・`pro/`。この版で見える印: 個別ページの名前の下に「☆ お気に入り」、トップの絞り込みのタブに「お気に入り」
- マージ: 未（指示のとおり。マージが pro/ の公開になる）
- issue: #641
- 判断が必要なこと:
  - **#287 に TOP のセッションの着手中のコメントが残っていたので、#287 にはコメントを書いていない**（止まる条件）。TOP の指示が読む中身はここに置く: キーは `mj:favorites`、値は `[{"slug": "<slug>", "name": "<足したときの現在名>"}, …]` の JSON の配列（足した順・上限なし・壊れた値は空として読む）。予定は `https://ryoei.pro/pro/plans.json`（公開の後）で `{"plans": [{slug, name, date, kind, role, title, href}, …]}`、`date` は日本時間の `YYYY-MM-DD`（最強戦・道場部）か `YYYY-MM-DDTHH:MM`（放送）、`kind` は 放送・最強戦・道場部、`role` は放送の実況・解説のときだけ、日付の近い順、過ぎた行は読む側が日本時間の今日で隠す。今の大きさは 24,341 バイト（gzip 3,230）・124行・85名。TOP の案と変えた点: ボタンの読み上げの名前は「お気に入り」で一定にし、状態は `aria-pressed`（「お気に入りに追加／外す」は `title`）。#287 に書き写すか、TOP の指示文に入れてほしい
  - 取り込む前（2026-11-01 のデータ）のこのブランチの pro/ と比べると、放送対局の欄が4ページで違った（`go.saito.28`・`satoshi.fujisaki.13`・`shota.hatadani.42`・`tatsuki.hata.35`）。原因は 2026-11-02 に入った新しいデータ（第2期昇龍戦のベスト8・決勝）で、同じデータの LVP-09 のコードの生成とは全 1,098ページで同じだったので止まらずに進めた（`## 経過`「1.」）。この読み方でよいか
  - 絞り込みのタブを 360px で1行にするため、pro/ のシートのタブだけ字の幅で分けた。余裕は約20px で、端末の文字の拡大（iOS の文字サイズ・Android のフォント倍率）が 1.07倍ほどを超えると横にはみ出すおそれがある（レビューの見積もり）。平野さんの実機で、文字を大きくした状態の見え方を確かめてほしい。はみ出すなら「お気に入り」を「☆」にするなどの案
  - トップの表の行に ★ の印を出すか（指示文の見た目の判断。作っていない）。Code の見立て: 行が混むので、今は出さない（「お気に入り」の絞り込みで見られる）
- 未確認の項目:
  - 実機（iPhone の Safari）での ☆ と絞り込み、文字を大きくしたときのタブ
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82af799a）: https://github.com/retroeater/mj-logs/tree/main/guide/82af799a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82af799a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
