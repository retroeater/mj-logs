# CHAT-1017-HOU-72

- 着手日時: 2026-10-17
- 対象issue: #541（関係: 起票する issue）
- ブランチ: work/1017-hou
- 着手時HEAD: 66cd4dd66

## 指示

【Claude作成】Claude Code 向け指示：#541 の残り（進行中の期だけリーグ名で通期を判断している所）を issue に起票する
Chat-Ref: CHAT-1017-HOU-72
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（`docs/decisions/houou.md` への決定の追記を含む）
貼る時機: いつでも（CHAT-1017-HOU-71 は完了。新しいセッションは任意）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の作成と push と、cloudflare へのマージの push と、issue の起票と #541 へのコメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

#541 の段3（CHAT-1017-HOU-71）で、生成物を変えない段だったために残した「進行中の期だけリーグ名で通期を判断している所」を、後で直す issue として起票する。この指示ではコードを変えない。

### 決定（2026-10-17、平野さん）

- HOU-71 で残した、進行中の期（`houou_upcoming`）でリーグ名から通期を判断している所は、残したままにせず issue を起票して後で直す（直す時期は未定）

### 前提（チャット側。平野さんの決定ではない）

- 残した所と理由は、CHAT-1017-HOU-71 のログの `## 報告` の「判断が必要なこと」の1つ目（`assets/houou.js` の `upcomingTerm()`、`generate_houou_pages.upcoming_last()`。`houou_upcoming` が選手ごとにリーグだけを持ち、通期の印が無いため。直すと生成物が変わる）
- 同じログの「判断が必要なこと」の2つ目（`lib/pro_stats.py` は通期のリーグの「後」で止めない）は、平野さんの判断をもらっていないので issue の本文には「関連（判断は未定）」として書くだけにする
- 起票した issue の対応の時期は未定。期日は付けない

## 手順

1. 「通期」「upcoming」「進行中の期」「SPAN_LEAGUES」で issue を検索し（クローズ済みを含む）、同じ主題の issue があればそこにコメントして起票しない。結果をログに書く。
2. 起票する。題は「houou/: 進行中の期（houou_upcoming）でもデータの通期の印で通期を判断する」の意。本文には、背景（#541 で内部を通期で持つ形にしたこと、HOU-71 では生成物を変えないため残したこと）、残っている所（CHAT-1017-HOU-71 のログの `## 報告`「判断が必要なこと」の1つ目と、`## 経過` で残した所を表にした節から引用する。要約を書き写さず、引用元の節名を書く）、直すと変わる生成物の見込み（`houou_upcoming` の形。要確認と書く）、関連（`lib/pro_stats.py` の件、判断は未定）、対応の時期は未定、を書く。ラベルは #541 と同じ系統から選ぶ。末尾に `Chat-Ref: CHAT-1017-HOU-72` の行。
3. #541（クローズ済み）に、残りを起票した issue の番号をコメントする（状態は変えない。末尾に `Chat-Ref:` の行）。`docs/decisions/houou.md` に上の「決定」を足す。push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` で入れる。

## 止まる条件

- 変更が `docs/logs/`・`docs/decisions/houou.md` の外に及ぶ
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、起票した issue の番号（またはコメントした既存の番号）を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-72.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-72 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66cd4dd6）: https://github.com/retroeater/mj-logs/tree/main/guide/66cd4dd6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66cd4dd6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
