# CHAT-1017-OUK-01

- 着手日時: 2026-10-17
- 対象issue: #519・#516
- ブランチ: work/OUK
- 着手時HEAD: 8988cc8

## 指示

【Claude作成】Claude Code 向け指示：女流桜花を houou/ と同じ形で ouka/ に作り直す（自律開発。未公開でプレビューまで） Chat-Ref: CHAT-1017-OUK-01 マージ: 判断待ちで止まる（平野さんがプレビューで動作を確かめ、仮置きをまとめて確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: CHAT-1017-AMP-03 の作業（work/1016-amp、鳳凰戦 個人成績の期別の進行中の期＋Ampai の行）が cloudflare にマージされた後。0章で確かめる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/OUK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/OUK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/OUK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて「貼る時機」を確かめる: `git log origin/cloudflare --grep="CHAT-1017-AMP-03" --oneline` が空、または `origin/work/1016-amp` がリモートにあって `git merge-base --is-ancestor origin/work/1016-amp origin/cloudflare` が偽なら、何もせず止まる（AMP のマージ待ち）。 これは「自律開発」の指示。平野さんへの事前の質問は済んでいる（下の「決定」）。着手後は質問せず、houou/ との差異・判断に迷う点・不明な点は「仮置き」で決めて最後まで進め、仮置きしたことをすべて報告の「判断が必要なこと」に一覧で書く。止まるのは下の「止まる条件」に当たるときだけ。

目的
女流桜花の旧ページ（`ouka_results.html`・`ouka_leagues.html`・`ouka_ranking.html`）を、鳳凰戦の `houou/`（#518、公開 #540）と同じ形の `ouka/` に作り直し、未公開の形でプレビューに出す。平野さんはプレビューで動作を確かめ、仮置きをまとめて確かめる。公開（旧ページの 301・navbar・サイトマップ・`llms.txt`）は別の issue・別の指示で行う。
決定（2026-10-17、平野さん）

