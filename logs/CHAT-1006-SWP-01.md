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

## 報告

- 状態: 作業中
- ブランチ: work/1006-swp
- ログ: https://github.com/retroeater/mj/blob/work/1006-swp/docs/logs/CHAT-1006-SWP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-swp
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj daff02ec）: https://github.com/retroeater/mj-logs/tree/main/guide/daff02ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/daff02ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9d644c33.md
