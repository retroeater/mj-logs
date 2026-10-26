# CHAT-1026-OHA-14

- 着手日時: 2026-10-26
- 対象issue: #563
- ブランチ: work/1026-oha
- 着手時HEAD: 59f3c4838

## 指示

【Claude作成】Claude Code 向け指示：morning.md の3節で、中央値の基準を「3節に載せる実行と同じ種類」に絞る（#563、MRP-01 への返事） Chat-Ref: CHAT-1026-OHA-14 マージ: 承認済み（チャットで）。条件は下の「止まる条件」のマージの条件をすべて満たすときだけ。1つでも外れたらマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-oha の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1026-oha を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-oha origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#563 の CHAT-1026-MRP-01 のコメント（2026-10-26）の「頼むこと」1・2 に、平野さんの決定で答える（CHAT-1026-OHA-01 は識別子の重複の確認で、ログを作る前に止まったので欠番。OHA の系列の続きとしてこの番号で出し直す）。morning.md の「3. 所要時間の異常」で、比べる基準（中央値）を、3節に載せる実行と同じ種類の実行だけから取るようにする。
決定（2026-10-26、平野さん）

* MRP-01 の「頼むこと」1（中央値の母集団）: 3節に載せる実行と同じ種類の実行だけを基準にする。cloudflare の実行で、かつ予約の実行かどうか（題の先頭が `[scheduled]` か）が、判定する実行とそろっているものに絞る
* MRP-01 の「頼むこと」2（run-weekly のように中身が変わる週次の束ね）: 3節の対象のままにする。外さない。基準値をワークフローごとに変える仕組みも作らない。上の絞り込みで直す
* この直しは今入れる。#563 は 10/28 の5日分の確認の後に閉じる（この直しのための別の issue は立てない）

前提（チャット側。平野さんの決定ではない）

* チャット側が cloudflare の `scripts/morning_report.py`（2026-10-26 09:00 JST ごろの版）を REST で読んだ。基準は `write()` の中の `durations = ... runs[:MEDIAN_RUNS]`（比の判定の中央値）と、`others = ... runs[:MEDIAN_RUNS] if o is not r`（絶対値の判定の中央値）の2か所。どちらもブランチで絞っていない。チャット側の案は、両方を同じ条件（cloudflare、かつ「予約の実行か」が判定する実行と同じ）で絞ること。比の中央値は判定する実行ごとに変わってよい（要確認）
* 予約の実行かどうかの見分け: run-daily・run-weekly は、Worker からの起動の題の先頭に `[scheduled]` が付いている（チャット側が REST で、両方の直近の実行の `display_title` を見て確かめた）。`[scheduled]` の目印が無いワークフローでは、`event == 'schedule'` を予約の実行とみなす案がある。既存の `SCHEDULED_EVENTS` などとの合わせ方は実物を見て決めてよい。決め方はログに書く
* `write()` に渡る実行のデータに `display_title` が入っているか（要確認）。入っていなければ、取得のところで足してよい
* 見込み: 10/26 の run-weekly の実行（run 40214364620、cloudflare・[scheduled]、所要 12分21秒）は、同じ種類のほかの実行が無い。今の作りの「ほかの実行が無ければ絶対値で見る」によって、「10分以上」の判定で3節に残る。来週からは 12分台どうしで比べるので出なくなる見込み
* 3節の見出しは「cloudflare の予約の実行だけ」の趣旨に直す。文面は実物に合わせてよい（今の見出しは「cloudflare の実行だけ。直近20回の中央値の…」）
* docs/decisions/automation.md にある 3節の判定の既存の決定（「中央値＋5分以上」の追加、#563。「ほかの実行の中央値が10分未満の本だけ絶対値で見る」）は変えない。基準を取る実行の範囲が変わるだけ

手順

