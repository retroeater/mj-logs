# CHAT-1016-WFL-21

- 着手日時: 2026-10-16
- 対象issue: なし（#565 に関係）
- ブランチ: work/1016-wfl-glossary
- 着手時HEAD: 0835017c8

## 指示

【Claude作成】Claude Code 向け指示：用語集 docs/notes/glossary.md を作り、チャット側と Claude Code が使う用語を揃える
Chat-Ref: CHAT-1016-WFL-21
マージ: ドキュメントのみ（docs/notes・CLAUDE.md・ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1016-wfl-glossary を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-wfl-glossary origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。この指示は、1 回目に貼ったものが「知見 → 選んだ層 → 理由」の表が無いため着手前に止まった（作業ブランチ・ログ・コミットは無い）ので、表を足して同じ Chat-Ref のまま貼り直したもの。

## 目的
チャット側の指示文・Claude Code のログ・docs で、同じものを別の語で呼んだり、仲間内の言い方（「本」など）を使ったりして、平野さんが読めないことがあった。用語集を 1 つ作り、チャット側と Claude Code の両方が従うようにする。

### 決定（2026-10-16、平野さん）
- 用語集は mj の `docs/notes/glossary.md` に置く（案 A）。チャット側は docs/notes/chat-side-operations.md、Claude Code は CLAUDE.md から 1 行で参照する（mj-logs のガイドに docs/notes が写るので、どのチャットからも読める）
- Google スプレッドシートの用語は公式表記に合わせ、ファイルを「スプレッドシート」、タブを「シート」と書く（「ブック」「タブ」と書かない）
- 次の語に揃える（左が使う語、括弧内が使わない語）:
  - ワークフロー（「本」と書かない。数えるときも「3 つのワークフロー」）
  - スケジュール（`workers/scheduler/schedule.json`。「起動の表」と書かない）
  - 定期実行（Worker が決まった時刻に、入力 `scheduled` を真にして起動する実行。「予約の起動」「予約実行」と書かない）
  - フォールバック実行（Worker からの定期実行が無かった日に備えて、GitHub の `schedule`（cron）で動く実行。その日の定期実行が成功済みなら何もせず終わる。「保険の実行」と書かない）
  - 本実行／ドライラン（スプレッドシート・カレンダー・issue・ブランチに書き込む動かし方／読んで結果を出すだけの動かし方。入力 `apply`・`dry_run` に対応。「書く道筋」「書かない道筋」と書かない。「本番」〈cloudflare への反映〉とは別の語）
- 今ある docs・issue の「SNS ブック」「ABEMA ブック」「〜タブ」などは一括で置き換えない（案 A）。新しく書く文から新しい語にし、今ある docs はその箇所を触るときに直す。用語集に新旧の対応（例: 「SNS ブック」＝ SNS スプレッドシート、「辞書」タブ＝「辞書」シート）を書く

### 知見 → 選んだ層 → 理由
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| チャット側の指示文と Claude Code のログが、仲間内の言い方（ワークフローを「本」、定期実行を「予約の起動」、フォールバック実行を「保険の実行」、本実行／ドライランを「書く道筋／書かない道筋」、スケジュールを「起動の表」）を使い、平野さんが同じ語の意味を 2 回尋ねた（2026-10-15・10-16） | 推論的センサー（`docs/notes/glossary.md` を作り、chat-side-operations.md と CLAUDE.md から参照の 1 行を足す） | 計算的センサー（テスト・hook で使わない語を検出する）は、今ある docs を一括で置き換えない決定（案 A）のため、今の docs で落ちてしまい入れられない。「本」のような一般の語は文脈なしに検出できない。語の選び方は書く側の判断なので、チャット側と Claude Code の両方が読む文書に置くのがいちばん近い。使わない語の検出は、今ある docs の置き換えが進んだ後に #565（文書参照の検査テスト）と合わせて検討する |
| CLAUDE.md の「スプレッドシートのファイルは『ブック』と書く」が、Google の公式表記（スプレッドシート／シート）と違い、平野さんが公式表記に合わせると決めた | 推論的センサー（CLAUDE.md の該当行を置き換え、用語集を参照させる） | 規則そのものの置き換えなので、規則のある場所を直すのがいちばん近い。テストでは「ブック」が新しい文か既存の文かを区別できない |

