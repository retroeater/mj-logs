# CHAT-1127-LVP-48

- 着手日時: 2026-11-27
- 対象issue: #401
- ブランチ: work/1127-lvp-pq
- 着手時HEAD: e69c8e6c4

## 指示

【Claude作成】Claude Code 向け指示：#401（プロクイーン決定戦の4人）を締め、回戦の対局者の判定を4人に絞れるか確かめる
Chat-Ref: CHAT-1127-LVP-48
マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる
貼る時機: 2026-11-28 の毎朝の取り込み〈update-live-channel、04:00 JST〉の後（平野さんが 11/27 に【3】へ入れた値が生成に入ってから）。取り込みの直後の30分は避ける
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1127-lvp-pq の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1127-lvp-pq を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1127-lvp-pq origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1127-LVP-47 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1127-LVP-48` を足す。

## 目的
#401 の値の入力が済んだので、記録を締めて issue を閉じる。あわせて、回戦の対局者を4〜5人まで認めている判定を4人に絞れるかを確かめ、絞れるなら絞る。

### 決定（2026-11-27、平野さん）
1. #401: プロクイーン決定戦 第11〜14期の回戦の動画48本の「対局者」を、各回戦で打った4人にした（平野さんが【3】の P 列に入れた、2026-11-27）。抜け番は連盟の観戦記（https://www.ma-jan.or.jp/category/title-fight/queen/final-queen の第11〜14期の観戦記）と動画で確かめた。途中で敗退した人は、その後の回戦の抜け番として扱う
2. 女流プロ麻雀日本シリーズ 2022 の回戦の動画は、節のページに分けず、今のまま（予選1〜28回戦は予選のページ、プレーオフ・決勝はそれぞれのページ。CHAT-1127-LVP-47 の判断待ちへの答え）
3. チャンピオンズリーグの「タイトル戦」シートの「自動掲載」は平野さんが N にした（2026-11-27）

### 前提（チャット側。平野さんの決定ではない）
- 2026-11-27 15:20 の【3】で、P 列（対局者）に5人の名前が入った行は0行（チャット側の gviz の数え）。【2】の値の側に5人の行が残るかは未確認
- 回戦の対局者の人数の判定（4〜5人を許す）の場所と名前は未確認（#401 の本文・LVP-35 のログ）

## 手順
1. #401 に着手中のコメントを書く。11/28 の生成で、プロクイーン第11〜14期の回戦のカードの対局者が4人になったことを確かめる。【2】【3】を合わせた全行で、回戦の動画の対局者が5人以上の行を洗う
2. 5人以上の行が無ければ、回戦の対局者の判定を4人だけにし（5人を許す例外を消す）、テストを直す。全ページを再生成して生成物の差分が無いことを確かめ、テストを回す。5人以上の行が残っていれば、判定は変えず、その行を全件書く
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。上の「決定」を docs/decisions/live.md に足し、#401 に結果をコメントして閉じる（「状況:」ラベルがあれば外す）

## 止まる条件
- 11/28 の生成で、プロクイーン第11〜14期の回戦のカードがまだ5人のまま（【3】の値と生成の様子を書いて止まる）
- 判定を4人にしたとき、生成物に差分が出る（マージせずに止まる）
- テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
- 未マージの work/ ブランチが同じ関数を変えている、または取り込みで衝突する
- #401 に他セッションの着手中コメントがある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1127-LVP-48.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1127-LVP-48 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-11-27 22:50 JST に貼られた。「貼る時機」（2026-11-28 04:00 JST の取り込みの後、直後の30分は避ける）より前なので、ログだけを先に push し、2026-11-28 04:45 JST に再開する（このセッションの予約で戻る）

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f268dbf5）: https://github.com/retroeater/mj-logs/tree/main/guide/f268dbf5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f268dbf5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f92ac9d9.md
