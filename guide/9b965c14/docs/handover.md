# 引継ぎメモ

新しい会話でこのプロジェクトを再開するときに、最初に読む文書。
**このファイルを読めば、それまでの経緯を知らなくても作業を再開できる**ことを目的にしている。

**この文書は現状・ルール・次にやることだけを書く。** 実装記録は `docs/notes/`、issue単位の経緯は GitHub Issues、過去の履歴は
`docs/notes/handover-archive-2026.md`。容量の上限と退避方法は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。

最終更新: 2026-10-16

- **鳳凰戦の新ページ `houou/` を公開した（#540、旧4ページは 301。後始末は #582）**
- **5章の期日の表を消した。期日はカレンダー「ryoei.pro」が正**
- **アクセス解析の導入の検討（#550）を「次にやること」に足した**

---

## 0. 新しい会話の始め方

会話開始時に読むのは `docs/handover.md` のみ（平野さんが毎回定型文を貼る前提にしない）。
**マージ・ブランチ操作・ルール追記を行う（指示する）前と会話が長くなったときは、CLAUDE.md の該当節（「ブランチ運用」「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」）も読み直す**（並行セッションが途中でも更新する、#313）。

**チャット側（claude.ai）は `docs/notes/chat-side-operations.md` を読む**（#294）。

**大きな作業の区切りごとに新しい会話を始める**とよい（長い会話は1回あたりのコストが上がる）。

---

## 1. このプロジェクトは何か

平野良栄（日本プロ麻雀連盟のプロ雀士・理事）の個人サイト `ryoei.pro` の改善。

中心となるコンテンツは**日本プロ麻雀連盟のプロ雀士1,100名超のデータベース**と、
鳳凰戦・女流桜花などの成績記録。

### 経緯

GitHub Pages からの移行の相談に始まり、Cloudflare への移行の中で50件以上の改善を行った。
2026年9月9日にドメイン切替が完了し、**現在は Cloudflare Workers で配信中**。

---

## 2. いまの構成

### 配信

| 項目 | 内容 |
|---|---|
| リポジトリ | `retroeater/mj`。**private**（#211） |
| 本番 | Cloudflare Workers（静的アセット配信）。`cloudflare` ブランチ |
| ドメイン | `ryoei.pro` / `www.ryoei.pro`。DNS・レジストラともCloudflare |
| 旧環境 | GitHub Pages。**2026-09-21 に無効化済み**（#84）。`gh-pages` ブランチは履歴として残す |
| ビルド | **なし**。静的ファイルをそのまま配信する |
| プラン | **Cloudflare Pro**（2026年9月9日〜）。$25/月 |

`wrangler.jsonc` の `assets.directory` がリポジトリ全体（`./`）を指すため、
公開したくないファイルは `.assetsignore` に列挙している（追加時のルールは CLAUDE.md「方針」、#133）。

`html_handling`・`_redirects` の先頭行の規則は CLAUDE.md「禁止事項」。canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
`_headers` はセキュリティヘッダ5件とキャッシュ制御（#92）。設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」

**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/session-network.md`（#328）。

### ページ構成

系統は `index.html`（テンプレート由来）・ビルド時生成（型A/A'/D と、`wayhome/`・`saikyo/`・`title/`・`live/`・`books/`・`houou/`・`ouka/`・`jwrc/` などのサブディレクトリ）・
静的なページ の3つ（Google Charts 依存の旧6ページと型C の2ページは #582 で消した）。**件数の正は `python3 scripts/regenerate.py --list`。**
ページの一覧（ページ数・生成スクリプト・公開状態）は `docs/notes/static-generation.md`「ページの一覧」。
**`books/`は2026-09-22開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。`docs/notes/books-freeze.md`。**

### データの流れ

選手データや成績はすべて**Googleスプレッドシート**にある（生成スクリプトが読むブックは7つ。ほかに連盟員名簿のブック1つを `generate_jpml_pros.py`〈英字の姓名・所属、#536〉・`check_meibo.py`・`sync_birthday_calendar.py` が読む）。

- ビルド時生成のページの仕組みは CLAUDE.md「データの流れ」。出力がディレクトリになるページの出力先の正は `scripts/regenerate.py` の `OUTPUT_OVERRIDES`
- `jpml_pros`のYouTubeアイコンだけはYouTube Data APIから取る（#3。取得条件は `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- `saikyo_pages`は生成する環境で選手写真の結果が揺らぐ。**本番はActionsの生成が正**（`docs/notes/saikyo-page-design.md`「7. 選手写真の更新」）

