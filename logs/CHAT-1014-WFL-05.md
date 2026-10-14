# CHAT-1014-WFL-05

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl-s1
- 着手時HEAD: 59e33bdf0

## 指示

【Claude作成】Claude Code 向け指示：段①の展開その1（upsert-issue に「常設 issue を開き直す」を入れ、検知 2 本をそろえる） Chat-Ref: CHAT-1014-WFL-05 マージ: 判断待ちで止まる（`.github/`・`scripts/` の変更。作業ブランチの手動実行の結果と差分をチャット側が見てから） 貼る時機: CHAT-1014-WFL-04 の完了の後 作業ブランチ: クラウドセッションで実行する。work/1014-wfl-s1 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-wfl-s1 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-04 のログの `## 報告` を読み、完了していなければ何もせず止まる。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」「共通の部品」を読む。

目的
#564 段①の展開の 1 本目。upsert-issue の型S（`mode: state`）を ACF-21 の作り（閉じた常設 issue を開き直す）にそろえ、`check-saikyo-unregistered.yml`（WFL-03 で部品化済み）と `check-meibo.yml`（2 つの検知）をその動きにする。check-image-links の 3 ジョブの部品化は次の指示。
決定（2026-10-14、平野さん）

* 常設 issue の型S は ACF-21 の作りにそろえる（案 A）: 同じ題・PR でない issue のうち、ラベル「種類: 常設」のものを状態を問わず優先する（複数なら番号の小さい方）。常設が無ければ開いているもの。検知で問題ありのとき、閉じていれば開き直す・開いていれば本文を書き換える・無ければラベル「種類: 常設」を付けて作る。問題 0 件のとき、開いていれば閉じる・閉じていれば何もしない。check-meibo ×2・check-saikyo-unregistered もこの動きに寄せる（10/14 の #562 のような重複を防ぎ、経緯を 1 つの issue にまとめるため）
* （WFL-04 の決定のとおり）issue の本文の組み立ては github-script から各スクリプトへ移し、元の JS と同じ文面かを突き合わせてテストを足す。入力 `scheduled` と `run-name` の `[scheduled]` を足す

前提（チャット側。平野さんの決定ではない）

* ACF-21 の作りの正は `scripts/check_image_links.py`（cloudflare efac1e4f）の `pick_issue()`・`issue_action()`、テストは `scripts/tests/test_check_sns_book_images.py` の `PickIssueTest`（4 件）・`IssueActionTest`（1 件）。経緯は CHAT-1014-ACF-21 のログ（要確認。WFL-04 の取り込み後の cloudflare で読む）。この 5 件の観点を部品側（`upsert.js`）のテストにも持たせる。check-image-links はこの指示では変えない（次の指示で部品へ移すまで、ACF-21 の作りのまま動く）
* 部品のテストの置き場所と回し方は実物に合わせて決めてよい（例: `node --test` で `.github/actions/upsert-issue/test/*.test.mjs`、`github` は偽物を渡す）。どこかの既存のワークフロー（例: `assets-check.yml`）で回すか、手で回すだけかはログに書いて判断待ちに入れる
* 3 つの常設 issue の今の状態（チャット側が 2026-10-14 12:50 JST に REST で見た。要確認）: 題「名簿と「プロ」シートの不一致」の最新は #445（closed・ラベル無し）。「Ampai の URL が無い期」「最強戦の出場者の登録漏れ」は題で探して確かめる。ラベル「種類: 常設」が付いている issue は #545・#506・#481・#475・#426・#357・#352 で、この 3 つの題のものは無い。ラベルを今ある closed の issue に付けるか（付ければ次に問題が出たときにそれが開き直される）、付けずに次に作られる issue から常設にするかは、題ごとに番号と状態を表にしてログに書き、判断待ちに入れる（この指示ではラベルを付けない）
* `check-meibo.yml` の今の作り: 名簿の不一致（題「名簿と「プロ」シートの不一致」、本文 = summary.md ＋脚注）と Ampai（題「Ampai の URL が無い期」）の 2 つを、それぞれ github-script で書く。dry_run の既定は手動で `true`、`SCHEDULE_ENABLED: 'true'`（WFL-02 の決定でやめる）。WFL-01 の対応表の推奨で、2 つの検知を 2 ジョブに分ける（片方が落ちても片方は動く）案があった（要確認。分けるかはこの指示で実物を見て決め、理由をログに書く）
* 書く道筋は作業ブランチで通さない（WFL-03 と同じ形）。予約の起動の道筋は、スクリプトの呼び出しを書かない形に強制する 1 行を足したコミットで `scheduled: true` を手動実行し、resolve-mode と upsert-issue の出力（dry-run の「することの一覧」）を確かめ、その行を戻すコミットの差分が 1 行だけであることを確かめる
* 初回の予約実行: `check-meibo.yml` は月曜 05:07 JST（次は 10/19）。check-saikyo-unregistered の 10/19 06:50 の初回の確かめはカレンダー【R#564】に入っている（WFL-04 のマージが前提）

