# CHAT-1009-STL-02

- 着手日時: 2026-10-09
- 対象issue: #475
- ブランチ: work/1009-stl
- 着手時HEAD: 1ddf8321

## 指示

【Claude作成】Claude Code 向け指示：「連盟プロ以外」「別名」の使われていない登録を #475 の知らせに並べる（実装・試運転・文書。マージ前に止まる） Chat-Ref: CHAT-1009-STL-02 マージ: 判断待ちで止まる 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1009-stl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-stl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-STL-01 のログの `## 経過`（手順1後半の表・手順2・手順3）と `## 報告` を読む。STL-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1009-STL-02` を足す。

目的
CHAT-1009-STL-01 の調査をもとに、「連盟プロ以外」「別名」（区分 `訂正`）の登録のうち、もうどこでも使われていないものを、#475 の毎日の知らせに並べて出す。手動実行でも出せるようにする。行を消すのは平野さんの手作業で、仕組みはシートを変えない。
決定（2026-10-09、平野さん）

* 「連盟プロ以外」「別名」の登録のうち、どこでも使われていないものを、#475 の毎日のコメントに並べて書く。手動実行でも出せるようにする。「別名」は区分 `訂正` の行だけを対象にする（`登録名変更` は対象外）（STL-01 で記録済み）
* 載せ方: 未使用の一覧が前の回から変わった日だけ、未登録の名前の知らせと同じコメントに「使われていない登録」として全件を並べる
* 手動実行: 入力 `unused_report` を足す。手動で回したときは、変わっていなくても今の一覧をコメントする
* 検査が失敗したとき: 取り込み・未登録の名前の知らせは止めない。コメントには「検査できなかった」の1行だけを書く
* 「タイトル」「鳳凰」の表示しない行（「タイトル」の表示≠Y、「鳳凰」の表示 N・LEAGUES 外）も「使われている」に数える。凍結中の「書籍」は数えない
* #475 の本文に、行を消すのは平野さんの手作業であることと、概要欄の直しが届くのは毎週水曜の取り込み（すぐ届かせるなら手動実行で `apply` と `verify` を付ける）であることを書き足す
* 実装のマージは判断待ちで止める（試運転の結果を見てから決める）

前提（チャット側。平野さんの決定ではない）

* 判定の入力と定義は STL-01 の「判定」と「判断が必要なこと」の案に、上の決定（表示しない行を足す）を加えたもの: 層1から別名を当てずに抜いた名前（`build_rows(videos)` を book なしで。【2】のタブは訂正の後なので使わない）、【3】の対局者・実況・解説（掲載を問わない）、カレンダーの抜き出し（全動画で近似）、「タイトル」（全行）、「テスト」（J=Y）、「最強戦」（K=Y）、「鳳凰」（全行）。`訂正` の行は変換前がこの集合にあれば使われている。「連盟プロ以外」の行は名前がこの集合にあるか、使われている `訂正` または `登録名変更` の行の変換後なら使われている（STL-01 の案。旧名で出ている人の写真・X に要るため）
* 前回の一覧は、bot のコメントの末尾の隠した注記（HTML のコメント）に書き、次の実行で最後の bot のコメントから読む案（STL-01）。実物に合わせて別の持ち方にしてよい（理由を報告に書く）
* 検査は層2・#475 の未登録の知らせ・層3の後の別のステップにし、`continue-on-error` で実行を失敗にしない案（STL-01）。失敗が続く間、「検査できなかった」の1行は初回の日だけ書く
* 一覧の各行は、行番号・名前（別名は変換前→変換後）・所属団体と所属補足（別名は区分と備考）。一覧は「消してよい候補」で、他団体の現役プロなど今後また出る人も入る（コメントの案内の文にそう書く）
* STL-01 の試算は「別名」2行・「連盟プロ以外」26行（読む範囲を変えた差は 0〜3行）。今日までに平野さんがシートを直していれば変わる

手順

