# CHAT-1026-UBU-01

- 着手日時: 2026-10-26
- 対象issue: #538
- ブランチ: work/1026-ubu
- 着手時HEAD: bdf27d95b

## 指示

【Claude作成】Claude Code 向け指示：#538 Ubuntu 26.04 で動いた回があるか・その成否を確かめる（2回目の確認、報告のみ） Chat-Ref: CHAT-1026-UBU-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1026-ubu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-ubu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-ubu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#538 の残り（`ubuntu-latest` の Ubuntu 26.04 への移行の後、26.04 で動いた回が success か）を確かめる。CHAT-1020-MRA-01 の確認（2026-10-19T23:43Z 開始の run まで）では 26.04 の回が0件だったので、その後の run で同じ確認をする。
決定（YYYY-MM-DD、平野さん）

* （無し。この指示で新しく決めたことは無い）

前提（チャット側。平野さんの決定ではない）

* #538 は open。最後のコメントは CHAT-1020-MRA-01（2026-10-20T00:13Z）で、次の確認を 2026-10-27 ごろにする案が書かれている（2026-10-26 にチャットで REST で読んだ）
* チャット側が REST で数えた件数（要確認）: 2026-10-19T23:44Z 以降に作られた mj の run は 444件（skipped でないもの 275件）、そのジョブで skipped でないもの約 403件。failure の run は「ページの再生成」10件（10/22〜10/25）・「道場部ゲストのカレンダーの同期」1件（run 39700900041）・「[scheduled] 毎朝の運行」1件（run 39657805637）・「本番ビルド失敗の検知」1件（run 39524306758）・「X の投稿の変換」1件（run 39523831303）
* ランナーの版はジョブのログ冒頭の「Operating System」「Runner Image」の group にしか出ない（CHAT-1020-MRA-01 の経過）。チャット側のセッションではジョブのログの置き場（blob.core.windows.net）がプロキシに拒否され、読めなかった
* 移行は1〜2か月かけて少しずつ進み、11月中に終わる見込み（#538 本文「移行で変わるもの」）

手順

1. #538 の本文とコメントを読み、上の前提（open・最後のコメントが CHAT-1020-MRA-01）と食い違えば止まる。他のセッションの着手中コメントが 2026-10-20 より後にあれば止まる
2. 2026-10-19T23:43Z より後に開始した mj の run の、skipped でないジョブ全件のログ冒頭を読み、ジョブごとに Operating System・Runner Image（`Image:`・`Version:`）・setup-python の `Successfully set up CPython (…)`・ジョブの結論を表にしてログの `## 経過` に書く（run ID・ワークフロー・ジョブ ID・開始時刻を添える。全件書き、略さない）。あわせて mj-logs の `sync-from-mj.yml` の同じ期間の run も、読めれば同じ表にする（読めなければその旨を書く）
3. 集計（26.04 の件数と、そのうち success でないもの。ワークフローの名前の一覧つき）と、上の failure の run それぞれが 26.04 で動いたものか・原因が移行と関係しそうか（annotations と失敗したステップのログから）をログに書き、`## 経過` の該当の表から引用して #538 にコメントする。本文・状態・ラベルは変えない（閉じない）

止まる条件

* 手順1の前提が食い違う
* 件数がチャット側の数（前提）から ±20件より大きくずれる（ずれと理由の見込みを書いて止まる）
* ジョブのログが読めない（手段を変えて2回試しても読めない。試した手段を書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。26.04 の回があり全部 success なら、「判断が必要なこと」に「#538 を閉じてよいか」を書く。26.04 の回が無ければ、次に確かめる時期の案を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-UBU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-UBU-01 を書く

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

ガイド文書（この版を写した時点の最新、mj 0d1ef6e5）: https://github.com/retroeater/mj-logs/tree/main/guide/0d1ef6e5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d1ef6e5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdf27d95.md
