# CHAT-1019-WRR-02

- 着手日時: 2026-10-19
- 対象issue: #366
- ブランチ: work/WRR
- 着手時HEAD: 8ea27fb30

## 指示

【Claude作成】Claude Code 向け指示：WRC-R リーグを jwrc/ と同じ形で wrcr/ に作る（自律開発。未公開でプレビューまで） Chat-Ref: CHAT-1019-WRR-02 マージ: 判断待ちで止まる（平野さんがプレビューで動作を確かめ、仮置きをまとめて確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも（jwrc/ は CHAT-1019-WRC-12 で公開済み。CHAT-1018-WRR-01 は完了）。新しいセッションを推奨（長い作業のため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/WRR の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/WRR を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/WRR origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。作業中・作業の最後に origin/cloudflare が進んでいたら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・別々の大会の設定）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解く。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 これは「自律開発」の指示。平野さんへの事前の質問は済んでいる（下の「決定」）。着手後は質問せず、jwrc/ との差異、判断に迷う点、不明な点は「仮置き」で決めて最後まで進め、仮置きしたことをすべて報告の「判断が必要なこと」に一覧で書く。止まるのは下の「止まる条件」に当たるときだけ。

目的
WRC-R リーグの成績表（#366）を、WRC リーグの `jwrc/`（公開済み）と同じ形の `wrcr/` として作り、未公開の形でプレビューに出す。平野さんはプレビューで動作を確かめ、仮置きをまとめて確かめる。公開（navbar・サイトマップ・`llms.txt`・noindex を外す）は別の issue・別の指示で行う。
決定（平野さん）

* 2026-10-18: WRC-R の成績は正本のスプレッドシート「R_タイトル戦_成績」の「WRCR」シートに置く。列は「JWRC」と同じ12列（名前・期・順位・結果・合計・第1〜5節・表示・備考）。載せるのは完了した期だけ（第1〜7期。第8期は完了してから）。名前は当時の表記のままで、表示のときに「別名」を通す。「結果」は通過・敗退（docs/decisions/operations.md の 2026-10-18〈CHAT-1018-WRR-01〉の節）
* 2026-10-19（この指示の事前の質問への回答）:
   * 進め方は「自律開発」。WRC リーグ（jwrc/）の仕様を踏襲して、WRC-R リーグのページ一式（トップ・質問欄・個人成績・ランキング・順位変動）を作る
   * jwrc/ とは別のページにする。URL は `wrcr/`
   * 到達点はブランチのプレビュー（cloudflare にはマージしない）
   * ランキングの規定は WRC と同じ（期平均・通過率は出場5期以上）
   * 公開のときの navbar は「WRC-R」を独立の項目にする（「WRC」の隣に1段の項目。この指示では navbar を変えず、公開の issue に書く）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 仕様の正: `docs/notes/jwrc-top.md`（jwrc/ が houou/・ouka/ と違う所）、`docs/notes/league-pages-compare.md`（3大会の差）、`docs/notes/houou-top.md`・`ouka-top.md`・`ask-worker.md`・`design.md`、`docs/decisions/houou.md` の WRC-07〜11 の節。「踏襲」は jwrc/ にある画面・部品・数え方・確かめ方の全部。jwrc/ が大会の設定（`Contest`）で切り替えている所は、WRC-R の設定を足して同じ生成で書く（例 `generate_wrcr_pages.py` を薄い入口に、`regenerate.py` の `wrcr_pages`、`OUTPUT_OVERRIDES`、`_redirects` の行は jwrc/ に倣う）。共有の部品は複製しない
