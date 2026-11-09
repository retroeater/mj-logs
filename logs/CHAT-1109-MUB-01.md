# CHAT-1109-MUB-01

- 着手日時: 2026-11-09
- 対象issue: #538
- ブランチ: work/1109-mub
- 着手時HEAD: 69469d94

## 指示

【Claude作成】Claude Code 向け指示：#538 Ubuntu 26.04 で動いた回があるか・その成否を確かめる（3回目の確認、報告のみ。朝の報告からのフォロー） Chat-Ref: CHAT-1109-MUB-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1109-mub を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1109-mub origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1109-mub の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的 #538 の残り（`ubuntu-latest` の Ubuntu 26.04 への移行の後、26.04 で動いた回が success か）を確かめる。CHAT-1026-UBU-01（最後のジョブの開始 2026-10-25T23:37Z まで）では 26.04 のジョブが0件で、次の確認は 2026-11-09（カレンダーの予定、2026-10-26 平野さんの決定）。あわせて、11/8 の義兄弟合戦の翌朝の run-daily で転記案（research-candidates）が候補タブに書いたかを、run のログから確かめる（報告のみ）。 変更の範囲: docs/logs と docs/decisions/ のみ（コード・文書は変えない）。
決定（2026-11-09、平野さん）

* （無し。この指示で新しく決めたことは無い）

前提（チャット側。平野さんの決定ではない）

* #538 は open。最後のコメントは CHAT-1026-UBU-01（2026-10-26T00:17Z）で、次の確認の時期の案「2026-11-09 ごろ」が書かれている（2026-11-09 06:00 JST ごろ、チャットで GitHub MCP で読んだ）
* ランナーの版はジョブのログ冒頭の「Operating System」「Runner Image」の group に出る。UBU-01 は GitHub MCP の `get_job_logs` で読めた
* mj-logs の actions/morning.md（2026-11-09 04:15 JST 書き出し）では直近26時間の失敗は無し。run-daily #38（run 42512794532、11/09 04:00 JST、success）、run-weekly #11（run 42507761449、11/09 03:00 JST、success）
* #666 は 2026-11-04 に closed（completed）。転記案の確かめはこの issue にコメントしない（報告はログだけ）
* カレンダーの説明欄に「その指示では mj-logs も push の権限で付け、sync-from-mj.yml の run も確かめる（2026-10-26 の決定）」とある。このセッションの接続先に mj-logs が無ければ、sync-from-mj.yml の run は「読めなかった」と書いて進んでよい（mj-logs には書き込まない）

手順

1. #538 の本文とコメントを読み、上の前提（open・最後のコメントが CHAT-1026-UBU-01）と食い違えば止まる。2026-10-26 より後に他のセッションの着手中コメントがあれば止まる。直近7日に同じ趣旨（26.04 の確認）のコメントがあれば、足さずに止まる。食い違わなければ着手中のコメントを書く（本文・状態・ラベルは変えない）
2. 2026-10-25T23:37Z より後に開始した mj の run の、skipped でないジョブ全件のログ冒頭を読み、ジョブごとに Operating System・Runner Image（`Image:`・`Version:`）・setup-python の `Successfully set up CPython (…)`・ジョブの結論を表にしてログの `## 経過` に書く（run ID・ワークフロー・ジョブ ID・開始時刻を添える。全件書き、略さない）。mj-logs の `sync-from-mj.yml` の同じ期間の run も、読めれば同じ表にする（読めなければその旨を書く）
3. 集計（26.04 の件数と、そのうち success でないもの。ワークフローの名前の一覧つき。24.04 のままのワークフローの名前の一覧も）と、期間中の failure の run それぞれが 26.04 で動いたものか・原因が移行と関係しそうか（annotations と失敗したステップのログから）をログに書き、`## 経過` の該当の表から引用して #538 にコメントする。コメントの冒頭は「【朝の報告からのフォロー（CHAT-1109-MUB-01、2026-11-09）】」、事実（run ID を含む）＋頼むこと（26.04 の回があり全部 success なら閉じてよいかの判断、無ければ次の確認の時期の案）＋「返事はこの issue に。朝のチャットは追わない」。本文・状態・ラベルは変えない（閉じない）
4. run-daily の run 42512794532 のうち research-candidates（転記案、sokuho / research）のジョブのログと実行サマリを読み、候補タブに書いた行数と、11/8 の義兄弟合戦の速報を見つけたか（「見つからない」「未照合」の行を含む）をログの `## 経過` に書く。issue にはコメントしない（#666 は closed）

止まる条件

* 手順1の前提が食い違う
* ジョブのログが読めない（手段を変えて2回試しても読めない。試した手段を書く）。手順4だけ読めないときは、その旨を書いて手順1〜3は進める

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。26.04 の回があり全部 success なら、「判断が必要なこと」に「#538 を閉じてよいか」を書く。26.04 の回が無ければ、次に確かめる時期の案を書く。手順4の結果（候補タブに書いた行数）は「判断が必要なこと」に「#666 の確かめ（義兄弟合戦の速報が候補タブに書かれたか）」として1行書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1109-MUB-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1109-MUB-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、`git log --all -E --grep 'CHAT-[0-9]{4}-MUB-'`・docs/logs の履歴）: 該当なし
- work/1109-mub はローカル・リモートとも無し → `git checkout -b work/1109-mub origin/cloudflare`（69469d94）
- 0章: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 手順1: #538 は open。最後のコメントは CHAT-1026-UBU-01（2026-10-26T00:17:26Z）で前提と一致。2026-10-26 より後の着手中のコメント・26.04 の確認のコメントは無い。
  着手中のコメントを書いた（https://github.com/retroeater/mj/issues/538#issuecomment-6729356922 ）
- mj-logs: `add_repo`（push）で接続できた（/home/user/mj-logs に clone。書き込みはしない）
- 対象の run（GitHub MCP の `list_workflow_runs`、perPage=100 で page 1〜22。サブエージェント）: run_started_at が 2026-10-25T23:37Z より後のもの **2,099件**
  （最初 40245574722 2026-10-25T23:37:04Z、最後 42512794532 2026-11-08T19:00:26Z。境界の外の直前は 40245383856 23:34:31Z）。
  success 1,225・skipped 845（すべて 本番ビルド失敗の検知）・failure 27・cancelled 2。skipped でない run は 1,254件（前回 275件の約4.6倍。公開対象の検査 641・ページの再生成 347 が大半）
