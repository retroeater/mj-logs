# CHAT-0930-CAL-16

- 着手日時: 2026-09-30（JST）
- 対象issue: #450・#453
- ブランチ: work/0930-cal-450
- 着手時HEAD: 08174106（origin/cloudflare。ローカルの work/0930-cal-450〈29cc15ac、cloudflare の祖先〉を `git merge --ff-only origin/cloudflare` で進めた）

## 指示

【Claude作成】Claude Code 向け指示：#450（過去の放送も「mj_放送対局」に載せる）を CAL-12 の決定どおりに実装し、書き込みなしの見込みまで出す（マージ・書き込みはしない） Chat-Ref: CHAT-0930-CAL-16 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push、書き込みなしのワークフローの手動実行を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal-450 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-cal-450 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 未承認（平野さんが見込みを見て決める。判断待ちで止まる）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-12 のログの `## 報告` が「完了（判断待ち）」であることを確かめ、状態の後ろに「→ 続き: CHAT-0930-CAL-16」と足す。CHAT-0930-CAL-15（シートの行数を足す修正）が cloudflare に入っていなければ止まる（層1の取り直しで /live の3層のシートの行数が増えるため）。

目的
CAL-12 の grill で決まった形で、放送済みの完全版のライブ配信を「mj_放送対局」に載せる実装をする。初回は約2,500件を作るので、マージと初回の書き込みは、平野さんが見込みを見てから別の指示で行う。
決定（2026-09-30、平野さん）

* CAL-12 のログの `## 報告`「決定」と、#450・#453 へのコメント（CAL-12）のとおり。ここには写さない。食い違えば止まる。
* 大会の一覧（`yotei.EVENTS`）には、CAL-12 で挙がった大会名（地方プロリーグ・麻雀格闘倶楽部など）を足す。ただし JPMLリーグは足さない（正式な大会名が決まってから足す。備忘は CHAT-0930-CAL-09 で起票する）。
* #453 は、#450 の実装が cloudflare に入ったら閉じる（この指示では閉じない）。

前提（チャット側。平野さんの決定ではない）

* 「カレンダーの除外」タブは平野さんが作る。この指示の時点では無いかもしれない。無ければ除外なしとして扱う（CAL-12 の決定）。
* Calendar API の上限の公式の値は CAL-12 で読めなかった。上限に当たったら止まり、翌朝に続きから作る作り（CAL-12 の決定）で吸収する。
* 層1の取り直し（ライブ 4,091本分を1回だけ追記、約4MB）は /live の3層のシートに書く。書き込みはこの指示ではしない。

手順

1. 確かめ: CAL-12 のログの決定と #450・#453 のコメントを読み、食い違いが無いことを書く。同じファイル（`lib/live_calendar.py`・`sync_live_calendar.py`・`lib/yotei.py`・層1の取り込み・`update-live-channel.yml`）を触る未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）が無いことを確かめる。/live の3層のスプレッドシートの今のセル数と、層1の取り直しの後のセル数の見込みを、Google スプレッドシートの上限（1ファイル 1,000万セル）と比べて書く。
2. 実装: 決定どおりに、(a) カレンダーに載せる範囲を過去（チャンネルの最初、2015-08-03）まで広げ、完全版のライブ配信の枠を規則で候補にする、(b) 「カレンダーの除外」タブ（動画ID・参考:題名・理由）を見出しの名前で読み、そこにある動画を載せない（タブが無ければ除外なし）、(c) 同じ日・同じ題名・同じ種類の版が2本以上なら別々の予定にする、(d) 予定表由来の仮の予定は日付が過ぎたら翌朝消す、(e) 大会の一覧に上の大会名を足す（JPMLリーグは除く）、(f) 非公開・削除（取得できない）の動画の予定は過去分も消す、(g) 件名・説明欄は今日以降の枠の予定と同じ形、(h) 終了時刻は層1の `actualEndTime`、返らない枠は開始＋長さ、(i) 初回の大量の作成は1回の実行で全部作り、上限などで止まったら何件目で・何時に・どんなエラーで止まったかをジョブの出力と #450 のコメントに残し、翌朝の実行が続きから作る、を入れる。層1の取り直しは、1回だけ動かす別の入口（スクリプトかワークフローの入力）にし、既存の行を変えずに追記だけすることを確かめる作りにする。定数・関数を変えるときは使う所をすべて挙げる。テストを足して全件通す。docs/notes/yotei-sheet.md（と /live の資料）を直す。
3. 見込み（書き込まない）: 今のシートの値で、(a) 候補の完全版の本数（年ごと）と、除外・同日同題名の扱いの後の載せる件数、(b) 今のカレンダー（138件前後）に対する作る／直す／消すの件数と、消すものの一覧（あれば）、(c) 予定表由来の仮の予定のうち、枠由来と同じ放送とみなされて出なくなるもの・二重に残るものの件数と例、(d) 層1の取り直しで追記する行数、(e) 初回の作成に要る API の回数と時間の見込み、を書く。作業ブランチで `update-live-channel.yml` を apply・yotei_apply・calendar_apply をすべて外して起動し、同じ見込みになることを確かめる。「カレンダーの除外」タブに入れる最初の4件（動画ID・参考:題名・理由）を、平野さんがそのまま写せる表の形でログに書く。

止まる条件

* CAL-12 の決定と #450・#453 のコメントが食い違う。
* CAL-15 が cloudflare に入っていない。同じファイルを触る未マージのブランチがある。
* 手順1で、層1の取り直しの後のセル数が上限の半分を超える。
* 手順3の見込みで、今のカレンダーの予定を消すものが、非公開・削除と予定表由来の過去の仮の予定のほかにあり、理由が説明できない。
* 手順3まで終えたら、判断待ちで止まる。cloudflare へはマージしない。シートにもカレンダーにも書かない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、見込みの要点、マージと初回の作成・層1の取り直しの順番（毎朝の実行の前後、どれを先にするか）、平野さんの作業（除外タブを作る時機）を書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-16.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-16` は0件。origin/work/0930-cal-450 は origin/cloudflare の祖先（マージ済み）。ローカルの work/0930-cal-450 も cloudflare の祖先なので、`git checkout work/0930-cal-450` のうえ `git merge --ff-only origin/cloudflare`（08174106）
- 手順0: 指示欄の末尾は指示文の最後の行と一致。CAL-12 の `## 報告` は「完了（判断待ち: #450 の実装の指示を待つ）」だったので、後ろに「→ 続き: CHAT-0930-CAL-16」を足した（このコミットに含める）。CAL-15 は cloudflare に入っている（69c62f6f `fix: add grid rows before writing past the sheet size` ほか）

## 報告

- 状態: 作業中
- ブランチ: work/0930-cal-450
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-450/docs/logs/CHAT-0930-CAL-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-450
- 確認用URL: なし
- マージ: 未（未承認）
- issue: #450・#453
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9a820f24）: https://github.com/retroeater/mj-logs/tree/main/guide/9a820f24

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9a820f24/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
