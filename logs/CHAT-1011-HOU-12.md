# CHAT-1011-HOU-12

- 着手日時: 2026-10-11
- 対象issue: #518
- ブランチ: work/1008-hou
- 着手時HEAD: 9690d73a

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の新ページ `houou/`（work/1008-hou）を未公開の形のまま cloudflare へマージし、公開の issue を起票する（段1） Chat-Ref: CHAT-1011-HOU-12 マージ: 承認済み（チャットで。2026-10-11、平野さんが HOU-11 のプレビューを確かめたうえで「この状態でいったんマージしてよい」と明示） 貼る時機: CHAT-1010-HOU-11 の判断待ちの後。いつでも（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-hou を続けて使う（HOU-01〜11 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「生成物を含む作業ブランチを取り込む・マージするとき」のとおり生成し直して解く。生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1010-HOU-11 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。HOU-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1011-HOU-12` を足す（`docs/notes/branch-operations.md`「作業ログの寿命」）。 マージを伴うので、着手前に CLAUDE.md「ブランチ運用」節と docs/notes/branch-operations.md を読む。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-01〜11 で作った `houou/`（トップ・個人成績・ランキング9部門・リーグ推移・順位変動）を、未公開の形（noindex・navbar・sitemap・`llms.txt`・既存ページからのリンクに載せない）のまま cloudflare（本番）へ入れる（docs/new-page-checklist.md「段1」）。あわせて公開の issue を起票し、公開の段で行うことを本文に書く。旧ページ（`houou_ranking.html`・`houou_leagues.html`・`houou_race.html`・`houou_results.html`）はこの指示では変えない。
決定（2026-10-11、平野さん）

* HOU-11 の状態でいったんマージしてよい（未公開の形のまま）
* マージの後に直したいこと（この指示ではしない。次の指示で行う）: 特別昇級のときも昇級の演出（紙吹雪）を付け、飛び級した段数（2〜9段）に応じて豪勢にする

前提（チャット側。平野さんの決定ではない）
実物に合わせて変えてよく、変えたら報告に書く。
マージの範囲と見込み（「決定とシートの変化で説明できる差分だけ」がマージしてよい差分）

* `origin/cloudflare...work/1008-hou` の差分の見込み: `houou/` 以下の新しいファイル（生成物）、`assets/houou.js`、`style.css` の `houou/` の節（と HOU-05 で分けた順位変動の節）、`scripts/generate_houou_pages.py`・`scripts/lib/`（ranking・results など）・`scripts/regenerate.py`（`OUTPUT_OVERRIDES`）・`scripts/tests/`、HOU-02 で関数に分けた `scripts/generate_houou_leagues.py`・`scripts/generate_houou_race.py`（旧ページの出力は変わらない）、`assets/share.js`（押したときに data 属性を読む）、`_redirects`（`/houou` の 301 とディレクトリの 200、ランキングの部門ページの 200）、`.github/workflows/assets-check.yml` の `allowed()` の `houou` の1語、`docs/`（notes・decisions・logs・new-page-checklist）。これ以外のファイルが差分にあれば、理由を確かめ、説明できなければ止まる
* 取り込みの後に `python3 scripts/regenerate.py all` を実行し、旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が無いことを確かめる（`jpml_pros`〈鍵〉・`resource_dictionary`・`books_pages`〈シートの確かめで止まる。この作業と無関係〉は外してその旨を書く）。シートの変化で動いた生成物は、変わったファイルと理由を報告に書く（止まらない）
* テスト: `python3 -m unittest discover -s scripts/tests` が通ること
* 未公開の形の確かめ（マージ前に作業ブランチで、マージ後に本番で）: `houou/` の全 HTML に `<meta name="robots" content="noindex">` がある／`navbar.js`・`sitemap*.xml`・`llms.txt` に `houou/` が無い／既存のページ（旧4ページを含む）から `houou/` へのリンクが無い（`git grep` で）／比較ページ（`compare.html`）が `houou/` に1つも無い
* 静的アセットの総数: `.assetsignore` を除いた配信対象のファイル数を数え、Workers の上限 20,000 に対する数を報告に書く（15,000 を超えていたら「判断が必要なこと」に書くが、止まらない）
* マージの手順は CLAUDE.md「ブランチ運用」のとおり（`git push origin work/1008-hou:cloudflare`、push 直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）
* マージの後: Workers Builds の check-run の成否（待つ上限15分）、本番の `https://ryoei.pro/houou/?v=<未使用の値>`・`/houou/players/`・`/houou/ranking/`・`/houou/ranking/<部門の1つ>/`・`/houou/leagues/`・`/houou/race/` が 200 で noindex があることを `curl` で確かめる（`x-robots-tag` も見る）。`/houou` が `/houou/` へ 301 になること。ブラウザでの見え方は平野さんが確かめる（check-run の成功だけで「本番の見え方を確かめた」としない）
* マージで動く自動処理の見込み: cloudflare への push で Workers Builds が1回走る。`regenerate-page.yml` などのワークフローがこの push で動くかを `.github/workflows/` の起動条件で確かめ、動くなら何が再生成されるかを報告に書く

