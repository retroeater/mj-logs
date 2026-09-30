# CHAT-0930-CAL-12

- 着手日時: 2026-09-30（JST）
- 対象issue: #450・#453
- ブランチ: work/0930-cal-450
- 着手時HEAD: 71e77c16

## 指示

【Claude作成】Claude Code 向け指示：#450（過去の放送も「mj_放送対局」に載せる）の下調べをし、/grill-me で平野さんと詰めて決定を issue に残す（実装・書き込みはしない） Chat-Ref: CHAT-0930-CAL-12 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-cal-450 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-cal-450 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
放送済みの過去の放送も公開カレンダー「mj_放送対局」に載せる（#450）。載せ方を決めるための数を先に調べ、そのうえで平野さんと1問ずつ詰めて、実装の指示を書ける状態にする。
決定（2026-09-30、平野さん）

* 情報源の分担: 過去（放送済み）の予定と、YouTube の放送枠ができた後の予定は YouTube のデータから作る。枠がまだ無い未来の予定だけ予定表から作り、枠ができたら YouTube のデータで置き換える（今の同期の考え方のまま）。
* 予定表の【3】の過去の行（2024-12-01〜）には掲載 Y/N を付けない。過去の放送は予定表からは作らない。
* 過去の放送も「mj_放送対局」に載せる。範囲はチャンネルの最初から。
* 載せるかどうか（掲載 Y/N）は /live の掲載とは別に持つ（#453）。完全版のライブ配信を規則で候補にし、例外（お正月特番など）だけ手で N にする。
* 予定表由来と動画由来で、同じ放送を二重に出さない。
* 件数と Calendar API の上限は、調べてから決める。

前提（チャット側。平野さんの決定ではない）

* CHAT-0930-CAL-11（CAL-08 の全期間の取り込みのマージと初回の書き込み、work/0930-cal-full）が同時に動くことがある。この指示は読むだけで、触るブランチもファイルも重ならないので、それを理由に止まらない。
* 今のカレンダーの同期は、今日以降の予定だけを扱う（CAL-08 でも変えていない）。

手順

1. 下調べ（読むだけ）: /live の3層のシートと YouTube のデータ（今の取り込みと同じ経路）から、(a) チャンネルのライブ配信の本数（年ごと）、(b) そのうち今の規則で「完全版」と判定される本数（年ごと。限定版・冒頭版・公開版などの内訳）、(c) 例外の候補になりそうなもの（お正月特番など、規則で完全版になるが対局の放送でないもの）の例を10件ほど、(d) いちばん古いライブ配信の日付、を書く。Calendar API の上限（1日あたり・1分あたりのリクエスト、1カレンダーあたりの予定数の目安など）を公式の資料で確かめ、(b) を初回に作るときの回数と所要時間の見込み、毎朝の差分の見込みを書く。#450・#453 の本文とコメント、同じ論点の issue（クローズ済みを含む）も読んで要点を書く。
2. grill-me: grill-me の skill（grilling）を使い、1問ずつ平野さんに聞いて決める。平野さんが答えないまま進めない。少なくとも次を扱う: 掲載 Y/N を持つ場所（/live の3層のどこか、別のシートか、#453 との関係）、完全版の候補の規則と例外の付け方、予定表由来の予定と動画由来の予定を同じ放送とみなす条件（二重に出さないため）、過去の予定の件名・時刻・説明欄の形（今の未来の予定と同じか）、動画が非公開・削除になったときの扱い、初回の大量の作成の進め方（分割するか・上限に当たったとき）。手順1の数を示してから聞く。
3. 記録: 決まったことを #450 にコメントする。#453 に関わる決定は #453 にもコメントする。起票が要るものがあれば起票し、#448 の sub-issue にする。

止まる条件

* 同じ論点を進めている他セッションの着手中コメントが #450・#453 にある。
* 手順3まで終えたら、判断待ちで止まる（実装の指示を待つ）。

完了条件

* ログの「### 手順3: 記録

- 平野さん「まとめのとおりでよい。#450・#453 にコメントして完了して」を受けて記録した
  - このブランチへの切り替えは、自動モードの権限判定で一度止められた。平野さんが許可して再開した
- #450: https://github.com/retroeater/mj/issues/450#issuecomment-5904859474
- #453: https://github.com/retroeater/mj/issues/453#issuecomment-5904860248
- 起票はしていない。どの決定も #450 の実装の指示で扱える

## 報告

- 状態: 完了（判断待ち: #450 の実装の指示を待つ） → 続き: CHAT-0930-CAL-16
- ブランチ: work/0930-cal-450
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal-450/docs/logs/CHAT-0930-CAL-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal-450
- 確認用URL: なし
- マージ: 済（docs/logs のみ。下のコミットを cloudflare へ）
- issue: #450・#453（コメントした。どちらも open のまま）
- 決定:
  - 情報源: 過去の予定と枠ができた後の予定は YouTube から作る。予定表から作るのは枠の無い未来の予定だけ。範囲は 2015-08-03 から
  - 完全版の枠はすべて規則で載せ、除外（N）だけを手で持つ
    - 置き場所: /live の3層のスプレッドシートの「カレンダーの除外」タブ（動画ID・参考:題名・理由）
  - 最初の除外は4件（テスト放送2・1分未満の断片2）
  - 同じ日・同じ題名・同じ種類の版が2本以上なら、それぞれ別の予定にする
  - 予定表由来と枠由来の同一視は今の条件のまま（`EVENTS` に大会名を足す）。予定表由来の予定は翌日に自動で消す
  - 過去の予定の終了時刻:
    - 層1を取り直して `actualEndTime` を埋める（ライブ 4,091本を1回だけ追記）
    - 返らない枠は開始＋長さで代える
  - 件名・説明欄は今と同じ形にする。非公開・削除になったら過去分も消す
  - 初回の作成は1回で全部。上限に当たったら翌朝に続きから作り、何件目で当たったか・時刻・エラーを記録する
- 判断が必要なこと:
  - #450 の実装の指示（層1の取り直しの手段、`live_calendar.plan()` の範囲の変更、除外タブの読み込み）
  - 除外タブは平野さんが作る
- 未確認の項目: Calendar API の上限の公式の値（公式の資料がセッションのプロキシで読めない）
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
