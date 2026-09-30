# CHAT-0930-BNG-03

- 着手日時: 2026-09-30
- 対象issue: #126（クローズ）、#156（コメント）、#486（新規起票）
- ブランチ: work/0930-bng
- 着手時HEAD: 0128fa1e

## 指示

【Claude作成】Claude Code 向け指示：#126 の結論（Bing に title/ 登録済み）を記録してクローズし、Bing Webmaster Tools の推奨事項の新 issue を起票する
Chat-Ref: CHAT-0930-BNG-03
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/0930-bng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-bng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-BNG-02 のログの `## 報告` を読み、マージ済みでなければ止まる。

## 目的
BNG-02 の直後に平野さんが Bing Webmaster Tools と Cloudflare の画面を確かめ、`/title/` はすでに Bing に登録済みと分かった。10/12 の確認は不要になったので、#126 を結論とともにクローズし、BNG-02 で書いた「10/12 まで待つ」の記述を直す。あわせて Bing の Recommendations の指摘を新 issue に残す。コードは変えない。

### 決定（2026-09-30、平野さん）
- #126 は下の結論を記録してクローズする。IndexNow の自前送信（案 c）は行わない。
- Cloudflare の Crawler Hints は On のままにする（効果は確認できていないが害は無い）。
- Bing の Recommendations の指摘（h1・description・タイトル・コンテンツ量・img の alt）は新 issue で対応する。この指示では起票と対象ページの照合まで（直さない）。
- 10/12 の Google カレンダーの予定はチャット側が削除済み。
- この指示の変更（docs/notes/cloudflare.md・docs/handover.md・docs/logs、#126 のコメントとクローズ、新 issue の起票）は、平野さんの判断として cloudflare へ入れてよい。変更の中身は承知している。push が権限判定で拒否されたら、別の手段を試さずに止まる。
- マージ: 承認済み（チャットで）

### 平野さんの画面確認の結果（2026-09-30、スクリーンショットと CSV。この環境からは確かめられない）
- Bing URL 検査 `https://ryoei.pro/title/`: 「正常にインデックスが付きました。URL は Bing に表示できます」。発見日 2026-09-28、前回クロール 2026-09-29 15:21、クロール・インデックス作成とも許可、ページ取り込み成功、正規 URL「- -」、SEO/GEO の問題なし、マークアップ OpenGraph 1種。
- Bing サイトマップ: `https://ryoei.pro/sitemap.xml`（サイトマップ インデックス）、最終送信 2026/09/12、最終クロール 2026/09/28、状態 成功、検出された URL 464（24＋39＋17＋384 と一致）。
- `?name=` 付き URL: Bing の検索パフォーマンスに `houou_results.html?name=麻生知花`・`houou_results.html?name=二階堂瑠美`・`houou_results.html?name=勝又健志`・`ouka_leagues.html?name=高宮まり`・`houou_results.html?name=近藤久春`・`houou_results.html?name=井出康平` などが個別の URL としてクリック・表示回数付きで出ている（Google と同じ扱い）。
- Bing の IndexNow のページ: 「Get Started」の導入画面のままで、送信の記録は無い。Recommendations でも「IndexNow が採用されていません（高）」と出ている。Cloudflare の Crawler Hints からの通知が Bing 側でこのサイトに記録された形跡は無い。
- Cloudflare Caching → Configuration の Crawler Hints: **On**（トグル有効）。
- Bing Recommendations（2026-09-30 時点。エラー合計 37、エラーのあるページ 35）:
  | 種類 | 重要度 | ページ数 |
  | --- | --- | --- |
  | IndexNow が採用されていません | 高 | - |
  | `<h1>` タグがありません | 高 | 4 |
  | 説明がページのヘッド セクションにありません | 高 | 2 |
  | ページに複数の `<h1>` タグがあります | 高 | 1 |
  | Meta descriptions on many of your pages are too short | 標準 | 5 |
  | タイトルが短すぎる多数のページ（50〜60文字を推奨） | 標準 | 14 |
  | コンテンツが不足している多数のページ | 標準 | 8 |
  | 高品質のドメインからのインバウンド リンクが不適切です | 標準 | - |
  | `<img>` タグに ALT 属性がありません | 低 | 1 |
- タイトルが短すぎる 14 ページ（CSV）: `/saikyo_results.html`, `/`, `/houou_leagues.html`, `/rh_paifu.html`, `/jpml_test.html`, `/houou_results.html`, `/rh_results.html`, `/resource_dictionary.html`, `/rh_results_detail.html`, `/wrc_ranking.html?sheet=JWRC`, `/ouka_leagues.html`, `/wrc_results.html`, `/rh_links.html`, `/jpml_logs.html`
- meta description が短い 5 ページ（CSV）: `/jpml_pros.html`, `/saikyo_results.html`, `/jpml_titles.html`（#441 で廃止済み・301）, `/`, `/ouka_results.html`
- コンテンツが不足している 8 ページ（画面）: `/ouka_results.html`, `/rh_paifu.html`, `/houou_results.html`, `/rh_results_detail.html`, `/wrc_ranking.html?sheet=JWRC`, `/wrc_results.html`, `/rh_links.html`, `/jpml_logs.html`
- h1 無し（4）・description 無し（2）・h1 複数（1）・img alt 無し（1）の対象 URL は CSV を取っていない（手順3で HTML から確かめる）。

