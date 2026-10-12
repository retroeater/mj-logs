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

### 着手時の確認

- 0章: ログの「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- Chat-Ref・識別子 RVC の重複: `git log --all -E --grep 'CHAT-[0-9]{4}-RVC-'`・`docs/logs/CHAT-*-RVC-*.md` の履歴とも0件（unshallow 後）
- 作業ブランチ: origin/work/1012-rvc はリモートに無く、ローカルにも無い → `git checkout -b work/1012-rvc origin/cloudflare`（283713b9d）

### 手順1

- issue 検索（「ガイド文書 整理 サイズ 上限 chat-side-operations」）: #492（open）・#421（closed）・#297（closed）。
  #492 の着手中コメントは CHAT-1009-REV-01 のもの（REV-01〜08 で済み、マージ済み）だけで、進行中の作業は無い。2026-10-12 の GCL-03 のコメントは申送り（この整理の依頼）
- #492 に着手中のコメントを残した（https://github.com/retroeater/mj/issues/492#issuecomment-6125007596）。本文・状態・ラベルは変えていない
- `git branch -r --no-merged origin/cloudflare` のうち、4文書と移す先（handover-archive-2026.md・branch-operations.md・skills.md・live-channel-write.md・yotei-sheet.md）を変えているもの:
  - origin/work/1012-abm-map（2026-10-12）: docs/notes/live-channel-write.md の冒頭の「例外: `live-channel-writer` は…」の行と「書き込みを許す先は…」の行（ABEMA ブック、#425）。この指示で足す節（末尾の「未確認のこと」の前）とは別の箇所
  - 4文書を変えている未マージのブランチは無い

### 前提 (a) の確認

- a1: #298 は 2026-10-10T02:50:38Z にクローズ（state_reason: completed）。クローズのイベントはコミット f8e76c033（CHAT-1009-REV-02、件名「docs: fix #298 deadline row and drop review-followup row in handover」）に付いている。
  件名の「fix #298」が GitHub の自動クローズのキーワードに当たり、cloudflare への push で閉じたものと見られる（クローズのコメントは無く、最後のコメントは 2026-10-07 の RVW-22「実装4の前半」）。
  実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除）を受け持つ open の issue: #504・#513・#492 の本文とコメントを「実装4」「sync-logs.yml」「MJ_LOGS_TOKEN」「[sync-logs]」で検索 → #504 に `sync-logs.yml` が起動の表から外れた注記があるだけで、削除を受け持つ記述は無い。**受け持つ issue は無い** → handover の行は直さない（判断が必要なことに書く）。
  あわせて、行の「次の Billing の確認は 2026-10-14」は docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-01）」の「次の確認は 10/21」と食い違う（行ごと直さない指示のため、これも判断が必要なことに書く）
