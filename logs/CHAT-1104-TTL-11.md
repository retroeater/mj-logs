# CHAT-1104-TTL-11

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-ttl
- 着手時HEAD: 001d30384

## 指示

【Claude作成】Claude Code 向け指示：title/ のレイアウト変更を告知しない決定を docs/decisions/title.md に記録する
Chat-Ref: CHAT-1104-TTL-11
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1104-TTL-10 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-ttl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-ttl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-ttl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
TTL 系列（CHAT-1104-TTL-01〜08）の title/ のレイアウト変更について、告知の要否の決定を記録する。変更は docs/decisions/title.md とログだけ。

### 決定（2026-11-04、平野さん）
- title/ のレイアウト変更（TTL-01〜08）は X などで告知しない（告知動画も作らない）

### 前提（チャット側。平野さんの決定ではない）
- 書き場所は docs/decisions/title.md の末尾に「## 2026-11-04（CHAT-1104-TTL-11）」の節。前の title/ のリニューアル（2026-10-03 の CHAT-1003-VID-01）では告知動画を作ったので、今回はそれと違うことが分かる一句を添えてよい

## 手順
1. docs/decisions/title.md の末尾の今の内容を読み、同じ趣旨の記述が無いことを確かめて、上の決定を1節として足す

## 止まる条件
- docs/decisions/title.md に、TTL 系列の告知について食い違う記述がある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TTL-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TTL-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: `git log --all --grep 'CHAT-1104-TTL-11'` は0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし。対象 issue の指定なし
- 作業ブランチ: `origin/work/1104-ttl` はマージ済み、ローカルも cloudflare の祖先だったので `git merge --ff-only origin/cloudflare` で 001d30384 へ進めた。手順0: 指示欄の末尾の行は指示文の最後の行と一致。ログを先に push（a2e431c57）
- 手順1: docs/decisions/title.md の告知の記述は 2026-10-03〜10-07 の CHAT-1003-VID-01 ほか（前のリニューアルの告知動画）だけで、TTL 系列の告知についての記述は無く、食い違いも無い。末尾に「2026-11-04（CHAT-1104-TTL-11）」の節を足した

## 報告

- 状態: 完了
- ブランチ: work/1104-ttl（docs のみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-TTL-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-ttl
- 確認用URL: なし
- マージ: 済（docs のみ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8c7dd9a3）: https://github.com/retroeater/mj-logs/tree/main/guide/8c7dd9a3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
