# CHAT-1027-NTF-01

- 着手日時: 2026-10-27
- 対象issue: 未定（手順1で起票）
- ブランチ: work/1027-ntf
- 着手時HEAD: 8030de194

## 指示

【Claude作成】Claude Code 向け指示：本番ビルド失敗の通知を、Cloudflare のビルドの情報で本番の失敗だけに絞る
Chat-Ref: CHAT-1027-NTF-01
マージ: 承認済み（チャットで。下の「止まる条件」の確かめが全部通ったときだけ）
貼る時機: 平野さんが Secret `CF_BUILDS_TOKEN` を登録した後（0章で `gh secret list` で確かめる。無ければ何もせず止まる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1027-ntf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1027-ntf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1027-ntf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、Secret `CF_BUILDS_TOKEN` が登録されているかを `gh secret list` で確かめ（値は出さない）、無ければ何もせず止まる。

## 目的
`check-build-failure.yml`（#561）は、本番とプレビューを GitHub の情報だけで見分けられないため、cloudflare に入ったコミットのプレビューの失敗でも通知している。失敗したときだけ Cloudflare のビルドの API でトリガー（本番か）を確かめ、本番の失敗だけを通知する。

### 決定（2026-10-27、平野さん）
- 本番ビルド失敗の通知の誤報（プレビューの失敗）を止める。方式は、Cloudflare の読み取り専用のトークンを足し、失敗した check-run の Build ID で Cloudflare に問い合わせて、本番のトリガーの失敗のときだけ通知する（チャット側が出した選択肢 1〈推奨〉を選んだ）
- 問い合わせができないとき（トークンが無い・Cloudflare が答えない・答えの形が想定と違う）は、今までどおり通知する（安全側）
- マージは、下の「止まる条件」の確かめが全部通ったときだけ承認

### 前提（チャット側。平野さんの決定ではない）
- 誤報の実例: 2026-10-27 の check-run 122116895714（コミット d6f4fa847、Build ID 7e2972c9-8e91-4333-9de9-fdf2860ea070）。チャット側がダッシュボードの API（`accounts/{id}/builds/builds/<Build ID>`）で読んだ結果、トリガーは「Deploy non-production branches」（ブランチ work/1027-skp）、失敗の理由はアセットのアップロード中の API エラー（code 10013）。同じコミットの本番のビルド（ブランチ cloudflare）は 09:41:14Z に始まって success で、その check-run は GitHub に付いていなかった。ほかの通知の失敗の回は 10/21（run 39524306758）で、誤報かは未確認
- 本番の失敗の実例（10/13、docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」の表）: check-run 115128041224（コミット 5cbc83eb、Build ID fd3617b6-7a81-41a1-bc64-4bba14f4abde）と 115130712178（9bba7d55）。本番のトリガーのものかは API で確かめる（要確認）
- ダッシュボードの API で読めたビルドの答えには `trigger.trigger_name`・`trigger.branch_includes`（本番は `["cloudflare"]`）・`build_trigger_metadata.branch`（実際のブランチ名）がある。本番かの判定は `build_trigger_metadata.branch == "cloudflare"` を第一の案とする（実物に合わせて変えてよい。変えたら理由をログに書く）
- トークン: 平野さんが作る。権限はアカウントの「Workers CI」の Read（権限グループの API 上の名前は「Workers CI Read」。チャット側が `user/tokens/permission_groups` で確かめた。この権限で `accounts/{id}/builds/builds/<Build ID>` が読めるかは未確認＝要確認）。Secret 名は `CF_BUILDS_TOKEN`。`CF_ANALYTICS_TOKEN` と同じく、`CLOUDFLARE_API_TOKEN` とは別の名前・別の権限（読み取りだけ、デプロイできない）で、CLAUDE.md「禁止事項」に当たらないものとして扱う（docs/decisions/cloudflare.md の 2026-10-14 の扱いと同じ）
- アカウント ID は check-run の `details_url`（`dash.cloudflare.com/<アカウントID>/workers/...`）から取れる。変数を足すかは実物を見て決めてよい。ID・トークンの値はログに書かない
- 判定のスクリプトは `scripts/check_build_failure.py`（要確認）。呼ばれ方は `check_run` の completed と手動（入力 check_run_id・dry_run）（docs/notes/github-actions.md「ワークフローの一覧」の行。ほかから `uses:` で呼ばれていないかを `git grep -n check-build-failure` で確かめる）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#561 は閉じている）、着手中・進行中の別の issue があれば止まって報告する。無ければ新しい issue を起票し（題の案:「本番ビルド失敗の通知でプレビューの失敗を除く」、本文に上の実例と決定を書く）、着手中のコメントを書く。続けて、`CF_BUILDS_TOKEN` で `accounts/{id}/builds/builds/<Build ID>` を1件だけ呼んで通ることを、作業ブランチで手動実行（dry_run）で確かめる（docs/notes/chat-routines.md「外部サービスの設定を頼むとき」。通らなければ止まり、HTTP の状態と Cloudflare のエラーの code だけを報告する）
2. 判定に「Cloudflare のビルドが本番のトリガーのものか」を足す。本番なら今までどおり失敗にして通知、本番でないと確かめられたら通知せず（ジョブは成功）、理由（トリガー名・ブランチ）を注釈（notice）に出す。確かめられないときは通知する。分岐の検査を `scripts/tests/` に足す（API の答えは検査の中の固定の値で与え、外へは通信しない）
3. docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」の通知の説明（「受け入れた近似」の文）と docs/notes/github-actions.md「ワークフローの一覧」の行・「権限と Secret の渡し方」の表を、今の内容を読んでから置き換える。docs/notes/cloudflare.md の `CLOUDFLARE_API_TOKEN` の記述の近くに、`CF_BUILDS_TOKEN` が禁止に当たらない理由を1行足す（同じ趣旨の記述があれば置き換え・拡張でよい）