公開の issue の起票（docs/new-page-checklist.md「段1」の最後の項目）

* 同じ主題の issue をクローズ済みを含めて検索し（「houou/ 公開」「鳳凰戦 公開」など）、無ければ起票する。題の案「鳳凰戦の新ページ houou/ を公開する（旧4ページの転送を含む）」。ラベルは実物の一覧から（`分野: UI/UX`・`対象:` の該当するもの）
* 本文は docs/new-page-checklist.md「段2 公開する」のチェックリストを写し、`docs/notes/houou-top.md`「非公開の形と公開の方針」の「公開の段」に HOU-11 でそろえた項目を足す。少なくとも次を含める:
   * 公開の条件: 平野さんの iPhone での確かめ（文字の拡大〈Chrome で最大〉で5ページが崩れないこと、演出の見え方）
   * navbar「鳳凰戦」の項目（仮置き: トップ・個人成績・ランキング・リーグ推移・順位変動。公開の issue で平野さんが決める）
   * 旧4ページの 301（`houou_results.html?name=` → `houou/players/?name=`、`houou_ranking.html?sheet=鳳凰&division=<部門>` → `houou/ranking/<スラッグ>/`、`houou_leagues.html?name=` → `houou/leagues/?name=`、`houou_race.html` → `houou/race/`。クエリの引き継ぎ方は Cloudflare の転送の仕組みで確かめる）。旧 `houou_race.html` の告知の X ポストのリンクが転送で生きること
   * sitemap: `houou/` のページを載せる。`sitemap.xml`・`sitemap-pages.xml` の冒頭のコメントの件数を実体に合わせて直す（2026-10-09 の平野さんの判断、#227 にコメント済み）
   * `llms.txt` の鳳凰戦の項目を `houou/` に差し替える
   * noindex を外す（`NOINDEX_TAG` の1か所）
   * 公開後の確かめ（docs/new-page-checklist.md「公開後の確かめ」）と Search Console
   * 公開した後に閉じる issue: #518・#520・#141（#7 の完了条件）・#371・#228。#111・#7 へのコメント
   * 旧ページと共用の `generate_houou_race.py`・`generate_houou_leagues.py` の「プロ」タブの列記号の読み方（#536 の対象外）は、旧ページを廃止するときに片付ける
* 起票した番号を #518 にコメントし、`docs/notes/houou-top.md` と `docs/notes/static-generation.md`「ページの一覧」の「公開は別 issue（未起票）」を「公開は #<番号>」に直す（マージと同じ作業ブランチで、マージの前に）

文書

* `docs/handover.md`: 「最終更新」と「次にやること」に、`houou/` が未公開で本番に入ったこと・公開の issue の番号・次の指示（特別昇級の演出）を1〜2行で書く（追記先の今の内容を読んでから。上限は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）
* `docs/decisions/houou.md` に上の「決定」節を 2026-10-11 の節として足す
* 作業ブランチの片付けは、マージの後に docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、その手順のとおり（片付けは、関係のない検証の成否に条件づけない）

手順

1. 0章の確かめ、`origin/cloudflare` の取り込み、差分の範囲の確かめ、全ページの再生成とテスト、未公開の形の確かめ、静的アセットの総数
2. 公開の issue の起票、#518 へのコメント、文書（houou-top.md・static-generation.md・handover.md・decisions）の更新。push する
3. cloudflare へのマージ（CLAUDE.md「ブランチ運用」の手順）、本番の確かめ（check-run・`curl`）、作業ブランチの片付け。報告の「判断が必要なこと」に、平野さんが本番で確かめる手順（URL は `https://ryoei.pro/houou/` など本番のもの。未公開でも URL を直接打てば見える）と、公開の issue の番号を書く

止まる条件

