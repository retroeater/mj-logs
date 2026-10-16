# CHAT-1016-SKR-06

- 着手日時: 2026-10-16
- 対象issue: #549、#425、#566、#382、#383、#530、#458
- ブランチ: work/1015-skr-pub
- 着手時HEAD: 8ab674ba5

## 指示

【Claude作成】Claude Code 向け指示：最強戦の新しいページを公開する（#549。work/1015-skr-pub と告知動画のスクリプト work/1015-skr-vid をマージし、公開後の確かめと issue の片付け） Chat-Ref: CHAT-1016-SKR-06 マージ: 承認済み（チャットで。平野さんが 2026-10-15 にプレビューを iPhone で確かめて「この形で公開してよい」、2026-10-16 に告知動画を「確定でよい」と答えた。下の止まる条件に当たったらマージしない） 貼る時機: いつでも（新しいセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr-pub の push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-skr-pub を続けて使う（CHAT-1015-SKR-02 の判断待ちのマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込む（rebase しない）。未マージの origin/work/1015-skr-vid（CHAT-1016-SKR-05、告知動画のスクリプト）も merge で取り込む。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページ（`saikyo/`・サイトマップなど）だけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。生成物でない文書の衝突は、両方の変更が両立するもの（追記どうし・隣り合う行）なら両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1015-SKR-02 と CHAT-1016-SKR-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-06` を足す（このブランチ上のログで）。

目的
最強戦の新しい年度・対局・卓のページとトップの作り直しを公開する（#549 の段2）。`work/1015-skr-pub`（CHAT-1015-SKR-02）と `work/1015-skr-vid`（告知動画の制作のスクリプト、CHAT-1016-SKR-05）を cloudflare にマージし、本番で確かめ、関係する issue を片付ける。
決定（2026-10-15〜16、平野さん）

1. CHAT-1015-SKR-02 のプレビューを確かめ、この形で公開してよい（2026-10-15。docs/decisions/saikyo.md の CHAT-1015-SKR-03 の決定5 に記録済み）
2. 告知動画の初版（`saikyo-promo-b.mp4`、23.0秒、CHAT-1016-SKR-05）を確定する。場面2（「桑」1字で結果の面が開く）はこのまま。場面4・5 の 2025 決勝の卓の ABEMA の回（`68-38_s25_p55`）は「麻雀最強戦2025 ファイナル2ndStage 決勝卓」で、決勝の回で正しい（平野さんが ABEMA で確かめた）（2026-10-16）
3. 【3】手動補正に URL（ライブ枠）だけを入れた卓は「▶ 再生」ではなく「ABEMA で見る」になる（CHAT-1015-SKR-03）が、このまま公開し、直しは #577 で行う（2026-10-16）
4. ABEMA の卓ごとの動画は毎週金曜ごろに2週間ほど前の対局の分が出る見込み。平野さんが継続的に【1】へ取り込み、【3】のライブ枠の行を消す運用にする（毎週金曜の予定はチャット側がカレンダーに作った）（2026-10-16）

前提（チャット側。平野さんの決定ではない）

* マージの指示へ回された作業の一覧は CHAT-1015-SKR-02 のログの経過の表「#549 の実装の一覧」（「マージの指示」の行）。`docs/notes/static-generation.md`「ページの一覧」の段落の行（最強戦の新しいページを「noindex・メニュー未掲載、公開は #549」と書いた1行）は、未マージの work/JBK と同じ行だったので SKR-02 で直していない（要確認: JBK が cloudflare に入っていれば取り込み後に直す。未マージなら JBK と同じ行を変えることになるので、直さずにログに書き、#549 のコメントに残りとして書く）
* 2026-10-16 09:18 に平野さんが `regenerate-saikyo.yml` を cloudflare で手動実行し（ABEMA ブック【1】に3回足した分と【3】のライブ枠の行）、回が当たった卓が 603→606 になった（コミット 1447edda1）。毎朝の再生成でも cloudflare の `saikyo/` は進む。取り込み後に `python3 scripts/regenerate.py saikyo_pages` で生成し直す
* 公開の直後に平野さんが Cloudflare のリダイレクトルール（`?match=` の Single Redirects、#549 の本文の手順）を作る。この指示の実行中には無い見込みなので、`?match=` の転送は確かめず「未確認の項目」に書く（チャット側が後で確かめる）
* 告知動画はリポジトリに入れない（docs/notes/page-announcement.md）。実例の表に「最強戦（2026-10-16 公開）」の行を足す（`scripts/promo_video/saikyo/`、作り直しは同じフォルダの README）

