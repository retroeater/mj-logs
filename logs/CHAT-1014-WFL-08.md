# CHAT-1014-WFL-08

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl-s2
- 着手時HEAD: baccec3a8

## 指示

【Claude作成】Claude Code 向け指示：段①の展開その2（check-image-links の部品化・commit-push の部品・cleanup-logs・部品のテストを assets-check で回す） Chat-Ref: CHAT-1014-WFL-08 マージ: 判断待ちで止まる（`.github/`・`scripts/` の変更。作業ブランチの手動実行の結果と差分をチャット側が見てから） 貼る時機: CHAT-1014-WFL-07 の完了の後 作業ブランチ: クラウドセッションで実行する。work/1014-wfl-s2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl-s2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-07 のログの `## 報告` を読み、完了していなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」「共通の部品」を読む。

目的
#564 段①の展開の 2 本目。(1) 部品のテストを `assets-check.yml` で push ごとに回す (2) `check-image-links.yml` の 3 ジョブを部品で書き直す (3) commit-push の部品を作り、`cleanup-logs.yml` を部品で書き直す。
決定（2026-10-14、平野さん）

* （WFL-07 の決定）部品のテストは `assets-check.yml` に 1 ステップ足して push ごとに回す
* （WFL-04 の決定）issue の本文の組み立ては github-script から各スクリプトへ移し、元の JS と同じ文面かを突き合わせてテストを足す。commit-push の部品は展開の指示で作る。入力 `scheduled` と `run-name` の `[scheduled]` は Worker の起動の表に無い本にも足す
* （WFL-05 の決定）常設 issue の型S は ACF-21 の作り（閉じた常設 issue を開き直す）にそろえる

前提（チャット側。平野さんの決定ではない）

* `check-image-links.yml` は ACF-21（efac1e4f）の後の版を元にする。`check` と `sns_book` は同じ issue（#352、題「画像リンク切れの検知結果」・常設）の自分の節だけを書き換える（`scripts/check_image_links.py --issue-section`）。`saikyo` は別の issue（題「最強戦の選手写真のリンク切れ検知結果」）を書く。`needs: check`（同じ issue を同時に書かない）・`saikyo_resolve_test` の入力・Worker からの 04:30 の起動（入力 `scheduled`）は保つ（要確認。着手時に cloudflare の版で読み、ACF-21 より後に変わっていたら変えた Chat-Ref をログに書く）
* 節の書き換えは upsert-issue の 1 回の呼び出しでは収まらない（今の本文を読む → Python で自分の節を差し替える → 書く）。WFL-01 の案のとおり、`mode: read`（今の本文を書き出す）と `mode: state` の 2 回の呼び出しにするか、`--issue-section` を部品の外に残すかは実物を見て決め、理由をログに書く。`check_image_links.py` の `pick_issue()`・`issue_action()` と部品の型S が同じ判定になることをテストで確かめ、片方に寄せられるなら寄せる（二重に持たない）
* `saikyo` の issue（題「最強戦の選手写真のリンク切れ検知結果」）を常設ラベルにするか（今 issue があるか・番号・状態）は、表にしてログに書き、判断待ちに入れる（この指示ではラベルを付けない）
* commit-push の部品の案は WFL-01 のログ「composite action の案（段①）」の 4（入力 `paths`・`message`・`body`・`message-file`・`branch`・`retry`〈`rebase`／`none`〉・`autostash`、出力 `committed`・`sha`）。この指示で使うのは `cleanup-logs.yml` だけ（`message-file` に一覧を入れる形）。`cleanup-logs.yml` の通知（題「作業ログ: 自動削除の条件外のログ」#357、常設、無ければ失敗させる）は upsert-issue の `mode: notice`・`create: 'false'` で書く。本文の組み立ては `scripts/cleanup_logs.py` へ移す
* `cleanup-logs.yml` は push する（docs/logs の削除）。作業ブランチでは `dry_run: true` で通し、commit-push の部品は「差分が無ければ何もしない」道筋と、作業ブランチへ空でないコミットを push する道筋（試験の 1 ファイルを作って消す、など。cloudflare には push しない）を確かめる。試験のコミットを戻すコミットの差分を確かめる
* `check-image-links.yml` の書く道筋（issue の書き換え）は作業ブランチでは通さない（dry-run の入力が無ければ、upsert-issue の `dry-run` を一時的に `'true'` に固定したコミットで手動実行し、戻すコミットの差分が 1 行だけであることを確かめる）。作業ブランチでの手動実行は古いデータで #352 の本文を上書きしうる（docs/notes/github-actions.md「ワークフローを手動実行するとき」）ので、書く道筋で手動実行しない
* `check-image-links.yml` の完了は `regenerate-saikyo.yml` を `workflow_run`（`name:` で参照、cloudflare だけ）で起動する。`name:` は変えない（改名は段③）
* 初回の予約実行: check-image-links は毎日 04:30 JST（Worker）、cleanup-logs は月曜 06:23 JST

