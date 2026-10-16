# CHAT-1017-SKR-17

- 着手日時: 2026-10-17
- 対象issue: #564（親の候補）、起票する issue
- ブランチ: work/1017-skr-img
- 着手時HEAD: c345618d3

## 指示

【Claude作成】Claude Code 向け指示：ログを消すときに docs/logs/<Chat-Ref>/ の画像も消すよう cleanup-logs を直す issue を起票する（起票だけ）
Chat-Ref: CHAT-1017-SKR-17
マージ: ログだけ（docs/logs/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1017-SKR-16 と同じセッションの続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-skr-img の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-skr-img を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1017-SKR-16 で、Code が撮った画像を作業ブランチの `docs/logs/<Chat-Ref>/` に置いてよいと決めた。一方、`cleanup-logs.yml`（`scripts/cleanup_logs.py` の `LOG_GLOB = "CHAT-*.md"`）はログの .md だけを消し、同名のフォルダの画像は残る（SKR-16 の時点で docs/logs/ は 39MB、大半が画像）。画像が増える前に、ログと一緒に画像も消すよう直す issue を起票する。この指示ではコードを直さない。

### 決定（2026-10-17、平野さん）
- 上の直しの issue を起票する（SKR の振り返りの後の判断で b を選んだ）

### 前提（チャット側。平野さんの決定ではない）
- issue の本文に書くこと: 目的（上のとおり）／今の挙動（`LOG_GLOB`・消す条件・`docs/logs/` の .md 以外のファイルの件数と容量を実物で数える）／直しの案（ログ `CHAT-xxx.md` を消すときに、同じ名前のフォルダ `docs/logs/CHAT-xxx/` も消す。ログが既に消えて画像のフォルダだけ残っているもの〈孤児〉の扱いは、件数を出したうえで論点として残す）／`docs/` は `.assetsignore` で配信の対象外であること
- ラベル: 既存のラベルから選ぶ（分野: 自動化 など。対象は該当するものがあれば）。親 issue は Actions の整理の #564 と見ている（要確認: #564 が open で、子 issue を本文で参照する形か。合わなければ関連として書くだけにする）
- 担当の系列は WFL（Actions の整理）。本文の末尾に「担当: WFL の系列（#564）」と書く。WFL のチャットへの伝言は作らない（issue で拾われる）

## 手順
1. 同じ論点の issue（「cleanup-logs」「ログの画像」「docs/logs 容量」）を、クローズ済みも含めて検索し、同じ論点の open issue があれば止まって報告する。`scripts/cleanup_logs.py` と `cleanup-logs.yml` を読み、上の数を数える
2. 起票する（本文の末尾に `Chat-Ref: CHAT-1017-SKR-17`）。#564 に子 issue として参照を足すのが今の書き方なら足す
3. ログを仕上げ、cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-SKR-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-SKR-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c345618d）: https://github.com/retroeater/mj-logs/tree/main/guide/c345618d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c345618d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a4a72a63.md