### 前提（チャット側。平野さんの決定ではない）
- `/title/` は 9/28 のサイトマップのクロールで入ったと考えるのが自然（IndexNow の記録が無いため）。Crawler Hints の効果は「確認できず」と書く（否定はしない）。
- 「コンテンツ不足」の多くは Google Charts 依存の型B・ランキングのページ（#7・#111・#141）で、本文が JS 描画のため Bing の判定に掛かっていると考えられる。新 issue ではそれを指摘し、直すのは #7 の移行と合わせる案とする。
- 被リンク不足は ryoei.pro の方針（被リンク依頼はしない）どおり対応なし。
- タイトルの長さは Google 側の #5（title 整備）・#142 と重なる。新 issue は #5・#142・#13（構造化データ）・#7 を関連として本文に書く。既存の issue に同じ論点（Bing の Recommendations、h1、meta description）があれば起票せずそちらにコメントする。

## 手順
1. #126 に結論のコメントを1件書き（見出し「## 結論（2026-09-30、CHAT-0930-BNG-03）」）、クローズする。内容: 上の「画面確認の結果」の要点（`/title/` 登録済み・サイトマップ検出 464・`?name=` は Bing でも個別に登録・IndexNow の記録は無く Crawler Hints の効果は確認できず・Crawler Hints は On のまま・案 c は行わない・10/12 の確認は不要でカレンダーの予定は削除済み）と、9/12 の完了条件の読み替え（「Bing のインデックス数と GSC の22URLの突き合わせ」は、当時の 22 URL に対して現在は Bing 検出 464／GSC 検出 79〈9/28〉で前提が変わったため、URL 検査で `/title/` 登録済みを確認したことをもって完了とする）。ラベル `状況: 待ち` を外す。#156 のコメントに「#126 はクローズ。Crawler Hints の効果判定の共有先は無くなった」を1件書く。
2. 文書を直す: `docs/handover.md`「次にやること」の #126 の行を削除（BNG-02 で書いた行）。`docs/notes/cloudflare.md` の Crawler Hints の2行（BNG-02 で「Speed 設定の現状」の末尾に追加）を「Crawler Hints: On（2026-09-28〜、申告）。IndexNow への通知の記録は Bing 側に無く効果は確認できていない（#126、2026-09-30）。On のままにする」の趣旨に置き換える。追記先の現在の内容を読んでから直す。
3. 同じ論点の issue を検索し（open・closed、`Recommendations`・`h1`・`meta description`・`alt`・`Bing` で確認）、無ければ新 issue を起票する（題「Bing Webmaster Tools の Recommendations（h1・description・タイトル・コンテンツ量・alt）に対応する」、ラベル `分野: SEO/AIO`・`対象: 全ページ`）。本文は上の表と3つの一覧、関連 issue（#5・#142・#13・#7・#126）、前提の3点。起票前に `sitemap-pages.xml` の 24 URL について、リポジトリの HTML から h1 の有無・個数、`<meta name="description">` の有無と長さ、`<title>` の長さ（文字数）、alt の無い `<img>` を表にして本文に載せる（生成ページは生成物の HTML を読む。Bing の「50件まで」の注記どおり、Bing の一覧は全件でない可能性がある）。`/jpml_titles.html` は廃止済みのため対象外と明記する。直さない。
4. `python3 scripts/check_asset_limits.py` とテストを通し、変更が docs/ だけであることを確かめて cloudflare へマージする。

