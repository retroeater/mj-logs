# CHAT-1103-TOP-02

- 着手日時: 2026-11-03
- 対象issue: #101, #231, #286, #287, #656
- ブランチ: work/1103-top
- 着手時HEAD: e4049b271

## 指示

【Claude作成】Claude Code 向け指示：新トップの生成スクリプトを未公開の top/ に作る（大会日程・今日が誕生日・各セクションへの導線、#101） Chat-Ref: CHAT-1103-TOP-02 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-top を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-top origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる（TOP-01 は 2976c97d6 でマージ済みの見込み）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1103-TOP-01 のログの `## 報告` が「完了」かマージ済みであることを確かめる（判断待ちのままでも、マージ済み〈`docs/decisions/top.md` が origin/cloudflare にある〉なら進む）。

目的
#101 の新トップを、未公開の `top/`（noindex・メニュー未掲載）に生成するスクリプトを作る。この指示の範囲は、面の (5) 開催中・直近の大会日程（#231）・(4) 今日が誕生日の選手（#286）・(3) 各セクションへの導線と、(1) 質問欄・(2) お気に入りの今後の予定の置き場（空の面か出さない形。中身は TOP-03・TOP-04）。あわせて TOP-01 の「判断が必要なこと」への平野さんの回答を記録・反映する。
決定（2026-11-03、平野さん。TOP 系列のチャットで。TOP-01 の「判断が必要なこと」への回答）

* 面の並び: 質問欄 → お気に入りの今後の予定 → 開催中・直近の大会日程 → 今日が誕生日の選手 → 各セクションへの導線。お気に入りが0件の人には予定の面を出さず、代わりに「pro/ の個別ページの ★ でお気に入りに登録できます」の1行を出す
* お気に入りの今後の予定は、日付の近い順に5件と「ほかN件」（pro/ の個別ページと同じ）。トップに焼かず、トップの JS が実行時に pro/ 側の予定の JSON を読み、localStorage のお気に入りで絞る
* 題（`<title>`）は「日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro」（サイト名を最後に。ほかのページの「… | ryoei.pro」と同じ並び）。`r/` は今の題「平野良栄 | 麻雀プロ（日本プロ麻雀連盟） | ryoei.pro」を残す
* `index.html` は生成物にする（`scripts/generate_top_page.py`・ページ名 `top_page`）。未公開の間は `top/` に書き、`r/` と pro/ の公開の後の切り替えの指示で出力先を `index.html` に変える
* 大会日程（#231）: 「タイトル戦」（title/）に掲載されているタイトル戦（および麻雀最強戦）の、ベスト16以上の放送対局を載せる。予選・リーグ戦は載せない（お気に入りの選手のリーグ戦は「今後の予定」で見つけられるため）。#231 の (b)（各大会のページの見出し下の1行）は第一弾に含めない
* 導線の「放送対局」の行き先は `/live/`（新トップの公開は #362 の後の見込み）。「番組」は navbar と同じ
* 質問欄は、トップでは横断（`contest: "all"`）で答える（AIC-20 で Worker の横断が入った。呼び出しの部品は TOP-04）
* docs/new-page-checklist.md「localStorage の規約」の「マイ選手 #287」を「お気に入り #287（上限なし）」に書き換える
* ラベル `対象: pro` を作り、#287 に付ける
* docs/handover.md「新サイトの第一弾は index.html（#101）」・docs/new-site-design.md「最初に作るのは index.html」の食い違いは、新トップの切り替えの指示で直す（この指示では変えない）。#101 を #296 の子から外したのはそのまま

前提（チャット側。平野さんの決定ではない）

