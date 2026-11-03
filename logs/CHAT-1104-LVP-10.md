# CHAT-1104-LVP-10

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 53edca83d

## 指示

【Claude作成】Claude Code 向け指示：live/ を公開する変更を作り、プレビューで止める（noindex を外す・navbar・サイトマップ・llms.txt・旧 video_live.html の 301・プレーオフ） Chat-Ref: CHAT-1104-LVP-10 マージ: 判断待ちで止まる（プレビューで止め、告知動画を撮った後にマージ＝公開する。マージは次の指示） 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。work/1017-lvp（CHAT-1102-LVP-09）が cloudflare にマージ済みであることを確かめ、違えば止まる。

目的
/live の正式公開（#362）の変更を1本の作業ブランチに作り、プレビューで止める。平野さんがプレビューから告知動画を撮った後、次の指示でマージ（＝公開）する（最強戦と同じ順）。pro/ の公開（#641）は live/ の公開を待つ。
決定（2026-10-16〜11-04、平野さん）

* 公開の段（docs/new-page-checklist.md の段2）: noindex を外す。navbar の「番組 > 放送対局」の行き先を `/video_live.html` から `/live/` に替える（位置・名前は今のまま）。生成で `sitemap-live.xml` を書き出して `sitemap.xml` から参照する（ほかの生成ページと同じ形）。`llms.txt` の放送対局の行を `/live/` に替え、本数を今の値に直す
* 旧 `video_live.html` は公開と同時に `/live/` へ 301 し、旧ページの生成を止める（`jpml_titles`・帰り道・Mつくの前例にそろえる）。`docs/notes/redirects.md` に行を足す
* 旧 `video_live.html?name=<名前>` の流入を受けるため、/live が `?name=` を `?q=` と同じ絞り込みとして受ける。これは期限つきの暫定とし、外すトリガを `docs/notes/redirects.md` に書く（転送の四半期の棚卸し〈#568〉で、Cloudflare の集計で `?name=` の流入が無いと確かめたら、301 の行と `?name=` の受け付けを一緒に外す）
* canonical は live/ の全ページに付ける（トップの `?q=`・`?name=` は表示の絞り込みなので、2026-10-31 の基準〈docs/decisions/seo-bing.md、CHAT-1031-SEO-13〉でも自分自身を指す）
* URL パラメータの規約の表（docs/new-page-checklist.md）に `q` と、期限つきの `name` を足す
* 達人戦のプレーオフ（第1〜3期、各期2本）を載せる。この指示でステージの記号「プレーオフ」と並び（ベスト16 の前）をコードに足し、Claude Code が【3】の6行に入れる値の表を作る。平野さんは表を見て【3】に入れ、その後にマージする。掲載はメンバー限定版を Y、公開版は【3】の「冒頭」で決める（冒頭が Y なら N。決勝と同じ扱い）
* 文書の直しを公開の変更に入れる（B14）: docs/notes/live-page-design.md の古い記述（4章の「Workers のプラン 未確認」、2-3 の「未検証」、2-7 の公開手順など）、docs/notes/static-generation.md と docs/handover.md の「未公開」、docs/new-page-checklist.md の実例表の live 列、#362 の本文
* SEO-03 の B7（「18 の件」）は、#362 のコメントから中身を読み、公開前の確かめに入れる
* 公開後の確かめは段2のとおり（本番の HTML、X・LINE のカード、実機、Search Console の再送信と URL 検査）。約2週間後のインデックス登録の確認はカレンダーに入れる（チャット側）。確かめは次の指示で行う
* 公開の後に回すもの（この指示ではしない）: #495（公開の直後に別の指示）、#490 の残り・#398〜#403・#354（決定どおり公開の後に順に）、#396・#397 の本文を「かな・所属」だけに直して残す、LV-04 の 798 と 794 の差4は調べずに閉じる

前提（チャット側。平野さんの決定ではない）

