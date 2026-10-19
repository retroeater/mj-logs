# CHAT-1019-WRR-06

- 着手日時: 2026-10-19
- 対象issue: #611・#366
- ブランチ: work/1019-wrr06
- 着手時HEAD: b48ac4d18

## 指示

【Claude作成】Claude Code 向け指示：WRC-Rリーグ（wrcr/）を公開する（navbar に「WRC-R」の1段の項目、noindex を外す、サイトマップ・llms.txt）。告知は別の指示 Chat-Ref: CHAT-1019-WRR-06 マージ: 下の確かめがすべて通れば cloudflare にマージする（2026-10-19、平野さんが実機で確かめて「公開の作業を進めてよい」） 貼る時機: いつでも（CHAT-1019-WRR-05 は完了・cloudflare にマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrr06 の作成と push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-wrr06 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-wrr06 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。マージの前に `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase・force push はしない）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git log origin/cloudflare --grep="CHAT-1019-WRR-05" --oneline` が空なら止まる。

目的
#611（wrcr/ の公開）。WRR-05 で未公開のまま本番に入れた wrcr/ を、jwrc/ と同じ形で公開する。告知（X の投稿と告知動画）は別の指示で行う。
決定（2026-10-19、平野さん）

* wrcr/ を公開する。平野さんが実機で確かめて OK
* navbar に「WRC-R」を独立の1段の項目として足す（「WRC」の隣。2026-10-19 の WRR-02 の事前の質問への回答）。wrcr/ のナビは白地（jwrc/ と同じ）
* 告知はする（2026-10-19 か 10-20）。WRC-R のページは作り直しでなく新しく作ったもの（告知の文は「リニューアル」でなく「公開」）。告知は別の指示

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 手順は CHAT-1019-WRC-12（jwrc/ の公開）の形に倣う。違いは、旧ページが無いので 301 が無いこと、navbar は「WRC」を変えずに「WRC-R」の項目を足すこと
* navbar: `navbar.js` の判定の if 連鎖に `/wrcr/` の分岐を足し（白地のナビ）、「WRC」の後に「WRC-R」の1段の項目（`/wrcr/` へ）を置く。項目名は「WRC-R」。`scripts/tests/test_navbar.py` の jwrc/ のテストに倣うテストを足す。項目が1つ増えて、スマホ（360px・375px）と PC でナビが折り返す・はみ出すときは、止まらずに仮置きの直し方で収め、報告に書く
* `WRCR` の `noindex=True` を外す。`sitemap-pages.xml` に wrcr/ の各ページを足す（lastmod は `--from-git` の値）。`llms.txt` に WRC-R の行を足す（jwrc/ の行の書き方に倣う）
* `docs/notes/static-generation.md`「ページの一覧」、`docs/notes/wrcr-top.md`、`league-pages-compare.md` を公開済みの形に直す
* jpml_pros・`title/` のページは変えない（`title/jwrc-r/` から wrcr/ へのリンクは別の課題。要るなら報告に書く）
* issue: マージと本番の確かめが済んだら #611 をクローズする（「告知は別の指示」と書く）。#366 の目的（WRC-R の成績表を載せる）も済むので、残りが無ければ #366 もクローズする（第8期の入力は完了後に行うことを書く。残りがあれば書いて開けたままにする）

手順

1. 直し: navbar（分岐・1段・テスト）、`WRCR` の noindex を外す、サイトマップ・`llms.txt`、文書。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す。`python3 scripts/regenerate.py all` の後、差分が wrcr/ の noindex を外した行・navbar・サイトマップ・`llms.txt`・文書だけで、houou/・ouka/・jwrc/・jpml_pros の生成物が差分0であることを確かめる（種類ごとの件数と例をログに書く）。上の「決定」を docs/decisions（jwrc/ の公開の決定と同じ置き場所）に足す
2. マージ: CLAUDE.md「ブランチ運用」節のとおり `git push origin work/1019-wrr06:cloudflare` でマージし、Workers Builds（mj・mj-ask）の成否を確かめる
3. 本番の確かめ（headless Chrome）:
   * wrcr/ の各ページ（トップ・個人成績・ランキング9部門・順位変動）が 200 で、robots の meta・`x-robots-tag` が無い
   * 本番の `navbar.js` で「WRC-R」が `/wrcr/` への1段で「WRC」の隣にあり、wrcr/ のナビが白地。houou/・ouka/・jwrc/ と他のページのナビが「WRC-R」の項目のほかは変わらない。360px・375px・PC 幅でナビが折り返さない（はみ出さない）
   * サイトマップ・`llms.txt` に wrcr/ がある
   * wrcr/ の質問欄に1問聞いて答えが返る（例「通過回数がいちばん多いのは？」） それぞれ結果を書く。#611 をクローズし、#366 は前提のとおり

