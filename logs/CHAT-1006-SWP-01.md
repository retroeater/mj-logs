# CHAT-1006-SWP-01

- 着手日時: 2026-10-06
- 対象issue: なし（この指示では起票しない）
- ブランチ: work/1006-swp
- 着手時HEAD: daff02ec

## 指示

【Claude作成】Claude Code 向け指示：サイト全体の横断レビュー（5観点・読むだけ）— 指摘の一覧と DESIGN.md の材料をログに書く（何も直さない） Chat-Ref: CHAT-1006-SWP-01 マージ: ドキュメントのみ（docs/logs/・docs/decisions/）の変更なので、完了報告のうえ cloudflare へ入れてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる 貼る時機: いつでも（新しいセッションに、この指示だけを貼る） 作業ブランチ: クラウドセッションで実行する。work/1006-swp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-swp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-swp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ryoei.pro を5つの観点（デザインの一貫性・スマホ・状態の表示・実際の操作・公開前の基本）で横断して点検し、指摘の一覧と DESIGN.md の材料をログに書く。この指示では何も直さず、issue も起票しない。一覧を平野さんが見て採否を決め、直す作業・DESIGN.md の作成・起票は後の指示で行う。
決定（2026-10-06、平野さん）

* レビューの目的は、現行サイトを直すことと、新サイト（#296）の要件を洗い出すことの両方。現行サイトで直すのは小さいものだけ
* DESIGN.md を作る。中身は既決の値を集めたものにする。不足している部分・不整合の洗い出しは別の issue にする
* 凍結・廃止予定・作り直し予定のページはレビューの対象外

前提（チャット側。平野さんの決定ではない）

* 元になった観点は X の投稿（公開前のサイトを5つのサブエージェントで20項目点検する、という趣旨）。ryoei.pro に合わせて下の「観点」に読み替えた。読み替えが実物に合わなければ、実物に合わせて変え、変えた点を報告に書く
* 進め方（この指示は調査だけ、DESIGN.md の作成と起票は次の指示）はチャット側の案。平野さんの決定は上の3点だけ
* チャット側が読んだのは mj-logs のガイド `guide/fad9eb53/` の CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md・docs/decisions/page-release.md と、docs/notes/static-generation.md「ページの一覧」「style.css の共通クラス」「navbar.js と検索欄」、docs/notes/cloudflare.md の headless Chromium の注意、docs/notes/a11y-manual-check.md の冒頭。issue・リポジトリのファイル・本番の画面は読んでいない
* 対象外にするページ（文書で読んだもの。状態は実物で要確認）: `books/`（2026-09-22 開発凍結）、`saikyo_mens.html`（廃止予定）、ランキング3ページ `houou_ranking`・`ouka_ranking`・`wrc_ranking`（#141 で Python へ移植）、`index.html`（#101 で作り直し）
* `houou_race.html` は未公開で作成中（#507）のため対象外とする（チャット側の判断。平野さんには確かめていない）
* 対象に含めるもの: 上以外のトップ階層のページ、`title/`・`saikyo/`・`wayhome/`・`live/`（`live/` は noindex・メニュー未掲載だが本番にある）、成績3ページ（Google Charts 依存、#111 で据え置き）、`404.html`
* 既存の issue と重なる見込みの観点（番号は文書で読んだだけ。状態・本文は要確認）: キーボード操作・アクセシビリティは #186 と #178〜#185、h1・title・description は #283・#486・#5、favicon は #174、表の配色は #345・#344、スマホ幅の計測は #414、CSP は #9
* 既決で「問題」として挙げないもの（文書で読んだ例。ほかは docs/decisions/ と handover「4. 押さえておくべき方針」を正とする）: canonical を付けない（wayhome/ の個別ページは例外）、Facebook の共有ボタンを付けない、`404.html` に description を足さない、「Designed by BootstrapMade」は削除できない、構造化データは新サイトで対応、仮想スクロールは採用しない、Lighthouse のスコアを到達点として扱わない
* `docs/new-site-design.md` に新サイトのデザインの記述があるかもしれない（チャット側は読んでいない。要確認）
* sign-up・checkout・送信フォームは無い見込み（要確認）。ダークモード（`prefers-color-scheme`・`data-bs-theme`）の実装の有無は未確認
* 表示の確認は、作業ツリーを `python3 -m http.server` で配信し、Playwright の Chromium で開く想定（クラウドセッションでの実績は CHAT-1005-LGR-01・CHAT-1006-LGR-02 のログに「Playwright の Chromium、python3 -m http.server」とある。要確認）。本番（ryoei.pro）をブラウザで巡回しない（#124 が Rate Limiting の観察中のため。チャット側の判断）
* 使う skill: サブエージェントへの依頼文を書くときに `/writing-for-agents`

観点（サブエージェント1本につき1グループ）

* G1 デザインの一貫性: 色・文字サイズ・余白・角丸が、既決の値（`style.css` とそのコメント、docs/decisions/、docs/notes/site-findings.md）に従っているか。同じ種類のボタン・カード・入力欄・表・タブがページの系統をまたいで同じ見た目か。配色の種類が増えすぎていないか、見出しの階層がはっきりしているか。濃い背景（navbar・ヒーロー）と明るい背景の両方で文字が読めるか（コントラスト比は WCAG 2.x の相対輝度の式で計算し、式と値を書く）
* G2 スマホ: 横スクロール・画面からのはみ出しが無いか、スマホのメニューが開閉できるか、ボタン・リンクが押せる大きさか（大きさは実測値で書く）、文字を拡大してもレイアウトが崩れないか
* G3 状態の表示: 読み込み中・0件・エラーのときに画面がどうなるか（検索・絞り込みが0件、JSON の取得失敗、Google Charts の取得失敗、画像の取得失敗、404）。ボタン・リンク・タブ・並べ替えの hover・押下・フォーカス・無効の見た目。モーダル・ドロップダウン・タブ切り替えの遷移
* G4 実際の操作: コアフローを最初から最後まで操作する。候補（実物に合わせて直す）: navbar から各ページへ／jpml_pros の検索・並べ替え・ページ送り／title/ の入口 → 大会 → 期／saikyo/ のトップ → 年度／video_wayhome → wayhome/ の個別ページ／live/ の入口 → 絞り込み／共有ボタン／resource_dictionary のダウンロード／`?name=` 付きの URL。何もしないボタン、サイト内リンクの切れ（対象ページの全件を、ファイルの有無と `_redirects` で静的に判定）、キーボードだけで操作できるか（#186 のチェックリストと重なる項目は重複として扱う）
* G5 公開前の基本: 各ページの title・description・favicon・h1 を対象ページの全件で機械的に集計する。仮の文言（Lorem・ダミー・TODO・準備中・sample など）の残り。各ページで何のページかが冒頭で分かるか、主な導線が1つに定まっているか（この2つはデータベース中心のサイトには当てはめにくいので、所見は新サイトの要件の材料として書く）

手順

1. 洗い出す（主セッションが行う）。結果をログに書いてから手順2へ進む
   * 関係する issue を、クローズ済みとコメントの決定まで含めて検索して表にする（検索語の例: デザイン、DESIGN、スマホ、モバイル、タップ、横スクロール、文字サイズ、フォーカス、キーボード、hover、エラー、空、0件、リンク切れ、title、description、favicon、h1、配色、コントラスト、レビュー、点検）。上の「前提」の番号は状態・本文を実物で確かめる
   * `git branch -r --no-merged origin/cloudflare` で未マージのブランチを出し、同じ目的（サイト全体の横断レビュー・デザインの指針の文書）のものが無いか確かめる
   * 対象ページを決める。件数は `python3 scripts/regenerate.py --list` と docs/notes/static-generation.md「ページの一覧」を正とする。トップ階層は対象外を除く全ページ、サブディレクトリ（`title/`・`saikyo/`・`wayhome/`・`live/`）は系統ごとに入口と、型の違う代表ページを2〜3枚。選んだページと、対象外にしたページとその理由を表にする
   * 既決事項を読む: docs/decisions/ の全ファイル、handover「4. 押さえておくべき方針」、docs/notes/site-findings.md、docs/notes/a11y-manual-check.md、docs/new-site-design.md、`style.css` のコメント。サブエージェントに渡す「挙げなくてよいこと」の一覧にまとめる
   * 表示の確認の手段（http.server と Playwright の Chromium）がこのセッションで動くか確かめる。セッションから届かない外部ドメイン（画像・`docs.google.com`・`www.gstatic.com` など）を控える
2. サブエージェントを5本立て、G1〜G5 を1本ずつ点検させる（5本を超えて立てない）。依頼文は `/writing-for-agents` を使って書き、5本の全文をログの「経過」に貼る。依頼文に必ず入れること:
   * 読むだけ。ファイルの編集・コミット・push・issue の操作・ワークフローの実行・本番（ryoei.pro）のブラウザでの巡回をしない
   * 手順1の対象ページの表、「挙げなくてよいこと」の一覧、関係する issue の表
   * 画面は幅 1280px と 390px（360px も）で見る。スマホ幅の計測は docs/notes/cloudflare.md の headless Chromium の注意（`mobile: true` は内容が画面より広いページで実機と違う）に従う。文字の拡大は近似の手段（ルートの文字サイズを 200% にする等）で行い、手段を書く
   * セッションから届かない外部ドメインによる表示の欠け（外部の画像が出ない等）は指摘にしない
   * 指摘1件ごとに書く項目: 観点（G1〜G5 のどの項目か）／ページ（系統と URL のパス）／事象（見たままの事実、幅、再現の手順）／根拠（ファイルと行、または計測値）／直すならどのファイルか（生成物の HTML ではなく、生成スクリプト・`scripts/lib/`・`style.css`・`navbar.js` などの生成元）
   * 同じ種類の指摘が多数のページにあるときは1件にまとめ、ページ数を書く。1グループ40件を超えるなら、重要な順に40件とし、残りは件数と種類だけ書く
   * G1 は指摘とは別に「DESIGN.md の材料」を返す: (a) 既決の値の表（色・文字サイズ・余白・角丸・ブレークポイント・部品ごとの見た目。値と、出典のファイルと行・決定の記録） (b) 値が決まっていない箇所と、ページの系統の間で値が食い違う箇所の一覧（実測値つき）
   * サブエージェントのモデル・effort は既定のままにする。変えたグループがあれば理由を報告に書く
3. 主セッションが5本の結果を1つの一覧にまとめ、ログの `## 経過` に表で書く（`## 報告` には件数の集計と上位だけを書く）
   * 重複をまとめ、通し番号（G1-01 の形）を付ける
   * 「現行で直す（小）」に分類するものと重要と判断したものは、主セッションが実物で再現を確かめる。再現しなければ外し、外した件数を書く。確かめていない指摘には「未検証」と書く
   * 指摘ごとに次を付ける
      * 分類: 新規／既存の issue と重複（番号）／決定済みで対象外（根拠の文書と節）
      * 大きさ: 小（共通部品の1〜2ファイルの変更で、見た目の新しい決定が要らない）／中／大
      * 行き先の案: 現行で直す／新サイト（#296）の要件／見送り
      * 実機確認: 要（スマホ幅の異常をエミュレーションだけで見つけたもの、文字の拡大）／不要。「要」には、平野さんが iPhone で開く本番の URL（https://ryoei.pro/… の形でそのままクリックできるもの）と、見る点を1行で書く
   * 集計の表（グループ × 分類 × 大きさ）を書く
   * G1 の「DESIGN.md の材料」(a)(b) を、そのまま次の指示で DESIGN.md と別 issue の本文に使える形でログに書く。DESIGN.md を置く場所の案（リポジトリ直下か docs/ か、`.assetsignore` への追加の要否、容量の上限の対象か）も書く
   * `## 報告` の「判断が必要なこと」に、平野さんが決めること（どの指摘を現行で直すか、実機で見てほしいページ、DESIGN.md の置き場所、起票の単位）を書く

止まる条件

* 同じ目的（サイト全体の横断レビュー、デザインの指針の文書）の open issue がある、または同じ目的の未マージのブランチがある（#186・#283・#486・#5 など個別の観点の issue は重複として扱い、止まらない）
* 対象外のページの状態が「前提」と食い違う（凍結・廃止予定・作り直し予定でない、など）。そのページを対象に入れるかを決めずに止まる
* 表示の確認の手段（http.server と Playwright の Chromium）がこのセッションで動かない（手段を入れ替えず、試したことと結果を書いて止まる。静的に確かめられる G5 とサイト内リンクの判定だけは済ませてよい）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 何も直していない・issue を起票していないこと（`git diff origin/cloudflare --stat` が docs/logs/・docs/decisions/ だけであること）を確かめて報告に書く
* スクリーンショットや中間ファイルはコミットしない（その指示の中だけで使う一時ファイルとして扱う）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-SWP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-SWP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。Chat-Ref・識別子 SWP の重複確認: `git log --all --grep` と `docs/logs/` の履歴に該当なし（`git fetch --unshallow` 後）。
- 作業ブランチ: リモートに無かったため `git checkout -b work/1006-swp origin/cloudflare`。
- 指示欄の末尾（「この行が指示文の最後の行です。」）は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。

### 手順1 洗い出し

#### 1-1 関係する issue（2026-10-06 時点。open 204件の一覧と検索語での検索）

GitHub の検索は意味検索で、語を足しても件数は少なかった（レート制限で1回失敗）。open 全件の題を読んで照合した。
**同じ目的（サイト全体の横断レビュー・デザインの指針の文書）の open issue は無い。** 近いのは #107（新サイトの UI 方針）・#270（`style.css` にデザイントークンを集約）だが、いずれも別の目的。

| 状態 | issue | 題・内容 | この指示との関係 |
|---|---|---|---|
| open | #186 | 実機の支援技術でアクセシビリティを通し確認 | G4 のキーボード操作と重複する項目あり（読み上げは対象外） |
| open | #283 / #486 | 非表示 h1 と title の文言統一 / Bing の Recommendations | G5 の h1・title。h1 の無い11ページは work/1006-rvw-h1 が対応中 |
| open | #5 | 他ページへの SEO 展開（title の長さ・共通の末尾） | G5 |
| open | #9 | CSP | 外部ドメインは増やさない（既決） |
| open | #7 / #141 | Google Charts 依存の解消 / ランキング3ページの移植 | 成績3ページは据え置き（#111 クローズ） |
| open | #107 | 新サイトの UI 方針（カードUI・段階的開示・ダークモード） | G1 の新サイト要件 |
| open | #160 | 新サイトの情報設計（h1・パンくず・内部リンク階層） | G5 の所見 |
| open | #250 / #417 | navbar の現在地（aria-current）/ ナビの position（保留） | G1・G4 |
| open | #247 | 絞り込みが0件のときのメッセージ | G3 |
| open | #411 | 写真の読み込み失敗の取りこぼし | G3 |
| open | #415 / #416 / #420 | 検索の正規化 / 列見出しの略語の凡例 / ?name= で全体に戻る手段 | G4 |
| open | #245 #248 #249 #251 #252 #253 | モバイルの検索欄固定・検索欄の常時表示・ページ送りの最初/最後・トップへ戻る・type="search"・index の aria-label | G2〜G4 |
| open | #270 #271 #272 #273 #274 | デザイントークン集約・theme-color・数値列の右揃え・印刷用・型A 2列のモバイルのカード化 | G1・G2 |
| open | #23 | スクロール中に検索ボックスを開くと背後にデータ行が見える | G2 |
| open | #202 | apple-touch-icon の展開 | G5 |
| open | #352 | 画像リンク切れの検知結果（常設） | G3（外部画像は対象外） |
| open | #296 | 新サイト（保留） | 行き先の案 |
| closed | #178〜#185 | アクセシビリティの個別修正（open 一覧に無い。本文は未確認） | G4 |
| closed | #174 | favicon（open 一覧に無い。本文は未確認） | G5 |
| closed | #344 / #345 | 表の縞の配色（`#f2f2f2`） | G1（検索で closed を確認） |
| closed | #414 | jpml_pros のスマホ幅 935px | G2（docs/decisions/pros.md でそのままにすると決定） |

