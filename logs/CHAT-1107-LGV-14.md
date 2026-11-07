# CHAT-1107-LGV-14

- 着手日時: 2026-11-07
- 対象issue: #636（参照）
- ブランチ: work/1107-lgv-repost
- 着手時HEAD: ffba09c32

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の閉業・旧店名・再掲の投稿を載せない変更（CHAT-1107-LGV-13）を cloudflare へマージする
Chat-Ref: CHAT-1107-LGV-14
マージ: 承認済み（チャットで、2026-11-07、平野さん）
貼る時機: いつでも（CHAT-1107-LGV-13 の続き。LGV-13 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-repost の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1107-lgv-repost を続けて使う（CHAT-1107-LGV-13 の変更があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行。docs/decisions/*.md の末尾の節どうしを含む）は、両方を残して日付順に解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。`log/` などの生成物が衝突したら、CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-13 のログの `## 報告` が「判断待ち」で、work/1107-lgv-repost がリモートにあることを確かめ、そうでなければ何もせずに止まる。

## 目的
CHAT-1107-LGV-13 の変更（`HIDDEN_POSTS` の19件、再掲の語、`WORD_ALLOWED_POSTS`、常設 issue の題「ログ: 本文の語で外した投稿」）を cloudflare へマージする。

### 決定（2026-11-07、平野さん）
- LGV-13 のプレビューを見て（チャット側が Chrome で確かめた結果を受けて）、work/1107-lgv-repost を cloudflare へマージしてよい
- LGV-13 の報告の「判断が必要なこと」の2つ（「〇選」から選手・選択・選挙・選べを除く、常設 issue の題「ログ: 本文の語で外した投稿」）は、LGV-13 の形のままでよい

### 前提（チャット側。平野さんの決定ではない）
- チャット側が 2026-11-07 12:33 JST 頃にプレビュー（2ae8c0194 の生成物）を Chrome で開いて確かめた: search.json は店 1,718・投稿 2,388。`?shop=ChIJQbWaOxiNGGAR2Wr2BfXxEU8` は「やまぐち・西早稲田・2026-07-16 · 谷岡育夫」の1件。`ChIJs9lZQwCLGGARB52nqWASVr8` は「すぎだま」。`ChIJHS8Vi9KNGGARPcMp59xBpUo`（Rock'anDo 六感堂）の投稿は1件
- マージで `scripts/generate_log_pages.py` が変わるので、push の検知で log/ が再生成される（その時点のシートで生成し、毎朝の取り込みの差が入りうる）

## 手順
1. CHAT-1107-LGV-13 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-14` を足す。origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` と `node --test scripts/tests/js/log.test.js` を回す。ワークフローの検査（docs/notes/github-actions.md「ワークフローを変更したとき」の確かめ、zizmor を含む）を回す
2. 取り込みで生成物が古くなった・衝突したときは log/ を生成し直す。`git diff --stat origin/cloudflare...HEAD` と、`log/search.json` の cloudflare との差（店・投稿の数、外れた投稿が LGV-13 の (b) の19件と同じか、毎朝の取り込みの差）を書く。止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）
3. マージの後、check-run と push の検知で走る自動処理（再生成など）の結果を書き、本番の `https://ryoei.pro/log/search.json?v=<マージの SHA か再生成の SHA>`（UA に `mj-internal`）で、店・投稿の数と、上の前提の3つの店がプレビューと同じか（毎朝の取り込みの差を除く）を確かめる

## 止まる条件
- テスト・ワークフローの検査が失敗する（取り込みで落ちたものが今回の変更によるものでなく、cloudflare の側でも落ちるなら、原因を書いて進めてよい）
- `log/search.json` で、LGV-13 の (b) の19件以外の投稿が外れている、または19件のどれかが出ている（毎朝の取り込みで増えた投稿が本文の語で外れたときは止まらず、投稿 ID・店名・語を書く）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が同じファイルの同じ箇所を変えている、または取り込みで衝突する（上の解き方で解けるものは除く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を書いて残りを進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-07）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1107-LGV-13 のログの `## 報告` は「判断待ち」、work/1107-lgv-repost はリモートにある（ffba09c32、ローカルと同じ）
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- CHAT-1107-LGV-13 のログの状態を「判断待ち / 続き: CHAT-1107-LGV-14」にした（ログ先行のコミットに含めた）

### 1. 取り込み・テスト・ワークフローの検査

- `git merge-base --is-ancestor origin/cloudflare HEAD` は真（LGV-13 の着手から cloudflare は進んでいない）。取り込むものは無く、log/ は古くなっていないので作り直していない
- `python3 -m unittest discover -s scripts/tests`: OK。`node --test scripts/tests/js/log.test.js`: pass 23・fail 0
- zizmor 1.31.1（`--offline`）: cloudflare・作業ブランチとも 69件（self-repository 61・artipacked 8）で同じ（題・コメントを変えただけ）
- ワークフローの手動実行: LGV-13 で作業ブランチの ref で `run-monthly.yml`（run 8、closed・closed-issue を含めて success）を済ませた。その後ワークフローは変えていない
- 未マージの work/ ブランチで同じファイルを変えているもの: `work/1101-prp-pub`・`work/1103-ryo5` が docs/notes/github-actions.md の「ワークフローの一覧」の別の行（`check-image-links.yml`・`fetch-x-posts.yml`）を変えている。こちらが変えたのは `convert-x-posts.yml`・`run-monthly.yml` の行と run-monthly の節で、同じ行ではない（LGV-11 と同じ）

### 2. 差分

- `git diff --stat origin/cloudflare...HEAD`: .github/workflows/convert-x-posts.yml・.github/workflows/run-monthly.yml・docs/decisions/operations.md・docs/logs/CHAT-1107-LGV-12.md・docs/logs/CHAT-1107-LGV-13.md・docs/logs/CHAT-1107-LGV-14.md・docs/notes/github-actions.md・docs/notes/log-page.md・log/index.html・log/search.json・scripts/convert_x_posts.py・scripts/generate_log_pages.py・scripts/tests/test_convert_x_posts.py・scripts/tests/test_log_pages.py（14ファイル）
- `log/search.json` の cloudflare（82a75fac9 の生成）との差: 店 1,721 → 1,718・投稿 2,407 → 2,388。外れた投稿は LGV-13 の (b) の19件と一致。増えた投稿は0

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-repost
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-repost/docs/logs/CHAT-1107-LGV-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-repost
- 確認用URL: なし
- マージ: 未
- issue: #636（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a2e78691）: https://github.com/retroeater/mj-logs/tree/main/guide/a2e78691

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a2e78691/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f451ca9f.md
