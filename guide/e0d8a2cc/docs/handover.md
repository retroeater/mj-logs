# 引継ぎメモ

新しい会話でこのプロジェクトを再開するときに、最初に読む文書。
**このファイルを読めば、それまでの経緯を知らなくても作業を再開できる**ことを目的にしている。

**この文書は現状・ルール・次にやることだけを書く。** 完了した作業の実装記録は
`docs/notes/`、issue単位の経緯は GitHub Issues に置く。

**容量の上限と、超えそうなときの退避方法**は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。

過去の履歴は `docs/notes/handover-archive-2026.md`。

最終更新: 2026-09-26

- **スマホのチャットから作業ログとガイド文書を読めるようにした**（public の `retroeater/mj-logs`、#440。読み方は3章「チャット側のアクセス手段」）
- **`/titles` 統合（#435）は中止**（情報量過多で見つけにくくなるため）。`/title`・`/live` は別ページのまま開発継続。
  検討中の成果は `/live` の掲載範囲拡大（#437、親 #346）に引き継いだ（`docs/notes/live-page-design.md`「3-5」）
- **最強戦（`saikyo/`）を一般公開した**（#348・#319 クローズ、`docs/notes/saikyo-page-design.md`）。残件は #350・#425・#382・#383・#428・#410・#412

---

## 0. 新しい会話の始め方

会話開始時に読むのは `docs/handover.md` のみ。
平野さんが毎回定型文を貼る前提にしない。
**ただし、マージ・ブランチ操作・ルール追記を行う（チャット側は指示する）前と、会話が長くなったときは、
CLAUDE.md の該当節（「ブランチ運用」「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」）も読み直すこと。**
複数セッションが並行しており、会話の途中でも両ファイルは更新される（#313）。

issueの状況（Open/Closedの別、本文・コメント）はGitHubのIssues一覧ページで
確認する。Claude Codeのセッションは `gh issue list` / `gh issue view`（クラウドセッションは GitHub MCP）を使う。
**チャット側（claude.ai）は、作業ログとガイド文書を public の `retroeater/mj-logs` で読む。**
入口は平野さんが送る「ログ（公開）」の行で、ガイド文書はそのログの末尾のリンク（`guide/<SHA>/`）から読む。
新しい会話の始めは、前回の最後の「ログ（公開）」の行を送ってもらう。
issue は private のままなので、チャット側が要る issue の状態は Claude Code に確かめさせてログに書かせる。
PC では Claude for Chrome で GitHub の issue・ファイルを直接読むこともできる（#210）。
**チャット側は指示文を書く前とブラウザを使う前に `docs/notes/chat-side-operations.md` を読む**
（ログの読み方・ブラウザ操作の回数・代わりの手段・指示文を書くときの注意）。写し方は `docs/notes/cloud-sessions.md`「作業ログ」。
**チャット側が Claude Code への指示文を書くときは `docs/instruction-template.md` を使う**（#294）。

会話が長くなると1回あたりのコストが上がるため、
**大きな作業の区切りごとに新しい会話を始める**とよい。

---

## 1. このプロジェクトは何か

平野良栄（日本プロ麻雀連盟のプロ雀士・理事）の個人サイト `ryoei.pro` の改善。

中心となるコンテンツは**日本プロ麻雀連盟のプロ雀士1,100名超のデータベース**と、
鳳凰戦・女流桜花などの成績記録。事業上の目的は、企業案件（出演・タイアップ・
イベント）の入口として機能させること。

### 経緯

GitHub Pages からの移行の相談に始まり、Cloudflare への移行の中で50件以上の改善を行った。
2026年9月9日にドメイン切替が完了し、**現在は Cloudflare Workers で配信中**。

---

## 2. いまの構成

### 配信

| 項目 | 内容 |
|---|---|
| リポジトリ | `retroeater/mj`。**private**（2026-09-13〜、#211）。チャット側は作業ログとガイド文書を public の `retroeater/mj-logs` で読む（3章「チャット側のアクセス手段」） |
| 本番 | Cloudflare Workers（静的アセット配信）。`cloudflare` ブランチ |
| ドメイン | `ryoei.pro` / `www.ryoei.pro`。DNS・レジストラともCloudflare |
| 旧環境 | GitHub Pages。**2026-09-21 に無効化済み**（#84）。`gh-pages` ブランチは履歴として残す |
| ビルド | **なし**。静的ファイルをそのまま配信する |
| プラン | **Cloudflare Pro**（2026年9月9日〜）。$25/月 |

