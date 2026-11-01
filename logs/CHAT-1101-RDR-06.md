# CHAT-1101-RDR-06

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-age
- 着手時HEAD: 50dcad8

## 指示

【Claude作成】Claude Code 向け指示：転送の棚卸しに「入れてから90日未満は判定しない」を足し、入れた日を実日付に直して #568 の表に「判定できる日」を出す。本番への一括アクセスに目印の UA を付ける規則を置く
Chat-Ref: CHAT-1101-RDR-06
マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が docs/notes/redirects.md・scripts/fetch_redirect_counts.py・scripts/tests/ の転送の2つのテスト・手順3の目印の UA の変更（共通の HTTP の部品・CLAUDE.md・docs/notes/chat-side-operations.md）・docs/decisions/cloudflare.md の追記・ログだけ
貼る時機: CHAT-1101-RDR-05 が cloudflare へマージされた後（同じスクリプトを変えるため）。RDR-05 のセッションの続きでもよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-age の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-rdr-age を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-rdr-age origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-RDR-05 の変更が cloudflare にマージ済みかを確かめ、未マージなら何もせず止まる。

## 目的
2027-01 の初回の棚卸しで、入れて間もない転送を「流入0件」で消してしまわないようにし、判定できる行を表で一目に分かるようにする。あわせて、自分たちの一括アクセスが集計に混ざらないよう、本番へまとめて要求を送るときの目印の UA を決める。

### 決定（2026-11-01、平野さん。チャットで推奨どおりと決めた）
- 削除の基準（直近90日でボット以外の転送0件かつ Search Console の着地0件、2026-10-14）に「入れてから90日経っていない行は判定せず、次の棚卸しへ回す」を足す
- #568 の月次のコメントの表に「判定できる日」（入れた日＋90日）の列を足す
- 本番へまとめて要求を送るとき（ページの一括取得・HEAD の確かめなど）は、UA に `mj-internal` を含める
- Search Console の着地の列の自動化は今は行わない（2027-01 はチャット側が page.csv を見る）

### 前提（チャット側。平野さんの決定ではない）
- redirects.md の「棚卸しの対象」の「入れた日」に、日付でない値（「#540 の公開の日」「#549 の公開の日」）と、注記付きの値（「2026-10-17（未公開。#519）」「2026-10-18（公開は 2026-10-19、#599）」など）がある。#540 のクローズは 2026-10-16、#549 の本番反映も 2026-10-16 のビルド（要確認）
- 「入れた日」は、その行が cloudflare（本番）に入った日（転送が効き始めた日）にそろえる案。ページの「公開」（noindex を外す等）の日ではない。注記は括弧で残してよい。実日付は `git log` で `_redirects` のその行が cloudflare に入ったコミットの日（JST）で確かめる
- 申送りの知見と、選んだ層:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 入れて間もない転送は90日の流入が測れない | 計算的（スクリプトが「判定できる日」を出す。テストが「入れた日」の列が日付で始まることを確かめる） | 表を見るだけで判定の可否が分かり、手順を読み落としても誤って消さない |
| 自分たちの一括アクセスが「ボット以外」に入る | 計算的（共通の HTTP の部品の既定の UA に `mj-internal` を入れる。集計は RDR-05 で除外済み）＋ガイド文（部品を通らない curl・ブラウザでの取得） | 部品を通る要求は書き忘れが起きない。チャット側・Code がその場で使う curl・Playwright は部品を通らないため、規則の一文が要る |

