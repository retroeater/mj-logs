# CHAT-1104-LVP-10

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 53edca83d

## 指示

【Claude作成】Claude Code 向け指示：live/ を公開する変更を作り、プレビューで止める（noindex を外す・navbar・サイトマップ・llms.txt・旧 video_live.html の 301・プレーオフ） Chat-Ref: CHAT-1104-LVP-10 マージ: 判断待ちで止まる（プレビューで止め、告知動画を撮った後にマージ＝公開する。マージは次の指示） 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。work/1017-lvp（CHAT-1102-LVP-09）が cloudflare にマージ済みであることを確かめ、違えば止まる。

目的
/live の正式公開（#362）の変更を1本の作業ブランチに作り、プレビューで止める。平野さんがプレビューから告知動画を撮った後、次の指示でマージ（＝公開）する（最強戦と同じ順）。pro/ の公開（#641）は live/ の公開を待つ。
決定（2026-10-16〜11-04、平野さん）

* 公開の段（docs/new-page-checklist.md の段2）: noindex を外す。navbar の「番組 > 放送対局」の行き先を `/video_live.html` から `/live/` に替える（位置・名前は今のまま）。生成で `sitemap-live.xml` を書き出して `sitemap.xml` から参照する（ほかの生成ページと同じ形）。`llms.txt` の放送対局の行を `/live/` に替え、本数を今の値に直す
* 旧 `video_live.html` は公開と同時に `/live/` へ 301 し、旧ページの生成を止める（`jpml_titles`・帰り道・Mつくの前例にそろえる）。`docs/notes/redirects.md` に行を足す
* 旧 `video_live.html?name=<名前>` の流入を受けるため、/live が `?name=` を `?q=` と同じ絞り込みとして受ける。これは期限つきの暫定とし、外すトリガを `docs/notes/redirects.md` に書く（転送の四半期の棚卸し〈#568〉で、Cloudflare の集計で `?name=` の流入が無いと確かめたら、301 の行と `?name=` の受け付けを一緒に外す）
* canonical は live/ の全ページに付ける（トップの `?q=`・`?name=` は表示の絞り込みなので、2026-10-31 の基準〈docs/decisions/seo-bing.md、CHAT-1031-SEO-13〉でも自分自身を指す）
* URL パラメータの規約の表（docs/new-page-checklist.md）に `q` と、期限つきの `name` を足す
* 達人戦のプレーオフ（第1〜3期、各期2本）を載せる。この指示でステージの記号「プレーオフ」と並び（ベスト16 の前）をコードに足し、Claude Code が【3】の6行に入れる値の表を作る。平野さんは表を見て【3】に入れ、その後にマージする。掲載はメンバー限定版を Y、公開版は【3】の「冒頭」で決める（冒頭が Y なら N。決勝と同じ扱い）
* 文書の直しを公開の変更に入れる（B14）: docs/notes/live-page-design.md の古い記述（4章の「Workers のプラン 未確認」、2-3 の「未検証」、2-7 の公開手順など）、docs/notes/static-generation.md と docs/handover.md の「未公開」、docs/new-page-checklist.md の実例表の live 列、#362 の本文
* SEO-03 の B7（「18 の件」）は、#362 のコメントから中身を読み、公開前の確かめに入れる
* 公開後の確かめは段2のとおり（本番の HTML、X・LINE のカード、実機、Search Console の再送信と URL 検査）。約2週間後のインデックス登録の確認はカレンダーに入れる（チャット側）。確かめは次の指示で行う
* 公開の後に回すもの（この指示ではしない）: #495（公開の直後に別の指示）、#490 の残り・#398〜#403・#354（決定どおり公開の後に順に）、#396・#397 の本文を「かな・所属」だけに直して残す、LV-04 の 798 と 794 の差4は調べずに閉じる

前提（チャット側。平野さんの決定ではない）

