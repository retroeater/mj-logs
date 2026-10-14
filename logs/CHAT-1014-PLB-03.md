# CHAT-1014-PLB-03

- 着手日時: 2026-10-14
- 対象issue: #567
- ブランチ: work/1014-plb
- 着手時HEAD: a8d9013

## 指示

【Claude作成】Claude Code 向け指示：（PLB-02 の続き）work/1014-plb を cloudflare へマージし、JWRC の G 列「結果」を残す決定を #567 に書く Chat-Ref: CHAT-1014-PLB-03 マージ: 承認済み（チャットで。この指示文を貼ることが承認） 貼る時機: いつでも（CHAT-1014-PLB-02 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-plb を続けて使う（PLB-02 の成果をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1014-PLB-02 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。判断待ちなら、その状態の末尾に `/ 続き: CHAT-1014-PLB-03` を足す。

目的
PLB-02 の成果（読む先を「ryoei.pro_選手」に切り替え、「桜花」「JWRC」を見出しで読む直し。issue #567）を本番に入れる。あわせて、消す列の決定の変更を #567 と docs/decisions に残す。
決定（2026-10-14、平野さん）

* work/1014-plb を cloudflare へマージする（PLB-02 の報告〈生成物の差なし、JS の33ページは旧と一致、切り替えなかった読み手は無し〉を読んだうえの承認）
* 「JWRC」の G 列「結果」（値のある行がある）は消さずに残す。予選の通過・敗退を載せる可能性があるため。そのための issue は、平野さんが将来別に起票する（この指示では起票しない）
* 「桜花」の C・G・Q〜U・W 列と、「JWRC」の C〜E・N〜U・W 列は、予定どおりマージ後に平野さんが消す

前提（チャット側。平野さんの決定ではない）

* PLB-02 の直しは見出しで列を引くので、JWRC の G 列が残っていても結果は変わらない見込み（要確認。G 列があってもなくても同じ列を指すことを、手順1で確かめる）
* マージで動く自動処理: cloudflare への push を Workers Builds が本番に反映する。`regenerate-page.yml` の自動再生成が走った場合、生成物の差はシートの変化による分だけで、この変更による差は無い見込み（PLB-02 の手順3の結果「差なし」から）
* 未マージの work/1014-skd・work/1014-jpl は、`generate_saikyo_pages.py`・`generate_title_pages.py` に近い変更を持つことがある。この指示ではそれらのブランチに触らない

手順

1. origin/cloudflare を取り込んだうえで、`python3 -m unittest discover -s scripts/tests` を通し、PLB-02 の手順3と同じ全ページの比較（同じ時点で origin/cloudflare のコードと作業ブランチのコード）をもう一度行う。見込みは差なし。「JWRC」の見出しから G 列「結果」だけを残した場合（C〜E・N〜U・W を除いた見出し）でも、`wrc_results.js`・`league_ranking.js` の引き直した問い合わせが同じ列を指すことを、PLB-02 と同じ方法（node）で確かめる。
2. #567 の本文の「マージ後の平野さんの手作業」を、JWRC は G 列を残す形に直し（消すのは C〜E・N〜U・W）、残す理由を1行書く。進み具合をコメントする。手作業の順番（改名・削除はマージ後。選手の追加・改名はマージ後は「ryoei.pro_選手」で行う）も本文に残す。
3. マージする（CLAUDE.md「ブランチ運用」、docs/notes/branch-operations.md「マージするとき」）。マージ後、Workers Builds の check-run の成否と、本番の ouka_results・wrc_results のどちらか1ページで旧と同じ表が出ることを、確かめられる範囲で確かめる（届かなければ「未確認の項目」へ）。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否には条件づけない）。#567 は、平野さんの手作業（改名・列の削除・1週間後の削除）が残るので閉じない。

止まる条件

* 手順1で、テストが通らない、または全ページの比較で差がある（差の種類・件数・対象を書いて止まる。マージしない）
* 手順1で、G 列を残した見出しで JS の問い合わせが別の列を指す
* 取り込みで衝突し、上の「作業ブランチ」の行の解き方に当たらない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい。自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、マージ後の平野さんの手作業（改名・JWRC は G 列を残した列の削除・1週間後の削除）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-PLB-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-PLB-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-plb
- ログ: https://github.com/retroeater/mj/blob/work/1014-plb/docs/logs/CHAT-1014-PLB-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-plb
- 確認用URL: なし
- マージ: 未
- issue: #567
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 913ca2c1）: https://github.com/retroeater/mj-logs/tree/main/guide/913ca2c1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/913ca2c1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f0502df9.md
