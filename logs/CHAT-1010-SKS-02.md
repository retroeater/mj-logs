# CHAT-1010-SKS-02

- 着手日時: 2026-10-10
- 対象issue: #442、#511、#428、#530
- ブランチ: work/1010-sks
- 着手時HEAD: 872d35c9

## 指示

【Claude作成】Claude Code 向け指示：最強戦の年度プルダウンの先頭の項目を「歴代最強位」に変えて work/1010-sks をマージし、#442 を「保留」に、#511 を閉じる Chat-Ref: CHAT-1010-SKS-02 マージ: 承認済み（チャットで）。条件は「止まる条件」のとおり（決定と CHAT-1010-SKS-01 の変更・シートの変化・写真の揺らぎで説明できる差分だけ） 貼る時機: CHAT-1010-SKS-01 の判断待ちの後（いつでも） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1010-sks を続けて使う（CHAT-1010-SKS-01 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1010-SKS-01 のログの `## 報告` を読み、状態が判断待ちでなければ何もせず止まる。SKS-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1010-SKS-02` を足す。

目的
CHAT-1010-SKS-01 の変更（最強戦のページの共有ボタンを外す・#428 の saikyo/ の分）に、年度プルダウンの先頭の項目の文言の変更を足して本番に出す。あわせて #442 を「保留」に直し、#511 を閉じる。
決定（2026-10-10、平野さん）

* SKS-01 のプレビュー（トップ・2026年度・`?match=` 付きの2026年度）を見て、マージしてよい
* 年度プルダウンの一番上の項目「麻雀最強戦」を「歴代最強位」に変える（docs/notes/saikyo-page-design.md 3章の CHAT-0918-RK-12 の「麻雀最強戦」への統一を置き換える）
* #428 は今の形のまま（生成スクリプトの中で `<body>` にクラスを足す）。`scripts/lib/page.py` の `render_content()` には移さない
* 固定バーが2行になる幅のずれ（セッションの環境では 435/436px、文書と CSS は 459/460px）は今は直さない。横断レビューの第3弾の iPhone 実機確認のときに見る
* #442 のラベルを「状況: 保留」にし、本文の先頭に「2026-10-10 に廃止を保留にした」旨を書き足す
* #511 を閉じる。平野さんが 2026-10-10 に Search Console の URL 検査で、`https://ryoei.pro/saikyo/` と `https://ryoei.pro/saikyo/2026.html` の両方が「URL は Google に登録されています」「ページはインデックスに登録済みです」であることを確かめた（スクリーンショットはチャット側で確認）

前提（チャット側。平野さんの決定ではない）

* 年度プルダウンは `build_year_select()`（`scripts/generate_saikyo_pages.py`、要確認）が作り、トップは年度を渡さないので先頭の項目が選ばれた状態で出る（docs/notes/saikyo-page-design.md 3章、要確認）。トップのプルダウンに「歴代最強位」と出るのは見込みどおり
* `?match=` 付きの年度ページでは年度の項目が未選択になる（同 4章、要確認）。そのときに先頭の項目の文言が選択中の表示として出るなら、平野さんの見込みと違う見え方になりうるので止まる
* 文言の変更は生成スクリプトの中で済み、`scripts/lib/` は変わらない見込み。マージの push で `regenerate-page.yml` が saikyo_pages を再生成する（要確認）
* 先にマージ済みの `work/1010-xap`（CHAT-1010-XAP-03）が `docs/decisions/saikyo.md` の末尾に節を足しているため、取り込みで末尾の追記どうしが衝突する見込み（SKS-01 の報告）
* #511 の本文に、閉じた後に残る作業（例: /saikyo/ 配下で検索結果に出たページの一覧、要確認）があれば、それを扱う open の issue（#142 など、要確認）があるかを確かめる。無ければ起票する（クローズ時に残る作業は別の issue に起票する、平野さんの決定）
* 作業ブランチの片付けは delete-merged-branches.yml の自動削除に任せる（手で消さない）

手順

1. 年度プルダウンの先頭の項目の文言を「麻雀最強戦」から「歴代最強位」に変える。生成物・JS・テスト・文書（docs/notes/saikyo-page-design.md 3章・4章の該当箇所。今の内容を読んでから直す）で、この項目の文言として使っている所を洗い出してすべて直す（ページの題名・OGP・共有テキストなど、ほかの意味の「麻雀最強戦」は変えない）。トップ・年度ページ（`?match=` あり・なし）で、プルダウンに選択中として出る文言をログに表で書く。saikyo_pages を生成し直してコミットし、`python3 -m unittest discover -s scripts/tests` を通す
2. マージする: origin/cloudflare を取り込む。生成物でない文書が衝突したときは、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり。決定を docs/decisions/saikyo.md に足し（今の内容を読んでから）、`git push origin work/1010-sks:cloudflare` でマージする。マージ後の `regenerate-page.yml` と Workers Builds の結果を待つ（上限15分。超えたらその時点の状態を書き「未確認の項目」に回して先へ進む）。本番（URL に `?v=<未使用の値>` を付ける）で、`/saikyo/` と `/saikyo/2026.html` に「このページを共有」のボタンが無いこと、対局の共有ボタンがあること、`<body>` に `mj-saikyo-page` のクラスがあること、プルダウンの先頭の項目が「歴代最強位」であることを確かめる
3. issue:
   * #442: ラベルを「状況: 保留」にし（ほかの「状況:」ラベルが付いていれば外す）、本文の先頭に「2026-10-10 に平野さんが廃止を保留にした（CHAT-1010-SKS-01・SKS-02）」の旨を書き足す。今の本文を読んでから直し、ほかの記述は消さない
   * #511: 本文・コメントを読み、閉じた後に残る作業を洗い出す。残る作業を扱う open の issue があれば、その番号を #511 のコメントに書く。無ければ起票し、その番号を書く。上の決定（平野さんの確認の結果）をコメントして閉じる。「状況:」ラベルは外す
   * #428・#530: 閉じない。マージした旨（本番の SHA）をコメントする。#428 には、完了の条件の本番の CLS は未計測で、title/・/live・表のページの分が残る旨を書く

止まる条件

* SKS-01 のログの状態が判断待ちでない
* `?match=` 付きの年度ページ（またはトップ以外のどこか）で、先頭の項目の文言「歴代最強位」が選択中の表示として出る（直さずに表を書いて止まる）
* 文言の変更に `scripts/lib/`・共通部品（`scripts/lib/share.py`・`assets/share.js`）の変更が要る
* 取り込みで、両方の変更が両立しない文書の衝突が出た
* マージ前の差分（origin/cloudflare との比較）に、SKS-01 の変更・手順1の文言の変更・シートの変化・写真の揺らぎ（`_400x400`・srcset）・決定の記録のどれでも説明できないものがある。saikyo/ 以外の生成物・ページに差分がある
* テストが通らない（この変更で落ちると分かっているテストは直してよい）
* マージ後の check-run の失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書き、残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-SKS-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-SKS-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1010-sks
- ログ: https://github.com/retroeater/mj/blob/work/1010-sks/docs/logs/CHAT-1010-SKS-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-sks
- 確認用URL: 未
- マージ: 未
- issue: #442、#511、#428、#530
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 647a8db8）: https://github.com/retroeater/mj-logs/tree/main/guide/647a8db8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/116fea10.md
