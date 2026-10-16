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

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 10ba2aac）: https://github.com/retroeater/mj-logs/tree/main/guide/10ba2aac

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/10ba2aac/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
