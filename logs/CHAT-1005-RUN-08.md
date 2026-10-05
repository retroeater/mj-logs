# CHAT-1005-RUN-08

- 着手日時: 2026-10-05
- 対象issue: #491（ほか #448・#472・#298・#498・#449 を参照）
- ブランチ: work/1005-run-08
- 着手時HEAD: a12c2f8e

## 指示

【Claude作成】Claude Code 向け指示：予約実行を Cloudflare の Worker から時刻どおりに起動する仕組みと、起動失敗・遅れの検知の設計案を作る（調査と設計のみ。実装しない）
Chat-Ref: CHAT-1005-RUN-08
マージ: ドキュメントのみ（docs/logs/・docs/decisions/。設計案を docs/notes/ に置くならそれも）の変更なので、CLAUDE.md「ブランチ運用」の規則どおり、完了報告のうえマージしてよい。それ以外のファイルを変える必要が出たら、マージせず判断待ちで止まる
貼る時機: いつでも（CHAT-1005-RUN-07 とは作業ブランチが別で、結果も使わない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1005-run-08〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1005-run-08 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1005-run-08 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1005-run-08 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
変更の範囲: docs/logs/・docs/decisions/（設計案を置くなら docs/notes/）と、#491 へのコメント。ワークフロー・コード・`wrangler.jsonc`・Cloudflare の設定は変えない。ワークフローの手動実行もしない。issue の起票・クローズ・題の変更はしない（案を報告に書く）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
GitHub Actions の予約実行（schedule）が、どのワークフローも予定より2時間半〜5時間遅れて動いている。平野さんは、Cloudflare の Worker の定時実行から GitHub のワークフローを起動する方式に変えると決めた。実装の指示を出す前に、実物（ワークフロー・デプロイの仕組み・既存の issue）を調べて、設計案と平野さんの作業の一覧を作る。

### 決定（2026-10-05、平野さん）
- 予約実行の遅れへの対処は、Cloudflare の Worker の定時実行（Cron Triggers）から GitHub のワークフローを起動する方式にする。理由: やがて時刻の正確さが問われるジョブが出る見込みで、先に備える
- 朝のジョブは、6時（JST）までに完了していること
- 起動の失敗や遅れを検知する仕組みを、別に入れる

### 前提（チャット側。平野さんの決定ではない）
- 識別子 RUN は同じチャットの RUN-01〜07 で使っている。識別子の確認でそれらのコミットやログが見つかっても、同じチャットの指示なので重複ではない
- 実測（チャット側が mj-logs の actions/status.md〈2026-10-05 10:18 JST の書き出し〉と CHAT-1001-GSC-01 のログから読んだ値。開始時刻は JST）:

| ワークフロー | 予定 | 実際の開始 |
|---|---|---|
| update-live-channel.yml（毎日） | 02:43 | 09-29 07:37・10-04 05:10・10-05 05:23 |
| sync-dojo-calendar.yml（毎日） | 07:12 | 10-03 10:07・10-04 09:32・10-05 09:50 |
| delete-merged-branches.yml（毎日） | 07:53 | 09-30 10:39・10-01 10:37・10-02 10:50・10-03 10:29・10-04 11:10 |
| sync-logs.yml（毎日、#498 で追加） | 08:29 | 10-05 は 10:18 の時点でまだ動いていない |
| check-image-links.yml（月曜） | 03:00 | 09-28 06:03・10-05 06:00 |
| check-meibo.yml（月曜） | 05:07 | 09-28 07:57・10-05 07:59 |
| sync-birthday-calendar.yml（月曜） | 05:17 | 09-21 07:27・09-28 08:04・10-05 08:11 |
| regenerate-page.yml（月曜） | 05:37 | 10-05 08:33 |
| cleanup-logs.yml（月曜） | 06:23 | 09-21 08:19・09-28 08:50・10-05 09:00 |
| check-saikyo-unregistered.yml（月曜） | 06:50 | 09-28 09:01・10-05 09:13 |
| fetch-gsc.yml（月次） | 06:00 | 10-01 09:13 |

- 原因の見立て: GitHub の公式の文書は、予約実行は負荷の高い時間帯に遅れることがあり、負荷が高ければ落とされることもあるとしている。cron の分をずらしても遅れは一律に出ており、こちらの設定では直せない（チャット側が 2026-10-05 に検索で確認。文書の今の文言は要確認）
- Cloudflare 側の事実（チャット側が検索で確認。今の値は要確認）: Cron Triggers はアカウントあたり Workers Free で5つ、Workers Paid で250。CLAUDE.md「構成」と docs/notes/cloudflare.md によると、本番反映は Workers Builds（ダッシュボードの Git 連携）で、`CLOUDFLARE_API_TOKEN` を GitHub Secret に登録しない方針（二重デプロイの防止）。Workers Builds は Free（同時に走るビルド1本）
- 設計のたたき台（チャット側の案。実物に合わなければ変えてよい。変えた理由を書く）:
  1. サイト本体とは別の Worker にする（サイトの Worker にコードを足すと `_headers` が効かなくなる。#449）
  2. cron は少ない本数にする（例: 5分おきの1本）。「どのワークフローを、何曜日の何時何分（JST）に起動するか」の表をコードの設定として持ち、時刻が来たものを GitHub の API（workflow_dispatch）で起動する。表はリポジトリに置く
  3. 検知は2層にする。層1: Worker が6時（JST）に、当日ぶんの実行が起動され成功したかを API で確かめ、未起動・失敗・実行中を通知する。層2: GitHub 側（#498 の status.md の書き出しに相乗りする案）でも同じ判定をし、Worker やトークンが止まっていても気づけるようにする
  4. 通知先は、既存の常設の通知 issue の流儀（「種類: 常設」のラベル）に合わせる
  5. 今の schedule は、保険として遅い時刻に残すか、外すかを決める（残すなら、当日すでに成功していれば何もしない作りが要る）