## 止まる条件
- 作業ブランチの条件を満たさない、または BNG-02 がマージ済みでない。
- #126 に他セッションの「着手中」コメントがある。
- 追記先と矛盾していて、どちらが正か判断が要る。
- 同じ論点の open issue がある（起票せず、そこへのコメント案をログに書いて止まる。手順1・2は進めてよい）。
- 変更が docs/ と issue 操作以外に及ぶ。
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。新 issue の番号と、手順3の表の要点（h1 無し・description 無し・alt 無しの該当ページ）を報告に書く。
- マージ: 承認済み（チャットで）。docs のみの変更なので、完了報告のうえ cloudflare へマージする。
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。BNG-02 のログの `## 報告` は「マージ: 済」、`origin/work/0930-bng` は `origin/cloudflare` の祖先（マージ済み）。ローカルの work/0930-bng を `git merge --ff-only origin/cloudflare` で 0128fa1e へ進めた。指示欄の末尾は指示文の最後の行と一致。
- #126 に他セッションの「着手中」コメントは無い（コメント5件とも平野さんの記録か Claude の記録）。
- 手順3の重複検索: 検索 API が使えないため、REST で issue 484件（PR 除く）を取得し、タイトルと本文で `Recommendations|h1|description|alt|Bing` を確認。Bing の Recommendations を扱う issue は無い。h1・description を含む open は #283（h1 と title の文言の統一）と #160（新サイトの情報設計、h1 は新サイト前提）で、論点が異なる（文言の統一・新サイトの設計）ため起票し、本文で関連に挙げた。#50・#52・#53・#158 は Closed。
- 手順3の照合（読むだけ）: `sitemap-pages.xml` は今は **23 URL**（指示の24は #441 で `jpml_titles.html` が外れる前の数）。`<title>` の文字数・description の有無と文字数・h1 の個数・alt の無い `<img>` を HTML から数えた（`sitemap-wayhome.xml`・`sitemap-saikyo.xml`・`sitemap-title.xml` も同様）。結果は #486 の本文の表。
  - h1 無し: 11ページ（`jpml_links`・`houou_ranking/leagues/results`・`ouka_ranking/leagues/results`・`wrc_ranking/results`・`resource_dictionary`・`rh_links`）。h1 複数: 0。description 無し: sitemap の URL では0（`404.html` だけ無い）。alt 無しの img: 0。title は全ページ 9〜32 文字（推奨 50〜60 より短い）。description の最短は `jpml_links.html` の 29 文字。
  - Bing の「description 無し 2」「h1 複数 1」「alt 無し 1」は sitemap の URL では再現しない（対象 URL の CSV が無いため、Bing が見ている別 URL の可能性）。
  - 旧 URL: `/jpml_logs.html` は本番で 404（`curl` で確認）、`/saikyo_results.html` は 301 → `/saikyo/`。Bing の一覧に残っている。
- 起票: #486「Bing Webmaster Tools の Recommendations（h1・description・タイトル・コンテンツ量・alt）に対応する」（ラベル `分野: SEO/AIO`・`対象: 全ページ`）。何も直していない。
- #126: 結論のコメント（https://github.com/retroeater/mj/issues/126#issuecomment-5905175268 ）。要約: `/title/` は Bing に登録済み（発見 9/28・クロール 9/29）、サイトマップ検出 464、`?name=` 付き URL は Bing でも個別に登録、IndexNow の記録は無く Crawler Hints の効果は確認できず、Crawler Hints は On のまま、案 c は行わない、10/12 の確認は不要（カレンダーは削除済み）、BNG-02 の再送信・手動送信をしない決定は解除、9/12 の完了条件（22URLの突き合わせ）は URL 検査で `/title/` 登録済みを確認したことをもって完了と読み替え。`状況: 待ち` ラベルを外して completed でクローズ。値は平野さんの申告で、ここからは検証できない。
- #156: コメント1件（https://github.com/retroeater/mj/issues/156#issuecomment-5905175827 ）。#126 はクローズ、Crawler Hints の効果判定の共有先は無くなった、この issue は単独で続ける。
- 文書: `docs/handover.md`「次にやること」の #126 の行を削除。`docs/notes/cloudflare.md`「Speed 設定の現状」末尾の Crawler Hints の2行を、On のまま・Bing 側に送信の記録が無く効果は未確認・案 c は行わない、に置き換え。
- 検査: `python3 scripts/check_asset_limits.py` は警告・超過なし。`python3 -m unittest discover -s scripts/tests` は 446 件 OK。`git diff --name-only origin/cloudflare` に docs/ 以外は無し。

## 報告

- 状態: 完了
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし（docs のみ）
- マージ: 済（cloudflare へ fast-forward。docs/ のみで Workers Builds は走らない見込み〈#171〉）
- issue: #126（結論のコメントとクローズ。`状況: 待ち` を外した）、#156（コメント1件）、#486（新規起票。何も直していない）
- 判断が必要なこと: #486 の「決めること」（タイトルを 50〜60 文字に伸ばすか、h1 の無い 11 ページに h1 を足すか〈#283・#160・#7 との順序〉、短い description を伸ばすか、`404.html` に description を足すか、`/jpml_logs.html`〈本番 404〉を 301 にするか、Bing の CSV で h1 無し・description 無し・h1 複数・alt 無しの対象 URL を取るか）。手順3の表の要点: h1 無しは11ページ（`jpml_links`・`houou_ranking/leagues/results`・`ouka_ranking/leagues/results`・`wrc_ranking/results`・`resource_dictionary`・`rh_links`）、description 無しは sitemap の URL では無し（`404.html` のみ）、alt 無しの img は無し。指示の「24 URL」は現在 23 URL（#441 の後）
- 未確認の項目: Bing・Cloudflare の画面の内容はすべて平野さんの申告でここからは検証できない。Bing の「description 無し 2」「h1 複数 1」「alt 無し 1」の対象 URL（CSV 未取得）。JS 描画後の h1・本文（HTML の静的な内容のみ照合）
- エラー: 一度、auto モードの分類器が Bash に判定なしのエラーを返した（読み取りの grep。同じ操作を Grep ツールに替えて続行）。検索 API が使えず REST で代替（BNG-01 と同じ）

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ce5031e6）: https://github.com/retroeater/mj-logs/tree/main/guide/ce5031e6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5031e6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5031e6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5031e6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5031e6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ce5031e6/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