手順

1. `assets-check.yml` に部品のテストのステップを足す（`node --test` の 1 ステップ。`.github/actions/**` の変更を含む push で動くように `paths` も確かめる）。作業ブランチへの push で動き、success になることを確かめる
2. `check-image-links.yml` の 3 ジョブを部品（setup-python・resolve-mode・upsert-issue）で書き直す。本文の組み立てをスクリプトへ移し、元の JS と同じ文面かを突き合わせてテストを足す。「前提」の形で手動実行（`scheduled` なし・書かない）と予約の起動の道筋を確かめる。3 ジョブの結果（件数）が直前の cloudflare の実行と同じか、データの変化で説明できることを確かめる
3. commit-push の部品を作り、`cleanup-logs.yml` を部品（setup-python・resolve-mode・upsert-issue・commit-push）で書き直す。`SCHEDULE_ENABLED` を外し、入力 `scheduled` と `run-name` を足す。「前提」の形で手動実行を確かめる。docs/notes/github-actions.md の「共通の部品」と 3 本の記述を今の動きに直す。saikyo の issue の表をログに書く。#564 にコメントする。完了報告のうえ判断待ちで止まる

止まる条件

* WFL-07 が完了していない
* cloudflare の `check-image-links.yml` が ACF-21 より後に変わっていて、変更が「前提」の作り（節の書き換え・`needs: check`・issue の見つけ方）を変えている
* 元の JS と移した後の本文の文面が合わない（差を書いて止まる。直し方が明らかな差〈空白・改行の 1 か所〉は直してよい）
* 手動実行が起動できない（403・404）: 平野さんに GitHub の画面の「Run workflow」を頼む形で止まる
* 手動実行の結果が直前の cloudflare の実行と違い、データの変化で説明できない
* 試験のコミットが cloudflare へ push されそうになる（commit-push の部品の `branch` が実行ブランチ以外を指す）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-image-links.yml`・`cleanup-logs.yml`・`assets-check.yml`・`.github/actions/`・`scripts/check_image_links.py`・`scripts/check_sns_book_images.py`・`scripts/collect_saikyo_images.py`・`scripts/cleanup_logs.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（重なる本だけ外して進め、ログに書く。全部が重なるときだけ止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」にマージの可否と saikyo の issue の常設ラベルを書く。初回の予約実行の日時を「未確認の項目」に書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-08 は未使用
- CHAT-1014-WFL-07 のログ（cloudflare）の状態は「完了」
- 作業ブランチ: work/1014-wfl-s2 はローカル・リモートとも無し → `git checkout -b work/1014-wfl-s2 origin/cloudflare`（baccec3a8）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-s2
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s2/docs/logs/CHAT-1014-WFL-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s2
- 確認用URL: なし
- マージ: 未
- issue: #564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 806fba19）: https://github.com/retroeater/mj-logs/tree/main/guide/806fba19

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/806fba19/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
