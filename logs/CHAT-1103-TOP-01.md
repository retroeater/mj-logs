# CHAT-1103-TOP-01

- 着手日時: 2026-11-03
- 対象issue: #101, #287, #231, #286, #394, #395
- ブランチ: work/1103-top
- 着手時HEAD: ad26c5345

## 指示

【Claude作成】Claude Code 向け指示：サイトのトップ（#101）の作り直し — 位置づけの更新・起票・実装前の調べ Chat-Ref: CHAT-1103-TOP-01 マージ: ドキュメントのみ（issue・ログ・docs/decisions・docs/notes）なので完了報告のうえ cloudflare へ入れてよい（コード・生成物・サイトの見た目は変えない） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-top を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 このセッションが TOP 系列の最初のセッション（新しく始めたもの）かをログに書く。識別子 TOP は mj-logs の使用済み一覧（chat-ids、2026-11-03 10時の版）に無いことをチャット側が確かめた。受け手側の確認（CLAUDE.md「Chat-Ref」）で重複があれば止まる。

目的
サイトのトップ `index.html`（#101）を、個人の実績の場から「サイトの入口」へ位置づけ直して作り直す。この指示は、その最初の1本として、関係 issue の位置づけの更新・起票と、実装（次の指示 TOP-02 以降）に要る調べを行う。コードは変えない。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* サイトのトップ `index.html` は「サイトの入口」にする。個人の実績（自己紹介・出演・経歴）は「良栄」のトップ `r/`（Chat-Ref 系列 RYO、CHAT-1103-RYO-01 で作成中）へ移すので、#101 の本文「トップページは個人の実績を見せる場」は置き換える
* 新サイト（#296）の第一弾・#21（Astro）の判断待ちをやめ、houou/・pro/ と同じ現行サイトの基本の形（白地のナビ・400px の面・Python の生成スクリプト）で前倒しで作る（2026-10-29 の決定「新サイトの要件のうち現行サイトでも実装できるものは前倒しで実装する」に沿う）
* 第一弾のトップに入れるもの: (1) AIコンシェルジュ統合版の質問欄 (2) お気に入りの選手の「今後の予定」 (3) 各セクション（連盟プロ・鳳凰戦・女流桜花・WRC リーグ・WRC-R・最強戦・タイトル戦・放送対局・番組・良栄）への導線 (4) 今日が誕生日の選手（#286） (5) 開催中・直近の大会日程（#231）
* AIコンシェルジュ統合版: 大会別の質問欄は残したまま、サイト横断で常に画面の右上などから呼び出せる形（1つの入口）にする。開いているページの大会を既定の文脈にできるようにし、トップでは5大会を横断して答える。大会別の欄を同じ部品に置き換えるかは後で決める。Worker `mj-ask` 側の横断モードは AIC 系列（#654）が担当し、呼び出しの部品とトップ・ナビ側は TOP 系列が担当する
* お気に入り: 名前は「お気に入り」（「フォロー」は採らない。フォロー・通知はサーバ側アカウント〈#394〉の後の #395 に残す）。#287「マイ選手」をこの名前に改める。★ は pro/ の選手の個別ページの名前の面に置き、PRO/PRP 系列が作る（TOP から伝言済み）。登録の上限は設けない。トップには、お気に入りの選手の今後の予定を日付の近い順に N 件と「ほか N 件」で出す（N は TOP-02 で決める）
* 進め方は自律開発（不明点は仮置きして最後まで作り、プレビューと仮置きの一覧で平野さんが確かめる）。新トップの公開は `r/` の公開（RYO）と pro/ の公開（#641）の後にする

前提（チャット側。平野さんの決定ではない）

