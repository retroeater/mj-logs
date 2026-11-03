# CHAT-1104-PRP-11

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-unpub
- 着手時HEAD: dbac236b1（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：pro/ のここまでの改善を、未公開（noindex）のまま本番に入れる。公開の段の変更は work/1101-prp-pub に残す。あわせて pro/ のトップのページの中の質問欄を外す Chat-Ref: CHAT-1104-PRP-11 マージ: 承認済み（チャットで。2026-11-04 に平野さんが「ここまでの変更内容を非公開のままで pro/ に反映して」と依頼し、下の線引き〈案A〉を単独の問いで選んだ。下の「止まる条件」に1つでも当たればマージしない） 貼る時機: いつでも（CHAT-1104-PRP-10 の完了報告〈判断待ち〉の後。新しいセッションを推奨〈別の作業ブランチで、公開の準備のブランチと混ぜないため〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-prp-unpub を使う（公開の準備の work/1101-prp-pub とは分ける）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-unpub origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。`navbar.js`・`assets/` の取り込みで TOP 系列（work/1104-top）の変更と衝突したら、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）。work/1101-prp-pub はこの指示では消さない・変えない（公開のマージで使う）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-10 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1104-PRP-11` を足して work/1101-prp-pub に push する（ログだけ。ほかは変えない）。

目的
未マージの work/1101-prp-pub（PRP-03〜10）のうち、未公開の pro/ の中だけで効く変更を先に本番へ入れ、毎朝の生成でも改善した pro/ が動くようにする。pro/ を外から見えるようにする変更（公開の段）は work/1101-prp-pub に残し、公開のマージで入れる。
決定（2026-11-04、平野さん）

* ここまでの変更を、未公開のまま pro/ に反映する（本番に入れる）。線引きは次のとおり（案A）
   * 入れる: 個別ページの直し（名前の面・☆/★・今後の予定・放送対局の見出しと「すべて（N件）」）、トップの絞り込みのシート（6つのタブ・複数選択・既定の「すべて」・お気に入り・`?all=1` の書き換え）、全在籍者の予定の JSON（`pro/plans.json`）、個別ページの題の形、並べ替えのボタンの大きさなど pro/ の中の見た目とメタ
   * 残す（work/1101-prp-pub に置いたまま、公開のマージで入れる）: noindex を外すこと・canonical・`sitemap-pro.xml`、navbar・トップページのサイドメニュー・`llms.txt` の向け替え、`jpml_pros.html` の 301（転送の Worker `workers/pros-redirect/`）と転送の集計の行、公開中のページから pro/ へのリンクの向け替え（順位変動の `playerUrl()` の4大会・title/ の期ページの決勝メンバー・`scripts/lib/pro_search.py`）、X の画像のリンク切れの検知の付け替え
* TOP 系列のチャットで決まったこと（2026-11-04、平野さん。TOP からの伝言による）: ページの中の上部の質問欄は全ページで廃止し、入口は共通ナビの右上の AI 検索のアイコン1つにする。pro/ のトップの質問欄は、PRP 系列が pro/ の作業の中で外す

前提（チャット側。平野さんの決定ではない）

* 線引きの「残す」に入れたもののうち、OGP の画像の置き場所の移動（K14、`img/ogp/pros/` → `img/ogp/pro/`）は `jpml_pros.html` の参照も変えるので、今回は入れない（公開のマージで入れる、チャット側の案）。pro/ の og:image は今の `img/ogp/pros/index-white.png` のまま
* pro/ のトップの質問欄を外すので、K8（質問欄の答えのカードの行き先を pro/ の個別ページに、PRP-04）は今回は入れない。work/1101-prp-pub の K8 の変更（`houou_ask.js` の `playerHref()` など）は公開のマージで要らなくなる見込み。`houou_ask.js` は5大会の共通の部品なので、pro/ のトップから読み込みを外すだけにし、部品そのものは変えない
* TOP の伝言によれば、TOP-12（work/1104-top、2026-11-04 時点で未マージ）は houou/ などの質問欄を消し、`generate_houou_pages.py` の `ASK_TEMPLATE` などと `houou_ask.*` は pro/ が使うので残している。pro/ の生成は各大会の `ask-players.json` からいまのリーグを読む（要確認）。外した後に、質問欄の部品と `houou_ask.*` を読むページが残るかを調べて書く（部品を消すのは TOP の後の指示）
* 作り方はチャット側の案: origin/cloudflare から作った work/1104-prp-unpub に、work/1101-prp-pub の該当の変更を移す（コミットを選んで移すか、差分のうち該当のファイル・関数だけを当てる）。どちらでもよく、移したもの・移さなかったものの一覧（ファイルと関数）を `## 経過` に書く。pro/ は Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. work/1104-prp-unpub に「入れる」の変更を移し、pro/ のトップの質問欄（`top_ask()` の欄と `houou_ask.js`・`houou_ask.css` の読み込み）を外す。テストを今の形に合わせる（公開の段のテスト〈noindex が無いこと・canonical・`sitemap-pro.xml` など〉は移さない）。`docs/notes/pro-pages.md` に、未公開のまま入れたものと、公開のマージで入れるもの（work/1101-prp-pub に残したもの）の一覧を書く
2. 確かめ（下の「止まる条件」の機械的な確かめを全部ここで行い、結果を `## 経過` に書く）: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。`regenerate-page.yml` の `check`（`target_page: pro_pages`）が止まらない。生成物の差分を種類ごとに数えた表。プレビュー（付かなければローカル）で、トップ（既定の「すべて」・シート・お気に入り）と `pro/sho.shiratori.23/` を 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。本番の `pro/`・`pro/sho.shiratori.23/` が 200 で noindex があり、新しい形（既定の「すべて」・☆）になったことを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。上の「決定」を docs/decisions/pros.md に足す。#641 にコメントする（未公開のまま入れたもの・公開のマージに残したもの・pro/ のトップの質問欄を外したこと・質問欄の部品と `houou_ask.*` を読むページが残るか。TOP が読む）

