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

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。`git log --all --grep=CHAT-1019-LNK-06` は0件
- ブランチ: `work/1019-lnk` はローカル・リモートとも 4a4d5937f で cloudflare と一致（LNK-05 のログを入れたマージ済みの状態）。`git merge --ff-only origin/cloudflare` は「Already up to date」
- 手順1: LNK-05 のログの `## 報告`（最後の一致）の状態を「判断待ち / 続き: CHAT-1019-LNK-06」にした

### 手順2: 本番（2026-10-19 14:31 UTC、セッションから curl）

| URL | 結果 |
|---|---|
| `/img/ogp/pros/index-white.png`（クエリ無し・`?v=l6a91`） | 200・image/png・18,578 bytes。中身はリポジトリの `img/ogp/pros/index-white.png` とバイト単位で一致（`?v=l6a92`） |
| `/jpml_pros.html`（クエリ無し・`?v=l6a91`） | og:image `https://ryoei.pro/img/ogp/pros/index-white.png`、og:image:alt「連盟プロ」 |

チャット側の前提（23:30 JST ごろの読み）と同じ値。ブラウザでの見え方（SNS のカード）は確かめていない。

### check-run（GitHub API、`commits/<SHA>/check-runs`）

- ec8c5cbca（LNK-05 のマージ。コミット 13:44:38 UTC）: 「Workers Builds: mj」は1件だけで、summary は作業ブランチのプレビューのもの（13:56:33 完了）。**本番のビルドの check-run は後からも付いていない**。「Workers Builds: mj-ask」は 14:01:38 に success で完了していた（LNK-05 の時点では `in_progress`）
- ec8c5cbca より後に cloudflare に入ったコミットの「Workers Builds: mj」: e5c968f8c（13:43:43）・1682e060c（14:07:14）・640683f3a（14:10:48）・7249a1de6（14:13:30）はどれもプレビュー。**本番のビルドが付いていたのは 4a4d5937f（LNK-05 のログを cloudflare に入れたマージ。14:27:57 UTC 完了）**だけ
- 本番に反映されたのは、この 4a4d5937f の本番のビルドとみられる（ec8c5cbca を含む）。マージ（13:45 ごろ）から反映まで約43分。LNK-05 の `## 経過` の push の時刻「13:55 UTC ごろ」は誤りで、コミットと check-run の時刻からは 13:45 ごろ
- 原因（本番のビルドが ec8c5cbca で走らなかったのか、遅れて 4a4d5937f にまとめられたのか）は、指示どおり調べていない

- 手順3: #616（クローズ済みのまま）に結果をコメントした

## 報告

- 状態: 完了
- ブランチ: work/1019-lnk
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-LNK-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-lnk
- 確認用URL: なし
- マージ: 済（ログのみ。下の最終報告の SHA）
- issue: #616（クローズ済み。結果をコメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

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