前提の番号の確かめ: #186・#283・#486・#5・#9 は open で、指示の記述と合う。#178〜#185・#174 は「open 一覧に無い＝closed」とだけ確かめ、本文・コメントの決定は読んでいない（未確認）。

#### 1-2 未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）

`origin/work/1006-rvw-h1`（h1 の無い11ページに h1 を足す、CHAT-1005-RVW-06、ログ1コミット）と、この作業の `work/1006-swp` のみ。
前者は #283 の h1 だけの別の目的で、止まる条件の「同じ目的」ではない（h1 の追加が反映されると G5 の h1 の件数が変わる。この指示の点検は origin/cloudflare の内容で行う）。ほかの `work/*` は cloudflare にマージ済み。

#### 1-3 対象ページ

件数の正（`python3 scripts/regenerate.py --list`）は20ターゲット（books_pages・houou_race を含む）。実物の確認: `houou_race.html` は `noindex`・navbar/sitemap/llms.txt に無い（未公開）。`live/index.html` は `noindex`。`books/` は凍結（docs/notes/books-freeze.md）。`saikyo_mens`（#442 open）・`index.html`（#101 open）・ランキング3ページ（#141 open）も前提どおりで、**止まる条件の「対象外の状態の食い違い」に当たるものは無い。**
ずれ: docs/notes/static-generation.md の `live/` は「691」だが実物は908 HTML（`find live -name '*.html'`）。

| 系統 | 対象にしたページ | 理由 |
|---|---|---|
| 型A（15列） | `/jpml_pros.html` | 最重要 |
| 型A（2列/3列） | `/jpml_test` `/resource_logs` `/video_live` `/video_en` `/rh_paifu` `/video_mtsuku` | 全件 |
| 型A' | `/rh_results` `/rh_results_detail` | 全件 |
| 型C・D | `/houou_leagues` `/ouka_leagues` `/resource_efficiency` | 全件 |
| 型B（Google Charts） | `/houou_results` `/ouka_results` `/wrc_results` | 全件（このセッションでは gstatic が届かず描けない） |
| 濃色固定 | `/video_wayhome.html` | |
| 手書き | `/jpml_links` `/resource_dictionary` `/rh_links` `/404.html` | |
| `title/` | `index.html`（入口）・`houou/index.html`（大会）・`ourai/12.html`（期） | 384 HTML のうち入口・大会・期の3種 |
| `saikyo/` | `index.html`（トップ）・`2022.html`・`2012.html`（順位の空欄が多い古い年度） | 17 HTML |
| `wayhome/` | `atD2e-NgnKw.html`・`dxkS1JZW-U0.html` | 39 HTML |
| `live/` | `index.html`・`ourai/index.html`・`ourai/12.html`・`ourai/3/b16-a.html` | 908 HTML のうち入口・大会・回・卓の4階層 |

| 対象外 | 理由 |
|---|---|
| `books/`（191 HTML） | 2026-09-22 開発凍結 |
| `saikyo_mens.html` | 廃止予定（#442） |
| `houou_ranking` `ouka_ranking` `wrc_ranking` | #141 で Python へ移植 |
| `index.html` | #101 で作り直し |
| `houou_race.html` | 未公開で作成中（#507。チャット側の判断、平野さんには未確認） |

G4（リンク切れ）と G5（title・description の機械集計）は、サブディレクトリの全 HTML を静的に調べる。

#### 1-4 既決事項

読んだもの: `docs/decisions/` の page-release・features・seo-bing・pros・saikyo・publishing・cloudflare・automation、handover「4. 押さえておくべき方針」、docs/notes/a11y-manual-check.md、docs/new-site-design.md §2・§12、`style.css` のコメントの見出し。title・live・houou・operations・broadcast-calendar・dojo-guest の各 decisions と site-findings は、サブエージェントに「作業の最初に読む」ことを指示した。
サブエージェントに渡す「挙げなくてよいこと」は下の「手順2」の共通部に載せた。

#### 1-5 表示の確認の手段

- `python3 -m http.server`（作業ツリー、127.0.0.1:8801）と Playwright 1.56.1（Node、`/opt/node22/lib/node_modules/playwright`、Chromium は `/opt/pw-browsers/chromium`）: **動く**。`/jpml_pros.html` を幅 390px で開き、title と `scrollWidth`=934 を取得できた（既決の 934px 固定の表と一致）。Python の playwright は入っていない。
- セッションから届かない外部ドメイン（curl で確認）: `www.gstatic.com`（接続失敗。Google Charts が描けず、成績3ページ `houou_results`・`ouka_results`・`wrc_results` は表示を確かめられない）、`pbs.twimg.com`・`yt3.googleusercontent.com`（400）、`img.youtube.com`（404）。届く: `docs.google.com`（302）、`ryoei.pro`（200。ただしブラウザでは巡回しない）。

### 手順2 サブエージェントの依頼文（5本）

`/writing-for-agents` を読んでから書いた。5本とも `general-purpose` のサブエージェントで、モデル・effort は既定のまま（変えたグループは無い）。
各本には、次の「起動時の依頼文」（本体は2ファイルへの参照）を渡し、2ファイルは主セッションの scratchpad に置いた。**エージェントが読んだ全文は、「共通部」＋「個別部」の連結**（下に全文）。読むだけ（編集・コミット・push・issue 操作・ワークフロー実行・本番のブラウザ巡回をしない）を共通部の冒頭に入れてある。

#### 起動時の依頼文（5本共通の形）

```text
ryoei.pro のサイト全体レビューの <観点> を担当してください。読むだけの点検です。最初に次の2ファイルを Read し、書かれたとおりに進めてください: <scratchpad>/common.md（共通の前提）と <scratchpad>/gN.md（個別の依頼）。作業用ディレクトリは <scratchpad>/gNwork/ 。<各観点で先に読む文書>を読んでから点検に入ってください。最終メッセージに指摘の一覧を返してください。（G1 は「DESIGN.md の材料」(a)(b) も、G5 は集計の表も）
```

各観点で先に読む文書: G1: docs/decisions/ の全ファイルと docs/notes/site-findings.md・docs/new-site-design.md（§2・§12）／G2: docs/decisions/ の全ファイルと docs/notes/cloudflare.md の headless Chromium の注意／G3: docs/decisions/ の全ファイル／G4: docs/decisions/ の全ファイルと docs/notes/a11y-manual-check.md／G5: docs/decisions/ の全ファイル（特に seo-bing.md）と docs/notes/site-findings.md の SEO・favicon の節

#### 共通部（common.md）全文

````markdown
## 共通の前提（5本すべてに同じものを渡す）

### あなたの仕事
ryoei.pro（日本プロ麻雀連盟の選手データベースの個人サイト。静的HTML・ビルド工程なし・Bootstrap 5.3 をローカル配信・生成スクリプトが Google スプレッドシートから HTML を焼き込む）を、1つの観点（下の「個別の依頼」）で点検し、指摘の一覧を返す。主セッションが5本の結果を1つの一覧にまとめ、平野さん（サイトの持ち主）が採否を決める。
目的は2つ: 現行サイトで直せる小さな問題を見つけること、新サイト（#296）の要件を洗い出すこと。

### してはいけないこと（読むだけ）
- リポジトリのファイルの編集・コミット・push・ブランチ操作・issue の操作・GitHub Actions の実行をしない。`git stash`・`git reset`・`git checkout -- .` も使わない
- 本番（ryoei.pro）をブラウザで巡回しない（Rate Limiting の観察中）。画面は作業ツリーを配信して見る
- 一時ファイル・スクリーンショットは自分の作業用ディレクトリ（指定された scratchpad）にだけ置く。リポジトリの中に作らない

### 作業ツリーと画面の見方
- リポジトリ: `/home/user/mj`（ブランチ work/1006-swp）。HTTP サーバーは主セッションが `http://127.0.0.1:8801/` で起動済み（作業ツリーのルートを配信。落ちていたら `python3 -m http.server <別のポート> --bind 127.0.0.1` を自分で起動し、終わったら止める。ポートは他と重ねない: G1=8811、G2=8812、G3=8813、G4=8814、G5=8815）
- ブラウザ: Playwright 1.56.1（Node）。`const {chromium}=require('/opt/node22/lib/node_modules/playwright'); chromium.launch({executablePath:'/opt/pw-browsers/chromium'})`。Python の playwright は入っていない
- 画面幅は 1280px と 390px（必要なら 360px も）で見る。
  **スマホ幅の計測に `mobile: true`（`Emulation.setDeviceMetricsOverride` の mobile）を使わない。** 内容が画面より広いページでは実機と違う結果になる（docs/notes/cloudflare.md「headless Chromium」の注意）。`viewport: {width: 390, height: 844}` を使い、横スクロールは `document.documentElement.scrollWidth` と `clientWidth` の比較で測る
- 文字の拡大は近似で行う（ルート `<html>` の `font-size` を 200% にする、または `page.addStyleTag`）。使った手段を指摘に書く。実機の「文字サイズを大きくする」とは同じではない
- **セッションから届かない外部ドメインによる表示の欠けは指摘にしない。** 確認済みで届かないもの: `www.gstatic.com`（Google Charts のローダー。成績3ページ `houou_results`・`ouka_results`・`wrc_results` は、このセッションでは Charts が描けない）、`pbs.twimg.com`・`img.youtube.com`・`yt3.googleusercontent.com` などの画像（400/404 を返す）。画像が出ない・Charts が出ないこと自体は報告に含めない（ただし「画像の取得に失敗したときの画面」の設計は G3 の対象）。届くもの: `docs.google.com`、`ryoei.pro`
- 指摘は実物で確かめたものだけにする。確かめていないことは「未検証」と明記する。推測を事実のように書かない

### 対象ページ（20ページ＋4系統の代表）
トップ階層（生成ページ・手書きページ）: `/houou_leagues.html` `/ouka_leagues.html`（型C・静的SVG）、`/houou_results.html` `/ouka_results.html` `/wrc_results.html`（型B・Google Charts、gstatic が届かず描けない）、`/jpml_pros.html`（型A・15列・最重要）、`/jpml_test.html` `/resource_logs.html` `/video_live.html` `/video_en.html` `/rh_paifu.html` `/video_mtsuku.html`（型A・2列/3列）、`/rh_results.html` `/rh_results_detail.html`（型A'）、`/resource_efficiency.html`（型D・静的SVG）、`/video_wayhome.html`（濃色固定・ヒーロー＋横スクロール）、`/jpml_links.html` `/resource_dictionary.html` `/rh_links.html`（手書き）、`/404.html`。
サブディレクトリ（入口と代表）:
- `title/`: `/title/index.html`（入口）、`/title/houou/index.html`（大会）、`/title/ourai/12.html`（期）
- `saikyo/`: `/saikyo/index.html`（トップ）、`/saikyo/2022.html`・`/saikyo/2012.html`（年度。2012 は順位の空欄が多い古い年度）
- `wayhome/`: `/wayhome/atD2e-NgnKw.html`・`/wayhome/dxkS1JZW-U0.html`（エピソード個別）
- `live/`（noindex・メニュー未掲載、908ページ）: `/live/index.html`、`/live/ourai/index.html`、`/live/ourai/12.html`、`/live/ourai/3/b16-a.html`
URL は `http://127.0.0.1:8801/…`（ローカル）で開く。`_redirects` があるため、`/title/houou` のように末尾スラッシュなし・`/live` のような形はローカルでは 404 になる（本番では 301）。ローカルでは `.html` か `/index.html` まで書く。

### 対象外（点検しない）
`books/`（2026-09-22 開発凍結）、`saikyo_mens.html`（廃止予定、#442）、`houou_ranking.html` `ouka_ranking.html` `wrc_ranking.html`（#141 で Python へ移植中）、`index.html`（#101 で作り直し）、`houou_race.html`（未公開で作成中、#507）。ただし、対象ページから対象外ページへのリンクが切れているかは G4 の対象。

### 挙げなくてよいこと（既決。「問題」として挙げない）
次は決定済み。ほかの既決は `docs/decisions/` の全ファイルと `docs/handover.md` の「4. 押さえておくべき方針」、`docs/notes/site-findings.md`、`docs/notes/a11y-manual-check.md`、`docs/new-site-design.md`（§2 デザイン方針・§12 パイロット）、`style.css` のコメントを読んで確かめる（作業の最初に読む）。
- canonical を付けない（`wayhome/` の個別ページだけ付ける）。Facebook の共有ボタンは付けない。`404.html` に description を足さない。「Designed by BootstrapMade」の表記は削除できない（index.html のテンプレート由来。対象外）
- 構造化データ（JSON-LD）は新サイトで対応（#13）。仮想スクロールは採用しない。Lighthouse のスコアを到達点として扱わない
- `jpml_pros.html` がスマホ幅で横にはみ出す（934px 固定の表）件は、新しいページで作り直すのでそのままにする（docs/decisions/pros.md）。ただし「はみ出し以外」のスマホの問題は挙げてよい
- ほとんどのページの h1 は visually-hidden（#53 の意図的判断）。h1 が画面に見えないこと自体は指摘にしない。ただし h1 が無い・複数ある、見出しの階層が飛ぶ、は挙げてよい（h1 の無い11ページは #283・#486 で対応中のため、「h1 が無い」は重複として分類される）
- 動画系（`video_wayhome`・`wayhome/`）は濃色固定（新サイトのトーンの例外、docs/new-site-design.md §2）。ダークモード対応は新サイトの方針（現行は未対応のページが多い）
- 現行サイトに作り込みすぎない方針（docs/handover.md「4.」）。大きな作り直しの提案は「新サイトの要件」として書き、現行の修正案にしない
- 外部ドメインへの依存を増やさない方針（CSP #9 の予定）。外部ライブラリ・CDN・フォントの追加は提案しない
- 動き（自動再生・アニメーション）を足さない。`prefers-reduced-motion` を尊重する