## 手順
1. 同じ論点（転送の棚卸しの基準・UA の規則）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。redirects.md・CLAUDE.md・docs/notes/chat-side-operations.md の今の内容を読む
2. redirects.md の「棚卸しの対象」の「入れた日」を、前提の案のとおり実日付（YYYY-MM-DD で始まる形）に直す。直した行を全件（転送元・前の値・新しい値・根拠のコミット）ログに書く。「棚卸しの手順」の削除の基準の次に「入れた日から90日経っていない行は判定せず、『最後の棚卸し』に『判定前（判定できる日 YYYY-MM-DD）』と書いて次へ回す」の趣旨を足す。`fetch_redirect_counts.py` が「入れた日」を読み、#568 のコメントの表に「判定できる日」の列を出すようにする。`test_redirects_inventory.py`（または集計のテスト）で「入れた日」の列が日付で始まることを確かめる
3. 目印の UA: scripts に本番（ryoei.pro）へ HTTP 要求を送る共通の部品（例: `scripts/lib` の取得の関数）があれば、その既定の UA に `mj-internal` を含める（要求先が ryoei.pro のときだけでよい。外部 API への UA は変えない）。部品が無い・複数あるときは、変えたもの・変えなかったものと理由をログに書く。CLAUDE.md と docs/notes/chat-side-operations.md に「本番へまとめて要求を送るとき（一括取得・HEAD の確かめ・Playwright での巡回）は UA に `mj-internal` を含める（#568 の集計で人として数えないため）」の規則を1文ずつ足す。追記の前に、両文書に容量の上限・警告域の決まりがあれば今の大きさとの差をログに書き、上限を超えるなら止まる。決定を docs/decisions/cloudflare.md に足し、テストを通し、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

## 止まる条件
- 手順1で同じ論点の open issue がある
- 「入れた日」を `git log` で確かめられない行がある（その行だけ前の値のまま残して進め、ログに書く。全部が確かめられないときだけ止まる）
- 追記先（CLAUDE.md・chat-side-operations.md）が容量の上限を超える、または追記先に目印の UA と矛盾する規則があり、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- 未マージの work/ ブランチが redirects.md の「棚卸しの対象」の表・「棚卸しの手順」の節、`fetch_redirect_counts.py` のコメントの表を作る関数、CLAUDE.md・chat-side-operations.md の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: このログの「指示」欄の末尾は指示文の最後の行と一致。RDR-05 の変更（6d0c14f ほか）は `origin/cloudflare` の祖先（RDR-07 のマージ 3911d5f で入った）
- 同じ Chat-Ref のコミット: なし。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし。#568: open
- ガイド文に規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある（CLAUDE.md「Chat-Ref」の確認に当たらない）
- 作業ブランチ: ローカル・リモートとも `work/1101-rdr-age` が無かったため `git checkout -b work/1101-rdr-age origin/cloudflare`（起点 50dcad8）
- 未マージの work/ ブランチ: 触るファイルを変えているのは `origin/work/1017-lvp`（`scripts/lib/members.py`・`scripts/lib/study.py`）と `origin/work/1029-lgn-page`（`scripts/lib/stations.py`）だけ。どちらも本番への HTTP や転送の一覧と関係しない別のファイルなので止まらない

### 手順1: 同じ論点の issue

- `search/issues`（`is:issue`、open・closed）で「mj-internal」「User-Agent」「転送 棚卸し」「入れた日」「redirect」「一括取得 UA」を検索。棚卸しの基準・UA の規則を主題にする issue は対象の #568 だけだった（#485 は jpml_titles の転送の終え方、#554 は AI クローラーの 403 で別の論点）
- redirects.md・CLAUDE.md「判断・作業の原則」・chat-side-operations.md「読み方」「指示文の書き方・渡し方」を読んだ。目印の UA と食い違う規則は無い（chat-side-operations.md の「チャット側から本番を読む手段は WebFetch と Chrome」は読む手段の記述で、矛盾しない）

### 手順2: 入れた日・判定できる日（6bb7e4b）

調べ方:

- `_redirects` のその行（行頭が転送元）を最初に足したコミットを `git log origin/cloudflare --reverse -G '^<転送元>[ \t]' -- _redirects` で探した
- そのコミットが cloudflare に入った時刻は、GitHub の `GET /repos/retroeater/mj/activity?ref=refs/heads/cloudflare`（push の記録、2026-09-06 以降の 2,556 件）の各 push の `before..after` に、そのコミットが初めて含まれた push の時刻（JST）とした。コミットの日付は push より前のことがある（例: 167b4ec はコミットが 10/15、cloudflare に入ったのは 10/16）
- 全 39 行で確かめられた（確かめられない行は無い）。18 行は今の値と同じ日だった。直したのは次の 21 行

