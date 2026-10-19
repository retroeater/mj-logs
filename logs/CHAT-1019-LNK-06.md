# CHAT-1019-LNK-06

- 着手日時: 2026-10-19
- 対象issue: #616（クローズ済み。結果を書く）
- ブランチ: work/1019-lnk
- 着手時HEAD: 4a4d5937f

## 指示

【Claude作成】Claude Code 向け指示：LNK-05 の本番の確かめをやり直して完了にする（本番の反映の遅れの記録）
Chat-Ref: CHAT-1019-LNK-06
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-lnk の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1019-LNK-05 は、マージの約20分後も本番に反映されていなかったため「判断待ち」で止まった。その後に反映されたので、本番の確かめをやり直して LNK-05 を閉じる。

### 前提（チャット側。平野さんの決定ではない）
- 2026-10-19 23:30 ごろ（JST）にチャット側が Chrome で本番を読んだ: `/img/ogp/pros/index-white.png` は 200・image/png・18,578 bytes（クエリ無しと付き）、`/jpml_pros.html?v=…` の og:image は `https://ryoei.pro/img/ogp/pros/index-white.png`、og:image:alt は「連盟プロ」。平野さんのダッシュボードでの確かめは頼んでいない
- 反映が遅れた原因（本番のビルドの遅れか、走らなかったか）は、この指示では調べない

## 手順
1. LNK-05 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1019-LNK-06` を足す。
2. 本番で `/img/ogp/pros/index-white.png` が 200（image/png）、`/jpml_pros.html` の og:image がその画像・og:image:alt が「連盟プロ」であることを、クエリ `?v=<未使用の値>` 付きでも確かめる。`gh api repos/retroeater/mj/commits/ec8c5cbca/check-runs`（実物の手段に合わせる）で、本番のビルドの check-run が後から付いたか・その完了時刻を `## 経過` に書く（付いていなければそう書く）。
3. 通れば #616（クローズ済みのまま）に、本番で確かめたことと反映までの時間（分かる範囲）をコメントする。

## 止まる条件
- 本番がまだ旧のまま（画像が 404、og:image が旧）なら、何も直さずに読んだ値を書いて止まる

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-LNK-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-LNK-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7249a1de）: https://github.com/retroeater/mj-logs/tree/main/guide/7249a1de

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7249a1de/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/19fa8d37.md