* WRCR のデータ（CHAT-1018-WRR-01 で確かめた。要確認）: 572行、第1〜7期、期ごとの通過・敗退は 1期 32・57／2期 32・39／3期 30・47／4期 28・60／5期 27・57／6期 28・49／7期 27・59。「表示」は全行 Y。途中敗退の行は「合計」に出た節の合計が入っている（公式の表では「敗退」の文字）。名前は「別名」を通すと全員が「プロ」か「連盟プロ以外」にいる。行数の止める範囲（`JWRC.rows` の 1,000〜4,000 にあたるもの）は WRC-R の行数に合わせる（例 400〜2,000。仮置き）
* 規定の値: jwrc/ と同じ（期平均・通過率は5期以上、節浮き率は25節以上、`lib/ranking.py` の `CONTESTS["JWRC"]` を写した WRC-R の設定）。表示は50位まで・同点は55人まで（`rank_cut` (50, 55)）
* 最高到達・現タイトルホルダー: jwrc/ は「タイトル」タブの決勝の順位（大会名「JPML WRCリーグ」「WRCリーグ」）を読む。WRC-R の決勝の行が「タイトル」タブにあるか（大会名「JPML WRC-Rリーグ」「WRC-Rリーグ」）は確かめていない（要確認）。あれば jwrc/ と同じく使い、無ければ最高到達は「予選通過」の段だけ、履歴が無いときの1件は仮置きで決める（例: 最新の期の予選1位）
* 進行中の期（第8期）は「WRCR」シートに行が無いので出ない。行が入ったときの行のリンクは公式の成績表（jwrc/ の `result_urls`・`result_index` に倣う。大会のページは https://www.ma-jan.or.jp/title-fight/jpmlwrcr.html 、成績表の一覧は https://www.ma-jan.or.jp/category/result-jpmlwrcr 。第8期の成績表は https://www.ma-jan.or.jp/result-jpmlwrcr/118085.html 〈第3節の時点〉）
* 仮置きの例（ほかにも出たら同じ扱い）: h1「WRC-Rリーグ」・title「<ページ> | WRC-Rリーグ | 日本プロ麻雀連盟 | ryoei.pro」（#523 で「JPML」を外した名前）、最近見た選手のキー `mj:recent-players:wrcr`、大会の印（`is-wrcr`・`data-contest="wrcr"`）、トップの3項目の説明と質問欄の例、OGP 画像 `img/ogp/wrcr/index-white.png`（白地・黒字の「WRC-Rリーグ」＋「ryoei.pro」。docs/notes/ogp.md の規則。`scripts/tests/test_og_image.py` の `TEXT_IMAGES` に足す）、質問欄の答えの範囲（WRC-R の予選の成績・ランキング・決勝・放送対局。WRC リーグと世界麻雀は答えない。jwrc/ の質問欄に WRC-R が混ざらないことも確かめる）
* 質問欄: Worker `mj-ask` と1日の上限は共有し、`contest: "wrcr"` と `data-wrcr.json` を足す（jwrc/ の `data-jwrc.json`・`JWRC_PLAN_SYSTEM` に倣う）。Worker はプレビューから問いを送れない見込み（jwrc/ と同じ。要確認）。確かめは `node --test 'workers/ask/test/*.test.mjs'` と Claude を呼ばない範囲で行い、実際の問いはマージ後に回して「未確認の項目」に書く。`wrangler.jsonc` の vars（上限・IP）は変えない
* `.github/workflows/check-assets.yml` の許可リストに `wrcr` を足すのは、この作業ブランチで行ってよい（jwrc/ も WRC の作業ブランチで足した、2026-10-18）。CLAUDE.md「ブランチ運用」のワークフローの変更の確かめに従う
* 変えないもの: navbar・サイトマップ・`llms.txt`・既存のページからのリンク（公開の指示で行う）、jwrc/・houou/・ouka/ の生成物（共通の部品を変えたときは差分が無いことを確かめる）、旧 `wrc_*` の転送、`title/` のページ
* #366 の本文は「方針: #111 は据え置き」「載せ方は未定」「データ: シートは未作成」のまま（CHAT-1018-WRR-01 で読んだ）。この指示の決定で、載せ方は「jwrc/ と同じ形の別ページ `wrcr/`」に決まった

