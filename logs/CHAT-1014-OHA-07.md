# CHAT-1014-OHA-07

- 着手日時: 2026-10-14
- 対象issue: #564（関連）
- ブランチ: work/1014-oha-call
- 着手時HEAD: 7a3dac288

## 指示

【Claude作成】Claude Code 向け指示：morning_report.py が workflow_call で呼ばれるワークフロー（個別の run を持たない）をどう扱うかを確かめる（調査のみ） Chat-Ref: CHAT-1014-OHA-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（OHA-06 と同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-oha-call の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-oha-call を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-oha-call origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#564 の段③（WFL）で、毎朝の5本を `run-daily.yml` に束ね、各本は `workflow_call` で呼ばれる形になる見込み（WFL の伝言、2026-10-14）。呼ばれた本は個別の run を持たず、実行は `run-daily.yml` の run に含まれる。`scripts/morning_report.py`（と `actions_status.py`）がその形をどう扱うかを、コードを読んで確かめる。WFL が段③の指示を書く前の材料にする。コードは変えない。
決定（2026-10-14、平野さん）

* 調査のみ。`scripts/`・ワークフロー・文書は変えない（ログだけ）

前提（チャット側。平野さんの決定ではない。実物で確かめる）

* チャット側の見込み: (a) 1・3・4節は「各ワークフローの実行（API の新しい順に最大100件）」を見るので、`workflow_call` だけの本は実行が0件になり、何も出ない。(b) 2節の予定は `on.schedule` と `schedule.json` から作るので、呼ばれた本に `on.schedule`（保険）が残っている間はその予定で判定され、保険の実行が無ければ「未実行」と出る。(c) `run-daily.yml` は `schedule.json` の1行として予定を持ち、自分の run で判定される。(d) 新しいワークフローは直近20回が揃うまで所要時間の中央値が少ない件数で計算される。(e) 4節は `run-daily.yml` の最初の success で1回「初回成功」が出る
* WFL の段③の設計: 段④まで呼ばれた5本は `on.schedule` の保険を持つ。束ねた中の失敗は1節の「失敗したジョブ・ステップ」で読む（WFL の伝言）

手順

1. `scripts/morning_report.py`・`scripts/actions_status.py`・`scripts/tests/test_morning_report.py` を読み、次を表でログの「経過」に書く: ワークフローの一覧の取り方（`state` の扱い）／各節が見る実行の集合（契機・ブランチ・件数）／2節の予定の出どころと、`on.schedule` が無く `schedule.json` にも無いワークフローの扱い／`workflow_call` だけのワークフローで各節がどうなるか（実行0件のときの中央値・初回成功の扱いを含む）／`run-daily.yml` のような新しいワークフロー（実行が20件未満）の3・4節の扱い
2. 1節の「失敗したジョブ・ステップ」の取り方（jobs API のどの項目か）と、束ねた run の中で呼ばれた本のジョブ名・ステップ名がどう見えるか（`workflow_call` の呼び出しではジョブ名に呼び出し元のジョブ名が付く）を、GitHub の API の実物（mj の既存の `workflow_call` の例があればその run、無ければ公式文書）で確かめて書く
3. `## 報告` に、前提の (a)〜(e) それぞれの当否と、段③の指示で WFL が決めるべき項目（morning.md 側で変更が要るなら何か）を箇条書きで書く。`actions_status.py` の status.md が呼ばれた本をどう出すか（実行0件の表になるか）も1行

止まる条件

* 同じ論点の open issue がある（#564・#563 以外で morning.md の判定を変える issue）
* （未マージの work/ ブランチとの重なり）ログだけなので止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-OHA-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-OHA-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。雛形の4行は揃っている。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: ローカル・リモートとも無いため `git checkout -b work/1014-oha-call origin/cloudflare`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7a3dac28）: https://github.com/retroeater/mj-logs/tree/main/guide/7a3dac28

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7a3dac28/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
