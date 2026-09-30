# CHAT-0930-BNG-02

- 着手日時: 2026-09-30
- 対象issue: #126
- ブランチ: work/0930-bng
- 着手時HEAD: 50c5a508（BNG-01 のログを含む work/0930-bng に origin/cloudflare 64c349ab を取り込んだ後）

## 指示

【Claude作成】Claude Code 向け指示：#126（Bing・IndexNow）の記録を文書と issue に書き残し、cloudflare へマージする Chat-Ref: CHAT-0930-BNG-02 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/0930-bng を続けて使う（BNG-01 のログを一緒に cloudflare へ入れるため）。`git checkout -b work/0930-bng origin/work/0930-bng` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-BNG-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。

目的
BNG-01 の洗い出しで、Crawler Hints を On にした記録が #126 のコメントにしか無く、#126 の完了条件の一部（Bing と GSC の突き合わせ・?name= の扱い）が未実施のまま記録されていないと分かった。10/12 の確認までに文書と issue を実態に合わせ、10/12 に見ることを1か所にまとめる。コードは変えない。
決定（2026-09-30、平野さん）

* 10/12 ごろまでは Crawler Hints（案 b）の効果を切り分けて見る。それまで Bing Webmaster Tools でのサイトマップの再送信・URL の手動送信はしない。
* Bing Webmaster Tools の現状（サイトマップの検出数、`/title/` の URL 検査、`?name=` 付き URL の登録、Crawler Hints が On のまま）は平野さんが画面で確かめ、結果は別の指示で #126 に書く。
* この指示の変更（docs/notes/cloudflare.md・docs/handover.md・docs/logs・#126 へのコメント）は、平野さんの判断として cloudflare へ入れてよい。変更の中身は承知している。push が権限判定で拒否されたら、別の手段を試さずに止まる。
* マージ: 承認済み（チャットで）

前提（チャット側。平野さんの決定ではない）

* 書き場所・文面は下の案。追記先の現在の内容を読み、同じ趣旨の記述があれば置き換え・拡張してよい（どう処理したかを報告に書く）。矛盾していてどちらが正か判断が要るときだけ止まる。
* BNG-01 のログの `## 報告` の末尾に、テンプレートの空の項目がもう一組残っている（「ブランチ: work/0930-bng」から「エラー: なし」までの2組目。「未確認の項目: 手順1〜3すべて」等）。1組目が正。

手順

1. `docs/notes/cloudflare.md` の現在の内容を読み、Cloudflare の設定の記録（Speed 設定の表か、ボット系〈AI Crawl Control・Rate limiting〉の節の近く。文書の構成に合わせて選ぶ）に「Crawler Hints: On（2026-09-28、平野さんの申告。IndexNow への自動通知の試行、#126）。効果の判定は #126 の 10/12 の確認と #156 の結果を待つ」を1〜2行で足す。参照は #126 に置き、内容の重複は避ける。
2. `docs/handover.md`「次にやること」の #126 の行を、現状に合わせて直す: 「Bing で `title/` の登録を確認（10/12 ごろ、カレンダー登録済み）。Crawler Hints の効果の判定は #156 に依存。9/12 の完了条件（Bing のインデックス数と GSC の突き合わせ、`?name=` 付き URL の扱い）は未実施で、10/12 にまとめて見る。10/12 までサイトマップ再送信・URL 手動送信はしない」の趣旨で、表の1行に収まる長さにする。容量の上限（handover.md 警告 26KB）に注意する。
3. #126 に1件コメントする（見出し「## 2026-09-30 の整理（CHAT-0930-BNG-02）」）: (a) 9/12 の完了条件2件が未実施であること、10/12 にまとめて見ること、(b) 10/12 まで Crawler Hints の効果を切り分けるため Bing Webmaster Tools でのサイトマップ再送信・URL 手動送信はしないと決めたこと、(c) 10/12 に見る項目の一覧（`/title/` の URL 検査、サイトマップの検出数〈9/12 は 25。現在は sitemap.xml 配下に4本〉、`?name=` 付き URL の登録、Crawler Hints が On のまま、未登録なら案 c を検討）、(d) 現状の記録（平野さんの画面確認）は別途コメントする旨。コメント内容の要約をログにも書く。あわせて BNG-01 のログの `## 報告` の2組目（前提の項）を消す。

