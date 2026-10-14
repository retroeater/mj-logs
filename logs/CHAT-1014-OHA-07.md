# CHAT-1014-OHA-07

- 着手日時: 2026-10-14
- 対象issue: #564（関連）
- ブランチ: work/1014-oha-call
- 着手時HEAD: 7a3dac288

## 指示

【Claude作成】Claude Code 向け指示：morning_report.py が workflow_call で呼ばれるワークフロー（個別の run を持たない）をどう扱うかを確かめる（調査のみ） Chat-Ref: CHAT-1014-OHA-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（OHA-06 と同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-oha-call の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-oha-call を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-oha-call origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#564 の段③（WFL）で、毎朝の5本を `run-daily.yml` に束ね、各本は `workflow_call` で呼ばれる形になる見込み（WFL の伝言、2026-10-14）。呼ばれた本は個別の run を持たず、実行は `run-daily.yml` の run に含まれる。`scripts/morning_report.py`（と `actions_status.py`）がその形をどう扱うかを、コードを読んで確かめる。WFL が段③の指示を書く前の材料にする。コードは変えない。
決定（2026-10-14、平野さん）

* 調査のみ。`scripts/`・ワークフロー・文書は変えない（ログだけ）

前提（チャット側。平野さんの決定ではない。実物で確かめる）

* チャット側の見込み: (a) 1・3・4節は「各ワークフローの実行（API の新しい順に最大100件）」を見るので、`workflow_call` だけの本は実行が0件になり、何も出ない。(b) 2節の予定は `on.schedule` と `schedule.json` から作るので、呼ばれた本に `on.schedule`（保険）が残っている間はその予定で判定され、保険の実行が無ければ「未実行」と出る。(c) `run-daily.yml` は `schedule.json` の1行として予定を持ち、自分の run で判定される。(d) 新しいワークフローは直近20回が揃うまで所要時間の中央値が少ない件数で計算される。(e) 4節は `run-daily.yml` の最初の success で1回「初回成功」が出る
* WFL の段③の設計: 段④まで呼ばれた5本は `on.schedule` の保険を持つ。束ねた中の失敗は1節の「失敗したジョブ・ステップ」で読む（WFL の伝言）

手順

1. `scripts/morning_report.py`・`scripts/actions_status.py`・`scripts/tests/test_morning_report.py` を読み、次を表でログの「経過」に書く: ワークフローの一覧の取り方（`state` の扱い）／各節が見る実行の集合（契機・ブランチ・件数）／2節の予定の出どころと、`on.schedule` が無く `schedule.json` にも無いワークフローの扱い／`workflow_call` だけのワークフローで各節がどうなるか（実行0件のときの中央値・初回成功の扱いを含む）／`run-daily.yml` のような新しいワークフロー（実行が20件未満）の3・4節の扱い
2. 1節の「失敗したジョブ・ステップ」の取り方（jobs API のどの項目か）と、束ねた run の中で呼ばれた本のジョブ名・ステップ名がどう見えるか（`workflow_call` の呼び出しではジョブ名に呼び出し元のジョブ名が付く）を、GitHub の API の実物（mj の既存の `workflow_call` の例があればその run、無ければ公式文書）で確かめて書く
3. `## 報告` に、前提の (a)〜(e) それぞれの当否と、段③の指示で WFL が決めるべき項目（morning.md 側で変更が要るなら何か）を箇条書きで書く。`actions_status.py` の status.md が呼ばれた本をどう出すか（実行0件の表になるか）も1行

止まる条件

* 同じ論点の open issue がある（#564・#563 以外で morning.md の判定を変える issue）
* （未マージの work/ ブランチとの重なり）ログだけなので止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-OHA-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-OHA-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。雛形の4行は揃っている。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: ローカル・リモートとも無いため `git checkout -b work/1014-oha-call origin/cloudflare`
- 同じ論点の open issue（#564・#563 以外で morning.md の判定を変えるもの）: 検索で当たったのは #563 だけ。止まらない

### 1. コードの読み（cloudflare 7a3dac288 の `scripts/morning_report.py`・`scripts/actions_status.py`・テスト）