* 「女流桜花」のリニューアルを行う。「鳳凰戦」が `houou/` に構築したものを可能な限りすべて踏襲する
* URL は `ouka/`。未公開で作り、最後に公開を切り替える（いつものやり方。docs/new-page-checklist.md の段1・段2）
* 進め方は「自律開発」: 事前に質問をまとめて聞き（済み）、以後は自律的に最後まで開発し、開発中の不明点は仮置きして進め、動作確認のときに不明点・仮置きをまとめて確かめる
* 順位変動（節ごとの順位、#516）も `ouka/race/` として今回作る
* トップの質問欄（データ・コンシェルジュ、#542）も `ouka/` に置く。Worker（`mj-ask`）は鳳凰戦と共有し、1日の上限も共有する
* 進行中の期＋Ampai のリンクの行（AMP、#584）のマージの後に着手し、桜花の個人成績にも同じ行を入れる
* 到達点はブランチのプレビューで止まる（cloudflare にはマージしない）
* `assets/houou.js`・`assets/houou_race.js` は共通の名前に改名して鳳凰戦・女流桜花で共有する（2026-10-14 の「`assets/houou.js` は今のまま確定（変えるなら #519 で）」をここで置き換える）
* ランキングの規定（出場の下限）は、旧 `ouka_ranking.html` の値（`league_ranking.js` の桜花の値）を引き継ぐ
* 「最近見た選手」の履歴は大会ごとに分ける（鳳凰戦の `mj:recent-players` と共有しない）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 仕様の正: `docs/notes/houou-top.md`・`docs/decisions/houou.md`（2026-10-08〜）・`docs/notes/ask-worker.md`・`docs/notes/design.md`。「踏襲」はそこにある画面・部品・数え方・確かめ方の全部（トップの行の面・質問欄と選手のカード、個人成績の選手を探す部品・名前の面・4つのタブ〈期別・通算・リーグ・部門別〉・最近見た選手・パンくず、ランキング9部門の静的なページ・格子の選ぶ部品・規定の ⓘ・自動ルール、順位変動、字の大きさへの連動、在籍者だけ、「別名」による名寄せ、OGP の規則、`_redirects` の行）を指す。houou-top.md「女流桜花への展開」に、大会をパラメータにする範囲（集計・検索の索引・ページの枠）がある
* 桜花のデータの違い（要確認。手順1で実物を数える）: 集計元は正本の「桜花」タブ（2026-10-15 に列を消して17列。「前後」は無く、見出しで読む。`generate_ouka_leagues.py`・`lib/pro_stats.py` が読んでいる）。期は通期で「21期」の形（前後が無い）。節は第1〜8節まで。リーグ名・組の有無・タイトル保持者の行の名前（「桜花」の見込み）・節の成績が無い期・進行中の期は実物で確かめる。Ampai のブック（`lib/ampai.py`）には「女流桜花」の行がある
* 生成の作りの案: `generate_houou_pages.py` の `CONTEST` を大会ごとの設定にし、同じ生成で `houou/` と `ouka/` を書く（または `generate_ouka_pages.py` を薄い入口にする）。`regenerate.py` の対象名と `OUTPUT_OVERRIDES`（例 `"ouka_pages": "ouka/"`）、`_redirects`（`/ouka` → `/ouka/` の 301、`/ouka/`・`/ouka/:slug/`・`/ouka/ranking/:slug/` の 200、`/ouka/ranking/` → `total/` の 301）は houou/ に倣う。共有の部品は複製しない
* 改名する JS の名前は仮置き（例 `assets/league.js`・`assets/league_race.js`。`houou_ask.js` など ouka/ でも使うほかの JS・CSS のクラス名〈`.mj-houou` など〉・localStorage のキーを同じ趣旨でそろえるかも仮置き。内部の名前は URL と対応が分かる形にそろえるのが平野さんの方針〈2026-10-16、study〉）。旧 `houou_race.html` が読む `houou_race.js` は変えない
* 仮置きの例（ほかにも出たら同じ扱い）: 桜花のタイトル保持者のチップの色（鳳凰位の #9f1239 に当たるもの。WCAG 4.5:1 以上）、リーグのチップと帯の色（houou/ と同じ A〜E の色の流用か）、最近見た選手のキー（例 `mj:recent-players:ouka`）と履歴が無いときの1件（最新の期のタイトル保持者）、title・h1・説明文の文言（「<ページ> | 女流桜花 | 日本プロ麻雀連盟 | ryoei.pro」の形）、OGP 画像（docs/notes/ogp.md の規則で白地・黒字の「女流桜花」＋「ryoei.pro」、`img/ogp/ouka/index-white.png`）、順位変動の対象の期、トップの3項目の説明、質問欄の文言・答えの範囲（桜花の問いに桜花のデータで答え、鳳凰戦の問いへの答えは変えない）
* 質問欄: Worker は Preview Builds が OFF で、`ALLOWED_ORIGIN` が `https://ryoei.pro` のため、プレビューからは問いを送れない見込み（要確認）。確かめは `node --test 'workers/ask/test/*.test.mjs'` と、Claude を呼ばない範囲（費用と上限を使わない）。実際の問いの確かめはマージ後に回し、「未確認の項目」に書く。Worker の `wrangler.jsonc` の vars（上限 200・IP 10）は変えない
* 旧3ページ（`ouka_*.html`）と、ほかのページの桜花へのリンク（`jpml_pros` の桜花の欄など）は変えない（公開の指示で張り替える）。WRC（`wrc_*`）と、予選の通過・敗退（#571）は対象外
* `.github/workflows/` のファイルはこの指示では変えない（Actions の整理〈WFL 系列、#564〉が並行して変えているため）。`check-assets.yml` の許可リストなど要る変更は報告に書いて進める（作業ブランチの check がそれだけで失敗するのは想定どおり。最終報告に「失敗通知が届くが対応不要」と書く）

手順