`wrangler.jsonc` の `assets.directory` がリポジトリ全体（`./`）を指すため、
公開したくないファイルは `.assetsignore` に列挙している（追加時のルールは CLAUDE.md「方針」、#133）。

`html_handling: "none"`（#89）のため、`_redirects` 先頭の `/  /index.html  200` を消すとトップページが404になる。
canonical は付けない（#113）。例外は `wayhome/` の38枚で、URL変種を持たないため canonical を持つ（#162）。`_headers` はセキュリティヘッダ5件とキャッシュ制御（#92）を持つ。
設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」

### ページ構成

系統は `index.html`（テンプレート由来）・ビルド時生成（型A/A'/C/D と、`wayhome/`・`saikyo/`・`title/`・`live/`・`books/` のサブディレクトリ）・
Google Charts依存（#7の対象）・静的なページ の4つ。**件数の正は `python3 scripts/regenerate.py --list`。**
ページの一覧（ページ数・生成スクリプト・公開状態）は `docs/notes/static-generation.md`「ページの一覧」。
**`books/`は2026-09-22開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。`docs/notes/books-freeze.md`。**

### データの流れ

選手データや成績はすべて**Googleスプレッドシート**にある（生成スクリプトが読むのは6冊。ほかに連盟員名簿の1冊を `check_meibo.py`・`sync_birthday_calendar.py` が読む）。

- ビルド時生成のページ … `scripts/generate_<ページ名>.py` がビルド時に取得してHTMLに焼き込む（型ごとの仕組みは `docs/notes/static-generation.md`「現行の仕組み」）。`wayhome_episodes`は出力が`wayhome/`配下38枚、`saikyo_pages`は`saikyo/`配下、`title_pages`は`title/`配下、`live_pages`は`live/`配下と`_redirects`の生成部分・`data/live_*.json`になる（`scripts/regenerate.py`の`OUTPUT_OVERRIDES`）
- Google Charts依存の6ページ … 訪問者がページを開くたびにブラウザが `docs.google.com` へクエリを投げる
- `jpml_pros`のYouTubeアイコンだけはYouTube Data APIから取る（#3。取得条件は `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- `saikyo_pages`は生成する環境で選手写真の結果が揺らぐ。**本番はActionsの生成が正**（`docs/notes/saikyo-page-design.md`「7. 選手写真の更新」）

**スプレッドシートを直しただけでは、ビルド時生成のページには反映されない。** 即時に反映したいときは
`regenerate-page.yml` を`workflow_dispatch`で手動実行する（`target_page`にページ名、または`all`）。
週次（毎週月曜05:37 JST、#103）で`all`が走る。

### 自動化

ワークフローの一覧（実行の契機と内容）は `docs/notes/static-generation.md`「ワークフローの一覧」。正は `.github/workflows/`。
**手動実行の前に同「ワークフローを手動実行するとき」を読む**（古い作業ブランチから実行すると、そのブランチの状態で生成物や常設issueが上書きされる）。

---

## 3. 作業の進め方

### 役割分担

| 場所 | 担当する作業 |
|---|---|
| **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案の作成 |
| **Claude Code**（Codespace内） | ファイルの編集、`gh` コマンドでのissue操作、コミット・push |

Claude Codeは Codespace のターミナルで動いている（`/workspaces/mj` で `claude`）。
`gh` が認証済みのため、issueの開閉やラベル操作がそのまま通る。
クラウドセッション（Claude Code on the web）でも動く（`gh` の代わりに GitHub MCP を使う。docs/notes/cloud-sessions.md）。

**Rebuild 後は `post-create.sh` が Claude Code を自動で入れる**（#212）。`.devcontainer/` を変えたときの反映手順と、
codespace を作り直す前の確認は `docs/notes/session-network.md`「Rebuild と Claude Code」。

**チャット側のアクセス手段（2026-09-26、#211・#440）。**
リポジトリは private のため、チャット側は作業ログとガイド文書を、`sync-logs.yml` が写す public の `retroeater/mj-logs` で読む
（入口は0章。PC では Claude for Chrome で mj も読める）。
読み方・読めないときの代わりの手段・コミット履歴の独立検証は `docs/notes/chat-side-operations.md`。
**コミットの到達確認・差分・マージ判定は、Claude Code 側が `git` / `gh` で行い、結果をチャットに報告する。**