* #101 は open・`状況: 保留`・`分野: SEO/AIO`・`分野: 整理・保守`・`対象: index`。本文は「位置づけ: 新サイトの第一弾として実施する」「情報設計（決定済み）: トップページは個人の実績を見せる場」「依存: #21（Astroへの移行を検討する）の判断が前提」。コメント: 2026-09-19「#286 を本 issue に blocked by」、2026-09-21「最強戦・title/・/live への導線は #101 で扱う」、2026-10-29「トップの文言だけを前倒しした（CHAT-1029-SEO-04）」（REST で 2026-11-03 に読んだ）
* #287「マイ選手（ブラウザ内ブックマーク）を作る」は open・`分野: UI/UX`・`対象: jpml_pros`。本文: localStorage に選手名を複数保存、jpml_pros の「マイ選手」フィルタ、#276・#235・#279 で使う、訪問時の更新表示（#224 の日付列）、カウンタは行わない（2026-09-14）、論点「登録できる最大人数（候補: 10名）」。#395（フォロー・通知、`状況: 保留`）は「アカウント無しの第一段は #287」、#394（ユーザ登録）も同じ参照。#236（最近見た選手、houou/players/ の `mj:recent-players` 5件）は別項目（同日に読んだ）
* #231「開催中・直近の大会日程を表示する」（open、`対象: index`、親 #221）: (a) index.html に欄 (b) 各大会のページの見出し下に1行。データ源の候補は連盟の予定表（#448、`fetch_yotei.py`）・#221（Ampai）・#96（カレンダー）で「着手時に決める」。差し込み方式は #286 とそろえる。#286「トップページに『今日が誕生日の選手』を出す」（open、`状況: 保留`、`対象: index`、#101 に blocked by）: 誕生日の一覧は `scripts/lib/birthdays.py`（docs/notes/birthday-calendar.md「#393・#286 でも使う」）
* #654「質問欄を1つにまとめる（5大会のデータ・コンシェルジュの統合）」は open。AIC 系列が `work/1102-aic`（CHAT-1102-AIC-16、判断待ち）・`work/1103-aic`（CHAT-1103-AIC-18、着手中コメント 2026-11-03）で進めている。#654 の本文・コメントはこの指示では変えない（Worker 側の依頼はチャット側が AIC へ伝言する）
* localStorage の規約は docs/new-page-checklist.md「localStorage の規約」（キー接頭辞 `mj:`、値は JSON、項目ごとに上限、同意表示なし。項目の一覧に「マイ選手 #287」がある）。「上限なし」の決定はこの規約の「項目ごとに上限を決める」と食い違わない（決めた結果が「なし」）が、規約の行の書き方を変えるかは「判断が必要なこと」に書く
* pro/ の個別ページの「今後の予定」は `scripts/generate_pro_pages.py` の `load_plans()`（放送対局〈`sync_live_calendar.load_desired()`〉・未放送の最強戦・道場部ゲスト、日付の近い順に5件、`assets/pro.js` が `data-date` で過ぎた行を隠す。docs/notes/pro-pages.md「個別ページ」3）。トップの「お気に入りの今後の予定」は、全在籍者の予定を1つの JSON に毎朝の再生成で書き、トップの JS が localStorage のお気に入りで絞って出す案（JSON の生成は PRO/PRP 系列へ伝言済み。形は TOP-02 までに両系列で合わせる）
* 現行 `index.html` は iPortfolio テンプレート由来（`index.css`・`index.js`・`assets/vendor` のテンプレート用ライブラリ、独自の `<nav id="navbar">` で `navbar.js` を読まない。docs/notes/static-generation.md「ページの一覧」の行、#15・#98）。内容の `r/` への写しは RYO が行い、トップ側は `r/` の公開後に `index.html` を新トップに置き換える。`_redirects` 先頭行 `/ /index.html 200` は消さない（CLAUDE.md 禁止事項）
* `navbar.js` は RYO（白地の対象パスの一覧に `/r/` を足す1行、公開時に「良栄」の項目）も変える。TOP が後の指示で足す「質問欄の呼び出し」は別の行・別の関数にする（この指示では `navbar.js` を変えない）
* 新トップの生成スクリプトの名前・ページ名（`scripts/generate_top_page.py`・`top_page` など）はこの指示では決めず、調べの結果として案を書く。`index.html` が生成物になると「`index.html` は生成対象外」を前提にした記述（#286 の論点、static-generation.md の行）が変わる

手順