1. #563 に着手中のコメントを書く（本文・ラベル・状態は変えない）。`git branch -r --no-merged origin/cloudflare` で、`scripts/morning_report.py` の `write()` の3節の判定か、そのテストを変えている未マージのブランチが無いかを確かめる。そのあと、上の「前提」の2か所の作りが今もそのとおりかを確かめ、違えば止まる
2. 中央値の基準（比・絶対値の両方）を、判定する実行と同じ種類（cloudflare、かつ予約の実行かどうかが同じ）の実行だけから取るように直す。3節の見出しも直し、テストを足す。テストには少なくとも次の2つを入れる。(a) run-weekly の今の5件の形（cloudflare・[scheduled] が1件で 12分21秒、ほかは手動の 0分台）で、その1件が「10分以上」で出ること。(b) 予約の実行が十分にあるワークフローで、cloudflare の手動の実行や作業ブランチの実行が基準に混ざらないこと
3. 同じ API の応答で、新旧のコードの morning.md を作って比べる。3節の差を全件、表にしてログに書く（行・ワークフロー・新旧の中央値・差の理由）。1節・2節・4節・5節と、status.md・運行表に差が無いことも確かめる。マージの条件を満たせば cloudflare へ入れる。そのあと、#563 に、平野さんの決定と結果を書く。中身はログの `## 経過` の「結果」から引用する。マージ後の最初の mj-logs の同期の書き出し（actions/morning.md）の3節もログに引用する。同期を待つのは CLAUDE.md の待機の上限まで。それまでに書き出されなければ「未確認」と書く

止まる条件

* 「前提」の2か所の作りが今と違う。または、未マージの work/ ブランチが `write()` の3節の判定か、そのテストの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #563 に、他のセッションの着手中のコメントで、まだ完了のコメントが無いものがある
* マージの条件（すべて満たすときだけマージする。1つでも外れたら、マージせずにここまでの結果をログに書いて止まる）
   * 既存と追加のテストがすべて通る
   * 手順3の比較で、3節の差がすべて、基準の実行の範囲を絞ったこと（cloudflare・予約の実行かどうか）で説明できる。説明できない差が1行でもあれば止まる
   * 10/26 の run-weekly（run 40214364620）の行が、新しいコードでも3節に残る（「10分以上」の判定で）
   * 1節・2節・4節・5節と、status.md・運行表に差が無い
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら、原因を報告に書いたうえで残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-OHA-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-OHA-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（手順0・1）

- 手順0: このログの `## 指示` の最後の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- Chat-Ref: `CHAT-1026-OHA-14` のコミットは全ブランチに無い。OHA の系列の続きの番号（前の指示 CHAT-1026-OHA-01 は識別子の重複〈1014-OHA-02〜07・1023-OHA-08〜13〉で、ログを作る前に止まった）
- issue: #563 は open。着手中のコメントは OHA-08〜13 と MRP-01 のもので、どれも後に完了・結果のコメントがある。着手中のコメントを書いた（issuecomment-6405863007）
- ブランチ: `work/1026-oha` はローカルにもリモートにも無かったので `git checkout -b work/1026-oha origin/cloudflare`（59f3c4838）
- 未マージの `work/` ブランチ: `scripts/morning_report.py`・`scripts/tests/test_morning_report.py` を変えたものは無い
- 前提の2か所: 今もそのとおり（比の中央値 `durations = [s for s in map(seconds, runs[:MEDIAN_RUNS]) …]`、絶対値の判定の `others = [seconds(o) for o in runs[:MEDIAN_RUNS] if o is not r …]`。どちらもブランチで絞っていない）。ただし2か所は `write()` ではなく、`write()` から呼ぶ `render()` の中にある（作りは同じなので止めていない）
- `display_title`: actions_status.py が引くワークフローの実行の一覧（`/actions/workflows/{id}/runs`）の応答に入っている（morning_report.py の `daily_runs()` が既に使っている）。取得は変えていない

### 実装（4b5c46ae1）