* 2026-11-04 の cloudflare（53edca83d）で、`navbar.js` は `/video_live.html` を指し、`llms.txt` の放送対局の行は `video_live.html`・「2,332本」、`_redirects` に `/live`（301）・`/live/`・`/live/:slug/` と生成の節がある。`sitemap-live.xml` は無い。旧ページの生成は `scripts/generate_video_live.py`（ほかに `scripts/generate_jpml_pros.py`・`scripts/lib/page.py` が `video_live` を参照する。要確認）
* 2026-11-04 の時点で、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・live/ の生成スクリプトを変えている未マージの work/ ブランチは無い（要確認）
* 前例: `/video_mtsuku.html` → `/mtsuku/`（#570）、`/saikyo/?q=` の期限つきの行（#627）の書き方が docs/notes/redirects.md にある
* pro/（未公開）は live/ の期・卓のページと `/live/?q=<名前>` へリンクしている（CHAT-1101-PRP-01）。この指示の変更の後も、その形のリンクが開けて絞り込まれる必要がある
* プレーオフの6行（2026-10-17 の行番号）: 4289 `pPhdXp3U7mU`・4283 `lQTUI_C_Vw0`（第1期）、4228 `4j_vrUqShCc`・4222 `1-DyHZm29OM`（第2期）、4240 `DfjwueadOwc`・4264 `XQLktBZyx5A`（第3期）。【3】の掲載は空欄、ステージ・まとめ単位も空（要確認）
* 公開前の基準値（Search Console）は残さない（B11）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。`regenerate-page.yml` を作業ブランチで `check`・`target_page: live_pages` で手動実行し、生成が止まらないことを確かめる。【3】のプレーオフの6行を動画ID で読み、「冒頭」の値と、上の決定のとおりに入れる値（掲載・ステージ・ステージの並び・まとめ単位）の表を `## 経過` に書く（並びの値は既存のステージとの前後が分かるように）。#362 のコメントから SEO-03 の B7 を読み、何を確かめるかを書く
2. 上の決定を実装する（noindex・navbar・`sitemap-live.xml` と `sitemap.xml`・`scripts/update_sitemap_lastmod.py` の対象・`llms.txt`・旧ページの 301 と生成の停止〈`regenerate.py`・ワークフロー・テストの参照も〉・`?name=` の受け付け・canonical・ステージの記号「プレーオフ」・URL パラメータの表・B14 の文書・docs/notes/redirects.md の行）。旧ページを参照する箇所は `git grep -n 'video_live'`（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）で洗い出し、残すものは理由を書く。`npx wrangler dev --port 8789 --ip 127.0.0.1 --persist-to /tmp/wrangler-state` で、`/video_live.html`・`/video_live.html?name=<名前>`（クエリが残って絞り込まれるか）・`/live/?q=<名前>`・pro/ の個別ページから live/ へのリンク1つを確かめる。全ページを再生成し、live/・sitemap・`llms.txt`・navbar・`_redirects`・文書のほかの生成物の差分が0であることを確かめる。テストを回す
3. 作業ブランチに push してプレビューで止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、プレーオフの6行に入れる値の表、プレビューで見てほしいページ（告知動画に使えるページの候補を含む）、SEO-03 の B7 の確かめの結果、次の指示（マージ＝公開と公開後の確かめ）に要る項目を書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 0章の確かめに当たる
* 手動実行の `check` が止まる、または `--check live_pages` が止まる理由を出す
* プレーオフの6行が【3】に見つからない、または「前提」の行と動画ID が合わない（その行を書いて止まる）
* `/video_live.html?name=` の 301 でクエリが残らず、`?name=` の受け付けが働かない（実測の結果と、代わりの案〈`_redirects` で `?name=` を `?q=` に書き換える等〉を書いて止まる）
* live/・sitemap・`llms.txt`・navbar・`_redirects`・文書のほかの生成物に差分が出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`navbar.js`・`_redirects`・`sitemap.xml`・`llms.txt`・`scripts/generate_live_pages.py`・`scripts/lib/live.py`・`assets/live.js`・`scripts/generate_video_live.py`・`scripts/regenerate.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-10` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1017-lvp は origin/cloudflare の祖先（LVP-09 はマージ済み）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1104-lvp はローカル・リモートとも無く、origin/cloudflare（53edca83d）から作成。着手は 2026-11-04 01:42 JST（毎朝の取り込みの直後ではない）

## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 667be053）: https://github.com/retroeater/mj-logs/tree/main/guide/667be053

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
