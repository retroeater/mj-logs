# CHAT-1107-LGV-09

- 着手日時: 2026-11-07
- 対象issue: なし
- ブランチ: work/1107-lgv-hide
- 着手時HEAD: e77f18fe4

## 指示

【Claude作成】Claude Code 向け指示：「ログ」で催事・期間限定の出店の投稿を載せない変更（CHAT-1107-LGV-08）を cloudflare へマージする
Chat-Ref: CHAT-1107-LGV-09
マージ: 承認済み（チャットで、2026-11-07、平野さん）
貼る時機: いつでも（CHAT-1107-LGV-08 の続き。LGV-08 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1107-lgv-hide の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1107-lgv-hide を続けて使う（CHAT-1107-LGV-08 の変更があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行。docs/decisions/*.md の末尾の節どうしを含む）は、両方を残して日付順に解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。`log/` などの生成物が衝突したら、CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-08 のログの `## 報告` が「判断待ち」で、work/1107-lgv-hide がリモートにあることを確かめ、そうでなければ何もせずに止まる。

## 目的
CHAT-1107-LGV-08 の変更（`HIDDEN_POSTS`・`EVENT_WORDS`・`EVENT_ALLOWED_POSTS`、`HIDDEN_SHOPS` への #6 の追加、毎月の一覧）を cloudflare へマージする。

### 決定（2026-11-07、平野さん）
- LGV-08 のプレビューを見て（チャット側が Chrome で確かめた結果を受けて）、work/1107-lgv-hide を cloudflare へマージしてよい
- LGV-08 の報告の「判断が必要なこと」の2つ目・3つ目（毎月の一覧の出し先、閉業の店の投稿が一覧に出続けること）は、この指示では変えない（別に決める）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が 2026-11-07 00:43 JST 頃にプレビュー（a23f399ef の生成物）を Chrome で開いて確かめた: search.json は店 1,721・投稿 2,407。`ChIJGeYlX_mLGGARwsJahMe-XZg`（天外天の場所）と `ChIJ86b9L4qJGGARGEWCsXZx9tQ`（WACCA）は出ない。新横浜ラーメン博物館の2店（`ChIJf41M_dNeGGARVFT6mtftFbk`・`ChIJNSoA_dNeGGARjAJl8smPb4w`）は出る。`?shop=ChIJQeHeBsWIGGAR7VoAcu4Wb5k`（つきひ）は「2025-04-10 · 谷岡育夫」の1件だけ
- マージで `scripts/generate_log_pages.py` が変わるので、push の検知で log/ が再生成される（その時点のシートで生成し、毎朝の取り込みの差が入りうる）

## 手順
1. CHAT-1107-LGV-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1107-LGV-09` を足す。origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` と `node --test scripts/tests/js/log.test.js` を回す
2. 取り込みで生成物が古くなった・衝突したときは log/ を生成し直す。`git diff --stat origin/cloudflare...HEAD` と、`log/search.json` の cloudflare との差（店・投稿の数、外れた投稿の一覧が LGV-08 の (b) の18件と同じか、毎朝の取り込みの差）を書く。止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）
3. マージの後、check-run と push の検知で走る自動処理（再生成など）の結果を書き、本番の `https://ryoei.pro/log/search.json?v=<マージの SHA か再生成の SHA>`（UA に `mj-internal`）で、店・投稿の数と、上の前提の4つの place_id の有無・つきひの投稿の数がプレビューと同じか（毎朝の取り込みの差を除く）を確かめる

## 止まる条件
- テストが失敗する（取り込みで落ちたテストが今回の変更によるものでなく、cloudflare の側でも落ちるなら、原因を書いて進めてよい）
- `log/search.json` で、LGV-08 の (b) の18件以外の投稿が外れている、または (b) の18件のどれかが出ている（毎朝の取り込みで増えた投稿が催事の語で外れたときは止まらず、投稿 ID・店名・語を書く）
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `generate_log_pages.py`・`convert_x_posts.py`・`run-monthly.yml` の同じ箇所を変えている、または取り込みで衝突する（上の解き方で解けるものは除く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗は原因を書いて残りを進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1107-LGV-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1107-LGV-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手（2026-11-07）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。CHAT-1107-LGV-08 のログの `## 報告` は「判断待ち」、work/1107-lgv-hide はリモートにある（e77f18fe4、ローカルと同じ）
- 同じ Chat-Ref のコミット・ログ: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- CHAT-1107-LGV-08 のログの状態を「判断待ち / 続き: CHAT-1107-LGV-09」にした（ログ先行のコミットに含めた）

### 1. 取り込みとテスト

- `git fetch origin cloudflare` の後、`git merge-base --is-ancestor origin/cloudflare HEAD` は真（LGV-08 の着手から cloudflare は進んでいない）。取り込むものは無く、衝突も無い。log/ は古くなっていないので作り直していない
- `python3 -m unittest discover -s scripts/tests`: OK（2,047件）。`node --test scripts/tests/js/log.test.js`: pass 23・fail 0
- 未マージの work/ ブランチで `generate_log_pages.py`・`convert_x_posts.py`・`run-monthly.yml` を変えているもの: 0件

### 2. 差分

- `git diff --stat origin/cloudflare...HEAD`: docs/decisions/operations.md・docs/logs/CHAT-1106-LGV-07.md・docs/logs/CHAT-1107-LGV-08.md・docs/logs/CHAT-1107-LGV-09.md・docs/notes/github-actions.md・docs/notes/log-page.md・log/index.html・log/search.json・scripts/convert_x_posts.py・scripts/generate_log_pages.py・scripts/tests/test_convert_x_posts.py・scripts/tests/test_log_pages.py・sitemap-pages.xml（13ファイル）
- `log/search.json` の cloudflare（b40b3a415 の生成）との差: 店 1,737 → 1,721・投稿 2,425 → 2,407。外れた投稿は LGV-08 の (b) の18件と一致。増えた投稿は0（毎朝の取り込みの差は無い）

## 報告

- 状態: 作業中
- ブランチ: work/1107-lgv-hide
- ログ: https://github.com/retroeater/mj/blob/work/1107-lgv-hide/docs/logs/CHAT-1107-LGV-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1107-lgv-hide
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