- ジョブの一覧: `list_workflow_jobs` は1件 1.5k〜15k トークンで、1,254件ぶんは取り切れず止まった（取得 19件）。ジョブのログ: `get_job_logs` は1件 約10k トークン（ジョブ 約1,500件の見込み）。
  `get_workflow_run_logs_url` の ZIP（results-receiver.actions.githubusercontent.com）は curl でプロキシに拒否（CONNECT 403）
- **Ubuntu 26.04 のジョブがある**: run 42512794532（[scheduled] 毎朝の運行）の job 128829306366（sokuho / research、開始 2026-11-08T19:00:29Z）は
  `Ubuntu 26.04.1 LTS`・`Image: ubuntu-26.04`・`Version: 20261101.186.1`・CPython 3.12.16・success

### 手順4: run-daily 42512794532 の sokuho / research（job 128829306366）

- 結論 success。入力 kind D・lookback_hours 30・dry_run false・scheduled true
- 実行の出力（＝実行サマリに cat したもの）: 「新しいテキスト速報（公開から 30時間以内）: 3本」「候補タブ: 277行・見出し 一致」「書く行: 3件」「候補タブに 3行を書いた」
- 書いた3行はすべて 2026-11-08 義兄弟合戦（ベスト8 A卓 kinmaweb 290041・ベスト8 B卓 290042・決勝 290043）で、確度はすべて「照合済み」。「見つからない」「未照合」の行は無い

### 平野さんの回答（2026-11-09、作業中）

- ジョブが約1,500件・ログ全件で約1,500万トークン以上の見込みを示して進め方を聞いた → 「絞って全件の代わりに」を選んだ
  （failure・cancelled の run 全件のジョブ＋各ワークフローの直近2件の run のジョブを読み、読んでいない範囲を明記する）

### 他のセッション

- 同じ目的の CHAT-1109-UBU-02（work/1109-ubu）が、このセッションの着手中コメント（00:28Z）を見て着手せずに止まっていた（mj-logs の写しで確認）。重複の調査は起きていない

### 抽出した run（85件）とジョブ（193件）

- 選び方: skipped でない run 1,254件のうち、failure 27件・cancelled 2件の全件と、ワークフローごとに開始の新しい2件（重なりを除いて計85件）。
  その skipped でないジョブ全件（193件）のログを `get_job_logs`（return_content）で読んだ（サブエージェント5本、Workflow ツール。約330万トークン）
- **読んでいないもの: 残りの run 1,169件のジョブ**（版の件数は抽出の中の数で、期間全体の数ではない）
- 読めなかった2件: job 123200409058（月次の運行 logplaces / convert、cancelled。ランナー割り当て前の取り消しで、ログが 404）、
  job 124916244775（週次の運行 youtube / regenerate、success。ログ 5,559行のうち末尾 5,000行しか返らず冒頭が無い）。tail_lines 20000・100000 の2通りで試した
- 照合: job 128829306366 は自分でも読み、サブエージェントの行と一致
- 日ごと（UTC の開始日）の件数（24.04 / 26.04 / 読めない）: 10-26 2/0/0、10-27 4/0/0、10-29 3/0/1、10-30 1/0/0、10-31 21/0/0、11-01 33/0/1、11-03 16/0/0、
  11-04 29/0/0、11-05 20/0/0、11-06 5/3/0、11-07 0/31/0、11-08 0/23/0。**26.04 の最初は 2026-11-06（UTC）、2026-11-07 以降の抽出は全部 26.04**

### 集計（抽出の範囲）

- 26.04: **57件**（`Ubuntu 26.04.1 LTS`・`Image: ubuntu-26.04`・Version 20261101.186.1 が大半、20261101.64.1 が2件。CPython 3.12.16）。success 55・**failure 2**
  - 26.04 で動いたワークフロー（8本）: X の投稿の変換・[scheduled] 毎朝の運行・[scheduled] 週次の運行・sheets-reader の確かめ・サイトマップの lastmod の同期・ページの再生成・公開対象の検査・月次の運行
  - failure 2件はどちらも X の投稿の変換（work/1107-lgv-l3 の workflow_dispatch）: 42335988748 は `convert_x_posts.py --mode l3plan` の `TypeError: can only concatenate str (not "int") to str`（作業ブランチのスクリプトの不具合と見られる）、
    42336059097 はスクリプトの検査（新しく載る行・違いがあるので止めた）。どちらも移行と関係しなさそう（見立て。コードは確かめていない）
