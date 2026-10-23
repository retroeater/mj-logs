# CHAT-1024-SKP-08

- 着手日時: 2026-10-24
- 対象issue: なし（同じ論点の issue を探し、無ければ起票する）
- ブランチ: work/1024-skp-rank
- 着手時HEAD: bfa482d30

## 指示

【Claude作成】Claude Code 向け指示：最強戦シートで順位（F列）が空の対局済みの卓を数え、ABEMA の放送で順位を入れる issue を起票する（未起票なら）
Chat-Ref: CHAT-1024-SKP-08
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変わるのは docs/logs と docs/decisions だけ）
貼る時機: いつでも（ほかの SKP の指示と並行してよい。新しいセッションに貼る）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp-rank の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-skp-rank を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp-rank origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
「最強戦」シートで順位（F列）が空の対局済みの卓に順位を入れる作業（平野さんが ABEMA の放送で調べる）を、issue にして残す。起票済みなら、その issue に今の件数を足す。

### 決定（2026-10-24、平野さん）
- 最強戦で順位が入っていない対局について、順位を入れる。調べ方は ABEMA の放送を見る
- 未起票なら起票する

### 前提（チャット側。平野さんの決定ではない）
- docs/notes/saikyo-page-design.md の2章に、F 列が空欄の卓は 2011〜2021 年度の 397 卓とある（CHAT-1015-SKD-21 の時点。要確認。手順1で今の数を数える）。表示は順位の代わりに結果（通過・優勝・敗退）の順で並べている
- 2016 以前の対局には、ABEMA に回の無いものがある（docs/notes/abema-book.md、#573）。放送で調べられない卓が残りうる
- 順位が入ると変わりうるもの（要確認。手順1で今の作りを読んで書く）: 卓内の並び（`player_order()`）・卓のページの帯「1位 通過」・個人成績の年度別タブの順位・対戦成績は使わない（通過/敗退だけで数える）

## 手順
1. 同じ論点の issue（最強戦の順位・F列の空欄）を検索し（クローズ済みも含めて確認）、あれば使う。`regenerate-page.yml` の check（saikyo_pages）か生成と同じ経路で「最強戦」シートを読み、F 列が空の対局済みの卓（全員の結果〈G列〉がある卓）を、年度ごとの卓数と、対局ごとの卓数（年度・対局名・対局日・卓数）の表にして `## 経過` に書く。年度ごとに ABEMA ブック（【2】＋【3】）で回が当たっている卓の数も添える（放送で調べられる見込みの卓）。シートの行数も書く
2. issue が無ければ起票する。題は「最強戦: 順位（F列）が空の卓に順位を入れる（ABEMA の放送で調べる）」。本文は、目的・上の決定・手順1の年度ごとの表と、ABEMA に回の無い卓の扱い（今の表示のまま残る）・順位が入ると変わる画面（手順1で読んだもの）。あれば、その issue に手順1の表をコメントする。#628（名前かな）と #573（片方にしか無い対局・卓）からリンクする
3. 上の「決定」を docs/decisions/saikyo.md に足す。シートには書き込まない

## 止まる条件
- 同じ論点の issue があり、中身が上の決定と食い違う
- シートの行数が読み直すたびに変わる（件数を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SKP-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SKP-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1024-skp-rank
- ログ: docs/logs/CHAT-1024-SKP-08.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d53626ec）: https://github.com/retroeater/mj-logs/tree/main/guide/d53626ec

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d53626ec/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