* 0章の確かめが通らない（HOU-11 が判断待ちでない、work/1008-hou がリモートに無い）
* 差分に、上の「マージの範囲と見込み」で説明できないファイルがある
* 旧ページ・ほかのページの生成物に、シートの変化で説明できない差分が出た
* テストが通らない
* 未公開の形の確かめが1つでも通らない（noindex が無いページがある、navbar・sitemap・`llms.txt` に `houou/` がある、既存のページから `houou/` へのリンクがある、比較ページが残っている）
* 生成スクリプト・CSS・JS・データ・設定の衝突（生成物だけの衝突と、両立する文書の衝突は解いてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（マージした後の結果〈check-run・本番の `curl`〉を書く docs/logs のみの追いの push は可）
* `docs/decisions/houou.md` に上の「決定」節を足す（2026-10-11）
* マージは冒頭の「マージ:」の行のとおり（承認済み。止まる条件に当たれば止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-HOU-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-HOU-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-11 着手。**この会話は HOU-01〜11 から続いている（新しく始めたセッションではない）**
- 0. 指示欄の末尾は指示文の最後の行と一致。HOU-11 のログの報告は 状態「判断待ち」。HOU-11 の状態の末尾に `/ 続き: CHAT-1011-HOU-12` を足した（このログと同じコミット）。`CHAT-1011-HOU-12` のコミットは無し。`origin/work/1008-hou` はリモートにありローカルと一致（9690d73a）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md（「マージするとき」「ブランチを削除するとき」「生成物を含む…」）、docs/notes/cloud-sessions.md「ブランチの削除」を読んだ

### 手順1 取り込み・差分の範囲・再生成とテスト・未公開の形・アセット数

- 取り込み: `git merge origin/cloudflare`（d237af1c）。衝突なし。取り込んだのは docs/logs・`scripts/promo_video/dictionary/`（DIC の動画）・ワークフローの Python 3.12 固定（#538）など
- 差分の範囲（`origin/cloudflare...HEAD`）: `houou/` 747ファイル（HTML 13・`search.json`・`results/*.json`・`race/*.json`）、`assets/houou.js`・`assets/houou_race.js`、`assets/share.js`（押したときに data 属性を読む）、`style.css`、`scripts/generate_houou_pages.py`・`generate_houou_leagues.py`・`generate_houou_race.py`・`lib/ranking.py`・`lib/results.py`・`regenerate.py`・`tests/test_houou_pages.py`、`_redirects`（`/houou` の 301、`/houou/` と `/houou/:slug/`・`/houou/ranking/:slug/` の 200）、`.github/workflows/assets-check.yml`（`allowed()` に `houou` の1語）、`docs/`（decisions/houou.md・publishing.md、new-page-checklist.md、notes/houou-race.md・houou-top.md・static-generation.md、logs）。見込みの外のファイルは無い。指示の見込みに無い `assets/houou_race.js` は順位変動の写し（HOU-05、`houou/race/` が読む）で説明できる
- ワークフローの変更（`assets-check.yml`）は、`work/**` への push でも走る（`on.push.branches`）。作業ブランチの push で check-run「check」が success（直近は f5bcfa4a・9788afb3）なので、変えた後の版は作業ブランチで実行済み
- マージで動く自動処理（`.github/workflows/` の起動条件）: Workers Builds 1回。`assets-check.yml`（cloudflare への push）。`regenerate-page.yml`（`scripts/generate_*.py`・`scripts/lib/**`・`*.js` の変更で動く。`scripts/lib/` が変わるので `regenerate.py --changed` が全ページを対象にし、その時点のシートで再生成。`books_pages` などシートの確かめで止まるものは飛ばす作り）。`sitemap-lastmod.yml`（`**.html` の変更で動く。既存のエントリの lastmod を直すだけで、`houou/` を sitemap に足さない）
- 全ページの再生成（`jpml_pros`・`resource_dictionary`・`books_pages` は外した）: 差分なし
- テスト: `python3 -m unittest discover -s scripts/tests` 772件 OK
- 未公開の形（作業ブランチ）: `houou/` の HTML 13 ファイルすべてに `<meta name="robots" content="noindex">`／`navbar.js`・`sitemap.xml`・`sitemap-pages.xml`・`llms.txt` に `houou/` 無し（`sitemap-title.xml` の `title/houou/` は別のページ）／既存のページ（`houou/`・`docs/`・`scripts/`・新ページの JS を除く追跡ファイルの href・src・value・data 属性と `ryoei.pro/houou`）を相対パスまで解決して、ルートの `houou/` を指すものは 0件（`live/houou/`・`title/houou/` は別）／`compare.html` は 0
- 配信対象のファイル数: 追跡 3,471 − `.assetsignore` に当たる 1,014 = 2,457（Workers の上限 20,000 の約 12%）。うち `houou/` 747

### 手順2 公開の issue・文書

