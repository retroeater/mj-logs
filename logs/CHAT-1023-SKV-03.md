# CHAT-1023-SKV-03

- 着手日時: 2026-10-23
- 対象issue: #577
- ブランチ: work/1023-skv-play
- 着手時HEAD: c49be44e0

## 指示

【Claude作成】Claude Code 向け指示：最強戦の卓のページで、ライブ枠の URL の卓のボタンも「ABEMA で見る」ではなく「▶ 再生」にする Chat-Ref: CHAT-1023-SKV-03 マージ: 判断待ちで止まる（プレビューを見て決める） 貼る時機: いつでも（CHAT-1023-SKV-02 と並行してよい。別のセッションに貼るか、SKV-02 の完了の後に同じセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1023-skv-play〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv-play の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1023-skv-play を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skv-play origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦の卓のページで、ABEMA の URL がライブ枠（`abema.tv/channels/.../slots/...`。ABEMA ブック【3】手動補正に入れた放送枠）の卓は、ヒーローのボタンが「ABEMA で見る（新しいタブで開く）」になり、卓の回（`/video/episode/...`）の卓の「▶ 再生」と揃っていない（例: 本番の saikyo/2026/20261025-s1-a.html。2026-10-23 にチャット側が本番を取得して確かめた）。ライブ枠の卓も「▶ 再生」にする。
決定（2026-10-23、平野さん）

* ライブ枠の URL の卓も「ABEMA で見る」ではなく「再生」にする（例: saikyo/2026/20261025-s1-a.html）
* これは 2026-10-16（CHAT-1016-SKR-06）の 3「【3】に URL（ライブ枠）だけを入れた卓が『▶ 再生』ではなく『ABEMA で見る』になるのは、このまま公開し、直しは #577 で行う」のうち、ボタンの文言の部分を置き換える

前提（チャット側。平野さんの決定ではない）

* 文言・見た目は卓の回の卓の「▶ 再生」とまったく同じにする（チャット側の案。平野さんは「再生」と書いた）。行き先はライブ枠の URL のまま、新しいタブで開くのも同じ。ヒーローの画像（シリーズの画像）・出典「画像: ABEMA」は変えない
* ボタンの出し分けの場所（`scripts/generate_saikyo_pages.py` のどこか。URL の形で分けている見込み）は要確認。同じ部品を /live・帰り道など saikyo/ 以外が使っていれば、saikyo/ 以外の見た目は変えない（#566 の考え方）。変えずに済まない作りなら止まる
* #577（自動で切り替える仕組み）の状態は要確認。open なら、今回はボタンの文言だけで、【3】の「放送枠URL」の列などの残りは #577 に残す旨を #577 にコメントする（本文・状態・ラベルは変えない）
* コンテナには鍵が無いので、生成はセッションで動かさず `regenerate-page.yml` を作業ブランチを ref に手動実行して行う（docs/notes/cloud-sessions.md「鍵」）。差分の見込みは、2026-10-23 時点でライブ枠の卓 4ページ（saikyo/2026/20261025-s1-a・s1-b、20261101-s1-a・s1-b。CHAT-1023-SKV-01 のログの手順3の表）。シートの変化で別の差分が混ざることがある

手順

1. #577 の状態と本文、ボタンを出し分けているコード（関数・行）と、同じ部品を使う saikyo/ 以外のページの有無を確かめてログに書く。#577 が open なら着手中のコメントを書く
2. ライブ枠の卓のボタンを「▶ 再生」にする最小の変更をし、分岐の検査を `scripts/tests/` に1本足す（または今の検査を直す）。`python3 -m unittest discover -s scripts/tests` を流す
3. `regenerate-page.yml` を ref work/1023-skv-play・対象 saikyo_pages で手動実行し（作業ブランチにコミットされる）、コミットの `--stat` を種類に分けて全件書く: (a) ライブ枠の卓のページ（見込み 4） (b) それ以外の saikyo/ のページ (c) sitemap*.xml (d) その他。(a) の各ページのボタンの前後の HTML を引用する。(b)〜(d) はファイルごとに変わった理由を書く（シートの変化なら何の変化か）。確認用 URL を最終報告に書き、判断待ちで止まる。docs/decisions/saikyo.md に今回の決定を足す

止まる条件

* 前提（ボタンの出し分けの作り、#577 の中身）が食い違う
* saikyo/ 以外のページの見た目を変えずに直せない
* 手順3の (d) がある、または (b) に今回の変更で説明できない差分がある（内訳を書いて止まる。コミットは戻さない）
* 未マージの work/ ブランチ（特に work/1022-hth・work/1023-skp）が `scripts/generate_saikyo_pages.py` の同じ関数・同じ行を変えている、または取り込みで衝突する（別の関数の変更では止まらず、ログに書く。生成された saikyo/ のページの衝突は CLAUDE.md「ブランチ運用」のとおり取り込む側で生成し直して解くので、止まる理由にしない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（cloudflare へ入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c49be44e）: https://github.com/retroeater/mj-logs/tree/main/guide/c49be44e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c49be44e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ee47bdbe.md
