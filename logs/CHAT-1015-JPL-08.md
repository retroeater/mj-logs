# CHAT-1015-JPL-08

- 着手日時: 2026-10-15
- 対象issue: #523
- ブランチ: work/1015-jpl
- 着手時HEAD: 1854428a4

## 指示

【Claude作成】Claude Code 向け指示：JPL-07 の続き。WRC 2大会の新名称を ryoei.pro の表示すべて（過去の大会・予定・成績・動画の題名を含む）に効かせ、入力（YouTube 概要欄・連盟の予定表など）の旧名は読み替える（#523）。判断待ちで止まる
Chat-Ref: CHAT-1015-JPL-08
マージ: 判断待ちで止まる（表示の変化の範囲が広いため、差分の内訳とプレビューを平野さんが見てから決める。cloudflare へは入れない）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-jpl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1015-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1015-JPL-07.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-07 の `## 報告` の状態の末尾に ` / 続き: CHAT-1015-JPL-08` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

## 目的
WRC 2大会の改称を、ryoei.pro の表示すべてに効かせる。JPL-05 で入れた読み替え（`taikai_names` など）を、まだ旧名が出ている所（JPL-07 の「mj_放送対局」の仮の予定、#523 本文の「残り」）まで広げる。元のデータ（YouTube の概要欄・題名、連盟の予定表、シートの旧名の行）は書き換えず、ryoei.pro 側で読み替える。

### 決定（2026-10-15、平野さん）
- 「JPML WRCリーグ」「JPML WRC-Rリーグ」は新名称「WRCリーグ」「WRC-Rリーグ」が適用された。今後は、過去の大会・予定・成績なども含め、ryoei.pro での表示はすべて新名称にする
- YouTube の概要欄など（過去の分）は変わらないので、ryoei.pro 側で読み替える
- （JPL-07 の「判断が必要なこと」2点目〈仮の予定の件名〉は、上の決定で表示を新名にそろえる。案 (a)〜(c) の選び方は下の前提）
- video_mtsuku の再生成の停止（JPL-07 の1点目）は MTK のチャットで対応中。この指示では扱わない

### 前提（チャット側。平野さんの決定ではない）
- 方針の案: 入力（連盟の予定表の件名、YouTube の題名・概要欄、シートの旧名の行）は旧名・新名のどちらでも同じ大会に当たるようにし、出力（ページの文字・meta・OGP・`search.json`・`llms.txt`・カレンダー「mj_放送対局」の件名と説明・予定表シート【2】の「大会」列など ryoei.pro が書くもの）は新名にそろえる。読み替えは JPL-05 の仕組み（`taikai_names.current_title()` など。名前は実物で確かめる）に寄せ、別の対応表を増やさない
- 動画の題名（YouTube の題名を /live・title/ の期ページなどにそのまま出している所）も、表示では旧名を新名に置き換える（決定の「すべて新名称」に含まれるとみている。置き換える所と件数を報告に書く。要確認）
- 予定表シート【2】の「大会」列は ryoei.pro が書く出力なので新名にする（JPL-07 の案 (b) に近い。`yotei.EVENTS` の照合は旧名・新名の両方に当たるように）。カレンダーの件名は仮の予定・枠の予定とも新名（案 (a) を含む）
- #523 本文の「残り」の手書きのページ・メタ（`apply_page_meta.py`・`llms.txt`・`index.html`・wrc_*.html など）の「JPML WRC」も新名に直す（JPL-05 の報告の列挙。実物で足し引きする）
- 新しい名前に「JPML」が付かないことで WRC（リーチ麻雀世界選手権、slug wrc）と紛れる箇所が出たら、置き換えずに一覧にして止まらずに報告する（表示の文言の判断は平野さん）
- slug・URL（jwrc・jwrc-r、wrc_*.html）は変えない

