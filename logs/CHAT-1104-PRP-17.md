# CHAT-1104-PRP-17

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-polish
- 着手時HEAD: 8c7dd9a31（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：pro/ の中だけで効く直し（ホーム画面の名前・X の既定のアイコン・JS のテスト・「ほか N件」の確かめ）と古い docs を未公開のまま本番に入れ、#641 の本文を今の形に書き直し、ワークフローの安全上の論点を起票する
Chat-Ref: CHAT-1104-PRP-17
マージ: 承認済み（チャットで。2026-11-04 に平野さんが、下の「止まる条件」〈noindex が全ページに残る・生成物の差分が pro/ の中だけ・導線と転送が変わらない・テストが通る〉でマージしてよいと単独の問いで答えた。1つでも当たればマージしない）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-prp-polish を使う（公開の準備の work/1101-prp-pub とは分ける。work/1101-prp-pub はこの指示では変えない）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-polish origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1104-PRP-16 の洗い出し（そのログの `## 経過`「2. 洗い出しの表」。番号はその表）のうち、未公開のまま本番に入れられるもの（区分①）を入れる。あわせて #641 の本文を今の形に直し、pro/ と独立した安全上の論点を起票する。

### 決定（2026-11-04、平野さん）
- 区分①のうち B1・B2・B15・B16 を公開の前に入れる。B13（同じ年の獲得タイトルの並び、#408 が先）・B22（最終更新日・theme-color、#239・#271）は公開の後
- docs の古い記述（A8・A9）は直す（チャット側の見立てで、平野さんに聞かずに進めると伝えたもの）
- #641 の本文を、この指示で今の形に書き直す（済んだ項目にチェック・要らなくなった項目を消す・公開の日の手順を `docs/notes/pro-pages.md`「公開のマージ」に合わせる）
- B18（`regenerate-page.yml` の push の job が書き込みの資格情報を持ったまま `pip install` する）は issue を起票し、セキュリティ点検（SEC）の系列に任せる
- 「鳳凰」シートの第38期後期の3名の空白（A13）は、平野さんが 2026-11-02 に直した（申告）。公開のマージの生成で、3名が個別ページへ向くことを確かめる
- PRP-16 の Q1（2026-11-04）: live/ の選手のカード（A5）・成績ページから個別ページへの戻りのリンク（C10、#644）・帰り道の選手のリンク（C17、#195）は、3つとも公開のブランチに入れる（この指示ではしない。CHAT-1104-PRP-19 で行う）

### 前提（チャット側。平野さんの決定ではない）
- B1: `apple-mobile-web-app-title="連盟プロ"` は pro/ のトップにだけ付ける（旧 `jpml_pros.html` と同じ扱い。チャット側の案）
- B2: X の既定のアイコン（`abs.twimg.com/sticky/default_profile_images/…`）を「画像なし」と扱い、頭の1字の丸にする。PRP-01 の時点で12ページ（要確認。今の数と名前を書く）。#135 にコメントする
- B15: `assets/pro.js` の検査は既存の形（リポジトリにある JS の検査の置き方。新しい依存は足さない）で、並べ替え・予定を隠す処理（日本時間の日）・絞り込みの組み合わせ（複数選択・カテゴリの重ね掛け・「すべて」・`?all=1` の書き換え）・お気に入りを確かめる。既存の形で書けない部分は、確かめた表を `## 経過` に書く
- B16: 予定が6件以上ある選手の生成物を実データで1つ以上見て、「ほか N件」の行と live のシートの読み方を書く（直す必要が見つかれば直し、無ければ記録だけ）
- A8: `docs/notes/static-generation.md` の pro/ の行（今の絞り込み・`plans.json`・質問欄が無いこと）。未マージの work/ ブランチが同じ行を変えていれば、この文書だけ変えずに報告に書く（PRP-03 で LGN と衝突した例がある）
- A9: `docs/new-page-checklist.md`「URL パラメータの規約」の pro/ の行（`by`・`all` は今は使わない・複数の値は「,」区切り・`ouka` はシートのタブにもある）
- #641 の本文: PRP-16 の表の C1〜C5・C9 が古い項目。work/1101-prp-pub の `docs/notes/pro-pages.md`「公開のマージ（#641）」を読んで合わせる
- B18 の起票: 同じ主題の issue をクローズ済みを含めて探し、あれば起票せずコメントする。ラベルは「分野: セキュリティ」などがあれば付け、無ければ付けずに報告に書く。本文に PRP-16 の表の B18 の行を引用する
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. B1・B2・B15・B16 を入れ、テストを足す（足したテストは修正前のコードで失敗することを確かめる）。pro/ を Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す。A8・A9 を直す
2. 確かめ（下の止まる条件を全部ここで機械的に確かめ、結果を `## 経過` に書く）: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）。生成物の差分を種類ごとに数えた表。既定のアイコンだった選手のページを1つ、プレビュー（付かなければローカル）で 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ cloudflare へマージし（CLAUDE.md「ブランチ運用」）、本番の `pro/` と既定のアイコンだった選手のページが 200・noindex ありで新しい形になったことを確かめる（15分で古いままなら「未確認の項目」へ）。#641 の本文を書き直す（直した前と後の要点を `## 経過` に書く）。B18 を起票する。上の「決定」を docs/decisions/pros.md に足す

