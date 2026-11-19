# CHAT-1119-BIL-12

- 着手日時: 2026-11-19
- 対象issue: #298
- ブランチ: work/1119-bil
- 着手時HEAD: 2f708e595

## 指示

【Claude作成】Claude Code 向け指示：朝の報告（mj-logs の actions/morning.md）に mj の Actions の使用量の節を足し、run-daily の内訳を記録する
Chat-Ref: CHAT-1119-BIL-12
マージ: 承認済み（チャットで。下の「止まる条件」を1つでも満たさなければマージせずに止まる）
作業ブランチ: クラウドセッションで実行する。work/1119-bil を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1119-bil origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
貼る時機: いつでも（CHAT-1119-BIL-11 は 2026-11-19 にマージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1119-bil〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1119-bil の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
1日で大きく使った日や、月の累計が含まれる分（3,000分）に近づいたことを、GitHub の 90% の知らせより早く、翌朝に気づけるようにする（#298）。あわせて、cloudflare の run-daily.yml の所要が1日 約24分から約39分に増えた内訳を実測として残す。

### 決定（2026-11-19、平野さん）
- 早く気づく仕組みは、mj-logs の actions/morning.md に mj の Actions の分数を出す案にする（GitHub に mj だけの予算を足す案は採らない）
- 朝の報告に載せる条件は、前日（JST）が 240分以上、または月の累計が含まれる 3,000分の 50%（1,500分）・75%（2,250分）を超えたとき
- run-daily.yml の内訳（下の表）を #298 と docs/notes/github-actions.md に実測として残す
- 11/30 の Actions の使用量の実測は BIL の系列が扱う
- マージは、止まる条件をすべて満たせば承認済み

### 前提（チャット側。平野さんの決定ではない）
- morning.md は mj の `scripts/morning_report.py` が書き、`scripts/actions_status.py` から呼ばれる。動くのは public の mj-logs の `sync-from-mj.yml`（mj の Actions の分は使わない）で、mj を読むトークンは `MJ_READ_TOKEN`（Contents・Actions が Read-only）。チャット側が 2026-11-19 に cloudflare（f13217ad）で読んだ。**マージした直後から、この変更は mj-logs の sync で動く。** 使用量の部分が失敗しても、ほかの写し・書き出し（ログ・status.md・morning.md の1〜5節・index.html）を止めないこと
- 分の数え方（チャット側が 10月・11月に使い、請求とほぼ一致した方法）: 対象の日（JST）に作られた mj の実行を、全ブランチ・全イベントで取る（`GET /repos/{repo}/actions/runs?created=<UTC の範囲>`。1回の検索は 1,000件までなので、時間帯で分けて取る）→ 各実行のジョブを `filter=all` で取る → `conclusion` が skipped でなく、`started_at`・`completed_at` があり所要が正のジョブの秒数を、ジョブごとに1分へ切り上げて足す。11/4 は実行が 652件あった（ジョブを引く呼び出しも同じくらい要る）
- 置き方の案（実物に合わせて変えてよい）:
  - 日ごとの分数は mj-logs の `actions/usage.json` などに貯め、済んだ日は計算し直さない（sync は push のたびに動くため）。今日（JST）の分は数えない
  - 埋まっていない日は古い順に埋め、1回の起動で埋める日数か API の呼び出しの数に上限を置く（初回は 11/1 からの埋め戻しになる）。月の累計は、その月の前日までの日がそろってから出す（そろっていなければ「集計中」と書く）
  - morning.md に「6. Actions の使用量（mj）」の節を足す。決定の条件に当たらなければ「なし」。当たれば、前日の分数と月の累計（3,000分に対する割合）を書く。累計が 50% を超えた後は、毎朝その状態を書いてよい。分が見積もり（ジョブごとの切り上げ）であることを節の題に書く。月の初日の朝は、前日の月（前月）の累計を書く
  - 閾値・含まれる分は定数にする