- 同じ主題の issue の検索（「鳳凰戦 新ページ houou/ 公開」「houou 公開 旧4ページ 転送 301」、クローズ済みを含む）: #518・#519・#520・#508（houou_race の公開、クローズ済み）などで、`houou/` の公開の issue は無かった
- 起票: #540「鳳凰戦の新ページ houou/ を公開する（旧4ページの転送を含む）」。ラベルは #518 と同じ `分野: UI/UX`・`対象: houou_results`・`対象: houou_leagues`・`対象: houou_ranking`（`対象: houou_race` のラベルは無い）。本文は docs/new-page-checklist.md「段2」を写し、公開の条件（iPhone での文字の拡大と演出の見え方、先に特別昇級の演出）・navbar の仮置き・旧4ページの 301 とクエリの引き継ぎ・sitemap と冒頭コメントの件数・`llms.txt`・`NOINDEX_TAG`・公開後の確かめと Search Console・閉じる issue（#518・#520・#141・#371・#228）とコメント（#111・#7）・共用の2本の「プロ」タブの読み方を書いた
- #518 に #540 をコメントした
- 文書: `docs/notes/houou-top.md`（公開の issue #540）、`docs/notes/static-generation.md`「ページの一覧」の2か所（「公開は別 issue（未起票）」→「公開は #540」）、`docs/handover.md`（「最終更新」を 2026-10-11 にして `houou/` の1行を足し、3行に収めるため #298 の写しの行〈2026-10-07〉を消した。「次にやること」の表に #540 の行。23,294 バイト → 行を消した後も警告域 26KB の下）、`docs/decisions/houou.md`（2026-10-11 の節）。handover の変更で走った check（`assets-check.yml`）は success

### 手順3 マージ・本番の確かめ・片付け

- マージ: 再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` が真（origin/cloudflare c75f5889）を確かめ、`git push origin work/1008-hou:cloudflare`（c75f5889..efa62293）
- check-runs（efa62293）: 「Workers Builds: mj」success（15:23:47Z）、check success、sync success、regenerate success、「Workers Builds: mj-scheduler」success
- 自動再生成（`regenerate-page.yml`、`scripts/lib/` の変更で全ページが対象）: 0c211541「chore: regenerate … via GitHub Actions」。変わったのは `sitemap-pages.xml` の `video_en.html` の lastmod 1行（2026-10-10 → 2026-09-13、`update_sitemap_lastmod.py --from-git` がファイルの最終コミット日にそろえた）だけで、ページの生成物の差分は無かった
- 本番（`curl`、`?v=` に未使用の値）: `/houou/`・`/houou/players/`・`/houou/ranking/`・`/houou/ranking/term-high/`・`/houou/leagues/`・`/houou/race/` はすべて 200 で `<meta name="robots" content="noindex">` あり。`x-robots-tag` の応答ヘッダは無い（noindex は meta だけ）。`/houou` は `/houou/` へ 301（クエリを引き継ぐ）。`/houou/leagues/compare.html` は 404。本番の `navbar.js`・`sitemap.xml`・`sitemap-pages.xml`・`llms.txt` に `houou/` 無し。ブラウザでの見え方は確かめていない（平野さん）
- 片付け: このクラウドセッションはブランチを削除できない（docs/notes/cloud-sessions.md「ブランチの削除」）。マージ済みの `work/*` は `delete-merged-branches.yml` が毎日（22:53 UTC）、先頭が24時間より前のものを削除する。このログの push の後、同じ先頭を `cloudflare` へも push して（docs/logs と docs/decisions だけの追いの push）マージ済みの状態にする。削除の対象の判定と記録はそのワークフローの出力に残る。追いの push の前の先頭は 0c211541（`origin/cloudflare` を fast-forward で取り込んだもの）

## 報告

- 状態: 完了
- ブランチ: work/1008-hou（マージ済み。削除は `delete-merged-branches.yml` に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1011-HOU-12.md
- 比較URL: なし（マージ済み）
- 確認用URL: https://ryoei.pro/houou/ ほか（下の「判断が必要なこと」）
- マージ: 済（c75f5889..efa62293。続いて自動再生成 0c211541、この報告の docs のみの追いの push）
- issue: #518（#540 をコメント）、#540（公開の issue、起票）
- 判断が必要なこと:
  1. 本番での確かめ（未公開でも URL を直接打てば見える）: トップ https://ryoei.pro/houou/ 、個人成績 https://ryoei.pro/houou/players/?name=白鳥翔 、ランキング https://ryoei.pro/houou/ranking/ （チップで部門を切り替える）、リーグ推移 https://ryoei.pro/houou/leagues/?name=大久保隼人 （再生のボタンで演出）、順位変動 https://ryoei.pro/houou/race/ 。旧4ページ（`houou_*.html`）は変わっていないこと
  2. 公開の issue は #540。navbar の項目の名前・並び、`?division=` の転送を 301 にするか、告知の有無は #540 で決める
  3. 次の指示: 特別昇級の演出（紙吹雪を付け、飛び級の段数 2〜9段に応じて豪勢にする）
  4. 配信対象のファイル数は 2,457（上限 20,000 の約 12%。うち `houou/` 747）。15,000 を超えていない
- 未確認の項目: 本番のブラウザでの見え方（平野さん）。`x-robots-tag` の応答ヘッダは無く、noindex は meta だけで効かせている
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj efa62293）: https://github.com/retroeater/mj-logs/tree/main/guide/efa62293

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/efa62293/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/efa62293.md