手順

1. 調べる: #366 の状態（本文・コメント・着手中のコメント）、同じ主題の issue（「WRC-R」「wrcr」、クローズ済みも）、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ。`generate_houou_pages.py`・`lib/ranking.py`・`assets/league*.js`・`houou_ask.js`・`workers/ask/` を変えているもの）を確かめる。「WRCR」シートを生成と同じ経路で2回読み、行数・見出し・期の範囲・期ごとの人数と通過の人数・在籍者の数・名寄せされる名前をログの `## 経過` に表で書く。「タイトル」タブの WRC-R の決勝の行の有無（大会名・期・優勝者）を書く。#366 に着手中のコメントを書き、本文の「方針」「データ」の行を今の状態（載せ方は `wrcr/`、データは「WRCR」シート）に直す
2. 作る（節目ごとにコミットして push）: (a) 大会の設定に WRC-R を足す（jwrc/ と同じくリーグの無い大会。houou/・ouka/・jwrc/ の生成物は変えない）→ (b) `wrcr/` のトップ（質問欄・選手のカード）・個人成績（期別・通算・部門別）・ランキング9部門・順位変動・`search.json`・成績 JSON・OGP 画像を、未公開（全ページ noindex・navbar／サイトマップ／`llms.txt` に載せない・既存のページからリンクしない）で生成する → (c) Worker の検索データと問いの振り分けに WRC-R を足す。テストを足す（集計・成績 JSON の形・大会の振り分け。Worker は既存の3大会のテストが通ったまま WRC-R のケースを足す）。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
3. 確かめと文書: `python3 scripts/regenerate.py all` の後、wrcr/ 以外の生成物（houou/・ouka/・jwrc/ を含む）の差分が0であることを確かめる。通過の3部門と通算・期平均の上位10人を「WRCR」シートから別に数えた値と突き合わせる。375px・360px・PC 幅・1920×1080 と、字の大きさ 100%・150%・200%（375px）で各ページを headless Chrome で見る。端のデータ（通過の多い選手・出場1期の選手・途中敗退の期がある選手・名寄せされる選手〈例: 第1期の椿椋〉・WRC と WRC-R の両方に出ている選手）の画面を見る。プレビュー（確認用 URL）を出す。公開の issue を起票し（本文に公開のチェックリスト、navbar の決定「WRC-R を独立の項目に」、告知、「#366 を待つ」）、#366 に番号をコメントする。`docs/notes/wrcr-top.md`（jwrc-top.md と違う所だけ）を書き、`league-pages-compare.md` に wrcr/ の列を足し、handover.md の該当の表に1行、`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く

止まる条件

* リモートに work/WRR があり、cloudflare にマージ済みでない
* 「WRCR」シートを2回読んで行数が変わる、行数が 572 でない、または見出しが前提と違い読み替えで済まない（違いを書いて止まる）
* 同じ主題の open issue（#366 以外）がある、または #366 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、変える共通の部品の同じ関数・同じ行を変えている、または取り込みで両方を残して解けない衝突が出る
* houou/・ouka/・jwrc/ の生成物、ほかの生成物に差分が出て、直しで消せない
* Worker の既存の3大会のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、仮置きしたことの一覧（何を・どう仮置きしたか・ほかの案）を、(1) 見た目の値（プレビューを見れば判断できるもの）と (2) 見ただけでは判断しにくいもの（数え方・規定・文言・名前・質問欄の範囲・最高到達と現タイトルホルダーの元データなど）に分けて書く。突き合わせで違った行、共通の部品で変えた所（jwrc/ などへの影響が0であることの確かめを含む）、マージ後に確かめること（質問欄の実際の問い）も書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（トップ・個人成績〈通過の多い選手・途中敗退の期がある選手・名寄せされる選手〉・ランキングの通算・通過率・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-02 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件。`WRR` はこのチャットの WRR-01（このセッションの前の指示）だけで、他のセッションの使用は無い
- 作業ブランチ: work/WRR はローカル・リモートとも無し → `git checkout -b work/WRR origin/cloudflare`（8ea27fb30）
- 0. 「指示」欄の末尾は指示文の最後の行（「着手後は質問せず、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1: 調べる