- a2: a1 で受け持つ issue が無いため、CLAUDE.md「作業ログ」節の「#298 の実装4の後半」はそのまま
- a3: handover.md「最終更新: 2026-10-11」。2026-10-12 に ec1f49f0e（#550 の行を足した）がある → 2026-10-12 に直し、3行の最古（#518 の houou/ を未公開で入れた）を外して #550 の行に置き換える
- a4: 紙吹雪（特別昇級）: 84577fda5「feat: add houou card open marks, ranking notation, jump confetti (#518)」は origin/cloudflare の祖先。docs/decisions/houou.md「2026-10-11（CHAT-1011-HOU-14）」に「リーグ推移の演出（特別昇級の紙吹雪を含む）は OK」「上を直してマージしてよい」。済んでいる → #540 の行から外す。
  「次の会話の順番（2026-10-09）」: #388・#389 は open・状況: 待ち、#497・#488 は open。変わっていない → そのまま
- a5: `ls docs/notes/` のうち handover.md に無いのは ask-worker.md・sns-book.md の2つ（指示のとおり）→ 1行ずつ足す
- a6: 「3文書に出典の Chat-Ref を書かない」は 2026-09-22 の平野さんの判断（94144c055「docs: ガイド文書3つから出典としてのChat-Refを外す」。対象は CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md。archive「2026-09-28 の整理で CLAUDE.md から外した記述（#297）」）。
  instruction-template.md はその後（#492、CHAT-1010-REV-06）に上限の対象に入ったが、この規則を広げた決定は archive・#492 に見つからない → 「4文書」には直さず、3つの文書名を明記して「この3文書」の指す先の曖昧さだけを除く（判断が必要なことに書く）。今の instruction-template.md に出典の Chat-Ref は無い（`CHAT-MMDD-XXX-nn` の書式だけ）

### 手順2: 対応表

成果物のコミットは ログと別（件名「docs: review the four size-limited guide documents (#492)」）。外した括弧書き・言い換えは `docs/notes/handover-archive-2026.md`「2026-10-12 の整理で4文書から外した記述（#492）」に書いた。

| 候補 | 確かめた事実 | 処理 |
|---|---|---|
| a1 handover 5章 #298 の行 | #298 はコミット f8e76c033 の「fix #298」で自動クローズと見られる。実装4の後半を受け持つ open の issue は無い。Billing の次の確認は decisions では 10/21、行は 10/14 | 直さない（判断が必要なことへ） |
| a2 CLAUDE.md「作業ログ」の #298 | a1 のとおり | そのまま |
| a3 handover「最終更新」 | 2026-10-12 に #550 の行（ec1f49f0e） | 2026-10-12 に直し、3行の最古（#518）を外して #550 を足した |
| a4 #540 の紙吹雪・次の会話の順番 | 紙吹雪は 84577fda5 で cloudflare に入り、HOU-14 で OK。#388・#389・#497・#488 は open のまま | 紙吹雪の一文を削除（archive へ）。順番はそのまま |
| a5 6章の表 | ask-worker.md・sns-book.md が無い | 1行ずつ足した |
| a6 「この3文書」 | 2026-09-22 の判断の対象は CLAUDE.md・handover.md・chat-side-operations.md。4文書に広げた決定は見つからない | 「4文書」にせず、3つの名前を書いた（判断が必要なことへ） |
| b1 chat-side 冒頭の上限3行 | CLAUDE.md「更新ルール」と重複 | 参照1行にした（上限の数値・「上限を上げずに整理」を外した） |
| b2 待機の上限・全ページ再生成 | chat-side「止まる条件と検証の指定」の最後の項と template の項が同じ趣旨 | chat-side から削除。template の項を正にした（「15分」を足した） |
| b3 handover の chat-side への参照 | 0章・3章の表・3章の段落・6章の表の4か所 | 3章の2か所を削除。0章と6章の表を残した |
| b4 マージの基準・承認済みの条件・許すずれ | chat-side の2項と template の1項 | chat-side の1項にまとめ、template の項を削除（雛形の「止まる条件」の例示の行は残した）。template 冒頭の「正」の列挙に「止まる条件と検証の指定」を足した |
| c1 コミット履歴の検証（#211） | archive「コミット履歴の bundle による検証」が chat-side の節を参照していた | 本文を archive の同じ節へ移し、chat-side は「読み方」の表の issue・コミット・ブランチの行に参照を足した |
| c2 `.claude/` を変える指示 | skills.md「git の hook（mj-git-guard）」に同じ趣旨（Self-Modification で拒否、平野さんが PR でマージ） | chat-side は1行に。「Create a new branch」の確認と「マージ: 判断待ち」を skills.md の同じ項へ移した。handover 6章 skills.md の行に「`.claude/` を変える指示の前も」 |
| c3 /live の2項 | live-channel-write.md・yotei-sheet.md に「チャット側の指示」の節は無かった | live-channel-write.md に「チャット側の指示」の節を作って2項を移した（手動実行の入力は yotei-sheet.md「手動実行」を参照）。chat-side は参照1行。handover 6章 live-channel-write.md の行に「チャット側が /live の指示を書く前」 |
| c4 chat-side の長い項 | 調査だけの指示・判断待ちの後のマージ用の指示・複数のチャット・シート・Actions の行 | 言い換え・理由の重複を削った（条件・issue 番号は残した）。ほかに「事実は実物で」「触れる領域の資料」「確認用 URL」「設定の変更」「見た目」「スプレッドシート」「途中切れ」の項も圧縮 |
| c5 template | 作業ブランチの文面3つは変えていない | 冒頭の2段落を1段落に、雛形の注記の参照を外し、追記・着手中コメント・完了条件・欠番・貼る時機・手順1の項を圧縮 |
| c6 CLAUDE.md | — | 上限の書き方をまとめ、着手中コメントのセッション URL の項と「構成は」の項を圧縮（−45）。21,500 には届かない（規則を消さないと減らせないため、ここで止めた） |
| ほか（handover） | 3章の Rebuild・gh の認証の行は 6章 session-network.md の行と重複 | Codespace の括弧書きに寄せた。4章の「具体的には、次のような判断をしてきた。」を「これまでの判断:」に |

### 手順3: サイズ（バイト、work/1012-rvc の上で `wc -c`）

| 文書 | 前（283713b9d） | 後 | 差 | 目安 | 警告域・上限 |
|---|---:|---:|---:|---:|---|
| CLAUDE.md | 22,153 | 22,108 | −45 | 21,500 以下（届かず） | 26,624・28,672 |
| docs/handover.md | 23,629 | 23,586 | −43 | 増やさない | 26,624・28,672 |
| docs/notes/chat-side-operations.md | 26,623 | 24,711 | −1,912 | 24,500 以下（211 届かず） | 26,624・28,672 |
| docs/instruction-template.md | 14,013 | 13,147 | −866 | 13,300 以下 | 14,336・16,384 |
| 計 | 96,418 | 93,552 | −2,866 | | |
| docs/notes/handover-archive-2026.md | 90,471 | 94,173 | +3,702 | | 上限なし |
| docs/notes/skills.md | 11,816 | 12,201 | +385 | | |
| docs/notes/live-channel-write.md | 31,064 | 31,730 | +666 | | |

- 4文書とも assets-check の警告域の外（`assets-check.yml` の check と同じ値で比べた）。`python3 scripts/check_asset_limits.py` は rc=0。`python3 -m unittest discover -s scripts/tests` は 907件 OK
- 取り込み: 成果物のコミットの後に `git fetch origin cloudflare` → origin/cloudflare は 283713b9d から進んでおらず、取り込みは不要（衝突なし）

### 手順3: 整理後の4文書の全文（work/1012-rvc の成果物のコミット）

#### CLAUDE.md

`````markdown
# CLAUDE.md

# ryoei.pro

## 概要

日本プロ麻雀連盟の選手データベースを含む個人サイト。

### 引き継ぎ

経緯・現状・次にやることは docs/handover.md（文脈が要るときはまず読む）。
**起動時に読み込んだこのファイルは古いことがある**（確かめ方: docs/notes/branch-operations.md「起動時に読み込んだ CLAUDE.md が古くないか確かめる（Codespace）」）。

### 構成
- 静的HTML。ビルド工程なし。ページの一覧はdocs/notes/static-generation.md「ページの一覧」（件数の正は`python3 scripts/regenerate.py --list`）
- `llms.txt`（AIクローラー向けのページ索引）は手書きの静的ファイル1枚で、生成スクリプトは持たない（#161）
- Cloudflare Workersの静的アセットとして配信（`wrangler.jsonc`、assets.directory は `./`）
- **本番反映は Cloudflare Workers Builds（ダッシュボードのGit連携）が `cloudflare` への push を検知して行う**（`chore: regenerate ...` も含む。
  Actionsにデプロイのジョブは無い、#169。設定はダッシュボード側。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- Bootstrap 5.3.8 をローカル配信（assets/vendor）。CDNは使わない
- ページ本体（例: `jpml_pros.html`）とロジック（同名の `.js`）は分ける。ページ末尾で navbar.js を読み込んで共通ナビを描画する
- **手書きHTMLを新規に追加する前に**docs/notes/static-generation.md「navbar.js と検索欄」を読む（hrefはルート相対〈#162〉、`data-search="off"`〈#163〉）
- skill（`.claude/skills/`、plugin は使わない）と git の hook（`.claude/hooks/`）の導入・入れ直しはdocs/notes/skills.md

### データの流れ
- 選手データ・成績データはすべてGoogleスプレッドシートが正本。ビルド時生成のページは`scripts/generate_<ページ名>.py`が読んで焼き込む
  （型ごとのページと仕組みはdocs/notes/static-generation.md「ページの一覧」「現行の仕組み」）
- **型Cの選手選択リストに退会済みの選手が出ないのは正しい挙動**（同「ページ側のJS」、#168）。Google Charts依存の6ページは旧方式（#7）
- 選手のプロフィール画像はX(pbs.twimg.com)など外部ドメインに依存し、リンク切れしやすい

### メンテナンス用スクリプト（scripts/）
- `scripts/`は`.assetsignore`で公開対象外。説明・使い方はdocs/notes/static-generation.md「メンテナンス用スクリプトの詳細」
- ページの再生成は`python3 scripts/regenerate.py <ページ名>`（`all`で全ページ、`--list`で対象一覧、`--changed`で変更ファイルから判定）

## 方針

### 応答について
- 日本語で応答すること

### 判断・作業の原則
- **セッションから到達できない領域（Cloudflareダッシュボード、ブラウザでの本番の見え方など）の状態を、到達できないことを根拠に「無い」と結論づけない。**
  確かめられる手段（check-runs、issueの検索など）を試し、残りは平野さんに確認する（#169）。平野さんの目視申告値は
  「申告値ではこうなっている。ここからは検証できない」と書く（#312）。文書の「できない」を実測の代わりにしない（環境は変わる）
- **check-runsの成功だけで「本番反映を確認した」と報告しない**（「本番のHTML」と「ブラウザでの見え方」は確認できる範囲が違う。docs/notes/cloudflare.md「ビルド成否と本番の確認範囲（check-runs）」）
- **修正の検証は、先に「修正前のコードでも通らないか」を確かめる**（#310）。実データに依存する検証は、実施時点で前提を確かめる
- 外部ドメインへの依存を増やさない（CSP導入を予定しているため）
- `.assetsignore` に開発用ファイルを列挙。公開対象を増やさない。新しいディレクトリ・ファイルは公開してよいか確認し、公開しないものは追加する（#133）
- ビルド・lint の自動化コマンドはなし。HTML/JSの変更はブラウザで直接確認する
- 外部の状態を待つ待機は上限15分（超えたら状態を書き「未確認の項目」に回して進む。自分のコマンドの実行は対象外）。共有の定数・関数を変えるときは参照を洗い出し、
  マージ前に全ページを再生成して差分を確かめる（docs/notes/static-generation.md「ワークフローを手動実行するとき」「生成スクリプトの構成」）
- テストは`scripts/tests/`（unittest、標準ライブラリのみ）。実行は`python3 -m unittest discover -s scripts/tests`。週次の`check-meibo.yml`も実行する

### 禁止事項（理由は参照先）
- `gh-pages` ブランチに触らない（docs/handover.md「gh-pages ブランチは触らない」）
- `CLOUDFLARE_API_TOKEN` をGitHub Secretに登録しない。到達できても `wrangler deploy` しない（二重デプロイ。docs/notes/cloudflare.md「本番反映（デプロイ）の仕組み」）
- `_redirects` 先頭の `/  /index.html  200` を消さない（トップページが404になる。docs/notes/cloudflare.md「配信設定: html_handling・_redirects・canonical・_headers」）
- `wrangler dev` は必ず `npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で起動する（素だと無限リロード。docs/notes/cloudflare.md「ローカル確認（wrangler dev）」）
- assets/vendor 配下に `sourceMappingURL` コメントを残さない（#99）。更新時はファイルのパスを変える（30日キャッシュ、#92）
- sitemap の lastmod を手で書き換えない（`scripts/update_sitemap_lastmod.py --from-git`が導出する。docs/notes/sitemap-lastmod.md、#265）
- `git stash` を使わない
- 未コミットの変更がある作業ツリーで、`git checkout HEAD -- .`・`git reset --hard`・`git clean` などまとめて消すコマンドを使わない
  （docs/notes/branch-operations.md「未コミットの変更を戻すとき」）

## ブランチ運用

複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）。場面ごとの手順はdocs/notes/branch-operations.md。
**チャット側の指示文がこの節と食い違う（`cloudflare`上での直接作業など古い前提を含む）ときは、指示文には従わずこの節に従うこと**（#205）。

- **`cloudflare`: 統合・デプロイ専用。セッションはここへ直接pushしない**（マージ＝本番反映）
- **`work/<識別子>`: セッションの作業ブランチ。** 識別子は Chat-Ref から取る（`CHAT-0913-QMX-02`なら`work/0913-qmx`）。指示文に指定が無くても切る。
  複数issueを1ブランチで扱ってよいが、作業に関係しない独立した変更（ルール追記・ドキュメントのみの修正等）は別ブランチに分ける
- **Codespace: `/workspaces/mj`は全セッションが共有する。`origin/cloudflare`を明示して作った worktree の中で作業し、`/workspaces/mj`自身では`git checkout`/`git switch`を行わない**
  （手順・片付けは同「作業ディレクトリの分離（Codespace）」）。**クラウドセッション（`/workspaces/mj`・worktree・`gh`が無い）の読み替えはdocs/notes/cloud-sessions.md**
- **成果物の`cloudflare`へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。** 無ければ完了を報告し、判断待ちで止まる。
  承認は処理中に求めない。ドキュメントのみの変更は完了報告のうえマージしてよい。承認済みでも、確認が1つでも通らない・止まる条件に当たった・
  前提が崩れたときはマージせずに報告する（同「マージするとき」）
- **マージは`git push origin <作業ブランチ>:cloudflare`で行い、push直前に再fetchして`git merge-base --is-ancestor origin/cloudflare HEAD`を確かめる**（同「マージするとき」）
- **`.github/workflows/`を追加・変更する作業では、着手時に同「ワークフローを変更したとき」を読み、マージ前に作業ブランチで手動実行して確かめる。実行できなければ報告して判断を仰ぐ**
- 長期間マージされないブランチは、定期的に`cloudflare`を取り込んで乖離を小さく保つ
- **`origin/cloudflare`の取り込みで衝突したら、同「生成物を含む作業ブランチを取り込む・マージするとき」に従う**（生成されたページだけなら取り込んだ後のスクリプトで生成し直して解く。
  それ以外が衝突したら止まる。指示文が解き方を書いている衝突はそのとおりに解き、該当箇所をログに引用する）
- **ブランチを削除する前に同「ブランチを削除するとき」を読む。読むまで削除しない。** 未マージのブランチは削除しない。削除直前の先頭SHAを記録に残す（#207）
- **このブランチ運用ルールに反した作業（自他を問わない）は#176にコメントで記録する**（記録対象と書式は#176の「スコープ変更」節）

## Chat-Ref

チャット（claude.ai）で作った指示文に付く識別子（`CHAT-MMDD-XXX-nn`: 発行日・チャットセッション識別子・連番）。新しい識別子は英大文字3文字。
既存の識別子（2文字、`A`・`DOC`・`K7`・`W2` など）は有効なままで、重ねない（#474）。チャット側の規則はdocs/notes/chat-side-operations.md。

受け取る側（Claude Code）:

- **着手前の確認**（コマンドと項目はdocs/notes/branch-operations.md「Chat-Ref の着手前の確認」）。当たれば着手せず報告して止まる:
  - 同じChat-Refのコミットがある（`git log --all --grep="<Chat-Ref>"`。同じ指示文が再度貼られることがあり、追記は冪等でないため）。issue操作のみの作業は対象issueの既存コメントも見る。再開も同じ確認による
  - そのセッションの最初の指示（撤回の欠番があれば`02`以降）で、`XXX`が他のセッションで使われている（全ブランチのコミットと`docs/logs/`の履歴。`MMDD`が違っても重複させない）
  - 指示文に実装が含まれ、0章ゲート（対象issue・文書サイズ・他セッションの作業・未マージブランチとの重なり、#265・#336）に食い違いや重なりがある
- **指示文に書かれた事実はチャット側が会話の記憶から書いたもので、誤っていることがある**（#319）。前提と実物が食い違ったら、指示に合わせて手を入れず、中断して報告する
- **指示文の記述同士が食い違ったときは、個別の指定より前提・ルールの側を優先し、その旨を報告する**
- **着手時に、指示文の冒頭に docs/instruction-template.md の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が揃っているかを確かめ、
  無い行があれば止まらずに、その行の名前をログの経過と `## 報告` の「判断が必要なこと」に書く**
- **実行しない判断をした指示も、その旨を平野さんに伝える**（黙って落とすと「貼り忘れ」と区別がつかない）
- **コミットメッセージの末尾にはトレーラをこの並びで入れる**（`Chat-Ref`はChat-Refを含む指示のときだけ。`Claude-Session`は常に付ける。
  **モデル名は例を写さず、そのセッションで実際に動作しているもの**）:

  ```
  Chat-Ref: CHAT-MMDD-XXX-nn
  Co-Authored-By: <モデル名> <noreply@anthropic.com>
  Claude-Session: https://claude.ai/code/session_<ID>
  ```
- **コミットを伴わない指示（issueの起票・編集のみ、確認のみ）では、関係するissueのコメント末尾に`Chat-Ref:`行を書く**
- 撤回されたChat-Refの番号は欠番とし、再利用しない

## 作業ログ

指示1件ごとの経過と報告を残す（全セッション共通）。書き方・最終報告の形は`docs/logs/_template.md`、寿命はdocs/notes/branch-operations.md「作業ログの寿命」。
`docs/logs/`は公開されないが、push したログとガイド文書は public の`retroeater/mj-logs`に写る（mj-logs の`sync-from-mj.yml`、#440・#298）。
**ログに人の個人情報（氏名と結びついた属性など）・鍵やトークンの値・非公開の URL（Claude Code のセッション URL は可）を書かない。**
プレビューの URL（Workers Builds の別名 URL など）も非公開として扱い、ターミナルへの最終報告にだけ書く。

- **1指示につき1ファイル。パスは`docs/logs/<Chat-Ref>.md`**
- **ログのpushは、調査・計測・実装より先に行う最初の手順とする。** Chat-Refの重複確認の直後に、ヘッダと`## 指示`だけのログを
  単独でコミットしてpushし、それが済むまで他の作業（読み込み・計測・編集・ブランチ操作）を始めない（issueの着手コメントだけは同時でよい）。
  以降は節目ごとに追記してpushし、完了時に仕上げてpushする（セッションが失われても記録が残るように）
- **着手時と、作業を終える（完了・判断待ち・中断）最後のpushのコミットメッセージ本文に`[sync-logs]`を入れる。**
  途中の節目のpushには付けない（今の写しは目印を見ないが、規則の削除は #298 の実装4の後半）
- 構成は ヘッダ・`## 指示`〈貼られた指示文をそのまま〉・`## 経過`〈詳細はすべてここ〉・`## 報告`（`_template.md`をコピー）。
  **`## 報告`はログの末尾に置き、作業の最後に更新してpushする。** チャット側はこの節だけを読むため、**10項目を省かず、該当が無ければ「なし」と書く**。
  状態の書き方（「完了」の条件・` / 続き: CHAT-…`）は`_template.md`と同「作業ログの寿命」
- **ログの節を書き換えるときは、`## 指示`欄より後ろの見出しを相手にし（`## 報告`はファイルの最後の一致）、push 前に`## 指示`欄が変わっていないことを確かめる**（理由は`_template.md`）
- ログは作業ブランチにだけpushし、成果物と別のコミットにする。`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる（マージの結果を書く docs/logs のみの追いのpushは可）
- 後の指示で使うスクリプト・中間データの置き場所は docs/notes/session-network.md「作業ファイルの置き場所」（scratchpad は再起動で消える）
- **ターミナルへ返す最終報告は、状態・ログのURL・ブランチ・（あれば）確認用・ログ（公開）・Chat-Ref の行だけにする**（形と書き方は`_template.md`「ターミナルへ返す最終報告」）。
  最後の行は`Chat-Ref: CHAT-MMDD-XXX-nn`、その直前は「ログ（公開）」の行（mj-logs で今回の版が返ることを確かめてから書く）。判断が必要なこと・エラーを含め、詳細はログの`## 報告`に書く
- **例外として、次の2つはログに届かないためターミナルに内容を書く:** 作業途中で平野さんに質問して止まるとき／pushに失敗したとき
- **指示の完了時（完了・判断待ち・中断の最後の push）に、その指示の「決定」節と作業中の平野さんの回答（grill を含む）を
  `docs/decisions/<分野>.md` に足す**（ログと同じコミットでよい。書き方は`docs/decisions/README.md`）

## issueの着手ルール
- **issueに着手したら、コードを触る前にそのissueへ「着手中」のコメントを残す**（並行するセッションから着手状況を知る唯一の手段）。
  セッションのURL（`Claude-Session` と同じ。取得できなければ Chat-Ref の `XXX`）を含める
- **着手する前に、そのissueに他セッションの着手中コメントが無いか確認する。** あれば着手せず、ユーザーに確認する（#157）
- **issueを新規作成する前に、同じ主題のissueをクローズ済みも含めて検索する**（`gh issue list --state all --search "<キーワード>"`）。
  **issue の起票やページ・機能を作る指示では、着手の前に同じ目的の issue と未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチの
  コミットの件名とログ）を確かめ、見つかったら作る前に止まって報告する**（0章ゲートは触るファイルの重なりを見るもので、目的の重なりは拾えない）
- **作業を中断・放棄したときも、その旨をコメントに残す**（着手中のまま放置されると、他セッションが着手を見送り続ける）
- **issueをクローズするときは「状況:」ラベル（待ち/対応中/保留）を外す**（#112）
- タスクはGitHub Issuesで管理し、Projects のボードは使わない。優先順位と状況は issue の本文・ラベル・期日で表す

## 新サイト送りの issue（親 #296）
- **issue を新サイト送りにする・取り消す前に #296 の本文「新サイト送りにするとき・やめるとき」を読む。**
  コメント「親: #296」と sub-issue の登録は片方だけにしない

## コード規約
- 着手中のissueと無関係なコードは触らない。自分が書いた・変更した箇所以外にコメントを足さない。変更行数は最小にする
- 繰り返し使う値・意味のある値（列番号、画像サイズ、URL、閾値）は定数にする。一度きりで自明な値はインラインでよい
- ネストを深くしない。早期return / continueを使う
- JSのif文は1行でも必ず`{}`を付ける
- コメントは「何を・なぜ」を短く。「どうやって」はコードに語らせる
- 人が読む文章（コメント、コミットメッセージ、応答）は最小限の語数で。賞賛・相槌は不要
- スプレッドシートのファイルは「ブック」と書く（「冊」で数えない）

## コミットのルール
- コミット前に `git status` / `git diff --stat` を確認し、**着手中のissueと無関係なファイル・ハンクを含めない。**
  複数の変更が混ざっていたらissueごとに分けてコミットする（#157）
- コミットメッセージ: 件名は50字目安（72字上限）・命令形・末尾ピリオドなし、空行を挟んで本文に「何を・なぜ」。
  既存の`chore:`等の接頭辞形式を維持し、トレーラは「Chat-Ref」節のとおり入れる
- **文章（ログ・issue本文・コメント・コミットメッセージ）をシェル経由で書かない**（引用符なしのヒアドキュメントや`"..."`ではバッククォートと`$`が展開される）。
  ファイルは Write / Edit で書く。ヒアドキュメントは必ず引用符付き（`<<'EOF'`、変数が要っても外さない）。issue の本文・コメントは `--body-file`、コミットメッセージは `git commit -F - <<'EOF'` で渡す

## CLAUDE.md / handover.md の更新ルール
- ページの移行・追加・削除を行ったときは、同じコミットで docs/notes/static-generation.md「ページの一覧」の件数・ページ列挙と`llms.txt`（手書き、#161）を更新する。
  **新しいページは未公開で入れ、`llms.txt`・navbar・サイトマップには公開の issue で載せる**（docs/new-page-checklist.md、#243）。
  CLAUDE.md にはページを列挙しない。型が増えたときだけ「データの流れ」を直す（#136）
- **docs/handover.md は「現状・ルール・次にやること」のみを書く。** 実装の詳細は issue のコメントか `docs/notes/<topic>.md` へ、handover には結論1〜2行と参照だけ
- 記述を更新するときは古い記述を消して置き換える（追記型にしない）。同じ内容を2箇所に書かず、片方は参照にする
- handover.md / CLAUDE.md をissueやコメントから参照するときは、行番号ではなく節・項目の見出しで書く
- 「最終更新」は日付＋直近の変更3行以内。外した行は archive へ移さず消してよい
- 上限はファイルごとに **CLAUDE.md・handover.md・docs/notes/chat-side-operations.md 各28KB（警告域26KB）・docs/instruction-template.md 16KB（警告域14KB）**
  （`assets-check.yml`、1KB=1024バイト。**作業する worktree の上で測る**）。
  警告域に近づいたら、足す前に `docs/notes/` へ移す。**警告が出たら、上限を上げずに4文書とも整理する**（同じ趣旨の記述をまとめ、
  事例は `handover-archive-2026.md` へ、場面限定の手順は `docs/notes/` へ。退避先には上限を置かない、#421）。
  上限は下げる方向にだけ動かし、上げるのは平野さんの判断。最終目標は CLAUDE.md 20KB・handover.md 24KB 前後（#292・#301 が進んだ時点で見直す、#492）
- **CLAUDE.md・handover.md・chat-side-operations.md には出典としてのChat-Refを書かない**（ログは定期削除で消え、行き先の無い参照になる。issue番号は書いてよい）
`````

#### docs/handover.md

`````markdown
# 引継ぎメモ

新しい会話でこのプロジェクトを再開するときに、最初に読む文書。
**このファイルを読めば、それまでの経緯を知らなくても作業を再開できる**ことを目的にしている。

**この文書は現状・ルール・次にやることだけを書く。** 実装記録は `docs/notes/`、issue単位の経緯は GitHub Issues、過去の履歴は
`docs/notes/handover-archive-2026.md`。容量の上限と退避方法は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」。

最終更新: 2026-10-12

- **アクセス解析の導入の検討（#550）を「次にやること」に足した**
- **最強戦の新しい年度・対局・卓のページ `saikyo/<年度>/`（#425、878ページ）を未公開の形（noindex・どこからも未リンク）で本番に入れた。公開は #549**
- **`houou/` に「別名」の名寄せ・A1・A2 の期の書き方（暫定、#541）・個人成績とランキングの直しを入れた（未公開のまま）**

---

## 0. 新しい会話の始め方

会話開始時に読むのは `docs/handover.md` のみ（平野さんが毎回定型文を貼る前提にしない）。
**マージ・ブランチ操作・ルール追記を行う（指示する）前と会話が長くなったときは、CLAUDE.md の該当節（「ブランチ運用」「Chat-Ref」「CLAUDE.md / handover.md の更新ルール」）も読み直す**（並行セッションが途中でも更新する、#313）。

**チャット側（claude.ai）は `docs/notes/chat-side-operations.md` を読む**（#294）。

**大きな作業の区切りごとに新しい会話を始める**とよい（長い会話は1回あたりのコストが上がる）。

---

## 1. このプロジェクトは何か

平野良栄（日本プロ麻雀連盟のプロ雀士・理事）の個人サイト `ryoei.pro` の改善。

中心となるコンテンツは**日本プロ麻雀連盟のプロ雀士1,100名超のデータベース**と、
鳳凰戦・女流桜花などの成績記録。

### 経緯

GitHub Pages からの移行の相談に始まり、Cloudflare への移行の中で50件以上の改善を行った。
2026年9月9日にドメイン切替が完了し、**現在は Cloudflare Workers で配信中**。

---

## 2. いまの構成

### 配信

| 項目 | 内容 |
|---|---|
| リポジトリ | `retroeater/mj`。**private**（#211） |
| 本番 | Cloudflare Workers（静的アセット配信）。`cloudflare` ブランチ |
| ドメイン | `ryoei.pro` / `www.ryoei.pro`。DNS・レジストラともCloudflare |
| 旧環境 | GitHub Pages。**2026-09-21 に無効化済み**（#84）。`gh-pages` ブランチは履歴として残す |
| ビルド | **なし**。静的ファイルをそのまま配信する |
| プラン | **Cloudflare Pro**（2026年9月9日〜）。$25/月 |

`wrangler.jsonc` の `assets.directory` がリポジトリ全体（`./`）を指すため、
公開したくないファイルは `.assetsignore` に列挙している（追加時のルールは CLAUDE.md「方針」、#133）。

`html_handling`・`_redirects` の先頭行の規則は CLAUDE.md「禁止事項」。canonical は付けない（#113）。例外は `wayhome/` の個別ページで、URL変種を持たないため canonical を持つ（#162）。
`_headers` はセキュリティヘッダ5件とキャッシュ制御（#92）。設定値と経緯は `docs/notes/cloudflare.md`「配信設定: html_handling・_redirects・canonical・_headers」

**本番反映**は CLAUDE.md「構成」（ゲートは #170）。設定値・確認範囲は `docs/notes/cloudflare.md`「本番反映（デプロイ）の仕組み」、セッションからの到達は `docs/notes/session-network.md`（#328）。

### ページ構成

系統は `index.html`（テンプレート由来）・ビルド時生成（型A/A'/C/D と、`wayhome/`・`saikyo/`・`title/`・`live/`・`books/` のサブディレクトリ）・
Google Charts依存（#7の対象）・静的なページ の4つ。**件数の正は `python3 scripts/regenerate.py --list`。**
ページの一覧（ページ数・生成スクリプト・公開状態）は `docs/notes/static-generation.md`「ページの一覧」。
**`books/`は2026-09-22開発凍結（自動生成・自動取得を停止、本番はそのまま残す）。`docs/notes/books-freeze.md`。**

### データの流れ

選手データや成績はすべて**Googleスプレッドシート**にある（生成スクリプトが読むブックは7つ。ほかに連盟員名簿のブック1つを `generate_jpml_pros.py`〈英字の姓名・所属、#536〉・`check_meibo.py`・`sync_birthday_calendar.py` が読む）。

- ビルド時生成・Google Charts依存のページの仕組みは CLAUDE.md「データの流れ」。出力がディレクトリになるページの出力先の正は `scripts/regenerate.py` の `OUTPUT_OVERRIDES`
- `jpml_pros`のYouTubeアイコンだけはYouTube Data APIから取る（#3。取得条件は `docs/notes/static-generation.md`「生成スクリプトの構成（lib/page.py）」）
- `saikyo_pages`は生成する環境で選手写真の結果が揺らぐ。**本番はActionsの生成が正**（`docs/notes/saikyo-page-design.md`「7. 選手写真の更新」）

**スプレッドシートを直しただけでは、ビルド時生成のページには反映されない。** 即時に反映したいときは
`regenerate-page.yml` を`workflow_dispatch`で手動実行する（`target_page`にページ名、または`all`）。
週次（毎週月曜05:37 JST、#103）で`all`が走る。最強戦（saikyo/）は毎朝、写真のリンク切れ検知の後にも再生成する（`regenerate-saikyo.yml`、#537）。セッション内でも `python3 scripts/regenerate.py <ページ名>` で生成できる。

### 自動化

ワークフローの一覧（実行の契機と内容）は `docs/notes/static-generation.md`「ワークフローの一覧」。正は `.github/workflows/`。
**手動実行の前に同「ワークフローを手動実行するとき」を読む**（古い作業ブランチから実行すると、そのブランチの状態で生成物や常設issueが上書きされる）。

---

## 3. 作業の進め方

### 役割分担

| 場所 | 担当する作業 |
|---|---|
| **Claudeとのチャット** | 設計の相談、調査、原因の切り分け、実装案・指示文の作成 |
| **Claude Code** | ファイルの編集、issue操作、コミット・push。コミットの到達確認・差分・マージ判定も行い、結果をログで報告する |

Claude Code の実行環境は Codespace（`/workspaces/mj` で `claude`、`gh` 認証済み。Rebuild・gh の認証は `docs/notes/session-network.md`）かクラウドセッション（`docs/notes/cloud-sessions.md`）。
Chat-Ref・並行作業の規則は CLAUDE.md「Chat-Ref」「ブランチ運用」、決まるまでの経緯は archive。

### skill と hook

skill は `.claude/skills/`（`/grill-me`・`/grill-with-docs` など）、git の危険な操作を止める hook は `.claude/hooks/mj-git-guard.py` に置く。
hook の判定は deny（実行させない）だけで、確認（ask）は出さない。入れ方・更新・判定一覧は `docs/notes/skills.md`。

### タスク管理

**GitHub Issues** で管理する（Projects のボードは 2026-10-03 から使わない。着手中コメントなどの規則は CLAUDE.md「issueの着手ルール」、#157）。

- ラベルは3系統。コロンの後に半角空白が入る（例: `分野: 整理・保守`。正は`gh label list`）:
  `状況:`（対応中、保留、待ち。未着手はラベルなし）、
  `分野:`（SEO/AIO、パフォーマンス、自動化、セキュリティ、整理・保守、インフラ、UI/UX）、
  `対象:`（ページ名。jpml_pros、index、全ページ など）
- 完了分もcloseした状態で残す（判断の経緯を後から追えるように）。エクスポートファイルは廃止（#210）
- **新サイト全体の親 issue は #296（sub-issue 21件）。** #101 はトップページの作り直しに限る。新サイト送りの手順は #296 の本文
  「新サイト送りにするとき・やめるとき」
- ユーザ登録（サーバ側のアカウント）が前提の機能は #394（保留）に blocked by で依存させる
- 月次の手作業（AI言及・Core Web Vitals・デバイス比率・robots.txt差分・Cloudflare の設定と記録の照合）は
  #304 に集約し、実施ごとにコメントを残す

---

## 4. 押さえておくべき方針

### 現行サイトに作り込みすぎない

**新サイトを別途新規構築する方針**が決まっている（`docs/new-site-design.md`）。
現行サイトはいずれ役目を終えるため、大きな投資は避ける。これまでの判断:

- 構造化データ（#13）は現行サイトでは見送り、新サイトで対応
- 共有ボタン: 新サイトの選手個別ページは #82（新サイト送り）。現行サイトは live/・saikyo/（対局ごとの共有だけ）に共通の部品（#409。`scripts/lib/share.py`・`assets/share.js`・`style.css`）。title/・wayhome/（帰り道）と saikyo/ のページ全体の共有は外した。見直しは #530。books/ は凍結中で旧実装
- Astroへの移行（#20/#21）は新サイト構築時に判断。現行サイトの残作業はPythonで進める

**新サイトの第一弾は index.html（#101）。** 他ページと構造が独立し依存が最も少ないため（Astro の試作対象も index.html に変え、#20 はクローズ）。

ボトルネックは #21（Astroへの移行を検討する）の判断。
ここが保留のままだと新サイトの着手ができない。

### 外部ドメインへの依存を増やさない

方針は CLAUDE.md「判断・作業の原則」（CSP〈#9〉のため）。残る外部依存は Google Charts（`www.gstatic.com` / `docs.google.com`、6ページ、#7）・Cloudflare Web Analytics（`static.cloudflareinsights.com`。
CSPでは `script-src` にのみ必要で、送信先は自ドメインの `/cdn-cgi/rum`）・画像12ドメインと `image.p-c2-x.abema-tv.com`（最強戦の卓・対局のページの ABEMA のサムネイル、#502。やむをえない依存として 2026-10-11 に足した）。
**ランキング3ページは #141 で外すが、成績3ページ（#111）を据え置くため `gstatic.com` は残り、#9 の CSP は gstatic を許可する形で書く。** 詳細は `docs/notes/handover-archive-2026.md`「外部ドメイン依存の詳細」と
`docs/notes/site-findings.md`（画像ドメインの実測）、#9 でCSPの`img-src`を書くときの指針は #9 のコメント。

### 表の色とアクセシビリティ

`.mj-table` の文字は WCAG AAA（7:1）、並べ替えボタンのフォーカス枠は 1.4.11（3:1）。縞と見出しの背景は `#f2f2f2`（#345。AAA の上限は `#e4e4e4`、境界はリンク色 `#14459b`）。
新サイトでも引き継ぐ。値と理由は `style.css` のコメント、経緯は `docs/notes/site-findings.md`「リンクの配色」。

### gh-pages ブランチは触らない

移行前の GitHub Pages の内容を、移行前の実装の記録として凍結したまま残す（更新しない）。GitHub Pages は無効化済みで配信されていない（#84）。

---

## 5. 次にやること

**次の会話の順番（2026-10-09）:** (1) 待ち: #388（平野さんが「映画」のタブを作ってから）・#389（平野さんの校正した書籍の一覧の整備）・#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）。
(2) 期日待ちは下の表の期日の順。決定は `docs/decisions/features.md`・`operations.md`。

**期限付き・確認待ちタスク**（期日の順）

| # | 内容 | 期限・目安 |
|---|---|---|
| #298 | Actions の使用量: 10/7 に Billing を実測済み（10/1〜10/7 で 928 / 3,000 分、請求 $0。平野さんのカレンダーの記録）。写しの移設（mj-logs 側へ）は実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除）が残る | 次の Billing の確認は **2026-10-14**。実装4の後半は **2026-10-21** |
| #513 | 作業ログの自動削除の見直しの効果の確認（規則を入れた日 D は 2026-10-08） | **2026-10-19** と **2026-10-26** の週次で測り、#513 へ報告する（指標は `docs/notes/branch-operations.md`「作業ログの寿命」） |
| #486 | Bing の Recommendations | **2026-10-30** に再確認 |
| #485 | 旧表 `jpml_titles.html` の転送と title/ の `?name=` の受け取りを終える | **2026-11-02** に 11/1 の取得（廃止後はじめての値。10/1 は廃止直前の基準値）で旧 URL への着地を見て、十分に減ったら終える（基準は未定。作業は #485 の本文） |
| #492 | ガイド文書の上限の見直し | **11月中旬** |
| #504 | 予約実行を Worker から起動する（段階1・2は済。設計・段階は #504 の本文） | 今の `schedule` を外す予定日 **2026-11-30**（仮） |
| #97 | 書籍ページ開発凍結中の楽天データ保存期限 | **2026-12-22**（最後に取得した2026-09-22の3か月後）までに再取得するか削除する（`docs/notes/books-freeze.md`「楽天の期限」） |
| #390 | 書き込み済みの月の自動更新（予定の書き換え・削除）は、画像の差し替えが起きるまで本番で動いていない（サービスアカウントの削除の権限も未確認） | 随時: 差し替えで #426 に「自動で更新しました」か「自動では更新していません」が出たら、カレンダーの当日以降が画像と合っているかを確かめる |

GitHub Issues（Open）に全件あるが、着手可能な主なものは以下。

| # | 内容 | 備考 |
|---|---|---|
| **#7** | Google Charts依存の解消 | **最大の残件。** #9 の前提でもある。進め方は下の「#7 の進め方」 |
| #540 | 鳳凰戦の新ページ `houou/` の公開（旧4ページの 301 を含む） | 公開は #542（データ・コンシェルジュ）ができてから、その質問欄までを含めて行う。手順は #540 の本文 |
| #549 | 最強戦の新しい年度・対局・卓のページの公開（今の年度ページの 301、`?match=` のリダイレクトルールを含む） | 時期は平野さんが決める。手順は #549 の本文 |
| #141 | ランキング3ページを Python の生成に移す | 2026-10-05 に移植と決定。#371・#228・#234 と、`select#selectbox` のラベル・選択と同時の遷移（#180 の残り）はこの移植の中で扱う |
| #486 | Bing の Recommendations | h1 は8ページに付けた（2026-10-06）。残りのランキング3ページは #141 の移植で付ける。title の長さ（短いページの共通の末尾）は #5 |
| #9 | CSP設定 | #7の後にやると強いポリシーが書ける |
| #550 | アクセス解析の導入の検討（利用者の画面幅・Cloudflare Pro の要否） | `docs/notes/design.md` の最小の画面幅（375px、仮置き）を見直す材料。外部ドメインへの依存（#9）に注意 |
| #4 | SentryでJSエラー検知 | 外部サービスの登録が必要 |
| #96 | カレンダーの参照・更新を自動化 | スコープ未定。決めるべき項目が4つある |
| #186 | アクセシビリティの実機での通し確認 | 静的レビュー（#178〜#185）の残り。チェックリストは `docs/notes/a11y-manual-check.md`。**Lighthouse のスコアを到達点として扱わない**（根拠は #186） |
| #408 | タイトル戦の対局日を確定させる | `YYYY-XX-XX` の大半は決勝ライブが無く【2】【3】から直せない（外の資料が要る）。書式は `docs/notes/title-pages.md`「日付列の書式」 |
| #490 | /live 層2の残りの規則と掲載範囲 | 残りは U1〜U4（ライブの無い組の対局日・紅龍戦のステージの並び・件数の少ない列・「プレイヤー解説：」）・候補の判別・公開版の掲載・複数卓・達人戦／昇龍戦／鳳匠戦の扱い（#437・#477 から集約。済んだものと件数は #490 の本文） |
| #475 | /live の未登録の名前（2026-10-03 に0名） | 常設。毎日の取り込みが増減の日だけコメントする。出たら実在の人は「連盟プロ以外」、誤記は「別名」に `訂正` で平野さんが登録し、概要欄の読み違いは層2の規則で直す（#490） |
| — | 現行サイトで小さく作れるもの | 次は #388・#389（上の「待ち」）。#425 も現行サイトで作る（#501・#502）。#366 は載せ方が未決、#378 は新サイト（#296）送り。未決: #365・#367 のデータを誰がいつ入力するか。決定は `docs/decisions/features.md` |

### #7 の進め方（検討済み）

**現状**: 対象18ページ中15完了（型B 3ページは #111 で据え置きに決め対象外、2026-10-05。完了分の `jpml_titles` は #441 で廃止）。残りはランキング3ページで、#141 で Python へ移植すれば完了。
共通部品は出そろっている（`docs/notes/static-generation.md`、型別の進捗表は `docs/notes/handover-archive-2026.md`「#7 の型別の進捗表」）。
移行しても速くはならず、目的は外部依存とインラインハンドラの解消（static-generation.md「#7 の期待値」）。
テーブル描画ライブラリの選定（#95）は #7 の前提から外し、新サイトのスタック（#21）と併せて検討する。型Aの表の方針は static-generation.md「型Aの表の方針」。

---

## 6. 関連文書

完了済み作業の実装記録・調査結果は `docs/notes/` にある（handover には結論と参照先だけ）。`docs/notes/` 以下は下の一覧が正（`ls docs/notes/` にあって載っていないものは、開く場面を1行で足す）。

| ファイル | 開く場面 |
|---|---|
| `CLAUDE.md` | 作業のルール（ブランチ運用・Chat-Ref・作業ログ）。Claude Code がセッション開始時に読む |
| `docs/instruction-template.md` | チャット側が指示文を書くときの雛形 |
| `docs/new-page-checklist.md` | 新しいページを作る・公開する前（未公開で入れ、公開は別 issue、#243） |
| `docs/decisions/` | 平野さんの決定の記録（分野ごと。書き方は `README.md`） |
| `docs/new-site-design.md` | 新サイト（#296）の設計。中断中で再開手順まである |
| `docs/astro-migration-study.md` | 新サイトのスタック（#21）の判断 |
| `docs/lighthouse-baseline.md` | パフォーマンスの改善前後の比較（ページ別スコアの基準値） |
| `docs/gsc/` | Search Console の数値（#142） |
| `docs/notes/cloudflare.md` | Cloudflare の設定・配信（`_headers`・`_redirects`・`wrangler dev`）・本番反映と確認範囲 |
| `docs/notes/session-network.md` | セッションから外部に届くか、gh の認証、Rebuild、シートの行番号、作業ファイルの置き場所 |
| `docs/notes/cloud-sessions.md` | クラウドセッション（Claude Code on the web）での CLAUDE.md の読み替え（ブランチの用意・GitHub MCP・ネットワーク・プレビュー） |
| `docs/notes/chat-side-operations.md` | チャット側が指示文を書く前（ログの読み方もここ） |
| `docs/notes/chat-routines.md` | チャット側が「レビュー」「振り返り」「申送り」を行う前 |
| `docs/notes/chrome-reading.md` | チャット側が PC で Claude for Chrome を使い mj を直接読む前 |
| `docs/notes/skills.md` | skill の追加・更新、git の hook の判定を変える・確かめる前（`.claude/` を変える指示を書く前も） |
| `docs/notes/branch-operations.md` | ブランチの削除・ワークフローの変更・作業ログの寿命・Chat-Ref の着手前の確認（入口の規則は CLAUDE.md） |
| `docs/notes/static-generation.md` | ページの一覧・生成スクリプト・ページ側のJS・ワークフローの一覧・メンテナンス用スクリプト、#7 の残り |
| `docs/notes/sitemap-lastmod.md` | sitemap の lastmod（#265） |
| `docs/notes/scheduler-worker.md` | 予約実行を起動する Worker（`mj-scheduler`、#504）。起動の表の直し方・通知（#506）・トークンの期限と差し替え・ダッシュボードの設定と作り直す手順・未確認の点 |
| `docs/notes/dojo-guest-calendar.md` | 道場部ゲストの告知画像の取り込みと平野さん側の設定（#390） |
| `docs/notes/ogp.md` | OGP 画像・`og:title`・SNS のカード表示 |
| `docs/notes/page-announcement.md` | 新しいページの X での告知の型（投稿文・告知動画・進め方） |
| `docs/notes/video-wayhome.md` | 「帰り道」の一覧・個別ページ、新しい回の追加 |
| `docs/notes/site-findings.md` | 現行サイトの実測値（パフォーマンス・画像ドメイン・SEO・配色） |
| `docs/notes/design.md` | 見た目の既決の値（色・文字・寸法・部品・濃色の例外）。ページや部品を作る・直す前 |
| `docs/notes/a11y-manual-check.md` | アクセシビリティの実機確認（#186） |
| `docs/notes/mj-lead.md` | 表・グラフのあるページの説明文（`.mj-lead`、#312） |
| `docs/notes/saikyo-page-design.md` | 最強戦（`saikyo/`）と選手写真の更新 |
| `docs/notes/title-pages.md` | タイトル戦の新構成（`title/`、#222） |
| `docs/notes/houou-race.md` | 鳳凰戦の順位変動（`houou_race.html`、#507） |
| `docs/notes/houou-top.md` | 鳳凰戦の新ページ `houou/` の仕様（トップ＋検索・ランキング・リーグ推移・順位変動、#518） |
| `docs/notes/ask-worker.md` | データ・コンシェルジュの Worker（`mj-ask`、#542） |
| `docs/notes/live-page-design.md` | 放送対局ページ（`live/`、#346）。掲載範囲の拡大は「3-5」（残りは #490） |
| `docs/notes/birthday-calendar.md` | 誕生日カレンダーの同期（#379） |
| `docs/notes/yotei-sheet.md` | 連盟の予定表の取り込みと放送対局の公開カレンダーへの同期（#448） |
| `docs/notes/live-channel-write.md` | /live の3層（【1】【2】【3】）の書き込み・毎日の取り込み・平野さんの入力の手順（#438）。チャット側が /live の指示を書く前 |
| `docs/notes/sns-book.md` | SNS ブック（選手の SNS の ID・画像の URL、#514）。ID の直し・写真の検知 |
| `docs/notes/books-freeze.md` | **書籍ページの開発凍結（2026-09-22、#97）。決定・凍結時点の状態・楽天データの期限・再開手順** |
| `docs/notes/books-calendar.md` | 書籍の発売日のカレンダー同期の仕組み（#97、凍結時点の記録） |
| `docs/notes/books-covers.md` | 書籍の書影（楽天ブックス書籍検索API）の仕組みと規約（#97、凍結時点の記録） |
| `docs/notes/decisions-2026-09-13-review.md` | #219〜#289 の重複・統合の判断 |
| `docs/notes/handover-archive-2026.md` | 過去の事故・経緯（作業の前提にはしない） |
`````

#### docs/notes/chat-side-operations.md

`````markdown
# チャット側（claude.ai）の運用

チャット側がログ・ガイド文書を読む手順と、Claude Code へ渡す指示文を書くときの注意。受け手側（Claude Code）の規則は CLAUDE.md が正。
**上限あり**（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。足すときは既存項目への統合・置き換えで、規則と理由の一句だけを書く。事例は `docs/notes/handover-archive-2026.md`「docs/notes/chat-side-operations.md から」へ。

## 読み方

**主な読み方は mj-logs（public、スマホ・PC 共通）。** mj は private（#211）。チャットのセッションに読み取りでつなげば `git clone` と REST（`gh api`、GraphQL は不可）で読める（セッションごと。書き込みは Code）。
**PC で Claude for Chrome を使うときは `docs/notes/chrome-reading.md` を先に読む。**

| 確認したいこと | 手段 |
| --- | --- |
| 作業ログ（`docs/logs/`）・ガイド文書 | mj-logs（下の「作業ログの読み方」） |
| Actions の実行結果 | mj-logs の `actions/status.md`（各ワークフローの直近5回。冒頭の書き出した時刻を見る、#498）。予約実行は2〜3時間遅れる（#504）。動いたかは契機・開始時刻で見て、ジョブのログは Code に確かめさせる。ジョブのサマリ（`GITHUB_STEP_SUMMARY`）は API に無いため Chrome で実行のページを開いて読む（annotations は Code が check-run の API で読める） |
| issue・コミット・ブランチ | つないだチャットは REST、ほかは Claude Code に `gh`（クラウドは GitHub MCP）/ `git` で確かめさせ、ログに書かせる。PC では Chrome でも読める。コミット履歴を独立に検証するときは `docs/notes/handover-archive-2026.md`「コミット履歴の bundle による検証」（#211） |
| 本番の見え方 | 平野さんの目視か、Claude Code の headless Chrome。Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビルド成否と本番の確認範囲（check-runs）」。**チャット側が本番（ryoei.pro）のページ・ファイルを読むときは URL に `?v=<未使用の値>` を付ける（変更の直後の確かめでは必ず）**（クエリ無しでは古い版が返ることがある） |

### 作業ログの読み方

- **サンドボックスに `git clone --depth 1 https://github.com/retroeater/mj-logs.git` で取得して読める（読む前に毎回 `git pull`）。**
  最新のガイドは `guide/HISTORY` の最後の行の SHA の `guide/<SHA>/`、使用済みの識別子の一覧は `chat-ids/HISTORY` の最後の行の版。
  取得できたチャットでは、ログの URL が届く前でも最新のログとガイドを読んでよい。取得できるかはチャットごとに違う
- **取得できないチャットでは、平野さんが送る「ログ（公開）」の行の URL をそのまま使う**（組み立てた URL は開けず、一度読んだ URL は古い版が返る。末尾の `?v=<SHA>` を変えない）。
  ガイド文書はそのログの末尾のリンク（`guide/<SHA>/`）から読む。読めない・途中の版に見えるときは `## 報告` 節だけを貼ってもらう。
  **末尾のリンク（`guide/<SHA>/`・`chat-ids/`）が読めないときは、指示文を書く前にその URL か読める最新の「ログ（公開）」の行を送ってもらう**（読まずに書くと雛形や handover と食い違う）
- **平野さんは「ログ（公開）」の行を送る（新しい会話の始めは前回の最後の行）。** どのログの完了報告かの指定になるので、clone できるチャットでも変えない。
  完了報告はこの行を送るだけ。単発の確認でも作業ログに書かせる
- **ログは push のたびに数分以内に写る**（`docs/notes/cloud-sessions.md`「作業ログ」、#298）。
  **最終報告が出ているはずなのに `## 報告` が仕上がっていない・404 なら、再開の指示を作る前に、Claude Code の画面の最後に最終報告が出ているかを平野さんに確かめてもらう**（#454）。
  出ていれば写しの漏れ（`## 報告` を貼ってもらう）、出ていなければセッションが止まっている
- **申告をそのまま信じない。** ログ・ブランチ・issue に実行の痕跡があるかを先に確かめる
- **ガイド文書は、ログを読んだその場で必要なもの（マージを伴う指示なら CLAUDE.md「ブランチ運用」）を読む**（後の手番では同じ URL を開けなくなることがある）。
  **ガイドの版（`guide/<SHA>`）が前に読んだ版と違えば、指示文を作る前に `docs/instruction-template.md` を読み直す**
- **ブランチ名は最終報告の「ブランチ:」の行か比較 URL から取り、推測で組み立てない（指示文でも）。** mj-logs にログが無いときは、
  Claude Code に `git log --all --grep=<Chat-Ref> --oneline` で到達を確かめさせ、ブランチ名とログの URL を返させる
- 貼られた `## 報告` には Chat-Ref の行が無い。「ログ:」の URL の Chat-Ref とブランチ名で見分け、見分けられなければ読む前に平野さんに確かめる
- **ログは `## 報告` だけで足りる。** 全体を貼ってもらうのは「報告だけでは判断できない」と明示して頼んだときに限る
- ログの「指示」欄の末尾が、送った指示文の末尾と一致しているかを確かめる

### 確認用 URL

**プレビュー URL はログに無く、最終報告の「確認用:」の行にだけある**（非公開扱い）。平野さんに送ってもらう。ログイン不要でスマホでも開ける（`docs/notes/cloudflare.md`「work/ ブランチのプレビュー」、#363）。
ビルドが走らなかったときは Codespace の確認用 URL（GitHub ログイン必須。`docs/notes/session-network.md`「Codespace の確認用 URL」）

## セッション識別子の確認

- **新しい識別子（`XXX`）は英大文字3文字。** 既存の2文字の識別子は有効なままで重ねない（#474）
- **最初の識別子は、読んだログの末尾の「使用済みの Chat-Ref 識別子」の一覧に無いものを選ぶ**（`MMDD` を問わない。#440 より前も載る）。
  一覧は写した時点のもので、以後に始まったセッションは載らないことがある。**本命は受け手側の確認**（CLAUDE.md「Chat-Ref」節）

## Claude Code とのやり取り

- **Claude Code が作業ブランチの作成（`git checkout -b work/…`）などを分類器に拒否されて止まったら**（通る回もある）、
  同じセッションに「平野さんの判断として、そのコマンド（全文）を許可する。同じ操作がまた拒否されたら別の手段を試さずに止まる」を貼ってもらう
  （別のブランチや許可ルールで回避させない。Code 側は `docs/notes/cloud-sessions.md`「作業ブランチの用意」）
- **平野さんが「レビュー」「振り返り」「申送り」と言ったら** `docs/notes/chat-routines.md` の手順で行う

## 指示文を書くときの注意

雛形は `docs/instruction-template.md`。
**Claude が作成した下書き（Claude Code に貼る文面など）には、先頭行を「【Claude作成】…」にするなど Claude 作成と分かる見出しを付ける**（平野さんの発言と区別するため）。

### 書く前に実物で確かめる

- **事実は実物で確かめたものだけを断定する**（会話の記憶・前回の報告・推測から書かない）。「目的」「前提」の事実は、そのチャットで実物を読んだもの以外に「（要確認）」を付けて Claude Code に確かめさせ、
  怪しいものは「〜を確かめ、食い違えば止まる」の形にする。issue 番号・ページ数は洗い出しのログにあるものだけ使う（#486）。確かめた場所を正しく書き、見ていない出典を書かない。解釈を事実として書かせない。
  **issue の状態（Open/Closed・本文・コメント）を確かめられないまま、その issue の作業の指示を書かない**（読めなければ Code に確かめさせてログに書かせてから書く）
- 場面ごとに次も確かめる:
  - 最初の指示: 関係する issue の本文・コメント、未マージの work/ ブランチ、既存の仕組み（テストの置き場所など）が「ある」こと
  - マージ・クローズの指示: issue の Open/Closed と比較（`/compare/cloudflare...<ブランチ>`）。別のチャットが並行して進めていることがある
  - 複数の指示にまたがる作業: 最初の指示で、論点に関係する issue（クローズ済みを含む）を洗い出させる。検索語には変える対象そのものの語を入れる
  - 起票・方針の提案、運用ルールや文書整理: 同じ論点の issue を、クローズ済みとコメントの決定まで含めて確かめる（#328）。読めなければ提案せず、確かめさせる指示にする
  - インフラやデプロイの提案: 実物の設定（CLAUDE.md「構成」、`docs/notes/cloudflare.md`）
  - 他のセッションが同じ日に変えている領域（`title/` など）や前の指示から写す前提: 使う直前に確かめ直させる（写すときは対象ファイルの範囲が同じかも）。**「残っている作業」を書く前に、docs/decisions/ の置き換え・済の注記と、コード・シートに今もあるかを確かめる**（または手順に入れる）。
    **複数のチャットが同じ仕組みを変えているときは、見込みを平野さんに伝える前と指示を書く前に、もう一方のチャットの最新のログを読む**（触るかを推測で言わない。変える関数名・ファイル名で mj-logs の `logs/` を grep し、判断待ち・中断のログがあればそのチャットの最新のログを読む）
  - 平野さんがシート（タブ）を用意した・直したと言ったとき: 実装の指示の前に、生成と同じ経路で読ませ、見出し・件数・既存の公開物との差を報告させる（差は止まる条件にせず、どちらを正とするかを平野さんに聞いてから書く）。
    消す・直すを頼むときは済んだ後の状態（ブックごとのタブの数など）を書き、平野さんに数を返してもらってから次の指示を作る（申告だけで作ると、残りがあって止まる）
- **触れる領域の資料を読んでから書く**（CLAUDE.md・handover.md に追記・変更するときは追記先の節の今の内容、ある領域に触れるときはその領域の `docs/notes/` の最近の変更）。
  **マージ・ブランチ操作・`/workspaces/mj` の操作を含む指示文の前には CLAUDE.md「ブランチ運用」節を必ず読む**（#313）
- **仮説を確かめるときは、当たり外れを一発で分ける確認を先に行い、仮説が正しい前提での準備はその後にする**
- **平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む**
- **平野さんに判断を求める前提（「A を直せば B も不要になる」等）や、シートの値を変えるよう頼む前の今の値は、先に Claude Code に照合させるか「未確認」と明記する**（誤った前提で判断をもらうと決定が誤る）
- **シートの行・カレンダーの予定は動画ID（または行番号）で一意に指す。値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる**

### 平野さんの判断とマージの許可

**平野さんは指示文を読まずに貼る**（添え書きの確認は読み落とされ、未決のことが判断として記録される）。次はこの前提による。

- **「平野さんの決定」の欄には、実際に判断をもらった内容だけを書く。** 現状維持も明示が無ければ「前提」の欄へ（欄は `docs/instruction-template.md`）。
  **チャット本文にも同じ内容を書き、日付は判断した日にする**（Chat-Ref の日付ではない）
- **平野さんの決定は、ログの末尾のリンク「docs/decisions/README.md」から分野のファイルを読んで確かめ、**指示文の「決定」節はそれと食い違わないように書く（置き換えるときはその旨を書く）
- **指示文を作る前にマージの可否を平野さんに確かめ、「マージ:」の行に「承認済み（チャットで）」か「判断待ちで止まる」を書く**（Code は処理中に承認を求めない）。
  プレビューを見て決める変更は「判断待ちで止まる」。「承認済み」はプレビューの確認が済んだことをチャットで確かめてから書く（「貼った＝見た」とみなさない）。承認済みでも、マージを止める確認の基準は「止まる条件」に書く。
  **調査だけの指示（変わるのがログだけ）は「ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい」にする**（判断待ちで止めると、マージだけの指示が後で要る）。判断の要る点は「判断が必要なこと」に書かせ、続きは作業ブランチを origin/cloudflare から作り直して始める。**どの指示でも、完了条件にログの状態（完了・判断待ち）を書かない**（状態は CLAUDE.md「作業ログ」節に従い Code が決める。書くと指定とルールの食い違いを毎回判定させる）
- **「判断待ち」で止めた報告を確認した返答に、マージ用の指示文を添える**（口頭の「マージしてよい」を前提にしない）。同じ作業ブランチ・マージで動く自動処理の見込み・止めた指示のログの状態の直し・作業ブランチの片付けを含める。
  「承認済み（チャットで。この指示文を貼ることが承認）」としてよいのは、(1) 止めた報告のマージだけで新しい変更を含まない (2) 何がどう変わるか（件数だけでなく中身、コードなら影響の範囲）をチャットに示した (3) origin/cloudflare を取り込んだ後の再検証（テスト・模擬など）を条件に書いた、のすべてを満たすときだけ（報告を読んだうえの承認で、「貼った＝見た」とは別）。満たさなければチャットで「よい」をもらってから書く。
  **片付けは、関係のない検証（ワークフローの実行結果など）の成否に条件づけない**
- **`.claude/` の hook・settings を変える指示は平野さんの手作業を前提に組み、「マージ:」は「判断待ちで止まる」**（Code は試験と文書まで。手順は docs/notes/skills.md「git の hook（mj-git-guard）」）
- **クローズの時点で残る作業は別の issue に起票する**（平野さんの決定。クローズの指示に書く）
- **「添付した」と書く前に、ファイルを提示し終えたことを確かめる**
- **公開など取り消しにくい段階の指示は、作る前に実施の時期を平野さんに確かめる**
- **報告の「〜は読み取れない」「〜は不明」を平野さんに伝える前に、handover と突き合わせる**

### 指示文の書き方・渡し方

- **作業ブランチ名は書く**（文面は `docs/instruction-template.md`「作業ブランチ」の行。既存ブランチを続けるときも）。未マージのブランチとの重なりは名指しせず、
  `git branch -r --no-merged origin/cloudflare` の一覧から確かめさせる（書いた後に増えたブランチを見落とさない）
- **コミットSHAを固定して書かない。** 土台や比較対象は「その時点の`origin/cloudflare`」と書く。既存の特定コミットの参照（`git show <sha>:<path>` など）は書いてよい
- 未マージの試作ブランチを捨てさせるときは、CLAUDE.md「未マージのブランチは削除しない」の例外であることを明記する
- **貼った指示文が途中で切れて届くことがある。** 0章に途中切れの確認の一文を入れ、最後に確認文を置く（文面は `docs/instruction-template.md`）
- **設定の読み込みやセッションの状態に左右される検証は、チャット本文で「新しいセッションで貼る」と伝え、0章で「このセッションが新しく始めたものか」をログに書かせる**
- **チャットの添付ファイル（CSV 等）は Code から読めないことがある。** チャット側で集計し、表にして指示文に入れる
- **1本の指示は3項目程度まで**（途中で止まったときに何が済んだか分かるように）
- **配った後の指示を直すときは、先に平野さんがもう貼ったかを確かめる。** 貼った後なら新しい番号で出し直す
- **並行する指示の中で、ほかの実行中の指示の進み具合に触れない。** 結果が要るなら「`<Chat-Ref>` のログの `## 報告` を読み、完了していなければ止まる」の形にする
- **後の実行の結果（毎朝の実行・別の指示）を見込みに使う指示は、その結果が出てから作る。** 前もって作るときは「貼る時機」に前提を書き、
  見込みは「`<Chat-Ref>` のログの `## 報告` に従う」の形にして数を写さない
- **同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを分ける**（`work/<MMDD>-<識別子>-<短い名前>` など）。
  平野さんには「1つのセッションに貼る指示は1つ」と伝える
- **既定モデルは Opus 5.5。別モデルは指示文の手前のチャット本文で伝える**（指示文に「推奨モデル」欄は入れない）。
  単発の確認・定型作業は Sonnet 5.5（`claude --model claude-sonnet-5-5`。別名 `sonnet` は最新の Sonnet。指示ごとに指定して試行中）。Sonnet でも共通手順とログは省かない

### 止まる条件と検証の指定

- **「差分が N 行ならマージ」のような止める条件は、先に変更対象の参照箇所を調べさせてから決める**（容量の増減も実測を報告させる）
- **マージ・止まる条件は、見込みで説明できる差分の範囲として書く。** 生成物を含む変更は差分を種類に分け、許す種類と確かめ方（変わった画像 URL は HTTP 200 など）を書き、ほかが出たら止める（再生成のたびにシートの変化が混ざる）。
  「承認済み」の条件は「決定とシートの変化で説明できる差分だけ（見込み: ○○）」の形にする（表示の一部の列挙では、そこから決まる並び〈入口のカードの順・`search.json` の順など〉が外れる）。
  見込みと比べる条件は許すずれを数で書き（例: 直すは ±3件まで、作る・消すは見込みと同じ。無いと進むかを受け手が決める）、見込みの数は直前のログのどの表のどの数から計算したかを書く（推測の幅は表と食い違う）
- **止まる条件で数値の増減を見るときは、別名の寄せなど正しい理由で動く場合を想定して書く**
- **スプレッドシートを読む指示には行数の止まる条件を入れる**（想定と違う・読み直すたびに変わるなら件数を書いて止まる。大量の貼り替えの後は貼り終えたことを伝えてから読ませる。`docs/notes/static-generation.md`「生成を止める条件の設計」）
- **画像やページから値を読み取らせるときは、元の資料に書かれている値と、こちら側が持つ値を項目ごとに分けて書く**
- **「通知が出ること」を確認に書く前に、その通知が出る条件を確かめる**
- **ログの計測値や事実を issue に書かせるときは、要約を渡さず「どのログのどの節から引用するか」を指定する**
- **/live の【3】・`update-live-channel.yml` に触れる指示の前に `docs/notes/live-channel-write.md`「チャット側の指示」を読む**

### 見た目の決め方

- **見た目は文章で往復せず実物で見比べる。** 案が2つ以上なら両方を実装し、ページ内のラジオボタン（URL パラメータではない。平野さんの指定）で切り替える比較ページにする。
  比較ページは noindex・どこからもリンクしない・sitemap に載せない。
  **採用後は使わない案のコードとラジオボタン、比較ページを消す**（作り方は `docs/notes/title-pages.md`）
- 試作 → 画面イメージ → 平野さんの判断 → 本実装 → マージ の順に指示を分けると手戻りが少ない
- **規則から決めた値も、実装の前にその値を含めた見本を見せる。** 規則を満たすことと見た目として受け入れられることは別
- 見た目から断定しない
- **コントラスト比などの数値は手計算せず、計算式（WCAG 2.x の相対輝度など）を明示して Claude Code にも計算させ、突き合わせる**

### 期日とカレンダー

- **issue に期日を書かせたら、同時にカレンダー「ryoei.pro」に予定を作る。** 件名は「【R#番号】…」、説明の冒頭に issue のリンク、色は指定しない。
  番号の無い予定も同じ。個人の予定表には書かない（コネクタは ryoei.pro だけを共有した別アカウント）
- **期日を過ぎた繰り返しの予定は、その回を済むまで翌日へ繰り越す**（別の予定は作らない）。繰越しは開いた時にまとめてよい
- **予定が済んだら・issue のクローズを知ったら、消さずに件名の冒頭に【済】を付けてグラファイトにする。** 説明欄の残課題は行き先の予定へ移す
- **動きを変える指示では、通知先の常設 issue（種類: 常設）の本文も文書更新の対象に入れる**

### 外部サービスの設定

- **最初の調査の指示に「権限・共有・組織ポリシーの制約」の洗い出しを入れ、実装の指示では要る Secret・変数・共有をすべて列挙する**
- **外部の API を使う実装の指示では、本番の手順の前に「そのアカウント・プランで対象の API を1件だけ呼んで通る」ことを手動実行で確かめる手順を最初に置く**
- **設定の変更を頼むときは、選択肢の意味と、ダッシュボードの画面の名前（ボタン・欄）とその影響を公式ドキュメントで確かめてから頼む**（名前から推測しない。「害は無い」と確かめずに言わない）。
  **設定が保存されたかは、スクリーンショットではなく動き（check-run・実行の結果など）で確かめる**
- **検索を止めうる変更は、疑いが出た時点で確かめる。後日に回さない**

## ほかのセッションへの共有

- **宛先は相手の「チャット」にする。** 相手のチャットが自分の Chat-Ref の指示文に書き直してから Claude Code に渡す（直接渡すと作業ログが残らない）
- **Actions の失敗通知を受けたら、原因を調べる前に、その題材（ワークフロー・実行したブランチ）を扱っているチャットを特定し、そこへ引き継ぐ。** 並行するチャットで同じ系列の Chat-Ref を出さない
- **「ログにこう書いてある」「平野さんの申告（Code が確かめるまで未確認）」「確かめた事実」を分けて書く**
- 共有文を受け取ったら、先に自分がその系列のチャット（履歴・プロジェクトの指示がある）かを確かめ、違えば平野さんに伝えてから進める
`````

#### docs/instruction-template.md

`````markdown
# 指示文テンプレート（チャット側 → Claude Code）

チャット側（claude.ai）が Claude Code へ渡す指示文の骨組み（#294）。受け手側のルールは CLAUDE.md が正で、ここには複製しない。
作業ブランチ名を書くこと・SHA を固定しないこと・「決定」と「前提」の欄の分け方・0章の途中切れの確認・見込みと比べる止まる条件の規則は `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」「平野さんの判断とマージの許可」「止まる条件と検証の指定」が正。ここには行の文面と書き方だけを置く。

- 「作業ブランチ」の行の文面（式の向きが2つで逆なので、そのまま写す）:
  - Codespace で既存の作業ブランチを続ける: 「作業ブランチ: origin/cloudflare を起点に切った既存の work/<識別子> を続けて使う（〜のため）。着手時と作業中に origin/cloudflare が進んでいたら merge で取り込んでよい（push 済みなので rebase しない）」。取り込みの可否を書かないと、祖先確認だけで中断する
  - クラウドセッションで新しく作る: 「作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる」
  - クラウドセッションで未マージの作業を続ける: 「作業ブランチ: クラウドセッションで実行する。未マージの work/<識別子> を続けて使う（〜のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）」。`git checkout -b work/<識別子> origin/work/<識別子>` は書かない（同じセッションを続けるとローカルに同名のブランチがあり `-b` が失敗する）
- 未マージの作業ブランチを続ける指示とマージの指示には、取り込みで生成物でない文書が衝突したときの扱いを書く。別のセッションが同じ文書を変えていそうなとき（一覧の表・追記の続く文書）は「両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる」と書く。「また衝突したら止まる」とは書かない（同じ形の衝突のたびに1往復かかる）
- 定型部分は「共通手順」1行にまとめ、個々の手順を文章で書き直さない（写し間違いと途中切れを減らす）
- **共通手順の行の手前に「貼る時機:」の行を置く。** 前提（ほかの指示の完了・毎朝の実行の後など）があれば書いて0章で確かめさせ（満たされなければ何もせず止まる）、無ければ「いつでも」（前提の指示より先に貼られると番号を1つ使う）
- 運用ルールの変更を含む指示は、手順1を「同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する」に固定する（チャット側が検索できないことがあるため）
- 既存の文書への追記を含む指示は、追記先の今の内容を必ず読ませる（読ませないと古い記述と新しい記述が両方残る）。止まる条件は次のとおり書き分ける
  - **止まるのは、追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要るとき**
  - **同じ趣旨の記述があるだけなら止めず、既存の記述を置き換え・拡張してよい**（処理を報告に書かせる。受け手は CLAUDE.md の「同じ内容を2箇所に書かず」「古い記述を消して置き換える」に従う）
- **計測を伴う指示は、環境・回数・判定を最初の指示で決めて書く**
  - 環境: **本番と比較対象を交互に、同じ環境で測る**。本番とプレビュー、本番と `file://` を混ぜない（環境差が支配的になる）
  - 回数: **20回を目安**（本番10回・比較対象10回）。5〜6回では偶然に左右される
  - 判定: **0 でない回数と中央値で見る。1回ごとの最大値と「すべて 0」は条件にしない**（確率的に出る事象では、同じコードでも値が揺れる）
  - `git diff --stat` で生成物が +10% を超えて大きくなる直しは、プレビューの指示の段階で計測させる（マージの指示で初めて測ると、止まったときの往復が増える）
- 見た目を直す指示で、受け入れた値（ページの長さ・高さ・サイズなど）が既にあるときは、その値を止まる条件に写す（超えたときの扱いが無いと、マージ後に判断待ちが残る）
- issue を変えない読むだけの指示でも着手中のコメントは書かせ、「本文・状態・ラベルは変えない」と書き分ける（「issue を変えない」だけだと CLAUDE.md の着手中コメントと食い違う）
- 「変更は docs のみ」「docs/logs のみ」と範囲を書くときは、`docs/decisions/` も含めて書く（受け手は完了時に決定を足すため、範囲から外れると指示とルールが食い違う）
- 依存（blocked by）は「A は B を待つ」の文で書き、矢印（→）は使わない（向きが読み手によって逆になる）
- 報告の項目は数で書かず「CLAUDE.md『作業ログ』節のとおり」と指す（項目が増えたとき、引き写した指示文が古い数を運ぶ）
- **該当者の数を報告させるときは、名前の一覧も書かせる**（数と例だけだと、残りを平野さんが探すことになる）
- 完了条件の Chat-Ref の行は、ターミナルへの最終報告の最後の行を指す（ログの `## 報告` には無く、「ログ:」の URL で見分ける、#419 (b)）
- 送る前に指示文を直したときは番号を進めて直す前の番号を欠番にし、次の指示文の目的欄に「〜は送る前に差し替えたため欠番」と書く（直す前の版が渡っても番号の違いで分かる）。貼った後に直すときは `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」
- 待機の上限15分と、共有の定数・関数を変えるときの全ページ再生成は CLAUDE.md「判断・作業の原則」が受け手に課す。指示文には例外だけ書く
- **試運転（dry-run の手動実行など）が失敗で止まる見込みのある指示は、そのことを書き、最終報告に「失敗通知が届くが対応不要」と書かせる**（失敗の通知メールで、別のチャットが原因を調べ直すことになる）
- **本番に書き込むワークフローの変更をマージ前に試すときは、書き込みを止めるスイッチ（`SCHEDULE_ENABLED` など）を一時的に切ったコミットで、変えた契機の道筋を手動実行で確かめさせ、スイッチを戻すコミットの差分がその1行だけであることを確かめさせる。** 手では起こせない契機（`schedule`）の分岐は、判定の結果を毎回ログに出す作りにして、手動実行で判定の部分を確かめさせる
- 新しいページ・メニューを作る指示に公開（noindex を外す・navbar・サイトマップ・`llms.txt`）を含めない（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」、`docs/new-page-checklist.md`、#243）
- **未マージの `work/` ブランチとの重なりで止まる条件は、ファイル単位でなく「同じ行・同じ関数を変えている、または取り込みで衝突する」の形で書く**（行が離れた変更で止まると往復が増える、#277）
- **マージ後の check-run の失敗で止める条件は、「今回の変更による失敗」と「無関係な失敗」を分けて書く。** 無関係なら原因を報告に書いたうえで残りの手順（本番の確かめ・issue のクローズ）を進めてよい、とする。自分の変更で落ちると分かっているテストは直してよい、と書く（#277）
- **続きの指示（前の指示の判断待ち・中断への回答、再開）には、前のログの `## 報告` の状態の末尾に ` / 続き: <この指示の Chat-Ref>` を足す手順を入れる。** 続き先が完了・取り下げになると前のログが自動で削除される（`docs/notes/branch-operations.md`「作業ログの寿命」）

```
【Claude作成】Claude Code 向け指示：（題。何をするかを1行で）
（題の行は必ず入れる。平野さんの発言と区別するため）
Chat-Ref: CHAT-MMDD-XXX-nn
マージ: 承認済み（チャットで）／判断待ちで止まる／ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉（どれかを残す。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）
貼る時機: いつでも／<前提>の後（例: CHAT-MMDD-XXX-nn の完了の後、毎朝の取り込み〈update-live-channel、04:00 JST の Worker からの起動〉の後）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
（0章のこの一文は必ず入れる。途中切れの確認のため）
（「貼る時機」に前提を書いたときは、0章にその確かめを足す。前の指示の結果が前提なら「<Chat-Ref> のログの `## 報告` を読み、完了していなければ何もせず止まる。」）

## 目的
（何のために何を変えるか。1〜2行）

### 決定（YYYY-MM-DD、平野さん）
- （チャットで平野さんが明示したことだけ。現状維持を含め、明示が無ければ下の「前提」へ）

### 前提（チャット側。平野さんの決定ではない）
- （チャット側の提案、書き場所・文面の案、背景。実物に合わせて変えてよいものはここに書く。実物で確かめていない事実には「（要確認）」を付ける）

## 手順
1. （前提は「〜を確かめ、食い違えば止まる」の形で書く）
   （運用ルールの変更を含む指示では、上の注意のとおり手順1を固定する）
2.
3. （3項目程度まで）

## 止まる条件
- （前提が食い違う、同じ論点の issue がある、他セッションの着手中コメントがある 等）
- （見込みと比べるときは許すずれを数で。例: 直すは見込み ±3件まで、作る・消すは見込みと同じ）
- （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが〈変えるファイル〉の〈同じ行・関数・節〉を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- （マージを伴う指示では）cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-MMDD-XXX-nn.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-MMDD-XXX-nn を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。
```
`````

### 手順3: 移した先の差分（283713b9d → 成果物のコミット）

`````diff
diff --git a/docs/notes/handover-archive-2026.md b/docs/notes/handover-archive-2026.md
index a1fea744a..cc4e8d8cf 100644
--- a/docs/notes/handover-archive-2026.md
+++ b/docs/notes/handover-archive-2026.md
@@ -778,7 +778,14 @@ Builds が既に稼働しており不要かつ二重デプロイになるもの
 
 ### コミット履歴の bundle による検証
 
-`docs/notes/chat-side-operations.md`「コミット履歴を独立に検証するとき」の graph.txt で足りないとき（深掘り、64MB）は、
+チャット側がコミット履歴を独立に検証するとき（#211）は、Claude Code 側で書き出し、平野さんがチャットにアップロードする
+（85KB程度。親子関係・祖先判定・Chat-Ref の到達・変更ファイルと行数を追える。2026-10-12 に chat-side-operations.md から移した）:
+
+```
+git log --all --pretty='COMMIT|%H|%P|%ci|%s' --numstat > /tmp/graph.txt
+```
+
+graph.txt で足りないとき（深掘り、64MB）は、
 Claude Code 側で分割バンドルを作り、3ファイルを平野さんがアップロードし、チャット側で `cat` 結合して clone する。
 
 ```
@@ -877,6 +884,37 @@ docs/handover.md:
 - 「コミットのルール」: 無関係なハンクを混ぜない理由「混入すると `git blame` / `git log -- <file>` が無関係なissueを指す」。シェル経由で書かない理由の「コマンドの出力が混入する」
 - 「更新ルール」: 見出しで参照する理由「行番号はすぐずれる」。置き換えの規則の「「→その後こうした」という追記型にしない」
 
+### 2026-10-12 の整理で4文書から外した記述（#492）
+
+規則は各文書に残すか移した先に置き、重複・言い換えと済んだことだけを外した。
+
+docs/notes/chat-side-operations.md:
+
+- 冒頭の上限の数値（警告26KB／失敗28KB）と「警告が出たら上限を上げずに整理する」は CLAUDE.md「CLAUDE.md / handover.md の更新ルール」への参照だけにした。受け手側の規則の括弧書き「（Chat-Ref・ブランチ運用・作業ログ）」、「指示文を書くときの注意」冒頭の「受け手側の規則は CLAUDE.md『Chat-Ref』節」（文書の冒頭と同じ）
+- 「止まる条件と検証の指定」の最後の項（待機の上限と全ページ再生成は CLAUDE.md「判断・作業の原則」にある）は、instruction-template.md の同じ趣旨の項に寄せた
+- 「止まる条件と検証の指定」の生成物のマージの基準と「承認済み」の条件の2項に、instruction-template.md の「見込みと比べて止まる条件は許すずれを数で書く」を合わせて1項にした
+- /live の【3】の「掲載」と `update-live-channel.yml` の手動実行の入力の2項は docs/notes/live-channel-write.md「チャット側の指示」へ、
+  `.claude/` の hook・settings の手順（平野さんが GitHub の画面で作業ブランチにコミットし PR でマージする）は docs/notes/skills.md「git の hook（mj-git-guard）」へ、
+  「コミット履歴を独立に検証するとき（#211）」の節はこの文書「コミット履歴の bundle による検証」へ移した
+- 調査だけの指示の項の「（ログはマージ済みのまま）」（「cloudflare へ入れてよい」と同じ）。0章の一文の文面の引用（instruction-template.md にある）。「確認用 URL」の「CLAUDE.md『作業ログ』節」への参照
+
+docs/instruction-template.md:
+
+- 冒頭の段落の分け方と、雛形の題・0章の注記の docs/notes/chat-side-operations.md への参照（冒頭の段落にある）
+- 「共通手順」の行を「2行目」とした記述（今は5行目）
+- 手順1を固定する理由の「受け手側の検索で代替する」、追記の止まる条件の理由の「『同じ趣旨なら止まる』は厳しすぎる」
+
+docs/handover.md:
+
+- 「最終更新: 2026-10-11」の3行の最古「鳳凰戦の新ページ `houou/`（#518）を未公開の形で本番に入れた。公開は #540」（5章 #540 の行にある）
+- 5章 #540 の行の「先に、リーグ推移の特別昇級に紙吹雪を付け段数に応じて豪勢にする（2026-10-11 の決定）」は済んだ（84577fda5、docs/decisions/houou.md「2026-10-11（CHAT-1011-HOU-14）」で OK）
+- 3章「役割分担」の表と直後の段落の chat-side-operations.md への参照（0章と6章の表にある）。3章の「Rebuild・gh の認証は…」の行は Codespace の括弧書きに寄せた
+- 4章「現行サイトに作り込みすぎない」の「具体的には、次のような判断をしてきた。」
+
+CLAUDE.md:
+
+- 「更新ルール」の「この3文書」は、2026-09-22 の決定の対象（CLAUDE.md・handover.md・chat-side-operations.md）を名前で書いた（上限の対象が4文書になり、指す先が曖昧になったため）
+
 
 ## Open issue の棚卸し（INV、2026-10-02〜03）
 
diff --git a/docs/notes/live-channel-write.md b/docs/notes/live-channel-write.md
index b3ba026cf..624c8991a 100644
--- a/docs/notes/live-channel-write.md
+++ b/docs/notes/live-channel-write.md
@@ -248,6 +248,13 @@ Sheets APIの呼び出し回数（サービスアカウントの書き込み上
 - **今の見出しの並び**（2026-10-05）: A 動画ID・B 掲載・C 冒頭・D 参考:配信日時・E 参考:タイトル・F タイトル戦・G 期・H 期の並び・I ステージ・J ステージの並び・K 卓・L 動画の単位・M 回戦・N 枝番・O まとめ単位・P 対局者・Q 実況・R 解説・S 対局日・T 追加日・U 備考。**手で行を貼るときは「参考:」の列（D・E）に値を貼らず、A〜C と F〜U に分けて貼る**（数式を上書きしない）
 - **見出しが「参考:」で始まる列は参考の数式の列（#482）。** 補正ではなく、生成は読まない。毎日の追記はその列に値を送らず（数式の広がりを止めないため）、追記の確認でも比べない。数式の LET の変数名にセル番地に見える名前（`s1` など）を使わない（`#NAME?` になる）
 
+## チャット側の指示
+
+2026-10-12 に `docs/notes/chat-side-operations.md`「止まる条件と検証の指定」から移した。
+
+- **【3】の「掲載」を変える指示では、/live への影響も確かめさせる。** 掲載は /live と title/ の両方が使う
+- **マージの後に `update-live-channel.yml` を手動実行させる指示は、届く先（【2】・予定表・公開カレンダー・生成物）ごとに要る入力を `docs/notes/yotei-sheet.md`「手動実行」で確かめて書く。** カレンダーの件名・説明文が変わるなら `calendar_apply` を含める（無いと反映は次の定時の実行）
+
 ## 未確認のこと
 
 - 「My First Project」で鍵作成が止められていないことは birthday-calendar での実施結果からの推測（2026-09-22時点の平野さんの報告）。
diff --git a/docs/notes/skills.md b/docs/notes/skills.md
index 113601c5c..f87c197b1 100644
--- a/docs/notes/skills.md
+++ b/docs/notes/skills.md
@@ -98,7 +98,9 @@ Bash のコマンド文字列を `&&`・`||`・`;`・`|`・改行で区切り、
 | `git branch -d work/old`・`git checkout -b work/y origin/cloudflare` | 通過 |
 
 - **hook の変更は、セッションからは書き換えも cloudflare へのマージも auto モードの分類器が `[Self-Modification]` として拒否する**（2026-09-30）。
-  平野さんが手でコミットし、PR（Create a merge commit）でマージする。セッションは試験と文書まで
+  平野さんが手でコミットし、PR（Create a merge commit）でマージする。セッションは試験と文書まで。
+  チャット側の指示はこの手作業を前提に組み、「マージ:」は「判断待ちで止まる」にする（`.claude/` の settings を変える指示も同じ）。
+  平野さんは GitHub の画面で作業ブランチにコミットする（「Create a new branch」が選ばれているかを確かめてもらう。外れると cloudflare に直接入る）
 
 限界（文字列の解析であり、事故の防止であって強制ではない）:
 
`````

## 報告

- 状態: 判断待ち / 続き: CHAT-1012-RVC-02
- ブランチ: work/1012-rvc
- ログ: https://github.com/retroeater/mj/blob/work/1012-rvc/docs/logs/CHAT-1012-RVC-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-rvc
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #492（着手中のコメントのみ。本文・状態・ラベルは変えていない）
- 判断が必要なこと:
  - マージの可否: 4文書の整理（成果物のコミット1つ。移した先は handover-archive-2026.md・skills.md・live-channel-write.md）。整理後の全文と移した先の差分は `## 経過`「手順3」。規則は消していない。origin/cloudflare は着手時（283713b9d）から進んでおらず、取り込みは不要だった
  - a1: #298 は 2026-10-10 にコミット f8e76c033（CHAT-1009-REV-02）の件名「docs: fix #298 deadline row …」で自動クローズされたと見られる（クローズのコメントは無い）。実装4の後半（`[sync-logs]` の規則・`sync-logs.yml`・`MJ_LOGS_TOKEN` の削除、2026-10-21）を受け持つ open の issue は無い（#504・#513・#492 を確認）。handover 5章の #298 の行と CLAUDE.md「作業ログ」の「#298 の実装4の後半」は直していない。#298 を開け直すか、別の issue に移すかの判断が要る。あわせて、行の「次の Billing の確認は 2026-10-14」は docs/decisions/operations.md「2026-10-12（CHAT-1012-ACF-01）」の「次の確認は 10/21」と食い違う（行を直さない指示のため、そのまま）
  - a6: 「この3文書」は 2026-09-22 の平野さんの判断で CLAUDE.md・handover.md・chat-side-operations.md を指す（94144c055）。instruction-template.md へ広げた決定は見つからないため「4文書」にはせず、3つの名前を書いた。instruction-template.md も対象にするかは平野さんの判断（今の template に出典の Chat-Ref は無い）
  - 目安に届かなかったもの: CLAUDE.md 22,108（目安 21,500）、chat-side-operations.md 24,711（目安 24,500）。規則を消さずに削れる言い換え・重複はここまでと判断した
  - live-channel-write.md は未マージの origin/work/1012-abm-map も変えている（冒頭の「例外」の行と「書き込みを許す先」の行）。この指示で足したのは末尾の「チャット側の指示」の節で、行は重ならない
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a23c9980）: https://github.com/retroeater/mj-logs/tree/main/guide/a23c9980

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a23c9980/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/283713b9.md
