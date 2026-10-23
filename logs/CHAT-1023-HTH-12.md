# CHAT-1023-HTH-12

- 着手日時: 2026-10-23
- 対象issue: #372
- ブランチ: work/1022-hth（CHAT-1023-HTH-11 の続き）
- 着手時HEAD: a8cfb0d3f

## 指示

【Claude作成】Claude Code 向け指示：最強戦の「対戦成績」の HTH-11 の直し（未公開のまま）を cloudflare にマージする（#372） Chat-Ref: CHAT-1023-HTH-12 マージ: 承認済み（チャットで） 貼る時機: いつでも（CHAT-1023-HTH-11 の続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1022-hth の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1022-hth を続けて使う（CHAT-1023-HTH-11 の判断待ちをマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-HTH-11 で判断待ちにした work/1022-hth を本番に入れる。`/saikyo/vs/` は noindex・どこからも辿れない形のまま（公開は #623）。新しい変更は足さない。
決定（2026-10-23、平野さん）

* HTH-11 の仮置き3点は HTH-11 の形のまま確定: 「もっと見る」で出せるのは30件まで（5件＋5回押して25件）、出し切ったらボタンを隠す、ペアの写真は 32px・間 4px
* HTH-11 の版を cloudflare にマージしてよい（チャット側がプレビュー〈見出し「注目の対戦カード（超技巧合戦・11月1日）」・ペアの写真が重ならない・5件の下に「もっと見る」〉と HTH-11 の画像〈before-360-light〉を確かめ、変わるもの〈`saikyo/vs/index.html`・対戦成績の CSS・JS・テスト・docs〉をチャットで示したうえでの承認）

前提（チャット側。平野さんの決定ではない）

* HTH-11 の時点の差分（HTH-11 のログの `## 報告`・`## 経過`）: `saikyo/vs/index.html`（約 268KB）・`assets/saikyo.js` と `style.css` の対戦成績の節・`generate_saikyo_pages.py` の対戦成績の関数・テスト・docs。既存の saikyo/ のページは変わらない（要確認。手順2で確かめる）
* origin/cloudflare は HTH-11 の後も進んでいる（2026-10-23 19:30 の時点で d9c51a05。要確認）。work/1023-skp（SKP の系列）がこの間にマージされていれば、取り込みで `generate_saikyo_pages.py`・`assets/saikyo.js`・`style.css` が変わる。対戦成績の関数・節の外の変更なら止めず、ログに書く
* マージの後の自動処理の見込み: Workers Builds の本番反映。翌朝からの `regenerate-saikyo.yml`（と週次の `all`）が `saikyo/vs/index.html` も生成し直す
* #372 は閉じない（公開〈#623〉まで開いたまま）

手順

1. CHAT-1023-HTH-11 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1023-HTH-12` を足す
2. origin/cloudflare を取り込み（要れば）、`python3 -m unittest discover -s scripts/tests` と `python3 scripts/regenerate.py --check saikyo_pages` を流す。saikyo_pages を再生成し、取り込んだ後の HEAD と origin/cloudflare の差分が、上の前提のファイルだけ（生成物は `saikyo/vs/index.html` だけ。取り込みでシートの変化が入ったときは、その変化で説明できる saikyo/ の差分を別に数えて書く）であることを確かめる
3. CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」のとおり `git push origin work/1022-hth:cloudflare` でマージする。本番で `https://ryoei.pro/saikyo/vs/?v=<マージのSHA>` が 200 で noindex を含み、中身が作業ブランチの `saikyo/vs/index.html` と同じこと、`assets/saikyo.js`・`style.css` が新しい版であること、`/saikyo/` が今のままであることを確かめる（15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする）。#372 にマージしたこと（公開は #623）をコメントし、ラベルは変えない。決定を docs/decisions/saikyo.md に足す。マージ後の作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否に条件づけない）

止まる条件

* 手順2のテスト・`--check` が通らない、または差分に上の説明できないものがある（種類と件数を書いて止まる）
* 取り込みで対戦成績の関数・JS の節・CSS の節が衝突する（`docs/decisions/saikyo.md` の末尾の追記どうしの衝突では止まらない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで残りの手順（本番の確かめ・#372 のコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-HTH-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-HTH-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fe3d9966）: https://github.com/retroeater/mj-logs/tree/main/guide/fe3d9966

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe3d9966/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
