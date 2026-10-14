# CHAT-1014-WFL-02

- 着手日時: 2026-10-14
- 対象issue: #564
- ブランチ: work/1014-wfl
- 着手時HEAD: b1259d7e3

## 指示

【Claude作成】Claude Code 向け指示：WFL-01 のマージ（衝突の解き方つき）と、改名・段①の決定の記録 Chat-Ref: CHAT-1014-WFL-02 マージ: ドキュメントのみ（ログ・docs/decisions・issue のコメント）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1014-WFL-01 の判断待ちへの回答） 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-wfl を続けて使う（WFL-01 の成果物〈ログ・決定の記録〉を同じブランチでマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1014-WFL-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
WFL-01（親 issue #564 の起票と、改名・統合の対応表）を cloudflare に入れる。`docs/decisions/operations.md` の衝突の解き方を指定し、WFL-01 の「判断が必要なこと」への平野さんの回答を決定として記録する。
決定（2026-10-14、平野さん）
WFL-01 の `## 報告`「判断が必要なこと」への回答。いずれも Claude Code の推奨のとおり。

* WFL-01 のマージ: `docs/decisions/operations.md` の衝突は、両方を残し、OHA-05 の節の後に WFL-01 の節を置く形で解いてマージする
* 改名（#564 の段③で行う）:
   * A-1: `sitemap-lastmod.yml` は `regenerate-sitemap.yml` にする（`regenerate` = 生成物を作り直してコミット）
   * A-2: `regenerate-saikyo.yml` はそのまま残す（手動の「【2】を作り直して再生成」を1回で行える形を保つ）
   * A-3: `fetch-youtube.yml` は YouTube のチャンネル情報と帰り道の情報の取り直しとコミットだけを持ち、最後に `regenerate-page` を `all` で呼ぶ。OGP の作成と帰り道の知らせは `regenerate-page` に残す（#340 の「同じコミット」を保つ）。凍結中の楽天の書影の取得（`if: false`）も `regenerate-page` に残す
   * A-4: `check-meibo.yml` の `name:` はそのまま（Ampai はファイル冒頭のコメントに書く）
   * A-5: 予定表の切り出し先は `sync-yotei.yml`
   * A-6: 束ねる本は `run-daily.yml`。動詞 `run`（ほかのワークフローを束ねて動かす）を規則の 7 つ目に足す
* 段①（共通化の部品）:
   * `SCHEDULE_ENABLED`（6 本）はやめ、予約の起動の有効・無効は Worker の起動の表の `enabled` に任せる
   * 部品への置き換えは、まず 1〜2 本（`check-saikyo-unregistered.yml`・`sync-birthday-calendar.yml`）で試してから広げる
   * `sync-dojo-calendar.yml` の結果ごとの複数コメントは 1 件にまとめてよい（upsert-issue を 1 回で呼ぶ）
   * `.python-version`（`3.12`）を段①で入れ、#539（3.14）はその 1 行の変更にする。#308（Node 24 の版）は部品に入れるときに同時に上げず、別に扱う
* 段③: Worker の起動の表（`workers/scheduler/schedule.json`）の 5 行を `run-daily.yml` の 1 行に置き換えるのは段③で同時に行う（Worker のコードは変えない。#504 の観察の後に回さない）
* `sync-dojo-calendar.yml`・`delete-merged-branches.yml` の保険の `schedule`（毎日 2 回動く）は段④まで置く（外す時機は #504 の観察で決める）
* 親 issue #564 の本文に 10/21・11/30 の日付を写さず「#298 の予定」「#504 の予定」とした件はこのままでよい

前提（チャット側。平野さんの決定ではない）

* WFL-01 のログの `## 報告` は「判断待ち」で、マージは `origin/cloudflare`（9ec82bb79 の時点）の取り込みで `docs/decisions/operations.md` が衝突したため止めている（要確認。着手時の `origin/cloudflare` はさらに進んでいてよい）
* 未マージの work/1014-gds も `docs/decisions/operations.md` の末尾に追記している（WFL-01 の「未確認の項目」）。この指示の取り込みでは gds は未マージのため衝突しない見込み。後にマージする側で解く
* 決定の記録の置き場所: 改名・段①・段③・保険の決定は `docs/decisions/automation.md`、「衝突の解き方」「#564 の本文の日付」はログだけでよい（README の「決まったことだけ」に照らしてチャット側が分けた。実物に合わせて変えてよい）

手順

1. WFL-01 のログ（`docs/logs/CHAT-1014-WFL-01.md`）の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-WFL-02` を足す。`origin/cloudflare` を merge で取り込む。衝突は次のとおり解く: `docs/decisions/operations.md` は両方を残し、OHA-05 の節の後に WFL-01 の節を置く（見出しの日付順の規則に合う並び）。ほかに両方の変更が両立する衝突（追記どうし・隣り合う行）があれば、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
2. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-14（CHAT-1014-WFL-02）` として足す（書き方は docs/decisions/README.md。前の見出しの並びは日付順のまま）。#564 に、決まった名前（`run-daily.yml`・`regenerate-sitemap.yml`・`sync-yotei.yml`・`fetch-youtube.yml`、動詞 `run` の追加）と段①の進め方（試す 2 本・`SCHEDULE_ENABLED` の廃止・`.python-version`）をコメントする（本文は変えない）
3. 衝突の解決・決定の記録・ログをコミットして、cloudflare へマージする（CLAUDE.md「ブランチ運用」の手順。push 直前に再 fetch と祖先の確認）。マージの結果をログに書く

止まる条件

* WFL-01 のログの状態が「判断待ち」でない
* `docs/decisions/operations.md` 以外で、両立しない衝突がある（生成物以外のため解かずに止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-WFL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-WFL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git log --all --grep` と docs/logs）: CHAT-1014-WFL-02 は未使用。識別子 WFL は同じチャットの WFL-01 の続き
- 作業ブランチ: work/1014-wfl はリモート・ローカルとも b1259d7e3 で一致。そのまま使う
- 0. 指示欄の末尾は指示文の最後の行と一致。WFL-01 のログの `## 報告` の状態は「判断待ち」
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順（＋作業ブランチ）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1014-wfl
- ログ: https://github.com/retroeater/mj/blob/work/1014-wfl/docs/logs/CHAT-1014-WFL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-wfl
- 確認用URL: なし
- マージ: 未
- issue: #564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a372bb9f）: https://github.com/retroeater/mj-logs/tree/main/guide/a372bb9f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a372bb9f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
