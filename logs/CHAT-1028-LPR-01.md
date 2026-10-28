# CHAT-1028-LPR-01

- 着手日時: 2026-10-28（JST）
- 対象issue: なし（参照: #296・#186・#530）
- ブランチ: work/1028-lpr
- 着手時HEAD: cfad705

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群（houou/・ouka/・jwrc/・wrcr/・saikyo/）の横断レビューを、観点を分けたサブエージェント3人（読み取り専用）で行い、指摘の一覧をログに残す
Chat-Ref: CHAT-1028-LPR-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs と docs/decisions だけ。ページ・コード・ほかの文書は直さない）
貼る時機: いつでも（新しいセッションを推奨。AGT のセッションは長くなっているため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1028-lpr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
鳳凰戦・女流桜花・WRC リーグ・WRC-R リーグ・最強戦の成績ページ（`houou/`・`ouka/`・`jwrc/`・`wrcr/`・`saikyo/`）を横に比べ、現行サイトで直す小さな指摘と、新サイト（#296）の要件の候補を洗い出す。この指示では何も直さず、指摘の一覧をログに残すだけ。直すものは平野さんが一覧から選び、別の指示で直す。

### 決定（2026-10-28、平野さん）
- 成績ページ群の横断レビューを行う。対象は houou/・ouka/・jwrc/・wrcr/・saikyo/
- 目的は、現行サイトの修正（小さいものだけ）と、新サイト（#296）の要件出しの両方
- 観点で分けたサブエージェント3人で行う: ①画面・表示の一貫性 ②コード（生成・JS・CSS の重複と共通化の余地）③アクセシビリティ・スマホ
- 画面は本番を headless Chrome で計測する（375px と PC 幅）。スクリーンショットはコミットしない
- saikyo/ も含める。ただし saikyo/ の指摘は記録だけにし、直すのは最強戦の作業の系列（SKP）が落ち着いてから

### 前提（チャット側。平野さんの決定ではない）
- 4大会のページは `scripts/generate_houou_pages.py` を大会の設定 `Contest` で動かす同じ作り。大会で違うところと、その差が意図したものかは docs/notes/league-pages-compare.md の「意図した差（残す）」の表にある。**この表にある差は指摘にしない**（指摘にするのは、表に無い差と、表のよりどころと実物が食い違うものだけ）
- saikyo/ は別の生成（docs/notes/saikyo-page-design.md「3. ページ構成」、最新の部分は「年度別・個人成績・ランキングと新しいトップ」の節）。saikyo/ は 2026-10-27 にランキングを6部門にする決定があり（要確認: docs/decisions/saikyo.md の最新の節と本番が一致しているかを確かめ、食い違いは「作業中」として記録だけする）、告知の期間中（docs/notes/page-announcement.md）。この指示は読むだけなので告知の期間の守り方には当たらない
- 2026-10-06 のサイト全体の横断レビュー（docs/decisions/site-review.md）と同じく、凍結・廃止予定・作り直し予定のページは対象外。現行で直すのは小さいものだけ
- 前回のレビュー（CHAT-1028-AGT-02）では、「機械的に確認」が文字列・見出しの有無だけで、意味の照合（入れ子・語の定義・役割の違い）を欠いた指摘が3件出た。今回は「重複」「不一致」の指摘には、両方の場所の役割を読んで比べたかを必ず書かせる
- 同じ主題の open issue がある指摘（例: #186 アクセシビリティの実機確認、#530 共有の部品、#296 の sub-issue）は、指摘に issue 番号を添える。issue は GitHub MCP の読み取りで確かめてよい（読むだけで副作用が無いため）
- 本番を読むときは URL に `?v=<未使用の値>` を付ける（古い版が返ることがあるため。docs/notes/chat-side-operations.md「読み方」）
- 読む量を絞る（前回は文書の読み込みだけで1人あたり約20万トークン）。docs/logs・docs/notes/handover-archive-2026.md は読まない。saikyo-page-design.md（約100KB）は必要な節だけ grep で読む。ページは大会ごとに代表のページ（トップ・個人成績・ランキングの1部門・順位変動〈saikyo/ は対戦成績・年度別〉）に絞り、選んだ URL を全件ログに書く

## 手順
1. 対象のページの一覧を作る。`python3 scripts/regenerate.py --list` と `scripts/regenerate.py` の `OUTPUT_OVERRIDES` から5つの大会の生成と出力先を確かめ、各大会の代表のページ（上の前提）の本番 URL を表にしてログに書く。あわせて、docs/notes/league-pages-compare.md・houou-top.md・ouka-top.md・jwrc-top.md・wrcr-top.md と saikyo-page-design.md の該当の節を、サブエージェントに渡す読む文書として決める
2. サブエージェント3人（モデル Sonnet 5.5、読み取り専用: ファイルの作成・編集、git コマンド、issue の操作をしない。Read・Grep・Glob と読み取りの Bash、headless Chrome〈Playwright〉での本番の計測だけ。計測のスクリプトは scratchpad に置き、コミットしない）を起動する。担当:
   - ①画面・表示の一貫性: 5大会の代表のページを 375px と 1280px で開き、見出し・ナビ・タブ・表・チップ・数値の書式・空のときの表示・語（期・節・部門名など）の揺れを比べる。4大会どうしは league-pages-compare.md の表に無い差だけ、saikyo/ は4大会との差を全部挙げ、意図した差に見えるもの（大会の性質による差）は「意図した差の候補」と分ける
   - ②コード: `scripts/generate_houou_pages.py`・saikyo/ の生成スクリプト・`lib/ranking.py`・`assets/league.js`・`league_race.js`・`houou_ask.js`・saikyo/ の JS・`style.css` の該当の節・`scripts/tests/` の関係するテストを読み、重複・使われていないコード・saikyo/ と4大会で共通にできる部品・テストの抜けを挙げる。共通にできると言うときは、両方の役割と入力を読んで比べたかを書く
   - ③アクセシビリティ・スマホ: 5大会の代表のページを 375px と 1280px で計測する（横のはみ出しの要素の数、タップの対象の大きさ、`.mj-table` の文字のコントラスト比〈handover.md 4章の基準: 文字は 7:1、フォーカス枠は 3:1〉、見出しの順、画像の alt、タブ・並べ替えの ARIA とキーボードでの操作）。測った値を全件書く
   各指摘は「番号（①-1 など）／大会・ページ（URL）またはファイル:行／内容／証拠（計測値・コードの引用）／区分〈現行で直す小さいもの・新サイトの要件・意図した差の候補〉／確かめ方〈機械的〈コマンド・計測〉・意味の照合をした・判断のみ〉／関係する issue」の形で全件書く（「略」にしない）
   相互検証: ①の指摘を②が、②を③が、③を①が読み、指摘ごとに「同意／異議（理由）／機械的に確認した」を返す。サブエージェントどうしは SendMessage で直接やり取りしてよい（CHAT-1028-AGT-02 で使えた）。使えなければリーダー経由で渡す。異議が残った指摘は両方の言い分を残す。終わったら3人を止める