- issue（REST の `repos/retroeater/mj/issues?state=all` 全件の題・本文を語で絞る。search の API はセッションで使えない）:
  - 「WRC-R」: #594 closed（jwrc/ への作り直し）・#523 closed（「JPML」を外す）・#479・#452・#385・#222（いずれも closed、本文に語があるだけ）・#366 open
  - 「wrcr」: #366 だけ。同じ主題の open issue は #366 以外に無い
- #366: Open、ラベル「状況: 待ち」「分野: データ」「対象: wrc_results」「対象: wrc_ranking」。コメント4件（RUN-05・RUN-06・RUN-07・WRR-01）で、他のセッションの着手中のコメントは無い
  - 着手中のコメント: https://github.com/retroeater/mj/issues/366#issuecomment-6260346684
  - 本文を REST の PATCH で直した（直前の `updated_at` 2026-10-19T05:26:47Z は自分の着手中のコメントによるもの）:
    - 「方針」→「**方針: jwrc/ と同じ形の別ページ `wrcr/` に載せる（平野さん、2026-10-19、CHAT-1019-WRR-02。#111 は据え置き）**」
    - 「データ」→「**データ: 「R_タイトル戦_成績」の「WRCR」シート（第1〜7期、572行。第8期は完了してから入れる。2026-10-18、CHAT-1018-WRR-01）**」
    - 「未定のこと」の「載せ方（…）は未定」→「載せ方は決まった（上の「方針」）」
- 未マージの work/ ブランチ（10本）のうち、共通の部品（`generate_houou_pages.py`・`generate_houou_race.py`・`generate_ask_data.py`・`lib/ranking.py`・`assets/league*.js`・`houou_ask.js`・`workers/ask/`・`regenerate.py`・`_redirects`・`style.css`・`check-assets.yml`・`test_og_image.py`）を変えているのは `work/1017-lvp`（style.css の saikyo・title・live の箇所、1,540〜3,260 行台）だけ。WRC-R で触る `.is-jwrc` の行（5,700 行台）とは重ならない
- 「WRCR」シート（`fetch_records(houou_tab.SPREADSHEET_ID, "WRCR", 12列)`、生成と同じ経路）を2回読んだ: 1回目 572行・2回目 572行、内容も同一。見出しは前提どおり `名前・期・順位・結果・合計・第1節〜第5節・表示・備考`

  | 期 | 人数 | 通過 | 敗退 | 「プロ」 | 「連盟プロ以外」 |
  |---|---|---|---|---|---|
  | 7 | 86 | 27 | 59 | 86 | 0 |
  | 6 | 77 | 28 | 49 | 77 | 0 |
  | 5 | 84 | 27 | 57 | 82 | 2 |
  | 4 | 88 | 28 | 60 | 85 | 3 |
  | 3 | 77 | 30 | 47 | 73 | 4 |
  | 2 | 71 | 32 | 39 | 69 | 2 |
  | 1 | 89 | 32 | 57 | 84 | 5 |

  - 期の範囲は第1〜7期。シートの名前は 196種、別名を通した今の名前は 192人、うち「プロ」の在籍者 184人（ページに出るのは在籍者だけ）
  - 名寄せされる名前（シートの名前 → 今の名前、出た期）: ガースネルソン → ガース（1・2・4・5期）、仲田浩二 → ポロリ（1〜4期）、椿椋 → つばきりょう（連盟プロ以外、1・2期）、樫野凪 → かしのなぎ（4〜7期）、江嵜晋之介 → 江崎しんのすけ（4期）、渡辺航平 → こうちゃん（4期）