1. 調べる: #519・#516 の状態（本文・コメント・着手中のコメント）、同じ主題の issue（「女流桜花」「ouka」、クローズ済みも）、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめる。「桜花」タブを生成と同じ経路で2回読み、行数・列の見出し・期の範囲・リーグ名の一覧・組の有無・タイトル保持者の行・節の成績が無い期・進行中の期・在籍者の数をログの `## 経過` に表で書く。`league_ranking.js` から桜花の9部門の設定（規定・最小回数など）を写す。#519 に着手中のコメントを書く（#516 は #519 に取り込む旨をコメント）
2. 作る（節目ごとにコミットして push）: (a) 共通化と JS の改名（houou/ の生成物の差分は、改名した JS を指す行だけ）→ (b) `ouka/` のトップ（質問欄・選手のカードを含む）・個人成績（4つのタブ＋進行中の期と Ampai の行）・ランキング9部門・順位変動・`search.json`・成績 JSON・OGP 画像を、未公開（段1: 全ページ noindex・navbar／サイトマップ／`llms.txt` に載せない・既存のページからリンクしない）で生成する → (c) Worker の検索データと問いの振り分けに桜花を足す。テストを足す（`scripts/tests/test_houou_pages.py` に倣い桜花の集計・自動ルール・成績 JSON の形。Worker は鳳凰戦の既存のテストが通ったまま桜花のケースを足す）。`python3 -m unittest discover -s scripts/tests` と Worker のテストを回す
3. 確かめと文書: `python3 scripts/regenerate.py all` の後、houou/ 以外の生成物（旧 `houou_*`・`ouka_*` を含む）の差分が0、houou/ は改名した JS を指す行だけであることを確かめる。ランキング9部門を旧 `ouka_ranking.html` の集計と突き合わせ、違う行と理由（名寄せ・在籍者だけ・浮動小数など）を書く。375px・PC 幅・1920×1080 と、字の大きさ 100%・150%・200%（375px）で各ページを headless Chrome で見て、houou-top.md の「確かめ方」（リーグのタブの再生のアイコンの画面内の座標など）も行う。プレビュー（確認用 URL）を出す。公開の issue を起票し（本文に段2のチェックリスト、旧3ページの 301 の行き先の案、navbar「女流桜花」を `/ouka/` への1段にする案〈houou/ と同じ。チャット側の案で未決〉、告知、「#519 を待つ」）、#519 に番号をコメントする。`docs/notes/` に仕様（例 `docs/notes/ouka-top.md`。houou-top.md と同じことは書かず違いだけ）を書き、handover.md の6章の表に1行、`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」と書く

止まる条件

* 0章の「貼る時機」が満たされない（AMP がマージされていない）
* 「桜花」タブを2回読んで行数が変わる（行数を書いて止まる）、または集計元の見出しが前提と違い読み替えで済まない
* 同じ主題の open issue（#519・#516 以外）がある、または #519 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数（`generate_houou_pages.py`・`lib/ranking.py`・`lib/results.py`・`assets/houou.js`・`assets/houou_race.js`・`assets/houou_ask.js`・`style.css` の houou/ の節・`workers/ask/` の src と生成・`regenerate.py` の `OUTPUT_OVERRIDES`・`_redirects`）を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* houou/ の生成物に、改名した JS を指す行以外の差分が出て直しで消せない。ほかの生成物（旧ページ・title/・jpml_pros など）に差分が出て直しで消せない
* Worker の鳳凰戦の既存のテストが通らなくなり、直しで戻せない
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、仮置きしたことの一覧（何を・どう仮置きしたか・ほかの案）を、(1) 見た目の値（プレビューを見れば判断できるもの）と (2) 見ただけでは判断しにくいもの（URL・数え方・規定・文言・名前・質問欄の範囲など）に分けて書く。ランキングの突合で違った行の一覧、要るワークフローの変更、マージ後に確かめること（質問欄の実際の問い）も書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（トップ・個人成績〈タイトル保持者・在籍の長い選手・進行中の期の行がある選手〉・ランキングの通算・順位変動）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-OUK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-OUK-01 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致
- 貼る時機: `git log origin/cloudflare --grep="CHAT-1017-AMP-03"` は4件（6e4edb9 など）、`origin/work/1016-amp` は cloudflare の祖先（マージ済み）
- Chat-Ref・識別子 `OUK` の重複なし（全ブランチのコミット・docs/logs の履歴、`--unshallow` の後）。`work/OUK` はローカル・リモートとも無く、origin/cloudflare（8988cc8）から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はそろっている