1. 実装とテスト: 着手時に `git branch -r --no-merged origin/cloudflare` で、層2・`names.py`・`live_candidate`・`update-live-channel.yml`・#475 の知らせに触れるブランチが無いことを確かめる。判定の部品（読み込み・判定・前回との比較・コメントの文面）を作り、既存の関数を流用するときは import・参照している所をすべて挙げ、既存の関数の振る舞いを変えない（変えるなら、マージ前に全ページを再生成して差分が無いことを確かめる）。テストを足す（判定の定義、`訂正` だけを数える、`登録名変更` の変換後、一覧が変わらない日は書かない、`unused_report` のときは書く、検査が失敗したときの1行）。`update-live-channel.yml` に入力 `unused_report` とステップを足す。docs/notes/branch-operations.md「ワークフローを変更したとき」を読んで従う
2. 試運転（書かない）: 作業ブランチで `update-live-channel.yml` を `apply` を付けずに `unused_report` を付けて手動実行し（15分を上限に待つ。超えたらその時点の状態を書き「未確認の項目」に回す）、実行サマリに出た一覧（件数・全行）をログに引用する。シート・#475・カレンダー・cloudflare には何も書かれていないことも確かめる。セッションのトークンで起動が403なら、平野さんに GitHub の画面の「Run workflow」を頼んで止まる
3. 文書: docs/notes/live-channel-write.md（#475 の知らせの節に「使われていない登録」と入力 `unused_report`、概要欄の直しが届く日）、docs/notes/live-page-design.md「1-5」（「使われている」の定義と読む利用先）、`update-live-channel.yml` の冒頭のコメント、docs/decisions/live.md を、それぞれ今の内容を読んでから直す。#475 の本文に書き足す文面はログに案として書き、本文はまだ変えない（マージの後に書く）

止まる条件

* 手順1で、重なる未マージのブランチがある
* 既存の関数の振る舞いを変える必要があり、全ページの再生成で差分が出る
* 試運転の一覧の件数が STL-01 の試算（「別名」2行・「連盟プロ以外」26行）から、それぞれ ±3行を超えて違う（表示しない行を足したことと、シートの直しで説明できる差は止めずに理由を書く）
* 試運転でシート・#475・カレンダー・cloudflare のどれかに書き込みがあった
* 追記先の文書が決定と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい。どう処理したかを報告に書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れずに判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-STL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-STL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複なし。work/1009-stl はローカル・リモートとも cloudflare の祖先（STL-01 でマージ済み）→ cloud-sessions.md「ローカルにあり origin/cloudflare の祖先」に従い、`git merge --ff-only origin/cloudflare`（b2529448..1ddf8321）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。「作業ブランチ」の行もある
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。STL-01 の `## 経過`・`## 報告` を読んだ（同じセッションで書いたもの）。STL-01 の状態に ` / 続き: CHAT-1009-STL-02` を足した

### 手順1: 実装とテスト

- 未マージのブランチ: work/1008-hou・work/1009-stl だけ。work/1008-hou の差分に `names`・`live_candidate`・`live_extract`・`write_live_channel`・`update-live-channel`・`live_layer3`・`live_calendar`・`sync_live` は無い（止まる条件に当たらない）
- 読んだ: docs/notes/branch-operations.md「ワークフローを変更したとき」（既存のワークフローなので作業ブランチを ref にして手動実行できる）
- 足したもの（既存の関数は1つも変えていない。全ページの再生成は不要）:
  - `scripts/lib/unused_names.py`: 判定（`find_unused()`）・比較の鍵（`keys()`。行番号は行を消すとずれるので含めない）・節の文面（`section()`）・注記（`build_marker()`・`parse_marker()`）・載せるかの判断（`decide()`）。値だけを受け取る
  - `scripts/check_unused_names.py`: 読み込み（下）と、#475 の bot（`github-actions[bot]`）のコメントのうち注記のある最後のものの読み取り（REST、`GITHUB_TOKEN`）。読み込みの例外は捕まえて「検査できなかった」にし、終了コード 0。`--force`（unused_report）・`--no-issue`（手元の確認用）
  - `scripts/tests/test_unused_names.py`（16件）: 判定の定義、`訂正` だけを数える、`登録名変更` の変換後（使われている行の変換後は使われている・使われていない行の変換後はそうならない）、注記の往復と最後のものを取る、一覧が変わらない日は書かない、`unused_report` のときは書く、変わったときは増減を書く、失敗の1行と初回の日だけ、`unused_report` なら失敗でも書く、失敗からの回復は書く、空の一覧、読み込みの失敗で1行を書き出して正常に終わる
  - `.github/workflows/update-live-channel.yml`: 入力 `unused_report`、環境変数 `UNUSED_REPORT`（予約の起動では偽）、層2の後・知らせの前のステップ「層2 使われていない登録を調べる」（`continue-on-error: true`）、知らせの段で未登録の名前のコメントと使われていない登録の節を1つのコメントにまとめる（節の後ろに注記、その後ろに今までのフッタ）。検査の段が JSON を残さずに落ちたとき（#475 の読み取りの失敗など）は注記なしで1行だけ書く（この経路は前回の状態が分からないため、失敗が続くと毎日出る）。冒頭のコメントに説明を足した