3. ログの `## 経過` に、代表のページの表、指摘の一覧（全件。番号・大会・場所・内容・証拠・区分・確かめ方・検証者・判定・issue の表）、実測（所要時間、3人のモデル名とトークン〈取れる範囲〉、相互検証の経路、指示に反してファイルを変えた・git を使った事象の有無、権限の確認が出た回数）を書く。`## 報告` の「判断が必要なこと」には、判定が「同意」か「機械的に確認」の指摘を、区分ごと（現行で直す小さいもの／新サイトの要件／意図した差の候補）・大会ごとに番号を振ってまとめる（平野さんが番号で選べる形。saikyo/ の指摘には「記録だけ」と添える）。上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げて `git push origin work/1028-lpr:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 手順1で5つの大会のどれかの生成・出力先が見つからない、または本番の代表のページが 200 でない（その大会だけ外して進め、外した大会と理由を報告に書く。全部が外れるときだけ止まる）
- 手順2で3人のうち1人でも起動できない、または同じエラーが3回続いた（別の手段を試さずに止まる）
- 手順2の後の `git status --porcelain` に docs/logs/CHAT-1028-LPR-01.md 以外の変更がある（戻さずに止まる。`git checkout .` は使わない）
- 本番に headless Chrome で届かない（`curl` で 200 が返るのに Chrome で開けないときを含む。計測なしで進めず、届かない状態をログに書いて止まる）
- 作業ブランチに docs/logs・docs/decisions 以外の変更がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-27 21:49 UTC 〜）
- 0章: 「指示」欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致（指示文をそのまま貼った）
- 同じ Chat-Ref・`LPR` の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-LPR-'` と docs/logs の履歴ともに0件
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 指示文が着手・クローズの対象にする issue は無い（#296・#186・#530 は参照のみ）ため、issue の state の確認と着手中のコメントは対象外
- 作業ブランチ: work/1028-lpr はローカル・リモートとも無し → `git checkout -b work/1028-lpr origin/cloudflare`（着手時 HEAD cfad705）

### 手順1: 対象のページ
`python3 scripts/regenerate.py --list` に houou_pages・ouka_pages・jwrc_pages・wrcr_pages・saikyo_pages があり、`OUTPUT_OVERRIDES` の出力先は順に houou/・ouka/・jwrc/・wrcr/・saikyo/。生成は4大会が scripts/generate_houou_pages.py、最強戦が scripts/generate_saikyo_pages.py（ほか generate_ask_data_saikyo.py）。5大会とも外さない。

代表のページ（個人成績は4大会と最強戦の全部に成績がある 仲田加南。URL の `N` は `%E4%BB%B2%E7%94%B0%E5%8A%A0%E5%8D%97`）。全22件とも `curl`（`?v=lpr1028a` 付き）で 200。headless Chrome でも https://ryoei.pro/houou/ が 200 で開けた（375px で scrollWidth 375）。

| 大会 | トップ | 個人成績 | ランキング（1部門） | 順位変動／対戦成績・年度別 |
|---|---|---|---|---|
| 鳳凰戦 | https://ryoei.pro/houou/ | https://ryoei.pro/houou/players/?name=N | https://ryoei.pro/houou/ranking/total/ | https://ryoei.pro/houou/race/ |
| 女流桜花 | https://ryoei.pro/ouka/ | https://ryoei.pro/ouka/players/?name=N | https://ryoei.pro/ouka/ranking/total/ | https://ryoei.pro/ouka/race/ |
| WRC リーグ | https://ryoei.pro/jwrc/ | https://ryoei.pro/jwrc/players/?name=N | https://ryoei.pro/jwrc/ranking/total/ | https://ryoei.pro/jwrc/race/ |
| WRC-R リーグ | https://ryoei.pro/wrcr/ | https://ryoei.pro/wrcr/players/?name=N | https://ryoei.pro/wrcr/ranking/total/ | https://ryoei.pro/wrcr/race/ |
| 最強戦 | https://ryoei.pro/saikyo/ | https://ryoei.pro/saikyo/players/?name=N | https://ryoei.pro/saikyo/ranking/appearances/ | https://ryoei.pro/saikyo/vs/ ・ https://ryoei.pro/saikyo/years/ |

（curl の確認では https://ryoei.pro/saikyo/2025/ も 200。代表には含めない）

サブエージェントに渡す読む文書: docs/notes/league-pages-compare.md（全部）、houou-top.md（66KB のため grep）・ouka-top.md・jwrc-top.md・wrcr-top.md、saikyo-page-design.md の「3. ページ構成」（77〜331行、うち対戦成績 294行〜・年度別ほか 316〜331行）と「5. アクセシビリティ」（536〜555行）、docs/decisions/saikyo.md の末尾3節（735行〜、6部門の決定）、③は handover.md「4. 押さえておくべき方針」。共通の前提は scratchpad の brief.md に書いて3人に渡した（コミットしない）。

### 手順2: サブエージェントの起動（手順1の直後、2026-10-27 21:5x UTC）
3人とも general-purpose・モデル sonnet（Sonnet 5.5）でバックグラウンド起動。読み取り専用の約束は brief で指定（Explore 型は Bash が無く Playwright を使えないため general-purpose にした）。3人とも起動でき、エラーで止まった回は無い。