## 止まる条件（1つでも当たればマージしない）
- 生成した pro/ の全ページに `<meta name="robots" content="noindex">` が残っていない（1件でも無い）
- 生成物の差分が pro/ の外に出た（毎朝の生成などで動いた差分として分けられるものは除き、分けたものを書く）
- `navbar.js` の「連盟プロ」の行き先・`index.html` のサイドメニュー・`llms.txt`・`sitemap*.xml`・`_redirects`・`wrangler.jsonc`・`workers/` が origin/cloudflare から変わっている
- テストが1つでも落ちる、または `regenerate-page.yml` の `check`（`pro_pages`）で止まる理由が出た（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 未マージの work/ ブランチ（work/1101-prp-pub を除く）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（A8 の文書だけが当たるなら、その文書だけ外して進める）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open で、ほかのセッションの着手中のコメントは無い（最後は CHAT-1104-PRP-16 の完了）
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし。「マージ: 承認済み（チャットで）」あり
- work/1104-prp-polish はリモートにもローカルにも無かったので `git checkout -b work/1104-prp-polish origin/cloudflare`（8c7dd9a31）で作った

### 1. 変更（b838264a8・f767b851a、生成 ce1c11079）

- B1: `generate_pro_pages.py` に `APP_TITLE_TAG`（`<meta name="apple-mobile-web-app-title" content="連盟プロ">`）を足し、`render_top()` の head にだけ入れた（旧 `jpml_pros.html` 8行目と同じ文字列）
- B2: `x_photo(pro, size)` を足し、X の既定のアイコン（`lib/x_images.is_default_avatar()`、`abs.twimg.com/sticky/default_profile_images/`）を画像なしと同じに扱う。顔写真・トップの表・`search.json` の3か所を置き換えた。対象は12名（cloudflare 8c7dd9a31 の生成物で数えた。PRP-01 の時点と同じ数）: 三田不二夫・比嘉秀樹・神代陽向・村瀬寛哉・吉田勝弥・戸井田清光・福本正雄・仁平宣明・藤﨑龍一・大滝聡・比屋定秀太・犬見武史
- B15: `assets/pro.js` の並べ替えの比べ方（`compareSortKeys()`）と予定を隠す処理の日本時間の日（`jstDay()`・`isPastPlan()`）を IIFE の外の部品に出し、`module.exports` に足した（中の処理はこの部品を呼ぶだけで、比べ方は前と同じ）。検査 `scripts/tests/test_pro_js.py`（node で動かす。test_pro_filter.py と同じ形）: 50音の昇順・降順、同じ値は元の並び、値の無い行、0詰めの数（入会期・リーグの順位）、日本時間 0 時の境目、当日の予定は出す、月またぎ。絞り込みの組み合わせ・`?all=1` の書き換えは `test_pro_filter.py`、お気に入りは `test_pro_favorites.py` に前からある
- B16: 今後の予定が6件以上の選手は、今の `pro/plans.json`（112行、最多は3件: yukinori.toriya.31・kazunori.takizawa.16・sho.shiratori.23）にも、git の履歴にある `pro/plans.json` の2つの版（最多3件）にもいない。「ほか N件」の行は実データで出たことが無い。生成の処理（`plans_panel()`、`PLAN_MAX = 5` を超えたら `<p class="mj-pro-plan-more">ほか{N}件</p>`）は `test_pro_pages.py` の「ほか2件」の検査で確かめている。live のシートは生成1回で2回読む（今後の予定は `sync_live_calendar.load_desired()`、放送対局の行は `generate_live_pages.load()`）。直す必要は見つからず、記録だけにした
- テストの足し: `test_pro_pages.py` の `MetaTest` に `test_top_has_home_screen_title`（トップにだけある）・`test_default_x_avatar_is_initial`（既定のアイコンの URL が個別ページ・トップ・search.json に出ない、頭の1字の丸、ふつうの画像は 200x200 のまま）
- A8: `docs/notes/static-generation.md` の pro/ の行を今の形に（絞り込み〈よみ・所属・入会・鳳凰・桜花・★、複数選択と重ね掛け、既定は全員〉・質問欄が無い・`pro/plans.json`）
- A9: `docs/new-page-checklist.md`「URL パラメータの規約」の pro/ の行から `by` を外し、複数の値の「,」区切り・切り口の重ね掛け・`ouka` はシートのタブにもある・`birthday` だけチップから・pro/ では `all`・`by` を使わず旧の `?all=1` は消す、を書いた

