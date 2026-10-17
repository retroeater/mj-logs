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

## 報告

- 状態: 作業中
- ブランチ: work/OUK
- ログ: https://github.com/retroeater/mj/blob/work/OUK/docs/logs/CHAT-1017-OUK-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/OUK
- 確認用URL:
- マージ: 未
- issue: #519・#516
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c52dc764）: https://github.com/retroeater/mj-logs/tree/main/guide/c52dc764

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c52dc764/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c52dc764.md
