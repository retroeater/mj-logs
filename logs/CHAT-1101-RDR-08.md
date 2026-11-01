# CHAT-1101-RDR-08

- 着手日時: 2026-11-01
- 対象issue: #568
- ブランチ: work/1101-rdr-img
- 着手時HEAD: 2caddbb

## 指示

【Claude作成】Claude Code 向け指示：画像リンク切れの検知（check_image_links.py）の UA に目印 mj-internal を足す
Chat-Ref: CHAT-1101-RDR-08
マージ: 承認済み（チャットで、2026-11-01）。条件: cloudflare との差分が scripts/check_image_links.py・docs/notes/session-network.md の UA の記述（あれば）・docs/decisions/cloudflare.md の追記・ログだけ
貼る時機: いつでも（RDR-06 のセッションの続きでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-rdr-img の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1101-rdr-img を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-rdr-img origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1101-RDR-06 で決めた「本番へまとめて要求を送るときは UA に `mj-internal` を含める」を、毎朝動く画像リンク切れの検知にも当てる。

### 決定（2026-11-01、平野さん）
- `scripts/check_image_links.py` の `USER_AGENT` に `mj-internal` を足す
- 本番へ要求を送る共通の HTTP の部品は、今は作らない（本番をまとめて巡回する仕組みを新しく作るときに作る）

### 前提（チャット側。平野さんの決定ではない）
- 今の `USER_AGENT` は `Mozilla/5.0 (compatible; ryoei.pro link checker; +https://ryoei.pro/jpml_pros.html)`。案は括弧の中に足す形（例 `Mozilla/5.0 (compatible; ryoei.pro link checker; mj-internal; +https://ryoei.pro/jpml_pros.html)`）。実物に合わせて変えてよい
- 確かめる先は主に外部の画像のホスト（X の画像など）。docs/notes/session-network.md に、この UA で 200 が返った記録がある。UA を変えると返り方が変わるホストがありうる（要確認）

## 手順
1. `USER_AGENT` に `mj-internal` を足す。docs/notes/session-network.md など、今の UA の文字列をそのまま書いている文書があれば（`git grep -n 'ryoei.pro link checker'`、docs/logs・docs/decisions の過去の行は除く）、合わせて直す
2. 作業ブランチで画像リンク切れの検知を手動で1回動かし（今のワークフローの手動実行の仕組みに従う。書き込み・通知をしない形があればそれで）、切れと判定した件数とその URL の一覧を、cloudflare の直近の毎朝の運行（images / check）の結果と並べてログに書く
3. 決定を docs/decisions/cloudflare.md に足し、冒頭の「マージ:」の条件を `git diff origin/cloudflare...HEAD --stat` で確かめてから cloudflare へマージする

## 止まる条件
- 手順2で、切れと判定した件数が直近の毎朝の結果より増えた（UA を変えたことで拒むホストがある疑い。増えた URL とホストをログに書いて止まる）
- 手動実行の仕組みが書き込み・通知を伴い、避ける方法が無い（実行せずに止まる）
- 未マージの work/ ブランチが `scripts/check_image_links.py` の `USER_AGENT` の行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-RDR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-RDR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f0cfaaa）: https://github.com/retroeater/mj-logs/tree/main/guide/6f0cfaaa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f0cfaaa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