| 転送元 | 前の値 | 新しい値 | 根拠のコミット |
|---|---|---|---|
| `/ouka/ranking/` | 2026-10-17（未公開。#519） | 2026-10-18（未公開で入れた。#519） | d8443ed（コミット 2026-10-17T03:18 UTC）。cloudflare への push 2026-10-18 21:23 JST（8165a7b） |
| `/houou_ranking.html` | #540 の公開の日 | 2026-10-16（#540 の公開） | edcbe3b（コミット 2026-10-16T06:42 UTC）。cloudflare への push 2026-10-16 16:19 JST（5b362ee） |
| `/houou_leagues.html` | #540 の公開の日 | 2026-10-16（#540 の公開） | edcbe3b（コミット 2026-10-16T06:42 UTC）。cloudflare への push 2026-10-16 16:19 JST（5b362ee） |
| `/houou_race.html` | #540 の公開の日 | 2026-10-16（#540 の公開） | edcbe3b（コミット 2026-10-16T06:42 UTC）。cloudflare への push 2026-10-16 16:19 JST（5b362ee） |
| `/houou_results.html` | #540 の公開の日 | 2026-10-16（#540 の公開） | edcbe3b（コミット 2026-10-16T06:42 UTC）。cloudflare への push 2026-10-16 16:19 JST（5b362ee） |
| `/saikyo/2011.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2012.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2013.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2014.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2015.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2016.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2017.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2018.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2019.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2020.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2021.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2022.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2023.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2024.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2025.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |
| `/saikyo/2026.html` | #549 の公開の日 | 2026-10-16（#549 の公開） | 167b4ec（コミット 2026-10-15T09:59 UTC）。cloudflare への push 2026-10-16 09:39 JST（push 後の先頭 fa27910） |

- 前提「#540 のクローズは 2026-10-16、#549 の本番反映も 2026-10-16」は、cloudflare への push の時刻（10/16 16:19 JST・10/16 09:39 JST）と合う
- `/ouka/ranking/` は、今の値 2026-10-17 がコミットの日（UTC 10/17 03:18 = JST 10/17 12:18）で、cloudflare に入ったのは 10/18 21:23 JST。前提の案（cloudflare に入った日）に従い 2026-10-18 にした
- redirects.md「棚卸しの対象」の説明に「入れた日」の定義（cloudflare に入った日、JST、YYYY-MM-DD で始める）を1文足した。「棚卸しの手順」の削除の基準の下に「入れた日から90日経っていない行は判定しない。『最後の棚卸し』に『判定前（判定できる日 YYYY-MM-DD）』と書いて次へ回す」を足した
- `fetch_redirect_counts.py`: 表の読み取りを `section_table` にまとめ、`inventory_added_dates`（「入れた日」の列を見出しで探し、先頭の YYYY-MM-DD を読む）と `judge_date`（＋`JUDGE_DAYS` = 90 日）を足した。`comment_text` に引数 `added_dates` を足し、渡したときだけ表の右端に「判定できる日」の列と注記の1行を出す（日付で始まらない行は「（入れた日が日付でない）」）
- テスト（`test_fetch_redirect_counts.py`）: 読み取り・90日後の計算・列の有無・列の数（見出し・区切り・行）・注記。**実物の redirects.md の全行の「入れた日」が日付で始まること**（`test_real_inventory_added_dates_are_dates`。redirects.md を直す前は FAIL、直した後は OK）。`python3 -m unittest discover -s scripts/tests` は OK。修正前のスクリプト（origin/cloudflare）に新しいテストを当てると 3 件 ERROR（#310 の確認）

### 手順3: 目印の UA

- **本番（ryoei.pro）へ HTTP 要求を送る共通の部品は scripts に無い。** 変えたスクリプトは無い
  - `scripts/lib/net_retry.py` は再試行の共通処理で、UA を持たない（要求は各スクリプトが作る）
  - ryoei.pro へ要求を送るのは `scripts/fetch_gsc.py` の `robots.txt` の取得（月1回1件、`requests` の既定の UA `python-requests/…`。RDR-05 で集計から除外済み）だけ。一括の取得ではないので変えていない
  - ほかの UA を持つ要求（`check_image_links.py`・`collect_saikyo_images.py`・`lib/dojo_guest.py`・`lib/wayhome.py`・`generate_live_pages.py` など）の送り先は外部（画像・連盟サイト・ABEMA など）で、指示どおり変えていない
  - `workers/`・`.github/workflows/` にも ryoei.pro へまとめて要求を送るものは無い（grep）
- ガイド文の大きさ（上限は各 28KB=28,672 バイト、警告域 26KB=26,624 バイト。`check-assets.yml`）: CLAUDE.md 23,768 → 23,967 バイト（警告域まで 2,657）、chat-side-operations.md 22,473 → 22,746 バイト（警告域まで 3,878）
- （32ea23c）CLAUDE.md「判断・作業の原則」の「外部ドメインへの依存を増やさない」の次に1文、chat-side-operations.md「指示文の書き方・渡し方」に1文（指示文で UA に `mj-internal` を含めさせる）を足した。部品が無いので、層は「ガイド文」だけになった（表の「計算的」の側は該当なし）
- docs/decisions/cloudflare.md に決定を足した

### 作業ブランチでの手動実行（dry-run、month 2026-10、run 41268340019、32ea23c）

- 成功。#568 のコメントの表の右端に「判定できる日」が全 39 行に出た（「（入れた日が日付でない）」の行は無し）。例: `/tanilog.html` 2026-12-06、`/saikyo_results.html` 2026-12-20、`/jpml_titles.html` 2026-12-29、`/houou_*.html`・`/saikyo/<年度>.html` 2027-01-14、`/saikyo/ranking/qualifier-*/` 2027-01-25。注記に「判定できる日: 「入れた日」（`docs/notes/redirects.md`）の90日後。この日より前の棚卸しでは判定せず、次へ回す」が出た
- 2027-01 上旬の初回の棚卸しで判定できるのは、判定できる日が棚卸しの日以前の行だけ（2027-01-14 以降の行は、上旬に行うなら判定前になる）

### 差分の自己レビュー（新しい文脈のサブエージェント、1回）

レビュー側でも `/houou_results.html`・`/saikyo/2015.html`・`/ouka/ranking/` の入れた日を git で照合し、記載と合うとした。テストは 1733 件 OK。

| 指摘 | 扱い |
|---|---|
| 低: 「入れた日」の列が無いと `{}` が返り、列が出て全行が「（入れた日が日付でない）」になる | 直した（10239334a。列が無ければ None を返し、表に列を出さない） |
| 低: 同じ節に2つ目の表が増えると、行が混ざる・見出しは1つ目のものになる | 意図どおり（今の節は表が1つ。1列目の読み方は前からの挙動と同じ） |
| 低: 行のセルが見出しより少ない分岐のテストが無い | 直した（10239334a。テストを1つ足した） |

### マージ前の確認

- `git diff origin/cloudflare...HEAD --stat`: CLAUDE.md（+1）・docs/decisions/cloudflare.md（追記のみ、削除行 0）・docs/logs/CHAT-1101-RDR-06.md・docs/notes/chat-side-operations.md（+1）・docs/notes/redirects.md・scripts/fetch_redirect_counts.py・scripts/tests/test_fetch_redirect_counts.py。冒頭の「マージ:」の条件どおり（転送の2つのテストのうち、変えたのは集計のテストだけ。`test_redirects_inventory.py` は変えていない）
- `python3 -m unittest discover -s scripts/tests`: OK

- マージ: push 直前に再 fetch し `git merge-base --is-ancestor origin/cloudflare HEAD` が真。`git push origin work/1101-rdr-age:cloudflare` で 50dcad8..e5e4fcb（fast-forward）
## 報告

- 状態: 完了
- ブランチ: work/1101-rdr-age
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1101-RDR-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-rdr-age
- 確認用URL: なし（scripts/ と docs/ と CLAUDE.md だけの変更。Workers Builds の対象外）
- マージ: 済（e5e4fcb）
- issue: #568
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e5e4fcb7）: https://github.com/retroeater/mj-logs/tree/main/guide/e5e4fcb7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e5e4fcb7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