### 前提（チャット側。平野さんの決定ではない）
- CLAUDE.md「コード規約」の「スプレッドシートのファイルは『ブック』と書く（『冊』で数えない）」は上の決定と食い違う。「スプレッドシートのファイルは『スプレッドシート』、タブは『シート』と書く（用語は docs/notes/glossary.md）」の形に置き換える（要確認。同じ趣旨の記述がほかの文書〈chat-side-operations.md・instruction-template.md など〉にあれば洗い出し、参照に置き換える）
- 用語集に載せる語は、上の決定の語に加えて次を入れる（チャット側の案。意味は実物の docs から確かめて書く）: ジョブ・実行（run）・作業ブランチ・マージ（作業ブランチを `cloudflare` に入れること＝本番反映）・本番反映・取り込み（git の `origin/cloudflare` の取り込み〈merge〉にだけ使い、データを外から持ってくることは「取得」と書く案。今の docs での使われ方を数えて、案が合わなければ別の案を判断待ちに入れる）。ほかに docs で同じものを別の語で呼んでいる例が見つかれば、用語集には入れずに候補として `## 報告`「判断が必要なこと」に挙げる（比較と推奨つき）
- 用語集の形: 1 語 1 行の表（使う語・使わない語・意味・対応する英語やファイル・入力の名前）。冒頭に「新しく書く文はこの語に従う。今ある docs はその箇所を触るときに直す」と書く
- CLAUDE.md・chat-side-operations.md の上限（各 28KB、警告域 26KB）を足す前と後で測る。CLAUDE.md・chat-side-operations.md に足すのは参照の 1 行ずつ（と CLAUDE.md の「ブック」の行の置き換え）だけ
- WFL-20 の `## 報告` も「本」を使っていた。この指示ではログを直さない（過去の記録）

## 手順
1. CLAUDE.md・docs/notes・docs/instruction-template.md で、決定の語（使わない語の側）と「ブック」「タブ」の使われ方を数え、ログに書く（ファイルごとの件数だけでよい）
2. `docs/notes/glossary.md` を作る。CLAUDE.md の「ブック」の行を置き換え、CLAUDE.md と chat-side-operations.md に参照の 1 行を足す。docs/notes/static-generation.md「ページの一覧」など、文書の一覧を持つ所があれば glossary.md を載せる（`.assetsignore` で公開しないことを確かめる）
3. 上の「決定」を `docs/decisions/` の合う文書（無ければ `docs/decisions/operations.md`）に `## 2026-10-16（CHAT-1016-WFL-21）` として足す。完了報告のうえ cloudflare へマージする（push 直前に再 fetch と祖先の確認）

## 止まる条件
- 同じ目的の用語集・issue が既にある（`git grep -i glossary`・「用語集」、`gh issue list --state all --search "用語"`）: 作らずに報告する
- CLAUDE.md か chat-side-operations.md が足した後に警告域（26KB）を超える（足さずに判断待ち）
- 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（ログ自体も新しい語で書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-WFL-21.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-WFL-21 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1016-WFL-21 のコミットは無い（1 回目に貼られたものは着手前に止めたので、作業ブランチ・ログ・コミットは無かった）
- 作業ブランチ: work/1016-wfl-glossary はリモート・ローカルとも無かったので origin/cloudflare（0835017c8）から作った
- 0. 指示欄の末尾は指示文の最後の行と一致。今回は「知見 → 選んだ層 → 理由」の表があるので、CLAUDE.md「Chat-Ref」の止まる条件に当たらない
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている
- 着手は JST 10:42
- 同じ目的の用語集・issue: `git grep -il 'glossary\|用語集'` で当たるのは `.claude/skills/` の汎用のスキル文書だけ（このプロジェクトの用語集は無い）。GitHub の issue の検索（「用語 用語集 glossary 呼び方を揃える」）は 0 件。止まる条件に当たらない

### 手順1: 使わない語の件数（CLAUDE.md・docs/notes〈handover-archive を除く〉・docs/instruction-template.md）

| 語 | 件数（行） | 多いファイル |
| --- | --- | --- |
| ブック | 115 | ask-worker.md 20・saikyo-page-design.md 15・static-generation.md 12・video-wayhome.md 11・sns-book.md 9・books-covers.md 9・github-actions.md 6・books-freeze.md 6 |
| タブ | 191 | static-generation.md 31・title-pages.md 30・live-page-design.md 28・live-channel-write.md 22・houou-top.md 20・video-wayhome.md 9・yotei-sheet.md 7・video-study.md 5 |
| 起動の表 | 10 | github-actions.md 6・scheduler-worker.md 3・redirects.md 1 |
| 予約の起動 | 18 | scheduler-worker.md 8・github-actions.md 8・saikyo-page-design.md 1・dojo-guest-calendar.md 1 |
| 予約実行 | 32 | github-actions.md 11・scheduler-worker.md 7・dojo-guest-calendar.md 4・live-channel-write.md 2・cloud-sessions.md 2・ほか |
| 保険の実行 | 5 | github-actions.md 3・scheduler-worker.md 2 |
| 書く道筋 | 0 | — |
| 書かない道筋 | 2 | github-actions.md 2 |
| 本ずつ（「1本ずつ」など） | 4 | github-actions.md 2・yotei-sheet.md 1・scheduler-worker.md 1 |
| 取り込み | 49（「取り込」を含む行は 65） | live-page-design.md 9・title-pages.md 8・live-channel-write.md 7・yotei-sheet.md 5・instruction-template.md 5・branch-operations.md 4 |