- 照合の値（チャット側の見積もり。docs/notes/github-actions.md「Actions の使用量（実測、#298）」の「### 2026-11」の「日ごと」の行）: 11/1〜18 の日ごとの分数、合計 4,649分。チャット側は 11/4 の 1,769分・11/17 の 98分・11/18 の 53分を 2026-11-19 に取り直して同じ値を得た
- run-daily.yml の内訳（チャット側が API で集計。cloudflare の `[scheduled]` の実行 35回、ジョブごとに1分へ切り上げ。日付は JST）:

| 項目 | 値 |
|---|---|
| 1日の分数の推移 | 10/16〜20 は 21〜30分（平均 約24分）、10/22〜31 は 24〜37分、11/3 から 32〜52分、11/12〜19 は 35〜44分（平均 約39分） |
| 増えた分（約+15分）の内訳 | 新しく入ったジョブで 約+11分（11/3 に run-weekly から移した meibo の2ジョブ・birthday・unregistered・xfetch・xconvert で 約7分。logs〈10/24〜〉・sokuho〈11/5〜〉・xconvert-issue〈11/13〜〉・logpage〈11/14〜〉）。再生成のジョブ（live・sns・saikyo の regenerate）が各 約1分から約2分になって 約+3分。そのほか 約+1分 |
| 切り上げの分 | 11/12〜19 の1日あたり、実際の所要 29.0分に対し課金の見積もり 38.6分。22ジョブのうち13ジョブは実際には1分未満（logs は約6秒、xconvert-issue は約18秒） |

- 10月にジョブをまとめる案を見送ったときより、ジョブは約11本増えた。まとめる案の見直しは 12/2 に 11月の確定値と並べて行う（チャット側の予定。この指示では何も変えない）

## 手順
1. 着手中のコメントを #298 に書く（本文・ラベル・状態は変えない）。`git branch -r --no-merged origin/cloudflare` の各ブランチが `scripts/morning_report.py`・`scripts/actions_status.py`・`scripts/tests/test_morning_report.py`・`scripts/tests/test_actions_status.py`・docs/notes/github-actions.md「Actions の使用量（実測、#298）」「run-daily.yml」の節を変えていないかを確かめる（チャット側は 2026-11-19 にスクリプトとテストについて「無し」を確かめた）。上の「前提」の、スクリプトの呼ばれ方・トークンの権限を実物で確かめ、食い違えば止まる
2. 「前提」の数え方で、morning.md に節を足す（置き方の案は実物に合わせて変えてよい。選んだ形と理由をログの経過に書く）。テストを `scripts/tests/` に足す（日の境目〈JST〉・切り上げ・skipped の除外・閾値の各条件・累計がそろわないとき・月の初日・API が失敗してもほかの節が書かれること）。作業ブランチで、実際の API に対して 11/1〜18 を数え、照合の値と日ごとに比べる表（18日すべて、名前を略さない）をログに書く。セッションから mj の Actions の API に届かなければ、届いた範囲とその理由をログに書いて止まる
3. docs を書く: docs/notes/github-actions.md の「run-daily.yml」の節の morning.md の説明に、6節の行を足す（動き・閾値・数え方・置き場所）。「Actions の使用量（実測、#298）」の「### 2026-11」に、上の run-daily.yml の内訳の表を「チャット側の見積もり」として足す。#298 に、この2つと決定をコメントする（ログの `## 経過` と docs/notes の節から引用する）