### 1. 調べたこと

issue:

- #519（open、状況: 待ち）: 本文は「#518 の部品で ouka/ を出す」。着手中のコメントは無い（コメントは横断レビューの所見 1件: G2-10・G3-09・G3-12。新ページで再発させないか確かめる）
- #516（open、状況: 保留、コメント無し）: 順位変動。#519 に取り込む
- 同じ主題の issue（「女流桜花」「ouka」「桜花 ページ ランキング 個人成績」で検索、クローズ済みを含む）: #519・#516 のほかは #376（昇降級ボーダーの予想）・#369（昇降級のヒートマップ）・#234（前回比）で、主題が違う。止まる条件に当たらない

未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）:

| ブランチ | 中身 | 重なり |
|---|---|---|
| work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1017-hou | ログだけ | なし |
| work/1017-amp（AMP-06、判断待ち） | jpml_pros の鳳凰の列。`generate_houou_pages.py` に関数 `current_league_table()` を `current_league()` の後ろに足す（新しい関数。既存の関数は変えない）。`style.css` は `#pros_table` の幅 | 同じファイルの別の箇所。止まらない。取り込むときは、足した関数が使う `load_rows`・`houou_tab.fetch_rows(GROUP_HEADERS)`・`leagues_data()`・`current_leagues()`・`upcoming_term()` を鳳凰戦の既定のまま呼べるように残す |
| work/1017-pad（PAD-02） | `lib/page.py` の `PAGE_TEMPLATE`（表のページの body のクラス）、`style.css` の固定ナビの節 | 同じファイルの別の箇所（houou/ の節でない）。止まらない |
| work/1017-mck-367-all | `convert_x_posts.py` | なし |

「桜花」タブ（`lib/sheets.fetch_table`、`generate_ouka_leagues.py` と同じ経路）を2回読んだ:

| 項目 | 値 |
|---|---|
| 行数 | 1回目 1,719行・2回目 1,719行（同じ） |
| 列の見出し（17列） | 名前・期・リーグ・リーグキー・順位・結果・合計・第1節〜第8節・表示・備考（「前後」「組」は無い） |
| 表示 | Y 1,580行・N 139行（21期の138行と、16期 A の早川林香の1行） |
| 期の範囲 | 第1期〜第21期（表示 Y は第1期〜第20期） |
| リーグ（リーグキー） | 桜花(0)・A(1)・B(2)・C1(3)・C2(4)・C3(5)。C1 は第7期〜、C2 は第15期〜、C3 は第19期〜 |
| 組 | 列が無い（組なし） |
| タイトル保持者の行 | 「桜花」20行（第2期〜第21期に1行ずつ。前の期の優勝者。順位・合計・節は空。同じ期のほかのリーグには出ない＝鳳凰位と同じ扱い）。第20期・第21期は清水香織 |
| 結果 | 1,719行すべて空（2026-10-15 に #571 のために列を残した。昇級・降級の値は無い） |
| 節の数 | 第1〜8期は6節、第9〜14期は7節、第15〜20期は8節（表示 Y の最大） |
| 節の成績が無い期 | 無い（節が空の Y の行は「桜花」の行だけ） |
| 進行中の期 | 第21期（138行、すべて表示 N、順位・合計・節は空） |
| 選手 | 262名（表示 Y 248名、うち「プロ」に在籍 163名）。第21期の138名のうち在籍 137名 |
| Ampai のブック | 「女流桜花」は第21期の A・B・C1・C2・C3（前後は空、組なし） |

