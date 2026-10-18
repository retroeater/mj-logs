# CHAT-1019-OUK-15

- 着手日時: 2026-10-19
- 対象issue: #591
- ブランチ: work/1019-ouk15（origin/cloudflare から作成）
- 着手時HEAD: 8c9ed13cb

## 指示

【Claude作成】Claude Code 向け指示：女流桜花（ouka/）を公開する形にする（navbar・白地のナビ・旧3ページの 301・noindex 解除・jpml_pros の列）。プレビューで止まる Chat-Ref: CHAT-1019-OUK-15 マージ: 判断待ちで止まる（公開は本番の navbar・転送・jpml_pros を変えるため、平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk15 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-ouk15 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて、未マージの work/1017-lvp（放送対局）が origin/cloudflare に入っているかを確かめてログに書く（入っていなくても止まらない。下の「前提」のとおり進める）。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
#591（女流桜花の公開）。ouka/ は CHAT-1018-OUK-12 で cloudflare に入り、未公開（noindex・navbar に無い）のまま。houou/ の公開（#540）と同じ形で公開に切り替える。プレビューで止まる。
決定（平野さん）

* 2026-10-17: navbar の「女流桜花」を `/ouka/` への1段にする
* 2026-10-19: ouka/ にも houou/ と同じ白地のナビ（`navbar.js` の `.mj-nav-new`）を当てる。公開の navbar の変更と一緒に入れる
* 2026-10-19（#591 の事前の質問への回答）:
   * Q1: 旧3ページはページごとに対応する所へ 301。`ouka_results.html` → `ouka/players/`（`?name=` を引き継ぐ）、`ouka_ranking.html` → `ouka/ranking/total/`、`ouka_leagues.html` → `ouka/players/` のリーグのタブ
   * Q2: jpml_pros（連盟 > プロ）の桜花の3列を1列「桜花<最新の期>期」（今は「桜花21期」）にまとめ、個人成績の期別へのリンクにする。同時に「鳳凰」の見出しを「鳳凰<最新の期>」（今は「鳳凰43後」）にする（今期出場していない人は表示されないため）。見出しの期は、期が進むたびに最新の期へ自動で変わる
   * Q3: 公開の指示は今作り、work/1017-lvp が未マージなら `navbar.js` の衝突を解いて進める
   * Q4: #591 と jwrc/ の公開（#599）は別々に行う。先に公開した方が navbar を直し、後の方はそれを取り込む
   * Q5: 公開後に X の告知文の下書きを作る（チャット側で作る。この指示では作らない）
* 消した部門のページ（`ouka/ranking/promotion-streak/`）には転送を足さない（2026-10-18）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 手本: houou/ の公開（#540）の指示とログ、`docs/new-page-checklist.md`（段2 まで）、`docs/notes/redirects.md`、`docs/decisions/page-release.md`。houou/ と違うやり方をする所は理由をログに書く
* navbar: 「女流桜花」の項目（今は旧3ページへの段か）を `/ouka/` への1段にする。`navbar.js` の新しいナビを当てる判定に `/ouka/` を足す。work/1017-lvp が未マージで同じ分岐を変えていても、こちらは origin/cloudflare の形に足すだけにする（LVP 側が後で取り込むときに衝突する所を、ログの報告に「LVP への引き継ぎ」として引用して書く）。jwrc/ の項目と判定には触らない（#599 で行う。後で取り込む WRC 側のために、変えた行をログの報告に「WRC への引き継ぎ」として書く）
* 301: `_redirects` に3行。`?name=` の引き継ぎは houou/ の旧ページの転送と同じやり方（Cloudflare の転送はクエリを引き継ぐか、を `docs/notes/redirects.md` で確かめる）。リーグのタブは `ouka/players/` の既定のタブの指定のしかた（例: `?view=league`）を実物から決める。旧ページのファイル・生成（`generate_ouka_leagues.py` など）の扱いは houou/ の前例のとおりにする。転送の一覧（`docs/notes/redirects.md`）を直す
* 公開の切り替え: ouka/ の全ページの noindex を外す、サイトマップ・`llms.txt` に載せる、`docs/new-page-checklist.md` の段2 の項目を済ませる（houou/ の公開と同じ範囲）。トップのページ一覧などに女流桜花の入口があれば、旧ページから ouka/ に替える（サイト内の旧3ページへのリンクを検索して全部替える）
* jpml_pros: 桜花の列を1列にし、値は名前の横のチップと同じ決め方（進行中の期のリーグ、AMP と同じ）、リンク先は `ouka/players/?name=<今の登録名>` の期別。今期に出ていない人は空欄。「鳳凰」の見出しも同じく最新の期を付ける。見出しの期は生成のときにデータから決める（手で書かない）。鳳凰の列の値・リンクは変えない
* 質問欄: ouka/ の質問欄は公開とともに本番で使われる。Worker の1日の上限（鳳凰戦と共有）に変更は要らない見込み。変える所があればログに書く
* `.github/workflows/` は変えない