## 止まる条件
- 「前提」のスクリプトの呼ばれ方・トークンの権限が実物と食い違う。`sync-from-mj.yml`（mj-logs）を変えなければ動かない（mj-logs は変えない。要る変更を報告する）
- 他セッションの着手中コメントが #298 にある
- 未マージの work/ ブランチが、手順1に挙げたファイルの同じ関数・節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 11/1〜18 の日ごとの分数が、照合の値と1日でも ±5分を超えて違う（実行の再試行など、差の理由をジョブで示せれば止まらず、ログに書く）
- `python3 -m unittest discover -s scripts/tests` が1件でも通らない
- 作業ブランチで `scripts/actions_status.py --repo retroeater/mj --dest <mj-logs のクローン>` を通しで動かし、(1) 例外で止まる (2) morning.md の1〜5節が、変更の前のコードを続けて動かしたときと違う（実行の合間に増えた実行で説明できる差は除く） (3) API が失敗するように差し替えたとき、使用量の節以外が書かれない、のどれかに当たる。動かした結果の morning.md の6節と、1回の起動の API の呼び出しの数（埋め戻しのとき・埋まった後）をログに書く
- 埋まった後の起動で、使用量のための API の呼び出しが増える（済んだ日を計算し直している）
- 新しい依存（パッケージ・ワークフロー）が要る
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1119-BIL-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1119-BIL-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット無し。BIL は最初の指示でないため XXX の重複確認は対象外。CLAUDE.md は origin/cloudflare と同じ。work/1119-bil はリモートにありマージ済みのため origin/cloudflare から作成
- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠け: なし

### 手順1（着手の確認）

- #298 のコメント 50件を読んだ。最後の着手中は CHAT-1119-BIL-11（マージ済み）。他セッションの着手中は無し。着手中のコメントを書いた（issuecomment-6999496274）
- 未マージの work/ ブランチ: 手順1のスクリプト・テストを変えたものは無し。docs/notes/github-actions.md を変えているのは `work/1101-prp-pub`（一覧の表の `check-image-links.yml` の行）と `work/1103-ryo5`（一覧の表の `fetch-x-posts.yml` の行）だけで、どちらも「run-daily.yml」「Actions の使用量（実測、#298）」の節ではない（同じファイルの別の箇所。止まらない）
- 前提の確かめ（mj-logs の `sync-from-mj.yml` を raw で読んだ）: 「写す・書き出す・push」の step が mj のクローンの中で `python3 scripts/actions_status.py --repo retroeater/mj --dest "$GITHUB_WORKSPACE"` を呼ぶ。env の `GITHUB_TOKEN` は `secrets.MJ_READ_TOKEN`、コメントに「mj の Contents・Actions が Read-only」。`actions_status.py` の `main()` の最後で `morning_report.write()` を呼ぶ。前提と一致。step は `set -euo pipefail` なので、使用量の例外は `actions_status.py` の中で捕まえる。push が拒否されたら `git reset --hard origin/main` でやり直す（そのときは usage.json の更新も捨てられ、次の回で同じ日を続きから引く）
- セッションから `api.github.com` の mj の Actions の API に届く（`GET /repos/retroeater/mj/actions/runs?per_page=1` が 200）。mj-logs の Actions の API はこのセッションの許可の外（sync の頻度は mj-logs のクローンのコミットで見た: 11/18 JST に変更のあった回が 18回）

### 手順2（実装）: 選んだ形と理由

