# CHAT-1106-PFU-01

- 着手日時: 2026-11-06
- 対象issue: （起票予定）
- ブランチ: work/1106-pfu
- 着手時HEAD: 021cf833

## 指示

【Claude作成】Claude Code 向け指示：「良栄 > 牌譜」（rh_paifu.html）を廃止する（旧 URL は 404） Chat-Ref: CHAT-1106-PFU-01 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1106-pfu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1106-pfu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1106-pfu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
メニュー「良栄」の「牌譜」（`rh_paifu.html`）を廃止する。ページ・生成・再生成の対象・サイト内のリンクをすべて外し、旧 URL は転送せず 404 にする。
決定（2026-11-06、平野さん）

* 「良栄 > 牌譜」（`rh_paifu.html`）を廃止する
* 旧 URL は転送せず 404 にする（「番組 > English」〈#617〉・「連盟 > リンク」の廃止と同じ扱い。`/r/` へは転送しない）
* 下の「止まる条件」の確かめがすべて通れば、判断待ちにせずそのまま cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 牌譜へのリンクがある場所（mj-logs のガイド・ログで読んだもの。実物は要確認）:
   * 共通ナビ「良栄」のドロップダウン（成績・成績詳細・牌譜。CHAT-1104-TOP-11 のログ）と、新しいナビのメニューのシートの「良栄」のまとまり
   * r/ の一覧の「成績」の節（成績・成績詳細・牌譜の3リンク、`docs/decisions/ryoei.md` の CHAT-1103-RYO-07）と、空間の対局室の「牌譜の紙」（`RESULTS`、`docs/notes/ryoei-world.md`「触れる」）
   * 再生成の対象 `rh_paifu`（`regenerate.py --list` の32件、CHAT-1105-MFQ-02 のログ）・生成スクリプト・毎朝の再生成の一覧・sitemap・`llms.txt`・テスト
   * 文書: `docs/notes/static-generation.md`（型A の表）・`docs/notes/site-findings.md`（`img.youtube.com` の依存先・`?name=` の表）・`docs/decisions/README.md` と `ryoei.md` の分野名「良栄（r/・成績・成績詳細・牌譜、#389）」
* 対局室の「牌譜の紙」は、物ごと消す（仮置き。チャット側の案。消すと場面が寂しくなる・置き場所の番号が空くなど気になる点があれば、触れる先だけ外して物を残す方に変えてよく、どちらにしたかを報告に書く）
* 「牌譜」という語はほかのページ（用語・説明文など）にも出る。変えるのは良栄の牌譜ページを指すものだけで、ほかの「牌譜」は変えない
* 牌譜のページの元データ（スプレッドシートのタブなど）は、この指示では消さない。どのブック・タブから読んでいたかを `scripts/` の定数名とタブ名で報告に書く（ID・URL は書かない）。消すかは平野さんがマージ後に決める
* issue: 同じ主題の issue が無ければ「良栄 > 牌譜（rh_paifu.html）の廃止」で起票し、関係する #389 にリンクのコメントを残す（#389 の状態は要確認）。マージと本番の確かめが済んだら、起票した issue をクローズする
* mj-logs の actions/morning.md（2026-11-06 20:25 JST 時点）に出ている異常は「run-weekly.yml の予約実行の未実行」だけ（今回の変更と無関係）

手順

1. 洗い出し: 同じ主題の issue（クローズ済みを含む。検索語「牌譜」「rh_paifu」）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、同じ目的のものがあれば止まる。 無ければ issue を起票して着手中のコメントを残す。続けて `git grep -n -i -e 'rh_paifu' -e 'paifu'` と `git grep -n '牌譜'` をリポジトリ全体（CLAUDE.md・docs/handover.md を含む）で実行し、除外（`docs/decisions/` の過去の行・`docs/logs/`・`docs/notes/handover-archive-2026.md`）を除いた全件を「消す／書き換える／変えない（別の意味の牌譜）」に分けた表にしてログの `## 経過` に書く（件数と、ファイル名・行の一覧を全件）。
2. 削除と書き換え: 表の「消す／書き換える」を実施する（ページ・生成スクリプト・再生成の対象・毎朝の再生成の一覧・共通ナビとメニューのシート・r/ の一覧と対局室・sitemap・`llms.txt`・テスト・文書）。`_redirects` と `docs/notes/redirects.md` には足さない（404 のため）。 文書は `docs/notes/static-generation.md` の表に「旧 `rh_paifu.html` は廃止し転送しない（404、2026-11-06、#<起票した issue>）」の形で足し（`video_en` の行と同じ書き方）、`docs/decisions/` には「決定」節を足す。分野名の「牌譜」は外してよい（過去の行は書き換えない）。 共有の部品（ナビ・`scripts/lib/`）を変えたら CLAUDE.md「判断・作業の原則」のとおり全ページを再生成する。待つ時間は同じセッションで合計45分まで延長してよい。
3. 確かめとマージ: 下の「止まる条件」をすべて確かめ、通ればマージする。マージ後、本番で `/rh_paifu.html?v=<未使用の値>` が 404、トップ・r/・houou/ の共通ナビの「良栄」に牌譜が無いことを確かめる（本番への要求の UA に `mj-internal` を含める）。 15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする。確かめが済んだら起票した issue をクローズする（「状況:」ラベルがあれば外す）。