#### 前提のずれ（③が報告、①・②も否定せず）
- 代表の20ページに `.mj-table` は無い。表は ranking の `table.mj-houou-rank` だけ。コントラストの 7:1 はこの表で測った
- `role=tablist/tab/tabpanel`・`aria-selected`・`aria-sort` は20ページのどこにも無く、並べ替えボタンも無い（フォーカス枠 3:1 は並べ替えボタンでは測れず、ナビ・切り替えボタンで測った）。切り替えは `div[role=group]` の `button[aria-pressed]`、期の開閉は `button[aria-expanded][aria-controls]`、順位変動の期の選択は `role=dialog` のシート

### 指摘の一覧（全件）

区分: 小＝現行で直す小さいもの、新＝新サイトの要件、差＝意図した差の候補、作業中＝SKP 系列で作業中（記録だけ）。確かめ方: 機＝機械的（コマンド・計測）、意＝意味の照合をした、判＝判断のみ。検証者: ①の指摘は②、②は③、③は①が検証した。

#### ① 画面・表示の一貫性（検証者: ②）

| 番号 | 大会・場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue |
|---|---|---|---|---|---|---|---|
| ①-1 | saikyo/years/・saikyo/vs/ | 1280px でこの2ページだけ幅928px の左寄せ。他（saikyo の players・ranking・トップ、4大会）は368px の中央寄せ。375px でもパンくず・h1 の位置が違う。パンくずの class も違う | 1280px の h1 は years/・vs/ が left 16・幅 928、他は left 456・幅 368。375px の top はパンくず 69 対 73、h1 121 対 97。パンくずは `mj-saikyo-n-crumb`（下線なし・高さ44）対 `mj-houou-crumbs`（下線あり・高さ20）。saikyo/2024/ も `mj-saikyo-n-crumb` | 新（年度ページの形を保つなら差） | 機・意（一部） | 同意。②の補足: saikyo-page-design.md:416,477 は年度ページ系の本文幅を最大960px・余白16px と定める。①が挙げた 316〜324行の「houou/・jwrc/ と同じ形」は総論で、幅の記述とは別。両方読んで区分に同意 | #566（closed） |
| ①-2 | saikyo/years/・saikyo/vs/ | h1 が「年度別」「対戦成績」で大会名が無い。saikyo/players・ranking と4大会は「<大会> <ページ>」 | h1 の実測: years「年度別」、vs「対戦成績」、players「最強戦 個人成績」、ranking「最強戦 ランキング（出場数）」 | 小 | 機 | 同意（機械的に確認） | なし |
| ①-3 | 4大会の players と saikyo/players | 4大会の title は選手を選ぶと「<選手> \| 成績詳細 \| <大会> \| …」、h1・パンくずは「個人成績」。saikyo は「<選手> \| 個人成績 \| 最強戦 \| ryoei.pro」 | 4大会の `document.title` は4つとも「成績詳細」。houou-top.md:49・71 が「成績詳細」と明記 | 差（4大会内は意図あり。新サイトでそろえる） | 意 | 同意。②の補足: 静的な title は「個人成績 \| 鳳凰戦 \| …」で、「成績詳細」は選手を選んだ後に league.js:493 が付ける。saikyo は saikyo.js:660,975 | なし |
| ①-4 | saikyo/ 全ページ | title に「日本プロ麻雀連盟」が無い。h1・nav は「最強戦」、title・description・質問欄の見出しは「麻雀最強戦」 | トップの title「麻雀最強戦 \| 最強戦 \| ryoei.pro」、h1「最強戦」、h2「麻雀最強戦について AI に質問する」 | 差（連盟の大会でない。「麻雀最強戦」は正式名） | 判 | 同意 | なし |
| ①-5 | saikyo/ の各ページと4大会 | canonical が saikyo/ にだけあり、4大会は全ページ無し。saikyo/players/?name=… の canonical は `/saikyo/players/` 固定 | DOM・curl で saikyo/・years・vs・players・ranking に canonical あり、4大会は0。docs/notes/cloudflare.md:481-489 は「`?name=` で中身が変わる URL には付けない（#113）」 | 小 または決定の追記（判断が要る） | 機・意 | 同意。②の補足: docs/decisions/saikyo.md:123 に「canonical は個別ページも年度ページも自分自身」（置き換え: 2026-10-15 の注記付き）があり、#113 の記述と食い違う。決定の追記が要る | #113 |
| ①-6 | saikyo のランキング・個人成績の部門別 | 勝率の書式が「.474」「1.000」、4大会の率は「0.750」 | saikyo 勝率の先頭「1.000 / .704 / .636」、jwrc 通過率「1.000 / 0.833」。decisions/saikyo.md の 2026-10-27 の決定は「.xxx」 | 差（決定済み。そろえるなら新） | 意 | 同意（decisions/saikyo.md:706,715 と本番一致を確認） | #633 |
| ①-7 | saikyo と jwrc・wrcr | 「通過÷出場」の率を saikyo は「勝率」、jwrc・wrcr は「通過率」と呼ぶ | saikyo の ⓘ「対局数10回以上の選手が対象です」、jwrc は「出場5期以上」 | 差（分母が卓と期で違う別物） | 意 | 同意。②: saikyo は `c.passes / c.tables`（generate_saikyo_pages.py:211）、ranking.pass_rate()（ranking.py:184-191）は分母が出場期数。数え方も名前も違う | #633 |
| ①-8 | saikyo と4大会 | 出場・連続の単位が saikyo は「回」、4大会は「期」 | saikyo「出場数 16回」「連続出場 16回」、4大会「出場 42期」「連続通過 6期」 | 差（decisions/saikyo.md:706,715） | 意 | 同意。②の補足: 4大会の「通過回数」は「回」なので、「期」は連続系に限る | なし |
| ①-9 | saikyo/ranking | 「（在籍者のみ）」が無い。時点の表記が「2026年10月25日の対局まで」で、4大会は「第N期終了時点」 | 4大会の description の末尾は「（在籍者のみ）」 | 差 | 意 | 同意。②: 全出場者が対象なのは decisions/saikyo.md:579 の決定 | なし |
| ①-10 | saikyo/years/ | 2026 年度の行が年号と矢印だけの空の行（「開催中」などの語が無い） | 375px・1280px とも 2026 の行だけ写真・名前なし | 新（空のときの表示） | 機 | 同意 | なし |
| ①-11 | saikyo/players の年度別 | 順位が空の行がある（仲田加南 2021「ファイナル 1st Stage ベスト16 A卓」「女流プロMの城壁」） | decisions/saikyo.md:701 は「順位の空欄（#630）が残っていても公開は止めない」 | 作業中 | 機 | 同意 | #630 |
| ①-12 | jwrc/race・wrcr/race | 期の選択シートの題が「期・リーグを選ぶ」。この2大会はリーグが無く、シートには期だけが出る | `#raceSheetTitle` の本文。生成は scripts/generate_houou_pages.py:917・924 の固定文字列。league-pages-compare.md は「選ぶ画面: 期だけ」 | 小（`no_leagues` で出し分け、数行） | 機・意 | 同意。②: HTML に2か所、ボタンの文言は league_race.js:143 が期だけにしている | なし |
| ①-13 | wrcr/players（期が1つの選手） | 通算グラフで「ベスト 1期 +137.0」と「ワースト 1期 +137.0」が同じ点に重なり、グラフの下の約8割が空 | 仲田加南の wrcr の通算（375px）。jwrc は期が4つで正常 | 新（1点のときの表示） | 機（見た目） | 同意（②も同じ表示を確認） | なし |
| ①-14 | saikyo/ranking・players | ランキング6部門は決定と本番が一致（並び・単位・勝率の規定・外した3部門は 301 で /saikyo/ranking/champion/ へ） | 下の「saikyo/ranking の6部門と決定の照合」 | 作業中（食い違いなし） | 機 | 同意 | #633 |

