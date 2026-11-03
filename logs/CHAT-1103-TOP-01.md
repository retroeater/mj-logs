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

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- このセッションは TOP 系列の最初のセッション（新しく始めたもの）。このセッションに来た指示は TOP-01 だけ
- 識別子の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-TOP-'`・`docs/logs/CHAT-*-TOP-*.md` の追加の履歴とも0件（`git fetch --unshallow` の後）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4行とも有り
- 作業ブランチ: `origin/work/1103-top` はリモートにもローカルにも無かったので `git checkout -b work/1103-top origin/cloudflare`（着手時 HEAD ad26c5345）
- 対象 issue の state: #101・#287・#231・#286・#394・#395 はすべて open（#654・#296 も open）。参照の #96 は 2026-11-01 にクローズ済み（指示文は「#96（カレンダー）」をデータ源の候補に挙げているが、経緯の参照なので止まらない。下の 3 の表に実装の有無を書いた）

### 1. 同じ論点の issue と未マージのブランチ

**issue の検索**（`search/issues`、「トップページ」「index」「お気に入り」「マイ選手」「フォロー」「質問欄」「誕生日」「大会日程」「トップ」「ブックマーク」、クローズ済みを含む）:
前提の issue（#101・#287・#231・#286・#394・#395・#236・#654）の状態・本文・コメント・ラベルは指示文の前提と一致した。
同じ目的（トップの作り直し・お気に入り・トップの質問欄）の open issue は前提のもの以外に無い。近いが別目的のもの（止まらない、TOP-02 で触れる候補）:

| issue | 題 | 関係 |
|---|---|---|
| #655・#656 | 「良栄」のトップ r/ を作る・公開する | 前提の RYO。#656 に「title の重なり（r/ と index.html が同じ題）」「r/ と index.html の内容の重複の解消は #101 側」の項目がある |
| #253 | index.html のモバイルナビボタンの aria-label を日本語にする | 新トップで現行のナビが無くなれば解消（切り替えの指示で閉じる候補） |
| #254 | navbar.js の document.write をやめ、ナビを静的HTMLに焼き込む | 質問欄の呼び出しを navbar.js に足すとき、書き方が変わる可能性 |
| #235 | 全ページ共通の選手名検索ボックス | 新サイト送り。トップの「選手を探す」を置くなら関係する |
| #417 | 共通ナビの position の方針 | 新サイト送り。右上の呼び出しの位置に関係 |

**未マージの work/ ブランチ**（`git branch -r --no-merged origin/cloudflare`、2026-11-03 時点）で対象の6ファイルを変えているもの:

| ブランチ | Chat-Ref（コミットのトレーラ） | 変えているファイル | 中身 |
|---|---|---|---|
| work/1029-lgn-page | LGN-09〜17 | navbar.js（+3/−1） | `/log/` を白地のナビの対象に足す分岐（`newNavCurrent = 'リソース'`） |
| work/1101-prp-pub | PRP-03〜06 | index.html（1行）・navbar.js（2行）・assets/pro.js（1行）・scripts/generate_pro_pages.py（+71/−21 の一部） | pro/ の公開。index.html のナビの「Database」を `jpml_pros.html` → `/pro/`、navbar.js の「連盟プロ」を `/pro/` に |
| work/1103-ryo | RYO-01 | navbar.js（+2） | `/r/` を白地のナビの対象に足す分岐（`newNavCurrent = '良栄'`） |

ほかの未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes）は6ファイルを変えていない。前提の `work/1102-aic`・`work/1103-aic` はどちらも cloudflare にマージ済み（`merge-base --is-ancestor` が真）。
TOP-02 で navbar.js に呼び出しを足すときは、3本とも `newNavCurrent` の if の連なりを変えるので、その連なりの外（別の関数）に書けば重ならない。

**着手中コメント**: #101・#287 に他セッションの着手中コメントは無かった。両方に着手中コメントを書いた。

**docs/decisions の確認**: #101・#287・#286・#231・「マイ選手」「お気に入り」「個人の実績」を grep。矛盾する決定は無い（`design-renewal.md` の「#101 のトップをこの形で作る」は同じ趣旨）。