### 2. 確かめ（止まる条件）

- テスト: `python3 -m unittest discover -s scripts/tests` → Ran 1970 tests OK。`node --test 'workers/*/test/*.test.mjs'` → pass 212 / fail 0
- 足したテストを origin/cloudflare（8c7dd9a31）の `git archive` に入れて実行: `FAILED (failures=2, errors=6)`（`test_pro_js.py` の6件は関数が無い、`MetaTest` の2件は旧の出力）
- `regenerate-page.yml` の `check`（`pro_pages`、run 41779708313）: success。「- pro_pages: 通る(--check)」「check: 止まるページなし」
- 生成（target_page pro_pages → ce1c11079）の差分:

| 種類 | 件数 | 中身 |
|---|---|---|
| pro/<slug>/index.html | 12 | 名前の面の写真の1行が `<img … default_profile_200x200.png>` から `<span class="… is-initial">頭の1字</span>` に（12名、上の名前） |
| pro/index.html | 1 | 表の12行（同じ12名の写真）と `apple-mobile-web-app-title` の1行 |
| pro/search.json | 1 | 1行（12名の画像の欄が空に） |
| pro/ の外 | 0 | — |

- noindex: 生成した pro/ の 1,099ページすべてに `<meta name="robots" content="noindex">`（無いページ 0）。`default_profile_images` は pro/ の全ページ・`search.json` で 0件
- `git diff --stat origin/cloudflare...HEAD -- navbar.js index.html llms.txt 'sitemap*.xml' _redirects wrangler.jsonc workers/` → 出力なし（変わっていない）
- 未マージの work/ ブランチ（work/1101-prp-pub を除く）: 変えるファイルに触るのは work/1028-lgn（`docs/new-page-checklist.md`・`docs/notes/static-generation.md`）・work/1104-acf-cache・work/1104-lgn-policy（`docs/notes/static-generation.md`）。`git merge-tree` で、work/1028-lgn は両方の文書で衝突するが、cloudflare を起点にしても同じ箇所（`new-page-checklist.md` 36行目・`static-generation.md` 256・284行目）で同じ数だけ衝突し、この指示の変更で増えない（work/1028-lgn は 2026-10-29 で止まった古い版）。work/1104-acf-cache・work/1104-lgn-policy は衝突なし
- プレビュー（ce1c11079 の Workers Builds、05:36Z に success）を Playwright（UA に mj-internal、360px）: `pro/fujio.mita.11/`（三田不二夫）の写真は `SPAN`「三」72×72・X の画像の読み込み 0・noindex あり・横のはみ出し 0。トップは `apple-mobile-web-app-title` が「連盟プロ」・三田不二夫の行の写真は `is-initial` の「三」。入会期の並べ替えは昇順で「1期」から、もう一度押して「43期」から

### 3. 差分のレビュー（新しい文脈のサブエージェント、1回。作業ツリーを変えないように頼んだ）

origin/cloudflare...ce1c11079。bug・should-fix の指摘なし（並べ替え・日本時間の日・過ぎた予定の比べ方は前と同じ、既定のアイコンの置き換えは3か所で漏れなし、search.json の位置は変わらない、meta はトップだけ、文書はコードと合う、という結果）。

| 指摘 | 扱い |
|---|---|
| 1. `jstDay()` の上の注記が `hidePastPlans()` の注記と重なる | 直した（f767b851a。「日本時間の YYYY-MM-DD」に縮めた） |
| 2. `isPastPlan()` は `<` 1つを包むだけ | 意図どおり（B15 の検査から呼ぶための形） |
| 3. `test_missing_key` は undefined と '' の同じことだけを見る | 意図どおり（値の無い行の並びは `test_ties_keep_original_order` の "" で見ている） |

### 4. B18 の起票

- 同じ主題の issue を「pip install」「資格情報」「regenerate-page.yml push」「GITHUB_TOKEN pip」「書き込みの権限」で探した（`gh api search/issues`、クローズ済みを含む）。近いのは #619（#598 の残り。1 は CI の Python の依存を版の指定なしで入れること、`regenerate-page.yml` も一覧にある）で、「書き込みの資格情報を持ったまま入れる」ことは書かれていない。別の論点として起票した
- #668「regenerate-page.yml の push の job が、書き込みの資格情報を持ったまま pip install する」（ラベル「分野: セキュリティ」）。本文に PRP-16 の表の B18 の行を引用し、#619・#598・#564 を関係に書いた。確かめた形: job `regenerate` は `permissions: contents: write`（78行目）、`actions/checkout` は `persist-credentials` を指定しない（既定で true）、109行目で `pip install --quiet google-auth requests`