手順

1. 作る: navbar・白地のナビ・301・noindex 解除・サイトマップ・`llms.txt`・サイト内リンク・jpml_pros の列を直す。テストを合わせて足す（jpml_pros の見出しの期がデータから決まること、転送の3行、noindex が無いこと）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す
2. 確かめる: `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、生成物の差分が「ouka/ の noindex を外した行」「jpml_pros の列」「navbar を読み込むページの変化（あれば）」「サイトマップ・`llms.txt`」だけであることを確かめ、種類ごとに件数と例を書く。houou/ の生成物は差分0。375px・1280px で、navbar（女流桜花の1段、ouka/ の白地のナビ、houou/ と他ページのナビが変わらないこと）、jpml_pros（桜花・鳳凰の列の見出しと値・リンク）を headless Chrome で見て書く。転送3行は、プレビューで確かめられるなら確かめる（できなければマージ後に確かめる旨を書く）
3. 記録: プレビュー（確認用 URL）を出す。#591 に進み具合を1件コメントする（末尾に `Chat-Ref: CHAT-1019-OUK-15`）。`docs/notes/ouka-top.md`・`docs/decisions/houou.md`（または page-release.md）を今の形に直す

止まる条件

* houou/ の生成物に差分が出て直しで消せない。手順2の種類以外の差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチ（work/1017-lvp を除く）が、変えるファイルの同じ行・同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、新しく仮置きしたこと（リーグのタブの指定のしかた、旧ページのファイルの扱い、jpml_pros の列の値の細部など）を、(1) 見た目の値 と (2) 見ただけでは判断しにくいもの に分けて書く。「LVP への引き継ぎ」「WRC への引き継ぎ」を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（ouka/ のトップ・navbar が分かるページ・jpml_pros）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-15 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1017-lvp は origin/cloudflare に入っていない（未マージ）。前提のとおり `navbar.js` は origin/cloudflare の形に足すだけにした
- 未マージの work/ ブランチのうち、変えるファイルに触るのは work/1017-lvp の `navbar.js`・`style.css` だけ。`navbar.js` は同じ if 連鎖の別の分岐（最強戦の後に「番組」）、`style.css` は最強戦・動画の節で、どちらも今回の行と別。`git merge-tree` で今回の先頭と work/1017-lvp を合わせると `navbar.js` は衝突しない（衝突するのは `docs/decisions/live.md`・`live/` の3ファイルで、origin/cloudflare と work/1017-lvp の間でも同じく衝突する。今回の変更とは無関係）

### 1. 作る

- `navbar.js`: 「女流桜花」の段（旧3ページ）を `/ouka/` への1段にした（鳳凰戦と同じ形）。新しいナビを当てる判定に `/ouka/` を鳳凰戦の次に足した。jwrc/ の項目と判定には触れていない
- 白地のナビ・noindex: `Contest` に `leagues_moved` を足し、`OUKA` の `noindex=True` を外して `leagues_moved=True` にした（鳳凰戦も `leagues_moved=True`。旧データ・旧入口の後始末の2つは鳳凰戦だけのまま）
- 301: `_redirects` に3行（`/ouka_ranking.html` → `/ouka/ranking/total/`、`/ouka_leagues.html` → `/ouka/leagues/`、`/ouka_results.html` → `/ouka/players/`）。Cloudflare の `_redirects` は転送先にクエリが無ければ元のクエリを引き継ぐ（`docs/notes/redirects.md` の houou/ の行と同じ）ので、`?name=` は残る
- リーグのタブ: houou/ の前例（`houou_leagues.html` → `houou/leagues/` → JS で `players/?view=league`）に合わせ、`ouka/leagues/`（移すだけ、noindex、サイトマップ外）を作った。`_redirects` で直接 `ouka/players/?view=league` へ送ると、転送先にクエリがあるため `?name=` が落ちる。`ouka/players/` の既定のタブの指定は `?view=league`（`docs/new-page-checklist.md` の URL パラメータの規約）
- 旧ページのファイル・生成（`ouka_*.html`・`generate_ouka_leagues.py` など）は houou/ と同じく残した（後始末は houou/ の #582 と同じ扱い）
- サイトマップ: `sitemap-pages.xml` の旧3ページを ouka/ の11ページ（トップ・個人成績・ランキング8部門・順位変動）に替え、冒頭のコメントに除外（`ouka/leagues/`・旧3ページ）を足した。lastmod は `update_sitemap_lastmod.py --from-git` が出す値と同じ（書き換え0件）。`xml.etree.ElementTree` で parse できる
- `llms.txt`: 「女流桜花」の節を ouka/ の4行（鳳凰戦の節と同じ形）にした
- サイト内リンク: 旧3ページへのリンクは `jpml_pros.html`（生成）だけで、今回の列の変更で無くなった。旧3ページ同士のリンクは旧ページのまま（転送される）
- jpml_pros: 桜花の3列（出場・21期・最高）を「桜花<br>21期」の1列にし、値は `current_league_table` を `using(OUKA)` で呼んだ現在のリーグ（ouka/ の名前の面のチップと同じ）、リンクは `./ouka/players/?name=<名前>`。鳳凰の見出しは「鳳凰<br>43後」。見出しの期は `pro_stats.periods()`（データの最新の期）から `header_texts()` で入れる。表の幅は 766px → 654px（`style.css` の `--mj-pros-table-width`）
- テスト: navbar の1段・`/ouka/` の判定、ouka/ に noindex が無く `leagues/` だけにあること、転送の3行と転送先のファイル、`promotion-streak/` に転送が無いこと、jpml_pros の桜花の列（値・リンク・空欄）、見出しの期がデータで決まること、書き出した jpml_pros の桜花の列が `ouka/ask-players.json` のチップと一致すること。前のコード（origin/cloudflare）では新しいテストはどれも落ちる（確かめた）
- `python3 -m unittest discover -s scripts/tests` 1,424件 OK、`node --test 'workers/ask/test/*.test.mjs'` 47件 pass
- 文書: `docs/notes/ouka-top.md`・`static-generation.md`（「ページの一覧」の件数 12→13）・`redirects.md`（棚卸しの対象に3行、JS の移動に1行）・`league-pages-compare.md`（公開・ナビの行）・`handover.md`（参照の表の「未公開」）・生成スクリプトの docstring の「未公開」を直した
- 質問欄: Worker の変更は要らない（1日の上限は鳳凰戦と共有のまま）
- `regenerate.py --check ouka_pages jpml_pros` は通る

### 2. 確かめる

- `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローン（8c9ed13cb）で同時に回した（どちらも exit 0）。`diff -r` の差は次だけ:
  - ouka/ の noindex を外した行: 11ページ（トップ・個人成績・ランキング8・順位変動）で `<meta name="robots" content="noindex">` の1行ずつ。ほかの差は無い。`ouka/leagues/index.html` は新しく増えた
  - jpml_pros の列: `jpml_pros.html` の見出し1行と1,098行（全行）。行ごとに桜花の列を除いて比べると差は0、表の外の差も0
  - navbar を読み込むページの変化: 生成物の差は無い（`navbar.js` だけ）
  - サイトマップ・`llms.txt`: `sitemap-pages.xml`・`llms.txt`
  - ほかは手で直したファイル（`_redirects`・`navbar.js`・`style.css`）だけ。houou/ の生成物は差分0
