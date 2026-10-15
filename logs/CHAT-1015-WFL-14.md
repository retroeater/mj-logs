# CHAT-1015-WFL-14

- 着手日時: 2026-10-15
- 対象issue: #564・#263
- ブランチ: work/1015-wfl-s5
- 着手時HEAD: 664643716

## 指示

【Claude作成】Claude Code 向け指示：段③の調べと設計（毎朝のワークフローを run-daily.yml にまとめる）・#263 のクローズ Chat-Ref: CHAT-1015-WFL-14 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。`.github/`・`workers/`・`scripts/` は変えない 貼る時機: いつでも（CHAT-1014-WFL-13 は完了済み）。ただし JST 03:45〜05:15（毎朝のワークフローの起動の間）はマージの push をしない 作業ブランチ: クラウドセッションで実行する。work/1015-wfl-s5 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-wfl-s5 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む（この指示では `.github/` を変えないが、設計の前提にする）。

目的
#564 の段③の 1 本目。毎朝のワークフローを 1 本の `run-daily.yml` にまとめる設計を、実物を調べて決める。実装は次の指示で行う。この指示ではコードを変えない。あわせて #263 を閉じる。
決定（2026-10-14〜15、平野さん）

* 毎朝の運行は 1 本のワークフロー `run-daily.yml` にまとめる。先に済ませる本は `needs:` で書く。各本は `workflow_call` で呼べるようにし、手動実行（`workflow_dispatch`）も残す（2026-10-14）
* Worker の取りこぼしの埋め合わせは ACF 系列（592334b3）で実装済み。WFL では同じものを作らず、Worker のコードを変えない（2026-10-14）
* ワークフローの改名は `<動詞>-<対象>.yml` に揃える。ファイル名だけでなく `name:`・run-name・入力の description・冒頭コメント・docs も同じ規則で揃える（2026-10-14）
* #263（regenerate-page の concurrency）は今閉じる。本番では 10/15 朝の 3 回の呼び出しが重ならず、待ちは起きなかった。待ちの動きは WFL-10 の作業ブランチで確かめ済み。段③で毎朝の本を `needs:` でつなぐと重なり自体がほぼ起きなくなるため、本番の重なりを待たない（2026-10-15）

前提（チャット側。平野さんの決定ではない）

* 今の毎朝の起動（`workers/scheduler/schedule.json`、2026-10-15 のチャット側の読み。要確認）: update-live-channel 04:00、update-sns-book 04:10、sync-dojo-calendar 04:15、delete-merged-branches 04:20、check-image-links 04:30。regenerate-saikyo は check-image-links の完了を `workflow_run`（`name:`「画像リンク切れの検知」で参照）で起動する
* 保険の `schedule`（GitHub 側）を持つのは update-live-channel・sync-dojo-calendar・delete-merged-branches・check-image-links（要確認）。update-live-channel には「当日の `[scheduled]` の成功があれば保険は何もしない」ゲートがある。まとめた後は、このゲートが run-daily の実行を見ないと保険が二重に動く。保険の `schedule` を外すのは段④（#504 の観察の後）なので、段③ではゲートの見る先を直す
* Worker の朝の確かめ（06:00、#506）は、本ごとの `[scheduled]` の題の実行を探している見込み（要確認。どの値で探すか〈ファイル名・`name:`・題〉を `workers/scheduler/` で調べる）。schedule.json の 5 行を `run-daily.yml` の 1 行にしても、Worker のコードを変えずに確かめが通るか（通らなければ何が要るか）が設計の要
* 先に済ませる順の案（チャット側の案。実物で検める）: update-live-channel → update-sns-book → check-image-links → regenerate-saikyo。sync-dojo-calendar・delete-merged-branches は他と関係しないので並行。前の本が失敗したときに後の本を動かすか（`if: ${{ !cancelled() }}` など）は本ごとに決める
* 呼び出しの入れ子: run-daily → update-live-channel・update-sns-book・regenerate-saikyo → regenerate-page で 2 段になる。GitHub の入れ子の上限、`permissions` は呼ぶ側が上限になること、`secrets: inherit` の要否、呼ばれる側のワークフロー全体の `concurrency` が呼ぶ側とどう効くか（同じ組の名前で詰まらないか）を確かめる
* 入力 `scheduled` と resolve-mode（`.github/actions/resolve-mode`）: 呼ばれたときに `scheduled` を run-daily から渡す形にする。各本の run-name の `[scheduled]` の目印は、呼ばれた本では題にならない（呼ばれた本の実行は run-daily の中のジョブになる）。mj-logs の morning.md・status.md（`scripts/morning_report.py`・`scripts/actions_status.py`）と Worker の確かめに効く
* update-live-channel の中の yotei（予定表のカレンダー）を `sync-yotei.yml` に、YouTube の取り込みを `fetch-youtube.yml` に分ける案（2026-10-14 のチャット側の案）。分けたときに先行関係（`needs:`）がどうなるかを含め、分けるか・まとめたままにするかを判断材料と推奨つきで出す
* 2026-10-15 の朝に update-sns-book が飛ばしたページ video_mtsuku の失敗（`NO_COLUMN: O`）は MTK の担当（#569）。この指示では扱わない
* 未マージの work/ ブランチで、毎朝の本・`workers/scheduler/`・`scripts/morning_report.py`・`scripts/actions_status.py` を変えているものがあれば、設計の前提として書く（ACF・OHA 系列の作業が近い）