- 調べて、表にまとめること:
  1. 予約実行を持つ全ワークフロー: cron、`workflow_dispatch` の有無と入力、`github.event_name` や `github.event.schedule` で分岐している箇所（起動の契機が手動実行に変わると動きが変わるもの）、所要時間、ほかのワークフローとの順序の依存（同じファイルへの push がぶつかる組など）
  2. 6時（JST）までに完了させるための起動時刻の案と、Worker から起動する範囲の案（毎日のものだけか、週次・月次も含めるか）。予約実行のままでよいものがあれば理由を書く
  3. 別の Worker を同じリポジトリに置く場合の、デプロイの経路の選択肢（Workers Builds をもう1つつなぐ、ほか）と、それぞれで要る設定・Secret。push のたびに余計なビルドが走らないか（Workers Builds は同時1本）。`CLOUDFLARE_API_TOKEN` を GitHub に置かない方針との整合
  4. GitHub 側のトークン: 種類（fine-grained の PAT など）、要る権限（ワークフローの起動、通知に issue のコメントを使うならその権限）、期限と更新の手間、置き場所（Worker の Secret）。トークンが切れたときに層2で気づけるか
  5. 検知の具体案: 何を「遅れ」「失敗」とするか、通知の文面と通知先、誤報を出さないための条件（祝日や手動実行との重なりなど）。status.md（#498）への相乗りでできる範囲
  6. 費用と上限: Workers のリクエスト数・CPU 時間・Cron Triggers の本数、Actions の使用量（#298）への影響
  7. 段階の案（例: まず毎日の1〜2本で試し、問題が無ければ広げる）と、試験の方法（Worker の定時実行をどう試すか、失敗の検知をどう試すか）
  8. 平野さんの作業の一覧（トークンの発行、Cloudflare のダッシュボードでの設定、Workers のプランの確認など）。それぞれ、いつ・どの画面で・何を、まで書く
  9. 同じ論点の issue（クローズ済みとコメントの決定も含めて検索する）: #491（実行時刻の遅れ）、#448（#491 の元）、#472（週次実行の確認・再試行）、#298（Actions の使用量）、#498（status.md）、#449 など。これまでの決定とこの方式が食い違わないかを整理し、この作業を #491 の範囲を広げて扱うか、新しい issue にするかの案を書く
- #491 には、上の実測の表と、平野さんの決定（Worker から起動する方式・6時までに完了・検知を入れる）を1つのコメントにまとめる（末尾に Chat-Ref）。本文・題・ラベルは変えない
- 設計案は `## 経過` に書く。長くなるなら docs/notes/ に1ファイルで置き、ログにはその場所と要点を書く
- 決定は docs/decisions/ の合う分野のファイルに追記する
- ログは public（mj-logs）。トークンや鍵の値、非公開の URL は書かない

## 手順
1. 0章ゲート（docs/notes/branch-operations.md）と、同じ論点の issue の検索・整理（上の 9）
2. ワークフロー・デプロイの仕組み・Cloudflare の記録（docs/notes/cloudflare.md）を読み、上の 1〜8 を調べる。Cloudflare と GitHub の公式の文書で、今の上限と仕様を確かめる
3. 設計案（構成・起動時刻の表・検知・段階・平野さんの作業）と、決めてほしい点を書き出す
4. #491 にコメントし、決定を docs/decisions/ に追記して、マージする（冒頭の「マージ:」の行）

## 止まる条件
- 同じ論点の issue に、この方式と食い違う決定（例: Worker を足さない、外部のスケジューラを使わない）が既にある（設計は進めず、その決定を報告する）
- #491 が閉じている（コメントはせず、報告に書く。調査は続ける）
- 調べるのに、ワークフローの実行や Cloudflare の設定の変更が要る（やらずに、要る理由を報告に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RUN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RUN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0. 指示欄の末尾は指示文の最後の行（「この行が指示文の最後の行です。」）と一致。
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある。
- 識別子の確認: `CHAT-1005-RUN-08` のコミットは 0件。RUN の他のコミットは同じチャットの RUN-01〜07。
- `origin/work/1005-run-08` は無く、`git checkout -b work/1005-run-08 origin/cloudflare` で作成。

## 報告

- 状態: 作業中
- ブランチ: work/1005-run-08
- ログ: https://github.com/retroeater/mj/blob/work/1005-run-08/docs/logs/CHAT-1005-RUN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1005-run-08
- 確認用URL: なし
- マージ: 未
- issue: #491
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a12c2f8e）: https://github.com/retroeater/mj-logs/tree/main/guide/a12c2f8e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a12c2f8e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b89c3b19.md
