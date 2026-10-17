# CHAT-1016-SNK-01

- 着手日時: 2026-10-17
- 対象issue: なし（起票する）
- ブランチ: work/1016-snk
- 着手時HEAD: e61b4b998

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦の進行中の期の成績反映を検討する issue を「状況: 保留」で起票する
Chat-Ref: CHAT-1016-SNK-01
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈起票だけの指示〉
貼る時機: いつでも（新しいセッション: 任意〈続きでも可〉）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
鳳凰戦で今まさに進行中の期（例: 第43期後期）の成績を、ryoei.pro のページに反映するかを検討する issue を起票して残す。当面は対応しないので「状況: 保留」で置く。

### 決定（2026-10-16、平野さん）
- 「鳳凰戦の進行中の期の成績反映を検討する（Ampai 参照等）」の issue を起票する
- 当面は対応予定が無いので、ラベルは「状況: 保留」でよい
- 範囲は鳳凰戦（平野さんの指定）

### 前提（チャット側。平野さんの決定ではない）
- 今の扱い（issue の「背景」に書く。文面は実物に合わせて直してよい。出典は docs から引用して書く）:
  - 進行中の表は「鳳凰」に入力しないか、入力しても「表示」を N にしておく運用（docs/decisions/houou.md 2026-10-07〈CHAT-1007-LGR-13〉、docs/notes/houou-race.md）
  - 最新の進行中の期は積み上げ棒から除外し、その期が初参加の選手は一時的にスキップされる（docs/notes/static-generation.md）
  - houou/ の個人成績では進行中の期は「鳳凰位（進行中）」等の表示、前期で A1・A2・鳳凰位の選手の現在のリーグは `houou_current.span`（docs/notes/houou-top.md）
  - Ampai の URL は「ryoei.pro_Ampai」で期・リーグ・組ごとに持っている（#548、docs/decisions/houou.md）。Ampai の成績表から節ごとの途中成績を読めるか、読んでよいか（利用条件）は未確認（要確認。この指示では調べない）
- 「検討すること」の欄の候補（チャット側の案。issue には「候補」として書く）: 途中成績の出どころ（Ampai を参照・手入力・ほか）、どのページのどこに出すか（houou/ の個人成績・ランキング・順位変動、jpml_pros の「鳳凰<最新の期>」など）、確定前の数字であることの示し方、更新の頻度
- 題の案: 「鳳凰戦: 進行中の期の成績を反映するか検討する（Ampai 参照など）」
- 分野・対象のラベルは `gh label list` にあるものから選ぶ（例: `分野: UI/UX`、対象は鳳凰戦のページに当たるもの）。無いラベルは作らない
- 似た issue の候補（中身はチャット側で読めていない、要確認）: #221（Ampai 基盤）、#223（速報）、#548（Ampai の URL）

## 手順
1. 同じ目的の issue をクローズ済みも含めて検索する（`gh issue list --state all --search` で「進行中」「途中経過」「速報」「Ampai」「43期後期」など）。#221・#223・#548 は本文とコメントまで読む。あわせて未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）に同じ目的の作業が無いか確かめる。同じ目的のものがあれば起票せず止まる。関係はあるが目的が違うものは、起票する issue の本文に「関連: #n（違い）」として書く
2. issue を起票する。本文は「目的」「背景（今の扱い）」「検討すること（候補）」「保留の理由: 当面は対応予定が無い（2026-10-16、平野さん）」の形で書き、末尾に `Chat-Ref: CHAT-1016-SNK-01` の行を置く。ラベルは「状況: 保留」と、前提のとおり選んだ分野・対象
3. 起票した issue の番号・題・ラベル・本文をログの `## 経過` に引用し、決定を docs/decisions/houou.md に足す

## 止まる条件
- 手順1で同じ目的の issue または未マージの作業が見つかった（番号・ブランチと、どこが同じかを報告に書く）
- 「状況: 保留」のラベルが `gh label list` に無い
- 他セッションの着手中コメントが、似た issue の候補（#221・#223・#548）にあり、同じ目的に見える

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を報告に書く）
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SNK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SNK-01 を書く

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

ガイド文書（この版を写した時点の最新、mj e61b4b99）: https://github.com/retroeater/mj-logs/tree/main/guide/e61b4b99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e61b4b99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
