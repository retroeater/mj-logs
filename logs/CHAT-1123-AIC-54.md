# CHAT-1123-AIC-54

- 着手日時: 2026-11-23
- 対象issue: #654
- ブランチ: work/1123-aic
- 着手時HEAD: 820ada9d1

## 指示

【Claude作成】Claude Code 向け指示：2回目の指示の tie の行に残る実データの例を「N回」「〇〇さん」の形に直して検査の除外（TIE_EXAMPLES）を外し、持ち越しの一覧を閉じて #654 に残す（AIC-53 の続き） Chat-Ref: CHAT-1123-AIC-54 マージ: 承認済み（チャットで、2026-11-23、平野さん）。条件は AIC 系列のマージの例外〈2026-11-02〉のとおり、テストが通ることと、本番の答えがデータと一致すること。この指示は2回目の指示の文を変えるので、答えの書き方が変わりうる。マージ後の本番の確かめで答えがデータと合わなければ、戻す指示を待たずに revert を cloudflare へ push して止まる。本番の確かめは、この指示で4問まで使う（1日10問の残りは、同じ日のほかの指示に残す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1123-aic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-aic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。識別子 `AIC` はこのチャットの系列（AIC-01〜53 で使用。AIC-17・24・31 は欠番）。CHAT-1117-AIC-53 のマージ（122f9e4a8）が origin/cloudflare の祖先であることを確かめる（祖先でなければ止まる）。

目的
AIC-53 で、2回目の指示の共通の一文（`answerSystem()` の tie の行）に実データの数・名前の例が残っていると分かった。AIC-47 と同じ形の誤り（Claude が例の数・名前を別の答えに写す）を前もって防ぐため、例を実データでない形に直し、検査の除外を外す。あわせて、2週続けて聞かれなかった持ち越しの一覧を閉じる。
決定（2026-11-23、平野さん）

* tie の行は、例を「N回」「〇〇さん」の形にする（A）。プログラムが同点の文を作る形（B）は、tie の答えが揺れたときに検討する
* 「40期後期のリーグ」（2026-11-16、context が WRC-R で、WRC-R として答えた）は、この指示で直さない。新しい持ち越しの一覧に載せる
* 持ち越しの一覧（正は CHAT-1109-AIC-44 のログの「未確認の項目」）は、週次の書き出しの1週目（11/09〜15）にも2週目（11/16〜22）にも聞かれなかった。2026-11-09 の決定どおり一覧から外し、#654 にコメントで残す

前提（チャット側。平野さんの決定ではない）

* 2週目の書き出しは、平野さんがチャットに渡した CSV（11/16〜22 の45行、重複なし）をチャット側が見た。来訪者の問いに、一覧の項目と「今期の首位は？」は無かった（「今期の首位は？」は 11/17 12:04 の AIC-52 の本番の確かめの1回だけ）
* 例は「N回が最多で、M人が並んでいます（〇〇さん・〇〇さん・〇〇さん）」の形にする。文面は実物に合わせてよい。直した後は `TIE_EXAMPLES` が要らなくなる（要確認）。`RULE_NUMBERS` はそのまま
* 新しい持ち越しの一覧は、このログの「未確認の項目」を正にする。中身は「context の大会と違う大会の期を聞かれたとき（例: WRC-R の context で「40期後期のリーグ」。2026-11-16 に1回）」の1行
* AIC-53 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1123-AIC-54` を足す

手順

1. 実物で確かめる: `workers/ask/src/ask.mjs` の tie の行と `workers/ask/test/prompt-examples.test.mjs` の `TIE_EXAMPLES` を読み、AIC-53 の報告の2つの引用と一致するかを確かめる。食い違えば止まる。#654 の状態を REST で確かめてログに書く。追記先（docs/notes/ask-worker.md「運用の学び」・docs/decisions/operations.md）の今の内容を読む
2. 直す: tie の行の2つの引用を前提の形に直し、`TIE_EXAMPLES` を外す。直す前の文に例を戻すと検査が fail し、直した後は pass することを確かめる（#310）。`node --test 'workers/ask/test/*.test.mjs'` と `python3 -m unittest discover -s scripts/tests` を通す。自己レビュー（docs/notes/self-review.md）を受ける
3. マージと記録: cloudflare へマージし、本番で同点の答えになる問いを4問まで聞く。どれが同点になるかは `workers/ask/data*.json` で選び、問い・選んだ理由・データの答え・本番の答えをログの表に書く。外れたら冒頭の「マージ:」の行のとおり revert して止まる。#654 に、閉じた持ち越しの一覧（CHAT-1109-AIC-44 の7項目を全件）と新しい持ち越しの1行をコメントで残す（本文・状態・ラベルは変えない）。docs/notes/ask-worker.md「運用の学び」と docs/decisions/operations.md に「## 2026-11-23（CHAT-1123-AIC-54）」として、上の決定と結果を書く

止まる条件

* 手順1の実物が AIC-53 の報告と食い違う
* 追記先が上の決定と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
* 未マージの work/ ブランチが `answerSystem()` か `prompt-examples.test.mjs` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 取り込みで生成物でない文書が衝突した。ただし、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 2回目のレビューの後に直した（マージせず判断待ち、docs/notes/self-review.md「収束」）
* マージ後の本番の確かめで、答えがデータと合わない（revert して止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」には新しい持ち越しの一覧を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-AIC-54.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-AIC-54 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 820ada9d）: https://github.com/retroeater/mj-logs/tree/main/guide/820ada9d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/820ada9d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a3916043.md