### 関係する issue（状態は 2026-10-06 の open 一覧との照合。本文は主セッションが全文を読んだわけではない）
open: #186（実機の支援技術でアクセシビリティを通し確認。キーボード・読み上げ）、#283（非表示 h1 と title の文言統一）、#486（Bing の Recommendations: h1・description・タイトル・alt）、#5（他ページへの SEO 展開・title の長さ）、#9（CSP）、#7（Google Charts 依存の解消、ランキング3ページの移植で完了予定）、#141（ランキング3ページの移植）、#107（新サイトの UI 方針: カードUI・段階的開示・ダークモード）、#160（新サイトの情報設計: h1・パンくず・内部リンク階層）、#250（navbar に aria-current）、#417（共通ナビの position をそろえる方針、保留）、#247（絞り込みが0件のときのメッセージ）、#411（写真の読み込み失敗の取りこぼし）、#415（選手名の絞り込みの正規化）、#416（列見出しの略語の凡例）、#420（?name= のとき全体へ戻る手段）、#245・#248・#249・#251・#252・#253（検索欄・ページ送り・トップへ戻る・type="search"・index の aria-label）、#270（デザイントークンの集約）・#271（theme-color）・#272（数値列の右揃え）・#273（印刷用スタイル）・#274（型A 2列のモバイルのカード化）、#23（スクロール中に検索を開くと背後にデータ行が見える）、#202（apple-touch-icon の展開）、#352（画像リンク切れの検知結果）、#296（新サイト）、#13（構造化データ）
closed（open 一覧に無い。番号は文書で読んだだけ）: #178〜#185（アクセシビリティの個別修正: ナビの名前・フォーカス・セレクト・名前セルの th 化・main/スキップリンク・別タブ予告・ページ送り・ハンバーガー）、#174（favicon）、#344・#345（表の縞の配色 `#f2f2f2`）、#414（jpml_pros のスマホ幅 935px）、#409（共有ボタン）、#312（説明文の折り返し幅）
同じ事象が上に当てはまるときは、あなたの指摘に「重複: #NNN」と書く（分類は主セッションが決める）。

### 指摘1件ごとに書く項目
1. 観点: G1〜G5 のどの項目か
2. ページ: 系統と URL のパス
3. 事象: 見たままの事実。幅、再現の手順（クリックの順など）
4. 根拠: ファイルと行、または計測値（数値と単位。コントラスト比は式と入力値も）
5. 直すならどのファイルか: 生成物の HTML ではなく生成元（`scripts/generate_*.py`・`scripts/lib/`・`style.css`・`navbar.js`・`table.js`・各ページの `.js`・`assets/`）。生成物 HTML のうち手書きのもの（`404.html`・`jpml_links.html`・`resource_dictionary.html`・`rh_links.html`）はそのファイル
6. 確かさ: 確認済み（再現した）／未検証
7. （任意）重複する issue の番号

同じ種類の指摘が多数のページにあるときは1件にまとめ、ページ数と代表を書く。1グループ40件を超えるなら、重要な順に40件とし、残りは件数と種類だけ書く。

### 返し方
最終メッセージに、指摘の一覧（上の項目。Markdown の表またはリスト）、確認した範囲（見たページ・幅・手段）、見られなかったこと、使った手段の制約を書く。長くなってよいが、事実と推測を混ぜない。モデル・effort は既定のまま。
````

#### 個別部（g1.md）全文 — G1（デザインの一貫性）

````markdown
## 個別の依頼: G1 デザインの一貫性

点検すること（ページの系統をまたいで見る）:
1. 色・文字サイズ・余白・角丸が、既決の値（`style.css` とそのコメント、`docs/decisions/`、`docs/notes/site-findings.md`、`docs/new-site-design.md` §2・§12）に従っているか。`style.css` の中のハードコードされた色・サイズの種類を数え、CSS 変数（`--mj-*`）で管理されている範囲と、されていない範囲を分ける
2. 同じ種類の部品（ボタン・カード・入力欄・表・タブ・パンくず・固定バー・共有ボタン・ページ送り・説明文 `.mj-lead`・見出し）が、系統（型A/B/C/D、`video_wayhome`・`wayhome/`、`saikyo/`、`title/`、`live/`、手書きページ）をまたいで同じ見た目か。実測（`getComputedStyle` の値）で比べる
3. 配色の種類が増えすぎていないか（実際に使われている色の数を集計する）、見出しの階層（h1〜h3 の大きさ・太さ・余白）がはっきりしているか
4. 濃い背景（navbar・ヒーロー・濃色帯）と明るい背景の両方で文字が読めるか。**コントラスト比は WCAG 2.x の相対輝度の式で計算し、式と入力値と結果を指摘に書く。** 式: 線形化 `c ≤ 0.03928 ? c/12.92 : ((c+0.055)/1.055)^2.4`、`L = 0.2126R + 0.7152G + 0.0722B`、比 = `(L1+0.05)/(L2+0.05)`（L1 が明るいほう）。本文 4.5:1・大きい文字（18.66px 太字または 24px 以上）3:1・非テキスト 3:1 を基準にする。計測は `getComputedStyle` の色と背景を実際の要素から取る（半透明の背景は重ねた結果の色で計算する）。表の文字は既決で AAA（7:1）、縞は `#f2f2f2`（#344・#345）

指摘とは別に、**「DESIGN.md の材料」** を返す。次の DESIGN.md と別 issue の本文にそのまま使う。
(a) **既決の値の表**: 色・文字サイズ・余白・角丸・ブレークポイント・影・フォント・部品ごとの見た目。列は「項目／値／適用範囲／出典（ファイルと行、または決定の記録の文書名と日付）」。`style.css` の `:root` や `--mj-*` の定義、コメントに理由のあるもの、`docs/decisions/` と `docs/new-site-design.md`（カラートークン §12 など）の決定を拾う。出典の行番号は `style.css` を実際に開いて確かめる
(b) **値が決まっていない箇所と、系統の間で値が食い違う箇所の一覧**: 例えば同じ役割のリンク色・見出しサイズ・角丸・余白・ボタンの高さが系統ごとに違うもの。列は「箇所／系統Aの実測値／系統Bの実測値／出典（行）／決まっているか」。実測値は幅 1280px で取り、必要なら 390px の値も書く。これは指摘の件数に数えない（別の返し）

指摘に入れるもの: 既決の値に従っていないもの、同じ役割なのに系統で見た目が違うもの、コントラストが基準に満たないもの、見出しの階層の問題。系統の違いが既決の例外（動画系は濃色固定など）に当たるものは指摘にせず、(b) に「例外として決定済み」と書く。
````

#### 個別部（g2.md）全文 — G2（スマホ）

````markdown
## 個別の依頼: G2 スマホ

点検すること（幅 390px と 360px。1280px は比較用）:
1. 横スクロール・画面からのはみ出し: 各対象ページで `scrollWidth` と `clientWidth` を比べる。はみ出すページは、はみ出す要素（幅が `clientWidth` を超える最初の要素）を特定する。表の中の横スクロール（意図したもの）とページ全体のはみ出しを分ける。`jpml_pros.html` のページ全体のはみ出しは既決（挙げない）
2. スマホのメニュー（共通ナビ `navbar.js`）が開閉できるか: ハンバーガーを押して開閉する。開いたとき項目が画面内に収まり、スクロールできるか。固定バー・固定ナビが本文を隠さないか
3. ボタン・リンクが押せる大きさか: 主な操作要素（ナビの項目、ハンバーガー、検索の虫眼鏡、ページ送り、並べ替えの見出し、共有ボタン、タブ、カード）の幅×高さを `getBoundingClientRect` で実測して書く。基準は 44×44px（WCAG 2.5.5 の目標。2.5.8 の最低は 24×24px）。隣り合う操作要素の間隔も測る
4. 文字を拡大してもレイアウトが崩れないか: ルート `font-size` を 200% にして重なり・はみ出し・文字の切れを見る（近似。手段を書く）。実機の文字サイズ設定とは同じではないため、「実機確認が要る」と書く
5. スマホで固定バー・固定ナビの高さの合計が画面の何割を占めるか（`position: fixed`/`sticky` の要素の高さを測る）

指摘には実測値（幅・高さ・px）を必ず書く。エミュレーションだけで見つけたものには「エミュレーションのみ」と書く（主セッションが実機確認の要否を決める）。
````

#### 個別部（g3.md）全文 — G3（状態の表示）

````markdown
## 個別の依頼: G3 状態の表示

点検すること:
1. 読み込み中・0件・エラーのときの画面。対象: 検索・絞り込みが0件（`jpml_pros`・`resource_logs`・`video_*`・`rh_*`・`saikyo/` の検索・`title/` の検索・`live/` の絞り込み・`wayhome` 一覧の検索など。入力欄に出ない文字列を入れる）、JSON の取得失敗（`houou_leagues_data.json`・`houou_race` は対象外・`live` や `title` が読む JSON・`?name=` 付き URL。Playwright の `page.route` で該当リクエストを abort/404 にして画面を見る）、Google Charts の取得失敗（成績3ページ。このセッションでは gstatic が届かないため、そのまま読み込み失敗の状態を見られる。何が表示されるか、読み込み中の表示があるか）、画像の取得失敗（外部画像は届かないため、そのまま失敗状態になる。代替アバター `img/avatar.svg` への差し替え、`alt`、枠の崩れ。これは「画像の取得に失敗したときの画面の設計」の点検で、画像が出ないこと自体は指摘にしない）、404（`/404.html` と、存在しない URL を開いたときの本番の挙動は `_redirects`・`wrangler.jsonc` から読む。ローカルの http.server の 404 は本番と違う）
2. ボタン・リンク・タブ・並べ替えの hover・押下（`:active`）・フォーカス（`:focus-visible`）・無効（`:disabled`・`aria-disabled`）の見た目。ページ送りの「前へ」が先頭で無効になるときの見た目。Playwright で `hover()`・`focus()`・`keyboard.press('Tab')` を行い、`getComputedStyle` で outline・background・color を比べる。背景が濃い領域（navbar・ヒーロー・帯）でフォーカスリングが見えるか
3. モーダル・ドロップダウン・タブ切り替え・折りたたみ（共通ナビのドロップダウン、`saikyo/` と `title/` の開閉する写真カード、共有ボタンのメニュー、コピー結果のトースト）の遷移: 開閉の動き、`prefers-reduced-motion` を `emulateMedia({reducedMotion:'reduce'})` で与えたとき動きが止まるか、開いた状態の見た目
4. 読み込み中: JS が描画するまでの間（`page.route` で JS を遅らせる）に、空の表・空のグラフが見える時間があるか

指摘は「状態 × ページ（系統）」で書く。0件のメッセージは #247 が open なので、重複として書く（現状の実測は書く）。
````

#### 個別部（g4.md）全文 — G4（実際の操作）

````markdown
## 個別の依頼: G4 実際の操作

コアフローを最初から最後まで操作する（Playwright。幅 1280px を基本に、必要なものは 390px でも）。候補（実物に合わせて直してよい）:
1. 共通ナビ（`navbar.js`）から各ページへ: ナビのすべての項目（ドロップダウンの中を含む）を列挙し、リンク先が存在するか（ファイルの有無と `_redirects` で判定）、開いたページで現在地が分かるか
2. `jpml_pros.html`: 検索・並べ替え・ページ送り・`?name=` 付き URL（#420 が open）
3. `title/` の入口 → 大会 → 期、入口の検索
4. `saikyo/` のトップ → 年度、年度の選択、`?match=` の絞り込みと「すべての対局へ戻る」
5. `video_wayhome.html` → `wayhome/` の個別ページ → 一覧へ戻る（前後の回）
6. `live/` の入口 → 絞り込み → 個別
7. 共有ボタン（URL コピー。`assets/share.js`）、`resource_dictionary.html` のダウンロード（`dic/` のファイルが存在するか）
8. 手書きの `jpml_links.html`・`rh_links.html` の外部リンク（ホスト名とリンク先の形だけ確かめる。外部へアクセスしない）

加えて次を行う:
- **何もしないボタン**: 各ページの `button`・`[role=button]`・`a[href="#"]`・`href` の無い `a` を列挙し、クリックして何も起きない（URL もDOMも変わらない）ものを探す
- **サイト内リンクの切れ（静的に全件判定）**: 対象ページの HTML から `href`・`src`（`a`・`link`・`script`・`img`・`source`・`meta[content]` の OGP 画像）を抜き出し、サイト内（ルート相対・相対）のものを、ファイルの存在と `_redirects`（`/title/:slug/` の置換や 301 を含む）と `wrangler.jsonc`（`html_handling: "none"`）で判定する。`#アンカー` は同じページ内の `id` の有無を見る。サイト内向けの `?name=` や `?match=` の値は存在判定の対象外（形だけ確かめる）。`title/`・`saikyo/`・`wayhome/`・`live/` は代表だけでなく、サブディレクトリの全 HTML（title 384・saikyo 17・wayhome 39・live 908）を機械的に調べてよい（読むだけ）。結果は「リンク先が無い件数」と代表例で書く
- **キーボードだけで操作できるか**: Tab の順序、フォーカスが固定バーに隠れないか、ドロップダウン・開閉カードが Enter/Space で開くか、Esc で閉じるか、フォーカストラップの有無。#186 のチェックリスト（`docs/notes/a11y-manual-check.md`）と重なる項目は「重複: #186」と書く。実機の読み上げ（NVDA 等）は点検できないため扱わない

指摘は、再現の手順（クリックの順・キーの順）を必ず書く。
````

#### 個別部（g5.md）全文 — G5（公開前の基本）

````markdown
## 個別の依頼: G5 公開前の基本

点検すること:
1. **title・description・favicon・h1 を、対象ページの全件で機械的に集計する。** 対象はトップ階層の対象ページ（20ページ）と、`title/`（384）・`saikyo/`（17）・`wayhome/`（39）・`live/`（908）の全 HTML。HTML のパースは Python の標準ライブラリ（`html.parser`）か Playwright で行い、結果の表（ページ数・欠けているもの・重複しているもの）を書く。見る項目: `<title>` の有無・長さ・重複・サイト名の付き方、`meta name=description` の有無・長さ・重複、`link rel=icon`・`apple-touch-icon` の有無（`/favicon.ico`・`/apple-touch-icon.png` の存在も）、h1 の数（0・1・2以上）、`<html lang>`、`meta viewport`、`og:title`・`og:description`・`og:image`（画像ファイルの存在）・`twitter:card`、`robots`（noindex の付き方が意図どおりか。`live/`・`houou_race.html`・`books/` は noindex が正）、`canonical`（`wayhome/` だけが持つのが既決）、`og:locale`（#267）。既決: `404.html` に description を足さない、h1 の無い11ページは #283・#486 で対応中（重複として分類される。件数だけ確かめる）
2. **仮の文言の残り**: `Lorem`・`ダミー`・`TODO`・`TBD`・`準備中`・`sample`・`サンプル`・`xxx`・`テスト`・`調整中`・`仮`・`dummy`・`placeholder`・`example.com`・`#`だけのリンクなどを、対象の全 HTML と手書きページのテキスト・属性・コメントから検索する。意図したもの（例: `jpml_test.html` はプロテストのページ名、`placeholder` 属性の説明文）は除き、残りを「仮の文言」として挙げる。`console.log`・`debugger`・`.map` への参照・使われていないテンプレートの文言も見る
3. **各ページで何のページかが冒頭で分かるか／主な導線が1つに定まっているか**: この2つはデータベース中心のサイトには当てはめにくい。代表ページ（`jpml_pros`・`video_live`・`title/index`・`saikyo/index`・`live/index`・`resource_efficiency`・`jpml_links`）の画面の冒頭（幅 1280px と 390px）を見て、事実（何が最初に見えるか・主な操作が何か）と所見を書く。これは指摘ではなく「新サイトの要件の材料」として別に書く
4. `robots.txt`・`sitemap*.xml`・`llms.txt`・`_headers`・`wrangler.jsonc`・`_redirects` の整合: sitemap の URL がファイルとして存在するか、noindex のページが sitemap に載っていないか、sitemap に載っていない公開ページがないか（機械的に照合）
5. 画面に出る誤字・文言の不統一（同じ概念の表記ゆれ、例: ページ名とナビの項目名と title のずれ）を、対象ページ全体を横断して集計する

