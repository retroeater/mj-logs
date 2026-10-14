# CHAT-1014-OHA-03

- 着手日時: 2026-10-14
- 対象issue: #498・#298（起票予定の issue）
- ブランチ: work/1014-oha
- 着手時HEAD: 191d9dbde

## 指示

【Claude作成】Claude Code 向け指示：mj-logs に朝の報告用 `actions/morning.md`（直近26時間の異常だけ）を書き出し、朝の報告の自動実行を検討する issue を起票する Chat-Ref: CHAT-1014-OHA-03 マージ: 判断待ちで止まる（手元で書き出した morning.md をログに貼って止まる。mj の cloudflare へのマージと mj-logs の main への push は、平野さんが見た後の次の指示） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-oha の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。mj 側（作業ログだけ）は work/1014-oha を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-oha origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。mj-logs はこの指示では読むだけ（`add_repo` は read）。push が要る変更（ワークフロー）は差分の全文をログに貼り、次の指示で `add_repo`（push）→ main へ直接 push する（CHAT-1005-RVW-22 と同じ）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが毎朝チャットに「おはよう」と言うと、チャット側（Sonnet 5.5）が定型の報告を出す。ジョブの異常の判定を毎朝チャットに読ませると漏れが出るので、`actions/status.md` と同じ経路で、異常だけを抜いた `actions/morning.md` を mj-logs 側で書き出す。チャット側は morning.md だけを読む。 CHAT-1014-OHA-01 は送る前に差し替えたため欠番。
決定（2026-10-14、平野さん）

* 置き場所は mj-logs の `actions/morning.md`。`actions/status.md` はそのまま残す（変えない）
* 書き出すのは次の5節だけ。該当が無い節は「なし」の1行
   1. 失敗など: 直近26時間に開始した実行で、結論が success・skipped 以外（failure・cancelled・timed_out・実行中〈結論が空〉）
   2. 予約実行の未実行: 予約（Worker の表の起動と `on.schedule`）のあるワークフローで、直近の予定時刻から書き出し時刻までに、契機 schedule または workflow_dispatch の実行が無いもの。予定時刻から3時間以内なら「遅れの可能性」と別に書く（予約実行は2〜3時間遅れることがある、#504）
   3. 所要時間の異常: 直近26時間の実行で、同じワークフローの直近20回（26時間の外も含む）の中央値の3倍以上、または10分以上
   4. 初回成功: 直近26時間の success で、そのワークフローのそれ以前の実行（API で取れる範囲、最大100件）に success が無いもの
   5. 未報告のログ: mj-logs の `logs/` のうち `## 報告` の節が無いもの（ファイル名・着手日時）
* 1行に1件。各行に実行のページのリンク（`https://github.com/retroeater/mj/actions/runs/<ID>`）か、ログのリンクを付ける
* 冒頭に、書き出した時刻（JST）と、見た範囲（26時間の始点）を書く

前提（チャット側。平野さんの決定ではない）

* status.md は mj の `scripts/actions_status.py`（`--repo`）が書き、mj-logs の `sync-from-mj.yml` がそれを呼ぶ（CHAT-1005-RVW-20〜22 のログ。要確認）。morning.md も同じスクリプト（または隣の新しいスクリプト）で書き、同じ API の結果を使い回す。mj 側のスクリプトと `sync-from-mj.yml` の呼び出し1行で済む見込み
* 目的欄の経緯: 2026-10-14 のチャットで、毎朝の報告は固定のチャット（Sonnet 5.5）で手動の「おはよう」から始める形に決めた。自動実行（スケジュールされたタスク。毎回新しいセッションで動き、使用量は通常どおり数えられる）は後で検討する
* 予約の一覧は、mj の各ワークフローの `on.schedule`（毎週のものは曜日を見る）と、Worker `mj-scheduler` の `scheduler.mjs` の表（docs/notes/scheduler-worker.md「今の表」）から作る。Worker の表は mj-logs から直接読めないので、`sync-from-mj.yml` が mj の `scheduler.mjs`（置き場所は要確認）を REST で読むか、予約の一覧を mj-logs 側の定数に写す。どちらにするかは実装側の判断でよく、定数に写すなら「Worker の表を変えたら mj-logs の定数も直す」を docs/notes/scheduler-worker.md に1行足す
* 同じワークフローが同じ日に schedule と workflow_dispatch の両方で動くのは #504 の移行中の既知の形。2 の判定では「どちらかがあれば実行あり」とし、二重実行は書かない
* status.md の冒頭「書き出した実行の契機」と同じ形で morning.md にも書く
* 文書: docs/notes/cloud-sessions.md「作業ログ」か docs/notes/chat-side-operations.md「読み方」の表の「Actions の実行結果」の行に、morning.md の1句を足す（chat-side-operations.md は警告域の直前〈26,556 バイト、警告域 26,624〉なので、同じ行の中で置き換えて増やさない。増えるなら cloud-sessions.md にだけ書く）

手順

1. #498・#298 の本文とコメント、`sync-from-mj.yml` と status.md を書くスクリプトを読み、書き出しの経路と API の呼び方を確かめる。「同じ論点の open issue」（朝の報告・morning.md・自動実行）を検索し、あれば止まって報告する。無ければ mj に issue「朝の報告（おはよう）の自動実行を検討する」を起票する（ラベルは既存の3系統から実物に合わせて選ぶ。本文: 今は固定のチャットで手動の「おはよう」〈Sonnet 5.5〉。検討する点は (1) スケジュールされたタスクは毎回新しいセッションで動き、前日の会話を引き継がない (2) 使用量は通常のセッションと同じに数えられ、5時間の枠の開始の扱いは公式文書に無い (3) 実行の時刻は毎朝の取り込み〈04:00〜04:30 JST の Worker の起動〉と morning.md の書き出しの後。情報源は mj-logs の `actions/morning.md` とカレンダー「ryoei.pro」。着手の時期は未定）
2. morning.md の書き出しを実装し、作業ブランチでスクリプトを手元で実行して（mj-logs のワークフローは動かさない）1回書き出す。出た morning.md の全文と、`sync-from-mj.yml` に足す差分をログの「経過」に貼る。status.md の出力が変わっていないこと（差分0。「書き出した時刻」を除く）も確かめる
3. 文書の1句を足し（前提のとおり）、ログの `## 報告` に「morning.md の各節の判定の式（何を何と比べたか）」と「予約の一覧の作り方（REST か定数か）」を書いて止まる（マージは平野さんの判断）

止まる条件

* status.md を書く経路が前提と違い、morning.md を同じ経路で書けない（経路をログに書いて止まる）
* 手元の実行で status.md の出力が変わる（今の仕組みを壊さないため。戻して止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが status.md を書くスクリプトの同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 作業ブランチへの push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-OHA-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-OHA-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow` のうえ、`OHA` のコミット・`docs/logs/` の履歴ともに0件。`OHA-01` は欠番（指示文）、`OHA-02` も全ブランチに無い（このセッションの最初の指示は 03）
- 作業ブランチ: ローカル・リモートとも `work/1014-oha` が無いため `git checkout -b work/1014-oha origin/cloudflare`
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも揃っている
- 0. 指示欄の末尾は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 916667a9）: https://github.com/retroeater/mj-logs/tree/main/guide/916667a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