手順

1. 確かめる・まとめる:
   * #549・#566・#425 に他セッションの着手中コメント（未解除）が無いこと。#549 に着手中のコメントを書く
   * 作業ブランチを用意し、origin/cloudflare と origin/work/1015-skr-vid を取り込み、`saikyo_pages` を生成し直す。`python3 -m unittest discover -s scripts/tests` が通ること
   * 生成物の差（origin/cloudflare との差）を CHAT-1015-SKR-02 のログの生成物の差の表と同じ種類に分けて表にする。SKR-02 の後に入ったシートの変化（2026年度の卓の ABEMA のリンク・【3】のライブ枠の「ABEMA で見る」、上の 603→606 の3卓とその対局・関連のカード）で説明できる差はそう書く
   * 決定 2〜4 を docs/decisions/saikyo.md（2〜4）と docs/decisions/page-release.md（2）に足す。docs/notes/page-announcement.md の実例の表に最強戦の行を足す。handover.md 5章の #549 の行・「最終更新」を公開に合わせて直す。前提の1点目の static-generation.md の行
2. マージする: CLAUDE.md「ブランチ運用」「マージするとき」のとおり（push 直前に再 fetch して祖先を確かめる）。マージ後の check-run「Workers Builds: mj」を確かめる
3. 公開後の確かめと片付け（#549 の本文の「公開後の確かめ」のうち、Code ができるもの）:
   * 本番（`curl`、クエリ付き）: トップ・2025年度 `/saikyo/2025/`・対局1枚・卓2枚（ABEMA の回がある卓と、回が無い卓 `2026/20261004-s1-a`）が 200 で noindex が無い（`x-robots-tag` も）。`/saikyo/2025.html`・`/saikyo/2025` が `/saikyo/2025/` へ 301。`sitemap-saikyo.xml`・`llms.txt` に新しい URL がある
   * issue: #549 と #425 に公開した日とマージの SHA をコメントする。#549・#425・#382・#383 を閉じる（「状況:」ラベルを外す。#382・#383 は docs/decisions/saikyo.md の CHAT-1015-SKR-01 の決定2 の理由を書く）。#566 の本文の古い「まだ決めていないこと」を今の状態に直して閉じる。#530 に最強戦の共有ボタンが無くなったことをコメントする。#458 に、旧 URL 15件の確認（期日 2026-11-02）は公開により不要になり、新しい URL の登録の確認は公開から約2週間後に行うとコメントする（期日はチャット側がカレンダーに作る）
   * 残る作業（平野さんの手作業: Single Redirects のルール・X と LINE のカード・iPhone の実機・Search Console の sitemap 再送信と URL 検査）を #549 を閉じる前のコメントにチェックリストで書く（閉じた issue に残さず別に起票するなら、同じ主題の issue を確かめてから起票する）

止まる条件

* #549・#566・#425 に他セッションの着手中コメントがある
* 取り込みで生成物でない文書・コードが両立しない形で衝突した
* 未マージの work/ ブランチ（work/1015-skr-pub・work/1015-skr-vid を除く）が、この指示で変えるファイルの同じ行・関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。前提の static-generation.md の行は止まらずに扱いを書く）
* テストが通らない
* 生成物の差に、SKR-02 の表の種類と SKR-02 の後のシートの変化で説明できない差がある（saikyo/ 以外のページの変化を含む。ほかのセッションのマージで説明できるものはそう書いて進めてよい）
* マージ後の check-run が今回の変更による失敗（無関係な失敗なら原因を書いて残りの手順を進めてよい）
* 本番で新しいページが 200 でない、noindex が残る、旧 URL が 301 にならない（マージ済みなら戻さずに止まって報告する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。平野さんの手作業の残り（Single Redirects のルールなど）と、`?match=` の転送が未確認であることを「未確認の項目」に書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b4879ad7）: https://github.com/retroeater/mj-logs/tree/main/guide/b4879ad7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