- 「タイトル」タブの WRC-R の決勝: 大会名「JPML WRC-Rリーグ」で32行（第8期の4行は名前が空・「表示」が空。第1〜7期の28行は「表示」Y）。「WRC-Rリーグ」の行は無い。優勝: 1期 仲田加南・2期 角谷陽介・3期 勝又健志・4期 小車祥・5期 覚野陽生・6期 猿渡輝也・7期 小川尚哉
  - → jwrc/ と同じく最高到達の優勝〜4位と現タイトルホルダー（第7期の優勝者）に使う。大会名は「JPML WRC-Rリーグ」と #523 の後の「WRC-Rリーグ」の両方を受ける

### 手順2: 作る

- (a) 大会の設定（コミット 7c2a2b94d）:
  - `lib/ranking.py` の `CONTESTS["WRCR"]`（`CONTESTS["JWRC"]` と同じ値）
  - `generate_houou_pages.py` に `WRCR`（`JWRC` と同じ形で、`noindex=True`・`rows=(400, 2000)`・`final_titles=("WRC-Rリーグ", "JPML WRC-Rリーグ")`・`result_urls={8: 公式の第8期の成績表}`・`result_index=大会のページ`・`script` の suffix と `recentKey`）。共通の関数は変えていない
  - 入口 `generate_wrcr_pages.py`・`generate_ask_data_wrcr.py`（jwrc の入口の写しで、設定だけ替えた）。`regenerate.py` の `SHARED_GENERATORS`・`OUTPUT_OVERRIDES` に `wrcr_pages`・`ask_data_wrcr`
  - `generate_ask_data.py` の `OTHERS` に `wrcr`（決勝・放送対局の大会名「WRC-Rリーグ」、`/title/jwrc-r/`・`/live/jwrc-r/`、`final_name`「WRC-Rリーグ決勝」）
  - `_redirects` に jwrc/ と同じ5行（`/wrcr`・`/wrcr/`・`/wrcr/ranking/`・`/wrcr/:slug/`・`/wrcr/ranking/:slug/`）
  - `style.css`: 期別のカードの期の欄の幅の規則の選択子に `.is-wrcr .mj-houou-term-bar` を足した（1行。ouka・jwrc の値と同じ）
  - `check-assets.yml` の最上位の許可に `wrcr`。作業ブランチへの push で動き、dffc79e5c（wrcr/ を含む）の check-run「check」が success
  - OGP 画像 `img/ogp/wrcr/index-white.png`（`build_ogp_image.py --text WRC-Rリーグ --footer ryoei.pro --color '#212529' --bg '#ffffff' --max-size 200 --tracking -0.03`。字体 NotoSansJP-Bold を `~/.local/share/fonts/` に置き、同じ引数で jwrc・ouka の画像を作り直すとコミット済みのものと同じバイト列になることを先に確かめた）。`test_og_image.py` の `TEXT_IMAGES` に足した
  - 文書のテスト（`test_league_pages_compare.py`・`test_redirects_inventory.py`）が落ちたので、`league-pages-compare.md` に wrcr/ の列、`redirects.md` に `/wrcr/ranking/` の行と末尾スラッシュ・`?division=` の行の `wrcr` を足した