### 5. マージと本番

- 決定を `docs/decisions/pros.md` に足した（2026-11-04〈CHAT-1104-PRP-17〉の節）
- origin/cloudflare が進んでいたので取り込んだ（衝突なし。docs・log/・scripts・style.css の他の作業）。取り込みの後もテスト OK（Ran 1971 tests OK）、cloudflare との差は pro/ 14ファイルと上の変更・docs だけ、導線と転送のファイルの差なし、noindex の無いページ 0
- push の直前に `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1104-prp-polish:cloudflare` → `8b36d3c34..0921e3458`
- `Workers Builds: mj` は 0921e3458 で success（05:39Z）。本番の HTML（UA に mj-internal、2件、7秒あけて）: `https://ryoei.pro/pro/` 200・noindex 1・`apple-mobile-web-app-title` 1・`default_profile_images` 0。`https://ryoei.pro/pro/fujio.mita.11/` 200・noindex 1・`mj-pro-photo is-initial` 1・`default_profile_images` 0。本番へのリクエストはこの指示で計2件。確かめたのは本番の HTML まで（見え方はプレビューで 360px を見た）

### 6. #641 の本文の書き直し

前（CHAT-1101-PRP-02 の版）: 「公開でやること」17件・「公開後の確かめ」9件で、チェック 0件。公開の条件は「live/ の公開を待つ」。K8（質問欄の答えのカードの行き先）・`jpml_pros.html` → `/pro/` の 301 を `_redirects` で・`?name=` の 301 を Worker `mj` で・「トップの質問欄に問いを送り答えを確かめる」が残っていた。

後（2026-11-04）:

- 公開の条件: live/ の公開は済。点検は PRP-01 と PRP-16。告知動画は保留
- 「未公開のまま cloudflare に入れたもの（済）」5件にチェック（題の形・並べ替えのボタン・個別ページとシートとお気に入りと `plans.json`・説明文・PRP-17 の B1・B2・B15）
- 「公開のブランチ work/1101-prp-pub に用意したもの（済。公開のマージで入る）」9件にチェック（noindex・navbar とサイドメニューと `llms.txt`・サイトマップ・canonical・OGP の移動・別の Worker `mj-pros-redirect` の 301 と集計・画像の検知・順位変動と title/ のリンク・文書）
- 「公開の前に残っていること」7件（cloudflare の取り込み直し〈185コミット遅れ〉・`sitemap-live.xml` の3か所・`llms.txt` の文・PRP-19 の3つのリンク・取り込みの後の `check`・平野さんの最終確認と Search Console の数字・マージは平野さんの確認の後）
- 「公開の日・その後にやること」3件（P1 と「入れた日」・「鳳凰」シートの3名の確かめ・閉じる issue〈#82 の閉じる理由を書いた〉）
- 「公開後の確かめ」8件（質問欄の確かめを外した。告知動画は保留と書いた）
- 外したもの: K8、`_redirects` の 301、Worker `mj`、トップの質問欄の確かめ
- 書き直しの前の本文はこのセッションの作業ファイルに残した（`gh api` で読んだ 5,048 バイト）。#135 に既定のアイコンの12名を直したことをコメントした

## 報告

- 状態: 完了
- ブランチ: work/1104-prp-polish
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-PRP-17.md
- 比較URL: https://github.com/retroeater/mj/compare/8b36d3c34...0921e3458
- 確認用URL: 本番 https://ryoei.pro/pro/ ・ https://ryoei.pro/pro/fujio.mita.11/ （未公開〈noindex〉。目印: 三田不二夫の写真が頭の1字「三」の丸）
- マージ: 済み（0921e3458 を cloudflare へ。承認済み〈チャットで〉）
- issue: #641（本文を書き直した）・#668（B18 を起票）・#135（コメント）
- 判断が必要なこと:
  - B16: 今後の予定が6件以上の選手は今のデータにも履歴にもいない（最多3件）。「ほか N件」は検査だけで、実データでは確かめられていない。起きたときに見るでよいか
  - work/1101-prp-pub は cloudflare から185コミット遅れた（この指示のマージの後）。公開の前の取り込み直し（#641 の「公開の前に残っていること」の1行目）は、PRP-19 の前にするか一緒にするか
- 未確認の項目: ブラウザでの本番の見え方（HTML まで）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0921e345）: https://github.com/retroeater/mj-logs/tree/main/guide/0921e345

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0921e345/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