## 手順
1. 洗い出す: リポジトリ全体（生成スクリプト・手書きのページ・テンプレート・テスト・docs 以外の静的ファイル）と生成物で「JPML WRC」「JPML WRC-R」を検索し、入力の照合／出力の表示／手書きの文言／テストに分けて、ファイルと件数を表で `## 経過` に書く。カレンダー「mj_放送対局」と予定表【2】で旧名が出ている件数も書く
2. 直す: 上の前提の方針で、入力は旧名・新名の両方に当たり、出力は新名になるようにする。テストを足す（旧名の入力が新名で表示される、新名の入力も同じ大会に当たる、WRC〈世界選手権〉と混ざらない、slug・URL が変わらない）。`python3 -m unittest discover -s scripts/tests` を通す。影響のある生成を走らせ、origin/cloudflare との差分を種類に分けて書く（ページ数、「JPML WRC(-R)リーグ」→「WRC(-R)リーグ」の置き換えの件数、それ以外の差分は全件）。カレンダー同期と予定表【2】の書き込みは dry-run（書き込みなし）で、変わる予定・行の件数と例を書く
3. 確かめる: Workers Builds のプレビュー（上限15分）を headless Chromium で開き、title/jwrc/・title/jwrc-r/ の入口と期ページ1つずつ、/live の WRC の回1つ、wrc_*.html、index.html の該当箇所の表示を確かめて書く。平野さんに見てもらう URL の一覧（確認用 URL からの相対パス）を報告に書く。docs/notes/title-pages.md（と /live・予定表の文書）に方針を短く書き、docs/decisions/title.md に上の「決定」を足す。#523 に結果をコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）