止まる条件（1つでも当たればマージしない）

* 0章の確かめで、PRP-10 のログの状態が「判断待ち」でない
* 生成した pro/ の全ページ（トップと個別 1,098件）のうち、`<meta name="robots" content="noindex">` が無いページが1件でもある
* `navbar.js` の「連盟プロ」の行き先・`index.html` のサイドメニュー・`llms.txt`・`sitemap*.xml`・`_redirects`・`wrangler.jsonc`・`workers/` が origin/cloudflare から変わっている（TOP の変更の取り込みで変わる行は除く。除いた行を書く）
* `assets/league_race.js`・`scripts/generate_title_pages.py`・`scripts/generate_houou_pages.py`・`scripts/check_image_links.py`・`jpml_pros.html` が origin/cloudflare から変わっている
* pro/ のほかに生成物の差分が出た
* テストが1つでも落ちる、または `regenerate-page.yml` の `check` で止まる理由が出た（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
* 未マージの work/ ブランチ（work/1101-prp-pub を除く）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たらなければマージする）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 同じ Chat-Ref のコミット・ログは無し。#641 は open
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）に欠けなし。「マージ: 承認済み（チャットで）」あり
- CHAT-1104-PRP-10 の `## 報告` の状態は「判断待ち」。末尾に `/ 続き: CHAT-1104-PRP-11` を足して work/1101-prp-pub に push（44e86cbb9。ログだけ）
- work/1104-prp-unpub はリモートにもローカルにも無かったので、`git checkout -b work/1104-prp-unpub origin/cloudflare`（dbac236b1）で作った。拒否なし

### 1. 移したもの・移さなかったもの

作り方: `git diff origin/cloudflare...origin/work/1101-prp-pub` のうち pro/ の中だけで効くファイル（`assets/pro.js`・`style.css`・`scripts/generate_pro_pages.py`・テスト3本）を `git apply -3` で当て、公開の段の部分を元に戻した（3dc36b5a3）。