手順

1. 「前提」の要確認をすべて実物で確かめ、表にしてログに書く（各本: 起動・保険の `schedule`・ゲート・`concurrency`・`permissions`・入力・呼んでいる本・呼ばれている所・出力〈issue・コミット・mj-logs〉）
2. 設計をログに書く。少なくとも次を含める:
   * `run-daily.yml` のジョブの並びと `needs:`・`if:`（前の本が失敗したときの扱いを本ごとに）
   * 各本を `workflow_call` にする差分の形（入力・出力・`secrets`・`permissions`・`concurrency`）
   * schedule.json の行をどうするか、Worker の朝の確かめと取りこぼしの埋め合わせ（ACF-18）が Worker のコードを変えずに通るか
   * update-live-channel の保険のゲートの見る先、regenerate-saikyo の `workflow_run` を外す手順
   * mj-logs の morning.md・status.md への影響と直す箇所（本ごとの節が run-daily のジョブに変わること）
   * `sync-yotei.yml`・`fetch-youtube.yml` に分けるか
   * 改名の表（今のファイル名 → 新しい名前、`name:`・run-name も）と、改名を段③のどの指示で行うか。`name:` を参照する所（`workflow_run`・Worker・mj-logs・テスト・docs）の洗い出し
   * 実装を何本の指示に分けるか、その順と、それぞれの確かめ方（作業ブランチの手動実行で書く道筋をどこまで通せるか）
