# CHAT-1015-WFL-14

- 着手日時: 2026-10-15
- 対象issue: #564・#263
- ブランチ: work/1015-wfl-s5
- 着手時HEAD: 664643716

## 指示

【Claude作成】Claude Code 向け指示：段③の調べと設計（毎朝のワークフローを run-daily.yml にまとめる）・#263 のクローズ Chat-Ref: CHAT-1015-WFL-14 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。`.github/`・`workers/`・`scripts/` は変えない 貼る時機: いつでも（CHAT-1014-WFL-13 は完了済み）。ただし JST 03:45〜05:15（毎朝のワークフローの起動の間）はマージの push をしない 作業ブランチ: クラウドセッションで実行する。work/1015-wfl-s5 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-wfl-s5 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。着手時に docs/notes/github-actions.md「ワークフローを変更したとき」を読む（この指示では `.github/` を変えないが、設計の前提にする）。

目的
#564 の段③の 1 本目。毎朝のワークフローを 1 本の `run-daily.yml` にまとめる設計を、実物を調べて決める。実装は次の指示で行う。この指示ではコードを変えない。あわせて #263 を閉じる。
決定（2026-10-14〜15、平野さん）

* 毎朝の運行は 1 本のワークフロー `run-daily.yml` にまとめる。先に済ませる本は `needs:` で書く。各本は `workflow_call` で呼べるようにし、手動実行（`workflow_dispatch`）も残す（2026-10-14）
* Worker の取りこぼしの埋め合わせは ACF 系列（592334b3）で実装済み。WFL では同じものを作らず、Worker のコードを変えない（2026-10-14）
* ワークフローの改名は `<動詞>-<対象>.yml` に揃える。ファイル名だけでなく `name:`・run-name・入力の description・冒頭コメント・docs も同じ規則で揃える（2026-10-14）
* #263（regenerate-page の concurrency）は今閉じる。本番では 10/15 朝の 3 回の呼び出しが重ならず、待ちは起きなかった。待ちの動きは WFL-10 の作業ブランチで確かめ済み。段③で毎朝の本を `needs:` でつなぐと重なり自体がほぼ起きなくなるため、本番の重なりを待たない（2026-10-15）

前提（チャット側。平野さんの決定ではない）

* 今の毎朝の起動（`workers/scheduler/schedule.json`、2026-10-15 のチャット側の読み。要確認）: update-live-channel 04:00、update-sns-book 04:10、sync-dojo-calendar 04:15、delete-merged-branches 04:20、check-image-links 04:30。regenerate-saikyo は check-image-links の完了を `workflow_run`（`name:`「画像リンク切れの検知」で参照）で起動する
* 保険の `schedule`（GitHub 側）を持つのは update-live-channel・sync-dojo-calendar・delete-merged-branches・check-image-links（要確認）。update-live-channel には「当日の `[scheduled]` の成功があれば保険は何もしない」ゲートがある。まとめた後は、このゲートが run-daily の実行を見ないと保険が二重に動く。保険の `schedule` を外すのは段④（#504 の観察の後）なので、段③ではゲートの見る先を直す
* Worker の朝の確かめ（06:00、#506）は、本ごとの `[scheduled]` の題の実行を探している見込み（要確認。どの値で探すか〈ファイル名・`name:`・題〉を `workers/scheduler/` で調べる）。schedule.json の 5 行を `run-daily.yml` の 1 行にしても、Worker のコードを変えずに確かめが通るか（通らなければ何が要るか）が設計の要
* 先に済ませる順の案（チャット側の案。実物で検める）: update-live-channel → update-sns-book → check-image-links → regenerate-saikyo。sync-dojo-calendar・delete-merged-branches は他と関係しないので並行。前の本が失敗したときに後の本を動かすか（`if: ${{ !cancelled() }}` など）は本ごとに決める
* 呼び出しの入れ子: run-daily → update-live-channel・update-sns-book・regenerate-saikyo → regenerate-page で 2 段になる。GitHub の入れ子の上限、`permissions` は呼ぶ側が上限になること、`secrets: inherit` の要否、呼ばれる側のワークフロー全体の `concurrency` が呼ぶ側とどう効くか（同じ組の名前で詰まらないか）を確かめる
* 入力 `scheduled` と resolve-mode（`.github/actions/resolve-mode`）: 呼ばれたときに `scheduled` を run-daily から渡す形にする。各本の run-name の `[scheduled]` の目印は、呼ばれた本では題にならない（呼ばれた本の実行は run-daily の中のジョブになる）。mj-logs の morning.md・status.md（`scripts/morning_report.py`・`scripts/actions_status.py`）と Worker の確かめに効く
* update-live-channel の中の yotei（予定表のカレンダー）を `sync-yotei.yml` に、YouTube の取り込みを `fetch-youtube.yml` に分ける案（2026-10-14 のチャット側の案）。分けたときに先行関係（`needs:`）がどうなるかを含め、分けるか・まとめたままにするかを判断材料と推奨つきで出す
* 2026-10-15 の朝に update-sns-book が飛ばしたページ video_mtsuku の失敗（`NO_COLUMN: O`）は MTK の担当（#569）。この指示では扱わない
* 未マージの work/ ブランチで、毎朝の本・`workers/scheduler/`・`scripts/morning_report.py`・`scripts/actions_status.py` を変えているものがあれば、設計の前提として書く（ACF・OHA 系列の作業が近い）

