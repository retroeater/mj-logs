# CHAT-1016-JPL-10

- 着手日時: 2026-10-16
- 対象issue: #523
- ブランチ: work/1016-jpl
- 着手時HEAD: dbb58b89d

## 指示

【Claude作成】Claude Code 向け指示：JPL-09 の続き。今朝の反映（予定表【2】・/live の【2】）を確かめて #523 をクローズし、JPL-09 を片付ける（記録のみ）
Chat-Ref: CHAT-1016-JPL-10
マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。それ以外を変える必要が出たら、変えずに判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-jpl の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1016-jpl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-jpl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1015-JPL-09.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-09 の `## 報告` の状態の末尾に ` / 続き: CHAT-1016-JPL-10` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

## 目的
JPL-09 でマージした WRC 2大会の新名称の全面反映について、今朝の毎朝の実行での書き換えを確かめ、#523 をクローズする。

### 決定（2026-10-15、平野さん）
- #523 は、翌朝（2026-10-16）の反映を確かめてからクローズする（JPL-09 の案 (i)）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が 2026-10-16 07:40 頃に確かめたこと: mj-logs の actions/status.md（06:46 JST の書き出し）で、今朝の毎朝の実行は run-daily.yml #3（04:00 JST、workflow_dispatch、cloudflare、success、14分24秒）。Google カレンダーの連携で読んだ「mj_放送対局」の WRC 2大会の仮の予定 8件（2026-12-03〜2027-02-07）は、件名がすべて新名（「第8期WRC-Rリーグベスト16AB卓」…「第20期WRCリーグ決勝」）になり、最終更新は 2026-10-15T19:04Z
- 見積もり（JPL-08 の報告）: 予定表【2】257行・/live の【2】402行の大会名が新名になる。チャット側では読めていない
- 毎朝の実行が update-live-channel.yml から run-daily.yml に移っている（WFL の系列の変更とみている。要確認）。docs の記述で update-live-channel.yml を「毎朝の実行」としている箇所があっても、この指示では直さない（気付いたらログに書く）

## 手順
1. 確かめる: 予定表シート【2】と /live の【2】を生成と同じ経路で読み、「大会」（など大会名の列）に旧名「JPML WRCリーグ」「JPML WRC-Rリーグ」が残っていないこと、新名の行数（見積もり 257行・402行と比べ、違えば数と理由の見込み）を書く。常設 issue「予定表の変化」（#481）に今朝の知らせが出たか（出たなら引用）を書く。run-daily.yml #3 のログで、予定表・/live の取り込みとカレンダー同期のステップが成功したことを確かめる
2. 旧名が残っていた・数が大きく外れた（±10行を超える）ときは、#523 をクローズせず、原因の見込みと案を「判断が必要なこと」に書いて止まる。問題なければ #523 に結果をコメントしてクローズする（「状況:」ラベルがあれば外す。末尾に Chat-Ref の行）
3. docs/decisions/title.md に上の決定と結果を1行足す。JPL-09 の `## 報告` の状態を「完了」にする（判断が必要なことはこの指示で扱った旨）。「マージ:」の行のとおり cloudflare へ入れ、作業ブランチを片付ける

## 止まる条件
- 0章で JPL-09 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
- 手順2の条件に当たった
- シートが読めない（権限・通信）。読めない理由を書き、#523 はクローズしない
- docs/ 以外を変える必要が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JPL-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JPL-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1016-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1016-jpl/docs/logs/CHAT-1016-JPL-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-jpl
- 確認用URL:
- マージ:
- issue: #523
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8e48fa5f）: https://github.com/retroeater/mj-logs/tree/main/guide/8e48fa5f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8e48fa5f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dbb58b89.md