止まる条件

* 同じ目的の issue か未マージの work/ ブランチがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。対象が複数なら重なる対象だけ外して進め（外したものと理由を報告に書く）、全部が重なるときだけ止まる。この場合はマージしない
* 手順1の表の「消す／書き換える」を実施した後、`git grep` で良栄の牌譜ページへの参照が1件でも残る（除外は手順1と同じ）
* テストが1件でも落ちる（自分の変更で落ちると分かっているテストは直してよい）
* プレビューで `/rh_paifu.html` が 404 にならない（転送・200 になる）
* 差分に、牌譜の削除と、それによる再生成の変化（ナビ・r/ のリンクが外れたこと、sitemap・`llms.txt` の行が消えたこと）で説明できない行がある。 再生成のページの差分は、同じデータで origin/cloudflare のコードと作業ブランチのコードの両方で生成して比べ、牌譜の部分以外の差があれば全行をログに書いて止まる（シートの変化が混ざるため）
* r/ の重さが `docs/notes/ryoei-world.md`「重さ」の上限（最初の画面 600KB・`world.js` 150KB、gzip）を超える
* マージ後の check-run・手動実行が今回の変更で失敗する（無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（元データのブック・タブの定数名とタブ名、対局室の「牌譜の紙」をどうしたかを含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1106-PFU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1106-PFU-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0. 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref・`PFU` の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-PFU-'`・`docs/logs/CHAT-*-PFU-*.md` の追加履歴とも0件
- 作業ブランチ: `work/1106-pfu` はローカル・リモートとも無し → `git checkout -b work/1106-pfu origin/cloudflare`（着手時 HEAD 021cf833）

### 手順1: 洗い出し

- 同じ主題の issue（検索 `is:issue 牌譜`・`is:issue rh_paifu`、クローズ済みを含む）: 廃止を扱うものは無し（近いのは #197「rh_paifu.html のリンクの下線を消す」closed・#531「rh_links.html の廃止」closed で、目的は別）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）: 1009-swp-526・1011-swp-hnd・1011-swp-nav（ログの追いのみ）・1015-skd-notes（ログのみ）・1101-prp-pub（wayhome の主人公名のリンク。cloudflare と大きく乖離）・1103-ryo5（r/ の写真調査。scripts/survey_ryoei_photos.py・docs/decisions/ryoei.md の末尾）・1106-dcr（辞書ページの見た目、ログのみ）。同じ目的のものは無し
- #389 は open（題は「ポートフォリオ「校正」: 過去に校正した書籍の一覧を作る」。`docs/decisions/ryoei.md` の分野名が #389 を挙げているので関係としてリンクのコメントを残した）
- 起票: #673「良栄 > 牌譜（rh_paifu.html）の廃止」。着手中のコメントと #389 へのリンクのコメントを残した
- 元データ: `scripts/generate_rh_paifu.py` の定数 `SPREADSHEET_ID`（`scripts/check_sheets_reader.py` の BOOKS で「mj_ryoei」と呼ぶブック。成績・成績詳細と同じブック）の `SHEET_NAME = "牌譜"` シート。消さない

`git grep -n -i -e 'rh_paifu' -e 'paifu'` 55件と `git grep -n '牌譜'` 96件（除外: `docs/logs/`・`docs/notes/handover-archive-2026.md`）を行で重ねると141行。うち `docs/decisions/ryoei.md:12`・`:55` は過去の行で除外 → **139行**。CLAUDE.md・docs/handover.md には0件。