- 「本」は一般の語（「本番」「本実行」「本文」など）と区別できないので、ワークフローを数える「〜本」は件数に入れていない（「本ずつ」だけを数えた）
- 「取り込み」: git の意味（`origin/cloudflare` を merge する）で使っている行は 10 ほどで、残りはデータを外から持ってくる意味（「毎朝の取り込み」「層1の取り込み」「CSV を取り込む」、ワークフローの名前「「連盟ch」の取り込み」「予定表の取り込み」「道場部ゲストの取り込み」など）。前提の案（git の意味にだけ使い、データは「取得」）は今の使われ方と合わないので、用語集では git の意味の行を置き、使い分けは判断待ちにした
- 「ブック」の規則（「ブックと書く」）は CLAUDE.md「コード規約」の1か所だけ。chat-side-operations.md・instruction-template.md には同じ趣旨の規則は無い（chat-side-operations.md の「ブックは `scripts/` の定数名などで呼ぶ」、instruction-template.md の「シート・SNS ブックなど」は呼び名として使っているだけ。触っていない）

### 手順2: 用語集と参照（b31739cd1）

- `docs/notes/glossary.md` を作った。3 つの表（GitHub Actions・Worker／ブランチ・反映／Google スプレッドシート）で、1 語 1 行（使う語・使わない語・意味・対応する英語やファイル・入力）。冒頭に「新しく書く文はこの語に従う。今ある docs・issue はその箇所を触るときに直す」。載せた語: ワークフロー・ジョブ・実行・スケジュール・定期実行・フォールバック実行・手動実行・本実行・ドライラン・作業ブランチ・マージ・本番反映・取り込み（git）・スプレッドシート・シート。新旧の対応（SNS ブック → SNS スプレッドシート、ABEMA ブック → ABEMA スプレッドシート、「〜タブ」→「〜シート」）
- CLAUDE.md「コード規約」の「スプレッドシートのファイルは『ブック』と書く（『冊』で数えない）」を「スプレッドシートのファイルは『スプレッドシート』、タブは『シート』と書く。ほかの語（ワークフロー・定期実行・本実行など）も docs/notes/glossary.md に従う（新しく書く文から）」に置き換えた（置き換えと参照を1行にした）
- docs/notes/chat-side-operations.md「指示文の書き方・渡し方」の先頭に参照の1行を足した
- docs/handover.md の文書の一覧の表（chat-side-operations.md の次）に glossary.md の行を足した（docs/notes/static-generation.md「ページの一覧」はページの一覧で、文書の一覧ではないので載せていない）
- 公開しないこと: `.assetsignore` の 7 行目 `docs` で docs/ 全体が配信から外れている
- サイズ（バイト。上限 28,672・警告域 26,624）: CLAUDE.md 23,651 → 23,801、chat-side-operations.md 22,192 → 22,361、handover.md 23,172 → 23,349。警告域を超えない
- unittest: OK

### 手順3

- `docs/decisions/operations.md` に「## 2026-10-16（CHAT-1016-WFL-21）」を足した（自動化だけの話ではないので operations.md にした）

## 報告

- 状態: 完了
- ブランチ: work/1016-wfl-glossary（マージ済み。削除は cleanup-branches.yml に任せる）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1016-WFL-21.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-wfl-glossary
- 確認用URL: なし
- マージ: 済（f6fae4d33）
- issue: なし
- 判断が必要なこと:
  1. 「取り込み」の使い分け: 今の docs では git の意味（`origin/cloudflare` の取り込み）は 10 行ほどで、残りの 50 行ほどはデータを持ってくる意味（ワークフローの名前「「連盟ch」の取り込み」「予定表の取り込み」を含む）。(a) git の意味は「`origin/cloudflare` の取り込み」と対象を添えて書き、データの意味の「取り込み」は今のまま使う／(b) 前提の案どおり git の意味にだけ使い、データは「取得」と書く（ワークフローの名前も変えることになる）／(c) git の意味を「マージ」と区別して「merge」と書く。推奨 (a)（ワークフローの名前を変えずに済み、git の意味は対象を添えれば取り違えない）。用語集は (a) を仮に書いた
  2. ほかに同じものを別の語で呼んでいる例（用語集には入れていない）: GitHub の `schedule`（cron）で動く実行のうち、フォールバックではないもの（週次の check-meibo・check-saikyo-unregistered・cleanup-logs など、cron が本来の起動）を、今の docs は「予約実行」と呼んでいる。(a) 「cron の実行」と書く／(b) 「定期実行」に含める（Worker からの定期実行と区別できなくなる）。推奨 (a)（定期実行は Worker の起動に限る今回の決定と食い違わない）
  3. chat-side-operations.md は冒頭で「純増したら、足した分と同じ量を docs/notes/ の各文書へ移す」としている。今回は指示どおり参照の1行（169 バイト）を足しただけで、移していない（警告の 24,576 バイトより下の 22,361 バイト）。移すならどの項目を移すかはチャット側で決めてほしい
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2536efeb）: https://github.com/retroeater/mj-logs/tree/main/guide/2536efeb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2536efeb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