### 2. issue の更新

- #101: 本文を書き直した（位置づけ「サイトの入口」・第一弾の5つ・分担の表・公開の順・同時に解消されること〈既存の表のまま〉・片付け・進め方）。「情報設計（決定済み）: 個人の実績を見せる場」「依存: #21」「着手前に決めること（演出の引き継ぎ・portfolio・スタック）」は消した（演出と portfolio は r/ へ移る中身、スタックは前倒しの決定で不要）。ラベル `状況: 保留` → `状況: 対応中`。#286 の blocked by はそのまま
- **#101 を #296 の sub-issue から外し、#296 の表の行を「親の対象外」へ移した。** 指示文の手順2には無いが、CLAUDE.md「新サイト送りの issue」と #296 本文「新サイト送りにするとき・やめるとき」（やめた issue はコメントと sub-issue の解除を片方だけにしない）に従った（決定「#296 の第一弾・#21 の判断待ちをやめ前倒し」が新サイト送りの取り消しに当たるため）。#101 に取り消しのコメント。#296 本文は #101 の行の移動のほか、「経緯」の「#101 はトップページ部分の子 issue として位置づける」と #21 の行の「（#101 着手前に判断が必要）」を今の形に直した。`get_parent` で #101 の親が無いことを確かめた
- #287: 題を「お気に入り（ブラウザ内の選手のしおり）を作る」に、本文を書き直した。ラベル `対象: jpml_pros` を外し `対象: index` を付けた。**pro/ のラベルはリポジトリに無い**（`対象:` の一覧に pro が無い）ので付けていない
- #395・#394: 「アカウント無しの第一段は #287（名前は『お気に入り』に改めた、2026-11-03）」をコメント
- #231・#286: 「#101 の第一弾に含める（2026-11-03）。データ源・差し込み方式は CHAT-1103-TOP-01 の調べ（手順 3）に従う」をコメント

### 3. 実装前の調べ

#### 3-1. 5つの面

| 面 | データの所在 | 毎朝の鮮度 | 既存の部品 | 新しく要るもの |
|---|---|---|---|---|
| (1) 質問欄（統合版） | Worker `mj-ask`（`workers/ask/`、`POST /api/ask`）。データは `workers/ask/data*.json`（`ask_data*` の生成） | Worker のデータは各大会の再生成で更新（トップ側は持たない） | `assets/houou_ask.js`（`data-contest` で大会を選ぶ）・`generate_houou_pages.ASK_TEMPLATE`（pro/ のトップ `top_ask()` が鳳凰戦のデータで流用済み） | 横断モード（#654、Worker 側）と、ナビから呼び出す部品（下の 3-3） |
| (2) お気に入りの今後の予定 | `generate_pro_pages.load_plans()`（放送対局 = `sync_live_calendar.load_desired()`〈シート・層1を読む。カレンダーは読まない〉、最強戦 = `saikyo/players/` の対局前の卓、道場部 = 「mj_道場部ゲスト」を Calendar API）。`{名前: [Plan(date, time, kind, role, title, href, external)]}` | pro_pages は毎朝 `update-live-channel.yml` の手順5（`regenerate`、`target_page: live_pages title_pages pro_pages`）で作り直される | pro/ の `plan_row()`（`mj-houou-card mj-pro-plan`、`data-date` で `assets/pro.js` が過ぎた行を隠す）・`plans_panel()`（`PLAN_MAX` 件と「ほかN件」） | 全在籍者の予定の JSON（pro/ 側が作る、前提のとおり）。トップの JS が localStorage のお気に入りで絞って並べる。JSON の案: `pro/plans.json` = `{"asof": "YYYY-MM-DD", "p": {"<slug>": [[date, time, kind, role, title, href, external], …]}}`（キーは名前より slug が改名に強い。★ の保存も slug にそろえるかは PRO/PRP と合わせる） |
| (3) 各セクションへの導線 | 固定の一覧（navbar.js の項目と同じ行き先）。在籍者の数など数字を添えるなら各ページの生成物（`pro/search.json` の件数など） | 固定なら鮮度の問題なし | houou/ のカード（`mj-houou-card`、`row_link()`・`panel()`） | 10項目の行き先と1行の説明の文言（仮置きで作る）。「番組」「放送対局」の行き先は今の navbar の `/video_live.html`・`/live/` のどちらにするか（#362 の公開前は `/live/` は noindex） |
| (4) 今日が誕生日の選手（#286） | `scripts/lib/birthdays.py`（「プロ」の表示 Y × 名簿の誕生日、`build()` が月日の順の一覧）。pro/ の `Pro.birthday`（`load_roster()`、名簿から）も同じ値で、slug を持つ | 毎朝の生成で焼けば当日分は鮮度を保てる。ただし生成が失敗した日は前の日の誕生日が出る | `birthdays.build()`（#379・#393 と共用。#370 の名簿の取り込みは済み）。pro/ の個別ページへのリンクは slug が要る | **一覧そのものは `birthdays.py` で足りる**（slug は無いので、リンクを付けるなら pro/ の `Pro` から引くか、名前で pro/search.json と突き合わせる）。日付の取り違えを避けるため、前後数日分を焼き、JS が日本時間の今日の分だけを出す案（生成が1〜2日止まっても正しい日が出る） |
| (5) 開催中・直近の大会日程（#231） | 下の「#231 のデータ源」 | `load_desired()` を使えば pro/ と同じく毎朝 | pro/ の予定の行（`plan_row()` の形） | 大会ごとにまとめる（`live_calendar` の予定の件名から大会名。`yotei.EVENTS`・`taikai_names`） |

