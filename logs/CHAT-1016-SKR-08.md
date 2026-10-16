# CHAT-1016-SKR-08

- 着手日時: 2026-10-16
- 対象issue: #549、#425、#382、#383、#566、#530、#458
- ブランチ: work/1016-skr-after
- 着手時HEAD: 374ad1fba

## 指示

【Claude作成】Claude Code 向け指示：最強戦の公開（#549）の後の確かめと issue の片付け（CHAT-1016-SKR-06 の続き） Chat-Ref: CHAT-1016-SKR-08 マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（本番への反映はチャット側が確かめた。新しいセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-skr-after の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-skr-after を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-SKR-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-SKR-08` を足す。

目的
CHAT-1016-SKR-06 は cloudflare へのマージ（fa2791042）の後、本番のビルドが順番待ちで止まった。その後 5bf5d9b0f の本番向けの「Workers Builds: mj」が success になり（2026-10-16 01:33 UTC）、本番に出た。SKR-06 の手順3（公開後の確かめと片付け）を行う。
決定（2026-10-16、平野さん）

* なし（SKR-06 の決定のまま）

前提（チャット側。平野さんの決定ではない）

* チャット側が確かめた事実（2026-10-16 01:48 UTC ごろ、WebFetch・REST）: 本番の `/saikyo/2025/` が新しい年度ページ（canonical `https://ryoei.pro/saikyo/2025/`、og:image が ABEMA の作品の画像、noindex の meta なし）。`/saikyo/2018.html` を開くと canonical が `/saikyo/2018/` のページ。本番向けのビルドは 5bf5d9b0f（success）。その後の f6fae4d33 も走っていた
* 本番のビルドが止まって見えた理由（チャット側の読み。平野さんのスクショ〈Build history〉から）: 同時に多くの作業ブランチが push してプレビューのビルドが順番待ちになり、待つ間に cloudflare へ新しい push が来ると古い本番のビルドは飛ばされ（⏩）、新しいものだけが残る。check-run の「Workers Builds: mj」は同じコミットのプレビューのものも付くので、本番かどうかは出力に「Preview Alias URL」が無いことで見分ける（要確認: docs/notes/cloudflare.md の「ビルド成否と本番の確認範囲（check-runs）」にこの見分け方と「飛ばされる」挙動が書かれているか。無ければ、ログの「判断が必要なこと」に、書き足す案として書く。この指示では文書を直さない）
* `?match=` の Single Redirects のルールは平野さんがこれから作る。この指示では確かめず「未確認の項目」に書く（チャット側が後で確かめる）
* 新しい URL の Google 登録の確認は、チャット側がカレンダーに 2026-10-30 の予定を作った。#458 の旧 URL 15件の予定（11/2）と #428 の CLS の予定（11/1）は【済】にした

手順

1. 本番（`curl`、クエリ付き）: トップ `/saikyo/`・2025年度 `/saikyo/2025/`・対局1枚・卓2枚（ABEMA の回がある卓と、回が無い卓 `2026/20261004-s1-a`）が 200 で noindex が無い（`x-robots-tag` も）。`/saikyo/2025.html`・`/saikyo/2025` が `/saikyo/2025/` へ 301。`sitemap-saikyo.xml`・`llms.txt` に新しい URL がある。結果を表でログに書く
2. issue（SKR-06 の手順3 のとおり。各コメントの末尾に `Chat-Ref: CHAT-1016-SKR-08`）: #549 と #425 に公開した日（2026-10-16）とマージの SHA（fa2791042）と本番に出たビルドのコミット（5bf5d9b0f）をコメントする。#549 に平野さんの手作業の残りのチェックリスト（Single Redirects のルール・X と LINE のカード・iPhone の実機・Search Console の sitemap 再送信と URL 検査・2026-10-30 の登録の確認）を書く。#549・#425・#382・#383 を閉じる（「状況:」ラベルを外す。#382・#383 は docs/decisions/saikyo.md の CHAT-1015-SKR-01 の決定2 の理由）。#566 の本文の古い「まだ決めていないこと」を今の状態に直して閉じる。#530 に最強戦の共有ボタンが無くなったことをコメントする。#458 に、旧 URL 15件の確認は公開により不要になり、新しい URL の登録は 2026-10-30 に確かめるとコメントする
3. ログを仕上げ、cloudflare へ入れる（docs/decisions への記録は無し。SKR-06 の決定は記録済みのはず。無ければ足す）

止まる条件

* 本番で新しいページが 200 でない、noindex が残る、旧 URL が 301 にならない（issue を閉じずに止まって報告する）
* #549・#425・#566 に他セッションの着手中コメント（未解除）がある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 374ad1fb）: https://github.com/retroeater/mj-logs/tree/main/guide/374ad1fb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/374ad1fb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98839f38.md