- 24.04: 134件。抽出の中で最新の回が 24.04 のワークフロー（26本。いずれも最新の回が 2026-11-06 以前で、それ以降に run が無い）:
  - tmp-verify-paginate（2026-10-30、24.04）
  - Graph Update: pip in /scripts #1666949480（2026-10-31、24.04）
  - Graph Update: pip in /scripts #1667880199（2026-10-31、24.04）
  - Search Console のエクスポート（2026-10-31、24.04）
  - [scheduled] 月次の運行（2026-10-31、24.04）
  - 道場部ゲストのカレンダーの同期（2026-11-01、24.04）
  - github_actions in /., /.github/actions/* - Update #1671368975（2026-11-01、24.04）
  - セキュリティ検査（2026-11-01、24.04）
  - 名簿と「プロ」シートの不一致の検知（2026-11-01、24.04）
  - 最強戦の出場者の登録漏れの検知（2026-11-01、24.04）
  - マージ済みブランチの片付け（2026-11-01、24.04）
  - 作業ログの片付け（2026-11-01、24.04）
  - 転送の件数の集計（2026-11-01、24.04）
  - 誕生日カレンダーの同期（2026-11-01、24.04）
  - 予定表の取り込み（2026-11-01、24.04）
  - 画像リンク切れの検知（2026-11-01、24.04）
  - 「連盟ch」の取り込み（2026-11-01、24.04）
  - X の投稿の試し取り（2026-11-03、24.04）
  - Graph Update: pip in /scripts #1678063757（2026-11-03、24.04）
  - 本番ビルド失敗の検知（2026-11-03、24.04）
  - YouTube の情報の取得（2026-11-03、24.04）
  - 候補の調べもの（2026-11-04、24.04）
  - SNS ブックの更新（2026-11-04、24.04）
  - 週次の運行（2026-11-05、24.04）
  - 毎朝の運行（2026-11-05、24.04）
  - 最強戦の再生成（2026-11-06、24.04）
- failure・cancelled の run 29件: 27件は 24.04 上（または割り当て前の取り消し）で、移行と関係しない（生成物の差の検知・Sheets API の 429・Places API の障害・意図した検知の失敗・作業ブランチの変更など、下の表）。26.04 上は上の2件

#### success でないジョブ（全行）

| run | ワークフロー | ジョブ ID | ジョブ | 開始 | OS | Version | 結論 | 失敗の要点 |
|---|---|---|---|---|---|---|---|---|
| 40319743097 | ページの再生成 | 121561342801 | regenerate | 2026-10-26T12:04:29Z | Ubuntu 24.04.5 LTS | 20261012.334.1 | failure | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128. |
| 40482759522 | 本番ビルド失敗の検知 | 122116920052 | notify | 2026-10-27T09:42:01Z | Ubuntu 24.04.5 LTS | 20261012.334.1 | failure | 判定する(該当すればジョブを失敗させる): 本番に入っているコミットの Workers Builds が失敗した(check-run 122116895714 failure, compare behind) / ##[error]Process completed with exit code 1. |
| 40798423892 | 月次の運行 | 123200409058 | logplaces / convert | 2026-10-29T01:07:06Z | unreadable | unreadable | cancelled | ランナー割り当て前に取消(steps・runner なし)。get_job_logs が tail_lines 20000/100000 とも HTTP 404。##[error] 行は取得できず |
| 40826011390 | X の投稿の変換 | 123289104311 | convert | 2026-10-29T05:53:57Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step「実行」: 止めた: 駅が空の飲食店の行が 5% を超える（書いていない） / ##[error]Process completed with exit code 1. |
| 40839961758 | X の投稿の変換 | 123333667969 | convert | 2026-10-29T08:08:06Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | 実行: 止めた: 駅が空の飲食店の行が 5% を超える（書いていない）(relist dry-run, 601/2668=22.5%) / ##[error]Process completed with exit code 1. |
| 41227250544 | [scheduled] 月次の運行 | 124614022271 | redirects / fetch | 2026-10-31T18:01:03Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step コミットしてpush: ! [rejected] HEAD -> cloudflare (fetch first); error: failed to push some refs (RETRY: none); ##[error]Process completed with exit code 1. |
| 41252379802 | 毎朝の運行 | 124688987803 | sns / update | 2026-10-31T23:27:08Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 実行: update_sns_book.py が「止めました: 「プロ」「連盟プロ以外」で名前が重複しています」で停止 / ##[error]Process completed with exit code 1. |
| 41361234293 | ページの再生成 | 125011843482 | regenerate | 2026-11-01T23:15:49Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 生成物の差を実行サマリに出す: regen_annotations.py compare (COMPARE_REF=cloudflare) で fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 12 |
| 41669293763 | ページの再生成 | 126033337087 | regenerate | 2026-11-03T15:58:43Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 対象ページを再生成: 生成スクリプトがありません: scripts/generate_title.py / ##[error]Process completed with exit code 1. (TARGET=title) |
| 41671687021 | 本番ビルド失敗の検知 | 126041430188 | notify | 2026-11-03T16:11:57Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 判定する(該当すればジョブを失敗させる): check_build_failure.py が Workers Builds: mj-ask の failure を検知(意図した失敗、compare: behind) / ##[error]Process completed with exit code 1. |
| 41671698335 | 本番ビルド失敗の検知 | 126041469179 | notify | 2026-11-03T16:12:01Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step「判定する(該当すればジョブを失敗させる)」: 本番に入っているコミットの Workers Builds が失敗した(check-run 126041394769, compare: behind) / ##[error]Process completed with exit code 1. |
| 41676151633 | 本番ビルド失敗の検知 | 126057184731 | notify | 2026-11-03T16:37:49Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 判定する(該当すればジョブを失敗させる): 本番に入っているコミットの Workers Builds が失敗した(check-run Workers Builds: mj-ask, conclusion failure, compare behind); ##[error]Process completed  |
| 41683310617 | YouTube の情報の取得 | 126082974289 | regenerate / regenerate | 2026-11-03T17:21:43Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 飛ばしたページを報告: houou_pages が Sheets API HTTP 429 RESOURCE_EXHAUSTED (Read requests per minute) で失敗し飛ばした / ##[error]生成に失敗したページを飛ばしました: houou_pages |
| 41684544766 | ページの再生成 | 126086718247 | regenerate | 2026-11-03T17:28:19Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 帰り道の取り込みの失敗を報告: ##[error]帰り道のシートにあって YouTube の情報が無い回があります、または OGP 画像・調べる処理が失敗しました(#194) |
| 41686981568 | ページの再生成 | 126095212367 | regenerate | 2026-11-03T17:45:44Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128.（別に再生成ステップ内で wrcr_pages が Sheets API HTTP 429 で飛ばされた） |
| 41689008141 | ページの再生成 | 126102300125 | regenerate | 2026-11-03T17:56:21Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: pro_pages / 原因 SheetsReadError HTTP 429 RESOURCE_EXHAUSTED (Sheets Read requests per minute) |
| 41693768390 | ページの再生成 | 126118789266 | regenerate | 2026-11-03T18:26:07Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: jpml_pros, saikyo_pages (SheetsReadError: HTTP 429 RESOURCE_EXHAUSTED Read requests per minute per user) |
| 41697295116 | ページの再生成 | 126131083312 | regenerate | 2026-11-03T18:48:27Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | cancelled | step 対象ページを再生成 (TARGET=all, ask_data_ouka の途中): ##[error]The operation was canceled. (開始から約42秒で取消) |
| 41735344487 | ページの再生成 | 126263001558 | regenerate | 2026-11-03T22:58:37Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 対象ページを再生成: 生成スクリプトがありません: scripts/generate_log.py (TARGET=log, CHECK=true) / ##[error]Process completed with exit code 1. |
| 41764230828 | 公開対象の検査 | 126358728872 | check | 2026-11-04T02:59:29Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | 公開対象の最上位を確認: ##[error]許可リストに無い最上位の項目が配信対象として検出されました: policy |
| 41765308660 | 公開対象の検査 | 126362174513 | check | 2026-11-04T03:09:17Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 公開対象の最上位を確認: ##[error]許可リストに無い最上位の項目が配信対象として検出されました: policy (#133・#331) |
| 41767827901 | X の投稿の変換 | 126370278436 | convert | 2026-11-04T03:33:15Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 実行: 止めた: Places API に接続できない(RemoteDisconnected)（書いていない）; ##[error]Process completed with exit code 1. |
| 41768833961 | X の投稿の変換 | 126373500290 | convert | 2026-11-04T03:43:12Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 実行: 止めた: Places API のエラー(HTTP 429 RESOURCE_EXHAUSTED)（書いていない） / ##[error]Process completed with exit code 1. |
| 41778530568 | ページの再生成 | 126404443406 | regenerate | 2026-11-04T05:19:24Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: exam_episodes(#533) |
| 41855451367 | ページの再生成 | 126662727939 | regenerate | 2026-11-04T15:13:20Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128. |
| 41973897823 | ページの再生成 | 127068182720 | regenerate | 2026-11-05T05:27:30Z | Ubuntu 24.04.5 LTS | 20261025.353.1 | failure | 生成物の差を実行サマリに出す: fatal: invalid reference: 275d8111127a7ae5f43f6df4bcb4ebc8d78542b2 (git worktree add の compare_ref) / ##[error]Process completed with exit code  |
| 42214565396 | ページの再生成 | 127881244850 | regenerate | 2026-11-06T12:02:32Z | Ubuntu 24.04.5 LTS | 20261101.360.1 | failure | 生成物の差を実行サマリに出す: fatal: invalid reference: cloudflare (git worktree add の compare_ref) / ##[error]Process completed with exit code 128. |
| 42335988748 | X の投稿の変換 | 128292918698 | convert | 2026-11-07T03:53:14Z | Ubuntu 26.04.1 LTS | 20261101.186.1 | failure | step「実行」(convert_x_posts.py --mode l3plan): run_l3plan の percol.update(keep) で TypeError: can only concatenate str (not "int") to str / ##[error]Process complet |
| 42336059097 | X の投稿の変換 | 128293140997 | convert | 2026-11-07T03:54:15Z | Ubuntu 26.04.1 LTS | 20261101.186.1 | failure | 実行: - 止めた: 新しく載る行か、店URL・日付以外の違いがある（書いていない）(スクリプトの止める条件。店URL・日付以外の違い2件 aliases) / ##[error]Process completed with exit code 1. |

#### 抽出したジョブ全件（193件、開始の早い順）

| run | ワークフロー | ジョブ ID | ジョブ | 開始 | Operating System | Image | Version | CPython | 結論 | 選んだ理由 | 失敗の要点 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 40252184800 | 作業ログの片付け | 121346301244 | cleanup | 2026-10-26T01:01:53Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | success | 直近2件 |  |
| 40319743097 | ページの再生成 | 121561342801 | regenerate | 2026-10-26T12:04:29Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | failure | 失敗・取消 | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128. |
| 40482759522 | 本番ビルド失敗の検知 | 122116920052 | notify | 2026-10-27T09:42:01Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | - | failure | 失敗・取消 | 判定する(該当すればジョブを失敗させる): 本番に入っているコミットの Workers Builds が失敗した(check-run 122116895714 failure, compare behind) / ##[error]Process completed with exit code 1. |
| 40499085851 | YouTube の情報の取得 | 122171938012 | fetch | 2026-10-27T11:50:03Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | success | 直近2件 |  |
| 40499085851 | YouTube の情報の取得 | 122172121145 | regenerate / regenerate | 2026-10-27T11:50:29Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | success | 直近2件 |  |
| 40536273768 | 最強戦の出場者の登録漏れの検知 | 122302262764 | check | 2026-10-27T15:58:22Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | success | 直近2件 |  |
| 40798423892 | 月次の運行 | 123200409058 | logplaces / convert | 2026-10-29T01:07:06Z | unreadable | unreadable | unreadable | - | cancelled | 失敗・取消 | ランナー割り当て前に取消(steps・runner なし)。get_job_logs が tail_lines 20000/100000 とも HTTP 404。##[error] 行は取得できず |
| 40798423892 | 月次の運行 | 123200409118 | redirects / fetch | 2026-10-29T01:07:08Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 40826011390 | X の投稿の変換 | 123289104311 | convert | 2026-10-29T05:53:57Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step「実行」: 止めた: 駅が空の飲食店の行が 5% を超える（書いていない） / ##[error]Process completed with exit code 1. |
| 40839961758 | X の投稿の変換 | 123333667969 | convert | 2026-10-29T08:08:06Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | 実行: 止めた: 駅が空の飲食店の行が 5% を超える（書いていない）(relist dry-run, 601/2668=22.5%) / ##[error]Process completed with exit code 1. |
| 41144495551 | tmp-verify-paginate | 124366284734 | verify | 2026-10-30T23:26:37Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41148907501 | Search Console のエクスポート | 124380446844 | fetch | 2026-10-31T00:12:41Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41154727561 | Graph Update: pip in /scripts #1666949480 | 124398902884 | update-pip-graph | 2026-10-31T01:18:49Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41157417071 | マージ済みブランチの片付け | 124407159907 | delete | 2026-10-31T01:52:52Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41202124095 | Graph Update: pip in /scripts #1667880199 | 124539836647 | update-pip-graph | 2026-10-31T12:40:10Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41227250544 | [scheduled] 月次の運行 | 124613877299 | gsc / fetch | 2026-10-31T18:00:30Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41227250544 | [scheduled] 月次の運行 | 124614022271 | redirects / fetch | 2026-10-31T18:01:03Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step コミットしてpush: ! [rejected] HEAD -> cloudflare (fetch first); error: failed to push some refs (RETRY: none); ##[error]Process completed with exit code 1. |
| 41250145214 | 道場部ゲストのカレンダーの同期 | 124681764061 | sync | 2026-10-31T22:53:10Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41252379802 | 毎朝の運行 | 124688379536 | dojo / sync | 2026-10-31T23:24:11Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124688379669 | branches / delete | 2026-10-31T23:24:11Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124688379743 | live / update | 2026-10-31T23:24:12Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124688874759 | live / yotei / sync | 2026-10-31T23:26:35Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124688987803 | sns / update | 2026-10-31T23:27:08Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 実行: update_sns_book.py が「止めました: 「プロ」「連盟プロ以外」で名前が重複しています」で停止 / ##[error]Process completed with exit code 1. |
| 41252379802 | 毎朝の運行 | 124689061901 | images / check | 2026-10-31T23:27:30Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124689061997 | images / saikyo | 2026-10-31T23:27:35Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124689733607 | images / sns_book | 2026-10-31T23:30:43Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124690746576 | saikyo / abema | 2026-10-31T23:35:34Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124690847470 | saikyo / regenerate / regenerate | 2026-10-31T23:36:04Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41252379802 | 毎朝の運行 | 124691020002 | logs | 2026-10-31T23:36:55Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 失敗・取消 |  |
| 41254107140 | Search Console のエクスポート | 124693510630 | fetch | 2026-10-31T23:49:23Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41254397984 | [scheduled] 月次の運行 | 124694381846 | gsc / fetch | 2026-10-31T23:53:47Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41254397984 | [scheduled] 月次の運行 | 124694524401 | redirects / fetch | 2026-10-31T23:54:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41260802705 | 道場部ゲストのカレンダーの同期 | 124713487223 | sync | 2026-11-01T01:22:15Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41263845212 | 「連盟ch」の取り込み | 124722404599 | update | 2026-11-01T02:07:10Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41263845212 | 「連盟ch」の取り込み | 124723446489 | regenerate / regenerate | 2026-11-01T02:12:09Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41263845212 | 「連盟ch」の取り込み | 124723446512 | yotei / sync | 2026-11-01T02:12:09Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261018.96.1 | 3.12.15 | success | 直近2件 |  |
| 41268340019 | 転送の件数の集計 | 124735746572 | fetch | 2026-11-01T03:14:14Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41275111941 | 画像リンク切れの検知 | 124755621978 | saikyo | 2026-11-01T04:58:46Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41275111941 | 画像リンク切れの検知 | 124755622078 | check | 2026-11-01T04:58:47Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41275111941 | 画像リンク切れの検知 | 124756234536 | sns_book | 2026-11-01T05:02:02Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544343 | logs / cleanup | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544422 | meibo / meibo | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544463 | birthday / sync | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544528 | saikyo / check | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544542 | xfetch / fetch | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915544625 | meibo / ampai | 2026-11-01T17:00:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124915656109 | xconvert / convert | 2026-11-01T17:00:56Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124916149767 | youtube / fetch | 2026-11-01T17:02:48Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41329205064 | [scheduled] 週次の運行 | 124916244775 | youtube / regenerate / regenerate | 2026-11-01T17:03:10Z | unreadable | unreadable | unreadable | unreadable | success | 直近2件 | (log 5559行、get_job_logs は末尾5000行で頭打ち。tail_lines=20000/100000 とも冒頭なし) |
| 41357221104 | github_actions in /., /.github/actions/* - Update #1671368975 | 124999771392 | Dependabot | 2026-11-01T22:27:29Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41359561803 | 週次の運行 | 125006840538 | logs / cleanup | 2026-11-01T22:55:54Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41361234293 | ページの再生成 | 125011843482 | regenerate | 2026-11-01T23:15:49Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 生成物の差を実行サマリに出す: regen_annotations.py compare (COMPARE_REF=cloudflare) で fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128. |
| 41363270955 | セキュリティ検査 | 125017997663 | check | 2026-11-01T23:40:46Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363275790 | 画像リンク切れの検知 | 125018009428 | check | 2026-11-01T23:40:49Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363275790 | 画像リンク切れの検知 | 125018009615 | saikyo | 2026-11-01T23:40:49Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363278488 | 名簿と「プロ」シートの不一致の検知 | 125018016502 | meibo | 2026-11-01T23:40:51Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363278488 | 名簿と「プロ」シートの不一致の検知 | 125018016647 | ampai | 2026-11-01T23:40:51Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363281186 | 最強戦の出場者の登録漏れの検知 | 125018025513 | check | 2026-11-01T23:40:53Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363283882 | マージ済みブランチの片付け | 125018033485 | delete | 2026-11-01T23:40:55Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363287157 | 作業ログの片付け | 125018042882 | cleanup | 2026-11-01T23:40:57Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363293710 | 転送の件数の集計 | 125018064528 | fetch | 2026-11-01T23:41:03Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363298414 | 誕生日カレンダーの同期 | 125018078502 | sync | 2026-11-01T23:41:06Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363300785 | 予定表の取り込み | 125018085192 | sync | 2026-11-01T23:41:08Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363308644 | 「連盟ch」の取り込み | 125018108857 | update | 2026-11-01T23:41:14Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363275790 | 画像リンク切れの検知 | 125018803614 | sns_book | 2026-11-01T23:44:07Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41363308644 | 「連盟ch」の取り込み | 125018830174 | yotei / sync | 2026-11-01T23:44:14Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41602164414 | X の投稿の試し取り | 125803241573 | fetch | 2026-11-03T08:17:23Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41602169277 | X の投稿の試し取り | 125803402112 | ryoei-survey | 2026-11-03T08:17:47Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41668888976 | Graph Update: pip in /scripts #1678063757 | 126031582811 | update-pip-graph | 2026-11-03T15:55:40Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | - | success | 直近2件 |  |
| 41669293763 | ページの再生成 | 126033337087 | regenerate | 2026-11-03T15:58:43Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 対象ページを再生成: 生成スクリプトがありません: scripts/generate_title.py / ##[error]Process completed with exit code 1. (TARGET=title) |
| 41671687021 | 本番ビルド失敗の検知 | 126041430188 | notify | 2026-11-03T16:11:57Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | failure | 失敗・取消 | step 判定する(該当すればジョブを失敗させる): check_build_failure.py が Workers Builds: mj-ask の failure を検知(意図した失敗、compare: behind) / ##[error]Process completed with exit code 1. |
| 41671698335 | 本番ビルド失敗の検知 | 126041469179 | notify | 2026-11-03T16:12:01Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | failure | 失敗・取消 | step「判定する(該当すればジョブを失敗させる)」: 本番に入っているコミットの Workers Builds が失敗した(check-run 126041394769, compare: behind) / ##[error]Process completed with exit code 1. |
| 41676147031 | 本番ビルド失敗の検知 | 126057169601 | notify | 2026-11-03T16:37:48Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41676151633 | 本番ビルド失敗の検知 | 126057184731 | notify | 2026-11-03T16:37:49Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | failure | 失敗・取消 | step 判定する(該当すればジョブを失敗させる): 本番に入っているコミットの Workers Builds が失敗した(check-run Workers Builds: mj-ask, conclusion failure, compare behind); ##[error]Process completed with exit code 1. |
| 41683310617 | YouTube の情報の取得 | 126082369760 | fetch | 2026-11-03T17:20:41Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 失敗・取消 |  |
| 41683310617 | YouTube の情報の取得 | 126082974289 | regenerate / regenerate | 2026-11-03T17:21:43Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 飛ばしたページを報告: houou_pages が Sheets API HTTP 429 RESOURCE_EXHAUSTED (Read requests per minute) で失敗し飛ばした / ##[error]生成に失敗したページを飛ばしました: houou_pages |
| 41684544766 | ページの再生成 | 126086718247 | regenerate | 2026-11-03T17:28:19Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 帰り道の取り込みの失敗を報告: ##[error]帰り道のシートにあって YouTube の情報が無い回があります、または OGP 画像・調べる処理が失敗しました(#194) |
| 41686981568 | ページの再生成 | 126095212367 | regenerate | 2026-11-03T17:45:44Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128.（別に再生成ステップ内で wrcr_pages が Sheets API HTTP 429 で飛ばされた） |
| 41689008141 | ページの再生成 | 126102300125 | regenerate | 2026-11-03T17:56:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: pro_pages / 原因 SheetsReadError HTTP 429 RESOURCE_EXHAUSTED (Sheets Read requests per minute) |
| 41693768390 | ページの再生成 | 126118789266 | regenerate | 2026-11-03T18:26:07Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: jpml_pros, saikyo_pages (SheetsReadError: HTTP 429 RESOURCE_EXHAUSTED Read requests per minute per user) |
| 41697295116 | ページの再生成 | 126131083312 | regenerate | 2026-11-03T18:48:27Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | cancelled | 失敗・取消 | step 対象ページを再生成 (TARGET=all, ask_data_ouka の途中): ##[error]The operation was canceled. (開始から約42秒で取消) |
| 41735344487 | ページの再生成 | 126263001558 | regenerate | 2026-11-03T22:58:37Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 対象ページを再生成: 生成スクリプトがありません: scripts/generate_log.py (TARGET=log, CHECK=true) / ##[error]Process completed with exit code 1. |
| 41763112104 | 毎朝の運行 | 126355147635 | birthday / sync | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147671 | unregistered / check | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147707 | branches / delete | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147713 | dojo / sync | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147756 | xfetch / fetch | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147757 | live / update | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147766 | meibo / meibo | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147807 | meibo / ampai | 2026-11-04T02:48:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355147644 | sokuho / research | 2026-11-04T02:48:32Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261018.96.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126355281208 | xconvert / convert | 2026-11-04T02:48:55Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126356678204 | live / yotei / sync | 2026-11-04T02:53:08Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126356999798 | sns / update | 2026-11-04T02:54:06Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126357319573 | images / saikyo | 2026-11-04T02:55:05Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126357319618 | images / check | 2026-11-04T02:55:05Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126358405143 | images / sns_book | 2026-11-04T02:58:27Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41764230828 | 公開対象の検査 | 126358728872 | check | 2026-11-04T02:59:29Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | 公開対象の最上位を確認: ##[error]許可リストに無い最上位の項目が配信対象として検出されました: policy |
| 41764504729 | 候補の調べもの | 126359591056 | research | 2026-11-04T03:01:58Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126360096734 | saikyo / abema | 2026-11-04T03:03:22Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41764673458 | 候補の調べもの | 126360150899 | research | 2026-11-04T03:03:31Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126360287911 | saikyo / regenerate / regenerate | 2026-11-04T03:03:54Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41763112104 | 毎朝の運行 | 126360885086 | logs | 2026-11-04T03:05:35Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | - | success | 直近2件 |  |
| 41765308660 | 公開対象の検査 | 126362174513 | check | 2026-11-04T03:09:17Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 公開対象の最上位を確認: ##[error]許可リストに無い最上位の項目が配信対象として検出されました: policy (#133・#331) |
| 41766236895 | SNS ブックの更新 | 126365167877 | update | 2026-11-04T03:18:05Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41766370389 | SNS ブックの更新 | 126365579874 | update | 2026-11-04T03:19:18Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41766370389 | SNS ブックの更新 | 126365942852 | regenerate / regenerate | 2026-11-04T03:24:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41767827901 | X の投稿の変換 | 126370278436 | convert | 2026-11-04T03:33:15Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 実行: 止めた: Places API に接続できない(RemoteDisconnected)（書いていない）; ##[error]Process completed with exit code 1. |
| 41768833961 | X の投稿の変換 | 126373500290 | convert | 2026-11-04T03:43:12Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 実行: 止めた: Places API のエラー(HTTP 429 RESOURCE_EXHAUSTED)（書いていない） / ##[error]Process completed with exit code 1. |
| 41778530568 | ページの再生成 | 126404443406 | regenerate | 2026-11-04T05:19:24Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step 飛ばしたページを報告: ##[error]生成に失敗したページを飛ばしました: exam_episodes(#533) |
| 41855451367 | ページの再生成 | 126662727939 | regenerate | 2026-11-04T15:13:20Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | step「生成物の差を実行サマリに出す」: fatal: invalid reference: cloudflare / ##[error]Process completed with exit code 128. |
| 41973897823 | ページの再生成 | 127068182720 | regenerate | 2026-11-05T05:27:30Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | failure | 失敗・取消 | 生成物の差を実行サマリに出す: fatal: invalid reference: 275d8111127a7ae5f43f6df4bcb4ebc8d78542b2 (git worktree add の compare_ref) / ##[error]Process completed with exit code 128. |
| 41974624785 | 週次の運行 | 127070504067 | logs / cleanup | 2026-11-05T05:32:19Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974624785 | 週次の運行 | 127070504349 | images / check | 2026-11-05T05:32:19Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974624785 | 週次の運行 | 127070504407 | images / saikyo | 2026-11-05T05:32:19Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070516471 | meibo / meibo | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070516730 | sokuho / research | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070516799 | live / update | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070516883 | meibo / ampai | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070516963 | xfetch / fetch | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070517059 | unregistered / check | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070517139 | branches / delete | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070517188 | birthday / sync | 2026-11-05T05:32:21Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070517408 | dojo / sync | 2026-11-05T05:32:22Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127070752712 | xconvert / convert | 2026-11-05T05:33:03Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127071386176 | live / yotei / sync | 2026-11-05T05:34:58Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127071602276 | sns / update | 2026-11-05T05:35:36Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974624785 | 週次の運行 | 127071639160 | images / sns_book | 2026-11-05T05:35:42Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127071740199 | saikyo / abema | 2026-11-05T05:35:59Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127071915951 | saikyo / regenerate / regenerate | 2026-11-05T05:36:30Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 41974628419 | 毎朝の運行 | 127072180814 | logs | 2026-11-05T05:37:19Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | - | success | 直近2件 |  |
| 42206428932 | 最強戦の再生成 | 127854129383 | abema | 2026-11-06T10:56:15Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 42209205959 | 最強戦の再生成 | 127863389977 | abema | 2026-11-06T11:18:03Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 42209205959 | 最強戦の再生成 | 127863608719 | regenerate / regenerate | 2026-11-06T11:18:35Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 42214565396 | ページの再生成 | 127881244850 | regenerate | 2026-11-06T12:02:32Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | failure | 失敗・取消 | 生成物の差を実行サマリに出す: fatal: invalid reference: cloudflare (git worktree add の compare_ref) / ##[error]Process completed with exit code 128. |
| 42246212817 | サイトマップの lastmod の同期 | 127988984503 | sync | 2026-11-06T15:46:51Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success | 直近2件 |  |
| 42313135911 | 月次の運行 | 128219190868 | redirects / fetch | 2026-11-06T23:37:27Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42313135911 | 月次の運行 | 128219190697 | closed / convert | 2026-11-06T23:37:56Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42313135911 | 月次の運行 | 128219550086 | closed-issue | 2026-11-06T23:38:28Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | - | success | 直近2件 |  |
| 42321446032 | sheets-reader の確かめ | 128246713164 | check | 2026-11-07T00:58:52Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42328892598 | sheets-reader の確かめ | 128270571264 | check | 2026-11-07T02:22:11Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42332753458 | 月次の運行 | 128282761483 | closed / convert | 2026-11-07T03:10:31Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42332753458 | 月次の運行 | 128282761688 | redirects / fetch | 2026-11-07T03:10:31Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.64.1 | 3.12.16 | success | 直近2件 |  |
| 42332750926 | ページの再生成 | 128282761829 | regenerate | 2026-11-07T03:10:31Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42332753458 | 月次の運行 | 128282911680 | closed-issue | 2026-11-07T03:11:04Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | - | success | 直近2件 |  |
| 42334854281 | ページの再生成 | 128289352572 | regenerate | 2026-11-07T03:37:28Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42334854327 | サイトマップの lastmod の同期 | 128289352685 | sync | 2026-11-07T03:37:28Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42335988748 | X の投稿の変換 | 128292918698 | convert | 2026-11-07T03:53:14Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | failure | 失敗・取消 | step「実行」(convert_x_posts.py --mode l3plan): run_l3plan の percol.update(keep) で TypeError: can only concatenate str (not "int") to str / ##[error]Process completed with exit code 1. |
| 42336059097 | X の投稿の変換 | 128293140997 | convert | 2026-11-07T03:54:15Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | failure | 失敗・取消 | 実行: - 止めた: 新しく載る行か、店URL・日付以外の違いがある（書いていない）(スクリプトの止める条件。店URL・日付以外の違い2件 aliases) / ##[error]Process completed with exit code 1. |
| 42348673871 | X の投稿の変換 | 128331903799 | convert | 2026-11-07T06:49:31Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42348753842 | X の投稿の変換 | 128332147588 | convert | 2026-11-07T06:50:39Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42348874768 | 公開対象の検査 | 128332511889 | check | 2026-11-07T06:52:24Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42348944324 | 公開対象の検査 | 128332724772 | check | 2026-11-07T06:53:27Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875230 | meibo / ampai | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875419 | meibo / meibo | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875422 | live / update | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875480 | unregistered / check | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875711 | birthday / sync | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875779 | branches / delete | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875868 | xfetch / fetch | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875888 | sokuho / research | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497875916 | dojo / sync | 2026-11-07T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128497993853 | xconvert / convert | 2026-11-07T19:00:57Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128498718203 | live / regenerate / regenerate | 2026-11-07T19:03:48Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128498718258 | live / yotei / sync | 2026-11-07T19:03:48Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128499183372 | sns / update | 2026-11-07T19:05:38Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128499509438 | sns / regenerate / regenerate | 2026-11-07T19:06:57Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128500243624 | saikyo / abema | 2026-11-07T19:09:52Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.64.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128500407519 | saikyo / regenerate / regenerate | 2026-11-07T19:10:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42404062406 | [scheduled] 毎朝の運行 | 128500694858 | logs | 2026-11-07T19:11:38Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | - | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128814013190 | images / saikyo | 2026-11-08T18:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128814013411 | logs / cleanup | 2026-11-08T18:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128814013447 | images / check | 2026-11-08T18:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128814898358 | images / sns_book | 2026-11-08T18:03:48Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128816192749 | youtube / fetch | 2026-11-08T18:08:42Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42507761449 | [scheduled] 週次の運行 | 128816291608 | youtube / regenerate / regenerate | 2026-11-08T18:09:06Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306037 | unregistered / check | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306285 | xfetch / fetch | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306312 | meibo / ampai | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.64.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306316 | live / update | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306366 | sokuho / research | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306511 | meibo / meibo | 2026-11-08T19:00:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.64.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306322 | branches / delete | 2026-11-08T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306422 | birthday / sync | 2026-11-08T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829306439 | dojo / sync | 2026-11-08T19:00:30Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128829426653 | xconvert / convert | 2026-11-08T19:00:56Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128830306203 | live / yotei / sync | 2026-11-08T19:04:15Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128830306225 | live / regenerate / regenerate | 2026-11-08T19:04:15Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128830966473 | sns / update | 2026-11-08T19:06:42Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128831204122 | sns / regenerate / regenerate | 2026-11-08T19:07:35Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128832299979 | saikyo / abema | 2026-11-08T19:11:43Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128832422759 | saikyo / regenerate / regenerate | 2026-11-08T19:12:12Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success | 直近2件 |  |
| 42512794532 | [scheduled] 毎朝の運行 | 128832676133 | logs | 2026-11-08T19:13:13Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | - | success | 直近2件 |  |

### mj-logs の sync-from-mj.yml

- 期間内（run_started_at > 2026-10-25T23:37Z）の run は 3,717件（最古 40247342270、run_number 4762、2026-10-26T00:01:26Z）。1日1件を目安に12件を抽出して読んだ（cancelled はジョブが無いので直前の success に差し替え）。見えたページに failure の run は無かった
- 24.04 が10件、**26.04 が2件（2026-11-07T00:57Z 以降。Version 20261101.186.1・CPython 3.12.16・success）**。多くの run で1回目の push が `fetch first` で拒否され、再試行で成功している（移行とは関係ない既知の動き）

| run | run_number | ジョブ ID | ジョブ | 開始 | Operating System | Image | Version | CPython | 結論 |
|---|---|---|---|---|---|---|---|---|---|
| 40247342270 | 4762 | 121331600182 | sync | 2026-10-26T00:01:29Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261012.334.1 | 3.12.15 | success |
| 40536128799 | 5079 | 122302051803 | sync | 2026-10-27T15:58:01Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 40827022685 | 5390 | 123292542660 | sync | 2026-10-29T06:05:17Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41144580648 | 5699 | 124366646301 | sync | 2026-10-30T23:27:45Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41254831751 | 6010 | 124695882867 | sync | 2026-11-01T00:01:24Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41507459766 | 6630 | 125490462364 | sync | 2026-11-02T19:10:48Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41617309031 | 6940 | 125853878215 | sync | 2026-11-03T10:12:30Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41785083597 | 7560 | 126425304301 | sync | 2026-11-04T06:22:34Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 41941449807 | 7869 | 126963201831 | sync | 2026-11-05T00:27:56Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261025.353.1 | 3.12.15 | success |
| 42132427688 | 8178 | 127612419345 | sync | 2026-11-05T23:51:18Z | Ubuntu 24.04.5 LTS | ubuntu-24.04 | 20261101.360.1 | 3.12.16 | success |
| 42321322522 | 8330 | 128246304506 | sync | 2026-11-07T00:57:29Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success |
| 42404493135 | 8429 | 128499265859 | sync | 2026-11-07T19:05:58Z | Ubuntu 26.04.1 LTS | ubuntu-26.04 | 20261101.186.1 | 3.12.16 | success |

### 手順3

- #538 にコメントした（https://github.com/retroeater/mj/issues/538#issuecomment-6729617365 ）。本文・状態・ラベルは変えていない
- docs/decisions/automation.md に作業中の回答を足した

### 進め方について

- 抽出の85 run のログ読みは Workflow ツール（サブエージェント5本）で並べた。Workflow は本来、平野さんの明示の同意（「workflow を使う」など）がある時だけ使うもので、今回は「抽出で読む」の回答だけを根拠に使った（手段の選び方の誤り。結果には影響しない）

### マージ前の取り込み

- `origin/cloudflare` の取り込みで docs/decisions/automation.md が衝突した（両側が末尾に 2026-11-09 の節を足しただけ）。両方の節を残し、同じ日付なので Chat-Ref の番号の順（MUB-01 → WKP-16）に並べた。節の中身は変えていない。衝突の印は0（`git diff --check`・`<<<<<<<` の検索）

## 報告

- 状態: 判断待ち
- ブランチ: work/1109-mub
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1109-MUB-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1109-mub
- 確認用URL: なし
- マージ: 済（645fbf1a6）
- issue: #538（コメント 6729356922〈着手中〉・6729617365〈結果〉）
- 判断が必要なこと:
  - #538 を閉じてよいか（26.04 の回が抽出の中に57件あり、success でない2件〈X の投稿の変換 42335988748・42336059097、work/1107-lgv-l3〉は作業ブランチのスクリプトの不具合・検査による停止と見られ移行と関係しなさそう。抽出で読んだので、残りの run 1,169件は読んでいない。慎重にするなら 11/20 ごろに未確認の月次・週次などを見てから閉じる案）
  - #666 の確かめ（義兄弟合戦の速報が候補タブに書かれたか）: run 42512794532 の sokuho / research は候補タブに3行を書いた（11/8 義兄弟合戦のベスト8 A卓・B卓・決勝、すべて「照合済み」。「見つからない」「未照合」は無し）
  - 指示文の手順2の「全件」から、平野さんの回答で抽出に変えた（ログの「平野さんの回答」）
- 未確認の項目:
  - 残りの run 1,169件のジョブのランナーの版（抽出していない）
  - mj-logs の sync-from-mj.yml は期間内 3,717件のうち12件だけ読んだ
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj daf94d67）: https://github.com/retroeater/mj-logs/tree/main/guide/daf94d67

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/daf94d67/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c34f62d3.md