| 項目 | 実物 |
|---|---|
| ワークフローの一覧 | `actions_status.py` の `GET /repos/{repo}/actions/workflows?per_page=100`（API が返す全件。`state` では絞らない）。morning.md・status.md とも同じ一覧を使う |
| `state` の扱い | 1・3・4節は `state` を見ない（無効のワークフローの過去の実行も対象）。2節だけ `state` が `active` 以外を飛ばす。status.md は `state` を表示するだけ |
| 実行の取り方 | ワークフローごとに `GET /actions/workflows/{id}/runs?per_page=100`（新しい順に最大100件、契機・ブランチで絞らない）。status.md はその先頭5件 |
| 1節（失敗など） | 上の100件のうち開始（`run_started_at`、無ければ `created_at`）が直近26時間のもので、結論が `success`・`skipped` 以外。契機・ブランチで絞らない。**行は実行の1行だけで、ジョブ名・ステップ名は書かない**（ジョブを引くのは status.md だけ） |
| 2節（予約実行） | 予定は `on.schedule` の cron（`crons_in` がワークフローのファイルを読む）と `workers/scheduler/schedule.json` の `enabled` な行。どちらも無いワークフローは2節の対象外（何も書かない）。実行の対象は契機 `schedule`・`workflow_dispatch` でブランチ `cloudflare`、`created_at` が直近の予定時刻以降のもの（OHA-06） |
| 3節（所要時間） | 直近26時間の完了した実行（skipped を除く）の所要が 10分以上、または同じワークフローの新しい順20件のうち完了・非 skipped の所要の中央値の3倍以上。契機・ブランチで絞らない |
| 4節（初回成功） | 直近26時間の `success` で、取得した100件のうちそれより古い実行に `success` が無いもの。契機・ブランチで絞らない |
| 実行0件のワークフロー | `recent` が空なので1・3・4節は何も出ない。中央値は 0（`durations` が空のとき 0 にし、比の判定は `median > 0` のときだけ）。2節は予定があれば「遅れの可能性」／「未実行」になる |
| 実行が20件未満の新しいワークフロー | 3節: ある分だけで中央値を取る。1件目は自分が中央値になるので比では出ず、10分以上なら出る。2〜数件のうちは中央値が揺れやすい。4節: 最初の `success` が直近26時間にあれば1回出る（それより前の実行がすべて失敗でも、0件でも出る） |

### 2. `workflow_call` の実物（mj の既存の例）

mj には `workflow_call` で呼ばれるワークフローが既にある: `regenerate-page.yml`（`on.workflow_call`）を `update-live-channel.yml`・`update-sns-book.yml`・`regenerate-saikyo.yml` が `uses: ./.github/workflows/regenerate-page.yml` で呼ぶ（呼ぶ側のジョブ名はどれも `regenerate`）。

- update-live-channel の run 38453650761（workflow_dispatch、cloudflare、success）の `GET /actions/runs/{id}/jobs`: ジョブは `update`・`yotei`・**`regenerate / regenerate`**（呼ぶ側のジョブ名 / 呼ばれた側のジョブ名）の3件。呼ばれた側のジョブの `steps` も同じ応答に並ぶ（22件、`Set up job`・`リポジトリをチェックアウト`・…）。`workflow_name` は呼ぶ側の run の名前（`[scheduled] 「連盟ch」の毎日の取り込み`）
- `GET /actions/workflows/regenerate-page.yml/runs?per_page=100`: 100件の契機は push 79・workflow_dispatch 19・schedule 2 で、`path` はすべて regenerate-page.yml。**呼ばれた分の実行は、呼ばれた側のワークフローの一覧に出ない**（呼ぶ側の run の中のジョブになる）
- status.md の「失敗したジョブ・ステップ」（`actions_status.py` の `failed_steps`）: 失敗した run の jobs API の `jobs[].name`・`jobs[].conclusion` と `jobs[].steps[].name`・`steps[].conclusion` を見る。束ねた run が失敗すれば、呼ばれた本の失敗は `ジョブ「regenerate / regenerate」 ステップ「…」` の形で、呼ぶ側（`run-daily.yml`）の節に出る見込み（上の jobs API の形から。失敗の実例はまだ見ていない）。対象は status.md に載る直近5回のうち失敗したものだけ

