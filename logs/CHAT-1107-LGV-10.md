# CHAT-1107-LGV-10

- 着手日時: 2026-11-07
- 対象issue: なし
- ブランチ: work/1107-lgv-notify
- 着手時HEAD: f451ca9f8

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の催事の語で外した投稿の毎月の一覧を、常設 issue で知らせる（閉業の店の投稿は一覧から除く）
Chat-Ref: CHAT-1107-LGV-10
マージ: 判断待ちで止まる（issue の本文の形と dry-run の出力を見てから決める）
貼る時機: CHAT-1107-LGV-09 のマージの後（LGV-09 の最終報告が届いてから）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-notify の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1107-lgv-notify を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1107-lgv-notify origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-09 のログの `## 報告` が「完了」で、origin/cloudflare に `EVENT_WORDS`（`scripts/generate_log_pages.py`）があることを確かめ、そうでなければ何もせずに止まる。

## 目的
CHAT-1107-LGV-08 で足した「催事の語で外した投稿」の毎月の一覧は、今は `run-monthly.yml` のジョブ closed の実行サマリーに出るだけで、知らせが無い。外した投稿があるときに GitHub の issue で知らせ、間違って外れた投稿に平野さんが気づけるようにする。あわせて、閉業の店の投稿を一覧から除く。

### 決定（2026-11-07、平野さん）
- 毎月の一覧は、自動で外した投稿が1件以上のときに issue で知らせる（案b）
- 閉業の店の投稿は毎月の一覧から除く（案b。一覧の側で「閉業」シートを読んで除く。Places の呼び出しは増やさない）

### 前提（チャット側。平野さんの決定ではない）
- 知らせ先（チャット側の案。平野さんへの答えでは「#664 にコメント」と書いたが、#664 は閉業の初回の確かめ〈2026-12-01〉の後に閉じる予定なので、閉じない常設 issue にする）: 部品 `upsert-issue` の `mode: state`（型S、docs/notes/github-actions.md）で、題「ログ: 催事の語で外した投稿」の常設 issue に書く。1件以上なら開いて（無ければラベル「種類: 常設」で作って）本文を一覧に書き換え、0件なら開いていればコメントして閉じる。こうすると、issue が開いているのは平野さんが見るべき投稿があるときだけになる。題は合わせて変えてよい（変えたら書く）
- 本文: 件数、1件1行（投稿 ID・日付・投稿者・店名・当たった語。本文は出さない。投稿 ID は X の投稿へのリンクにしてよい）、戻し方（「載せるなら `scripts/generate_log_pages.py` の `EVENT_ALLOWED_POSTS` に足す指示を出す」）、実行の URL。本文の組み立ては `convert_x_posts.py` 側に置き、`--issue-dir` の形で書き出す（ほかの本の作りに合わせる）
- 閉業の店の除き方: 一覧を作るときに、閉業の店（「閉業」シートの place_id）の投稿を除く。本番の実行では、その実行で書き直した後の閉業の店を使うのがよい（今月閉業と分かった店の投稿も除くため）。そのために一覧を出す位置を Place Details の後に移してよい。dry-run（Place Details を呼ばない）では今の「閉業」シートで除く。Place Details が失敗して閉業のシートを書かなかったときも、今のシートで除いて一覧を出す
- 権限: `convert-x-posts.yml` の closed のジョブ（と呼ぶ側の `run-monthly.yml` の closed）に `issues: write` を足す。ほかの mode・ジョブの権限は変えない（docs/notes/github-actions.md「権限と Secret の渡し方」の表も直す）
- 手動実行（dry-run）では issue に書かず、書く内容を実行サマリーに出す（`upsert-issue` の `dry-run`）