`league_ranking.js`（＝`lib/ranking.py` の `CONTESTS["桜花"]`）の桜花の設定:

| 設定 | 値 |
|---|---|
| 通算得点/期・期単位浮き率の規定（min_seasons） | 5期以上 |
| 節単位浮き率の規定（min_sections） | 25節以上 |
| 期連続浮き回数の最少（旧の表示の下限） | 3回 |
| 節連続浮き回数の最少（旧の表示の下限） | 6回 |
| 表示件数（旧） | 通算得点 50・通算得点/期 30・期単位浮き率 23・節単位浮き率 20・ほか 100 |
| 部門 | 旧 `ouka_ranking.html` は8部門（「連続昇級回数」が無い。「結果」が空のため） |

着手のコメント: #519（着手中）・#516（#519 に取り込む）に書いた。

### 2. 作ったもの

(a) 共通化と JS の改名（0780ba3）

- `assets/houou.js` → `assets/league.js`、`assets/houou_race.js` → `assets/league_race.js`（`git mv`）。ページの `<script type="application/json" id="league_contest">` があればリーグ・期の書き方・履歴のキー・題名の接尾辞を読み、無ければ鳳凰戦の既定のまま動く。`assets/houou_ask.js` も同じ設定でチップの段を引き、`data-contest` があれば問いに大会を添える
- `generate_houou_pages.py`: 大会の設定を `Contest`（`HOUOU`・`OUKA`）にし、`using(contest)` で `CONTEST`・出力先を切り替える。前期・後期の無い大会は全行を「後」にして通期のリーグと同じ扱い（画面は「21期」）。work/1017-amp が足す `current_league_table()` が使う関数（`load_rows`・`leagues_data`・`current_leagues`・`upcoming_term`）は鳳凰戦の既定のまま呼べる形に残した
- `generate_houou_race.py`: `build_periods()` にリーグ・節の見出し、`render_page()`・`pick_default()` に「結果を求めるか」の引数を足した（既定は今のまま）
- `lib/results.py` の `standings()` にリーグの引数（既定は鳳凰戦）
- houou/ の生成物の差分は、改名した JS を指す14行だけ（13ページの `league.js`・順位変動の `league_race.js`）。旧 `houou_race.html` は差分0
- 直したテスト: `test_houou_ask.py` の node への入力を `setEncoding('utf8')` で読む（全員の成績を流すとチャンクの境目で名前が割れ、1回目の全テストで `KeyError: '今村壮吾'` になった。もう一度回すと通る。直した後は3回続けて通った）

(b) ouka/（d8443ed）

- `scripts/generate_ouka_pages.py`（`using(OUKA)` で同じ生成を動かす入口）、`regenerate.py` の `"ouka_pages": "ouka/"`
- 生成: トップ・個人成績・ランキング8部門・順位変動・`search.json`・`players/leagues.json`・`ask-players.json`・成績 JSON 164ファイル・`race/<期>-2.json` 20ファイル。合計 198ファイル・919,857バイト。全ページ noindex、ページの中に「鳳凰」の字は無い
- 生成のログ: 選手 248名（在籍 164名）、名寄せ 樫野凪 → かしのなぎ（2行）、進行中の期 21期・在籍者 124名（Ampai のリンクあり 123名。無い1名は桜花の清水香織）、ランキングは「第20期終了時点」
- ランキングの表示対象（自動ルール）: 通算 100件・期平均 47件（全件）・期最高 100件・期浮き率 94件（全件）・期連続浮き 56件（57〜135位の 2 の群れを外す）・節最高 100件・節浮き率 95件（全件）・節連続浮き 104件（45〜104位の 5 を含める）
- `style.css` に `.is-ouka` のリーグの色、`img/ogp/ouka/index-white.png`（`build_ogp_image.py`、フォントは noto-cjk から取得。同じ手順で `img/ogp/houou/index-white.png` を `--check` すると「一致」）、`_redirects` に `/ouka`・`/ouka/ranking/` の 301 と 200 の3行、`docs/notes/redirects.md` に `/ouka/ranking/` の行（`test_redirects_inventory.py` が求める）

