# CHAT-1012-SNM-04

- 着手日時: 2026-10-12
- 対象issue: #544
- ブランチ: work/1012-snm
- 着手時HEAD: 1a0da912

## 指示

【Claude作成】Claude Code 向け指示：「(旧)連盟プロ以外」タブの削除を確かめ、読む所が無いことを確かめて #544 を閉じる（SNM-03 の続き。コードは変えない） Chat-Ref: CHAT-1012-SNM-04 マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シートは変えない 貼る時機: いつでも（CHAT-1012-SNM-03 は完了・マージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-snm の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜03 で使ったもので、重複ではない。 作業ブランチ: クラウドセッションで実行する。work/1012-snm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-snm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
平野さんが /live 用のブック（「連盟プロ以外」のあるブック）の「(旧)連盟プロ以外」タブを削除した。タブが消えたことと、それを読むコード・ワークフローが無いことを確かめ、タブの扱いを決めるための issue #544 を閉じる。
決定（2026-10-12、平野さん）

* 「(旧)連盟プロ以外」タブは削除した（平野さん、2026-10-12 13:51 までに）
* #544 は、この指示の確かめが通れば閉じる

前提（チャット側。平野さんの決定ではない）

* 削除は平野さんの申告（XAP のチャットからの伝言、2026-10-12）。Code はまだ実物を見ていない（要確認）
* CHAT-1012-SNM-03 のログ「手順1」: /live 用のブックの xlsx エクスポートで `#REF!` は 0（数式 52）。ほかのタブが消えたタブを引いていた形跡は無い
* CHAT-1011-PHT-25 のログ「経過」: 削除の前の旧シート（/live 用のブック）のタブは8つ（連盟プロ以外・別名・タイトル・タイトル戦・書籍・辞書・帰り道・(旧)連盟プロ以外）。「(旧)連盟プロ以外」には数式が4つあった
* #544 は CHAT-1011-PHT-25 で起票された（Open。PHT-26 のログ）。本文・コメントはこのチャットでは読んでいない（要確認）
* 使う skill は無い

手順

1. 確かめ: #544 の Open/Closed・本文・コメントを読み、ほかの Chat-Ref の未完了の着手中コメントが無いことを確かめる。#544 に着手中のコメントを書く。/live 用のブックのタブの一覧を xlsx エクスポート（CHAT-1011-RDN-10 と同じ方法）と gviz の読み取りで確かめ、「(旧)連盟プロ以外」が無いこと、残るタブが前提の7つ（連盟プロ以外・別名・タイトル・タイトル戦・書籍・辞書・帰り道）であることを書く。`#REF!` の件数も書く
2. 読む所: リポジトリ全体（`scripts/`・`scripts/tests/`・`.github/workflows/`・ページ側の JS・`docs/`）を「(旧)連盟プロ以外」「旧)連盟プロ以外」で grep し、読む所（コード・ワークフロー）が無いことを確かめる。文書の記述は、今も「ある」としている記述（直す必要があるもの）と、経緯としての記述（直さない）に分けて一覧にする。直す必要がある記述があれば、ログに一覧を書くだけにして直さない（次の指示で直す）
3. 記録とクローズ: 止まる条件に当たらなければ、docs/decisions/live.md（無ければ同じ分野の決定のファイル。docs/decisions/README.md で確かめる）に上の「決定」を足し、#544 に結果（タブの一覧・grep の結果・`#REF!` の件数）をコメントして閉じる（「状況:」ラベルがあれば外す）。cloudflare へマージする

止まる条件

* 「(旧)連盟プロ以外」タブがまだある、または残るタブが前提の7つと違う
* `#REF!` が1件でもある
* 「(旧)連盟プロ以外」を読むコード・ワークフローがある
* #544 が Closed、またはほかの Chat-Ref の未完了の着手中コメントがある、または本文に、タブの削除のほかに残る作業が書かれている（閉じずに一覧を書いて止まる）
* docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* タブの一覧・`#REF!` の件数・grep の結果（直す必要のある文書の記述の一覧を含む）・#544 のコメントの URL がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-SNM-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-SNM-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 387505ef）: https://github.com/retroeater/mj-logs/tree/main/guide/387505ef

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/387505ef/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/950bd402.md