**gh は `GH_TOKEN`（Codespaces のユーザーシークレット）で認証済み。** `gh auth status` の Active account が `(GH_TOKEN)` なら正常。
詳細と失効時の対処は `docs/notes/session-network.md`「gh の認証」。

### チャット側から渡された指示と Chat-Ref（2026-09-13）

チャット側（claude.ai）で作った指示文には `Chat-Ref`（形式 `CHAT-MMDD-XX-nn`）が付く。
トレーラの入れ方・重複確認・報告の書き方などのルールは CLAUDE.md「Chat-Ref」節が正。
チャット側の運用（識別子の確認、完了報告の受け方、「申送り」）は `docs/notes/chat-side-operations.md`。

### 複数セッションの並行作業（2026-09-12〜13）

作業ツリーは全セッションで共有される。ルールは CLAUDE.md「ブランチ運用」が正（決まるまでの経緯は `docs/notes/handover-archive-2026.md`）。

### 重要な約束事

**Claudeが作成した下書き（Claude Codeに貼る文面など）には、必ず見出しを付ける。**

```
## 📋 Claude Codeへ貼る文面（Claudeが作成した下書き）
```

これは、後から会話を読み返したときに
**平野さんの発言とClaudeの下書きが区別できなくなる**問題への対策。

指示文を書くときの注意（ブランチ名・マージ・SHAの書き方などのルールを含む）は `docs/notes/chat-side-operations.md`「指示文を書くときの注意」。

### タスク管理

**GitHub Issues + Projects** で管理している。

- ラベルは3系統。コロンの後に半角空白が入る（例: `分野: 整理・保守`。正は`gh label list`）:
  `状況:`（対応中、保留、待ち。未着手はラベルなし）、
  `分野:`（SEO/AIO、パフォーマンス、自動化、セキュリティ、整理・保守、インフラ、UI/UX）、
  `対象:`（ページ名。jpml_pros、index、全ページ など）
- 優先順位は Projects ボード（`ryoei.pro enhancements`）の並びで表す
- 完了分もcloseした状態で残している（判断の経緯を後から追えるように）
- **issueに着手したら、コードを触る前に「着手中」のコメントを残す（#157）。** ルールの本文は `CLAUDE.md`「issueの着手ルール」節
- issueの状況確認はGitHub Issuesを直接見る（0章。エクスポートファイルは廃止、#210）
- 2026-09-13〜14 のレビューで起票した #219〜#289: `gh issue list --state all --search "CHAT-0913-SP-01"` / `"CHAT-0913-RV-01"`（判断は `docs/notes/decisions-2026-09-13-review.md`）
- **新サイト全体の親 issue は #296（sub-issue 21件）。** #101 はトップページの
  作り直しに限る。新サイト送りにするときの手順は #296 の本文
  「新サイト送りにするとき・やめるとき」
- 平野さんの過去のタスクリストから移管した29件: `gh issue list --state all --search "過去のタスクリストからの移管"`
- ユーザ登録（サーバ側のアカウント）が前提の機能は #394（保留）に blocked by で依存させる
- 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分）は
  #304 に集約し、実施ごとにコメントを残す

---

## 4. 押さえておくべき方針

### 現行サイトに作り込みすぎない

**新サイトを別途新規構築する方針**が決まっている（`docs/new-site-design.md`）。
現行サイトはいずれ役目を終えるため、大きな投資は避ける。

具体的には、次のような判断をしてきた。

- 構造化データ（#13）は現行サイトでは見送り、新サイトで対応
- SNSシェアボタン（#82）も新サイトの選手個別ページに置く
- Astroへの移行（#20/#21）は新サイト構築時に判断。現行サイトの残作業はPythonで進める

**新サイトの第一弾は index.html（#101）。** 他ページと構造が独立し依存が最も少ないため（Astro の試作対象も index.html に変え、#20 はクローズ）。

ボトルネックは #21（Astroへの移行を検討する）の判断。
ここが保留のままだと新サイトの着手ができない。

### 外部ドメインへの依存を増やさない

CSP（#9）の導入を予定しているため。Bootstrapのローカル化やインライン
`onerror` の廃止も、この方針に沿ったもの。