- `is_scheduled(run)`: 予約の実行か。題（`display_title`）の先頭が `[scheduled]`（`SCHEDULED_TITLE`、Worker からの起動）か、`event == 'schedule'`（GitHub の予約）なら真
  - 決め方: Worker から起動するワークフローはどれも run-name で `[scheduled]` を付ける（#504）。GitHub の `schedule` で動く本（fetch-youtube の保険など）は目印が無いので event で見る。`SCHEDULED_EVENTS`（`schedule`・`workflow_dispatch`）は2節の「予約のあとに起動があったか」のためのもので、`workflow_dispatch` は手動も含むので、ここでは使わない
- `same_kind(runs, run)`: 判定する実行と同じブランチで、`is_scheduled` もそろった実行の直近 `MEDIAN_RUNS`（20）回。取得済みの各ワークフローの直近100回から選ぶ。判定する実行自身を含みうる（今までの `runs[:MEDIAN_RUNS]` と同じ）
- `render()` の3節: 比の中央値（`durations`）と絶対値の判定の `others` を、どちらも `same_kind()` から取る（判定する実行ごとに変わる）。3節は今までどおり cloudflare の実行だけを判定する。cloudflare の手動・push の実行も3節に載り、それらは cloudflare の手動・push の実行と比べる
- 3節の見出し: 「3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）」
  - 前提には「cloudflare の予約の実行だけ」の趣旨とあった。しかし決定は基準の範囲を絞るだけで、3節に載せる実行（cloudflare の手動・push を含む）は変えていない。見出しは実物の判定に合わせた（指示文の記述どうしが食い違ったので、決定の側に従った）
- 既存の判定（「中央値＋5分以上」、ほかの実行の中央値が10分未満の本だけ絶対値で見る）は変えていない
- 検査（test_morning_report.py の SlowTest）:
  - (a) run-weekly の10/26の形（予約の1件が 12分21秒、ほかは手動の 0分台〈cloudflare 2件・作業ブランチ 2件〉）で、その1件が「10分以上」で出る（基準は自分だけで、中央値は 12分21秒と出る）
  - (b) 予約の実行が7件（14分・15分）あるワークフローで、cloudflare の手動の実行8件と作業ブランチの `[scheduled]` の実行8件（どれも 1分）が基準に混ざらない（混ざると中央値が 1分になり、3倍・＋5分を満たして出てしまう）
  - `is_scheduled` の見分け（`event: schedule`・題の `[scheduled]`・目印なし）
- `python3 -m unittest discover -s scripts/tests`: 1,572 件 OK
- 修正前のコード（origin/cloudflare を `git archive` で展開し、test_morning_report.py だけ差し替え）: 3件が落ちた（(b) は FAIL〈旧では出る〉、(a) は FAIL〈旧でも行は出るが、中央値の表示が 0分44秒〉、`is_scheduled` の検査は ERROR）
- docs/notes/github-actions.md の morning.md の項に、中央値の基準の範囲を足した

### 確かめ: 同じ API の応答で新旧のコードを回す

- 2026-10-26 09:59 JST の API の応答を作業ファイルの置き場所に貯め、作業ブランチのコードと origin/cloudflare（59f3c4838）のコードで `actions_status.py` を1回ずつ回した（OHA-10 の作業用の `run_cached.py`。書き出した時刻を揃えた。mj-logs には書いていない）。API の呼び出しは新旧とも同じ 62 回
- status.md: 差なし。運行表（actions/index.html）: 差なし
- morning.md の差（全行）:

```
15c15
< ## 3. 所要時間の異常（cloudflare の実行だけ。直近20回の中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）
---
> ## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）
17,20c17,20
< - 2026-10-25 19:40 regenerate-page.yml success（push・cloudflare）所要 11分11秒（直近20回の中央値 1分07秒） https://github.com/retroeater/mj/actions/runs/40185765407
< - 2026-10-25 19:42 regenerate-page.yml success（push・cloudflare）所要 18分05秒（直近20回の中央値 1分07秒） https://github.com/retroeater/mj/actions/runs/40185846374
< - 2026-10-26 02:00 run-weekly.yml success（workflow_dispatch・cloudflare）所要 12分21秒（直近20回の中央値 0分44秒） https://github.com/retroeater/mj/actions/runs/40214364620
< - 2026-10-26 07:47 fetch-youtube.yml success（schedule・cloudflare）所要 10分06秒（直近20回の中央値 7分36秒） https://github.com/retroeater/mj/actions/runs/40241832250
---
> - 2026-10-25 19:40 regenerate-page.yml success（push・cloudflare）所要 11分11秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185765407
> - 2026-10-25 19:42 regenerate-page.yml success（push・cloudflare）所要 18分05秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185846374
> - 2026-10-26 02:00 run-weekly.yml success（workflow_dispatch・cloudflare）所要 12分21秒（直近20回の中央値 12分21秒） https://github.com/retroeater/mj/actions/runs/40214364620
> - 2026-10-26 07:47 fetch-youtube.yml success（schedule・cloudflare）所要 10分06秒（直近20回の中央値 7分55秒） https://github.com/retroeater/mj/actions/runs/40241832250
```

- 1節・2節・4節・5節と、題・時刻の行: 差なし。3節は見出しの文と、4行の中央値の表示だけが違う。3節に載る行（4行）は新旧で同じで、増えた行・消えた行は無い

| 行 | ワークフロー | 判定する実行の種類 | 旧の基準（中央値／ほかの実行の中央値） | 新の基準（中央値／ほかの実行の中央値） | 差の理由 | 3節に出る判定 |
|---|---|---|---|---|---|---|
| 2026-10-25 19:40（40185765407、11分11秒） | regenerate-page.yml | cloudflare・手動/push | 直近20回＝cloudflare の push 7件＋作業ブランチの手動 13件: 1分07秒／1分02秒 | cloudflare の push 17件＋手動 3件: 0分57秒／0分57秒 | 作業ブランチの実行を基準から外した | 3倍以上かつ＋5分以上、10分以上（両方） |
| 2026-10-25 19:42（40185846374、18分05秒） | regenerate-page.yml | cloudflare・手動/push | 同上: 1分07秒／1分02秒 | 同上: 0分57秒／0分57秒 | 同上 | 同上 |
| 2026-10-26 02:00（40214364620、12分21秒） | run-weekly.yml | cloudflare・予約（`[scheduled]`） | 5件（予約1・cloudflare の手動2・作業ブランチ2）: 0分44秒／0分42秒 | 自分の1件だけ: 12分21秒／なし | 手動・作業ブランチの実行を基準から外した。同じ種類のほかの実行が無いので「10分以上」で出る | 10分以上（ほかの実行が無い） |
| 2026-10-26 07:47（40241832250、10分06秒） | fetch-youtube.yml | cloudflare・予約（`event: schedule`） | 4件（schedule 2・cloudflare の手動2）: 7分36秒／5分44秒 | schedule の2件: 7分55秒／5分44秒 | cloudflare の手動の実行を基準から外した | 10分以上（ほかの実行の中央値 5分44秒が10分未満） |

- マージの条件: テストはすべて通る／3節の差はすべて基準の範囲を絞ったことで説明できる／run 40214364620 の行は新しいコードでも3節に残る（「10分以上」で）／1節・2節・4節・5節と status.md・運行表に差が無い。すべて満たした

### 気づいたこと（今回は直していない）

- OHA-08（c0a8ebd57）で、3節を cloudflare の実行だけにするために `render()` のループに入れた `if r.get('head_branch') != SCHEDULED_BRANCH: continue` が、4節「初回成功」の判定より前にある。そのため、作業ブランチの実行の初回成功が4節に出なくなっている（決定 B は3節だけの話だった）。テストの形で確かめた: 作業ブランチの「failure の後の success」は4節が「なし」、同じ形の cloudflare の実行は4節に出る
- 今回の条件「4節に差が無いこと」に合わせて直していない。直すなら `continue` を4節の判定の後ろへ移す（3節の判定だけを飛ばす）1か所の変更で済む。#563 に書いた

### マージ