止まる条件

* 0章の確かめに当たる
* houou/・ouka/・jwrc/・jpml_pros の生成物に差分が出て直しで消せない。手順1の種類以外の差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている（同じファイルの別の箇所なら止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後に本番の houou/・ouka/・jwrc/ や navbar が壊れた（ページが出ない・JS のエラー・ナビが出ない）ときは、CLAUDE.md の戻し方の節のとおりに戻して止まる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の最初の方に「WRC-Rリーグ（wrcr/）を公開した（マージのコミット <SHA>）」と書く。「判断が必要なこと」に、本番の確かめで合わなかった所（あれば）、navbar の仮置き（あれば）、告知は別の指示である旨を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-06 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件。`git log origin/cloudflare --grep="CHAT-1019-WRR-05"` は空でない（bd1da1259 など）
- 作業ブランチ: work/1019-wrr06 はローカル・リモートとも無し → `git checkout -b work/1019-wrr06 origin/cloudflare`（b48ac4d18）
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1: 直し

- navbar（`navbar.js`）: 「WRC」の後に `<li class="nav-item"><a class="nav-link" href="/wrcr/" data-short-label="WRC-R">JPML<br>WRC-R</a></li>` を足した（黒地のナビは「JPML / WRC-R」の2行、白地のナビは `data-short-label` の「WRC-R」。jwrc/ の「WRC」と同じ形）。白地のナビの判定に `/wrcr/` の分岐（`newNavCurrent = 'WRC-R'`）
  - テスト: `test_navbar.py` に「WRC-R は WRC の隣・最強戦の前、`/wrcr/` の分岐がある」を1件。既存の `test_wrc_item_label` は「`data-short-label` は1つ」を前提にしていたので、WRC-R を含めて2つに直した
  - ローカル（静的サーバーと headless Chrome）で、wrcr/・jwrc/・houou/・ouka/・saikyo/（白地）と jpml_pros・title/（黒地）を 360・375・992・1280・1920px で開いた。992px 以上は9項目が1行（折り返し・はみ出しなし、白地のナビの高さ 61px・黒地 80px は前と同じ）、360・375px は折りたたみ（ハンバーガー）。wrcr/ で「WRC-R」に印（`aria-current`）。JS のエラーなし。jpml_pros の 360・375px の横はみ出しは本番の今の版でも同じ（表の幅。ナビによるものでない）。仮置きの直しは要らなかった
- `WRCR` の `noindex=True` を外した（`generate_houou_pages.py`）。テスト（`test_wrcr_pages.py`）を「noindex が無い」に直した
- `sitemap-pages.xml` に wrcr/ の12ページ（トップ・個人成績・ランキング9部門・順位変動）を jwrc/ の後に足し、冒頭の説明に wrcr/（#611）。`update_sitemap_lastmod.py --from-git` の後も lastmod は 2026-10-19 のまま
- `llms.txt` に「## WRC-Rリーグ」の節（jwrc/ の4行と同じ書き方）
- 文書: wrcr-top.md（公開の形）、static-generation.md「ページの一覧」、league-pages-compare.md（公開・noindex の欄）、ask-worker.md・redirects.md・handover.md の「未公開」を直した
- `python3 -m unittest discover -s scripts/tests`: 1449件 OK。`node --test 'workers/ask/test/*.test.mjs'`: 73件 pass
- `python3 scripts/regenerate.py all`（終了コード 0）: 生成物の差分は wrcr/ の12ページの `<meta name="robots" content="noindex">` を消した12行だけ（例 `wrcr/index.html`）。houou/・ouka/・jwrc/・jpml_pros・ほかの生成物は差分 0。手で直したのは navbar.js・sitemap-pages.xml・llms.txt・テスト2・生成の設定2・文書
- 未マージの work/ ブランチ: `work/1017-lvp` が navbar.js の白地のナビの判定の if 連鎖に `mj-live-n-page` の分岐を足している（最強戦の分岐の後）。この作業の `/wrcr/` の分岐（jwrc/ の分岐の後）とは別の箇所で、同じ行ではない
- 決定を docs/decisions/houou.md に「## 2026-10-19（CHAT-1019-WRR-06）」として足した（jwrc/ の公開〈WRC-12〉と同じファイル）
- コミット: 直し（navbar・設定・テスト・サイトマップ・llms.txt・文書）と生成物（wrcr/）

## 報告

- 状態: 作業中
- ブランチ: work/1019-wrr06
- ログ: https://github.com/retroeater/mj/blob/work/1019-wrr06/docs/logs/CHAT-1019-WRR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-wrr06
- 確認用URL: なし
- マージ: 未
- issue: #611・#366
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 5f8dc791）: https://github.com/retroeater/mj-logs/tree/main/guide/5f8dc791

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/5f8dc791/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