指摘は集計の表を主にして、件数と代表例（パス）で書く。多数のページに同じ事象があれば1件にまとめる。
````

実行の結果: 5本とも完了。サブエージェントの作業時間は 5〜19分、ツール呼び出し 51〜88回。HTTP サーバーはポート 8801（主セッション）と G4 が 8814 を使い、終了済み。

### 手順3 統合した指摘の一覧

通し番号は主グループ内の連番。複数グループが同じ事象を挙げたものは1件にまとめ、「出所」に元のグループを書く。
ページは `title/` `saikyo/` `wayhome/` `live/` を除き、トップ階層のファイル名。直す先は生成元（生成物の HTML ではない）。
「検証」: **再現** = 主セッションが実物で再現した／**サブ再現** = サブエージェントが再現し、主セッションは未再現（未検証）／**コード** = 主セッションがコードを読んで確かめた。
「実機」: 要 = 平野さんの iPhone で確かめる（URL は本番）／不要。

#### G1 デザインの一貫性（12件）

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| G1-01 | 濃色の動画系 7ページ（`video_wayhome`・`wayhome/`・`live/`） | Tab で出る「本文へスキップ」が `#121212` の上に紺 `#14459b` で、読めない | 計算値 `rgb(20,69,155)` on `rgb(18,18,18)`。L(#14459b)=0.0677、L(#121212)=0.0060、(0.0677+0.05)/(0.0060+0.05)=**2.10:1**（基準 4.5:1）。濃色用のリンク色の規則は `.mj-video-page` の中だけに効き、スキップリンクは外（`scripts/lib/page.py:164` SKIP_LINK） | `style.css`（`body:has(.mj-video-page) .visually-hidden-focusable { color: var(--mj-v-accent) }` 相当。`#7fb3d5` on `#121212` は 8.31:1） | 新規（#182 の濃色版） | 小 | 現行で直す | 不要 | 再現 |
| G1-02 | 全ページ（共通ナビ） | ナビ項目のフォーカスが地とほぼ同色（1.29:1）。虫眼鏡 `a.btn` は枠も影も無くフォーカスが見えない。ドロップダウン項目は背景 `#f8f9fa`（白との比 1.04:1）に変わるだけ | `.nav-link` の `box-shadow: rgba(13,110,253,.25) 0 0 0 4px`（Bootstrap 既定）、`#1c375e` vs `#212529` = 1.29:1。虫眼鏡は `outline:none`・`box-shadow:none`・`:focus-visible` true（主セッション計測）。`style.css` にナビのフォーカス規則が無い | `style.css`（`.navbar .nav-link:focus-visible`・`.navbar .btn:focus-visible`・`.dropdown-item:focus-visible`） | 重複: #186（#178 の修正範囲外に見える） | 小 | 現行で直す | 不要 | 再現（`.nav-link`・虫眼鏡）／ドロップダウン項目は未検証 |
| G1-03 | 全系統 | 共通ナビの position が fixed（型A・title/・saikyo/）・sticky（動画系・live/）・relative（型C・B・D・手書き）の3通り。z-index も 1030／1029／1020／1019（`style.css:1316-1320` のコメントは 1020 のまま） | `style.css:361・1266-・1802-・2158-` | `style.css`・`navbar.js` | 重複: #417（保留） | 大 | 新サイトの要件 | 不要 | サブ再現（G1） |
| G1-04 | 型A（検索欄・ページ送り） | 入力欄とページ送りが UA 既定に近く、他系統と別物（入力: `2px inset #767676`・角丸 0・幅 100px。ページ送り: 角丸 4px・枠 `#ced4da`＝白地で 1.49:1。他の部品は角丸 6px）。他系統の入力欄は濃色で `#3a3a3a` | `style.css:145-147・716-723・1884-1888` | `style.css` | 新規 | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G1-05 | `title/` `saikyo/` `live/` `video_wayhome` | 濃色の入力欄・セレクトの枠 `#3a3a3a` が地 `#121212` と 1.65:1（アイコンとプレースホルダ〈8.29:1〉で識別は補われる） | `style.css:1888・1900・2221・2230`・`--mj-v-border` | `style.css` | 新規 | 小 | 見送り（補われている。新サイトの DESIGN.md で値を決める） | 不要 | サブ再現 |
| G1-06 | `title/` `saikyo/` `live/` 型A 手書き | 本文の列の幅・寄せが系統で違う。`title/` は 960px 中央、`saikyo/` は同じ 960px で**左寄せ**（1280px で右 320px が空白）、`live/` は本文 1100px 中央（ヒーローは x=48）、型A は幅いっぱい、手書きは x=32。`.mj-lead` の左右パディングも 4／16／48px | `style.css:80・1117・1526-1529・2111-2116・754-796` | `style.css`（`.mj-saikyo` の寄せ）。値の決定は DESIGN.md | 新規（#312 の延長） | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G1-07 | `404`・`resource_efficiency`・動画系・`live/`・`title/`・`saikyo/` | 見える見出しの大きさ・階層が系統でばらつく。h2 が 16／17.6／18／24px の4種類、h2 と h3 の差が 1.6〜2px（`title/`・`live/`）。`live/index`・`video_wayhome` は h1 が 13.6px で大きい文字は `<p>`／h2、`live/` のサブページは h1 が 48px。`title/` はパンくず、`saikyo/` は年度プルダウンが見出し代わり | 実測表は下の DESIGN.md の材料 (b) | `style.css`（`.mj-title-section h2/h3`・`.mj-live-*`・`.mj-video-episodes-heading`） | 重複: #283・#160 | 中 | 新サイトの要件 | 不要 | サブ再現（`title/` の h3 は CSS からの読みで未検証） |
| G1-08 | カードのある系統 | 角丸が 4／6／8／12／50%／999px の6種類。写真カード（12px＋影）と動画カード（8px＋枠・影なし）が `live/ourai/3/b16-a` の同じページに混在 | `style.css` の `border-radius` の集計、`1158-・1667-・2279-` | `style.css` | 重複: #270 | 中 | 新サイトの要件（DESIGN.md） | 不要 | サブ再現 |
| G1-09 | 全系統 | 色の直接指定が多く、トークンが系統ごとに複製されている。色リテラルは異なる値で55種類、変数定義に現れるのは13種類。変数を経由しない直接使用は138件・49種類。ほぼ黒の文字色が `#212529`／`#202124`／`#1a1a1a` の別値。灰色が Bootstrap・Google 系・動画系自作の3系列。フォーカスリングの様式が7種類。`--mj-v-fg`・`--mj-v-fg-muted` が3か所に定義 | `style.css` 全体（3429行。829／1860／2196 行など） | `style.css`（`:root` へ集約） | 重複: #270 | 大 | 新サイトの要件（DESIGN.md の受け皿） | 不要 | サブ再現 |
| G1-10 | `title/index`・`saikyo/2022` ほか写真カード | 写真の下端の名前（白文字 22px/700、グラデーション `rgba(0,0,0,.55)`）が、写真しだいでコントラスト不足。サンプル44枚のうち帯の画素の中央値が 4.5:1 未満が20枚、3:1 未満が2枚（2.80・2.98） | `style.css:1696-1714・2325-`。計測は文字を透明にして背景だけを撮り、ラベル下部65%の画素で白とのコントラストを計算（文字のある画素ではなく帯の画素の分布なので目安。`text-shadow` は WCAG の計算に入らない） | `style.css`（グラデーションの濃さ・影）。見た目の決定が要る | 新規 | 中 | 現行で直すかは判断（新サイトの DESIGN.md に「写真上の文字」の規則を置く案） | 不要 | サブ再現（計測方法の限界あり） |
| G1-11 | 型A の表・動画系 | 同じ役割のリンクで下線の有無・色が違う（`jpml_pros` の表は下線あり、`jpml_test`・`resource_logs`・`rh_paifu` は `.mj-plain` で下線なし＝周りの文字との差 1.73:1、動画系は `#7fb3d5`） | `style.css:295・856` | `style.css` | 重複: #26・#108 | 小 | 見送り（WCAG 1.4.1 の判断は未実施） | 不要 | サブ再現 |
| G1-12 | `jpml_links`・`resource_dictionary` | 16px のアイコンだけがリンクで、隣の文字（「公式サイト」など）はリンクの外。`rh_links` は文字リンク | `jpml_links.html:36-38`・`resource_dictionary.html:36-37` | 手書きの HTML | 新規 | 小 | 現行で直す | 不要 | サブ再現 |

#### G2 スマホ（11件。1件を外した）

幅は 390px と 360px、`viewport` のみ（`mobile: true` 不使用）。全指摘が「エミュレーションのみ」。

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| G2-01 | `title/` 全384・`saikyo/` 全17（確認: `title/index`・`title/houou/index`・`title/ourai/12`・`saikyo/index`・`saikyo/2022`・`saikyo/2012`） | ハンバーガーを開くと、メニューが 390×844 で 269px（本来 520px）、390×667 で 92px、360×640 で 65px にしか開かず項目が1〜4個しか見えない。固定のフィルタバーは開いたナビの高さ（575px）の位置に下がり、ナビとの間に白い隙間（390×844 で 250px）ができる | `max-height: calc(100vh - var(--mj-title-nav-h))`（`style.css:1835-1838`・`2188-2191`）。`--mj-title-nav-h` を `assets/title.js:47`・`assets/saikyo.js:47-69` が `navbar.offsetHeight` で更新し、開いたナビの高さ自身が max-height に入る循環。同じ問題の帰り道向けの修正が `style.css:1299-1313`（WH-17）にある | `style.css`（2か所。帰り道と同じ 80vh 固定）・`assets/title.js`・`assets/saikyo.js` | 新規（帰り道は WH-17 で修正済み） | 小 | 現行で直す | **要**: https://ryoei.pro/title/ と https://ryoei.pro/saikyo/ をスマホで開き、ハンバーガーを押して全項目が見えて、バーとの間に隙間が出ないか | 再現（390×844 で max-height 269px、390×667 で 92px） |
| G2-02 | 固定ナビの型A・A'（`jpml_test`・`resource_logs`・`video_live`・`video_en`・`rh_paifu`・`video_mtsuku`・`jpml_pros`・`rh_results`・`rh_results_detail`） | ハンバーガーを開き「リソース」のドロップダウンを開くと、nav が 810px（390×667）で画面を超え、最後の項目（良栄）と虫眼鏡が画面の外。ホイールで動かしても nav は動かず、内側スクロールも無い | `.navbar-collapse` の `max-height: none`・`overflow-y: visible`、nav は `position: fixed`（主セッション計測 810.77px、画面 667px）。内側スクロールの指定は動画系・saikyo・title だけ | `style.css`（固定ナビ共通の `.navbar-collapse.show` に `max-height`・`overflow-y: auto`）。#417 と一緒に決める | 重複: #417 に近い（現象は別） | 小 | 現行で直す | **要**: https://ryoei.pro/jpml_test.html をスマホで開き、ハンバーガー→「リソース」を押して最後の項目に届くか | 再現 |
| G2-03 | 検索アイコンのある12ページ（型A 7＋`jpml_pros`＋`houou_leagues`・`ouka_leagues`・`houou_results`・`ouka_results`・`wrc_results`） | 390・360px で虫眼鏡が「R」とメニューの下の行に落ち、ナビが 94.8px（画面の 11〜14%。ほかの20ページは 56px）。虫眼鏡は 50×38.8px（44px 未満） | `navbar.js:13-14,88`（虫眼鏡が collapse の後に置かれ flex-wrap で折り返す）。主セッション計測 nav height 94.77px。出所 G1-03・G2 | `navbar.js`・`style.css`（`.navbar .btn` の配置と最小サイズ） | 新規（#163・#245 周辺） | 小 | 現行で直す | **要**: https://ryoei.pro/jpml_pros.html をスマホで開き、虫眼鏡の位置と大きさを見る | 再現（nav の高さ） |
| G2-04 | 全ページ（`navbar.js`） | ナビのブランド「R」が 14×40px、ハンバーガーが 56×40px（44px 未満。メニュー項目 366×64px・ドロップダウン項目 364×44px・ページ送り 58×44px・共有 44×44px は良好） | `getBoundingClientRect`（390 と 360 で同じ） | `navbar.js`・`style.css`（`.navbar-brand` に 44px の最小サイズ） | 新規 | 小 | 現行で直す | 不要 | サブ再現 |
| G2-05 | `jpml_pros`・`title/` `saikyo/` `live/` `video_wayhome` | 固定・sticky のバーが画面を占める割合（390×844）: `jpml_pros` で検索欄を開くと 331px＝39%（閉じても 147px＝17%）、`title/`・`saikyo/`・`live/` は 171px＝20%（360×640 で 27%）、帰り道一覧 17% | 実測 | `style.css`。新サイトの要件 | 重複: #23 | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G2-06 | `video_wayhome`・`wayhome/` 個別 | 文字を 200% にする（ルート `font-size` の近似）と、ヒーロー（高さ 608px、`overflow:hidden`）に中身が収まらず、系列名の h1 が上端の外（y=−69）、選手名の h2 が nav の裏になる。個別ページはエピソード題の h1 が 167px はみ出す。150%（360×640）でも個別の2ページで題が切れる | `style.css:877`（`height: clamp(420px, 72vh, 640px)`・`overflow: hidden`）。主セッション計測 h1 top=−69.25（200%、390×844） | `style.css`（`.mj-video-hero` の高さを `min-height` に） | 新規 | 小 | 現行で直す | **要**（文字の拡大は近似）: https://ryoei.pro/video_wayhome.html と https://ryoei.pro/wayhome/atD2e-NgnKw.html で、iPhone の「文字サイズ」を最大にして見出しが切れないか | 再現（`video_wayhome`）／個別ページはサブ再現 |
| G2-07 | `rh_results_detail` | 390px で数値だけのセル 1,281 個のうち 471 個（37%）が折り返し（360px で 663 個＝52%）、着順 `23214` が `2321/4`、得点 `-42.7` が `-42./7`、日付が3行になる。1280px では 0 | `style.css:687-690`（`#rh_results_detail_table td { white-space: normal; overflow-wrap: anywhere; }`）。コメント（675-686）は旧版の折り返しに合わせたと説明 | `style.css`（着順・得点・日付の列だけ `white-space: nowrap`） | 重複: #274 | 小 | 現行で直す | **要**: https://ryoei.pro/rh_results_detail.html をスマホで開き、着順・得点が桁の途中で折れていないか | サブ再現（主セッションは折り返しの発生だけ確認。個数は数え方が違い一致せず） |
| G2-08 | `jpml_pros`・`resource_logs`・`rh_paifu`・`jpml_links`・`wayhome/`・`title/` のパンくず | 表の中のリンクの高さ 15〜20px（`jpml_links` は 17px、パンくず 20px）。24px 未満だが WCAG 2.5.8 の間隔の例外は満たす（`jpml_links` は中心間 24px でぎりぎり） | 実測（400組で間隔 8px 未満は 0） | `style.css`（`.mj-table a`）・`jpml_links.html` | 新規 | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G2-09 | `resource_efficiency` | 見える h1（`.mj-page-heading`、16px）が x=0・y=56 でナビの直下に隙間なく置かれ、本文の左端（x=32）とそろわない（390・1280px とも） | 主セッション計測 left=0、`.mj-margin-text` left=32 | `scripts/generate_resource_efficiency.py`・`style.css`（`.mj-page-heading` の余白） | 新規 | 小 | 現行で直す | 不要 | 再現 |
| G2-10 | `houou_leagues`・`ouka_leagues` | SVG の軸ラベルの実効サイズが 390px で 9.2px・360px で 8.5px（1280px は 11.0px。凡例は約 16px） | viewBox の縮小率から換算 | `scripts/generate_leagues.py` 系（SVG の font-size） | 新規 | 中 | 新サイトの要件 | 不要 | サブ再現（換算値） |
| G2-11 | `title/ourai/12` ほか | 文字 200% の近似で、カードのラベルが写真の顔に重なる。`.mj-table` の 13px・入力欄の 16px は px 指定のため近似では拡大されず、実機の設定で拡大されるかは未確認 | 200% の近似（14ページ） | `style.css` | 新規 | 中 | 実機で確かめてから決める | **要**: iPhone の「文字サイズ」を最大にして https://ryoei.pro/title/ourai/12.html と https://ryoei.pro/jpml_pros.html を見る | サブ再現（近似のみ） |

**外した指摘（1件）**: G2 の「検索アイコンを押すと 8×8px の空の `#searchBoxes` が開く、5ページ」。主セッションの再現では、`houou_leagues`・`ouka_leagues` は選択欄と「表示」ボタンが入っていて空ではなかった（52px 高）。空だったのは `houou_results`・`ouka_results`・`wrc_results` の3ページだけで、これらは Charts（gstatic が届かない）が検索欄を作るため、このセッションの環境が原因の可能性が高い。未検証として扱い、指摘から外した。

#### G3 状態の表示（12件）

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| G3-01 | 型A 7ページ（`jpml_pros`・`jpml_test`・`resource_logs`・`video_live`・`video_en`・`rh_paifu`・`video_mtsuku`） | 出ない文字列を入れると見出し行だけの空の表になり、画面に出るメッセージが無い。`#result_count` の「0件を表示しています」は visually-hidden。`?name=存在しない` も同じ。`resource_logs?name=存在しない` は名前セレクトが「名前を選択」のままで、なぜ空かの手がかりが画面に無い。`jpml_pros` の検索は「アオ」（カタカナ）・「ＡＯＫＩ」（全角）が 0 件で「あお」は 11 件 | `jpml_pros.html:45`・`jpml_test.html:40` の `class="visually-hidden"`、`table.js`。出所 G3・G4 | `table.js`・`jpml_pros.js`・`scripts/lib/page.py` | 重複: #247・#420・#415 | 中 | 新サイトの要件（#247 は open） | 不要 | サブ再現 |
| G3-02 | `title/` の検索 | `search.json` の取得が即失敗（abort・404・不正 JSON）すると、検索語を入れても結果欄が空白のまま。失敗メッセージが出ない（`load()` が差し込んだ li を 120ms 後の `applyFilter()` の `replaceChildren()` が消す。`loading` を失敗時に戻さないため再試行もされない）。応答が1秒後に失敗するときだけ表示され、その文には専用のスタイル・`role` が無い | `assets/title.js:84,123-140,232-246`、`.mj-title-results-error` の規則が `style.css` に無い | `assets/title.js`・`style.css` | 新規 | 小 | 現行で直す | 不要 | サブ再現 |
| G3-03 | 型A（`jpml_pros`・`resource_logs`・`video_live`・`video_en`・`rh_paifu`） | 外部画像の失敗の応答が DOMContentLoaded より前に返ると、`data-fallback` への差し替えが効かず壊れた画像アイコンと alt が残る（`jpml_pros` 22/22、`video_live` 11/11、`resource_logs` 11/11。応答を2.5秒遅らせると差し替わる）。`resource_logs`・`video_live` は行が 16px 高に潰れる。`video_wayhome` の先頭カードの maxres→hq も同様 | `table.js:50-58`・`jpml_pros.js:19-26` は DOMContentLoaded でリスナーを登録。`assets/saikyo.js:34`・`title.js:23` に「実行前に失敗した画像を拾う」処理がある | `table.js`・`jpml_pros.js`・`video_wayhome.js` | 重複: #411 | 小 | 現行で直す（#411 の範囲） | 不要 | サブ再現（404 即返し。実網では未検証） |
| G3-04 | `live/ourai/12`・`wayhome/` のヒーロー・`title/ourai/12` の決勝動画サムネイル | `data-fallback` が無く、壊れた画像アイコンが黒いヒーロー（375×640）の左上や動画サムネイル（358×201）に出る。`alt=""`、代替の色面も無い | ヒーローの `img` に `data-fallback` 属性無し | `scripts/lib/`（live・wayhome 生成）・`wayhome_episodes.js`・`assets/live.js` | 新規（#352・#411 に近い） | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G3-05 | `houou_results`・`ouka_results`・`wrc_results` | Charts のローダーが取れないと `google is not defined` で `main` が空（高さ 700px）、読み込み中表示も失敗メッセージも無い。クエリ失敗時は `alert()` で生のエラー文字列 | `houou_results.js` 冒頭（`google.charts.load` を無条件に呼ぶ）、`handleQueryResponse` の `alert('Error in query: ...')`（コードから） | `houou_results.js`・`ouka_results.js`・`wrc_results.js` | 重複: #7 | 大 | 新サイトの要件（据え置き〈#111〉。現行では直さない） | 不要 | サブ再現（空の main）／alert はコード |
| G3-06 | `jpml_pros` | 並べ替え中の列を示すのが `aria-sort` だけで、見た目の矢印・色が無く、hover も変わらない（`all:unset`）。押した後も見出しが押す前と同じ | `style.css` に `[aria-sort]` の規則が無い。`jpml_pros.js:138` は属性を更新するだけ | `style.css`・`jpml_pros.js` | 新規（#416 に近い） | 中 | 新サイトの要件（見た目の新しい決定が要る） | 不要 | サブ再現 |
| G3-07 | `saikyo/`・`title/`・`live/`・`wayhome`（共有ボタンのある約1,349ページ） | クリップボードも `execCommand` も使えないとき「コピーできませんでした。URLを選択してコピーしてください: https://…」を8秒出すが、トーストは `pointer-events: none` で URL を選択できない | `style.css:1089-1110`（`.mj-share-toast`、`pointer-events: none`）、`assets/share.js:62-66`（主セッションがコードを確認） | `style.css`・`assets/share.js`（失敗時だけ `pointer-events: auto`、または入力欄に URL を出す） | 新規 | 小 | 現行で直す | 不要 | コード（実際の範囲選択は未検証） |
| G3-08 | `resource_logs`（2,630行）・`video_live`（2,331行）・`jpml_pros`（1,099行） | JS を遅らせると全行が表示され（`resource_logs` の `scrollHeight` 258,522px）、ページ送りは有効で状態テキストが空のまま。JS 後に 100 行へ縮む。ローカルの first-paint は 60〜92ms、DCL は 0.7〜1.3秒 | 計測（HTML は 1.1〜2.1MB） | `scripts/lib/page.py`（初期 HTML に `hidden` を焼く）・`table.js` | 新規 | 中 | 新サイトの要件 | 不要 | サブ再現（JS 遅延。実網のタイミングは未検証） |
| G3-09 | `houou_leagues`・`ouka_leagues` の `?name=` | JSON 取得中は焼き込みの既定選手（白鳥翔）の折れ線と凡例が出て指定と別人。取得失敗では黙って既定選手のまま（URL と表示が食い違い、通知なし。データ無しの名前だけ通知あり） | `leagues.js:43-52`（`catch` が空。主セッションがコードを確認） | `leagues.js`・`scripts/generate_*leagues*` | 新規 | 小 | 現行で直す | 不要 | コード（失敗時の凡例はサブ再現） |
| G3-10 | `404.html` | 本文に「トップへ戻る」リンクもナビ以外の導線も無く、スマホ幅でテキストが右端にくっつく（`.mj-margin-text` の margin-right 0） | 主セッション計測: margin-left 32px・margin-right 0px・right=390=画面幅、`main` 内のリンク 0 個。`404.html:30-40` | `404.html`（手書き） | 新規 | 小 | 現行で直す | 不要 | 再現 |
| G3-11 | 型A のページ送り | `.mj-pager-button` に hover・`:active`・`:focus-visible` の規則が無く、通常・hover・active の計算値が同じ。先頭の「前へ」の無効（`aria-disabled`、opacity .5）は読み取れる | `style.css:716-730` | `style.css` | 新規（#248 周辺） | 小 | 見送り（見た目の新しい決定が要る。DESIGN.md で決める） | 不要 | サブ再現 |
| G3-12 | `houou_leagues`・`ouka_leagues` | 検索欄を開き、選択が空のまま「表示」を押すと何も起きず、メッセージも無い | `leagues.js:23-27`（`if (select.value)` のみ。主セッションがコードを確認） | `leagues.js`（`disabled` か短い案内） | 新規 | 小 | 現行で直す | 不要 | コード |

問題が見つからなかった状態（G3 の報告）: 0件メッセージは `video_wayhome`・`saikyo/`・`title/`・`live/` で画面に見え `role=status`。`saikyo/2022` の52枚・`title/` 入口の20枚・`title/ourai/12` の4枚は `img/avatar.svg` に差し替わる。`prefers-reduced-motion: reduce` は14ページで `transition`/`animation` が 0。`title/` の写真カード・共有メニュー・ハンバーガー・ドロップダウンの開閉と `aria-expanded` は正常。濃色領域のフォーカスは白のリングで見える。

#### G4 実際の操作（9件）

静的なリンク判定（`href`・`src`・`og:image` などサイト内参照の計約3.6万件・`script src` 約5.5千件を、ファイルの有無と `_redirects`・`wrangler.jsonc`〈`html_handling: "none"〉・`#アンカー` で判定。対象は対象外6ページと `books/` を除く HTML 1,368ページ）で、**リンク先が無い件数は 0 件、アンカー切れも 0 件**（判定器が失敗を検出できることは存在しない URL 4件で確認済み）。sitemap 6ファイルの `<loc>` 計658件も全件存在。ナビの全26リンクは存在し、`resource_calendar.html`・`resource_books.html` は `_redirects` で外部へ 301。何もしないボタンは29ページで列挙してクリックし、空振りは1ページ目の「前へ」（`aria-disabled`、意図どおり）のみ。ページ全体の横スクロールは 390・360px とも `jpml_pros`（934px、既決）だけ。

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| G4-01 | `navbar.js` を読む全ページ | ナビの `<a role="button">`（ドロップダウンのトグル・虫眼鏡）は Space で動かず、ページが 630px スクロールする。Enter では開き、Esc で閉じてフォーカスはトグルへ戻る | 再現: `jpml_test.html` で `.nav-link.dropdown-toggle` にフォーカスして Space → `aria-expanded="false"`・`scrollY=630`。Enter → `true`。`navbar.js:14,31-` | `navbar.js`（Space の keydown、または `<button>`） | 重複: #186 に近い（Space の項目は無いと見る） | 小 | 現行で直す | 不要 | 再現 |
| G4-02 | 全ページ | ナビに現在地の表示が無い（26項目で `.active`・`aria-current` が 0 件） | 25ページを開いて確認 | `navbar.js`・`style.css` | 重複: #250 | 小 | 現行で直す（#250 の範囲） | 不要 | サブ再現 |
| G4-03 | `houou_leagues`（`ouka_leagues` も同じ作りで未検証） | `?name=蒼井ゆりか` でグラフは蒼井ゆりかになるが、`#selectbox` は「名前を選択」のまま（折りたたまれた検索欄の中） | `leagues.js:32-60`（`name` を折れ線と凡例にだけ反映） | `leagues.js` | 新規 | 小 | 現行で直す（低） | 不要 | サブ再現 |
| G4-04 | `video_live`（型A の2列系も同じ作りと推測） | 一番下の「次へ」を Enter で押すと、次ページの先頭ではなく下の方（`scrollY` 0→3018、再度 4324）に居続ける。フォーカスは「次へ」に残る（#184 の意図どおり） | scrollY の実測。意図の有無は未確認 | `table.js` | 新規（#248・#249 周辺の可能性） | 小 | 現行で直すかは判断（#184 の意図との兼ね合い） | 不要 | サブ再現 |
| G4-05 | `jpml_pros` | `target="_blank"` の4,401件のうち3,259件に「（新しいタブで開く）」の予告が無い。2,530件は同サイト内リンク（「3回」「E3」など）。画像リンクには予告がある。ほかの13ページは 0 件 | `scripts/generate_jpml_pros.py:150-153` の `get_internal_link()`（`NEW_TAB_HINT` が付かない） | `scripts/generate_jpml_pros.py` | 新規（#183 は画像リンクのみ） | 小 | 現行で直す | 不要 | サブ再現（静的集計） |
| G4-06 | `resource_dictionary` | 開始タグの無い `</a>` が2つ（36行・40行。`<a>` 7に対し `</a>` 9）、`<p>` 2・`</p>` 3（42行の余分）。ダウンロードリンク4本のアクセシブルネームがすべて「ダウンロード」（画像の alt）で、どのファイルか分からない（隣の「Microsoft IME」「Google 日本語入力」はリンクの外）。`dic/` の4ファイルは存在 | 主セッションがタグ数を数えた | `resource_dictionary.html`（手書き） | 新規 | 小 | 現行で直す | 不要 | 再現（タグ数）／読み上げは未検証 |
| G4-07 | `rh_links` | 「GitHub」のリンク先 `https://github.com/retroeater/mj` は未ログインで 404（非公開）。外部リンク16本に `target="_blank"` も予告も無い（`jpml_links` は全件あり） | 主セッション: 未認証の `curl https://github.com/retroeater/mj` が 404、`api.github.com/repos/retroeater/mj` は 200 | `rh_links.html`（手書き） | 新規 | 小 | 現行で直す（リンクを外すかは平野さんの判断） | 不要 | 再現（404）。vis.js・ImportJSON・Google Analytics などの掲載が現状と合うかは未確認 |
| G4-08 | `title/index`（ほかの選択欄のあるページも同じと推測） | `#title_select`（大会）にフォーカスして ↓ を押すと、一覧を見る前に即座に `/title/houou/` へ遷移する。`houou_leagues` は「表示」ボタン方式（#180） | `assets/title.js:63`（`change` で遷移）。`saikyo/` の年度プルダウンは未検証 | `assets/title.js` | 新規（#180 の方針と食い違う） | 小 | 現行で直すかは判断 | 不要 | サブ再現 |
| G4-09 | 共有ボタンのある約1,349ページ | 「URLをコピー」の後、メニューが閉じて `document.activeElement` が BODY になり、フォーカスが先頭へ戻る（クリップボードの内容は正しい） | `assets/share.js` | `assets/share.js`（コピー後に共有ボタンへ戻す） | 新規 | 小 | 現行で直す（低） | 不要 | サブ再現 |

#### G5 公開前の基本（12件）と集計

機械集計（`html.parser`、1,368 HTML）の結果。

| 項目 | top(20) | title(384) | saikyo(17) | wayhome(39) | live(908) |
|---|---|---|---|---|---|
| title 無し／description 無し | 0／1（`404.html`、既決） | 0／0 | 0／0 | 0／0 | 0／0 |
| title・description の重複 | 0 | 0 | 0 | 2ページ（1組） | 0 |
| title 長 min/中央/max | 11/22.5/32 | 34/36/52 | 23/27/27 | 38/42/50 | 16/35/46 |
| h1 = 0／h1 ≥ 2 | 8／0 | 0／0 | 0／0 | 0／0 | 0／0 |
| `lang`≠ja／viewport 無し／favicon.ico 無し | 0／0／0 | 0／0／0 | 0／0／0 | 0／0／0 | 0／0／0 |
| apple-touch-icon 無し／twitter:image 無し／og:locale 無し | 20／20／20 | 384／384／384 | 17／17／17 | 39／39／39 | 908／908／908 |
| og:image が実在しないローカルファイル | 0 | 0 | 0 | 0 | 0 |
| robots=noindex | 1（`404`） | 0 | 0 | 0 | 908（正） |
| canonical あり | 0 | 384 | 17 | 39 | 0 |

h1 の無いトップ階層は8ページ（`houou_leagues`・`ouka_leagues`・`houou_results`・`ouka_results`・`wrc_results`・`jpml_links`・`resource_dictionary`・`rh_links`）。対象外のランキング3ページを足すと11で、#283・#486 の件数と一致。noindex は `live/`・`houou_race`・`books/index`・`404` だけで意図どおり。sitemap: 4ファイルの URL 全件にファイルがあり、noindex のページは載っておらず、公開ページで載っていないものは無い。`_redirects` の `/title/<大会>` 20件・先頭の `/ /index.html 200` は正常。仮の文言（Lorem・ダミー・TODO・準備中・サンプル など）は全1,368 HTML で 0件、`href="#"`・`href=""` は 0件（`navbar.js` が JS で出すトグルを除く）。

| ID | ページ | 事象 | 根拠 | 直す先 | 分類 | 大きさ | 行き先 | 実機 | 検証 |
|---|---|---|---|---|---|---|---|---|---|
| G5-01 | `404.html`（本番では存在しない任意のパスで配信） | 存在しない URL が深い階層（`/title/nothing/here.html`）だと、CSS・Bootstrap JS・navbar.js が 404 になり、素の Times 系フォントでナビも出ない。`/nothing.html` は正常。出所 G3・G4・G5 | `404.html:8,10,11,13,23` が相対参照（`favicon.ico`・`assets/vendor/…`・`style.css`・`navbar.js`）。主セッション: 404 応答をモックで返すと `/title/nothing/style.css` など4件が 404、`nav` なし、`font-family` が "Times New Roman"。他の手書きページはルート相対の規約（#162） | `404.html`（`href="/…"`・`src="/…"`） | 新規 | 小 | 現行で直す | **要**（本番の挙動は未検証。`not_found_handling: 404-page` が要求パスのまま返す前提）: https://ryoei.pro/title/nothing/here.html を開き、ナビと書式が出るか | 再現（モック。本番は未検証） |
| G5-02 | `ouka_results.js`（`ouka_results.html`） | `console.log(season)`・`console.log(formattedClass)` がデバッグ出力のまま残り、行ごとの呼び出しでコンソールに大量に出る。`houou_results.js`・`wrc_results.js` には無い | `ouka_results.js:281,285`（grep）。ほか `league_ranking.js:664` に `// TODO`（対象外ページ向け） | `ouka_results.js` | 新規 | 小 | 現行で直す（Charts の旧方式のため小さな削除のみ） | 不要 | 再現（grep）。画面への影響は未検証 |
| G5-03 | 全ページ | `og:locale` が無い | 集計。`scripts/lib/page.py` の head に出力が無い | `scripts/lib/page.py`・`scripts/generate_jpml_pros.py`・手書き3ページ | 重複: #267 | 小 | 現行で直す（#267 の範囲） | 不要 | サブ再現（集計） |
| G5-04 | `index.html` 以外の全ページ | `apple-touch-icon` の link が無い（`/apple-touch-icon.png` は存在し、ルートのフォールバックで実害なしと判断済み〈site-findings「favicon」〉）。ホーム画面に追加したときの名前は `<title>` の先頭になる | 集計 | `scripts/lib/page.py` | 重複: #202 | 小 | 現行で直す（#202 の範囲） | 不要 | サブ再現（iOS での見え方は未検証） |
| G5-05 | `wayhome/9drvti0iySM.html` と `wayhome/FrZWVou_Z6g.html` | title・description・og:title・og:description が完全に同一（同じ人・同じ大会の別動画2本）。ほかに title の重複は無い | 重複検出で1組 | `scripts/generate_wayhome_episodes.py`（動画の区別語を足すか） | 重複: #5 に近い | 小 | 現行で直すかは判断 | 不要 | サブ再現 |
| G5-06 | `wayhome/` 39ページ | `<title>` は「… ｜ 帰り道ついていってイイっすか ｜ ryoei.pro」、`og:title` は「… ｜ 帰り道 ｜ ryoei.pro」（#339 の SERIES_SHORT による意図的な短縮の可能性が高い） | データ集計 | — | 決定済みの可能性（docs/notes/static-generation.md「og_title」節に、og:title を「<大会名> <選手名> \| 帰り道 \| ryoei.pro」にする記載） | 小 | 見送り | 不要 | コード（文書の記載と一致） |
| G5-07 | `sitemap.xml`・`sitemap-pages.xml` | 冒頭コメントの件数が古い（「27ページ中25件」に対し実体 23、「ルート直下25ページ」に対し 23、「wayhome 38枚」に対し 39） | 主セッション: `sitemap-pages.xml` の `<loc>` 23、`wayhome/*.html` 39、コメント「27ページ中25件」 | `sitemap.xml`・`sitemap-pages.xml` のコメント（lastmod とは別。#265 の「lastmod を手で書き換えない」の対象外） | 新規 | 小 | 現行で直す | 不要 | 再現 |
| G5-08 | `llms.txt`（手書き） | 件数が現物とずれている: 「ルート直下の24ページ」→実体 23、「帰り道各話ページ38枚」（2か所）→ 39、放送対局「2,332本」→ `video_live` の description は 2,331、「選手691名」→ `houou_leagues` の description は 690、「164名」→ 163（最後の2つは未検証） | 主セッションが 24／38／2,332 の食い違いを確認（`llms.txt:24,30,44,64,65`、`video_live.html` の description は 2,331） | `llms.txt` | 新規（#161 の更新ルールに関わる） | 小 | 現行で直す | 不要 | 再現（24・38・2,332）／690・163 は未検証 |
| G5-09 | `jpml_pros`・`リンク` ほか | 呼称のゆれ: `jpml_pros` は nav「プロ」・h1「プロ雀士データベース」・description「麻雀プロ」。nav の「リンク」が連盟用と良栄用で同名で、title の先頭も「リンク」。`video_wayhome` は nav「帰り道」・title/h1「帰り道ついていってイイっすか」（略称と正式名。意図的な可能性） | `navbar.js` と各 title・h1 の対照表（G5 報告 D 節） | `navbar.js`・各 title | 重複: #283・#5 | 中 | 新サイトの要件 | 不要 | サブ再現 |
| G5-10 | トップ階層の20ページ | title の第1セグメントが一般名詞だけ（「成績詳細」「リンク」「ログ」「辞書」「牌効率」「プロ」「Mつく」「English」）。全ページに「｜ryoei.pro」が付き、9字台は無い（最短は `404` の 11字） | 集計（min 11／中央 22.5／max 32）。決定は seo-bing.md BNG-04 | `scripts/lib/page.py` ほか | 重複: #5・#486 | 小 | 見送り（#5 に任せる） | 不要 | サブ再現 |
| G5-11 | `sitemap-books.xml` | サイトルートで配信され、`sitemap.xml` のインデックスからは参照していない（#97・books-freeze.md で意図的）が、`/sitemap-books.xml` で直接取得でき、noindex の `books/` 191ページを載せている。`.assetsignore` には無い | ファイルの有無 | `.assetsignore` か、公開時にインデックスへ足す | 新規 | 小 | 見送り（凍結中。Search Console への送信状況は未検証） | 不要 | サブ再現 |
| G5-12 | `title/`（384）・`saikyo/`（17） | canonical を持つ（既決は `wayhome/` のみ）と G5 が挙げたが、`title/` と `saikyo/` は例外として決定済み | docs/notes/title-pages.md:21（「サイトの既定〈#113、出さない〉の例外。`saikyo/` と同じ形」）、docs/notes/saikyo-page-design.md:263-267。（docs/notes/static-generation.md の「他27ページは canonical 無し」はこの2系統を含まず古い） | — | **決定済みで対象外**（title-pages.md「ページと生成」、saikyo-page-design.md） | 小 | 見送り | 不要 | コード（主セッションが両文書を確認） |

#### 集計

| グループ | 件数 | 新規 | 既存 issue と重複 | 決定済みで対象外 | 小 | 中 | 大 |
|---|---|---|---|---|---|---|---|
| G1 デザインの一貫性 | 12 | 6 | 6 | 0 | 5 | 5 | 2 |
| G2 スマホ | 11 | 8 | 3 | 0 | 7 | 4 | 0 |
| G3 状態の表示 | 12 | 9 | 3 | 0 | 7 | 4 | 1 |
| G4 実際の操作 | 9 | 7 | 2 | 0 | 9 | 0 | 0 |
| G5 公開前の基本 | 12 | 7 | 4 | 1 | 11 | 1 | 0 |
| **合計** | **56** | **37** | **18** | **1** | **39** | **14** | **3** |

- 行き先の案（上の表の「行き先」欄を数えた値）: **現行で直す 33件**（うち「判断」が付くもの4件: G1-10・G4-04・G4-08・G5-05。「低」が付くもの2件: G4-03・G4-09。判断が付かない確定寄りは27件）、**新サイトの要件 15件**（G1-03・04・06・07・08・09／G2-05・08・10／G3-01・04・05・06・08／G5-09）、**見送り 7件**（G1-05・G1-11・G3-11・G5-06・G5-10・G5-11・G5-12）、**実機で確かめてから決める 1件**（G2-11）。合計 56件。
- 主セッションが実物で再現したもの **15件**（G1-01・G1-02・G2-01・G2-02・G2-03・G2-06・G2-07〔折り返しの発生のみ〕・G2-09・G3-10・G4-01・G4-06・G4-07・G5-01・G5-02・G5-07。G5-08 は24・38・2,332の3点のみ）。コードを読んで確かめたもの 5件（G3-07・G3-09・G3-12・G5-06・G5-12）。**それ以外は、サブエージェントが再現した結果を主セッションが再現していないため「未検証」**（表の「検証」欄の「サブ再現」）。再現せず外したもの 1件（G2 の空の検索欄、上記）。
- 「現行で直す（小）」のうち主セッションが再現していないもの: G1-12・G2-04・G3-02・G3-03・G4-03・G4-05・G4-08・G4-09・G5-03・G5-04・G5-05・G5-08（690・163の2点）。採否を決める前に再現の確認が要る。

#### 実機確認が要るもの（平野さんが iPhone で開く本番の URL）

| ID | URL | 見る点 |
|---|---|---|
| G2-01 | https://ryoei.pro/title/ ・ https://ryoei.pro/saikyo/ | ハンバーガーを押して全8項目が見えるか、固定バーとの間に白い隙間が出ないか |
| G2-02 | https://ryoei.pro/jpml_test.html | ハンバーガー→「リソース」を開いて最後の項目（良栄）に届くか、画面の下で切れないか |
| G2-03 | https://ryoei.pro/jpml_pros.html | 虫眼鏡が2行目に落ちていないか、押せる大きさか |
| G2-06 | https://ryoei.pro/video_wayhome.html ・ https://ryoei.pro/wayhome/atD2e-NgnKw.html | iPhone の「文字サイズ」を最大にして見出しが切れないか |
| G2-07 | https://ryoei.pro/rh_results_detail.html | 着順・得点・日付が桁の途中で折り返されていないか |
| G2-11 | https://ryoei.pro/title/ourai/12.html ・ https://ryoei.pro/jpml_pros.html | 「文字サイズ」を最大にして、ラベルが顔に重ならないか・表の文字が拡大されるか |
| G5-01 | https://ryoei.pro/title/nothing/here.html | 存在しない深い URL でナビと書式が出るか（本番の挙動は未検証） |

### DESIGN.md の材料（G1 の報告をそのまま）

#### (a) 既決の値の表

`style.css` の行番号は G1 が実際に開いて確かめた（2026-10-06 の作業ツリー）。

| 項目 | 値 | 適用範囲 | 出典 |
|---|---|---|---|
| トーン | 静か。白基調・余白多め・装飾を削ぐ。濃色・高コントラスト・動き多めは採らない | サイト全体 | docs/new-site-design.md §2「トーン」 |
| ダークモード | 新サイトは最初から両モードで色を決める。現行は未対応のページが多い | 新サイト | docs/new-site-design.md §2 |
| 濃色固定の例外 | 動画系（`video_wayhome` 系）は OS の設定に関わらず常に濃色。例外はこの1系統に限る。配色のみで動きには及ばない | `video_wayhome`・`wayhome/` | docs/new-site-design.md §2・§12（2026-09-12決定） |
| 画面の原則 | 選手一覧は表形式（カードグリッドにしない）。写真は小さなアバターで主役にしない | 選手データベース | docs/new-site-design.md §2「選手一覧は表形式を維持する」 |
| ヒューマンインターフェース原則 | 一貫性、タッチ領域、エラーの建設的な表示など（採用項目の一覧） | 新ページ・レビュー | docs/new-site-design.md §2「画面設計の原則」 |
| 装飾 | 角丸・影・グラデーションは最小限 | 動画系のパイロット | docs/new-site-design.md §12「トーン」 |
| 動き | `transition` を付けない。`prefers-reduced-motion` を尊重。自動再生なし | サイト全体 | style.css:1021-1022、docs/new-site-design.md §12「第3段」 |
| フォント | `"Hiragino Sans","Yu Gothic Medium","Meiryo",sans-serif`、`font-feature-settings:"palt"`、`font-variant-numeric:tabular-nums`。Web フォントなし | 全ページ（`index.html` 除く） | style.css:1-18（宣言は 11-18） |
| 本文色 | `#212529`（Bootstrap 既定） | 明色ページ | style.css に定義なし。docs/notes/site-findings.md「リンクの配色」表で前提 |
| リンク色 | 通常 `#14459b`／ホバー `#0d2f6e`。`--bs-link-color(-rgb)`・`--bs-link-hover-color(-rgb)` を `:root` で上書き | 明色ページ | style.css:39-44、docs/notes/site-findings.md（#26/#108、2026-09-12） |
| リンクの下線 | `text-underline-offset:0.2em`、`text-decoration-thickness:1px`。画像だけを包むリンクは下線なし | 全ページ | style.css:52-55、63-65 |
| 表の文字 | AAA（7:1）を満たす。リンク 7.97:1 など | `.mj-table` | docs/notes/site-findings.md「表の背景色の方針」、style.css:337-343 |
| 縞・見出し背景 | 偶数行と見出し `#f2f2f2`、奇数行 `#ffffff`。上限は `#e4e4e4`（リンク 7.02:1）。縞自体に WCAG の数値基準は無い | 型A・A' の表 | style.css:308-313、332-345、#344・#345（2026-09-16） |
| 表のホバー行 | `#d6e9f8`（`!important`、縞より後に書く） | 型A・A' | style.css:349-352 |
| フォーカス枠（表） | `#1B3B6F` 2px、offset -2px（3:1 を満たす） | 並べ替えボタン | style.css:515-518、docs/handover.md「表の色とアクセシビリティ」 |
| 表の文字サイズ | 13px、`line-height:1.25`、セル `padding:4px`、見出しは中央揃え・太字 | `.mj-table` | style.css:268-285、308-312 |
| 行の高さ | 画像 48px＋上下 4px＝56px。`contain-intrinsic-size:auto 56px` | `jpml_pros` | style.css:315-320、560-574 |
| タップ領域 | 最小 44×44px（`min-height:44px`） | ナビ項目、絞り込み欄、ページ送り、動画ボタン、共有ボタン | style.css:67-72、258-262、716-723、999-1004、1045-1052 |
| スキップリンク | フォーカス時に `position:relative; z-index:1040`（固定ナビより手前） | 固定ナビのページ | style.css:372-375、#182 |
| ナビ | 常に `navbar-dark bg-dark`（`#212529`）。濃色固定の動画系も同じ | 全ページ | docs/new-site-design.md §12（theme-color 節）、`navbar.js` |
| `theme-color` | 入れない（全ページ共通値を置けないため。別の機会に検討） | 全ページ | docs/new-site-design.md §12（2026-09-12判断） |
| `data-bs-theme` | 採用しない。独自トークン＋`@media (prefers-color-scheme)` で完結させる | サイト全体 | docs/new-site-design.md §12 |
| 説明文 `.mj-lead` | `13px`、`line-height:1.7`、色 `#555555`（7.46:1）、上枠 `0.5px #e5e5e5`、`padding:12px 4px 18px`、既定 `max-width:720px`。表・グラフのページでは表／SVG の幅に揃える | 全ページ | style.css:754-762、764-796、#158・#312、docs/notes/mj-lead.md |
| 濃色トークン（7個） | bg `#121212`／surface `#1e1e1e`／fg `#f5f5f5`／fg-muted `#a8adb3`／border `#3a3a3a`／accent `#7fb3d5`／accent-fg `#0d1b24`（ライト側の値は new-site-design.md に保存済み） | 動画系（`body:has(.mj-video-page)`） | style.css:827-833、docs/new-site-design.md §12「カラートークン」 |
| 濃色のコントラスト | 本文 4.5:1・大きい文字 3:1 を事前計算して選定。境界色は装飾なので対象外。計測は両モードで行う | 動画系 | docs/new-site-design.md §12「固定化で顕在化したコントラスト不足」 |
| 濃色ページの `color-scheme` | `dark`（`html:has(.mj-video-page)`） | 動画系 | style.css:817-825 |
| トークンの命名 | 機能名で付ける（色名・見た目で付けない）。接頭辞 `--mj-v-` は暫定、新サイトで再設計 | 新サイト | docs/new-site-design.md §12「持ち越せる部分／捨てる部分」 |
| 共有ボタン | 丸（44×44px、`border-radius:50%`、透明地）が絞り込み欄の右と最強戦の帯。ヒーローは「再生」と同じ枠の 44×44px（角丸 6px）。トーストは `rgba(20,20,20,.92)`、角丸 6px、2秒程度 | live/・saikyo/・帰り道・title/ | style.css:1036-1109、docs/new-site-design.md §12「共有ボタン」、#409・#191 |
| ボタン（動画系） | 角丸 6px、`min-height:44px`、文字 0.9rem・600、主ボタンは白地 `#1a1a1a`、副ボタンは `rgba(0,0,0,.3)` 地＋`rgba(255,255,255,.7)` 枠 | 動画系ヒーロー | style.css:999-1034 |
| 最強戦の見た目 | Material Design 3 寄り。影 elev-1/2/3、角丸 12/8、8px グリッド。本文 `#212529`・リンク `#14459b` は共通のまま | `saikyo/` | style.css:1572-1574、1542-1544、docs/notes/saikyo-page-design.md（SK-09） |
| 最強戦の帯 | 対局名 `#3c4043`＋白文字（10.47:1）＋elev-2＋角丸 12px。卓 `#e8eaed`＋`#202124`（13.36:1）＋elev-1＋角丸 8px。対局のまとまり `1px #a8adb3`・角丸 12px・間隔 24px | `saikyo/` | style.css:1575-1646、docs/notes/saikyo-page-design.md（SK-19・SK-31） |
| 本文の列 | 最大幅 960px・左右 16px | `saikyo/`・`title/` | style.css:1526-1529、2111-2116 |
| 順位の金 | `#f2c230`＋文字 `#2b2100`（9.49:1）。優勝（タイトル戦の期カード）は淡い金 `#fff3cd`＋`#8a5a00`（5.35:1） | saikyo/・title/ | style.css:1750、2094、2567-2568、#412・#406 |
| 写真カード（名前・帯） | 名前はカード幅の 13%（13〜22px）、下端から透明になるグラデーションに白文字と濃い影。大会名の帯は `rgba(0,0,0,.75)`＋白字 | `title/`・`saikyo/`・`live/` | style.css:1696-1714、2123-2126（TP-14〜TP-16）、docs/notes/title-pages.md |
| 固定バー | navbar 直下に固定。背景は不透明 `#131316`（動画系の `rgba(20,20,24,.6)` を白地に重ねた合成色）、下枠 `rgba(255,255,255,.14)`、`padding:8px`、`gap:10px`、高さ 61px（459px 以下は 115px） | saikyo/・title/（動画系・live/ は sticky の半透明） | style.css:1316-1332、1859-1888、2194-2222、docs/notes/saikyo-page-design.md（SK-18） |
| 「メンバー限定」ラベル | 文字 `#8fd19e`、地 `#1d3524`（7.42:1）、角丸 4px、0.75rem・600 | live/ | style.css:2684-2698、docs/notes/live-page-design.md:362 |
| パンくずの区切り | 「/」（title/ の Bootstrap `.breadcrumb` と live/ で同じ）、live/ は 13.6px・700、項目の高さ 44px | live/・title/ | style.css:2703-2724（判断 LP-11） |
| ブレークポイント | 480px（グラフ切替・2列化）、991.98px（Bootstrap lg、ナビの折りたたみ）、992px（live の4列）、459.98px（絞り込みバー2行）、599.98px（books） | 各系統 | style.css:191・223・788・1657・2741（480）、1840-、2663-（992）、2997（599.98） |
| 画像の寸法 | 選手アイコン 48px、サムネイル 160×90、`img.thumbnails` 120px、`img.avatar` 80px | 型A | style.css:99-140 |
| 横棒グラフの幅 | デスクトップ 1200px、モバイル 360px、リーグ 1400px、`jpml_pros` 表 934px | 型C・D・`jpml_pros` | style.css:179-180、216、493 |
| 共通の部品の置き場所 | HTML は `scripts/lib/share.py` など、JS は `assets/share.js`、CSS は style.css の節 | 共有ボタン | docs/new-site-design.md §12「共有ボタン」 |

#### (b) 値が決まっていない箇所と、系統の間で値が食い違う箇所

実測は 1280px（390px の値は併記）。系統 A＝型A・A'（表）、B＝`title/`、C＝`saikyo/`、D＝動画系（`video_wayhome`・`wayhome/`）、E＝`live/`、F＝手書き・`404`。「決まっているか」は、既決の文書・CSS コメントで値が決まっているかを指す。

| 箇所 | 系統Aの実測値 | 系統Bの実測値 | 出典（行） | 決まっているか |
|---|---|---|---|---|
| ページ背景 | A・B・C・F: `#fff` | D・E: `#121212` | style.css:827-833 | D は決定済み。**E（`live/`）の濃色は §2「例外は1系統に限る」に記録が無い**（docs/notes/live-page-design.md:362 に「常に濃色」とだけ） |
| 共通ナビの position | A: `fixed`（390px で 94.8px の高さが12ページ） | B・C: `fixed`、D・E: `sticky`、F・型C・型D: `relative`（390px で 56px） | style.css:361、1266-、1802-、2158- | 未決（#417 保留） |
| 本文の列 | A: 画面幅いっぱい（x=0〜）、F: x=32 | B: 960px 中央（x=176〜）、C: 960px **左寄せ**（x=16〜）、D・E: ヒーロー x=48／本文 1100px 中央（x=130〜） | style.css:80、1117、1526-1529、2111-2116 | 値（960px・16px）は決定済み（SK-21）。寄せ位置は未決 |
| `.mj-lead` の左右パディング／最大幅 | A: 4px／表の幅（none）または 934px・1200px・1400px | B: 4px／none、C: 16px／960px、E: 48px／1100px（margin 82.5px）、D: 4px／720px | style.css:754-796、1090 付近、2819-2823 | 表・グラフ側は #312 で決定済み。他系統は未決 |
| 本文のフォントサイズ | A: 表 13px、F: 16px | B: 16px、C: 14px（`.mj-saikyo`）、D・E: 16px（バッジ・メタは 12〜14.4px） | style.css:268-273、1540-1548 | 表は決定済み（13px）。ほかは未決 |
| h1（見える場合） | F `404`: 40px/500（Bootstrap 既定）、`resource_efficiency`: 16px/500 | D・E ヒーロー: 48px/700（390px で 28px）。`live/index`・`video_wayhome` では 13.6px/700（48px は `<p>`／h2） | style.css:87-89、918-922 | `resource_efficiency` は #152 で決定。他は未決。h1 を隠す方針は #53 |
| h2（セクション見出し） | C・D・E: 17.6px/700（`1.1rem`） | B: 18px/700＋下線 2px（`1.125rem`）、E ステージ: 24px、E「他の対局」: 16px | style.css:1131-1135、2144-2150、2670-2672、2678-2684 | 未決 |
| h3 | C: 14.4px/700（淡い帯） | B: 16px/700（余白 16/8、CSS のみ・未描画）、E `.mj-live-subheading`: 16px/700 | style.css:1634-1642、2152-2156、2678-2684 | 未決 |
| パンくず | E: 13.6px/700・白・下線あり・項目高 44px・区切り「/」`opacity:.7` | B: 18px/400・`#14459b` 下線・活性項目 `#595c5f`（6.73:1）・区切り「/」 | style.css:2129-2132、2703-2724 | 区切り「/」のみ決定済み（LP-11） |
| リンク色 | A・B・C・F: `#14459b`（表は下線あり、`.mj-plain` は下線なし） | D・E: `#7fb3d5`（カード内は下線なし、ヒーローは白＋下線） | style.css:39-44、295、856 | 明色・濃色の色は決定済み。下線の有無は未決 |
| 入力欄 | A: UA 既定（`2px inset #767676`、角丸 0、白地、文字 `#000`、幅 100px） | B・C・D・E: `#121212` 地、`2px inset #3a3a3a`、角丸 0、文字 `#f5f5f5`、高さ 44px、幅は残り全部 | style.css:145-147、258-262、1884-1888、2219-2222 | 未決。Bootstrap の `.form-control` は不使用 |
| ボタンの高さ・角丸 | A: ナビ検索 50×38.8px・角丸 6px、ページ送り 58×44px・角丸 4px・枠 `#ced4da` | D・E: 主ボタン 81×44px・角丸 6px・白地、共有（ヒーロー）44×44px・角丸 6px、共有（丸）44×44px・50% | style.css:716-723、999-1034、1045-1052 | 44px は決定済み（ナビの検索ボタンのみ未達）。角丸は未決 |
| カード | C・B: 写真カード 226×226px・角丸 12px・elev 影・背景 `#e9ecef`（画像の下地） | D・E: 動画カード 286〜324px 幅・角丸 8px・枠 `#3a3a3a`・影なし・地 `#1e1e1e` | style.css:1158-1167、1667-1680、2279-2290 | C は Material 3 に合わせると決定（SK-09）。D は未決。混在時の指針なし |
| 角丸の種類 | A: 4px・6px | 全系統: 4／6／8／12／50%／999px の6種類（＋3px・`0 0 8px 0` が1か所ずつ） | style.css 全体 | 一部のみ決定（C: 12/8） |
| 影 | A・F: なし | B・C・E の写真カード: elev-1/3（Material 3 の値、4か所に複製）。D のカード: なし | style.css:1542-1544、2259-2260、2381-2382、2730-2731 | 値は決定済み（SK-09）。置き場所（トークン化）は未決 |
| 固定バー（絞り込み） | — | B・C: `position:fixed`・不透明 `#131316`・高さ 61px（390px で 115px）、D・E: `sticky`・`rgba(20,20,24,.6)`＋`blur(10px)`・高さ 61px（390px で 115px／90px） | style.css:1320-1332、1859-1888、2194-2222 | 見た目をそろえる方針は決定済み（SK-18）。実装が系統ごとに別 |
| 表ページの余白の既定 | A: `padding-top = --content-offset`（90px）、`scroll-padding-top` も同値 | B: `calc(--mj-title-nav-h(80/56px) + --mj-title-filter-h(61px))`、C: 同形で `--mj-saikyo-*` | style.css:377-392、2158-2170、1802-1840 | 値は決定済みだが、変数名・式が系統ごとに別 |
| フォーカスリング | A: ナビ以外 UA 既定、並べ替えボタン `#1B3B6F` 2px | D・E: 白 2px（offset 2）、共有ボタン `#f5f5f5` 2px（offset -4）、動画カード `#7fb3d5` 2px。B・C: 写真カードは白 3px＋紺 3px の影。ナビは全系統 Bootstrap の薄い青 | style.css:515、1031-1034、1062-1065、1188、1786-1800 | 表の枠のみ決定済み（3:1）。他は未決 |
| ナビのフォーカス | 全系統: `box-shadow: rgba(13,110,253,.25) 0 0 0 4px`（地 `#212529` に対して 1.29:1） | 同左 | Bootstrap 5.3 の既定 | 未決（#178 は closed） |
| 色のトークン化 | 変数は動画系の7個（`--mj-v-*`）、レースの `--mj-race-*`、影・帯の変数のみ | 他の色は直接指定（`#fff` 37件、`#14459b` 8件など。重複するのは `#121212`・`#3a3a3a`・`#14459b`・`#a8adb3`） | style.css:827-833、1542-1546、2124-2125、3076-3089 | 未決（#270） |
| ほぼ黒の文字色 | A・F: `#212529` | C: 卓の帯 `#202124`、D: ボタン `#1a1a1a`、レース `#0e1116` | style.css:1640、979、1017、3076 | 未決 |
| 灰色のパレット | Bootstrap（`#212529` `#e9ecef` `#ced4da`） | Google 系（`#202124` `#3c4043` `#e8eaed` `#f1f3f4` `#a8adb3`）／動画系自作（`#121212` `#1e1e1e` `#3a3a3a` `#131316` `#f5f5f5`） | style.css 全体 | 未決（3系列が併存） |
| 見出し代わりの要素 | B: パンくず 18px（h1 は隠す）、C: 年度プルダウン（h1 は隠す） | D・E: ヒーローの h1 | style.css:2128-2132、1891-1905 | 未決（#283・#160 に関係） |
| 手書きページのリンク | F（`jpml_links`・`resource_dictionary`）: アイコン 16px だけがリンク、`a:has(>img)` で下線なし | F（`rh_links`）: 文字リンク `#14459b`・下線あり（17件） | style.css:63-65、`jpml_links.html:36-38` | 未決 |
| 動画系の例外の範囲 | D: 濃色固定（決定済み） | E（`live/`）・`books/`（凍結）も濃色 | docs/new-site-design.md §2、docs/notes/live-page-design.md:362 | **文書は「1系統に限る」のまま。E は例外として未記録** |

補足（指摘にしなかったもの、G1）: 表の文字・縞・ホバー行は既決どおり AAA（7:1）を満たす（`#14459b` on `#d6e9f8` = 7.17:1、`#212529` on `#f2f2f2` = 13.78:1）。フォントファミリは全ページで同一。ナビの項目色 `rgba(255,255,255,.55)` on `#212529` は 5.67:1、`.mj-lead` の `#555` on `#fff` は 7.46:1、濃色の `.mj-lead` は 8.29:1、明色入力欄のプレースホルダ `#757575` on `#fff` は 4.61:1。コントラスト走査（全10,383要素）で基準を割ったのは G1-01（7件・7ページ）、写真上の名前（G1-10 で別に計測）、無効状態のページ送り（2件、対象外）だけ。ヒーローの写真上の文字は式で出せず未検証。ホバー・押下は実測していない。

#### DESIGN.md を置く場所の案

確かめたこと（現物）: `.assetsignore` は `docs`・`scripts`・`data`・`CLAUDE.md` などを除外しており、リポジトリ直下の `DESIGN.md` は除外されていない。`.github/workflows/assets-check.yml` は、配信される最上位の項目を許可リスト（`allowed`）と突き合わせて失敗させる（#331）。容量の上限（警告 30720／失敗 32768 バイトなど）を見るのは `CLAUDE.md`・`docs/handover.md`・`docs/notes/chat-side-operations.md` の3文書だけ。`scripts/sync_guides.py` の `ALLOWED_PATTERNS`（mj-logs のガイドに写すもの）は `CLAUDE.md`・`docs/handover.md`・`docs/instruction-template.md`・`docs/logs/_template.md`・`docs/notes/` 直下の .md・`docs/decisions/` 直下の .md で、`docs/DESIGN.md` は含まれない。

| 案 | 内容 | 公開の扱い | 容量の上限 | チャット側が読めるか（ガイドへの写し） |
|---|---|---|---|---|
| 案1 | `docs/DESIGN.md` | `docs/` は `.assetsignore` で丸ごと除外され、配信されない。`.assetsignore` の変更は不要 | 対象外 | **写らない**（`ALLOWED_PATTERNS` に無い。写すには `scripts/sync_guides.py` の変更が要り、別の指示になる） |
| 案2 | リポジトリ直下の `DESIGN.md` | **`.assetsignore` への追加が必須**。無いと本番で `https://ryoei.pro/DESIGN.md` が取れ、`assets-check.yml` の許可リストにも無く検査が失敗する | 対象外 | 写らない |
| 案3（推奨） | `docs/notes/design.md` | 配信されない（`docs/` 除外） | 対象外（上限を見るのは3文書だけ。`docs/notes/` の他ファイルと同じ扱い） | **写る**（`docs/notes/` 直下の .md）。チャット側が DESIGN.md を読めて、次の指示の「別 issue」の本文づくりに使える |

主セッションの推奨は案3。`docs/notes/` は中身が長くなってもよい置き場で、チャット側の取得の道具から読める。CLAUDE.md の「docs/handover.md は現状・ルール・次にやること」の方針とも合う（実装の詳細は `docs/notes/<topic>.md`）。

#### 別 issue（不足・不整合の洗い出し）の材料

(b) の表のうち「未決」の行が、別 issue の候補。大きく4つにまとめられる: (1) ナビ（position・フォーカス・検索アイコンの位置とサイズ・現在地）= G1-02・G1-03・G2-02・G2-03・G4-02、(2) 色・トークンの集約（灰色3系列・ほぼ黒の別値・ハードコード）= G1-09・#270、(3) 見出し・本文の列・余白の系統差 = G1-06・G1-07・#283・#160、(4) 部品（入力欄・ボタン・カード・角丸・フォーカスリング）の統一 = G1-04・G1-08・G3-11。「E（`live/`）の濃色が例外として未記録」は、DESIGN.md か docs/new-site-design.md §2 の更新で決める論点。

#### 新サイトの要件の材料（G5 の所見。指摘ではない）

画面の冒頭に何が見えるか（1280px と 390px。画像は外部ドメインのため出ない）:
- `jpml_pros`: ナビと表の見出し行だけ。見出し（h1 は visually-hidden）も説明もなく、検索欄はナビ右上の虫眼鏡を押すと出る形で画面上は見えない。主な操作は絞り込み。
- `video_live`: 冒頭は表の見出し「動画／概要」のみ。説明文・件数・検索欄は見えない。1280px では「概要」列が画面幅いっぱいに伸び、右が大きく空く。
- `title/index`: ナビの下に絞り込みバー（大会セレクト・検索欄・共有）と大会カード。何のページかはカードとプレースホルダで分かる。主な導線は「カードを選ぶ」「検索」の2つ。
- `saikyo/index`: セレクトと検索欄、その下に「歴代最強位」のカード。1280px では囲み枠が約930pxで左に寄り、右が空く（G1-06）。2026 のカードは名前が空欄で、理由が画面に出ない。
- `live/index`: 冒頭の大部分が先頭の配信を大きく見せるヒーロー。主な導線は「▶ 再生」と検索の2つ。`live/index` の2,918本と `video_live` の2,331本は別集計に見え、範囲の違いが画面から分からない（未検証）。
- `resource_efficiency`: 見出しがナビ直下でプレーン文字、すぐ下にグラフ。主な導線は無く読むだけ。
- `jpml_links`: 見出し（【日本プロ麻雀連盟】など）とリンクの縦一覧。ページ全体の見出し（h1）は無い。同名「公式サイト」が3つあり区別は見出しだけ。
- 共通: データベース系ページは、見出しの代わりに検索・絞り込みが最初に出るもの（title・saikyo・live）と、表がいきなり始まるもの（pros・video_live）に分かれる。「ここは何のページか」の一文を冒頭に出す型を新サイトで決めると、現行の揺れ（説明が description にしか無い）をそろえられる。

### その他の経過

- 点検の対象は、着手時の `origin/cloudflare`（daff02ec）の内容。着手後に `origin/cloudflare` へ他セッションの再生成のコミットが入ったが、点検は着手時点の作業ツリーで行った。
- 観点の読み替え: 指示の G1〜G5 をそのまま使った。実物に合わせて変えた点は無い。`houou_race.html` は未公開（noindex・navbar/sitemap/llms.txt に無い）なので対象外のまま。sign-up・checkout・送信フォームは無く（`href="#"` も `navbar.js` の JS 出力だけ）、ダークモード（`prefers-color-scheme`）は動画系の濃色トークンにあるが `data-bs-theme` は不採用（docs/new-site-design.md §12）。
- サブエージェントの出力のうち、`pbs.twimg.com` などの画像が出ないことと Charts が描けないことは、指示どおり指摘にしていない。
- 主セッションの再現確認で使った Playwright のスクリプトは scratchpad の `verify/`（リポジトリには入れない）。

## 報告

- 状態: 完了（読むだけ。何も直さず、issue も起票していない）
- ブランチ: work/1006-swp（指示どおり。origin/cloudflare から作成）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1006-SWP-01.md （マージ前は https://github.com/retroeater/mj/blob/work/1006-swp/docs/logs/CHAT-1006-SWP-01.md ）
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-swp
- 確認用URL: なし（docs のみ）
- マージ: 済（docs/logs/・docs/decisions/ のみの変更を cloudflare へ push。マージ後の SHA はターミナルの最終報告）
- issue: なし（起票していない）。関係する既存 issue は上の「1-1」の表
- 判断が必要なこと:
  - **指摘の集計**: 56件（新規 37・既存 issue と重複 18・決定済みで対象外 1）。大きさは小 39・中 14・大 3。行き先の案は、現行で直す 33（うち「判断」付き4）・新サイトの要件 15・見送り 7・実機で確かめてから決める 1。主セッションが実物で再現したものは15件、再現せず外したものは1件（G2 の空の検索欄）、それ以外はサブエージェントの再現のみで「未検証」。
  - **上位（現行で直す小さなもの。再現済み）**: G2-01（`title/`・`saikyo/` のスマホのメニューが 269px〈本来 520px〉にしか開かず、下に白い隙間）／G2-02（固定ナビの型A でドロップダウンを開くと nav が画面より長くなりスクロールできない）／G5-01（`404.html` が相対パスのため、深い階層の 404 で CSS・ナビが読めない）／G1-01（濃色ページの「本文へスキップ」が 2.10:1）／G1-02（ナビのフォーカスが 1.29:1、虫眼鏡は表示なし）／G2-03（検索アイコンが2行目に落ち、ナビ 95px・アイコン 50×39px）／G4-01（ナビの `role=button` が Space で動かない）／G2-06（帰り道ヒーローが文字拡大で見出しを切る）／G4-06・G4-07（`resource_dictionary` の壊れたタグ、`rh_links` の GitHub のリンク先が非公開）。
  - **どの指摘を現行で直すか**: 上の再現済み15件を第一弾にする案。「判断」付きの G1-10（写真上の名前のコントラスト、見た目の決定が要る）・G4-04（ページ送り後のスクロール位置、#184 の意図）・G4-08（`title/` の大会セレクトが即遷移、#180 との兼ね合い）・G5-05（帰り道の title 重複）は、採否を決めてほしい。
  - **実機で見てほしいページ**（iPhone。URL は上の「実機確認が要るもの」の表）: `https://ryoei.pro/title/`・`https://ryoei.pro/saikyo/`（メニュー）、`https://ryoei.pro/jpml_test.html`（メニュー＋ドロップダウン）、`https://ryoei.pro/jpml_pros.html`（虫眼鏡）、`https://ryoei.pro/video_wayhome.html`・`https://ryoei.pro/wayhome/atD2e-NgnKw.html`・`https://ryoei.pro/title/ourai/12.html`（文字を最大にして）、`https://ryoei.pro/rh_results_detail.html`（折り返し）、`https://ryoei.pro/title/nothing/here.html`（本番の 404）。
  - **DESIGN.md の置き場所**: 案3（`docs/notes/design.md`。配信されず、mj-logs のガイドに写ってチャット側が読める）を推奨。案1（`docs/DESIGN.md`）はガイドに写らず、案2（直下）は `.assetsignore` への追加が必須。上限（3文書）の対象外はどの案も同じ。
  - **起票の単位（案）**: (1) 共通ナビ一式（G1-02・G2-02・G2-03・G2-04・G4-01・G4-02〈#250〉）、(2) `title/`・`saikyo/` のメニューの循環（G2-01。帰り道の G2-06 のヒーローは別）、(3) `404.html`（G5-01・G3-10）、(4) 手書き3ページ（G4-06・G4-07・G1-12）、(5) 文書の件数のずれ（G5-07・G5-08）、(6) 濃色ページのスキップリンク（G1-01）、(7) 別 issue「DESIGN.md の不足・不整合の洗い出し」（(b) の未決の行。ナビ・色トークン・見出しと列・部品の4つに分けられる）。
  - **DESIGN.md と文書の食い違い**: docs/new-site-design.md §2 は「濃色固定の例外はこの1系統に限る」だが、`live/` も濃色（live-page-design.md:362）。例外として記録するか決めてほしい。docs/notes/static-generation.md の `live/` 691ページ（実物 908）と「他27ページは canonical 無し」（`title/`・`saikyo/` も canonical あり）も古い。
  - **ほか**: `rh_links` の GitHub リンク（`retroeater/mj`）は未ログインで 404（非公開）。リンクを外すかどうかは平野さんの判断。
- 未確認の項目:
  - 本番（ryoei.pro）はブラウザで巡回していない（#124）。`404.html` の本番の挙動は、404 応答を返すモックでの再現（G5-01）。
  - `houou_results`・`ouka_results`・`wrc_results` の表示（gstatic が届かず Charts が描けない）。画像（`pbs.twimg.com`・`img.youtube.com`・`yt3.googleusercontent.com` など）の見え方。
  - 実機（iPhone Safari）、実機の文字サイズ設定、NVDA などの読み上げ。文字の拡大はルート `font-size` 200% の近似。
  - サブエージェントだけが再現した指摘（「検証」欄が「サブ再現」のもの。特に「現行で直す（小）」の G1-12・G2-04・G3-02・G3-03・G4-03・G4-05・G4-08・G4-09・G5-03・G5-04・G5-05・G5-08 の690・163の2点）。
  - G1-10（写真上の名前のコントラスト）は、文字のある画素ではなく帯の画素の分布で計測した目安。ヒーローの写真上の文字は式で出せず未検証。
  - #178〜#185・#174 の本文・コメントの決定は読んでいない（open 一覧に無いことだけ確認）。`houou_race` を対象外にしたのはチャット側の判断（平野さんには未確認）。
  - 実機の支援技術（#186）、Safari・Firefox のフォーカスリング。
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 61abd870）: https://github.com/retroeater/mj-logs/tree/main/guide/61abd870

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/61abd870/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9d644c33.md
