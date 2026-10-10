# CHAT-1010-XAP-06

- 着手日時: 2026-10-10
- 対象issue: #514
- ブランチ: work/1010-xap
- 着手時HEAD: bb1a9342

## 指示

【Claude作成】Claude Code 向け指示：SNS ブックの共有を直した後の続き。init → 毎日の更新 → Worker の表・文書 → マージ（#514） Chat-Ref: CHAT-1010-XAP-06 マージ: 承認済み（チャットで。CHAT-1010-XAP-05 の承認と同じ範囲）。条件は「止まる条件」のとおり 貼る時機: 平野さんが SNS ブックを `live-channel-writer` に「編集者」で共有した後 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-xap を続けて使う（CHAT-1010-XAP-05 で作った SNS ブックの仕組みがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1010-xap の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1010-XAP-05 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。その状態の末尾に `/ 続き: CHAT-1010-XAP-06` を足す。

目的
CHAT-1010-XAP-05 で書き込みの権限が無く止まった SNS ブックの作業を、共有を直した後に最後まで進める（init・毎日の更新・Worker の表・文書・マージ）。
決定（平野さん）

* CHAT-1010-XAP-05 の「決定」のとおり
* 2026-10-10: 共有は平野さんが直して、続きを進める

前提（チャット側。平野さんの決定ではない）

* 初回の毎日の更新で X API を 30 件（約 $0.30）呼び、残り 19 件は「上限で未取得」になり翌日に呼ぶ見込み（XAP-05 の報告。「連盟プロ以外」で X ID があり画像の無い 49 人）。これは決めてある1回の上限 30 件の内なので、そのまま進めてよいとチャット側は考えている（平野さんへはチャットで伝えた）
* XAP-05 の手順1の数（【1】の予定 1,864 行など）は着手時点で変わっていることがある。比べるのは、この指示の中で読み直した数
* そのほかは CHAT-1010-XAP-05 の「前提」のとおり（note は取らず到達だけ、【4】には書かない、【2】の入力の守り、生成・旧列・検知は変えない）
* 使う skill は無い

手順

1. 確かめる: #514 に他セッションの着手中コメントが無いこと。mode check を作業ブランチで手動実行し、「編集者か: はい」になること、4つのタブの見出し・行数（【2】【4】に入力が無いか、平野さんがすでに入れていればその件数）を表でログに書く
2. 書く: (a) init の dry-run で書く予定の行数・列ごとの件数を出し、手順1で読み直した「プロ」「連盟プロ以外」の数と比べる。(b) init を実行し、ブックを読み直して、写した値が元の列と全セル一致することを確かめる。(c) 毎日の更新の dry-run で X API を呼ぶ予定の件数を出し、続けて実際に1回実行する。【2】の入力が1つも変わっていないこと、X API を呼んだ件数、【3】の状態ごとの件数を確かめる。(a)〜(c) を表でログに書く
3. 仕上げてマージする: Worker の表に毎日の更新の行を足す（04:30 の検知より前。時刻は表の今の行と重ならないように選ぶ。表を変える前に、その時点の `origin/cloudflare` の表と未マージのブランチを確かめる）。文書を作る・直す（docs/notes/sns-book.md を新しく作る、live-channel-write.md に例外の1行と書き込み先の追記、static-generation.md・scheduler-worker.md の一覧。どれも先に今の内容を読み、古い記述は置き換える）。`python3 -m unittest discover -s scripts/tests` と `node --test` を通し、cloudflare へマージする（仮置きの `update-sns-book.yml` は作業ブランチの版で置き換わる）。マージ後の check-run（「Workers Builds: mj-scheduler」を含む）を確かめる。#514 にコメントする（閉じない）

止まる条件

* CHAT-1010-XAP-05 の状態が「判断待ち」でない、#514 に他セッションの着手中コメントがある、work/1010-xap がリモートに無い
* 「編集者か」が「はい」にならない（平野さんに頼むことを報告に書く）
* 【2】【4】にすでに平野さんの入力があって、init がそれを上書きしうる（上書きせずに止まる）
* 【1】の行数が「プロ」の行数＋「連盟プロ以外」の行数と一致しない、写した値が元の列と1セルでも食い違う（ずれは 0 件まで）
* 毎日の更新の後に【2】の入力が1つでも変わった・欠けた
* X API の呼び出しが1回 30 件を超えた、認証・クレジットの失敗
* 手動実行の完了を待つのは1回15分まで。超えたらその時点の状態を書いて止まる（マージしない）
* 変えるファイルが CHAT-1010-XAP-05 の止まる条件の範囲（scripts/ の SNS ブックの仕組み・`sheets_write.py`・テスト、.github/workflows/update-sns-book.yml、workers/scheduler/ の表とテスト、docs/）を出る。生成のスクリプトやページの出力が変わる（変えずに止まる）
* 直す先の文書が決定と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述は置き換え・拡張してよく、どう処理したかを報告に書く）
* マージ後の check-run の失敗のうち、今回の変更による失敗（無関係な失敗なら原因を報告に書いて先へ進む。自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1・2の表、Worker の表に足した行、文書の直しの扱い、#514 へのコメントの URL、XAP-05 のログの状態の直しがログにある
* 次の指示（生成の読む先を SNS ブックへ切り替える）に向けて平野さんが決めること・すること（【2】への手入力を始める時期、旧列を空にする日など）を、報告の「判断が必要なこと」に書く
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-XAP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-XAP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子確認: `git log --all --grep=CHAT-1010-XAP-06` の到達なし
- 作業ブランチ: work/1010-xap はローカル・リモートとも bb1a9342。`origin/cloudflare` は祖先でない → ログの push の後に merge で取り込む
- 手順0: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1010-XAP-05 の状態は「判断待ち」→ 末尾に ` / 続き: CHAT-1010-XAP-06` を足した（このコミット）
- 雛形の行: Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順がそろっている

## 報告

- 状態: 作業中
- ブランチ: work/1010-xap
- ログ: https://github.com/retroeater/mj/blob/work/1010-xap/docs/logs/CHAT-1010-XAP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-xap
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 22ca975d）: https://github.com/retroeater/mj-logs/tree/main/guide/22ca975d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/22ca975d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b4d859a5.md