止まる条件

* 作業ブランチの条件を満たさない、または BNG-01 の状態が「判断待ち」でない。
* 追記先と矛盾していて、どちらが正か判断が要る。
* 容量の上限の警告が出て、統合で収まらない（案を書いて止まる）。
* 変更が docs/ と #126 のコメント以外に及ぶ。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* `python3 scripts/check_asset_limits.py` とテストを通し、変更が docs/ だけであることを確かめる。
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* マージ: 承認済み（チャットで）。docs のみの変更なので、完了報告のうえ、BNG-01・BNG-02 のログごと cloudflare へマージする（マージ後の再生成で変わるページは無い見込み。あれば報告する）。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。BNG-01 のログの `## 報告` の状態は「判断待ち」。ローカルの work/0930-bng は origin/work/0930-bng（3de2dec1）と一致。`origin/cloudflare`（64c349ab）が祖先でなかったため `git merge origin/cloudflare` で取り込んだ（衝突なし）。
- 指示欄の末尾は指示文の最後の行と一致。
- 手順1: `docs/notes/cloudflare.md`「Speed 設定の現状」の節の末尾（Smart Hints の記述の直後、「Synthetic monitoring」の節の前）に Crawler Hints: On（2026-09-28、申告）・#126・#156 待ちを2行で追加。同趣旨の既存記述は無かった（`Crawler Hints` の語はこの文書に無し）。ボット系の節は「AIクローラーの扱い」「Rate limiting rules」で、キャッシュ・IndexNow の話題は Speed 設定側が近いため、そちらにした。
- 手順2: `docs/handover.md`「次にやること」の #126 の行を、依存（#156）・未実施の完了条件・10/12 までサイトマップ再送信と URL 手動送信をしない、に書き換え（置き換え。追記型にしていない）。handover.md は 22,851 バイト（警告 26KB 未満）。
- 手順3: #126 にコメント（https://github.com/retroeater/mj/issues/126#issuecomment-5904166851 ）。見出し「## 2026-09-30 の整理（CHAT-0930-BNG-02）」。要約: (a) 9/12 の完了条件2件（Bing と GSC の22URLの突き合わせ・`?name=` 付き URL の登録）は未実施で 10/12 にまとめて見る、(b) 10/12 まで Crawler Hints の効果を切り分けるため Bing Webmaster Tools でのサイトマップ再送信・URL 手動送信はしない（平野さんの決定）、(c) 10/12 に見る項目: `/title/` の URL 検査〈未登録なら案 c〉・サイトマップの検出数〈9/12 は 25、現在は4本〉・`?name=` 付き URL の登録・Crawler Hints が On のまま・突き合わせ、(d) 現状の記録は平野さんの画面確認の後に別途コメント。
- BNG-01 のログの `## 報告` の2組目（空のテンプレート項目）を削除。1組目は変更なし。
- 検査: `python3 scripts/check_asset_limits.py` は全項目 OK。`python3 -m unittest discover -s scripts/tests` は 446 件 OK。`git diff --stat origin/cloudflare` の docs/ 以外の変更は無し。
- 気づいたこと: 取り込んだ `origin/cloudflare` には `jpml_titles.html` の削除（#441）が含まれる（BNG-01 の時点では未マージだった）。#126 の 10/12 の確認事項には影響しないが、Bing に旧 URL が登録されていれば 301 の影響を受ける（Bing 側の状況は未確認）。

## 報告

- 状態: 完了（マージの結果は下）
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし（docs のみ）
- マージ: 済（abf2a3e6 を cloudflare へ fast-forward。docs/ のみの変更で Workers Builds は走らない見込み〈#171〉）
- issue: #126（コメント1件: https://github.com/retroeater/mj/issues/126#issuecomment-5904166851 ）
- 判断が必要なこと: なし。10/12 ごろの確認と、平野さんの画面確認の結果を #126 に書く別の指示が次
- 未確認の項目:
  - Crawler Hints が On であること・Bing Webmaster Tools の現状は平野さんの申告で、この環境からは確認できない
  - マージ後の check-run（docs のみのため出ない見込み）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e8e763b3）: https://github.com/retroeater/mj-logs/tree/main/guide/e8e763b3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e8e763b3/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