手順

1. upsert-issue の `mode: state` を「決定」の作りに直す（題で探す範囲を `state: all` にし、常設ラベルの優先・開き直し・作るときのラベル付けを入れる。入力に `label`〈既定「種類: 常設」〉を足してよい）。ACF-21 のテスト 5 件と同じ観点のテストを部品側に作る。`check-saikyo-unregistered.yml` は部品の変更だけで新しい動きになることを、作業ブランチの `dry_run: true` の手動実行のサマリ（見つけた issue の番号・状態・することの一覧）で確かめる
2. `check-meibo.yml` を部品（setup-python・resolve-mode・upsert-issue）で書き直す。名簿と Ampai の本文の組み立てを `scripts/check_meibo.py`・`scripts/check_ampai.py` へ移し（`--issue-dir` のように本文・閉じるときのコメントのファイルを書き出す形。check_saikyo_unregistered と同じ）、元の JS と同じ文面かを突き合わせてテストを足す。`SCHEDULE_ENABLED` を外し、入力 `scheduled` と `run-name` を足す。2 ジョブに分けるかを決めて実装する。作業ブランチで `dry_run: true` の手動実行と、「前提」の形の予約の起動の道筋を確かめる。`python3 -m unittest discover -s scripts/tests` と部品のテストが通ること
3. docs/notes/github-actions.md の「共通の部品」と check-meibo・check-saikyo-unregistered の記述を今の動きに直す。3 つの常設 issue の表（題・番号・状態・ラベル、ラベルを付けるかの案と推奨）をログに書く。#564 にコメントする（本文は変えない）。完了報告のうえ判断待ちで止まる

止まる条件

* WFL-04 が完了していない
* ACF-21 の作り（`pick_issue()`・`issue_action()`）が「前提」と違う、または cloudflare で check-image-links がさらに変わっている（変えた Chat-Ref をログに書いて止まる）
* 元の JS と移した後の本文の文面が合わない（差を書いて止まる。直し方が明らかな差〈空白・改行の 1 か所〉は直してよい）
* 手動実行が起動できない（403・404）: 平野さんに GitHub の画面の「Run workflow」を頼む形で止まる
* 手動実行の結果（不一致の件数・Ampai の期）が直前の success の実行と違い、データの変化で説明できない
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-meibo.yml`・`check-saikyo-unregistered.yml`・`.github/actions/`・`scripts/check_meibo.py`・`scripts/check_ampai.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」にマージの可否、常設ラベルの付け方（題ごと）、部品のテストの回し方を書く。初回の予約実行の日時を「未確認の項目」に書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-05 は未使用
- CHAT-1014-WFL-04 のログ（cloudflare）の状態は「完了」
- 作業ブランチ: work/1014-wfl-s1 はローカル・リモートとも無し → `git checkout -b work/1014-wfl-s1 origin/cloudflare`（59e33bdf0）
- 0. 指示欄の末尾は指示文の最後の行と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-s1
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-s1/docs/logs/CHAT-1014-WFL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-s1
- 確認用URL: なし
- マージ: 未
- issue: #564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 76487b6d）: https://github.com/retroeater/mj-logs/tree/main/guide/76487b6d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/76487b6d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1d39d68a.md
