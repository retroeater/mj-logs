# CHAT-1020-LNK-09

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-lnk
- 着手時HEAD: 2ec24f239

## 指示

【Claude作成】Claude Code 向け指示：LNK 系列の振り返りの申送り（指示文の雛形と振り返りの手順に1行ずつ足す）
Chat-Ref: CHAT-1020-LNK-09
マージ: ドキュメントのみ（docs/ と docs/decisions/・docs/logs/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-lnk の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1020-lnk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1020-lnk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
LNK 系列（CHAT-1019-LNK-01〜08、「連盟 > リンク」の廃止とメニューの整理）の振り返りと、Fable 5.1 のレビューで出た知見のうち、手順に落とせる2つを文書に書く。

### 決定（2026-10-20、平野さん）
- LNK 系列の振り返り・レビューの申送りを行う（平野さんが「申し送り」と言った。書く中身は下の「前提」のチャット側の案）

### 前提（チャット側。平野さんの決定ではない）

振り返りで分かったこと（ログの事実）:
- CHAT-1019-LNK-05 は、マージ後15分で本番が古いままだったため「判断待ち」で止まり、続きの LNK-06 が要った。docs/decisions/operations.md の CHAT-1017-PAD-09 には「本番が古いままなら Code は15分で待つのをやめて『未確認の項目』に回し、チャット側が予約（send_later）で後から確かめる」とある。チャット側が LNK-05 の指示文にこれを書かず、決定も読んでいなかった
- 振り返りで「前回の約束が守られたか」を確かめるとき、チャット側は手元の会話に前回の振り返りが無いことを理由に確かめなかった。PAD-09 の決定は docs/decisions/operations.md の振り返りの節にあった

| 知見 | 選んだ層 | 置き場所 | 理由 |
| --- | --- | --- | --- |
| マージ後の本番の確かめが15分で済まないときは、判断待ちにせず「未確認の項目」に回して完了にし、チャット側が send_later で確かめる（PAD-09 の決定） | ガイド文 | docs/instruction-template.md の箇条書き（「待機の上限15分と…」の行の近く） | チャット側が指示文に書き落とすと Code が判断待ちで止まる。push の順番は他のセッションが決めるのでテスト・hook では防げない。雛形に1行あれば書き落としが減る |
| 振り返りの「前回の約束」は、docs/decisions/operations.md の直近の振り返り・申送りの節で確かめる | ガイド文（推論的センサーの手順） | docs/notes/chat-routines.md「振り返り」の観点のチェックリストの「前回の振り返り・レビューで立てた約束…」の行 | 約束は会話をまたいで残り、会話の中には無いことが多い。読む場所を1つ書けば確かめられる |

- 計算的センサー（テスト・hook・ワークフロー）は検討したが、どちらも当たらない（チャット側の書き方と読み方の問題で、リポジトリの状態からは検出できない）
- 書かないもの: 「提案や起票の前に docs/decisions・docs/notes を読む」は chat-routines.md「指示の種類ごとの確かめ」の「起票・方針の提案」の行と chat-side-operations.md「書く前に実物で確かめる」に既にある（今回は守らなかっただけ）。足さない
- 文言は既存の行に統合してよい（新しい行を足すより、既存の行の拡張を優先する）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。追記先の2文書の今の内容（該当の行の前後）を読み、残り容量（上限・警告域との差。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と check-assets.yml の対象かどうか）を測って `## 経過` に書く。
2. 上の表の2つを、規則と理由の一句だけで書く（事例は書かない。要れば docs/notes/handover-archive-2026.md へ）。docs/decisions/operations.md に「2026-10-20（CHAT-1020-LNK-09）」として、この申送りを書く。
3. 変えた箇所の前後を `## 経過` に引用し、cloudflare へ入れる。

## 止まる条件
- 同じ論点の open issue がある
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止まらず、置き換え・拡張してよい）
- 追記で容量の上限を超える、または警告域に入る（測った値を書いて止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-LNK-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-LNK-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 2ec24f23）: https://github.com/retroeater/mj-logs/tree/main/guide/2ec24f23

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/2ec24f23/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e7c8d9e0.md
