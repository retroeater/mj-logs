# CHAT-1014-JPL-01

- 着手日時: 2026-10-14
- 対象issue: #488
- ブランチ: work/1014-jpl
- 着手時HEAD: 59e33bdf0

## 指示

【Claude作成】Claude Code 向け指示：第1期「JPMLリーグ」を yotei.EVENTS に足す（#488） Chat-Ref: CHAT-1014-JPL-01 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらないこと（テストがすべて通り、変わるのが scripts/lib/yotei.py・scripts/tests/test_yotei.py・docs/（decisions・notes・logs）だけ） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。#488 が Open で、他セッションの着手中コメントが無いことを確かめる（Closed なら何もせず止まる）。着手中コメントを残す。

目的
連盟の公式予定表で大会名の「(仮)」が外れたので、2026-10-06 の決定（docs/decisions/features.md「第1期 JPMLリーグの追加〈#497・#488〉は…『(仮)』が外れてから行う」、broadcast-calendar.md「正式な大会名が決まってから yotei.EVENTS に足す〈#488〉」）に従い、「JPMLリーグ」を `scripts/lib/yotei.py` の `EVENTS` に足して #488 をクローズする。
決定（2026-10-14、平野さん）

* #488 のマージは承認する（テストが通り、変わるのが yotei.py・テスト・docs だけなら）
* 「タイトル戦」タブへの行の追加（#497）は平野さんが手で行う。#497 はこの指示では扱わない（別の指示 CHAT-1014-JPL-02）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-14 に、連盟サイト「年間行事予定」が埋め込む予定表（`yotei.CALENDAR_ID` のものと同じはず。要確認）を Google カレンダーの連携で読んだ。件名は「第1期JPMLリーグ第1節」〜「第1期JPMLリーグ第20節」（2026-10-06〜2027-01-21）、「第1期JPMLリーグトーナメント」（2027-02-06）、「第1期JPMLリーグベスト16AB卓」「…ベスト16CD卓」「…ベスト8AB卓」「…決勝」（2027-03）、「第2期JPMLリーグ第1節」（2027-03-01）。いずれも「(仮)」は無く、件名の更新は 2026-10-13 14:09〜14:15 JST。「JPMLリーグ 仮」の検索は0件
* 予定表には「第20期JPML WRCリーグ…」「第8期JPML WRC-Rリーグ…」もある。足し方によってはこれらが JPMLリーグ に誤って当たる・JPMLリーグ がこれらに当たるおそれがある（#488 の本文の「並び順に注意」の件、要確認）
* `EVENTS` は予定表の【2】の「大会」列と、YouTube の枠との結び付け（`event_of()`）に使われている（docs/notes/yotei-sheet.md。要確認）。YouTube の JPMLリーグ の放送の題名の書き方も実物で確かめる

手順