- push の直前に再 fetch し、origin/cloudflare（59f3c4838）が HEAD の祖先であることを確かめて `git push origin work/1026-oha:cloudflare`（59f3c4838..51e9cf8cb）。取り込みは要らなかった
- 51e9cf8cb の check-run: check success、Workers Builds: mj success（完了の2件。in_progress の2件は始まりの記録）、notify は skipped。check-assets（push、run 40252208053）success、check-build-failure は skipped

### 結果

- 決定（2026-10-26、平野さん）: MRP-01 の「頼むこと」1 は、3節の中央値（比・絶対値の両方）の基準を、判定する実行と同じ種類（cloudflare で、予約の実行かどうかがそろったもの）の実行だけにする。「頼むこと」2 は、run-weekly などの週次の束ねも3節の対象のままにし、基準値をワークフローごとに変える仕組みは作らない
- 実装: 予約の実行は「題の先頭が `[scheduled]`（Worker からの起動）か、`event` が `schedule`（GitHub の予約）」とした。基準は、取得済みの直近100回から、同じ種類の直近20回を判定する実行ごとに選ぶ。3節に載せる実行（cloudflare の実行）と、既存の判定（中央値＋5分以上、10分以上）は変えていない
- 同じ API の応答（2026-10-26 09:59 JST）での新旧の比べ: 3節に載る4行は新旧で同じ。違うのは中央値の表示と見出しだけで、どれも基準から作業ブランチ・手動の実行を外したためだった。1節・2節・4節・5節と status.md・運行表に差は無い
- run-weekly（run 40214364620、12分21秒）の行は、新しいコードでも「10分以上」で3節に残る（同じ種類のほかの実行が無いため。来週からは予約の実行どうしで比べる）
- マージ後の mj-logs の同期の最初の書き出し（actions/morning.md、2026-10-26 10:06 JST）の3節:

```
## 3. 所要時間の異常（cloudflare の実行だけ。基準は同じ種類〈cloudflare・予約の実行か手動か〉の直近20回。その中央値の3倍以上かつ中央値＋5分以上、または10分以上。skipped は除く）

- 2026-10-25 19:40 regenerate-page.yml success（push・cloudflare）所要 11分11秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185765407
- 2026-10-25 19:42 regenerate-page.yml success（push・cloudflare）所要 18分05秒（直近20回の中央値 0分57秒） https://github.com/retroeater/mj/actions/runs/40185846374
- 2026-10-26 02:00 run-weekly.yml success（workflow_dispatch・cloudflare）所要 12分21秒（直近20回の中央値 12分21秒） https://github.com/retroeater/mj/actions/runs/40214364620
- 2026-10-26 07:47 fetch-youtube.yml success（schedule・cloudflare）所要 10分06秒（直近20回の中央値 7分55秒） https://github.com/retroeater/mj/actions/runs/40241832250
```

- 別件: OHA-08 の変更で、作業ブランチの実行の初回成功が4節に出なくなっていた（「気づいたこと」）。今回の条件に合わせて直していない

## 報告

- 状態: 判断待ち
- ブランチ: work/1026-oha
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1026-OHA-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1026-oha
- 確認用URL: なし（ページ・見た目の変更は無い。Actions のスクリプトだけ）
- マージ: 済（51e9cf8cb）
- issue: #563
- 判断が必要なこと:
  - OHA-08（c0a8ebd57）で、morning.md の4節「初回成功」に作業ブランチの実行が出なくなっている（3節のための `continue` が4節の判定より前にある。決定 B は3節だけの話だった）。直すなら、`continue` を4節の判定の後ろへ移す1か所の変更（推奨。テストを1本足す）。今回は「4節に差が無いこと」がマージの条件だったので直していない（「経過」の「気づいたこと」）
  - 指示文の記述の食い違い: 前提では見出しを「cloudflare の予約の実行だけ」の趣旨にするとあったが、決定は基準の範囲を絞るだけで、3節に載せる実行（cloudflare の手動・push を含む）は変えていない。見出しは決定と実物に合わせた（「経過」の「実装」）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9ca2970d）: https://github.com/retroeater/mj-logs/tree/main/guide/9ca2970d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9ca2970d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9ca2970d.md