(c) Worker（e542cdc）

- `scripts/generate_ask_data_ouka.py` → `workers/ask/data-ouka.json`（83,758バイト。選手 248名・決勝 20期・放送対局 9ページ、「contest」に大会の名前・リーグ・部門）。`data.json` は差分0
- `query.mjs` の文言・リーグを `data.contest`（無ければ鳳凰戦）から、`ask.mjs` は問いの `contest` でデータ・指示・出力の形を選ぶ。知らない大会は 400（数えない）。記録の型は「ouka:league」の形
- テスト: `python3 -m unittest discover -s scripts/tests` 1311件 OK、`node --test 'workers/ask/test/*.test.mjs'` 42件 OK（鳳凰戦の既存の36件はそのまま通る。桜花は `ouka.test.mjs` の6件）

### 3. 確かめ

ランキングの突合（旧 `ouka_ranking.html` を headless Chrome で開き、Google Charts が出した表を取り出した。8部門）:

旧ページは退会者を含む・表示件数が旧の上限（50・30・23・20・100）。旧の表から在籍者でない選手を除いて順位を付け直し、新の表の先頭と比べた。

| 部門 | 旧の件数 | 在籍者でない（除いた） | 新の先頭と一致 | 不一致 |
|---|---|---|---|---|
| 通算 | 50 | 8 | 42 | 0 |
| 期平均 | 30 | 3 | 27 | 0 |
| 期最高 | 100 | 25 | 75 | 0 |
| 期浮き率 | 23 | 1 | 12 | 10（下） |
| 期連続浮き | 62 | 6 | 56 | 0 |
| 節最高 | 100 | 25 | 75 | 0 |
| 節浮き率 | 20 | 2 | 18 | 0 |
| 節連続浮き | 51 | 7 | 44 | 0 |

期浮き率の不一致は齋藤麻衣子の1件から後ろが1つずつずれたもの: 旧は 0.684（12位）、新は 0.267（15期中4期浮き）。旧 JS の既知の誤り（名前の順で最後の選手に1人前の選手の率が入る。`lib/ranking.py` の先頭の説明）で、直前の黒沢咲の 0.684 が入っていた。新を正とする。名寄せ（樫野凪）で変わった行は無かった。

表示（ローカルの `python3 -m http.server` で ouka/ を開いた。headless Chromium）:

- 375×812・1280×800・1920×1080、375px の字の大きさ 150%・200% で、トップ・個人成績（清水香織の期別、二階堂亜樹のリーグ・部門別）・ランキング（通算）・順位変動の30通り。横のはみ出し 0、JS のエラー 0（質問欄の `/api/ask/status` は手元に無いので 404）
- 順位変動のシート: 期の行の下は「A・B・C」の1行（前期・後期の行と A・B の番号の行は出さない）。C を押すと「C1・C2・C3」の行が出て、C2 を選ぶと題名が「第20期 C2」、URL が `?term=20-2&league=C2`
- リーグのタブの再生（二階堂亜樹、24.5秒）のアイコンの画面内の座標（x, y / 幅・高さの比）: 375px は落ちた直後 (60, 341 / 0.16, 0.42)・寄った後 (90, 476 / 0.24, 0.59)・終わり (317, 342 / 0.84, 0.42)。1920px は (823, 453 / 0.43, 0.42)・(855, 595 / 0.45, 0.55)・(1099, 450 / 0.57, 0.42)。どれも画面の中


### 4. 確かめ（続き）と文書