3. 平野さんに決めてもらう点を、判断材料・推奨・その理由をつけて `## 報告` の「判断が必要なこと」に書く（選択肢を出すときは比較と推奨を必ず付ける）
4. #263 に「段②（44920ebbc）で入れた concurrency で閉じる。本番で 10/15 朝に重なりは無く、待ちの動きは WFL-10 の作業ブランチで確かめ済み（#564）」とコメントし、「状況:」ラベルを外してクローズする
5. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-15（CHAT-1015-WFL-14）` として足す。#564 に設計の要点とログの場所をコメントする（本文は変えない）。完了報告のうえ cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）

止まる条件

* 調べの途中で、`run-daily.yml` にまとめると Worker のコードを変えないと朝の確かめ・取りこぼしの埋め合わせが成り立たないと分かった（設計の残りは書き、選択肢と推奨を判断待ちに入れて止まる。マージはしてよい）
* #263 の状態が open でない、または他セッションの着手中コメントがある（#263 は触らずに進め、報告に書く）
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-WFL-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-WFL-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1015-WFL-14 は未使用（WFL は同じセッションの続きの連番）
- 作業ブランチ: work/1015-wfl-s5 はリモート・ローカルとも無かったので origin/cloudflare（664643716）から作った
- 0. 指示欄の末尾は指示文の最後の行と一致。docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ（設計に効く点: 新規のワークフローは既定ブランチに無いうちは作業ブランチで手動実行できない〈404〉ため、副作用の無い状態でマージして cloudflare で手動実行してから有効化する／`workflow_dispatch` を持たない本はマージ前に検証できない／ワークフローを足す・消す・改名するときは「ワークフローの一覧」の表を同じコミットで直す〈`scripts/tests/test_workflows_table.py`〉）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- 着手は JST 11:03（03:45〜05:15 の外）

### 手順1: 実物の確かめ

#### Worker（`workers/scheduler/`）

- `schedule.json` の 5 行は前提どおり: update-live-channel 04:00・update-sns-book 04:10・sync-dojo-calendar 04:15・delete-merged-branches 04:20・check-image-links 04:30（すべて enabled）
- Worker は**行の `workflow`（ファイル名）だけ**で動く（`scheduler.mjs`）:
  - 起動: `POST /actions/workflows/<file>/dispatches`（`ref: cloudflare`、`inputs: {scheduled: 'true'}`）
  - 取りこぼしの埋め合わせ（ACF-18、7e6d723a6）: 予定から 5・10・20・40 分後の回で `GET /actions/workflows/<file>/runs?event=workflow_dispatch&branch=cloudflare&created>=予定` を引き、`display_title` が `[scheduled]` で始まる実行が無ければ起動する（60 分まで）
  - 朝の確かめ（06:00、06:15・06:30 は書き直し）: 当日・`event=workflow_dispatch` の実行のうち `display_title` が `[scheduled]` で始まる最新の1件の結論を見る。**ブランチでは絞らない**（作業ブランチで `scheduled` 付きの実行をすると、その日の朝の確かめに数えられる）
  - `name:` は見ない。テスト（`test/*.mjs`）のファイル名は例の文字列で、`schedule.json` は読まない
- `schedule.json` を変えて cloudflare に入ると Workers Builds の `mj-scheduler` がデプロイする（watch paths `workers/scheduler/**`、docs/notes/scheduler-worker.md）。`index.mjs` が `schedule.json` を import してビルドに焼き込むので、表の変更はデプロイで効く
- 結論: **`schedule.json` の 5 行を `{"workflow": "run-daily.yml", "time": "04:00"}` の 1 行にすれば、Worker のコードを変えずに起動・埋め合わせ・朝の確かめが通る**（`run-daily.yml` が入力 `scheduled` を持ち、run-name で `[scheduled]` を題の先頭に付けることが条件）。止まる条件には当たらない

#### 毎朝の本（cloudflare 664643716）

| 本（`name:`） | 起動 | 保険の `schedule`（JST） | ゲート | `concurrency` | `permissions` | 入力 | 呼んでいる本 | 呼ばれている所 | 出力 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| update-live-channel.yml（「連盟ch」の毎日の取り込み） | Worker 04:00・手動 | 毎日 06:43（`43 21 * * *`） | あり（job update の step gate: 当日・cloudflare・`[scheduled]`・success の `update-live-channel.yml` の実行があれば schedule は何もしない。`actions: read`） | ワークフロー全体 `update-live-channel`・false | 全体 contents: write／job update: contents write・issues write・actions read／job yotei: contents read・issues write | apply・verify・allow_many_changes・allow_shrink・allow_many・backfill・yotei_apply・calendar_max_delete・calendar_apply・unused_report・scheduled | regenerate-page（job regenerate、`live_pages title_pages`、`secrets: inherit`） | Worker・schedule | 層1のコミット（cloudflare へ push、`pull --rebase` で3回）・3層のシート・予定表のシート・放送対局のカレンダー・issue（#446 の常設・#479 の常設・#450） |
| update-sns-book.yml（SNS ブックの更新） | Worker 04:10・手動 | なし | なし | 全体 `update-sns-book`・false | 全体 contents: read／job regenerate: contents write | mode・dry_run・scheduled | regenerate-page（job regenerate、pages_changed のとき `all`） | Worker | SNS ブック【1】【2】【3】・（変化があれば）全ページの再生成のコミット |
| sync-dojo-calendar.yml（道場部ゲストのカレンダー同期） | Worker 04:15・手動 | 毎日 07:12（`12 22 * * *`） | なし（毎日2回動く。WFL-02 で段④まで置くと決定） | 全体 `sync-dojo-calendar`・false | contents: read・issues: write | image・month・apply・scheduled | なし | Worker・schedule | 道場部のカレンダー・issue のコメント |
| delete-merged-branches.yml（マージ済みの作業ブランチを削除する） | Worker 04:20・手動 | 毎日 07:53（`53 22 * * *`） | なし（同上） | 全体 `delete-merged-branches`・false | contents: write | dry_run・scheduled | なし | Worker・schedule | ブランチの削除 |
| check-image-links.yml（画像リンク切れの検知） | Worker 04:30・手動 | 週1 月曜 03:00（`0 18 * * 0`。job check だけ） | なし | **なし** | contents: read・issues: write | saikyo_limit・saikyo_resolve_test・dry_run・scheduled | なし | Worker・schedule。**完了で regenerate-saikyo が `workflow_run`（`name:`「画像リンク切れの検知」）で起動** | issue（#352 の常設・最強戦の写真の常設 #534） |
| regenerate-saikyo.yml（最強戦の再生成） | check-image-links の完了（`workflow_run`、cloudflare）・手動 | なし | なし（取り消された回だけ動かない） | 全体 `regenerate-saikyo`・false | 全体 contents: write／job abema: contents read | abema・regenerate | regenerate-page（job regenerate、`saikyo_pages`） | `workflow_run` | ABEMA ブック【2】・saikyo/ の再生成のコミット |
| regenerate-page.yml（ページの再生成） | push・週1 月曜 05:37・手動・`workflow_call` | — | — | job regenerate `regenerate-page-${{ github.ref }}`・false・`queue: max` | 指定なし（呼ばれたときは呼ぶ側の job が上限） | target_page | なし | update-live-channel・update-sns-book・regenerate-saikyo・（ほか check-meibo などのコメント） | 生成物のコミット（実行ブランチへ、`pull --rebase --autostash` で3回） |

- 前提との食い違い: check-image-links の保険は「毎日」ではなく**週1（月曜 03:00、job check だけ）**。前提の「保険の `schedule` を持つのは update-live-channel・sync-dojo-calendar・delete-merged-branches・check-image-links」は合っている。check-image-links には `concurrency` が無い
- 10/15 の朝の所要（mj-logs `actions/status.md`）: update-live-channel 7分06秒・update-sns-book 4分24秒（failure、#569）・sync-dojo-calendar 0分37秒・delete-merged-branches 0分40秒・check-image-links 8分07秒・regenerate-saikyo 1分16秒。直列にすると live→sns→images→saikyo で約 21 分（04:00 に起動して 04:25 前後に終わる見込み。06:00 の確かめに間に合う）
- checkout: 後に動く本は `ref: ${{ github.ref }}`（実行ブランチの最新）で取るので、同じ run-daily の中で前の本がコミットした生成物を読む。update-live-channel の job update だけ既定（`github.sha`）だが、先頭で動き push は `pull --rebase` で再試行する
- 呼ばれたときに変わる値: 呼ばれる側の `github.event_name`・`github.ref`・`github.workflow`・`github.workflow_ref` は呼ぶ側（run-daily）のもの。毎朝の本はどれも `github.event_name == 'schedule' || inputs.scheduled == true` で予約の起動を見分けており、呼ばれたときは `workflow_dispatch`＋`inputs.scheduled`（`workflow_call` の入力）で今と同じに動く。`concurrency` の組は名前が固定の文字列（`github.workflow` を使っていない）なので、run-daily の組と名前がぶつからない
- regenerate-page の OGP・帰り道の知らせは `contains(github.workflow_ref, '/regenerate-page.yml@')` のときだけ動く。呼ばれたとき（今も update-live-channel などから）は偽で、run-daily からの入れ子でも変わらない
- 未マージの work/ ブランチで毎朝の本・`workers/scheduler/`・`scripts/morning_report.py`・`scripts/actions_status.py`・`.github/actions/` を変えるもの: なし（`origin/work/1014-wfl-s3` が手元の追跡に残っていたが、リモートでは削除済み〈`git ls-remote` が空〉で対象外）

#### mj-logs の朝の報告（`scripts/morning_report.py`・`scripts/actions_status.py`）

- どちらもワークフローの**ファイル**ごとに節・行を作る（API の `path` と `schedule.json` の行から。`name:` は見出しの「名前」に出すだけ）
- morning.md の「2. 予約実行の未実行」は、`schedule.json` の行と各ファイルの `schedule` の cron から予定を出し、予定の後の cloudflare の schedule・workflow_dispatch の実行を探す。呼ばれた本の実行は run-daily の実行の中のジョブになり、呼ばれた本のファイルには実行が付かない
- morning.md の「3. 所要時間の異常」は `secs >= 600` か「直近 20 回の中央値の 3 倍以上」。**run-daily は毎回 600 秒（10 分）を超える見込みなので、今の判定のままでは毎朝「所要時間の異常」に出る**（直す箇所は設計に書いた）
- status.md は失敗した実行についてジョブ名と失敗したステップ名を書く（`/actions/runs/<id>/jobs?per_page=100`）。run-daily の失敗は「live / update」のような入れ子のジョブ名で出る。morning.md の「1. 失敗など」はファイル名と結論だけで、どの本が落ちたかは出ない

#### 既に決まっていること（docs/decisions/automation.md、WFL-01・WFL-02）

- 束ねる本の名前は `run-daily.yml`（動詞 `run` を7つ目に足す）
- 改名: `sitemap-lastmod.yml` → `regenerate-sitemap.yml`、`regenerate-saikyo.yml` はそのまま、`check-meibo.yml` の `name:` はそのまま
- 切り出し: `fetch-youtube.yml` は **`regenerate-page.yml` から**（YouTube のチャンネル情報と帰り道の情報の取り直しとコミットだけ、最後に regenerate-page を `all` で呼ぶ）。予定表の切り出し先は `sync-yotei.yml`（update-live-channel の job yotei）
- `schedule.json` の 5 行を `run-daily.yml` の 1 行に置き換えるのは段③で同時に行う（#504 の観察の後に回さない）
- sync-dojo-calendar・delete-merged-branches の保険の `schedule` は段④まで置く
- **指示文の前提との食い違い**: 前提は「update-live-channel の中の YouTube の取り込みを `fetch-youtube.yml` に分ける案」と書くが、WFL-02 で平野さんが決めた `fetch-youtube.yml` は regenerate-page からの切り出しで、update-live-channel の YouTube の取り込み（層1、`fetch_live_channel_raw.py`）とは別のもの。設計は WFL-02 の決定に従い、update-live-channel の層1は分けない案を推奨した（下の「分けるか」）。CLAUDE.md「Chat-Ref」の「前提・ルールの側を優先」による
- WFL-01 の対応表の残り（`assets-check.yml` → `check-assets.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`、`name:` の言い回しの統一）は平野さんの確定が記録に無い（WFL-02 の決定は上の3点だけ）。判断待ちに入れた

### 手順2: 設計

#### run-daily.yml のジョブ

```
run-daily.yml（name: 毎朝の運行）
run-name: ${{ inputs.scheduled && '[scheduled] 毎朝の運行' || '' }}
on: workflow_dispatch（入力 scheduled〈Worker 用、手では付けない〉・dry_run〈手動用、既定 true〉）
permissions: {}                       ← 各 job で呼ぶ本の分だけ与える
concurrency: { group: run-daily, cancel-in-progress: false }
jobs:
  live:     uses update-live-channel.yml                       （内で regenerate-page・sync-yotei〈分けた後〉を呼ぶ）
  sns:      needs live;   if !cancelled();  uses update-sns-book.yml
  images:   needs sns;    if !cancelled();  uses check-image-links.yml
  saikyo:   needs images; if !cancelled();  uses regenerate-saikyo.yml
  dojo:     uses sync-dojo-calendar.yml          （先行なし）
  branches: uses delete-merged-branches.yml      （先行なし）
```

| job | needs | 前の本が失敗したとき | 理由 |
| --- | --- | --- | --- |
| live | なし | — | 先頭。層1のコミットが後の本の生成物の土台 |
| sns | live | 動かす（`!cancelled()`） | SNS ブックは live の結果を読まない。並べるのは regenerate-page の push を前後に分けるため（#263） |
| images | sns | 動かす | 今も sns の成否と関係なく 04:30 に動いている。sns が落ちた日も jpml_pros.html・最強戦の写真は確かめたい |
| saikyo | images | 動かす | 今の `workflow_run` も検知の成否に関係なく動く（取り消された回だけ動かない）。`!cancelled()` で同じ |
| dojo・branches | なし | — | ほかと読み書きが重ならない。並行に動かす |

- 案との違い: WFL-01 の案は saikyo を `needs: sns` にしていたが、今の動き（検知の完了の後）と同じ並びにする（images → saikyo）。images の job saikyo・sns_book が写真を確かめた後に再生成するのは今と同じ
- run-daily 全体の結論は、どれか1つの job が失敗すると failure。失敗した job だけの再実行（GitHub の「Re-run failed jobs」）で同じ実行が success になれば、Worker の朝の確かめも success を読む（同じ run の最新の試行の結論）
- 作業ブランチで `scheduled` 付きの実行をすると Worker の朝の確かめに数えられる（ブランチで絞らないため）。書く道筋の試験は 06:30 より後に行う

#### 各本を workflow_call にする差分

- 各本の `on:` に `workflow_call:` を足し、入力は `scheduled`（boolean、既定 false）と、手動の書かない道筋に要る入力だけを `workflow_call.inputs` にも書く（`workflow_dispatch` の入力はそのまま残す）:
  - update-live-channel: `scheduled`・`apply`
  - update-sns-book: `scheduled`・`mode`・`dry_run`
  - sync-dojo-calendar: `scheduled`・`apply`
  - delete-merged-branches: `scheduled`・`dry_run`
  - check-image-links: `scheduled`・`dry_run`（`saikyo_resolve_test` は既定 `''` で宣言しておく。宣言しないと null になり、`== ''` の判定に頼ることになる）
  - regenerate-saikyo: `abema`・`regenerate`（`scheduled` は無くてよい。今も予約の区別を持たない）
- run-daily の手動実行（`scheduled` なし）は**書かない道筋だけ**にする: `dry_run: true`（既定）のとき、live に `apply: false`、sns に `mode: check`、dojo に `apply: false`、branches・images に `dry_run: true`、saikyo に `abema: check`・`regenerate: true` を渡す。書く道筋は `scheduled` だけ（各本の手動実行は今のまま残すので、書く手動実行は各本で行う）
- 出力: 要らない（run-daily の後の判断に使う値が無い）
- `secrets`: 呼ぶ job ごとに `secrets: inherit`（今の regenerate-page の呼び方と同じ）
- `permissions`: 呼ばれる側の job は呼ぶ側の job の権限を超えられないので、run-daily の job ごとに与える（live: contents write・issues write・actions read／sns: contents write／images: contents read・issues write／saikyo: contents write／dojo: contents read・issues write／branches: contents write）
- `concurrency`: 呼ばれる側のワークフロー全体の組（update-live-channel など、固定の名前）はそのまま。run-daily は別の名前の組 `run-daily`。regenerate-page は job の組 `regenerate-page-<ref>`。名前がすべて違うので詰まらない見込み（要確認: 呼ばれる側のワークフロー全体の `concurrency` が呼ばれたときにどう効くかは GitHub の文書に明記が無い。実装の最初の指示の手動実行で確かめる）
- 入れ子: run-daily → update-live-channel → regenerate-page（sync-yotei を分けた後は → sync-yotei も）で3段。GitHub の入れ子の上限（以前の文書は4段、今は10段とされる）のどちらにも収まる。手動実行で確かめる
- 呼ばれた本の run-name は使われない（題は run-daily の run-name だけ）。各本の run-name はそのまま残す（各本の手動実行・保険の実行で使う）

#### schedule.json・Worker の朝の確かめ・埋め合わせ

- `schedule.json` の 5 行を `run-daily.yml` 04:00 の 1 行にする（WFL-02 の決定のとおり段③で行う）。Worker のコードは変えない
- 朝の確かめは run-daily の1行になる。どの本が落ちたかは Worker の通知（#506 のコメント）には出ない（status.md のジョブ名・ステップ名で見る）
- 埋め合わせは run-daily に対して今と同じに働く（04:05・04:10・04:20・04:40 の回で `[scheduled]` の run-daily が無ければ起動）。run-daily が起動して途中の本が落ちた日は埋め合わせない（今も同じ）
- `schedule.json` の切り替えと、run-daily の `scheduled` の道筋・update-live-channel のゲートの直し・regenerate-saikyo の `workflow_run` の削除は**同じマージ**に入れる（片方だけだと、朝の本が2回動くか、1回も動かない）

#### update-live-channel の保険のゲート・regenerate-saikyo の workflow_run

- ゲートの見る先: 当日・cloudflare・`[scheduled]` の **run-daily.yml** の実行を引き、その実行のジョブ（`/actions/runs/<id>/jobs`）のうち名前が `live / update` のジョブが success なら保険は何もしない。run-daily 全体の結論で見ると、ほかの本（例: 10/15 の update-sns-book の失敗）が落ちた日に保険が update-live-channel をもう1回動かしてしまう。移行の間に update-live-channel.yml を直接起動した実行（手動で `scheduled` を付けた場合）も今のまま見る（両方の和）
- 引けなかったときは止めずに実行する今の方針（動かないより2回動くほう）はそのまま
- regenerate-saikyo の `workflow_run` は外し、`workflow_dispatch` を残して `workflow_call` を足す。呼ばれた check-image-links の実行は「画像リンク切れの検知」という名前の実行にならないので、`workflow_run` を残しても毎朝は起動しない（週1の保険・手動の check-image-links のときだけ起動する）。外すのは run-daily に saikyo の job を入れるのと同じマージ。`if:` の `github.event_name != 'workflow_run' || …` の条件も外す

#### mj-logs の morning.md・status.md

- 本ごとの毎朝の実行は run-daily の1行（status.md は run-daily の節）に変わる。呼ばれた本の節に残るのは手動実行と保険の実行だけ
- 直す箇所（`scripts/morning_report.py`、段③の切り替えと同じ指示）:
  1. 「3. 所要時間の異常」の絶対値の判定（600 秒以上）を、中央値が 600 秒未満の本だけに効かせる（run-daily は中央値との比で見る）。直さないと毎朝 run-daily が出る
  2. 「1. 失敗など」の行に、失敗したジョブ名を足す（status.md と同じ `jobs_by_run` を `actions_status.py` から渡す）。run-daily の失敗でどの本が落ちたかを morning.md だけで読めるようにする
- 「2. 予約実行の未実行」は `schedule.json` の行（run-daily）と各本の保険の cron で今のまま働く（保険の実行は残るので、呼ばれた本の cron の予定にも実行が付く）。変えない
- status.md（`actions_status.py`）は変えない（ジョブ名・ステップ名は今も出る。`per_page=100` は run-daily のジョブ数〈入れ子を含めて 15 前後〉に足りる）

#### sync-yotei.yml・fetch-youtube.yml に分けるか

- `sync-yotei.yml`（update-live-channel の job yotei）: WFL-02 で切り出しは決定済み。分け方として、run-daily の job にするのではなく、**update-live-channel の job yotei を `uses: ./.github/workflows/sync-yotei.yml` に置き換える**（入れ子で呼ぶ）案を推奨する。
  - 比較: (a) update-live-channel の中から呼ぶ（推奨）: 保険の実行（schedule）でも yotei が今と同じに動く。run-daily の並びは変わらない。入れ子は3段のまま（run-daily → live → sync-yotei）／(b) run-daily の job にする（`needs: live`）: 保険の実行で yotei が動かなくなる（sync-yotei に保険とゲートを別に足すことになる）
- `fetch-youtube.yml`: WFL-02 の決定（regenerate-page から切り出す）のとおり。毎朝の本ではない（regenerate-page の週次 `all` から切り出す）ので run-daily には入れない。update-live-channel の層1（YouTube の取り込み）は分けない（層1→【1】→【2】→【3】が順に依存し、分けても並行にならず、ゲートと入力が2本に割れるだけ）

#### 改名の表

| 今のファイル（`name:`） | 新しいファイル（`name:`／run-name の `[scheduled]` の後） | 状態 | 行う指示 |
| --- | --- | --- | --- |
| （新） | run-daily.yml（毎朝の運行） | 決定済み（WFL-02） | 段③-1 |
| update-live-channel.yml（「連盟ch」の毎日の取り込み） | 同じ（「連盟ch」の取り込み） | `name:` は WFL-01 の案。確定待ち | 段③-3 |
| update-sns-book.yml（SNS ブックの更新） | 同じ（同じ） | 変えない | — |
| sync-dojo-calendar.yml（道場部ゲストのカレンダー同期） | 同じ（道場部ゲストのカレンダーの同期） | `name:` は案。確定待ち | 段③-3 |
| delete-merged-branches.yml（マージ済みの作業ブランチを削除する） | cleanup-branches.yml（マージ済みブランチの片付け） | WFL-01 の案。確定待ち | 段③-3 |
| check-image-links.yml（画像リンク切れの検知） | 同じ（同じ） | 変えない（`workflow_run` を外した後は `name:` を参照する所が無くなる） | — |
| regenerate-saikyo.yml（最強戦の再生成） | 同じ | 決定済み（WFL-02） | — |
| sitemap-lastmod.yml（サイトマップのlastmodを同期） | regenerate-sitemap.yml（サイトマップの lastmod の同期） | ファイル名は決定済み（WFL-02） | 段③-3 |
| assets-check.yml（公開対象を検査する） | check-assets.yml（公開対象の検査） | WFL-01 の案。確定待ち | 段③-3 |
| notify-build-failure.yml（本番ビルドの失敗の通知） | check-build-failure.yml（本番ビルド失敗の検知） | WFL-01 の案。確定待ち | 段③-3 |
| （新、job yotei から） | sync-yotei.yml（予定表の取り込み） | 決定済み（WFL-02） | 段③-3 |
| （新、regenerate-page から） | fetch-youtube.yml（YouTube の情報の取り直し） | 決定済み（WFL-02） | 段③-4 |

`name:`・ファイル名を参照する所（改名のときに追う所。`git grep`、docs/logs・docs/decisions を除く）:

- `workflow_run`（`name:` で参照）: regenerate-saikyo.yml の `['画像リンク切れの検知']` だけ。段③-2 で外すので、改名の時点では残らない
- Worker: `schedule.json` の `workflow`（段③-2 で run-daily の1行になった後は、改名する本は表に載らない）。テスト（`test/*.mjs`）は例の文字列で、直さなくても通る
- update-live-channel のゲートの URL（自分のファイル名・段③-2 からは run-daily.yml とジョブ名 `live / update`）。run-daily の job 名を変えるときはゲートも直す
- regenerate-page の `contains(github.workflow_ref, '/regenerate-page.yml@')`（regenerate-page は改名しない）
- mj-logs: `actions_status.py`・`morning_report.py` はファイル名を持たない（API の path と schedule.json から作る）。消した・改名した古いファイルの節は status.md に残らない（WFL-11 で確かめた）
- テスト: `scripts/tests/test_workflows_table.py`（docs/notes/github-actions.md の一覧の表と実物の一致）、`test_morning_report.py`・`test_actions_status.py` は例の文字列
- docs: delete-merged-branches は cloud-sessions・github-actions・scheduler-worker・static-generation、assets-check は CLAUDE.md を含む 12 か所（WFL-01 の表）。CLAUDE.md の文言の直しは同じ指示で行う

#### 実装の分け方（段③を4本に）

| 指示 | 中身 | 確かめ方 |
| --- | --- | --- |
| 段③-1 | 6 本に `workflow_call`（上の入力）を足す。`run-daily.yml` を足す（`workflow_dispatch` のみ、`scheduled` の道筋も書くが `schedule.json` には載せない）。docs の一覧の表に run-daily の行 | 各本を作業ブランチで今の手動実行（書かない入力）で動かし、`workflow_call` を足しても動きが変わらないこと。run-daily は新規のため作業ブランチで起動できない（404）ので、書かない道筋だけのつもりでマージし、cloudflare で `dry_run: true` の手動実行をして、入れ子・`permissions`・`concurrency`・`needs`・`!cancelled()` を確かめる（saikyo に `regenerate: true` を渡すと run-daily → regenerate-saikyo → regenerate-page の3段が通る。差分が無ければコミットしない） |
| 段③-2（切り替え） | `schedule.json` を run-daily の1行に。update-live-channel のゲートを run-daily のジョブ `live / update` を見る形に。regenerate-saikyo の `workflow_run` を外す。`morning_report.py` の2点。docs（scheduler-worker・github-actions） | テスト（unittest・Worker の `node --test`）。run-daily を作業ブランチで `dry_run` の手動実行（この時点では cloudflare に run-daily があるので作業ブランチでも起動できる）。ゲートは作業ブランチで update-live-channel を手動実行して「引いた結果」の1行を読む。書く道筋の初回は切り替えの翌朝 04:00 の本番（Worker の朝の確かめ・status.md・morning.md で読む）。戻すときは `schedule.json` を5行に戻す1コミット |
| 段③-3（改名・sync-yotei） | 改名の表の確定分と sync-yotei.yml の切り出し（update-live-channel の job yotei を `uses:` に）。docs・CLAUDE.md・一覧の表 | `test_workflows_table.py`。改名した本・sync-yotei を作業ブランチで手動実行（新しい名前のファイルは既定ブランチに無いので、作業ブランチで起動できない。段③-1 と同じく書かない道筋でマージして cloudflare で手動実行）。run-daily の `dry_run` の手動実行 |
| 段③-4（fetch-youtube） | regenerate-page から fetch-youtube.yml を切り出す（WFL-02 の (a)） | fetch-youtube.yml は新規なので段③-1 と同じ扱い。regenerate-page の `all` の手動実行 |

- 段③-1 と ③-2 を分けるのは、run-daily が新規で作業ブランチでは試せないため（③-1 のマージの後に cloudflare で書かない道筋を確かめてから、③-2 で切り替える）
- 改名（③-3）を切り替え（③-2）の後にするのは、切り替えの後は Worker の表に改名する本が載らず、Worker のデプロイと改名の時機を合わせなくて済むため

### 手順4・5

- #263: open で、他セッションの着手中コメントは無かった。「状況:」ラベルは付いていなかった（「分野: パフォーマンス」「分野: 自動化」）。指示の文面でコメントし、クローズした（completed）
- `docs/decisions/automation.md` に「## 2026-10-15（CHAT-1015-WFL-14）」を足した
- JST 11:11 に origin/cloudflare を merge で取り込み（衝突なし。CHAT-1010-MCK-04 のログが入った）、祖先を確かめて `git push origin work/1015-wfl-s5:cloudflare`（bcec5f2be..d28a0c278）

## 報告

- 状態: 判断待ち
- ブランチ: work/1015-wfl-s5（マージ済み。削除は delete-merged-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1015-WFL-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-wfl-s5
- 確認用URL: なし
- マージ: 済（d28a0c278。ログ・docs/decisions のみ）
- issue: #263（クローズ）・#564（設計の要点をコメント）
- 判断が必要なこと:
  1. run-daily.yml の並び: live → sns → images → saikyo（直列、後の3つは前が失敗しても `!cancelled()` で動かす）と、dojo・branches を並行。推奨: この形（今の時刻の並びと `workflow_run` の動きを保ち、regenerate-page の push が前後に分かれる。所要は約 21 分で 06:00 の確かめに間に合う）。代案は saikyo を sns の後に images と並行にする形（数分早く終わるが、写真の確かめの前に再生成する点が今と変わる）
  2. update-live-channel の保険のゲートの見る先: (a) run-daily の実行のジョブ `live / update` の結論（推奨。ほかの本が落ちた日に保険が二重に動かない）／(b) run-daily 全体の結論（API 1回で済むが、10/15 のように update-sns-book が落ちた日は update-live-channel が保険でもう1回動く）
  3. sync-yotei.yml の呼び方: (a) update-live-channel の job yotei を `uses: sync-yotei.yml` に置き換える（推奨。保険の実行でも yotei が今と同じに動き、run-daily は変わらない）／(b) run-daily の job にする（保険の実行で yotei が動かなくなる）
  4. 指示文の前提の「YouTube の取り込みを fetch-youtube.yml に分ける」は、WFL-02 の決定（fetch-youtube.yml は regenerate-page から切り出す）と別のもの。推奨: WFL-02 の決定のとおり regenerate-page から切り出し、update-live-channel の層1は分けない（層1→【1】→【2】→【3】は順に依存し、分けても並行にならない）。前提を変える意図だったかの確認
  5. 改名の残り（WFL-01 の案で、確定の記録が無いもの）: `assets-check.yml` → `check-assets.yml`、`delete-merged-branches.yml` → `cleanup-branches.yml`、`notify-build-failure.yml` → `check-build-failure.yml`、`name:` の言い回し（「〜の同期」「〜の取り込み」など、経過の改名の表）。推奨: 案のとおり確定し、段③-3 でまとめて行う（切り替えの後は Worker の表に載らないので、改名と Worker のデプロイの時機を合わせなくて済む）
  6. 実装の分け方: 段③-1（6本に `workflow_call`・run-daily.yml を書かない道筋でマージし cloudflare で手動実行）→ ③-2（切り替え: schedule.json・ゲート・`workflow_run` の削除・morning_report.py の2点）→ ③-3（改名・sync-yotei）→ ③-4（fetch-youtube）。推奨: この順（run-daily は新規で作業ブランチでは起動できないため、③-1 で cloudflare に入れて確かめてから ③-2 で切り替える）
  7. morning_report.py の直し（③-2 に含める）: 所要時間の絶対値（600 秒）の判定を中央値が 600 秒未満の本だけにする・失敗の行に失敗したジョブ名を足す。推奨: 両方行う（前者を直さないと毎朝 run-daily が「所要時間の異常」に出る）
- 未確認の項目: 呼ばれる側のワークフロー全体の `concurrency` が workflow_call のときにどう効くか（GitHub の文書に明記が無い）と、入れ子3段が通るか（段③-1 の cloudflare での手動実行で確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a177d10d）: https://github.com/retroeater/mj-logs/tree/main/guide/a177d10d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a177d10d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