①は、同じ形か表の意図した差どおりとして、次を指摘にしていない: 4大会の DOM の class の集合（`is-<大会>` を除き同じ）、空のときの表示（選手が見つからない文）、ナビの現在位置（`aria-current`）、連続・最高系ランキングで同じ選手が複数行に出ること（記録ごとの掲載）。

#### ② コード（検証者: ③）

| 番号 | 場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue |
|---|---|---|---|---|---|---|---|
| ②-1 | scripts/generate_houou_pages.py:138 | `MIN_ROWS, MAX_ROWS = HOUOU.rows` は未使用。行数の検査は load_rows() の `CONTEST.rows`（233行）が行う | grep で定義1件のみ（tests にも無い） | 小（1行削除） | 機 | 機械的に確認（233〜235行の `low, high = CONTEST.rows`） | なし |
| ②-2 | scripts/lib/ranking.py:22 | `DEFAULT_DIVISION = "通算得点"` は参照なし | grep で定義のみ | 小（1行削除） | 機 | 機械的に確認 | なし |
| ②-3 | scripts/lib/ranking.py の `rank(limit=True)` 経路・`Contest.limits`・`DEFAULT_RANK_LIMIT`・`MIN_CONSECUTIVE_PROMOTIONS`・`CONTESTS["特昇"]` | 本番の呼び出し3か所（generate_houou_pages.py:588,620,621）は全部 `limit=False`。これらはテストからだけ動く。表示の上限は今は `rank_cut`・`auto_cut` が担う | grep。ranking.py:225-254 を読んだ | 小（死んだ経路とテストの直し。数十行） | 機・意 | 機械的に確認（limit を既定で呼ぶのは test_houou_pages.py:38 だけ、特昇は ranking.py:57 のみ） | なし |
| ②-4 | scripts/generate_houou_pages.py:167 | `OG_IMAGE = race_page.OG_IMAGE` は test_houou_pages.py:212 でしか使われない（本番は `CONTEST.og_image`） | grep | 小 | 機 | 機械的に確認 | なし |
| ②-5 | generate_houou_pages.py:82-125（Contest.script）と assets/league.js:13-60 | script の辞書は12キー中8キーが Contest の他の項目から導ける二重定義。HOUOU は script=None で、JS の既定値（league.js:20,21,26,45）が Contest と一致することに頼るが、一致を見るテストが無い | jwrc/players/index.html の `league_contest` と照合 | 小（script_of(contest) を1関数で） | 意 | 条件つき同意。③: top は1対1で導けない（league.js:24 の `CONTEST.top \|\| '鳳凰位'` で jwrc/wrcr の空文字が「鳳凰位」に戻り、championLabel で避けている）。作るなら空文字の扱いを含める。テストが無いのは同意 | #614 |
| ②-6 | scripts/lib/houou_race.py:52-70,300-350 と generate_houou_pages.py:879-940 | 旧 houou_race.html の既定値（`DATA_URL="houou_race/"`・実在しない `script_src="houou_race.js"`・旧 og_url など）が残り、render_race()/race_layout() が旧テンプレートを文字列置換で作り替える。render_race・race_layout・race_head・load_race の直接のテストが無い | grep。呼び出しは1件 | 新（骨組みを大会ごとに生成する形へ） | 機・意 | 機械的に確認（houou_race.py:58,342。assets/ に houou_race.js は無い。テストの参照0件） | #614 |
| ②-7 | style.css:5886-5888 | `.is-ouka/.is-jwrc/.is-wrcr .mj-houou-term-bar` の列挙は実質「halves=False の大会」。大会を足すたびにセレクタが要る | grep と Contest.halves の照合 | 小（共通のクラスで1規則に） | 意 | 機械的に確認（クラスは contest_class() が `is-<dir>` で付ける） | #614 |
| ②-8 | style.css の未使用クラス | 13種（②は「15種」と書いたが列挙は13種。③が訂正）: houou 側 `mj-houou-card-icon`（4094ほか）・`mj-houou-up`/`-down`（4689,4693）・`mj-houou-pyramid`（5249）・`mj-houou-section-title`（4349）・`mj-houou-trend-range`（4715,6012）、saikyo 側（旧トップの名残）`mj-saikyo-n-search`/`-search-input`（2017〜2053）・`-hit-head`/`-hit-toggle`/`-toggle`（2083〜2145）・`-menu`/`-menu-title`（2182〜2191）。約24規則・約125行。あわせて saikyo-page-design.md の 155行と160行が build_top_body() について食い違う（155行で消したと書き、160行で説明） | .html・.js・.mjs・.py と生成済み HTML を grep。`mj-saikyo-n-hit-toggle` だけは scripts/promo_video/saikyo/capture.mjs:123 が参照（旧トップを撮る動画用） | 小（saikyo 側は作業中・記録だけ） | 機 | 訂正つき同意（13種） | なし |
| ②-9 | assets/saikyo.js:634-870 と assets/league.js:64-70,192-424,434-456 | 「選手を探す」部品（約240行）が同じ手順の写し（同じ id、先頭一致→部分一致、最近見た選手、キー生成）。違いは索引の読み方（非同期の search.json か埋め込みか）と写真の srcset | 両方を並べて読んだ | 新（共通部品） | 意 | 条件つき同意。③も両方の役割を読んだ（league.js は createSearch(opts) の部品、saikyo.js は DOMContentLoaded に展開）。playerKey の寄せ方は ②-11 を踏まえて決める | #614 |
| ②-10 | assets/saikyo.js:290-305 | vs の節の中の el()・chevron() が同じファイル46-60行のトップレベル関数と字句まで同一（内側で隠しているだけ） | 2か所を比較 | 小（16行削除） | 機・意 | 機械的に確認 | なし |
| ②-11 | assets/saikyo.js:39・635 と assets/league.js:65・scripts/lib/results.py:51-54 | 検索の正規化に JS と Python の等価性テストが無い。saikyo.js の playerKey・playerMatches は test_saikyo_vs.py の JS_FUNCTIONS に無い | 正規表現と 0x60 の引き算を照合 | 小（テスト1本） | 機・意 | 条件つき同意。③の訂正: saikyo.js の normalize に折り畳みが無いのは playerKey がその上で折り畳む二段構えのため、合わせると league.js と同じ。実際のずれは、テストが無いことと、対戦成績の絞り込み（saikyo.js:251,273,514 は normalize だけ）がカタカナとひらがなを区別し、個人成績の検索は区別しないこと | なし |
| ②-12 | scripts/generate_saikyo_pages.py:1534-1537（crumbs_html）・1619-1627（ranking_row_html）・1630-1635（division_chips）・1448-1462（順位付け） | houou 側の crumbs・ranking_row_html・division_chips・ranking.rank() の写し。パンくずは名前の取り方だけ違う。行の骨組みは同じで入力が違う（with_size の URL・value_text 対 photos 辞書・整形済みの値）。順位付けは houou が表示文字列、saikyo が数値で比べる | 両方の関数を並べて読んだ | 小（crumbs に名前の引数）／新（行と順位付けの共通化） | 意 | 意味の照合をして同意。③の補足: houou の division_chips は `data-division`（旧 `?division=` の受け口）と略称を持ち、saikyo には無い。順位付けは丸めの有無で結果が変わりうるので、共通化は入力をそろえてから | #614 |
| ②-13 | docs/notes/league-pages-compare.md:58（noindex の行）と4行 | noindex の行が wrcr を「あり」としているが、実物の WRCR は noindex=False、本番の wrcr/ にも無い。同じ文書の11行・test_wrcr_pages（assertFalse）と食い違う。4行の「未公開で足した」も現状の説明としては古い。test_league_pages_compare は項目名しか見ない | `curl https://ryoei.pro/wrcr/?v=… \| grep -ci noindex` が0（houou/leagues/ は1） | 小（docs のみ） | 機 | 機械的に確認 | なし |
| ②-14 | scripts/tests/test_houou_pages.py:369-392 | current_league() と trend_marks() は、仮の document を作り league.js を require する前半の約8行が同じ | 読んだ | 小 | 意 | 機械的に確認 | なし |
| ②-15 | scripts/generate_saikyo_pages.py:192 | ランキングの上限は houou だけ100/110、他3大会と最強戦は50/55（意図した差の表と一致）。最強戦は `RANK_CUT=(50,55)` を別定数で持ち、4大会の rank_cut と同じ値の重複 | コメント「jwrc/・wrcr/ と同じ」 | 差（意図の確認のみ） | 意 | 同意（本番の saikyo の出場数は46件で、上限は今は働いていない） | なし |

