# CHAT-1013-WHS-23

- 着手日時: 2026-10-13
- 対象issue: 起票する（経過に書く）
- ブランチ: work/1013-whs-url
- 着手時HEAD: 346c65b1

## 指示

【Claude作成】Claude Code 向け指示：帰り道の一覧の URL を /video_wayhome.html から /wayhome/ に移す（issue を起票。旧 URL は 301 で転送）。確かめが通れば cloudflare へマージする Chat-Ref: CHAT-1013-WHS-23 マージ: 承認済み（チャットで、2026-10-13。下の確かめ〈旧 URL が 301、新 URL が 200、サイト内に旧 URL へのリンクが残らない〉が通れば、プレビューを見ずにマージしてよい。条件は「止まる条件」のとおり） 貼る時機: CHAT-1013-WHS-22 のマージの後（同じ生成スクリプトを触るため） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1013-whs-url を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-whs-url origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1013-WHS-22 が cloudflare にマージ済みであることを確かめ、済んでいなければ止まる。

目的
各話は `/wayhome/<動画ID>.html` にあるのに、一覧だけが `/video_wayhome.html` にある。一覧を `/wayhome/`（`wayhome/index.html`）に移し、帰り道を1つのディレクトリにまとめる。
決定（2026-10-13、平野さん）

* 一覧の URL を `/wayhome/` にする
* 旧 URL `/video_wayhome.html` は 301 で `/wayhome/` へ転送する（`/saikyo_results.html` → `/saikyo/`、`/jpml_titles.html` → `/title/` と同じ扱い）
* issue を起票して扱う。確かめが通れば、プレビューを見ずにマージしてよい

前提（チャット側。平野さんの決定ではない）

* 前例: `_redirects` に `/saikyo /saikyo/ 301`・`/saikyo/ /saikyo/index.html 200`・`/saikyo_results.html /saikyo/ 301`（`title/`・`live/` も同じ形）。帰り道も `/wayhome /wayhome/ 301`・`/wayhome/ /wayhome/index.html 200`・`/video_wayhome.html /wayhome/ 301` の3行になる見込み。`_redirects` 先頭の `/ /index.html 200` は消さない（CLAUDE.md「禁止事項」）。配信設定の仕組み（`html_handling`・canonical）は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」
* 移すときに直すと見込まれる所（すべて洗い出してログに一覧を書く）: 一覧の生成先（`scripts/generate_video_wayhome.py` の出力パス。`regenerate.py` のページ名 `video_wayhome` は変えなくてよいかを判断する）、一覧の中の相対パス（CSS・JS・画像・各話へのリンク）、各話の「一覧に戻る」・シリーズ名のリンク（今は `../video_wayhome.html`）、canonical・og:url・JSON-LD（`BreadcrumbList`・`ItemList` などの URL）、`navbar.js`・`llms.txt`・サイトマップ（`sitemap*.xml`。lastmod は手で書かず `scripts/update_sitemap_lastmod.py --from-git` で導出する。ファイルの移動で git の履歴がどう扱われるかを確かめる）、`index.html` などほかのページからのリンク、`title/` の期のページの帰り道の節（各話へのリンクなら変わらない見込み）、`.assetsignore`、ワークフロー（`regenerate-page.yml` で一覧の HTML と OGP 画像を同じコミットに入れる所など）、テスト、文書（`docs/notes/video-wayhome.md`・`docs/notes/static-generation.md`「ページの一覧」・`docs/notes/cloudflare.md`・`docs/decisions/` など）。ページの移行の手順は `docs/notes/static-generation.md`・`docs/new-page-checklist.md` に従う（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）
* 新しく手書きの HTML を足すわけではないが、`docs/notes/static-generation.md`「navbar.js と検索欄」の href はルート相対（#162）の規則を確かめる
* 外部ドメインは増やさない。ほかのページの生成物は、URL の書き換え以外は変わらない見込み

手順