- (b) 生成（コミット dffc79e5c）: `wrcr/` 205ファイル（選手ごとの成績 184、ランキングは第7期終了時点）。ランキングの表示件数: 通算 50・期平均 27・期最高 50・通過回数 51（29〜51位が2回の同点）・通過率 41・連続通過 34・節最高 50・節浮き率 39・節連続浮き 39。現タイトルホルダー 小川尚哉（在籍）
- (c) Worker: `ask.mjs` に `WRCR_PLAN_SYSTEM`（jwrc の写しで大会名を WRC-R に。`none` に「R の付かない WRC リーグ」を足した）、`PLAN_SYSTEMS.wrcr`。`index.mjs` に `data-wrcr.json`。`data-wrcr.json` は 59,095 バイト（選手 192名、決勝 7期、放送対局 47ページ）
- テスト: `scripts/tests/test_wrcr_pages.py`（設定が jwrc と同じ形・ページの noindex と大会の印・進行中の期のリンク・質問欄のデータの行き先・regenerate の登録・生成物の全 HTML が noindex）、`workers/ask/test/wrcr.test.mjs`（データの形・WRC と WRC-R のデータが混ざらない・予選・選手〈旧名〉・優勝者・9部門・contest の振り分け・jwrc の指示に WRC-R が入らない）
- `python3 -m unittest discover -s scripts/tests`: 1438件 OK。`node --test 'workers/ask/test/*.test.mjs'`: 53件 pass（既存の3大会のテストを含む）
- 公開の issue: #611 を起票し、#366 にコメントした（https://github.com/retroeater/mj/issues/366#issuecomment-6260453474 ）
- 文書（コミット 65a284195）: `docs/notes/wrcr-top.md`、handover.md の表に1行（24,006 → 24,167 バイト、警告域 26KB の下）、static-generation.md「ページの一覧」、ask-worker.md に WRC-R の節

### 手順3: 確かめと文書

- `python3 scripts/regenerate.py all`（終了コード 0。全31ページ）: houou/・ouka/・jwrc/・`workers/ask/data*.json`（wrcr を含む）・ほかの大会の生成物に差分なし。wrcr/ も生成済みと同じ（差分なし）
  - 差分が出たのは 43ファイル＋新規1つ（`jpml_pros.html`・`live/masters/24/`・`mtsuku/` 6・`saikyo/` 約30〈2026-10-18 の新しい対局 `saikyo/2026/20261018-s2.html` を含む〉・`sitemap-saikyo.xml`・`title/masters/24.html`・`title/nihon-series/`）。中身は対局の回数・件数（例「14回」→「15回」、「他の対局（13件）」→「（14件）」）と新しい対局の行で、この作業で変えていない生成（saikyo・mtsuku・live・title・jpml_pros）の元データの変化。作業ブランチには入れず、ファイル名を挙げて `git restore` で戻した（cloudflare の定期の再生成で入る。取り込んだ cloudflare にも同じ 2026-10-18 の対局がすでに入っていた）
- 突き合わせ（「WRCR」シートを `fetch_records` で読み、別名を通し「プロ」の在籍者だけで、生成のコードを使わずに数え直した上位10人と `wrcr/ranking/` の表）: 通過回数・通過率・連続通過・通算・期平均の5部門とも名前・値が一致。違いは期平均の9位の同点3人（新谷翔平・小川尚哉・ポロリ、+76.7）の並びだけ（ページは名前の順）
- 画面（ローカルの静的サーバーと headless Chrome〈playwright〉）: トップ・個人成績（選手を選ばない画面、藤本哲也の期別・通算・部門別、黒田良、葉山唯一、`?name=樫野凪`、`?name=椿椋`、小川尚哉）・ランキング（通算・通過率）・順位変動（第7期・第1期）を、375px・360px・1280px・1920×1080 と 375px の字 150%・200% で 84枚。はみ出し 0、JS のエラー 0（404 はローカルに Worker が無い `/api/ask/status` と、在籍者でない `椿椋` の成績 JSON だけ）。ダーク表示（375px、黒田良・順位変動）も見た
  - 端のデータ: 通過の多い選手（藤本哲也 通過4回・通過率 0.667 で1位。黒田良 通過4回）・出場1期（葉山唯一。WRC リーグにも出ている）・途中敗退の期（黒田良 第3期 第2節まで。第1期の 88位 木原翼・89位 山脇千文美は順位変動で「–」「第4節まで」）・名寄せ（`?name=樫野凪` で かしのなぎ が開く）・連盟プロ以外（`?name=椿椋` は「椿椋 の成績は見つかりません（「プロ」に在籍する選手だけを載せています）」。houou/ と同じ扱い）・現タイトルホルダー（選手を選ばない画面の1件は 小川尚哉、印「現タイトルホルダー」、名前の面は「最高到達 優勝 1回（7期）」）
  - WRC と WRC-R の両方に出ている選手は 170人（在籍者 184人のうち）。最近見た選手のキーが別（`mj:recent-players:wrcr`）なので、jwrc/ の履歴は wrcr/ に出ない