## 手順
1. `run_closed()`・`event_lines()`・`convert-x-posts.yml`・`run-monthly.yml` の今の作りと、`upsert-issue` を使うほかの本（例 `check-saikyo-unregistered.yml`）の書き方を確かめる。閉業の店の除き方と issue への書き出しを足し、テストを足す（閉業の店の投稿が一覧に出ない・0件なら閉じる側・本文に本文〈投稿の文〉が出ない）
2. docs/notes/log-page.md（「載せない投稿・店」の語の行と毎月の一覧の説明）・docs/notes/github-actions.md を直す。docs/notes/self-review.md のとおり差分をレビューさせる
3. 作業ブランチの ref で `convert-x-posts.yml` を `mode: closed`・dry_run 真で手動実行し、実行サマリーの「書く内容」（issue の題・すること・本文）をログに引用する。Place Details は呼ばない。判断待ちで止まる

## 止まる条件
- 閉業の店を除くために Place Details の呼び出しを増やす作りになる
- `issues: write` を closed 以外のジョブ・mode にも足す作りになる
- dry-run で issue に書いてしまう
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが `convert_x_posts.py` の `run_closed()`・`event_lines()`、`convert-x-posts.yml`・`run-monthly.yml` の同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、issue の題・本文の形でよいかを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-07）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1107-LGV-09 のログの `## 報告` は「完了」、origin/cloudflare の `scripts/generate_log_pages.py` に `EVENT_WORDS` がある
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- ブランチ: work/1107-lgv-notify はローカル・リモートとも無し → `git checkout -b work/1107-lgv-notify origin/cloudflare`
- 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml`・`run-monthly.yml` を変えているもの: 0件（LGV-08 の確かめのあと増えていない）
- ワークフローの変更なので docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ。`convert-x-posts.yml` を呼ぶのは `run-daily.yml` の `xconvert`（mode all、contents: read）と `run-monthly.yml` の `closed`（contents: read）

### 1. 今の作り

- `run_closed()`: `shown_pairs()` を1回読み、閉業の対象（`closed_targets()`）と催事の一覧（`event_lines()`）を作る。一覧は Place Details の予定の行の前に実行サマリーへ出すだけで、閉業の店は除いていなかった
- `upsert-issue`（型S）の使い方は `check-saikyo-unregistered.yml` と同じ: スクリプトが `--issue-dir issue` に `body.md`（問題あり）か `close.md`（なし）を書き、`close: ${{ hashFiles('issue/body.md') == '' }}`、`dry-run` は手動実行で真

### 2. 作り（973a69fed・0b674df2d）

- **権限の置き場を前提から変えた**: 前提は「`convert-x-posts.yml` の closed のジョブに `issues: write`」だが、`workflow_call` で呼ばれる側の job の権限は呼ぶ側の job を超えられず、起動の時点で全 job について確かめられる（docs/notes/github-actions.md「入れ子」）。`convert-x-posts.yml` の job に `issues: write` を足すと、`run-daily.yml` の `xconvert` にも足さないと毎朝の取り込みが起動で失敗する（止まる条件「closed 以外のジョブにも足す作り」に当たる）。そこで:
  - `convert-x-posts.yml` は権限を変えず、closed のときだけ `--issue-dir issue` を付け、書き出した本文を job の出力 → `on.workflow_call.outputs`（`issue_body`・`issue_close`）で返す（ステップ「常設 issue の内容を出力に渡す」、`GITHUB_OUTPUT` に乱数の区切りで複数行）
  - `run-monthly.yml` に job `closed-issue`（`needs: closed`、`contents: read`・`issues: write`）を足し、出力を env で受けてファイルにし、`upsert-issue` の型S で題「ログ: 催事の語で外した投稿」に書く（`dry-run: ${{ !inputs.scheduled }}`・`run-url-footer: 'true'`）。`issues: write` を持つのはこの job だけ
- `convert_x_posts.py`:
  - `closed_events(shown, closed)`: 語で外した投稿から閉業の店（place_id が `closed` にある）の投稿を除く。店名は文字参照を戻す
  - `run_closed()` を組み替え、確かめと書き込みを `check_and_write_closed()` に分けた。一覧は確かめの後に、書き直したときはその「閉業」の店、書き直さなかったとき（dry-run・Place Details の失敗・上限・10% 超え・書き込みの失敗・鍵が無い）は今のシートの店で除いて出す（try/finally）。Places の呼び出しは増えない
  - `event_issue(events, today)`: 1件以上なら本文（件数・表〈投稿 ID は X の投稿へのリンク・日付・投稿者・店名・語〉・戻し方・規則の場所）、0件なら閉じるコメント。投稿の本文は持たない。表のセルの `|` は逃がし、改行は空白に
  - 戻し方: 「載せるなら `EVENT_ALLOWED_POSTS` に、外したままにするなら `HIDDEN_POSTS` に足す指示を出す。どちらかに足すと次の月の一覧から消える」。外したままの投稿も毎月の一覧に残り続けて issue が閉じなくなるので、前提の戻し方（`EVENT_ALLOWED_POSTS` だけ）に `HIDDEN_POSTS` を足した
  - `--issue-dir` の引数を足した
- テスト（`ClosedTest`）: 閉業の店の投稿が一覧に出ない・本文と閉じるコメントの形（表の `|`・改行・リンク無し・投稿の本文が出ない）・dry-run で今のシートで除いて `body.md` を書く・本番で書き直した後の閉業の店で除く（前の閉業の店で再開した店の投稿は出る）・Place Details の失敗と10% 超えで書かなかったときも今のシートで除いて `body.md` を書く・0件なら `close.md`・`shown_pairs()` を1回だけ読む。直す前のコード（origin/cloudflare）では failures 2・errors 5。直した後 `python3 -m unittest discover -s scripts/tests` OK
- 文書: docs/notes/log-page.md（「載せない投稿・店」の語の行・表の下の項）、docs/notes/github-actions.md（convert-x-posts の行・run-monthly の行と節・権限の表に `run-monthly` の `closed`〈今まで無かった〉・`closed-issue` の行）

### 3. 手動実行（作業ブランチの ref）

- `convert-x-posts.yml`（mode closed・dry_run 真）: run 68（ID 42313132948）success。Place Details は呼んでいない。実行サマリーの行:

```
- 予定: log/ に出す店のうち place_id のある 1704店に Place Details（項目は businessStatus だけ）を 1704回（上限 2500回、1分 500回まで・約 3.4分）。書く先は「閉業」だけ（今 0店）
- 催事の語で外した投稿（閉業の店を除く）: 0件（載せるなら generate_log_pages.py の EVENT_ALLOWED_POSTS、外したままなら HIDDEN_POSTS に足す）
```

- このワークフローを直接動かしたときは issue の job が無い（書くのは run-monthly の `closed-issue`）。新しい job を確かめるため `run-monthly.yml` も手動実行した（手動実行はすべて dry-run: gsc は飛ばし、redirects・closed は dry-run）: run 7（ID 42313135911）success。`closed / convert` success・`redirects / fetch` success（dry-run）・`gsc` skipped・`closed-issue` success。`closed-issue` の実行サマリー（upsert-issue の dry-run の「書く内容」）:

```
title: ログ: 催事の語で外した投稿
body-file: issue/body.md
comment-file: issue/close.md
close: true
dry-run: true
[dry-run] 「ログ: 催事の語で外した投稿」(無い) は閉じるものが無い
```

- 今のデータは0件なので、本文（1件以上のとき）は出ていない。形は見本の値で手元で組み立てた（実データではない）:

```
log/ で、【1】の本文の催事の語（`EVENT_WORDS`）で自動で外した投稿が 2件あります（2026-12-01 の確かめ。閉業の店の投稿は除く）。

