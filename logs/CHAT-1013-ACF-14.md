# CHAT-1013-ACF-14

- 着手日時: 2026-10-13
- 対象issue: なし
- ブランチ: work/1013-acf-rules
- 着手時HEAD: 7320c07146f0f24be390aa2795f23c0fc11bf16c

## 指示

【Claude作成】Claude Code 向け指示：ACF の振り返り（2026-10-13）の申送り — chat-side-operations.md の「複数のチャットが同じ仕組みを変えているとき」の確かめ方を書き換え、止まる条件の数の揺れと貼る時機の並行の但し書きの規則を足す（docs のみ）
Chat-Ref: CHAT-1013-ACF-14
マージ: ドキュメントのみ（docs/notes・docs/instruction-template.md・docs/notes/handover-archive-2026.md・ログ）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい。足した後のサイズが警告域を超えるなら、入れずに判断待ちで止まる
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-acf-rules の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 ACF は同じチャットの CHAT-1012-ACF-01〜05・CHAT-1013-ACF-06〜13 で使ったもの。
作業ブランチ: クラウドセッションで実行する。work/1013-acf-rules を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-acf-rules origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。cloudflare へ入れるときの取り込みで docs/ の文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#558 の段階1の指示（CHAT-1013-ACF-08・10・11）が、未マージのブランチとの重なり・関数の使われ方・ページの凍結を確かめずに書かれて3本続けて止まり、ACF-12 が通信の揺れによる1件の差で止まった。既にある規則の確かめ方を書き換え、無かった規則を2つ足す。

### 決定（2026-10-13、平野さん。振り返りと Fable 5.1 のレビューのおすすめのとおり）
- 新しい規則を増やすのではなく、既にある規則（docs/notes/chat-side-operations.md「複数のチャットが同じ仕組みを変えているときは…もう一方のチャットの最新のログを読む」）の確かめ方を書き換える: ログを読むだけでなく、未マージの work/ ブランチの差と、変える関数の呼び出し元と、ページが凍結中・`all` の再生成の対象外でないかを見る。調べの指示（起票・洗い出し）の手順にも同じ3点を書かせる
- 止まる条件で数の増減を見るとき、外の通信（HEAD・API）の揺れで動く数は「同じ実行をもう1回行い、それでも外れたら止まる」の形で書く
- 「貼る時機」の「別のセッションに貼る」は、並行で動かすときだけ書く。前の指示の完了後に貼るなら同じセッションでよいと書く

### 前提（チャット側。平野さんの決定ではない）
- 事例（根拠）: ACF-08（work/1012-dsn が `generate_houou_pages.py` の `avatar_html()` を変えていた）、ACF-10（`generate_houou_race.py` の `IMAGE_SIZE` が houou/ の写真にも効いていた）、ACF-11（books/ は 2026-09-22 から凍結、`FROZEN_FROM_ALL`、ACF-07 の調べは 9/22 の生成物から件数を数えていた）、ACF-12（dry_run の1回目で X API を呼ぶ予定が 0→1、2回目は 0）、ACF-09・10（「別のセッションに貼る」の但し書きが、前の指示の完了後に同じセッションに貼られてログに差として書かれた）
- 今のサイズ（チャット側が mj-logs の guide/5cfee13c で測った。要確認）: chat-side-operations.md 25,326 バイト（上限 28KB・警告域 26KB、残り約 670 バイト）、instruction-template.md 13,563 バイト（上限 16KB・警告域 14KB、残り約 440 バイト）。どちらも余裕が少ないので、足す分は既存の行の書き換えで賄い、事例は archive へ書く
- 書き換える箇所の案（行番号は guide/5cfee13c のもの。実物で確かめる）:
  - chat-side-operations.md 75行の太字の規則。確かめ方の括弧内「変える関数名・ファイル名で mj-logs の `logs/` を grep し、判断待ち・中断のログがあればそのチャットの最新のログを読む」を「`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に変えるファイルがあるか、変える関数・定数の呼び出し元（別のページの生成を含む）、ページが凍結中・`regenerate.py` の `FROZEN_FROM_ALL` でないかを見る。調べの指示にはこの3点を手順に書く」に置き換える（ログを grep する文は、ブランチの差を見れば要らなくなるので外してよい。残すなら短く）
  - chat-side-operations.md 129行「止まる条件で数値の増減を見るときは、別名の寄せなど正しい理由で動く場合を想定して書く」に「外の通信（HEAD・API）で動く数は、同じ実行をもう1回行い、それでも外れたら止まる、の形にする」を続ける
  - instruction-template.md 12行の「貼る時機:」の規則に「別のセッション（並行）に貼らせるのは、同時に動かすときだけ。前の指示の完了後に貼るなら書かない」を続ける
- 事例は docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」（または該当の節）に、1件につき1〜2行で書く（上の「事例」から。日付 2026-10-13、ACF の振り返り）
- 使う skill は無い

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。「指示文」「止まる条件」「未マージ」「凍結」など）、あれば止まって報告する。chat-side-operations.md・instruction-template.md・handover-archive-2026.md の今の内容（追記先の節）と、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」（サイズの上限）を読む。3文書の今のバイト数をログに書く
2. 上の「書き換える箇所の案」のとおり直す。足す前後のバイト数をログに書く（警告域 26KB・14KB を超えるなら、同じ節の冗長な行を archive へ移して収めるか、収まらなければ止まる）。事例を archive に書く。書き換えた行の全文（前後）をログの `## 経過` に引用する
3. 上の「決定」を docs/decisions/operations.md に足す

## 止まる条件
- 同じ論点の open issue がある
- 書き換える箇所が実物と違い（行の文面が変わっている・無い）、置き換え先を決められない
- 足した後に警告域を超え、同じ節の整理で収まらない（サイズをログに書いて、入れずに判断待ちで止まる）
- 差分が上の3文書・docs/decisions/operations.md・ログ以外に出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（3文書の前後のバイト数を書く）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-ACF-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-ACF-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ed93cb73）: https://github.com/retroeater/mj-logs/tree/main/guide/ed93cb73

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ed93cb73/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7320c071.md