### 3. 今の5本の所要（参考、cloudflare の schedule・workflow_dispatch の success、新しい順に5件、秒）

update-live-channel 7・438・6・266・10／update-sns-book 39・218・37・30・42／sync-dojo-calendar 33・32・26・29・30／delete-merged-branches 22・37・22・31・16／check-image-links 466・55・183・61・74（regenerate-saikyo 31・26）。直列に束ねると、多い日は10分を超える

### 4. 束ねた後の保険のゲート（気づいたこと）

`update-live-channel.yml` の保険の予約のゲートは、自分のワークフローの実行の一覧から「当日・cloudflare・題が `[scheduled]`・success」の `workflow_dispatch` を探す（docs/notes/scheduler-worker.md「保険の予約実行のゲート」）。`run-daily.yml` から呼ばれた分は自分の一覧に出ないため、束ねた後はゲートが「当日の予約の起動なし」と判定し、保険の予約がそのまま本処理を実行する（二重実行）見込み

## 報告

- 状態: 完了
- ブランチ: work/1014-oha-call
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-OHA-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-oha-call
- 確認用URL: なし
- マージ: 済（ログのみ。作業ブランチの先頭を cloudflare へ fast-forward で push）
- issue: なし（#564 の段③の材料）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし
- 前提 (a)〜(e) の当否:
  - (a) 当たり。呼ばれた分は呼ばれた側の実行の一覧に出ない（regenerate-page.yml の100件で確かめた）ため、`workflow_call` だけの本は1・3・4節に何も出ない。ただし同じ本に push・workflow_dispatch・schedule の契機が残っていれば、その分の実行は今までどおり出る
  - (b) 当たり。`on.schedule` が残っている間は保険の予定で判定され、保険の実行（契機 schedule、cloudflare）が無ければ「遅れの可能性」／「未実行」、3時間を超えて始まれば「遅れて実行」。`schedule.json` から外した後は Worker の時刻では判定しない
  - (c) 当たり。`schedule.json` の行の `workflow` が `run-daily.yml` なら、`run-daily.yml` 自身の run（workflow_dispatch・cloudflare）で判定される
  - (d) 当たり（補足: 1件目は自分が中央値になるので比では出ず、10分の絶対値だけで出る）
  - (e) 当たり（補足: 契機・ブランチで絞らないので、作業ブランチでの試運転が先に success になっていれば、cloudflare の最初の success では出ない）
  - WFL の伝言「束ねた中の失敗は1節の失敗したジョブ・ステップで読む」は**外れ**: morning.md の1節はジョブ名・ステップ名を書かない（実行の1行だけ）。ジョブ・ステップは status.md の直近5回の失敗にだけ出る
- WFL が段③の指示で決めること（morning.md 側の変更の要否を含む）:
  - 1節で束ねた中のどの本が落ちたかを morning.md で読みたいなら、1節に失敗したジョブ名（`呼ぶ側 / 呼ばれた側`）を足す変更が要る（今は status.md を読む）
  - 3節: `run-daily.yml` は5本の合計の所要になり、10分の絶対値の判定で毎朝のように出る見込み。`run-daily.yml` を10分の判定から外す・閾値を本ごとに持つ、などを決める
  - 2節: 呼ばれた5本の保険の `on.schedule` が残る段④までは、5本が保険の予約で個別に判定される（保険の遅れで「遅れて実行」が出る）。保険を2節の対象から外すか、今のまま見るか
  - 保険のゲート（update-live-channel）が `run-daily.yml` から呼ばれた分を見つけられず、保険が二重に実行する見込み（経過の4）。ゲートの見る先を `run-daily.yml` の run に変える必要がある
  - 4節: `run-daily.yml` の最初の success は1回「初回成功」に出る（試運転を cloudflare 以外でしたなら出ない）。出してよいか
- status.md: 呼ばれるだけ（`workflow_call` のみ）のワークフローは、ワークフローの一覧に出れば「実行なし」の節になる見込み。`workflow_call` だけのファイルが workflows API の一覧に出るかは、mj に実例が無く確かめていない（regenerate-page.yml は push 等の契機も持つため一覧に出ている）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 18f8ff58）: https://github.com/retroeater/mj-logs/tree/main/guide/18f8ff58

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/18f8ff58/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