## 止まる条件
- 0章で JPL-07 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
- テストが通らない、または生成が止まる
- 差分に「旧名→新名の置き換え」と生成日時など毎回変わるもの以外が出て、シートの変化で説明できない（全件書いて止まる。直さない）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、変えるファイルの同じ関数・行を変えている、または取り込みで衝突する（重なるファイルだけ外して残りを進め、外したものと理由を報告に書く。全部が重なるときだけ止まる。MTK のブランチの mtsuku 関係のファイルは触らない）
- cloudflare への push が求められる状況になった（しない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、マージの可否と、見てもらう URL、WRC（世界選手権）と紛れる箇所の一覧（あれば）を書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JPL-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JPL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- Chat-Ref: `CHAT-1015-JPL-08` のコミットは0件（同じセッションの続き）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。「指示」欄の末尾は指示文の最後の行と一致
- ブランチ: `origin/work/1015-jpl` は origin/cloudflare の祖先（マージ済み）、ローカルも祖先 → `git merge --ff-only origin/cloudflare`（1854428a4）
- JPL-07 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1015-JPL-08` を足した
- #523: Open。着手中コメントはこのセッション（JPL-04〜07）のものだけ → 着手中コメントを残した
- 未マージのブランチとの重なり: 変えるファイルを触っているのは `origin/work/MTK` の `scripts/lib/wayhome.py` だけで、MTK が変えているのは `build_card_thumb_html()` と、新しく足した `BACK_ICON_SVG`・`build_filterbar_html()`（Mつく用）。
  私が変えるのは `to_rows()` だけで同じ関数・行ではないため、外さずに進めた（MTK の mtsuku 関係のファイルは触っていない）

### 1. 洗い出し（origin/cloudflare 1854428a4、docs/ 以外。「JPML WRC」「JPML%20WRC」「JPMLWRC」）

| 分類 | ファイル | 件数 | 扱い |
|---|---|---|---|
| 入力（元のデータ） | `data/live_channel_raw.jsonl`（層1、YouTube の題名・概要欄） | 562 | 変えない（入力） |
| 入力の照合 | `scripts/lib/live_extract.py`（`KW`・`WAR_STRIP`・`STAGE_ORDER` の大会名） | 5 | 照合のパターンは旧名・新名の両方に当たる。出す大会名（/live の【2】の「タイトル戦」）を新名に |
| 入力の照合 | `scripts/lib/yotei.py`（`EVENTS` の `("WRCリーグ", "JPML WRCリーグ")` など） | 1 | 語（WRCリーグ・WRC-Rリーグ）は旧名・新名の両方に当たる。出す大会名（予定表【2】の「大会」）を新名に |
| 入力の照合 | `scripts/lib/wayhome_intake.py`・`scripts/lib/taikai_names.py` | 4・3 | JPL-05 の読み替え（対応表と照合）。そのまま |
| 出力の表示（生成物） | `rh_results_detail.html`（「成績詳細」ブック） | 238（80行） | `generate_rh_results_detail.py` の行で新名に |
| 出力の表示（生成物） | `video_live.html`（「対局」ブック） | 756（252行） | `generate_video_live.py` の行で新名に |
| 出力の表示（生成物） | `wayhome/` の20ページ＋一覧（「帰り道」ブックの題名） | 136 | `wayhome.to_rows()` で新名に |
| 出力の表示（生成物） | title/・live/・ask | 0 | JPL-05 で新名になっている |
| 手書きの文言 | `index.html` 2・`llms.txt` 2・`scripts/apply_page_meta.py` 5・`wrc_ranking.html` 4・`wrc_results.html` 5 | 18 | 「…リーグ」を伴う10か所を新名に。「JPML WRC」単独の8か所（apply_page_meta のコメント1を含む）は世界選手権と紛れるため残した |
| コメント | `scripts/generate_title_pages.py`（帯の例「JPML WRCリーグ 第14期」） | 1 | 新名に |
| テスト | `test_yotei.py` 10・`test_taikai_names.py` 13・`test_live_calendar.py` 4・`test_live_candidate.py` 3・`test_write_yotei_sheet.py` 3・`test_wayhome_intake.py` 2 | — | 入力として旧名を使うものはそのまま。出力の期待値3件を新名に（下） |

- カレンダー「mj_放送対局」: 今後の予定で旧名が出ているのは、予定表由来の仮の予定 8件（JPL-07 で確認）。枠の予定の件名は JPL-05 から新名
- 予定表【2】: 「大会」が旧名の行 257（JPML WRC-Rリーグ 128・JPML WRCリーグ 129。今後の分は 67）。「件名」は連盟の予定表の写しなので入力として変えない
- /live の【2】（`live_layer3.fetch_layer2()`、14,194行）: 「タイトル戦」が旧名の行 402（JPML WRCリーグ 272・JPML WRC-Rリーグ 130）

### 2. 直したもの（コミット cf61e703a）

- `scripts/lib/live_extract.py`: `KW`・`WAR_STRIP`・`STAGE_ORDER` の大会名を新名に（パターン `r"WRC-R"`・`r"JPML\s*WRCリーグ|WRCリーグ|WRC"` などは変えないので、旧名・新名の題名とも当たる）
- `scripts/lib/yotei.py`: `EVENTS` の WRC 2大会の出す名前を新名に（語は変えない）
- `scripts/lib/live_calendar.py`: 枠の件名（`clean_title()`）と仮の予定の件名（`yotei.strip_title(row["件名"])`）を `taikai_names.current_title()` に通す
- `scripts/lib/wayhome.py`: `to_rows()` で「タイトル」「紹介文（補正用）」を `current_title()` に通す
- `scripts/generate_rh_results_detail.py`・`scripts/generate_video_live.py`: `build_row_html()` で大会名・題名を `current_title()` に通す
- 手書き: `index.html`（「第3期WRC-Rリーグ」「4th Place in WRC-R League」）、`llms.txt`（2か所）、`apply_page_meta.py` と `wrc_ranking.html`・`wrc_results.html` の description・og:description（「WRCリーグについて」）
- テスト: 出力の期待値を新名に直した3件（`test_live_calendar` の仮の予定の件名、`test_live_candidate` のタイトル戦、`test_yotei` の `event_of`）。
  `test_taikai_names.py` に `OutputsTest` を足した（旧名・新名の題名・件名が新名の大会に当たる〈`live_extract.war_of`・`yotei.event_of`〉、世界選手権と混ざらない、カレンダーの件名、帰り道の行、成績詳細・動画の行）。
  `test_yotei` に新名の件名と世界選手権の確かめを足した。修正前のコード（origin/cloudflare の scripts を scratchpad に展開）では新しいテストが7件落ち、修正後は `python3 -m unittest discover -s scripts/tests` 全1,116件 OK

### 生成と差分（`python3 scripts/regenerate.py all`、HEAD〈修正前の生成物〉比、コミット 2件目）

- 飛ばしたのは `video_mtsuku` だけ（`NO_COLUMN: O`、JPL-07 で調べた「Mつく」シートの件。MTK で対応中）
- 差分 22ファイル: `rh_results_detail.html`・`video_live.html`・`wayhome/` 20ページと `wayhome/index.html`。すべて「JPML WRC-Rリーグ」→「WRC-Rリーグ」「JPML WRCリーグ」→「WRCリーグ」の置き換え（URL エンコード・JSON の形を含む）で説明でき、それ以外の差分は0件。
  置き換えの件数: rh_results_detail 238（WRC-R 84・WRC 154）、video_live 756（228・528）、wayhome 136（64・72）
- title/（384ページ）・live/（908ページ）・ask の data.json・sitemap は差分なし（JPL-05 で新名）。ページの増減なし。生成後の「JPML WRC」は rh_results_detail・video_live・wayhome とも0件

### 書き込みなしの見積もり（ワークフローは起動していない。シートを gviz で読み、修正前・修正後のコードで組み立てて比べた）

- 予定表【2】（`yotei.build_layer2()`、【1】は予定表シートの【1】、【3】は gviz）: 1,132行のうち 257行が変わる。変わる列は「大会」「分け」「開始の根拠」の文言だけ（例: 2024-12-05「第4期JPML WRC-Rリーグベスト16AB卓」の大会「JPML WRC-Rリーグ」→「WRC-Rリーグ」、分け「JPML WRC-Rリーグ その他」→「WRC-Rリーグ その他」、根拠の文の分けの名前）。
  内訳: WRC-R その他 123・WRC その他 124・WRC-R 決勝 5・WRC 決勝 5。開始(仮置き)・終了・枠の値は変わらない（分けの名前が両側で同じく変わるため）
- /live の【2】（`live_candidate.build_rows()`、層1 から）: 14,194行のうち 402行の「タイトル戦」だけが変わる（WRCリーグ 272・WRC-Rリーグ 130）。ほかの列は変わらない。修正前のコードの結果は今のシートの件数（272・130）と一致
- カレンダー（`live_calendar.build_desired()`、2,630件）: 予定の数・キーは変わらない。件名が変わるのは今後の仮の予定 8件（2026-12-03「第8期WRC-Rリーグベスト16AB卓」〜2027-02-07「第20期WRCリーグ決勝」）。
  枠の予定 77件は内部の大会の値（`event`、カレンダーには書かない）だけが変わり、件名は変わらない
- 実際の書き込みはマージ後の毎朝の update-live-channel.yml（/live の【2】・予定表【2】・カレンダー同期）で行われる

### 3. プレビュー（Workers Builds、6427fca83 の check-run success。URL は最終報告だけ）

headless Chromium（`/opt/pw-browsers/chromium-1194`、`--dump-dom`、`?v=jpl08a`）で開いた結果:

| ページ | `<title>` / h1 | 「JPML WRC」 |
|---|---|---|
| title/jwrc/ | WRCリーグ 歴代優勝者（第1期〜第19期） / WRCリーグ 歴代優勝者 | 0 |
| title/jwrc/19.html | 第19期WRCリーグ 決勝結果 | 0 |
| title/jwrc-r/ | WRC-Rリーグ 歴代優勝者（第1期〜第7期） | 0 |
| title/jwrc-r/7.html | 第7期WRC-Rリーグ 決勝結果 | 0 |
| live/jwrc-r/1/f.html | 第1期WRC-Rリーグ 決勝 | 0 |
| wayhome/0OPca1Bl5xk.html | 第18期WRCリーグ 橘来奈 | 0 |
| rh_results_detail.html | （WRCリーグ 154・WRC-Rリーグ 84） | 0 |
| video_live.html | （WRCリーグ 528・WRC-Rリーグ 228） | 0 |
| index.html | 「WRC-Rリーグ」1・「WRC-R League」1 | 0 |
| wrc_ranking.html | ランキング \| JPML WRC \| ryoei.pro（description は「WRCリーグについて」） | 2（title・og:title、残した所） |
| wrc_results.html | 成績詳細 \| JPML WRC \| ryoei.pro / h1「JPML WRC 成績詳細」 | 3（title・og:title・h1、残した所） |

- 見たのは DOM の文字まで（スクリーンショットの目視はしていない）。wrc_* の表は JS がシートから読み、DOM に描画されていた（約38KB・101KB）

### 記録

- docs/notes/title-pages.md「シートを直すときの注意」に方針（入力は両方、出力は新名、「JPML WRC」単独は残す）、docs/notes/yotei-sheet.md の件名の項に1文、docs/decisions/title.md に決定
- #523 に結果をコメントした（本文・状態・ラベルは変えていない）
- cloudflare へは入れていない（指示のとおり判断待ち）

## 報告

- 状態: 判断待ち
- ブランチ: work/1015-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1015-jpl/docs/logs/CHAT-1015-JPL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-jpl
- 確認用URL: プレビューあり（URL は最終報告）。確かめたのは title/jwrc/・title/jwrc/19.html・title/jwrc-r/・title/jwrc-r/7.html・live/jwrc-r/1/f.html・wayhome/0OPca1Bl5xk.html・rh_results_detail.html・video_live.html・index.html・wrc_ranking.html・wrc_results.html。この版で見える印: 成績詳細（rh_results_detail.html）の大会の列が「WRC-Rリーグ」
- マージ: 未（平野さんの判断待ち）
- issue: #523（結果のコメント。Open のまま）
- 判断が必要なこと:
  - マージしてよいか。差分は旧名→新名の置き換えだけ（生成物 22ファイル、コード・手書き）。マージ後の最初の毎朝の実行で、予定表【2】257行・/live の【2】402行の大会名と、mj_放送対局 の今後の仮の予定 8件の件名が新名に書き換わる（見積もり）。
    なお、マージの push で全ページの再生成が動き、video_mtsuku が「Mつく」シートの件で飛ばされてジョブが失敗表示になる（MTK の対応が済むまで。この変更とは無関係）
  - 見てもらうページ（確認用 URL からの相対パス）: `title/jwrc/`・`title/jwrc/19.html`・`title/jwrc-r/`・`title/jwrc-r/7.html`・`live/jwrc-r/1/f.html`・`wayhome/0OPca1Bl5xk.html`・`rh_results_detail.html`・`video_live.html`・`index.html`（ポートフォリオの第3期WRC-Rリーグ）・`wrc_ranking.html`・`wrc_results.html`
  - WRC（リーチ麻雀世界選手権）と紛れるため置き換えなかった箇所（「JPML WRC」単独の略記）: `wrc_ranking.html` の `<title>`・og:title「ランキング | JPML WRC | ryoei.pro」、`wrc_results.html` の `<title>`・og:title「成績詳細 | JPML WRC | ryoei.pro」と h1「JPML WRC 成績詳細」、`scripts/apply_page_meta.py` の同じ2つの文言とコメント「# ---- JPML WRC ----」。
    案: 「WRCリーグ」にする（例「ランキング | WRCリーグ | ryoei.pro」）/「JPML WRC」のまま。文言は平野さんの判断
- 未確認の項目:
  - プレビューの見え方の目視（DOM の文字だけを確かめた）
  - マージした場合、最初の毎朝の実行で予定表【2】・/live の【2】・カレンダーが見積もりどおりに変わるか、常設 issue「予定表の変化」（#481）に知らせが出るか
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66464371）: https://github.com/retroeater/mj-logs/tree/main/guide/66464371

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d12b1f2e.md