- プレビュー: dffc79e5c の check-run「Workers Builds: mj」は 2026-10-19T05:41 から `in_progress` のまま（docs/notes/cloud-sessions.md の「報告が止まったまま残ることがある」）。プレビューの別名 URL（URL は最終報告にだけ書く）で、`/wrcr/`・`/wrcr/ranking/total/`・`pass-rate/`・`race/`・`search.json`・成績 JSON・OGP 画像が 200、`/wrcr` と `/wrcr/ranking/` が 301、トップの title「WRC-Rリーグ | 日本プロ麻雀連盟 | ryoei.pro」と noindex を確かめた
- 取り込み: 作業の最後に origin/cloudflare（13コミット進んでいた。saikyo・mtsuku の生成物と文書・ログ）を `git merge` で取り込んだ。衝突なし。この作業で触ったファイルとの重なりなし
- 決定の記録: docs/decisions/houou.md に「## 2026-10-19（CHAT-1019-WRR-02）」（jwrc/ の決定と同じファイル）

## 報告

- 状態: 判断待ち / 続き: CHAT-1019-WRR-03
- ブランチ: work/WRR
- ログ: https://github.com/retroeater/mj/blob/work/WRR/docs/logs/CHAT-1019-WRR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRR
- 確認用URL: プレビューあり（URL は最終報告）。トップ・個人成績（藤本哲也・黒田良・`?name=樫野凪`）・ランキング（通算・通過率）・順位変動。この版で見える印: トップの h1「WRC-Rリーグ」と質問欄の例「例：7期の予選1位は？」
- マージ: 未（平野さんの判断待ち。プレビューと仮置きを確かめた後の別の指示）
- issue: #366（着手中のコメント・本文の「方針」「データ」の行を直した・公開の issue の番号のコメント）、#611（公開の issue を起票）
- 判断が必要なこと:
  - プレビューで動作を確かめ、下の仮置きを決めて、cloudflare へのマージの指示を出す
  - 仮置き (1) 見た目の値（プレビューで判断できる）:
    - h1・題名の大会名「WRC-Rリーグ」（title「<ページ> | WRC-Rリーグ | 日本プロ麻雀連盟 | ryoei.pro」。#523 の後の名前）。ほかの案: 「WRC-R リーグ」（空白あり）
    - 質問欄の例「例：7期の予選1位は？」（jwrc/ の「例：19期の予選1位は？」に倣った）
    - トップの3項目の説明は jwrc/ と同じ（「選手ごとの期別・通算・最高到達」「通算・通過率など9部門」「期ごとの節単位の予選の順位」）
    - OGP 画像 `img/ogp/wrcr/index-white.png`（白地・黒字の「WRC-Rリーグ」＋「ryoei.pro」）。文字ベースの画像の7種類目（docs/notes/ogp.md の規則〈メニューの最上位でそれ自体がページ〉には、navbar を独立の項目にする決定で当たる）
    - 期別のカード・結果のチップ・通算のグラフの金の丸・順位変動の通過ラインの帯は jwrc/ と同じ色と形
    - 未公開の間の共通ナビは黒地の旧ナビのまま（白地の `.mj-nav-new` は公開の #611 で当てる。jwrc/ も公開で当てた）
  - 仮置き (2) 見ただけでは判断しにくいもの:
    - 大会の設定の名前と印: 設定 `WRCR`・シート名「WRCR」、`is-wrcr`・`data-contest="wrcr"`・最近見た選手のキー `mj:recent-players:wrcr`・質問欄の `contest: "wrcr"`・記録の型「wrcr:league」
    - 行数の止める範囲 400〜2,000（`WRCR.rows`。2026-10-19 は 572行。第8期以降も毎期 90行前後の見込みで当面収まる）
    - 規定・表示の上限は jwrc/ と同じ（`CONTESTS["WRCR"]` は `CONTESTS["JWRC"]` の写し、`rank_cut` (50, 55)）。WRC-R は人数が少なく、期平均は27人・通過率は41人・節浮き率は39人で全件が出る
    - 最高到達と現タイトルホルダーの元データは「タイトル」タブの「JPML WRC-Rリーグ」（第1〜7期の28行。第8期の4行は名前が空で読まない）。#523 の後の「WRC-Rリーグ」も受ける。現タイトルホルダーは第7期の優勝者 小川尚哉。第5期の優勝者 覚野陽生は在籍者でないので wrcr/ に出ない（名前の面・ランキング・最近見た選手の1件の対象外。決勝の質問欄の答えには出る）
    - 質問欄の答えの範囲: WRC-R の予選の成績・ランキング・決勝（title/search.json の「WRC-Rリーグ」、`/title/jwrc-r/`）・放送対局（/live の「WRC-Rリーグ」、`/live/jwrc-r/`、47ページ）。WRC リーグ（R の付かない方）と世界麻雀は `none`（`WRCR_PLAN_SYSTEM` に明記）。決勝の名前「WRC-Rリーグ決勝」（`OTHERS.wrcr.final_name`、jwrc の「WRCリーグ決勝」に倣った）
    - jwrc/ の質問欄の指示（`JWRC_PLAN_SYSTEM`）は変えていない（WRC-R を名指しで `none` にする文は無い。WRC リーグ以外の大会として `none` に入る想定）。ほかの案: jwrc 側にも「WRC-R リーグ」を `none` の例に足す（jwrc/ の挙動が変わるので今回はしない）
    - 進行中の期のリンク: 第8期は公式の成績表 `result-jpmlwrcr/118085.html`（第3節の時点のページ）、ほかの期は大会のページ `title-fight/jpmlwrcr.html`。第8期の行は「WRCR」シートに無いので今は出ない。第8期が完了して行を入れるときは「表示」Y で入れる（進行中の行としては出ない）
    - 連盟プロ以外の選手（つばきりょう など8人）は個人成績・ランキングに出ず、順位変動には出る（jwrc/ と同じ）
  - 共通の部品で変えた所: `style.css` の期の欄の幅の規則に選択子 `.is-wrcr` を1つ足しただけ（jwrc/・ouka/ は同じ値のまま）。`generate_houou_pages.py`・`generate_ask_data.py`・`lib/ranking.py`・`regenerate.py`・`ask.mjs`・`index.mjs` は設定・辞書の項目を足しただけで関数は変えていない。`regenerate.py all` で houou/・ouka/・jwrc/・data*.json の差分 0 を確かめた
  - 突き合わせで違った行: なし（期平均の9位の同点3人の並びだけ。ページは名前の順）
- 未確認の項目:
  - 質問欄の実際の問い（Anthropic を呼ぶ）。ローカル・プレビューには Worker が無いので、マージ後の本番で wrcr/ の質問欄から1〜2問（例「7期の予選1位は？」「WRC-Rの歴代優勝者は？」）を送り、WRC リーグの答えが混ざらないこと、jwrc/ の質問欄の答えが変わらないことを確かめる
  - 実機（iPhone など）での見え方・字の大きさの連動は、平野さんがプレビューで確かめる
  - Workers Builds の check-run（dffc79e5c）は `in_progress` のまま（プレビューの別名 URL は新しい版を返した）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 268fdb4e）: https://github.com/retroeater/mj-logs/tree/main/guide/268fdb4e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/268fdb4e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