**スプレッドシートを直しただけでは、ビルド時生成のページには反映されない。** 即時に反映したいときは
`regenerate-page.yml` を`workflow_dispatch`で手動実行する（`target_page`にページ名、または`all`）。
週次（毎週月曜05:37 JST、#103）で`all`が走る。最強戦（saikyo/）は毎朝、写真のリンク切れ検知の後にも再生成する（`regenerate-saikyo.yml`、#537）。セッション内でも `python3 scripts/regenerate.py <ページ名>` で生成できる。

### 自動化

ワークフローの一覧（実行の契機と内容）は `docs/notes/github-actions.md`「ワークフローの一覧」。正は `.github/workflows/`。
**手動実行の前に同「ワークフローを手動実行するとき」を読む**（古い作業ブランチから実行すると、そのブランチの状態で生成物や常設issueが上書きされる）。

---

## 3. 作業の進め方

### 役割分担

| 場所 | 担当する作業 |
|---|---|
| **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案・指示文の作成 |
| **Claude Code** | ファイルの編集、issue操作、コミット・push。コミットの到達確認・差分・マージ判定も行い、結果をログで報告する |

Claude Code の実行環境は Codespace（`/workspaces/mj` で `claude`、`gh` 認証済み。Rebuild・gh の認証は `docs/notes/session-network.md`）かクラウドセッション（`docs/notes/cloud-sessions.md`）。
Chat-Ref・並行作業の規則は CLAUDE.md「Chat-Ref」「ブランチ運用」、決まるまでの経緯は archive。

### skill と hook

skill は `.claude/skills/`（`/grill-me`・`/grill-with-docs` など）、git の危険な操作を止める hook は `.claude/hooks/mj-git-guard.py` に置く。
hook の判定は deny（実行させない）だけで、確認（ask）は出さない。入れ方・更新・判定一覧は `docs/notes/skills.md`。

### タスク管理

**GitHub Issues** で管理する（Projects のボードは 2026-10-03 から使わない。着手中コメントなどの規則は CLAUDE.md「issueの着手ルール」、#157）。

- ラベルは3系統。コロンの後に半角空白が入る（例: `分野: 整理・保守`。正は`gh label list`）:
  `状況:`（対応中、保留、待ち。未着手はラベルなし）、
  `分野:`（SEO/AIO、パフォーマンス、自動化、セキュリティ、整理・保守、インフラ、UI/UX）、
  `対象:`（ページ名。jpml_pros、index、全ページ など）
- 完了分もcloseした状態で残す（判断の経緯を後から追えるように）。エクスポートファイルは廃止（#210）
- **新サイト全体の親 issue は #296（sub-issue 21件）。** #101 はトップページの作り直しに限る。新サイト送りの手順は #296 の本文
  「新サイト送りにするとき・やめるとき」
- ユーザ登録（サーバ側のアカウント）が前提の機能は #394（保留）に blocked by で依存させる
- 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）は
  #304 に集約し、実施ごとにコメントを残す

---

## 4. 押さえておくべき方針

### 現行サイトに作り込みすぎない

**新サイトを別途新規構築する方針**が決まっている（`docs/new-site-design.md`）。
現行サイトはいずれ役目を終えるため、大きな投資は避ける。これまでの判断:

- 構造化データ（#13）は現行サイトでは見送り、新サイトで対応
- 共有ボタン: 新サイトの選手個別ページは #82（新サイト送り）。現行サイトは live/ に共通の部品（#409。`scripts/lib/share.py`・`assets/share.js`・`style.css`）。title/・wayhome/（帰り道）・saikyo/ の共有は外した（saikyo/ の対局ごとの共有は #549 で廃止）。見直しは #530。books/ は凍結中で旧実装
- Astroへの移行（#20/#21）は新サイト構築時に判断。現行サイトの残作業はPythonで進める

**新サイトの第一弾は index.html（#101）。** 他ページと構造が独立し依存が最も少ないため（Astro の試作対象も index.html に変え、#20 はクローズ）。

ボトルネックは #21（Astroへの移行を検討する）の判断。
ここが保留のままだと新サイトの着手ができない。

### 外部ドメインへの依存を増やさない