- `python3 scripts/regenerate.py all` を作業ブランチ（9e1a255 の前）と、cloudflare のクローン（8988cc8）で同時に回した。どちらも EXIT 0・作業ツリーの差分0（データは着手時と同じ）。cloudflare との差の生成物は houou/ の13ページの JS を指す14行と、新しい ouka/・`workers/ask/data-ouka.json` だけ（旧 `houou_*`・`ouka_*`・title/・jpml_pros などは差分0）
- プレビュー（work/OUK の別名）: ouka/ の4ページ・`assets/league.js`・`league_race.js`・houou/ のトップが手元のファイルと一致（`cmp`）。`/ouka` → `/ouka/`、`/ouka/ranking/` → `/ouka/ranking/total/` の 301。個人成績（二階堂亜樹）を headless Chrome で開いて JS のエラー 0。Workers Builds の check-run「Workers Builds: mj」は d8443ed で success（その後のコミットは scripts・workers・docs だけで、ビルドの run は付かなかった。配信のファイルは d8443ed と同じ）
- check「check」（`check-assets.yml`）は failure: 「許可リストに無い最上位の項目が配信対象として検出されました: ouka」。指示どおりワークフローは変えていない
- 公開の issue #591 を起票し、#519 に番号をコメントした
- 文書: `docs/notes/ouka-top.md`（新規、houou/ との違いだけ）、handover.md の6章の表に1行、`docs/notes/static-generation.md`「ページの一覧」に ouka/ の行（noindex・メニュー未掲載、公開は #591）と `ask_data_ouka`、`houou-top.md`（JS の改名・「女流桜花への展開」）・`ask-worker.md`・`ogp.md`・`design.md`・`houou-race.md` の JS の名前、`redirects.md`、`docs/decisions/houou.md` に決定（2026-10-14 の「houou.js は今のまま確定」に置き換えの印）

## 報告