- headless Chrome（手元）375px・1280px:
  - navbar: どのページでも「女流桜花」は `/ouka/` への1段（段の印なし）。ouka/ のトップ・個人成績・ランキング・順位変動は白地のナビ（`.mj-nav-new`）で「女流桜花」に印。houou/・houou/players/ は白地のナビで「鳳凰戦」に印、saikyo/ は「最強戦」に印（変える前と同じ）。jpml_pros・index は前と同じ（濃色のナビ／ナビ無し）。JS のエラー 0
  - ouka/ のページに robots の meta が無い。`ouka/leagues/?name=二階堂亜樹` は `ouka/players/?name=二階堂亜樹&view=league` へ移る
  - jpml_pros: 見出しは「鳳凰<br>43後」「桜花<br>21期」。二階堂亜樹の行は鳳凰「A2 → ./houou/players/?name=二階堂亜樹」・桜花「A → ./ouka/players/?name=二階堂亜樹」。表の幅 654px。桜花の検索欄（placeholder「桜花21期」）で C1 は24行。桜花の列の並べ替えは変える前と同じ順（降順で 桜花・C3…）
  - 横のはみ出しは jpml_pros の 375px だけで、変える前も同じ（表が画面より広い）

### 3. 記録

- 成果物のコミット 50f5a1fe8 を push。Workers Builds の mj は success（プレビューあり、URL は最終報告）
- プレビュー（50f5a1fe8 の版）で確かめた転送: `/ouka_results.html?name=二階堂亜樹` → 301 `/ouka/players/?name=二階堂亜樹`、`/ouka_ranking.html` → 301 `/ouka/ranking/total/`、`/ouka_leagues.html?name=二階堂亜樹` → 301 `/ouka/leagues/?name=二階堂亜樹` → JS で `/ouka/players/?name=二階堂亜樹&view=league`。`/ouka/ranking/promotion-streak/` は 404 のまま。houou/ の旧ページの転送は変わらない
- プレビューの ouka/ の HTML に robots の meta は無い（`leagues/` だけにある）。応答ヘッダの `x-robots-tag: noindex` はプレビューの別名だけに付くもの（houou/ にも付く。`_headers`）。本番で無いことはマージ後に確かめる
- プレビューでの headless Chrome（375px）の navbar の見え方は手元と同じ。jpml_pros の見出しは「鳳凰<br>43後」「桜花<br>21期」
- #591 に進み具合を1件コメントした。`docs/decisions/houou.md` に 2026-10-19（OUK-15）の節を足した
- 途中で、経過を書いた Edit が `## 報告` の見出しを消していたので書き直した（`## 指示` 欄は変わっていない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-ouk15
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk15/docs/logs/CHAT-1019-OUK-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk15
- 確認用URL: プレビューあり（URL は最終報告）。ouka/ のトップ・ouka/players/・jpml_pros.html・houou/ を確かめた。この版で見えるはずの印: navbar の「女流桜花」が段（▼）でなく1段で、ouka/ のナビが白地。jpml_pros の見出しが「鳳凰 43後」「桜花 21期」
- マージ: 未（平野さんの判断待ち。プレビューで確かめてから別のマージの指示で）
- issue: #591 にコメント
- 判断が必要なこと:
  - マージしてよいか（navbar・転送・jpml_pros が本番で変わる）
  - 仮置き (1) 見た目の値: jpml_pros の桜花の検索欄の placeholder は「桜花21期」のまま、鳳凰の検索欄は「鳳凰」のまま（見出しの期は付けていない）。桜花の列の値の字は ouka/ のチップと同じ（「桜花」「A」「B」「C1」〜「C3」）。jpml_pros の表の幅は 654px（10列）
  - 仮置き (2) 見ただけでは判断しにくいもの: リーグのタブへの転送は houou/ と同じ2段（`ouka_leagues.html` → 301 `ouka/leagues/` → JS で `ouka/players/?view=league`。`_redirects` で直接 `?view=league` へ送ると `?name=` が落ちるため）。`ouka/leagues/` は noindex・サイトマップ外。旧3ページのファイルと生成（`generate_ouka_leagues.py` など）は houou/ と同じく残した（後始末を houou/ の #582 と同じ扱いにするか）。jpml_pros の桜花の列は女流桜花の進行中の期に出ていない人を空欄にし、桜花Ampai へのリンクは付けない（鳳凰と同じく個人成績の期別から行く）。サイトマップの新しい行の lastmod は `--from-git` の値（2026-10-18）
  - LVP への引き継ぎ: `navbar.js` の新しいナビの判定の if 連鎖で、鳳凰戦の分岐の直後に `} else if (location.pathname.indexOf('/ouka/') === 0) {` と `newNavCurrent = '女流桜花' // 鳳凰戦と同じ白地のナビ(2026-10-19、#591)` を足した。work/1017-lvp の「番組」の分岐（最強戦の後）とは行が離れており、`git merge-tree` では衝突しない。取り込むと連鎖は 鳳凰戦・女流桜花・最強戦・番組 の順になる。ほかに「女流桜花」の段を `<li class="nav-item"><a class="nav-link" href="/ouka/">女流桜花<br>Ouka</a></li>` の1行に替えた
  - WRC への引き継ぎ: `navbar.js` で今回変えた行は、判定の if 連鎖の女流桜花の分岐（鳳凰戦の直後）、「女流桜花」の項目の1行、上の説明のコメント（「女流桜花の ouka/ も同じ(#591)。」）。jwrc/ の公開（#599）では、女流桜花の分岐の後に `/jwrc/` の分岐を足し、WRC の段を1段にする。`scripts/tests/test_navbar.py` に `test_ouka_is_single_item_to_top` を足した。`Contest.leagues_moved` は jwrc/ では要らない（リーグのタブが無い）。公開は `JWRC` の `noindex=True` を外す
- 未確認の項目:
  - 本番での確かめ（マージ後）: ouka/ が 200 で robots の meta・`x-robots-tag` が無い、本番の `navbar.js`・サイトマップ・`llms.txt` に ouka/ がある、旧3ページの 301
  - 平野さんの実機での見え方（セッションから確かめられるのはプレビューの HTML と headless Chrome まで）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f18afda）: https://github.com/retroeater/mj-logs/tree/main/guide/0f18afda

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4afe5cd1.md