1. 確かめる・起票する: 同じ目的の issue（REST の `repos/.../issues?state=all` を「video_wayhome」「wayhome/」「URL」で絞る）と未マージのブランチを確かめ、重なれば止まる。issue「帰り道の一覧の URL を /wayhome/ に移す」を起票する（ラベルは #553 に合わせる。旧 URL の 301 と、上の「前提」の直す所の一覧を本文に）。着手中のコメントを残す。旧 URL を参照している所をすべて洗い出してログに書く（`git grep -n "video_wayhome"`。生成物・スクリプト・文書・ワークフローを分けて）
2. 移す: 一覧を `wayhome/index.html` に生成するようにし、旧 `video_wayhome.html` を消す。`_redirects` に上の3行を足す。洗い出した所を直す。テストを足す（一覧の出力パス、`_redirects` の3行、サイト内の生成物に `video_wayhome.html` へのリンクが残らないこと）。`python3 -m unittest discover -s scripts/tests` が通ること。`python3 scripts/regenerate.py all` で全ページを生成し直し、差が URL の書き換えで説明できるものだけであること
3. 確かめる: `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state`（CLAUDE.md「禁止事項」のとおりの起動）または Workers Builds のプレビューで、`/video_wayhome.html` → 301 → `/wayhome/`、`/wayhome` → 301 → `/wayhome/`、`/wayhome/` が 200 で一覧が出ること、各話の「一覧に戻る」が `/wayhome/` に行くこと、一覧の CSS・画像・各話へのリンクが切れていないこと（リンク切れの検査を一覧と各話1枚にかける）
4. マージして確かめる: 決定を `docs/decisions/wayhome.md` に足す。CLAUDE.md「ブランチ運用」のとおり cloudflare へ push し、push を契機の再生成と Workers Builds の check-run を待つ（それぞれ上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）。本番で手順3と同じことを確かめる（`curl` が通らなければ WebFetch 相当の手段で読む。読めなければ「未確認の項目」へ）。issue に結果をコメントして閉じる（「状況:」ラベルを外す）。作業ブランチを片付ける

止まる条件

* WHS-22 が cloudflare にマージされていない
* 手順1で、同じ目的の issue・未マージのブランチと重なる
* 旧 URL の参照が、生成スクリプトで直せない所（外部のサービス・ほかのリポジトリなど）にある（一覧を書いて止まる。転送で足りるなら止まらず報告に書く）
* テストが落ちる。帰り道以外の生成物に、URL の書き換えで説明できない差がある
* 手順3で、301・200・リンクのどれかが見込みと違う
* 取り込みで生成物でない文書・コード・ワークフローが衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号、直した所の一覧を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-WHS-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-WHS-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1013-WHS-23"` は0件
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行は揃っている（指示文は改行が詰まった形で貼られたため、そのまま写した）
- WHS-22 のマージ（0f532f4d）は `origin/cloudflare` の祖先 → 済み
- 作業ブランチ: `work/1013-whs-url` はローカル・リモートとも無し → `git checkout -b work/1013-whs-url origin/cloudflare`（346c65b1）

### 手順1 確かめ・起票

- 同じ目的の issue: 全 issue を「video_wayhome / wayhome/」かつ「URL / 移 / 301 / index.html」で絞った。一覧の URL を移す issue は無い
- 未マージのブランチ: `work/1012-dsn` が `navbar.js` を変えているが、鳳凰戦向けの追記（末尾）だけで目的も重ならない。ほかは関係なし
- 起票: #559「帰り道の一覧の URL を /wayhome/ に移す」（ラベル `分野: SEO/AIO`・`対象: video_wayhome`）。着手中のコメントを残した
- 旧 URL（`video_wayhome`）を参照している所（`git grep -n "video_wayhome"`、`docs/logs` を除く）:

