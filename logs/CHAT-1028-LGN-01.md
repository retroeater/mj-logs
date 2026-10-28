# CHAT-1028-LGN-01

- 着手日時: 2026-10-28
- 対象issue: なし（起票せずに止まった。同じ目的・近い目的として #378・#255 を確認）
- ブランチ: work/1028-lgn
- 着手時HEAD: c6bd0e3

## 指示

【Claude作成】Claude Code 向け指示：「ログ」を log/ に作り直す（未公開で作業ブランチに入れ、プレビューまで）
Chat-Ref: CHAT-1028-LGN-01
マージ: 判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lgn の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-lgn を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lgn origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「ログ」（飲食店の投稿、今は `resource_logs.html`）を、houou/ の個人成績と同じ形の **log/** に作り直す。トップの検索を主な導線にし、表・時系列の一覧はやめる。
自律開発の形で進める。開発中の不明点は仮置きして最後まで作り、プレビューで平野さんが確かめられるところまでで止まる。段1（docs/new-page-checklist.md「段1 未公開で本番に入れる」）の形で作り、公開（段2）は含めない。

### 決定（2026-10-28、平野さん）
- 「ログ」を log/ に移す。デザイン・UI は最近作り直したページに合わせる。参考にする画面は **houou/ の個人成績**（`houou/players/`。選手を探す部品・幅 400px など。docs/notes/houou-top.md）
- トップページの検索が主な導線。表形式・時系列の一覧は廃止する
- トップに、カテゴリごとに1行ずつ数件の店を並べる。カテゴリは「最近追加された店」「掲載回数の多い店」など
- 「よく見られている店」は**外し**、データから出せる別のカテゴリに置き換える（店ごとの閲覧数は取らない）
- 「掲載回数の多い店」の名寄せのキーは **place_id だけ**。place_id が空の行は数えない（検索には出す）
- 位置からの検索は、今回は**駅名から探す**だけを入れる。**現在地から探すは今回入れない**（別の issue にする）。2026-10-18 の「座標・駅名検索・現在地検索・マイマップはいったん保留」のうち、駅名検索だけを再開する

### 前提（チャット側。平野さんの決定ではない）
- 決定の正は docs/decisions/operations.md の #367・LOG の節（要確認）。`?tag=` は残すと決めてある（同節、要確認）
- 今の生成は `scripts/generate_resource_logs.py`。3層のスプレッドシートの【2】自動変換後を【3】手動補正で直した行のうち、判定が「飲食店」で「表示」が Y の行を出す。【2】【3】には「place_id」「駅」「駅の候補」の列があり、緯度・経度の列は無い（チャット側が 2026-10-28 に `origin/cloudflare` のコードを読んだ範囲。要確認）
- `?tag=` は今、絞り込み欄の初期値（検索語）として使われている（`generate_resource_logs.py` の `TableConfig`、要確認）
- 以下はチャット側の案で、仮置きとして進めてよい（違う形がよければ変えてよい。変えたものと理由は報告の「仮置きの一覧」に書く）
  - 置き換えのカテゴリ: 「2人とも行った店」（平野良栄と谷岡育夫の両方の投稿がある place_id）
  - 「最近追加された店」: place_id ごとの最初の投稿の日付が新しい順。「掲載回数の多い店」: place_id ごとの投稿の数の多い順
  - 駅名から探す: 「駅の候補」（店から 800m 内の駅、複数）に当てる。表示する駅は今どおり「駅」の1つ
  - 検索の対象: 店名・メニュー・駅・人の名前。人での絞り込み（今の名前のセレクトボックスに当たるもの）は残す
  - 結果の形: 店ごとにまとめ、その店の投稿（写真・日付・メニュー・人・X の投稿へのリンク）を並べる。place_id が空の行は1投稿を1件として出す
  - `log/?tag=<語>` は検索語の初期値にする（段2で `resource_logs.html?tag=` からの転送先にするため）
  - 外部ドメインへの依存・地図は足さない。`_headers` の `Permissions-Policy`（今は `geolocation=()`）と CSP は変えない
  - `resource_logs.html` とメニュー・転送はこの指示では変えない（段2の公開の issue で、`resource_logs.html` から `log/` への 301・メニューの位置と名前を決める）
- シートはセッションから読めない（鍵は Actions にだけある）。生成と確かめは `regenerate-page.yml` を作業ブランチの ref で手動実行して行う（docs/notes/github-actions.md「regenerate-page.yml」）

## 手順
1. 同じ目的の issue（クローズ済みを含む。「ログ」「resource_logs」「log/」などで検索）と、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、同じ目的のものがあれば止まる。無ければ次の3件を起票する（ラベルは既存の issue の形に合わせる）。
   - 作る issue: 「log/ を作る」（本文に上の「決定」と、関係する #367・#636。#367 には触らない。LOG 系列のチャットが閉じる）
   - 公開の issue: docs/new-page-checklist.md「段2」を写し、公開の条件・メニューの位置・`resource_logs.html` の 301 転送と `?tag=` の引き継ぎ・「作る issue を待つ」を書く。作る issue に番号をコメントする
   - 現在地から探す issue: 状況は保留。本文に、座標の列が無いこと（変換の Places の応答に店の座標はあるが、シートに書いていない。`scripts/convert_x_posts.py` の `PLACE_FIELDS`、要確認）、`Permissions-Policy` の `geolocation=()`、既存の行は place_id から座標を引き直す必要があることを書く
2. 作る issue に着手中のコメントを残し、log/ を作る。docs/new-page-checklist.md の「作る段の確かめ」と「段1」に従う（noindex・navbar／サイトマップ／`llms.txt` に載せない・既存のページからリンクしない・`_redirects` に houou/ と同じ形の行・`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」）。houou/ の部品・CSS・`lib/` の共通関数で足りるものは使い、仕様は `docs/notes/<名前>.md` に houou-top.md の形で書く。検査（`scripts/tests/`）を残す。X・Claude・Places の API は呼ばない（費用がかかり、この作業には要らないため）
3. `regenerate-page.yml` を作業ブランチの ref で、まず入力 `check` で、次に入力無しで手動実行し、生成物を作業ブランチにコミットさせる。docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで見るべき URL（トップ・`?tag=tanilog`・駅名で探した結果・人で絞った結果）と、仮置きの一覧（上の前提の案を含め、作る途中で決めたこと全部）を報告に書いて止まる

## 止まる条件
- 同じ目的の issue・未マージのブランチがある、作る issue に他セッションの着手中のコメントがある
- 前提の列（place_id・駅・駅の候補・判定・表示・投稿ID）が【2】【3】に無い、または生成が読む行の条件が前提と違う
- 生成した log/ の投稿の件数が、同じ実行で生成した `resource_logs.html` の行数と一致しない（一致しなければ両方の件数と、差の投稿IDを全件書いて止まる）
- 作るのに、新しい依存（パッケージ・外部 API・新しいワークフロー）・`_headers` の変更・シートへの書き込み・`convert_x_posts.py` の変更が要る
- 未マージの work/ ブランチが、この作業で変える共通の部品（`style.css`・`assets/` の houou/ の JS・`scripts/lib/`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（プレビューを平野さんが確かめるまで cloudflare へ入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LGN-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LGN-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `CHAT-1028-LGN-01` のコミット無し。識別子 `LGN` のコミット・ログ無し（`git fetch --unshallow` の後に全ブランチで確認）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し。CLAUDE.md は起動時のものと `origin/cloudflare` で同じ
- 0（指示欄の末尾）: ログの「指示」欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 作業ブランチ: `work/1028-lgn` はローカル・リモートとも無かったので `git checkout -b work/1028-lgn origin/cloudflare`（c6bd0e3）で作った
- 前提の確かめ（コードを読んだ範囲。シートは読んでいない）:
  - `scripts/convert_x_posts.py` の `OUT_HEADER`（【2】の見出し）に「place_id」「駅」「駅の候補」「判定」「表示」「投稿ID」がある。緯度・経度の列は無い。`PLACE_FIELDS` は `places.id,places.displayName,places.formattedAddress,places.location`（店の座標は応答にあるがシートに書いていない）。前提どおり
  - `scripts/generate_resource_logs.py` の行の条件は「【2】を【3】の空でないセルで上書き → 判定が飲食店・表示が Y」。前提どおり。今は place_id・駅の候補を読んでいない
  - `?tag=` は `TableConfig(filter_param="tag")` で絞り込み欄の初期値。docs/decisions/operations.md の 2026-10-18（CHAT-1018-LOG-02）の節に「`?tag=` は、絞り込み欄の初期値として残す」。前提どおり
  - 名前のセレクトボックスの選択肢は `NAME_OPTIONS = ["石立岳大", "谷岡育夫", "平野良栄"]`（3人。前提の「2人とも行った店」は平野良栄・谷岡育夫の2人）
  - 【3】の見出しはコードからは分からない（`records()` が見出しの名前で読むため、【3】に無い列は【2】の値が使われる）
- 手順1 の検索（issue はクローズ済みを含む意味検索3回: 「ログ resource_logs log/ 飲食店 ページ 作り直し」「飲食店 駅名 検索 店 place_id 現在地」「ログ トップ 検索 カテゴリ 最近追加 掲載回数 店ごと」）:
  - **#378「「ログ」で位置情報から近い店を探せるようにする」（open、状況: 保留、親 #296 の sub-issue、対象: resource_logs）**: 利用者の位置情報（GPS）で直線距離の近い店を探す。起票する予定の「現在地から探す issue」と同じ目的
  - #255「resource_logs を分割する」（open）: resource_logs（2.1MB）の DOM 要素数の問題で、年別・選手別に分ける案（判断待ち）。目的は表示速度で log/ とは違うが、log/ が置き換えれば解決の道が変わる（jpml_titles・video_live・saikyo_results は「構成変更で置き換える」として対象から外した前例がある）
  - #636（open、【2】の要確認の行を【3】で直す）: 関係する issue として本文に挙げる予定のもの。同じ目的ではない
  - 「log/ を作る」「公開」と同じ目的の issue は無かった
- 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）: 1009-swp-526・1011-swp-hnd・1011-swp-nav（ログのみ）、1015-skd-notes（ログのみ）、1017-lvp（live/ の作り直し、935ファイル）、1026-aic（ログと4ファイル）。同じ目的のものは無い。共通の部品（`style.css` など）の重なりは、手順2 に入っていないので見ていない
- 止まる条件「同じ目的の issue がある」（#378）に当たったので、issue の起票（3件）・着手中のコメント・log/ の実装・ワークフローの実行はしていない。決定節は docs/decisions/operations.md に足した

## 報告

- 状態: 判断待ち
- ブランチ: work/1028-lgn
- ログ: https://github.com/retroeater/mj/blob/work/1028-lgn/docs/logs/CHAT-1028-LGN-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lgn
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #378・#255（同じ目的・近い目的として見つけたもの。触っていない）。起票は無し
- 判断が必要なこと:
  - 手順1 の検索で、「現在地から探す issue」と同じ目的の #378（open、状況: 保留、親 #296）が見つかったので止まった。案: 新しく起票せず #378 を使い、指示文の本文に書く内容（座標の列が無いこと・`PLACE_FIELDS`・`geolocation=()`・既存の行は place_id から座標を引き直す要ること）を #378 にコメントで足す。#378 は新サイト送り（#296）なので、扱いを変えるなら #296 の本文「新サイト送りにするとき・やめるとき」に従う
  - #255（resource_logs を分割する、open）: log/ が resource_logs.html を置き換えると、分割の論点は log/ の公開（段2）の 301 で片付く見込み。対象外にする・log/ の公開で閉じる・そのまま残す、のどれにするか
  - 上の2つが決まれば、残り（「log/ を作る」「公開」の2件の起票と log/ の実装、または3件）は新しい Chat-Ref で貼り直す（この Chat-Ref のコミットがあるので同じ番号では再開できない、docs/notes/branch-operations.md「作業を再開するとき」）。同じ作業ブランチ work/1028-lgn を続けて使える
- 未確認の項目: 【3】手動補正の見出しに「place_id」「駅の候補」があるか（シートを読めないため。【3】に無くても【2】の値で読める）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1242ae11）: https://github.com/retroeater/mj-logs/tree/main/guide/1242ae11

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1242ae11/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c6bd0e38.md