* 2026-11-04 の cloudflare（53edca83d）で、`navbar.js` は `/video_live.html` を指し、`llms.txt` の放送対局の行は `video_live.html`・「2,332本」、`_redirects` に `/live`（301）・`/live/`・`/live/:slug/` と生成の節がある。`sitemap-live.xml` は無い。旧ページの生成は `scripts/generate_video_live.py`（ほかに `scripts/generate_jpml_pros.py`・`scripts/lib/page.py` が `video_live` を参照する。要確認）
* 2026-11-04 の時点で、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・live/ の生成スクリプトを変えている未マージの work/ ブランチは無い（要確認）
* 前例: `/video_mtsuku.html` → `/mtsuku/`（#570）、`/saikyo/?q=` の期限つきの行（#627）の書き方が docs/notes/redirects.md にある
* pro/（未公開）は live/ の期・卓のページと `/live/?q=<名前>` へリンクしている（CHAT-1101-PRP-01）。この指示の変更の後も、その形のリンクが開けて絞り込まれる必要がある
* プレーオフの6行（2026-10-17 の行番号）: 4289 `pPhdXp3U7mU`・4283 `lQTUI_C_Vw0`（第1期）、4228 `4j_vrUqShCc`・4222 `1-DyHZm29OM`（第2期）、4240 `DfjwueadOwc`・4264 `XQLktBZyx5A`（第3期）。【3】の掲載は空欄、ステージ・まとめ単位も空（要確認）
* 公開前の基準値（Search Console）は残さない（B11）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。`regenerate-page.yml` を作業ブランチで `check`・`target_page: live_pages` で手動実行し、生成が止まらないことを確かめる。【3】のプレーオフの6行を動画ID で読み、「冒頭」の値と、上の決定のとおりに入れる値（掲載・ステージ・ステージの並び・まとめ単位）の表を `## 経過` に書く（並びの値は既存のステージとの前後が分かるように）。#362 のコメントから SEO-03 の B7 を読み、何を確かめるかを書く
2. 上の決定を実装する（noindex・navbar・`sitemap-live.xml` と `sitemap.xml`・`scripts/update_sitemap_lastmod.py` の対象・`llms.txt`・旧ページの 301 と生成の停止〈`regenerate.py`・ワークフロー・テストの参照も〉・`?name=` の受け付け・canonical・ステージの記号「プレーオフ」・URL パラメータの表・B14 の文書・docs/notes/redirects.md の行）。旧ページを参照する箇所は `git grep -n 'video_live'`（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）で洗い出し、残すものは理由を書く。`npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で、`/video_live.html`・`/video_live.html?name=<名前>`（クエリが残って絞り込まれるか）・`/live/?q=<名前>`・pro/ の個別ページから live/ へのリンク1つを確かめる。全ページを再生成し、live/・sitemap・`llms.txt`・navbar・`_redirects`・文書のほかの生成物の差分が0であることを確かめる。テストを回す
3. 作業ブランチに push してプレビューで止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、プレーオフの6行に入れる値の表、プレビューで見てほしいページ（告知動画に使えるページの候補を含む）、SEO-03 の B7 の確かめの結果、次の指示（マージ＝公開と公開後の確かめ）に要る項目を書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 0章の確かめに当たる
* 手動実行の `check` が止まる、または `--check live_pages` が止まる理由を出す
* プレーオフの6行が【3】に見つからない、または「前提」の行と動画ID が合わない（その行を書いて止まる）
* `/video_live.html?name=` の 301 でクエリが残らず、`?name=` の受け付けが働かない（実測の結果と、代わりの案〈`_redirects` で `?name=` を `?q=` に書き換える等〉を書いて止まる）
* live/・sitemap・`llms.txt`・navbar・`_redirects`・文書のほかの生成物に差分が出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・`scripts/generate_live_pages.py`・`scripts/lib/live.py`・`assets/live.js`・`scripts/generate_video_live.py`・`scripts/regenerate.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-10` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1017-lvp は origin/cloudflare の祖先（LVP-09 はマージ済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1104-lvp はローカル・リモートとも無く、origin/cloudflare（53edca83d）から作成。着手は 2026-11-04 01:42 JST（毎朝の取り込みの直後ではない）

### 手順1: 着手前の確かめ

- #362 のコメント（48件）に他セッションの着手中は無し。着手中のコメントを書いた
- 未マージの work/ ブランチで、指定のファイルを変えているもの（同じ行の変更は無い）:
  - work/1028-lgn・work/1029-lgn-page: `navbar.js`（`newNavCurrent` の先頭の分岐〈/log/〉）、`_redirects`（`/log` の2行）、`scripts/regenerate.py`（log_pages の行）
  - work/1101-prp-pub: `navbar.js`（「連盟プロ」の項目の href と pro/ の分岐のコメント）、`llms.txt`（連盟プロの行とサイトマップの行）、`sitemap.xml`（sitemap-pro.xml の節）、`scripts/regenerate.py`（pro_pages の行）
  - work/1104-top: `navbar.js`（`newNavCurrent` に番組の新しいページ〈`/live/` を含む〉の分岐と新しいナビの帯）、`scripts/regenerate.py`（top/ のコメント）。live/ の個別ページのナビの見た目に関わるが、こちらが変える「番組 > 放送対局」の href の行とは別
  - work/1104-ttl: `navbar.js`（タイトル戦の分岐）
  - `sitemap.xml` と `llms.txt` は work/1101-prp-pub と同じ節の近くを足すことになるので、行を離して置く（サイトマップの説明の行は変えない）
- regenerate-page.yml を作業ブランチで check・`live_pages`（run 41677090621、一時の出力のコミット 755f08d13 で）: 止まるページなし（ページ 944、公開する動画 2,986本）。警告は「未確認 691件」の1件だけ
- 一時の出力（【3】と【2】の値。読んだ後に revert した）で読んだプレーオフの6行（行番号は 2026-10-17 のまま、動画ID も前提どおり）:

| 行 | 動画ID | 期 | 【2】の限定 | 【3】の掲載 | 【3】の冒頭 | 【3】のステージ・並び・まとめ単位 | 【2】のステージ・並び・動画の単位・まとめ単位 |
|---|---|---|---|---|---|---|---|
| 4289 | pPhdXp3U7mU | 第1期 | Y（メンバー限定） | 空欄 | 空欄 | 空欄 | 空・空・ステージ・空 |
| 4283 | lQTUI_C_Vw0 | 第1期 | N（公開版） | 空欄 | Y | 空欄 | 空・空・ステージ・空 |
| 4228 | 4j_vrUqShCc | 第2期 | Y | 空欄 | 空欄 | 空欄 | 空・空・ステージ・空 |
| 4222 | 1-DyHZm29OM | 第2期 | N | 空欄 | Y | 空欄 | 空・空・ステージ・空 |
| 4240 | DfjwueadOwc | 第3期 | Y | 空欄 | 空欄 | 空欄 | 空・空・ステージ・空 |
| 4264 | XQLktBZyx5A | 第3期 | N | 空欄 | Y | 空欄 | 空・空・ステージ・空 |

- 同じ期の決勝（掲載 Y の6行: 4257・4242・4252・4291・4293・4287）は、【2】のステージ「決勝」・まとめ単位「決勝」・ステージの並び 空（`live_extract.py` の `STAGE_ORDER` に達人戦が無いため）
- SEO-03 の B7（#362 の 2026-10-29 のコメント）: 「live/ のトップ・大会・期の og:image を最新話のサムネイルにする件（LVP の B9、#581 から移した）」。LVP-06 で実装済み。公開前の確かめとして、生成物のトップ・タイトル戦・期の og:image がその範囲で最新の動画のサムネイルになっていること（と、個別ページは今のまま）を見る

### 手順2: 実装（主なコミット bff719d11・b3ebd398c・bf699c350）

- `scripts/generate_live_pages.py`: noindex を外し、全ページに canonical（`page_url(path)`、トップは `https://ryoei.pro/live/`）を付けた。`build_sitemap()`（`sitemap-mtsuku.xml` と同じ形。既にある lastmod を残し、新しい URL は当日）を足し、`main()` で `sitemap-live.xml` を書き、`--check` でも組み立てる
- `scripts/lib/live.py`: ステージの記号 `"プレーオフ": "po"`。日本シリーズの「プレーオフ第1節」は卓の形でないので当たらない（テストあり）
- `scripts/lib/live_extract.py`: `STAGE_ORDER` に `"達人戦": {"プレーオフ": "1", "ベスト16": "2", "ベスト8": "3", "決勝": "4"}`。【2】の達人戦の決勝の並びが空から 4 になり、プレーオフの行もステージ・並びが入る（毎日の取り込みの次の回から。【3】の既存の行は書き換えない）。毎日の取り込みの「行数・候補が減ったら止める」には当たらない（候補の判定は `classify()` で、`analyze()` の影響を受けない。自己レビューで実データ 10,002 件にかけて、変わるのは達人戦の18件だけと確かめた）
- `assets/live.js`: `?q=` が無く `?name=` があれば、それを検索欄の初期値にする（期限つき）
- `navbar.js`: 「番組 > 放送対局」の href を `/live/` に
- `_redirects`: `/video_live.html  /live/  301`（`/video_mtsuku.html` の次）
- `sitemap.xml`: `sitemap-live.xml` の節（sitemap-title の後。work/1101-prp-pub が足す sitemap-pro の節と離した）。`sitemap-pages.xml` から `video_live.html` の URL を消した
- `scripts/update_sitemap_lastmod.py`: `SITEMAP_LIVE`（`live/` 配下）を足し、`--from-git` の対象に入れた
- `.github/workflows/regenerate-page.yml`: サイトマップの XML の確かめの一覧に `sitemap-live.xml` を足した。作業ブランチで手動実行して通った（run 41678301131〈生成・確かめ・コミット〉、41678927453〈dry_run〉）
- `scripts/regenerate.py`: `live_pages` の出力に `sitemap-live.xml` を足した（`--check` で戻す対象に入る）
- 旧ページ: `video_live.html` と `scripts/generate_video_live.py` を消した。それを使っていたテスト（`test_simple_pages_check.py` は rh_paifu に、`test_taikai_names.py` は該当の確かめを消した）を直した
- `llms.txt`: 放送対局の行を `https://ryoei.pro/live/` に替え、本数を 2,986本（`live/index.html` の「動画2986本」）にした。プレーオフの3本が入ると 2,989本になる
- 文書: docs/notes/redirects.md（棚卸しの対象の行。入れた日は「2026-11-04（予定。公開の指示でマージした日に直す）」）、docs/new-page-checklist.md（URL パラメータの `q`・`name`、実例の表の live 列）、docs/notes/live-page-design.md（2-3 の「未検証」、2-7 の公開の手順、4章の Workers のプラン、冒頭の noindex、2-10 の見出し、1章の「現行 video_live.html」）、docs/notes/static-generation.md（放送対局の行・型A の行と件数 4→3）、docs/handover.md（共有ボタンの行・BreadcrumbList の並び）、docs/notes/title-pages.md（消した生成の名前）
- `git grep -n 'video_live'`（docs/decisions・docs/logs・handover-archive・docs/gsc を除く）で残したもの:
  - `_redirects`・`assets/live.js`・docs/notes/redirects.md・docs/new-page-checklist.md・docs/notes/live-page-design.md・docs/notes/static-generation.md・`scripts/generate_live_pages.py`・`scripts/lib/page.py`: 今回の転送・経緯の説明
  - `jpml_pros.html`（319件）・`scripts/generate_jpml_pros.py:270`・`scripts/tests/test_jpml_pros.py`: 旧 jpml_pros の放送対局の件数のリンク `./video_live.html?name=`。直すと jpml_pros.html が変わり「ほかの生成物に差分が出ない」に当たる。301 と `?name=` の受け付けで届く。jpml_pros は #641 で pro/ に替わる
  - `style.css:433`・`table.js:4`: 型Aの共通部品のコメント（動作に関わらない）
  - docs/lighthouse-baseline.md・docs/notes/decisions-2026-09-13-review.md・docs/notes/site-findings.md・docs/review-followup-instructions.md・docs/notes/static-generation.md の 17〜18 行: 過去の記録
