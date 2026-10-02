# CHAT-1002-INV-01

- 着手日時: 2026-10-02
- 対象issue: Open の issue 全件（変更はしない）
- ブランチ: work/1002-inv
- 着手時HEAD: 9b1b456（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：Open issue 全件の棚卸し（分類表をログに書いて止まる。issue は変えない）
Chat-Ref: CHAT-1002-INV-01
マージ: 判断待ちで止まる（成果物はログの分類表のみ。issue の編集・クローズ・起票はしない）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
Open の issue を全件読み、再編成（クローズ・集約・分割・範囲と期限の更新）の候補を分類表にしてログに書く。実際の変更は、平野さんが表を見て決めたあとの次の指示で行う。

### 決定（2026-10-02、平野さん）
- Open の issue を全件棚卸しし、次の観点で再編成する: (A) 実態はすでに完了しているものはクローズ (B) 内容が重複するものは1つに集約して他をクローズ (C) 内容が相反するものは直近の方針に統一 (D) 大部分が完了して一部だけ残るものは残課題を新規起票して元をクローズ (E) 適用範囲が当初から拡大・縮小しているものはその旨を本文に反映 (F) 期限が適切でないものは新しい期限を設定
- 棚卸しの結果は分類表として提示し、issue への変更は平野さんの判断の後に行う（この指示では変更しない）

### 前提（チャット側。平野さんの決定ではない）
- 件数と中身はチャット側には見えない。本文・コメント（末尾の状況）・docs/handover.md・docs/decisions/・origin/cloudflare の実装（例: 旧表 jpml_titles.html は廃止済み、#441）を材料に分類する
- Google カレンダーに予定がある issue: #97 #124 #142 #269 #297 #298 #304 #327 #357 #390 #413 #426 #434 #438 #441 #445 #448 #450 #453 #457 #459 #461 #472 #473 #476 #482 #485 #486。閉じているものが分かればチャット側が予定を消す
- 「issue のクローズで予定を自動削除する仕組み」の要望が平野さんから出ている（2026-10-02）。#96（カレンダーの参照・更新を自動化）と関係しそうなので、#96 の本文との関係を表の備考に書く

## 手順
1. `gh issue list --state open --limit 500 --json number,title,labels,createdAt,updatedAt`（クラウドセッションは GitHub MCP の相当する操作）で全件を取り、件数とラベル別の件数をログに書く。各 issue は本文と全コメントを読む（長い本文をログに写さない）。重複・集約先・完了済みの判断で過去の issue を見るときは `--state all --search` で探す
2. 1件ずつ、分類（A〜F、または「問題なし」。複数可）・根拠（1行）・提案（クローズ／集約先の番号／新規起票の題と残課題／本文の更新内容／新しい期限の案）を1行にまとめ、番号順の表をログに書く。あわせて次も表の備考に書く: 「状況:」ラベルと中身の食い違い、「着手中」コメントが残ったまま進んでいないもの、親 #296（新サイト送り）との整合、本文に期日が書かれているのに前提のカレンダーの一覧に無い番号
3. 前提のカレンダーの一覧の番号について、Open/Closed と、Closed なら閉じた日を別の表に書く。最後に、表の全体から見た改善の提案（ラベルの整理、親子関係、期限の扱いなど）があれば「## 報告」に短く書いて止まる

## 止まる条件
- 取得が 500 件で切れた（残りの取り方を報告する）
- 他セッションの着手中コメントの確認は不要（issue を変えないため）。ただし、この指示と同じ目的の open issue（issue の棚卸し・整理）があれば番号を報告して続行してよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-INV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-INV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1002-inv
- ログ: https://github.com/retroeater/mj/blob/work/1002-inv/docs/logs/CHAT-1002-INV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-inv
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9b1b456f）: https://github.com/retroeater/mj-logs/tree/main/guide/9b1b456f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9b1b456f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/601aa7ce.md
