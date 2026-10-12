# CHAT-1012-RVC-01

- 着手日時: 2026-10-12
- 対象issue: #492
- ブランチ: work/1012-rvc
- 着手時HEAD: 283713b9d

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書（CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md・docs/instruction-template.md）をレビューして古い事実・重複・冗長を直し、サイズを減らして判断待ちで止まる
Chat-Ref: CHAT-1012-RVC-01
マージ: 判断待ちで止まる（整理後の全文をチャット側が読み比べてから、マージの指示を出す）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-rvc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1012-rvc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-rvc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
容量制限のある4文書を見直し（古い事実・重複・冗長・食い違い）、規則を失わずにサイズを減らす。chat-side-operations.md は警告域（26KB = 26,624 バイト）の1バイト手前で、instruction-template.md も警告域（14KB = 14,336 バイト）に近い。
チャット側の実測（mj-logs の guide/6fd87436 と、REST で読んだ 2026-10-12 21時台の origin/cloudflare は同じ中身）: CLAUDE.md 22,153・handover.md 23,629・chat-side-operations.md 26,623・instruction-template.md 14,013 バイト。
目安（届かなくても規則は消さない）: chat-side-operations.md 24,500 以下・instruction-template.md 13,300 以下・CLAUDE.md 21,500 以下・handover.md は増やさない。

### 決定（2026-09-29、平野さん）
- 「レビュー」は、容量制限のある文書を包括的に見直し（妥当性・重複・冗長・不整合）、サイズを削減する。人間向けの可読性は下がってよく、Claude チャット／Code として問題がなければ表現等を圧縮してよい。文書の変更は判断待ちで止め、整理後の全文をログに貼らせて読み比べてからマージする

### 前提（チャット側。平野さんの決定ではない）
チャット側が mj-logs のガイド（guide/6fd87436）と REST で見つけた候補。実物（その時点の origin/cloudflare）で確かめ、違えば直さずに報告に書く。下の候補以外の重複・冗長も直してよい。
足す行は、`docs/notes/handover-archive-2026.md` の「〜から外した記述」の節を先に確かめ、前に意図して外した行（例: 6章の `docs/review-followup-instructions.md`）を足し戻さない。

- (a) 古い事実（要確認）:
  - a1. handover.md 5章「期限付き・確認待ちタスク」の #298 の行: #298 は 2026-10-10 にクローズされている（REST で確認）。行は「次の Billing の確認は 2026-10-14」だが、docs/decisions/operations.md の 2026-10-12（CHAT-1012-ACF-01）は「次の確認は 10/21」。実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除、2026-10-21）を今どの issue が受け持つかを、#298 のクローズの経緯・コメントと open の issue（#492・#504・#513 など）で確かめる。受け持つ issue があればその番号に直し、無ければ行は直さずに「判断が必要なこと」に書く（この指示では起票しない）
  - a2. CLAUDE.md「作業ログ」節の「規則の削除は #298 の実装4の後半」: a1 の結果に合わせる（受け持つ issue が無ければそのまま）
  - a3. handover.md「最終更新: 2026-10-11」: 本文には 2026-10-12 の変更（5章の #550 の行など）がある。CLAUDE.md「更新ルール」の書き方で直す
  - a4. handover.md 5章 #540 の行の「先に、リーグ推移の特別昇級に紙吹雪を付け…（2026-10-11 の決定）」と「次の会話の順番（2026-10-09）」: 済んだもの・変わったものがあれば直す（docs/decisions/houou.md・features.md と issue の状態で確かめる）
  - a5. handover.md 6章の表に `docs/notes/ask-worker.md` と `docs/notes/sns-book.md` の行が無い（表の冒頭の規則「載っていないものは、開く場面を1行で足す」）。1行ずつ足す
  - a6. CLAUDE.md「CLAUDE.md / handover.md の更新ルール」の最後の項「この3文書には出典としての Chat-Ref を書かない」: 容量制限のある文書は4つ。どの文書を指すかを archive・#492 で確かめ、4文書なら「4文書」に直す。決められなければ直さずに報告に書く
