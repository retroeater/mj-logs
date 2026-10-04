# CHAT-1002-CLD-09

- 着手日時: 2026-10-04
- 対象issue: なし（#448 の関連）
- ブランチ: work/1002-cld
- 着手時HEAD: b03045fc

## 指示

【Claude作成】Claude Code 向け指示：CLD のチャットの振り返りの申送り（雛形の読み直しと必須の行の検査、行の指し方）を文書に書き残す Chat-Ref: CHAT-1002-CLD-09 マージ: 判断待ちで止まる（足した文面をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLD のチャット（2026-10-02〜10-04、CHAT-1002-CLD-01〜08、#448 の関連）の振り返りで平野さんが採った規則を、チャット側と受け手側の文書に書き残す（申送り）。規則だけを書き、事例は archive へ置く。
決定（2026-10-04、平野さん）

* チャット側の提案「指示文を作る前に、読んだログの末尾のガイドの版が前回と違えば雛形を読み直す。受け手側で『貼る時機:』などの必須の行が無い指示を報告させる検査にもできる」に「OK」
* チャット側の提案「値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる」への返答:「行を指定するときは動画ID（または行番号）で一意に特定できるようにしてください」
* 動画 `jt4E_u--mxg`（2026-04-17 第6期鸞和戦、D卓4回戦南場の27分の動画）の予定は、「【4】カレンダー非掲載」に入れて、全編（動画 `R3IZ244ANxQ`）だけにする
* 「申送り」（上の規則を文書に書き残す）

前提（チャット側。平野さんの決定ではない）

* 2つ目の決定は、チャット側の提案（層を行ごとに書かせる）に、平野さんが「動画ID（または行番号）で一意に」を加えたものと読んでいる。両方を1つの規則として書く
* 書き場所と文面の案（実物を読んで、既存の項目への統合・置き換えで書く。場所は変えてよい）:
   * (a) docs/notes/chat-side-operations.md「作業ログの読み方」の「ガイド文書は、ログを読んだその場で必要なものを読む」の項に統合: 指示文を作る前に、読んだログの末尾のガイドの版（`guide/<SHA>`）が前回読んだ版と違えば docs/instruction-template.md を読み直す
   * (b) CLAUDE.md の受け手側の規則（「Chat-Ref」節か「作業ログ」節。0章の末尾の確認と同じ場面）: 着手時に、指示文の冒頭に「マージ:」「貼る時機:」「共通手順:」の行があるかを確かめ、無い行があれば、止まらずに経過と報告の「判断が必要なこと」にその行の名前を書く。必須の行の正は docs/instruction-template.md の雛形
   * (c) docs/notes/chat-side-operations.md「止まる条件と検証の指定」か「書く前に実物で確かめる」: シートの行・カレンダーの予定を指すときは、指示文でも平野さんへの報告でも、動画ID（または行番号）で一意に特定する。値の食い違いを調べさせる指示では、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる
* 事例（docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」へ）: CHAT-1002-CLD-07・CLD-08 の指示文に「貼る時機:」の行が無かった（チャット側が、ガイドの版が進んだ後に雛形を読み直していなかった）／CHAT-1002-CLD-04 の表が、足される先の値が【2】由来か【3】由来かを行ごとに区別しておらず、チャット側が「7件が4名に減る」と誤って伝え、CHAT-1002-CLD-05 が手順1で止まった（実際は6件が【2】由来で変わらなかった）
* `jt4E_u--mxg` を【4】に入れるのは平野さんの手作業（この指示の実行時に済んでいるかは分からない）。Code はシートを書き換えない。決定の記録だけを docs/decisions/broadcast-calendar.md に足す
* 受け手側の検査 (b) を入れた後、古い指示文（「貼る時機:」の無い CLD-07 以前の形）が貼られても止まらない

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語は「雛形」「instruction-template」「貼る時機」「必須の行」「動画ID」「行番号」など、変える対象そのものの語を入れる。
2. 追記先（docs/notes/chat-side-operations.md・CLAUDE.md・docs/instruction-template.md・docs/notes/handover-archive-2026.md・docs/decisions/operations.md・docs/decisions/broadcast-calendar.md）の今の内容を読み、上限のある文書は今のバイト数と上限（`assets-check.yml` の警告・失敗の値）を測って書く。前提の (a)(b)(c) を、既存の項目への統合・置き換えで、規則と理由の一句だけで書く（writing-for-agents の skill を使う）。事例は archive へ、決定は docs/decisions/ の分野のファイルへ足す。同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く。
3. 足した・変えた文面を、文書ごとに変更前後が分かる形（差分）でログに貼り、変更後のバイト数を書いて、判断待ちで止まる。

止まる条件

* 手順1で、同じ論点の issue がある
* 追記先の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* 足すと、上限のある文書が警告の値を超える（超えない書き方が無ければ、整理の案を書いて止まる。上限は上げない）
* docs/・CLAUDE.md 以外のファイル（ワークフロー・スクリプト・シート）を変える必要が出た
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、issue の検索の結果、文書ごとの変更前後の文面とバイト数（上限つき）、既存の記述をどう統合・置き換えたか、決定を足したファイルを入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-09` は無し
- 作業ブランチ: `origin/work/1002-cld`（d592a73f）は cloudflare へマージ済み。ローカルも cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で b03045fc へ進めた

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d82931aa）: https://github.com/retroeater/mj-logs/tree/main/guide/d82931aa

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d82931aa/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8cd42e0.md
