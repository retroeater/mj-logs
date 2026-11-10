# CHAT-1110-ACF-44

- 着手日時: 2026-11-10
- 対象issue: #352・#298
- ブランチ: work/1110-acf
- 着手時HEAD: 449e771dfe

## 指示

【Claude作成】Claude Code 向け指示：画像リンク切れの検知（check-image-links）を run-weekly から run-daily の SNS ブック更新の後へ戻す（#352 の誤検知の解消） Chat-Ref: CHAT-1110-ACF-44 マージ: 承認済み（チャットで。条件: 手順3の作業ブランチでの手動実行〈書かない道筋〉がすべて success で、run-daily に images のジョブが出て run-weekly に出ないとき。今回の変更による失敗が直らなければマージせず報告して止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: ワークフローの変更は docs/notes/github-actions.md「ワークフローを変更したとき」「チャット側」を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
画像リンク切れの検知を、週次（月曜 03:00 JST の run-weekly）から毎朝の run-daily の SNS ブック更新（`sns`）の後へ戻す。いまは更新の前の古い値を検知して誤って #352 を開き直し、直った後も最大7日開いたままになるため。
決定（2026-11-10、平野さん）

* `check-image-links.yml` を `run-weekly.yml` から外し、`run-daily.yml` で `sns`（update-sns-book）の後に毎日呼ぶ。CHAT-1105-MIN-01 の「週次へ移す」決定を戻す
* 使用量の増え（下の前提の見込み、月 約 250〜260 分・約 $2）は受け入れる。予算 $20 はそのまま。11/30 の実測（カレンダー「ryoei.pro」に登録済み）はこの増えを含めて判断する
* work/ ブランチの Actions の失敗通知メールは、平野さんが Gmail のフィルタで振り分ける（作成済み。リポジトリ側の変更は無し。docs に書く必要は無い）

前提（チャット側。平野さんの決定ではない）

* #352 が 11/9 03:03 JST に開き直されたのは、jpml_pros.html の石川正明さんの X 画像（`_80x80`）1件の 404。1時間後の 11/9 04:11 JST の run-daily の SNS ブック更新 → 全ページの再生成（commit 69469d94）でその URL はページから消えている（チャット側で cloudflare の jpml_pros.html の履歴を見て確認）。11/5 まで検知は `sns` の後にあったので、この順番の逆転は MIN-01 の移動で生じた
* 見込み（チャット側の実測。job ごとに分を切り上げ）: images の3ジョブは 11/5 の run-daily（41892691939）で check 199秒・saikyo 52秒・sns_book 297秒、11/9 の run-weekly（42507761449）で 196・59・292 秒。1回 約10分 → 毎日で月 約300分、週次の今は月 約40〜50分、差は月 約250〜260分
* 並びの案: `images` は `needs: sns`（`!cancelled()` で、sns が失敗しても動く。11/5 までと同じ）にする。`saikyo` を images の後に戻す（11/5 までの直列）か、`sns` の後で images と並行にするかは、実物の依存（saikyo の再生成と images の検知が同じ値や同じブランチへの書き込みを共有するか）を見て決めてよい。決めた理由をログに書く（要確認）
* 入力 `scheduled` の渡し方・手動実行の書かない道筋（dry-run）は、11/5 まで run-daily にあった形（MIN-01 の前の `run-daily.yml`。`git log -p` で取れる）と、いまの run-weekly の `images` の形に合わせる
* 書き直す文書の候補: docs/notes/github-actions.md（表の `check-image-links.yml`・`run-daily.yml`・`run-weekly.yml` の行、「run-daily.yml」の並び、戻し方の手順〈84行目付近の「04:30 の行を戻すなら run-weekly の `images` を外す」〉）、docs/notes/sns-book.md（「週次の検知」の段落）、docs/decisions/automation.md（この決定を足す）、#352 の本文の冒頭の「毎週月曜の」（本文は check-image-links のスクリプトが書く文面なので、スクリプト側の文字列を直す）。ほかの記述は `git grep -n -e 'check-image-links' -e '画像リンク切れ' -e 'run-weekly'` で洗う（docs/decisions・docs/logs・handover-archive の過去の行は除く。CLAUDE.md・docs/handover.md は含める）

手順

1. 上の前提（#352 の開き直しの原因、run-weekly の `images` の今の形、MIN-01 の前の run-daily の形）を実物で確かめ、食い違えば止まる。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で `run-daily.yml`・`run-weekly.yml`・`check-image-links.yml` の同じ行を変えているもの・取り込みで衝突するものを確かめる。#298・#352 に着手中のコメントを書く（本文・状態・ラベルは変えない）
2. `run-weekly.yml` から `images` を外し、`run-daily.yml` に `sns` の後の `images` を足す。`check-image-links.yml` 側の「週次」前提の文字列（issue 本文の文面、コメント、入力の説明）と、上の文書を直す。docs/decisions/automation.md に決定を足す。docs/notes/github-actions.md の「ワークフローを変更したとき」の手順（運行表〈mj-logs の actions/index.html〉の起こし直しの扱いを含む）に従う
3. 確かめ: テスト（scripts/tests 一式）と、作業ブランチで run-daily・run-weekly を手動実行（書かない道筋）し、どちらも success で、run-daily に images の3ジョブが `sns` の後に出て、run-weekly に images が出ないことを確かめる（ジョブ名・所要時間・結論の表をログに書く）。今回の変更による失敗は直して再実行する。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429〈#662〉で `SheetsReadError` になって失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージし、#298 に「検知を毎日に戻した・見込みの増え（月 約250〜260分）」を、#352 に「検知を run-daily の SNS ブック更新の後へ戻した理由（11/9 の誤検知）」をコメントする。どちらも閉じない（#352 は常設、#298 は 11/30 の実測が残る）。報告の「未確認の項目」に、マージ後の初回の run-daily（2026-11-11 04:00 JST）で images が動き、切れが無ければ #352 が自動で閉じることを書く

止まる条件

* 前提が実物と食い違う（#352 の開き直しの原因が別、run-weekly に `images` が無い等）
* #298・#352 に他セッションの着手中コメントがある、または同じ論点（検知の頻度・置き場所）の open issue がほかにある
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `run-daily.yml`・`run-weekly.yml`・`check-image-links.yml` の同じ行・同じジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で、今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-ACF-44.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-ACF-44 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bee60139）: https://github.com/retroeater/mj-logs/tree/main/guide/bee60139

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bee60139/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/449e771d.md