- 載せ方の持ち方: 指示の案どおり、前回の一覧は bot のコメント末尾の HTML コメントで持つ（`<!-- unused-registrations: {"failed": …, "keys": […]} -->`、JSON は ASCII にエスケープ）。失敗の注記にも最後に成功した一覧を持ち越し、回復した日は一覧が同じでも書く（失敗の注記のままだと次の失敗を知らせられないため）
- 検査のステップを層3の後ではなく層2と知らせの間に置いた: 知らせと同じコメントにまとめるため。知らせの段を層3の後へ動かすと、層3の失敗で未登録の名前の知らせが止まる（今の動き）が変わるため動かさなかった。この時点の【3】は当日の追記の前だが、追記する行は補正の列が空欄なので判定は変わらない
- 流用した既存の関数・定数（振る舞いは変えていない。参照している既存の所）:
  - `live_candidate.load_latest_videos()`・`build_rows()`（write_live_channel_candidate.py・append_live_layer3_candidates.py・fetch_live_channel_raw.py・sync_live_calendar.py・テスト）、`live.split_list()`（多数）
  - `live_calendar.player_lines()`・`GAME_PREFIX_RE`、`live_extract.extract_players_and_staff()`（lib/live_calendar.py・lib/live_candidate.py・テスト）
  - `sheets.fetch_records()`・`fetch_sheet()`・`check_not_filtered()`（生成スクリプト全般）、`live_layer3.SPREADSHEET_ID`・`SHEET_NAME`・`HEADERS`・`FIRST_SHEET`・`EXPLICIT_BLANK`
  - `generate_title_pages.fetch_tab()`・`TAB_*`・`SPREADSHEET_ID`（generate_title_pages.py・generate_jpml_test.py）、`generate_jpml_test.player_name()`・`SPREADSHEET_ID`・`SHEET_NAME`・`QUERY`、`generate_saikyo_pages.text()`・`QUERY`・`COL_NAME`、`generate_houou_race` の `HEADERS`・`FIRST_SHEET`・`SHEET_NAME`
  - `names.KIND_CORRECTED`
- 手元: `python3 -m unittest discover -s scripts/tests` 675件 OK。`python3 scripts/check_unused_names.py --no-issue --out …` の一覧は「連盟プロ以外」26行・「別名」2行で、STL-01 の試算と同じ行（「テスト」は J=Y の34行）
- 新しいテストは新しいモジュールのものなので、修正前のコードでは import できず通らない（#310 の確かめ）

### 手順2: 試運転（書かない）

- 2026-10-09 06:18 UTC、`actions_run_trigger` で `update-live-channel.yml` を ref `work/1009-stl`、入力 `{"apply": "false", "unused_report": "true"}` で起動（403 にならなかった）。run 37892845226（run #80、head ecbf58d4）、約3分で success
- 実行サマリ（ジョブのログ）の「層2: 使われていない登録」:

  ```
  「タイトル」3110行、「テスト」34行、「最強戦」2568行、「鳳凰」16011行
  「連盟プロ以外」: 765行
  「別名」: 28行
  使われている名前: 2691、「連盟プロ以外」765行、「別名」28行
  ### 使われていない登録: 「連盟プロ以外」26行・「別名」(訂正)2行
  （中略: 案内の文）
  「連盟プロ以外」(行・名前・所属団体 / 所属補足):
  - 15行 有賀一宏（最高位戦 / -）
  - 23行 石田時敬（最高位戦 / -）
  - 87行 齋藤けーすけ（協会 / -）
  - 93行 佐藤崇（最高位戦 / -）
  - 98行 設楽遙斗（最高位戦 / -）
  - 102行 清水裕貴（協会 / -）
  - 135行 田中航（最高位戦 / -）
  - 144行 綱川隆晃（最高位戦 / -）
  - 147行 寿（とし）（- / 一般）
  - 159行 中邨光康（最高位戦 / -）
  - 167行 野村勇介（協会 / -）
  - 169行 筥崎弘太郎（協会 / -）
  - 170行 長谷川来輝（最高位戦 / -）
  - 196行 松島リキヤ（協会 / -）
  - 254行 伊東一（- / 空欄）
  - 292行 夏目（- / 空欄）
  - 302行 萱場貞二（- / 空欄）
  - 317行 菊池俊幸（- / 空欄）
  - 330行 宮城拓二（- / 空欄）
  - 396行 佐藤聖誠（- / 元最高位戦）
  - 476行 上野龍一（- / 空欄）
  - 503行 清原大（- / 空欄）
  - 560行 大脇貴久（- / 空欄）
  - 619行 土井泰昭（- / 空欄）
  - 674行 平林加一（- / 空欄）
  - 685行 名古屋潤（- / 空欄）

  「別名」(行・変換前 → 変換後・区分 / 備考):
  - 11行 佐月真理子 → 佐月麻理子（訂正 / 概要欄の誤記）
  - 17行 根越英人 → 根越英斗（訂正 / 概要欄の誤記）
  知らせに載せる: あり
  ```