1. 同じ論点の issue（「トップページ」「index」「お気に入り」「マイ選手」「フォロー」「質問欄」「誕生日」「大会日程」）をクローズ済みも含めて検索し、上の「前提」の issue の状態・本文・コメントと食い違えば止まる。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）で `index.html`・`index.css`・`index.js`・`navbar.js`・`scripts/generate_pro_pages.py`・`assets/pro.js` を変えているものを一覧にして `## 経過` に書く（止まらない。TOP-02 の重なりの判断に使う）。#101・#287 に着手中コメントを書く（#231・#286・#395 は本文・状態・ラベルを変えず、コメントだけ）。
2. issue を更新する:
   * #101 の本文を書き直す（古い記述は消して置き換える）: 位置づけ「サイトの入口。houou/・pro/ と同じ現行サイトの基本の形で前倒しで作る（#21 を待たない）」、第一弾に入れる5つ（上の決定）、分担（Worker 側の横断モードは #654、★ と予定の JSON は pro/ 側）、公開の順（`r/` の公開と #641 の後）、同時に解消されること（既存の表はそのまま）、片付け（`index.css`・`index.js`・`assets/vendor` のテンプレート用ライブラリは新トップへの切り替えの後の指示）。「依存: #21」は消す。`状況: 保留` を外す（対応中に変える）。#286 の blocked by はそのまま
   * #287 の題を「お気に入り（ブラウザ内の選手のしおり）を作る」に改め、本文を書き直す: 名前は「お気に入り」、★ は pro/ の個別ページの名前の面（jpml_pros は廃止予定〈#641 で 301〉なのでフィルタは作らない）、保存はブラウザの localStorage（規約は docs/new-page-checklist.md）、上限なし、トップに今後の予定（#101）、通知・サーバ同期は #395（#394 の後）。`対象: jpml_pros` は外し、`対象: index` と pro/ のラベルがあれば付ける（無ければ付けずに報告に書く）。「訪問時の更新表示」「#276・#235・#279 で使う」は「後で検討（第一弾に含めない）」として残す
   * #395・#394 に「アカウント無しの第一段は #287（名前は『お気に入り』に改めた、2026-11-03）」の1行をコメント。#231・#286 に「#101 の第一弾に含める（2026-11-03）。データ源・差し込み方式は CHAT-1103-TOP-01 の調べ（下の 3）に従う」の1行をコメント
3. 実装前の調べを `## 経過` に表で書く（コードは変えない）:
   * 新トップの5つの面それぞれについて、データの所在（関数・生成物・シート・カレンダー）、毎朝の再生成で鮮度を保てるか（`update-live-channel.yml`・`run-daily` のどこに入るか）、既存の部品で足りるか（houou/ のカード・pro/ の面・`lib/page.py` の `PageMeta`・`assets/houou_ask.js`）、新しく要るもの。#231 はデータ源の候補3つ（予定表・Ampai・カレンダー）の今の実装の有無と、鳳凰戦の節の日程がどれから取れるかを確かめて推奨を書く。#286 は `scripts/lib/birthdays.py` の一覧で足りるか
   * 新トップを生成物にする作り方の案（スクリプト名・ページ名・`regenerate.py` の `OUTPUT_OVERRIDES`/`RUN_LAST` の要否・`index.html` を直接書くときの `_redirects`/`.assetsignore`/check-assets の影響）と、「`index.html` は生成対象外」を前提にした記述の所在（`git grep -n 'index.html'` を docs/・scripts/・.github/ で、docs/logs・docs/decisions・handover-archive を除く）
   * `navbar.js` に「質問欄の呼び出し」を足すときの置き場（右上の既存の検索ボタンとの関係〈#112〉、`updateOffsets()`、`index.html` が `navbar.js` を読まないこと）と、AIC に頼む Worker 側の横断モードに要る API の形（今の `POST /api/ask` の要求・応答の項目を `workers/ask/src/index.mjs`・`ask.mjs`・`assets/houou_ask.js` から写し、「大会をまたぐ」ために足りない項目を列挙）。TOP-02 の指示を3項目に割る案（例: ①生成スクリプトと導線・誕生日・大会日程の面 ②お気に入りの今後の予定 ③質問欄の呼び出し）も書く
   * 平野さんの判断が要る点（面の並び・N 件の数・題〈`<title>` をサイト名主にするか〉・`index.html` を生成物にするか、など）を「判断が必要なこと」に列挙する。題の案は `r/` の公開の issue（番号は CHAT-1103-RYO-01 の `## 報告`。まだ無ければ「RYO-01 の完了後に TOP から伝える」と書く）に伝える前提で書く

止まる条件

* 「前提」の issue の状態・本文が実物と食い違う（#101 がクローズ、#287 の本文が別の内容に変わっている、など）
* #101・#287 に他セッションの着手中コメントがある
* 同じ目的（トップの作り直し・お気に入り・トップの質問欄）の未マージ work/ ブランチや open issue が「前提」に無いものとして見つかった（一覧を書いて止まる）
* 手順2で変える issue の本文に、この指示の決定と矛盾する平野さんの決定（docs/decisions/ にある）が見つかった（同じ趣旨なら止めず置き換える）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 62570dd4）: https://github.com/retroeater/mj-logs/tree/main/guide/62570dd4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/62570dd4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