手順

1. 「前提」の要確認をすべて実物で確かめ、表にしてログに書く（各本: 起動・保険の `schedule`・ゲート・`concurrency`・`permissions`・入力・呼んでいる本・呼ばれている所・出力〈issue・コミット・mj-logs〉）
2. 設計をログに書く。少なくとも次を含める:
   * `run-daily.yml` のジョブの並びと `needs:`・`if:`（前の本が失敗したときの扱いを本ごとに）
   * 各本を `workflow_call` にする差分の形（入力・出力・`secrets`・`permissions`・`concurrency`）
   * schedule.json の行をどうするか、Worker の朝の確かめと取りこぼしの埋め合わせ（ACF-18）が Worker のコードを変えずに通るか
   * update-live-channel の保険のゲートの見る先、regenerate-saikyo の `workflow_run` を外す手順
   * mj-logs の morning.md・status.md への影響と直す箇所（本ごとの節が run-daily のジョブに変わること）
   * `sync-yotei.yml`・`fetch-youtube.yml` に分けるか
   * 改名の表（今のファイル名 → 新しい名前、`name:`・run-name も）と、改名を段③のどの指示で行うか。`name:` を参照する所（`workflow_run`・Worker・mj-logs・テスト・docs）の洗い出し
   * 実装を何本の指示に分けるか、その順と、それぞれの確かめ方（作業ブランチの手動実行で書く道筋をどこまで通せるか）
3. 平野さんに決めてもらう点を、判断材料・推奨・その理由をつけて `## 報告` の「判断が必要なこと」に書く（選択肢を出すときは比較と推奨を必ず付ける）
4. #263 に「段②（44920ebbc）で入れた concurrency で閉じる。本番で 10/15 朝に重なりは無く、待ちの動きは WFL-10 の作業ブランチで確かめ済み（#564）」とコメントし、「状況:」ラベルを外してクローズする
5. 上の「決定」を `docs/decisions/automation.md` に `## 2026-10-15（CHAT-1015-WFL-14）` として足す。#564 に設計の要点とログの場所をコメントする（本文は変えない）。完了報告のうえ cloudflare へマージする（push 直前に再 fetch と祖先の確認。JST 03:45〜05:15 は push しない）

止まる条件

* 調べの途中で、`run-daily.yml` にまとめると Worker のコードを変えないと朝の確かめ・取りこぼしの埋め合わせが成り立たないと分かった（設計の残りは書き、選択肢と推奨を判断待ちに入れて止まる。マージはしてよい）
* #263 の状態が open でない、または他セッションの着手中コメントがある（#263 は触らずに進め、報告に書く）
* 取り込みで両立しない衝突がある（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-WFL-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-WFL-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66464371）: https://github.com/retroeater/mj-logs/tree/main/guide/66464371

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66464371/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