* 作り方は TOP-01 のログ `## 経過`「3-1」「3-2」の案に従う（スクリプト名・`OUTPUT_OVERRIDES`・`_redirects` の `/top` → `/top/`〈301〉と `/top/` → `index.html`〈200〉・`check-assets.yml` の `allowed()` に `top`・`.assetsignore` には足さない・noindex は `NOINDEX_TAG` の定数・毎朝の `update-live-channel.yml` の `target_page` に `top_page` を足す）。実物と食い違えば実物に合わせ、`## 経過` に書く
* 見た目と部品は houou/・pro/ のもの（`lib/page.py` の `PageMeta`、`mj-houou-card`・`row_link()`・`panel()`、pro/ の `plan_row()` の形。値は docs/notes/design.md、最小の画面幅 360px）。白地のナビは `navbar.js` の新しい形の対象パスに `/top/` を足す（RYO が `/r/` を足した行と同じ形。`newNavCurrent` は付けない〈どのメニューにも当たらない〉かは実物で決めて仮置き）
* (5) 大会日程: 元は `sync_live_calendar.load_desired()`（pro/ の放送対局の予定と同じ。TOP-01 の 3-1）。「タイトル戦に掲載されている大会」は title/ の生成が読む大会の一覧（「R_タイトル戦_マスタ」・`generate_title_pages.py` の大会の定義。要確認）で判定する。「ベスト16以上」の段（ベスト16・ベスト8・準決勝・決勝・決定戦など）をどの値から判定できるか（予定の件名・【2】の規則〈docs/notes/yotei-sheet.md「【2】の規則」〉・`yotei.EVENTS`・`taikai_names`）を先に調べる。麻雀最強戦は `saikyo/` のデータの未放送の卓（pro/ の最強戦の予定と同じ元）のうち、ファイナル（ベスト16）以上に当たるもの（要確認）。鳳凰戦は A1 リーグ（リーグ戦）を除き決定戦だけ。判定できない段は載せない側に倒して仮置きし、件名の一覧（載せた・外した）を `## 経過` に全件で書く。面の行は pro/ の予定の行と同じ形（日付・大会のチップ・件名、行き先は YouTube の枠）。日付の近い順に5件と「ほかN件」（仮置き）。1件も無ければ面を出さない。過ぎた行は JS が `data-date` で隠す（pro/ の `assets/pro.js` と同じ基準）
* (4) 今日が誕生日: `scripts/lib/birthdays.py` の `build()`（TOP-01 の 3-1）。前後数日分を焼き、JS が日本時間の今日の分だけ出す（生成が1〜2日止まっても正しい日が出る）。名前は pro/ の個別ページへリンク（slug は pro/ の `Pro` か `pro/search.json` から。pro/ は未公開なので、この指示ではリンクにしてよいがプレビューでだけ確かめる）。該当者がいなければ面を出さない
* (3) 導線: 10項目（連盟プロ・鳳凰戦・女流桜花・WRC リーグ・WRC-R・最強戦・タイトル戦・放送対局・番組・良栄）。行き先は navbar.js と同じ（連盟プロは `/pro/`〈公開前〉、放送対局は `/live/`、良栄は `/r/`）。1行の説明の文言は仮置き
* (1)(2) の置き場: この指示では中身を作らない。面の順を決定のとおりにしたうえで、HTML に差し込み口（空の要素か、生成の関数の引数）だけを用意する。お気に入り0件の1行は TOP-03 で JS が出す
* 共有の部品（`lib/page.py`・`style.css`・`navbar.js`・pro/ の関数）に手を入れるときは参照を洗い出し、全ページを再生成して差分を確かめる（CLAUDE.md「判断・作業の原則」）。`generate_pro_pages.py`・`assets/pro.js` は PRO/PRP 系列が ★ と予定の JSON で変えるので、関数を import して使うだけにし、変えない（変えたくなったら止まらず仮置きにして報告に書く）
* #656 のコメント（2026-11-03）: r/ は 2026-11-03 に未公開でマージ済み。「title の重なり」の項目がある

手順

1. TOP-01 の回答を反映する（コードより先に）: `docs/decisions/top.md` に上の「決定」を足す。docs/new-page-checklist.md「localStorage の規約」の該当の行を書き換える（読んでから。ほかの項目は変えない）。ラベル `対象: pro` を作り #287 に付ける。#656 に「トップの題は『日本プロ麻雀連盟の選手と大会のデータ | ryoei.pro』に決まった（2026-11-03）。r/ は今の題を残すので重ならない」とコメントする（本文・状態・ラベルは変えない）。#231・#286 の本文の差し込み方式の論点を、この決定（トップを生成物にして焼く、日付で変わる行は JS で出し分ける）に置き換え、#231 は掲載範囲（タイトル戦〈title/ 掲載〉と最強戦のベスト16以上の放送対局、予選・リーグ戦は載せない）を書く。#101・#231・#286 に着手中コメント
2. (5) の判定の調べ（段の判定に使える値と、今の予定の件名の全件の分類）を `## 経過` に書いてから、`scripts/generate_top_page.py`・テスト（`scripts/tests/test_top_page.py`。大会日程の判定と並び・誕生日の出し分けの行・導線の行き先）・JS（要れば `assets/top.js`。日付の出し分けだけ）・`_redirects`・`check-assets.yml`・`navbar.js` の1行・`regenerate.py`・`update-live-channel.yml` を作り、`top/index.html` を生成する。docs/notes/static-generation.md「ページの一覧」に `top/` の行（「noindex・メニュー未掲載。切り替えの後に index.html」）と、新しい docs/notes/top-page.md（構成・面・データ・未公開の段の扱い）を足す。ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」のとおり作業ブランチで手動実行して確かめる（シートを読むので `regenerate-page.yml` の作業ブランチの ref・`target_page: top_page` で生成し、止まる理由があれば一覧を書いて止まる）
3. 検査・自己レビュー（docs/notes/self-review.md）・画面の確かめ（360px と 400px。端のデータ: 大会日程が0件・6件以上・いちばん長い件名、誕生日が0人・多い日）を流し、仮置きの一覧をログに書く。ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* TOP-01 がマージされていない、または `docs/decisions/top.md` の内容がこの指示の「決定」と矛盾する（同じ趣旨なら止めず足す）
* 「タイトル戦に掲載されている大会」を title/ の生成のデータから決められない（大会の一覧の所在が分からない）。段の判定ができないだけなら止まらず、載せない側に倒して仮置き
* 同じ目的（新トップの生成・`top/`）の未マージ work/ ブランチや open issue がある
* 未マージの work/ ブランチが `scripts/regenerate.py`・`_redirects`・`.github/workflows/check-assets.yml`・`.github/workflows/update-live-channel.yml`・`navbar.js`・`docs/new-page-checklist.md` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。navbar.js の新しい形の対象パスの連なりに行を足す変更どうし〈work/1029-lgn-page・work/1101-prp-pub など〉は、両方の行を残せば両立するので止まらない。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* 共有の部品の変更で、top/ 以外のページの生成物に差分が出た（見込みは0件。出たら一覧を書いて止まる）
* #101・#231・#286・#287 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 93f8976c）: https://github.com/retroeater/mj-logs/tree/main/guide/93f8976c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/93f8976c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad26c534.md