### 文書に足す規則の表（CLAUDE.md「Chat-Ref」節）
| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 本番かは check-run でなく Cloudflare のビルドのトリガーで見分ける | ワークフローのコード＋検査（docs/notes は説明だけ） | 判定を機械に持たせ、文書の規則にしない |

## 止まる条件
- 0章・手順1の確かめが通らない（Secret が無い、1件の呼び出しが通らない、同じ論点の進行中の issue がある）
- マージ前に、作業ブランチで次の手動実行（dry_run）が全部期待どおりにならない（どれか1つでも違えばマージしない）:
  - check_run_id 122116895714（10/27 のプレビューの失敗）→ 通知しない
  - check_run_id 115128041224（10/13 の本番の失敗）→ 通知する。API で本番のトリガーでないと出たら、期待が誤りなので止まって報告する
  - トークンが無い・使えないとき（Secret を一時的に読まない入力・検査のどちらで確かめてもよい。確かめ方をログに書く）→ 通知する
- `python3 -m unittest discover -s scripts/tests` が1件でも落ちる（今回の変更で落ちると分かっている検査は直してよい）
- 外のサイト・API に触れる手動実行の一時的な接続の失敗は、失敗したジョブだけを1回再実行して期待どおりなら止まらない
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `check-build-failure.yml`・`scripts/check_build_failure.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて残りを進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり。マージ後、起票した issue に結果を書いてクローズする（残る作業があれば別の issue に起票する）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1027-NTF-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1027-NTF-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・`NTF` の重複（全ブランチのコミットと docs/logs の履歴）とも無し。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し。work/1027-ntf はローカル・リモートとも無かったため `git checkout -b work/1027-ntf origin/cloudflare` で作成
- 0章: 指示欄の末尾は指示文の最後の行と一致。`gh secret list` はセッションのプロキシで HTTP 403（Actions の secrets のパスを通さない）で確かめられなかった。到達できないことを根拠に「無い」とせず、手順1の作業ブランチでの手動実行（dry_run）で、Secret を使った API の呼び出しが通ることをもって登録ありと確かめた（下）
- 0章ゲート: 未マージの work/ ブランチで `check-build-failure.yml`・`scripts/check_build_failure.py`・その検査・`docs/notes/cloudflare.md`・`docs/notes/github-actions.md` を変えているものは無し。CLAUDE.md 23,488 バイト・handover.md 22,221 バイト（警告域の外）
- 手順1: issue の検索（「本番ビルド失敗の通知 プレビュー」など）で同じ論点の進行中の issue は無し（#561・#622 は閉じている）。#634 を起票し、着手中のコメントを書いた。`git grep -n check-build-failure -- .github` で、ほかのワークフローから `uses:` で呼ばれていないことを確かめた
- 実装（eb159f943）:
  - `scripts/check_build_failure.py`: check-run の `details_url` からアカウント ID と Build ID を取り、`accounts/<id>/builds/builds/<Build ID>` を `CF_BUILDS_TOKEN` で読む。`build_trigger_metadata.branch == "cloudflare"` なら本番（第一の案のまま。実物の答えで本番のトリガー名は人の読める名前でなく ID 風の値だったため、トリガー名でなくブランチで判定する案が妥当）。本番でないと確かめられたら知らせず、`::notice::` に理由（トリガー名・ブランチ）を出す。Secret が無い・details_url が想定外・HTTP エラー（状態と Cloudflare の code を出す）・通信の失敗・答えの形の違いはすべて「確かめられない」として知らせる。問い合わせは、Workers Builds の失敗で cloudflare に入ったコミットのときだけ行う
  - `.github/workflows/check-build-failure.yml`: 判定のステップの `env:` で `CF_BUILDS_TOKEN` を受ける。冒頭の説明を直した
  - `scripts/tests/test_check_build_failure.py`: API の答えを固定値で与える検査 `LookupTest`（プレビュー・本番・トークン無し・details_url 想定外・HTTP 403・通信の失敗・答えの形の違い・judge への反映・問い合わせない場合）を足した。`MainTest` は Secret を外して実行し、知らせる（安全側）ことを確かめる。修正前のコードでは `lookup_production` などが無く `LookupTest` は通らない
  - `python3 -m unittest discover -s scripts/tests`: 1582 件 OK
- 作業ブランチでの手動実行（dry_run、eb159f943）:
  - run 40511445712（check_run_id 122116895714）: 「本番のトリガーのビルドではない(トリガー: Deploy non-production branches、ブランチ: work/1027-skp)ため知らせない」→ 期待どおり（通知しない）。これが手順1の「1件だけ呼んで通る」確かめも兼ねる（HTTP 200 で答えが読めた）
  - run 40511450666（check_run_id 115128041224）: 「Cloudflare のビルド: トリガー: <ID 風の値>、ブランチ: cloudflare」で知らせる判定（dry-run のためジョブは成功）→ 期待どおり（通知する）。10/13 の失敗が本番のトリガーのものであることも API で確かめられた
  - トークンが無い・使えないとき: 手動実行ではなく検査で確かめた（`LookupTest.test_unknown_when_no_token`・`test_unknown_when_http_error`、`MainTest.test_notify_fails_job` は環境変数 `CF_BUILDS_TOKEN` を外してスクリプトを実行し終了コード 1〈知らせる〉を確かめる）
- 文書（別コミット）: docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」の「受け入れた近似」の文を置き換え、「ビルド成否と本番の確認範囲」の `CLOUDFLARE_API_TOKEN` の方針の直後に、読み取りだけのトークン（`CF_ANALYTICS_TOKEN`・`CF_BUILDS_TOKEN`）が当たらない理由を1行足した（同じ趣旨の記述は cloudflare.md には無かった）。docs/notes/github-actions.md「ワークフローの一覧」の行を直し、「権限と Secret の渡し方」の表に `check-build-failure` の行を足した（表は呼ぶ側の job ごとの形のため、「なし（直接起動）」として足した）
- docs/decisions/cloudflare.md に決定を足した

## 報告

- 状態: 完了
- ブランチ: work/1027-ntf
- ログ: https://github.com/retroeater/mj/blob/work/1027-ntf/docs/logs/CHAT-1027-NTF-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1027-ntf
- 確認用URL: なし（ワークフロー・スクリプト・docs の変更でページは変わらない）
- マージ: 未（この後マージする）
- issue: #634（起票）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj baa00969）: https://github.com/retroeater/mj-logs/tree/main/guide/baa00969

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/baa00969/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8030de19.md