- (b) 重複（片方を正にし、もう片方は削るか参照1つにする）:
  - b1. chat-side-operations.md 冒頭の「上限あり（警告26KB／失敗28KB…）」の3行と CLAUDE.md「更新ルール」: chat-side は参照1行に
  - b2. 待機の上限と共有の定数・関数の全ページ再生成: chat-side-operations.md「止まる条件と検証の指定」の最後の項と instruction-template.md の同じ趣旨の項。どちらか1つに
  - b3. handover.md の chat-side-operations.md への参照（0章「チャット側は…読む」、3章「役割分担」の表と直後の段落、6章の表）: 6章の表以外は1か所に
  - b4. chat-side-operations.md「止まる条件と検証の指定」の「生成物を含む変更のマージの基準」と「『マージ: 承認済み』の条件は…の形で書く」、instruction-template.md の「見込みと比べて止まる条件は、許すずれを数で書く」: 同じ趣旨の部分をまとめる
- (c) 冗長・場面限定の手順の移動（規則と参照1行を残し、本文は移す。移す先が notes なら handover.md 6章の「開く場面」も合わせる）:
  - c1. chat-side-operations.md「コミット履歴を独立に検証するとき（#211）」の節: archive の「コミット履歴の bundle による検証」の近くか docs/notes/branch-operations.md へ移し、chat-side には参照1行
  - c2. chat-side-operations.md「平野さんの判断とマージの許可」の `.claude/` の hook・settings を変える指示の項（3行）: 手順の本文を docs/notes/skills.md へ移し、chat-side には「`.claude/` を変える指示は平野さんの手作業を前提に組み、マージは判断待ち（docs/notes/skills.md）」の1行
  - c3. chat-side-operations.md「止まる条件と検証の指定」の /live・`update-live-channel.yml` に限る2項（【3】の「掲載」、手動実行の入力）: docs/notes/live-channel-write.md・yotei-sheet.md の該当の節へ移し、chat-side には参照1行（「/live・予定表を変える指示の前に live-channel-write.md・yotei-sheet.md の『チャット側の指示』を読む」など）
  - c4. chat-side-operations.md の長い項の圧縮: 「平野さんの判断とマージの許可」の調査だけの指示の項・判断待ちの後のマージ用の指示の項、「書く前に実物で確かめる」の複数のチャット・シートの項、「読み方」の表の Actions の行。規則・条件・issue 番号は残し、言い換えと理由の重複だけを削る
  - c5. instruction-template.md: 「作業ブランチ」の行の文面3つは変えない（写す文面のため）。ほかの項の言い換え・理由の重複を削る
  - c6. CLAUDE.md: 規則を消さずに言い換え・重複だけを削る（目安まで届かなければそのままでよい）
- (d) 圧縮で消してはならないもの: 規則そのもの、issue 番号、止まる条件、決定の内容。迷ったら残して報告に書く。外した括弧内の事例・経緯は handover-archive-2026.md に「2026-10-12 の整理で4文書から外した記述（#492）」の節を作って移す
- 取り込みの衝突: 別のセッションが同じ文書を変えていそうなので、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492 など文書の上限・整理の issue）、この指示と食い違う進行中の作業（着手中のコメントがある等）があれば止まって報告する。`git branch -r --no-merged origin/cloudflare` の各ブランチのうち、4文書（と移す先の notes・archive）を変えているものを一覧にしてログに書く（重なっても止まらない。同じ行・同じ節を変えているものは報告に書く）。#492 に着手中のコメントを残す（本文・状態・ラベルは変えない）
2. 前提 (a)〜(c) を実物で確かめて直し、ほかに見つけた重複・冗長・食い違いも直す。対応表（候補 → 確かめた事実 → 処理〈削除／統合先／参照化／移した先／残した理由〉）を経過に書く
3. 4文書の前後のサイズ（バイト、作業ブランチの上で `wc -c`）を報告に書き、整理後の4文書の全文と、移した先（notes・archive）の差分を経過に貼る。assets-check の上限・警告域に当たらないことを確かめる

## 止まる条件
- 手順1で同じ論点の進行中の作業が見つかった
- 規則を消さないと減らせない箇所がある（消さずに報告に書く。止まるのはその箇所だけで、ほかは進める）
- 取り込みで、両立しない衝突が出た
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしないので通常は起きない）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の「判断が必要なこと」に、マージの可否と a1・a6 の結果を書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-RVC-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-RVC-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1012-rvc
- ログ: https://github.com/retroeater/mj/blob/work/1012-rvc/docs/logs/CHAT-1012-RVC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rvc
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 283713b9）: https://github.com/retroeater/mj-logs/tree/main/guide/283713b9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/283713b9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