方針は CLAUDE.md「判断・作業の原則」（CSP〈#9〉のため）。残る外部依存は Cloudflare Web Analytics（`static.cloudflareinsights.com`。
CSPでは `script-src` にのみ必要で、送信先は自ドメインの `/cdn-cgi/rum`）・画像12ドメインと `image.p-c2-x.abema-tv.com`（最強戦の卓のページの ABEMA のサムネイル〈#502〉と、年度・対局・卓の og:image・回の無い卓のヒーローの作品の画像〈#549〉。やむをえない依存として 2026-10-11 に足した）。
Google Charts（`www.gstatic.com` / `docs.google.com`）は 2026-10-19 に依存するページが0になった（#582）。詳細は `docs/notes/handover-archive-2026.md`「外部ドメイン依存の詳細」と
`docs/notes/site-findings.md`（画像ドメインの実測）、#9 でCSPの`img-src`を書くときの指針は #9 のコメント。

### 表の色とアクセシビリティ

`.mj-table` の文字は WCAG AAA（7:1）、並べ替えボタンのフォーカス枠は 1.4.11（3:1）。縞と見出しの背景は `#f2f2f2`（#345。AAA の上限は `#e4e4e4`、境界はリンク色 `#14459b`）。
新サイトでも引き継ぐ。値と理由は `style.css` のコメント、経緯は `docs/notes/site-findings.md`「リンクの配色」。

### gh-pages ブランチは触らない

移行前の GitHub Pages の内容を、移行前の実装の記録として凍結したまま残す（更新しない）。GitHub Pages は無効化済みで配信されていない（#84）。

---

## 5. 次にやること

**次の会話の順番（2026-10-09）:** (1) 待ち: #388（平野さんが「映画」のタブを作ってから）・#389（平野さんの校正した書籍の一覧の整備）・#523（WRC 2大会の改称の残り。手書きのページ・メタの「JPML WRC」と、過去の期の表記を新名で出すかの決定。残りは本文の「残り」の節）。
(2) 期日待ちはカレンダーの順。決定は `docs/decisions/features.md`・`operations.md`。

**期日のあるタスクはカレンダー「ryoei.pro」が正（件名【R#番号】…、作るのはチャット側）。handover に日付は書かない。**

GitHub Issues（Open）に全件あるが、着手可能な主なものは以下。

| # | 内容 | 備考 |
|---|---|---|
| **#7** | Google Charts依存の解消 | 依存するページは 2026-10-19 に0（#582）。閉じるかは平野さんの判断。下の「#7 の進め方」 |
| #582 | 鳳凰戦の旧4ページ（`houou_*.html`）の生成の停止とファイルの削除 | houou/ は #540 で公開済み（2026-10-16、旧4ページは 301）。旧4ページへの流入が 301 に移ったことを Search Console で見てから。本文にチェックリスト |
| #141 | ランキング3ページを Python の生成に移す | 2026-10-05 に移植と決定。#371・#228・#234 と、`select#selectbox` のラベル・選択と同時の遷移（#180 の残り）はこの移植の中で扱う |
| #486 | Bing の Recommendations | h1 は8ページに付けた（2026-10-06）。残りのランキング3ページは #141 の移植で付ける。title の長さ（短いページの共通の末尾）は #5 |
| #9 | CSP設定 | #7の後にやると強いポリシーが書ける |
| #550 | アクセス解析の導入の検討（利用者の画面幅・Cloudflare Pro の要否） | `docs/notes/design.md` の最小の画面幅（360px、2026-10-18）を見直す材料。外部ドメインへの依存（#9）に注意 |
| #4 | SentryでJSエラー検知 | 外部サービスの登録が必要 |
| #96 | カレンダーの参照・更新を自動化 | スコープ未定。決めるべき項目が4つある |
| #390 | 道場部ゲストの書き込み済みの月の自動更新（本番で未稼働。サービスアカウントの削除の権限も未確認） | 随時: 差し替えで #426 に「自動で更新しました」か「自動では更新していません」が出たら、カレンダーの当日以降が画像と合っているかを確かめる |
| #186 | アクセシビリティの実機での通し確認 | 静的レビュー（#178〜#185）の残り。チェックリストは `docs/notes/a11y-manual-check.md`。**Lighthouse のスコアを到達点として扱わない**（根拠は #186） |
| #408 | タイトル戦の対局日を確定させる | `YYYY-XX-XX` の大半は決勝ライブが無く【2】【3】から直せない（外の資料が要る）。書式は `docs/notes/title-pages.md`「日付列の書式」 |
| #490 | /live 層2の残りの規則と掲載範囲 | 残りは U1〜U4（ライブの無い組の対局日・紅龍戦のステージの並び・件数の少ない列・「プレイヤー解説：」）・候補の判別・公開版の掲載・複数卓・達人戦／昇龍戦／鳳匠戦の扱い（#437・#477 から集約。済んだものと件数は #490 の本文） |
| #475 | /live の未登録の名前（2026-10-03 に0名） | 常設。毎日の取り込みが増減の日だけコメントする。出たら実在の人は「連盟プロ以外」、誤記は「別名」に `訂正` で平野さんが登録し、概要欄の読み違いは層2の規則で直す（#490） |
| — | 現行サイトで小さく作れるもの | 次は #388・#389（上の「待ち」）。#425 も現行サイトで作る（#501・#502）。#366 は載せ方が未決、#378 は新サイト（#296）送り。未決: #365・#367 のデータを誰がいつ入力するか。決定は `docs/decisions/features.md` |

