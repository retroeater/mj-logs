# CHAT-1020-MRA-01

- 着手日時: 2026-10-20
- 対象issue: #538
- ブランチ: work/1020-mra
- 着手時HEAD: 9a00b59e5

## 指示

【Claude作成】Claude Code 向け指示：10/20 の朝の報告からのフォロー（#538 Ubuntu 26.04 のランナーで動いた回が success か）を確かめ、issue にコメントする（報告のみ） Chat-Ref: CHAT-1020-MRA-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示） 貼る時機: 2026-10-20 04:00 JST の run-daily.yml（run 39307312990）が完了した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-mra の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1020-mra を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-mra origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。run 39307312990 が完了していなければ何もせず止まる。#538 に「着手中」のコメントを書く（本文・状態・ラベルは変えない）。他セッションの未解除の着手中コメントがあれば止まる。

目的
朝のチャットでは run のログを読めないため、#538 の残り（GitHub のランナーの Ubuntu 26.04 への移行〈2026-10-19〜〉の後、26.04 で動いた回が success か）を確かめて #538 に書く。コードも文書も変えない。
前提（チャット側。平野さんの決定ではない）

* #538 の残りは「移行の後に Ubuntu 26.04 で動いた回が success か」だけ（python3 の 3.12 固定は CHAT-1010-RGN-05 で済み）。カレンダーの予定「【R#538】Ubuntu 26.04 で動いた回が success か確認」の説明欄による（要確認）
* 渡し先の #538 の題・状態はチャットから読めていない（題は未確認）。閉じている、または題が上の内容と無関係なら、新規起票に切り替える（ラベルは付けない）
* 変更の範囲は docs/logs と docs/decisions/ のみ

手順

1. #538 の直近7日のコメントに同じ趣旨（26.04 のランナーでの success の確かめ）のものがあれば、足さずに止まる。#538 の題・状態を読み、上の前提と食い違えば新規起票に切り替える
2. 2026-10-19 以降の各ワークフローの run（run 39307312990 を含む。status.md に載る run を中心に）を `gh`（クラウドは GitHub MCP）で読み、ジョブごとのランナーの版（「Operating System」の行など）と結論を表にしてログの `## 経過` に書く（run ID・ジョブ名・該当行の引用を添える。全件書き、略さない）
3. #538 にコメントする。文面は「【朝の報告からのフォロー（CHAT-1020-MRA-01、2026-10-20）】」＋事実（26.04 で動いた run の ID と結論、まだ 24.04 のままのものの一覧）＋頼むこと（26.04 で success が揃っていれば #538 を閉じる判断をチャット〈ACF の系列〉へ。揃っていなければ次の確かめの時期）＋「返事はこの issue に。朝のチャットは追わない」。#538 の本文・状態・ラベルは変えない

止まる条件

* run 39307312990 が完了していない
* #538 に直近7日の同じ趣旨のコメントがある（その分は足さない）
* 他セッションの未解除の着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-MRA-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-MRA-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`git fetch --unshallow` 後、`git log --all --grep`・トレーラ・docs/logs の履歴）: CHAT-1020-MRA・`-MRA-` とも該当なし
- work/1020-mra はローカル・リモートとも無し → `git checkout -b work/1020-mra origin/cloudflare`
- 0章: 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致。
  run 39307312990（run-daily.yml #11、cloudflare 218ab5d1、2026-10-19T19:00:26Z 開始・19:17:22Z 更新）は completed / success
- #538: 題「Actions: ubuntu-latest の Ubuntu 26.04 への移行（2026-10-19〜）への対応」、open（reopened）。前提と一致するので新規起票はしない。
  コメント5件はすべて 2026-10-10〜12 で、直近7日（10/13〜）に同じ趣旨のものは無い。
  着手中のコメント（RGN-05・RGN-08）はどちらも後続の報告・コミットで済んでおり、未解除のものは無いと判断した。
  着手中のコメントを書いた（https://github.com/retroeater/mj/issues/538#issuecomment-6280081796 ）
- 対象の run: `GET /actions/runs?created>=2026-10-18T15:00:00Z`（JST 10/19 0:00 以降）で284件。
  skipped 137件（すべて check-build-failure.yml。ジョブが動かずランナーは割り当てられない）を除く147件のジョブ181件のうち、動いたジョブは173件（skipped 8件）
