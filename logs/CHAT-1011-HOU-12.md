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

## 報告

- 状態: 作業中
- ブランチ: work/1008-hou
- ログ: https://github.com/retroeater/mj/blob/work/1008-hou/docs/logs/CHAT-1011-HOU-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-hou
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 396a7262）: https://github.com/retroeater/mj-logs/tree/main/guide/396a7262

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/396a7262/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/21efaaec.md