**#231 のデータ源の3候補の今の実装**:

| 候補 | 今の実装 | 鳳凰戦の節の日程 |
|---|---|---|
| 連盟の予定表（#448、`fetch_yotei.py`） | あり。毎朝 `update-live-channel.yml` の `yotei`（`sync-yotei.yml`）が予定表の全期間を読み、「ryoei.pro_日本プロ麻雀連盟_予定表」の【1】【2】【3】へ書く。放送対局の公開カレンダー「mj_放送対局」へも同期し、`sync_live_calendar.load_desired()` が「枠（YouTube のライブの枠）」と「仮（【3】の掲載 Y で、まだ枠の無い予定）」を組み立てる | 取れるのは放送のある A1・A2・B1 リーグと決定戦だけ（`docs/notes/yotei-sheet.md`「【2】の規則」の大会・分け）。C1 以下の節の日程は無い（コードの規則による。実データの件数はこのセッションからシートを読めず未確認） |
| Ampai（#221） | 日程の取得は無い。`scripts/lib/ampai.py` は「R_タイトル戦_マスタ」の「Ampai」シートから成績表の URL を引くだけ（#548）。#221（取得基盤）は open で、規約の確認から未着手 | 取れない（取得基盤ができれば候補） |
| カレンダー（#96） | #96 は 2026-11-01 にクローズ。具体の自動化は別 issue に分かれ、放送対局は #448 の公開カレンダー（上の予定表と同じ仕組み）、誕生日 #379、道場部 #390 | 予定表と同じ（A1・A2・B1・決定戦） |

**推奨**: (5) は `sync_live_calendar.load_desired()` の予定（pro/ の放送対局の予定と同じ元）から、今日以降の予定を大会ごとにまとめて出す。新しい取得を足さず、pro/ と同じ毎朝の再生成で鮮度を保てる。C1 以下の節は出さない（`簡略化:` で上限と次の一手〈#221〉を残す）。#231 (b)（各大会のページの見出し下の1行）は第一弾に含めず #231 に残す。
**差し込み方式（#286 とそろえる）**: どちらも生成時にトップへ焼く（`apply_page_meta.py` 相当の差し込みではなく、トップを生成物にする。下の 3-2）。日付で変わるもの（誕生日・過ぎた予定）は、焼いた行に `data-date` を付けて JS が日本時間の今日で出し分ける（pro/ の `assets/pro.js` と同じ形）。