1. 確かめる: #488 の本文・コメント、`scripts/lib/yotei.py` の `EVENTS` と `event_of()` の当て方（順・部分一致か）、`scripts/tests/test_yotei.py`。層1・予定表【1】の今のデータで「JPMLリーグ」を含む件名・題名を洗い出し（予定表とYouTubeそれぞれ、件数と例）、上の前提と食い違えばログに書く（件名の書き方が前提と違うだけなら止まらず、実物に合わせる）
2. 足す: `EVENTS` に JPMLリーグ を足す（名前・位置は #488 と既存の書き方に合わせる）。テストに、第1期JPMLリーグの節・トーナメント・ベスト16・決勝・第2期が JPMLリーグ に当たること、JPML WRCリーグ・JPML WRC-Rリーグ の件名が今までどおりの大会に当たることを足す。テストをすべて走らせる。手元のデータで `event_of()` を足す前と後で比べ、大会が変わる件名・題名を全件（前→後）ログに書く。JPMLリーグ 以外が変わるなら止まる
3. マージする（CLAUDE.md「ブランチ運用」）。マージで動く自動処理（ワークフロー）と、次の毎朝の取り込み・カレンダー同期で変わる見込み（【2】の「大会」列、mj_放送対局 の予定）を報告に書く。#488 に結果（コメントの末尾に Chat-Ref の行）を書いてクローズする。docs/decisions/broadcast-calendar.md に1節足す（上の「決定」と、2026-10-13 に「(仮)」が外れたことをチャット側が確かめた旨）。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で #488 が Closed、または他セッションの着手中コメントがある
* `EVENTS` を足すと JPMLリーグ 以外の件名・題名の大会が変わる、またはテストが通らない
* scripts/lib/yotei.py・scripts/tests/test_yotei.py・docs/ 以外を変える必要が出た
* 未マージの work/ ブランチが yotei.py の `EVENTS`・`event_of()` かテストの同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` で確かめる。同じファイルの別の箇所なら止まらずログに書く）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- Chat-Ref: `git fetch --unshallow` の後、`CHAT-1014-JPL-01` のコミット・`-JPL-` のトレーラ・`docs/logs/CHAT-*-JPL-*.md` の履歴とも0件
- ブランチ: `work/1014-jpl` はローカル・リモートとも無し → `git checkout -b work/1014-jpl origin/cloudflare`（59e33bdf0）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- #488: Open、コメント2件（2026-10-06 着手の時期、2026-10-10 親の付け替え）。他セッションの着手中コメント無し → 着手中コメントを残した
- 0章ゲート: 未マージの `origin/work/*` で `scripts/lib/yotei.py`・`scripts/tests/test_yotei.py` を変えているブランチは無し（`git diff --stat origin/cloudflare...<branch>` で全件0）。CLAUDE.md・handover.md はこの指示では触らない

### 1. 確かめたこと

- `event_of()` は `normalize()`（NFKC・【メンバー限定】などの印を外す・漢数字の期）の後、`EVENTS` を先頭から見て**部分一致**で最初に当たったもの
- 「JPMLリーグ」は既存の語（`WRC-Rリーグ`・`WRCリーグ`・`A1リーグ`・`プロリーグ決勝` など）と重ならない。「JPML WRCリーグ」は空白を挟むため「JPMLリーグ」を含まず、「第1期JPMLリーグ…」は既存のどの語も含まない。並び順に依らない
- 予定表【1】（スプレッドシートの CSV、取得日時 2026-10-13T19:23 UTC の版、1,132行）: 「JPMLリーグ」を含む件名 32件（「(仮)」は0件）。
  第1期 第1〜20節（2026-10-06〜2027-01-21）・トーナメント（2027-02-06）・ベスト16AB卓（03-12）・ベスト16CD卓（03-13）・ベスト8AB卓（03-19）・決勝（03-29）、第2期 第1〜7節（2027-03-01〜03-29）。
  更新日時は 2026-10-13 05:09〜05:16 UTC（14:09〜14:16 JST）。チャット側の前提と一致。第2期は前提の「第1節」だけでなく第7節まである（前提と食い違うのは件数の書き方だけ）
- 【1】は `yotei.CALENDAR_ID` の予定表を取り込んだもの（docs/notes/yotei-sheet.md）。チャット側が読んだ予定表と件名・更新時刻が一致するので同じ予定表と見てよい（カレンダーIDそのものは照合していない）
- JPML WRC 系の件名は 263件（例「 第20期JPML WRCリーグ第10節」、先頭に空白あり）
- 層1（data/live_channel_raw.jsonl、18,387行・題名14,220種、最終取得 2026-10-13T19:20 UTC）: 「JPMLリーグ」を含む題名は**0件**。
  「JPML」を含む題名 421件のうち WRC 系以外は6件（「ジェンとガースのライクアプロ…[JPML]」「JPMLの世界NEWS」）で、いずれも JPMLリーグ ではない。
  2026-10-06〜10-09 の第1〜4節も YouTube の枠が層1に無い。YouTube での JPMLリーグ の題名の書き方は実物で確かめられなかった（全角の「ＪＰＭＬリーグ」は NFKC で当たる。「JPML リーグ」のように空白が入ると当たらない）
- 予定表【3】では JPMLリーグ の32行のうち掲載 Y は4行（第1期ベスト16AB卓・ベスト16CD卓・ベスト8AB卓・決勝）、残り28行は N。【3】の件名は追記時（2026-09-30）の「(仮)」付きのまま（2026-09-30 の決定どおり、今の件名は【2】で見る）

### 2. 足したこと

- `EVENTS` の `("WRCリーグ", "JPML WRCリーグ")` の直後に `("JPMLリーグ", "JPMLリーグ")`（JPML の他の大会の隣。#450 の行のコメントにあった「JPMLリーグは正式な大会名が決まってから足す」を外した）
- テスト: `test_jpml_league` を足した（第1期 第1節・第20節・トーナメント・ベスト16AB卓・ベスト8AB卓・決勝、第2期 第1節 → JPMLリーグ。決勝の分けが「JPMLリーグ 決勝・決定戦」。「 第20期JPML WRCリーグ第10節」→ JPML WRCリーグ、「第8期JPML WRC-Rリーグベスト16AB卓」→ JPML WRC-Rリーグ）。
  `test_events_added_for_published_yotei` の「(仮)」付きの件名が None になる確かめと docstring の「JPMLリーグは足さない」は外した
- 修正前のコードで新しいテストが落ちることを確かめた（`test_jpml_league` が8件 FAIL）。修正後 `python3 -m unittest discover -s scripts/tests` は 1,061件 OK
- 足す前（origin/cloudflare の yotei.py）と後で `event_of()` を比べた。大会が変わるのは予定表【1】の32件だけで、すべて None → JPMLリーグ。層1は0件。JPMLリーグ以外の変化は0件:

  | 日付 | 件名 | 前 → 後 |
  |---|---|---|
  | 2026-10-06〜2027-01-21 | 第1期JPMLリーグ第1節〜第20節（20件） | None → JPMLリーグ |
  | 2027-02-06 | 第1期JPMLリーグトーナメント | None → JPMLリーグ |
  | 2027-03-12 | 第1期JPMLリーグベスト16AB卓 | None → JPMLリーグ |
  | 2027-03-13 | 第1期JPMLリーグベスト16CD卓 | None → JPMLリーグ |
  | 2027-03-19 | 第1期JPMLリーグベスト8AB卓 | None → JPMLリーグ |
  | 2027-03-29 | 第1期JPMLリーグ決勝 | None → JPMLリーグ |
  | 2027-03-01〜03-29 | 第2期JPMLリーグ第1節〜第7節（7件） | None → JPMLリーグ |

- `yotei` を使うのは fetch_yotei.py・write_yotei_sheet.py・sync_live_calendar.py・lib/live_calendar.py・lib/sheets_write.py だけで、ページの生成スクリプトは使わない（再生成の対象なし）

### 次の毎朝の取り込み・同期で変わる見込み

- 【2】の「大会」「分け」: 32行が空 → 「JPMLリーグ」、分けは「JPMLリーグ その他」31行・「JPMLリーグ 決勝・決定戦」1行（第1期決勝）
- 【2】の「開始(仮置き)」: 変わらない（14:00 のまま）。JPMLリーグ の過去の配信と配信予定の枠が層1に無いため、全体の中央値に落ちる。YouTube に配信予定の枠が出たら、その枠の予定の開始に変わる
- 【2】の「枠の開始(YouTube)」・動画ID: 当たる枠が無いので変わらない
- mj_放送対局: 今は変わらない（掲載 Y の4件は件名・開始が【2】のまま）。今後 YouTube に JPMLリーグ の枠が出れば、同じ日の仮の予定が枠の予定に置き換わる（今までは大会が None で置き換わらず2件並ぶ状態だった）
- 常設 issue「予定表の変化」（#481）: 件名は変わらないので、改名の知らせは出ない見込み

## 報告

- 状態: 作業中
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL:
- マージ:
- issue: #488
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d244ac11）: https://github.com/retroeater/mj-logs/tree/main/guide/d244ac11

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d244ac11/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
