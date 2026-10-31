# CHAT-1031-PRO-14

- 着手日時: 2026-11-01
- 対象issue: なし（PRO-11 の続き。指示に issue の指定なし）
- ブランチ: work/1031-pro-nav
- 着手時HEAD: ce0aeee

## 指示

【Claude作成】Claude Code 向け指示：ナビの直しに2点（濃色固定のページは常に暗いドロップダウン・design.md の表）を足し、条件を満たせば cloudflare へマージする
Chat-Ref: CHAT-1031-PRO-14
マージ: 承認済み（チャットで。2026-10-31 に平野さんが、下の「止まる条件」の①〜③の機械的な条件をすべて満たしたら work/1031-pro-nav を cloudflare へマージしてよいと、ほかの問いと分けて答えた。1つでも満たさなければマージしない）
貼る時機: いつでも（CHAT-1031-PRO-11 のセッションの続きでも、新しいセッションでもよい）。CHAT-1031-PRO-12（work/1031-pro-sched）・PRO-13 とは別のセッションで並行してよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1031-pro-nav への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1031-pro-nav を続けて使う（PRO-11 の続きとマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成物の衝突は取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1031-PRO-11 のログ（作業ブランチの `docs/logs/CHAT-1031-PRO-11.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に ` / 続き: CHAT-1031-PRO-14` を足す。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「ブランチを削除するとき」を読む

## 目的
PRO-11 の「判断が必要なこと」への回答を入れ、ナビの配色の直しを cloudflare へマージする。

### 決定（2026-10-31、平野さん）
- 濃色固定のページ（live/・wayhome/・books/・exam/・mtsuku/・study/・最強戦の卓のページなど、PRO-11 の表で濃色固定とした種類）は、端末が明るい配色でも、ナビのドロップダウンを暗い地・明るい字にそろえる（PRO-11 で暗い配色のときだけにした規則の `@media` を、濃色固定のページについては外す）
- docs/notes/design.md「4. 部品」の表の崩れ（新サイトの行の最後の「出典」のセルが表示されない）を直す: 見出しに「出典」の列を足し、出典の無い古い行は空欄でそろえる
- 次の条件をすべて満たしたら、work/1031-pro-nav を cloudflare へマージしてよい: ① 濃色固定のページのドロップダウンが、明暗とも PRO-11 の「暗い配色の直した後」と同じ計算済みの色 ② それ以外のページ（`/title/`・`/jpml_pros.html`・`/houou/`・`/pro/`・`/no-such-page`・`/pro/taro.yamada.99/`）は、PRO-11 の版（ce0aeee）と計算済みのスタイルまで同じ ③ この指示で足す変更は `style.css` と docs だけ

### 前提（チャット側。平野さんの決定ではない）
- 確かめ方は PRO-11 と同じ（headless Chromium の計算済みのスタイルと撮影、360・390px × 明暗、ナビを閉じた状態とドロップダウンを開いた状態）。①は live/・wayhome/ を代表にし、PRO-11 の表の濃色固定のほかのページは計算済みの色だけでよい。撮影を `docs/logs/CHAT-1031-PRO-14/` に置く
- 取り込みの後（PRO-12 などが先にマージされていても）①〜③を確かめ直す
- マージ後の本番の確かめ: `https://ryoei.pro/live/?v=<マージのSHA>` の HTML が読む `style.css` に今回の規則が入っていること（本番への取得は数件に限る。Cloudflare のレート制限、docs/notes/cloudflare.md）。15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする（チャット側が後で確かめる）
- マージの後、cloudflare に入った push で動くワークフローの結果を確かめる（失敗したら、今回の変更による失敗か無関係な失敗かを分けて報告に書く。無関係なら原因を書いて残りの手順を進めてよい）
- 作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」のとおり
- 決定は完了時に docs/decisions/design-renewal.md（PRO-11 が書いた分野）に足す

## 手順
1. 0章の確かめの後、濃色固定のページの規則と design.md の表を直す
2. origin/cloudflare を取り込み、①〜③を確かめる。新しい文脈のサブエージェントに差分（PRO-11 の ce0aeee から先。生成物とログを除く）をレビューさせる
3. 止まる条件に当たらなければ cloudflare へマージし（`git push origin work/1031-pro-nav:cloudflare`、直前に再 fetch と `git merge-base --is-ancestor origin/cloudflare HEAD`）、本番の確かめ・push で動くワークフローの結果・作業ブランチの片付けを行う

## 止まる条件（1つでも当たればマージしない）
- 0章の確かめが通らない（PRO-11 のログの状態が「判断待ち」でない）
- 上の決定の①〜③のどれかを満たさない（どのページの何が違うかを書いて止まる）
- レビューで「重大」の指摘が残る（直して確かめ直しても、往復は CLAUDE.md の2回まで）
- 取り込みで、上の作業ブランチの行が解いてよいとしたもの以外の衝突が起きる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージせず報告する）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f797a06d）: https://github.com/retroeater/mj-logs/tree/main/guide/f797a06d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f797a06d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7aaff304.md
