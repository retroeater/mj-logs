# CHAT-1103-PLG-01

- 着手日時: 2026-11-03
- 対象issue: なし
- ブランチ: work/1103-plg
- 着手時HEAD: 11e7c6551

## 指示

【Claude作成】Claude Code 向け指示：hookify の試行の振り返り（規則ファイルを消し、skills.md の規則の表を消し、PLG-10 を片付ける） Chat-Ref: CHAT-1103-PLG-01 マージ: 承認済み（チャットで） 貼る時機: いつでも（新しいセッションは任意。平野さんは claude.ai から hookify を外した後） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-20 に入れた hookify の規則（`.claude/hookify.warn-sitemap-lastmod.local.md`、sitemap に lastmod を手で書くと warn）の振り返り（予定 2026-11-04 を前倒し）。規則と hookify をやめ、文書と判断待ちのログ（CHAT-1020-PLG-10）を片付ける。
決定（2026-11-03、平野さん）

* hookify の規則ファイル `.claude/hookify.warn-sitemap-lastmod.local.md` を消す。hookify は claude.ai のアカウントから外した（平野さんの手作業、済み）
* docs/notes/skills.md「hook の層の分け方」の方針（止める規則はリポジトリの hook かテスト・CI、注意を出す規則は hookify）は、次に必要になったときの判断基準として残し、規則の表（この 1 本）は消す
* 理由: 2 週間（2026-10-20〜11-03）で規則が働いた記録が無い。手で書いた lastmod は `regenerate-sitemap.yml`（HTML を含む push）と週次の全ページ再生成で git から計算し直されて自動で直る（docs/notes/sitemap-lastmod.md）。hookify はツールの呼び出しのたびに動く
* この指示の中で cloudflare へマージしてよい（平野さんがチャットで承認）
* CHAT-1020-PLG-10 の未確認の項目（実際の warn の表示）は、規則をやめたので確かめない

前提（チャット側。平野さんの決定ではない）

* `.claude/` の変更（hook・settings・hookify の規則ファイル）は、ほかのコードと同じ運用でセッションから変えてマージする（docs/notes/skills.md「git の hook」、CHAT-1101-WFL-29）。分類器に拒否されたら、その旨を報告して止まる
* 規則が働いた記録が無いことは、チャット側が mj-logs の 10/21 以降のログを `hookify`・`warn-sitemap-lastmod` で検索して確かめた（hookify は発火を記録しないので、回数は確かめられない）
* hookify を外した後でも、このセッションが外す前に始まっていれば `claude plugin list` に残ることがある（2026-10-20 の PLG-07 では、同じセッションの途中で同期し直されて消えた）

手順

1. `claude plugin list` を取り、「Synced from claude.ai」に hookify が無いことを確かめてログの `## 経過` に一覧を写す（残っていれば、その旨とセッションの開始時刻を書き、止まらない）。`.claude/` の下で hookify の規則ファイル（`hookify.*.local.md`）を全部挙げ、この 1 本だけであることを確かめる（ほかにあれば止まる）。CLAUDE.md・docs/ の中で `hookify`・`warn-sitemap-lastmod` を参照している箇所を全部挙げる（docs/logs/ を除く）。
2. 規則ファイルを `git rm` で消す。docs/notes/skills.md の「hook の層の分け方」から規則の表と、規則ファイル・試験のしかたの箇条（hookify の規則の置き場所・書式・試験）を消し、方針の箇条は残して「hookify は 2026-11-03 にやめた（規則が働かず、守る対象〈sitemap の lastmod〉が自動で直るため。CHAT-1103-PLG-01）。注意を出す規則が要るときは、プラグインを入れ直して規則を足す」の趣旨を 1〜2 行足す。「plugin の扱い」「コンテキストの負荷」に残っている plugin の件数・hookify の数があれば今の値（手順 1 の一覧）に直す。手順 1 で見つけたほかの参照も同じ趣旨で直す。docs/decisions/operations.md に決定を追記する（CLAUDE.md「作業ログ」節のとおり。PLG-08・09 の決定を置き換える旨を書く）。
3. CHAT-1020-PLG-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-PLG-01` を足す。テスト（`python3 -m unittest discover -s scripts/tests`）を回して結果をログに書き、冒頭の「マージ:」の行のとおり cloudflare へマージする。

止まる条件

* 手順 1 で hookify の規則ファイルがこの 1 本以外にもあるとき
* 手順 2 で docs/notes/skills.md の記述と決定が矛盾し、判断が要るとき（同じ趣旨の記述があるだけなら止まらない）
* `.claude/` の変更が分類器に拒否されたとき（別の手段を試さずに止まる）
* 差分が、決定で説明できる範囲（規則ファイルの削除、docs/notes/skills.md、手順 1 で挙げた参照箇所、docs/decisions/operations.md、PLG-10 とこのログ）の外に出たとき
* テストが今回の変更で失敗したとき（無関係な失敗なら原因を書いて進んでよい）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが docs/notes/skills.md の同じ節・`.claude/` の同じファイルを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-PLG-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-PLG-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1103-plg
- ログ: https://github.com/retroeater/mj/blob/work/1103-plg/docs/logs/CHAT-1103-PLG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f520341f）: https://github.com/retroeater/mj-logs/tree/main/guide/f520341f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f520341f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/11e7c655.md