- 新しいモジュール `scripts/actions_usage.py`（数えて貯める）と、`morning_report.py` の `usage_lines()`（6節の文）。`actions_status.py` は status.md の後・morning.md の前に `actions_usage.update()` を呼び、例外は捕まえて 6節に「使用量を出せなかった（例外の型）」と書く（1〜5節・status.md・index.html は止めない）。数える処理は morning_report の判定と別の関心で約150行になるため、actions_dashboard.py と同じく別のモジュールにした
- 置き場所は指示の案のとおり mj-logs の `actions/usage.json`（`days`: 日 → `{minutes, runs}`、`pending`: 途中の日の実行ごとの分）。sync の `git add -A` でそのままコミットされる。中身が変わったときだけ書く
- 1回の起動の上限は API の呼び出し 250回（`CALL_BUDGET`）。日の上限にしなかったのは、11/4 のように1日で 650件を超える実行があり、1日単位では1回の起動の呼び出しが読めないため。途中の日は実行ごとの分を `pending` に貯め、次の起動はまだ引いていない実行のジョブだけを引く（実行の一覧は日ごとに1回だけ）
- 締めた日（`days` にある日）は数え直さない。締めていない日が無ければ API を1回も呼ばない（`/rate_limit` も呼ばない）
- 締めていない日があるときは、先に `/rate_limit`（レートに数えられない）を見て、残りが 1,000回未満なら数えない（sync は1日20回前後、MJ_READ_TOKEN の1時間 5,000回を埋め戻しで食い尽くさないため）
- 実行の一覧は `created=<開始>..<終わり>`（UTC、JST の日の 00:00:00〜23:59:59）。`total_count` が 1,000 を超えたら範囲を半分に割って取り直す
- 動いているジョブがある実行は分を貯めず、次の起動で引き直す（その日は締めない）。2日より前の日は終わった分だけで締める（`STALE_DAYS`、待ちのまま残る実行で後の日が止まらないように）。締めた後の再実行は数えない（`簡略化:` のコメント）
- 6節: 前日が 240分以上か、そろった月の累計が 1,500分・2,250分を超えたら、前日の分と月の累計（割合と超えた閾値）。前日が締まっていない・月がそろっていないときも2行（「集計中」）。どれにも当たらなければ「なし」。月の初日の朝は前日の月＝前月の累計。閾値・含まれる分・初めの日は `actions_usage.py` の定数
- テスト `scripts/tests/test_actions_usage.py`（24件）: 切り上げ・skipped と時刻なしと所要0の除外・失敗と取り消しは数える・JST の日の境目（10/31 23:59 JST の実行を 11/1 に入れない、今日を数えない）・締めた日を数え直さない（2回目の起動の呼び出し 0）・翌日は新しい日だけ・上限で途中まで→続きから（同じ実行・一覧を引き直さない）・動いている実行は締めない／2日より前は締める・1,000件超の分割・ジョブのページング・API の失敗でそれまでの分を書いて例外・レートの残りが少ないと数えない・6節の閾値の境界（239/240、1,500/1,501、2,251）・集計中・前日が無い・月の初日・失敗の表示・失敗でも1〜5節が書かれる（`morning_report.write`）
- `python3 -m unittest discover -s scripts/tests`: Ran 2249 tests, OK

### 通しの実行（止まる条件の (1)〜(3)）

mj-logs のクローン（main の先頭）を3つ複製し、変更前（origin/cloudflare の scripts・.github・workers を git archive で取り出したもの）と変更後を続けて動かした（`--repo retroeater/mj --event workflow_dispatch --ref main`）。

- (1) 変更後: 例外なしで終わった。出力 `actions/usage.json: API 250回、締めた日 1日、途中 2026-11-02`（埋め戻しの初回の、使用量のための呼び出しは 250回）
- (2) morning.md の差分（変更前→変更後）: 「書き出した時刻」の行（13:20→13:21）と、6節の追加だけ。1〜5節は同じ。そのときの6節:

```
## 6. Actions の使用量（mj。ジョブごとに1分へ切り上げた見積もり、日付は JST。前日が240分以上か、月の累計が含まれる3,000分の50%・75%を超えたとき）

- 前日（11/18）: 集計中
- 11月の累計（11/1〜11/18）: 集計中（1/18日）
```

- (3) `actions_status.get` を差し替え、`/rate_limit`・`created=`・`filter=all` の呼び出しを HTTP 503 にして動かした: `::warning::actions/usage.json を更新できなかった: HTTP Error 503: test` の後に `write: actions/morning.md`・`actions/index.html: 変更なし`。morning.md は変更前と時刻の行のほかは同じで、6節は「- 使用量を出せなかった（HTTPError）」
- 変更前と後の status.md・index.html はどちらも「変更なし」（クローンの版と同じ）

### 差分の自己レビュー（docs/notes/self-review.md）

1回目（新しい文脈のサブエージェント、`git diff origin/cloudflare...HEAD -- scripts docs/notes/github-actions.md`。テスト 2249件 OK）:

| 指摘 | 扱い |
|---|---|
| 実行が終わったかをジョブの一覧だけで見ている。ジョブが空の実行・`uses:` の後の段のジョブがまだ作られていない実行を少ない分で締める（`actions_usage.py` の `update()`、要修正） | 直した（422269700）。一覧の時点で completed でなかった実行を `pending.open` に残し、締める前に `GET /runs/{id}` で completed を確かめる。テスト「ジョブが空で実行が終わっていない」を足した |
| 50%・75% を超えた後は月末まで毎朝出る（提案） | 意図どおり。指示文の置き方の案「累計が 50% を超えた後は、毎朝その状態を書いてよい」 |
| 1分まで割っても 1,000件を超えると先頭だけで数える（提案） | 直した（422269700、`簡略化:` のコメント） |
| テストの抜け: 割った境目・STALE_DAYS の境目（提案） | 直した（422269700、テスト2本）。`actions_status.main` の try/except のテストは見送り（通しの実行の (3) で確かめた。下の「通しの実行」） |

2回目（422269700 だけ。テスト 2252件 OK。修正前に戻すと新しいテストが落ちることをレビュー側が確かめた）: 要修正なし。提案3件は、2回目の後にコードを直すとマージできない（self-review.md「収束」）ため直さない。

| 指摘 | 扱い |
|---|---|
| `test_split_edges_counted_once` は範囲の重なり（重複）を検出できない（dict のマージで消えるため。漏れは検出できる） | 見送り（数える結果は重複しても変わらない。テストの名前どおりの検出にするのは次にこのファイルを触るとき） |
| `簡略化:` のコメントは「先頭の分だけ」だが、コードは 1,000件を超えるページも要求し、例外になる見込み | 見送り（1分に 1,000件の実行は起きない規模。起きても 6節が「出せなかった」になるだけで1〜5節は止まらない） |
| このコミットより前の形式の pending（`open` 無し）は従来どおりの判定 | 意図どおり（usage.json はまだどこにも無く、前の形式の pending は存在しない） |
| 参考: 実行が削除されて 404 になると、その日で毎回例外になり詰まる（差分による悪化ではない） | 見送り（起きれば 6節が毎朝「出せなかった（HTTPError）」になり気づける。#298 にコメントで残す） |

### 11/1〜18 の照合（実際の API、変更後の数え方）

`actions_usage.update()` を上限 10,000回・now を 2026-11-19 15:00 JST にして、空の usage.json から動かした（スクリプトは scratchpad の使い捨て。中身は `update()` を呼ぶだけ）。API 2,592回、17分16秒、18日すべて締めた。実行 2,559件。照合の値は docs/notes/github-actions.md「### 2026-11」の「日ごと」の行。

| 日（JST） | 照合の値（分） | 数えた分 | 差 | 実行の件数 |
|---|---|---|---|---|
| 11/1 | 273 | 386 | +113 | 174 |
| 11/2 | 332 | 332 | 0 | 144 |
| 11/3 | 204 | 204 | 0 | 232 |
| 11/4 | 1,769 | 1,769 | 0 | 652 |
| 11/5 | 237 | 237 | 0 | 190 |
| 11/6 | 66 | 66 | 0 | 61 |
| 11/7 | 95 | 95 | 0 | 91 |
| 11/8 | 25 | 25 | 0 | 1 |
| 11/9 | 117 | 117 | 0 | 64 |
| 11/10 | 104 | 104 | 0 | 63 |
| 11/11 | 60 | 60 | 0 | 41 |
| 11/12 | 242 | 242 | 0 | 38 |
| 11/13 | 543 | 543 | 0 | 394 |
| 11/14 | 87 | 87 | 0 | 33 |
| 11/15 | 119 | 119 | 0 | 83 |
| 11/16 | 181 | 181 | 0 | 181 |
| 11/17 | 98 | 98 | 0 | 95 |
| 11/18 | 53 | 53 | 0 | 22 |
| 計 | 4,605 | 4,718 | +113 | 2,559 |

