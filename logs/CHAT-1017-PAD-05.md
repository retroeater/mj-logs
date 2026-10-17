# CHAT-1017-PAD-05

- 着手日時: 2026-10-17
- 対象issue: #428・#589
- ブランチ: work/1017-pad
- 着手時HEAD: 7155f4361

## 指示

【Claude作成】Claude Code 向け指示：#428 本番の CLS を測って閉じる・`対象: live` を作って #589 に付ける（issue とログのみ）
Chat-Ref: CHAT-1017-PAD-05
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（PAD-04 と同じセッションに続けて貼ってよい。本番への反映はチャット側が 2026-10-17 13:04 ごろ確かめた）
作業ブランチ: クラウドセッションで実行する。work/1017-pad を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。CHAT-1017-PAD-04 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。

## 目的

CHAT-1017-PAD-04 が本番の反映待ちで残した、本番の CLS の計測と #428 のクローズを行う。あわせて PAD-04 の「判断が必要なこと」の残り2点を片付ける。

### 決定（2026-10-17、平野さん。CHAT-1017-PAD-04 の「判断が必要なこと」への回答）

1. `対象: live`（説明の案「放送対局のページ群 live/」）を新しく作り、#589 に付けて `対象: video_live` を外す
2. `jpml_pros` に残る表のセル（`td`）のずれは起票しない。#428 を閉じるコメントに書いておく
3. 本番の CLS の計測と #428 を閉じる作業は、この指示で行う（PAD-04 の決定 2 の案A のまま。`:has()` 由来のずれが 0 なら閉じる）

### 前提（チャット側。平野さんの決定ではない）

- 本番への反映: チャット側が 2026-10-17 13:04 ごろ（JST）に Chrome で本番を読み、`style.css` に `body.mj-table-page` があり、`rh_results.html` が `<body class="mj-table-page" data-search="off">`、`jpml_pros.html` が `<body class="mj-table-page">` を返すことを確かめた。PAD-04 のマージ（2922a7222）の後、本番のビルドが走ったのは cloudflare の `6f7b8b9` の push のとき（間の push は docs だけでビルドが走らなかった。チャット側の check-run の読み）。Code 側でも本番の版を取得して確かめ直す
- 計測の方法は PAD-04 の前提のとおり（本番だけ、PAD-02 と同じ5枚、390px・1100px、各10回、`:has()` 由来と他に分ける）
- #428 を閉じるコメントには、PAD-04 の前提のとおり、#382 の入れ直しは不要になったこと（#382 は 2026-10-16 に閉じた）と、`jpml_pros` のセルのずれ（PAD-02 の経過、上余白とは別、原因は未調査、起票しないと決めた）を書く。残りは #588・#589 で追うと書く
- ラベルの色は既存の `対象:` のラベルに合わせる
- 決定の書き先は `docs/decisions/site-review.md`
- 使う skill は無い

## 手順

1. 確かめる: CHAT-1017-PAD-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-PAD-05` を足す。本番の `style.css`・表のページの HTML が新しい版であることを取得して確かめる（古ければ止まる）
2. 測る: 上の前提の方法で本番を測り、表にしてログの `## 経過` に書く
3. issue: `:has()` 由来のずれが全ページ・全幅で 0/10 なら、#428 に結果（表はログの `## 経過` から引用）と前提のことを書いて閉じる（「状況:」ラベルがあれば外す）。0 でない回が残れば閉じずに結果を書き、「判断が必要なこと」に書く。`対象: live` を作って #589 に付け、`対象: video_live` を外す。各コメントの末尾に `Chat-Ref: CHAT-1017-PAD-05`。決定 1〜3 を `docs/decisions/site-review.md` に足す

## 止まる条件

- CHAT-1017-PAD-04 の状態が判断待ちでない
- 本番が古い版を返す（ほかのクエリでも古ければ、ビルドの状況を書いて止まる）
- `対象: live` と同じ趣旨のラベルが既にある（作らずに、そのラベルを付けて報告に書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs/ と docs/decisions/ だけの見込み）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（`CHAT-1017-PAD-05`）のコミット: 0件。識別子 PAD はこのセッションの PAD-01 で確認済み
- CHAT-1017-PAD-04 の `## 報告` の状態: 判断待ち
- 作業ブランチ: `origin/work/1017-pad` はマージ済み（cloudflare の祖先）、ローカルも cloudflare の祖先 → `git merge --ff-only origin/cloudflare`（7155f4361）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4つとも有る
- 0. 「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-pad
- ログ: https://github.com/retroeater/mj/blob/work/1017-pad/docs/logs/CHAT-1017-PAD-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad
- 確認用URL: なし
- マージ: 未
- issue: #428・#589
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f3633dc8）: https://github.com/retroeater/mj-logs/tree/main/guide/f3633dc8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f3633dc8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