②の補足: Contest の24項目に未使用は無い。`MENU_DIRS`・`MENU_REDIRECT_LINES`・`VS_REDIRECT_LINES` はテストからだけ使われ、意図どおりとして指摘にしていない。直接のテストが名前で出ない関数（統合テストでは動く場合がある）: 順位変動の組み立て（②-6）、generate_saikyo_pages.py の division_chips・build_ranking_body・build_players_body・write_menu、generate_ask_data_saikyo.py の player_rows・years_data・totals_data・load_people、saikyo.js の個人成績の節（playerKey・playerMatches・divisionView）。#530 は共有ボタンの issue で、②-5〜②-12 の主題ではない（関係するのは #614）。

#### ③ アクセシビリティ・スマホ（検証者: ①）

| 番号 | 大会・場所 | 内容 | 証拠 | 区分 | 確かめ方 | 判定 | issue |
|---|---|---|---|---|---|---|---|
| ③-1 | houou・ouka・jwrc・wrcr の /race/ | `aria-modal="true"` の期の選択シートを開いても、フォーカスが閉じ込められず、背面も inert でない | 開いて Tab を40回: シートの中の9個の後、背面の節のボタン・選手チップ・スキップリンク・ナビへ抜けた。Esc で閉じ、フォーカスは `button.mj-houou-picker` に戻る | 小（開く関数で背面を inert にする、1関数） | 機（押したのは houou、他3大会は属性） | 同意・機械的に確認（①も houou で Tab 40回を再現、4大会とも inert なし） | #186 |
| ③-2 | /race/ 4大会 | 選手の丸 `.mj-race-chip` が 28×28px、節のボタン `.mj-race-nd` は高さ36px（44px の設計値未満。24px 以上で 2.5.8 は満たす） | 375px で chip が houou 15・ouka 16・jwrc 89・wrcr 84個。nd は幅41〜67（1280px で44〜72）×36 | 差（44px にするなら新）。docs/notes/design.md:70,78 の「押せる行は44px以上」が図の部品に及ぶかは判断が要る | 機・意 | 同意・機械的に確認 | #186 |
| ③-3 | ranking（4大会 total/ と saikyo appearances/） | 選手名のリンク `a.mj-houou-rank-name` が高さ24px（行全体は data-href でクリックできる） | houou 100・ouka/jwrc/wrcr 各50・saikyo 46件、最小 32〜48×24。行の高さ 56px（①が jwrc で測定） | 差 | 機 | 同意・機械的に確認 | #186 |
| ③-4 | saikyo/players/?name=… | 独立した行のリンク「対戦成績を見る ›」(`a.mj-houou-player-span`、inline) が 100×15px で 24px 未満（375・1280 とも）。2.5.8 の文中の例外に当たらない | 24px 未満はこの1件だけ | 小（inline-block と min-height で数行） | 機 | 同意・機械的に確認（99.56×15） | #186 |
| ③-5 | 個人成績・質問欄の入力欄（全大会） | input は `outline:none` で、フォーカス枠は親（`.mj-houou-search`・`.mj-houou-ask-form`）に付く。saikyo/vs/ の入力欄は入力欄そのものに枠 | フォーカス前後の computed style: 親が solid 2px rgb(20,69,155) | 差（枠は見える。統一は新） | 機 | 同意・機械的に確認 | #186 |
| ③-6 | saikyo/vs/・saikyo/years/ | パンくずの「最強戦」と vs/ の「もっと見る」ボタンのフォーカス枠が Chrome 既定（auto 1px #101010）。他ページは solid 2px #14459b | computed style。コントラスト 17:1 で見えはする | 小（既存の focus-visible 規則を当てる） | 機 | 同意・機械的に確認 | #186 |
| ③-7 | /race/ 4大会の `.mj-race-nd` | 今の節を支援技術に伝える `aria-current`/`aria-pressed` が無い（他の切り替えは aria-pressed を持つ） | 5〜8個すべてで無し。aria-label は「第2節の終わりへ」 | 新 | 機 | 同意・機械的に確認 | #186 |
| ③-8 | /race/ の `.mj-race-chip` | 頭文字が `color: rgba(0,0,0,0)` で背景画像の顔を見せる作り。画像が切れる（pbs.twimg.com）と無地の丸になる可能性。aria-label はある | 透明の文字が houou 15・ouka 16・jwrc 89・wrcr 84個 | 差（読み上げは aria-label で足りる） | 機（画像切れの見え方は未確認。サンドボックスから外部画像に届かない） | 同意・機械的に確認。①の補足: 画像が無いときの表示として ①-13 と同種で、新サイトの要件に回せる | なし |
| ③-9 | ranking・個人成績・saikyo vs/years の写真 | `alt=""` が多数だが、隣に名前のリンクか aria-label があり装飾として妥当 | ranking の avatar houou 91・ouka 48・jwrc 45・wrcr 48・saikyo 42、vs/ 42、years/ 14、個人成績 1。alt 属性なしは0 | 指摘なし（確認の記録） | 意 | 同意・機械的に確認 | — |