#### 3-2. 新トップを生成物にする作り方の案

- スクリプト `scripts/generate_top_page.py`・ページ名 `top_page`（`regenerate.py` は `generate_<ページ名>.py` を探す）。出力は `OUTPUT_OVERRIDES` に `"top_page": "index.html"`（無いと `top_page.html` とみなされ、ワークフローのコミット対象から外れる）
- **未公開で入れる段**: `index.html` は公開中なので、TOP-02〜04 では未公開の別のパス（案: `top/index.html`、noindex・メニュー未掲載。最強戦の仮の `saikyo/next/` と同じ考え方）に書き、`r/` と #641 の公開の後の切り替えの指示で出力先を `index.html` に変える。`top/` を作るなら `_redirects` に `/top` → `/top/`（301）・`/top/` → `index.html`（200）の行、`.github/workflows/check-assets.yml` の `allowed()`、`.assetsignore`（公開するので足さない）を pro/ と同じ形で
- **RUN_LAST**: (2) を生成時に焼くなら pro/ の生成物（予定の JSON）を読むので pro_pages の後に作る必要がある。今の `run_order()` は `sorted(key=p in RUN_LAST)` で、RUN_LAST の中の順は渡された順のまま。`("pro_pages", "top_page")` と足すだけでは `top_page pro_pages` の順で渡されると逆になるので、RUN_LAST の中の位置で並べる直しが要る。**(2) を JS が実行時に `pro/plans.json` を読む形にすれば、RUN_LAST も pro_pages への依存も要らない**（推奨。お気に入りはブラウザにしか無く、どのみち JS で絞るため）
- 毎朝の対象: (4)(5) を焼くなら `update-live-channel.yml` の `target_page` に `top_page` を足す（pro/ と同じ鮮度）。(5) は `load_desired()` を呼ぶので pro/ と同じくシートを読む（`SHEETS_READER_SA_KEY`）
- `_redirects` 先頭の `/  /index.html  200` は消さない。切り替えでは行を変えない
- `index.html` を直接書くときの影響: check-assets（配信の対象・上限）・`.assetsignore`（`index.html` は配信対象のまま）・sitemap の lastmod（`update_sitemap_lastmod.py --from-git` が `/` を `index.html` に対応させる。生成のコミットで lastmod が毎朝動く可能性。pro/ と同じ扱いでよいか切り替えの指示で見る）
- テスト: `scripts/tests/test_breadcrumb_json_ld.py` の `test_top_has_website` が root の `index.html` の JSON-LD が `WebSite` だけであることを確かめている。新トップでも `WebSite` を出す（`PageMeta` にパンくずを出さない）か、テストを直すかを切り替えの指示で決める

**「`index.html` は生成対象外」を前提にした記述の所在**（`git grep -n 'index.html'` を docs/・scripts/・.github/ で、docs/logs・docs/decisions・handover-archive を除く。サブディレクトリの index.html の行は除いた）:

| 所在 | 記述 | 新トップで |
|---|---|---|
| docs/notes/static-generation.md「ページの一覧」の表 | `index.html` の行「Webサイトテンプレート（iPortfolio）由来。`index.css` と11個のvendorライブラリを使う」 | 切り替えの指示で生成の系統へ移す |
| docs/handover.md「新サイト」の節 | 「**新サイトの第一弾は index.html（#101）。**…ボトルネックは #21」 | 2026-11-03 の決定と食い違う（下の「判断が必要なこと」） |
| docs/handover.md（系統の列挙） | 「系統は `index.html`（テンプレート由来）・ビルド時生成…」 | 切り替えの指示で直す |
| docs/new-site-design.md「着手順序: 最初に作るのは index.html」・同 548・554・570 行 | Astro での試作・着手対象は index.html | 決定と食い違う（判断が必要なこと） |
| docs/astro-migration-study.md 冒頭・45・54 行 | index.html を試作対象・共通レイアウトの対象外とする | 調べの記録。直すかは #21 の側 |
| #286 本文「論点」 | 「index.html は生成対象外のため、この1行だけを `apply_page_meta.py` 相当で差し込むか、`data/birthdays.json` を JS で読むか」 | 上の推奨で解消（実装の指示で #286 の本文を直す） |
| docs/notes/decisions-2026-09-13-review.md 21 行 | 「index.html への差し込み: #231 と #286 で方式を揃える」 | そのままでよい（方式はそろえる） |
| scripts/lib/page.py 175 行のコメント | 「index.html以外はbootstrap.min.cssを読む」 | 新トップが page.py で書けば当てはまらなくなる（切り替えの指示で直す） |
| docs/notes/a11y-manual-check.md「D. index.html（別系統・テンプレート由来）」・docs/notes/site-findings.md「index.html の特殊性（#15）」・docs/lighthouse-baseline.md | 別系統としての点検・記録 | 切り替え・片付けの指示で直す |
| docs/notes/saikyo-page-design.md 152 行 | 「トップページ（`index.html`）は共通の navbar を使わないため、そこからの導線は無いまま（#101 で扱う）」 | 新トップの導線で解消 |
| docs/notes/sitemap-lastmod.md 24 行・scripts/update_sitemap_lastmod.py | `https://ryoei.pro/` は `index.html` | そのまま（出力先が index.html なら変わらない） |
| scripts/tests/test_breadcrumb_json_ld.py 117 行 | root の index.html の JSON-LD | 上のとおり |

#### 3-3. 質問欄の呼び出しと Worker の横断モード

**navbar.js の置き場**:
- 右上の虫眼鏡（`searchIconHtml`）は `<nav>` の中の `.navbar-collapse` の後ろ（右端）に `document.write` で出る。`#searchBoxes`（表のページの絞り込み欄）の開閉で、`<body data-search="off">` のページでは出さない（#163。#112 は (a) の形で閉じている）。houou/・pro/ など白地のナビのページはすべて `data-search="off"` なので、右上は空いている
- 案: 虫眼鏡と同じ位置に「質問」のボタンを別の変数・別の関数で足す（`newNavCurrent` の if の連なりの外。RYO・LGN・PRP の3本の差分と重ならない）。押すと質問欄のシート（houou/ の絞り込みのシートと同じ形）を開き、中身は `assets/houou_ask.js` の部品を使う。開いているページの大会はパス（`/houou/`・`/ouka/`・`/jwrc/`・`/wrcr/`・`/saikyo/`）から既定の文脈にし、それ以外（トップ・pro/ など）は横断
- 虫眼鏡のあるページ（表のページ）では2つ並ぶ。`updateOffsets()` は `table.js`・`jpml_pros.js` にあり、`nav.navbar` の高さと `#searchBoxes` の高さで `--navbar-height`・`--content-offset` を決める。質問欄をナビの下に差し込む開閉にすると、この2つの計算に入らず表の見出しと重なる。シート（画面の下からの重ね）にすれば `updateOffsets()` に触らずに済む（推奨）
- 現行の `index.html` は `navbar.js` を読まない（独自の `<nav id="navbar">`）。新トップは `lib/page.py` の形で書けば `navbar.js` を読むので、この問題は新トップで消える。今の `index.html` には呼び出しを足さない
- `navbar.js` を読むページはすべて対象になるので、未公開の段では呼び出しを特定のパスだけで出す（`top/`・pro/ などに限る）か、ページ側の目印（`data-ask="on"`）で出す

**今の `POST /api/ask`**（`workers/ask/src/index.mjs`・`ask.mjs`・`assets/houou_ask.js`）:

| 向き | 項目 | 内容 |
|---|---|---|
| 要求 | `question` | 文字列（`QUESTION_MAX` 字まで。空は 400 `empty`、長すぎは 400 `too_long`） |
| 要求 | `contest` | 省略で鳳凰戦。`ouka`・`jwrc`・`wrcr`・`saikyo`（`deps.contests` のキー）。知らない値は 400 `bad_request`。ページの `data-contest` から |
| 要求 | ヘッダ `Origin` | `ALLOWED_ORIGIN`（`https://ryoei.pro`）以外は 403 |
| 応答 | `status` | `answered`・`unanswerable`・`limit`（429 も）・`invalid`・`region`・`unavailable`・`forbidden` |
| 応答 | `lead`・`answer`・`notes[]`・`asof[]`・`sources[{label, url}]`・`remaining` | 答えた時。`asof` は「データ: …」の行、`sources` は出典のリンク |
| 応答 | `scope`・`resetAt`（limit）・`reason`・`max`（invalid） | |
| `GET /api/ask/status` | `status`（open・limit・unavailable）・`remaining`・`max` | 受付の残り |
| クライアント | 答えの文に出た選手のカード | `data-players-url`（大会の `ask-players.json`）の名簿と突き合わせ、`data-players-page` の個人成績へリンク（大会ごとに1つ） |

**「大会をまたぐ」ために足りない項目**（AIC へ伝える材料）:
1. 要求に「横断」を表す値が無い（`contest` の省略は鳳凰戦の意味なので、省略を横断に変えると今のページの答えが変わる）。案: `contest: "all"`（または `scope: "all"`）と、既定の文脈の `context: "<大会>"`（開いているページの大会。問いに大会名が無いときに使う）
2. 応答に「どの大会のデータで答えたか」が無い。案: `contests: ["houou", …]`（または `sources[]` の各項目に `contest`）。クライアントが選手のカードの名簿・行き先を大会ごとに選ぶため
3. 選手のカードは1大会の名簿（`data-players-url`）前提。横断では、応答に `players: [{name, contest, url}]` を返すか、クライアントが5大会の `ask-players.json` を読む（5本の取得が増える）
4. 1回目の指示（`plan()`）は1大会の検索の型を選ぶ形。横断では大会の選び方（問いの大会名・`context`・複数大会の比較）を決める段が要る
5. 記録（`recordType(c, …)`・D1 の `questions`）に横断の印が無い。週次の確かめ（#654）で横断の問いを見分けるため
6. `GUIDE_PAGES` に最強戦が無い（#654 の「ほかの残り」。横断では案内そのものが要らなくなる可能性）
7. `ALLOWED_ORIGIN` が本番だけなので、work/ のプレビュー（別名の URL）からは 403 になり、呼び出しの部品をプレビューで確かめられない。部品は応答の見本（固定の JSON）で確かめ、本番の答えは公開後に見る形になる

#### 3-4. TOP-02 を割る案

| 指示 | 範囲 | 依存 |
|---|---|---|
| ① TOP-02 | `scripts/generate_top_page.py`（未公開の `top/`、noindex）・`lib/page.py` の `PageMeta`・houou/ のカードで、(3) 導線・(4) 誕生日・(5) 大会日程の面。`regenerate.py` の `OUTPUT_OVERRIDES`・毎朝の `update-live-channel.yml` の `target_page` に足す。テスト | なし（pro/ の生成の関数を読むだけ） |
| ② TOP-03 | (2) お気に入りの今後の予定（トップの JS が `pro/plans.json` を読み、localStorage のお気に入りで絞って N 件と「ほかN件」）。キーと値の形を PRO/PRP の ★ と合わせる | pro/ 側の予定の JSON と ★（PRO/PRP） |
| ③ TOP-04 | (1) 質問欄の呼び出し（navbar.js の別の関数・シート・`houou_ask.js` の流用）。横断は Worker の対応までは、トップでは鳳凰戦の既定（pro/ のトップと同じ）で仮置き | Worker の横断モード（#654、AIC） |
| ④ 切り替え | r/ の公開（#656）と pro/ の公開（#641）の後に、出力先を `index.html` へ。`index.css`・`index.js`・テンプレート用 vendor の片付けはその後の指示 | #656・#641 |

### 4. 決定の記録

- 指示文の「決定」節を `docs/decisions/top.md`（新しい分野「サイトのトップ」）に書き、`docs/decisions/README.md` の表に1行足した（未マージの work/1103-ryo が同じ表に `ryoei.md` の行を足しているが、位置が違うので重ならない）
- コード・生成物・ワークフロー・配信設定は変えていないので、サブエージェントのレビューは行っていない（docs/notes/self-review.md の対象外）