| ファイル | 移したもの | 移さなかったもの（公開のマージで入れる） |
|---|---|---|
| `scripts/generate_pro_pages.py` | `face_chips()`・`chip_html()`（チップ3つ・「8/27生」）、`fav_button()`、`face_html()`（読みだけ・☆）、`LIVE_HEADINGS`・`live_panels()`（「すべて」は最後の面に1つ）、`PLAYER_TITLE`（題の形）、`FACETS`・`facet_values()`・`sheet_html()`（6つのタブ・「すべて」・桜花・★）、`top_row()`（`hidden` なし・`data-slug`）、`FAV_*`・`plans_json()`・`PLANS_FILE` と書き出し・`run_check()` の「今後の予定の JSON」、`SEARCH_FILE`、`TOP_URL` | `NOINDEX_TAG` を消すこと（残した）、`canonical=`、`build_sitemap()`・`SITEMAP_PATH`・`import xml.etree`、`redirect_table()`・`REDIRECT_TABLE`・`Site.aliases`、`OGP_IMAGE` の `img/ogp/pro/`（`pros/` のまま）、`top_ask()` の K8（`data-player-pages`）、`search_index()` の `pro_search` の注記 |
| `scripts/generate_pro_pages.py`（この指示で） | トップの質問欄を外した: `top_ask()`・`ASK_PLAYERS_URL`・`ASK_PLAYERS_PAGE`・`ASK_PLACEHOLDER`・`houou_pages.ASK_HEAD` の読み込み（`assets/houou_ask.js`・`houou_ask.css`）。各大会の `ask-players.json` からいまのリーグを読む `load_roster_of()` はそのまま | — |
| `assets/pro.js` | すべて（絞り込みの部品・`initTop()`・お気に入り・`?all=1` の書き換え）。冒頭の注記だけ「未公開。公開は #641」に戻した | — |
| `style.css` | pro/ の節の追加・変更すべて（☆/★・チップ・タブ・値のチップ・並べ替えのボタン 44px）。節の見出しの注記だけ「未公開」に戻した | — |
| `scripts/tests/test_pro_pages.py` | チップ・読み・☆・見出し・「すべて」の検査 | `PublishTest`（noindex なし・canonical・`sitemap-pro.xml`・転送の表・`pro_search` の位置・画像の検知）。代わりに `MetaTest`（noindex あり・canonical なし・題の形・`img/ogp/pros/`・トップに `houou_ask` と `data-players-page` が無い）を書いた |
| `scripts/tests/test_pro_favorites.py`・`test_pro_filter.py` | そのまま（新しいファイル） | — |
| `docs/new-page-checklist.md` | 「保存する項目」の `mj:favorites`（work/1101-prp-pub と同じ文。900dc1e19） | — |
| `docs/notes/pro-pages.md` | work/1101-prp-pub の版から、質問欄・Worker・サイトマップ・canonical・OGP の移動の記述を未公開の形に戻し、「未公開のまま入れたものと、公開のマージで入れるもの」の節を足した（382bfda5f） | — |
| そのほか（移さない） | — | `navbar.js`・`index.html`・`llms.txt`・`sitemap*.xml`・`scripts/update_sitemap_lastmod.py`・`.github/workflows/*`・`workers/pros-redirect/`・`scripts/regenerate.py`・`assets/league_race.js`・`assets/houou_ask.js`・`scripts/generate_houou_pages.py`・`scripts/generate_title_pages.py`・`scripts/lib/pro_search.py`・`scripts/check_image_links.py`・`scripts/check_sns_book_images.py`・`scripts/generate_jpml_pros.py`・`jpml_pros.html`・`img/ogp/`・ほかのテスト（`test_houou_ask.py`・`test_houou_pages.py`・`test_navbar.py`・`test_og_image.py`・`test_redirects_inventory.py`・`test_regenerate.py`・`test_title_member_links.py`・`test_check_sns_book_images.py`）・docs/notes の公開の段の記述 |

### 2. 確かめ（止まる条件の機械的な確かめ）