- ランナーの版はジョブのログ冒頭の「Operating System」「Runner Image」の group にしか出ない。ジョブの API の `labels` は全件 `ubuntu-latest`。
  check-run の annotations の移行の注記（"The ubuntu-latest label will migrate to Ubuntu 26 beginning October 19, 2026..."）は173件すべてに出ており、版の区別に使えない。
  ログの本体は `productionresultssa16.blob.core.windows.net` へのリダイレクトでプロキシに拒否される（`CONNECT tunnel failed, response 403`）ため、
  GitHub MCP の `get_job_logs` で読む（サブエージェント5本に分けて読ませた）

- ジョブごとのランナーの版（動いたジョブ173件・ジョブが skipped の8件。開始の早い順。「Operating System」の group の3行を空白でつないで引用、「Runner Image」の `Image:`・`Version:`、setup-python の `Successfully set up CPython (…)`）。
  サブエージェントの読み取りのうち 118247633806（run-daily の branches / delete）と 118367201333（check-assets の check）は自分でもログを読み、表と一致することを確かめた

| # | run ID | ワークフロー | event | ブランチ | ジョブ ID | ジョブ名 | 開始（UTC） | Operating System | Runner Image | CPython | ジョブの結論 |
|---:|---:|---|---|---|---:|---|---|---|---|---|---|
| 1 | 39142046859 | check-assets.yml | push | work/1018-ouk13 | 117718289621 | check | 2026-10-18T15:47:17 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 2 | 39147212259 | check-assets.yml | push | work/1019-ouk15 | 117733372791 | check | 2026-10-18T16:55:30 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 3 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117761449953 | live / update | 2026-10-18T19:00:29 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 4 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117761450118 | dojo / sync | 2026-10-18T19:00:29 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 5 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117761450120 | branches / delete | 2026-10-18T19:00:30 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 6 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117762548573 | live / regenerate / regenerate | 2026-10-18T19:05:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 7 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117762549671 | live / yotei / sync | 2026-10-18T19:05:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 8 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117762826188 | sns / update | 2026-10-18T19:06:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 9 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117763055086 | sns / regenerate / regenerate | 2026-10-18T19:07:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 10 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117764278718 | images / saikyo | 2026-10-18T19:12:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 11 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117764278805 | images / check | 2026-10-18T19:12:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 12 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117765035183 | images / sns_book | 2026-10-18T19:15:56 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 13 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117766123098 | saikyo / abema | 2026-10-18T19:20:42 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 14 | 39156749487 | run-daily.yml | workflow_dispatch | cloudflare | 117766258914 | saikyo / regenerate / regenerate | 2026-10-18T19:21:15 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 15 | 39162563404 | check-image-links.yml | schedule | cloudflare | 117778747477 | saikyo | 2026-10-18T20:16:38 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 16 | 39162563404 | check-image-links.yml | schedule | cloudflare | 117778746756 | check | 2026-10-18T20:16:40 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 17 | 39162563404 | check-image-links.yml | schedule | cloudflare | 117779495176 | sns_book | 2026-10-18T20:19:57 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 18 | 39171945710 | check-meibo.yml | schedule | cloudflare | 117806676800 | meibo | 2026-10-18T22:22:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 19 | 39171945710 | check-meibo.yml | schedule | cloudflare | 117806677078 | ampai | 2026-10-18T22:22:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 20 | 39172249264 | sync-birthday-calendar.yml | schedule | cloudflare | 117807576533 | sync | 2026-10-18T22:26:48 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 21 | 39172875765 | fetch-youtube.yml | schedule | cloudflare | 117809420838 | fetch | 2026-10-18T22:35:11 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 22 | 39172875765 | fetch-youtube.yml | schedule | cloudflare | 117809495651 | regenerate / regenerate | 2026-10-18T22:35:31 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 23 | 39172875765 | fetch-youtube.yml | schedule | cloudflare | 117810665723 | report | 2026-10-18T22:40:53 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 24 | 39173455273 | check-assets.yml | push | work/1019-ouk15 | 117811164686 | check | 2026-10-18T22:43:17 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 25 | 39173475305 | regenerate-sitemap.yml | push | cloudflare | 117811226625 | sync | 2026-10-18T22:43:34 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 26 | 39173475366 | regenerate-page.yml | push | cloudflare | 117811226969 | regenerate | 2026-10-18T22:43:34 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 27 | 39174221547 | check-assets.yml | push | work/1019-ouk15 | 117813446095 | check | 2026-10-18T22:54:07 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 28 | 39176286283 | cleanup-logs.yml | schedule | cloudflare | 117819520993 | cleanup | 2026-10-18T23:23:05 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 29 | 39176424002 | check-assets.yml | push | work/1019-wrc12 | 117819936556 | check | 2026-10-18T23:25:06 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 30 | 39177207321 | regenerate-page.yml | push | cloudflare | 117822225869 | regenerate | 2026-10-18T23:36:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 31 | 39177207417 | regenerate-sitemap.yml | push | cloudflare | 117822226208 | sync | 2026-10-18T23:36:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 32 | 39177551040 | update-live-channel.yml | schedule | cloudflare | 117823239915 | update | 2026-10-18T23:41:10 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | （setup-python 無し） | success |
| 33 | 39177551040 | update-live-channel.yml | schedule | cloudflare | 117823272186 | regenerate | 2026-10-18T23:41:17 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 34 | 39177551040 | update-live-channel.yml | schedule | cloudflare | 117823272244 | yotei | 2026-10-18T23:41:17 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 35 | 39177742395 | check-saikyo-unregistered.yml | schedule | cloudflare | 117823805755 | check | 2026-10-18T23:44:03 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 36 | 39179567227 | sync-dojo-calendar.yml | schedule | cloudflare | 117829222606 | sync | 2026-10-19T00:10:22 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 37 | 39180153330 | check-assets.yml | push | work/1019-ouk17 | 117830989179 | check | 2026-10-19T00:18:03 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 38 | 39181283933 | cleanup-branches.yml | schedule | cloudflare | 117834368802 | delete | 2026-10-19T00:33:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 39 | 39183466393 | check-assets.yml | push | work/1019-ouk19 | 117840929062 | check | 2026-10-19T01:02:17 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 40 | 39183694692 | check-assets.yml | push | work/1019-ouk19 | 117841598605 | check | 2026-10-19T01:05:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 41 | 39183898293 | check-assets.yml | push | work/1019-ouk19 | 117842186606 | check | 2026-10-19T01:07:44 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 42 | 39192458923 | check-assets.yml | push | work/1019-ouk20 | 117868028302 | check | 2026-10-19T02:58:37 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 43 | 39197532675 | regenerate-page.yml | push | cloudflare | 117883420570 | regenerate | 2026-10-19T04:02:51 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 44 | 39197568531 | check-assets.yml | push | work/1019-ouk19 | 117883526790 | check | 2026-10-19T04:03:18 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 45 | 39198361284 | check-assets.yml | push | work/1019-pny | 117885930222 | check | 2026-10-19T04:13:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 46 | 39198590505 | check-assets.yml | push | work/1019-ouk20 | 117886610064 | check | 2026-10-19T04:15:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 47 | 39202962966 | check-assets.yml | push | work/1019-ouk19 | 117899655510 | check | 2026-10-19T05:08:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 48 | 39203609157 | check-saikyo-unregistered.yml | workflow_dispatch | cloudflare | 117901587754 | check | 2026-10-19T05:15:38 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 49 | 39204322369 | regenerate-saikyo.yml | workflow_dispatch | cloudflare | 117903739041 | abema | 2026-10-19T05:23:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 50 | 39204322369 | regenerate-saikyo.yml | workflow_dispatch | cloudflare | 117903869793 | regenerate / regenerate | 2026-10-19T05:24:11 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 51 | 39204776634 | check-saikyo-unregistered.yml | workflow_dispatch | cloudflare | 117905127132 | check | 2026-10-19T05:28:56 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 52 | 39204785035 | check-assets.yml | push | work/1019-xic | 117905150688 | check | 2026-10-19T05:29:02 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 53 | 39204813330 | check-assets.yml | push | work/1019-skg | 117905237184 | check | 2026-10-19T05:29:22 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 54 | 39204974094 | check-saikyo-unregistered.yml | workflow_dispatch | cloudflare | 117905715751 | check | 2026-10-19T05:31:09 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 55 | 39205132590 | check-assets.yml | push | work/WRR | 117906202586 | check | 2026-10-19T05:33:01 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 56 | 39205399532 | check-assets.yml | push | work/WRR | 117907014527 | check | 2026-10-19T05:36:06 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 57 | 39207064476 | check-assets.yml | push | work/WRR | 117912096285 | check | 2026-10-19T05:55:47 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 58 | 39207340711 | regenerate-saikyo.yml | workflow_dispatch | cloudflare | 117912951411 | abema | 2026-10-19T05:59:08 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 59 | 39207340711 | regenerate-saikyo.yml | workflow_dispatch | cloudflare | 117913079797 | regenerate / regenerate | 2026-10-19T05:59:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 60 | 39207961809 | check-assets.yml | push | work/1019-ouk19 | 117914816197 | check | 2026-10-19T06:05:58 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 61 | 39207964445 | regenerate-sitemap.yml | push | cloudflare | 117914824350 | sync | 2026-10-19T06:05:59 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 62 | 39208081147 | check-assets.yml | push | work/1019-skg | 117915177797 | check | 2026-10-19T06:07:15 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 63 | 39210808602 | check-assets.yml | push | work/WRR | 117923523885 | check | 2026-10-19T06:35:38 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 64 | 39211582973 | check-assets.yml | push | work/1019-aic | 117925963754 | check | 2026-10-19T06:43:45 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 65 | 39211751035 | check-assets.yml | push | work/1019-ouk25 | 117926470993 | check | 2026-10-19T06:45:25 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 66 | 39212335736 | check-assets.yml | push | work/1019-wrc14 | 117928307436 | check | 2026-10-19T06:51:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 67 | 39213568587 | check-assets.yml | push | work/1019-ouk25 | 117932164108 | check | 2026-10-19T07:03:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 68 | 39213571384 | regenerate-page.yml | push | cloudflare | 117932172807 | regenerate | 2026-10-19T07:03:44 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 69 | 39213571410 | regenerate-sitemap.yml | push | cloudflare | 117932172917 | sync | 2026-10-19T07:03:44 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 70 | 39213852555 | check-assets.yml | push | work/1019-exm | 117933057875 | check | 2026-10-19T07:06:27 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 71 | 39214595783 | check-assets.yml | push | work/1019-exm | 117935402760 | check | 2026-10-19T07:13:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 72 | 39215404253 | check-assets.yml | push | work/1019-ouk25 | 117937963636 | check | 2026-10-19T07:20:59 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 73 | 39215856685 | check-assets.yml | push | work/1019-wrc15 | 117939405565 | check | 2026-10-19T07:25:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 74 | 39216222039 | regenerate-sitemap.yml | push | cloudflare | 117940588448 | sync | 2026-10-19T07:28:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 75 | 39216222076 | regenerate-page.yml | push | cloudflare | 117940588690 | regenerate | 2026-10-19T07:28:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 76 | 39217118390 | check-assets.yml | push | work/1019-wrc15 | 117943461596 | check | 2026-10-19T07:36:53 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 77 | 39217601137 | check-assets.yml | push | work/WRR | 117945027303 | check | 2026-10-19T07:41:27 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 78 | 39218853122 | check-assets.yml | push | work/1019-exm | 117949120067 | check | 2026-10-19T07:52:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 79 | 39218881677 | check-assets.yml | push | work/1019-exm | 117949215194 | check | 2026-10-19T07:53:07 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 80 | 39220068661 | check-assets.yml | push | work/1019-ouk26 | 117953032051 | check | 2026-10-19T08:03:33 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 81 | 39220774691 | check-assets.yml | push | work/1019-wrc14 | 117955289360 | check | 2026-10-19T08:09:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 82 | 39223067717 | check-assets.yml | push | work/1019-exm | 117962713611 | check | 2026-10-19T08:29:01 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 83 | 39224108466 | check-assets.yml | push | work/WRR | 117966099748 | check | 2026-10-19T08:37:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 84 | 39224146900 | check-assets.yml | push | work/WRR | 117966227031 | check | 2026-10-19T08:37:58 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 85 | 39225440280 | regenerate-page.yml | push | cloudflare | 117970464923 | regenerate | 2026-10-19T08:48:48 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 86 | 39225440273 | regenerate-sitemap.yml | push | cloudflare | 117970464877 | sync | 2026-10-19T08:48:49 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 87 | 39226190364 | check-assets.yml | push | work/1019-wrc17 | 117972947339 | check | 2026-10-19T08:55:07 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 88 | 39226525235 | check-assets.yml | push | work/1019-exm | 117974042723 | check | 2026-10-19T08:57:54 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 89 | 39227723642 | check-assets.yml | push | work/1019-sec11 | 117978000590 | check | 2026-10-19T09:07:46 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 90 | 39228306567 | check-assets.yml | push | work/1019-ouk27 | 117979906057 | check | 2026-10-19T09:12:32 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 91 | 39228582023 | check-assets.yml | push | work/1019-sec11 | 117980789646 | check | 2026-10-19T09:14:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 92 | 39230632532 | check-assets.yml | push | work/1019-wrc14 | 117987445218 | check | 2026-10-19T09:30:53 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 93 | 39231089617 | regenerate-sitemap.yml | push | cloudflare | 117988942007 | sync | 2026-10-19T09:34:33 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 94 | 39231188499 | check-assets.yml | push | work/1019-exm | 117989263603 | check | 2026-10-19T09:35:19 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 95 | 39232188659 | check-assets.yml | push | work/1019-exm | 117992546886 | check | 2026-10-19T09:43:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 96 | 39232196186 | regenerate-sitemap.yml | push | cloudflare | 117992571759 | sync | 2026-10-19T09:43:28 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 97 | 39232196280 | regenerate-page.yml | push | cloudflare | 117992572100 | regenerate | 2026-10-19T09:43:28 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | failure |
| 98 | 39234121716 | check-assets.yml | push | work/1019-ouk27 | 117998953181 | check | 2026-10-19T09:59:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 99 | 39239237012 | check-assets.yml | push | work/1019-sec11 | 118015738579 | check | 2026-10-19T10:41:25 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 100 | 39242227791 | check-assets.yml | push | work/1019-exm | 118025546002 | check | 2026-10-19T11:06:34 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 101 | 39242473739 | check-assets.yml | push | work/1019-sec12 | 118026353182 | check | 2026-10-19T11:08:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 102 | 39242759173 | check-assets.yml | push | work/1019-exm | 118027279456 | check | 2026-10-19T11:10:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 103 | 39242785280 | check-assets.yml | push | work/1019-exm | 118027364325 | check | 2026-10-19T11:11:05 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 104 | 39242849915 | regenerate-page.yml | workflow_dispatch | work/1019-exm | 118027585655 | regenerate | 2026-10-19T11:11:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 105 | 39243851759 | regenerate-page.yml | push | cloudflare | 118030870809 | regenerate | 2026-10-19T11:19:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 106 | 39243851762 | regenerate-sitemap.yml | push | cloudflare | 118030870966 | sync | 2026-10-19T11:19:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 107 | 39244012830 | check-assets.yml | push | work/1019-sec12 | 118031400296 | check | 2026-10-19T11:21:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 108 | 39244016954 | regenerate-sitemap.yml | push | cloudflare | 118031415175 | sync | 2026-10-19T11:21:15 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 109 | 39244602040 | check-assets.yml | push | work/1019-exm | 118033360140 | check | 2026-10-19T11:26:07 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 110 | 39244928782 | check-assets.yml | push | work/1019-wrr06 | 118034435849 | check | 2026-10-19T11:28:53 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 111 | 39245620588 | check-assets.yml | push | work/1019-sec12 | 118036695789 | check | 2026-10-19T11:34:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 112 | 39247084722 | check-assets.yml | push | work/1019-wrr06 | 118041506378 | check | 2026-10-19T11:46:38 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 113 | 39247089982 | regenerate-page.yml | push | cloudflare | 118041523722 | regenerate | 2026-10-19T11:46:40 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 114 | 39247089999 | regenerate-sitemap.yml | push | cloudflare | 118041523922 | sync | 2026-10-19T11:46:40 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 115 | 39247442969 | check-assets.yml | push | work/1019-exm | 118042716620 | check | 2026-10-19T11:49:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 116 | 39247799192 | check-assets.yml | push | work/1019-lnk | 118043897770 | check | 2026-10-19T11:52:36 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 117 | 39248467232 | check-assets.yml | push | work/1019-eng | 118046110361 | check | 2026-10-19T11:58:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 118 | 39248707430 | regenerate-sitemap.yml | push | cloudflare | 118046911358 | sync | 2026-10-19T12:00:18 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 119 | 39248707460 | regenerate-page.yml | push | cloudflare | 118047188700 | regenerate | 2026-10-19T12:01:08 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 120 | 39249442471 | check-assets.yml | push | work/1019-eng | 118049328453 | check | 2026-10-19T12:06:10 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 121 | 39249957550 | regenerate-sitemap.yml | push | cloudflare | 118051035048 | sync | 2026-10-19T12:10:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 122 | 39249957549 | regenerate-page.yml | push | cloudflare | 118051037171 | regenerate | 2026-10-19T12:10:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 123 | 39250113789 | check-assets.yml | push | work/1019-wrr07 | 118051552994 | check | 2026-10-19T12:11:25 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 124 | 39251966508 | check-assets.yml | push | work/1019-lnk | 118057704868 | check | 2026-10-19T12:25:42 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 125 | 39252146420 | check-assets.yml | push | work/1019-lnk | 118058312163 | check | 2026-10-19T12:27:05 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 126 | 39253387040 | check-assets.yml | push | work/1019-exm | 118062484638 | check | 2026-10-19T12:36:34 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 127 | 39254113364 | check-assets.yml | push | work/1019-wrr08 | 118064967811 | check | 2026-10-19T12:42:13 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 128 | 39254772616 | check-assets.yml | push | work/1019-wrr08 | 118067175646 | check | 2026-10-19T12:47:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 129 | 39254839024 | regenerate-sitemap.yml | push | cloudflare | 118067403286 | sync | 2026-10-19T12:47:44 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 130 | 39254839233 | regenerate-page.yml | push | cloudflare | 118067403540 | regenerate | 2026-10-19T12:47:44 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 131 | 39255548733 | check-assets.yml | push | work/1019-lnk | 118069802917 | check | 2026-10-19T12:53:12 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 132 | 39255552992 | regenerate-page.yml | push | cloudflare | 118069818305 | regenerate | 2026-10-19T12:53:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 133 | 39256532041 | check-assets.yml | push | work/1019-wrr08 | 118073114047 | check | 2026-10-19T13:00:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 134 | 39257787668 | check-assets.yml | push | work/1019-log-367-src | 118077336324 | check | 2026-10-19T13:09:43 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 135 | 39257864045 | check-assets.yml | push | work/1019-wrr07 | 118077590315 | check | 2026-10-19T13:10:15 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 136 | 39257885892 | regenerate-page.yml | push | cloudflare | 118077662560 | regenerate | 2026-10-19T13:10:23 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 137 | 39257945382 | check-assets.yml | push | work/1019-wrr07 | 118077864359 | check | 2026-10-19T13:10:48 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 138 | 39257952355 | regenerate-sitemap.yml | push | cloudflare | 118077887486 | sync | 2026-10-19T13:10:51 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 139 | 39258925917 | check-assets.yml | push | work/1019-lnk | 118081201054 | check | 2026-10-19T13:17:48 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 140 | 39259150573 | check-assets.yml | push | work/1019-lnk | 118081975514 | check | 2026-10-19T13:19:24 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 141 | 39259565324 | check-assets.yml | push | work/1018-log-367-migrate | 118083374691 | check | 2026-10-19T13:22:18 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 142 | 39259631900 | check-assets.yml | push | work/1019-log-367-src | 118083600802 | check | 2026-10-19T13:22:46 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 143 | 39260355669 | check-assets.yml | push | work/1019-wrr10 | 118086069918 | check | 2026-10-19T13:27:48 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 144 | 39260399613 | check-assets.yml | push | work/1019-eng | 118086228684 | check | 2026-10-19T13:28:07 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 145 | 39261077390 | check-assets.yml | push | work/1019-xdp | 118088558935 | check | 2026-10-19T13:32:50 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 146 | 39261094317 | update-sns-book.yml | workflow_dispatch | work/1019-xdp | 118088635927 | update | 2026-10-19T13:32:59 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 147 | 39261094317 | update-sns-book.yml | workflow_dispatch | work/1019-xdp | 118088935386 | regenerate | 2026-10-19T13:33:34 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 148 | 39261099815 | update-sns-book.yml | workflow_dispatch | cloudflare | 118088986128 | update | 2026-10-19T13:33:42 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 149 | 39261099815 | update-sns-book.yml | workflow_dispatch | cloudflare | 118089219617 | regenerate | 2026-10-19T13:34:08 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 150 | 39261301450 | check-assets.yml | push | work/1019-xdp | 118089345074 | check | 2026-10-19T13:34:26 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 151 | 39261671898 | check-assets.yml | push | work/1019-eng | 118090619732 | check | 2026-10-19T13:37:01 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 152 | 39262761904 | regenerate-sitemap.yml | push | cloudflare | 118094435197 | sync | 2026-10-19T13:44:47 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 153 | 39262762146 | regenerate-page.yml | push | cloudflare | 118094437041 | regenerate | 2026-10-19T13:44:47 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 154 | 39262756690 | check-assets.yml | push | work/1019-lnk | 118094473517 | check | 2026-10-19T13:44:55 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 155 | 39263374326 | check-assets.yml | push | work/1019-wrr10 | 118096563544 | check | 2026-10-19T13:49:00 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 156 | 39263883580 | check-assets.yml | push | work/1019-eng | 118098333156 | check | 2026-10-19T13:52:30 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 157 | 39264085410 | check-assets.yml | push | work/1019-eng | 118099009387 | check | 2026-10-19T13:53:51 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 158 | 39264591904 | check-assets.yml | push | work/1019-xdp | 118100746764 | check | 2026-10-19T13:57:17 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 159 | 39267428840 | check-assets.yml | push | work/1019-lnk | 118110549776 | check | 2026-10-19T14:16:16 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 160 | 39269516177 | update-sns-book.yml | workflow_dispatch | cloudflare | 118117829779 | update | 2026-10-19T14:30:14 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 161 | 39269516177 | update-sns-book.yml | workflow_dispatch | cloudflare | 118118201048 | regenerate / regenerate | 2026-10-19T14:30:56 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 162 | 39270491080 | security-check.yml | workflow_dispatch | cloudflare | 118121170073 | check | 2026-10-19T14:36:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 163 | 39271283373 | check-assets.yml | push | work/1019-xdp | 118123928937 | check | 2026-10-19T14:41:51 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 164 | 39273029669 | check-assets.yml | push | work/1019-sec14 | 118130044632 | check | 2026-10-19T14:53:31 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 165 | 39275736700 | check-assets.yml | push | work/1019-lnk | 118139442416 | check | 2026-10-19T15:11:32 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 166 | 39287700905 | check-assets.yml | push | work/1020-sec15 | 118179585045 | check | 2026-10-19T16:42:39 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 167 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118247633806 | branches / delete | 2026-10-19T19:00:31 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261018.339.1` | 3.12.15 | success |
| 168 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118247634325 | live / update | 2026-10-19T19:00:31 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261018.339.1` | 3.12.15 | success |
| 169 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118247634642 | dojo / sync | 2026-10-19T19:00:31 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 170 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118250316847 | live / yotei / sync | 2026-10-19T19:05:59 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 171 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118250316775 | live / regenerate / regenerate | 2026-10-19T19:06:00 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 172 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118250929752 | sns / update | 2026-10-19T19:07:15 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261018.339.1` | 3.12.15 | success |
| 173 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118251230421 | sns / regenerate | 2026-10-19T19:07:51 | —（ジョブ skipped、ランナー割り当て無し） | — | — | skipped |
| 174 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118251228981 | images / saikyo | 2026-10-19T19:07:52 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261018.339.1` | 3.12.15 | success |
| 175 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118251229062 | images / check | 2026-10-19T19:07:53 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 176 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118252810965 | images / sns_book | 2026-10-19T19:11:08 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 177 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118255148620 | saikyo / abema | 2026-10-19T19:15:58 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 178 | 39307312990 | run-daily.yml | workflow_dispatch | cloudflare | 118255438031 | saikyo / regenerate / regenerate | 2026-10-19T19:16:35 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 179 | 39340179387 | check-assets.yml | push | work/1020-lnk | 118359494495 | check | 2026-10-19T23:13:16 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 180 | 39342514585 | check-assets.yml | push | work/1020-sec16 | 118367201333 | check | 2026-10-19T23:35:29 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261012.334.1` | 3.12.15 | success |
| 181 | 39343294432 | regenerate-page.yml | push | cloudflare | 118369789833 | regenerate | 2026-10-19T23:43:25 | Ubuntu 24.04.5 LTS | `Image: ubuntu-24.04` `Version: 20261018.339.1` | 3.12.15 | success |

- ジョブが動かなかった run（run の結論 skipped、137件。すべて check-build-failure.yml）の run ID:
  39142441656・39142893604・39147450268・39173725688・39174584850・39174896130・39176777018・39177386414・39180472645・39182831615
  39183752338・39184123289・39184339729・39192684321・39197843830・39198087724・39198663161・39198970718・39199196773・39203195204
  39205060053・39205307398・39205560672・39205828356・39206091930・39207395388・39207684723・39208379787・39208737530・39209070124
  39209443034・39211217146・39211869348・39212273013・39212533542・39212912246・39213438961・39214008810・39214289278・39214550964
  39215406376・39215685270・39216196943・39216640558・39216948620・39217376945・39217801280・39218238614・39219242946・39219704371
  39221236565・39224411319・39224764929・39225947706・39226374383・39226858997・39227221437・39227543390・39228028451・39228478525
  39229011532・39229368526・39229703336・39230128017・39231043256・39231422835・39231851631・39232184804・39232517905・39232883769
  39233273005・39233576580・39234524366・39235020063・39239579593・39239930081・39242813263・39243170942・39243501992・39244235727
  39244531105・39245225376・39245566818・39245889793・39246298578・39246632757・39247422475・39247763573・39248169062・39248446724
  39248824867・39249181686・39249785141・39250418237・39250868675・39252351887・39252797477・39253775972・39254611413・39255195935
  39255626394・39256827053・39257347907・39257708041・39258197427・39258711626・39259076609・39259449643・39259564087・39259803021
  39260221169・39260794352・39261176618・39261980159・39262619158・39263161302・39263797080・39264495041・39265250125・39266078375
  39266618239・39267020994・39267937641・39268779703・39269186240・39270069692・39272059795・39273062835・39273905899・39274294093
  39276362019・39288353520・39289579046・39340472237・39340847671・39342758807・39343588602

- 結論: 26.04 で動いたジョブは0件。動いたジョブ173件はすべて `Ubuntu 24.04.5 LTS`・`Image: ubuntu-24.04`
  （`Version: 20261012.334.1` が168件、`20261018.339.1` が5件）。setup-python を使う172件はすべて CPython 3.12.15。
  failure は regenerate-page.yml の run 39232196280（job の annotations「Process completed with exit code 1.」「生成に失敗したページを飛ばし、成功したページだけをコミットしました…(#533)」）だけで、24.04 上。移行とは関係しない
- mj-logs の `sync-from-mj.yml` は、セッションの GitHub MCP の対象が mj だけのため読んでいない（#538 のコメントにも書いた）
- #538 にコメントした（https://github.com/retroeater/mj/issues/538#issuecomment-6280216274 ）。最初の投稿で最後の run の ID を「39343…」と途中で切って書いたため、
  同じコメントを編集して 39343294432 に直した。本文・状態・ラベルは変えていない
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 決定: なし（平野さんの回答も無し）。docs/decisions には足さない

## 報告

- 状態: 完了
- ブランチ: work/1020-mra
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1020-MRA-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-mra
- 確認用URL: なし
- マージ: 済（ログのみ。このコミットを fast-forward で cloudflare へ push）
- issue: #538
- 判断が必要なこと: なし（次に確かめる時期の判断は #538 に移した）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82a0e192）: https://github.com/retroeater/mj-logs/tree/main/guide/82a0e192

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82a0e192/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9a00b59e.md
