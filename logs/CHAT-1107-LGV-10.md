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

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-notify
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-notify/docs/logs/CHAT-1107-LGV-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-notify
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 00db63f7）: https://github.com/retroeater/mj-logs/tree/main/guide/00db63f7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/00db63f7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ff7af9eb.md