### 計測の値（③、全件）
- 横のはみ出し: 20ページ×2幅とも `scrollWidth` は 375 / 1280 ちょうど。viewport を超える要素は、直接・スクロール容器の中とも0。JS のエラー0、`main` 1つ、`lang=ja`
- 見出し: h1 は全ページ1個、飛びなし。並びはトップ（5大会）・race（4大会）・個人成績（saikyo を除く）が 1,2、ranking は 1 のみ、saikyo/players は 1,2,3×8、saikyo/vs は 1,2,2,2、saikyo/years は 1,2
- タップの対象（a・button・input・select・summary、スキップリンクを除く）: 24px 未満は ③-4 の1件だけ。44px 未満は ranking の選手名リンク（houou 100・ouka 50・jwrc 50・wrcr 50・saikyo 46、最小 32〜48×24）、race の chip（houou 15・ouka 16・jwrc 89・wrcr 84、28×28）、race の nd（houou 5・ouka 8・jwrc 5・wrcr 5、幅41〜67〈1280px で44〜72〉×36）、saikyo/players の対戦成績リンク 1（100×15）。375px と 1280px で数は同じ。スキップリンクは 1×1px（フォーカス時 114×17）
- コントラスト（light・dark、20ページ×2幅）: ranking の表のセル（th・順位・選手・値）は 7:1 以上（7未満0件）。表以外の最小は light 5.1（降級のチップ、白字 on #1c6fc4、12px）、dark 5.1〜5.39。4.5:1 未満の実際の文字は0件（race の chip の透明な頭文字 15〜89件は ③-8）。5〜7 の組: houou/ の質問欄の注意書き #555 on #f2f2f7 6.68、個人成績の通算ボタン・ランキングのチップ #555 on #e9e9ee 6.16、昇級チップ 5.18、桜花チップ 6.04、jwrc/ の敗退チップ #555 on #e5e5e5 5.92
- フォーカス枠: 標準は solid 2px rgb(20,69,155)・offset 2px、背景との比 7.37〜8.92。スキップリンクは auto 1px #101010（17.05）。1280px のナビ10項目はすべて2px の枠で高さ44px。#186 のコメントにある G1-02（ナビのフォーカスが地と同色）・G4-01（ドロップダウンが Space で動かない）は本番で再現しなかった
- キーボード（houou/ で押した）: 個人成績の切り替えは Tab で届き Enter・Space で aria-pressed が切り替わる（矢印キーは何もしない）。期の開閉は aria-expanded・aria-controls があり Enter・Space で開閉し hidden も連動。選手検索の combobox は role・aria-controls・aria-expanded・aria-activedescendant・listbox/option があり、「仲田」→ ArrowDown → Enter で players/?name=仲田加南 へ遷移。Esc は入力を空にするが一覧は残る（③の検証で②-9 の pinned() により最近見た選手を出す設計と判明、不具合ではない）。質問欄の結果は aria-live="polite"。race の nd は Space で表が変わり ArrowRight で次の nd へ（roving）。saikyo/vs/ の入力欄2つに aria-label、結果に role=status
- 未計測: 360px（設計上の最小幅）、iOS・Android の実機、prefers-reduced-motion、文字の拡大。ナビの虫眼鏡が 1280px の Tab 順に出ない理由（ページの検索欄の有無と推測、未確認）