| 区分 | 所 | 扱い |
| --- | --- | --- |
| 生成物 | `video_wayhome.html`（一覧そのもの）、`wayhome/*.html` 42枚（「一覧に戻る」・シリーズ名 `../video_wayhome.html`、`BreadcrumbList` の `https://ryoei.pro/video_wayhome.html`） | 一覧は `wayhome/index.html` に移し旧ファイルを消した。各話は `./` と `https://ryoei.pro/wayhome/` |
| 配信するファイル | `navbar.js`（`/video_wayhome.html`）、`llms.txt`（一覧の URL と説明）、`sitemap-pages.xml`（一覧の URL） | `navbar.js` は `/wayhome/`、`llms.txt` は `https://ryoei.pro/wayhome/`、サイトマップは `sitemap-pages.xml` から外し `sitemap-wayhome.xml` の先頭へ（各話の生成が書く） |
| スクリプト | `generate_video_wayhome.py`（出力パス・og:url・JS・相対パス）、`generate_wayhome_episodes.py`（戻るリンク・パンくず）、`regenerate.py`（出力パス）、`apply_page_meta.py`（`PAGES` のキー）、`check_wayhome.py`（常設 issue の本文の文言） | 直した。`regenerate.py` のページ名 `video_wayhome` と `video_wayhome.js` は変えない（`--changed` の判定 `<ページ名>.js` がそのまま効く） |
| ワークフロー | `regenerate-page.yml`（一覧の OGP 画像を同じコミットに入れる条件 ` video_wayhome.html `、コメント） | ` wayhome/index.html ` に直した。298行目のラベル `対象: video_wayhome` は issue のラベル名なので変えない |
| テスト | `test_wayhome_no_share.py`（一覧のパス）、`test_wayhome_links.py`（一覧のボタンの href） | 直した |
| 文書 | `docs/notes/video-wayhome.md`・`static-generation.md`（ページの一覧）・`docs/decisions/wayhome.md`・`README.md` ほか | 今の状態を書く所だけ直した。経緯の記録（lighthouse-baseline・new-site-design など）は当時のファイル名のまま |
| コメントだけ | `assets/*.js`・`style.css`・`scripts/lib/*.py` などの「video_wayhome.js と同じ」 | JS のファイル名なので変えない |

- 外部（GSC の書き出し `docs/gsc/*.csv` など）にある旧 URL は、301 で足りる

### 手順2 移す（551e53e7・生成 4c5b5611）

- `_redirects` の `/houou/` の後に3行: `/wayhome  /wayhome/  301`・`/wayhome/  /wayhome/index.html  200`・`/video_wayhome.html  /wayhome/  301`（先頭の `/  /index.html  200` は残る）
- 一覧（`wayhome/index.html`）: 共通アセット・JS・代替画像・期のページへのボタンは `../`、各話へは `<動画ID>.html`。og:url は `https://ryoei.pro/wayhome/`。旧 `video_wayhome.html` との差は、これらのパスと og:url だけ（中身を行ごとに比べた）
- 各話の生成がシートに無いファイルを消す処理で、一覧の `index.html` を消さないようにした（`LIST_FILENAME`。`regenerate.py all` は `video_wayhome` → `wayhome_episodes` の順に動くため、直さないと一覧を消していた）
- テスト: `scripts/tests/test_wayhome_url.py`（5件: 一覧の出力パス・og:url・再生成の出力パス・旧ファイルが無い、各話の生成が index.html を残す、サイトマップの先頭が一覧、`_redirects` の3行と先頭の1行、配信するファイルに旧 URL へのリンクが残らない）。直す前のコードでは出力パス・リンク・`_redirects` の検査が落ちる。全体 979件 OK
- `python3 scripts/regenerate.py all`: 作業ツリーに差なし（帰り道以外の生成物は変わらない）
- `regenerate-page.yml` を変えたので、作業ブランチで手動実行した: run 38344494366（`target_page: video_wayhome wayhome_episodes`）success。「変更をコミット・push」で ` wayhome/index.html ` の条件が当たり `git add -A -- img/ogp/wayhome` まで進み、差が無いので「変更なし」

### 手順3 確かめ（Workers Builds のプレビュー、551e53e7）

- `/video_wayhome.html` → 301 → `/wayhome/`、`/wayhome` → 301 → `/wayhome/`、`/wayhome/` 200、`/wayhome/index.html` 200、`/wayhome/Yjp_PKHvldw.html` 200
- リンク切れの検査（ページ内の href・src・data-fallback のうちサイト内のもの）: 一覧 50件・各話 15件、切れなし（一覧の1件は通信のタイムアウトで、取り直すと 200）。各話の「一覧に戻る」（`./`）は `/wayhome/` に解決する

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1013-whs-url
- ログ: https://github.com/retroeater/mj/blob/work/1013-whs-url/docs/logs/CHAT-1013-WHS-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1013-whs-url
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0637b1cc）: https://github.com/retroeater/mj-logs/tree/main/guide/0637b1cc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0637b1cc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b5bbbea3.md