残る外部依存は Google Charts（`www.gstatic.com` / `docs.google.com`、6ページ、#7）・Cloudflare Web Analytics（`static.cloudflareinsights.com`。
CSPでは `script-src` にのみ必要で、送信先は自ドメインの `/cdn-cgi/rum`）・画像12ドメイン。
**`gstatic.com` を消すには #111 と #141 の両方の判断が要る。** 詳細は `docs/notes/handover-archive-2026.md`「外部ドメイン依存の詳細」と
`docs/notes/site-findings.md`（画像ドメインの実測）、#9 でCSPの`img-src`を書くときの指針は #9 のコメント。

### 表の色とアクセシビリティ

`.mj-table` の文字は WCAG AAA（7:1）、並べ替えボタンのフォーカス枠は 1.4.11（3:1）。縞と見出しの背景は `#f2f2f2`（#345。AAA の上限は `#e4e4e4`、境界はリンク色 `#14459b`）。
新サイトでも引き継ぐ。値と理由は `style.css` のコメント、経緯は `docs/notes/site-findings.md`「リンクの配色」。

### gh-pages ブランチは触らない

移行前の GitHub Pages の内容を凍結したまま残している。GitHub Pages は 2026-09-21 に無効化したため
**配信されていない**（#84）。移行前の実装の記録として残すもので、内容の更新はしない。

---

## 4-x. 本番反映（デプロイ）の仕組み（#169の後始末、2026-09-12）

**本番反映の仕組み**（Workers Builds が `cloudflare` への push を検知する。`chore: regenerate ...` も即座に反映される。ゲートは #170）と、
**到達できないこと・目視申告値を根拠に「存在しない」「確定」と結論づけない原則**（#169・#312）は CLAUDE.md「構成」「方針」が正。

詳細（ダッシュボードの設定値・APIトークン・check-runs での確認範囲）は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、
セッションからの到達の実測は `docs/notes/session-network.md`（#328）、経緯は `docs/notes/handover-archive-2026.md`。

**`scripts/regenerate.py` はセッション内で実行できる**（2026-09-14 実測）。
worktree 内で `python3 scripts/regenerate.py <ページ名>` を実行する。

## 5. 次にやること

**期限付き・確認待ちタスク**

| # | 内容 | 期限・目安 |
|---|---|---|
| #130 | AIボット制御の再設定 | 旧トグルは廃止済み。Training は **Disallow**（Block は混在クローラーも遮断する）。**2026-09-24** に検索用クローラーが弾かれていないか再確認してクローズ |
| #269 | Search Console の月次取得 | **2026-10-01** の初回の定期実行（`fetch-gsc.yml`）を確かめてクローズ |
| #97 | 書籍ページ開発凍結中の楽天データ保存期限 | **2026-12-22**（最後に取得した2026-09-22の3か月後）までに再取得するか削除する（`docs/notes/books-freeze.md`「楽天の期限」） |

GitHub Issues（Open）に全件あるが、着手可能な主なものは以下。

| # | 内容 | 備考 |
|---|---|---|
| **#7** | 残り6ページ（型B 3 / ランキング 3）のGoogle Charts依存を解消 | **最大の残件。** #9 の前提でもある |
| #111 | 型B 3ページ（Dashboard＋ローソク足）の移行方針を決める | #7の残り判断1/2 |
| #141 | ランキング3ページの移行方針を決める | #7の残り判断2/2 |
| #8 | 龍龍の所属・出身地等との照合 | #61の仕組みを流用できる |
| #9 | CSP設定 | #7の後にやると強いポリシーが書ける |
| #4 | SentryでJSエラー検知 | 外部サービスの登録が必要 |
| #96 | カレンダーの参照・更新を自動化 | スコープ未定。決めるべき項目が4つある |
| #186 | アクセシビリティの実機での通し確認 | 静的レビュー（#178〜#185）の残り。チェックリストは `docs/notes/a11y-manual-check.md`。**Lighthouse のスコアを到達点として扱わない**（根拠は #186） |
| #180 | `select#selectbox` のラベル・選択と同時の遷移 | ランキング3ページ分は #141 の移行で対応する |
| #408 | タイトル戦の対局日を確定させる | 日付列の仮の値を実際の対局日に直す。書式と未確定の2期は `docs/notes/title-pages.md`「日付列の書式」 |
| **#222** | タイトル戦の新構成（`title/`、noindex・未公開） | 次は (1) #356（2026-09-24 に平野さんが連盟競技部に確認）→ (2) 公開の判断（#356 の解決が条件）。知見は `docs/notes/title-pages.md` |
| #370 | 連盟員名簿の属性（誕生日・段位など） | `check-meibo.yml`（週次）まで済み。**未確認: 予約実行の初回（2026-09-21 05:07 JST）で dry-run が外れて成功するか。** 使い道は #405・#286・#379（済） |
| — | 現行サイトで小さく作れる6件 | **チャット側の提案で、平野さんは未了承**: #389 → #388 → #377 → #366 → #371 → #378。未決: #365・#367 のデータを誰がいつ入力するか |

