# CHAT-1026-SKP-14

- 着手日時: 2026-10-26
- 対象issue: #627・#623・#372・#626
- ブランチ: work/1026-skp-pub（公開）・work/1024-skp-vid（動画の制作のスクリプト）
- 着手時HEAD: 7202705d5（work/1026-skp-pub）

## 指示

【Claude作成】Claude Code 向け指示：最強戦のリニューアル（新トップ・年度別・個人成績・ランキング・質問欄 #627、対戦成績 #623）を公開し、告知動画の制作のスクリプトをマージする Chat-Ref: CHAT-1026-SKP-14 マージ: 承認済み（チャットで。条件は「止まる条件」）。告知の期間中の変更を承認（2026-10-26、平野さん） 貼る時機: いつでも（CHAT-1026-SKP-13 のセッションに続けて貼ってよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1026-skp-pub と work/1024-skp-vid の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。公開の変更は work/1026-skp-pub を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1026-skp-pub origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。動画のスクリプトは未マージの work/1024-skp-vid をそのままマージする（`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい。rebase しない）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり、取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
告知動画が確定したので、最強戦のリニューアルを公開する。公開の変更は、#627 のコメント「公開の指示に入れる項目」（CHAT-1023-SKP-02 で作り、SKP-03・04・13 で更新）と、#623 の本文のチェックリスト。公開が済んだら、平野さんが X に投稿する（告知の期間は投稿まで続く）。
決定（2026-10-26、平野さん）

* 告知動画は第2版（CHAT-1026-SKP-13 の 54.97秒）で確定。制作のスクリプトと文書をマージする
* 公開は今行い、済みしだい平野さんが X に投稿する
* 公開の後、#372・#623 を閉じる（2026-10-23 の決定）
* （公開に入れる項目の決定は既にある: CHAT-1023-SKP-02 の決定 1・2・5・6・19。docs/decisions/saikyo.md の 2026-10-23〈CHAT-1023-SKP-02〉の節）
   * 1: 新しいトップを `/saikyo/` に切り替え（`MENU_PUBLIC = True`）、仮の URL `saikyo/next/` と `_redirects` の next の行を消す
   * 2: 新トップのメニューの4行目に対戦成績「出場者同士の勝敗と同卓の記録」
   * 5: 年度ページの年度の選択の先頭「歴代最強位」の行き先を年度別 `/saikyo/years/` へ
   * 6: jpml_pros と卓のページの「すべて見る」は個人成績 `/saikyo/players/?name=` へ直接張り替える。外で共有された `/saikyo/?q=` だけ、新トップの JS が個人成績へ移す。この転送は、公開から3か月後の転送の棚卸しで `?q=` の流入が無いと確かめたら外す
   * 19: 個人成績の名前の面の下に「対戦成績を見る ›」（`/saikyo/vs/?name=<選手名>`）
* 「名前かな」の空欄（#628）・順位の空欄（#630）が残っていても、公開は止めない（2026-10-23）

前提（チャット側。平野さんの決定ではない）

* 公開の作業の一覧の正は docs/new-page-checklist.md（noindex を外す・サイトマップ・`llms.txt`・navbar・OGP・title の確かめなど）と、#623 の本文のチェックリスト（`VS_PUBLIC = True` など）。navbar の「最強戦」は今のまま `/saikyo/` へ行く（要確認）
* 今のトップの検索欄の直下の対戦成績の入口（`build_vs_entry()`）は、トップの切り替えで要らなくなる見込み。新トップのメニューの4行目と重なるなら消す（#623 の本文の「入口の位置」も直す。HTH からの引継ぎ）
* 年度別 `/saikyo/years/` は今のトップから検索を外した形で、歴代最強位の面だけ（CHAT-1023-SKP-02 の決定4）
* 新トップ・年度別・個人成績・ランキング・対戦成績のサイトマップと `llms.txt` の行を足す。canonical・og:url は各ページ自身
* マージ後の自動処理の見込み: Workers Builds の本番反映。翌朝の `regenerate-saikyo.yml` と週次の `all` が新しいページも生成し直す
* 投稿文の案はチャットで平野さんに渡す（この指示では扱わない）

手順

1. CHAT-1026-SKP-13 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1026-SKP-14` を足す。work/1024-skp-vid（動画の制作のスクリプト・README・docs/notes/page-announcement.md の実例の行・ページの資料の「告知動画」の節）を、origin/cloudflare を取り込んでから `git push origin work/1024-skp-vid:cloudflare` でマージする（差分は `scripts/promo_video/saikyo_renewal/` と docs だけであること）
2. work/1026-skp-pub で、上の決定 1・2・5・6・19 と docs/new-page-checklist.md・#623 のチェックリストの公開の作業を入れる。テストを足し、`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`regenerate-page.yml` の check（saikyo_pages）を流す。生成し直し、origin/cloudflare との差分を種類ごとに数えて書く（`?q=` の転送は外す条件をコードのコメントと docs/notes/redirects.md に書く）。`git push origin work/1026-skp-pub:cloudflare` でマージする
3. 本番（`?v=<マージのSHA>`）で確かめる: `/saikyo/` が新トップ（メニュー4行・質問欄、noindex なし）、`/saikyo/next/` が 404、`/saikyo/years/`・`/saikyo/players/?name=桑田憲汰`（「対戦成績を見る ›」がある）・`/saikyo/ranking/champion/`・`/saikyo/vs/` が noindex なし、`/saikyo/?q=桑田憲汰` が個人成績へ移る、jpml_pros の最強戦のリンクと卓のページの「すべて見る」が個人成績へ行く、`sitemap-saikyo.xml`・`llms.txt` に新しいページがある、本番の質問欄（新トップ）に「2025年度の最強位は？」を1回送って答える。#623 の本文の古い2点を直してから、#372・#623 を閉じる（公開したことと、このログを書く）。#626・#627 も、残る作業が無ければ閉じる（残るものは別の issue にあることを書く。#628・#630）。docs/decisions/saikyo.md に公開したことを足す。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う

止まる条件

* 手順1の差分に、動画のスクリプトと docs 以外がある
* テスト・check が通らない
* origin/cloudflare との差分に、上の決定と公開の作業で説明できないものがある（年度・対局・卓のページは、年度の選択の先頭の行き先と「すべて見る」の行き先とシートの変化だけ許す。種類と件数を書いて止まる）
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`_redirects`・`llms.txt`・`scripts/generate_jpml_pros.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の本番の確かめで、`/saikyo/` が新トップにならない・noindex が残る・`?q=` が移らない（15分で本番が古いままなら「未確認の項目」に回して、issue は閉じずに完了にする）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1026-SKP-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1026-SKP-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1026-skp-pub
- ログ: docs/logs/CHAT-1026-SKP-14.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7202705d）: https://github.com/retroeater/mj-logs/tree/main/guide/7202705d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7202705d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f9a809f1.md