### #7 の進め方（検討済み）

**現状**: Google Charts 依存のページは0（成績・ランキングの旧6ページは houou/・ouka/・jwrc/ へ移り、#582 で消した、2026-10-19）。
共通部品は出そろっている（`docs/notes/static-generation.md`、型別の進捗表は `docs/notes/handover-archive-2026.md`「#7 の型別の進捗表」）。
移行しても速くはならず、目的は外部依存とインラインハンドラの解消（static-generation.md「#7 の期待値」）。
テーブル描画ライブラリの選定（#95）は #7 の前提から外し、新サイトのスタック（#21）と併せて検討する。型Aの表の方針は static-generation.md「型Aの表の方針」。

---

## 6. 関連文書

完了済み作業の実装記録・調査結果は `docs/notes/` にある（handover には結論と参照先だけ）。`docs/notes/` 以下は下の一覧が正（`ls docs/notes/` にあって載っていないものは、開く場面を1行で足す）。

| ファイル | 開く場面 |
|---|---|
| `CLAUDE.md` | 作業のルール（ブランチ運用・Chat-Ref・作業ログ）。Claude Code がセッション開始時に読む |
| `docs/instruction-template.md` | チャット側が指示文を書くときの雛形 |
| `docs/new-page-checklist.md` | 新しいページを作る・公開する前（未公開で入れ、公開は別 issue、#243） |
| `docs/decisions/` | 平野さんの決定の記録（分野ごと。書き方は `README.md`） |
| `docs/new-site-design.md` | 新サイト（#296）の設計。中断中で再開手順まである |
| `docs/astro-migration-study.md` | 新サイトのスタック（#21）の判断 |
| `docs/lighthouse-baseline.md` | パフォーマンスの改善前後の比較（ページ別スコアの基準値） |
| `docs/gsc/` | Search Console の数値（#142） |
| `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲（チャット側の check-run の確かめ方を含む）・設定の照合（Chrome） |
| `docs/notes/redirects.md` | 転送（リダイレクト）の一覧と四半期の棚卸しの手順・流入の測り方（#568）。旧 URL の 301 やダッシュボードのルールを足す・消すとき |
| `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code on the web）での CLAUDE.md の読み替え（ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
| `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方・場面ごとに読む文書の表もここ） |
| `docs/notes/glossary.md` | 用語集（チャット側・Claude Code・docs で使う語。ワークフロー・定期実行・本実行・スプレッドシートなど） |
| `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前。指示の種類ごとの確かめ・期日とカレンダー・外部サービスの設定を頼むとき・ほかのセッションへの共有 |
| `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
| `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前（`.claude/` を変える指示を書く前も） |
| `docs/notes/branch-operations.md` | ブランチの削除・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md） |
| `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・メンテナンス用スクリプト、#7 の残り |
| `docs/notes/github-actions.md` | GitHub Actions のワークフローの一覧・`regenerate-page.yml`・手動実行と変更の前・チャット側の扱い（実行結果の確かめ方・失敗通知） |
| `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
| `docs/notes/scheduler-worker.md` | 予約実行を起動する Worker（`mj-scheduler`、#504）。起動の表の直し方・通知（#506）・トークンの期限と差し替え・ダッシュボードの設定と作り直す手順・未確認の点 |
| `docs/notes/dojo-guest-calendar.md` | 道場部ゲストの告知画像の取り込みと平野さん側の設定（#390） |
| `docs/notes/ogp.md` | OGP 画像・`og:title`・SNS のカード表示 |
| `docs/notes/page-announcement.md` | 新しいページの X での告知の型（投稿文・告知動画・進め方） |
| `docs/notes/video-wayhome.md` | 「帰り道」の一覧・個別ページ、新しい回の追加 |
| `docs/notes/video-mtsuku.md` | 「Mつく」の一覧 `/mtsuku/`・各話（帰り道と同じ形、2026-10-15 に公開、#570） |
| `docs/notes/video-study.md` | 「女流勉強会」の一覧 `/study/`・各話（帰り道・Mつくと同じ形、未公開、#575・公開は #576） |
| `docs/notes/site-findings.md` | 現行サイトの実測値（パフォーマンス・画像ドメイン・SEO・配色） |
| `docs/notes/design.md` | 見た目の既決の値（色・文字・寸法・部品・濃色の例外）。ページや部品を作る・直す前。チャット側の決め方（比較ページ・見本・コントラスト比） |
| `docs/notes/a11y-manual-check.md` | アクセシビリティの実機確認（#186） |
| `docs/notes/mj-lead.md` | 表・グラフのあるページの説明文（`.mj-lead`、#312） |
| `docs/notes/saikyo-page-design.md` | 最強戦（`saikyo/`）と選手写真の更新 |
| `docs/notes/abema-book.md` | ABEMA ブック（最強戦の卓ごとの ABEMA の回、#425）。サムネイル・「▶ 再生」の元データを直す・確かめる前 |
| `docs/notes/title-pages.md` | タイトル戦の新構成（`title/`、#222） |
| `docs/notes/houou-race.md` | 順位変動（`houou/race/`・`ouka/race/`・`jwrc/race/` の土台、#507） |
| `docs/notes/houou-top.md` | 鳳凰戦の新ページ `houou/` の仕様（トップ＋検索・ランキング・リーグ推移・順位変動、#518） |
| `docs/notes/ouka-top.md` | 女流桜花の新ページ `ouka/` の houou/ との違い（#519。公開は #591） |
| `docs/notes/jwrc-top.md` | WRC リーグの新ページ `jwrc/` の houou/・ouka/ との違い（#594。公開は #599） |
| `docs/notes/league-pages-compare.md` | houou/・ouka/・jwrc/ の3大会の差（意図した差と、公開でそろえる差）。大会の設定や共通の部品を変える前 |
| `docs/notes/ask-worker.md` | データ・コンシェルジュの Worker（`mj-ask`、#542） |
| `docs/notes/live-page-design.md` | 放送対局ページ（`live/`、#346）。掲載範囲の拡大は「3-5」（残りは #490） |
| `docs/notes/birthday-calendar.md` | 誕生日カレンダーの同期（#379） |
| `docs/notes/yotei-sheet.md` | 連盟の予定表の取り込みと放送対局の公開カレンダーへの同期（#448） |
| `docs/notes/live-channel-write.md` | /live の3層（【1】【2】【3】）の書き込み・毎日の取り込み・平野さんの入力の手順（#438）。チャット側が /live の指示を書く前 |
| `docs/notes/sns-book.md` | SNS ブック（選手の SNS の ID・画像の URL、#514）。ID の直し・写真の検知 |
| `docs/notes/books-freeze.md` | **書籍ページの開発凍結（2026-09-22、#97）。決定・凍結時点の状態・楽天データの期限・再開手順** |
| `docs/notes/books-calendar.md` | 書籍の発売日のカレンダー同期の仕組み（#97、凍結時点の記録） |
| `docs/notes/books-covers.md` | 書籍の書影（楽天ブックス書籍検索API）の仕組みと規約（#97、凍結時点の記録） |
| `docs/notes/decisions-2026-09-13-review.md` | #219〜#289 の重複・統合の判断 |
| `docs/notes/handover-archive-2026.md` | 過去の事故・経緯（作業の前提にはしない） |
| `docs/notes/security.md` | セキュリティ点検の観点・道具・手順（結果は親 #598。CSP #9 の後に再点検） |
| `docs/notes/sheets-reader.md` | スプレッドシートを非公開にするための読み取り用サービスアカウントの用意（#557） |