### #7 の進め方（検討済み）

**現状**: 21ページ中15完了。残り6ページは #111（型B: `houou_results` / `ouka_results` / `wrc_results`）と #141（ランキング3ページ）の方針待ち。
共通部品は出そろっている（`docs/notes/static-generation.md`、型別の進捗表は `docs/notes/handover-archive-2026.md`「#7 の型別の進捗表」）。
移行しても速くはならず、目的は外部依存とインラインハンドラの解消（static-generation.md「#7 の期待値」）。
テーブル描画ライブラリの選定（#95）は #7 の前提から外し、新サイトのスタック（#21）と併せて検討する。型Aの表の方針は static-generation.md「型Aの表の方針」。

---

## 7. 関連文書

完了済み作業の実装記録・調査結果は `docs/notes/` にある。handover には結論と参照先だけを残す。

`docs/notes/` 以下は下の一覧が正（`ls docs/notes/` にあって載っていないものは、開く場面を1行で足す）。

| ファイル | 開く場面 |
|---|---|
| `CLAUDE.md` | 作業のルール（ブランチ運用・Chat-Ref・作業ログ）。Claude Code がセッション開始時に読む |
| `docs/new-site-design.md` | 新サイト（#296）の設計。中断中で再開手順まである |
| `docs/astro-migration-study.md` | 新サイトのスタック（#21）の判断 |
| `docs/lighthouse-baseline.md` | パフォーマンスの改善前後の比較（ページ別スコアの基準値） |
| `docs/gsc/` | Search Console の数値（#142） |
| `docs/review-followup-instructions.md` | 2026-09-11 の包括レビューの指摘の出どころ（完了済み） |
| `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲 |
| `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
| `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前と、ブラウザを使う前（ログの読み方もここ） |
| `docs/notes/branch-operations.md` | ブランチの削除・ワークフローの変更・作業ログの寿命（入口の規則は CLAUDE.md） |
| `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・ワークフローの一覧・メンテナンス用スクリプト、#7 の残り |
| `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
| `docs/notes/dojo-guest-calendar.md` | 道場部ゲストの告知画像の取り込みと平野さん側の設定（#390） |
| `docs/notes/ogp.md` | OGP 画像・`og:title`・SNS のカード表示 |
| `docs/notes/video-wayhome.md` | 「帰り道」の一覧・個別ページ、新しい回の追加 |
| `docs/notes/site-findings.md` | 現行サイトの実測値（パフォーマンス・画像ドメイン・SEO・配色） |
| `docs/notes/a11y-manual-check.md` | アクセシビリティの実機確認（#186） |
| `docs/notes/mj-lead.md` | 表・グラフのあるページの説明文（`.mj-lead`、#312） |
| `docs/notes/saikyo-page-design.md` | 最強戦（`saikyo/`）と選手写真の更新 |
| `docs/notes/title-pages.md` | タイトル戦の新構成（`title/`、#222） |
| `docs/notes/live-page-design.md` | 放送対局ページ（`live/`、#346）。掲載範囲の拡大は「3-5」（#437） |
| `docs/notes/birthday-calendar.md` | 誕生日カレンダーの同期（#379） |
| `docs/notes/live-channel-write.md` | 「連盟ch」への書き込み用サービスアカウントの設定手順（#438） |
| `docs/notes/books-freeze.md` | **書籍ページの開発凍結（2026-09-22、#97）。決定・凍結時点の状態・楽天データの期限・再開手順** |
| `docs/notes/books-calendar.md` | 書籍の発売日のカレンダー同期の仕組み（#97、凍結時点の記録） |
| `docs/notes/books-covers.md` | 書籍の書影（楽天ブックス書籍検索API）の仕組みと規約（#97、凍結時点の記録） |
| `docs/notes/decisions-2026-09-13-review.md` | #219〜#289 の重複・統合の判断 |
| `docs/notes/handover-archive-2026.md` | 過去の事故・経緯（作業の前提にはしない） |