## 報告

- 状態: 判断待ち
- ブランチ: work/1103-top
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1103-TOP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-top
- 確認用URL: なし
- マージ: 済（2976c97d6、fast-forward。docs のみの変更なので完了報告のうえマージ。この行の追記は docs/logs のみの追いの push）
- issue: #101（本文の書き直し・`状況: 対応中`・#296 の子から外した）・#287（題・本文・ラベル）・#296（#101 の行を「親の対象外」へ、経緯と #21 の行を直した）・#394・#395・#231・#286（1行コメント）
- 判断が必要なこと:
  - 面の並び（仮置きの案）: 質問欄 → お気に入りの今後の予定 → 開催中・直近の大会日程 → 今日が誕生日の選手 → 各セクションへの導線。お気に入りが0件の人（初めての訪問者）には (2) の面を出さないか「pro/ の個別ページの ★ で登録できます」の1行にするか
  - お気に入りの今後の予定の N 件（案: pro/ の個別ページと同じ5件〈`PLAN_MAX`〉と「ほかN件」）
  - 題（`<title>`）: 案「ryoei.pro | 日本プロ麻雀連盟の選手と大会のデータ」（サイト名を主にする）。今の index.html と `r/` が同じ「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」なので、`r/` 側はこの題を残し、トップをサイト名主にすれば重ならない。決まったら #656 の「title の重なり」の項目に TOP から伝える
  - `index.html` を生成物にするか（案: する。`scripts/generate_top_page.py`・ページ名 `top_page`・`OUTPUT_OVERRIDES` で `index.html`。それまでは未公開の `top/` に書く。`## 経過` 3-2）
  - お気に入りの今後の予定を、トップに焼くか、JS が `pro/plans.json` を実行時に読むか（案: 実行時に読む。RUN_LAST の直しが要らない。`## 経過` 3-2）。JSON の形とキー（名前か slug か）は PRO/PRP と合わせる
  - 大会日程（#231）は連盟の予定表から取れる放送のある A1・A2・B1・決定戦などだけを出し、C1 以下の節は出さない（Ampai の取得基盤 #221 まで）でよいか。#231 (b)（各大会のページの見出し下の1行）は第一弾に含めない案
  - 導線の「放送対局」「番組」の行き先（今の navbar は「放送対局」→ `/video_live.html`、`/live/` は #362 の公開前で noindex）
  - 質問欄の呼び出しの部品は、Worker の横断モード（#654）ができるまでトップでは鳳凰戦の既定で仮置きしてよいか。プレビューでは `ALLOWED_ORIGIN` が本番だけのため答えを確かめられない（`## 経過` 3-3 の 7）
  - docs/new-page-checklist.md「localStorage の規約」の「マイ選手 #287」を「お気に入り #287（上限なし）」に書き換えるか（今の「項目ごとに上限を決める」とは食い違わない）。この指示では変えていない
  - #287 に pro/ のラベルを付けられなかった（`対象: pro` がリポジトリに無い）。ラベルを作るか
  - ガイド文の食い違い（この指示の範囲〈issue・ログ・docs/decisions・docs/notes〉の外なので直していない）: docs/handover.md「新サイト」の節の「**新サイトの第一弾は index.html（#101）。**…ボトルネックは #21」と、docs/new-site-design.md「着手順序: 最初に作るのは index.html（トップページ）」が 2026-11-03 の決定と食い違う
  - #101 を #296 の sub-issue から外した（指示文の手順2には無いが、#296 本文の「やめた issue はコメントと sub-issue の解除を片方だけにしない」に従った）。戻すなら #296 に足し直す
- 未確認の項目:
  - 連盟の予定表の【2】に、C1 以下の鳳凰戦の節が実際に無いか（コードの規則で判断した。このセッションからはシートを読めない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 12483fda）: https://github.com/retroeater/mj-logs/tree/main/guide/12483fda

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/12483fda/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