- テスト: `test_live_pages.py`（noindex 無し・canonical・サイトマップの lastmod の保持と当日・プレーオフの記号）、`test_live_extract.py`（達人戦の決勝の並び 4・プレーオフの題名で並び 1）を足した・直した。`python3 -m unittest discover -s scripts/tests`: 1,814件 OK（cloudflare を取り込んだ後、e64d1d101）

### 手順2: 確かめ

- 作業ブランチで regenerate-page.yml の `live_pages`（run 41678301131、d3344288b）: live/ の 944 ページを生成し直し、`sitemap-live.xml`（944 URL）を書き出した。どのページにも noindex が無く、canonical は自分自身
- `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state`（wrangler 4.45.0、npx で一時に取得。リポジトリに依存は足していない。作業ツリーに作られた `node_modules/.mf` は消した）:
  - `/video_live.html` → 301 `/live/`、`/video_live.html?name=%E5%A0%80%E6%85%8E%E5%90%BE` → 301 `/live/?name=%E5%A0%80%E6%85%8E%E5%90%BE`（クエリが残る）
  - ローカルの wrangler dev では、`_redirects` の 200 の書き換えでディレクトリを返す URL が 404 になる（`/`・`/saikyo/`・`/mtsuku/` も同じ。今回の変更と無関係）ので、`/live/` の絞り込みはプレビューで確かめた