### 計測の値（①）
- 1280px のパンくず・h1 の左端と幅: 4大会と saikyo の players・ranking・トップ 456・368、saikyo/years/・vs/ 16・928、saikyo/2024/ 15（h1 の幅は未計測）、saikyo/2024/20240915-s2.html 48・624
- meta: canonical は saikyo/ の各ページにだけ（自己参照）、robots メタは全ページ無し。ranking の description の部門数は houou 9・ouka 8・jwrc 9・wrcr 9・saikyo 6
- ランキングの件数: houou 通算100・期浮き率104、ouka・jwrc・wrcr 通算各50、saikyo 最強位12・ファイナル53・勝率53・出場数46・連続出場43・対局数53（上限50・同率で55まで）。4大会の連続・最高系は31〜104件
- 仲田加南: jwrc 部門別 通算 +305.8（48位）・期平均 +76.5（規定未達）・通過回数3回・通過率 0.750（規定未達）・連続通過1期。wrcr 通過率 1.000（規定未達）。saikyo 最強位0回・ファイナル2回（23位）・勝率 .474（19位、9÷19 で一致）・出場数8回・連続出場8回（34位）・対局数19回（31位）
- 取得エラーは全ページで外部画像の `ERR_TUNNEL_CONNECTION_FAILED` 1件だけ（サンドボックスの外へのつながり。サイトの不具合ではない）

#### saikyo/ranking の6部門と決定（docs/decisions/saikyo.md:715）の照合（①）
| 項目 | 決定 | 本番 | 一致 |
|---|---|---|---|
| 並び（2段×3列） | 最強位・ファイナル・勝率／出場数・連続出場・対局数 | 同じ（ランキングのチップと個人成績の部門別） | はい |
| 単位 | 回・回・.xxx／回・回・回 | 同じ | はい |
| 勝率の数え方・規定 | 金のピルの数÷対局数、対局数10回以上 | 同じ（ⓘ に「対局数10回以上の選手が対象です」） | はい |
| 外した3部門 | 予選優勝・予選決勝・予選通過 | 本番に無い。旧スラッグ qualifier-win・qualifier-final・qualifier-pass は 301 で /saikyo/ranking/champion/ へ | はい |
| 説明文 | 6部門 | トップのメニュー「最強位・勝率など6部門」、description「6部門」 | はい |

スラッグ champion・final・pass-rate・appearances・streak・games は 200、`/saikyo/ranking/total/` は 404。最強位の12人は年度別の歴代最強位と一致。8〜9部門への追加は #633 に先送りで、本番に無いのは決定どおり。食い違いは無く、「作業中」として記録するのは ①-11（#630）と、この #633 の先送り分だけ。

### 相互検証の経路
リーダー経由。各サブエージェントの最初の結果を受け取った後、リーダーが SendMessage で検証する側のサブエージェント（同じ文脈を続けたもの）に相手の指摘の全件を渡し、判定を受け取った（②→③は②の完了直後、①→②・③→①は①の完了後に並行）。依頼文で相手への直接の SendMessage も許したが、3人とも使わなかった。3人の完了時刻がずれたため、直接のやり取りを前提にした段取りにはしなかった。異議は0件（条件つき・訂正つきの同意は表の判定欄に両方の言い分を書いた）。

### 実測
- 所要時間: 着手 2026-10-27 21:49 UTC、相互検証の終了 22:14 UTC 前後（コンテナの時計）。サブエージェントの1回ごとの所要（通知の値）: ① 本体 1,218秒・検証 1,339秒、② 本体 490秒・検証 137秒、③ 本体 979秒・検証 1,104秒（通知の所要の値とコンテナの時計が合わないため、時計の値は参考）
- モデル: 3人とも sonnet 指定（Sonnet 5.5）。トークン（通知の subagent_tokens。各回の終了時の値）: ① 195,621 → 205,045、② 192,832 → 216,090、③ 139,420 → 185,030
- 指示に反してファイルを変えた・git を使った事象: 3人とも「なし」と申告。リーダーが手順2の後に `git status --porcelain` を実行し、出力は空（作業ツリーの変更なし）。②は最初の計測スクリプトが120秒を超えてバックグラウンドに回ったため TaskStop で止め、方法を変えて再実行した（ファイルの変更なし）。①は tabs.js を誤って2回起動した（scratchpad の出力の上書きだけ）
- 権限の確認が出た回数: 3人とも0回。リーダー側も0回
- 計測のスクリプトは scratchpad の r1/・2/・r3/（コミットしない）
- 3人は検証の返答を返して止まった（バックグラウンドの子は無い）

### 食い違いを見つけた記述（docs。この指示では直さない）
CLAUDE.md「判断・作業の原則」のとおり、記述の食い違いは docs のみなら別コミットで直すが、この指示は「ほかの文書は直さない」ため直さず報告に列挙した:
- docs/notes/league-pages-compare.md の noindex の行と4行（②-13）
- docs/notes/saikyo-page-design.md の 155行と160行（build_top_body()、②-8）
- canonical: docs/notes/cloudflare.md:481-489（#113）と docs/decisions/saikyo.md:123・本番の saikyo/（①-5）

### 規則からの外れ（自分の作業）
- `## 報告` の節は、引用符付きのヒアドキュメント（`cat >> … <<'EOF'`）で追記した。CLAUDE.md「コミットのルール」の「文章をシェル経由で書かない」に反する。引用符付きのため展開は起きておらず、バッククォートが残っていることを grep で確かめた

### マージ
- push 直前に `git fetch origin cloudflare` のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真、`git diff --name-only origin/cloudflare HEAD` は docs/decisions/site-review.md と docs/logs/CHAT-1028-LPR-01.md だけ
- `git push origin work/1028-lpr:cloudflare` の出力の範囲: `cfad705fe..73042912c`
- この追記（マージの結果）は docs/logs のみの追いの push として同じ形で cloudflare へ入れる