| 分類 | ファイル: 行 | 件数 | 内容 |
|---|---|---|---|
| 消す | rh_paifu.html: 6,7,11,12,13,35,38,43,44,46,115 | 11 | ページ本体（ファイルごと削除） |
| 消す | scripts/generate_rh_paifu.py: 2,5,10,21,25,28,29,30,31,32,35,39,40,43,44,55,68,69,70 | 19 | 生成スクリプト（ファイルごと削除。再生成の対象・毎朝の再生成は `regenerate.py` が `generate_*.py` を glob するので、これで外れる） |
| 消す | navbar.js: 58 | 1 | 共通ナビ「良栄」の項目（新しいナビのメニューのシートは `mjNavBand()` がここから読むので同時に外れる） |
| 消す | r/index.html: 59 / r/world-data.json: 1 | 2 | r/ の一覧の「成績」の節のリンク・空間のデータ（生成物。generate_ryoei_pages.py の `RESULTS` から） |
| 消す | scripts/generate_ryoei_pages.py: 86 | 1 | `RESULTS` の牌譜の行 |
| 書き換える | scripts/generate_ryoei_pages.py: 269 | 1 | 「成績・成績詳細・牌譜へのリンク」の docstring |
| 消す／書き換える | assets/ryoei/world.js: 911,995,996,1003,1008 | 5 | 対局室の「牌譜の紙」（1003・1008 の物ごと消す）と説明のコメント・分割代入 |
| 消す | sitemap-pages.xml: 300 | 1 | sitemap の行 |
| 消す | llms.txt: 72 | 1 | llms.txt の行 |
| 消す／書き換える | style.css: 158,264,433,459,673,678 | 6 | `img.videos`（rh_paifu だけが使う）・`#paifu_table` の節は消す。264・433・459 は共通の規則のコメントの中の例示を書き換える |
| 書き換える | scripts/lib/page.py: 3 | 1 | docstring の「今は rh_paifu・saikyo_mens など」 |
| 消す／書き換える | scripts/tests/test_simple_pages_check.py: 15,19,35,43,44,47,54,61 | 8 | `page.check_rows` の検査の題材を rh_paifu から saikyo_mens に替える・`GENERATE_PAGES` から外す |
| 消す | scripts/tests/test_safe_url.py: 54,56,60 | 3 | rh_paifu の行の検査（成績詳細の検査は残す） |
| 書き換える | scripts/tests/test_ryoei_pages.py: 145,147,166 | 3 | 一覧・空間のデータのリンクの期待値を2つに |
| 書き換える | docs/notes/static-generation.md: 262 | 1 | 型A の表から外し「旧 `rh_paifu.html` は廃止し転送しない（404、2026-11-06、#673）」を足す |
| 書き換える | docs/notes/site-findings.md: 51,244 | 2 | `img.youtube.com` の依存先から外す・`?name=` の表の行に廃止を書く |
| 書き換える | docs/notes/ryoei-world.md: 42,51 | 2 | 一覧・触れる の記述 |
| 書き換える | docs/decisions/README.md: 27 / docs/decisions/ryoei.md: 1 | 2 | 分野名から「牌譜」を外す |
| 変えない（時点の記録） | docs/notes/static-generation.md: 22,74 / docs/notes/site-findings.md: 257,288 | 4 | 2026-09 の移行・調査の経緯（`video_en` の廃止〈#617〉でも同じ行を残している） |
| 変えない（時点の記録） | docs/lighthouse-baseline.md: 180,228,235,264,266,558 / docs/gsc/2026-10-03/pages-unindexed.md: 59 / docs/review-followup-instructions.md: 115 | 8 | 日付つきの計測・GSC の写し・当時の指示文 |
| 変えない（別の意味の牌譜） | data/live_channel_raw.jsonl: 181,366,539,1154,1694,2655,3652,3909,3947,3973,5410,6599,6704,7237,7881,9270,9619,9638,9931,10017,10193,12057,12581,12857,13432,13823,14236,14294,14347,14537,14699,14961,15298,15311,15739,16065,16097,16249,16814,16930,16936,17030,17059,17120,17657,17804,18157 | 47 | YouTube の説明文（牌譜解説など） |
| 変えない（別の意味の牌譜） | dic/mahjong.json: 1 / docs/new-site-design.md: 274,276,277 / scripts/lib/live_extract.py: 108,109 / scripts/tests/test_live_extract.py: 2,110,112,117 | 10 | 用語・牌譜データ・牌譜解説 |

小計: 消す／書き換える 70・変えない 69（計139）。

「止まる条件」の「参照が1件でも残る」は、ページへのリンク・生成・検査の対象（生きている参照）と読む。static-generation.md の表に足す廃止の記録自体が `rh_paifu.html` を含むため、時点の記録の行（上の「変えない（時点の記録）」12件）は `docs/decisions/` の過去の行と同じ扱いにした。

## 報告

- 状態: 対応中
- ブランチ: work/1106-pfu
- ログ: https://github.com/retroeater/mj/blob/work/1106-pfu/docs/logs/CHAT-1106-PFU-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1106-pfu
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82fe7190）: https://github.com/retroeater/mj-logs/tree/main/guide/82fe7190

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82fe7190.md