- プレビュー（d3344288b の Workers Builds: success。ヘッドレス Chromium、UA に `mj-internal`）:
  - `/video_live.html?name=堀慎吾` → `/live/?name=堀慎吾`。検索欄が「堀慎吾」、「767件中 3件を表示」（`/live/?q=堀慎吾` と同じ件数）
  - `/video_live.html` → `/live/`。`/live/` は noindex なし、canonical `https://ryoei.pro/live/`、navbar の「放送対局」は `/live/`
  - pro/ の個別ページ（`/pro/hiro.shibata.17/`）からのリンク: `/live/?q=HIRO柴田` が開いて「767件中 125件を表示」、`/live/masters/34/b8-b.html` が 200
  - `/sitemap.xml` に `sitemap-live.xml` があり、`/sitemap-live.xml`・`/llms.txt`（放送対局の行が `/live/`）が 200
- 取り込んだ後の e64d1d101 のプレビュー（Workers Builds: success）でも、`/live/`・`/live/tatsujin/`・`/sitemap-live.xml` が 200、`/video_live.html?name=…` が 301 でクエリを保つ、navbar が `/live/`、`/live/` に robots の meta が無いことを確かめた
- 全ページの新旧の比べ（regenerate-page.yml の dry_run・all・compare_ref `origin/cloudflare`、run 41678927453。比べの相手は d580617a1）: 中身の違うファイルは5件で、`wayhome/index.html`・`wayhome/OkKAh8yP8Uk.html`・`wayhome/aNSsJicbMlM.html`・`top/index.html`・`title/tatsujin/3.html`。cloudflare 側にだけあった YouTube の情報の更新（a1f075557、`data/youtube_meta.json` に帰り道の新しい回）によるもの（帰り道の一覧・各話、トップの新着の番組、帰り道の `aNSsJicbMlM` へリンクする title/ の期のページ）。今回の変更による live/・サイトマップ・`llms.txt`・navbar・`_redirects` のほかの差は無い。その後 cloudflare を取り込んだ（e64d1d101、衝突なし）
- SEO-03 の B7: 生成物の og:image は、トップ `-K8QmXaw8uU`（第24期プロクイーン決定戦、トップの最新の行）、`/live/judan/` と `/live/judan/43.html` は `jtimc62TkpE`（第43期十段戦 ベスト8 B卓 5回戦、十段戦で最新）、個別ページ `/live/judan/43/f.html` はそのページの先頭の動画のまま。決定（トップ・大会・期は範囲で最新の動画のサムネイル、個別は今のまま）のとおり