- 状態: 判断待ち
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1017-OUK-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページ: ouka/ のトップ・個人成績（清水香織・二階堂亜樹）・ランキング（通算・期平均）・順位変動。この版の印: 個人成績の清水香織の名前の横のチップが桜色の「桜花」、期別の一番上が「21期 桜花」
- マージ: 未（平野さんの判断待ち。プレビューの確かめと仮置きの決定の後、別のマージの指示で）
- issue: #519（着手中のコメント・#591 の番号）、#516（#519 に取り込むコメント）、#591（公開の issue を起票）
- 判断が必要なこと:
  - (1) 見た目の値（プレビューを見れば判断できるもの）
    - 桜花のチップと帯の色: #be185d（濃い桜色、白字 6.04:1）。ほかの案: 鳳凰位と同じ #9f1239、もっと淡い桜色に黒字
    - リーグの色: A #fa5d27・B #ef972e（houou/ の A・B の「中」）、帯は C1 #ccaa2d・C2 #e8c133・C3 #eed066、チップの C1〜C3 は C2 の色で1色。ほかの案: houou/ の A1・B1 の濃い側、C の3段もチップで分ける
    - ランキングの部門の格子は8部門で3段×3列の最後の1マスが空く。ほかの案: 4列×2段、空きに何かを置く
    - 結果のチップ・通算のグラフの昇級・降級の丸・順位変動の昇級・降級・決定戦の帯が無い（「結果」の列が空のため）。ほかの案: リーグの移り（次の期のリーグ）から昇級・降級を導いて出す
    - 順位変動の選ぶシートは「A・B・C」の1行と、C のときだけ「C1・C2・C3」の行。前期・後期の行は出さない
    - 最近見た選手が無いときの1件は「桜花」の印で最新の期の桜花（清水香織）
    - OGP 画像 `img/ogp/ouka/index-white.png`（白地・黒字の「女流桜花」＋「ryoei.pro」）。ogp.md の「文字ベースの画像は4種類」に5種類目を足した（navbar を1段にする案が前提）
  - (2) 見ただけでは判断しにくいもの
    - JS の名前: `assets/league.js`・`assets/league_race.js`。`houou_ask.js`・`houou_ask.css`・CSS のクラス名（`.mj-houou-*`）・要素の id（`houou_search` など）・ページの JSON の id（`houou_current`・`houou_upcoming`）は変えていない（houou/ の生成物の差分を JS の行だけにするため）。ほかの案: これらも `league_*` にそろえる（houou/ のページの差分が増える）
    - 大会の設定の渡し方: ouka/ のページだけに `<script type="application/json" id="league_contest">`、枠に `is-ouka` のクラス、質問欄に `data-contest="ouka"`。houou/ は何も持たず JS の既定で動く
    - 生成の作り: `generate_houou_pages.py` の `Contest`（`HOUOU`・`OUKA`）と `using()` でモジュールの `CONTEST`・出力先を切り替え、`generate_ouka_pages.py` は入口だけ。ファイル名は `generate_houou_pages.py` のまま（work/1017-amp が同じファイルに関数を足しているため、名前を変えなかった）
    - `regenerate.py --changed` は `generate_houou_pages.py` が変わっても `ouka_pages` を選ばない（`scripts/lib/` の変更なら全ページ）。週次の `all` では作り直される
    - ランキングは8部門（連続昇級回数なし）。表示対象は houou/ と同じ自動ルール（旧の件数の上限 50・30・23・20 は使わない）。規定は 5期・25節（決定どおり）
    - 前期・後期が無い大会を「全行が後期・全リーグが通期」として扱った（期のキー `21-2`、URL `?term=20-2&league=A`）。ほかの案: 期のキーを `21` にする（JS と生成の分岐が増える）
    - 順位変動の対象の期は第1期〜第20期（節の成績のある期すべて）
    - 文言: title「<ページ> | 女流桜花 | 日本プロ麻雀連盟 | ryoei.pro」、トップの h1「女流桜花」、説明文は houou/ の大会名を替えたもの、トップのランキングの説明「通算・期最高など8部門」、質問欄の例「例：20期の女流桜花は？」
    - 最近見た選手のキー `mj:recent-players:ouka`
    - 質問欄の範囲: ouka/ の問いは女流桜花のデータ（`data-ouka.json`）と女流桜花の指示だけで答え、鳳凰戦の問いを ouka/ で聞かれても「答えられません」（指示の none）。鳳凰戦の問いへの答え方は変えていない。D1 の記録の型は「ouka:league」の形（答えられなかった問いは「ouka:」）。ほかの案: 表に大会の列を足す（schema の変更が要る）
    - Worker の決勝の名前は「女流桜花決定戦」、決勝の人数の上限の検査は鳳凰戦と同じ4名（20期とも収まった）
  - ランキングの突合で違った行: 期浮き率の齋藤麻衣子（旧 0.684・12位、新 0.267）の1件。旧 JS の既知の誤り（1人前の選手の率が入る）で、新を正とした。ほかの7部門は在籍者だけで付け直すと一致。旧は退会者を含む（通算 8件・期平均 3件・期最高 25件・期浮き率 1件・期連続浮き 6件・節最高 25件・節浮き率 2件・節連続浮き 7件）
  - 要るワークフローの変更: `check-assets.yml` の許可リストに `ouka` を足す（今は作業ブランチの check が失敗する。失敗通知が届くが、この指示では対応不要）。#519 のマージ前に足すか、公開（#591）で足すかの判断
  - このマージでよいか（プレビューの確かめと上の仮置きの決定の後、別のマージの指示で）
- 未確認の項目:
  - 質問欄の実際の問い（Claude を呼ぶ確かめ）: プレビューには `/api/ask` が無く（Worker のルートは本番の ryoei.pro だけ）、確かめていない。マージ後に本番の ouka/ で1問（例「20期のAリーグは？」）を聞き、答えと D1 の記録の型「ouka:league」を見る。Worker `mj-ask` の Workers Builds が `data-ouka.json` を含めてビルドされるかもマージ後に見る
  - 平野さんの実機（iPhone の文字の拡大・リーグのタブの再生）での見え方
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f7b8b94）: https://github.com/retroeater/mj-logs/tree/main/guide/6f7b8b94

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f7b8b94/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