- 11/1 だけ +113分（止まる条件の ±5分を超える）。ほかの17日は差 0
- 11/1 の内訳をジョブで確かめた:
  - JST 11/1 00:00〜08:59:59（UTC では 10/31 15:00〜23:59:59）に作られた実行 31件で 131分（cloudflare の run-daily.yml の手動実行 51分、作業ブランチの regenerate-page.yml の手動実行 37分・run-daily.yml 20分・check-assets.yml 10分・run-weekly.yml 7分 など）
  - JST 11/1 09:00〜23:59:59（UTC の 11/1 の 00:00〜14:59:59）は 255分。ジョブの開始日はすべて JST 11/1、再実行（run_attempt が2以上）のジョブは無し
  - 照合の値 273分は、UTC の 11/1 から数えた 255分とも、JST の1日の 386分とも合わない（255分との差 18分は、ジョブでは説明できない）
- 照合の値の側の食い違い: github-actions.md の「日ごと」の18日の和は 4,605分で、同じ行の「合計 4,649分」と 44分違う。チャット側が取り直したのは 11/4・11/17・11/18 だけで、11/1 は取り直していない
- 結論: 差の理由をジョブで示しきれないため、止まる条件に当たる。マージせずに止まる（判断待ち）

### 埋まった後の起動

上の18日分の usage.json を mj-logs のクローンに置いて `actions_status.py --repo retroeater/mj --dest <クローン>` を動かした: `actions/usage.json: API 0回、締めた日 18日`（使用量のための呼び出しは 0回。`/rate_limit` も呼ばない）。そのときの6節:

```
- 前日（11/18）: 53分
- 11月の累計（11/1〜11/18）: 4,718分（含まれる 3,000分の 157%。75% を超えた）
```

1回の起動の呼び出しの数: 埋め戻しのとき 250回（上限。ほかに `/rate_limit` 1回、レートに数えられない）、埋まった後 0回、新しい日が1日増えた朝は「一覧 1〜数回＋実行の数（＋一覧の時点で終わっていなかった実行の数）」。11/1 からの埋め戻しは実行 2,559件で約 2,600回なので、sync の約11回で埋まる（1日20回前後動くため、マージの当日中の見込み）

### 手順3（docs）

- docs/notes/github-actions.md の「run-daily.yml」の節に6節の行（動き・閾値・数え方・置き場所）、「### 2026-11」に run-daily.yml の内訳の表（チャット側の見積もり）を足した（935280103）
- docs/decisions/automation.md に決定を足した
- `python3 -m unittest discover -s scripts/tests`（最後のコミットの後）: Ran 2252 tests, OK

## 報告

- 状態: 判断待ち
- ブランチ: work/1119-bil
- ログ: https://github.com/retroeater/mj/blob/work/1119-bil/docs/logs/CHAT-1119-BIL-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1119-bil
- 確認用URL: なし（ページは変えていない。動くのは mj-logs の sync）
- マージ: 未（止まる条件に当たったため）
- issue: #298
- 判断が必要なこと:
  - 止まる条件「11/1〜18 の日ごとの分数が照合の値と ±5分を超えて違う」に当たった。11/1 だけ照合 273分に対し数えた分 386分（+113分）。ほかの17日は差 0。JST 11/1 の 0〜9時（UTC では 10/31）の実行が 131分あり、チャット側が月の初めを UTC で切った可能性があるが、それでも 255分で 18分合わず、ジョブでは理由を示しきれない（`## 経過`「11/1〜18 の照合」）。また照合の値の「日ごと」の和は 4,605分で「合計 4,649分」と合わない。11/1 をチャット側で取り直して照合の値を確かめるか、この差を認めてマージするかの判断
  - レビューの提案（`## 経過`「差分の自己レビュー」の2回目の表）は直していない。直すならその分のレビューが要る
- 未確認の項目:
  - mj-logs の sync での実際の動き（マージ後。初回は 11/1 からの埋め戻しで、1回の sync の所要が API 250回ぶん延びる。約11回で埋まる見込み）
  - 実行が削除されて API が 404 を返すと、その日で毎回止まり 6節が「出せなかった」になる（今回の差分による悪化ではない。#298 にコメントした）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f13217ad）: https://github.com/retroeater/mj-logs/tree/main/guide/f13217ad

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f13217ad/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c2fd0dd9.md