### 自己レビュー（新しい文脈のサブエージェント、1回）

| 指摘 | 扱い |
|---|---|
| `llms.txt` の「サイトマップ」の行の列挙に `sitemap-live.xml` が無い（mtsuku・exam の公開では足している） | 見送り: work/1101-prp-pub が同じ行を変えている（止まる条件の「同じ行」に当たらないよう触らない）。次の指示で足す（判断が必要なこと） |
| docs/notes/static-generation.md の「HTMLは21ページ」が古い（直下のページは origin で 11） | 見送り: 元からの食い違い。判断が必要なことに挙げた |
| `regenerate-sitemap.yml`・docs/notes/sitemap-lastmod.md・static-generation.md の列挙・regenerate-page.yml のコメントに sitemap-live.xml が無い（mtsuku・exam も元から無い） | 見送り: 元からの抜け。判断が必要なことに挙げた |
| live-page-design.md:27 の「現行 video_live.html」 | 直した（bf699c350） |
| `live.py` の「プレーオフ」のコメントが日本シリーズの「プレーオフ」単体に触れていない | 直した（bf699c350）。日本シリーズの【3】で「プレーオフ」単体を掲載 Y にすると `po` のページになる（今は該当なし） |
| 達人戦のプレーオフの題名の並びの検査、サイトマップの新しい URL の当日の検査が無い | 直した（bf699c350） |
| 【3】に手で入れた達人戦の並びと新しい表の番号が今後の期で混ざりうる | 意図どおり: 今は決勝の1ステージだけで影響なし。プレーオフの並びは表と同じ 1 を入れる案にした |

