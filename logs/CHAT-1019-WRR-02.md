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

## 報告

- 状態: 作業中
- ブランチ: work/WRR
- ログ: https://github.com/retroeater/mj/blob/work/WRR/docs/logs/CHAT-1019-WRR-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/WRR
- 確認用URL: なし
- マージ: 未
- issue: #366
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8ea27fb3）: https://github.com/retroeater/mj-logs/tree/main/guide/8ea27fb3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8ea27fb3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