- テスト: `python3 -m unittest discover -s scripts/tests` → Ran 1832 tests OK。`node --test 'workers/*/test/*.test.mjs'` → tests 212 / pass 212 / fail 0
- `regenerate-page.yml` の `check`（target_page pro_pages、run 41687288495）: success。「- pro_pages: 通る(--check)」「check: 止まるページなし」
- 生成（run の結果 de3c4a813「chore: regenerate pro/ via GitHub Actions」）

| 種類 | 件数 |
|---|---|
| pro/<slug>/index.html | 1,098 |
| pro/index.html | 1 |
| pro/plans.json | 1（新しいファイル） |
| pro/ のほかの生成物 | 0 |

- 生成した pro/ の 1,099ページ: `<meta name="robots" content="noindex">` の無いページ 0、`rel="canonical"` のあるページ 0、`houou_ask` を読むページ 0、og:image が `img/ogp/pros/index-white.png` のページ 1,099
- `git diff --stat origin/cloudflare...HEAD -- navbar.js index.html llms.txt 'sitemap*.xml' _redirects wrangler.jsonc workers/ assets/league_race.js scripts/generate_title_pages.py scripts/generate_houou_pages.py scripts/check_image_links.py jpml_pros.html` → 出力なし（変わっていない。TOP の取り込みは無かったので除いた行も無し）
- トップ: 表の行 1,098・`hidden` の行 0・1行の初期値「すべて」。白鳥翔のページ: ☆ のボタン1つ・「すべて（93件）」1つ・題「白鳥翔 | 連盟プロ | 日本プロ麻雀連盟 | ryoei.pro」
- プレビュー（de3c4a813 の Workers Builds、約2分で success）を Playwright（UA に mj-internal）で 360px・明暗: トップは開いた直後「すべて」1,098名、質問欄なし、東京 → 23期 → あ → 「すべて」で戻る流れと ★・誕生日と「解除」・`?all=1` の書き換えが PRP-09・10 のローカルと同じ結果。白鳥翔のページは ☆ → ★（明 rgb(20,69,155)・暗 白）、「すべて（93件）」は実況・解説の欄の下に1つ。どれも横のはみ出し 0

### 3. 未マージの work/ ブランチ（work/1101-prp-pub を除く）

変えるファイルに触るのは: work/1028-lgn・work/1029-lgn-page（`style.css`・`docs/new-page-checklist.md`）、work/1104-lvp（`docs/new-page-checklist.md`）、work/1104-top（`docs/notes/pro-pages.md`・`style.css`）、work/1104-ttl（`style.css`）。どれも別の箇所で、`git merge-tree` で cloudflare を起点にしたときより衝突が増えるブランチは無い。

work/1104-top の `docs/notes/pro-pages.md` の追記（「トップの質問欄〈`top_ask()`・`assets/houou_ask.js`〉は…pro/ の公開の作業が同じ関数を変えているため残っている」）は、この指示で pro/ のトップの質問欄を外したので古くなる。#641 のコメントで TOP に伝える。

### 4. 質問欄の部品と `houou_ask.*` を読むページ（この指示の後）

- pro/ は読まなくなった（生成した 1,099ページで 0）
- cloudflare の今の生成物で `houou_ask.js`・`.css` を読むのは houou/・jwrc/・ouka/・wrcr/・saikyo/・top/ のトップ。`ASK_TEMPLATE`・`ASK_HEAD` を使う生成は `generate_houou_pages.py`（4大会）・`generate_saikyo_pages.py`・`generate_top_page.py`。work/1104-top（TOP-12）が houou/ などの欄を消すと、残るのは top/（`generate_top_page.py` の5大会の横断の欄）だけの見込み
- pro/ の生成は各大会の `ask-players.json`（`houou_pages.ASK_PLAYERS`）をいまのリーグと最強戦の出場に読み続ける（`load_roster_of()`・`SAIKYO_ROSTER`）。欄を消しても `ask-players.json` の生成は残す必要がある

## 報告

- 状態: 作業中
- ブランチ: work/1104-prp-unpub
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-unpub/docs/logs/CHAT-1104-PRP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-unpub
- 確認用URL: 作業中
- マージ: 作業中
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dbac236b）: https://github.com/retroeater/mj-logs/tree/main/guide/dbac236b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