| 投稿 | 日付 | 投稿者 | 店名 | 当たった語 |
|---|---|---|---|---|
| [2116743444546896002](https://x.com/it_uesama/status/2116743444546896002) | 2026-11-01 | 谷岡育夫 | 見本の店A | 大つけ麺博 |
| 1234567890 | 2026-11-20 | 平野良栄 | 見本の店B | 出店中 |

間違って外れた投稿（常設の店で食べた投稿など）を載せるなら `scripts/generate_log_pages.py` の `EVENT_ALLOWED_POSTS` に、外したままにするなら `HIDDEN_POSTS` に足す指示を出す。どちらかに足すと、次の月の一覧から消える（0件になった月にこの issue を閉じる）。

規則: docs/notes/log-page.md「載せない投稿・店」。毎月1日に `run-monthly.yml` の closed（閉業の確かめ）と一緒に書き直す。
```

本文の末尾には upsert-issue が実行の URL を足す（`run-url-footer`）。0件で開いている issue を閉じるときのコメント: 「催事の語で外した投稿（閉業の店を除く）は0件になりました（2026-12-01 の確かめ）。」

### 4. 差分のレビュー（docs/notes/self-review.md、1回）

| 指摘 | 扱い |
|---|---|
| 権限の置き場（outputs で返し、run-monthly の job で書く） | 意図どおり（レビューも妥当とした。呼ばれる側の権限の確かめは起動のときに全 job に及ぶ） |
| 中: closed が失敗した（exit 1）月に、呼んだ本の出力が `needs.closed.outputs` に届いて issue まで書くかは確かめていない | 未確認のまま（作業ブランチで失敗させるには本番の Place Details を呼ぶか鍵を外す必要があり、安全に試せない。実行サマリーには一覧が出る。log-page.md に未確認と書き、「未確認の項目」に回した） |
| 低: 一覧は累積するので、数百件になると issue 本文・env の上限に当たる | 見送り（当面の件数では当たらない。足したまま残る投稿は HIDDEN_POSTS に移すと一覧から消える作り） |
| 低: 表のセルは `|` だけを逃がしていて、改行で表が壊れる | 直した（0b674df2d。改行を空白に。`@`・`#` などは値が店名・人名で見込みが低いので見送り） |
| 低: `today_jst()` が既存の `fx.JST` を使っていない | 直した（0b674df2d） |
| 低: テストが「書き直した後のシート」と「今のシート」を区別できていない。10% 超え・書き込み失敗の道筋のテストが無い | 直した（0b674df2d。前の閉業の店で再開した店の投稿が出ることを確かめ、10% 超えで書かなかったときのテストを足した） |
| 低: finally の中の例外が元の例外を隠す | 見送り（一覧と本文を作る処理は読むだけで、例外の見込みが低い） |
| 低: log-page.md の「dry-run と Place Details の失敗のとき」は実装より狭い | 直した（0b674df2d。「書き直さなかったとき」に） |

## 報告

- 状態: 判断待ち / 続き: CHAT-1107-LGV-11
- ブランチ: work/1107-lgv-notify
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-notify/docs/logs/CHAT-1107-LGV-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-notify
- 確認用URL: なし（ページは変えていない。ワークフローの手動実行で確かめた）
- マージ: 未（issue の本文の形と dry-run の出力を見てから決める）
- issue: なし（常設 issue「ログ: 催事の語で外した投稿」は、1件以上になった最初の月に作られる）
- 判断が必要なこと:
  - issue の題「ログ: 催事の語で外した投稿」と本文の形（経過の「3.」の見本）でよいか
  - 権限の置き場を前提から変えた: `convert-x-posts.yml` には `issues: write` を足さず（足すと run-daily の xconvert にも要る）、本文を出力で返して `run-monthly.yml` の新しい job `closed-issue` だけに `issues: write` を持たせた。この形でよいか
  - 戻し方に「外したままにするなら `HIDDEN_POSTS` に足す」を足した（足さないと外したままの投稿が毎月の一覧に残り、issue が閉じない）。この運用でよいか
- 未確認の項目:
  - closed が失敗した月（Place Details の失敗など）に、issue まで書くか（実行サマリーには出る）。安全に試す手段が無いので、失敗が起きた月に実行を見て確かめる
  - 本番の初回: 2026-12-01 02:00 JST の run-monthly（closed と closed-issue）。マージした場合
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1e1ee61d）: https://github.com/retroeater/mj-logs/tree/main/guide/1e1ee61d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1e1ee61d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
