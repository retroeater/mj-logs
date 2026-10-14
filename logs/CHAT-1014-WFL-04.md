# CHAT-1014-WFL-04

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl-parts
- 着手時HEAD: 63cdd9362

## 指示

【Claude作成】Claude Code 向け指示：段①の部品と 2 本の書き直し（work/1014-wfl-parts）をマージし、展開の決定を記録する Chat-Ref: CHAT-1014-WFL-04 マージ: 承認済み（チャットで。この指示文を貼ることが承認。止まる条件つき） 貼る時機: いつでも（CHAT-1014-WFL-03 の判断待ちへの回答） 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-wfl-parts を続けて使う（WFL-03 の成果物をそのままマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
#564 段①の部品 3 つ（setup-python・resolve-mode・upsert-issue）・`.python-version`・`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml` の書き直しを cloudflare に入れ、WFL-03 の「判断が必要なこと」への回答を決定として記録する。
決定（2026-10-14、平野さん）
WFL-03 の `## 報告`「判断が必要なこと」への回答。いずれも Claude Code の推奨のとおり。

* work/1014-wfl-parts をマージする。件名と中身が合わない df7d24ea0・トレーラの無い 93c8fdec1 はそのままでよい（ブランチを作り直さない）
* upsert-issue は dry-run でも題で issue を探す（書かずに、することを実行サマリに出す）。今の作りのまま
* 展開では、issue の本文の組み立てを github-script から各スクリプトへ移す（check-meibo ×2・check-image-links ×3・cleanup-logs・update-live-channel ×2・sync-dojo-calendar）。check_saikyo_unregistered と同じく、元の JS と同じ文面かを突き合わせてテストを足す
* sync-dojo-calendar の結果ごとの複数コメントは、`---` で区切って 1 件にまとめる
* commit-push の部品は展開の指示で作る
* 展開でも、入力 `scheduled` と `run-name` の `[scheduled]` を Worker の起動の表に無い本にも足す（段④の差分を表の行だけにするため）

前提（チャット側。平野さんの決定ではない）

* WFL-03 の作業ブランチでの手動実行は 5 回すべて success。書く道筋（`apply: true`・予約の起動）は作業ブランチでは通していない。初回の予約実行で確かめる: `sync-birthday-calendar` 2026-10-19（月）05:17 JST、`check-saikyo-unregistered` 2026-10-19（月）06:50 JST（GitHub の予約は 2.5〜5 時間遅れる）。チャット側がカレンダー「ryoei.pro」に予定を入れる
* 2 本はどちらも `schedule` と `workflow_dispatch` だけで呼ばれる（WFL-03 の「前提の確かめ」）。ACF-21（efac1e4f）は `check-image-links.yml`・`scripts/check_image_links.py` を変えたが、この 2 本と部品には触れていない（要確認）
* マージで本番の配信物は変わらない（`.github/`・`scripts/`・docs・`.assetsignore` に `.python-version` を足しただけ）

手順

1. WFL-03 のログ（`docs/logs/CHAT-1014-WFL-03.md`）の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-WFL-04` を足す。`origin/cloudflare` を merge で取り込む。両方の変更が両立する衝突（追記どうし・隣り合う行。`docs/decisions/automation.md`・`docs/notes/github-actions.md` で起こりうる）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
2. 取り込んだ後で、`python3 -m unittest discover -s scripts/tests` が通ること、2 本と部品の YAML が取り込みで変わっていないこと（`git diff <WFL-03 の最終コミット> HEAD -- .github/` が空）を確かめる。取り込みで 2 本・部品が変わっていたら、2 本を作業ブランチで `dry_run: true`／`apply: false` で手動実行し直して success を確かめる。上の「決定」を `docs/decisions/automation.md` に `## 2026-10-14（CHAT-1014-WFL-04）` として足す
3. cloudflare へマージする（CLAUDE.md「ブランチ運用」の手順。push 直前に再 fetch と祖先の確認）。マージ後に cloudflare の該当コミットの check-run を見て、今回の変更による失敗が無いことを確かめる（無関係な失敗は原因を報告に書いて進めてよい）。#564 の段①のチェック項目に「部品と 2 本をマージ（<SHA>）。展開は次の指示」とコメントする（本文は変えない）。初回の予約実行の日時を「未確認の項目」に書く

止まる条件

* WFL-03 のログの状態が「判断待ち」でない
* 取り込みで両立しない衝突がある
* unittest が失敗する（今回の変更で落ちると分かったテストは直してよい。直したらログに書く）
* 取り込みで 2 本・部品が変わり、手動実行し直した結果が failure
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml`・`.github/actions/`・`scripts/check_saikyo_unregistered.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: CHAT-1014-WFL-04 は未使用
- 作業ブランチ: work/1014-wfl-parts はローカル・リモートとも 63cdd9362 で一致。そのまま使う
- 0. 指示欄の末尾は指示文の最後の行と一致。WFL-03 のログの `## 報告` の状態は「判断待ち」
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

### 手順1: 続きの印と取り込み

- WFL-03 のログの `## 報告`（最後の一致）の状態を「判断待ち / 続き: CHAT-1014-WFL-04」にした
- 未マージの work/ ブランチで `.github/`・`scripts/check_saikyo_unregistered.py`・`docs/notes/github-actions.md`・`docs/decisions/automation.md` を変えるものは無い（重なり無し）
- 前提の ACF-21（`check-image-links.yml`・`scripts/check_image_links.py`）は、WFL-03 の着手時（be98b1fc3）より前に cloudflare に入っていて、この2本と部品には触れていない
- `git merge --no-edit origin/cloudflare`（082398d78）は衝突なし。取り込んだのは docs・ログと `scripts/check_unused_names.py`・`scripts/lib/unused_names.py`・テスト（SNM-23 など）

### 手順2: 確かめと決定の記録

- `python3 -m unittest discover -s scripts/tests`: OK
- `git diff 63cdd9362 HEAD -- .github/` は空（2本と部品は取り込みで変わっていない）。`scripts/check_saikyo_unregistered.py`・`.python-version`・`.assetsignore` も変わっていない。手動実行のやり直しは不要
- `docs/decisions/automation.md` に「## 2026-10-14（CHAT-1014-WFL-04）」を足した（WFL-03 の節の後）

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl-parts
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl-parts/docs/logs/CHAT-1014-WFL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl-parts
- 確認用URL: なし
- マージ: 未
- issue: #564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3f0d362d）: https://github.com/retroeater/mj-logs/tree/main/guide/3f0d362d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3f0d362d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/be98b1fc.md