### 作業ブランチの片付け（docs/notes/branch-operations.md「ブランチを削除するとき」を読んだ）
- ローカルの work/1028-lpr は、この追記を cloudflare へ入れた後、`git branch --merged origin/cloudflare` に含まれることを確かめ、HEAD を origin/cloudflare（detached）へ移して `git branch -d` で消す。削除直前の先頭はこの追記のコミット（push 後の cloudflare の先頭と同じ。SHA はターミナルへの最終報告の「ログ（公開）」の `?v=` と同じ）
- リモートの origin/work/1028-lpr は消さない（文書はローカルの削除の手順だけを書いており、リモートの削除の手順は無いため。cloudflare の祖先なので、次に同じ名前を使うときは「マージ済み」として扱える）

## 報告

- 状態: 判断待ち / 続き: CHAT-1028-LPR-02
- ブランチ: work/1028-lpr（マージ後にローカルを削除、リモートは残す）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1028-LPR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1028-lpr
- 確認用URL: なし
- マージ: 済（73042912c。docs のみ）
- issue: なし（指摘に添えた参照: #186・#614・#113・#630・#633・#566）
- 判断が必要なこと:
  - 直すものを番号で選ぶ（判定はすべて「同意」か「機械的に確認」。詳細・証拠は `## 経過` の「指摘の一覧」の元の番号の行）。saikyo/ の指摘は記録だけ（SKP の系列が落ち着いてから）
  - 現行で直す小さいもの
    - 4大会: S1 jwrc/race・wrcr/race の期の選択シートの題「期・リーグを選ぶ」を no_leagues で「期を選ぶ」に（①-12）／S2 /race/ の期の選択シートでフォーカスを閉じ込める・背面を inert に（③-1、#186）／S3 未使用の定数 MIN_ROWS・MAX_ROWS を消す（②-1）／S4 未使用の DEFAULT_DIVISION を消す（②-2）／S5 ranking.rank(limit=True) の経路・limits・特昇などテストでしか動かないものを消す（②-3、数十行）／S6 テストだけが使う OG_IMAGE を消す（②-4）／S7 Contest.script を Contest から導く関数にして JS の既定値との一致のテストを足す（②-5、#614。top の空文字の扱いに注意）／S8 style.css の .is-ouka/.is-jwrc/.is-wrcr の列挙を1つのクラスに（②-7、#614）／S9 style.css の houou 側の未使用クラス6種を消す（②-8 の前半）／S10 league-pages-compare.md の noindex の行と4行を直す（②-13、docs のみ）／S11 test_houou_pages.py の league.js を読む前半の重複をまとめる（②-14）
    - saikyo/（記録だけ）: S12 years/・vs/ の h1 に「最強戦」を付ける（①-2）／S13 個人成績の「対戦成績を見る ›」のタップの対象を24px 以上に（③-4、#186）／S14 vs/・years/ のパンくずと「もっと見る」のフォーカス枠を他ページと同じに（③-6、#186）／S15 style.css の saikyo 側の未使用クラス7種と saikyo-page-design.md の155・160行の食い違い（②-8 の後半。-hit-toggle は promo_video の capture.mjs が参照）／S16 saikyo.js の vs の節の el()・chevron() の写しを消す（②-10）／S17 検索の正規化の JS と Python の一致のテストと、対戦成績の絞り込みがカタカナとひらがなを区別する差（②-11）／S18 crumbs_html に大会名の引数を足して houou と共通に（②-12 の前半）
    - 横断（判断が要る）: S19 canonical: saikyo/ にだけあり players/?name= でも /saikyo/players/ 固定。docs/notes/cloudflare.md（#113、`?name=` の URL には付けない）と docs/decisions/saikyo.md:123（自分自身）が食い違う。どちらに合わせるか（①-5。saikyo/ の変更は記録だけ）
  - 新サイトの要件
    - 4大会: N1 順位変動の組み立てを旧 houou_race.html の文字列置換から大会ごとの生成へ（②-6、#614）／N2 期が1つの選手の通算グラフ（ベストとワーストが重なる）の表示（①-13）／N3 順位変動の節のボタンに今の節の aria-current（③-7、#186）
    - 共通: N4 「選手を探す」部品を league.js と saikyo.js で1つに（②-9、#614）／N5 ランキングの行・順位付けの共通化（②-12 の後半。順位付けは丸めの扱いをそろえてから）／N6 入力欄のフォーカス枠の付け方の統一（③-5）／N7 図の中の部品（race の chip 28px・nd 高さ36px）に44px を求めるか（③-2）／N8 画像が無いときの表示（race の chip の透明な頭文字、③-8）
    - saikyo/（記録だけ）: N9 years/・vs/ の幅928px 左寄せとパンくずの形を他ページとそろえるか（①-1）／N10 年度別の開催中の年度（2026）の空の行の表示（①-10）
  - 意図した差の候補（残すか、新サイトでそろえるか）
    - 4大会と saikyo/: D1 個人成績の title「成績詳細」と「個人成績」（①-3）／D2 saikyo の title に「日本プロ麻雀連盟」が無い・「最強戦」と「麻雀最強戦」（①-4）／D3 率の書式 .474 と 0.750（①-6、決定済み、#633）／D4 「勝率」と「通過率」（①-7、分母が卓と期で別物）／D5 単位「回」と「期」（①-8）／D6 saikyo/ranking に「（在籍者のみ）」が無い・時点の表記（①-9）／D7 ランキングの選手名リンクの高さ24px（行全体は押せる、③-3）／D8 最強戦の上限 RANK_CUT が4大会と同じ値の別定数（②-15）
  - 作業中（記録だけ）: W1 saikyo/players の年度別の順位の空欄（①-11、#630）／W2 ランキング6部門は決定と本番が一致、8〜9部門は #633 に先送り（①-14）
- 未確認の項目: 360px（設計上の最小幅）での計測、iOS・Android の実機、画像が切れたときの race の chip の見え方（サンドボックスから外部画像に届かない）、③-1 のシートの Tab は houou/ でだけ押した（他3大会は属性だけ）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2c57b6ae）: https://github.com/retroeater/mj-logs/tree/main/guide/2c57b6ae

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2c57b6ae/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