## 報告

- 状態: 判断待ち / 続き: CHAT-1104-LVP-11
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: プレビューあり（URL は最終報告）。この版の印は、`/video_live.html` を開くと `/live/` に移り、navbar の「番組 > 放送対局」が `/live/` を指すこと
- マージ: 未（判断待ち。告知動画を撮った後、次の指示でマージ＝公開）
- issue: #362（着手中のコメント）
- 判断が必要なこと:
  - 【3】のプレーオフの6行に入れる値（公開版は「冒頭」が Y なので N。ステージの並びの 1 は決勝〈【2】で 4〉・ベスト16〈2〉より前）:

| 行 | 動画ID | 期 | 掲載 | ステージ | ステージの並び | まとめ単位 |
|---|---|---|---|---|---|---|
| 4289 | pPhdXp3U7mU（メンバー限定） | 第1期 | Y | プレーオフ | 1 | プレーオフ |
| 4283 | lQTUI_C_Vw0（公開版、冒頭 Y） | 第1期 | N | （空欄のまま） | （空欄のまま） | （空欄のまま） |
| 4228 | 4j_vrUqShCc（メンバー限定） | 第2期 | Y | プレーオフ | 1 | プレーオフ |
| 4222 | 1-DyHZm29OM（公開版、冒頭 Y） | 第2期 | N | （空欄のまま） | （空欄のまま） | （空欄のまま） |
| 4240 | DfjwueadOwc（メンバー限定） | 第3期 | Y | プレーオフ | 1 | プレーオフ |
| 4264 | XQLktBZyx5A（公開版、冒頭 Y） | 第3期 | N | （空欄のまま） | （空欄のまま） | （空欄のまま） |

  - 入れると `live/tatsujin/1/po.html`・`2/po.html`・`3/po.html` ができ、期のページで決勝の前に「プレーオフ」の見出しが出る見込み（ステージに1対局なので行の題は出ない）。入れた後は次の指示の前に `--check live_pages` で確かめる
  - プレビューで見てほしいページ（告知動画の候補に ★）: ★`/live/`（白地のトップ・題名のシート・検索）、★`/live/judan/43.html`（期のページ・ステージの見出し）、★`/live/judan/43/f.html`（濃色のヒーローの個別ページ）、`/live/judan/`、`/live/tatsujin/`、`/video_live.html?name=堀慎吾`（旧 URL から絞り込まれて開く）、pro/ の個別ページからの `/live/?q=…`
  - SEO-03 の B7 の確かめ: トップ・大会・期の og:image はその範囲で最新の動画のサムネイル、個別ページは今のまま（`## 経過`）。X・LINE のカードの見え方は公開の後に確かめる
  - 次の指示（マージ＝公開と公開後の確かめ）に要る項目:
    - docs/notes/redirects.md の `/video_live.html` の行の「入れた日」を、マージした日に直す
    - `llms.txt` の「サイトマップ」の行の列挙に `sitemap-live.xml` を足す（work/1101-prp-pub が同じ行を変えているので、この指示では触っていない）。プレーオフを入れたら本数（2,986→2,989）も直す
    - #362 の本文を公開後の形に直す（決定の B14。公開前に「公開した」と書かないため、この指示では直していない）
    - 元からの食い違い（直すかの判断）: docs/notes/static-generation.md の「HTMLは21ページ」（直下は 10）、`regenerate-sitemap.yml`・docs/notes/sitemap-lastmod.md・static-generation.md の列挙・regenerate-page.yml のコメントに mtsuku・exam・live のサイトマップが無い
    - 公開後の確かめ（段2）: 本番の HTML（noindex 無し・canonical・`/video_live.html` の 301 とクエリ）、X・LINE のカード、実機、Search Console のサイトマップの再送信と URL 検査
    - 達人戦の `STAGE_ORDER` は毎朝の取り込みの次の回から【2】に入る（決勝の並び 4）。公開の前にその回を挟むと、`--check live_pages` でも変化を見られる
    - 未マージの work/1104-top が `navbar.js` で live/ の個別ページを新しいナビの帯にする分岐を足している。どちらを先にマージしても行は重ならないが、個別ページのナビの見た目が変わる
- 未確認の項目:
  - 実機（iPhone など）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce650cc3）: https://github.com/retroeater/mj-logs/tree/main/guide/ce650cc3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce650cc3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