- 件数は STL-01 の試算と同じ（「別名」2行・「連盟プロ以外」26行、行も同じ）。表示しない行（「タイトル」表示≠Y・「鳳凰」表示 N・LEAGUES 外）を足しても差は0（STL-01 の「読む範囲を変えたときの差」のとおり）。止まる条件に当たらない
- 知らせの段: 「issue #475 にコメントする:」に上の節と注記（`<!-- unused-registrations: {"failed": false, "keys": [...28件] } -->`）を出し、「apply なしのためコメントしません」で終わった。未登録の名前は変化なしで、コメントの中身は使われていない登録の節だけ。前回の注記は #475 にまだ無い（JSON が書き出されて節が出たので、#475 の読み取りは成功している）
- 書き込みが無いことの確認:
  - シート: ジョブの全ステップで `APPLY: false`。層3は「--dry-run のため書き込みません」。【1】【2】は `APPLY` が真でないとき `--dry-run` を付ける（ワークフローの既存の分岐、今回は変えていない）
  - #475: コメント数は 9 のまま（最後は 10/8 19:03 UTC）
  - カレンダー・予定表: yotei ジョブは `APPLY: false`・`CALENDAR_APPLY: false`、「作成は止まっていません」
  - cloudflare・作業ブランチ: 06:18 UTC 以降のコミットはこのセッションの3件だけ（bot のコミットなし）。regenerate ジョブは skipped

### 手順3: 文書

- docs/notes/live-channel-write.md「7」: 層2の項に「使われていない登録」（定義の参照・前回の一覧の持ち方・失敗の扱い）、手動実行の段落に `unused_report` と概要欄の直しが届く日（水曜の verify、すぐなら `apply`+`verify`）を足した。今の記述と矛盾は無く、追記だけ
- docs/notes/live-page-design.md「1-5」: 末尾に「使われていない登録」（使われている名前の利用先・定義・利用先を足したときの注意）を足した。1〜6 の検査とは別のものとして書き、既存の項目は変えていない
- `update-live-channel.yml` の冒頭のコメント: 3. の項に3行足した
- docs/decisions/live.md: この指示の決定を足した
- #475 の本文に書き足す文面（案。マージの後に書く。本文はまだ変えていない）: 本文の案内の段落（「実在の人は…」の次）に次の2段落を足す

  > どこでも使われていない「連盟プロ以外」の行と「別名」の `訂正` の行は、一覧が変わった日に同じコメントの「使われていない登録」に並べます。消してよい候補で、他団体の現役プロなど今後また出る人も入ります。行を消すのは手作業で、この仕組みはシートを変えません。手動実行で `unused_report` を付けると、変わっていなくても今の一覧を出します。
  >
  > YouTube の概要欄の直しは、毎週水曜の取り込みで届きます（それまでは古い読み違いが残り、使われていない登録にも出ません）。すぐ届かせるときは、手動実行で `apply` と `verify` を付けます。

## 報告

- 状態: 判断待ち（実装・試運転・文書は済み。マージは平野さんの判断） / 続き: CHAT-1009-STL-03
- ブランチ: work/1009-stl
- ログ: https://github.com/retroeater/mj/blob/work/1009-stl/docs/logs/CHAT-1009-STL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-stl
- 確認用URL: なし（ページは変えていない。Workers Builds のプレビューは確かめていない）
- マージ: 未（平野さんの判断待ち）
- issue: #475（変えていない）
- 判断が必要なこと:
  - 実装のマージ: 試運転（run 37892845226、apply なし・unused_report あり）の一覧は「連盟プロ以外」26行・「別名」(訂正)2行で STL-01 の試算と同じ。何も書かれていない。マージしてよいか
  - マージ後の最初の apply の実行（翌朝の予約の起動を含む）では、#475 にまだ注記が無いため、一覧が変わっていなくても全件（28行）を1回コメントする。その後は変わった日だけ
  - #475 の本文に書き足す文面の案は「## 経過」の手順3の末尾。マージの後に本文へ足してよいか（指示どおり、本文はまだ変えていない）
  - 検査の段が JSON を残さずに落ちたとき（#475 の読み取りの失敗など）は、前回の状態が分からないので「検査できなかった」の1行を注記なしで書く。この経路だけは失敗が続くと毎日出る（読み込み〈シート・層1〉の失敗は、決定どおり初回の日だけ）。このままでよいか
  - 検査の段の位置: 案の「層3の後」ではなく層2と知らせの間に置いた（同じコメントにまとめるため。理由は「## 経過」手順1）
- 未確認の項目:
  - マージ後の apply の実行で、コメントが実際に #475 に書かれ、次の実行が注記を読んで「変わっていない」と判定すること（試運転は apply なしのため、書き込みと注記の読み戻しは通していない。読み戻しはテストで確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cd8470aa）: https://github.com/retroeater/mj-logs/tree/main/guide/cd8470aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cd8470aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/14f14a50.md
