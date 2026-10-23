# CHAT-1024-SHR-01

- 着手日時: 2026-10-23
- 対象issue: #530（関連: #526・#362）
- ブランチ: work/1024-shr
- 着手時HEAD: 066dfde

## 指示

【Claude作成】Claude Code 向け指示：共有ボタンの見直し（#530）の残りの決定を記録する（文書と issue のコメントだけ。コードは変えない）
Chat-Ref: CHAT-1024-SHR-01
マージ: ドキュメントのみ（docs/decisions・docs/notes・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-shr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-shr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
#530（サイト全体の共有ボタンの見直し）の「決めること」の残り（共通部品の扱い・books/・#526 から移した G3-07・G4-09）について、平野さんの決定を docs/decisions と #530 に記録する。共通部品を消す作業そのものは、#362（live/ の作り直し、未マージの work/1017-lvp）のマージの後に別の指示で行う。

### 決定（2026-10-24、平野さん）
- 共有ボタンの共通部品（`scripts/lib/share.py`・`assets/share.js`・`style.css` の「共有ボタン(共通部品、#409)」の節と、それを確かめるテスト）は消す。消すのは #362 のマージで live/ から共有ボタンが外れた後（#362 のマージ前に消すと live/ が壊れ、work/1017-lvp と衝突するため）
- 凍結中の books/ の共有ボタン（旧実装 `.mj-book-share`・`assets/books.js`）は凍結のまま残す（#530 の対象外。2026-09-22 の書籍ページの開発凍結の決定を崩さない）
- #526 から移した G3-07・G4-09 は、共通部品を消すので直さない（不要）

### 前提（チャット側。平野さんの決定ではない）
- チャット側が 2026-10-24 に origin/cloudflare（066dfde）と #530 の本文・コメントを読んで確かめたこと（食い違えば止まる）:
  - title/・帰り道・最強戦のページ全体の共有と、最強戦の対局ごとの共有は外し済み（#530 のコメント）
  - origin/cloudflare で共通部品を読むのは live/ だけ（`git grep -l 'mj-share\|share\.js'` の HTML は live/ の923件）。live/ から外す変更は未マージの work/1017-lvp（CHAT-1017-LVP-06、docs/decisions/live.md に「共有ボタンを live/ から外す（#530 …）」の行がある）
  - `assets/league.js` の2行目のコメントに share.js の名残がある（houou/ 等の HTML は共有ボタンを持たない）。直すのは消す指示の中で行う（この指示では変えない）
- 決定の書き場所は docs/decisions/ の分野のファイル（#530 の過去の決定は wayhome.md・saikyo.md・title.md・site-review.md にある）。どのファイルにするかは README.md の分野表に合わせて決めてよい。分野表の記述が実態とずれれば直す
- docs/notes/ で共通部品を「残す」「#530 で決める」と書いている箇所（例: `docs/notes/design.md` の共有ボタンの行、`docs/handover.md` 4章の共有ボタンの行）は、今回の決定（#362 の後に消す）に合わせて直してよい。直した箇所はログに引用する

## 手順
1. #530・#526・#362 の本文・コメント・状態（Open/Closed）を読み、上の「前提」の事実と食い違わないか確かめる（食い違えば止まる）。#530 に着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが、この指示で変える docs/decisions・docs/notes の同じ節を変えていないかを確かめる
2. 上の決定を docs/decisions に記録し、前提の3つ目のとおり docs/notes・handover の記述を直す
3. #530 に、決定3件と「残りは #362 のマージ後に共通部品を消す指示で行い、そのときに #530 を閉じる」ことをコメントする（閉じない。本文・ラベルは変えない）。コメントの末尾に `Chat-Ref: CHAT-1024-SHR-01`

## 止まる条件
- 前提の事実（外し済みのページ、共通部品を読むのが live/ だけ、work/1017-lvp の live/ の決定）が実物と食い違う
- #530 または #362 が Closed、または #530 に他セッションの着手中コメントがある（この作業と重なるもの）
- 未マージの work/ ブランチが、この指示で変える文書の同じ節・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- コード（scripts/・assets/・style.css・生成物）を変える必要が出た（この指示では変えない）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SHR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SHR-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 着手前の確認: `CHAT-1024-SHR` のコミットは全リモートに無し。`SHR` の重複無し。0章ゲートは実装を含まないため対象外。#530・#362 は Open（#526 は Closed だが、G3-07・G4-09 は #530 に移し済みで止まる条件に当たらない）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠け無し
- ブランチ: `work/1024-shr` はローカル・リモートとも無し → `git checkout -b work/1024-shr origin/cloudflare`（066dfde）
- 手順1（前提の確認、origin/cloudflare 066dfde）:
  - `git grep -l 'mj-share\|share\.js' -- '*.html'` は live/ の923件だけ（title/・wayhome/・saikyo/ ほかは0件）
  - `assets/league.js` 2行目「share.js より先に実行されるため、共有ボタンの差し込みはここで済ませる。」（変えない）
  - work/1017-lvp の docs/decisions/live.md 177行目「共有ボタンを live/ から外す（#530。共通部品 `scripts/lib/share.py`・`assets/share.js` の扱いは #530 で決める）」
  - #530 のコメント: title/（本文）・帰り道（2026-10-10）・最強戦（2026-10-10、対局ごとは #549 で 2026-10-16）の外し済みを確認。他セッションの着手中コメントは CHAT-1010-WHS-01 の1件で、帰り道の分（完了済み）。重ならない
  - 未マージの work/ ブランチのうち、今回触る4ファイル（docs/decisions/README.md・site-review.md・docs/handover.md・docs/notes/design.md）を変えているものは無し。work/1017-lvp は docs/notes/live-page-design.md の共有ボタンの記述を変えるので、そのファイルは触らなかった
  - #530 に着手中コメント（issuecomment-6376528076）
- 手順2: 書き場所は分野表の site-review.md（G3-07・G4-09 の出どころで、#530 の「サイト全体」の分野に当たる）。分野表の site-review.md の行に「サイト全体の共有ボタンの見直し（#530）」を足した。直した箇所:
  - docs/handover.md 4章「現行サイトに作り込みすぎない」: 「見直しは #530。books/ は凍結中で旧実装」→「共通の部品は #362 のマージで live/ から外れた後に消す（#530、docs/decisions/site-review.md）。books/ は凍結中で旧実装のまま残す」
  - docs/notes/design.md の共有ボタンの行: 「が絞り込み欄の右と最強戦の帯（帰り道のヒーローの形は #530 で外した）」→「が live/ の絞り込み欄の右（帰り道・最強戦からは #530、title/ からは #277 で外した）」、末尾に「部品は #362 のマージで live/ から外れた後に消す（#530）」（最強戦の帯は #549 で無くなっていたため、あわせて直した）
  - そのまま残したもの: docs/notes/title-pages.md「共有ボタンのサイト全体の見直しは #530」（参照として正しい）、docs/decisions/wayhome.md の「#530 で別に扱う」（決定の記録は書き換えない）、docs/new-site-design.md（docs/notes の外で、共通部品を残すとは書いていない）
  - `python3 -m unittest discover -s scripts/tests`: 1563件 OK。handover.md 22,221 バイト（上限内）
  - コミット c916d0838
- 手順3: #530 に決定3件と「残りは #362 のマージ後に共通部品を消す指示で行い、そのときに閉じる」をコメント（issuecomment-6376539876）。閉じていない。本文・ラベルは変えていない

## 報告

- 状態: 完了
- ブランチ: work/1024-shr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1024-SHR-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1024-shr
- 確認用URL: なし
- マージ: 済（fast-forward。cloudflare の先頭はこのログを含むコミット）
- issue: #530（コメントのみ。閉じていない）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj afc3145d）: https://github.com/retroeater/mj-logs/tree/main/guide/afc3145d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/afc3145d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/066dfde6.md
